---
title: 加强命题
description: >
  A short lead description about this content page. It can be **bold** or _italic_ and can be split over multiple paragraphs.
date: 2017-01-05
weight: 2


---

{{< alert >}}
第$k$项归纳假设是作为条件来使用的

{{< /alert >}}


> There should be no margin above this first sentence.
>
> Blockquotes should be a lighter gray with a border along the left side in the secondary color.
>
> There should be no margin below this final sentence.





【】证明：$\cfrac{1}{2}+\cfrac{1}{2^2}+\cdots+\cfrac{1}{2^n} < 1$

【解析】当$n=1$时，不等式成立，即$\cfrac{1}{2} < 1$；

假设当$n=k$时，不等式成立，可得：$\cfrac{1}{2}+\cfrac{1}{2^2}+\cdots+\cfrac{1}{2^k} < 1$

则当$n=k+1$时，可得：$\cfrac{1}{2}+\cfrac{1}{2^2}+\cdots+\cfrac{1}{2^k}+ \cfrac{1}{2^{k+1}}=\cfrac{1}{2}+\cfrac{1}{2}\left(\cfrac{1}{2}+\cfrac{1}{2^2}+\cdots+\cfrac{1}{2^k}  \right) < \cfrac{1}{2}+\cfrac{1}{2}=1$

综上，可得：$\cfrac{1}{2}+\cfrac{1}{2^2}+\cdots+\cfrac{1}{2^n} < 1$














## Components

### Alerts

{{< alert >}}This is an alert.{{< /alert >}}
{{< alert title="Note" >}}This is an alert with a title.{{< /alert >}}
{{% alert title="Note" %}}This is an alert with a title and **Markdown**.{{% /alert %}}
{{< alert color="success" >}}This is a successful alert.{{< /alert >}}
{{< alert color="warning" >}}This is a warning.{{< /alert >}}
{{< alert color="warning" title="Warning" >}}This is a warning with a title.{{< /alert >}}



