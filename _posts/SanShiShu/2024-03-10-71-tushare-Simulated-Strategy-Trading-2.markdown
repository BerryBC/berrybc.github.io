---
layout: post
title:  柒拾壹- tushare 模拟策略交易 （二）
date:   2024-03-10 20:54:46 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---



书接上一回

[柒拾- tushare 模拟策略交易 （一）](https://blog.csdn.net/BerryBC/article/details/136514591)

----

## 一、模拟交易

根据官网上的文章，并且以他们的[规则](https://tushare.pro/document/1?doc_id=90)。

主要是要`模拟`**买入卖出**的情况，以下是代码（如果不是官网的代码不能直接拿来，我就可以懒惰一点）：


```python
'''
Descripttion: 回测模块
Version: 1.0
Author: BerryBC
Date: 2024-02-18 17:50:01
LastEditors: BerryBC
LastEditTime: 2024-02-20 18:00:00
'''

import datetime
import time
import mysql.connector
from sqlalchemy import create_engine
import tushare

from Lib.DBConn import claDBConn


class claBackTestingDailyDeal(object):
    def __init__(self, strTitle='临时测试', strDesc='', fltInitCash=500000):
        '''
        description: 初始化
        '''
        # 存一个固定资产
        self.fltInitCash = fltInitCash

        # 获取链接
        self.dbMySQL = claDBConn()
        strSQLMaxInd = "SELECT MAX(bt_ind) FROM tb_bt_daily_cfg"
        listMaxInd = self.dbMySQL.ReturnSQLData(strSQLMaxInd)

        # 如果有回测序号则取最大，如没有则取1
        self.intInd = 1
        if listMaxInd is not None and listMaxInd[0][0] is not None:
            self.intInd = listMaxInd[0][0] + 1

        # 初始化该次回测数据
        strSQLBTCfg = "INSERT INTO tb_bt_daily_cfg (bt_ind, bt_title,bt_desc,bt_init_cash,bt_time) VALUES (" + \
            str(self.intInd)+", '"+strTitle+"', '"+strDesc+"', "+str(fltInitCash)+" ,now())"
        self.dbMySQL.RunSQLOnce(strSQLBTCfg)

        strSQLBTCfg = "DELETE FROM  tb_bt_daily_my_capital WHERE bt_ind = "+str(self.intInd)+""
        self.dbMySQL.RunSQLOnce(strSQLBTCfg)

        strSQLBTCfg = "DELETE FROM  tb_bt_daily_tmp_my_stock_pool WHERE bt_ind = "+str(self.intInd)+""
        self.dbMySQL.RunSQLOnce(strSQLBTCfg)

        # 初始化数据库数据
        self.dbMySQL.RefreshDailyQuote()
        self.dbMySQL.FixCalDay()

    def CalCapital(self, strDate=None):
        '''
        description: 计算当前资产
        return: {dict}  intLock : 股票资产
                        intRest : 现金资产
        '''
        dictCapital = {'intLock': 0, 'intRest': 0}
        if strDate is not None:
            # 先取最近的一个可用资产
            strSQLRestMoney = "SELECT * FROM  tb_bt_daily_my_capital WHERE bt_ind = " + \
                str(self.intInd)+"  AND state_dt <= '"+strDate+"' ORDER BY seq DESC LIMIT 1"
            listRestMoney = self.dbMySQL.ReturnSQLData(strSQLRestMoney)

            # 数据库中能查出的剩余资产
            if len(listRestMoney)>0 and listRestMoney[0][2] is not None:
                dictCapital['intRest'] = listRestMoney[0][2]
            else:
                # 如果查不出来则赋予一个初始现金
                dictCapital['intRest']=self.fltInitCash

            # 查询手上的股票值多少钱
            strSQLLockMoney = "SELECT sum(d_sq.close * bt_sp.hold_vol) FROM tb_daily_stock_quote d_sq INNER JOIN tb_bt_daily_tmp_my_stock_pool bt_sp ON bt_sp.stock_code=d_sq.ts_code  WHERE d_sq.trade_date='" + \
                strDate+"' AND bt_sp.bt_ind = "+str(self.intInd)
            listLockMoney = self.dbMySQL.ReturnSQLData(strSQLLockMoney)
            if len(listLockMoney)>0 and listLockMoney[0][0] is not None:
                dictCapital['intLock'] = listLockMoney[0][0]

        return dictCapital

    def AssetSnapshot(self, strDate=None):
        '''
        description: 打印一个资产快照出来
        '''
        dictCapitalSS = self.CalCapital(strDate)
        # 插入一个快照
        strSQLBTCfg = "INSERT INTO tb_bt_daily_my_capital (capital, money_lock,money_rest,deal_action,state_dt,bt_ind) VALUES ("+str(
            dictCapitalSS['intLock']+dictCapitalSS['intRest'])+", "+str(dictCapitalSS['intLock'])+", "+str(dictCapitalSS['intRest'])+", 'Snapshot','"+strDate+"' ,"+str(self.intInd)+")"
        self.dbMySQL.RunSQLOnce(strSQLBTCfg)
        return True

    def BuyStock(self, strDate=None, strStockCode=None, intVol=0,strComment=''):
        '''
        description: 购买股票
        '''
        # 如果都有传入参数
        if strDate is not None and strStockCode is not None and intVol != 0:
            dictCapitalSS = self.CalCapital(strDate)

            fltBuyAmount = 0
            # 查询要购买的股票要多少钱
            strSQLStockValue = "SELECT * FROM  tb_daily_stock_quote WHERE trade_date='" + \
                strDate+"' AND ts_code = '"+strStockCode+"'"

            listStockValue = self.dbMySQL.ReturnSQLData(strSQLStockValue)
            if len(listStockValue)>0 and listStockValue[0][5] is not None:
                fltBuyAmount = listStockValue[0][5]*intVol

            # 如果该股票有价
            if fltBuyAmount > 0:
                # 看看是否比手上现金多
                if fltBuyAmount <= dictCapitalSS['intRest']:
                    # 手上持有多少
                    fltHoldAmount = 0
                    fltHoldVol = 0
                    boolExsit = False

                    # 现在手上有多少这个股票
                    strSQLStockHold = "SELECT buy_price*hold_vol,hold_vol,buy_price FROM tb_bt_daily_tmp_my_stock_pool WHERE bt_ind = " + \
                        str(self.intInd)+" AND stock_code = '"+strStockCode+"'"

                    listStockHold = self.dbMySQL.ReturnSQLData(strSQLStockHold)
                    if len(listStockHold) >0 and listStockHold[0][0] is not None:
                        fltHoldAmount = listStockHold[0][0]
                        fltHoldVol = listStockHold[0][1]
                        boolExsit = True

                    # 重新计算持股信息
                    fltHoldAmount += fltBuyAmount
                    fltHoldVol += intVol
                    fltHoldPrice = fltBuyAmount / intVol

                    # 更新信息，如果不存在则插入
                    if boolExsit:
                        strSQLStockHoldUpdate = "UPDATE tb_bt_daily_tmp_my_stock_pool SET buy_price="+str(fltHoldPrice) + \
                            ", hold_vol="+str(fltHoldVol)+"  WHERE bt_ind = " + \
                            str(self.intInd)+" AND stock_code = '"+strStockCode+"'"
                        self.dbMySQL.RunSQLOnce(strSQLStockHoldUpdate)
                    else:
                        strSQLStockHoldUpdate = "INSERT INTO tb_bt_daily_tmp_my_stock_pool (stock_code,buy_price,hold_vol,bt_ind) VALUES('"+strStockCode+"', "+str(fltHoldPrice)+", "+str(fltHoldVol)+","+str(self.intInd)+")"
                        self.dbMySQL.RunSQLOnce(strSQLStockHoldUpdate)
                    # 插入一个购买信息
                    strSQLBTCfg = "INSERT INTO tb_bt_daily_my_capital (capital, money_lock, money_rest, deal_action, stock_code, deal_price, stock_vol,comment,  state_dt,  bt_ind) VALUES ("+str(
                        dictCapitalSS['intLock']+dictCapitalSS['intRest'])+", "+str(dictCapitalSS['intLock']+fltBuyAmount)+", "+str(dictCapitalSS['intRest']-fltBuyAmount)+", 'Buy','"+strStockCode+"',"+str(listStockValue[0][5])+","+str(intVol)+",'"+strComment+"','"+strDate+"' ,"+str(self.intInd)+")"
                    self.dbMySQL.RunSQLOnce(strSQLBTCfg)
                else:
                    # 插入一个购买失败的信息
                    strSQLBTCfg = "INSERT INTO tb_bt_daily_my_capital (capital, money_lock, money_rest, deal_action, stock_code, deal_price, `comment`,  state_dt,  bt_ind) VALUES ("+str(
                        dictCapitalSS['intLock']+dictCapitalSS['intRest'])+", "+str(dictCapitalSS['intLock'])+", "+str(dictCapitalSS['intRest'])+", 'Buy - Faild','"+strStockCode+"',"+str(listStockValue[0][5])+",'账户资金不足 - "+strComment+"','"+strDate+"' ,"+str(self.intInd)+")"
                    self.dbMySQL.RunSQLOnce(strSQLBTCfg)
        return True

    def SellStock(self, strDate=None, strStockCode=None, intVol=0,strComment=''):
        '''
        description: 卖出的股票
        '''
        # 如果都有传入参数
        if strDate is not None and strStockCode is not None and intVol != 0:
            dictCapitalSS = self.CalCapital(strDate)
            # 手上持有多少
            fltHoldAmount =0
            fltHoldVol = 0
            fltHoldPrice=0
            boolExsit = False

            # 现在手上有多少这个股票
            strSQLStockHold = "SELECT (d_sq.close * bt_sp.hold_vol),bt_sp.hold_vol,d_sq.close,bt_sp.buy_price FROM tb_daily_stock_quote d_sq INNER JOIN tb_bt_daily_tmp_my_stock_pool bt_sp ON bt_sp.stock_code=d_sq.ts_code  WHERE d_sq.trade_date='" + \
                strDate+"' AND bt_sp.bt_ind = " + str(self.intInd) + \
                " AND d_sq.ts_code='"+strStockCode+"'"

            listStockHold = self.dbMySQL.ReturnSQLData(strSQLStockHold)
            if len(listStockHold)>0 and listStockHold[0][0] is not None:
                fltHoldAmount= listStockHold[0][0]
                fltHoldVol = listStockHold[0][1]
                fltNowPrice = listStockHold[0][2]
                fltHoldPrice = listStockHold[0][3]
                boolExsit = True

                fltProfit =(fltNowPrice-fltHoldPrice)*intVol
                fltChangeCap=fltNowPrice* intVol

            # 如果存在且够 持股 卖
            if boolExsit :
                if fltHoldVol >= intVol:
                    # 插入一个卖出信息
                    strSQLBTCfg = "INSERT INTO tb_bt_daily_my_capital (capital, money_lock, money_rest, deal_action, stock_code, deal_price, stock_vol,profit,profit_rate, comment, state_dt,  bt_ind) VALUES ("+str(
                        dictCapitalSS['intLock']+dictCapitalSS['intRest'])+", "+str(dictCapitalSS['intLock']-fltChangeCap)+", "+str(dictCapitalSS['intRest']+fltChangeCap)+", 'Sell','"+strStockCode+"',"+str(fltNowPrice)+","+str(-intVol)+", "+str(fltProfit)+","+str(fltNowPrice/fltHoldPrice - 1)+", '"+strComment+"','"+strDate+"' ,"+str(self.intInd)+")"
                    self.dbMySQL.RunSQLOnce(strSQLBTCfg)
                    if fltHoldVol == intVol:
                        strSQLHoldDelete = "DELETE FROM tb_bt_daily_tmp_my_stock_pool WHERE bt_ind = " + \
                            str(self.intInd)+" AND stock_code='"+strStockCode+"'"
                        self.dbMySQL.RunSQLOnce(strSQLHoldDelete)
                    else:
                        strSQLStockHoldUpdate = "UPDATE tb_bt_daily_tmp_my_stock_pool SET hold_vol=" + \
                            str(fltHoldVol-intVol)+"  WHERE bt_ind = " + \
                            str(self.intInd)+" AND stock_code = '"+strStockCode+"'"
                        self.dbMySQL.RunSQLOnce(strSQLStockHoldUpdate)
                else:
                    # 插入一个卖出失败的信息
                    strSQLBTCfg = "INSERT INTO tb_bt_daily_my_capital (capital, money_lock, money_rest, deal_action, stock_code, deal_price, `comment`,  state_dt,  bt_ind) VALUES ("+str(
                        dictCapitalSS['intLock']+dictCapitalSS['intRest'])+", "+str(dictCapitalSS['intLock'])+", "+str(dictCapitalSS['intRest'])+", 'Sell - Faild','"+strStockCode+"',"+str(fltNowPrice)+",'卖出超过持股 - "+strComment+"','"+strDate+"' ,"+str(self.intInd)+")"
                    self.dbMySQL.RunSQLOnce(strSQLBTCfg)

```


## 二、主要函数

`初始化该类`时，会存入数据库当前`配置`的初始资金、测试名字等，并给出一个测试编号（方便后续根据编号观察每次的买卖以及盈亏情况）。

#### 2.1 CalCapital

该方法需要传入一个时间，并计算当前持仓在那天的`股票资产`，并且计算当前手上的`现金`

#### 2.2 AssetSnapshot

就当我没有买卖的时候，其实是不会记录我的资产的，如果我想知道我某天就算`没进行操作`，但实际上股票资产以及手上现金时，则需要顶一个函数来记录当前资产了（就是`资产快照`）


#### 2.3 BuyStock

这个函数会比较复杂，就是需要计算当前手上的钱是否购买该股票，并且更新持仓成本


#### 2.4 SellStock

这个函数简单，主要是查询手上有没有该股票，有的话就可以卖

## 三、终于要模拟操作了！

好了，我老婆带着我儿子回来了，我要去溜娃了~