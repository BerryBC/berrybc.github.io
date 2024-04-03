---
layout: post
title:  LTE-以题会友-9-我讨厌‘EPS承载’
date:   2017-08-10 18:03:14 +0800
categories: LTE-以题会友
author: 崔秉龙
location: Guangzhou, China
---


开始发现微信公众号文章默认的字体改为16px，那么大，你当我是老人家吗？

好吧，人也是有情绪的，作为一个做错了那么多题的男人，情绪更甚，而且错的都是关于EPS承载的题目，我必须发一下火，好，火来了。


第一题

关于EPS承载说法不正确的是：（）

   A、EPS承载=无线承载+S1承载+S5/S8承载

   B、EPS承载的类型分为两类，默认承载和专有承载

   C、按照EPS系统的承载所能支持的业务又可以将其分为GBR承载和NON-GBR承载

   D、默认承载通常指NON-GBR承载，而专有承载是GBR承载



答案：D





在做这题之前，首先得解释下什么叫EPS，EPS全称为Evolved Packet System，按谷歌翻译来说就是“演进分组系统”（奶奶的名字这么奇怪），反正现在LTE就都是走分组业务了吧，所以这个就是描述LTE那些奇奇怪怪的东西是怎么走数据包的。

然后看选项A，有那么多承载，其实都可以通过一张图来看的，图在这儿：


![alt text](/photo/InPost/LTE/9/image.png)

（我不会告诉你我在盗百度的图的）

从上图可以看出每一段承载到底是在什么网元跟什么网元之间，例如：

无线承载：在UE跟eNodeB之间

S1承载：在eNodeB跟S-GW之间

S5/S8承载：在S-GW跟P-GW之间

E-RAB：其实就是S1承载+无线承载

那么EPS承载，唉，我也不说了，反正选项A是对的。

不过基本以我的性格，我是不会相信百度的东西的，所以我再粘一个3GPP的解释上来，um，没必要看得懂，强迫症要我粘而已：


![alt text](/photo/InPost/LTE/9/image-1.png)



好，我们来看选项B，我们关注的焦点有两个：

1、是否有默认承载跟专有承载？

2、除了这两类还有没有其他？

我们首先解决第一个问题：

在3GPP TS 24.401里面有这么一段话


![alt text](/photo/InPost/LTE/9/image-2.png)

翻译过来的意思就是：当UE连接到PDN时，建立一个EPS承载，并且在PDN连接的整个生命周期内保持建立一个EPS承载，以向UE提供始终在该IP上的IP连接。 该承载称为默认承载。 将为同一PDN连接建立的任何附加EPS承载称为专用承载。

很遗憾，是真的存在这两个承载的，但除了这两类外还会不会产生其他类型的承载？um，暂时来说没有（因为我懒得去看整篇规范）。

那么B是对的。



C选项来了。

（其实我懒得打字了）


![alt text](/photo/InPost/LTE/9/image-3.png)

其实这个可以顺便解释D答案，上面第一段话是说：“你业务需要保证比特率的话就用GBR呗，其他就随便用Non-GBR就行了啦~”

所以C是对的，根据业务选择，而D呢，对应最后面那句话，最后面那段话谷歌是如此翻译的：“专用承载可以是GBR或非GBR承载。 默认承载应为非GBR承载。”

See~？

Seeeeeeeeeeee~~~~

这题完事


第二题

卧槽，看到一题：


下述关于EPS bearer的概念，哪一个是错误的

   A、默认承载就是为新的PDN连接第一个建立的EPS bearer，并且在整个PDN连接的周期中保持连接

   B、一个EPS bearer指的是一个S1 Bearer 与相应的Data Radio Bearer的串联

   C、在一个E-RAB存在的场景，这个E-RAB与NAS层的EPS bearer是一一映射的

   D、一个EPS bearer唯一确定携带公共QoS的业务流，该业务流介于UE与PDN GW之间





答案是B

但我觉得我水平真的十分有限，我不懂这题（还是我英文水平不行）。

首先看A，这个在上一题其实已经有说明了，规范定义如下：


![alt text](/photo/InPost/LTE/9/image-4.png)

其实他们就是翻译了整个默认承载的定义作为选项，所以这个是对的。

选项B，卧槽，他们说选项B是错的，所以选项B留着最后看。

再看C，其实我觉得这个真的很纠结，NAS层哪里来EPS承载呢？但是真的有一个解释是这样：


![alt text](/photo/InPost/LTE/9/image-5.png)

其实我觉得翻译翻译错了吧（但谷歌都是这样翻译的），我觉得翻译是：“当E-RAB存在时，在NAS上是有E-RAB跟EPS承载的一对一对应的。”

好吧，有没有英文很好的人来解释。

好吧，那还是当这个是对的吧。

选项D：


![alt text](/photo/InPost/LTE/9/image-6.png)

其实就是他直接翻译了这段话前面作为选项。

选项B，看看上面选项C的时候截图的那段话就懂了，那段话翻译之后是：

“E-RAB唯一地识别S1 Bearer和相应的Data Radio Bearer的级联。”

我觉得这出题的人还真TM懒惰。

所以答案选B咯。


第三题

我真的不懂，为什么人们会这么懒惰。



SRB0使用（）逻辑信道承载RRC消息

   A、DCCH

   B、CCCH

   C、DTCH

   D、BCCH



答案：B



截个图就解决了：


![alt text](/photo/InPost/LTE/9/image-7.png)

我估计这不会英文都看得懂，但还是解释下吧。

SRB0：用CCCH来传送RRC信息；

SRB1：用DCCH来传送RRC信息（包括NAS信息）；

SRB2：还是用DCCH来传送，哇，传送的信息很复杂，我还是不说了，纯粹因为懒惰。

所以答案是B。


第四题



那个


每个EPS承载都有的QoS的两个相关参数，它们是（）

   A、QCI

   B、DSCP

   C、ARP

   D、BANDWITH



答案：A、C



直接上图：


![alt text](/photo/InPost/LTE/9/image-8.png)



![alt text](/photo/InPost/LTE/9/image-9.png)




我估计都没人会看的，准备下班了，我大致解释下吧：



每一个EPS承载（无论GBR还是非GBR）都必须包含以下两个QoS参数：

1、QCI：QoS类标识符；

2、ARP：分配和保留优先级。



每一个GBR承载，必须包含以下两个参数：

1、保证比特率（GBR）；

2、最大比特率（MBR）。

如果有兴趣可以看英文，没兴趣可以，那啥。



好了，下班了，再减。




