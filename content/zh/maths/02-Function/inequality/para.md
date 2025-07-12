---
title: 含参不等式求参范围
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


题目一般会给定两个变量在相同区间上，然后给定不等式恒成立。

【】我们知道若函数$f(x)$在区间$[a,b]$上单调递增，且$x_1,x_2 \in [a,b]$上，且$x_1 < x_2$

可得：$f(x_1)<f(x_2)$

【】若函数$f(x)$定义域为$ [a,b]$，$x_1,x_2 \in [a,b]$，且$ x_1 < x_2 $，不等式$ f(x_1) <f(x_2) $恒成立，则问题转化为函数$f(x)$在区间$ [a,b] $上单调递增


## 一、分离参数

---
&#128311;已知函数$ f(x)=ax^2 - a - \ln x $，其中$a \in R$，若不等式$ f(x) \ge  1-a $ 恒成立，求实数$a$的取值范围

{{<alert title="说明" >}}



{{</alert>}}

【**解析**】

原不等式可化简为：$ \forall x \in (0,+ \infty)$，$a \ge \cfrac{ \ln x +1 }{x^2}$

记$g(x) = \cfrac{ \ln x +1 }{x^2} $，$x \in (0,+ \infty)$

记$g_1(x) = g^{'}(x) = - \cfrac{ 2\ln x +1 }{x^3} $

可得：$g_1(\operatorname{e}^{-\frac{1}{2}}) = 0$

则当$0<x<\operatorname{e}^{-\frac{1}{2}}$时，$g_1(x) >0$；当$x>\operatorname{e}^{-\frac{1}{2}}$时，$g_1(x) <0$；

故$g(x)$在区间$(0,\operatorname{e}^{-\frac{1}{2}})$上单调递增，在区间$(\operatorname{e}^{-\frac{1}{2}},+ \infty)$上单调递减

可得：$g(x)_{max} = g(\operatorname{e}^{-\frac{1}{2}}) = \cfrac{\operatorname{e}}{2} $

故实数$a$的取值范围：$[\cfrac{\operatorname{e}}{2},+\infty)$

---
&#128311;已知函数$ f(x)=x^2 - a\ln x $，其中$a \in (0,+ \infty)$，若对任意$x \in (\cfrac{1}{ \operatorname{e} },+ \infty)$，$ f(x) > x^2 - \operatorname{e}^x +a $ 恒成立，求实数$a$的取值范围

{{<alert title="说明" >}}

- $\cfrac{\operatorname{e}^x (x\ln x +x -1)}{x(1+ \ln x)^2}$中的$ \cfrac{\operatorname{e}^x }{x(1+ \ln x)^2}$部分的符号确定（为正），故这部分在下一次求导中不需要研究
- 极值点为确定的数值

{{</alert>}}

【**解析**】

当$a \in (0,+ \infty)$时，原不等式可化简为：$ \forall x \in (\cfrac{1}{ \operatorname{e}},+ \infty)$，$a < \cfrac{ \operatorname{e}^x }{1+ \ln x}$

记$g(x) = \cfrac{ \operatorname{e}^x }{1+ \ln x} $，$x \in (\cfrac{1}{ \operatorname{e}},+ \infty)$

记$g_1(x) = g^{'}(x) = \cfrac{\operatorname{e}^x (x\ln x +x -1)}{x(1+ \ln x)^2}$

令$h(x) = x\ln x +x -1 $

记$g_2(x) = h(x)^{'} = 2 + \ln x  > 0$

故$h(x)$在区间$(\cfrac{1}{ \operatorname{e}},+ \infty)$上单调递增

可得：$h(1) = 0$

则当$\cfrac{1}{ \operatorname{e}}<x<1$时，$g_1(x) <0$；当$x>1$时，$g_1(x) >0$；

故$g(x)$在区间$(\cfrac{1}{ \operatorname{e}},1)$上单调递减，在区间$(1,+ \infty)$上单调递增

可得：$g(x)_{min} = g(1) = \operatorname{e} $

故实数$a$的取值范围：$(0,\operatorname{e})$

---
&#128311;若对$ \forall x \in (1,+ \infty)$，不等式$ xln x +x >a(x-1) $恒成立，求整数$a$的最大值

{{<alert title="说明" >}}



{{</alert>}}

【**解析**】

原不等式可化简为：$ \forall x \in (1,+ \infty)$，$ a < \cfrac{ x\ln x +x }{x-1} $

记$g(x) = \cfrac{ x\ln x +x }{x-1} $，$x \in (1,+ \infty)$

记$g_1(x) = g^{'}(x) = \cfrac{x - \ln x -2}{(x-1)^2}$

令$h(x) =x - \ln x -2 $

记$g_2(x) = h(x)^{'} = \cfrac{x-1}{x}  > 0$

故$h(x)$在区间$(1,+ \infty)$上单调递增

可得：$h(3) = 1 - \ln 3 <0$，$h(4) = 2 - \ln 4 > 0$

故$h(x)$在区间$(1,+ \infty)$上存在唯一零点$x_0$，可得：$h(x_0) = x_0 - \ln x_0 -2 = 0$，且$ 3< x_0 < 4$

则当$1<x<x_0$时，$g_1(x) <0$；当$x>x_0$时，$g_1(x) >0$；

故$g(x)$在区间$(\cfrac{1}{ \operatorname{e}},1)$上单调递减，在区间$(1,+ \infty)$上单调递增

可得：$g(x)_{min} = g(x_0) =  \cfrac{ x_0 \ln x_0 +x_0 }{x_0-1} = \cfrac{ x_0 (x_0 - 2)  +x_0 }{x_0-1} = x_0$

可得：$a < x_0$

又$ 3 < x_0 < 4$，故整数$a$的最大值为$3$

---
&#128311;已知函数$ f(x)=(x-1)  \operatorname{e}^x - ax^2 - f^{'}(0)x $，其中$a \in R$，若对任意$x>0$，$ f^{'}(x) \ge 1 + \ln x - x $ 恒成立，求实数$a$的取值范围

{{<alert title="说明" >}}

- 零阶单调

{{</alert>}}

【**解析**】

对$ f(x)=(x-1)  \operatorname{e}^x - ax^2 - f^{'}(0)x $两边同时求导，可得：$ f^{'}(x)=x \operatorname{e}^x - 2ax - f^{'}(0) $

令$x=0$，可得：$ f^{'}(0)=0 $

故$ f(x)=(x-1)  \operatorname{e}^x - ax^2$，可得：$ f^{'}(x)=x(\operatorname{e}^x - 2a)$

原不等式$ f^{'}(x) \ge 1 + \ln x - x $可化简为：$2a \le \cfrac{x\operatorname{e}^x -1 -\ln x +x}{x}$（其中$x>0$）

记$g(x) = \cfrac{x\operatorname{e}^x -1 -\ln x +x}{x}$，$x \in (0,+ \infty)$

记$g_1(x) = g^{'}(x) = \cfrac{x^2\operatorname{e}^x + \ln x}{x^2}$

令$h(x) = x^2\operatorname{e}^x + \ln x$

记$g_2(x) = h(x)^{'} = (x^2 + 2x) \operatorname{e}^x +\cfrac{1}{x}  > 0$

故$h(x)$在区间$(0,+ \infty)$上单调递增

可得：$h(1) = \operatorname{e} > 0$，$h(\operatorname{e}^ {-1}) = \operatorname{e}^ {\frac{1}{\operatorname{e}}-2} < 0$

故$h(x)$在区间$(0,+ \infty)$上存在唯一零点$x_0$，使得：$h(x_0) = {x_0}^2\operatorname{e}^{x_0} + \ln x_0 = 0$，$\cfrac{1}{\operatorname{e}} < x_0 < 1$

则当$0<x<x_0$时，$g_1(x) <0$；当$x>x_0$时，$g_1(x) >0$；

故$g(x)$在区间$(0,x_0)$上单调递减，在区间$(x_0,+ \infty)$上单调递增

可得：$g(x)_{min} = g(x_0) = \cfrac{x_0 \operatorname{e}^{x_0} -1 -\ln x_0 +x_0}{x_0} = $


可得：


















