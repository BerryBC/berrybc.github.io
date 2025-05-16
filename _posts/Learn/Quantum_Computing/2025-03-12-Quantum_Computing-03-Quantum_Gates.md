---
layout: post
title:  量子计算 - 03 - 量子门
date:   2025-03-12 14:32:13 +0800
categories: 崔BB教学大纲
author: 崔秉龙
location: Guangzhou, China
---


各种量子门的定义以及说明

## 1. 量子门 是啥

量子门是用来操作各个量子比特的, 介绍说, 所有量子门的本质都是 `幺正矩阵`

![alt text](/photo/InPost/Learn/Quantum_computing/3/1.png)

其中 `单量子门` , 是作用于单量子的 , `多量子门` 可以理解为把多个量子组合成一个 `量子系统`

## 2. 泡利门 (Pauli Gates)

X门, Y门, Z门

![alt text](/photo/InPost/Learn/Quantum_computing/3/2.png)

## 3. H 门 (Hadamard Gates)

H门

![alt text](/photo/InPost/Learn/Quantum_computing/3/3.png)

## 4. 相位门 (Phase Gates)

S门

![alt text](/photo/InPost/Learn/Quantum_computing/3/4.png)

T门

![alt text](/photo/InPost/Learn/Quantum_computing/3/5.png)

TDG门 (T-dagger Gate) 是T门的共轭转置 (Hermitian 共轭) 门，也称为T门的逆门

![alt text](/photo/InPost/Learn/Quantum_computing/3/16.png)

P门

![alt text](/photo/InPost/Learn/Quantum_computing/3/11.png)

## 5. 旋转门 (Rotation Gates)

在 布洛赫球 上, 分别沿着 X / Y / Z 轴, 旋转 θ 度

RX门 / RY门 / RZ门

![alt text](/photo/InPost/Learn/Quantum_computing/3/6.png)

## 6. CNOT 门 (Controlled-NOT Gate)

CNOT门 / CX门

![alt text](/photo/InPost/Learn/Quantum_computing/3/7.png)

CZ门

![alt text](/photo/InPost/Learn/Quantum_computing/3/18.png)

![alt text](/photo/InPost/Learn/Quantum_computing/3/19.png)

![alt text](/photo/InPost/Learn/Quantum_computing/3/20.png)

CY门

![alt text](/photo/InPost/Learn/Quantum_computing/3/21.png)

## 7. Toffoli 门 (CCNOT 门)

T门

![alt text](/photo/InPost/Learn/Quantum_computing/3/8.png)

## 8. SWAP 门

SWAP门

![alt text](/photo/InPost/Learn/Quantum_computing/3/9.png)

![alt text](/photo/InPost/Learn/Quantum_computing/3/10.png)

## 9. SY 门

SY门 通常指的是 Pauli-Y 门的平方根门（Square Root of Y Gate）

![alt text](/photo/InPost/Learn/Quantum_computing/3/12.png)

![alt text](/photo/InPost/Learn/Quantum_computing/3/13.png)

## 10. I 门（Identity Gate，单位门）

I门

![alt text](/photo/InPost/Learn/Quantum_computing/3/14.png)

![alt text](/photo/InPost/Learn/Quantum_computing/3/15.png)

## 11. 延迟门 （Delay门）

![alt text](/photo/InPost/Learn/Quantum_computing/3/17.png)

## 12. RXX 门

RXX门 / RZZ门 / RYY门

![alt text](/photo/InPost/Learn/Quantum_computing/3/22.png)

![alt text](/photo/InPost/Learn/Quantum_computing/3/23.png)

![alt text](/photo/InPost/Learn/Quantum_computing/3/24.png)

可以通过单量子门, 以及 CNOT 门来模拟 RXX门

![alt text](/photo/InPost/Learn/Quantum_computing/3/25.png)

## 13. U 门

![alt text](/photo/InPost/Learn/Quantum_computing/3/26.png)

就是说这个门才是所有门的数学基础

![alt text](/photo/InPost/Learn/Quantum_computing/3/27.png)

## 14. CP 门 (Controlled Phase Gate, 受控相位门)

![alt text](/photo/InPost/Learn/Quantum_computing/3/28.png)

![alt text](/photo/InPost/Learn/Quantum_computing/3/29.png)