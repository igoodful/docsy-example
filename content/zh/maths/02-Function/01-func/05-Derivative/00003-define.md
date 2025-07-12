---
title: 3. 导数定义
description: 导数定义
date: 2023-10-30
weight: 30
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;

$ \rightleftharpoons $



## 定义

{{<note >}}

（1）$ f^{'}(x_0) = \lim\limits_{ \Delta x \rightarrow 0 } \cfrac{ f(x_0 + \Delta x) - f(x_0) }{\Delta x} $

（2）$  f^{'}(x_0) = \lim\limits_{ x \rightarrow x_0 } \cfrac{ f(x) - f(x_0) }{x - x_0} $

（3）$ f^{'}(x) = \lim\limits_{ \Delta x \rightarrow 0 } \cfrac{ f(x + \Delta x) - f(x) }{\Delta x} $

{{</note>}}


{{<note >}}

1. $ \Delta x $包含了从$0$的左侧和右侧两种情况无限接近$0$的情况，但$ \Delta x $不能等于$0$

2. 函数在某一点的导数是一个常数，不是一个函数

3. $ f^{'}(x_0) $仅仅与$x_0$有关，而与$\Delta x$无关

4. $f^{'}(x_0)$的意义：是函数$f(x)$在$x=x_0$处的切线斜率

5. $f^{'}(x_0) = 0$中的$x_0$的意义：是函数$f(x)$的驻点

6. $\Delta x$是自变量的变化量，$f(x - \Delta x) - f(x)$表示函数值的变化量，$\cfrac{ f(x - \Delta x) - f(x) }{\Delta x}$表示函数的平均变化率，$ \lim\limits_{ \Delta x \to 0 } \cfrac{ f(x - \Delta x) - f(x) }{\Delta x} $表示对函数的平均变化率的极限


{{</note>}}


在导数的定义中，**$ \Delta x$不能等于 0**。这是因为导数本质上是描述函数在某一点附近的变化率，而这一变化率是通过**极限过程**来定义的。

### 导数的定义：
导数在某点  $x_0$  处的定义为：$ f^{'}(x_0) = \lim\limits_{ \Delta x \to 0 } \cfrac{ f(x_0 - \Delta x) - f(x_0) }{\Delta x} $

### 为什么 $ \Delta x$不能等于 0？
- 在导数的定义中，我们通过极限的过程让$ \Delta x$**趋近于 0**，即计算当$\Delta x$非常接近 0 时，$ \cfrac{ f(x_0 - \Delta x) - f(x_0) }{\Delta x} $的值。
- **如果$ \Delta x = 0$**，则分母变为 0，导致表达式变为不确定形式 $\cfrac{0}{0}$，无法直接计算。这在数学上是未定义的。
- 导数反映的是在 $x_0$ 附近一个非常小的增量$\Delta x$时，函数值 $f(x)$ 的变化率，所以$\Delta x$只能趋近 0，但不能等于 0。

### 极限的意义：
- 导数使用极限的方式，让$\Delta x$逐渐接近 0 来观察变化率，但真正的导数值是计算当$ \Delta x$非零但非常小的情况下，函数的变化率如何。因此，我们需要考察的是**接近 0** 的过程，而不是 $\Delta x$ 本身等于 0。
- 当$ \Delta x$趋于 0 时，$ \cfrac{ f(x_0 - \Delta x) - f(x_0) }{\Delta x} $的极限值就是$ f^{'}(x_0) $。

### 总结：
在导数定义中，**$ \Delta x$ 必须趋近于$0$而不是等于$0$**。等于$0$的情况会导致除以$0$的未定义情况，而趋近于$0$则是极限概念的核心。


















&#128311;



（1）$f(x) = |x| $

$f^{'}(x) = 1 $（$x \ne 0$）

![](/maths_func/func-deri-00002-define-002.svg)


（2）$f(x) = x^3 $

![](/maths_func/func-deri-00002-define-003.svg)

可见三次函数在原点处的切线位于曲线的两侧。

（3）$f(x) = \sqrt[3]{x}$

$f^{'}(x) = \cfrac{\sqrt[3]{x}}{3x}$（$x \ne 0$）

![](/maths_func/func-deri-00002-define-001.svg)

在$x=0$处存在切线$x=0$，但该点不可导

----

&#128311;用导数的定义求下列函数的导数

（1）$f(x)=\cfrac{1}{x}$

（2）



## 极限定义例题



&#128311;已知函数$ f(x) = 2x^2 - 1 $

（1）求$ f(x) $在区间$(2,2 + \Delta x)$上的增量

（2）求$ f(x) $在区间$(2,2 + \Delta x)$上的平均变化率

（3）若$ f^{'}(x_0) $，求$x_0$，并解释$x_0$及$f^{'}(x_0)$的意义

驻点，切线的斜率

（4）求函数在点$ (1,1) $处的切线方程

（5）求$ \lim\limits_{  x \to 1} \cfrac{ f(x) - 1}{x - 1 } $

（6）求$ \lim\limits_{   x \to x_0} \cfrac{ f(x) - f(x_0)}{ x - x_0} $

（7）求$ \lim\limits_{  \Delta x \to 0} \cfrac{ f(x_0 + 2\Delta x) - f(x_0)}{ -3\Delta x } $

（8）求$ \lim\limits_{  \Delta x \to 0} \cfrac{ f(x_0 + 2\Delta x) - f(x_0 - 3 \Delta x)}{ 4 \Delta x } $



{{<note >}}

1. 化简

{{</note>}}



&#128311;设函数$f(x)$在$\text{R}$上可导，

（1）化简$\lim\limits_{ t \to 1} \cfrac{ f(t) - f(1) }{t - 1}$

表示$f^{'}(1) $

（2）化简$\lim\limits_{ \Delta x \to 0} \cfrac{ f(x_0 - m\Delta x) - f(x_0) }{\Delta x}$

表示$ - m f^{'}(x_0) $

（3）化简$\lim\limits_{ k \to 0} \cfrac{ f(1 - k) - f(1) }{3k}$

表示$ - \cfrac{1}{3} f^{'}(1) $

（4）化简$\lim\limits_{ \Delta x \to 0} \cfrac{ f(x + 3\Delta x) - f(x - \Delta x) }{2\Delta x}$

表示$ 2f^{'}(x) $

（5）化简$\lim\limits_{  x \to x_0} \cfrac{ [f(x)]^2 - [f(x_0)]^2 }{x - x_0}$

表示$ 2f(x_0)f^{'}(x_0) $，就是$[f(x)]^2$在$x= x_0$的导数


【**解析**】





---

{{<note >}}

1. 已知函数

{{</note>}}



&#128311;已知函数$f(x) = \cfrac{\ln x}{\sqrt{2x}} $

（1）求$\lim\limits_{ x \to a} \cfrac{ f(x) - f(a) }{x - a}$的值

表示$y = f(x) $在$x=a$处的导数

（2）求$\lim\limits_{ \Delta x \to 0} \cfrac{ f(1 - 3\Delta x) - f(1) }{\Delta x}$的值

表示$-3f^{'}(1) $

（3）求$\lim\limits_{ h \to 0} \cfrac{ f(\frac{1}{2} + h) - f(\frac{1}{2} - h) }{2h}$的值

表示$f^{'}(\cfrac{1}{2}) = 2 + \sqrt{2} $

（4）求$\lim\limits_{ \Delta x \to 0} \cfrac{ \cos (\frac{\pi}{6} + \Delta x) - \cos \frac{\pi}{6} }{\Delta x}$的值

表示$y = f(x) = \cos x $在$x=\cfrac{\pi}{6}$处的导数

（5）求$ \lim\limits_{ \Delta x \to 0} \cfrac{ (1 + \Delta x)^2 - 1 }{\Delta x} $的值

表示$y = f(x)=x^2$在$x=1$处的导数，由导数的几何意义可知，其表示曲线$ y = f(x) $在点$(1,1)$处的切线的斜率




【**解析**】

---



&#128311;已知函数$f(x)$的导数为$f^{'}(x)$，$f(x)= 2x \cdot f(\frac{\pi}{2}) + \cos x  $

（1）求$f^{'}(\frac{\pi}{2})$

表示$f^{'}(\frac{\pi}{2}) = 1$

（2）求$\lim\limits_{ \Delta x \to 0} \cfrac{ f(\frac{\pi}{2}) - f( \frac{\pi}{2} + \Delta x) }{\Delta x}$

表示$- f^{'}(\frac{\pi}{2}) = -1 $

（3）求$\lim\limits_{ \Delta x \to 0} \cfrac{ f(\frac{\pi}{2}) - f( \frac{\pi}{2} + \Delta x) }{\Delta x}$




【**解析**】

（1）同时求导，可得：，

可得：$f^{'}(\frac{\pi}{2}) = 1$

（2）$ $


（3）$\lim\limits_{ \Delta x \to 0} \cfrac{ f(\frac{\pi}{2}) - f( \frac{\pi}{2} + \Delta x) }{\Delta x} = $



---

{{<note >}}

1. 已知导数值

{{</note>}}

&#128311;已知函数$f(x)$满足：$ f(x_0) = 2 $，$ f^{'}(x_0) = -2 $

（1）求$ \lim\limits_{ \Delta x \to 0} \cfrac{ f(x_0) - f(x_0 - 2 \Delta x) }{ -3 \Delta x }$


**法一**：洛必达法则

（2）求$ \lim\limits_{ t \to 0} \cfrac{ f(x_0 + t) - f(x_0 - 3t) }{ t }$

$ \lim\limits_{ t \to 0} \cfrac{ f(x_0 + t) - f(x_0 - 3t) }{ t } = 4f^{'}(x_0)$

**法一**：洛必达法则

（3）求$ \lim\limits_{ x \to x_0} \cfrac{ f(x) - 2 }{ x - x_0 } + 1 $

**法一**：洛必达法则

（4）求$ \lim\limits_{ x \to x_0} \cfrac{ 2 x_0 -3f(x) }{ x - x_0 } $

$ \lim\limits_{ x \to x_0} \cfrac{ 2x_0-3f(x) }{ x - x_0 } = \lim\limits_{ x \to x_0} \cfrac{ 2x_0- 6 + 6 -3f(x) }{ x_0 - 3 } = 2 - 3 \cdot \lim\limits_{ x \to 3} \cfrac{ f(x) -f(x_0) }{ x_0 - 3 } = 8 $

**法一**：洛必达法则

【**解析**】

（1）




（2）



（3）




（4）







---

{{<note >}}

1. 已知极限

{{</note>}}

&#128311;函数$ f(x) $在$x=2$处连续，且$ \lim\limits_{ \Delta x \to 0} \cfrac{ f(2 + 2\Delta x) - f(2 - 4 \Delta x) }{ 3 \Delta x } = -2$

（1）求$ f^{'}(2) $

（2）求曲线$ y = f(x) $在点$ f(2,f(2)) $处的切线的斜率

（3）求曲线$ y = f(x) $在点$ f(2,f(2)) $处的切线的倾斜角





---



&#128311;函数$ f(x) $在$\text{R}$上可导，且$ \lim\limits_{  x \to 0} \cfrac{ f(a) - f(a - 2 x) }{ 2\sqrt{3} x } = -1$

（1）求$ f^{'}(a) $

（2）求曲线$ y = f(x) $在点$ f(a,f(a)) $处的切线的斜率

（3）求曲线$ y = f(x) $在点$ f(a,f(a)) $处的切线的倾斜角

$120^$

（4）若$ \lim\limits_{  x \to 1} \cfrac{ f(x)}{x - 1 } = 2$，求$f(1)$

$f(1) = 0$






