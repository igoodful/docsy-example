---
title: 无参不等式
description: inequality
date: 2023-10-30
weight: 2
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

1. 放缩方向保持一致

2. 预处理
   1. 因式分解
   2. 同时取对数、取指数
   3. 换元同构
   4. 分离对数

3. 寻找放缩源

4. 用在哪个阶段？直接、一阶、二阶、。。。


{{</alert>}}


---
&#128311;证明：$\forall_x \in (1,+ \infty)$，$ \operatorname{e}^x + \ln x - \operatorname{e} > 0$

{{<alert color="danger" title="注意" >}}

- 零阶单调

{{</alert>}}

【**解析**】

记$f(x) = \operatorname{e}^x + \ln x - \operatorname{e}$，$x \in (1,+ \infty)$

易知：$f(x)$在$(1,+ \infty)$上单调递增

又$f(1) = 0$，可得：$f(x) > 0 $

故$\forall_x \in (1,+ \infty)$，$ \operatorname{e}^x + \ln x - \operatorname{e} > 0$


---
&#128311;证明：$\forall_x \in (0,\cfrac{\pi}{6})$，$ \tan x < \operatorname{e}^{- \frac{1}{2} \cos 2x} < \operatorname{e}^{-\frac{1}{4}} $

{{<alert color="danger" title="注意" >}}

- 单调不等式
- 同时取对数
- 正切转化为正余弦
- 余弦二倍角公式展开

{{</alert>}}

【**解析**】


- &#9312; 证明：$ \operatorname{e}^{- \frac{1}{2} \cos 2x} < \operatorname{e}^{-\frac{1}{4}} $

只需证明：$ - \cfrac{1}{2} \cos 2x < -\cfrac{1}{4} $

又$ x \in (0,\cfrac{\pi}{6})$，可得：$ 2x \in (0,\cfrac{\pi}{3})$

可得：$ \cfrac{1}{2} < \cos 2x < 1 $

可得：$ - \cfrac{1}{2} \cos 2x < -\cfrac{1}{4} $


- &#9313; 证明：$ \tan x < \operatorname{e}^{- \frac{1}{2} \cos 2x} $

只需证明：$ \cfrac{\sin x}{ \cos x} < \operatorname{e}^{- \frac{1}{2} (\cos^2x - \sin^2 x)} $

只需证明：$ \ln {\cfrac{\sin x}{ \cos x}} < - \cfrac{1}{2} (\cos^2x - \sin^2 x) $

只需证明：$ \ln(\sin x) - \cfrac{1}{2} \sin^2 x < \ln(\cos x) - \cfrac{1}{2} \cos^2 x$

记$ f(x) = \ln x - \cfrac{1}{2}x^2 $，$x \in [0,1]$

可得：$ f^{'}(x) = \cfrac{-x^2 + 1}{x} \ge 0$

故$ f(x)$在$ [0,1 ] $上单调递增

又$x \in \left(0,\cfrac{\pi}{6} \right)$时，可得：$ 0 <\sin x < \cos x < 1 $

可得：$ f(\sin x) < f(\cos x) $

综上&#9312;&#9313;，可得：$\forall_x \in (0,\cfrac{\pi}{6})$，$ \tan x < \operatorname{e}^{- \frac{1}{2} \cos 2x} < \operatorname{e}^{-\frac{1}{4}} $

















