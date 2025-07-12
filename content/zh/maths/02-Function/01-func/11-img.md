---
title: 11. 画图
description: img
date: 2024-09-05
weight: 1100
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

**如何画一个函数的图象呢？**

一是确定函数的单调区间；

二是极限（洛必达法则）；

三是熟悉函数的图象变换（平移、伸缩、翻折）

断点和无穷大

求函数在某点或无穷大处的极限时，注意某点左右极限是否相等，正负无穷大的极限是否相等


{{</note>}}

---
&#128311;画出$f(x) = \cfrac{1}{x}$的图像


【**解析**】


函数$ f(x) $的图象大致如下：

![](/maths_func/20240929-img-004.svg)


（1）自变量趋近有限值时函数的极限：

- $ \lim\limits_{  x \to 1^-} \cfrac{1}{x} =  \lim\limits_{  x \to 1^+} \cfrac{1}{x} =  \lim\limits_{  x \to 1} \cfrac{1}{x} = f(1) = 1 $（说明：如果一个函数在某点周围连续不断，该点的极限等于函数值）

- $ \lim\limits_{  x \to 0^-} \cfrac{1}{x} = -\infty$（说明：当自变量$x$从$0$的左边无限趋近$0$时，函数$ f(x) $无限趋近负无穷大）

- $ \lim\limits_{  x \to 0^+} \cfrac{1}{x} = +\infty$（说明：当自变量$x$从$0$的右边无限趋近$0$时，函数$ f(x) $无限趋近负无穷大）


（2）自变量趋近无穷值时函数的极限：


- $ \lim\limits_{  x \to -\infty} \cfrac{1}{x} =  \lim\limits_{  x \to +\infty} \cfrac{1}{x} =  \lim\limits_{  x \to \infty} \cfrac{1}{x} = 0 $


## 渐近线


函数的渐近线是指：若在曲线 上当一动点 沿曲线无限远离坐标原点时，点与某一直线 的距离无限趋近于零，则称直线 为曲线的一条渐近线。渐近线是函数的几何性质之一





## 极限





### 洛必达法则

使用场景：$\cfrac{\infty}{\infty}$或$\cfrac{0}{0}$分式型

（1）若$ \lim\limits_{  x \to x_0} f(x) = 0 $，$ \lim\limits_{  x \to x_0} g(x) = 0 $，且均在点$x_0$的去心邻域内均可导，且$ g^{'}(x) \ne 0 $



（2）若$ \lim\limits_{  x \to \infty} $



---

$ \lim\limits_{  x \to 0} \cfrac{\sin x}{x} $






【**解析**】

$ \begin{aligned} 
\lim\limits_{  x \to 0} \cfrac{\sin x}{x} & =  \lim\limits_{  x \to 0} \cfrac{(\sin x)^{'}}{(x)^{'}} \\\\
& = \lim\limits_{  x \to 0} \cfrac{\cos x}{1} \\\\
& = 1 
\end{aligned}$





## 画图





---
&#128311;若函数$ h(x) = x^2 \text{e}^x -a $恰有一个零点，求$a$的取值范围

【**解析**】

令$ f(x) = x^2 \text{e}^x $，可得：$ f^{'}(x) = x(x+2) \text{e}^x $


则函数$ f(x) $的图象大致如下：

![](/maths_func/20240929-img-001.svg)





---
&#128311;若函数$ h(x) = \text{e}^x -ax^2  $恰两个零点，求$a$的取值范围

{{<note>}}
</br>

记$f(x) = \cfrac{\text{e}^x}{x^2}$

- $ \lim\limits_{  x \to -\infty} f(x) = 0$

- $ \lim\limits_{  x \to 0} f(x) =\lim\limits_{  x \to 0^{-}} f(x)= \lim\limits_{  x \to 0^{+} } f(x) = +\infty$


- $ \lim\limits_{  x \to +\infty} f(x) = +\infty$

{{</note>}}

【**解析**】

当$x=0$时，$h(0) = 1 \ne 0$，可得：$x \ne 0$

问题可转化为：关于$x$的方程$ \cfrac{\text{e}^x}{x^2} = a$恰有两个不同的实数根

记$f(x) = \cfrac{\text{e}^x}{x^2}$（$x \ne 0$），$g(x) = a$

记$f_1(x)=f^{'}(x) = \cfrac{(x-2) \text{e}^x}{x^3}$

可得：当$x<0$时，$f_1(x)>0$；当$0<x<2$时，$f_1(x)<0$；当x>2$时，$f_1(x)>0$

故$f(x)$的单调递增区间为$(-\infty,0)$和$[2,+\infty)$，递减区间为$(0,2)$

易知：$f(2)=\cfrac{\text{e}^2}{4}$

函数$ f(x) $的图象大致如下：

![](/maths_func/20240929-img-002.svg)


综上可得：
- 当$a\le 0$时，函数$ h(x)$没有零点；
- 当$0<a<\cfrac{\text{e}^2}{4}$时，函数$ h(x)$恰有一个零点；
- 当$a=\cfrac{\text{e}^2}{4}$时，函数$ h(x)$恰两个零点；
- 当$a>\cfrac{\text{e}^2}{4} $时，函数$ h(x)$恰三个零点

故$a$的取值范围为：$\\{\cfrac{\text{e}^2}{4}\\}$


---
&#128311;若函数$ h(x) = \text{e}^x -ax  $恰有一个零点，求$a$的取值范围

{{<note>}}
</br>

记$f(x) = \cfrac{\text{e}^x}{x}$

- $ \lim\limits_{  x \to -\infty} f(x) = 0$

- $ \lim\limits_{  x \to 0^{-}} f(x)=  -\infty$（当自变量$x$从$0$的左侧无限趋近$0$时）

- $ \lim\limits_{  x \to 0^{+} } f(x) = +\infty$（当自变量$x$从$0$的右侧无限趋近$0$时）

- $ \lim\limits_{  x \to +\infty} f(x) = +\infty$

- $ \lim\limits_{  x \to +\infty} \cfrac{\text{e}^x}{x} =   \lim\limits_{  x \to +\infty} \cfrac{\text{e}^x}{1} = +\infty$ （洛必达法则）


{{</note>}}

【**解析**】

当$x=0$时，$h(0) = 1 \ne 0$，可得：$x \ne 0$

问题可转化为：关于$x$的方程$ \cfrac{\text{e}^x}{x} = a$恰有一个实数根

记$f(x) = \cfrac{\text{e}^x}{x}$（$x \ne 0$），$g(x) = a$

记$f_1(x)=f^{'}(x) = \cfrac{(x-1) \text{e}^x}{x^2}$

可得：当$x<0$时，$f_1(x)<0$；当$0<x<1$时，$f_1(x)<0$；当x>1$时，$f_1(x)>0$

故$f(x)$的单调递增区间为$[1,+\infty)$，递减区间为$(-\infty,0)$和$(0,1)$

易知：$f(1)=\text{e}$

函数$ f(x) $的图象大致如下：

![](/maths_func/20240929-img-003.svg)


综上可得：
- 当$a< 0$时，函数$ h(x)$恰有一个零点；
- 当$0 \le a<\text{e}$时，函数$ h(x)$恰有一个零点；
- 当$a=\text{e}$时，函数$ h(x)$恰一个零点；
- 当$a>\text{e}$时，函数$ h(x)$恰两个零点

故$a$的取值范围为：$(-\infty,0) \cup \\{\text{e}\\}$



---
&#128311;画出函数$f(x) =  \cfrac{x^2}{\ln x}$的大致图像

{{<note>}}

</br>

- $ \lim\limits_{  x \to 0}  \cfrac{1}{\ln x} \cdot x^2 =  0$

- $ \lim\limits_{  x \to 1^{-}} \cfrac{1}{\ln x}=  -\infty$

- $ \lim\limits_{  x \to 1^{+}} \cfrac{1}{\ln x}=  +\infty$

- $ \lim\limits_{  x \to +\infty} \cfrac{x^2}{\ln x} = \lim\limits_{  x \to +\infty} \cfrac{2x}{\frac{1}{x}} = \lim\limits_{  x \to +\infty} 2x^2 = +\infty$ （洛必达法则）

{{</note>}}

函数$ y=\ln x $的图象大致如下：

![](/maths_func/log-timu.svg)


【**解析**】

已知：$f(x) =  \cfrac{x^2}{\ln x}$（$x > 0$，且$x \ne 1$）

记$f_1(x)=f^{'}(x) = \cfrac{x(2\ln x - 1)}{(\ln x)^2}$

可得：当$0<x<1$时，$f_1(x)<0$；当$1<x<\sqrt{\text{e}}$时，$f_1(x)<0$；当$x> \sqrt { \text{e} }$时，$f_1(x)>0$

故$f(x)$的单调递增区间为$(\sqrt { \text{e}},+\infty)$，递减区间为$(0,1)$和$(1,\sqrt { \text{e}})$

函数$ f(x) $的图象大致如下：

![](/maths_func/20240930-img-001.svg)









