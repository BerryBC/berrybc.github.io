---
layout: post
title:  柒拾贰- tushare 模拟策略交易 （三）
date:   2024-04-07 11:36:36 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---



书接上两回

[柒拾- tushare 模拟策略交易 （一）](https://berrybc.github.io/%E4%B8%89%E4%B8%96%E4%B9%A6%E8%AE%B0%E5%BD%95/70-tushare-Simulated-Strategy-Trading-1/)

[柒拾壹- tushare 模拟策略交易 （二）](https://berrybc.github.io/%E4%B8%89%E4%B8%96%E4%B9%A6%E8%AE%B0%E5%BD%95/71-tushare-Simulated-Strategy-Trading-2/)

----

# 开始

终于我们可以可以来进行模拟了

**复杂** 的我不会，所以就写个最简单的策略吧：

- 买入条件
1. 比两年前是上升的
2. 比一年前是上升的
3. 比一个月前上升更下降的区间在 ± 0.38% 内
4. 比昨天上涨 0.1% 以上 0.5% 以下

- 卖出条件
1. 如果当前下跌 5% 则全卖出

# 进行模拟

### 设计模拟时间

先进行模拟时间，即回测多少天

```python
strStartDate = '20230101'
strEndDate = '20240407'
```

就是从 2023年的1月1日开始进行策略，2024年4月7日结束

### 选股范围

```python
strIndustry = "'银行'"
```

只选择银行股进行回测

### 中间计算

因为需要计算 2年、1年、半年等时间窗口各股票的**涨跌**，用 `Python` 比较麻烦，于是使用 `SQL` 进行计算

# 完整代码
```python

'''
Descripttion: 就简单回测
Version: 1.0
Author: BerryBC
Date: 2024-02-18 12:09:31
LastEditors: BerryBC
LastEditTime: 2024-04-07 11:07:43
'''

import datetime
from Lib.DBConn import claDBConn
from Lib.BackTestingDailyDeal import claBackTestingDailyDeal

btDaily = claBackTestingDailyDeal('研究银行股', '根据时间窗口看追涨', 100000)
dbConn = claDBConn()

strStartDate = '20240101'
strEndDate = '20240407'

strStock = '601988.SH'

strMaxDate = ''


# 时间序列
strSQLTime = "SELECT * FROM tb_calendar WHERE trade_date >=" + \
    strStartDate+" AND trade_date <= "+strEndDate+" ORDER BY trade_date "
listCal = dbConn.ReturnSQLData(strSQLTime)

# 打印快照
btDaily.AssetSnapshot(listCal[0][0])

# 循环验证
for tubDay in listCal:
    # 计算日期
    strNowDate = tubDay[0]
    strMaxDate=strNowDate

    '''
                           .::::.
                         .::::::::.
                        :::::::::::
                     ..:::::::::::'
                  '::::::::::::'
                    .::::::::::
               '::::::::::::::..
                    ..::::::::::::.
                  ``::::::::::::::::
                   ::::``:::::::::'        .:::.
                  ::::'   ':::::'       .::::::::.
                .::::'      ::::     .:::::::'::::.
               .:::'       :::::  .:::::::::' ':::::.
              .::'        :::::.:::::::::'      ':::::.
             .::'         ::::::::::::::'         ``::::.
         ...:::           ::::::::::::'              ``::.
        ````':.          ':::::::::'                  ::::..
                           '.:::::'                    ':'````..
    '''
    # 追涨杀跌
    # # 每日数据
    # strSQLQuote = "SELECT * FROM tb_daily_stock_quote WHERE trade_date=" + \
    #     strNowDate+" AND ts_code = '"+strStock+"'"
    # listQuote = dbConn.ReturnSQLData(strSQLQuote)
    # if listQuote[0][8] > 0.2:
    #     btDaily.BuyStock(strNowDate, strStock, 500,'追涨')

    # elif listQuote[0][8] < -0.1:

    #     strSQLSell = "SELECT * FROM tb_bt_daily_tmp_my_stock_pool WHERE stock_code='" + strStock+"' AND bt_ind = '"+str(btDaily.intInd)+"'"
    #     listSell = dbConn.ReturnSQLData(strSQLSell)
    #     if len(listSell) > 0 and listSell[0][0] is not None:
    #         btDaily.SellStock(strNowDate, strStock, listSell[0][2], '全部卖了！！！')

    '''
                           .::::.
                         .::::::::.
                        :::::::::::
                     ..:::::::::::'
                  '::::::::::::'
                    .::::::::::
               '::::::::::::::..
                    ..::::::::::::.
                  ``::::::::::::::::
                   ::::``:::::::::'        .:::.
                  ::::'   ':::::'       .::::::::.
                .::::'      ::::     .:::::::'::::.
               .:::'       :::::  .:::::::::' ':::::.
              .::'        :::::.:::::::::'      ':::::.
             .::'         ::::::::::::::'         ``::::.
         ...:::           ::::::::::::'              ``::.
        ````':.          ':::::::::'                  ::::..
                           '.:::::'                    ':'````..
    '''
    # 长期来看是否可以买
    # 针对某个行业的股票
    listPeriod = [720,360,180,90,30,5]
    strIndustry = "'银行'"
    strEndDate ="DATE'"+(datetime.datetime.strptime(strNowDate, "%Y%m%d")).strftime('%Y-%m-%d')+"'"

    # 计算趋势中间表
    strSQLTruncate = "TRUNCATE TABLE TB_BT_MID_DAILY_PERIOD_TREND"
    dbConn.RunSQLOnce(strSQLTruncate)

    # 根据不同时间长度，如2年、1年、半年等计算股价升降
    for intCalPeriod in listPeriod:
        strSQLInsert ='''
            -- CREATE TABLE TB_BT_MID_DAILY_PERIOD_TREND as
            INSERT INTO TB_BT_MID_DAILY_PERIOD_TREND
            WITH CHECK_STOCK_LIST AS (
                -- 获取行业对应的所有股票
                SELECT
                    TS_CODE,
                    NAME,
                    INDUSTRY,
                    EXCHANGE
                FROM
                    TB_ALL_STOCK_LIST
                WHERE
                    1=1
                    AND INDUSTRY = '''+strIndustry+'''
            )
            SELECT
                '''+str(intCalPeriod)+'''                                              AS DAY_GAP,
                '''+strEndDate+'''                                            AS END_DATE,
                S_L_O.TS_CODE,
                S_L_O.NAME,
                S_L_O.INDUSTRY,
                S_L_O.EXCHANGE,
                -- 获取整体的上升下降趋势，以及简单统计
                MIN(TTL_U.TRADE_DAY)                                    AS TRADE_DAY,
                MIN(TTL_U.MIN_COLSE)                                    AS MIN_COLSE,
                MIN(TTL_U.MAX_COLSE)                                    AS MAX_COLSE,
                MIN(TTL_U.MIN_PCT_CHG)                                  AS MIN_PCT_CHG,
                MIN(TTL_U.MAX_PCT_CHG)                                  AS MAX_PCT_CHG,
                MIN(TTL_U.DOWN_DAY)                                     AS DOWN_DAY,
                MIN(TTL_U.UP_DAY)                                       AS UP_DAY,
                -- 期间内的最早收盘价以及最终收盘价
                MIN(TTL_U.BEGIN_CLOSE)                                  AS BEGIN_CLOSE,
                MIN(TTL_U.END_CLOSE)                                    AS END_CLOSE,
                (MIN(TTL_U.END_CLOSE) / MIN(TTL_U.BEGIN_CLOSE) - 1)*100 AS ALL_PCT_CHG,
                -- 期间内涨跌的天数占比
                MIN(TTL_U.UP_DAY) / MIN(TTL_U.TRADE_DAY)                AS UP_DAY_PCT,
                MIN(TTL_U.DOWN_DAY) / MIN(TTL_U.TRADE_DAY)              AS DOWN_DAY_PCT,
                MIN(TTL_U.END_CLOSE_PCT_C)                              AS END_CLOSE_PCT_C
            FROM
                (
                    (
                        -- 整体统计趋势
                        SELECT
                            S_DQ.TS_CODE,
                            COUNT(1)            AS TRADE_DAY,
                            MIN(S_DQ.CLOSE)     AS MIN_COLSE,
                            MAX(S_DQ.CLOSE)     AS MAX_COLSE,
                            MIN(S_DQ.PCT_CHG)   AS MIN_PCT_CHG,
                            MAX(S_DQ.PCT_CHG)   AS MAX_PCT_CHG,
                            COUNT(
                                CASE
                                    WHEN S_DQ.PCT_CHG < 0 THEN 1
                                    ELSE NULL
                                END
                            ) AS DOWN_DAY,
                            COUNT(
                                CASE
                                    WHEN S_DQ.PCT_CHG > 0 THEN 1
                                    ELSE NULL
                                END
                            ) AS UP_DAY,
                            NULL                AS BEGIN_CLOSE,
                            NULL                AS END_CLOSE,
                            NULL                AS END_CLOSE_PCT_C
                        FROM
                            TB_CALENDAR                                 S_T
                            INNER JOIN TB_DAILY_STOCK_QUOTE             S_DQ
                                ON S_T.TRADE_DATE   = S_DQ.TRADE_DATE
                            INNER JOIN CHECK_STOCK_LIST                 S_LIST
                                ON S_DQ.TS_CODE     = S_LIST.TS_CODE
                        WHERE
                            1=1
                            AND S_T.TRADE_DATE_DT   < '''+strEndDate+'''
                            AND S_T.TRADE_DATE_DT   >= DATE_SUB('''+strEndDate+''', INTERVAL '''+str(intCalPeriod)+''' DAY)
                        GROUP BY
                            S_DQ.TS_CODE
                    )
                    UNION ALL
                    (
                        -- 计算时间窗口内最早那天的收盘价
                        SELECT
                            MIN_MID.TS_CODE,
                            NULL                AS TRADE_DAY,
                            NULL                AS MIN_COLSE,
                            NULL                AS MAX_COLSE,
                            NULL                AS MIN_PCT_CHG,
                            NULL                AS MAX_PCT_CHG,
                            NULL                AS DOWN_DAY,
                            NULL                AS UP_DAY,
                            MIN_MID.CLOSE       AS BEGIN_CLOSE,
                            NULL                AS END_CLOSE,
                            NULL                AS END_CLOSE_PCT_C
                        FROM
                            (
                                SELECT
                                    S_DQ_MIN.TS_CODE,
                                    S_DQ_MIN.CLOSE,
                                    RANK() OVER(PARTITION BY S_DQ_MIN.TS_CODE ORDER BY S_T_MIN.TRADE_DATE_DT ASC)   AS RNK
                                FROM
                                    TB_CALENDAR                                 S_T_MIN
                                    INNER JOIN TB_DAILY_STOCK_QUOTE             S_DQ_MIN
                                        ON S_T_MIN.TRADE_DATE   = S_DQ_MIN.TRADE_DATE
                                    INNER JOIN CHECK_STOCK_LIST                 S_LIST
                                        ON S_DQ_MIN.TS_CODE     = S_LIST.TS_CODE
                                WHERE
                                    1=1
                                    AND S_T_MIN.TRADE_DATE_DT   < '''+strEndDate+'''
                                    AND S_T_MIN.TRADE_DATE_DT   >= DATE_SUB('''+strEndDate+''', INTERVAL '''+str(intCalPeriod)+''' DAY)
                            ) MIN_MID
                        WHERE
                            MIN_MID.RNK = 1
                    )
                    UNION ALL
                    (
                        -- 计算当前收盘价
                        SELECT
                            MAX_MID.TS_CODE,
                            NULL                AS TRADE_DAY,
                            NULL                AS MIN_COLSE,
                            NULL                AS MAX_COLSE,
                            NULL                AS MIN_PCT_CHG,
                            NULL                AS MAX_PCT_CHG,
                            NULL                AS DOWN_DAY,
                            NULL                AS UP_DAY,
                            NULL                AS BEGIN_CLOSE,
                            MAX_MID.CLOSE       AS END_CLOSE,
                            MAX_MID.PCT_CHG     AS END_CLOSE_PCT_C
                        FROM
                            (
                                SELECT
                                    S_DQ_MAX.TS_CODE,
                                    S_DQ_MAX.CLOSE,
                                    S_DQ_MAX.PCT_CHG,
                                    RANK() OVER(PARTITION BY S_DQ_MAX.TS_CODE ORDER BY S_T_MAX.TRADE_DATE_DT DESC)   AS RNK
                                FROM
                                    TB_CALENDAR                                 S_T_MAX
                                    INNER JOIN TB_DAILY_STOCK_QUOTE             S_DQ_MAX
                                        ON S_T_MAX.TRADE_DATE   = S_DQ_MAX.TRADE_DATE
                                    INNER JOIN CHECK_STOCK_LIST                 S_LIST
                                        ON S_DQ_MAX.TS_CODE     = S_LIST.TS_CODE
                                WHERE
                                    1=1
                                    AND S_T_MAX.TRADE_DATE_DT   < '''+strEndDate+'''
                                    AND S_T_MAX.TRADE_DATE_DT   >= DATE_SUB('''+strEndDate+''', INTERVAL '''+str(intCalPeriod)+''' DAY)
                            ) MAX_MID
                        WHERE
                            MAX_MID.RNK = 1
                    )
                ) TTL_U
                INNER JOIN CHECK_STOCK_LIST        S_L_O
                    ON TTL_U.TS_CODE    = S_L_O.TS_CODE
            GROUP BY
                S_L_O.TS_CODE,
                S_L_O.NAME,
                S_L_O.INDUSTRY,
                S_L_O.EXCHANGE
        '''
        dbConn.RunSQLOnce(strSQLInsert)

    # 计算趋势
    strSQLQuote = '''
        SELECT
            END_DATE,
            TS_CODE,
            NAME,
            INDUSTRY,
            EXCHANGE,
            MIN(END_CLOSE)              AS END_CLOSE,
            MIN(
                CASE
                    WHEN DAY_GAP = 720 THEN ALL_PCT_CHG
                    ELSE NULL
                END
            ) AS ALL_PCT_CHG_720,
            MIN(
                CASE
                    WHEN DAY_GAP = 360 THEN ALL_PCT_CHG
                    ELSE NULL
                END
            ) AS ALL_PCT_CHG_360,
            MIN(
                CASE
                    WHEN DAY_GAP = 180 THEN ALL_PCT_CHG
                    ELSE NULL
                END
            ) AS ALL_PCT_CHG_180,
            MIN(
                CASE
                    WHEN DAY_GAP = 90 THEN ALL_PCT_CHG
                    ELSE NULL
                END
            ) AS ALL_PCT_CHG_90,
            MIN(
                CASE
                    WHEN DAY_GAP = 30 THEN ALL_PCT_CHG
                    ELSE NULL
                END
            ) AS ALL_PCT_CHG_30,
            MIN(
                CASE
                    WHEN DAY_GAP = 5 THEN ALL_PCT_CHG
                    ELSE NULL
                END
            ) AS ALL_PCT_CHG_5,


            MIN(
                CASE
                    WHEN DAY_GAP = 720 THEN UP_DAY_PCT
                    ELSE NULL
                END
            ) AS UP_DAY_PCT_720,
            MIN(
                CASE
                    WHEN DAY_GAP = 360 THEN UP_DAY_PCT
                    ELSE NULL
                END
            ) AS UP_DAY_PCT_360,
            MIN(
                CASE
                    WHEN DAY_GAP = 180 THEN UP_DAY_PCT
                    ELSE NULL
                END
            ) AS UP_DAY_PCT_180,
            MIN(
                CASE
                    WHEN DAY_GAP = 90 THEN UP_DAY_PCT
                    ELSE NULL
                END
            ) AS UP_DAY_PCT_90,
            MIN(
                CASE
                    WHEN DAY_GAP = 30 THEN UP_DAY_PCT
                    ELSE NULL
                END
            ) AS UP_DAY_PCT_30,
            MIN(
                CASE
                    WHEN DAY_GAP = 5 THEN UP_DAY_PCT
                    ELSE NULL
                END
            ) AS UP_DAY_PCT_5,
            MIN(END_CLOSE_PCT_C)        AS END_CLOSE_PCT_C

        FROM
            TB_BT_MID_DAILY_PERIOD_TREND
        WHERE
            1=1
            AND END_DATE = '''+strEndDate+'''
        GROUP BY
            END_DATE,
            TS_CODE,
            NAME,
            INDUSTRY,
            EXCHANGE
        '''
    listQuote = dbConn.ReturnSQLData(strSQLQuote)

    # 针对不同的股票，看整体趋势，再看买还是卖
    for listStockQuote in listQuote:
        # 买入条件
        # 1. 比两年前是上升的
        # 2. 比一年前是上升的
        # 3. 比一个月前上升更下降的区间在 ± 0.38% 内
        # 4. 比昨天上涨 0.1% 以上 0.5% 以下
        if listStockQuote[18] is not None and listStockQuote[6]>0 and listStockQuote[7]>0 and listStockQuote[10]<0.38 and listStockQuote[10]>-0.38 and listStockQuote[18]>0.1 and listStockQuote[18]<0.5:
            # 判断有没有持仓，有则不买
            strSQLSell = "SELECT * FROM tb_bt_daily_tmp_my_stock_pool WHERE stock_code='" + listStockQuote[1]+"' AND bt_ind = '"+str(btDaily.intInd)+"'"
            listSell = dbConn.ReturnSQLData(strSQLSell)
            if len(listSell) == 0 :
                btDaily.BuyStock(strNowDate, listStockQuote[1], 1000,'追涨')
        # 如果当前下跌 5% 则全卖出
        if listStockQuote[18] is not None and listStockQuote[18]<-0.5 :
            strSQLSell = "SELECT * FROM tb_bt_daily_tmp_my_stock_pool WHERE stock_code='" + listStockQuote[1]+"' AND bt_ind = '"+str(btDaily.intInd)+"'"
            listSell = dbConn.ReturnSQLData(strSQLSell)
            if len(listSell) > 0 and listSell[0][0] is not None:
                btDaily.SellStock(strNowDate, listStockQuote[1], listSell[0][2], '全部卖了！！！')


# 打印快照
btDaily.AssetSnapshot(strMaxDate)


```



# 结果

不得不说，结果实在让人忧伤，我决定就不展示了

反正亏得裤衩都不剩了

