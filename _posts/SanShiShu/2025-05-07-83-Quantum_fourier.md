---
layout: post
title:  捌拾叁- 量子傅里叶变换
date:   2025-05-07 11:45:02 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---


## 1. 前言

最近公司地震，现在稍微有点时间继续学习。

看了几个算法，都说是基于 `量子傅里叶变换` ，好，就是他了 [Quantum Fourier](https://github.com/ScQ-Cloud/quafu-tutorial/tree/main/algorithm/Quantum_Fourier)。


## 2. 傅里叶变换

大学是学通信的，对于傅里叶变换还是有所理解的。其实就是基于一个 `时域` 的函数，变换为一个基于 `频域` 的函数。

例如：

[一篇彻底搞懂傅里叶变换及其背后原理](https://zhuanlan.zhihu.com/p/151226805)

![alt text](/photo/InPost/SanShiShu/83/1.png)

一般我们看到的都是时域的数据，例如

![alt text](/photo/InPost/SanShiShu/83/2.png)

这种，随时间的变化，v (速度) 的变化，这个变化对应的频率是怎样呢？通过傅里叶变换，就可以得出频率的变化。

## 3. 离散傅里叶变换


离散傅里叶变换 (Discrete Fourier Transform, DFT).

![alt text](/photo/InPost/SanShiShu/83/3.png)

还有，任何傅里叶变换都是可以相互变换的，从时域变为频域后，也能从频域变成时域。

离散傅里叶变换也一样

推导过程可以上网找，反正我也理解不了，接受就是了

(不过其实这也是一个人造的公式，符合使用，能拓展，自洽，其实就行了)

## 4. 公式推导

我看夸父的教程我看不懂，中间跳了好多东西，于是一直上网搜

![alt text](/photo/InPost/SanShiShu/83/4.png)

其中，`∣x⟩` 是输入态，`∣k⟩` 是输出态

[这个网页有一个 Gif 的展示！](https://zhuanlan.zhihu.com/p/618643953)

然后就是把 `N 个量子比特` 组成的整体系统，组成为一个 `输入态`

![alt text](/photo/InPost/SanShiShu/83/5.png)

## 5. 突然又感觉看不懂了，重新来

我们还是看回夸父的推导公式：

`∣j⟩` 是输入态，`∣k⟩` 是输出态

![alt text](/photo/InPost/SanShiShu/83/7.png)

首先我们的起点是：


![alt text](/photo/InPost/SanShiShu/83/6.png)

他喵的第二步我就看不懂了

为什么一个 `连加` 可以变成 `N 个连加` ？

思来想去后，右边可以展开为 ：

![alt text](/photo/InPost/SanShiShu/83/8.png)

但上面这个是错的！！！！！

因为 `|𝑘⟩` 这个东西是代表一个概率矩阵，应该这样看

![alt text](/photo/InPost/SanShiShu/83/11.png)

其中 `𝑘0` 的意思其实是右边的 `|𝑘⟩` 里面代表当 `k=0` 的时候的概率，同理延续下去

这个时候换基，把原本的 `|𝑘⟩` 换为二进制 (如上述第4点的最后来换)

![alt text](/photo/InPost/SanShiShu/83/18.png)

注意上面 **红色箭头** 跟 **蓝色箭头** ，代表的是这个 `二进制` 如何转化为 `十进制` ，对应的 十进制 就是原等式那个数值

例如，十进制 是 0 的，二进制就是 ....00000

十进制 是 1 的，二进制就是 ....00001

以3位比特为例：

十进制|二进制
---|---
0|000
1|001
2|010
3|011
4|100
5|101
6|110
7|111


上面的 K 意义是下面

![alt text](/photo/InPost/SanShiShu/83/14.png)

`x` 的意思是，换位二进制之后，第几位

`y` 的意思是，这个位数的概率是多少

**注意** `K` 是 `k` 的二进制表示，大小写区分的！ (跟原推导公式不一致)

此刻我们再改一下写法

![alt text](/photo/InPost/SanShiShu/83/19.png)

其实就是因为在指数项的相加是等于整体的相乘

![alt text](/photo/InPost/SanShiShu/83/20.png)

其中把二进制第 0 位 ( 即二进制最右边那位提取出来 )，也即 `K0`

还是以3位比特为例

十进制| 二进制 (左边两个比特) | 第0位比特
---|---|---
0|00|0
2|01|0
4|10|0
6|11|0
1|00|1
3|01|1
5|10|1
7|11|1

回到上面的公式

![alt text](/photo/InPost/SanShiShu/83/21.png)

把那个2的指数改一下

![alt text](/photo/InPost/SanShiShu/83/22.png)

原公式化为

![alt text](/photo/InPost/SanShiShu/83/23.png)

问了一下 `智谱` 的 大模型，可以写成

![alt text](/photo/InPost/SanShiShu/83/24.png)

变成了最终

![alt text](/photo/InPost/SanShiShu/83/25.png)


## 6. 然后发现

好像还有问题....为什么..

跟量子电路没关系呢？

下周继续看，快疯了