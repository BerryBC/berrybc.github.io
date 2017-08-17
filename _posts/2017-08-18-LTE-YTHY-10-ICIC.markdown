---
layout: post
title:  LTE-以题会友-10-ICIC?Oh I see.
date:   2017-08-17 21:18:14 +0800 
categories: LTE-以题会友
author: 不是谭翠平
cover: "/photo/InPost/20170817-1.JPG/"
location: Guangzhou, China
description: 以题会友的那个，关于ICIC的不了解这世界。
tail: 永远不说永别
---

我不了解为什么Markdown没有首行缩进这功能，  
但是既然开始尝试了，就得尝试下去，  
（起码这一篇~~）
这题的主题是ICIC！！  
![你不懂ICIC](/photo/InPost/20170817-1.JPG/)

----
第一题

----

什么都不说直入主题是不是很不好玩？  
好了，还是直接来了：  

**小区间干扰抑制技术主要包括有（）**  

- [X] A、小区间干扰随机化（Inter-cell interference randomisation）
- [X] B、小区间干扰消除（Inter-cell interference cancellation）
- [X] C、小区间干扰协调（ICIC：Inter-cell interference coordination）  
- [ ] D、小区间干扰平均（Inter-cell interference average）

（不知道有没有人会不习惯这种答案的写法，答案是ABC）  
不知道为什么，有些人会以为ICIC是“小区间干扰抑制”，但其实**ICIC**的实际意思是：  
Inter-cell interference coordination  
（其实我不想翻译的，不过翻译是：**小区间干扰协调**）  
那么其实这题答案怎么选择的呢？  
还是让我们来看看实际规范的定义  
![干扰减缓的方法](/photo/InPost/20170817-2.png/)  
可以看得出，完美的跟上面那题的3点契合
**但！！！！**  
姑且先看看图片中下面的那段英文，翻译过来中文的意思就是。
“此外，在基站处使用波束成形天线解决方案也是一般的方法，也可以被看作是用于下行链路小区间干扰减轻的手段。”（来源谷歌翻译）

所以这道题如果改为：  

**TD-LTE的干扰解决方案有哪些:**  

- [X] A、小区间干扰随机化（Inter-cell interference randomisation）
- [X] B、小区间干扰消除（Inter-cell interference cancellation）
- [X] C、小区间干扰协调（ICIC：Inter-cell interference coordination）  
- [X] D、发射端波束赋形（Beam-forming antenna solutions at the base station）  


这个时候就需要全选了！！
欢呼 ~~ 撒花 ~~ 撒币 ~~

----
来一个空闲
（Let's take a break）

----

有一件事情我觉得十分讨厌，关于名词“小区间干扰抑制”，这个词我从规范上面查到，它英文原文应该是这个“ Inter-cell interference mitigation ”，mitigation这个词的翻译应该是“减缓”、“缓解”，哪个撒币译为了“抑制”呢！？！？明明“抑制”的英文应该是----“suppression”啊！！
我上谷歌查的结果是：  
![百度你是不是收钱了！？](/photo/InPost/20170817-3.png/)  
我真的不懂了！！  

----
第二题

----

**常用的干扰随机化方法有：（）。**  

- [X] A、序列加扰和交织
- [ ] B、分集接收
- [ ] C、ICIC
- [ ] D、加扰  

好了，上题说了干扰抑制的三种手段，其中一种就是干扰随机化  
（好吧，准确来说，这个只是一个TR文档，是一个报告文档，而并不是一个TS技术规范文档。所以，不知道以后会不会有改变呢~？不过整体来说这个是最新的了。）  
那么干扰随机化到底说了些什么东西呢？  
来，我们看图！！  

![干扰随机化](/photo/InPost/20170817-4.png/)  

不用看英文，我们直接来翻译：
1、在编码、交织后进行加扰；
2、交织，也称为“交织分多址”（IDMA  牛逼）；
3、跳频。

我们看到上述的B在文档中没提及，ICIC是跟干扰随机化同等级的一样东西，至于为什么不选择D，只能说，你哪能猜到出题人的心思，只能选个最全的。  

----
第三题

----

**LTE小区干扰消除的方法有：（）。**  

- [X] A、利用接收端的多天线空间抑制方法来进行干扰消除
- [X] B、基于检测/删除的方法
- [ ] C、基于码本的方法
- [ ] D、联合检测  

我还是不粘图了，直接粘英文：
>Two methods have been discussed
>-	Spatial suppression by means of multiple antennas at the UE. It should be noted that the availability of multiple UE antennas is an assumption for E-UTRA.
>-	Interference cancellation based on detection/subtraction of the inter-cell interference. One example is the application of cell-specific interleaving (IDMA) to enable inter-cell-interference cancellation.

然后直接粘个谷歌翻译（我是不是很懒惰？）：
>已经讨论了两种方法
>- 在UE处通过多个天线的空间抑制。 应当注意，多个UE天线的可用性是对于E-UTRA的假设。
>- 基于小区间干扰的检测/减除的干扰消除。 一个示例是小区特定交织（IDMA）的应用以启用小区间干扰消除。

关于B答案，题目只是列出关键字，但实际的说明及准确描述完全不是这回事，有兴趣可以自行看3GPP的报告。
关于C答案，那是关于MIMO的“高级原理”，高级什么意思啊哼！反正是不关这个事。
D答案，哈 ~ 联合检测，反正现在没提及它就不用管了。


----
没有第四题

----

关于ICIC，首先我必须说他是一个大坑！各个厂家在争的大坑！！  
首先我们看一题！

**以下关于ICIC分类说法正确的是**  

- [X] A、静态ICIC
- [X] B、半静态ICIC
- [X] C、上行ICIC
- [X] D、下行ICIC

这题估计是没有任何异议的，因为3GPP TR 25.814 里面是有定义的，虽然我觉得这样分类是不对的，按报告要求应该这样分：

```graphLR
    A[上行ICIC] -->B(静态ICIC)
    A --> C(半静态ICIC)

    D[下行ICIC] -->E(静态ICIC)
    D -->F(半静态ICIC)
```

![分类](/photo/InPost/20170817-5.png/)  

因为报告里面分别都有如下说法：

>\-	Static interference co-ordination
>Reconfiguration of the restrictions is done on a time scale corresponding to days. The inter-node communication is very limited (set up of restrictions), basically with a rate of in the order of days.
>\-	Semi-static interference co-ordination
>Reconfiguration of the restrictions is done on a time scale corresponding to seconds or longer. Inter-node communication corresponds to information needed to decide on reconfiguration of the scheduler restrictions (examples of communicated information: traffic-distribution within the different cells, downlink interference contribution from cell A to cell B, etc.) as well as the actual reconfiguration decisions. Signaling rate in the order of tens of seconds to minutes.

反正意思就是**静态**跟**动态**

但悲剧的是，看到有另外一题是这样的：

**按照协调的方式，干扰协调可以分为：**  

- [X] A、静态干扰协调
- [X] B、半静态干扰协调
- [X] C、动态干扰协调
- [ ] D、半动态干扰协调

我蒙逼了，报告里面完全没有说动态是什么鬼，发觉不行了，于是上网百度一下论文，找到一篇[论文](http://www.docin.com/p-593833202.html)（请点击前面的超链接），里面列举了一堆文献以及资料算法，好吧，我就去看底部的文献，好吧，好吧，不是已经确定好的规范，只是！只是！一堆会议纪要，各家公司在一争长短！！！  
好吧，其实我没有全程跟踪3GPP的会议过程及新闻，我真的不太知道现在到底有没有确定的被3GPP定义的ICIC方案，我个人还是觉得是各个厂家自己制定的，仅此而已。
（所以我决定不管ICIC了。）

噢，不行，最后还是得说一个3GPP的规范里面有定义的东西**ABSs**：
>For the time domain ICIC, subframe utilization across different cells are coordinated in time through backhaul signalling or OAM configuration of so called Almost Blank Subframe patterns. The Almost Blank Subframes (ABSs) in an aggressor cell are used to protect resources in subframes in the victim cell receiving strong inter-cell interference. Almost blank subframes are subframes with reduced transmit power (including no transmission) on some physical channels and/or reduced activity. The eNB ensures backwards compatibility towards UEs by transmitting necessary control channels and physical signals as well as System Information. Patterns based on ABSs are signalled to the UE to restrict the UE measurement to specific subframes, called measurement resource restrictions. There are different patterns depending on the type of measured cell (serving or neighbour cell) and measurement type (e.g. RRM, RLM). MBSFN subframes can be used for time domain ICIC when they are also included in ABS patterns. The eNB cannot configure MBSFN subframes TS 36.211 [4] as ABSs when these MBSFN subframes are used for other usages (e.g., MBMS, LCS).  

好长一段，不过其实意思就是，ABSs这东西呢，就是一个几乎空白的子帧，然后他的作用为：
>攻击者小区中的几乎空白子帧（ABS）用于保护受干扰小区中接收到强小区间干扰的子帧中的资源。（谷歌翻译）  

---

好了，Markdown完败，好辛苦。
我该睡了，好困。  
下面是我的，微信订阅号二维码，好看点吧格式会。  

![微信订阅号二维码](/photo/InPost/20170817-6.png/)  