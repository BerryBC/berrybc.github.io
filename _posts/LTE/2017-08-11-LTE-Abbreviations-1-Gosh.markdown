---
layout: post
title:  LTE-Abbreviations-1-Gosh!
date:   2017-08-11 16:49:14 +0800
categories: LTE-以题会友
author: 崔秉龙
location: Guangzhou, China
---


这里是英文学习的乐园

Yeah~put your hands up~

DRX     Discontinuous Reception        不连续接收

DTX     Discontinuous Transmission    不连续发射

PRB     Physical Resource Block         物理资源块

RB        Resource Block                       资源块

VRB      Virtual Resource Block            虚拟资源块

RE        Resource elements                  资源单元

--



关于RB这个东西，其实我们真的需要多给他一点关怀，首先RB这个概念是用来映射物理信道到每一格RE里面的。

然后VRB是一个虚拟的概念，是只有下行才有的，就是上层资源都映射到一个RB里面时，这个RB到底是放在整个发射频段的哪个位置。

而最终确定在一个频段的某个位置就是已经具体确定在哪里的，这个时候就称为PRB了。



E-UTRAN  Evolved Universal Terrestrial Radio Access Network

演进的通用陆地无线电接入网络

EPC          Evolved Packet Core           演进分组核心网

EPS           Evolved Packet System       演进分组系统

--

这些东西其实还好玩，我昨天发的那个也有解释这几个东西的关系。


EMM          EPS Mobility Management             EPS移动性管理

ECM          EPS Connection Management        EPS连接管理

--

![alt text](/photo/InPost/LTE/A1/image.png)

在EMM-REGISTERED状态里面，UE应该：

- 总有一个活动的PDN链接

- 设置EPS安全上下文
--


当有RRC连接的时候，就是ECM-CONNECTED

--

其实还有很多深奥的内容。

MME         Mobility Management Entity     移动性管理实体

S‑GW        Serving Gateway                     服务网关

P‑GW        PDN Gateway                          PDN网关

PDN           Packet Data Networks              分组数据网络

--

其实如果知道了中文之后，会好理解很多，对不~？

