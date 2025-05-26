---
layout: post
title:  捌拾陆- 海森堡不确定性原理
date:   2025-05-22 15:56:52 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---

## 继续学习

Hello，我又来了，又来一个简单的（看到后续的非常复杂，所以先不看先）

又是一个物理理解的基础 [海森堡不确定性原理](https://github.com/ScQ-Cloud/quafu-tutorial/tree/main/algorithm/uncertainty_principle)

> 有时候真搞不懂，大学学完的东西为什么现在就是完全忘记了


另外还得有空复习一下数学原理，所以看一下 [量子计算 - 02 - 数学基础与狄拉克符号](https://berrybc.github.io/%E5%B4%94bb%E6%95%99%E5%AD%A6%E5%A4%A7%E7%BA%B2/Quantum_Computing-02-Mathematical_Foundations_and_Dirac_Notation/)

## 1. 海森堡不确定性原理

这东西物理原理就十分复杂，而且上网查下就行

目前只 `聚焦` 他对计算的影响，以及由此带来的对 `量子计算` 基础的理解

好，教程里面，第一句就是

![alt text](/photo/InPost/SanShiShu/86/1.png)

还是一下子跳过了许多东西，相当于直接给结论了 ( 你还是不是教程啊哥哥 )

## 2. 推导 ΔA

第一点中，关于 A 的不确定度 ΔA ，定义为

![alt text](/photo/InPost/SanShiShu/86/2.png)

我当时就懵了，按我理解 `<A>` 是一个实数 ，为什么一个矩阵可以减一个实数？

然后问了一下

![alt text](/photo/InPost/SanShiShu/86/3.png)

好吧，可以减的，然后，继续展开


![alt text](/photo/InPost/SanShiShu/86/4.png)

这里有一个比较不懂的点：

1. 为什么第3点取期望值时，可以直接对里面每个元素取期望值，而不是作为一个整体取期望值？
2. 为什么 `<A>` 的期望值仍然是 `<A>` ？

解答来了

![alt text](/photo/InPost/SanShiShu/86/5.png)

好的，是我对线性代数不熟....

补充一下物理定义


![alt text](/photo/InPost/SanShiShu/86/6.png)

## 3. 推导不下去

看了好多的推导过程的起点是

![alt text](/photo/InPost/SanShiShu/86/7.png)

ΔA 是没有根号的，问了一下，反馈

![alt text](/photo/InPost/SanShiShu/86/8.png)

十分绝望

## 4. 继续看 ΔAΔB

从新来

![alt text](/photo/InPost/SanShiShu/86/9.png)

又要构造一些 `∣f⟩=ΔA∣ψ⟩` 中间变量，快疯了

这里有一个很神奇的东西，叫 `柯西–施瓦茨不等式` ( 这个就是中学数学了 )

得到

![alt text](/photo/InPost/SanShiShu/86/10.png)

-----------

我感觉我不行了，突然间又忙起来了，先去努力工作先