---
title: 6. 周期性
description: Cycle
date: 2024-09-05
weight: 600
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;

$ \rightleftharpoons $


{{<note >}}


已知一个周期内的表达式，根据周期求函数解析式

{{</note>}}



---

&#128311;已知函数$f(x)$的定义域为$ \operatorname{R} $，且$ f(x) + f(2-x) = 0,f(x+1) = - \cfrac{1}{f(x)} $，当$0 \le x \le \cfrac{1}{2}$时，$f(x) = 3^x$

（1）证明：$ f(x) $的最小正周期为$2$

（2）证明：$f(x)$为奇函数

（3）求$f(x)$的解析式


{{<note >}}

- 抽象函数》递推方程

{{</note>}}

【**解析**】

（1）依题意，可知：$ f(x+1) = - \cfrac{1}{f(x)} $

令$x$替换为$x+1$，可得：$ f(x+2) = - \cfrac{1}{f(x+1)} $

综上，可得： $ f(x+2) = f(x) $

故$ f(x) $的最小正周期为$2$

（2）依题意，可知：$ f(x) + f(2-x) = 0 $

由（1）知$ f(x) $的最小正周期为$2$，可得：$ f(2-x) = f(-x) $

可得： $ f(x) = -f(-x) $

故$f(x)$为奇函数

（3）

$\textcircled{1}$当$0 \le x \le \cfrac{1}{2}$时，可知：$f(x) = 3^x$

$ \quad $当$2k \le x \le 2k + \cfrac{1}{2}$（$k \in \operatorname{Z}$）时，可得：$ 0 \le x-2k \le \cfrac{1}{2} $

$ \quad $$ f(x) $的周期为$2$，可得：$ f(x) = f(x-2k) = 3^{x-2k} $

$\textcircled{2}$当$ \cfrac{1}{2} \le x \le 1$时，可得：$0 \le 1-x \le \cfrac{1}{2}$

$ \quad $令$x$替换为$x-1$，可得：$ f(x) = - \cfrac{1}{f(x-1)} $

$ \quad $$f(x)$为奇函数，可得：$ f(x) = - \cfrac{1}{- f(1-x)} = \cfrac{1}{f(1-x)} = \cfrac{1}{3^{1-x} } = 3^{x-1}  $

$ \quad $当$2k +\cfrac{1}{2} \le x \le 2k + 1$（$k \in \operatorname{Z}$）时，可得：$ \cfrac{1}{2} \le x-2k \le 1 $

$ \quad $$ f(x) $的周期为$2$，可得：$ f(x) = f(x-2k) = 3^{x-2k-1} $

$\textcircled{3}$当$ -\cfrac{1}{2} \le x \le 0$时，可得：$0 \le -x \le \cfrac{1}{2}$

$ \quad $$f(x)$为奇函数，可得：$f(x) = -f(-x) = - 3^{-x} $

$ \quad $当$2k -\cfrac{1}{2} \le x \le 2k $（$k \in \operatorname{Z}$）时，可得：$ -\cfrac{1}{2} \le x-2k \le 0 $

$ \quad $$ f(x) $的周期为$2$，可得：$ f(x) = f(x-2k) = - 3^{- x+2k} $

$\textcircled{4}$当$ -1 \le x \le \cfrac{1}{2}$时，可得：$ \cfrac{1}{2} \le -x \le 1 $

$ \quad $$f(x)$为奇函数，可得：$f(x) = -f(-x) = - 3^{-x-1} $

$ \quad $当$2k -1 \le x \le 2k - \cfrac{1}{2}$（$k \in \operatorname{Z}$）时，可得：$ -1 \le x-2k \le -\cfrac{1}{2} $

$ \quad $$ f(x) $的周期为$2$，可得：$ f(x) = f(x-2k) = - 3^{-x+2k-1} $

综上，可得：$f(n)= \begin{cases} -3^{-x+2k-1}, &\text{$2k -1 \le x < 2k - \cfrac{1}{2}$} \\\\[2ex]
-3^{-x+2k}, &\text{$ 2k -\cfrac{1}{2} \le x < 2k $} \\\\[2ex]
3^{x-2k}, &\text{$2k \le x < 2k + \cfrac{1}{2}$} \\\\[2ex]
3^{x-2k-1}, & \text{$2k +\cfrac{1}{2} \le x \le 2k + 1$} \end{cases}$


（4）




