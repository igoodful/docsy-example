---
title: 基本结论证明
description: conclusion
date: 2025-05-18
weight: 2
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;

$ \rightleftharpoons $


{{<alert color="danger" title="注意" >}}

1. a、b、c关联方程：$ a^2 = b^2 + c^2 $

2. 离心率方程：$ e = \cfrac{c}{a} $

3. 点在曲线方程

4. a：椭圆半长轴长
5. b：椭圆半短轴长
6. c：椭圆的焦点长
7. 中心对称》椭圆上的点关于原点的对称点一定也在在椭圆上；


{{</alert>}}





## 离心率


$ e = \cfrac{c}{a}  $

又$ a^2 = b^2 + c^2 $，可得：$ e = \cfrac{c}{a} = \sqrt{1 - \cfrac{b^2}{a^2} } $


- 离心率$e$越接近$1$，椭圆就越扁。
- 离心率$e$越接近$0$，椭圆就越接近于圆。
- 若$c=0$时，则$a=b$，此时椭圆就变成了圆。
- 椭圆离心率范围：$(0,1)$


离心率相同，则形状相似






---

&#128311;已知椭圆$\Omega$：$ \cfrac{x^2}{a^2} + \cfrac{y^2}{b^2} = 1 $（其中$0<b<a$）

![1.svg](/maths_geometry/1.svg)

（1）若点$P(x_0,y_0)$在椭圆$\Omega$上

（2）若点$P(x_0,y_0)$在椭圆$\Omega$内

（3）若点$P(x_0,y_0)$在椭圆$\Omega$外

{{<alert title="提示" >}}

- 点在椭圆上
  - $ \left| x_2 - x_1 \right| = \sqrt{ (x_1 + x_2)^2 - 4 x_1 x_2 }  $
  - $ \left| y_2 - y_1 \right| = \sqrt{ (y_1 + y_2)^2 - 4 y_1 y_2 }  $
  - $ a^2 = b^2 + c^2 $：只需两个独立的关于这些变量的方程即可求出，只需一个方程即可求出离心率。（三个未知数三个方程，但这里已经有了一个方程，故只需两个独立的方程即可）
  - 如何刻画点在椭圆上

{{</alert>}}


【**解析**】（1）若点$P(x_0,y_0)$在椭圆$\Omega$上，则点满足椭圆方程，可得：$ \cfrac{{x_0}^2}{a^2} + \cfrac{{y_0}^2}{b^2} = 1 $

若点$P(x_0,y_0)$在椭圆$\Omega$上，由椭圆第一定义，可得：$ \left| PF_1 \right| +  \left| PF_2 \right| = 2a $

若点$P(x_0,y_0)$在椭圆$\Omega$上，由椭圆第二定义，可得：$ \cfrac{ \left| PF_1 \right|}{ \left| PM \right|} = \cfrac{ \left| PF_2 \right|}{ \left| PN \right|}  = e = \cfrac{c}{a} $

（2）若点$P(x_0,y_0)$在椭圆$\Omega$内，可得：$ \cfrac{{x_0}^2}{a^2} + \cfrac{{y_0}^2}{b^2} < 1 $

（3）若点$P(x_0,y_0)$在椭圆$\Omega$外，可得：$ \cfrac{{x_0}^2}{a^2} + \cfrac{{y_0}^2}{b^2} > 1 $

---



## 切线方程

---

&#128311;已知点$P(x_0,y_0)$在椭圆$ \cfrac{x^2}{a^2} + \cfrac{y^2}{b^2} = 1 $（$0<b<a$）上，求过点$P$的椭圆的切线方程

![1.svg](/maths_geometry/4.png)

【**解析**】

**方法一：导数求切线斜率**

对方程$ \cfrac{x^2}{a^2} + \cfrac{y^2}{b^2} = 1 $两边同时对$x$求导，可得：$ \cfrac{ { 2x }}{a^2} + \cfrac{ 2y \cdot y^{'}}{b^2} = 0 $

可得：$ y^{'} = - \cfrac{ b^2 x }{a^2 y} $

故椭圆上过点$P$的切线的斜率为：$k = - \cfrac{ b^2 x_0 }{a^2 y_0 } $

故过点$P$的椭圆的切线方程为：$ y - y_0 = - \cfrac{ b^2 x_0 }{a^2 y_0 } (x - x_0) $

点$P(x_0,y_0)$在椭圆上，可得：$ \cfrac{ { x_0 }^2}{a^2} + \cfrac{ {y_0}^2}{b^2} = 1 $

化简可得：$\cfrac{x_0 x}{a^2} + \cfrac{y_0 y}{b^2} = 1 $

**方法二：直线与椭圆交点个数**

设切线方程为：$y - y_0 = k(x - x_0)$

椭圆方程为：$ \cfrac{x^2}{a^2} + \cfrac{y^2}{b^2} = 1 $

联立可得：$ (a^2 + b^2k^2)x^2 + 2b^2k(y_0- kx_0)x + b^2 \left((y_0 - kx_0)^2 -a^2 \right) = 0 $

由直线与椭圆相切，即只有一个交点，可得：$ \Delta = 4b^4k^2(y_0- kx_0)^2 - 4 b^2(a^2 + b^2k^2) \left((y_0 - kx_0)^2 -a^2 \right) = 0  $

化简可得：$ ({x_0}^2 - a^2)k^2 -2x_0y_0k + {y_0}^2 -b^2 = 0  $

当点$P$的坐标给定时，切线方程的斜率有且只有一个，可得：$ \Delta = 4{x_0}^2{y_0}^2 - 4({x_0}^2 - a^2)({y_0}^2 -b^2) = 0  $

化简可得：$ b^2{x_0}^2 +a^2{y_0}^2 = a^2b^2  $

切线方程的斜率为：$k = \cfrac{2x_0y_0}{2({x_0}^2 - a^2)} = - \cfrac{ b^2 x_0 }{a^2 y_0 } $

故过点$P$的椭圆的切线方程为：$ y - y_0 = - \cfrac{ b^2 x_0 }{a^2 y_0 } (x - x_0) $

化简可得：$\cfrac{x_0 x}{a^2} + \cfrac{y_0 y}{b^2} = 1 $



{{<myhr >}}

{{</myhr>}}

#### 例题 3.3

🟦 点$P$为椭圆$ \cfrac{x^2}{a^2} + \cfrac{y^2}{b^2} = 1 (a>b>0)$上任意一点，$F_1$和$F_2$是椭圆的两个焦点

（1）求$PF_1$，$PF_2$的取值范围

（2）求$ \left| PF_1  \right| \cdot \left| PF_2  \right| $的取值范围

（3）求$ \overrightarrow{PF_1} \cdot \overrightarrow{PF_2} $的取值范围

（4）求$\angle F_1PF_2 $的最大值

（5）若$\angle F_1PF_2 = \theta $，求$ \left| PF_1  \right| \cdot \left| PF_2  \right| $和$ S_{ \triangle PF_1F_2  } $

![](/maths_geometry/20250712-1.svg)

{{<note  >}}

- $ x_1 x_2 = \cfrac{p^2}{4} $
- $ y_1 y_2 = -p^2 $

{{</note>}}

☑️ **【解析】**（1）由抛物线定义，可得：$ \left| AF \right| = \left| AA_1 \right| $





{{<myhr >}}

{{</myhr>}}



