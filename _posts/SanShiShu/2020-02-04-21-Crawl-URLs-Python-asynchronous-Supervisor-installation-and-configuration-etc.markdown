---
layout: post
title:  廿壹-爬 URL 、Python 异步 、Supervisor 安装配置等事宜
date:   2020.02.04 14:24:53 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---


# 1.综述

`伪代码` 可以参考[《廿-爬URL以及分词情绪分析初步设想》](https://www.jianshu.com/p/537e1378db77)，才发现这个要做一整天才做得出来，不过大部分时间都是用来 `部署` 服务器。

代码放在[大型同性交友网站](https://github.com/BerryBC/SpyTheLink)

其实根据 `伪代码` 来写程序很简单，但主要有很多地方卡住，需要现学现卖，以下均为现学的点。

# 2.关键技术点

### 2.1 Python Class

关于 `Python` 的 `类`，对于其他 `脚本语言` 的类来说并无太大差异，但不知道是不是习惯了 `JS` 的闭包，写到 `Python` 上就会很多莫名其妙的错误。

而且其实本次并无 `继承` 方面的需求，所以纯粹是为了写 `类` 而写。

### 2.2 异步

`异步` 这东西用在 `Python` 上面简直是崩溃，所以我不懂为什么那么多人喜欢 `Python`（还是因为它是天生 `同步` 的所以才那么多人喜欢么？还是纯粹是因为它有很多包？）。

`Python` 异步比较多的是 `asyncio` 。

关于 `asyncio` ，我觉得最绝望的是，根本不知道怎么 `列队`！就是如何启动，我尝试过两种办法：

```python

# --------- 以下不懂，失败尝试-------------
class classSpy():
    def __init__(self, arrInProxy):
        self.arrProxy = iter(arrInProxy)

    def __aiter__(self):
        return self

    async def __anext__(self):
        try:
            eleProxy = next(self.arrProxy)
        except StopIteration:
            raise StopAsyncIteration
        return eleProxy


arrTmp = []
arrTmp.append(1)
arrTmp.append(2)
arrTmp.append(3)
arrTmp.append(4)
arrTmp.append(5)
arrTmp.append(6)


async def run():
    print('Begin : '+time.strftime('%Y-%m-%d %H:%M:%S'))
    async for eleBe in classSpy(arrTmp):
        await asyncio.sleep(random.randint(1, 3))
        print('  Now : ' + str(eleBe) + ' , time: ' +
              time.strftime('%Y-%m-%d %H:%M:%S'))
    print('End : '+time.strftime('%Y-%m-%d %H:%M:%S'))
loop = asyncio.get_event_loop()
loop.run_until_complete(run())
# ------------------------------------------


# --------- 以下为异步尝试成功-------------
arrTmp = []
arrTmp.append(1)
arrTmp.append(2)
arrTmp.append(3)
arrTmp.append(4)
arrTmp.append(5)
arrTmp.append(6)


async def run(eleBe, inSemaphore):
    async with inSemaphore:
        await asyncio.sleep(random.randint(1, 3))
        print('  Now : ' + str(eleBe) + ' , time: ' +
              time.strftime('%Y-%m-%d %H:%M:%S'))


def funDone(waittask):
    print('Callback End : '+time.strftime('%Y-%m-%d %H:%M:%S'))


print('Begin : '+time.strftime('%Y-%m-%d %H:%M:%S'))

# -------------调用方式1----------------


async def main():
    semaphore = asyncio.Semaphore(2)
    waittask = asyncio.gather(*([run(proxy, semaphore) for proxy in arrTmp]))
    waittask.add_done_callback(funDone)
    await asyncio.gather(waittask)
asyncio.run(main())
# -------------------------------------


# -------------调用方式2----------------
loop = asyncio.get_event_loop()
semaphore = asyncio.Semaphore(2)
waittask = asyncio.gather(*([run(proxy, semaphore) for proxy in arrTmp]))
waittask.add_done_callback(funDone)
loop.run_until_complete(waittask)
loop.close()
# -------------------------------------


print('Program End : '+time.strftime('%Y-%m-%d %H:%M:%S'))
# ------------------------------------------


```

其中我尝试了很久，然后最终还是得去参考官方文档。参考的文档如下：

 - 异步处理

[这不懂，做不了异步](https://blog.csdn.net/tinyzhao/article/details/52684473)

[这是有些代码](https://github.com/Blackyukun/IPProxyPool)

[这可以了](https://segmentfault.com/a/1190000008814676?utm_source=tag-newest)

[最大并发数](https://www.cnblogs.com/shenh/p/9090586.html)

[没啥事还是得去看官方文档](https://docs.aiohttp.org/en/stable/client_advanced.html)

很艰难终于能 `启动` 真正的 `异步` 处理了，如上述方法2，但是在 `多线程异步` 的时候，发现 `Loop` 在同一程序中不支持 `同时多次调用`，找了好久终于找到以下方法：

- 解决办法

[[Python]在一段Python程序中使用多次事件循环](https://segmentfault.com/a/1190000011013476)


### 2.2 Python 链接 MongoDB

这个真是一个大坑，例如
1. `find()` 函数 在 `Python` 中还是 `find()`
2. `findOne()` 函数在 `Python` 中就是 `find_one()`
3. `deleteMany()` 函数在 `Python` 中就是 `delete_many()`
4. 因为 `Python` 没有 `JSON`，所以只能用`字典`，如：{\$lt:10} 要写成 {'\$lt':10}


参考资料有：

[某个资料](https://blog.51cto.com/1767340368/2092960)


### 2.3 网页请求及解析

这个倒比较方便，直接用人家写好的就行了，参考资料如下：

[别人的资料](http://c.biancheng.net/view/2011.html)


### 2.4 超时重复请求

当有当前引用的代理 `超时` 的时候，请求另外的 `代理服务器`，但好像没有特定的处理办法，所以只有参考材料，做一个 `While` 做判断是否超时，相关代码如参考文章：

[参考文章](https://www.cnblogs.com/gl1573/p/10129382.html)


# 3.部署Python程序

这个是真的是一个巨坑，我都不想弄了，但没办法，都已经开始了。

`NodeJS` 我是用 `PM2` 的，`PM2` 其实也有一些坑，例如在 `Start` 一个项目后，需要 `Save` 再 `Startup` 以达到 `开机启动` 的环境，但是中间需要配置一个 `环境配置`，不然开机后仍然无法启动。

而 `Python` 的程序管理软件有一个 `Supervisord` ，从很久之前就在用这个，可是重新配置还是很麻烦的。

### 3.1 关于安装

在装这个软件时，我发现 `腾讯云` 服务器是没有预装 `Pip` 的，装完 `Pip` 之后再装 `Supervisord` 发现很多东西根本没有配置，直到找到这个文章：

[centos7安装supervisor详细教程](https://blog.csdn.net/donggege214/article/details/80264811)

可是我已经装好了，用了一个不推荐的方法，唉。


### 3.2 关于配置

没关系，装好之后，为什么 `配置` 那么麻烦。因为我是想看看我的程序输出，所以必须把 `托管程序` 的输出 `输出` 到某个目录下，配置好麻烦，以下是主要能实施我功能的`配置`。

[配置介绍](https://www.cnblogs.com/jasonkoo/articles/3750638.html)

```ini
[program:spylink]
command=python3 /home/Berry/Repositories/SpyTheLink/run.py ; 这个就是我的路径了，因为我用 Python3 写的，所以这个比较重要
directory=/home/Berry/Repositories/SpyTheLink              ; directory to cwd to before exec (def no cwd)，这个需要配置，不然配置文件找不到路径
autostart=true       ; start at supervisord start (default: true) ，这个是当然的
startsecs=10         ; number of secs prog must stay running (def. 1)
autorestart=true     ; whether/when to restart (default: unexpected)
startretries=0       ; max # of serial start failures (default 3)
user=Berry          ; setuid to this UNIX account to run the program，用Root不能运行我也不知道为什么，好像没设可运行？还是运行环境问题？
priority=999         ; the relative start priority (default 999)
redirect_stderr=false ; redirect proc stderr to stdout (default false)，因为我不需要看stderr的，主要看输出结果（错是很正常的这种赶出来的程序）
stdout_logfile_maxbytes=10MB  ; max # logfile bytes b4 rotation (default 50MB)
stdout_logfile_backups = 10   ; # of stdout logfile backups (default 10)
stdout_logfile=/home/Berry/Repositories/SpyTheLink/logs/spylink.out
stderr_logfile=/home/Berry/Repositories/SpyTheLink/logs/spylink.err       ; stderr log path, NONE for none; default AUTO
stderr_logfile_maxbytes=10MB   ; max # logfile bytes b4 rotation (default 50MB)
stderr_logfile_backups=10     ; # of stderr logfile backups (default 10)
loglevel=info         ;(log level;default info; others: debug,warn,trace)，因为我是需要输出信息，所以不需要其他，默认就好

```

# 4.后记

需要做一个以 `Django` 做为服务器端的人工判断器并记录，我可能需要再思考一下我该怎么做下一步先。