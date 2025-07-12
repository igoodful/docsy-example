---
title: 方法
description: >
  距离
date: 2017-01-05
weight: 100
---
{{< alert >}}
##### 求点面距、线面距、面面距的方法
###### （1）直接定义：
###### （2）转化：①等体积（仅针对三棱锥）：②平行平移法：点面距，线面距，面面距；
###### （3）向量：$d=\cfrac{\left|\overrightarrow{PA}\cdot\overrightarrow{n}\right|}{\left|\overrightarrow{n}\right|}$ （其中$P$为平面外一点，$A$为平面内任意一点，$\overrightarrow{n}$为平面的法向量）
{{< /alert >}}



## 等体积法
等体积法：我们在求点面距的时候，有时候给出的平面不是用三角形表示的，此时我们需要从给定的平面找个恰当的三角形平面，转化为三棱锥形式，如下图，在三棱锥 （或称为四面体 ）中，其体积的计算方式也不同，通常底面三角形的面积都比较容易求，当底面选择不同时，三棱锥的高的计算难度可能相差很大，我们可找出容易计算的，核心是线面垂直存在，即某顶点在底面的射影的位置容易找到

恒有：$V_{P-ABC}=V_{A-PBC}=V_{B-PAC}=V_{C-PAB}$


{{< figure src="/images/maths/space/distance-v.png" title="图 001" >}}














## 公共

------

【例题】如图，已知$\triangle ABC$在平面$\alpha$外，它的三边所在直线分别交平面$\alpha$于点$P、Q、R$，证明：$P、Q、R$三点共线

{{< figure src="/images/maths/space/gongxian.png" title="图 001" >}}

【**解析：几何法**】设$\triangle ABC$所确定的平面为$\beta$，易知，$P\in BC$，$BC \subseteq \beta$，可得：$P\in \beta$

又$P\in \alpha$，可得：$P$为$\alpha$与$ \beta$的公共点

同理 ，可得：$Q$为$\alpha$与$ \beta$的公共点，$R$为$\alpha$与$ \beta$的公共点

综上，可得：$P、Q、R$三点共线

【**解析：几何向量法**】 设点$O$为平面$\alpha$内的任意一点，可设：$\overrightarrow{AB}=\overrightarrow{a},\overrightarrow{AC}=\overrightarrow{b},\overrightarrow{OA}=\overrightarrow{c}$

由$A、B、Q$共线，$A、C、R$共线，$C、B、P$共线，可得：$\overrightarrow{AQ}=\mu_1\overrightarrow{a},\overrightarrow{AR}=\mu_2\overrightarrow{b},\overrightarrow{CP}=\mu_3\overrightarrow{CB}=\mu_3(\overrightarrow{a}-\overrightarrow{b})$（其中$\mu_1>1$，$\mu_2>1$，$\mu_3>1$）

可得：$\overrightarrow{OP}=\mu_3\overrightarrow{a}+(1-\mu_3)\overrightarrow{b}+\overrightarrow{c}$，$\overrightarrow{OQ}=\mu_1\overrightarrow{a}+\overrightarrow{c}$，$\overrightarrow{OR}=\mu_2\overrightarrow{b}+\overrightarrow{c}$

由$P、Q、R、O$四点共面，可得：$\overrightarrow{OP}=x\overrightarrow{OQ}+y\overrightarrow{OR}$

解得：$\mu_1+\mu_2\mu_3=\mu_1(\mu_2+\mu_3)$

易知：$\overrightarrow{PR}=-\mu_3\overrightarrow{a}+(\mu_2+\mu_3-1)\overrightarrow{b}$，$\overrightarrow{QR}=-\mu_1\overrightarrow{a}+\mu_2\overrightarrow{b}$

又$\cfrac{-\mu_3}{-\mu_1}=\cfrac{\mu_2+\mu_3-1}{\mu_2}$，即存在实数$\lambda$，使得：$\overrightarrow{PR}=\lambda\overrightarrow{QR}$

综上可得：$P、Q、R$三点共线






























