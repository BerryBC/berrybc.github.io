---
layout: post
title:  柒拾叁- 数据分析师都在干嘛？
date:   2024-05-05 16:53:12 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---


# 1. 数据分析师

其实很多人对这个词会有不同的 `定义`，更有甚者很多`业务部门`也会专门 **成立** 一个组来进行自己业务的 `数据分析`。

这岗位最根本的 `工作` ( 或者叫 `行为` 吧 )，就是处理 **因业务而产生的数据** ，然后给反馈 ( 提供业务相关 **洞察与建议** 或 现在业务的 **现状** ) 。

--

有点类似 [《Business intelligence 到底是什么》](https://berrybc.github.io/%E4%B8%89%E4%B8%96%E4%B9%A6%E8%AE%B0%E5%BD%95/63-What-is-business-intelligence-exactly/)

# 2. 工作产出

数据分析有一个很重要的概念就是 `量化` ，但工作过的人都知道，越是决策类的工作，内容越难的 `量化产出`。

如果常规的 BI 类的工作还可以通过如 **报表产出量、回答问题数**。

但如果更多是进行 **数据挖掘、沟通访谈、逻辑假设验证等** 的数据分析则如何更好的量化呢？

--

我决定先跳过量化 `产出` 这个概念，先量化我的 `工作内容`。

其实基本只能通过 `工作时间` ( 就像软件开发的 `人天` 概念 )，来看看数据分析师到底 **干了什么** ， **工作重点** 在哪里。

# 3. 抓取工作时间代码

> 我一般使用 [VSCode](https://code.visualstudio.com/) 的一个插件 [WakaTime](https://wakatime.com/dashboard) 去看我在写代码对于不同项目的花费时间。
> 但此处我就不展示了，毕竟没啥特别。

我为了记录我每天干了啥，我决定简单的用 `Pyhton` 和 `MySQL` 简单记录我每天干了啥 (就有点像 HR 监控每个人电脑干了啥一样)。

#### 建立记录表的 DDL

```SQL
CREATE DATABASE `db_logcomputer` /*!40100 COLLATE 'utf8_general_ci' */;

CREATE TABLE `tb_logwindowsname` (
    `title_name` VARCHAR(400) NULL DEFAULT NULL COLLATE 'utf8_general_ci',
    `begin_time` TIMESTAMP NULL DEFAULT NULL,
    `end_time` TIMESTAMP NULL DEFAULT NULL
)
COLLATE='utf8_general_ci'
ENGINE=InnoDB
;
```

#### 简单自启动以及自我循环的 Python 代码

选择用 Pyhton ，单纯因为公司对 Python 比较上脑 ( 在此就不论语言的优劣，单纯他是一个方便的 `脚本语言` 而已 )

```python
'''
Descripttion: 记录自己是否摸鱼
Version: 1.0
Author: BerryBC
Date: 2024-02-28 19:17:46
LastEditors: BerryBC
LastEditTime: 2024-05-05 17:34:11
'''
import pygetwindow as gw
import mysql.connector
import datetime
import time
import asyncio


async def funLogTime():

    # 记录数据库信息
    strDBPW = 'ILoveTues'
    strDBUser = 'Watsons'
    strPort = 1024
    strDBName = 'db_logcomputer'
    strDBHost = '127.0.0.1'

    # 获取当前窗口的标题
    strTitle=''
    try:
        strTitle = gw.getActiveWindow().title
    except:
        strTitle =''
    strTitleNew = strTitle
    timeBegin = datetime.datetime.now()
    timeEnd = timeBegin

    # 不断循环自身
    while True:
        # 获取当前窗口标题
        try:
            strTitleNew = gw.getActiveWindow().title
        except:
            strTitleNew =''
        # 记录当前时间作为 End time
        timeEnd = datetime.datetime.now()

        # 如果仍然在当前的窗口，以及在5分钟内，则不重新记录
        if strTitleNew != strTitle or (timeEnd-timeBegin).seconds > 300:

            try:
                # 我就直接记录而已，插入！插入数据库！插入！
                conn = mysql.connector.connect(
                    host=strDBHost, user=strDBUser, password=strDBPW, port=strPort, database=strDBName)
                cursor = conn.cursor()
                strSQL = "INSERT INTO tb_logwindowsname (title_name, begin_time, end_time) VALUES ('"+str(
                    strTitle)+"', '"+timeBegin.strftime("%Y-%m-%d %H:%M:%S")+"', '"+timeEnd.strftime("%Y-%m-%d %H:%M:%S")+"')"
                cursor.execute(strSQL)
                conn.commit()
                cursor.close()
                conn.close()
                timeBegin = timeEnd
                strTitle = strTitleNew
            except:
                pass
        # 等几秒重新运行我自己
        await asyncio.sleep(5)
# 因为 Windows 有些限制（电脑我也没管理员权限），所以只能延后启动
time.sleep(360)
asyncio.run(funLogTime())
```


#### 输出工作内容

```sql

SELECT
    DATE_REC,
    ACTIVITY,
    WORK_HOURS
FROM
    (
        (
            SELECT
                DATE(BEGIN_TIME)                                                AS DATE_REC,
                '  --  Total'                                                   AS ACTIVITY,
                TIMESTAMPDIFF(SECOND,MIN(BEGIN_TIME),MAX(END_TIME)) / 60 / 60   AS WORK_HOURS
            FROM
                TB_LOGWINDOWSNAME
            WHERE
                -- BEGIN_TIME >= DATE'2024-2-29'
                -- AND BEGIN_TIME < DATE_ADD(DATE'2024-2-29',INTERVAL 1 DAY)
                BEGIN_TIME >= DATE(NOW())
            GROUP BY
                DATE(BEGIN_TIME)
        )
        UNION ALL
        (
            SELECT
                DATE(BEGIN_TIME)                    AS DATE_REC,
                CASE
                    WHEN TITLE_NAME IN ('','Windows 默认锁屏界面')
                        AND DATE_FORMAT(BEGIN_TIME, '%H:%min') BETWEEN '12:30' AND '14:00'      THEN '中午吃饭'
                    WHEN TITLE_NAME IN ('','Windows 默认锁屏界面')      THEN '摸鱼 或 开会'
                    WHEN TITLE_NAME LIKE '%- Excel'                     THEN 'Excel'
                    WHEN TITLE_NAME LIKE '%- Excel (未响应)'             THEN 'Excel'
                    WHEN TITLE_NAME LIKE '%- Google Chrome'             THEN 'Chrome'
                    WHEN TITLE_NAME LIKE '%- Visual Stoudio Code'       THEN 'VSCode'
                    WHEN TITLE_NAME LIKE '%- Visual Studio Code'        THEN 'VSCode'
                    WHEN TITLE_NAME LIKE '%- Outlook'                   THEN 'Outlook'
                    WHEN TITLE_NAME IN ('Microsoft Outlook','Outlook')  THEN 'Outlook'
                    WHEN TITLE_NAME LIKE '%- PL/SQL Developer -%'       THEN 'PLSQL'
                    WHEN TITLE_NAME LIKE '%- PL/SQL Developer'          THEN 'PLSQL'
                    WHEN TITLE_NAME LIKE '%- HeidiSQL Portable%'        THEN 'MySQL'
                    WHEN TITLE_NAME LIKE '%| Microsoft Teams'           THEN 'Teams'
                    WHEN TITLE_NAME LIKE '%的聊天记录%'                  THEN '钉钉'
                    WHEN TITLE_NAME IN ('聊天记录','钉钉')               THEN '钉钉'
                    WHEN TITLE_NAME IN ('微信')                         THEN '微信'
                    WHEN TITLE_NAME LIKE '%分享的图片%'                  THEN '钉钉'
                    WHEN TITLE_NAME LIKE 'Tableau - %'                  THEN 'Tableau'
                    WHEN TITLE_NAME LIKE 'FineReport %'                  THEN 'FineReport'
                    WHEN TITLE_NAME IN ('计算器')                       THEN '计算器'
                    -- ELSE TITLE_NAME
                    ELSE 'Others'
                END AS ACTIVITY,
                SUM(TIMESTAMPDIFF(SECOND,BEGIN_TIME,END_TIME)) / 60 / 60    AS WORK_HOURS
            FROM
                TB_LOGWINDOWSNAME
            WHERE
                -- BEGIN_TIME >= DATE'2024-2-29'
                -- AND BEGIN_TIME < DATE_ADD(DATE'2024-2-29',INTERVAL 1 DAY)
                BEGIN_TIME >= DATE(NOW())
            GROUP BY
                CASE
                    WHEN TITLE_NAME IN ('','Windows 默认锁屏界面')
                        AND DATE_FORMAT(BEGIN_TIME, '%H:%min') BETWEEN '12:30' AND '14:00'      THEN '中午吃饭'
                    WHEN TITLE_NAME IN ('','Windows 默认锁屏界面')      THEN '摸鱼 或 开会'
                    WHEN TITLE_NAME LIKE '%- Excel'                     THEN 'Excel'
                    WHEN TITLE_NAME LIKE '%- Excel (未响应)'             THEN 'Excel'
                    WHEN TITLE_NAME LIKE '%- Google Chrome'             THEN 'Chrome'
                    WHEN TITLE_NAME LIKE '%- Visual Stoudio Code'       THEN 'VSCode'
                    WHEN TITLE_NAME LIKE '%- Visual Studio Code'        THEN 'VSCode'
                    WHEN TITLE_NAME LIKE '%- Outlook'                   THEN 'Outlook'
                    WHEN TITLE_NAME IN ('Microsoft Outlook','Outlook')  THEN 'Outlook'
                    WHEN TITLE_NAME LIKE '%- PL/SQL Developer -%'       THEN 'PLSQL'
                    WHEN TITLE_NAME LIKE '%- PL/SQL Developer'          THEN 'PLSQL'
                    WHEN TITLE_NAME LIKE '%- HeidiSQL Portable%'        THEN 'MySQL'
                    WHEN TITLE_NAME LIKE '%| Microsoft Teams'           THEN 'Teams'
                    WHEN TITLE_NAME LIKE '%的聊天记录%'                  THEN '钉钉'
                    WHEN TITLE_NAME IN ('聊天记录','钉钉')               THEN '钉钉'
                    WHEN TITLE_NAME IN ('微信')                         THEN '微信'
                    WHEN TITLE_NAME LIKE '%分享的图片%'                  THEN '钉钉'
                    WHEN TITLE_NAME LIKE 'Tableau - %'                  THEN 'Tableau'
                    WHEN TITLE_NAME LIKE 'FineReport %'                  THEN 'FineReport'
                    WHEN TITLE_NAME IN ('计算器')                       THEN '计算器'
                    -- ELSE TITLE_NAME
                    ELSE 'Others'
                END,
                DATE(BEGIN_TIME)
        )
    ) TTL
ORDER BY
    WORK_HOURS DESC
```


# 4. 工作分类

#### 先看总工作时间

![alt text](/photo/InPost/SanShiShu/73/image1.png)

唉，我就是说明多么可怜，但实际上我们是朝九晚六，所以我们其实整体来说算上中午休息1个小时，在公司的总时间在9个小时。

#### 整体工作内容

![alt text](/photo/InPost/SanShiShu/73/image2.png)

按占比降序逐项来研究一下:

1. **开会** - 唉..就是每天开很多会，跟很多不同的人谈事情，怎么办嘛
2. **即时聊天工具** - 就是开会的延续
3. **Excel** - 对即时查出的数据进行快速的可视化、明细，找出对数据的感觉
4. **VSCode** - 做简单 ETL 或 数据处理代码编写
5. **Chrome** - 就 Google 嘛....
6. **数据库探查** - 对 ODS 层数据进行探查

#### 按日期工作内容

![alt text](/photo/InPost/SanShiShu/73/image3.png)

每天的工作都不同~好辛苦呢~

#### 小结

我们会有专注不同方向的同事，例如 :

- 有同事较多使用如 Tableau 、Fine Report、Power BI 等 `BI 报表`工作
- 有同事较多使用如 代码编写 等 `ETL` 相关工作
- 有同事可能直接数据库都不需要打开，就直接在数据结果的层面进行 `思考、结果整理、逻辑推导、用结果说服业务` 等工作

--

但整体来说，与业务进行更多的 `会议沟通`、参加 `业务的思考` 以及 `业务流程的理解` 是 **`数据分析师`** 更重要的工作。

而数据分析师的输出肯定也不是一份份 **报表** ( 虽然这可能是一个媒介 ) ， 而是给出对业务的一个 `清晰的认知` 以及未来可能的 `方向前景如何` 。

# 5. 总结

很多公司对 `数据分析师` 的概念不一致，甚至其实只是一个文员就叫做 `数据分析师` 。

其实也没错，毕竟，重要 `CEO` 其实如果抛开其`做决策的职责`，只看输入和输出，其实也跟一个文员无异。