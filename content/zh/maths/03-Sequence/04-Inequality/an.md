---
title: an不等式
description: >
  an不等式
date: 2023-10-29
weight: 4


---

{{< alert >}}
This is a placeholder page. Replace it with your own content.
{{< /alert >}}

### 递推关系包含自变量$n$

【】已知数列$\\{ a_n  \\}$满足：$a_1 = 1$，$a_{n+1} = (1 + \cfrac{1}{n^2 + n})a_n + \cfrac{1}{2^n}$

（1）证明：$a_n \ge 2$（$n  \ge 2$）

（2）证明：$a_n < \text{e}^2$

【解析】（1）

（2）下面用数学归纳法证明：

$\textcircled{1}$ 当$n = 1$时，不等式成立，可得：$a_1 = 1 < \text{e}^2$

$\textcircled{2}$ 假设当$n = k$时，不等式成立，可得：$a_k < \text{e}^2$

$\textcircled{3}$ 则当$n = k+1$时，可得：$a_{k+1} = (1 + \cfrac{1}{k^2 + k})a_k + \cfrac{1}{2^k}$


### 递推关系不包含自变量$n$

【】已知数列$\\{ a_n  \\}$满足：$0<a_n<1$，$a_{n+1}=\text{e}^{ a_n}-1-\sin{a_n}$. 证明：$a_n$是递减数列

【解析】$a_{n+1}-a_n=\text{e}^{ a_n}-1-\sin{a_n}-a_n$

设$f(x)=\text{e}^{ x}-1-\sin{x}-x$，$0<x<1$

对$f(x)$求导，可得：$F_1(x)=f^{'}(x)=\text{e}^{ x}-\cos{x}-1$

对$F_1(x)$求导，可得：$F_2(x)=F_1^{'}(x)=\text{e}^{ x}+\sin{x}$

故当$0<x<1$时，可得：$F_2(x) > 0$

故$F_1(x)$在$(0,1)$上单调递增，可得：$F_1(0) = -1 < 0 $，$F_1(1)=\text{e}-\cos{1}-1 > 0$

故存在$x_1 \in (0,1)$，使得$F_1(x_1)=0$

故$f(x)$在区间$(0,x_1)$上单调递减，在区间$(x_1,1)$上单调递增

可得：$f(0)=0$，$f(1)=\text{e}-2-\sin{1}<0$

故当$x_1 \in (0,1)$时，可得：$f(x)<0$

综上，$a_{n+1}-a_n < 0$，即$a_n$是递减数列









### 例题

【】已知数列$\\{ a_n  \\}$满足：$0 < a_1 < 1$,$a_{n+1} = a_n - \ln(1+a_n)$

（1）证明：$0 < a_n < 1$

（2）证明：$2a_{n+1} < a_n^2$

（3）若$a_1 = \cfrac{1}{2}$，记数列$\\{ a_n  \\}$的前$n$项和为$S_n$，证明：$S_n < \cfrac{9}{16}$
{{< alert color="secondary" title="思路" >}}

{{< /alert >}}

【解析】（1）下面用数学归纳法证明：

$\textcircled{1}$ 当$n = 1$时，不等式成立，即$0 < a_1 < 1$

$\textcircled{2}$ 假设当$n = k$时，结论成立，可得：$0 < a_k < 1$

$\textcircled{3}$ 则当$n = k+1$时，可得：$a_{k+1} = a_k - \ln(1+a_k) $

记$f(x) = x - \ln(1+x)$（$0 < x < 1$），可得：$f^{'}(x) = 1 - \cfrac{1}{1+x} = \cfrac{x}{1+x} > 0$

故$f(x) = x - \ln(1+x)$在区间$(0,1)$上单调递增，可得：$0 - \ln(1+0) = 0 < a_{k+1} < 1 - \ln(1+1) = 1 - \ln(2) < 1$

综上，可得：$0 < a_n < 1$

（2）由题意，可得：$a_{n+1} = a_n - \ln(1+a_n)$

要证$2a_{n+1} < a_n^2$，只需证：$a_n^2 - 2a_n + 2\ln(1+a_n) > 0$

记$f(x) = x^2 -2x + 2\ln(1+x)$（$0 < x < 1$），可得：$f^{'}(x) = 2x - 2 + \cfrac{2}{1+x} = \cfrac{2x^2}{1+x} > 0$

故$f(x) = x^2 -2x + 2\ln(1+x)$在区间$(0,1)$上单调递增，可得：$ f(x) > f(0) = 0 $

综上，可得：$2a_{n+1} < a_n^2$



### 例题

【】 已知$a_1=2$，$a_{n+1} = \cfrac{a_n}{2} + \cfrac{1}{a_n}$，证明：$\sqrt{2} < a_n < \sqrt{2} + \cfrac{1}{n}$

【解析】下面用数学归纳法证明：

$\textcircled{1}$ 当$n = 1$时，不等式成立，即$\sqrt{2} < a_1 = 2 < \sqrt{2} + 1$

$\textcircled{2}$ 假设当$n = k$时，结论成立，可得：$\sqrt{2} < a_k < \sqrt{2} + \cfrac{1}{k}$

$\textcircled{3}$ 则当$n = k+1$时，可得：$a_{k+1} = \cfrac{a_k}{2} + \cfrac{1}{a_k}$

又$f(x) = \cfrac{x}{2} + \cfrac{1}{x}$在区间$\left[ \sqrt{2}, + \infty  \right)$上单调递增，且$\sqrt{2} < a_k < \sqrt{2} + \cfrac{1}{k}$

可得：$\cfrac{\sqrt{2}}{2} + \cfrac{1}{\sqrt{2}} < a_{k+1} < \cfrac{\sqrt{2} + \cfrac{1}{k}}{2} + \cfrac{1}{\sqrt{2} + \cfrac{1}{k}}$

化简，可得：$\sqrt{2} < a_{k+1} < \cfrac{\sqrt{2}}{2} + \cfrac{1}{2k} + \cfrac{k}{1 + \sqrt{2}k} < \cfrac{\sqrt{2}}{2} + \cfrac{1}{2k} + \cfrac{1}{\sqrt{2}} < \sqrt{2} + \cfrac{1}{k + 1}$

故当$n = k+1$时，$\sqrt{2} < a_{k+1} < \sqrt{2} + \cfrac{1}{k+1}$，可得：$\sqrt{2} < a_n < \sqrt{2} + \cfrac{1}{n}$





### 例题

【】 已知$a_n > 0$，且$a_{n}^2 \le a_n - a_{n+1}$（$n \ge 1$），证明：$a_n < \cfrac{1}{n}$

【解析】下面用数学归纳法证明：

$\textcircled{1}$ 当$n = 1$时，$a_{1}^2 \le a_1 - a_{2}$，即$a_{2} \le a_1 - a_{1}^2 $

又$a_n > 0$，可得：$0 < a_{2} \le a_1 - a_{1}^2 $

可得：$0 < a_{1} < 1 $

当$n = 2$时，可得：$a_{2} \le a_1 - a_{1}^2 $

可得：$a_{2} \le \cfrac{1}{2} - (\cfrac{1}{2})^2 < \cfrac{1}{2} $


$\textcircled{2}$ 假设当$n = k$（$k \ge 2$）时，结论成立，可得：$a_k < \cfrac{1}{k}$

$\textcircled{3}$ 则当$n = k+1$时，可得：$a_{k+1} \le a_k - a_{k}^2 $

又$f(x) = x - x^2$在区间$(0, \cfrac{1}{2} )$上单调递增，且$a_k < \cfrac{1}{k}$

可得：$a_{k+1} \le a_k - a_{k}^2 <  \cfrac{1}{k} - (\cfrac{1}{k})^2 = \cfrac{k-1}{k^2} < \cfrac{1}{k+1}$

故当$n = k+1$时，$a_k < \cfrac{1}{k+1}$，可得：$a_n < \cfrac{1}{n}$










