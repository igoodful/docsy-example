---
title: 已知通项，加强不等式
description: >
  A short lead description about this content page. It can be **bold** or _italic_ and can be split over multiple paragraphs.
date: 2017-01-05
weight: 5


---

{{< alert >}}

根据数学归纳法的思考过程可以确定$g(n)$应满足的条件，$g(n)$的式子结构是由$a_n$决定的，利用待定系数法便可求出合理的$g(n)$，这样的$g(n)$往往不唯一,但系数有范围限制

- 若$a_n$是多项式，则$g(n)$是多项式；

- 若$a_n$是指数结构，则$g(n)$是指数结构；

- 若$a_n$是阶乘结构，则$g(n)$是阶乘结构；








{{< /alert >}}


### 一、幂次型

【】证明：对$n \in \text{N}_+$，$n > 2$，$\cfrac{1}{2^2}+\cfrac{1}{3^2}+\cdots+\cfrac{1}{n^2} < 1$

放缩裂项：$\cfrac{1}{n^2} < \cfrac{1}{n(n-1)} = \cfrac{1}{n-1} - \cfrac{1}{n}$

加强表达式为：$\cfrac{1}{2^2}+\cfrac{1}{3^2}+\cdots+\cfrac{1}{n^2} < 1- \cfrac{1}{n}$

加强不等式设成：$\cfrac{1}{4} - \cfrac{1}{xn + y}$

加强不等式设成：$\cfrac{1}{4} - \cfrac{1}{xn}$

【】证明：$\cfrac{1}{9}+\cfrac{1}{25}+\cdots+\cfrac{1}{(2n+1)^2} < \cfrac{1}{4}$

放缩裂项：$\cfrac{1}{(2n+1)^2}=\cfrac{1}{4n^2+4n+1} < \cfrac{1}{4}\left(\cfrac{1}{n} - \cfrac{1}{n+1}\right)$

加强表达式为：$\cfrac{1}{9}+\cfrac{1}{25}+\cdots+\cfrac{1}{(2n+1)^2} < \cfrac{n}{4(n+1)}$

加强不等式不能设成：$\cfrac{1}{4} - \cfrac{1}{x \cdot n^2}$，因为这会导致$x \le \cfrac{kn+b}{n^2}$，即$x=0$

加强不等式设成：$\cfrac{1}{4} - \cfrac{1}{xn + y}$

加强不等式不能设成：$\cfrac{1}{4} - \cfrac{1}{x \cdot n}$，因为这会不成立

加强不等式设成：$\cfrac{1}{4} - \cfrac{1}{x(2n+1)}$，此时$\cfrac{12}{5} \le x \le \cfrac{8}{3}$

### 二、指数型

【】证明：$\cfrac{1}{2-1}+\cfrac{1}{2^2-1}+\cfrac{1}{2^3-1}+\cdots+\cfrac{1}{2^n-1} < \cfrac{5}{3}$

通项：$a_n=\cfrac{1}{2^n-1}$

放缩等比：

加强表达式为：$\cfrac{1}{2-1}+\cfrac{1}{2^2-1}+\cfrac{1}{2^3-1}+\cdots+\cfrac{1}{2^n-1} < \cfrac{5}{3} - \cfrac{8}{7\cdot 2^n}$

验证：$b_n=f(n)-f(n-1)$，$a_n < b_n$（$n>1$）

加强表达式为：$\cfrac{1}{2-1}+\cfrac{1}{2^2-1}+\cfrac{1}{2^3-1}+\cdots+\cfrac{1}{2^n-1} < \cfrac{5}{3} - \cfrac{4}{3\cdot 2^n}$

验证：$b_n=f(n)-f(n-1)$，$a_n < b_n$（$n>1$）

- 如何得到加强不等式？

设加强不等式为：$\cfrac{1}{2-1}+\cfrac{1}{2^2-1}+\cfrac{1}{2^3-1}+\cdots+\cfrac{1}{2^n-1} < \cfrac{5}{3} - \cfrac{1}{x\cdot 2^n} = f(n)$（$x>0$）

通项：$a_n=\cfrac{1}{2^n-1}$

通项：$b_n=f(n)-f(n-1)=\left(\cfrac{5}{3} - \cfrac{1}{x\cdot 2^n}\right) - \left(\cfrac{5}{3} - \cfrac{1}{x\cdot 2^{n-1}}\right)$



通项分析，可得：$\begin{cases}
a_n \le b_n &(n \ge 2) \cr
a_1 \le f(1) &(n = 1) \cr
\end{cases}$ 恒成立

解得：$\begin{cases}
x \le 1 - \cfrac{1}{2^n} &(n \ge 2) \cr
x \ge \cfrac{3}{4} \cr
\end{cases}$

通用加强不等式：

设加强不等式为：$\cfrac{1}{2-1}+\cfrac{1}{2^2-1}+\cfrac{1}{2^3-1}+\cdots + \cfrac{1}{2^n-1}< \cfrac{5}{3} - \cfrac{1}{g(n)} = f(n)$

通项分析，可得：$\begin{cases}
a_{n+1} = \cfrac{1}{2^{n+1}-1}  \le b_{n+1} = \cfrac{1}{g(n)} - \cfrac{1}{g(n+1)} \cr
a_1 = \cfrac{1}{2-1}  \le f(1) = \cfrac{5}{3} - \cfrac{1}{g(1)} \cr
\end{cases}$ 恒成立

观察通项分析的不等式结构，可设：$g(n) = x \cdot 2^n$

解得：$\cfrac{3}{4} \le x < 1$

归纳法：
### 三、幂指型

【】证明：$\cfrac{1}{2 + 1}+\cfrac{2}{2^2 + 2}+\cdots+\cfrac{n}{2^n + n} < 2$

放缩错位相减：$\cfrac{n}{2^n + n} < \cfrac{n}{2^n}$

{{< alert >}}
问题：幂指型，加强不等式不好求，以及加强不等式结构都不知道
{{< /alert >}}




### 四、阶乘型

【】证明：$1+\cfrac{1}{2!}+\cfrac{1}{3!}+\cdots+\cfrac{1}{n!} < \cfrac{9}{5}$（$n > 1$）

加强表达式为：$1+\cfrac{1}{2!}+\cfrac{1}{3!}+\cdots+\cfrac{1}{n!} < \cfrac{9}{5} - \cfrac{1}{2\cdot n!}$

设加强不等式为：$1+\cfrac{1}{2!}+\cfrac{1}{3!}+\cdots+\cfrac{1}{n!} < \cfrac{9}{5} -  \cfrac{1}{g(n)} = f(n)$（$n > 1$）

通项分析，可得：$\begin{cases}
a_{n+1} = \cfrac{1}{(n+1)!} \le b_{n+1} = \cfrac{1}{g(n)} - \cfrac{1}{g(n+1)} (n > 1)）\cr
1+\cfrac{1}{2!} = \cfrac{1}{2-1}  \le f(2) = \cfrac{9}{5} -  \cfrac{1}{g(2)} \cr
\end{cases}$ 恒成立

观察通项分析的不等式结构，可设：$g(n) = x \cdot n!$

解得：$\cfrac{5}{3} \le x \le 2$

不妨取$x = 2$


【】证明：$1+\cfrac{1}{2!}+\cfrac{1}{3!}+\cdots+\cfrac{1}{n!} < 2$（$n \ge 1$）

加强表达式为：$1+\cfrac{1}{2!}+\cfrac{1}{3!}+\cdots+\cfrac{1}{n!} < 2 - \cfrac{1}{n}$

### 指数

【】证明：$(1-\cfrac{1}{3})(1-\cfrac{1}{3^2}) \cdots (1-\cfrac{1}{3^n}) > \cfrac{1}{2}$

设加强不等式为：$(1-\cfrac{1}{3})(1-\cfrac{1}{3^2}) \cdots (1-\cfrac{1}{3^n}) > \cfrac{1}{2} + \cfrac{1}{3^{n+1}}$




### 根式

【】证明：$1 + \cfrac{1}{\sqrt{2}} + \cfrac{1}{\sqrt{3}}+ \cdots + \cfrac{1}{\sqrt{n}} > 2(\sqrt{n} - 1)$

1. 通项分析无效

2. 直接归纳法无效


设加强不等式为：$1 + \cfrac{1}{\sqrt{2}} + \cfrac{1}{\sqrt{3}}+ \cdots + \cfrac{1}{\sqrt{n}} > 2(\sqrt{n + 1} - 1)$ 可通项分析













{{< alert >}}This is an alert.{{< /alert >}}
{{< alert title="Note" >}}This is an alert with a title.{{< /alert >}}
{{% alert title="Note" %}}This is an alert with a title and **Markdown**.{{% /alert %}}
{{< alert color="success" >}}This is a successful alert.{{< /alert >}}
{{< alert color="warning" >}}This is a warning.{{< /alert >}}
{{< alert color="warning" title="Warning" >}}This is a warning with a title.{{< /alert >}}


