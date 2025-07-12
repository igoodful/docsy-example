---
title: 三角换元
description: 三角换元
date: 2025-06-21
weight: 2000
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;





---

&#128311;已知$a^2+b^2=c^2$，求$ \cfrac{ a^3 +b^3 + c^3  }{abc} $的取值范围


{{<alert color="primary">}}

【**思路**】

（1）三角换元

（2）多元函数最值

{{</alert>}}


【**解析**】 记$ a=c\cos \theta,b=c\sin \theta $，其中$ \theta \in (0,\cfrac{\pi}{2})$，可得：

$\begin{aligned}
\cfrac{ a^3 +b^3 + c^3  }{abc} &=  \cfrac{ \cos^3 \theta +\sin^3 \theta +1 }{ \cos \theta \sin \theta } \\\\[2ex]
    &= \cfrac{ (\cos \theta + \sin \theta)(1- \cos \theta \sin \theta) +1 }{ \cos \theta \sin \theta } \\\\
\end{aligned}
$

记$ t = \cos \theta + \sin \theta $，两边同时平方，可得：$ \cos \theta \sin \theta = \cfrac{t^2-1}{2} $

可得：$ \cfrac{ a^3 +b^3 + c^3  }{abc} = \cfrac{2}{t-1} -t $

记$ f(t) = \cfrac{2}{t-1} -t $

又$ t = \cos \theta + \sin \theta $，$ \theta \in (0,\cfrac{\pi}{2})$，可得：$ t = \cos \theta + \sin \theta = \sqrt{2} \sin (\theta + \cfrac{\pi}{4}) \in (1,\sqrt{2}] $

易知：$ f(t) $在区间$ (1,\sqrt{2}] $上单调递减

可得：$ f(t) \ge f(\sqrt{2}) = 2 + \sqrt{2} $

故$ \cfrac{ a^3 +b^3 + c^3  }{abc} $的取值范围为：$ [2 + \sqrt{2},+\infty) $







