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

1. 对于人与向量 **u**, **v** 和 **w** 都属于 \\( R^n \\), 任意标量 a
和 b 则有以下性质(u’ is the additive inverse of u)

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

## 3, 矩阵（Matrix）

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

