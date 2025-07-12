---
title: 函数极限
description: func limit
date: 2025-06-27
weight: 100
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}

{{< /alert >}}


```viz-dot
digraph "自变量变化过程" {
rankdir = TB;
edge [
        fontsize="12"
        ];
node [
        shape = box;
        fontsize = "12"
];

"自变量变化过程" -> "有限值"
"自变量变化过程" -> "无穷大"

"有限值" -> "x ==> x0"
"有限值" -> "x ==> x0-"
"有限值" -> "x ==> x0+"
"无穷大" -> "x ==> oo"
"无穷大" -> "x ==> -oo"
"无穷大" -> "x ==> +oo"


}
```


## 极限定义


（1）已知$A$为常数，若$ \forall \varepsilon >0 $，$  \exists \delta > 0 $，当$ 0 < \left | x - x_0   \right| < \delta $时，则恒有不等式：$ \left | f(x) - A   \right| < \varepsilon $

那么常数$A$叫做函数$f(x)$当$ x \to x_0 $时的极限，记作：$ \lim\limits_{  x \to x_0 } f(x) = A $




















