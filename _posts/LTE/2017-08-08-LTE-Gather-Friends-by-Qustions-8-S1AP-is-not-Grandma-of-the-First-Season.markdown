---
layout: post
title:  LTE-以题会友-8-‘S1AP’不是‘第一赛季的阿婆’
date:   2017-08-08 22:29:14 +0800
categories: LTE-以题会友
author: 崔秉龙
location: Guangzhou, China
---



从前有一个乖巧的小孩子，叫‘啵啵’，又被人戏称‘阿婆’，他从王者荣耀刚开始有天梯排位时开始玩，虐人无数，于是民间就有一个称呼，叫做‘第一赛季的阿婆’，英文名叫‘S1AP’。

很冷，我也没办法继续下去了，来正事吧。




第一题



E-UTRAN仅由演进后的eNB组成，eNB之间通过X2接口互联，E-UTRAN系统和EPC之间通过S1接口互联，S1接口不支持“多对多”连接方式

   A对   B错



答案：B



如果想要一个图说明所有问题，那么将是没可能的，但两个图呢？可以，以下是两个图：


首先，我们上一个图，介绍下什么是‘咦~You 蠢’：


![alt text](/photo/InPost/LTE/8/image.png)

单纯的理解，eUTRAN（念‘咦~you蠢’）只包含eNB一种网元（当然其实可以包含多个eNB，另外eNB的全称其实是eNodeB）。

然后这个网元跟其他任何网元的链接只有两个接口，一个是S1，一个是X2，balabalabal（好像全世界都知道的东西我就不说了，尽管其实我说了很多）。

所以至此为止题目描述都是对的。

但那啥，那S1是不是不支持“多对多”呢？

还有一个图：


![alt text](/photo/InPost/LTE/8/image-1.png)

上面有很多英文，但大概的意思就是“你们还是看图吧”，从图中可以看出，其实每一格eNodeB都有很多条S1的线连上EPC的不同网元，所以，其实S1是可以多对多的。

另外上图有一个问题，eNodeB通过S1-U连接的网元写作S-GTW，那么是不是写错了呢！？还是本来就有S-GTW这种东西呢！这里先卖一个关子。




第二题

eNodeB侧处理S1接口用户面数据的协议层是____

   A、GTPU/UDP

   B、S1AP/SCTP

   C、X2AP/SCTP

   D、RRC



答案：A



wow，看题目，what 7 is GTP-U，what 7 is UDP，又what 7 is SCTP！？

我们将在此节解开帷幕。



然后X2跟S1的协议结构差不多，所以，只说S1就行了啦~

上面有说过，LTE是分为三层两面的（如果不知道这个概念可以找下之前发的信息），两面分为控制面和用户面，而S1是连接基站与核心网的唯一接口，所以当然是必须能同时走控制面和用户面的信息，相应不同的信息，其实是要通过不同的协议的！！！！

首先我们解释一下什么叫协议栈。

其实大家可以观看下一个别人网站的技术文章：

http://www.eepw.com.cn/article/274600.htm

如果不想看其实也没关系，来，看图！！

![alt text](/photo/InPost/LTE/8/image-2.png)

（不要告诉那个网站的主人我偷了他的图）

可以看出本层的净数据，其实已经包含了上层的数据以及协议表头，再添加上本层协议的表头，作为更下一层的净数据。

那终于放大招了，放出S1的协议栈：

S1控制面协议栈：


![alt text](/photo/InPost/LTE/8/image-3.png)



S1用户面协议栈：


![alt text](/photo/InPost/LTE/8/image-4.png)



（不要问我为什么S1-MME是控制面，S1-U是用户面，我不会截图给你看的）


![alt text](/photo/InPost/LTE/8/image-5.png)



回看题目的候选答案，B、C都是包含 S1-AP 以及 X2-AP 的，可得知这两个协议都是用在控制面的，与题目不符，然后RRC的全称是“RadioResource Control”，好吧，都看到Control了，就别问我是控制面还是用户面的了。

所以正确答案只有A！！！





第三题

S1-AP协议使用在S1-MME接口之上

   A对   B错



天呐！这道题的答案到底是什么啊！！我不知道啊！！！！

好吧，其实上图已经说明了，S1-AP是在S1-MME接口的协议栈内，而且还是最高层了呢！！那到底什么是S1-AP呢？

事实上，AP的意思是，Application Protocol （应用协议）。

另外一个事实上，其实3GPP已经写了一篇80多页的文档说明S1-AP的功效以及神奇作用：


![alt text](/photo/InPost/LTE/8/image-6.png)

好复杂，我也弄不懂。

晚安了，再减。