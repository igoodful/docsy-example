---
title: 通项已知，放缩裂项
description: >
  通项已知，放缩裂项
date: 2017-01-05
weight: 2


---

{{< alert >}}
- $\cfrac{1}{n^2} < \cfrac{1}{n^2 -\cfrac{1}{4}} = 2( \cfrac{1}{2n-1} - \cfrac{1}{2n+1})$

- $\cfrac{1}{n^3} < \cfrac{1}{n(n^2-1)} = \cfrac{1}{n(n-1)(n+1)} = \cfrac{1}{2}\left[ \cfrac{1}{n(n-1)} - \cfrac{1}{n(n+1)} \right]$（其中$n \ge 2$）

- $\cfrac{1}{\sqrt{n}} = \cfrac{2}{2\sqrt{n}} < \cfrac{2}{\sqrt{n} + \sqrt{n-1}} = 2(\sqrt{n} - \sqrt{n-1})$

- $\cfrac{1}{\sqrt{n}} = \cfrac{2}{2\sqrt{n}} > \cfrac{2}{\sqrt{n} + \sqrt{n+1}} = 2(\sqrt{n+1} - \sqrt{n})$

- $\cfrac{1}{n^{\frac{3}{2}}} = \cfrac{1}{n\sqrt{n}} < \cfrac{1}{n\sqrt{n-1}} = \cfrac{1}{\sqrt{n} \cdot  \sqrt{n} \cdot \sqrt{n-1}} = \cfrac{1}{\sqrt{n}}(\sqrt{n} + \sqrt{n-1})(\cfrac{1}{\sqrt{n-1}} - \cfrac{1}{\sqrt{n}}) < 2(\cfrac{1}{\sqrt{n-1}} - \cfrac{1}{\sqrt{n}})$ （其中$n \ge 2$）

- $\cfrac{1}{n^{\frac{3}{2}}} = \cfrac{1}{n\sqrt{n}} > \cfrac{1}{n\sqrt{n+1}} = \cfrac{1}{\sqrt{n} \cdot  \sqrt{n} \cdot \sqrt{n+1}} = \cfrac{\sqrt{n} + \sqrt{n+1}}{\sqrt{n}}( \cfrac{1}{\sqrt{n}} - \cfrac{1}{\sqrt{n+1}} ) > 2( \cfrac{1}{\sqrt{n}} - \cfrac{1}{\sqrt{n+1}} )$

{{< /alert >}}




### 幂函数类 $a_n = \cfrac{1}{n^k}$

（1）证明：$\sum\limits_{k=1}^{k=n} \cfrac{1}{k^2} < 2$.

（2）证明：$\sum\limits_{k=1}^{k=n} \cfrac{1}{k^2} < \cfrac{7}{4}$.

（3）证明：$\sum\limits_{k=1}^{k=n} \cfrac{1}{k^2} < \cfrac{5}{3}$.

（4）证明：$\sum\limits_{k=1}^{k=n} \cfrac{1}{(2k+1)^2} < \cfrac{1}{4}$.

（5）证明：$\sum\limits_{k=1}^{k=n} \cfrac{4}{(k+1)^2} < 3$.

{{< alert color="success" title="思路" >}}

求和不等式的通项为$a_n =\cfrac{1}{n^2}$，根据不等式符号方向可知，需要将$a_n $放大，而$a_n $是一个分式，分子为常数，只能将分母放小，因此，分母需要减去一个恰当的正数

- 当通项中$n$的系数不为$1$时，应先将系数提取出来，然后再减去$\cfrac{1}{4}$。比如$\sum\limits_{k=1}^{k=n} \cfrac{1}{(2k+1)^2} < \cfrac{1}{4}$中，通项$a_n =\cfrac{1}{(2n+1)^2} = \cfrac{1}{4} \cdot \cfrac{1}{(n+\cfrac{1}{2})^2} < \cfrac{1}{4} \cdot \cfrac{1}{(n+\cfrac{1}{2})^2 - \cfrac{1}{4}}$

- 减去的数越小越精确，但不一定还能进行裂项相消。$\cfrac{1}{n^2}$一般只需放缩到$\cfrac{1}{n^2 -\cfrac{1}{4}}$就足够。比如$\cfrac{1}{n^2} < \cfrac{1}{n^2 -\cfrac{1}{9}} = \cfrac{3}{2}\left[\cfrac{1}{n-\cfrac{1}{3}} - \cfrac{1}{n+\cfrac{1}{3}}\right]$，但不能裂项相消。

- 放缩的项数越少越精确。因为每放缩一项就会损失一定精度，一般最多保留前$3$项不放缩。


{{< /alert >}}


【解析】（1）由题意，可得：$a_n =\cfrac{1}{n^2} < \cfrac{1}{n^2 -n} = \cfrac{1}{n(n-1)} = \cfrac{1}{n-1} - \cfrac{1}{n}$ （$n \ge 2$）

可得：$\sum\limits_{k=1}^{k=n} \cfrac{1}{k^2} < 1 + (\cfrac{1}{1} - \cfrac{1}{2}) + (\cfrac{1}{2} - \cfrac{1}{3}) + \cdots + (\cfrac{1}{n-1} - \cfrac{1}{n}) = 2 - \cfrac{1}{n} < 2$


（2）由题意，可得：$a_n =\cfrac{1}{n^2} < \cfrac{1}{n^2 -1} = \cfrac{1}{(n-1)(n+1)} = \cfrac{1}{2}(\cfrac{1}{n-1} - \cfrac{1}{n+1})$ （$n \ge 2$）

可得：$\sum\limits_{k=1}^{k=n} \cfrac{1}{k^2} < 1 + \cfrac{1}{2}\left[(\cfrac{1}{1} - \cfrac{1}{3}) + (\cfrac{1}{2} - \cfrac{1}{4}) + (\cfrac{1}{3} - \cfrac{1}{5}) + \cdots + (\cfrac{1}{n-2} - \cfrac{1}{n}) + (\cfrac{1}{n-1} - \cfrac{1}{n+1})\right] =\cfrac{7}{4} - \cfrac{2n+1}{2n(n+1)} < \cfrac{7}{4}$


（3）由题意，可得：$a_n =\cfrac{1}{n^2} < \cfrac{1}{n^2 -\cfrac{1}{4}} = \cfrac{4}{(2n-1)(2n+1)} = 2( \cfrac{1}{2n-1} - \cfrac{1}{2n+1})$ （$n \ge 1$）

可得：$\sum\limits_{k=1}^{k=n} \cfrac{1}{k^2} < 1 + \cfrac{1}{4} +  \left[(\cfrac{1}{5} - \cfrac{1}{7}) + (\cfrac{1}{7} - \cfrac{1}{9}) + \cdots + (\cfrac{1}{2n-3} - \cfrac{1}{2n-1}) + (\cfrac{1}{2n-1} - \cfrac{1}{2n+1})\right] = \cfrac{33}{20} - \cfrac{2}{2n+1} < \cfrac{5}{3}$

（4）由题意，可得：$a_n =\cfrac{1}{(2n+1)^2} = \cfrac{1}{4} \cdot \cfrac{1}{(n+\cfrac{1}{2})^2}$

可得：$ \cfrac{1}{(n+\cfrac{1}{2})^2} <  \cfrac{1}{(n+\cfrac{1}{2})^2 - \cfrac{1}{4}} = \cfrac{1}{n} - \cfrac{1}{n+1} $ （$n \ge 1$）

可得：$\sum\limits_{k=1}^{k=n} \cfrac{1}{(2k+1)^2} < \cfrac{1}{4} \left[(1 - \cfrac{1}{2}) + (\cfrac{1}{2} - \cfrac{1}{3}) + \cdots + (\cfrac{1}{n} - \cfrac{1}{n+1})\right] = \cfrac{1}{4} - \cfrac{1}{4(n+1)} < \cfrac{1}{4}$

（5）由题意，可得：$a_n = \cfrac{4}{(n+1)^2} < \cfrac{4}{(n+1)^2 - \cfrac{1}{4}} = 8\left[ \cfrac{1}{2n+1} - \cfrac{1}{2n+3} \right] $

可得：$\sum\limits_{k=1}^{k=n} \cfrac{4}{(k+1)^2} < 8\left[(\cfrac{1}{3} - \cfrac{1}{5}) + (\cfrac{1}{5} - \cfrac{1}{7}) + \cdots + (\cfrac{1}{2n+1} - \cfrac{1}{2n+3})\right] = \cfrac{8}{3} - \cfrac{8}{2n+3} < 3$



### 幂函数类 $a_n = \cfrac{1}{n^k}$

（1）证明：$\sum\limits_{k=1}^{k=n} \cfrac{\sqrt{k}}{k^2} < 3$

（2）证明：$\sum\limits_{k=1}^{k=n} \cfrac{1}{k^3} < \cfrac{5}{4}$

{{< alert color="success" title="思路" >}}
（1）加强不等式为：$\sum\limits_{k=1}^{k=n} \cfrac{\sqrt{k}}{k^2} < 3 - \cfrac{2}{\sqrt{n}}$

通项放缩裂项为：$a_n = \cfrac{\sqrt{n}}{n^2} < 2(\cfrac{1}{\sqrt{n-1}} - \cfrac{1}{\sqrt{n}} )$（其中$n \ge 2$）



{{< /alert >}}

【解析】（1）由题意，可得：$a_n = \cfrac{1}{n^{\frac{3}{2}}} = \cfrac{1}{n\sqrt{n}} < \cfrac{1}{n\sqrt{n-1}} = \cfrac{1}{\sqrt{n} \cdot  \sqrt{n} \cdot \sqrt{n-1}} = \cfrac{1}{\sqrt{n}}(\sqrt{n} + \sqrt{n-1})(\cfrac{1}{\sqrt{n-1}} - \cfrac{1}{\sqrt{n}}) < 2(\cfrac{1}{\sqrt{n-1}} - \cfrac{1}{\sqrt{n}})$ （其中$n \ge 2$）

可得：$\sum\limits_{k=1}^{k=n} \cfrac{\sqrt{k}}{k^2} < 1 + 2\left[ (\cfrac{1}{\sqrt{1}} - \cfrac{1}{\sqrt{2}}) + (\cfrac{1}{\sqrt{2}} - \cfrac{1}{\sqrt{3}}) + \cdots +(\cfrac{1}{\sqrt{n-1}} - \cfrac{1}{\sqrt{n}}) \right] = 3 - \cfrac{2}{\sqrt{n}} < 3$

（2）由题意，可得：$a_n = \cfrac{1}{n^3} < \cfrac{1}{n(n^2-1)} = \cfrac{1}{n(n-1)(n+1)} = \cfrac{1}{2}\left[ \cfrac{1}{n(n-1)} - \cfrac{1}{n(n+1)} \right]$（其中$n \ge 2$）

可得：$\sum\limits_{k=1}^{k=n} \cfrac{1}{k^3} < 1 + \cfrac{1}{2}\left[ (\cfrac{1}{1 \times 2} - \cfrac{1}{2 \times 3}) + (\cfrac{1}{2 \times 3} - \cfrac{1}{3 \times 4}) + \cdots +(\cfrac{1}{n(n-1)} - \cfrac{1}{n(n+1)}) \right] = \cfrac{5}{4} - \cfrac{1}{2n(n+1)} < \cfrac{5}{4}$

















{{< alert color="secondary" title="思路" >}}

（1）证明：$\sum\limits_{k=1}^{k=n} \cfrac{1}{(2k+1)^2} < \cfrac{1}{4}$

通项分析，可得：$a_n = \cfrac{1}{(2n+1)^2} = \cfrac{1}{4} \cdot \cfrac{1}{(n+\cfrac{1}{2})^2} < \cfrac{1}{4} \cdot \cfrac{1}{(n+\cfrac{1}{2})^2 -\cfrac{1}{4}} = \cfrac{1}{4} (\cfrac{1}{n} - \cfrac{1}{n+1})$

（2）证明：$\sum\limits_{k=1}^{k=n} \cfrac{2}{k(2k+1)} < \cfrac{4}{3}$

通项分析，可得：$a_n = \cfrac{2}{n(2n+1)} = \cfrac{4}{2n(2n+1)} < \cfrac{4}{(2n-1)(2n+1)} = 2(\cfrac{1}{2n-1} - \cfrac{1}{2n+1}) $ （从第2项开始放缩）

（3）证明：$\sum\limits_{k=1}^{k=n} \cfrac{1}{(k+1)(2k+1)} < \cfrac{5}{12}$

通项分析，可得：$a_n = \cfrac{1}{(n+1)(2n+1)} < \cfrac{1}{2n(n+1)} = \cfrac{1}{2}(\cfrac{1}{n} - \cfrac{1}{n+1})$ （从第2项开始放缩）

（4）证明：$1 - \cfrac{1}{\sqrt{n+1}} < \sum\limits_{k=1}^{k=n} \cfrac{1}{2k \sqrt{k}} < \cfrac{3}{2} - \cfrac{1}{\sqrt{n}}$

通项分析，可得：$a_n = \cfrac{1}{2n \sqrt{n}} < \cfrac{1}{\sqrt{n-1}} - \cfrac{1}{\sqrt{n}}$ （其中$n \ge 2$），另外：$a_n = \cfrac{1}{2n \sqrt{n}} > \cfrac{1}{\sqrt{n}} - \cfrac{1}{\sqrt{n+1}}$

此题更适合通项比较：
{{< /alert >}}












