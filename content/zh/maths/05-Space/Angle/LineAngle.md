---
title: 线面角
description: >
 角度
date: 2023-10-03
weight: 100
---
{{< alert >}}
##### 题型：
###### （）求线面角
###### （）线面角最值求参数




##### 求点面距、线面距、面面距的方法
###### （1）直接定义：
###### （2）等体积（仅针对三棱锥，若平行平移法）
###### （3）向量：$d=\cfrac{\left|\overrightarrow{PA}\cdot\overrightarrow{n}\right|}{\left|\overrightarrow{n}\right|}$ （其中$P$为平面外一点，$A$为平面内任意一点，$\overrightarrow{n}$为平面的法向量）
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
























