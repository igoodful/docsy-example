---
title: 通项可求
description: >
  A short lead description about this content page. It can be **bold** or _italic_ and can be split over multiple paragraphs.
date: 2017-01-05
weight: 2


---

{{< alert >}}
递推是数列的灵魂与核心，递推数列的构建，则是数学建模，

递推关系包含递推方程和递推不等式，以及其他描述

{{< /alert >}}





【】数列$\\{a_n\\}$满足：$a_1=1,a_{n+1}=\cfrac{2a_n}{8a_n+8\sqrt{a_n}+(n+1)}$，记数列$\\{a_n\\}$的前$n$项和$S_n$，证明：$1<S_{2022}<\cfrac{3}{2}$

【解析】根据题意，可得：$\cfrac{1}{a_{n+1}}=\cfrac{8a_n+8\sqrt{a_n}+(n+1)}{2a_n}=\left(2+\cfrac{1}{\sqrt{a_n}}\right)^2+\cfrac{n-1}{2a_n}\ge\left(2+\cfrac{1}{\sqrt{a_n}}\right)^2$

可得：$\cfrac{1}{\sqrt{a_{n+1}}}-\cfrac{1}{\sqrt{a_n}} \ge 2$

可得：$a_n \le \cfrac{1}{(2n-1)^2}$

当$n \ge 2$时，可得：$a_n \le \cfrac{1}{(2n-1)^2} \le \cfrac{1}{(2n-1)(2n-3)} = \cfrac{1}{2}\left(\cfrac{1}{2n-1} - \cfrac{1}{2n-3} \right)$

当$n \ge 2$时，可得：$S_n \le \cfrac{7}{6}-\cfrac{1}{4n-2}$


{{< alert color="warning" title="Warning" >}}This is a warning with a title.{{< /alert >}}



