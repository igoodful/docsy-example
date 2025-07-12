---
title: 9. 关于函数与其导函数不等式
description: 关于函数与其导函数不等式
date: 2024-09-08
weight: 60000
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

- 同时求导
- 公式（右边能求出原函数）

{{</note>}}





---
&#128311;【001】定义在$(0,+\infty)$上的函数$f(x)$满足$ xf^{'}(x) = 1+x $，且$ f(1)=2 $，不等式$ f(x) \ge (a+1)x+1 $有解，求正实数a的取值范围

{{<alert title="思路" >}}

- 根据导数求原函数
- 点在曲线方程
- 不等式有解》分离参数

{{</alert>}}


【解析】根据题意，可知：$ f^{'}(x) = 1+\cfrac{1}{x} $

可得：$ f(x) =  x + \ln x + C  $

由$ f(1)=2 $，可得：$  C  = 1  $

可得：$ f(x) =  x + \ln x + 1 $

原不等式可等价为：$   a \le \cfrac{ \ln x}{x} $（其中$ x>0,a>0 $）

记$ g(x) = \cfrac{ \ln x}{x} $，$x>0$

可得：$ g^{'}(x) = \cfrac{ 1 - \ln x}{x^2} $

当$ 0<x<\operatorname{e} $时，$ g^{'}(x) > 0 $，则$ g(x) $在$ (0,\operatorname{e}) $上单调递增；

当$ x>\operatorname{e} $时，$ g^{'}(x) < 0 $，则$ g(x) $在$ (\operatorname{e},+\infty) $上单调递减

可得：$ g(x)_{max} = g(\operatorname{e}) =  \cfrac{ 1 }{\operatorname{e} } $

故正实数a的取值范围为：$\left(0, \cfrac{ 1 }{\operatorname{e} }\right]$


---
&#128311;【001】设函数$f(x)$是定义在$\operatorname{R}$上的可导函数，其导函数为$ f^{'}(x) $，且$ f(2)=\sqrt{2} $，$ 2f(x)f^{'}(x) > \cfrac{3}{10} $，求不等式$ \left[ f(x) \right]^2 < \cfrac{3}{10}x + \cfrac{7}{5} $的解集

{{<alert title="思路" >}}

- 复合函数求导
- 点在曲线方程

{{</alert>}}


【解析】由 $ 2f(x)f^{'}(x) > \cfrac{3}{10} $，可得：$ \left[ \left[ f(x) \right]^2 - \cfrac{3}{10} x \right]^{'}  > 0 $

记$ F(x) =  \left[ f(x) \right]^2 - \cfrac{3}{10}x - \cfrac{7}{5} $

可得：$ F^{'}(x) = 2f(x)f^{'}(x) - \cfrac{3}{10} >0 $

故$ F(x) $在$\operatorname{R}$上单调递增

又$ f(2)=\sqrt{2} $，可得：$ F(2) = \left[ f(2) \right]^2 - \cfrac{3}{10} \times 2 - \cfrac{7}{5} = 0 $

故不等式$ \left[ f(x) \right]^2 < \cfrac{3}{10}x + \cfrac{7}{5} $的解集为：$ (-\infty,2) $


---











| 序号         | 原函数                                             | 导函数                                                    |
| :----------- | :------------------------------------------------- | :-------------------------------------------------------- |
| （1）$\quad$ | $ \left[ x^n \cdot f(x) \right]^{'} \qquad\qquad $ | $  \left[ xf^{'}(x) + nf(x) \right] x^{n-1} $             |
| （2）        | $  a^{nx}  \cdot  f(x) $                           | $  \left[ f^{'}(x) + n \ln a \cdot f(x) \right] a^{nx}  $ |
| （3）        | $ \sin x  \cdot f(x) $                             | $  \sin x f^{'}(x) + \cos x f(x)   $                      |
| （4）        | $ \cfrac{f(x)}{ \sin x} $                          | $  \cfrac{ \sin x f^{'}(x) - \cos x f(x) }{\sin^2 x} $    |
| （5）        | $ \cos x  \cdot f(x) $                             | $  \cos x f^{'}(x) - \sin x f(x) $                        |
| （6）        | $ \cfrac{f(x)}{ \cos x} $                          | $  \cfrac{ \cos x f^{'}(x) + \sin x f(x) }{\cos^2 x} $    |
| （7）        | $ \ln x \cdot f(x) $                               | $  \ln x f^{'}(x) + \cfrac{f(x)}{x} $                     |
| （8）        | $ \ln f(x) $                                       | $  \cfrac{ f^{'}(x) }{f(x)} $                             |

注意区别：$  \left[ xf^{'}(x) + nf(x) \right] x^{n-1} $  和$  \left[ f^{'}(x) + n \ln a \cdot f(x) \right] a^{nx}  $

这两个模型是考试频率最高的，占$90\\%$以上，其中（1）~（6）是在考试中经常会遇到，但还无法达到胜任，下面给出通用的解决方案，并举例说明：

若题目中同时含有$f^{'}(x)$与$f(x)$的不等式是恒成立，这时候往往需要咱们构造出函数，那么如何才能$100\\%$地求出构造函数呢？而不是凭经验，有时候考经验是完全没法求出来的，如何一劳永逸地解决这个问题呢？这个时候，出题者往往将公因式给去掉了，造成难以发现原函数，去掉的因式越复杂越难找



---
&#128311;【】若关于$x$的不等式$ m(x) f^{'}(x) + n(x) f(x) > 0 $恒成立


{{<alert title="Note" >}}

- 构建一个导函数方程
- 不要记忆该公式，因为难以记住，但需要记住如何做的！！！

{{</alert>}}


【解析】我们假设原不等式去掉了因式$g(x)$，因此补上来即可

故原不等式等价为：$ g(x) m(x) f^{'}(x) + g(x) n(x) f(x) > 0 $

可得：$ \left[ g(x) m(x) \right]^{'} =  g(x) n(x)  $

可得：$  g^{'}(x) m(x) + g(x) m^{'}(x)  =  g(x) n(x)  $

可得：$  \cfrac{ g^{'}(x) }{ g(x) }   =  \cfrac{ n(x) - m^{'}(x)  }{ m(x) } $

可得：$   \left[ \ln g(x) \right]^{'}  =  \cfrac{ n(x) - m^{'}(x)  }{ m(x) } $




---
&#128311;【】已知函数$f(x)$是定义在$(0,+\infty)$上的可导函数，不等式$ f^{'}(x) + (2x+1) f(x) > 0 $恒成立，证明：$f(1) < \operatorname{e}^4 f(2) $


{{<alert title="思路" >}}

- 构建一个导函数方程

当$x>0$时，不妨设$g(x) >0$，则原不等式等价为：$ g(x) f^{'}(x) +  g(x)(2x+1)f(x) > 0 $

可得：$ \left[ g(x) \right]^{'} =  g(x)(2x+1)  $

可得：$   \left[ \ln g(x) \right]^{'}  =  2x+1 $

可得：$ \ln g(x) = x^2 +x +k $

方程两边同时取指数，可得：$ g(x) = \operatorname{e}^{x^2 +x} $ （不妨取$k=0$）


{{</alert>}}


【解析】设$h(x) = \operatorname{e}^{x^2 +x} f(x) $，可得：$ h^{'} (x) = \operatorname{e}^{x^2 +x} \left[f^{'}(x) + (2x+1) f(x)  \right] $

由题意，可知：$  h^{'} (x) > 0  $

故$h(x)$在$(0,+\infty)$上单调递增

可得：$  h(1) < h(2)  $，即$f(1) < \operatorname{e}^4 f(2) $


---
&#128311;【】已知函数$f(x)$是定义在$\operatorname{R}$上的可导奇函数，且对任意的正数$x$，都有$ 2f(x) + xf^{'}(x) - x f(x) > 0 $恒成立，求$f(x)$在$\operatorname{R}$上的的零点个数


{{<alert title="思路" >}}

- 构建一个导函数方程

当$x>0$时，不妨设$g(x) >0$，则原不等式等价为：$ g(x)xf^{'}(x) + g(x)(2-x) f(x) >0 $

可得：$ \left[ x g(x) \right]^{'} =  g(x)(2-x)  $

可得：$   \left[ \ln g(x) \right]^{'}  = \cfrac{1}{x} - 1 $

可得：$ \ln g(x) =\ln x -x +k $

方程两边同时取指数，可得：$ g(x) = \cfrac{x}{ \operatorname{e}^{x} }  $ （不妨取$k=0$）


{{</alert>}}


【解析】$f(x)$是定义在$\operatorname{R}$上的奇函数，可得：$ f(0) = 0 $

设$h(x) = \cfrac{x}{ \operatorname{e}^{x} }  f(x) $，可得：$ h^{'} (x) = \cfrac{x}{ \operatorname{e}^{x} } \left[ 2f(x) + xf^{'}(x) - x f(x) \right] $

由题意，当$x>0$时，可得：$  h^{'} (x) > 0  $

故$h(x)$在$(0,+\infty)$上单调递增

当$x>0$时，可得：$ h(x)= \cfrac{x}{ \operatorname{e}^{x} }  f(x)> h(0) = 0$

又$f(x)$是奇函数，故$f(x)$是在$(-\infty,0)$和$(0,+\infty)$上无零点

综上，$f(x)$在$\operatorname{R}$上的的零点个数为$1$


---
&#128311;【】已知函数$f(x)$是定义在$\operatorname{R}$上的可导奇函数，且$ f(2) = 0 $，当$x>0$，不等式$ xf^{'}(x) - f(x) > 0 $恒成立

（1）证明：$ \pi f(\operatorname{e}) < \operatorname{e} f(\pi)  \qquad \qquad \qquad \qquad \qquad $ （2）证明：$ f(2^{15}) < f(3^{15})  $

（3）求不等式$ xf(x) \ge 0  $的解集 $\qquad \qquad \qquad \qquad \quad$ （4）求不等式$ \cfrac{f(x)}{x} \le 0 $的解集



{{<alert title="思路" >}}

- 构建一个导函数方程

当$x>0$时，不妨设$g(x) >0$，则原不等式等价为：$ g(x)xf^{'}(x) - g(x)f(x) > 0 $

可得：$ \left[ x g(x) \right]^{'} = - g(x)  $

可得：$   \left[ \ln g(x) \right]^{'}  = -\cfrac{2}{x} $

可得：$ \ln g(x) =-2\ln x +k $

方程两边同时取指数，可得：$ g(x) = \cfrac{1}{ x^2 }  $ （不妨取$k=0$）

注意设的函数是$ h(x) = g(x) \cdot xf(x)  $，而不是$ h(x) = g(x)f(x)  $


{{</alert>}}


【解析】设$h(x) = \cfrac{1}{ x }  f(x) $，可得：$ h^{'} (x) = \cfrac{1}{ x^2 } \left[ xf^{'}(x) - f(x) \right] $

又$f(x)$是$\operatorname{R}$上的奇函数，可得：$ f(0) = 0 $

当$x>0$时，$ xf^{'}(x) - f(x) > 0 $，可得：$  h^{'} (x) > 0  $

故$h(x)$在区间$(0,+\infty)$上单调递增，在区间$(-\infty,0)$上单调递减

$ f(2) = 0 $，可得：$h(2) = \cfrac{f(2)}{2} = 0 $

当$x>0$时，可得：$ h(x)= \cfrac{1}{ x }  f(x)  $  （注意这里$h(0) $不一定为$0$）


（1）$h(x)$在区间$(0,+\infty)$上单调递增，可得：$ h(\operatorname{e}) < h(\pi) $，即$ \cfrac{f(\operatorname{e})}{ \operatorname{e} } < \cfrac{f(\pi)}{\pi} $

可得：$ \pi f(\operatorname{e}) < \operatorname{e} f(\pi) $
















