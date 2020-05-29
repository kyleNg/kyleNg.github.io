---
layout: post
category:  Linear Algebra
title: 线性代数（LinearAlgebra）
tags: Linear Algebra
---
<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

# **线性代数笔记 01** #

## 1，向量（Vector）

- vector就是一组数字，有两种，一种是row vector，一种是column vector。一般而言，我们没有特殊声明，说一个vector就是一个column vector。

$$ row \quad vector:\begin{vmatrix}{a_{1}}&{a_{2}}&{\cdots}&{a_{n}}\end{vmatrix}$$
$$ column \quad vector:\begin{vmatrix}{a_{1}}\\\\{a_{2}}\\\\{\vdots}\\\\{a_{n}}\end{vmatrix}$$

## 2, 向量空间（Spaces of Vectors）

- 空间表示有很多向量，一整个空间的向量，但并不是任意向量的组合，能称为空间的，空间本身必须满足一定的规则。必须能够进行加法和数乘运算，也就是说，必须能够线性组合。

- 二维向量空间

    \\( R^2 \\) = All 2 dimensional real vectors.
    $$v=\begin{vmatrix}{x_{1}}\\\\{x_{2}}\end{vmatrix}$$
    $$ (x_1 , x_2\in R) $$

    由上面的公式不难理解\\( R^2 \\)就是整个二维平面，那么就不难理解\\( R^3 \\)就包含了整个三维空间的所有向量。

    \\( R^n \\) = all column vectors with n real components.

## 3, 向量的性质（Properties of Vector）

对于向量 **u**, **v** 和 **w** 都属于 \\( R^n \\), 任意标量 a和 b 则有以下性质

- 交换性 (commutativity)：
    + \\( u + v = v + u \\)

- 结合性 (associativity)：
    + \\( (u + v) + w = u + (v + w) \\)

- 加法单位元 (additive identity)：
    + \\( 𝟎 + u = u \\)

- 加法逆元素 (additive inverse)：
    + \\( u’ + u = 0 \\)

- 乘法单位元 (multiplicative identity)：
    + \\( 1u = u \\)

- 分配性 (distributivity)：
    + \\( a(u+v) = au + av \\)
    + \\( (ab)u = a(bu) \\)
    + \\( (a+b)u = au + bu \\)

## 4, 矩阵（Matrix）

matrix就是一个vector set。一般我们用大写加粗字母表示，比如**M**。每一个元素用相同字母带上下标表示，比如\\( m_{ij}\\)，其中i表示第i行，j表示第j列。同样处于方便，很多时候直接用大写的字母表示矩阵。我们会叫一个行列相同的矩阵是方阵（square matrix）。



$$matrix： \quad \begin{vmatrix}
{a_{11}}&{a_{12}}&{\cdots}&{a_{1n}}\\\\
{a_{21}}&{a_{22}}&{\cdots}&{a_{2n}}\\\\
{\vdots}&{\vdots}&{\ddots}&{\vdots}\\\\
{a_{m1}}&{a_{m2}}&{\cdots}&{a_{mn}}\\\\
\end{vmatrix}$$


$$square \quad matrix： \quad \begin{vmatrix}
{a_{11}}&{a_{12}}&{\cdots}&{a_{1n}}\\\\
{a_{21}}&{a_{22}}&{\cdots}&{a_{2n}}\\\\
{\vdots}&{\vdots}&{\ddots}&{\vdots}\\\\
{a_{n1}}&{a_{n2}}&{\cdots}&{a_{nn}}\\\\
\end{vmatrix}$$

## 5, 特殊矩阵

- 零矩阵（Zero Matrix）

    零矩阵即所有元素皆为0的矩阵

$$O_{mn}： \quad \begin{vmatrix}
{0}&{0}&{\cdots}&{0}\\\\
{0}&{0}&{\cdots}&{0}\\\\
{\vdots}&{\vdots}&{\ddots}&{\vdots}\\\\
{0}&{0}&{\cdots}&{0}\\\\
\end{vmatrix}$$

- 单位矩阵（Identity matrix）: must be square

    行列必须相等，对角线全部为1，其余entities都是0

$$I_{nn}： \quad \begin{vmatrix}
{1}&{0}&{\cdots}&{0}\\\\
{0}&{1}&{\cdots}&{0}\\\\
{\vdots}&{\vdots}&{\ddots}&{\vdots}\\\\
{0}&{0}&{\cdots}&{1}\\\\
\end{vmatrix}$$ 

## 6, 矩阵的性质（Properties of Matrix）

对于**A**, **B**, **C** 是 mxn 的矩阵, 并且 s 和 t 是任意标量

- 交换性 (commutativity)：
    + \\( A + B = B + A \\)

- 结合性 (associativity)：
    + \\( (A + B) + C = A + (B + C) \\)
    + \\( (st)A = s(tA) \\)
    + \\( s(A + B) = sA + sB \\)

- 分配性 (distributivity)：
    + \\( (s+t)A = sA + tA \\)
t
## 7，矩阵的转置（Transpose）

将矩阵A的行换成同序号的列所得到的新矩阵称为矩阵A的转置矩阵，记作\\( A^T \\)或\\( A' \\)

例
$$ A \quad = \quad \begin{vmatrix}
{1}&{0}&{3}&{-1}\\\\
{2}&{1}&{0}&{2}\\\\
\end{vmatrix}$$

的专职矩阵为

$$ A^T \quad = \quad A' \quad = \quad \begin{vmatrix}
{1}&{2}\\\\
{0}&{1}\\\\
{3}&{0}\\\\
{-1}&{2}\\\\
\end{vmatrix}$$

运算性质（假设所有运算都是可行的）

- \\( (A')' = A \\)
- \\( (A+B)' = A' + B' \\)
- \\( (AB)' = A'B' \\)
- \\( (\lambda A)' = \lambda A' \quad \lambda 是常数\\) 