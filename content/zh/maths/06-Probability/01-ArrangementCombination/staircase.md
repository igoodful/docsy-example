---
title: 楼梯
description: 楼梯
date: 2023-10-30
weight: 10000


---

{{< alert >}}



{{< /alert >}}


【】如下图，从$A$到$B$有多少条不同的最短路径？
{{<figure src="/images/maths/probability/ac/staircase.png">}}
{{<alert color="primary">}}
【**思路**】
- 元素位置：元素是向右走$3$步（相同元素）和向上走$2$步（相同元素）。位置是$5$个不同位置
- 规则：从$A$到$B$的所有最短路径均是向右走$3$步，向上走$2$步
{{</alert>}}
【**解析**】如下图便是其中一些情况：
{{<figure src="/images/maths/probability/ac/staircase-a.png">}}
{{<figure src="/images/maths/probability/ac/staircase-b.png">}}
从$A$到$B$的所有最短路径为向右走$3$步和向上走$2$步，共$5$步，将$5$步排成一列。向右走的$3$步之间无顺序，向上走的$2$步之间也无顺序

故共有：$\cfrac{\operatorname{A} _ {5}^{5}}{\operatorname{A} _ {2}^{2}\operatorname{A} _ {3}^{3}}=\operatorname{C} _ {5}^{2}=\operatorname{C} _ {5}^{3}=10$条不同的最短路径



【】如下图，从$A$点出发每次只能向上或者向右走一步，则到达$B$点的路径的条数是多少？
{{<figure src="/images/maths/probability/ac/staircase-001.png">}}
{{<alert color="primary">}}
【**思路**】
- 做事：
- 规则：
{{</alert>}}
【解析】由$A$点到达$B$点，可分为$A\to C\to E\to B$和$A\to D\to B$两种路线：
- $\textcircled{1}$当路线为$A\to C\to E\to B$时，只需求$A\to C$时的路径条数，可得：$\operatorname{C}_ {5}^{2}=10$；
- $\textcircled{2}$当路线为$A\to D\to B$时，只需求$A\to D$时的路径条数，可得：$\operatorname{C}_ {6}^{3}=20$；
综上，共有：$10+20=30$条不同的路径





【】下图是某个区域的街道示意图（每个小矩形的边表示街道)，那么从$A$到$B$的最短线路有多少条？

{{<figure src="/images/maths/probability/ac/staircase-002.png">}}
{{<alert color="primary">}}
【**思路**】
- 做事：
- 规则：
{{</alert>}}
【解析】由$A$点到达$B$点，可分为$A\to C\to B$和$A\to D\to B$两种路线：
- $\textcircled{1}$当路线为$A\to C\to B$时，可得：

由$A\to C$时的路径条数为：$\operatorname{C}_ {6}^{3}=20$

由$C\to B$时的路径条数为：$\operatorname{C}_ {7}^{3}=35$

可得：$20\times 35=700$；
- $\textcircled{2}$当路线为$A\to D\to B$时，可得：

由$A\to D$时的路径条数为：$\operatorname{C}_ {6}^{2}=15$

由$D\to B$时的路径条数为：$\operatorname{C}_ {7}^{3}=35$

可得：$15\times 35=525$；

综上，共有：$700+525=1225$条不同的路径




【】如图所示是某个区域的街道示意图（每个小矩形的边表示街道），每个小正方形的边长为$1$，已知$A\left( 3,3 \right)$，$B\left( 10,5 \right)$，王大爷从坐标原点$O$开始沿着街道跑步锻炼身体，规定王大爷只能沿向上或向右方向跑步
{{<figure src="/images/maths/probability/ac/staircase-xy.png">}}
（1）若王大爷跑步至坐标$\left( m,n \right)$，设可能的跑步方案总数为$f\left( m,n \right)$，求$f\left( m,n \right)$

（2）若王大爷跑至坐标$B\left( 10,5 \right)$，有多少种跑步方案？

（3）若王大爷先跑到住在点$A\left( 3,3 \right)$的王大妈会合，再和王大妈一起跑到点$B\left( 10,5 \right)$，则王大爷到达点$B\left( 10,5 \right)$有多少种跑步方案？

（4）若住在点$A\left( 3,3 \right)$的王大妈养了一条狗，由于王大爷对狗有先天的恐惧，因此王大爷必须避开点$A\left( 3,3 \right)$跑到点$B\left( 10,5 \right)$，则王大爷到达点$B\left( 10,5 \right)$有多少种跑步方案？
{{<alert color="primary">}}
【**思路**】
- 做事：
- 规则：
{{</alert>}}
【**解析**】（1）王大爷跑步至$\left( m,n \right)$，一共需要走$m+n$步，其中向右走$m$步，向上走$n$步。

可得：只需要从$m+n$中选出向右走$m$步，或者从$m+n$中选出向上走$n$步

故跑步方案总数为：$f\left( m,n \right)=\operatorname{C} _ {m+n}^{n}$

（2）由（1）可得，王大爷由$O$到$B$共有：$f\left( 10,5 \right)=\operatorname{C} _ {15}^{5}=3003$种跑步方案

（3）第一步：王大爷由$O$到$A$共有：$\operatorname{C} _ {6}^{3}=20$种；

第二步：王大爷由$A$到$B$共有：$\operatorname{C} _ {9}^{2}=36$种；

综上，共有：$20\times 36=720$种

（4）由（2）可知，王大爷由$O$到$B$共有：$f\left( 10,5 \right)=\operatorname{C} _ {15}^{5}=3003$种跑步方案

由（3）可知，王大爷由$O$到$A$，再从$A$到$B$，共有：$20\times 36=720$种跑步方案

故从$O$出发并避开点$A\left( 3,3 \right)$跑到点$B\left( 10,5 \right)$的跑步方案数为：$3003-720=2283$


---
【】登一个$n$级的台阶，规定每步只能跨一级或两级，设不同的走法为$f\left( n \right)$

（1）求$f\left( 10 \right)$

（2）求$f\left( n \right)$

（3）若刚好$8$步登上一个$12$级的台阶，有多少种不同的走法？
{{<alert color="primary">}}
【**思路**】
- 做事：
- 规则：
{{</alert>}}
【解析】（1）根据走两步的个数作为分类标准：

- $\textcircled{1}$若恰有$0$步为两级，此时共$10$步，$10$步为一级，可得：$\operatorname{C}_ {10}^{10}$种走法；
- $\textcircled{2}$若恰有$1$步为两级，此时共$9$步，$8$步为一级，可得：$\operatorname{C}_ {9}^{1}$种走法；
- $\textcircled{3}$若恰有$2$步为两级，此时共$8$步，$6$步为一级，可得：$\operatorname{C}_ {8}^{2}$种走法；
- $\textcircled{4}$若恰有$3$步为两级，此时共$7$步，$4$步为一级，可得：$\operatorname{C}_ {7}^{3}$种走法；
- $\textcircled{5}$若恰有$4$步为两级，此时共$6$步，$2$步为一级，可得：$\operatorname{C}_ {6}^{4}$种走法；
- $\textcircled{6}$若恰有$5$步为两级，此时共$5$步，$0$步为一级，可得：$\operatorname{C}_ {5}^{5}$种走法；

综上，共有：$\operatorname{C}_ {10}^{10}+\operatorname{C}_ {9}^{1}+\operatorname{C}_ {8}^{2}+\operatorname{C}_ {7}^{3}+\operatorname{C}_ {6}^{4}+\operatorname{C}_ {5}^{5}=89$种不同的走法，即$f\left( 10 \right)=89$

（2）要上到$n$级的台阶，可分为如下两种情况：
- $\textcircled{1}$从第$n-1$级上一级到第$n$级；
- $\textcircled{2}$从第$n-2$级上两级到第$n$级；

综上，可得：$f\left( n \right)=f\left( n-1 \right)+f\left( n-2 \right)$

当$n=1$时，此时$f\left( 1 \right)=1$；当$n=2$时，此时$f\left( 1 \right)=2$；

（3）设一步上一级的有$x$步，设一步上两级的有$y$步，可得：$\begin{cases}
  & x+y=8 \\\\
 & x+2y=12 \\\\
\end{cases}$

解得：$ \begin{cases}
  & x=4 \\\\
 & y=4 \\\\
\end{cases} $

可得：一共$8$步，$4$步为上一级，$4$步为上两级，只需从$8$步中任选$4$步为上一级即可

故共有：$\operatorname{C}_ {8}^{4}=70$种不同的走法

---
【】共有$10$级台阶，某人一步可跨一级台阶，也可跨两级台阶或三级台阶，则他恰好$6$步上完台阶的方法种数是多少？
{{<alert color="primary">}}
【**思路**】
- 做事：元素是多个未定一级$x$，多个未定二级$y$，多个未定三级$z$；无位置
- 规则：确定分类元素个数比例：
{{</alert>}}
【**解析**】设一步跨一级有$x$步，一步跨两级有$y$步，一步跨三级有$z$步

可得：$\begin{cases}
  & x+y+z=6 \\\\
 & x+2y+3z=10 \\\\
\end{cases} $（$x,y,z\in \operatorname{N}$）

可得：$y=4+2z$（$x,y,z\in {{\operatorname{N}}_ {+}}$）

解得：$ \begin{cases}
  & x=2 \\\\
 & y=4 \\\\
 & z=0 \\\\
\end{cases} $，或$\begin{cases}
  & x=3 \\\\
 & y=2 \\\\
 & z=1 \\\\
\end{cases} $，或$\begin{cases}
  & x=4 \\\\
 & y=0 \\\\
 & z=2 \\\\
\end{cases} $

可得：$\operatorname{C}_ {6}^{2}\operatorname{C}_ {4}^{4}+\operatorname{C}_ {6}^{3}\operatorname{C}_ {3}^{2}\operatorname{C}_ {1}^{1}+\operatorname{C}_ {6}^{4}\operatorname{C}_ {2}^{2}=90$

故共有：$90$种

---
【】一只青蛙从平面坐标的原点出发，按照下列条件，有多少种跳法？

（1）规定每秒末必须向左跳一个单位或向右跳一个单位或向左跳两个单位长度或向右跳两个单位长度，若该青蛙在第$5$秒末恰好到达点$P\left( 3,0 \right)$；

（2）规定每秒末必须向右或向左或向上或向下跳一个单位长度，则该青蛙在第$10$秒末到达点$P\left( 2,4 \right)$；

（3）规定每秒末按照$\left( m,n \right)\to \left( m+1,n+1 \right)$或$\left( m,n \right)\to \left( m+1,n-1 \right)$跳动，且只能在在平面直角坐标系第一象限的整点上跳动（含第一象限$x$轴、$y$轴上的整点），若该青蛙在第$6$秒末恰好跳到点$F\left( 6,2 \right)$；
{{<alert color="primary">}}
【**思路**】
- 做事：
- 规则：
{{</alert>}}
【**解析**】

（1）设向右跳了$x$次一个单位，向右跳了$y$次两个单位，向左跳了$z$次一个单位，向左跳了$w$次两个单位

可得：$\begin{cases}
  & x+y+z+w=5 \\\\
 & x+2y-z-2w=3 \\\\
\end{cases} $（$x,y,z,w\in \operatorname{N}$）

可得：$y+2=2z+3w$

解得：$\begin{cases}
  & x=4 \\\\
 & y=0 \\\\
 & z=1 \\\\
 & w=0 \\\\
\end{cases} $或$\begin{cases}
  & x=3 \\\\
 & y=1 \\\\
 & z=0 \\\\
 & w=1 \\\\
\end{cases} $或$ \begin{cases}
  & x=1 \\\\
 & y=2 \\\\
 & z=2 \\\\
 & w=0 \\\\
\end{cases} $或$ \begin{cases}
  & x=0 \\\\
 & y=3 \\\\
 & z=1 \\\\
 & w=1 \\\\
\end{cases} $

- $\textcircled{1}$当$x=4,y=0,z=1,w=0$时，可得：$\operatorname{C}_ {5}^{4}\operatorname{C}_ {1}^{1}=5$；
- $\textcircled{2}$当$x=3,y=1,z=0,w=1$时，可得：$\operatorname{C}_ {5}^{3}\operatorname{C}_ {2}^{1}\operatorname{C}_ {1}^{1}=20$；
- $\textcircled{3}$当$x=1,y=2,z=2,w=0$时，可得：$\operatorname{C}_ {5}^{1}\operatorname{C}_ {4}^{2}\operatorname{C}_ {2}^{2}=30$；
- $\textcircled{4}$当$x=0,y=3,z=1,w=1$时，可得：$\operatorname{C}_ {5}^{3}\operatorname{C}_ {2}^{1}\operatorname{C}_ {1}^{1}=20$；

综上，共有：$5+20+30+20=75$种跳法

（2）设向左跳了$x$次，向右跳了$y$次，向上跳了$z$次，向下跳了$w$次

可得：$ \begin{cases}
  & x+y+z+w=10 \\\\
 & y-x=2 \\\\
 & z-w=4 \\\\
\end{cases} $（$x,y,z,w\in \operatorname{N}$）

解得：$\begin{cases}
  & x=0 \\\\
 & y=2 \\\\
 & z=6 \\\\
 & w=2 \\\\
\end{cases} $或$\begin{cases}
  & x=1 \\\\
 & y=3 \\\\
 & z=5 \\\\
 & w=1 \\\\
\end{cases} $或$\begin{cases}
  & x=2 \\\\
 & y=4 \\\\
 & z=4 \\\\
 & w=0 \\\\
\end{cases}$

- $\textcircled{1}$当$x=0,y=2,z=6,w=2$时，可得：$\operatorname{C}_ {10}^{2}\operatorname{C}_ {8}^{6}\operatorname{C}_ {2}^{2}=1260$；
- $\textcircled{2}$当$x=1,y=3,z=5,w=1$时，可得：$\operatorname{C}_ {10}^{1}\operatorname{C}_ {9}^{3}\operatorname{C}_ {6}^{5}\operatorname{C}_ {1}^{1}=5040$；
- $\textcircled{3}$当$x=2,y=4,z=4,w=0$时，可得：$\operatorname{C}_ {10}^{2}\operatorname{C}_ {8}^{4}\operatorname{C}_ {4}^{4}=3150$；

综上，共有：$1260+5040+3150=9450$种跳法

（3）设青蛙分别按照$\left( m,n \right)\to \left( m+1,n+1 \right)$和$\left( m,n \right)\to \left( m+1,n-1 \right)$跳动$x$次、$y$次

可得：$\begin{cases}
  & x+y=6 \cr
 & x+y=6 \cr
 & x-y=2 \cr
\end{cases}$ ，即$\begin{cases}
&x=4 \cr
&y=2 \cr
\end{cases}$

{{<figure src="/images/maths/probability/ac/staircase-003.png">}}

故可分为$O\to A\to B\to F$和$O\to A\to K\to J\to F$两种路径到达$F\left( 6,2 \right)$
- $\textcircled{1}$当沿着$O\to A\to B\to F$路径时，可得：$\operatorname{C}_ {4}^{2}=6$
- $\textcircled{2}$当沿着$O\to A\to K\to J\to F$路径时，可得：$\operatorname{C}_ {3}^{1}=3$

综上，共有：$6+3=9$种不同的运动轨迹

---
【你以为青蛙就只能左跳跳右跳跳吗？还可上下左右跳，对角线跳】

【】某班的甲、乙等$5$位同学玩传球游戏，规定第$1$次由甲将球传出，且每次每人传球只能向其他$4$人之一任意传出，设${{a}_ {n}}$和${{b}_ {n}}$，分别表示经$n$次传球后球回到甲和非甲（非甲是指除甲以外的$4$位同学）手里的传球方法种数.

（1）求${{a}_ {3}}$，${{b}_ {3}}$；

（2）求${{a}_ {n}}$和${{b}_ {n}}$满足的关系式；

（3）求数列$\\{ {{a}_ {n}} \\}$的通项公式；
{{<alert color="primary">}}
【**思路**】
- 做事：
- 规则：
{{</alert>}}
【**解析**】
（1）按照分步做事如下：
- $\textcircled{1}$经$1$次传球后，传给甲有$0$种，传给非甲有$\operatorname{C}_ {4}^{1}$种；
- $\textcircled{2}$经$2$次传球后，传给甲有$\operatorname{C}_ {4}^{1}\times 1$种，传给非甲有$\operatorname{C}_ {4}^{1}\operatorname{C}_ {3}^{1}$种；
- $\textcircled{3}$经$3$次传球后，传给甲有$\operatorname{C}_ {4}^{1}\operatorname{C}_ {3}^{1}\times 1$种，传给非甲有$\operatorname{C}_ {4}^{1}\operatorname{C}_ {3}^{1}\operatorname{C}_ {3}^{1}+\operatorname{C}_ {4}^{1}\times 1\times \operatorname{C}_ {4}^{1}$种；

可得：${{a}_ {3}}=12$，${{b}_ {3}}=52$

（2）按照分步做事如下：
- $\textcircled{1}$第$1$次传球后，可得：$\operatorname{C}_ {4}^{1}$种；
- $\textcircled{2}$第$2$次传球后，可得：$\operatorname{C}_ {4}^{1}$种；
- $\cdots$
- $\textcircled{n}$第$n$次传球后，可得：$\operatorname{C}_ {4}^{1}$种；

综上，经$n$次传球后，共有：${{4}^{n}}$种

又经$n$次传球后球回到甲和非甲的次数分别是${{a}_ {n}}$和${{b}_ {n}}$

可得：${{a}_ {n}}+{{b}_ {n}}={{4}^{n}}$

（3）考虑到经$n$次传球后和经$n+1$次传球后的关系：经$n$次传球后球回到非甲的次数${{b}_ {n}}$，则第$n+1$次传球传到甲的种数为$1$，可得：${{b}_ {n}}={{a}_ {n+1}}$

又有：${{a}_ {n}}+{{b}_ {n}}={{4}^{n}}$

可得：${{a}_ {n}}+{{a}_ {n+1}}={{4}^{n}}$

可得：$\cfrac{{{a}_ {n+1}}}{{{4}^{n+1}}}=-\cfrac{1}{4}\cdot \cfrac{{{a}_ {n}}}{{{4}^{n}}}+\cfrac{1}{4}$

可得：$\cfrac{{{a}_ {n+1}}}{{{4}^{n+1}}}-\cfrac{1}{5}=-\cfrac{1}{4}( \cfrac{{{a}_ {n}}}{{{4}^{n}}}-\cfrac{1}{5} )$

故数列$\\{ \cfrac{{{a}_ {n}}}{{{4}^{n}}}-\cfrac{1}{5} \\}$是以$\cfrac{{{a}_ {1}}}{{{4}^{1}}}-\cfrac{1}{5}=-\cfrac{1}{5}$为首项，$-\cfrac{1}{4}$为等比的等比数列

可得：$\cfrac{{{a}_ {n}}}{{{4}^{n}}}-\cfrac{1}{5}=-\cfrac{1}{5}\times {{( -\cfrac{1}{4} )}^{n-1}}$

解得：${{a}_ {n}}=\cfrac{4}{5}\times {{\left( -1 \right)}^{n}}+\frac{{{4}^{n}}}{5}$

故数列$\\{ {{a}_ {n}} \\}$的通项公式为：${{a}_ {n}}=\cfrac{4}{5}\times {{\left( -1 \right)}^{n}}+\cfrac{{{4}^{n}}}{5}$（$n\in {{\operatorname{N}}_ {+}}$）















