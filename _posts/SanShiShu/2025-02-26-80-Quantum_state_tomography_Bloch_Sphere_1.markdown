---
layout: post
title:  捌拾- 量子态层析 以及 布洛赫球 (1)
date:   2025-02-26 19:29:06 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---


## 1. 奥卡姆剃刀

在上一篇学习文章中 [柒拾玖- 贝尔不等式](https://berrybc.github.io/%E4%B8%89%E4%B8%96%E4%B9%A6%E8%AE%B0%E5%BD%95/79-Bell_s-inequality) ，我感觉，为啥这么量子计算的门槛那么高呢？

结果我打开了上级目录，发现，原来他是按首字母排序......

经过一系列查看，我挑了一个最简单的

[量子态层析](https://github.com/ScQ-Cloud/quafu-tutorial/blob/main/algorithm/Quantum_state_tomography/Quantum_state_tomography_cn.ipynb)

这里能更简单的说明量子比特到底测量出来的`本质`是什么，而且它物理性在量子计算上的体现。

但！！这里有一个**大坑**！！！

我在网上下的最新的 pyquafu 是 0.4.6，但这个教程是用 0.3.5 演示的！！！有些演示用的函数都没了！！！

## 2. 布洛赫球

首先，我先撇开物理性质去理解，单独理解数学层面的解释

先从单个粒子来看，它从3个`维度`构成，分别是 `X、Y、Z`

他们三个以及一个单位 `I` 可以够成一个粒子的密度函数，详细数学描述如下图

![alt text](/photo/InPost/SanShiShu/80/1.png)

我个人觉得可以先忘记上面的东西，反正得知，`X、Y、Z` 可以作为一个三维的矢量，可以得到一个球

![alt text](/photo/InPost/SanShiShu/80/2.png)

而关于这个球的详细描述，可以参看 [布洛赫球 (Bloch Sphere)](https://zhuanlan.zhihu.com/p/90976246)

## 3. 测量到底是基于什么？

这个就很扯淡，首先我们得说一下到底 pyquafu 到底是`测的什么`？

或者说**所有量子计算**都是基于这个逻辑去进行`测量的？`

首先，我们看到所有的教程均有一个函数叫 calculate_obs ，它允许直接对已经完成`量子计算`并进行测量的`量子比特`得到一个数学期望值。

但，我基于 0.4.6 的 quafu 居然就没了这个函数！？！？

却直接给出了一个密度函数，于是我上网搜，到底怎么计算？

得到一个结果

![alt text](/photo/InPost/SanShiShu/80/3.png)

um...

好，我知道了，不过问题是，我到底是用什么算符！？我是一个小白！！！！

于是我不断做实验，不断看文档，我看到教程里面有一段代码：

```python
def create_circuit(theta, phi, base="Z"):
    # 创建量子态：|ψ> = cos(θ/2)|0> + exp(i*φ)*sin(θ/2)|1>
    qc = QuantumCircuit(1)
    qc.ry(0,theta)
    qc.rz(0,phi)
    # 旋转测量基
    if base == "X":
        qc.ry(0, -pi/2)  # 用X的本征态为测量基
    elif base == "Y":
        qc.rx(0, pi/2)   # 用Y的本征态为测量基
    elif base == "Z":
        pass
    qc.measure([0])
    return qc
```

凭啥我测 X 轴、Y 轴 的时候需要旋转，Z轴就不需要旋转！？

有没有可能，我们测的，只是 Z 轴？

于是，我首先参考教程，写了一段测量算符的代码来代替 calculate_obs 函数

```python

# 引入包
from quafu import QuantumCircuit,Task,simulate
import numpy as np
import matplotlib.pyplot as plt
from numpy import pi

# 定义 Pauli Z 矩阵
sigma_Z = np.array([[1, 0], [0, -1]])


def create_circuit(theta, phi, base="Z"):
    # 创建量子态：|ψ> = cos(θ/2)|0> + exp(i*φ)*sin(θ/2)|1>
    qc = QuantumCircuit(1)
    qc.ry(0,theta)
    qc.rz(0,phi)
    # 旋转测量基
    if base == "X":
        qc.ry(0, -pi/2)  # 用X的本征态为测量基
    elif base == "Y":
        qc.rx(0, pi/2)   # 用Y的本征态为测量基
    elif base == "Z":
        pass
    qc.measure([0])
    return qc

for strD in ['X','Y','Z']:
    # 得出单量子
    q=create_circuit(theta=2*pi/3, phi=pi/4,base=strD)
    # 模拟运行
    sim_res = simulate(q)
    # 计算密度函数
    sim_rho = sim_res.calc_density_matrix()
    # 计算期望值
    # 先点成 密度函数 · 泡利 Z 矩阵
    # 再求迹
    expectation_value = np.trace(np.dot(sim_rho, sigma_Z)).real
    print( strD + " 期望值为:" + str(expectation_value))

# 且计算当 Z 轴测量时 ( 即不旋转测量子比特 )
# 得出单量子
q=create_circuit(theta=2*pi/3, phi=pi/4)
# 模拟运行
sim_res = simulate(q)
# 计算密度函数
sim_rho = sim_res.calc_density_matrix()
print(sim_rho)

```


**得出一样的结果**

![alt text](/photo/InPost/SanShiShu/80/4.png)

我悟了！！！



## 4. 那多比特呢！？

那就非常复杂了，得看张量积...
那又是啥.....
我得回家吃饭了
下次再说