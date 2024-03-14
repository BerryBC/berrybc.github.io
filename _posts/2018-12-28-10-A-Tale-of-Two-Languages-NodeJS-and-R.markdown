---
layout: post
title:  拾-关于NodeJS跟R语言的一段情
date:   2018.12.28 20:07:10 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---


其实它们本来毫无交集，但是，因为有一个意大利的哥们[albertosantini](https://github.com/albertosantini)，他弄了一个[node-rio](https://github.com/albertosantini/node-rio)，把他们搞在一起了。

# 一、本来无一物，为何在Windows上有R

反正我也不是很懂，先下载一个`R语言`玩一下[下载链接](https://mirrors.tuna.tsinghua.edu.cn/CRAN/)，发现，诶！还不错哦！还有`GUI`哦~

# 二、那个RIO来了

`RIO`不是那个鸡尾酒，官方解释是
>R Input Output, connects an app to [Rserve](http://www.rforge.net/Rserve/), a TCP/IP server which allows other programs to use facilities of [R](http://www.r-project.org/).


那可以了，其实关键就是，在`R语言`里面（我在`Windows`是直接在`RGUI`里面敲入启动指令）。


然后在` GitHub `直接把那个项目`Clone`下来，里面有例子的了（还是直接可用的哟）。


# 三、注意提前在R里面装好包


我是直接运行项目上的例程的，发现有些例程运行不了，我心慌啊！！

找了好多原因，发现都找不到为毛线，哎呀，还是心慌啊。

最后的最后，我直接把代码在`R`里面运行，我发现了，例程里面有很多`R语言`需求的`包`都是不在我的电脑里面，所以在运行前，需要把包都在本机的`R`里面装好，用`install.packages`。

# 四、在Linux上安装Rserve

前面的都是废话，因为我都试过了，下面开始一些没试过的。

当然首先我都是上网看看有没有前人试过，当然马上就找到了[LINUX/CENTOS R及RSERVE下载和安装](https://www.cnblogs.com/xxy1989/articles/4635039.html)（因为我这边yum安装不到所以只能编译了，是为毛线？有没有运维同志告诉我？）。可惜，文章里面`configure`命令不能直接复制用。

（偷偷悲伤一下，我服务器上很多包都没有装，好烦啊。）

在安装的过程中，我强烈推荐看这篇文章[centos7下安装R](https://blog.csdn.net/qq_34023608/article/details/51700974)的“安装过程中的问题”章节。

我觉得这一句超有用：
>缺少什么就使用yum search 缺少的名称，再根据相应的包来进行安装。

----------------

我有点崩溃了，我两个小时都没有装好`R`，我想死。

----------------

我的标题应该改为`R语言`为什么这么难装么？

----------------

两个小时后，我终于发现了装不了的`秘密`，之前主要是出现`internal compiler error: Killed`的错误，我也忘了我到底是怎么搜为什么出现这个的，反正在2个小时之后我找到了这个文章：[《gcc 编译出现 internal compiler error: Killed》](https://blog.csdn.net/qq_29573053/article/details/69665996)。

`MD`马上把服务器上的`Tomcat`给关掉，于是......

奇迹般，装上了。

# 五、放弃了

是时候该放弃了，反正也能在服务器上装了`R`，我内心已经是很难平复的了。