---
title: 数列极限性质
description: limit of a sequence
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



---

&#128311;证明下列结论

（1）若数列$\\{ x_n \\}$收敛，则数列$\\{ x_n \\}$的极限唯一

（2）若数列$\\{ x_n \\}$收敛，则数列$\\{ x_n \\}$一定有界

（3）若$ \lim\limits_{  n \to \infty } x_n = a $，且$a>0$，则存在正整数N，当$ n>N $时，都有：$ \left| x_n -a \right| < \cfrac{a}{2} $


【证明】（1）反证法：

$\textcircled{1}$ 设数列$\\{ x_n \\}$的一个极限为$a$，可得：$ \lim\limits_{  n \to \infty } x_n = a $

根据数列极限定义，可得：$ \forall \varepsilon_1 >0 $，$ \exists N_1 \in \text{N}_+ $，当$ n>N_1 $时，都有：$ \left| x_n -a \right| < \varepsilon_1  $

可得：$ a - \varepsilon_1 < x_n < a +\varepsilon_1  $

$\textcircled{2}$ 设数列$\\{ x_n \\}$的另一个极限为$b$，不妨设$a<b$，可得：$ \lim\limits_{  n \to \infty } x_n = b $

根据数列极限定义，可得：$ \forall \varepsilon_2 >0 $，$ \exists N_2 \in \text{N}_+ $，当$ n>N_2 $时，都有：$ \left| x_n -b \right| < \varepsilon_2  $

可得：$ b - \varepsilon_2 < x_n < b +\varepsilon_2  $

综上，取$ \varepsilon = \min\\{ \varepsilon_1,\varepsilon_2 \\}  $，$ N = \max \\{ N_1,N_2 \\} $，可得：$ a - \varepsilon < x_n < a+ \varepsilon $，且$ b- \varepsilon < x_n < b + \varepsilon $

可得：$ b-a < 2 \varepsilon $





