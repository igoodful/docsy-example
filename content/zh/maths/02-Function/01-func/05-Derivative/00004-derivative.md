---
title: 4. 求导
description: 求导
date: 2023-10-30
weight: 40
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

1. 基本求导公式
2. 四则运算
3. 复合函数：复合函数就是两个或者两个以上的函数组成的函数，我们的求导策略就是从内到外逐个击破求导
4. 隐函数
5. 同时取对数求导
6. 反求导数
7. 参数函数
8. 反函数求导

{{</note>}}








## 1. 基本求导公式


| 序号 | 公式                                                  | 说明             |
| :--- | :---------------------------------------------------- | :--------------- |
| 1    | $ \left(C \right)^{'} = 0 $                           | 常数的导数为$0$  |
| 2    | $ \left(x^n \right)^{'} = n  x^{n-1} $                | $n$为有理数      |
| 3    | $ \left(a^x \right)^{'} =  a^x  \ln a $               | $a>0$且$a \ne 1$ |
| 4    | $ \left(\log_a x \right)^{'} = \cfrac{1}{x  \ln a } $ | $a>0$且$a \ne 1$ |
| 5    | $ \left(\sin x \right)^{'} = \cos x $                 |                  |
| 6    | $ \left(\cos x \right)^{'} = - \sin x $               |                  |


## 2. 求导运算法则


| 序号 | 公式                                                                                                           | 说明 |
| :--- | :------------------------------------------------------------------------------------------------------------- | :--- |
| 1    | $ \left[f(x) + g(x) \right]^{'} = f^{'}(x) + g^{'}(x) $                                                        | 加   |
| 2    | $ \left[f(x) - g(x) \right]^{'} = f^{'}(x) - g^{'}(x) $                                                        | 减   |
| 3    | $ \left[Af(x) \right]^{'} = Af^{'}(x)$                                                                         | 数乘 |
| 4    | $ \left[f(x) \cdot g(x) \right]^{'} = f^{'}(x) g(x) + f(x) g^{'}(x) $                                          | 乘   |
| 5    | $ \left[ \cfrac{f(x)}{g(x)} \right]^{'} = \cfrac{f^{'}(x)g(x) - f(x)  \cdot g^{'}(x)}{ \left[g(x) \right]^2} $ | 除   |
| 6    | $ \left[f(g(x))\right]^{'} = f^{'} \left(g(x) \right) g^{'}(x) $                                               | 复合 |




## 3. 瞬时变化率

$ \lim\limits_{x \to x_0} {\cfrac{f(x) - f(x_0)}{x - x_0}} $


## 4. 极限



## 5. 斜率



## 6. 求导



## 证明

（1）求$ f^{'}(g(x)) $

含义是先求$ f^{'}(x) $，然后用$g(x)$替换$x$后的表达式：$ f^{'} (g(x)) = \lim\limits_{ \Delta t \to 0} \cfrac{ f(g(x + \Delta t)) - f(g(x)) }{\Delta t} $


（1）$ \left[f(g(x)) \right]^{'} = f^{'} \left( g(x) \right) g^{'}(x) $



【**解析**】

（1）证明：


$\begin{aligned} [f(g(x))]^{'}  &=\lim\limits_{ \Delta x \to 0} \cfrac{ f(g(x + \Delta x)) - f(g(x)) }{\Delta x} \\\\
&=\lim\limits_{ \Delta x \to 0} \left[ \cfrac{ f(g(x + \Delta x)) - f(g(x)) }{ g(x + \Delta x) - g(x) } \cdot \cfrac{ g(x + \Delta x) - g(x) }{\Delta x} \right] \\\\
&=\lim\limits_{ \Delta x \to 0} \cfrac{ f(g(x + \Delta x)) - f(g(x)) }{ g(x + \Delta x) - g(x) } \cdot \lim\limits_{ \Delta x \to 0} \cfrac{ g(x + \Delta x) - g(x) }{\Delta x} \\\\
&=f^{'} \left( g(x) \right) g^{'}(x)
\end{aligned}
$









---
&#128311;已知函数$f(x)$的对称中心为$(a,b)$，求$g(x)=f(x)+c$的对称中心


{{<alert title="Note" >}}

- 函数$f(x)$的对称中心为$(a,b)$ $ \Longleftrightarrow $ $f(x) + f(2a-x) = 2b$

{{</alert>}}

函数$f(x)$的对称中心为$(a,b)$，可得：$f(x) + f(2a-x) = 2b$

已知：$g(x)=f(x)+c$

可得：$g(2a-x)=f(2a-x)+c$

可得：$g(x) + g(2a-x) = f(x)+c + f(2a-x)+c = f(x) + f(2a-x) +2c = 2(b+c)$

故$g(x)c$的对称中心为$(a,b+c)$




## 求导例题

已知函数$f(x)$，$f_1(x) = f^{'}(x)$，$f_2(x) = f_1^{'}(x)$，$f_3(x) = f_2^{'}(x)$，$\cdots$，$f_n(x) = f_{n-1}^{'}(x)$


---
&#128311;求下列函数的$n$阶导数

（1）$f(x) = \sin x$

$ f_1(x) =  \cos x$，$ f_2(x) = - \sin x $，$ f_3(x) = - \cos x $，$ f_4(x) = \sin x $

$ f_n(x) =  $

（2）$f(x) = \cos x$

$ f_1(x) =  - \sin x$，$ f_2(x) = - \cos x $，$ f_3(x) = \sin x $，$ f_4(x) = \cos x $

$ f_n(x) = \cos (x + \cfrac{n\pi}{2}) $

（3）$f(x) = A\sin x + B\cos x$

$ f_1(x) =  A\cos x  - B\sin x$

$ f_2(x) =  -A\sin x  - B\cos x $

$ f_3(x) = -A\cos x  + B\sin x $

$ f_4(x) =  A\sin x  + B\cos x $

$ f_n(x) =  $

（4）$f(x) = \text{e}^x $

$ f_n(x) = \text{e}^x $

（5）$f(x) = \text{e}^{-x} $

$ f_1(x) =  - \text{e}^{-x} $，$ f_2(x) = \text{e}^{-x} $，可得：$ f_n(x) = (-1)^n \text{e}^{-x} $

（1）$f(x) = 2024 $

（2）$f(x) = x^N $，求$f_{N}(x)$，$f_{N+1}(x)$



---

&#128311;求下列函数的$n$阶导数

（1）$f(x) = \text{e}^x \sin x$

（2）$f(x) = \cos x$

（3）$f(x) = A\sin x + B\cos x $



（4）$f(x) = x \cos x $



（5）$f(x) = \cfrac{x}{\text{e}^x } $

$ f_1(x) = \cfrac{1 - x}{\text{e}^x } $

$ f_2(x) = \cfrac{x - 2}{\text{e}^x } $

$ f_3(x) = \cfrac{3 - x}{\text{e}^x } $

$ f_4(x) = \cfrac{x - 4}{\text{e}^x } $

$ f_n(x) = \cfrac{(-1)^n (x-n)}{\text{e}^x } $


（6）$f(x) =  \text{e}^x (\sin x + \cos x) $

周期为8

（7）$f(x) = \sin x + \cos x + x^{2024} $，求$f_{2025}(x)  $

（8）$f(x) = \cos x +  \text{e}^{-x} + x^{2024} $，求$f_{2025}(x)  $

周期为4


$ f_1(x) =  \text{e}^x (\cos x - \sin x) $

$ f_2(x) = -2\text{e}^x \sin x $

$ f_3(x) = -2\text{e}^x (\cos x - \sin x) $

$ f_4(x) = -2^2\text{e}^x \cos x $

$ f_5(x) = -2^2\text{e}^x (\cos x - \sin x) $

$ f_6(x) = 2^3\text{e}^x \sin x $

$ f_n(x) =  $


（9）$ f(x) = x \sin x $，求$f_{2019}(x) + f_{2021}(x) =\sin x$

写出前5项



---

&#128311;求下列函数的导数

{{<note >}}

1. 复杂函数导数

2. $ y = f(x)^{g(x)} $

先两边同时取自然对数，可得：$ \ln y = g(x) \ln f(x) $

再两边同时对自变量$x$求导，可得：$ \cfrac{y^{'}}{y} = g^{'}(x) \ln f(x) + \cfrac{g(x)}{f(x)} f^{'}(x) $

可得：$ y^{'} = \left[g^{'}(x) \ln f(x) + \cfrac{g(x)}{f(x)} f^{'}(x) \right] f(x)^{g(x)} $

{{</note>}}



（1）$ y = \cfrac{\cos x}{\sqrt{x}} $


（2）$ y = 5x^{10}\sin x - 2\sqrt{x} \cos x - 10 $


（2）$ y = 2x^{2}\sin x  + 2\cfrac{\ln x}{\sqrt{x}} - 4\text{e}^{2x} \cos x - 5 $

（2）$ y = \cfrac{ \text{e}^{2x} \sin(2x )+ x^2 \ln(3x)  }{\sqrt{2x} \log_{a}{2x}- 2^{3x}\sin(4x) } $

（3）$ y = (x+1)^{\frac{1}{x+1}} $（$x>0$）

同时取对数再求导：$  y^{'} = [1 - \ln (x+1)] \cdot (x+1)^{\frac{1}{x+1} - 2} $

递减区间为：$x > \text{e} - 1$


（3）$ y = x^{\frac{1}{x}} $（$x>0$）

递减区间为：$x > \text{e}$，递增区间为$(0,\text{e})$




---

&#128311;【】求下列函数的导数

（1）$ y=x^n \cdot f(x)   \qquad\qquad （2） y=a^{nx}  \cdot  f(x)  $

（3）$ y= \sin x  \cdot f(x)  \qquad\quad （4） y= \cfrac{f(x)}{ \sin x}  $

（5）$ y= \cos x  \cdot f(x)  \qquad\quad （6） y= \cfrac{f(x)}{ \cos x}  $

（7）$ y= \ln x \cdot f(x)  \qquad\qquad （8） y= \ln f(x)  $

$\qquad\qquad$

【解析】

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




#### 复核函数求导例题



---

&#128311;【】求下列函数的导数

（1）$ y = (x+1)^{\frac{1}{x+1}} $（$x>0$）

（2）$ y = \left[ f(x) \right]^{ g(x) } $

【解析】（1）两边同时取对数，可得：$ \ln y = \cfrac{1}{x+1} \ln (x+1) $

两边同时求导，可得：$ \cfrac{1}{y} \cdot y^{'} = -\cfrac{1}{(x+1)^2} \ln (x+1) + \cfrac{1}{(x+1)^2} $

可得：$ y^{'} = \left[ 1 - \ln (x+1) \right] \cdot (x+1)^{ \frac{1}{x+1} - 2 } $

（2）两边同时取对数，可得：$ \ln y = g(x) \cdot \ln f(x) $

两边同时求导，可得：$ \cfrac{1}{y} \cdot y^{'} = g^{'}(x) \cdot \ln f(x) +  g(x) \cdot \cfrac{ 1  }{ f(x) }  f^{'}(x) $

可得：$ y^{'} = \left[ f(x) \right]^{ g(x) } \cdot \left[ g^{'}(x) \ln f(x) +  g(x)  \cfrac{ f^{'}(x)  }{ f(x) }   \right]   $

















