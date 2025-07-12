---
title: 通项已知，放缩等比
description: >
  通项已知，放缩等比
date: 2017-01-05
weight: 2


---

{{< alert >}}

若通项含有指数类的形式时，通项相邻两项比值的极限为放缩等比数列的公比

{{< /alert >}}


> There should be no margin above this first sentence.
>
> Blockquotes should be a lighter gray with a border along the left side in the secondary color.
>
> There should be no margin below this final sentence.





【】证明：$1+\cfrac{1}{2^2-1}+\cfrac{1}{2^3-1}+\cdots+\cfrac{1}{2^n-1}<\cfrac{5}{3}$

【解析】令$a_n=\cfrac{1}{2^n-1}$，可得：$\cfrac{a_{n+1}}{a_n}=\cfrac{1}{2}\left(\cfrac{2^n-1}{2^n-\frac{1}{2}} \right)<\cfrac{1}{2}$

假设当$n=k$时，不等式成立，可得：$\cfrac{1}{2}+\cfrac{1}{2^2}+\cdots+\cfrac{1}{2^k} < 1$

则当$n=k+1$时，可得：$\cfrac{1}{2}+\cfrac{1}{2^2}+\cdots+\cfrac{1}{2^k}+ \cfrac{1}{2^{k+1}}=\cfrac{1}{2}+\cfrac{1}{2}\left(\cfrac{1}{2}+\cfrac{1}{2^2}+\cdots+\cfrac{1}{2^k}  \right) < \cfrac{1}{2}+\cfrac{1}{2}=1$

综上，可得：$\cfrac{1}{2}+\cfrac{1}{2^2}+\cdots+\cfrac{1}{2^n} < 1$




#### 通项放缩为等比数列

【】证明：$\cfrac{n}{2} - \cfrac{1}{3} < \cfrac{2^1 - 1}{2^2 -1} + \cfrac{2^2-1}{2^3 - 1} + \cdots + \cfrac{2^{n} - 1}{2^{n+1} -1} < \cfrac{n}{2}$

【解析】


【思路】通项分析：$a_n = \cfrac{2^{n} - 1}{2^{n+1} -1} < \cfrac{1}{2}$

$b_n = \cfrac{1}{2} -\cfrac{2^{n} - 1}{2^{n+1} -1} = \cfrac{1}{3 \times 2^n + 2^n -2} < \cfrac{1}{3 \times 2^n}$




#### 通项放缩为等比数列

【】证明：$\cfrac{1}{3 + 1} + \cfrac{1}{3 \times 2 + 1} + \cfrac{1}{3 \times 2^2+ 1} + \cdots + \cfrac{1}{3 \times 2^{n-1} + 1} < \cfrac{2}{3}$

【解析】


【思路】通项分析：$a_n = \cfrac{1}{3 \times 2^{n-1} + 1} < \cfrac{1}{3 \times 2^{n-1}}$

$$ \sum_{i=1}^{i=n}\cfrac{1}{3 \times 2^{i-1}} =  \cfrac{2}{3}(1 - \cfrac{1}{2^n})$$

#### 通项放缩为等比数列

【】证明：$a_n =  \cfrac{1}{2\left(\cfrac{3}{2}\right)^{n-1} -1} $，证明：$a_1 + a_2 + a_3 + \cdots + a_n < \cfrac{5}{2}$

【思路】通项分析：$a_n =  \cfrac{1}{2\left(\cfrac{3}{2}\right)^{n-1} -1} < \left(\cfrac{2}{3}\right)^n$（$n \ge 3$）

可得：$1 + \cfrac{1}{2} + \left(\cfrac{2}{3}\right) + \left(\cfrac{2}{3}\right)^2 + \cdots + \left(\cfrac{2}{3}\right)^n = \cfrac{7}{18} + 2 - 2\times \left(\cfrac{2}{3}\right)^n < \cfrac{7}{18} + 2 < \cfrac{5}{2}$

【解析】


{{< alert color="warning" title="Warning" >}}This is a warning with a title.{{< /alert >}}


#### 通项放缩为等比数列

【】证明：$\cfrac{1}{2 - 1} + \cfrac{1}{2^2 - 1} +\cdots + \cfrac{1}{2^n - 1} < \cfrac{5}{3}$

【思路】通项分析：$a_n =  \cfrac{1}{2^n - 1} < \cfrac{1}{3} \times \cfrac{1}{2^{n-2}}$（$n \ge 2$）

可得：$\lt 1 +  \cfrac{1}{3}\left(1 + \cfrac{1}{2} + \cdots + \cfrac{1}{2^{n-2}} \right) = 1 +  \cfrac{1}{3}\left(2 - \cfrac{1}{2^{n-1}} \right)$

【解析】























