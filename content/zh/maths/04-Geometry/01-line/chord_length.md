---
title: 弦长
description: chord length
date: 2025-05-17
weight: 2
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;

$ \rightleftharpoons $


{{<alert title="Tip" >}}

1. 放缩方向保持一致

2. 预处理
   1. 因式分解
   2. 同时取对数、取指数
   3. 换元同构
   4. 分离对数

3. 寻找放缩源

4. 用在哪个阶段？直接、一阶、二阶、。。。


{{</alert>}}





---


&#128311;已知直线$y=kx+b$与任意曲线$\Omega$相交于两点$A(x_1,y_1)$，$B(x_2,y_2)$，求弦长$\vert AB \vert$

{{<alert title="提示" >}}

- 任意曲线就包括圆、椭圆、双曲线、抛物线，但不限于此
- $ \left| x_2 - x_1 \right| = \sqrt{ (x_1 + x_2)^2 - 4 x_1 x_2 }  $
- $ \left| y_2 - y_1 \right| = \sqrt{ (y_1 + y_2)^2 - 4 y_1 y_2 }  $

{{</alert>}}


【解析】点$A(x_1,y_1)$在直线$y=kx+b$上，可得：$ y_1 = k x_1 + b $

点$B(x_2,y_2)$在直线$y=kx+b$上，可得：$ y_2 = k x_2 + b $

由两点间距离公式可得：

$\begin{aligned} \vert AB \vert  &= \sqrt{ (x_2 - x_1)^2 + (y_2 - y_1)^2 } \\\\
&= \sqrt{ (x_2 - x_1)^2 + k^2(x_2 - x_1)^2 } \\\\
&= \sqrt{ 1 + k^2 } \vert x_2 - x_1 \vert
\end{aligned}$

同理，可得：

$\begin{aligned} \vert AB \vert  &= \sqrt{ (x_2 - x_1)^2 + (y_2 - y_1)^2 } \\\\
&= \sqrt{ \cfrac{1}{k^2}(y_2 - y_1)^2 + (y_2 - y_1)^2 } \\\\
&= \sqrt{ 1 + \frac{1}{k^2} } \left| y_2 - y_1 \right|
\end{aligned}$























