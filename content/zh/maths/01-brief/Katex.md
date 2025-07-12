---
title: Katex
date: 2023-09-30
weight: 100
description: Katex 指南

---
{{< alert >}}
 LaTeX是一种高质量的排版系统
{{< /alert >}}


# 一. 配置项目：hugo.toml

```toml
# igoodful
[params.katex]
enable = true
html_dom_element = "document.body"
[params.katex.options]
throwOnError = false
errorColor = "#CD5C5C"
# 配置公式分隔符：display为true表示行间公式，为false表示行内公式
[[params.katex.options.delimiters]]
# 将 $$ 配置为行间公式，这样与typora中一致。否则，typora中的公式拷贝到这里将会失效
left = "$$"
right = "$$"
display = true
[[params.katex.options.delimiters]]
# 将 $$ 配置为行内公式，这样与typora中一致。否则，typora中的公式拷贝到这里将会失效
left = "$"
right = "$"
display = false

[[params.katex.options.delimiters]]
# 将 \\(\\) 配置为行内公式，
left = "\\("
right = "\\)"
display = false
[[params.katex.options.delimiters]]
# 将 \\[\\] 配置为行间公式
left = "\\["
right = "\\]"
display = true
[params.katex.mhchem]
enable = true
```

填空题的空白

$ a=\underline{ \hspace{1cm} }$

```bash
$ a=\underline{ \hspace{1cm} }$

```


波浪号：$ \sim $
```bash
$ \sim $
```

约等于：$ \approx $
```bash
$ \approx $
```


##  二. 希腊字母

> $ \alpha $ &emsp;&emsp; &emsp; &emsp; \alpha
>
> $ \beta $ &emsp;&emsp; &emsp; &emsp; \beta
>
> $ \gamma $ &emsp;&emsp; &emsp; &emsp; \gamma
>
> $ \lambda $ &emsp;&emsp; &emsp; &emsp; \lambda
>
> $ \mu $ &emsp;&emsp; &emsp; &emsp; \mu
>
> $ \nu $ &emsp;&emsp; &emsp; &emsp; \nu
>
> $ \pi $ &emsp;&emsp; &emsp; &emsp; \pi
>
> $ \theta $ &emsp;&emsp; &emsp; &emsp; \theta
>
> $ \eta $ &emsp;&emsp; &emsp; &emsp; \eta
>
> $ \omega $ &emsp;&emsp; &emsp; &emsp; \omega
>
> $ \sigma $ &emsp;&emsp; &emsp; &emsp; \sigma
>
> $ \rho $ &emsp;&emsp; &emsp; &emsp; \rho
>
> $ \phi $ &emsp;&emsp; &emsp; &emsp; \phi
>
> $ \varphi $ &emsp;&emsp; &emsp; &emsp; \varphi
>
> $ \chi $ &emsp;&emsp; &emsp; &emsp; \chi
>
> $\mathring{U}(x_0,\delta)$ 去心邻域`\mathring{U}(x_0,\delta)`



# 函数

> $ \sin $ &emsp;&emsp;&emsp;&emsp; \sin
>
> $ \cos $ &emsp;&emsp;&emsp;&emsp; \cos
>
> $ \tan $ &emsp;&emsp;&emsp;&emsp; \tan
>
> $ \cot $ &emsp;&emsp;&emsp;&emsp; \cot
>
> $ \sec $ &emsp;&emsp;&emsp;&emsp; \sec
>
> $ \csc $ &emsp;&emsp;&emsp;&emsp; \csc
>
> $ \arcsin $ &emsp;&emsp;&emsp;&emsp; \arcsin
>
> $ \ln $ &emsp;&emsp;&emsp;&emsp;&emsp; \ln
>
> $ \log_2x $ &emsp;&emsp;&emsp; \log_2x
>
> $ \lg(x) $ &emsp;&emsp;&emsp; \lg(x)
>

# 运算符

> $ + $ &emsp;&emsp;&emsp;&emsp; +
>
> $ - $ &emsp;&emsp;&emsp;&emsp; -
>
> $ \pm $ &emsp;&emsp;&emsp;&emsp; \pm
>
> $ \times $ &emsp;&emsp;&emsp;&emsp; \times
>
> $ \cdot $ &emsp;&emsp;&emsp;&emsp; \cdot
>
> $ \div $ &emsp;&emsp;&emsp;&emsp; \div
>
> $ = $ &emsp;&emsp;&emsp;&emsp; =
>
> $ \neq $ &emsp;&emsp;&emsp;&emsp; \neq
>
> $ > $ &emsp;&emsp;&emsp;&emsp; >
>
> $ \geq $ &emsp;&emsp;&emsp;&emsp; \geq
>
> $ < $ &emsp;&emsp;&emsp;&emsp; <
>
> $ \leq $ &emsp;&emsp;&emsp;&emsp; \leq
>
> $ \oplus $ &emsp;&emsp;&emsp;&emsp; \oplus
>
> $ \ominus $ &emsp;&emsp;&emsp;&emsp; \ominus
>
> $ \otimes $ &emsp;&emsp;&emsp;&emsp; \otimes
>
> $ \odot $ &emsp;&emsp;&emsp;&emsp; \odot
>




# 常见符号

## 省略号
数学公式中常见的省略号有两种：
- \ldots 表示与 文本底线 对齐的省略号
- \cdots 表示与 文本中线 对齐的省略号。

> $f(x_1,x_2  \ldots x_n) = x_1^2 + x_2^2 + \cdots + x_n^2 $ &emsp;&emsp;&emsp;&emsp;&emsp;&emsp; f(x_1,x_2  \ldots x_n) = x_1^2 + x_2^2 + \cdots + x_n^2
>

## 求导
> $f^{'}(x)$ &emsp;&emsp;&emsp;&emsp;&emsp;&emsp; f^{'}(x)
## 上下标
> $a_n$ &emsp;&emsp;&emsp;&emsp;&emsp;&emsp; a_n
>
> $a_{n+1}$ &emsp;&emsp;&emsp;&emsp;&emsp; a_{n+1}
>
> $2^x$ &emsp;&emsp;&emsp;&emsp;&emsp;&emsp; 2^x
>
> $2^{x+1}$ &emsp;&emsp;&emsp;&emsp;&emsp; 2^{x+1}
>
>$S_{n-1}^{n+1}$ &emsp;&emsp;&emsp;&emsp;&emsp; S_{n-1}^{n+1}

## 向量
> $\overrightarrow{ABC}$ &emsp;&emsp;&emsp;&emsp; \overrightarrow{ABC}
>
> $\overrightarrow{a}$ &emsp;&emsp;&emsp;&emsp;&emsp;&emsp; \overrightarrow{a}
>
> $ \vec{a} \cdot \vec{b}=0 $ &emsp;&emsp;&emsp; \vec{a} \cdot \vec{b}=0
>
> $\overrightarrow{AB}=\boldsymbol{a},\overrightarrow{AC}=\boldsymbol{b},\overrightarrow{OA}=\boldsymbol{c}$ &emsp;&emsp;&emsp;&emsp; \overrightarrow{AB}=\boldsymbol{a},\overrightarrow{AC}=\boldsymbol{b},\overrightarrow{OA}=\boldsymbol{c}
>
> $\overrightarrow{AB}=\overrightarrow{a},\overrightarrow{AC}=\overrightarrow{b},\overrightarrow{OA}=\overrightarrow{c}$ &emsp;&emsp;&emsp;&emsp; \overrightarrow{AB}=\overrightarrow{a},\overrightarrow{AC}=\overrightarrow{b},\overrightarrow{OA}=\overrightarrow{c}
## 根号
> $\sqrt{3x-1}$ &emsp;&emsp;&emsp;&emsp;&emsp; \sqrt{3x-1}
>
> $\sqrt[5]{2y^5-4}$ &emsp;&emsp;&emsp;&emsp; \sqrt[5]{2y^5-4}

## 阶乘
> $10!$ &emsp;&emsp;&emsp;&emsp; 10!
>

## 分数
这是因为数学公式有两种排版方式，就像 Word 中“环绕”、“四周”、“浮于文字”一样。一种是“显示”（Display），表示的是公式单独占一行；
另一种是“内嵌”（Inline），表示的是公式与其它文字混排。“内嵌”型，LaTeX 为了分式不影响正文行的高度，强行把分式压扁。
如果不需要 LaTeX 做这样的调整，需要用 \cfrac{}{} 指令代替 \frac{}{}
> $\rm A\it_n^m = \frac{n!}{m!(n-m)!}$ &emsp;&emsp;&emsp;&emsp; \rm A\it_n^m = \frac{n!}{m!(n-m)!}
>
> $f(x)=\frac{P(x)}{Q(x)}$ &emsp;&emsp;&emsp;&emsp;&emsp; f(x)=\frac{P(x)}{Q(x)}
>
> $ \cfrac{a+b}{c+d}$ &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp; \cfrac{a+b}{c+d}$

## 极限
> $\lim\limits_{x \to \inf} \rm e\it (-x) = 0$ &emsp;&emsp;&emsp;&emsp; \lim\limits_{x \to \infty} \rm e\it (-x) = 0

## 求和
#### 1. 最佳求和书写：加\limits
```sql
行内求和：$\sum\limits_{i=1}^{i=n} a_i = a_1 + a_2 + \cdots + a_n = S_n$
```
行内求和：$\sum\limits_{i=1}^{i=n} a_i = a_1 + a_2 + \cdots + a_n = S_n$

#### 2. 不合适的求和书写：不加\limits
```sql
行内求和：$\sum_{i=1}^{i=n} a_i = a_1 + a_2 + \cdots + a_n = S_n$
```
行内求和：$\sum_{i=1}^{i=n} a_i = a_1 + a_2 + \cdots + a_n = S_n$

单行与行内的区别
> $\sum_{k=1}^n k$ &emsp;&emsp;&emsp;&emsp; \sum_{k=1}^n k
> $$\sum_{k=1}^n k$$

## 求积
单行与行内的区别
> $\prod_{k=1}^n k$ &emsp;&emsp;&emsp;&emsp; \prod_{k=1}^n k
> $$\prod_{k=1}^n k$$


## 分段函数与方程组
这是非常常见的大括号环境。同样地，其使用 & 进行对齐
1. 使用条件表达式组 \begin{cases} … \end{cases} 来实现相同效果
$x = \begin{cases}
   a+b-c &\text{if  } b \cr
   b+c &\text{if  } d \cr
   x+y^2 &\text{if  } d \cr
   \sin(x) &\text{if  } d
\end{cases}
$

```bash
# 代码如下：
$x = \begin{cases}
   a+b-c &\text{if  } b \cr
   b+c &\text{if  } d \cr
   x+y^2 &\text{if  } d \cr
   \sin(x) &\text{if  } d
\end{cases}
$
```
2. 对符号{的转义
$
\left\\{
    \begin{array}{c}
        a_1x+b_1y+c_1z &=d_1 \\\\
        a_2x+b_2y+c_2z &=d_2 \\\\
        a_3x+b_3y+c_3z &=d_3 \\\\
    \end{array}
\right.
$

```bash
# 代码如下：
$
\left\\{
    \begin{array}{c}
        a_1x+b_1y+c_1z &=d_1 \\\\
        a_2x+b_2y+c_2z &=d_2 \\\\
        a_3x+b_3y+c_3z &=d_3 \\\\
    \end{array}
\right.
$
```

## 对齐
换行用4个斜线，即 \\\\ 表示，前后用 \begin{aligned}  {公式} \end{aligned} 包裹起来可以让换行后的公式自动对齐。

&符号锚定要对齐的位置。比如，按等号对齐，就在 = 前加 &，这样 LaTeX 就知道这两行的公式要在等号处对齐

人们经常想要一列等号对齐且居中的方程式序列,使用 \begin{align}…\end{align} 来创造一列方程式
> 请注意 {align} 语句是自动编号的，使用 {aligned} 声明不自动编号

$
\begin{aligned}\sin2\theta &=2\sin\theta\cos\theta \\\\
&=\frac{2\tan\theta}{1+\tan^2\theta}
\end{aligned}
$

$\begin{aligned}
    a &= b + c \\\\
    &= d + e + f \\\\
    &= g + h + i + j
\end{aligned}
$

```bash
# 代码如下：
$
\begin{aligned}\sin2\theta &=2\sin\theta\cos\theta \\\\
&=\frac{2\tan\theta}{1+\tan^2\theta}
\end{aligned}
$

$\begin{aligned}
    a &= b + c \\\\
    &= d + e + f \\\\
    &= g + h + i + j
\end{aligned}
$
```
$
\begin{aligned}
    \sqrt{37} & = \sqrt{\frac{73^2-1}{12^2}} \\\\
              & = \sqrt{\frac{73^2}{12^2}\cdot\frac{73^2-1}{73^2}} \\\\
              & = \sqrt{\frac{73^2}{12^2}}\sqrt{\frac{73^2-1}{73^2}} \\\\
              & = \frac{73}{12}\sqrt{1-\frac{1}{73^2}} \\\\
              & \approx \frac{73}{12}\left(1-\frac{1}{2\cdot73^2}\right) \\\\
\end{aligned}
$
```bash
# 代码如下：
\begin{aligned}
    \sqrt{37} & = \sqrt{\frac{73^2-1}{12^2}} \\\\
              & = \sqrt{\frac{73^2}{12^2}\cdot\frac{73^2-1}{73^2}} \\\\
              & = \sqrt{\frac{73^2}{12^2}}\sqrt{\frac{73^2-1}{73^2}} \\\\
              & = \frac{73}{12}\sqrt{1-\frac{1}{73^2}} \\\\
              & \approx \frac{73}{12}\left(1-\frac{1}{2\cdot73^2}\right) \\\\
\end{aligned}

```
默认右对齐：

$
\begin{aligned}
v + w = 0 \\\\
-w  = -w + 0 \\\\
-w + 0 = -w + (v + w)
\end{aligned}
$

```bash
# 代码如下：
$
\begin{aligned}
v + w = 0 \\\\
-w  = -w + 0 \\\\
-w + 0 = -w + (v + w)
\end{aligned}
$
```

## 适配行高：看着更舒服
1. 不适配2倍行高[2ex]：

$
f(n) =
    \begin{cases}
        \frac{n}{2}, & \text{if $n$ is even} \\\\
        3n+1,        & \text{if $n$ is odd} \\\\
    \end{cases}
$
```bash
# 代码如下：
$
f(n) =
    \begin{cases}
        \frac{n}{2}, & \text{if $n$ is even} \\\\
        3n+1,        & \text{if $n$ is odd} \\\\
    \end{cases}
$
```

2. 适配2倍行高[2ex]：

$
f(n) =
    \begin{cases}
        \frac{n}{2}, & \text{if $n$ is even} \\\\[2ex]
        3n+1,        & \text{if $n$ is odd} \\\\
    \end{cases}
$
```bash
# 代码如下：特别注意\text{}里面的 内容，如果是数学公式方面的符号则需要用$$包裹起来
$
f(n) =
    \begin{cases}
        \frac{n}{2}, & \text{if $n$ is even} \\\\[2ex]
        3n+1,        & \text{if $n$ is odd} \\\\
    \end{cases}
$
```

## 数组与表格
通常，一个格式化后的表格比单纯的文字或排版后的文字更具有可读性

 数组和表格均以 \begin{array} 开头，并在其后定义列数及每一列的文本对齐属性，c l r 分别代表居中、左对齐及右对齐。

 若需要插入垂直分割线，在定义式中插入 | ，若要插入水平分割线，在下一行输入前插入 \hline 。

 与矩阵相似，每行元素间均须要插入 & ，每行元素以 \\ 结尾，最后以 \ end{array} 结束数组。 使用单个数组或表格时无需声明 $ 或 $$ 符号

> \begin{array}{|c|l|c|r|} ... \end{array}

$
\begin{array}{|c|l|c|r|}
    \hline
    x & \text{左对齐} & \text{居中对齐} & \text{右对齐} \\\\
    \hline
    1 & 0.24 & 1 & 125 \\\\
    \hline
    2 & -1 & 189 & -8 \\\\
    \hline
    3 & -20 & 2000 & 1+10i \\\\
    \hline
\end{array}
$




## 注释
在 \text {文字} 中仍可以使用 $公式$ 插入其它公式。

$ f(n)= \begin{cases} \frac{n}{2}, &\text{if $n$ is even} \\\\[2ex]
 3n+1, & \text{if $n$ is odd} \end{cases} $

```bash
# 代码如下：
$ f(n)= \begin{cases} frac{n}{2}, &\text{if $n$ is even} \\\\[2ex]
 3n+1, & \text{if $n$ is odd} \end{cases} $
```

## 空格
有四种宽度的空格可以使用： \\,、\\;、\quad 和 \qquad，灵活使用 \text{n个空格} 也可以在任意位置实现空格。
本博客主题的$\KaTeX$ 是使用2个斜杠来转义，所以是\\;和\\,

## 标号
只能在$$中使用，而不能在$中使用，且公式前面必须空一行

$$f(x) = a - b \tag{1}$$
```bash
# 代码如下：
$$f(x) = a - b \tag{1}$$
```

## 项目编号

```sql
$\textcircled{1}$

$\textcircled{2}$

$\textcircled{3}$

```

$\textcircled{1}$

$\textcircled{2}$

$\textcircled{3}$





# 集合
> $\forall_x$ &emsp;&emsp;&emsp;&emsp; \forall_x
>
> $\exists_x$ &emsp;&emsp;&emsp;&emsp; \exists_x
>
> $a \in A$ &emsp;&emsp;&emsp;&emsp; a \in A
>
> $a \notin A$ &emsp;&emsp;&emsp;&emsp; a \notin A
>
> $A \subset B$ &emsp;&emsp;&emsp;&emsp; A \subset B
>
> $A \subseteq B$ &emsp;&emsp;&emsp;&emsp; A \subseteq B
>
> $A \nsubseteq B$ &emsp;&emsp;&emsp;&emsp; A \nsubseteq B
>
> $A \cap B$ &emsp;&emsp;&emsp;&emsp; A \cap B
>
> $A \cup B$ &emsp;&emsp;&emsp;&emsp; A \cup B
>
> $A \bigcap B$ &emsp;&emsp;&emsp;&emsp; A \bigcap B
>
> $A \bigcup B$ &emsp;&emsp;&emsp;&emsp; A \bigcup B
>
> $\complement_AB$ &emsp;&emsp;&emsp;&emsp; \complement_AB
>
> $BC\not\subset$ &emsp;&emsp;&emsp;&emsp; BC\not\subset
>
> $\text{e}^x$ &emsp;&emsp;&emsp;&emsp; \text{e}^x
>
> $\lvert x+1 \rvert$ &emsp;&emsp;&emsp;&emsp; \lvert x+1 \rvert
>
> $$

# 几何

> $\angle$ &emsp;&emsp;&emsp;&emsp; \angle
>
> $\bot$ &emsp;&emsp;&emsp;&emsp; \bot
>
> $a \perp b$ &emsp;&emsp;&emsp;&emsp; a \perp b
>
> $\cdots$ &emsp;&emsp;&emsp;&emsp; \cdots
>
> $a \parallel / \kern{-0.3em}/ b$ &emsp;&emsp;&emsp;&emsp; \kern -0.3em
>
> $\bigtriangleup$ &emsp;&emsp;&emsp;&emsp; \bigtriangleup
>
> $\parallel$ &emsp;&emsp;&emsp;&emsp; \parallel
>
> $\backsim$ &emsp;&emsp;&emsp;&emsp; \backsim
>
> $\sim$ &emsp;&emsp;&emsp;&emsp; \sim
>
> $\cong$ &emsp;&emsp;&emsp;&emsp; \cong
>
> $\because$ &emsp;&emsp;&emsp;&emsp; \because
>
> $\therefore$ &emsp;&emsp;&emsp;&emsp; \therefore
>
> $\bar{x}$ &emsp;&emsp;&emsp;&emsp; \bar{x}
>
> $\hat{x}$ &emsp;&emsp;&emsp;&emsp; \hat{x}
>
> $\triangle ABC$ &emsp;&emsp;&emsp;&emsp; \triangle ABC
>
> $\Delta=\frac{b-c}{a}$ &emsp;&emsp;&emsp;&emsp; \Delta=\frac{b-c}{a}
>
> $\sin\frac{\pi}{3}=\sin60^{\omicron}=\frac{\sqrt{3}}{2}$ &emsp;&emsp;&emsp;&emsp; \sin\frac{\pi}{3}=\sin60^{\omicron}=\frac{\sqrt{3}}{2}
>
> $f^{'}(x)$ &emsp;&emsp;&emsp;&emsp; f^{'}(x)  不能写成单引号
>
> $\overbrace{1+2+\cdots+100}$ &emsp;&emsp;&emsp;&emsp; \overbrace{1+2+\cdots+100}
>
> $\underbrace{a+b+\cdots+z}$ &emsp;&emsp;&emsp;&emsp; \underbrace{a+b+\cdots+z}
>
> $\left( \frac{1}{2} \right)$ &emsp;&emsp;&emsp;&emsp; \left( \frac{1}{2} \right)
>
> $\alpha\quad\beta$ &emsp;&emsp;&emsp;&emsp; \alpha\qquad\beta
>
> $\alpha\ \beta$ &emsp;&emsp;&emsp;&emsp; \alpha\ \beta
>
## 行内公式与行间公式
1. 行内公式：小括号分别用两个反斜杠转义：
```bash
格式：\\(公式\\)
```
2. 行间公式：四个美元符号之间为公式或math
```bash
格式：$$ 公式 $$


```
The probability of getting \\(k\\) heads when flipping \\(n\\) coins is:
```math
\tag*{(1)} P(E) = {n \choose k} p^k (1-p)^{n-k}
```



# 基础

> ^ 表示上标, _ 表示下标。如果上下标的内容多于一个字符，需要用 {} 将这些内容括成一个整体。上下标可以嵌套，也可以同时使用。

$$a_n$$
$$S_n$$
$$x^3$$
$$e^x$$
$$a_{n+1}$$
$$e^{x+1}$$

行内公式：\\(f(x) = a+b\\)

行内公式：$f(x) = a+b$








# markmap

```markmap
# markmap

## Links

- <https://markmap.js.org/>
- [GitHub](https://github.com/gera2ld/markmap)

## Related

- [coc-markmap](https://github.com/gera2ld/coc-markmap)
- [gatsby-remark-markmap](https://github.com/gera2ld/gatsby-remark-markmap)

## Features

- links
- **inline** ~~text~~ *styles*
- multiline
  text
- `inline code`
-
    ```js
    console.log('code block');
    ```
- Katex - $x = {-b \pm \sqrt{b^2-4ac} \over 2a}$
```


















# 参考


- [常用数学公式排版KaTex语法总结](https://kissingfire123.github.io/2022/02/18_%E6%95%B0%E5%AD%A6%E5%85%AC%E5%BC%8Fkatex%E5%B8%B8%E7%94%A8%E8%AF%AD%E6%B3%95%E6%80%BB%E7%BB%93/)
- [LaTeX各种符号](https://snaildove.github.io/2017/08/20/LaTeX_symbols/)

