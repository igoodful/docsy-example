---
title: 元素不相邻
description: adjacent
date: 2023-10-30
weight: 10000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}



{{< /alert >}}



&#128311;【】现有$A,B,C,D,E,F,G$共$7$人从左到右排成一排，按照如下条件，各有多少种排法？

（1）$A$与$B$不相邻；

（2）$A$与$B$不相邻，且不在两端；

（3）$A$、$B$、$C$、$D$互不相邻，$E$、$F$、$G$也互不相邻；

（4）$A$与$B$不相邻，$C$与$D$不相邻；（最佳方案是反面）

{{<alert color="primary">}}
【**思路**】
- 做事：$A,B,C,D,E,F,G$共$7$人；位置是$7$个不同位置
- 规则：如果遇到多组不相邻，最佳采用反面来做
{{</alert>}}
【**解析**】

（1）$|C|D|E|F|G|$
- 第一步：先对除$A$、$B$之外的$5$人全排列，则有：$\operatorname{A} _{5}^{5}$；
- 第二步：除$A$、$B$之外的$5$人全排列形成$6$个空位，将$A$、$B$插入空位，则有：$\operatorname{A} _{6}^{2}$；

综上，共有：$\operatorname{A} _{5}^{5}\operatorname{A} _{6}^{2}=3600$

（2）$C|D|E|F|G$
- 第一步：除$A$、$B$以外的$5$人全排列，则有：$\operatorname{A} _{5}^{5}$
- 第二步：从$A$、$B$以外的$5$人恰有$4$个空位任选$2$个（排除两端的空位），则有：$\operatorname{A} _{4}^{2}$

综上，共有：$\operatorname{A} _{5}^{5}\operatorname{A} _{4}^{2}=1440$

（3）$A|B|C|D$
- 第一步：先排列$A$、$B$、$C$、$D$，则有：$\operatorname{A} _{4}^{4}$
- 第二步：$A$、$B$、$C$、$D$形成$3$个空位，将其余$3$人插入中间的$3$个空位上，则有：$\operatorname{A} _{3}^{3}$

综上，共有：$\operatorname{A} _{4}^{4}\operatorname{A} _{3}^{3}=144$

（4）根据反面求得，尽可能避免使用多次插入：

1. 当只考虑$A$与$B$不相邻时，则有：$\operatorname{A} _{6}^{2}\operatorname{A} _{5}^{5}=3600$；
2. 当$A$与$B$不相邻，且$C$与$D$相邻时，则有：$\operatorname{A} _{2}^{2}\operatorname{A} _{4}^{4}\operatorname{A} _{5}^{2}=960$；

综上，共有：$3600-960=2640$

---
&#128311;【】现有$A,B,C,D,E,F,G$共$7$人从左到右排成一排，按照如下条件，各有多少种排法？

（1）$A$与$B$相邻，$C$与$D$不相邻；

（2）$A$与$B$相邻，$C$与$D$不相邻，$E$与$F$不相邻；

（3）$A$、$B$都不与$C$相邻；（安排位置）

（4）$A$与$B$相邻，且$A$、$B$都不与$C$相邻；

（5）$A$与$B$相邻，$A$、$C$不相邻；

（6）$A$、$B$、$C$至多两人不相邻；（解析未完成）

【思路】不相邻尽量转化为相邻进行计算：

【解析】（1）
- 第一步：$A$与$B$排列，则有：$\operatorname{A} _{2}^{2}$
- 第二步：$A$与$B$作为一个整体与$E,F,G$全排列，则有：$\operatorname{A} _{4}^{4}$
- 第三步：$A$与$B$的整体与另外$3$人排列形成$5$个空位，将$C$和$D$插入，则有：$\operatorname{A} _{5}^{2}$

综上，共有：$\operatorname{A} _{2}^{2}\operatorname{A} _{4}^{4}\operatorname{A} _{5}^{2}=960$

（2）反面：
1. 当只考虑$A$与$B$相邻且$C$与$D$不相邻时，则有：$\operatorname{A} _{2}^{2}\operatorname{A} _{4}^{4}\operatorname{A} _{5}^{2}=960$；
2. 当$A$与$B$相邻且$C$与$D$不相邻且$E$与$F$相邻时，则有：$\operatorname{A} _{2}^{2}\operatorname{A} _{2}^{2}\operatorname{A} _{3}^{3}\operatorname{A} _{4}^{2}=288$；

综上，共有：$960-288=672$

（3）根据C的位置作为分类标准：

1. 当C在两端时，如下步骤做事：
   - 第一步：先安排C，则有：$\operatorname{C} _{2}^{1}$；
   - 第二步：再安排A、B排列，则有：$\operatorname{A} _{6}^{2}$；（也可先安排C的相邻位置）
   - 第三步：再安排剩余$3$人排列，则有：$\operatorname{A} _{3}^{3}$；

则有：$\operatorname{C} _{2}^{1}\operatorname{A} _{6}^{2}\operatorname{A} _{3}^{3}=360$

2. 当C不在两端时，如下步骤做事：
   - 第一步：先安排C，则有：$\operatorname{C} _{5}^{1}$；
   - 第二步：再安排A、B排列，则有：$\operatorname{A} _{3}^{2}$；
   - 第三步：再安排剩余$3$人排列，则有：$\operatorname{A} _{3}^{3}$；

则有：$\operatorname{C} _{5}^{1}\operatorname{A} _{3}^{2}\operatorname{A} _{3}^{3}=180$

综上，共有：$360+180=540$

（4）
- 第一步：A、B排列，则有：$\operatorname{A} _{2}^{2}$；
- 第二步：ABC除外的$4$人排列，则有：$\operatorname{A} _{4}^{4}$；
- 第三步：ABC除外的$4$人排列形成$5$个空位，将AB整体和C分别插入，则有：$\operatorname{A} _{5}^{2}$；

综上，共有：$\operatorname{A} _{2}^{2}\operatorname{A} _{4}^{4}\operatorname{A} _{5}^{2}=960$

（5）
$\operatorname{C} _{2}^{1}\operatorname{C} _{4}^{1}\operatorname{A} _{4}^{4}+\operatorname{C} _{2}^{1}\operatorname{C} _{5}^{1}\operatorname{A} _{4}^{4}$

$\operatorname{C} _{2}^{1}\operatorname{C} _{3}^{1}\operatorname{A} _{4}^{4}+\operatorname{C} _{2}^{1}\operatorname{C} _{4}^{1}\operatorname{A} _{4}^{4}$

$\operatorname{C} _{2}^{1}\operatorname{C} _{3}^{1}\operatorname{A} _{4}^{4}+\operatorname{C} _{2}^{1}\operatorname{C} _{3}^{1}\operatorname{A} _{4}^{4}$

$\operatorname{C} _{2}^{1}\operatorname{C} _{3}^{1}\operatorname{A} _{4}^{4}+\operatorname{C} _{2}^{1}\operatorname{C} _{3}^{1}\operatorname{A} _{4}^{4}$

综上，共有：$\left( 18+14+12+12 \right)\operatorname{A} _{4}^{4}=1392$

（6）$\operatorname{A} _{7}^{7}-\operatorname{A} _{5}^{5}\operatorname{A} _{3}^{3}=4320$







