---
layout: post
title:  量子计算 - 02 - 数学基础与狄拉克符号
date:   2025-03-10 17:10:13 +0800
categories: 崔BB教学大纲
author: 崔秉龙
location: Guangzhou, China
---


以下数据均从各大地方搜刮下来，有些是 AI 生成的...很难一一列明出处

## 1. 量子比特的基本状态

上网查下资料

> 在量子计算中，|0⟩ 和 |1⟩ 是量子比特（qubits）的基本状态，它们通常被表示为二维复向量。这些向量可以用矩阵形式表示，通常作为列向量。以下是 |0⟩ 和 |1⟩ 的矩阵形式：
>
> |0⟩ 的矩阵形式：
>
> \[ |0⟩ = \begin{pmatrix} 1 \\ 0 \end{pmatrix} \]
>
> |1⟩ 的矩阵形式：
> \[ |1⟩ = \begin{pmatrix} 0 \\ 1 \end{pmatrix} \]
>
> 这里，|0⟩ 表示量子比特的基态，类似于经典计算中的0，而 |1⟩ 表示量子比特的激发态，类似于经典计算中的1。在量子力学中，这些状态也可以是量子叠加的，即量子比特可以同时处于 |0⟩ 和 |1⟩ 的状态。


## 2. 计算数学期望值

> 为了计算量子比特状态 \(| \psi \rangle\) 在观测算符 \(X\) 中的期望值 \(E(X)\)，我们需要进行以下矩阵运算：
> 1. **定义量子比特的状态** \(| \psi \rangle\)：
>    假设 \(| \psi \rangle\) 是一个归一化的量子态，可以表示为：
>    \[
>    | \psi \rangle = \begin{pmatrix} \alpha \\ \beta \end{pmatrix}
>    \]
>    其中，\(\alpha\) 和 \(\beta\) 是复数，且满足 \(\langle \psi | \psi \rangle = |\alpha|^2 + |\beta|^2 = 1\)。
> 2. **定义观测算符** \(X\)（泡利矩阵）：
>    \[
>    X = \begin{pmatrix} 0 & 1 \\ 1 & 0 \end{pmatrix}
>    \]
> 3. **计算 \(| \psi \rangle\) 的共轭转置** \(\langle \psi |\)：
>    \[
>    \langle \psi | = \begin{pmatrix} \alpha^* & \beta^* \end{pmatrix}
>    \]
>    其中，\(\alpha^*\) 和 \(\beta^*\) 分别是 \(\alpha\) 和 \(\beta\) 的复共轭。
> 4. **计算期望值** \(E(X)\)：
>    期望值 \(E(X)\) 是通过以下矩阵运算得到的：
>    \[
>    E(X) = \langle \psi | X | \psi \rangle
>    \]
>    这意味着我们需要先进行矩阵乘法 \(X | \psi \rangle\)，然后将结果与 \(\langle \psi |\) 进行内积。
>    首先，计算 \(X | \psi \rangle\)：
>    \[
>    X | \psi \rangle = \begin{pmatrix} 0 & 1 \\ 1 & 0 \end{pmatrix} \begin{pmatrix} \alpha \\ \beta \end{pmatrix} = \begin{pmatrix} \beta \\ \alpha \end{pmatrix}
>    \]
>    然后，计算 \(\langle \psi | X | \psi \rangle\)：
>    \[
>    \langle \psi | X | \psi \rangle = \begin{pmatrix} \alpha^* & \beta^* \end{pmatrix} \begin{pmatrix} \beta \\ \alpha \end{pmatrix} = \alpha^* \beta + \beta^* \alpha
>    \]
> 所以，期望值 \(E(X)\) 的矩阵计算过程如下：
> \[
> E(X) = \alpha^* \beta + \beta^* \alpha
> \]
> 这里使用的矩阵运算包括：
> - 矩阵乘法（观测算符作用在量子态上）
> - 共轭转置（得到量子态的bra表示）
> - 内积（计算bra和ket的乘积，得到期望值）
> 注意，\(\alpha^* \beta + \beta^* \alpha\) 是一个实数，因为它是两个复数的和，其中每个复数都是另一个复数的共轭。


## 3. 矩阵相乘



> 让我们用公式列出计算一个 3x4 的矩阵与一个 4x2 的矩阵的乘法过程。
> 设矩阵A为 3x4 矩阵，矩阵B为 4x2 矩阵，矩阵C为它们的乘积，即 3x2 矩阵。矩阵A、B和C的元素分别表示为：
> 矩阵A：
> \[ A = \begin{pmatrix}
> a_{11} & a_{12} & a_{13} & a_{14} \\
> a_{21} & a_{22} & a_{23} & a_{24} \\
> a_{31} & a_{32} & a_{33} & a_{34}
> \end{pmatrix} \]
> 矩阵B：
> \[ B = \begin{pmatrix}
> b_{11} & b_{12} \\
> b_{21} & b_{22} \\
> b_{31} & b_{32} \\
> b_{41} & b_{42}
> \end{pmatrix} \]
> 矩阵C（A乘以B的结果）：
> \[ C = \begin{pmatrix}
> c_{11} & c_{12} \\
> c_{21} & c_{22} \\
> c_{31} & c_{32}
> \end{pmatrix} \]
> 矩阵C的每个元素计算如下：
> \[ c_{11} = a_{11}b_{11} + a_{12}b_{21} + a_{13}b_{31} + a_{14}b_{41} \]
> \[ c_{12} = a_{11}b_{12} + a_{12}b_{22} + a_{13}b_{32} + a_{14}b_{42} \]
> \[ c_{21} = a_{21}b_{11} + a_{22}b_{21} + a_{23}b_{31} + a_{24}b_{41} \]
> \[ c_{22} = a_{21}b_{12} + a_{22}b_{22} + a_{23}b_{32} + a_{24}b_{42} \]
> \[ c_{31} = a_{31}b_{11} + a_{32}b_{21} + a_{33}b_{31} + a_{34}b_{41} \]
> \[ c_{32} = a_{31}b_{12} + a_{32}b_{22} + a_{33}b_{32} + a_{34}b_{42} \]


## 4. 矩阵的迹

> 矩阵的迹（Trace）是指矩阵主对角线上所有元素的和。对于任意方形矩阵（即行数和列数相等的矩阵），其迹可以通过将主对角线上的元素相加来计算。
> 设矩阵 \( A \) 为一个 \( n \times n \) 的方形矩阵，其元素表示为 \( a_{ij} \)，其中 \( i \) 和 \( j \) 分别表示行号和列号。那么，矩阵 \( A \) 的迹 \( \text{Tr}(A) \) 可以表示为：
> \[ \text{Tr}(A) = a_{11} + a_{22} + \cdots + a_{nn} \]
> 即：
> \[ \text{Tr}(A) = \sum_{i=1}^{n} a_{ii} \]
> 这里，\( \sum \) 表示求和符号。
> 例如，对于一个 3x3 矩阵：
> \[ A = \begin{pmatrix}
> a_{11} & a_{12} & a_{13} \\
> a_{21} & a_{22} & a_{23} \\
> a_{31} & a_{32} & a_{33}
> \end{pmatrix} \]
> 其迹为：
> \[ \text{Tr}(A) = a_{11} + a_{22} + a_{33} \]
> 请注意，只有方形矩阵才有迹的概念，因为非方形矩阵没有完整的主对角线。

## 5. 张量积

> 张量积的计算涉及将两个向量或矩阵结合成一个更高维度的对象。以下是张量积的基本计算方法：
> ### 向量的张量积
> 假设有两个向量 \( \mathbf{v} \) 和 \( \mathbf{w} \)，它们分别是 \( n \) 维和 \( m \) 维的列向量：
> \[ \mathbf{v} = \begin{pmatrix} v_1 \\ v_2 \\ \vdots \\ v_n \end{pmatrix}, \quad \mathbf{w} = \begin{pmatrix} w_1 \\ w_2 \\ \vdots \\ w_m \end{pmatrix} \]
> 那么它们的张量积 \( \mathbf{v} \otimes \mathbf{w} \) 是一个 \( nm \) 维的列向量，其元素是 \( \mathbf{v} \) 的每个元素与 \( \mathbf{w} \) 的每个元素的乘积：
> \[ \mathbf{v} \otimes \mathbf{w} = \begin{pmatrix} v_1 w_1 \\ v_1 w_2 \\ \vdots \\ v_1 w_m \\ v_2 w_1 \\ v_2 w_2 \\ \vdots \\ v_n w_m \end{pmatrix} \]
> ### 矩阵的张量积
> 假设有两个矩阵 \( A \) 和 \( B \)，它们分别是 \( p \times q \) 和 \( r \times s \) 的矩阵：
> \[ A = \begin{pmatrix} a_{11} & a_{12} & \cdots & a_{1q} \\ a_{21} & a_{22} & \cdots & a_{2q} \\ \vdots & \vdots & \ddots & \vdots \\ a_{p1} & a_{p2} & \cdots & a_{pq} \end{pmatrix}, \quad B = \begin{pmatrix} b_{11} & b_{12} & \cdots & b_{1s} \\ b_{21} & b_{22} & \cdots & b_{2s} \\ \vdots & \vdots & \ddots & \vdots \\ b_{r1} & b_{r2} & \cdots & b_{rs} \end{pmatrix} \]
> 那么它们的张量积 \( A \otimes B \) 是一个 \( pr \times qs \) 的矩阵，其元素是 \( A \) 的每个元素与 \( B \) 的整个矩阵的乘积：
> \[ A \otimes B = \begin{pmatrix} a_{11}B & a_{12}B & \cdots & a_{1q}B \\ a_{21}B & a_{22}B & \cdots & a_{2q}B \\ \vdots & \vdots & \ddots & \vdots \\ a_{p1}B & a_{p2}B & \cdots & a_{pq}B \end{pmatrix} \]
> 每个 \( a_{ij}B \) 是一个 \( r \times s \) 的矩阵，其中每个元素是 \( a_{ij} \) 乘以 \( B \) 的相应元素。
> ### 示例
> 假设 \( \mathbf{v} = \begin{pmatrix} 1 \\ 2 \end{pmatrix} \) 和 \( \mathbf{w} = \begin{pmatrix} 3 \\ 4 \end{pmatrix} \)，那么：
> \[ \mathbf{v} \otimes \mathbf{w} = \begin{pmatrix} 1 \cdot 3 \\ 1 \cdot 4 \\ 2 \cdot 3 \\ 2 \cdot 4 \end{pmatrix} = \begin{pmatrix} 3 \\ 4 \\ 6 \\ 8 \end{pmatrix} \]
> 假设 \( A = \begin{pmatrix} 1 & 2 \\ 3 & 4 \end{pmatrix} \) 和 \( B = \begin{pmatrix} 0 & 5 \\ 6 & 7 \end{pmatrix} \)，那么：
> \[ A \otimes B = \begin{pmatrix} 1 \cdot \begin{pmatrix} 0 & 5 \\ 6 & 7 \end{pmatrix} & 2 \cdot \begin{pmatrix} 0 & 5 \\ 6 & 7 \end{pmatrix} \\ 3 \cdot \begin{pmatrix} 0 & 5 \\ 6 & 7 \end{pmatrix} & 4 \cdot \begin{pmatrix} 0 & 5 \\ 6 & 7 \end{pmatrix} \end{pmatrix} = \begin{pmatrix} 0 & 5 & 0 & 10 \\ 6 & 7 & 12 & 14 \\ 0 & 15 & 0 & 20 \\ 18 & 21 & 24 & 28 \end{pmatrix} \]
> 张量积在量子计算中用于描述多个量子比特的联合状态和操作，是构建量子电路和量子算法的基础。
