---
layout: post
title:  拾玖-Python 异步 爬虫 MongoDB 等事宜实施
date:   2020.01.31 13:59:40 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
description: MongoDB 感觉很难用
---


# 1. 综述
根据前期的 `NodeJS` `代理池`开展的`爬虫`终于有所进展，技术实现已初步实现，相关引用`资料`如下所示：


- 爬虫
http://c.biancheng.net/view/2011.html

- 链接MongoDB
https://blog.51cto.com/1767340368/2092960

- 超时
https://www.cnblogs.com/gl1573/p/10129382.html

- 异步处理
https://blog.csdn.net/tinyzhao/article/details/52684473
             //这不懂，做不了异步
https://github.com/Blackyukun/IPProxyPool
                           //这是有些代码
https://segmentfault.com/a/1190000008814676?utm_source=tag-newest
   //这可以了
https://www.cnblogs.com/shenh/p/9090586.html
                        //最大并发数
https://docs.aiohttp.org/en/stable/client_advanced.html
             //没啥事还是得去看官方文档

# 2.代码及注释

```python
'''
@Descripttion: 爬虫测试页面
@Author: BerryBC
@Date: 2020-01-30 15:00:34
@LastEditors  : BerryBC
@LastEditTime : 2020-01-31 13:53:19
'''
# 超时
# https://www.cnblogs.com/gl1573/p/10129382.html

# 爬虫
# http://c.biancheng.net/view/2011.html

# 链接MongoDB
# https://blog.51cto.com/1767340368/2092960

# 异步处理
# https://blog.csdn.net/tinyzhao/article/details/52684473             //这不懂，做不了异步
# https://github.com/Blackyukun/IPProxyPool                           //这是有些代码
# https://segmentfault.com/a/1190000008814676?utm_source=tag-newest   //这可以了
# https://www.cnblogs.com/shenh/p/9090586.html                        //最大并发数
# https://docs.aiohttp.org/en/stable/client_advanced.html             //没啥事还是得去看官方文档


# 引用库
import requests
import time
import pymongo
from bs4 import BeautifulSoup
from target import arrTarget
import random
import time
import asyncio
import aiohttp

# 链接 MongoDB 的链接信息以及引用相关的表
myclient = pymongo.MongoClient("mongodb://localhost:27017/")
mydb = myclient["dbProxy"]
mydb.authenticate('Berry', 'Berry')
mycol = mydb["tbProxy"]

# 读取 MongoDB 中的代理信息
arrProxy = []
# 其中从数据库中读取 fail 次数小于等于8次的，只读取 u 跟 p 字段，按 fail 倒序排序
for x in mycol.find({"fail": {"$lte": 8}}, {"_id": 0, "u": 1, "p": 1}).sort("fail", -1):
    arrProxy.append(x)

# 传入给 aiohttp 用的信息
url = 'http://***.******.***/'
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.110 Safari/537.36'}
intProxyLen = len(arrProxy)

# 声明拼死干活的函数
async def funSpyWeb(eleWeb, inSemaphore):
    # 限制并发数
    async with inSemaphore:
        bolRetry = True
        intTryTime = 0
        url = eleWeb
        try:
            async with aiohttp.ClientSession() as session:
                # 判断是否需要重试以及是否所有代理均已用完
                while (bolRetry and (intTryTime < intProxyLen)):
                    try:
                        # 导入代理
                        real_proxy = "http://" + \
                            arrProxy[intTryTime]["u"] + \
                            ":"+arrProxy[intTryTime]["p"]
                        # 异步请求网页内容
                        async with session.get(url, proxy=real_proxy, timeout=5,headers=headers) as response:
                            # 如果成功了，这是测试，所以就暂时不管是否成功，只要不超时就行了
                            # if response.status == 200:
                            #     self._conn.put(proxy)
                            #     print('Valid proxy', proxy)
                            strhtml = await response.text()
                            # 列出所有链接，方便以后处理
                            soup = BeautifulSoup(strhtml, 'lxml')
                            data = soup.select('a')
                            # for item in data:
                            #     result = {
                            #         'title': item.get_text(),
                            #         'link': item.get('href')
                            #     }
                            #     print(result)
                            # 如果链接不超时即完成。
                            bolRetry = False
                            print("  After " + str(intTryTime) +
                                    " time, success reach " + url)
                            # print(data[0].get_text())
                    except Exception as e:
                        intTryTime += 1
                        # print('    Fail ' + str(intTryTime) + ' time')
        except Exception as e:
            print(e)

# 这里就只是调用而已，并且记录开始结束时间
print('Begin : '+time.strftime('%Y-%m-%d %H:%M:%S'))
loop = asyncio.get_event_loop()
# 定义并发线程数
semaphore = asyncio.Semaphore(20)
# 把待爬 URL 放进其中
waittask = asyncio.gather(
    *([funSpyWeb(strWebSite, semaphore) for strWebSite in arrTarget]))
loop.run_until_complete(waittask)
loop.close()
print('Program End : '+time.strftime('%Y-%m-%d %H:%M:%S'))


# --------- 以下不懂，失败尝试-------------
# class classSpy():
#     def __init__(self, arrInProxy):
#         self.arrProxy = iter(arrInProxy)

#     def __aiter__(self):
#         return self

#     async def __anext__(self):
#         try:
#             eleProxy = next(self.arrProxy)
#         except StopIteration:
#             raise StopAsyncIteration
#         return eleProxy
# arrTmp = []
# arrTmp.append(1)
# arrTmp.append(2)
# arrTmp.append(3)
# arrTmp.append(4)
# arrTmp.append(5)
# arrTmp.append(6)
# async def run():
#     print('Begin : '+time.strftime('%Y-%m-%d %H:%M:%S'))
#     async for eleBe in classSpy(arrTmp):
#         await asyncio.sleep(random.randint(1, 3))
#         print('  Now : ' + str(eleBe) + ' , time: ' +
#             time.strftime('%Y-%m-%d %H:%M:%S'))
#     print('End : '+time.strftime('%Y-%m-%d %H:%M:%S'))
# loop = asyncio.get_event_loop()
# loop.run_until_complete(run())
# ------------------------------------------


# --------- 以下为异步尝试成功-------------
# arrTmp = []
# arrTmp.append(1)
# arrTmp.append(2)
# arrTmp.append(3)
# arrTmp.append(4)
# arrTmp.append(5)
# arrTmp.append(6)
# async def run(eleBe,inSemaphore):
#     async with inSemaphore:
#         await asyncio.sleep(random.randint(1, 3))
#         print('  Now : ' + str(eleBe) + ' , time: ' +
#             time.strftime('%Y-%m-%d %H:%M:%S'))
# def funDone(waittask):
#     print('Callback End : '+time.strftime('%Y-%m-%d %H:%M:%S'))
# print('Begin : '+time.strftime('%Y-%m-%d %H:%M:%S'))

# # -------------调用方式1----------------
# async def main():
#     semaphore=asyncio.Semaphore(2)
#     waittask =asyncio.gather (*( [run(proxy,semaphore) for proxy in arrTmp]))
#     waittask.add_done_callback(funDone)
#     await asyncio.gather(waittask)
# asyncio.run(main())
# # -------------------------------------


# # -------------调用方式2----------------
# loop = asyncio.get_event_loop()
# semaphore=asyncio.Semaphore(2)
# waittask =asyncio.gather (*( [run(proxy,semaphore) for proxy in arrTmp]))
# waittask.add_done_callback(funDone)
# loop.run_until_complete(waittask)
# loop.close()
# # -------------------------------------


# print('Program End : '+time.strftime('%Y-%m-%d %H:%M:%S'))
# ------------------------------------------

```

# 3.输出结果

```
  ...
  After 2 time, success reach http://nx.cntour.cn/
  After 48 time, success reach http://www.cntour.cn/usercenter/default.aspx
  After 48 time, success reach http://bj.cntour.cn/
  After 48 time, success reach http://nmg.cntour.cn/
  After 48 time, success reach http://cntour.cn
  After 97 time, success reach http://sd.cntour.cn/
  After 97 time, success reach http://sh.cntour.cn/
  After 58 time, success reach http://sc.cntour.cn/
  After 58 time, success reach http://us.cntour.cn/
  After 21 time, success reach http://jp.cntour.cn/
  After 21 time, success reach http://www.cntour.cn/domestic/237/
  After 87 time, success reach http://mc.cntour.cn/
  ...
```

# 4.后续思考

其实主要做的当然是把`爬`出来的 `URL` 放进`数据库`，然后`标识时间`，定期情理。

每次爬的都是少量，可以尝试爬`门户网站`或者`社交网站`等。

之后当然要考虑爬相关网站时，如何区分`已爬`信息跟`未爬`信息，这个可能需要重点考虑或者考虑`个性化`。