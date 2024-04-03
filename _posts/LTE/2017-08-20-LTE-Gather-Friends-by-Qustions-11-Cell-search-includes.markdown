---
layout: post
title:  LTE-以题会友-11-小区搜索包括..........
date:   2017-08-20 13:18:14 +0800
categories: LTE-以题会友
author: 崔秉龙
location: Guangzhou, China
---

今天有且只有一题！

因为我不懂这个世界。




仅有的一题




和小区搜索有关的信道和信号有（）

   A、PBCH

   B、PSS

   C、SSS

   D、RS



参考答案：ABCD     ？？？？？



what！？

首先我们来看看，小区搜索是干嘛的：

![alt text](/photo/InPost/LTE/11/image.png)

大概翻译的意思就是：

小区搜索过程的作用就是，让UE获取时间、频率的同步，以及检测出小区的Cell ID （PCI）。

小区搜索是基于下行传输的主同步信号以及辅同步信号。

（PSS：Primary synchronization signal   主同步信号

    SSS：Secondary synchronization signal   辅同步信号）

主同步信号以及辅同步信号均是在中间的72个子载波，在每个帧里面第一个以及第六个子帧中传输（这里描述，是有问题的，其实，可以看看我之前写的帧结构）。



如果完全按规范理解，那么这个答案是应该选BC的，可为什么重选呢？

百思不得其解，然后醒悟到，如果你会得到一个错误的答案，肯定用了一个错误的方法，于是我马上用百度搜，我就搜到了以下的说法：

![alt text](/photo/InPost/LTE/11/image-1.png)

卧槽？

我好像懂得了些什么。

知识只是一种在不停的被修饰、加工的存在，所以，我们永远不会知道，我们所做的题是在被加工到哪个程度后再出的。



唉，加工来干嘛？好好的按照规范来不就好了嘛。

