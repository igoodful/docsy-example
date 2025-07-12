---
title: 元素相间
description: 元素相间
date: 2023-10-30
weight: 10000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}



- 多个相邻

- 相邻对数

- 相邻不相邻

{{< /alert >}}






&#128311;【】现有$A,B,C,D,E,F,G$共$7$人从左到右排成一排，按照如下条件，各有多少种排法？

（1）$AB$之间恰好间隔$2$人；

（2）$AB$两人中间恰好有$3$人；

（3）$AB$中间夹$1$人；

（4）$AB$中间至少$2$人；

（5）$A$、$B$、$C$、$D$与其余$3$人相间；

{{<alert color="primary">}}
【**思路**】
- 元素是$7$名人；位置是$7$个不同位置；
- 规则：因为是全排所以元素和位置可换，安排完位置和安排完元素均可完成这件事
{{</alert>}}
【**解析**】

（1）$A$与$B$之间间隔$2$人：
- 第一步：从除$A$、$B$以外的$5$人外任选$2$人排列在AB之间，可得：$\operatorname{A} _{5}^{2}$；
- 第二步：排列$A$、$B$，可得：$\operatorname{A} _{2}^{2}$；
- 第三步：将$AB$和$AB$中间的$2$人当作整体与剩下的$3$人全排列，可得：$\operatorname{A} _{4}^{4}$；

综上，共有：$\operatorname{A} _{5}^{2}\operatorname{A} _{2}^{2}\operatorname{A} _{4}^{4}=960$

（2）第一步：从除$AB$以外的$5$人中任选$3$人排列在AB之间，则有$\operatorname{A} _{5}^{3}$：
- 第二步：排列AB，可得：$\operatorname{A} _{2}^{2}$；
- 第三步：前面已经排好的$5$人作为整体与剩下的$2$人排列，可得：$\operatorname{A} _{3}^{3}$

综上，共有：$\operatorname{A} _{5}^{3}\operatorname{A} _{2}^{2}\operatorname{A} _{3}^{3}=720$

（3）$\operatorname{C} _{5}^{1}\operatorname{A} _{2}^{2}\operatorname{A} _{5}^{5}=1200$

（4）$\operatorname{A} _{7}^{7}-\operatorname{A} _{6}^{6}\operatorname{A} _{2}^{2}-\operatorname{C} _{5}^{1}\operatorname{A} _{2}^{2}\operatorname{A} _{5}^{5}$

（5）将A、B、C、D全排列有$\operatorname{A} _{4}^{4}$种不同排法，将其余$3$人全排列有$\operatorname{A} _{3}^{3}$种不同排法，要求相间，有$2$种方式，即男女男女男女男女，或女男女男女男女男

故共有：$2\times \operatorname{A} _{4}^{4}\operatorname{A} _{3}^{3}$种；




























