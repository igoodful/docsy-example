---
title: 无参不等式-一阶
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


{{<note>}}


{{</note>}}

{{<figure src="/maths_func/no_para-one-000.svg">}}

---
&#128311;证明下列不等式：

（1）$ \ln (1+x) + \sin x - 2x \le 0 $

（2）$ \operatorname{e}^x -2x -1 + \sin x \ge 0 $


【**解析**】

（1）方法一：直接

记$f(x) =  \ln (1+x) + \sin x - 2x $，$x \in (-1,+ \infty)$

可得：$ f^{'}(x) = \cfrac{1}{x+1} + \cos x - 2 $

&#9312; 当$x \ge 0$时，可得：$ \cfrac{1}{x+1} \le 1 $，$ \cos x \le 1 $

可得：$ f^{'}(x) \le 0 $

故$f(x)$在$(0,+\infty)$上单调递减，可得：$ f(x) \le f(0) = 0 $

&#9313; 当$x \in (-1,0)$时，记$g(x) =  \cfrac{1}{x+1} + \cos x - 2 $，$x \in (-1,0)$

可得：$ g^{'}(x) = - \cfrac{1}{(x+1)^2} - \sin x < -1 - \sin x < 0 $

故$g(x)$在$(-1,0)$上单调递减

可得：$g(x) > g(0) = 0$

故$f(x)$在$(-1,0)$上单调递增，可得：$ f(x) \le f(0) = 0 $

综上&#9312;&#9313;，可得：$ \ln (1+x) + \sin x - 2x \le 0 $

{{<figure src="/maths_func/no_para-one-001.svg">}}

---

方法二：放缩正弦

易知：$ \sin x \le x $

可得：$ \ln (1+x) + \sin x - 2x \le  \ln (1+x) - x $

易知：$\ln (1+x) \le x  $

可得：$ \ln (1+x) - x \le 0 $

故$ \ln (1+x) + \sin x - 2x \le 0 $

再分别证明：$ \sin x \le x $，$\ln (1+x) \le x  $即可


{{<note >}}
- $ f^{'}(x)$不需要分式通分
- $ g^{'}(x)$不需要分式通分

{{</note>}}

---
（2）方法一：直接

记$f(x) = \operatorname{e}^x -2x -1 + \sin x  $，$x \in (- \infty,+ \infty)$

记$ f_1(x) = f^{'}(x) =  \operatorname{e}^x  + \cos x - 2 $

&#9312; 当$x \le 0$时，可得：$ \operatorname{e}^x \le 1 $，$ \cos x \le 1 $

可得：$ f_1(x) \le 0 $

故$f(x)$在$(- \infty,0)$上单调递减，可得：$ f(x) \ge f(0) = 0 $

&#9313; 当$x \in (0,+ \infty)$时，记$f_2(x) =  f_1^{'}(x) = \operatorname{e}^x  - \sin x $，$x \in (0,+ \infty) $

可得：$ \operatorname{e}^x  - \sin x > 1 - \sin x \ge 0 $

故$f_1(x)$在$(0,+ \infty)$上单调递增，可得：$f_1(x) > f_1(0) = 0$

故$f(x)$在$(0,+ \infty)$上单调递增，可得：$ f(x) \le f(0) = 0 $

综上&#9312;&#9313;，可得：$ \operatorname{e}^x -2x -1 + \sin x \ge 0 $

{{<figure src="/maths_func/no_para-one-002.svg">}}

---

方法二：放缩指数

> 错误，

易知：$ \operatorname{e}^x \ge 1 + x +\cfrac{x^2}{2} $ ，其中$x \in (0 ,+\infty)$

可得：$ \operatorname{e}^x -2x -1 + \sin x \ge 1 + x +\cfrac{x^2}{2} -2x -1 + \sin x = \cfrac{x^2}{2} -x + \sin x  $

记$f(x) = \cfrac{x^2}{2} -x + \sin x $，$x \in (- \infty,+ \infty)$

记$ f_1(x) = f^{'}(x) =  x  + \cos x - 1 $

记$ f_2(x) = f_1^{'}(x) =  1  - \sin x \ge 0$

故$f_1(x)$在$x \in (- \infty,+ \infty)$上单调递增

又$ f_1(0) = 0 $，可得：当$x \in (- \infty,0)$时，$ f_1(x) < 0 $；当$x \in (0,+ \infty)$时，$ f_1(x) >0 $；

可得：$ f(x)_{min} = f(0) = 0 $

故$ f(x) \ge 0 $，可得：$ \operatorname{e}^x -2x -1 + \sin x \ge 0 $

再证明：$ \operatorname{e}^x \ge 1 + x +\cfrac{x^2}{2} $即可，此处省略



{{<note >}}
- $ f^{'}(x)$不需要分式通分
- $ g^{'}(x)$不需要分式通分

{{</note>}}


---















