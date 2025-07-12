---
title: 共线
description: >
  方程思想
date: 2017-01-05
weight: 200
---

{{< alert >}}
### 共线的表达：

- ##### $A、B、C$三点共线
- ##### 点$C$在直线$AB$上

### 解题思路：

- ##### 先找出两个平面，再证明这些点都是这两个平面的公共点，根据基本事实3知：
- ##### 选择其中两点确定一条直线，然后证明其他点也在这条直线上
- ##### 向量证明

{{< /alert >}}

## 公共

------

【例题】如图，已知$\triangle ABC$在平面$\alpha$外，它的三边所在直线分别交平面$\alpha$于点$P、Q、R$，证明：$P、Q、R$三点共线

{{< figure src="/images/maths/space/gongxian.png" title="图 001" >}}

【**解析：几何法**】设$\triangle ABC$所确定的平面为$\beta$，易知，$P\in BC$，$BC \subseteq \beta$，可得：$P\in \beta$

又$P\in \alpha$，可得：$P$为$\alpha$与$ \beta$的公共点

同理 ，可得：$Q$为$\alpha$与$ \beta$的公共点，$R$为$\alpha$与$ \beta$的公共点

由基本事实3，可得：$P、Q、R$三点共线

【**解析：几何向量法**】 设点$O$为平面$\alpha$内的任意一点，可设：$\overrightarrow{AB}=\overrightarrow{a},\overrightarrow{AC}=\overrightarrow{b},\overrightarrow{OA}=\overrightarrow{c}$

由$A、B、Q$共线，$A、C、R$共线，$C、B、P$共线，可得：$\overrightarrow{AQ}=\mu_1\overrightarrow{a},\overrightarrow{AR}=\mu_2\overrightarrow{b},\overrightarrow{CP}=\mu_3\overrightarrow{CB}=\mu_3(\overrightarrow{a}-\overrightarrow{b})$（其中$\mu_1>1$，$\mu_2>1$，$\mu_3>1$）

可得：$\overrightarrow{OP}=\mu_3\overrightarrow{a}+(1-\mu_3)\overrightarrow{b}+\overrightarrow{c}$，$\overrightarrow{OQ}=\mu_1\overrightarrow{a}+\overrightarrow{c}$，$\overrightarrow{OR}=\mu_2\overrightarrow{b}+\overrightarrow{c}$

由$P、Q、R、O$四点共面，可得：$\overrightarrow{OP}=x\overrightarrow{OQ}+y\overrightarrow{OR}$

解得：$\mu_1+\mu_2\mu_3=\mu_1(\mu_2+\mu_3)$

易知：$\overrightarrow{PR}=-\mu_3\overrightarrow{a}+(\mu_2+\mu_3-1)\overrightarrow{b}$，$\overrightarrow{QR}=-\mu_1\overrightarrow{a}+\mu_2\overrightarrow{b}$

又$\cfrac{-\mu_3}{-\mu_1}=\cfrac{\mu_2+\mu_3-1}{\mu_2}$，即存在实数$\lambda$，使得：$\overrightarrow{PR}=\lambda\overrightarrow{QR}$

综上，可得：$P、Q、R$三点共线










# &#10084;






</br>
</br>












