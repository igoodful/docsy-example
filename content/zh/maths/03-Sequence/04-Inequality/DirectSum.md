---
title: 通项已知，可直接求和
description: >
  通项已知，可直接求和
date: 2017-01-05
weight: 2


---

{{< alert >}}

通项已知，可直接求和

{{< /alert >}}


{{< alert >}}This is an alert.{{< /alert >}}
{{< alert title="Note" >}}This is an alert with a title.{{< /alert >}}
{{% alert title="Note" %}}This is an alert with a title and **Markdown**.{{% /alert %}}
{{< alert color="success" >}}This is a successful alert.{{< /alert >}}
{{< alert color="warning" >}}This is a warning.{{< /alert >}}
{{< alert color="warning" title="Warning" >}}This is a warning with a title.{{< /alert >}}




#### 直接裂项：等差
【】已知$a_n = \cfrac{n+1}{n+2} + \cfrac{n+2}{n+1}$，证明：$2n < S_n < 2n + \cfrac{1}{2}$

【思路】通项分析：$ \cfrac{n+1}{n+2} + \cfrac{n+2}{n+1} -2 > 0$

通项裂项：$a_n =  \cfrac{n+1}{n+2} + \cfrac{n+2}{n+1} = 2 +  \cfrac{1}{n+1} - \cfrac{1}{n+2}$

$$ \sum_{i=1}^{i=n}  \left( \cfrac{1}{i+1} - \cfrac{1}{i+2} \right)= \cfrac{1}{2} - \cfrac{1}{n-2}$$

#### 直接裂项：等差

【】证明：$\cfrac{1}{5} \le \cfrac{1}{1 \times 5} + \cfrac{1}{3 \times 7} + \cdots + \cfrac{1}{(2n-1)(2n+3)} < \cfrac{1}{3}$

【思路】通项裂项：$a_n =  \cfrac{1}{(2n-1)(2n+3)} = \cfrac{1}{4}\left( \cfrac{1}{2n-1} - \cfrac{1}{2n+3} \right)$

$$ \sum_{i=1}^{i=n}\cfrac{1}{4}\left( \cfrac{1}{2i-1} - \cfrac{1}{2i+3} \right)  = \cfrac{1}{3} - \cfrac{1}{4}\left( \cfrac{1}{2n+1} + \cfrac{1}{2n+3} \right)$$


#### 直接裂项：指数

【】已知$a_n = \cfrac{2^n}{(2^n-1)(2^{n+1}-1)}$，证明：$S_n < 1$

【思路】通项裂项：$a_n =  \cfrac{2^n}{(2^n-1)(2^{n+1}-1)} = \cfrac{1}{2^n-1} - \cfrac{1}{2^{n+1}-1}$

$$ \sum_{i=1}^{i=n}\cfrac{1}{2^i-1} - \cfrac{1}{2^{i+1}-1}  = 1 - \cfrac{1}{2^{n+1}-1}$$

#### 直接裂项：指数

【】已知$a_n = \cfrac{1}{4}\cfrac{3 \times 2^{2n-3}}{(2^{2n-1}-1)(2^{2n-3}-1)}$，证明：$S_n < 1$

【思路】通项裂项：$a_n = \cfrac{1}{4}\cfrac{3 \times 2^{2n-3}}{(2^{2n-1}-1)(2^{2n-3}-1)} = \cfrac{1}{4}\left(\cfrac{1}{2^{2n-3} -1} - \cfrac{1}{2^{2n-1} - 1}  \right)$

$ S_n = \cfrac{1}{4}\left(\cfrac{1}{2^{-1} - 1} - \cfrac{1}{2^{2n-1} - 1}  \right) < - \cfrac{1}{2}$








