---
title: 无参不等式-图像
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


{{</alert>}}


---
&#128311;证明下列不等式：

（1）$ x\operatorname{e}^x \ln x + \operatorname{e}^x - x > 0 $
- $ x\ln x + \cfrac{2}{\operatorname{e}} >  \cfrac{x}{\operatorname{e}^x}  $
- $ x\ln x + 1>  \cfrac{x}{\operatorname{e}^x} - \cfrac{2}{\operatorname{e}} $
- $ x\ln x + 1>  \cfrac{x}{\operatorname{e}^x}  $
- $ x\ln x + x>  \cfrac{x}{\operatorname{e}^x} - \cfrac{2}{\operatorname{e}} $


（2）$ \ln x + \cfrac{3}{4x^2} - \cfrac{1}{\operatorname{e}^x} > 0$

（3）$ x\ln x - x + 1 > \cfrac{x^2}{\operatorname{e}^x}  $

（4）$ \cfrac{\ln x}{x} < \cfrac{2}{ \operatorname{e}} - \cfrac{x}{ \operatorname{e}^x} $

（5）$\forall_x \in (1,+ \infty)$，$ \operatorname{e}^x + \ln x - \operatorname{e} > 0$

（6）$\forall_x \in (1,+ \infty)$，$ \operatorname{e}^x + \ln x - \operatorname{e} > 0$

{{<note>}}


{{</note>}}

【**解析**】

（1）方法一：原不等式可等价为：$ \ln x + \cfrac{1}{x} > \cfrac{1}{\operatorname{e}^x} $

{{<figure src="/maths_func/no_para-photo-001.svg">}}

记$f(x) = \ln x + \cfrac{1}{x} $，$g(x) = \cfrac{1}{\operatorname{e}^x} $，$x \in (0,+ \infty)$

可得：$f^{'}(x) = \cfrac{x - 1}{x^2} $

可得：当$0 < x <1$时，$f^{'}(x) < 0$；当$x >1$时，$f^{'}(x) > 0$

故$f(x)$在$(0,1)$上单调递减，在$(1,+\infty)$上单调递增

可得：$f(x)_{min} = f(1) = 1$

易知：$g(x)$在$(0,+\infty)$上单调递减

可得：$g(x) < g(0) = 1$

综上，可得：$ x\operatorname{e}^x \ln x + \operatorname{e}^x - x > 0 $

---

方法二：原不等式可等价为：$ x\ln x + 1> \cfrac{x}{\operatorname{e}^x} $

{{<figure src="/maths_func/no_para-photo-002.svg">}}

&#9312; 记$f(x) = x\ln x + 1 $，$x \in (0,+ \infty)$

可得：$f^{'}(x) = \ln x + 1 $

可得：当$0 < x <\operatorname{e}^{-1}$时，$f^{'}(x) < 0$；当$x >\operatorname{e}^{-1}$时，$f^{'}(x) > 0$

故$f(x)$在$(0,\operatorname{e}^{-1})$上单调递减，在$(\operatorname{e}^{-1},+\infty)$上单调递增

可得：$f(x)_{min} = f(\operatorname{e}^{-1}) = 1 - \operatorname{e}^{-1}$

&#9313; 记$g(x) = \cfrac{x}{\operatorname{e}^x} $，$x \in (0,+ \infty)$

可得：$g^{'}(x) = \cfrac{1-x}{\operatorname{e}^x}  $

可得：当$0 < x <1$时，$g^{'}(x) > 0$；当$x >1$时，$g^{'}(x) < 0$

故$g(x)$在$(0,1)$上单调递增，在$(1,+\infty)$上单调递减

可得：$g(x)_{max} = g(1) = \operatorname{e}^{-1}$

易知：$ f(x)_ {min} > g(x)_ {max} $

综上，可得：$ x\operatorname{e}^x \ln x + \operatorname{e}^x - x > 0 $

---

（2）**方法一**：图像

原不等式可等价为：$ x^2\ln x > \cfrac{x^2}{\operatorname{e}^x} - \cfrac{3}{4} $

{{<figure src="/maths_func/no_para-photo-003.svg">}}

&#9312; 记$f(x) = x^2\ln x $，$x \in (0,+ \infty)$

可得：$f^{'}(x) = x(2\ln x + 1) $

可得：当$0 < x < \cfrac{1}{\sqrt{\operatorname{e}}} $时，$f^{'}(x) < 0$；当$ x >\cfrac{1}{\sqrt{\operatorname{e}}} $时，$f^{'}(x) > 0$

故$f(x)$在$\left(0,\cfrac{1}{\sqrt{\operatorname{e}}}\right)$上单调递减，在$ \left(\cfrac{1}{\sqrt{\operatorname{e}}},+\infty \right)$上单调递增

可得：$f(x)_{min} = f(\cfrac{1}{\sqrt{\operatorname{e}}}) = -\cfrac{1}{2\operatorname{e}} $

&#9313; 记$g(x) = \cfrac{x^2}{\operatorname{e}^x} - \cfrac{3}{4}  $，$x \in (0,+ \infty)$

可得：$g^{'}(x) = \cfrac{x(2-x)}{\operatorname{e}^x}  $

可得：当$0 < x <2$时，$g^{'}(x) > 0$；当$x >2$时，$g^{'}(x) < 0$

故$g(x)$在$(0,2)$上单调递增，在$(2,+\infty)$上单调递减

可得：$g(x)_{max} = g(2) = \cfrac{4}{\operatorname{e}^2} - \cfrac{3}{4} $

易知：$ \cfrac{4}{\operatorname{e}^2} - \cfrac{3}{4} - (-\cfrac{1}{2\operatorname{e}}) =  \cfrac{(8-3\operatorname{e})(2+\operatorname{e})}{4\operatorname{e}^2} <0 $

可得：$ f(x)_ {min} > g(x)_ {max} $

综上，可得：$ \ln x + \cfrac{3}{4x^2} - \cfrac{1}{\operatorname{e}^x} > 0$

---

**方法二**：零阶讨论+放缩指数+放缩重要

原不等式可等价为：$ \ln x + \cfrac{3}{4x^2} -  \cfrac{1}{\operatorname{e}^x} >0 $

&#9312; 当$x\ge \operatorname{e} $时，可得：$ \ln x + \cfrac{3}{4x^2} -  \cfrac{1}{\operatorname{e}^x} > \ln x  -  \cfrac{1}{\operatorname{e}^x} \ge 1 -  \cfrac{1}{\operatorname{e}^x} >0 $

&#9313; 当$0< x < \operatorname{e} $时，由$\operatorname{e}^x \ge 1 + x$，可得：$ - \cfrac{1}{\operatorname{e}^x} > - \cfrac{1}{1 +x} $

易知：$\ln x \ge 1 - \cfrac{1}{x}$

可得：$ \ln x + \cfrac{3}{4x^2} -  \cfrac{1}{\operatorname{e}^x} > 1 - \cfrac{1}{x} + \cfrac{3}{4x^2} -  \cfrac{1}{1 +x} = \cfrac{4x^2+3}{4x^2} - \cfrac{1}{x}  -  \cfrac{1}{1 +x}$

由重要不等式，可得：$4x^2+3 \ge 4\sqrt{3}x$

可得：$ \cfrac{4x^2+3}{4x^2} - \cfrac{1}{x}  -  \cfrac{1}{1 +x} \ge  \cfrac{4\sqrt{3}x}{4x^2} - \cfrac{1}{x}  -  \cfrac{1}{1 +x} = \cfrac{(\sqrt{3} - 2)x + \sqrt{3} - 1}{x(x+1)} > 0 $

综上，可得：$ \ln x + \cfrac{3}{4x^2} - \cfrac{1}{\operatorname{e}^x} > 0$

再分别证明：$\ln x \ge 1 - \cfrac{1}{x}$，$\operatorname{e}^x \ge 1 + x$，限于篇幅，此处省略证明







