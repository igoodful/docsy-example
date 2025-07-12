---
title: 数学归纳法
description: >
  数学归纳法是证明与自然数有关的命题的一种方法
date: 2017-01-05
weight: 2


---

{{< alert >}}

### 数学归纳法证明的步骤：

- （1）验证$n$第一个值$n_0$时，结论成立；

- （2）假设当$n=k$时，结论成立；

- （3）证明当$n=k+1$时，结论成立。



###

【】证明：$a_1 + a_2 + \cdots + a_n < C$

【解析】下面用数学归纳法证明：

$\textcircled{1}$ 当$n = 1$时，不等式成立，即$a_1 < C$

$\textcircled{2}$ 假设当$n=k$（$k \ge 3$）时，结论成立，可得：$a_1 + a_2 + \cdots + a_k < C$

$\textcircled{3}$ 则当$n=k+1$时，若放大后k项为：$(a_2 + \cdots + a_k + a_{n+1}) \le p(a_1+ a_2  \cdots + a_k)$

可得：$a_1 + a_2 + \cdots + a_k + a_{n+1} = a_1 + (a_2 + \cdots + a_k + a_{n+1})$

$< a_1 +p(a_1+ a_2  \cdots + a_k) = a_1 +pC$

只需：$ a_1 +pC \le C$

可得：$p \le 1- \cfrac{a_1}{C}$

取$p = 1- \cfrac{a_1}{C}$



$$\sum_{i=1}^{n}a_i < C$$

$$\sum_{i=1}^{n}a_i < C - \cfrac{C}{a_1} \cdot a_{n+1}$$











{{< /alert >}}

### 起始项根据需要自由选择

【】证明 ：对于一切$n \in \text{N}_+$，都有$2^n +2 > n^2$

【解析】下面用数学归纳法证明：

$\textcircled{1}$ 当$n = 1$时，不等式成立，即$2^1 +2 > 1$

当$n = 2$时，不等式成立，即$2^2 +2 > 4$

当$n = 3$时，不等式成立，即$2^3 +2 > 9$

$\textcircled{2}$ 假设当$n=k$（$k \ge 3$）时，结论成立，可得：$2^k +2 > k^2$

$\textcircled{3}$ 则当$n=k+1$时，可得：$2^{k+1} + 2 = 2 \times (2^k +2) -2$

$ > 2 \times k^2 - 2 $

只需证：$2 \times k^2 - 2 \ge (k+1)^2$（$k \ge 3$），即$ k^2 - 2k -3 \ge 0$（$k \ge 3$）

当$k \ge 3$时，不等式$ (k - 3)(k + 1) \ge 0$恒成立

则当$n=k+1$时，不等式$2^{k+1} +2 > (k+1)^2$成立，故原不等式成立。





### 例题

【】证明：$\cfrac{1}{2}+\cfrac{1}{2^2}+\cdots+\cfrac{1}{2^n} < 1$

【解析】（1）当$n=1$时，不等式成立，即$\cfrac{1}{2} < 1$；

（2）假设当$n=k$时，不等式成立，可得：$\cfrac{1}{2}+\cfrac{1}{2^2}+\cdots+\cfrac{1}{2^k} < 1$

则当$n=k+1$时，可得：$\cfrac{1}{2}+\cfrac{1}{2^2}+\cdots+\cfrac{1}{2^k}+ \cfrac{1}{2^{k+1}}=\cfrac{1}{2}+\cfrac{1}{2}\left(\cfrac{1}{2}+\cfrac{1}{2^2}+\cdots+\cfrac{1}{2^k}  \right) < \cfrac{1}{2}+\cfrac{1}{2}=1$

综上，可得：$\cfrac{1}{2}+\cfrac{1}{2^2}+\cdots+\cfrac{1}{2^n} < 1$




【】证明：$\cfrac{1}{1!}+\cfrac{1}{2!}+\cdots+\cfrac{1}{n!} < 2$

【解析】（1）当$n=1$时，原不等式成立，即$\cfrac{1}{1!} < 2$；

（2）假设当$n=k$时，不等式成立，可得：$\cfrac{1}{1!}+\cfrac{1}{2!}+\cdots+\cfrac{1}{k!} < 2$

则当$n=k+1$时，可得：$\cfrac{1}{1!}+\cfrac{1}{2!}+\cdots+\cfrac{1}{k!}+\cfrac{1}{(k+1)!} < 2$

$= \cfrac{1}{1!}+\cfrac{1}{2}\left( \cfrac{1}{1!}+\cfrac{2}{3}\times\cfrac{1}{2!}+\cfrac{2}{4}\times\cfrac{1}{3!}+\cdots+\cfrac{2}{k}\cdot\cfrac{1}{(k-1)!}+\cfrac{2}{k+1}\cdot\cfrac{1}{k!}   \right)$

$< \cfrac{1}{1!}+\cfrac{1}{2}\left( \cfrac{1}{1!}+\cfrac{1}{2!}+\cfrac{1}{3!}+\cdots+\cfrac{1}{(k-1)!}+\cfrac{1}{k!}   \right) < \cfrac{1}{1!}+\cfrac{1}{2} \times 2 = 2$

综上，可得：$\cfrac{1}{1!}+\cfrac{1}{2!}+\cdots+\cfrac{1}{n!} < 2$
> 加强命题：$\cfrac{1}{1!}+\cfrac{1}{2!}+\cdots+\cfrac{1}{n!} < 2-\cfrac{1}{n}$






## 举例说明

【】设数列$\\{ a_n \\}$满足：$a_1=1$，$a_{n+1}=a_n+\cfrac{1}{a_n}$

（1）证明：$a_n<a_{n+1}$

（2）证明：$\sqrt{2n-1} \le a_n \le \sqrt{3n-2}$

【解析】

（2）当$n=1$时，不等式成立，即$1 \le a_1 \le 1$

假设当$n=k$时，不等式成立，可得：$\sqrt{2k-1} \le a_k \le \sqrt{3k-2}$

则当$n=k+1$时，可得：$a_{k+1}=a_k+\cfrac{1}{a_k}$

可得：$\sqrt{2k-1}+\cfrac{1}{\sqrt{2k-1}} \le a_k+\cfrac{1}{a_k} \le \sqrt{3k-2}+\cfrac{1}{\sqrt{3k-2}}$



## 举例说明

【】已知数列满足：$a_1=\cfrac{\pi}{3}$，$0<a_n<\cfrac{\pi}{3}$，$\sin{a_{n+1}} \le \cfrac{1}{3}\sin{3a_n}$（$n \ge 2$）. 证明：$\sin{a_n}<\cfrac{1}{\sqrt{n}}$

【解析】





### Alerts

{{< alert >}}This is an alert.{{< /alert >}}
{{< alert title="Note" >}}This is an alert with a title.{{< /alert >}}
{{% alert title="Note" %}}This is an alert with a title and **Markdown**.{{% /alert %}}
{{< alert color="success" >}}This is a successful alert.{{< /alert >}}
{{< alert color="warning" >}}This is a warning.{{< /alert >}}
{{< alert color="warning" title="Warning" >}}This is a warning with a title.{{< /alert >}}



## 举例说明

【】设数列$\\{ a_n \\}$满足：$a_n \ge 0$，$a_1=0$，$a_{n+1}^2+a_{n+1}-1=a_n^2$

（1）证明：$a_n<a_{n+1}$

（2）证明：$S_n > n-2$

【解析】（1）根据题意，可得：$a_{n+1}^2 - a_n^2 = a_{n+1}-1$

可得：$a_{n+1} - a_n = \cfrac{a_{n+1}-1}{a_{n+1} + a_n}$

又$a_n \ge 0$，故$a_{n+1} + a_n > 0$，只需证明：$a_{n+1} < 1$

下面用数学归纳法证明：$a_{n} < 1$

- 1）当$n=1$时，不等式成立：$a_1 = 0 < 1$

- 2）假设当$n=k$时，不等式成立，可得：$a_k < 1$

- 3）则当$n=k+1$时，可得：$a_{k+1}^2+a_{k+1}-1=a_k^2 < 1$，即$a_{k+1}^2+a_{k+1}-2 < 0$

解得：$a_{k+1} < 1$

故$a_{n} < 1$，可得：$a_n<a_{n+1}$

（2）要证$S_n > n-2$，只需证明：$(1-a_1) + (1-a_2) + \cdots + (1-a_n) < 2$

下面用数学归纳法证明：$(1-a_1) + (1-a_2) + \cdots + (1-a_n) < 2$

- 1）当$n=1$时，不等式成立：$1-a_1 = 1 < 2$

- 2）假设当$n=k$时，不等式成立，可得：$(1-a_1) + (1-a_2) + \cdots + (1-a_k) < 2$

- 3）则当$n=k+1$时，可得：$(1-a_1) + (1-a_2) + \cdots + (1-a_k) + (1-a_{k+1})$

$ = (1-a_1) + \cfrac{1}{2}\left[2(1-a_2) + \cdots + 2(1-a_k) + 2(1-a_{k+1})\right]$

$ = (1-a_1) + \cfrac{1}{2}\left[2(1-a_2) + \cdots + 2(1-a_k) + 2(1-a_{k+1})\right]$

只需证明：$2(1-a_{k+1}) \le (1-a_k)$




### 举例说明
【】数列$\\{a_n\\}$满足：$a_1 \ge 3$，$a_{n+1} = a_n^2 - na_n + 1 $

（1）证明：$a_n \ge n+2$


（2）证明：$\cfrac{1}{1+a_1} + \cfrac{1}{1+a_2} + \cdots +\cfrac{1}{1+a_n} \le \cfrac{1}{2}$

【解析】（1）


（2）下面用数学归纳法证明：

$\textcircled{1}$ 当$n = 1$时，$a_1 \ge 3$，可得：$\cfrac{1}{1+a_1} < \cfrac{1}{2}$

$\textcircled{2}$ 假设当$n=k$（$k \ge 3$）时，不等式成立，可得：$\cfrac{1}{1+a_1} + \cfrac{1}{1+a_2} + \cdots +\cfrac{1}{1+a_k} \le \cfrac{1}{2}$

$\textcircled{3}$ 则当$n=k+1$时，若放大后k项为：$(a_2 + \cdots + a_k + a_{n+1}) \le p(a_1+ a_2  \cdots + a_k)$

可得：$a_1 + a_2 + \cdots + a_k + a_{n+1} = a_1 + (a_2 + \cdots + a_k + a_{n+1})$

$< a_1 +p(a_1+ a_2  \cdots + a_k) = a_1 +pC$

只需：$ a_1 +pC \le C$

可得：$p \le 1- \cfrac{a_1}{C}$

取$p = 1- \cfrac{a_1}{C}$

最佳：放缩等比：$\cfrac{1+a_n}{1+a_{n+1}} \le  \cfrac{1}{2}$













