---
title: 5. 结论
description: 结论
date: 2023-10-30
weight: 50
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;

$ \rightleftharpoons $






---

&#128311;已知$f(x)$在$\text{R}$上可导

（1）若$f(x)$既是周期函数又是偶函数，证明：$f^{'}(x)$既是周期函数又是奇函数

（2）若$f(x)$既是周期函数又是奇函数，证明：$f^{'}(x)$既是周期函数又是偶函数

{{<note>}}

1. 复合函数求导

2. 同时求导

3. 导数定义


结论：原函数与导函数有相同的周期，奇偶性变化

{{</note>}}

【**解析**】

（1）$f(x)$为偶函数，可得：$f(x) = f(-x)$

两边同时求导，可得：$ f^{'}(x) = -f^{'}(-x) $

记$f(x)$的周期为$T$，可得：$ f(x) = f(x + T) $

由导数定义，可得：

$\begin{aligned} f^{'}(x + T)  &=\lim\limits_{ \Delta x \to 0} \cfrac{ f(x + T + \Delta x) - f(x + T) }{\Delta x} \\\\[2ex]
&=\lim\limits_{ \Delta x \to 0} \cfrac{ f(x + \Delta x) - f(x) }{\Delta x} \\\\[2ex]
&=f^{'}(x)
\end{aligned}$

综上，$f^{'}(x)$既是周期函数又是奇函数

（2）$f(x)$为奇函数，可得：$f(x) = -f(-x)$

两边同时求导，可得：$ f^{'}(x) = f^{'}(-x) $

记$f(x)$的周期为$T$，可得：$ f(x) = f(x + T) $

由导数定义，可得：

$\begin{aligned} f^{'}(x + T)  &=\lim\limits_{ \Delta x \to 0} \cfrac{ f(x + T + \Delta x) - f(x + T) }{\Delta x} \\\\[2ex]
&=\lim\limits_{ \Delta x \to 0} \cfrac{ f(x + \Delta x) - f(x) }{\Delta x} \\\\[2ex]
&=f^{'}(x)
\end{aligned}$

综上，$f^{'}(x)$既是周期函数又是偶函数
















