---
title: 部分相同元素不同位置
description: 部分相同元素不同位置
date: 2023-10-30
weight: 10000


---

{{< alert >}}
- 包含相同元素的全排列问题


{{< /alert >}}





---
&#128311;【】某电影院有$10$个座位连成一排，现有$4$人就座，按照下列条件，有多少种不同方案？

（1）任何两人都不相邻

（2）任何两人两旁都有空位

（3）任何两人都不相邻，且$4$人顺序一定

（4）任何两人都不相邻，且每人左右至多$2$个空位

（5）每个空位都不孤立

（6）恰有$6$个空位连在一起

（7）恰有$2$个空位相邻


{{<alert color="primary">}}
【**思路**】
- 做事：元素是$4$个不同人和$6$个相同空位。位置是$10$个不同位置
- 规则：
{{</alert>}}

【**解析**】

（1）$6$个空位排成一排形成$7$个空，从中任选$4$个插入$4$人，可得：$\operatorname{A} _{7}^{4} = 840$

故共有：$840$种不同方案

（2）$6$个空位排成一排形成$5$个空，从中任选$4$个插入$4$人，可得：$\operatorname{A} _{5}^{4}$

故共有：$120$种不同方案

（3）$6$个空位排成一排形成$7$个空，从中任选$4$个插入$4$人，可得：$\operatorname{A} _{7}^{4}$

又$4$人顺序一定，可得：$\frac{\operatorname{A} _{7}^{4}}{\operatorname{A} _{4}^{4}}=\operatorname{C} _{7}^{4}=35$

故共有：$35$种不同方案

（5）按照空位分组作为分类标准：
1. 当$6$个空位连在一起时，则有：$\operatorname{A} _{5}^{5}=120$
2. 当$6$个空位以$2:4$分组时，则有：$\operatorname{A} _{5}^{2}\operatorname{A} _{4}^{4}=480$
3. 当$6$个空位以$3:3$分组时，则有：$\operatorname{C} _{5}^{2}\operatorname{A} _{4}^{4}=240$
4. 当$6$个空位以$2:2:2$分组时，则有：$\operatorname{C} _{5}^{3}\operatorname{A} _{4}^{4}=240$；

综上，共有：$120+480+240+240=1080$


（6）恰有$6$个空位连在一起，则将$6$个空位作为整体与$4$人全排列，则有：$\operatorname{A} _{5}^{5}=120$

故共有：$120$



---
&#128311;【】编号为$1,2,3,4,5,6,7$的七盏路灯，晚上用时只亮三盏灯，且任意两盏亮灯不相邻，则不同的开灯方案有多少种？

{{<alert color="primary">}}
【**思路**】
- 做事：元素是$3$盏相同的亮灯和$4$盏相同的不亮灯；位置是$7$个不同位置；
- 规则：任意$2$盏亮灯不相邻；
{{</alert>}}


【**解析**】
- 第一步：安排$4$盏不亮灯，则有：$1$种方案；
- 第二步：$4$盏不亮灯形成$5$个空位，从$5$个空位任选$3$个空位安排$3$盏亮灯，则有：$\operatorname{C} _{5}^{3}=10$；

综上，共有：$1\times 10=10$

---
&#128311;【】马路上有编号为$1,2,3,4,5,6,7,8,9,10$的十盏路灯，为了节约用电又看清路面，可以把其中的三盏灯关掉，但不能同时关掉相邻的两盏或三盏，在两端的灯也不能关掉的情况下，求满足条件的关灯方法有多少种？

{{<alert color="primary">}}
【**思路**】
- 做事：元素是$3$盏相同的不亮灯和$7$盏相同的亮灯；位置是$10$个不同位置；
- 规则：任意$2$盏不亮灯不相邻，且不亮灯不在两端；
{{</alert>}}


【**解析**】
- 第一步：把$7$盏亮着的灯排成一排，则有：$\operatorname{C} _{7}^{7}=1$；
- 第二步： $7$盏亮灯之间形成$6$个不同空位，选出$3$个空位把不亮灯放入，则有：$\operatorname{C} _{6}^{3}=20$；

故共有：$1\times 20=20$种不同关灯方法














