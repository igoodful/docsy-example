---
title: 函数
description: Function
date: 2023-10-06
weight: 400


---

Function[Function, Number a, Number b]：生成函数 f 在 [a,b]区间上函数图像。

举例说明：f(x) = Function[x^2, -1, 1]得到函数x^2在区间[-1,1]上的函数图像。




## 条件函数
如果要建立条件函数可以使用布尔命令 If。如：f(x) = If[x<3, sin(x), x^2]相当于：

## 导数（微分）
Derivative[Function]：求函数 f(x)的导数（微分）。
Derivative[Function, Number n]：求求函数 f(x)的 n 阶导数（n 次微分）。

## 积分
Integral[Function]：求函数 f(x)的不定积分。

## 多项式
Polynomial[Function]：求函数 f 的展开式。
Polynomial[List of n points]：建立经过 n 个点的 n-1 次多项式。

## 展开式
Expand[Function]：将式子按乘法展开。

## 因式
Factor[Polynomial]：将多项式转换成因式乘法形式。
## 化简
Simplify[Function]：化简给定的函数。

·Simplify[x + x + x]得到 f(x)=3x
·Simplify[sin(x) / cos(x)]得到 f(x)=tan(x)
·Simplify[-2 sin(x) cos(x)]得到 f(x)=sin(-2x)

## 泰勒展开式
TaylorPolynomial[Function, Number a, Number n]：建立函数 f(x)在点 x=a 的 n 次泰勒展
开式。







