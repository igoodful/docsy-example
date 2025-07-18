---
title: 条件概率
description: Conditional Probability
date: 2025-02-27
weight: 2

---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;

{{< alert >}}

{{< /alert >}}



## 条件概率

文字表达：条件概率是指在事件$A$发生的条件下，事件$B$发生的概率

方程表达：$P(B|A) = \cfrac{P(AB)}{P(A)}$

1. $P(AB)$表示事件$A$与$B$同时发生的概率，$P(AB)$也可等价表示成$P(A \cap B)$，事件$A$与$B$不一定有因果关系或时间上的先后关系。同时发生不是物理上的同时发生。
   1. 在时间上， 事件$A$可能会先于$B$发生，也可能相反，也可能二者同时发生；
   2. 在因果上， $A$可能会导致$B$的发生，也可能相反，也可能二者之间根本就没有因果关系；


条件概率中的条件与题设条件



---
&#128311;【】若$A$与$B$相互独立，证明：$\bar{A}$与$B$相互独立，$A$与$\bar{B}$相互独立，$\bar{A}$与$\bar{B}$相互独立；



---
&#128311;【】小强进行三次定点投篮测试，已知第一次投篮命中的概率为$\cfrac{1}{4}$，第二次投篮命中的概率为$\cfrac{1}{2}$，前两次投篮是否命中相互之间没有影响，第三次投篮受到前两次结果的影响，如果前两次投篮至少命中一次，则第三次投篮命中的概率为$\cfrac{2}{3}$，否则为$\cfrac{1}{5}$

（1）求小强三次投篮至少命中一次的概率;

（2）记小强三次投篮命中的次数为随机变量$X$，求$X$的概率分布及数学期望。

【思路】第一次投篮和第二次投篮相互独立，但前两次投篮对第三次投篮命中的概率有影响，而不是相互独立关系。设前两次投篮至少命中一次为事件$A$ ，第三次投篮命中为事件$B$ 

- 第三次投篮命中的概率为$\cfrac{2}{3}$的含义为：$P(B|A) = \cfrac{2}{3} $，$P(\bar{B}|A) = \cfrac{1}{3} $

- 第三次投篮命中的概率为$\cfrac{1}{5}$的含义为：$P(B| \bar{A}) = \cfrac{1}{5} $，$ P(\bar{B} | \bar{A}) = \cfrac{4}{5} $


【解析】（1）设前两次投篮至少命中一次为事件$A$ ，第三次投篮命中为事件$B$，可得：$P(\bar{A}) = \cfrac{3}{4} \times \cfrac{1}{2} = \cfrac{3}{8} $

依题意，可知：$ P(\bar{B} | \bar{A}) = \cfrac{4}{5} $

可得：$ P(\bar{A} \bar{B}) = P(\bar{A}) P(\bar{B} | \bar{A}) = \cfrac{3}{8} \times \cfrac{4}{5} = \cfrac{3}{10} $

故小强三次投篮至少命中一次的概率为：$\cfrac{7}{10}$

（2）设前两次投篮至少命中一次为事件$A$ ，第三次投篮命中为事件$B$

依题意，可知：$X=0,1,2,3$

当$X=0$时，由（1）可知：$P(X=0) = 1- \cfrac{7}{10} = \cfrac{3}{10} $

当$X=1$时，可得：$P(X=1) = \cfrac{1}{4} \times (1-\cfrac{1}{2}) \times \cfrac{1}{3} + \cfrac{3}{4} \times \cfrac{1}{2} \times \cfrac{1}{3} + (1-\cfrac{1}{4}) \times (1-\cfrac{1}{2}) \times \cfrac{1}{5} = \cfrac{29}{120}$

当$X=3$时，可得：$P(X=3) = \cfrac{1}{4} \times \cfrac{1}{2} \times \cfrac{2}{3} = \cfrac{1}{12}$

当$X=2$时，可得：$P(X=2) = 1- (\cfrac{3}{10} + \cfrac{29}{120} + \cfrac{1}{12}) = \cfrac{3}{8}$

故$X$的概率分布为：

| $X$ | $0$             | $1$               | $2$            | $3$             |
|:----|:----------------|:------------------|:---------------|:----------------|
| $P$ | $\cfrac{3}{10}$ | $\cfrac{29}{120}$ | $\cfrac{3}{8}$ | $\cfrac{1}{12}$ |

可得：$E(X) = 0 + \cfrac{29}{120} + 2 \times \cfrac{3}{8} + 3 \times \cfrac{1}{12} = \cfrac{149}{120} $

故$X$的数学期望为：$ \cfrac{149}{120} $
















