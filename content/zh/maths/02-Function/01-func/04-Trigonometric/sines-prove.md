---
title: 正弦余弦面积相关证明
description: 正弦余弦面积相关证明，不需要记忆
date: 2024-09-12
weight: 2000
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;

$ \rightleftharpoons $


## 核心方程与不等式




---

&#128311;已知$\triangle ABC$中的三个角$A、B、C$对应的三边分别为$a、b、c$，证明如下结论

（1）$ a>b \Longleftrightarrow \cos A < \cos B $

（2）$ a>b \Longleftrightarrow \cos 2A < \cos 2B $




【**解析**】

（1）$ a>b $，可得：$ A>B $

函数$y=\cos x$在区间$(0,\pi)$上单调递减，可得：$\cos A < \cos B$

（2）$  \cos 2A < \cos 2B \Longleftrightarrow  1-2\sin^2 A < 1-2\sin^2 B \Longleftrightarrow  \sin^2 A > \sin^2 B$

易知：$ a>b \Longleftrightarrow  \sin A> \sin B >0$




---

&#128311;已知$\triangle ABC$中的三个角$A、B、C$对应的三边分别为$a、b、c$，证明如下结论

（1）若$a^2+b^2<c^2$，则$\triangle ABC$为钝角三角形

（2）若$a^3+b^3=c^3$，则$\triangle ABC$为锐角三角形

（3）若$c^2 < ab$，则$0 < C < \cfrac{\pi}{3}$

（4）若$(a^2+b^2)c^2  < 2a^2b^2$，则$0 < C < \cfrac{\pi}{3}$

（5）若$2ab>(a+b)c$，则$0 < C < \cfrac{\pi}{3}$

（6）若三边$a,b,c$的倒数成等差数列，则$0 < B \le \cfrac{\pi}{3}$

【**解析**】

（1）由题意，可知：$a^2+b^2<c^2$

由余弦定理，可得：$\cos C = \cfrac{ a^2 + b^2 - c^2 }{ 2ab } < 0$

函数$y=\cos x$在区间$(0,\pi)$上单调递减，可得：$C > \cfrac{\pi}{2}$

故$\triangle ABC$为钝角三角形

（2）由$a^3+b^3=c^3$，可知：$C$为最大角

由余弦定理，可得：

$\begin{aligned}
\cos C &= \cfrac{ a^2 + b^2 - c^2 }{ 2ab } \\\\
&= \cfrac{ a^2c + b^2c - c^3 }{ 2abc } \\\\
&= \cfrac{ a^2c + b^2c - (a^3+b^3) }{ 2abc } \\\\
&= \cfrac{ a^2(c-a) + b^2(c-b) }{ 2abc } > 0 \\\\
\end{aligned}$

故$\triangle ABC$为锐角三角形

（3）由题意，可知：$c^2 < ab$

由余弦定理，可得：

$\begin{aligned}
\cos C &= \cfrac{ a^2 + b^2 - c^2 }{ 2ab } \\\\
&\ge \cfrac{ 2ab - c^2 }{ 2ab } \\\\
&> \cfrac{ 2ab - ab }{ 2ab } \\\\
&= \cfrac{ 1}{ 2 }  \\\\
\end{aligned}$

函数$y=\cos x$在区间$(0,\pi)$上单调递减，可得：$0 < C < \cfrac{\pi}{3}$

（4）$(a^2+b^2)c^2  < 2a^2b^2$，可得：

$\begin{aligned}
c^2 &< \cfrac{ 2a^2b^2 }{ a^2 + b^2 } \\\\
&\le \cfrac{ 2a^2b^2 }{ 2ab }  \\\\
&= ab \\\\
\end{aligned}$

由（3），可得：$0 < C < \cfrac{\pi}{3}$

（5）由$2ab>(a+b)c$，可得：$c < \cfrac{2ab}{a+b}$

由基本不等式可得：$c < \cfrac{2ab}{a+b} < \cfrac{2ab}{2\sqrt{ab}} = \sqrt{ab}$

可得：$c^2 < ab$

由（3），可得：$0 < C < \cfrac{\pi}{3}$

（6）三边$a,b,c$的倒数成等差数列，可得：$\cfrac{2}{b} = \cfrac{1}{a} + \cfrac{1}{c}$

可得：$b = \cfrac{2ac}{a+c} \le \cfrac{2ac}{2\sqrt{ac}} = \sqrt{ac} $（当且仅当$a=c$时，等式成立）

由余弦定理可得：$\cos B = \cfrac{ a^2 + c^2 - b^2 }{ 2ac } \ge \cfrac{2ac-b^2}{2ac} \ge \cfrac{2ac -ac}{2ac}=\cfrac{1}{2} $（当且仅当$a=c$时，等式成立）

函数$y=\cos x$在区间$(0,\pi)$上单调递减，可得：$0 < C \le \cfrac{\pi}{3}$



---

&#128311;已知$\triangle ABC$中的三个角$A、B、C$对应的三边分别为$a、b、c$，证明如下结论

（1）$  \cfrac{ a-b\cos C }{ c-b\cos A } = \cfrac{\sin C}{\sin A} $




【**解析**】

（1）由正弦定理，可得：$\cfrac{\sin C}{\sin A} = \cfrac{c}{a} $

由余弦定理，可得：$\cos A = \cfrac{ b^2 + c^2 - a^2 }{ 2bc } $，$\cos C = \cfrac{ a^2 + b^2 - c^2 }{ 2ab } $

可得：$ \cfrac{ a-b\cos C }{ c-b\cos A } = \cfrac{ a - b \cdot \cfrac{ a^2 + b^2 - c^2 }{ 2ab } }{ c - b \cdot \cfrac{ b^2 + c^2 - a^2 }{ 2bc } } = \cfrac{c}{a} $

故$  \cfrac{ a-b\cos C }{ c-b\cos A } = \cfrac{c}{a} $




## 题型



很多OBS Studio新人用户在添加好源后发现画面不是超出屏幕就是太小了留有黑边，不知道如何去调整画面比例，下面就来教教大家如何在OBS上调整画面比例。




