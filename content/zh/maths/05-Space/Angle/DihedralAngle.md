---
title: 二面角
description: >
 角度
date: 2023-10-03
weight: 100
---
{{< alert >}}

{{< /alert >}}
















## 举例说明

------

【例题】如下图 1 所示，设$PAB$平面与平面$\alpha$所成的二面角为$\theta$，$\triangle PAB$在平面$\alpha$内的投影为$\triangle OAB$，证明：$\cos\theta=\cfrac{S_{\triangle OAB}}{S_{\triangle PAB}}$

{{< figure src="/images/maths/space/angle/dihedral-002.png" >}}
&emsp; &emsp; &emsp; &emsp; 图 1 &emsp; &emsp; &emsp; &emsp;&emsp; &emsp; &emsp; &emsp;&emsp;&emsp; &emsp;   图 2

【解析】 根据题意，作$PC \perp AB$交$AB$于$C$，连接$OC$，如图 2 所示

$PO \perp \alpha$，$AC\subset\alpha$，可得：$PO \perp AC$

又$PC \perp AB$，$PO \cap PC=P$，可得：$AB \perp$平面$POC$

由二面角定义，可得：$\theta=\angle PCO$

可得：$\cfrac{S_{\triangle OAB}}{S_{\triangle PAB}}=\cfrac{\frac{1}{2} AB \cdot OC}{\frac{1}{2}AB \cdot PC}=\cos\theta$，即原命题成立

> 注意：当二面角为直角时，一个平面在另一个平面的投影为一条直线



【例题】已知$\triangle ABC$的内角$A、B、C$所对应边分别为$a、b、c$，且$a<b<c$，点$P$在$\triangle ABC$所在平面上的投影恰好是$\triangle ABC$的重心$G$，设平面$PAB、PAC、PBC$与底面$ABC$所成的锐二面角分别为$\alpha、\beta、\gamma$，证明：$\alpha>\beta>\gamma$

【解析】 如下图所示，作$PF \perp AB,PE \perp AC,PD \perp BC$，连接$GF、GE、GD$

{{< figure src="/images/maths/space/angle/dihedral-003.png" >}}

根据题意，$PG \perp$平面$ABC$，$AB\subset$平面$ABC$，可得：$PG \perp AB$

又$PF \perp AB$，$PF \cap PG=P$，可得：$AB \perp$平面$PGF$

由二面角定义，可得：$\alpha=\angle PFG$

同理，可得：$\beta=\angle PEG$，$\gamma=\angle PDG$

可得：$\cos\alpha=\frac{GF}{PG}$，$\cos\beta=\frac{GE}{PG}$，$\cos\gamma=\frac{GD}{PG}$

点$P$在$\triangle ABC$所在平面上的投影恰好是$\triangle ABC$的重心$G$，可得：$S_{\triangle GAB}=S_{\triangle GAC}=S_{\triangle GBC}$

可得：$AB \cdot GF=AC \cdot GE=BC \cdot GD$

由$a<b<c$，可得：$GF<GE<GD$

可得：$\cos\alpha<\cos\beta<\cos\gamma$

由$f(x)=\cos x$在$(0,\frac{\pi}{2})$上单调递减，可得：$\alpha>\beta>\gamma$




