---
title: 正弦定理
description: 正弦定理
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



在三角形中，勾股定理是余弦定理的特例


## 核心方程与不等式

（1）正弦方程：$ \cfrac{a}{\sin A} = \cfrac{b}{\sin B} = \cfrac{c}{\sin C} = 2R $

（2）余弦方程：$ \cos C = \cfrac{ a^2 + b^2 - c^2 }{ 2ab } $

（3）面积方程：$ S_{ \triangle ABC} = \cfrac{1}{2} a \cdot h = \cfrac{1}{2} ab \sin C  = \cfrac{abc}{4R} = \cfrac{1}{2}r(a+b+c) $

（4）角平分线方程充要条件：$ \cfrac{AB}{AC}  = \cfrac{BD}{CD} $

（5）中线长方程：

  - $AB^2 + AC^2 = 2(AD^2 + BD^2)$
  - $\overrightarrow{AB} + \overrightarrow{AC} = 2\overrightarrow{AD}			$

（6）重心方程：$\overrightarrow{BA} + \overrightarrow{BC} = 3\overrightarrow{BG} $

（7）角方程：$A + B + C = \pi$，故平均值为60度，三角形中最大角肯定大于等于60度，最小角肯定小于等于60度

（8）边不等式：$a+b>c$

（9）锐角不等式：$0<A,B,C<\cfrac{\pi}{2}$；直角方程：$A=\cfrac{\pi}{2}$；钝角不等式：$A>\cfrac{\pi}{2}$；

（10）锐角三角形充要条件：三个内角均满足：$(0,\pi)$；三个角的余弦方程都小于0

（11）大角对大边：$A>B>C \Longleftrightarrow a>b>c \Longleftrightarrow \sin A > \sin B > \sin C$

（12）向量方程：$\overrightarrow{AB} + \overrightarrow{BC} = \overrightarrow{AC}$


## 题型


{{<alert >}}



**正弦方程**：

（1）边转角》和差》内角和

- $(2b-c) \cos A = a \cos C$，可得：$ 2 \sin B  \cos A = \sin C \cos A + \cos C \sin A = \sin (A+C) = \sin B$


（1）边转角》和差》内角和



（1）边转角》和差》内角和



**余弦方程**：


（1）已知角和对边》均值》面积





**面积方程**：

已知$\triangle ABC$中的三个角$A、B、C$对应的三边分别为$a、b、c$，设$p = \cfrac{a+b+c}{2}$

$\begin{aligned}
S_{\triangle ABC} &= \cfrac{1}{2}ah \\\\
&= \cfrac{1}{2}ab\sin C \\\\
&= \cfrac{abc}{4R} \\\\
&= pr \\\\
&= \sqrt{p(p-a)(p-b)(p-c)}
\end{aligned}
$


- 内切圆半径最值、范围



**中线方程**：









{{</alert>}}



## 结论证明

---

&#128311;已知$\triangle ABC$中的三个角$A、B、C$对应的三边分别为$a、b、c$，证明如下结论

（1）证明：$ \cos C = \cfrac{ a^2 + b^2 - c^2 }{ 2ab } $


【**解析**】

（1）
向量法：

$\overrightarrow{AB} = \overrightarrow{CB} - \overrightarrow{CA} $

坐标法：





---

&#128311;已知$\triangle ABC$中的三个角$A、B、C$对应的三边分别为$a、b、c$，证明如下结论

（1）$A>B$是$a >b$的充要条件（大边对大角）

（2）$A>B$是$\sin A > \sin B$的充要条件（大边对大角）


【**解析**】

（1）在$\triangle ABC$中，易知：$0<A<\pi$，$0<B<\pi$

由$A>B$，可得：$\cos A < \cos B$

由余弦定理，可得：$\cfrac{ b^2 + c^2 - a^2 }{ 2bc } < \cfrac{ a^2 + c^2 - b^2 }{ 2ac }$

可得：$a >b$

（2）$A>B$，由（1）可得：$a > b$

由正弦定理，可得：$ \cfrac{a}{\sin A} = \cfrac{b}{\sin B} $

可得：$\sin A > \sin B$

---

&#128311;已知$\triangle ABC$中的三个角$A、B、C$对应的三边分别为$a、b、c$，证明如下结论

（1）若$a^2 + b^2 = c^2$，则$C$为直角

（2）若$a^2 + b^2 > c^2$，则$C$为锐角

（3）若$a^2 + b^2 < c^2$，则$C$为钝角


{{<alert >}}

余弦函数在区间$[0,\pi]$上单调递减

![](/maths_func/cos.svg)

{{</alert>}}


【**解析**】

（1）由余弦方程，可得：$\cos C = \cfrac{a^2+b^2-c^2}{2ab}$

又$a^2 + b^2 = c^2$，可得：$\cos C = 0$

故$C$为直角

（2）由余弦方程，可得：$\cos C = \cfrac{a^2+b^2-c^2}{2ab}$

又$a^2 + b^2 > c^2$，可得：$\cos C > 0$

故$C$为锐角



（3）由余弦方程，可得：$\cos C = \cfrac{a^2+b^2-c^2}{2ab}$

又$a^2 + b^2 < c^2$，可得：$\cos C < 0$

故$C$为钝角






&#128311;已知$\triangle ABC$中的三个角$A、B、C$对应的三边分别为$a、b、c$，证明

（1）$\cfrac{a}{\sin A} = \cfrac{a+b}{\sin A + \sin B} = \cfrac{a+b+c}{\sin A + \sin B + \sin C} $

（2）$\cfrac{\sin A + \sin B}{\sin C} = \cfrac{a+b}{c}  $

（3）$\cfrac{\sin^2 A + \sin^2 B}{\sin^2 C} = \cfrac{a^2 + b^2}{c^2}  $



【**解析**】

（1）由正弦方程$ \cfrac{a}{\sin A} = \cfrac{b}{\sin B} = \cfrac{c}{\sin C} = 2R $，可得：$\cfrac{a+b}{\sin A + \sin B} = \cfrac{2R\sin A + 2R\sin B}{\sin A + \sin B} = 2R $

同理，可得：$\cfrac{2R \sin A + 2R \sin B + 2R \sin C }{\sin A + \sin B + \sin C} = 2R$

故$\cfrac{a}{\sin A} = \cfrac{a+b}{\sin A + \sin B} = \cfrac{a+b+c}{\sin A + \sin B + \sin C} $

（2）由正弦方程$ \cfrac{a}{\sin A} = \cfrac{b}{\sin B} = \cfrac{c}{\sin C} = 2R $，可得：$\cfrac{a+b}{c} =\cfrac{ 2R\sin A + 2R\sin B  }{2R \sin C}  = \cfrac{\sin A + \sin B}{\sin C} $

（3）由正弦方程$ \cfrac{a}{\sin A} = \cfrac{b}{\sin B} = \cfrac{c}{\sin C} = 2R $，可得：$\cfrac{a^2+b^2}{c^2} =\cfrac{ 4R^2\sin^2 A +4R^2\sin^2 B  }{4R^2 \sin^2 C}  = \cfrac{\sin^2 A + \sin^2 B}{\sin^2 C}$


---

&#128311;已知$\triangle ABC$中的三个角$A、B、C$对应的三边分别为$a、b、c$，证明

（1）$\cfrac{\sin (A - B)}{\sin A + \sin B} = \cfrac{a-b}{c}$

（2）证明中线长定理

（3）证明角平分线定理

（4）证明海伦公式：$ S = \sqrt{p(p-a)(p-b)(p-c)} $（其中$p = \cfrac{a+b+c}{2}$）

（5）证明：$c(a\cos B -b\cos A) = a^2 - b^2$


{{<note >}}

（1）正弦方程：$ \cfrac{a}{\sin A} = \cfrac{b}{\sin B} = \cfrac{c}{\sin C} = 2R $

（2）余弦方程：$ \cos C = \cfrac{ a^2 + b^2 - c^2 }{ 2ab } $

{{</note>}}

【**解析**】

（1）根据题意，可得：

$\begin{aligned}
\cfrac{\sin (A - B)}{\sin A + \sin B} &= \cfrac{\sin A \cos B - \cos A \sin B }{\sin A + \sin B} \text{（两角和差）} \\\\
&= \cfrac{a \cos B - b\cos A  }{a + b} \text{（正弦定理）} \\\\
&= \cfrac{a \cdot \cfrac{a^2+c^2-b^2}{2ac} - b \cdot \cfrac{b^2+c^2-a^2}{2bc}  }{a + b} \text{（余弦定理）} \\\\
&= \cfrac{a-b}{c}
\end{aligned}
$


（2）两个余弦方程即可


（3）正弦方程


（4）由余弦定理，可得：$ \cos A = \cfrac{ b^2 + c^2 - a^2 }{ 2bc } $

根据题意，可得：

$\begin{aligned}
\sqrt{p(p-a)(p-b)(p-c)} &= \sqrt{\cfrac{a+b+c}{2} \cdot \cfrac{b+c-a}{2} \cdot \cfrac{a+c-b}{2} \cdot \cfrac{a+b-c}{2}} \\\\
&= \cfrac{1}{4}\sqrt{ [(b+c)^2 - a^2] \cdot [a^2 - (b-c)^2] } \\\\
&= \cfrac{1}{4}\sqrt{ (b^2+c^2 - a^2+2bc) \cdot (a^2-b^2-c^2+2bc) } \\\\
&= \cfrac{1}{4}\sqrt{ (2bc\cos A+2bc) \cdot (-2bc \cos A +2bc) } \\\\
&= \cfrac{1}{2}bc\sin A
\end{aligned}
$



（5）由余弦定理，可得：$ \cos A = \cfrac{ b^2 + c^2 - a^2 }{ 2bc } $，$ \cos B = \cfrac{ a^2 + c^2 - b^2 }{ 2ac } $

根据题意，可得：

$\begin{aligned}
c(a\cos B -b\cos A)  &= ac\cos B -bc\cos A \\\\
&= \cfrac{ a^2 + c^2 - b^2 }{ 2 } - \cfrac{ b^2 + c^2 - a^2 }{ 2 } \\\\
&= a^2 - b^2
\end{aligned}
$








