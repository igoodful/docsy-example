---
title: 放缩不等式
description: zoom
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


{{<alert>}}

1. 放缩方向保持一致

2. 预处理
   1. 因式分解
   2. 同时取对数、取指数
   3. 换元同构
   4. 分离对数

3. 寻找放缩源

4. 用在哪个阶段？直接、一阶、二阶、。。。


{{</alert>}}


{{<alert>}}

- [sysbench](https://github.com/akopytov/sysbench)

{{</alert>}}

$ \rightleftharpoons $






## 结论放缩

> 题目前面的结论



有哪些形式：
1. 恒成立不等式，可能为最值极限不等式
2. 函数最值极限
3. 函数单调性



## 最值放缩


## 对数放缩



## 指数放缩




## 三角放缩







## 切线放缩




## 单调放缩

- 对于单调函数则直接根据获得最值有界，比如常见函数。




&#128311;已知函数$f(x) = \ln x - \cfrac{1}{2}x^2$在$(0,1)$上单调递增

{{<alert >}}

- 函数$f(x)$的对称中心为$(a,b)$ $ \Longleftrightarrow $ $f(x) + f(2a-x) = 2b$

{{</alert>}}

设$x_1,x_2 \in (0,1)$，且$x_1 < x_2$，可得：$f(x_1) < f(x_2)$

可得：$ \ln {\cfrac{x_1}{x_2}} < \cfrac{1}{2} (x_1^2 - x_2^2)$

可得：$ \cfrac{x_1}{x_2} < \operatorname{e}^{\frac{1}{2} (x_1^2 - x_2^2)}$



## 最值极限放缩
- 对非单调函数则有界放缩：比如正余弦：
> 已知函数最值或极限
>
> 已知不等式
>
> 用在哪一个阶段？直接、一阶、二阶

&#128311;已知函数$ f(x) = \operatorname{e}^{x} - \sqrt{\operatorname{e}}x -\cfrac{\sqrt{\operatorname{e}}}{2} $在$ x = \cfrac{1}{2} $处取得最小值为$0$

{{<alert>}}

-

{{</alert>}}


可得：$  f(x) \ge 0$

可得：$ \operatorname{e}^{x} - \sqrt{\operatorname{e}}x -\cfrac{\sqrt{\operatorname{e}}}{2} \ge 0 $





## 案例

#### 1. 指数放缩


&#128311; 证明下面不等式

（1）$\forall_x \in (-\infty,+ \infty)$，$ \operatorname{e}^x \ge 1 + x $

（2）$\forall_x \in (-\infty,+ \infty)$，$ \operatorname{e}^x \ge \operatorname{e}x $

（3）$\forall_x \in [0,+ \infty)$，$ \operatorname{e}^x \ge 1 + x + \cfrac{1}{2}x^2 $

（4）$\forall_x \in [-\infty,0]$，$ \operatorname{e}^x \le 1 + x + \cfrac{1}{2}x^2 $



（5）$\forall_x \in (-\infty,+ \infty)$，$ \operatorname{e}^x \ge 1 + x + \cfrac{1}{2}x^2 + \cfrac{1}{6}x^3 $

{{<alert>}}

-

{{</alert>}}

（1）记


{{<figure src="/maths_func/zoom_exp_001.svg">}}

---

（2）记

{{<figure src="/maths_func/zoom_exp_002.svg">}}

---

（3）

{{<figure src="/maths_func/zoom_exp_003.svg">}}

---

（4）

{{<figure src="/maths_func/zoom_exp_004.svg">}}

---

（5）

{{<figure src="/maths_func/zoom_exp_005.svg">}}

---

#### 2. 对数放缩


&#128311; 证明下面不等式

（1）$ \ln x \le x - 1 $

（2）$ \ln x \le 2x^2 - 1 $

（3）$ \ln x \ge 1 - \cfrac{1}{x} $



{{<note >}}

-

{{</note>}}

（1）记$ f(x) = x - \ln x - 1 $，$ x \in (0,+\infty) $

可得：$ f^{'}(x) = \cfrac{x - 1}{x} $

可得：当$ 0 < x < 1 $时，$ f^{'}(x) < 0 $；当$ x > 1 $时，$ f^{'}(x) > 0 $

故$f(x) $在$(0,1)$上单调递减，在$(1,+\infty)$上单调递增

可得：$ f(x)_{min} = f(1) = 0 $

可得：$f(x) >0$

{{<figure src="/maths_func/zoom_log_001.svg">}}

---

（2）记$ f(x) = 2x^2 - \ln x - 1 $，$ x \in (0,+\infty) $

可得：$ f^{'}(x) = \cfrac{(2x - 1)(2x + 1)}{x} $

可得：当$ 0 < x < \cfrac{1}{2} $时，$ f^{'}(x) < 0 $；当$ x > \cfrac{1}{2} $时，$ f^{'}(x) > 0 $

故$f(x) $在$(0,\cfrac{1}{2})$上单调递减，在$(\cfrac{1}{2},+\infty)$上单调递增

可得：$ f(x)_{min} = f(\cfrac{1}{2}) = \ln 2 - \cfrac{1}{2} > 0 $

可得：$f(x) >0$

{{<figure src="/maths_func/zoom_log_002.svg">}}

---

（3）记$ f(x) = \ln x + \cfrac{1}{x} -1 $，$ x \in (0,+\infty) $

可得：$ f^{'}(x) = \cfrac{x - 1}{x^2} $

可得：当$ 0 < x < 1 $时，$ f^{'}(x) < 0 $；当$ x > 1 $时，$ f^{'}(x) > 0 $

故$ f(x) $在$(0,1)$上单调递减，在$(1,+\infty)$上单调递增

可得：$ f(x)_{min} = f(1) = 0 $

可得：$f(x) >0$

{{<figure src="/maths_func/zoom_log_003.svg">}}

---



#### 3. 正弦放缩

&#128311; 证明下面不等式

（1）$ \sin x < x $，$ x \in [0,+\infty) $

（2）$  \sin x > x - \cfrac{1}{6}x^3 $


{{<alert>}}

-

{{</alert>}}

（1）记$ f(x) = x - \sin x $，$ x \in [0,+\infty) $

易知：$f(x)$在$\operatorname{R}$上为奇函数

记$ f_1(x) = f^{'}(x) = 1 - \cos x  \ge 0$

故$f(x) $在$ x \in [0,+\infty)$上单调递增

可得：$f(x)_{min} = f(0) = 0 $

可得：$ f(x) \ge 0 $

（2）记$ f(x) = \sin x - x + \cfrac{1}{6}x^3 $，$ x \in [0,+\infty) $

易知：$f(x)$在$\operatorname{R}$上为奇函数

记$ f_1(x) = f^{'}(x) = \cfrac{1}{2}( x^2 + 2\cos x -2 ) $

记$ f_2(x)= f_1^{'}(x) = x - \sin x $

记$ f_3(x)= f_2^{'}(x) = 1 - \cos x \ge 0 $

故$f_2(x) $在$ x \in [0,+\infty)$上单调递增

可得：$f_2(x)_{min} = f_2(0) = 0 $

可得：$f_2(x)  \ge 0$

故$f_1(x) $在$ x \in [0,+\infty)$上单调递增

可得：$ f_1(x)_{min} =f_1(0) = 0 $

可得：$f_1(x)  \ge 0$

故$f(x) $在$ x \in [0,+\infty)$上单调递增

可得：$f(x)_{min} = f(0) = 0 $

可得：$f(x)  \ge 0$


#### 4. 余弦放缩

&#128311; 证明下面不等式

（1）$ \cos x \ge 1 - \cfrac{1}{2}x^2 $




{{<alert>}}

-

{{</alert>}}

（1）记$ f(x) = \cos x +\cfrac{1}{2}x^2 - 1 $，$ x \in (-\infty,+\infty) $

易知：$ f(x) $为偶函数，故只需分析$ x \in [0,+\infty) $

记$ f_1(x) = f^{'}(x) = x - \sin x $

记$ f_2(x)= f_1^{'}(x) = 1 - \cos x \ge 0 $

故$f_1(x) $在$ x \in [0,+\infty)$上单调递增

可得：$f_1(x)_{min} = f_1(0) = 0 $

可得：$f_1(x)  \ge 0$

故$f(x) $在$ x \in [0,+\infty)$上单调递增

可得：$ f(x)_{min} =f(0) = 0 $

可得：$ f(x) \ge 0 $

综上，可得：$ \cos x \ge 1 - \cfrac{1}{2}x^2 $
























