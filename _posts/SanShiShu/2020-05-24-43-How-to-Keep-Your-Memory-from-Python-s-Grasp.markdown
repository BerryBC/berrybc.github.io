---
layout: post
title:  肆拾叁- 如何从 Python 手上守住你的内存
date:   2020-05-24 22:35:10 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---


# 0. 引用包

其实我只是引用了一个 gc 的包

只需要

```python
import gc
a = "I'm Berry"
del a
gc.collect()
```

# 1. 服务器爆炸了

前期有一个设置好`长期运行`的 `Python` 程序，但总是过一段时间之后，`内存`就被`占满`，于是`程序崩溃`了，`数据库`也`崩溃`了，除非重启该程序，如下图：

![爆掉的内存](/photo/InPost/43-1.png)

# 2. 为什么！？

服务器上会不断`吃内存`的程序估计一个是 `PM2` 托管的 `NodeJS` 程序，一个是 `Supervisor ` 托管的 ` Python` 程序，于是我决定各`停止`一段时间，查出是 `Python ` 程序出问题。

# 3. Python 不会自己释放么？

因为以前的代码均是`短期`运行，运行一次就整个`进程关掉`了，所以从来没有注意`内存问题`。

后来发现这个问题已经为时已晚。

一开始以为是`数据库`链接问题，已经多次研究，`优化`过后还是不断`耗内存`。

苦思冥想之下，上网一搜，我了个去。

原来这世界上还有一个 `Python 包`叫做 `gc`。

`gc 包`的介绍如下：

[Python之美[从菜鸟到高手]--Python垃圾回收机制及gc模块详解](https://blog.csdn.net/yueguanghaidao/article/details/11274737)

# 4. 代码

如以下这段代码：

```python
    def LoadLatestClf(self):
        self.clfLatestClf = 0
        self.objLatestClfCfg = 0
		# -------清除引用对象，清空内存---------
        del self.clfLatestClf
        del self.objLatestClfCfg
        # ------------------------------------
        gc.collect()
        self.objLatestClfCfg = []
        self.clfLatestClf = []
        curClfCfg = self.objMongoDB.LoadLimitBySort(
            'clfdb', {}, [('lt', -1)], 3)
        for eleCur in curClfCfg:
            self.objLatestClfCfg.append(eleCur)
            self.clfLatestClf.append(joblib.load(
                self.strDirForClf+eleCur['clfFileName']))
		# -------清除引用对象，清空内存---------
        del curClfCfg
        gc.collect()
        # ------------------------------------
```

针对引用过的`迭代器`、`包的引用`之类的，如果没有长期存在需求的话都必须`清掉`，不然`内存`会`爆掉`的。

# 5. 效果

![世界安静了](/photo/InPost/43-2.png)