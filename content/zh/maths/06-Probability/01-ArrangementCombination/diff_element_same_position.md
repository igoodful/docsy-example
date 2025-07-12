---
title: 不同元素相同位置分配
description: 不同元素相同位置分配
date: 2023-10-30
weight: 10000


---

{{< alert >}}
【】将$n$个不同的球放入到$m$个相同的盒子中（$n\ge m$），盒子不空，则有多少种不同的方法？

【**思路**】
- 做事：元素是$n$个不同的球$\\{ 1,2,\cdot \cdot \cdot ,n \\}$；
- 规则：盒子不空，分类标准是某球是否独占一个盒子；

【**解析**】设将$n$个不同的球放入到$m$个相同的盒子的方法种数为：$f\left( n,m \right)$

$\qquad\textcircled{1}$若$1$号小球独占一个盒子时，只需将剩下的$n-1$个不同的球放到剩下的$m-1$个相同的盒子中，共有：$f\left( n-1,m-1 \right)$；

$\qquad\textcircled{2}$若$1$号小球不独占一个盒子时，先将剩下的$n-1$个不同的球放到$m$个相同的盒子中，再将$1$号小球放入到$m$个相同的盒子中的任意一个盒子，共有：$mf\left( n-1,m \right)$；

综上，可得：$f\left( n,m \right)=f\left( n-1,m-1 \right)+mf\left( n-1,m \right)$

易知：$f\left( n,0 \right)=0$，$f\left( n,n \right)=1$


{{<caution>}}
<!---->
1. 当球的数量$n$较小时，最佳方法按照分组讨论求得；

2. 当球的数量$n$较大时，最佳方法是根利用推关系求得；
{{</caution>}}

【】将$n$个不同的球放入到$m$个相同的盒子中，且盒子可空，则有多少种不同的方法？

【思路】方法总数等于将$n$个不同的球分成$1$组、$2$组、$\cdot \cdot \cdot $、$\min \left( m,n \right)$组的所有方法之和，按照空盒个数作为分类标准讨论.
{{< /alert >}}

【】将$5$个不同的球放入到$3$个相同的盒子中，按照下列条件，有多少种方法？

（1）盒子不空；

（2）恰有$1$个空盒；

（3）恰有$2$个空盒；

（4）盒子可空；
{{<alert color="primary">}}
【**思路**】
- 做事：元素是$5$个不同的球；
- 放置方法：
  - （1）将$5$个不同的球分成$3$组，即按照$1:1:3$和$1:2:2$分组；
  - （2）将$5$个不同的球分成$2$组，即按照$1:4$和$2:3$分组；
  - （3）将$5$个不同的球分成$1$组，即只有$1$种方法；
  - （4）将$5$个不同的球分别分成$1$组、$2$组、$3$组；
{{</alert>}}
【**解析**】（1）方法一：将$5$个不同的球分成$3$组：
- $\textcircled{1}$将$5$个不同的球分成$3$组，按照$1:1:3$分组，可得：$\cfrac{\operatorname{C}_ {5}^{1}\operatorname{C}_ {4}^{1}\operatorname{C}_ {3}^{3}}{\operatorname{A}_ {2}^{2}}=10$种方法；
- $\textcircled{2}$将$5$个不同的球分成$3$组，按照$1:2:2$分组，可得：$\cfrac{\operatorname{C}_ {5}^{1}\operatorname{C}_ {4}^{2}\operatorname{C}_ {2}^{2}}{\operatorname{A}_ {2}^{2}}=15$种方法；

综上，共有$10+15=25$种不同的方法

方法二：设共有$f\left( 5,3 \right)$种不同的方法
- $\textcircled{1}$若$1$号小球独占一个盒子时，只需将剩下的$4$个不同的球放到剩下的$2$个相同的盒子中，则有：$f\left( 4,2 \right)$；
- $\textcircled{2}$若$1$号小球不独占一个盒子时，先将剩下的$4$个不同的球放到$3$个相同的盒子中，再将$1$号小球放入到$3$个相同的盒子中中的任意一个盒子，则有：$f\left( 4,3 \right)\times 3$；

综上，共有：$f\left( 5,3 \right)=f\left( 4,2 \right)+3f\left( 4,3 \right)$

同理可得：$f\left( 4,2 \right)=f\left( 3,1 \right)+2f\left( 3,2 \right)=7$，$f\left( 4,3 \right)=f\left( 3,2 \right)+3f\left( 3,3 \right)=6$
$f\left( 3,2 \right)=f\left( 2,1 \right)+2f\left( 2,2 \right)=3$

易知：将$n$个不同的球放入到$0$个相同的盒子中时，可得：$f\left( n,0 \right)=0$；

易知：将$n$个不同的球放入到$n$个相同的盒子中时，可得：$f\left( n,n \right)=1$；

可得：$f\left( 5,3 \right)=7+3\times 6=25$

综上，共有：$25$种.

（2）将$5$个不同的球分成$2$组，按照$1:4$分组，则有：$\operatorname{C}_ {5}^{1}\operatorname{C}_ {4}^{4}=5$种方法；

将$5$个不同的球分成$2$组，按照$2:3$分组，则有：$\operatorname{C}_ {5}^{2}\operatorname{C}_ {3}^{3}=10$种方法；

综上，共有：$5+10=15$种.

（3）将$5$个不同的球分成$1$组，即只有$1$种方法.

（4）将$5$个不同的球分成$1$组、$2$组、$3$组的所有方法总数之和：

- $\textcircled{1}$将$5$个不同的球分成$1$组，共有：$1$种；
- $\textcircled{2}$将$5$个不同的球分成$2$组，由（2）知，共有：$15$种；
- $\textcircled{3}$将$5$个不同的球分成$3$组，由（1）知，共有：$25$种；

综上，共有：$1+15+25=41$种.

【】$7$个不同的球任意地放入$4$个相同的盒子中，按照下列条件，共有多少方法？

（1）盒子不可空；

（2）盒子可空；
{{<alert color="primary">}}
【思路】
- 做事：元素是$7$个不同的球；
- 规则：（1）盒子不可空；（2）盒子可空；
{{</alert>}}
【**解析**】（1）$7$个不同的球分成$4$组：
- $\textcircled{1}$按照$1:1:1:4$分组，则有：种$\cfrac{\operatorname{C}_ {7}^{1}\operatorname{C}_ {6}^{1}\operatorname{C}_ {5}^{1}\operatorname{C}_ {4}^{4}}{\operatorname{A}_ {3}^{3}}=35$种；
- $\textcircled{2}$按照$1:1:2:3$分组，则有：$\cfrac{\operatorname{C}_ {7}^{1}\operatorname{C}_ {6}^{1}\operatorname{C}_ {5}^{2}\operatorname{C}_ {3}^{3}}{\operatorname{A}_ {2}^{2}}=210$种；
- $\textcircled{3}$按照$1:2:2:2$分组，则有：$\cfrac{\operatorname{C}_ {7}^{1}\operatorname{C}_ {6}^{2}\operatorname{C}_ {4}^{2}\operatorname{C}_ {2}^{2}}{\operatorname{A}_ {3}^{3}}=105$种；

综上，共有：$35+210+105=350$种（也可通过递推求得：$f\left( 7,4 \right)=350$）

（2）盒子可空，将$7$个不同的球按照分成$1$组、$2$组、$3$组、$4$组进行分类：
- 1）$7$个不同的球分成$1$组，共有：$1$种；
- 2）$7$个不同的球分成$2$组，分别按照$1:6$，$2:5$，$3:4$：
  - $\textcircled{1}$当按照$1:6$分组，则有：$\operatorname{C}_ {7}^{1}=7$种；
  - $\textcircled{2}$当按照$2:5$分组，则有：$\operatorname{C}_ {7}^{2}=21$种；
  - $\textcircled{3}$当按照$3:4$分组，则有：$\operatorname{C}_ {7}^{3}=35$种；

故将$7$个不同的球分成$2$组时，共有：$7+21+35=63$种；

- 3）$7$个不同的球分成$3$组，分别按照$1:1:5$，$2:5$，$3:4$：
  - $\textcircled{1}$当按照$1:1:5$分组，则有：$\cfrac{\operatorname{C}_ {7}^{1}\operatorname{C}_ {6}^{1}\operatorname{C}_ {5}^{5}}{\operatorname{A}_ {2}^{2}}=21$种；
  - $\textcircled{2}$当按照$1:2:4$分组，则有：$\operatorname{C}_ {7}^{1}\operatorname{C}_ {6}^{2}\operatorname{C}_ {4}^{4}=105$种；
  - $\textcircled{3}$当按照$1:3:3$分组，则有：$\cfrac{\operatorname{C}_ {7}^{1}\operatorname{C}_ {6}^{3}\operatorname{C}_ {3}^{3}}{\operatorname{A}_ {2}^{2}}=70$种；
  - $\textcircled{4}$当按照$2:2:3$分组，则有：$\cfrac{\operatorname{C}_ {7}^{2}\operatorname{C}_ {5}^{2}\operatorname{C}_ {3}^{3}}{\operatorname{A}_ {2}^{2}}=210$种；

故当$7$个不同的球分成$3$组时，共有：$406$种；
- 4）$7$个不同的球分成$4$组，由（1），共有：$350$种；

综上，共有：$1+63+406+350=820$种；



{{< alert >}}
【】将$n$个不同的球放入到$m$个相同的盒子中（$n\ge m$），盒子不空，则有多少种不同的方法？

【**思路**】
- 做事：元素是$n$个不同的球$\\{ 1,2,\cdot \cdot \cdot ,n \\}$；
- 规则：盒子不空，分类标准是某球是否独占一个盒子；

【**解析**】设将$n$个不同的球放入到$m$个相同的盒子的方法种数为：$f\left( n,m \right)$

$\qquad\textcircled{1}$若$1$号小球独占一个盒子时，只需将剩下的$n-1$个不同的球放到剩下的$m-1$个相同的盒子中，共有：$f\left( n-1,m-1 \right)$；

$\qquad\textcircled{2}$若$1$号小球不独占一个盒子时，先将剩下的$n-1$个不同的球放到$m$个相同的盒子中，再将$1$号小球放入到$m$个相同的盒子中的任意一个盒子，共有：$mf\left( n-1,m \right)$；

综上，可得：$f\left( n,m \right)=f\left( n-1,m-1 \right)+mf\left( n-1,m \right)$

易知：$f\left( n,0 \right)=0$，$f\left( n,n \right)=1$


{{<caution>}}
<!---->
1. 当球的数量$n$较小时，最佳方法按照分组讨论求得；

2. 当球的数量$n$较大时，最佳方法是根利用推关系求得；
{{</caution>}}

【】将$n$个不同的球放入到$m$个相同的盒子中，且盒子可空，则有多少种不同的方法？

【思路】方法总数等于将$n$个不同的球分成$1$组、$2$组、$\cdot \cdot \cdot $、$\min \left( m,n \right)$组的所有方法之和，按照空盒个数作为分类标准讨论.
{{< /alert >}}

【】将$5$个不同的球放入到$3$个相同的盒子中，按照下列条件，有多少种方法？

（1）盒子不空；

（2）恰有$1$个空盒；

（3）恰有$2$个空盒；

（4）盒子可空；
{{<alert color="primary">}}
【**思路**】
- 做事：元素是$5$个不同的球；
- 放置方法：
  - （1）将$5$个不同的球分成$3$组，即按照$1:1:3$和$1:2:2$分组；
  - （2）将$5$个不同的球分成$2$组，即按照$1:4$和$2:3$分组；
  - （3）将$5$个不同的球分成$1$组，即只有$1$种方法；
  - （4）将$5$个不同的球分别分成$1$组、$2$组、$3$组；
{{</alert>}}
【**解析**】（1）方法一：将$5$个不同的球分成$3$组：
- $\textcircled{1}$将$5$个不同的球分成$3$组，按照$1:1:3$分组，可得：$\cfrac{\operatorname{C}_ {5}^{1}\operatorname{C}_ {4}^{1}\operatorname{C}_ {3}^{3}}{\operatorname{A}_ {2}^{2}}=10$种方法；
- $\textcircled{2}$将$5$个不同的球分成$3$组，按照$1:2:2$分组，可得：$\cfrac{\operatorname{C}_ {5}^{1}\operatorname{C}_ {4}^{2}\operatorname{C}_ {2}^{2}}{\operatorname{A}_ {2}^{2}}=15$种方法；

综上，共有$10+15=25$种不同的方法

方法二：设共有$f\left( 5,3 \right)$种不同的方法
- $\textcircled{1}$若$1$号小球独占一个盒子时，只需将剩下的$4$个不同的球放到剩下的$2$个相同的盒子中，则有：$f\left( 4,2 \right)$；
- $\textcircled{2}$若$1$号小球不独占一个盒子时，先将剩下的$4$个不同的球放到$3$个相同的盒子中，再将$1$号小球放入到$3$个相同的盒子中中的任意一个盒子，则有：$f\left( 4,3 \right)\times 3$；

综上，共有：$f\left( 5,3 \right)=f\left( 4,2 \right)+3f\left( 4,3 \right)$

同理可得：$f\left( 4,2 \right)=f\left( 3,1 \right)+2f\left( 3,2 \right)=7$，$f\left( 4,3 \right)=f\left( 3,2 \right)+3f\left( 3,3 \right)=6$
$f\left( 3,2 \right)=f\left( 2,1 \right)+2f\left( 2,2 \right)=3$

易知：将$n$个不同的球放入到$0$个相同的盒子中时，可得：$f\left( n,0 \right)=0$；

易知：将$n$个不同的球放入到$n$个相同的盒子中时，可得：$f\left( n,n \right)=1$；

可得：$f\left( 5,3 \right)=7+3\times 6=25$

综上，共有：$25$种.

（2）将$5$个不同的球分成$2$组，按照$1:4$分组，则有：$\operatorname{C}_ {5}^{1}\operatorname{C}_ {4}^{4}=5$种方法；

将$5$个不同的球分成$2$组，按照$2:3$分组，则有：$\operatorname{C}_ {5}^{2}\operatorname{C}_ {3}^{3}=10$种方法；

综上，共有：$5+10=15$种.

（3）将$5$个不同的球分成$1$组，即只有$1$种方法.

（4）将$5$个不同的球分成$1$组、$2$组、$3$组的所有方法总数之和：

- $\textcircled{1}$将$5$个不同的球分成$1$组，共有：$1$种；
- $\textcircled{2}$将$5$个不同的球分成$2$组，由（2）知，共有：$15$种；
- $\textcircled{3}$将$5$个不同的球分成$3$组，由（1）知，共有：$25$种；

综上，共有：$1+15+25=41$种.

【】$7$个不同的球任意地放入$4$个相同的盒子中，按照下列条件，共有多少方法？

（1）盒子不可空；

（2）盒子可空；
{{<alert color="primary">}}
【思路】
- 做事：元素是$7$个不同的球；
- 规则：（1）盒子不可空；（2）盒子可空；
{{</alert>}}
【**解析**】（1）$7$个不同的球分成$4$组：
- $\textcircled{1}$按照$1:1:1:4$分组，则有：种$\cfrac{\operatorname{C}_ {7}^{1}\operatorname{C}_ {6}^{1}\operatorname{C}_ {5}^{1}\operatorname{C}_ {4}^{4}}{\operatorname{A}_ {3}^{3}}=35$种；
- $\textcircled{2}$按照$1:1:2:3$分组，则有：$\cfrac{\operatorname{C}_ {7}^{1}\operatorname{C}_ {6}^{1}\operatorname{C}_ {5}^{2}\operatorname{C}_ {3}^{3}}{\operatorname{A}_ {2}^{2}}=210$种；
- $\textcircled{3}$按照$1:2:2:2$分组，则有：$\cfrac{\operatorname{C}_ {7}^{1}\operatorname{C}_ {6}^{2}\operatorname{C}_ {4}^{2}\operatorname{C}_ {2}^{2}}{\operatorname{A}_ {3}^{3}}=105$种；

综上，共有：$35+210+105=350$种（也可通过递推求得：$f\left( 7,4 \right)=350$）

（2）盒子可空，将$7$个不同的球按照分成$1$组、$2$组、$3$组、$4$组进行分类：
- 1）$7$个不同的球分成$1$组，共有：$1$种；
- 2）$7$个不同的球分成$2$组，分别按照$1:6$，$2:5$，$3:4$：
  - $\textcircled{1}$当按照$1:6$分组，则有：$\operatorname{C}_ {7}^{1}=7$种；
  - $\textcircled{2}$当按照$2:5$分组，则有：$\operatorname{C}_ {7}^{2}=21$种；
  - $\textcircled{3}$当按照$3:4$分组，则有：$\operatorname{C}_ {7}^{3}=35$种；

故将$7$个不同的球分成$2$组时，共有：$7+21+35=63$种；

- 3）$7$个不同的球分成$3$组，分别按照$1:1:5$，$2:5$，$3:4$：
  - $\textcircled{1}$当按照$1:1:5$分组，则有：$\cfrac{\operatorname{C}_ {7}^{1}\operatorname{C}_ {6}^{1}\operatorname{C}_ {5}^{5}}{\operatorname{A}_ {2}^{2}}=21$种；
  - $\textcircled{2}$当按照$1:2:4$分组，则有：$\operatorname{C}_ {7}^{1}\operatorname{C}_ {6}^{2}\operatorname{C}_ {4}^{4}=105$种；
  - $\textcircled{3}$当按照$1:3:3$分组，则有：$\cfrac{\operatorname{C}_ {7}^{1}\operatorname{C}_ {6}^{3}\operatorname{C}_ {3}^{3}}{\operatorname{A}_ {2}^{2}}=70$种；
  - $\textcircled{4}$当按照$2:2:3$分组，则有：$\cfrac{\operatorname{C}_ {7}^{2}\operatorname{C}_ {5}^{2}\operatorname{C}_ {3}^{3}}{\operatorname{A}_ {2}^{2}}=210$种；

故当$7$个不同的球分成$3$组时，共有：$406$种；
- 4）$7$个不同的球分成$4$组，由（1），共有：$350$种；

综上，共有：$1+63+406+350=820$种；



