---
title: 6. 单调
description: monotone
date: 2024-09-08
weight: 30000
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;

$ \rightleftharpoons $


{{<note >}}


{{</note>}}







---
&#128311;【】设函数$ f_{n}(x) = 1+x+\cfrac{1}{2!}x^2  +\cfrac{1}{3!}x^3+ \cdots + \cfrac{1}{n!}x^n ,n\in \operatorname{N_{+}} $


（1）证明：$f_{3}(x)$在$\operatorname{R}$上单调递增，且有唯一零点；

（2）已知$f_{2n-1}(x)$在$\operatorname{R}$上单调递增且有唯一零点，求$f_{2n}(x)$的零点个数

（3）证明：$f_{2k}(x)$在$\operatorname{R}$上为单调递增

（4）证明：$f_{2k}(x)$在$\operatorname{R}$上为单调递增



【**解析**】

（1）$ f_{3}(x) = 1+x+\cfrac{1}{2}x^2  +\cfrac{1}{6}x^3 $，其中$x \in \operatorname{R}$

可得：$ f^{'}_{3}(x) = 1+x+\cfrac{1}{2}x^2 >0 $

故$f_{3}(x)$在$\operatorname{R}$上单调递增

又$ f_{3}(-2) = -\cfrac{1}{3}<0,f_{3}(0) = 1>0  $，由零点定理，可得：$f_{3}(x)$在$(-2,0)$上有唯一零点

故$f_{3}(x)$在$\operatorname{R}$上单调递增，且有唯一零点


（2）









---
&#128311;已知函数$f(x)$的对称中心为$(a,b)$，求$g(x)=f(x)+c$的对称中心


{{<alert  >}}

- 函数$f(x)$的对称中心为$(a,b)$ $ \Longleftrightarrow $ $f(x) + f(2a-x) = 2b$

{{</alert>}}

函数$f(x)$的对称中心为$(a,b)$，可得：$f(x) + f(2a-x) = 2b$

已知：$g(x)=f(x)+c$

可得：$g(2a-x)=f(2a-x)+c$

可得：$g(x) + g(2a-x) = f(x)+c + f(2a-x)+c = f(x) + f(2a-x) +2c = 2(b+c)$

故$g(x)c$的对称中心为$(a,b+c)$


