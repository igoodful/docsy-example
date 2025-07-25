---
title: 概率
description: Probability
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




---
&#128311;【】一个袋子中有$7$个大小相同的球，其中$4$个白球与$3$个黑球，现从袋子里随机取球。

（1）若从中任取$2$个球，求至少有一个黑球的概率

（2）若有放回地取$2$次，每次取一个球，求至少有一个黑球的概率

（3）若无放回地依次取$2$次，每次取一个球，求第一次取出黑球、第二次取出白球的概率



【思路】

- 做什么事？

（1）任取$2$个球。$\Longrightarrow$ 组合问题。

（2）有放回地取$2$次，每次取一个球。$\Longrightarrow$独立重复试验。

（3）无放回地依次取$2$次，每次取一个球。$\Longrightarrow$ 排列问题，显然有先后顺序之分。

【解析】（1）记从$7$个球中任取$2$个球，至少有一个黑球为事件$A$

可得：$ P(A) = 1 - \cfrac{ \operatorname{C}_4^2 }{ \operatorname{C}_7^2 } = \cfrac{5}{7} $

故至少有一个黑球的概率为：$\cfrac{5}{7}$

（2）记有放回地取$2$次，每次取一个球，至少有一个黑球为事件$B$

可得：$ P(B) = 1 - \cfrac{ 4 }{ 7 } \times \cfrac{ 4 }{ 7 } = \cfrac{33}{49} $

故至少有一个黑球的概率为：$ \cfrac{33}{49} $

（3）记无放回地依次取$2$次，每次取一个球，求第一次取出黑球、第二次取出白球为事件$C$

可得：$ P(C) = \cfrac{ \operatorname{C}_3^1 \operatorname{C}_4^1 }{ \operatorname{A}_7^2 } = \cfrac{2}{7} $

故第一次取出黑球、第二次取出白球的概率为：$ \cfrac{2}{7} $





---
&#128311;【】袋中有大小相同的$4$个红球，$6$个白球，每次从中摸取一球，每个球被取到的可能性相同，现不放回地取$3$个球

（1）求第三个取出红球的概率；

（2）求至少取到两个红球的概率；

（3）用兰与1分别表示取得的红球数与白球数，计算E、D、En、Dn；






