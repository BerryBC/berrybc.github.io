---
layout: post
title:  捌拾伍- 量子傅里叶变换 (3)
date:   2025-05-16 15:01:52 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---


前期的内容在 [捌拾叁- 量子傅里叶变换](https://berrybc.github.io/%E4%B8%89%E4%B8%96%E4%B9%A6%E8%AE%B0%E5%BD%95/83-Quantum_fourier/)

前期的内容在 [捌拾肆- 量子傅里叶变换 (2)](https://berrybc.github.io/%E4%B8%89%E4%B8%96%E4%B9%A6%E8%AE%B0%E5%BD%95/84-Quantum_fourier_2/)


## 9. 之前的

之前的公式写错了！ Markdown 的 `KaTeX` 真难用！！！

而且之前的公式是从 `j1` -- `jn` ，但量子计算都是从 `0` 开始的，我再改一下公式


![alt text](/photo/InPost/SanShiShu/85/1.png)

## 10. 拆解转化为量子电路

教程上，量子电路被描述为：

![alt text](/photo/InPost/SanShiShu/85/2.png)

注意它上面是从 `j1` -- `jn`，但我们这里是从 `j0` -- `jn-1`

### 10.1) 首先研究最左边

最左边是哪个呢？

![alt text](/photo/InPost/SanShiShu/85/3.png)

难点就是 `2𝜋𝑖0.𝑗𝑛-1`

教程上面写着，只需要一个 `H门` 就可以了

为什么呢！？

我们首先把 `0.jn-1` 这个二进制小数化回十进制展示

![alt text](/photo/InPost/SanShiShu/85/4.png)

然后

![alt text](/photo/InPost/SanShiShu/85/5.png)

![alt text](/photo/InPost/SanShiShu/85/6.png)



### 10.2) 当有两位二进制小数时，怎么办！？

例如这个

![alt text](/photo/InPost/SanShiShu/85/7.png)

根据

![alt text](/photo/InPost/SanShiShu/85/8.png)

得到出来的结果是

![alt text](/photo/InPost/SanShiShu/85/9.png)


教程上的说法是，给一个 `H门` , 再串联一个 `CP门`


这个时候必须得介绍一下 `CP 门` 了 [量子计算 - 03 - 量子门](https://berrybc.github.io/%E5%B4%94bb%E6%95%99%E5%AD%A6%E5%A4%A7%E7%BA%B2/Quantum_Computing-03-Quantum_Gates/)

这个 `CP 门` 的作用是 ： `控制比特` 的 **0** 时，不对 `目标比特` 进行任何处理；而为 **1** 时，对 `目标比特` 施加一个 θ 的相位旋转

问了一下大模型

![alt text](/photo/InPost/SanShiShu/85/10.png)

![alt text](/photo/InPost/SanShiShu/85/11.png)

![alt text](/photo/InPost/SanShiShu/85/12.png)

其中我们可以认为 `jn-1` 是 `控制比特`，`jn-2` 是 `目标比特`

`(-1)^t` 其实就是上面个 **10.1** 的结果， `t` 就是 `jn-2`

`c` 就是 `jn-1`

注意，在上面的教程例子里面， `θ` 等于 `2π/2^2`

### 10.3) 多位二进制小数

然后接下来就是不断的把当前输入的为先给一个量子，直接解释可以如下：

![alt text](/photo/InPost/SanShiShu/85/13.png)

![alt text](/photo/InPost/SanShiShu/85/14.png)

![alt text](/photo/InPost/SanShiShu/85/15.png)

![alt text](/photo/InPost/SanShiShu/85/16.png)

## 11. 其实之后的东西就是写代码验证

看着之后的内容，都是比较正常的内容，就是如何实现上面的量子电路，和理论验证

这部分直接相信就是了！

![alt text](/photo/InPost/SanShiShu/85/17.png)

## 12. 后续

这个真的好复杂，而且傅里叶变换以前都只是知道意义和简单的变换，特别傅里叶级数这东西只有一节来教

所以大学真的是一个，非常重要，又非常废的阶段

还有，数学真的很有用！！！但又很难用！！！！

为什么教程写得那么简单，以为看的人都是博士生么.....唉....