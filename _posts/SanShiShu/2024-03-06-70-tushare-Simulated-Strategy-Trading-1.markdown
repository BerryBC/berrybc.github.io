---
layout: post
title:  柒拾- tushare 模拟策略交易 （一）
date:   2024-03-06 19:41:13 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---

## 一、背景


过年无聊在家，想上网获取各公司的`损益表`，结果搜着搜着看到一个平台好像有些数据，平台叫 [《Tushare》](https://tushare.pro/)。

然后看着看着里面的文档，诶 ~ 居然有模拟交易 ~~ ，但看着看着，发现里面居然没有完整代码。

于是自己照着他的来学习一下啥是[量化分析](https://tushare.pro/document/1?doc_id=90)。


## 二、建个数据库

首先装好 MySQL ，然后按照文章上的内容先建好各种表：

```sql
/*
 *Descripttion:
 *Version: 1.0
 *Author: BerryBC
 *Date: 2024-03-06 18:48:28
 *LastEditors: BerryBC
 *LastEditTime: 2024-03-06 19:02:26
*/
-- --------------------------------------------------------
-- 主机:                           127.0.0.1
-- 服务器版本:                        8.0.12 - MySQL Community Server - GPL
-- 服务器操作系统:                      Win64
-- HeidiSQL 版本:                  11.1.0.6116
-- --------------------------------------------------------

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET NAMES utf8 */;
/*!50503 SET NAMES utf8mb4 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;


-- 导出 db_tushare_stock 的数据库结构
CREATE DATABASE IF NOT EXISTS `db_tushare_stock` /*!40100 DEFAULT CHARACTER SET utf8 */;
USE `db_tushare_stock`;

-- 导出  表 db_tushare_stock.tb_all_stock_list 结构
-- -*-*-*-*-*-*-*-*-*-*-*-*
-- 这个是存全量股票的
-- -*-*-*-*-*-*-*-*-*-*-*-*
CREATE TABLE IF NOT EXISTS `tb_all_stock_list` (
  `ind` int(11) DEFAULT NULL,
  `ts_code` varchar(50) DEFAULT NULL COMMENT 'TS代码',
  `symbol` varchar(50) DEFAULT NULL COMMENT '股票代码',
  `name` varchar(200) DEFAULT NULL COMMENT '股票名称',
  `area` varchar(100) DEFAULT NULL COMMENT '地域',
  `industry` varchar(200) DEFAULT NULL COMMENT '所属行业',
  `fullname` varchar(400) DEFAULT NULL COMMENT '股票全称',
  `enname` varchar(400) CHARACTER SET utf8 COLLATE utf8_general_ci DEFAULT NULL COMMENT '英文全称',
  `cnspell` varchar(200) DEFAULT NULL COMMENT '拼音缩写',
  `market` varchar(200) DEFAULT NULL COMMENT '市场类型',
  `exchange` varchar(200) DEFAULT NULL COMMENT '交易所代码',
  `curr_type` varchar(50) DEFAULT NULL COMMENT '交易货币',
  `list_status` varchar(50) DEFAULT NULL COMMENT '上市状态 L上市 D退市 P暂停上市',
  `list_date` varchar(50) DEFAULT NULL COMMENT '上市日期',
  `delist_date` varchar(50) DEFAULT NULL COMMENT '退市日期',
  `is_hs` varchar(50) DEFAULT NULL COMMENT '是否沪深港通标的，N否 H沪股通 S深股通',
  `act_name` varchar(400) DEFAULT NULL COMMENT '实控人名称',
  `act_ent_type` varchar(200) DEFAULT NULL COMMENT '实控人企业性质'
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

-- 数据导出被取消选择。

-- 导出  表 db_tushare_stock.tb_bt_daily_cfg 结构
-- -*-*-*-*-*-*-*-*-*-*-*-*
-- 存回测策略的记录表
-- -*-*-*-*-*-*-*-*-*-*-*-*
CREATE TABLE IF NOT EXISTS `tb_bt_daily_cfg` (
  `bt_ind` int(11) DEFAULT NULL,
  `bt_title` varchar(200) DEFAULT NULL,
  `bt_desc` text,
  `bt_init_cash` float DEFAULT NULL,
  `bt_time` timestamp NULL DEFAULT NULL,
  UNIQUE KEY `bt_ind` (`bt_ind`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

-- 数据导出被取消选择。

-- 导出  表 db_tushare_stock.tb_bt_daily_my_capital 结构
-- -*-*-*-*-*-*-*-*-*-*-*-*
-- 存每次交易数据
-- -*-*-*-*-*-*-*-*-*-*-*-*
CREATE TABLE IF NOT EXISTS `tb_bt_daily_my_capital` (
  `capital` float DEFAULT NULL COMMENT '总资产',
  `money_lock` float DEFAULT NULL COMMENT '股票资产',
  `money_rest` float DEFAULT NULL COMMENT '现金资产',
  `deal_action` varchar(45) CHARACTER SET utf8 COLLATE utf8_general_ci DEFAULT NULL COMMENT '交易动作',
  `stock_code` varchar(45) CHARACTER SET utf8 COLLATE utf8_general_ci DEFAULT NULL COMMENT '股票代码',
  `deal_price` float DEFAULT NULL COMMENT '成交价',
  `stock_vol` int(11) DEFAULT NULL COMMENT '成交量',
  `profit` float DEFAULT NULL COMMENT '收益额',
  `profit_rate` float DEFAULT NULL COMMENT '收益率',
  `comment` varchar(45) CHARACTER SET utf8 COLLATE utf8_general_ci DEFAULT NULL COMMENT '备注',
  `state_dt` varchar(45) CHARACTER SET utf8 COLLATE utf8_general_ci DEFAULT NULL COMMENT '交易日期',
  `seq` int(11) NOT NULL AUTO_INCREMENT COMMENT '序号（用作表主键）',
  `bt_ind` int(11) DEFAULT NULL,
  UNIQUE KEY `seq` (`seq`),
  KEY `bt_ind` (`bt_ind`),
  KEY `state_dt_bt_ind` (`state_dt`,`bt_ind`)
) ENGINE=InnoDB AUTO_INCREMENT=5681 DEFAULT CHARSET=utf8;

-- 数据导出被取消选择。

-- 导出  表 db_tushare_stock.tb_bt_daily_tmp_my_stock_pool 结构
-- -*-*-*-*-*-*-*-*-*-*-*-*
-- 当此回测的当前持仓
-- -*-*-*-*-*-*-*-*-*-*-*-*
CREATE TABLE IF NOT EXISTS `tb_bt_daily_tmp_my_stock_pool` (
  `stock_code` varchar(50) DEFAULT NULL COMMENT '股票代码',
  `buy_price` float DEFAULT NULL COMMENT '买入价',
  `hold_vol` int(11) DEFAULT NULL COMMENT '持仓量（单位：股）',
  `hold_days` int(11) DEFAULT NULL COMMENT '持仓天数（只计算交易日）',
  `bt_ind` int(11) DEFAULT NULL,
  KEY `bt_ind` (`bt_ind`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

-- 数据导出被取消选择。

-- 导出  表 db_tushare_stock.tb_bt_mid_daily_period_trend 结构
-- -*-*-*-*-*-*-*-*-*-*-*-*
-- 中间表 - 简单 By 天计算趋势
-- -*-*-*-*-*-*-*-*-*-*-*-*
CREATE TABLE IF NOT EXISTS `tb_bt_mid_daily_period_trend` (
  `DAY_GAP` bigint(20) DEFAULT NULL,
  `END_DATE` longtext CHARACTER SET latin1,
  `TS_CODE` varchar(50) DEFAULT NULL COMMENT 'TS代码',
  `NAME` varchar(200) DEFAULT NULL COMMENT '股票名称',
  `INDUSTRY` varchar(200) DEFAULT NULL COMMENT '所属行业',
  `EXCHANGE` varchar(200) DEFAULT NULL COMMENT '交易所代码',
  `TRADE_DAY` bigint(21) DEFAULT NULL,
  `MIN_COLSE` float DEFAULT NULL,
  `MAX_COLSE` float DEFAULT NULL,
  `MIN_PCT_CHG` float DEFAULT NULL,
  `MAX_PCT_CHG` float DEFAULT NULL,
  `DOWN_DAY` bigint(21) DEFAULT NULL,
  `UP_DAY` bigint(21) DEFAULT NULL,
  `BEGIN_CLOSE` float DEFAULT NULL,
  `END_CLOSE` float DEFAULT NULL,
  `ALL_PCT_CHG` double DEFAULT NULL,
  `UP_DAY_PCT` decimal(24,4) DEFAULT NULL,
  `DOWN_DAY_PCT` decimal(24,4) DEFAULT NULL,
  `END_CLOSE_PCT_C` float DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

-- 数据导出被取消选择。

-- 导出  表 db_tushare_stock.tb_calendar 结构
-- -*-*-*-*-*-*-*-*-*-*-*-*
-- 计算交易日期
-- -*-*-*-*-*-*-*-*-*-*-*-*
CREATE TABLE IF NOT EXISTS `tb_calendar` (
  `trade_date` varchar(50) DEFAULT NULL COMMENT '交易日期',
  `trade_date_dt` timestamp NULL DEFAULT NULL COMMENT '交易日期(日期格式)'
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

-- 数据导出被取消选择。

-- 导出  表 db_tushare_stock.tb_cfg 结构
-- -*-*-*-*-*-*-*-*-*-*-*-*
-- 整体配置
-- -*-*-*-*-*-*-*-*-*-*-*-*
CREATE TABLE IF NOT EXISTS `tb_cfg` (
  `field_key` varchar(200) CHARACTER SET utf8 COLLATE utf8_general_ci DEFAULT NULL,
  `field_value` varchar(200) DEFAULT NULL,
  `update_date` timestamp NULL DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

-- 数据导出被取消选择。

-- 导出  表 db_tushare_stock.tb_daily_stock_quote 结构
-- -*-*-*-*-*-*-*-*-*-*-*-*
-- 股票行情，直接获取
-- -*-*-*-*-*-*-*-*-*-*-*-*
CREATE TABLE IF NOT EXISTS `tb_daily_stock_quote` (
  `ts_code` varchar(50) DEFAULT NULL COMMENT '股票代码',
  `trade_date` varchar(50) DEFAULT NULL COMMENT '交易日期',
  `open` float DEFAULT NULL COMMENT '开盘价',
  `high` float DEFAULT NULL COMMENT '最高价',
  `low` float DEFAULT NULL COMMENT '最低价',
  `close` float DEFAULT NULL COMMENT '收盘价',
  `pre_close` float DEFAULT NULL COMMENT '昨收价',
  `change` float DEFAULT NULL COMMENT '涨跌额',
  `pct_chg` float DEFAULT NULL COMMENT '涨跌幅',
  `vol` float DEFAULT NULL COMMENT '成交量',
  `amount` float DEFAULT NULL COMMENT '成交额',
  KEY `ts_code` (`ts_code`),
  KEY `ts_code_trade_date` (`ts_code`,`trade_date`),
  KEY `trade_date` (`trade_date`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

-- 数据导出被取消选择。

/*!40101 SET SQL_MODE=IFNULL(@OLD_SQL_MODE, '') */;
/*!40014 SET FOREIGN_KEY_CHECKS=IF(@OLD_FOREIGN_KEY_CHECKS IS NULL, 1, @OLD_FOREIGN_KEY_CHECKS) */;
/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;


```


## 三、获取数据

需要看着文档来获取，在网站上注册完后，填完个人信息，就可以参考[数据获取文章](https://tushare.pro/document/1?doc_id=131)得到数据了。

为了方便，我**封装**了对`数据库`的操作以及简单的 `Tushare` 的调用。

代码如下：

```python
'''
Descripttion: 数据库链接库
Version: 1.0
Author: BerryBC
Date: 2024-02-18 12:12:35
LastEditors: BerryBC
LastEditTime: 2024-03-06 19:27:53
'''

import datetime
import time
import mysql.connector
from sqlalchemy import create_engine
import tushare


class claDBConn(object):
    def __init__(self):
        '''
        description: 初始化
        '''
        # 以下信息为系统配置赋值
        self.strDBPW = 'WatsonsBI'
        self.strDBUser = 'Berry'
        self.strPort = 1988
        self.strDBName = 'db_tushare_stock'
        self.strDBHost = '127.0.0.1'
        self.ts = tushare.pro_api('3245fd623dfsdf3hsfajhkh23k4jh34k52345ljh234o5h234io5uh345kljh')

    def RunSQLOnce(self, strSQL):
        '''
        description: 就单独运行一次代码
        param: strSQL 传入代码
        '''
        conn = mysql.connector.connect(
            host=self.strDBHost, user=self.strDBUser, password=self.strDBPW, port=self.strPort, database=self.strDBName)
        cursor = conn.cursor()
        cursor.execute(strSQL)
        conn.commit()
        cursor.close()
        conn.close()
        return True

    def ReturnSQLData(self, strSQL):
        '''
        description: 根据代码返回数据
        param: strSQL 传入代码
        return: {list}  返回输出的表, 以数组形式
        '''
        conn = mysql.connector.connect(
            host=self.strDBHost, user=self.strDBUser, password=self.strDBPW, port=self.strPort, database=self.strDBName)
        cursor = conn.cursor()
        cursor.execute(strSQL)
        listDataTable = cursor.fetchall()
        cursor.close()
        conn.close()
        return listDataTable

    def RetrunDBEngine(self):
        '''
        description: 创建引擎
        '''
        engMySQL = create_engine('mysql+pymysql://{}:{}@{}:{}/{}?charset=utf8'.format(
            self.strDBUser, self.strDBPW, self.strDBHost, self.strPort, self.strDBName))
        return engMySQL


    def RefreshDailyQuote(self):
        '''
        description: 更新数据库中的日数据
        return: {int}   0 成功
                        1 失败
        '''
        # 创建链接字符
        strDBEng=self.RetrunDBEngine()



        # 查询数据库最 Update 的时间
        strSQLMaxDate = "SELECT MAX(field_value) FROM tb_cfg WHERE field_key='DailyQuoteDate'"
        listMaxDate = self.ReturnSQLData(strSQLMaxDate)
        intMaxStartDate = ''

        # 如果不存在则从 2000年开始提取
        if listMaxDate is not None and listMaxDate[0][0] is not None:
            intMaxStartDate = listMaxDate[0][0]
        else:
            intMaxStartDate = '20000101'
            # intMaxStartDate = '20240101'

        # 获取日期的 Gap
        dateStart = datetime.datetime.strptime(intMaxStartDate, "%Y%m%d")
        dateEnd = datetime.datetime.now()
        intDayGap = (dateEnd - dateStart).days

        # 循环获取
        if intDayGap > 0 :
            for intDayAdd in range(intDayGap):
                strQueryDate = (dateStart+datetime.timedelta(days=(intDayAdd+1))).strftime('%Y%m%d')
                dfDateF = self.ts.daily(**{
                    "ts_code": "",
                    "trade_date": "",
                    "start_date": strQueryDate,
                    "end_date": strQueryDate,
                    "offset": "",
                    "limit": ""
                }, fields=[
                    "ts_code",
                    "trade_date",
                    "open",
                    "high",
                    "low",
                    "close",
                    "pre_close",
                    "change",
                    "pct_chg",
                    "vol",
                    "amount"
                ])
                dfDateF.to_sql('tb_daily_stock_quote', strDBEng, index=False, if_exists='append', chunksize=5000)
                time.sleep(0.1)
                print('Done - '+ strQueryDate + str(datetime.datetime.now()))


        # 更新当前最新时间
        strSQLDataMaxDate = "SELECT max(trade_date) FROM tb_daily_stock_quote"
        listDataMaxDate = self.ReturnSQLData(strSQLDataMaxDate)

        # 如果存在最新的数据，则记录，这里反复记录也没所谓
        if listDataMaxDate is not None and listDataMaxDate[0][0] is not None:
            intMaxStartDate = listDataMaxDate[0][0]
            strInsertMaxDataDateSQL="INSERT INTO tb_cfg (field_key, field_value,update_date) VALUES ('DailyQuoteDate', '"+intMaxStartDate+"' ,now());"
            self.RunSQLOnce(strInsertMaxDataDateSQL)

        strDBEng.dispose()
        return True

    def FixCalDay(self):
        '''
        description: 重新计算一个日历
        '''
        strTruncateCal="TRUNCATE TABLE tb_calendar"
        self.RunSQLOnce(strTruncateCal)

        strInsertCal="INSERT INTO tb_calendar SELECT trade_date,STR_TO_DATE(trade_date,'%Y%m%d') FROM tb_daily_stock_quote GROUP BY trade_date ORDER BY trade_date ASC"
        self.RunSQLOnce(strInsertCal)
        return True



    def GetAllStockBaseInfo(self):
        '''
        description: 更新股票基本信息
        '''
        # 创建链接字符
        strDBEng=self.RetrunDBEngine()
        # 清空原表
        strCleanTable='TRUNCATE TABLE tb_all_stock_list'
        self.RunSQLOnce(strCleanTable)
        # 插入获取的信息表
        dfDateF = self.ts.stock_basic(**{
            "ts_code": "",
            "name": "",
            "exchange": "",
            "market": "",
            "is_hs": "",
            "list_status": "",
            "limit": "",
            "offset": ""
        }, fields=[
            "ts_code",
            "symbol",
            "name",
            "area",
            "industry",
            "cnspell",
            "market",
            "list_date",
            "act_name",
            "act_ent_type",
            "fullname",
            "enname",
            "exchange",
            "curr_type",
            "list_status",
            "delist_date",
            "is_hs"
        ])
        dfDateF.to_sql('tb_all_stock_list', strDBEng, index=False, if_exists='append', chunksize=5000)
        return True
```

## 四、简单更新数据


通过代码，调用一下就可以更新数据了

```python
from Lib.DBConn import claDBConn

# 创建对象
dbConn = claDBConn()
# 获取基础的股票列表
dbConn.GetAllStockBaseInfo()
# 更新 By Day 的股票走势
dbConn.RefreshDailyQuote()
# 计算交易日期
dbConn.FixCalDay()
```


这里我就要吐槽一下，为什么交易日历的 API `https://tushare.pro/document/2?doc_id=26` 是要 2000 积分？

反而获取行情倒只需要 200分？

所以只能自己在 SQL 计算一下交易日期自己创建一个表

## 五、模拟交易

好了，老婆回来了，我得跟她去吃饭了，再见

说真的，股票行情这东西对于我来说也不会用，我其实是想要他获取各公司`损益表`的数据的，但需要积分是 2000积分

等我真要用的时候我再捐款给他们获取积分呗~不过学习一下其他东西倒挺好玩的