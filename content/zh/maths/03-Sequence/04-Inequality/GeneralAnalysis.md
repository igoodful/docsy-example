---
title: 通项已知，通项分析
description: >
  形如$\sum\limits_{i=1}^{i=n} < f(n)$数列不等式不等式的证明
date: 2023-10-25
weight: 2


---

{{< alert >}}

通项已知，通项分析

{{< /alert >}}





#### 通项分析找不等式

（1）证明：$\sum\limits_{k=1}^{k=n} \cfrac{1}{k^2} > \cfrac{n}{n+1}$



{{< alert color="primary" title="思路" >}}
（1）$a_n = \cfrac{1}{n^2}$，$b_n = f(n) -f(n-1) = \cfrac{n}{n+1} - \cfrac{n-1}{n}$，只需：$b_n < a_n$（$n \ge 2$）

{{< /alert >}}

【解析】（1）由题意，可得：$a_n = \cfrac{1}{n^2} > \cfrac{1}{n(n+1)} = \cfrac{1}{n} - \cfrac{1}{n+1}$

可得：$\sum\limits_{k=1}^{k=n} \cfrac{1}{k^2} > (\cfrac{1}{1} - \cfrac{1}{2}) + (\cfrac{1}{2} - \cfrac{1}{3}) + \cdots + (\cfrac{1}{n} - \cfrac{1}{n+1}) = \cfrac{n}{n+1}$




























