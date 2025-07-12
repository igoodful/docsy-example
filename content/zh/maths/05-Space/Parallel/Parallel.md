---
title: 平行问题
description: >
 平行问题
date: 2023-10-03
weight: 100
---
{{< alert >}}

## 一、平行的常见来源

{{< figure src="/images/maths/space/parallel/parallel-001.png" >}}
&emsp; &emsp; &emsp; &emsp; 图 1 &emsp; &emsp; &emsp; &emsp;&emsp; &emsp; &emsp; &emsp;&emsp;&emsp; &emsp;   图 2 &emsp; &emsp; &emsp; &emsp;&emsp; &emsp; &emsp; &emsp;&emsp;  图 3  &emsp; &emsp; &emsp; &emsp; &emsp;&emsp; &emsp; &emsp; &emsp;&emsp; &emsp; 图 4

{{< figure src="/images/maths/space/parallel/parallel-002.png" >}}
&emsp; &emsp; &emsp; &emsp; 图 5&emsp; &emsp; &emsp; &emsp;&emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; 图 6&emsp; &emsp; &emsp; &emsp;&emsp; &emsp; &emsp; &emsp;&emsp; &emsp; 图 7

{{< figure src="/images/maths/space/parallel/parallel-003.png" >}}
&emsp; &emsp; &emsp; &emsp; 图 8 &emsp; &emsp; &emsp; &emsp;&emsp; &emsp; &emsp; &emsp;&emsp;&emsp; &emsp;   图 9

- 图 1：在平行四边形（含菱形、矩形、正方形）中，可得：$AB // CD$，$AD // BC$

- 图 2：在梯形$ABCD$中，可得：$AD // BC$

- 图 3：在三棱柱$ABC // A_1B_1C_1$中，可得：侧面均为平行四边形

- 图 4：在正四棱锥$P-ABCD$中，可得：底面$ABCD$为正方形

- 图 5：在$\triangle ABC$中，若$\frac{AD}{AB}=\frac{AE}{AC}$，可得：$DE // BC$

- 图 6：在梯形$ABCD$中，若$\frac{AE}{AB}=\frac{DF}{DC}$，可得：$AD // EF // BC$

- 图 7：在平面内，直线$l$分别直线$m$与$n$相交
  - 若$\angle 1= \angle 2$（同位角相等），可得：$m // n$

  - 若$\angle 2= \angle 3$（内错角相等），可得：$m // n$

  - 若$\angle 2+ \angle 4=180^{\circ}$（同旁内角互补），可得：$m // n$

- 图 8：平行的传递性，在平面和空间中均成立

  - 文字描述：平行于同一条直线的两条直线平行

  - 数学表示：若$m // p$，$p // n$，可得：$m // n$

- 图 9：

  - 文字描述：垂直于同一个平面的两条直线平行

  - 数学表示：若$m \perp \alpha$，$n \perp \alpha$，可得：$m // n$

## 二、线面平行与面面平行

{{< figure src="/images/maths/space/parallel/parallel-004.png" >}}
&emsp; &emsp; &emsp; &emsp; 图 10 &emsp; &emsp; &emsp; &emsp;&emsp; &emsp; &emsp; &emsp;&emsp;&emsp; &emsp;   图 11
{{< figure src="/images/maths/space/parallel/parallel-005.png" >}}
&emsp; &emsp; &emsp; &emsp; 图 12 &emsp; &emsp; &emsp; &emsp;&emsp; &emsp; &emsp; &emsp;&emsp;&emsp; &emsp;   图 13

- 如图 10：**线面平行判定**

  - 文字描述：若平面外的一条直线与平面内的一条直线平行，则这条直线与平面平行

  - 数学表示：若 $m//n$，$m\not\subset\alpha$，$n\subset\alpha$，则 $m / / \alpha$

- 如图 11：**线面平行性质**
  - 文字描述：若一条直线和一个平面平行，经过这条直线的平面和这个平面相交，则该直线域交线平行

  - 数学表示：若 $m//\alpha$，$m\subset\beta$，$\alpha\cap\beta = n$，则 $m / / n$

- 如图 12：**面面平行判定**

  - 文字描述：若一个平面内有两条相交直线都平行于另一个平面，则这两个平面平行

  - 数学表示：若 $m \subset \alpha$，$n \subset \alpha$，$m \cap n = P$，$m // \beta$，$n // \beta$，则 $\alpha / / \beta$

- 如图 13：**面面平行性质**

  - 性质一：
    - 文字描述：若两个平行平面同时和第三个平面相交，则所得的两条交线平行

    - 数学表示：若 $\alpha / / \beta$，$\gamma\cap\alpha=m$，$\gamma\cap\beta=n$，则 $m / / n$

  - 性质二：

    - 文字描述：若两个平面平行，则在一个平面内的任意—条直线平行于另外一个平面

    - 数学表示：若 $\alpha / / \beta$，$m\subset\alpha$，则 $m / / \beta$



#####

















{{< /alert >}}



## 等体积法
等体积法：我们在求点面距的时候，有时候给出的平面不是用三角形表示的，此时我们需要从给定的平面找个恰当的三角形平面，转化为三棱锥形式，如下图，在三棱锥 （或称为四面体 ）中，其体积的计算方式也不同，通常底面三角形的面积都比较容易求，当底面选择不同时，三棱锥的高的计算难度可能相差很大，我们可找出容易计算的，核心是线面垂直存在，即某顶点在底面的射影的位置容易找到

恒有：$V_{P-ABC}=V_{A-PBC}=V_{B-PAC}=V_{C-PAB}$


{{< figure src="/images/maths/space/distance-v.png" >}}














## 举例说明

------

【例题】如图，在三棱锥$P-ABC$中，$PA=PB=PC$，点$P$在底面$ABC$上的射影$E$在$AC$上，$PF\bot AB$于$F$

{{< figure src="/images/maths/space/angle/line-angle.png" >}}
（1）证明：$BC//$平面$PEF$；

（2）若$\angle BAC=\angle APC=\cfrac{\pi}{3}$，求直线$BE$与平面$PAB$所成角的余弦值.

【思路】考试的时候，你是不可能一道题多个方法去做，你肯定只会选择其中你知道的或你熟悉的方法去做

【解析】

（1）证明：$PA=PC$，点$P$在底面$ABC$上的射影$E$在$AC$上，可得：$E$为$AC$中点

$PA=PB$，$PF\bot AB$，可得：$F$为$AB$中点

故$EF$为$\triangle ABC$中位线，可得：$EF//$$BC$

又$EF\subset$平面$PEF$，$BC\not\subset$平面$PEF$，可得：$BC//$平面$PEF$；


（2）【**等体积法**】设$PA=PB=PC=2a$，点$E$到平面$PAB$的距离为$d$，直线$BE$与平面$PAB$所成角为$\theta$

在三棱锥$E-PAB$中，可得：$V_{E-PAB}=V_{P-ABE}$

易知：$PA=PB=PC=AC=2a$，$AB=AE=BE=a$

可得：$\cfrac{1}{3} \times \cfrac{1}{2} \cdot a \cdot\cfrac{\sqrt{15}}{2}a \cdot d=\cfrac{1}{3} \times \cfrac{1}{2} \cdot \cfrac{1}{2} a \cdot a \cdot \sqrt{3}a$，即  $d=\cfrac{\sqrt{5}}{5}a$

又$\left|PE\right|=\sqrt{3}a$，可得：$\sin\theta=\cfrac{d}{\left|PE\right|}=\cfrac{\sqrt{15}}{5}$

直线$BE$与平面$PAB$所成角的余弦值为：$\cfrac{\sqrt{10}}{5}$


【**向量法**】根据题意，建立以$F$为原点的空间直角坐标系$F-xyz$，如下图所示

{{< figure src="/images/maths/space/angle/line-angle-001.png" >}}

设$PA=PB=PC=2a$，点$E$到平面$PAB$的距离为$d$，直线$BE$与平面$PAB$所成角为$\theta$

易知：$PA=PB=PC=AC=2a$，$AB=AE=BE=a$

可得：$F\left(0,0,0\right)$，$A\left(-\cfrac{1}{2}a,0,0\right)$，$B\left(\cfrac{1}{2}a,0,0\right)$，$E\left(0,\cfrac{\sqrt{3}}{2}a,0,0\right)$，$P\left(0,\cfrac{\sqrt{3}}{2}a,\sqrt{3}a\right)$

可得：$\overrightarrow{BE}=\left( -\cfrac{1}{2}a, \cfrac{\sqrt{3}}{2}a,0\right)$，$\overrightarrow{AB}=\left( a,0,0\right)$，$\overrightarrow{FP}=\left( 0,\cfrac{\sqrt{3}}{2}a,\sqrt{3}a\right)$

设平面$PAB$的一个法向量为$\overrightarrow{n} = \left( x,y,z \right)$，可得：$ \begin{cases} \overrightarrow{n} \cdot \overrightarrow{AB} = 0 \\\\[2ex] \overrightarrow{n} \cdot \overrightarrow{FP} = 0 \end{cases} $，即$ \begin{cases} x = 0 \\\\[2ex] \cfrac{\sqrt{3}}{2}ay + \sqrt{3}az = 0 \end{cases} $

不妨令$y=2$，则$z=-1$，故平面$PAB$的一个法向量为：$\overrightarrow{n} = \left( 0,2, -1 \right)$

可得：$\sin\theta = \left| \cfrac{\overrightarrow{n} \cdot \overrightarrow{BE}}{\left| \overrightarrow{n} \right| \cdot \left| \overrightarrow{BE} \right|} \right| = \cfrac{\sqrt{15}}{5}$

直线$BE$与平面$PAB$所成角的余弦值为：$\cfrac{\sqrt{10}}{5}$






【**作图法**】 如下图所示，过$E$作$EH \perp PF$，垂足为$H$，设$PA=PB=PC=2a$，设直线$BE$与平面$PAB$所成角为$\theta$

{{< figure src="/images/maths/space/angle/line-angle-002.png" >}}

根据题意，$AB \perp EF,AB \perp PF,EF \cap PF = F$，可得：$AB \perp$平面$PEF$

又$AB\subset$平面$PAB$，可得：平面$PEF\perp$平面$PAB$

又平面$PEF\cap$平面$PAB=PF$，$EH\subset$平面$PEF$，$EH \perp PF$，可得：平面$EH\perp$平面$PAB$

可得：$\theta=\angle EBH$

根据题意，$\triangle PAB$为等腰三角形，$\triangle ABE$为等边三角形，可得：$DF=\cfrac{\sqrt{15}}{2}a$

在$\triangle PEF$中，可得：$PE\cdot EF=PF\cdot EH$，即$HE=\cfrac{\sqrt{15}}{5}a$

可得：$\sin\theta=\cfrac{EH}{EB}=\cfrac{\sqrt{15}}{5}$，即$\cos\theta=\cfrac{\sqrt{10}}{5}$
























