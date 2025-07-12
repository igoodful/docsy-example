---
title: 数学符号
description: 数学符号
date: 2017-01-05
weight: 5
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


## 1. Markdown

| 符号      | markdown    | 作用 |
| :-------- | :---------- | :--- |
| &#11093;  | `&#11093;`  |      |
| &#128311; | `&#128311;` |      |
|           |             |      |
|           |             |      |


## 分段函数

```bash
综上，可得：$f(n)= \begin{cases} -3^{-x+2k-1}, &\text{$2k -1 \le x \le 2k - \cfrac{1}{2}$} \\\\[2ex]
-3^{-x+2k}, &\text{$ 2k -\cfrac{1}{2} \le x \le 2k $} \\\\[2ex]
3^{x-2k}, &\text{$2k \le x \le 2k + \cfrac{1}{2}$} \\\\[2ex]
3^{x-2k-1}, & \text{$2k +\cfrac{1}{2} \le x \le 2k + 1$} \end{cases}$
```

综上，可得：$f(n)= \begin{cases} -3^{-x+2k-1}, &\text{$2k -1 \le x \le 2k - \cfrac{1}{2}$} \\\\[2ex]
-3^{-x+2k}, &\text{$ 2k -\cfrac{1}{2} \le x \le 2k $} \\\\[2ex]
3^{x-2k}, &\text{$2k \le x \le 2k + \cfrac{1}{2}$} \\\\[2ex]
3^{x-2k-1}, & \text{$2k +\cfrac{1}{2} \le x \le 2k + 1$} \end{cases}$

## 连等号对齐

```bash
$\begin{aligned} f^{'}(x)  &=\lim\limits_{ \Delta x \to 0} \cfrac{ f(x + \Delta x) - f(x) }{\Delta x} \\\\
&=\lim\limits_{ \Delta x \to 0} \cfrac{ f(x + T + \Delta x) - f(x + T) }{\Delta x} \\\\
&=f^{'}(x + T)
\end{aligned}$

```

$\begin{aligned} f^{'}(x)  &=\lim\limits_{ \Delta x \to 0} \cfrac{ f(x + \Delta x) - f(x) }{\Delta x} \\\\[2ex]
&=\lim\limits_{ \Delta x \to 0} \cfrac{ f(x + T + \Delta x) - f(x + T) }{\Delta x} \\\\[2ex]
&=f^{'}(x + T)
\end{aligned}$





## 2. Latex

- https://www.cnblogs.com/dingdangsunny/p/12312966.html


项目编号

$\textcircled{1}$  `$\textcircled{1}$`

$\textcircled{2}$  `$\textcircled{2}$`


空格

有四种宽度的空格可以使用： \\,、\\;、\quad 和 \qquad，灵活使用 \text{n个空格} 也可以在任意位置实现空格。
本博客主题的$\KaTeX$ 是使用2个斜杠来转义，所以是\\;和\\,


## 希腊字母

| 字母符号  | Latex代码 | 备注   |
| :-------- | :-------- | :----- |
| $\pi$     | \pi       | 圆周率 |
| $\theta$  | \theta    |        |
| $\omega$  | \omega    |        |
| $\lambda$ | \lambda   |        |
| $\mu$     | \mu       |        |
| $\nu$     | \nu       |        |
| $\alpha$  | \alpha    |        |
| $\beta$   | \beta     |        |
| $\gamma$  | \gamma    |        |
| $\eta$    | \eta      |        |
| $\xi$     | \xi       |        |
| $\rho$    | \rho      |        |
| $\sigma$  | \sigma    |        |
| $\phi$    | \phi      |        |
| $\varphi$ | \varphi   |        |
| $\chi$    | \chi      |        |
| $\delta$    | \delta      |        |
| $\Delta$  | \Delta    |        |
| $\varepsilon$  | \varepsilon    |        |



## 运算符符号

| 字母符号                         | Latex代码                     | 备注     |
| :------------------------------- | :---------------------------- | :------- |
| $\bold m$                        | \bold m                       | 加粗     |
| $x \qquad y$                     | x \qquad y                    | 两个空格 |
| $+$                              | +                             |          |
| $-$                              | -                             |          |
| $\pm$                            | \pm                           |          |
| $\mp$                            | \mp                           |          |
| $\div$                           | \div                          |          |
| $\times$                         | \times                        |          |
| $\cdot$                          | \cdot                         |          |
| $\ast$                           | \ast                          |          |
| $\oplus$                         | \oplus                        |          |
| $\ominus$                        | \ominus                       |          |
| $\otimes$                        | \otimes                       |          |
| $\bigodot$                       | \bigodot                      |          |
| $\bigotimes$                     | \bigotimes                    |          |
| $\bigoplus$                      | \bigoplus                     |          |
| $x^{ab}$                         | x^{ab}                        |          |
| $a_{nm}$                         | a_{nm}                        |          |
| $\overline{n+m}$                 | \overline{n+m}                |          |
| $\underline{n+m}$                | \underline{n+m}               |          |
| $\underbrace{a_1+a_2}_{x}$       | \underbrace{a_1+a_2}_{x}      |          |
| $\overbrace{a_1+a_2}^{x}	$       | \overbrace{a_1+a_2}^{x}       |          |
| $\sqrt[nm]{ab}$                  | \sqrt[nm]{ab}                 |          |
| $\frac{a}{b}$                    | \frac{a}{b}                   |          |
| $\cfrac{a}{b}$                   | \cfrac{a}{b}                  |          |
| $\sum_{i=1}^{i=n}$               | \sum_{i=1}^{i=n}              |          |
| $\displaystyle\sum_{i=1}^{i=n}	$ | \displaystyle\sum_{i=1}^{i=n} |          |
| $\prod$                          | \prod                         |          |
| $f^{'}(x)$                       | f^{'}(x)                      | 求导     |
| $\partial$                       | \partial                      | 偏导     |
| $\int_{ab}^{cd}$                 | \int_{ab}^{cd}                | 积分     |
| $\nabla$                         | \nabla                        | 梯度     |
| $\in$                            | \in                           |          |
| $\notin$                         | \notin                        |          |
| $\subset$                        | \subset                       |          |
| $\subseteq$                      | \subseteq                     |          |
| $\supset$                        | \supset                       |          |
| $\supseteq$                      | \supseteq                     |          |
| $\cap$                           | \cap                          |          |
| $\bigcap$                        | \bigcap                       |          |
| $\cup$                           | \cup                          |          |
| $\bigcup$                        | \bigcup                       |          |
| $\complement_UA$                 | \complement_UA                |          |
| $\emptyset$                      | \emptyset                     | 空集     |
| $\lim\limits_{  x \to 1} f(x)$   | \lim\limits_{  x \to 1} f(x)  | 极限     |
|                                  |                               |          |
|                                  |                               |          |
|                                  |                               |          |
|                                  |                               |          |
|                                  |                               |          |
|                                  |                               |          |
|                                  |                               |          |


## 关系符号


| 字母符号     | Latex代码  | 备注   |
| :----------- | :--------- | :----- |
| $>$          | >          |        |
| $\geq$       | \geq       |        |
| $<$          | <          |        |
| $\leq$       | \leq       |        |
| $=$          | =          |        |
| $\neq$       | \neq       |        |
| $\equiv$     | \equiv     | 恒等于 |
| $\approx$    | \approx    | 约等于 |
| $\sim$       | \sim       | 相似   |
| $\cong$      | \cong      | 全等于 |
| $\neg$       | \neg       | 取反号 |
| $\ll$        | \ll        |        |
| $\gg$        | \gg        |        |
| $\equiv$     | \equiv     |        |
| $\not\equiv$ | \not\equiv |        |
|              |            |        |


## 几何



| 字母符号           | Latex代码        | 备注     |
| :----------------- | :--------------- | :------- |
| $\forall$          | \forall          | 任取     |
| $\exists$          | \exists          | 存在     |
| $\infty$           | \infty           | 无穷     |
| $\triangle$        | \triangle        |          |
| $\bigtriangleup$   | \bigtriangleup   |          |
| $\bigtriangledown$ | \bigtriangledown |          |
| $\triangleleft$    | \triangleleft    |          |
| $\triangleright$   | \triangleright   |          |
| $\bigcirc$         | \bigcirc         |          |
| $\angle$           | \angle           |          |
| $\bot$             | \bot             |          |
| $\perp$            | \perp            | 垂直     |
| $\parallel$        | \parallel        | 平行     |
| $\ldotp$           | \ldotp           | 句点     |
| $\dots$            | \dots            |          |
| $\cdots$           | \cdots           | 中三连点 |
| $\ldots$           | \ldots           | 下三连点 |
| $\vdots$           | \vdots           | 竖三连点 |
| $\ddots$           | \ddots           | 斜三连点 |
| $\circ$            | \circ            |          |
| $\bullet$          | \bullet          |          |
| $45\degree$        | 45\degree        | 度数     |
|                    |                  |          |
|                    |                  |          |



## 箭头与括号



| 字母符号              | Latex代码           | 备注 |
| :-------------------- | :------------------ | :--- |
| $\overrightarrow{AB}$ | \overrightarrow{AB} | 向量 |
| $\to$                 | \to                 | 结论 |
| $\gets$               | \gets               | 条件 |
| $\rightarrow$         | \rightarrow         |      |
| $\Rightarrow$         | \Rightarrow         |      |
| $\Longrightarrow$     | \Longrightarrow     |      |
| $\rightharpoonup$     | \rightharpoonup     |      |
| $\rightharpoondown$   | \rightharpoondown   |      |
| $\leftarrow$          | \leftarrow          |      |
| $\Leftarrow$          | \Leftarrow          |      |
| $\Longleftarrow$      | \Longleftarrow      |      |
| $\leftharpoonup$      | \leftharpoonup      |      |
| $\leftharpoondown$    | \leftharpoondown    |      |
| $\leftrightarrow$     | \leftrightarrow     |      |
| $\Leftrightarrow$     | \Leftrightarrow     |      |
| $\longleftrightarrow$ | \longleftrightarrow |      |
| $\Longleftrightarrow$ | \Longleftrightarrow |      |
| $\uparrow$            | \uparrow            |      |
| $\Uparrow$            | \Uparrow            |      |
| $\downarrow$          | \downarrow          |      |
| $\Downarrow$          | \Downarrow          |      |
| $\updownarrow$        | \updownarrow        |      |
| $\Updownarrow$        | \Updownarrow        |      |
| $\swarrow$            | \swarrow            |      |
| $\nearrow$            | \nearrow            |      |
| $\nwarrow$            | \nwarrow            |      |
| $\searrow$            | \searrow            |      |
| $\lbrace$             | \lbrace             |      |
| $\rbrace$             | \rbrace             |      |
| $\lbrack$             | \lbrack             |      |
| $\rbrack$             | \rbrack             |      |
| $\langle$             | \langle             |      |
| $\rangle$             | \rangle             |      |
| $\vert$               | \vert               |      |
| $\Vert$               | \Vert               |      |
| $\bar{A}$             | \bar                |      |
| $\widehat{A}$         | \widehat            |      |
| $\vec{a}$             | \vec                |      |
| $\dot{a}$             | \dot                |      |
| $\ddot{a}$            | \ddot               |      |

## 函数



| 字母符号              | Latex代码           | 备注           |
| :-------------------- | :------------------ | :------------- |
| $\overrightarrow{AB}$ | \overrightarrow{AB} | 向量           |
| $\lg x$               | \lg x               |                |
| $\ln x$               | \ln x               |                |
| $\log_ax$             | \log_ax             |                |
| $\sin x$              | \sin x              | 注意中间的空格 |
| $\cos x$              | \cos x              | 注意中间的空格 |
| $\tan x$              | \tan x              | 注意中间的空格 |
| $\cot x$              | \cot x              | 注意中间的空格 |
| $\arcsin x$           | \arcsin x           |                |
| $\arccos x$           | \arccos x           |                |
| $\arctan x$           | \arctan x           |                |
| $\sinh x$             | \sinh x             |                |
| $\cosh x$             | \cosh x             |                |
| $\tanh x$             | \tanh x             |                |
| $\coth x$             | \coth x             |                |




















