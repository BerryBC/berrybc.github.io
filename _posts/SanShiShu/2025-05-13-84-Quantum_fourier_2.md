---
layout: post
title:  捌拾肆- 量子傅里叶变换 (2)
date:   2025-05-07 11:45:02 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---


前期的内容在 [捌拾叁- 量子傅里叶变换](https://berrybc.github.io/%E4%B8%89%E4%B8%96%E4%B9%A6%E8%AE%B0%E5%BD%95/83-Quantum_fourier/)

## 7. 之前文章顺序有错

因为`二进制`是从右往左写的，例如 **1010** 是等于 **1×2^3 + 0×2^2 + 1×2^1 + 0×2^0** 所以之前的顺序是错的！！ K0 应该放在最右边

公式更改为

![alt text](/photo/InPost/SanShiShu/84/1.png)



## 8. 输入量子叠加态变成二进制形式

![alt text](/photo/InPost/SanShiShu/84/2.png)

但问题来了，我看到教程上面写成是二进制小数的形式，如下图：

![alt text](/photo/InPost/SanShiShu/84/3.png)

为什么会有这东西呢！？

![alt text](/photo/InPost/SanShiShu/84/4.png)

![alt text](/photo/InPost/SanShiShu/84/5.png)

( 我感觉我跟教程的方向好像完全不同了.....反正能看懂就行 )


好，我们来研究一下，如果当分母不是 2^n 的呢？

![alt text](/photo/InPost/SanShiShu/84/6.png)

好，我们来代入一下 e 的指数方程

![alt text](/photo/InPost/SanShiShu/84/7.png)

根据欧拉公式，以及 `jn` 取值只能是 0 或者 1

![alt text](/photo/InPost/SanShiShu/84/8.png)

![alt text](/photo/InPost/SanShiShu/84/9.png)

在写到公式中，我把 j 从 `0` -- `n-1` 调转过来，变成 `n` -- `1` ，原公式为：



![alt text](/photo/InPost/SanShiShu/84/10.png)

## 9. 然后

我该下班了...晚了回家会被老婆骂的，晚安 ~~ 之后有空再写 ~