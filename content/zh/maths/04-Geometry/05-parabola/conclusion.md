---
title: 基本推导
description: conclusion
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

能推导就记住结论，若不能推导就不要记了，因为记也是浪费自己的时间



- https://zhuanlan.zhihu.com/p/129045743
- https://zhuanlan.zhihu.com/p/26904612230
- https://zhuanlan.zhihu.com/p/658966832
- https://zhuanlan.zhihu.com/p/14338977779





{{<alert color="danger" title="注意" >}}

1）几何法

2）代数法，建立函数

3）设夹角，建立三角函数


{{</alert>}}


## 一、定义
## 代数法
## 几何法》三角函数

---

#### 例题 3.1

🟦 叙述抛物线的定义，并推导抛物线的一个标准方程

{{<note title="🔍 思路" >}}

- 距离方程

{{</note>}}

![](/maths_geometry/20250710-2.svg)


☑️ **【解析】** （1）定义：平面内与一定点和一定直线（定直线不经过定点）的距离相等的点的轨迹，其中定点叫抛物线的焦点，定直线叫抛物线的准线。

（2）推导：记定点为$F(\cfrac{p}{2},0)$（$p>0$），定直线为$l: x=-\cfrac{p}{2}$，动点为$P(x,y)$，建立如图所示坐标系

依题意，可得：$ \sqrt{ (x-\cfrac{p}{2})^2 + y^2 } = \left| x + \cfrac{p}{2}  \right| $

化简，可得：$ y^2 = 2px $（$p>0$）



{{<myhr >}}

{{</myhr>}}


#### 例题 3.2

🟦 已知抛物线$ y^2 = 2px (p>0) $，过焦点$F$的直线$l$交抛物线于$A(x_1,y_1)$、$B(x_2,y_2)$两点，准线与$x$轴的交点为$ F_1 $

（1）求$ x_1 x_2 $，$ y_1 y_2 $

（2）求$\left | AF \right | $，$ \left | BF \right | $，$ \left | AB \right |$，$ \cfrac{1}{ \left | AF \right |} + \cfrac{1}{ \left | BF \right |} $

（3）求$ S_{ \triangle AOB} $的最小值

（4）证明：$ FF_1 $为$ \angle AF_1B $的角平分线



{{<note title="🔍 思路" >}}
- $ x_1 x_2 = \cfrac{p^2}{4} $
- $ y_1 y_2 = -p^2 $

{{</note>}}


![](/maths_geometry/20250707-3.svg)

☑️ **【解析】** （1）记直线$l$的方程为：$ x = my+\cfrac{p}{2} $

联立直线与抛物线方程，可得：$ y^2 -2pmy -p^2 = 0 $

易知：$ \Delta = 4p^2m^2 +4p^2 >0 $

由韦达定理，可得：
$
\left\\{
    \begin{array}{c}
        y_1 + y_2 &= 2pm \\\\[2ex]
        y_1 y_2 &= -p^2 \\\\
    \end{array}
\right.
$

可得：$ x_1 x_2 = (my_1+\cfrac{p}{2})(my_2+\cfrac{p}{2}) = m^2y_1y_2 + \cfrac{pm}{2} (y_1 + y_2) + \cfrac{p^2}{4} $

可得：$ x_1 x_2 = \cfrac{p^2}{4} $

{{<minhr >}}

{{</minhr>}}


（2）由抛物线定义，可得：$ \left| AF \right| = \left| AA_1 \right| = \cfrac{p}{2} + x_1 $

同理，可得：$ \left| BF \right| = \left| BB_1 \right| = \cfrac{p}{2} + x_2 $

可得：$ \left| AB \right| = x_1 + x_2 + p $

可得：$ \cfrac{1}{ \left | AF \right |} + \cfrac{1}{ \left | BF \right |} = \cfrac{1}{ \cfrac{p}{2} + x_1 } + \cfrac{1}{ \cfrac{p}{2} + x_2 } = \cfrac{(x_1 +x_2) +p }{ x_1x_2 + \cfrac{p}{2} (x_1+x_2) + \cfrac{p^2}{4} } = \cfrac{2}{p} $

{{<minhr >}}

{{</minhr>}}

（3）方法一（水平共同边拆成两个三角形）：

依题意并结合（1），可得：

$\begin{aligned}
    S_{ \triangle AOB } &= S_{ \triangle OFA } + S_{ \triangle OFB } \\\\[2ex]
    &= \cfrac{1}{2} \left| OF \right| ( \left| y_1 - y_2 \right| ) \\\\[2ex]
    &= \cfrac{p}{4} \cdot \sqrt{ (y_1 + y_2)^2 - 4y_1  y_2 } \\\\[2ex]
    &= \cfrac{p^2}{2} \cdot \sqrt{ 1+m^2 }
\end{aligned}
$

故当$ m=0 $时，$ S_{ \triangle AOB } $取得最小值为：$ \cfrac{p^2}{2} $


方法二（点线距）：

记点$O$到直线$AB$的距离为$d$，可得：$ d = \left| \cfrac{p}{2} \right| \cdot \cfrac{ 1 }{ \sqrt{ m^2+1 } } $

依题意并结合（1）（2），可得：

$\begin{aligned}
    S_{ \triangle AOB } &= \cfrac{1}{2} \cdot  \left| AB \right| \cdot  d \\\\[2ex]
    &= \cfrac{ p(x_1 + x_2 + p) }{ 4\sqrt{ m^2+1 } } \\\\[2ex]
    &= \cfrac{p^2}{2} \cdot \sqrt{ 1+m^2 }
\end{aligned}
$

故当$ m=0 $时，$ S_{ \triangle AOB } $取得最小值为：$ \cfrac{p^2}{2} $

{{<minhr >}}

{{</minhr>}}

（4）依题意并结合（1），可得：

$\begin{aligned}
    k_{AF_1} + k_{BF_1} &= \cfrac{ y_1 }{ x_1 +\cfrac{p}{2} } + \cfrac{ y_2 }{ x_2 +\cfrac{p}{2} } \\\\[2ex]
    &= \cfrac{ x_1y_2 +x_2y_1 +\cfrac{p}{2}(y_1+y_2) }{ (x_1 +\cfrac{p}{2})(x_2 +\cfrac{p}{2}) } \\\\[2ex]
    &=  \cfrac{ (my_1+\cfrac{p}{2})y_2 + (my_2+\cfrac{p}{2})y_1 +\cfrac{p}{2}(y_1+y_2) }{ (x_1 +\cfrac{p}{2})(x_2 +\cfrac{p}{2}) } \\\\[2ex]
    &= \cfrac{ 2my_1y_2+(y_1+y_2) }{ (x_1 +\cfrac{p}{2})(x_2 +\cfrac{p}{2}) } \\\\[2ex]
    &= 0
\end{aligned}
$

故$ FF_1 $为$ \angle AF_1B $的角平分线


{{<myhr >}}

{{</myhr>}}


#### 例题 3.3

🟦 已知抛物线$ y^2 = 2px (p>0) $，过焦点$F$的直线$l$交抛物线于$A$、$B$两点，$ \theta $为直线$l$的倾斜角

（1）求$ \left | AF \right | $，$ \left | BF \right | $，$ \cfrac{ \left | AF \right | }{  \left | BF \right | } $，$ \cfrac{1}{ \left | AF \right |} + \cfrac{1}{ \left | BF \right |} $，$ \left | AB \right |$的最小值

（2）求$ S_{ \triangle AOB} $，$  S_{ \triangle AF_1B } $，$ \cfrac{ S_{ \triangle AOF } }{ S_{ \triangle BOF } } $，$  \cfrac{ S^2_{ \triangle AOB } }{ \left | AB \right | } $

（3）若直线$m$与直线$l$垂直且与抛物线交于$C$，$D$两点：

$\qquad$（i）求$ \cfrac{ 1 }{ \left| AB \right| } + \cfrac{1}{ \left| CD \right| } $的值

$\qquad$（ii）求$ \left| AB \right| + \left| CD \right| $的最小值

$\qquad$（iii）求$ \left| AB \right| \cdot \left| CD \right| $的最小值


![](/maths_geometry/20250707-4.svg)



{{<note  >}}

- $ x_1 x_2 = \cfrac{p^2}{4} $
- $ y_1 y_2 = -p^2 $

{{</note>}}

☑️ **【解析】**（1）由抛物线定义，可得：$ \left| AF \right| = \left| AA_1 \right| $

在矩形$AA_1F_1C$中，可得：$ \left| AA_1 \right| = \left| CF_1 \right| $

在直角$ \triangle AFC $中，可得：$ \left| CF \right| = \left| AF \right| \cdot \cos \theta $

综上，可得：$ \left| AF \right| = \left| FF_1 \right| + \left| CF \right| = p + \left| AF \right| \cdot \cos \theta $

解得：$ \left| AF \right| = \cfrac{p}{ 1 - \cos \theta } $

同理，可得：$ \left| BF \right| = \cfrac{p}{ 1 + \cos \theta } $

可得：$ \cfrac{ \left | AF \right | }{  \left | BF \right | } = \cfrac{ 1 + \cos \theta  }{ 1 - \cos \theta  } $

可得：$ \cfrac{1}{ \left | AF \right |} + \cfrac{1}{ \left | BF \right |} = \cfrac{1 - \cos \theta}{p} + \cfrac{1 - \cos \theta}{p} = \cfrac{2}{p} $

可得：$ \left| AB \right| = \left| AF \right| + \left| BF \right| = \cfrac{p}{ 1 - \cos \theta } + \cfrac{p}{ 1 + \cos \theta } = \cfrac{2p}{ \sin^2 \theta } $

当$ \theta = \cfrac{\pi}{2} $时，$ \left| AB \right|_{min} = 2p $，此时$AB$为抛物线的通径，即通径是抛物线焦点弦长的最小值。

{{<minhr >}}

{{</minhr>}}

（2）依题意并结合（1），可得：

$\begin{aligned}
    S_{ \triangle AOB } &= S_{ \triangle AOF } + S_{ \triangle BOF } \\\\[2ex]
    &= \cfrac{1}{2} \left| OF \right| \cdot \left| AF \right| \cdot \sin \theta + \cfrac{1}{2} \left| OF \right| \cdot \left| BF \right| \cdot \sin \theta \\\\[2ex]
    &= \cfrac{1}{2} \left| OF \right| \cdot \left| AB \right| \cdot \sin \theta  \\\\[2ex]
    &= \cfrac{p^2}{2 \sin \theta  }
\end{aligned}
$

同理，可得：

$\begin{aligned}
    S_{ \triangle AF_1B } &= S_{ \triangle AF_1F } + S_{ \triangle BF_1F } \\\\[2ex]
    &= \cfrac{1}{2} \left| F_1F \right| \cdot \left| AF \right| \cdot \sin \theta + \cfrac{1}{2} \left| F_1F \right| \cdot \left| BF \right| \cdot \sin \theta \\\\[2ex]
    &= \cfrac{1}{2} \left| FF_1 \right| \cdot \left| AB \right| \cdot \sin \theta  \\\\[2ex]
    &= \cfrac{p^2}{ \sin \theta }
\end{aligned}
$

可得：$ \cfrac{ S_{ \triangle AOF } }{ S_{ \triangle BOF } } =  \cfrac{ \left| AF \right| }{ \left| BF \right| } = \cfrac{ 1 + \cos \theta }{ 1 - \cos \theta } $

可得：$  \cfrac{ S^2_{ \triangle AOB } }{ \left | AB \right|  } = (\cfrac{ p^2 } { 2 \sin \theta } )^2  \cdot  \cfrac{ \sin^2 \theta }{ 2p }  = ( \cfrac{ p }{ 2 } )^3 $

{{<minhr >}}

{{</minhr>}}

（3）

（i）由（1）可知：$ \left| AB \right| = \cfrac{2p}{ \sin^2 \theta } $

由$ m \perp l $，可得：$ \left| CD \right| = \cfrac{2p}{ \sin^2 (\theta + \cfrac{\pi}{2}) } = \cfrac{2p}{ \cos^2 \theta } $

综上，可得：$ \cfrac{ 1 }{ \left| AB \right| } + \cfrac{1}{ \left| CD \right| } = \cfrac{1}{2p} $



（ii）由（i）可得：

$\begin{aligned}
    \left| AB \right| + \left| CD \right| &= \cfrac{2p}{ \sin^2 \theta } + \cfrac{2p}{ \cos^2 \theta } \\\\[2ex]
    &= (\cfrac{2p}{ \sin^2 \theta } + \cfrac{2p}{ \cos^2 \theta }) (\sin^2 \theta + \cos^2 \theta  )\\\\[2ex]
    &= 4p + 2p \cdot ( \cfrac{\cos^2 \theta}{ \sin^2 \theta } +  \cfrac{\sin^2 \theta}{ \cos^2 \theta }) \\\\[2ex]
\end{aligned}
$

故当且仅当$ \theta = \cfrac{\pi}{4} $或$ \theta = \cfrac{3\pi}{4} $时，$  \left| AB \right| + \left| CD \right| $取得最小值为$ 8p $

（iii）由（i）可得：

$\begin{aligned}
    \left| AB \right| \cdot \left| CD \right| &= \cfrac{2p}{ \sin^2 \theta } \cdot \cfrac{2p}{ \cos^2 \theta } \\\\[2ex]
    &= \cfrac{16p^2}{ (2\sin \theta \cos \theta)^2 } \\\\[2ex]
    &= \cfrac{16p^2}{ \sin^2 2\theta } \\\\[2ex]
\end{aligned}
$$

故当且仅当$ \theta = \cfrac{\pi}{4} $时，$  \left| AB \right| \cdot \left| CD \right| $取得最小值为$ 16p^2 $



{{<myhr >}}

{{</myhr>}}



## 三角函数

---

🟦证明：$f(x) = \ln x + \cfrac{2}{\operatorname{e} x} - \operatorname{e}^{-x} > 0$

{{<alert color="danger" title="注意" >}}

- 难题

{{</alert>}}


$ x_1 x_2 = \cfrac{p^2}{4} $

$ y_1 y_2 = -p^2 $

