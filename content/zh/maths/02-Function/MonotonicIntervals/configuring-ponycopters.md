---
title: 单调区间
description: 单调区间
date: 2023-10-30
weight: 2


---

{{< alert >}}
（1）关于导函数的约定：若原函数为：$f\left( x \right)$

第一次求导后的导函数记为：${f_{1}}\left( x \right)=f^{'} \left( x \right)$

第二次求导后的导函数记为：${f_{2}}\left( x \right)={f_{1}}^{'} \left( x \right)$

依次此类推.

理由：有时候一道题需要多次求导才能解决问题，那么多次求导后又必然需要原路返回进行判断单调性，如果求导次数较多，那么复杂度就越高，也就越容易忘记这是第几次求导了，通过这种标记后，就能相对完美地解决了该问题，因为你通过下标就能准确地知道这是第几次求导.因此本书所有求导均按照此规则.

下面举例说明
{{< /alert >}}


{{< alert >}}
（1）关于导函数的约定：若原函数为$f(x)$，

{{< /alert >}}

【案例一】若原函数为：$f\left( x \right)={{\operatorname{e}}^{x}}-1-x-\frac{{{x}^{2}}}{2}$

第一次求导后记为：${f_{1}}\left( x \right)=f^{'} \left( x \right)={{\operatorname{e}}^{x}}-1-x$

第二次求导后记为：${f_{2}}\left( x \right)={f_{1}}^{'} \left( x \right)={{\operatorname{e}}^{x}}-1$


【案例二】若原函数为：$f\left( x \right)={{\operatorname{e}}^{x}}-1-x-\frac{{x^{2}}}{2}$

第一次求导后记为：${f_{1}}\left( x \right)=f^{'} \left( x \right)=\frac{{x^{2}}+x-\ln x+1}{{x^{2}}}$

记${F_{2}}\left( x \right)={x^{2}}{f_{1}}\left( x \right)={x^{2}}+x-\ln x+1$

第二次求导后记为：${f_{2}}\left( x \right)={F_{2}}^{'} \left( x \right)=\frac{\left( 2x-1 \right)\left( x+1 \right)}{x}$


（2）关于常见的放缩方法的说明：本书按照"放缩+方法+位置"的方式进行标记，这样标记清晰地说明了什么放缩方法和放缩出现的位置，高中阶段只需要熟悉指数、对数、正弦、正切、均值即可，具体如下：

${{\operatorname{e}}^{x}}>1+x+\cfrac{1}{2}{{x}^{2}}+\cdot \cdot \cdot +\cfrac{1}{n!}{{x}^{n}}$

$\ln x<x-1$

$\sin x<x<\tan x$（$x\in \left( 0,\frac{\pi }{2} \right)$）

$\sqrt{ab}\le \cfrac{a+b}{2}\le \cfrac{{a^{2}}+{b^{2}}}{2}$

真正需要背记的放缩公式就是以上四个公式，其他杂七杂八的放缩尽可能地不要去花时间背记.那什么时候才用这些放缩呢？

只要出现了$\ln x,{{\operatorname{e}}^{x}},\sin x,\cos x,\tan x$这些函数，你均可尝试放缩，

根据求导的次数作为分类标准的话，有可能是原函数直接放缩，也可能是第一次求导后放缩，也可能是第二次求导后放缩，这并没有固定的位置，

当你别无选择的时候，那你就试试放缩吧，有时候放缩还不一定全部放缩，有可能只放缩一部分，比如一个函数有多个地方有$\sin x$，

可能只需放缩某一个地方的$\sin x$即可，而不是所有的$\sin x$都给放缩了.因此使用放缩的时候，可以按照求导次数和是否全部放缩进行尝试放缩。



（3）关于极限问题：虽然高考不考，但是基本的求极限还是很有必要的，比如洛必达法则，对画出函数图象很有帮助，极限可以分成两类：点极限和无穷极限；

- 若$\lim\limits_{x\to x_0} f(x) = 0$，$\lim\limits_{x\to x_0} g(x) = 0$，则$\lim\limits_{x\to x_0} \cfrac{f(x)}{g(x)} = \lim\limits_{x\to x_0} \cfrac{f^{'}(x)}{g^{'}(x)}$

- 若$\lim\limits_{x\to x_0} f(x) = \infty$，$\lim\limits_{x\to x_0} g(x) = \infty$，则$\lim\limits_{x\to x_0} \cfrac{f(x)}{g(x)} = \lim\limits_{x\to x_0} \cfrac{f^{'}(x)}{g^{'}(x)}$

举例说明：

$
\begin{aligned} \lim\limits_{x\to 0} \cfrac{\sin x}{x} &= \lim\limits_{x\to 0} \cfrac{(\sin x) ^{'}}{(x) ^{'}} \\\\
&= \lim\limits_{x\to 0} \cfrac{\cos x}{1} \\\\
&= 1
\end{aligned}
$


$
\begin{aligned} \lim\limits_{x\to +\infty} \cfrac{x^2}{\operatorname{e}^x} &= \lim\limits_{x\to +\infty} \cfrac{(x^2) ^{'}}{(\operatorname{e}^x) ^{'}} \\\\
&= \lim\limits_{x\to +\infty} \cfrac{2x}{\operatorname{e}^x} \\\\
&= \lim\limits_{x\to +\infty} \cfrac{(2x) ^{'}}{(\operatorname{e}^x) ^{'}} \\\\
&= \lim\limits_{x\to +\infty} \cfrac{2}{\operatorname{e}^x} \\\\
&= 0
\end{aligned}
$

（4）必须熟悉基本函数的图象和性质：指数函数，对数函数，三角函数，幂函数

（5）求导原则：由于求导次数越多，就越复杂，因此尽可能根据参数范围或自变量范围提前讨论，有时候求导后反而不能判断函数的单调性或正负性；每次求导后均可分为以下五种情况：

第一，一阶正负确定。即导函数的正负性已经确定，这时候原函数的单调性也就确定了；

第二，一阶零点。即导函数的零点已经确定下来了，这时候原函数的单调区间也确定了

第三，一阶单调。即导函数的零点看不出来，但导函数的单调性确定了，那么导函数的零点进而也会确定下来，导函数的零点确定了，那么原函数的单调区间也确定了；

第四，一阶讨论。即当参数或自变量的取值在某个范围时，导函数的正负确定或单调性确定，这虽然不能全部确定下来，但是缩减了下一次求导后的讨论范围；

第五，如果以上四点都不满足，那就只有再次求导看看了

以上只是列举了第一次求导后必然面临的五种情况，从前往后，难度依次增加，这里概括了所有求导后的情况，

其中前四种是能够解决问题的，第五种情况相当于把问题留到下一次求导了，因此“一阶正负确定，一阶零点，一阶单调，一阶讨论”就是每次求导后的标准分类方案，

这是浏览了约10万道函数题目而得到的一个完美分类标准，这个分类方案的通用性极强，在解决函数恒成立、函数零点问题这两大核心主题，都能完美地解决，经过检验，

目前仅2008年江西高考压轴题例外，尚未发现其他题目不适用的情况。


【整体思路】

第一步：直接写出定义域和对称特征；观察函数的单调性，利用复合函数单调性特征

第二步：求导，根据求导结果分为四类：正负号确定；零点确定；单调；讨论

第三步：如果第一次求导无法获得以上四种情况，则需要进行下一次求导

## 类型一：基本函数和复合函数

【思路】

（1）不需求导就能得到单调区间；

（2）定义域和对称性 》基本函数的单调性和复合函数的单调性 》直接得到单调区间

【01】求函数$f\left( x \right)={{x}^{2}}$的单调区间

【解析】函数$f\left( x \right)$的定义域为：$\left( -\infty ,+\infty  \right)$

易知：$f\left( x \right)$为偶函数

故$f\left( x \right)$的单调递减区间为$\left( -\infty ,0 \right)$，单调递增区间为$\left( 0,+\infty  \right)$

【02】求函数$f\left( x \right)=-{{x}^{2}}$的单调区间

【解析】函数$f\left( x \right)$的定义域为：$\left( -\infty ,+\infty  \right)$

易知：$f\left( x \right)$为偶函数

故$f\left( x \right)$的单调递减区间为$\left( 0,+\infty  \right)$，单调递增区间为$\left( -\infty ,0 \right)$

【03】求函数$f\left( x \right)=\frac{1}{{{x}^{2}}}$的单调区间

【解析】函数$f\left( x \right)$的定义域为：$\left( -\infty ,0 \right)\cup \left( 0,+\infty  \right)$

易知：$f\left( x \right)$为偶函数

故$f\left( x \right)$的单调递减区间为$\left( 0,+\infty  \right)$，单调递增区间为$\left( -\infty ,0 \right)$

【04】求函数$f\left( x \right)={{x}^{2}}-\frac{1}{{{x}^{2}}}$（$x>0$）的单调区间

【解析】函数$f\left( x \right)$的定义域为：$\left( 0,+\infty  \right)$

易知：$f\left( x \right)$为偶函数

故$f\left( x \right)$的单调递增区间为$\left( 0,+\infty  \right)$

【05】求函数$f\left( x \right)=\ln \left( {{\left( x-1 \right)}^{2}}-1 \right)$的单调区间

【解析】函数$f\left( x \right)$的定义域为：$\left( 0,+\infty  \right)$

故$f\left( x \right)$的单调递增区间为$\left( 0,+\infty  \right)$

【06】求函数$f\left( x \right)=x{{\operatorname{e}}^{x}}+\ln x+{{x}^{2}}\sin x-\frac{1}{{{\left( x+1 \right)}^{2}}}-\cos x$（$0<x<1$）的单调区间

【解析】函数$f\left( x \right)$的定义域为：$\left( 0,1 \right)$

故函数$f\left( x \right)$的单调递增区间为$\left( 0,1 \right)$







## 类型二：一阶正负确定
{{<alert title="思路：" color="secondary">}}
- 定义域和对称性$\Rightarrow $一阶正负确定$\Rightarrow $原函数单调；

- 每次求导后的基本操作有：分式通分$\Rightarrow $根式有理化$\Rightarrow $因式分解
{{</alert>}}

【例题】求下列函数的单调区间

（1）$f\left( x \right)=x\sin x$（$-\cfrac{\pi }{2}\le x\le \cfrac{\pi }{2}$）

（2）$f\left( x \right)=\cfrac{\cos x}{x}$（$-\cfrac{\pi }{2}\le x\le \cfrac{\pi }{2},$且$x\ne 0$）

（3）$f\left( x \right)=2\cos x-\cfrac{1}{\pi }{{x}^{2}}+3x$（$0<x<\cfrac{\pi }{2}$）

（4）$f\left( x \right)=\ln \left( x+1 \right)-\sqrt{2x+1}$

（5）$f\left( x \right)=\cfrac{1}{x+1}+\ln \left( \cfrac{1-x}{1+x} \right)$

（6）$f\left( x \right)={{\operatorname{e}}^{x}}-{{\operatorname{e}}^{-x}}-2x$


【解析】

（1）定义域为：$( -\cfrac{\pi }{2},\cfrac{\pi }{2} )$

易知：$f\left( x \right)$是偶函数

记${f_{1}}\left( x \right)=f^{'} \left( x \right)=\sin x+x\cos x$，${f_{1}}\left( 0 \right)=0$

当$x\in \left( 0,\frac{\pi }{2} \right)$时，可得：$\sin x>0$，$x\cos x>0$

可得：${{f}_{1}}\left( x \right)>0$

故函数$f\left( x \right)$的单调递减区间为$( -\cfrac{\pi }{2},0)$，单调递增区间为$( 0,\cfrac{\pi }{2} )$

（2）定义域为：$( -\cfrac{\pi }{2},0 )\cup ( 0,\cfrac{\pi }{2})$

易知：$f\left( x \right)$为奇函数

记${f_{1}}\left( x \right)=f^{'} \left( x \right)=-\cfrac{x\sin x+\cos x}{{{x}^{2}}}$

当$x\in \left( 0,\frac{\pi }{2} \right)$时，可得：$x\sin x>0$，$\cos x>0$

可得：${f_{1}}\left( x \right)<0$

综上，函数$f\left( x \right)$的单调递减区间为：$( -\cfrac{\pi }{2},0)$和$( 0,\cfrac{\pi }{2} )$

（3）定义域为：$( 0,\cfrac{\pi }{2})$

记${{f}_{1}}\left( x \right)=f^{'} \left( x \right)=3-\frac{2}{\pi }x-2\sin x>0$

可得：${{f}_{1}}\left( x \right)>0$

故函数$f\left( x \right)$的单调递增区间为$( 0,\cfrac{\pi }{2} )$

（4）定义域为：$( -\cfrac{1}{2},+\infty )$

记${f_{1}}\left( x \right)=f^{'} \left( x \right)=\cfrac{1}{x+1}-\cfrac{1}{\sqrt{2x+1}}$

$=\cfrac{\sqrt{2x+1}-\left( x+1 \right)}{\left( x+1 \right)\sqrt{2x+1}}$

$=\cfrac{-{{x}^{2}}}{\left( x+1 \right)\sqrt{2x+1}\left( \sqrt{2x+1}+\left( x+1 \right) \right)}$

可得：${f_{1}}\left( x \right)<0$

故函数$f\left( x \right)$的单调递减区间为$( -\cfrac{1}{2},+\infty )$

画出函数的大致图象

（5）定义域为：$\left( -1,1 \right)$

记${f_{1}}\left( x \right)=f^{'} \left( x \right)=- \cfrac{1}{{{\left( x+1 \right)}^{2}}} - \cfrac{2}{\left( 1-x \right)\left( 1+x \right)} < 0$

可得：${f_{1}}\left( x \right)<0$

故函数$f\left( x \right)$的单调递减区间为$\left( -1,1 \right)$

（6）函数$f\left( x \right)$的定义域为：$\left( -\infty ,+\infty  \right)$

记${f_{1}}\left( x \right)=f^{'} \left( x \right)={{\operatorname{e}}^{x}}+{{\operatorname{e}}^{-x}}-2\ge 0$

可得：${f_{1}}\left( x \right)>0$

故函数$f\left( x \right)$的单调递增区间为$\left( -\infty ,+\infty  \right)$


## 类型三：一阶零点
{{<alert title="思路：" color="secondary">}}
- 定义域和对称性$\Rightarrow $一阶零点$\Rightarrow $原函数单调区间；

- 每次求导后的基本操作有：分式通分$\Rightarrow $根式有理化$\Rightarrow $因式分解
{{</alert>}}

【例题】求下列函数的单调区间

（1）$f\left( x \right)=6\ln x+\cfrac{1}{2}{{x}^{2}}-5x+4$

（2）$f\left( x \right)=\cfrac{1}{2}{x^{2}}-\cfrac{8}{3}{{x}^{\frac{3}{2}}}+3x$

（3）$f\left( x \right)=\sqrt{1+x}-\ln x$

（4）$f\left( x \right)={{\left( x-1 \right)}^{2}}-8\ln \left| x-1 \right|$

（5）$f\left( x \right)={{x}^{n}}\ln x$（$n\in {{\operatorname{N}}_{+}}$）

（6）$f\left( x \right)=\cfrac{\ln x}{{{x}^{n}}}$（$n\in {{\operatorname{N}}_{+}}$）

（7）$f\left( x \right)=\cfrac{{x^{n}}}{{{\operatorname{e}}^{x}}}$（$n\in {{\operatorname{N}}_{+}}$）

（8）$f\left( x \right)={{\operatorname{e}}^{x}}\cos x$

（9）$f\left( x \right)=x\sin x+\cos x-\cfrac{1}{4}{x^{2}}$（$-\pi <x<\pi $）


【解析】

（1）定义域为：$\left( 0,+\infty  \right)$

令${f_{1}}\left( x \right)=f^{'} \left( x \right)=\cfrac{6}{x}+x-5$

$=\cfrac{{{x}^{2}}-5x+6}{x}$

$=\cfrac{\left( x-2 \right)\left( x-3 \right)}{x}$

$\textcircled{1}$ 当$x\in \left( 0,2 \right)$时，${f_{1}}\left( x \right)>0$；

$\textcircled{2}$ 当$x\in \left( 2,3 \right)$时，${f_{1}}\left( x \right)<0$；

$\textcircled{3}$ 当$x\in \left( 3,+\infty  \right)$时，${f_{1}}\left( x \right)>0$

综上，函数$f\left( x \right)$的单调递减区间为$\left( 2,3 \right)$，单调递增区间为$\left( 0,2 \right)$和$\left( 3,+\infty  \right)$

（2）定义域为：$\left[ 0,+\infty  \right)$

令${f_{1}}\left( x \right)=f^{'} \left( x \right)=x-4\sqrt{x}+3$

$=\left( \sqrt{x}-1 \right)\left( \sqrt{x}-3 \right)$

$\textcircled{1}$ 当$x\in \left( 0,1 \right)$时，${f_{1}}\left( x \right)>0$；

$\textcircled{2}$ 当$x\in \left( 1,9 \right)$时，${f_{1}}\left( x \right)<0$；

$\textcircled{3}$ 当$x\in \left( 9,+\infty  \right)$时，${f_{1}}\left( x \right)>0$

综上，函数$f\left( x \right)$的单调递减区间为$\left( 1,9 \right)$，单调递增区间为$\left( 0,1 \right)$和$\left( 9,+\infty  \right)$

（3）定义域为：$\left( 0,+\infty  \right)$

记${f_{1}}\left( x \right)=f^{'} \left( x \right)=\cfrac{1}{2\sqrt{1+x}}-\cfrac{1}{x}$

$=\cfrac{x-2\sqrt{1+x}}{2x\sqrt{1+x}}$（分式通分）

$=\cfrac{{x^2}-4x-4}{2x\sqrt{1+x}\left( x+2\sqrt{1+x} \right)}$（根式有理化）

$=\cfrac{\left( x-2-2\sqrt{2} \right)\left( x-2+2\sqrt{2} \right)}{2x\sqrt{1+x}\left( x+2\sqrt{1+x} \right)}$（因式分解）

$\textcircled{1}$ 当$x\in \left( 0,2+2\sqrt{2} \right)$时，${f_{1}}\left( x \right)<0$；

$\textcircled{2}$ 当$x\in \left( 2+2\sqrt{2},+\infty  \right)$时，${f_{1}}\left( x \right)>0$；

故函数$f\left( x \right)$的单调递减区间为$\left( 0,2+2\sqrt{2} \right)$，单调递增区间为$\left( 2+2\sqrt{2},+\infty  \right)$

（4）定义域为：$\left( -\infty ,1 \right)\cup \left( 1,+\infty  \right)$

$f\left( x \right)$可化为：$f(x) = \begin{cases}
   (x-1)^2 - 8\ln( x-1 ) & (x>1) \cr
   (x-1)^2 - 8\ln(1-x)   &  (x<1)
\end{cases}
$

记$f_1 (x) =  f^{'} (x) = \begin{cases}
   \cfrac{2(x-1)^2-8}{x-1} & (x>1) \cr
   \cfrac{-2(x-1)^2+8}{1-x}  &  (x<1)
\end{cases}
$
,${f_{1}}\left( 3 \right)={f_{1}}\left( -1 \right)=0$

$\textcircled{1}$ 当$x>1$时，若$x\in \left( 1,3 \right)$时，${f_{1}}\left( x \right)<0$；若$x\in \left( 3,+\infty  \right)$时，${f_{1}}\left( x \right)>0$

$\textcircled{2}$ 当$x<1$时，若$x\in \left( -1,1 \right)$时，${f_{1}}\left( x \right)>0$；若$x\in \left( -\infty ,-1 \right)$时，${f_{1}}\left( x \right)<0$

综上，函数$f\left( x \right)$的单调递增区间为$\left( -1,1 \right)$和$\left( 3,+\infty  \right)$，单调递减区间为$\left( -\infty ,-1 \right)$和$\left( 1,3 \right)$

（5）定义域为：$\left( 0,+\infty  \right)$

${f_{1}}\left( x \right)=f^{'} \left( x \right)={x^{n-1}}\left( n\ln x+1 \right)$

当$x\in ( 0,{{\operatorname{e}}^{-\frac{1}{n}}})$时，${f_{1}}\left( x \right)<0$；当$x\in ( {{\operatorname{e}}^{-\frac{1}{n}}},+\infty  )$时，${f_{1}}( x )>0$

故函数$f\left( x \right)$的单调递减区间为$( 0,{{\operatorname{e}}^{-\frac{1}{n}}} )$，单调递增区间为$( {{\operatorname{e}}^{-\frac{1}{n}}},+\infty  )$

（6）定义域为：$\left( 0,+\infty  \right)$

${f_{1}}\left( x \right)=f^{'} \left( x \right)=\cfrac{{x^{n-1}}-n{{x}^{n-1}}\ln x}{{{x}^{2n}}}=\cfrac{{{x}^{n-1}}\left( 1-n\ln x \right)}{{{x}^{2n}}}$

当$x\in \left( 0,\sqrt[n]{\operatorname{e}} \right)$时，${f_{1}}\left( x \right)>0$；当$x\in \left( \sqrt[n]{\operatorname{e}},+\infty  \right)$时，${f_{1}}\left( x \right)<0$

故函数$f\left( x \right)$的单调递增区间为$\left( 0,\sqrt[n]{\operatorname{e}} \right)$，单调递减区间为$\left( \sqrt[n]{\operatorname{e}},+\infty  \right)$

（7）定义域为：$\left( -\infty ,+\infty  \right)$

${f_{1}}\left( x \right)=f^{'} \left( x \right)=\cfrac{{x^{n-1}}\left( n-x \right)}{{{\operatorname{e}}^{x}}}$

$\textcircled{1}$ 当$n$为奇数时，若$x\in \left( -\infty ,n \right)$时，${f_{1}}\left( x \right)\ge 0$；若$x\in \left( n,+\infty  \right)$时，${f_{1}}\left( x \right)<0$

$\textcircled{2}$ 当$n$为偶数时，若$x\in \left( 0,n \right)$时，${f_{1}}\left( x \right)\ge 0$；若$x\in \left( -\infty ,0 \right)$和$\left( n,+\infty  \right)$时，${f_{1}}\left( x \right)<0$

综上可得：

当$n$为正奇数时，$f\left( x \right)$的单调递减区间为$\left( n,+\infty  \right)$，单调递增区间为$\left( -\infty ,n \right)$；

当$n$为正偶数时，$f\left( x \right)$的单调递减区间为$\left( -\infty ,0 \right)$和$\left( n,+\infty  \right)$，单调递增区间为$\left( 0,n \right)$；

（8）定义域为：$\left( -\infty ,+\infty  \right)$

${f_{1}}\left( x \right)=f^{'} \left( x \right)=\sqrt{2}{{\operatorname{e}}^{x}}\cos ( x+\cfrac{\pi }{4} )$

当$2k\pi -\cfrac{3\pi }{4}<x<2k\pi +\cfrac{\pi }{4}$（$k\in \operatorname{Z}$）时，${f_{1}}\left( x \right)>0$；

当$2k\pi +\cfrac{\pi }{4}<x<2k\pi +\cfrac{5\pi }{4}$（$k\in \operatorname{Z}$）时，${f_{1}}\left( x \right)<0$；

$f\left( x \right)$的单调递增区间为$( 2k\pi -\cfrac{3\pi }{4},2k\pi +\cfrac{\pi }{4} )$，单调递减区间为$( 2k\pi +\cfrac{\pi }{4},2k\pi +\cfrac{5\pi }{4} )$

（9）定义域为：$\left( -\pi ,\pi  \right)$

易知：$f\left( x \right)$为偶函数

记${f_1}\left( x \right)=f^{'} \left( x \right)=x\cos x-\cfrac{1}{2}x=x( \cos x-\cfrac{1}{2} )$，${f_{1}}\left( 0 \right)={f_{1}}( -\cfrac{\pi }{3} )={f_{1}}( \cfrac{\pi }{3} )=0$

$\textcircled{1}$ 当$-\cfrac{\pi }{3}<x<0$或$\cfrac{\pi }{3}<x<\pi $时，${f_{1}}\left( x \right)<0$；

$\textcircled{2}$ 当$-\pi <x<-\cfrac{\pi }{3}$或$0<x<\cfrac{\pi }{3}$时，${f_{1}}\left( x \right)>0$；

故函数$f\left( x \right)$的单调递增区间为$( -\pi ,-\cfrac{\pi }{3} )$和$( 0,\cfrac{\pi }{3} )$，单调递减区间为$( -\cfrac{\pi }{3},0 )$和$( \cfrac{\pi }{3},\pi )$



## 类型四：一阶单调

{{<alert title="思路：" color="secondary">}}
- 定义域和对称性$\Rightarrow $一阶单调$\Rightarrow $一阶零点$\Rightarrow $原函数单调区间；

- 每次求导后的基本操作有：分式通分$\Rightarrow $根式有理化$\Rightarrow $因式分解
{{</alert>}}

【例题】求下列函数的单调区间

（1）$f\left( x \right)={{\operatorname{e}}^{x}}+\cfrac{1}{2}{x^{2}}+x$

（2）$f\left( x \right)={{\operatorname{e}}^x}-\cfrac{x}{x+1}$（$x>-1$）

（3）$f\left( x \right)=x{{\operatorname{e}}^{x}}-2\operatorname{e}\ln x$

（4）$f\left( x \right)=\cfrac{\ln x-1}{x}-2x$

（5）$f\left( x \right)=\cfrac{\sin x}{x}$（$0<x<\pi $）

（6）$f\left( x \right)=\cfrac{x}{\operatorname{e}}-\cfrac{x+\ln x}{{{\operatorname{e}}^{x}}}$

（7）$f\left( x \right)=x-\cfrac{\ln x}{x}$


【解析】

（1）定义域为：$\left( -\infty ,+\infty  \right)$

记${f_{1}}\left( x \right)=f^{'} \left( x \right)={{\operatorname{e}}^x}+x-1$，${f_{1}}\left( 0 \right)=0$

易知：${f_{1}}\left( x \right)$在区间$\left( -\infty ,+\infty  \right)$上单调递增

$\textcircled{1}$ 当$x\in \left( -\infty ,0 \right)$时，可得：${f_{1}}\left( x \right)<0$；

$\textcircled{2}$ 当$x\in \left( 0,+\infty  \right)$时，可得：${f_{1}}\left( x \right)>0$

故函数$f\left( x \right)$的单调递减区间为$\left( -\infty ,0 \right)$，单调递增区间为$\left( 0,+\infty  \right)$

（2）定义域为：$\left( -1,+\infty  \right)$

令${f_{1}}\left( x \right)=f^{'} \left( x \right)={{\operatorname{e}}^{x}}-\cfrac{1}{{{\left( x+1 \right)}^{2}}}$，${f_{1}}\left( 0 \right)=0$

易知：${f_{1}}\left( x \right)$在区间$\left( -1,+\infty  \right)$上单调递增

$\textcircled{1}$ 当$x\in \left( -1,0 \right)$时，可得：${f_{1}}\left( x \right)<0$；

$\textcircled{2}$ 当$x\in \left( 0,+\infty  \right)$时，可得：${f_{1}}\left( x \right)>0$

故函数$f\left( x \right)$的单调递减区间为$\left( -1,0 \right)$，单调递增区间为$\left( 0,+\infty  \right)$

（3）定义域为：$\left( 0,+\infty  \right)$

令${f_{1}}\left( x \right)=f^{'} \left( x \right)=\left( x+1 \right){{\operatorname{e}}^{x}}-\cfrac{2\operatorname{e}}{x}$，${{f}_{1}}\left( 1 \right)=0$

易知：${{f}_{1}}\left( x \right)$在区间$\left( 0,+\infty  \right)$上单调递增

$\textcircled{1}$ 当$x\in \left( 0,1 \right)$时，可得：${f_{1}}\left( x \right)<0$；

$\textcircled{2}$ 当$x\in \left( 1,+\infty  \right)$时，可得：${f_{1}}\left( x \right)>0$

故函数$f\left( x \right)$的单调递减区间为$\left( 0,1 \right)$，单调递增区间为$\left( 1,+\infty  \right)$

（4）定义域为：$\left( 0,+\infty  \right)$

记${f_{1}}\left( x \right)=f^{'} \left( x \right)=\cfrac{2-\ln x}{{x^{2}}}-2=\cfrac{2-2{x^{2}}-\ln x}{{x^{2}}}$，${f_{1}}\left( 1 \right)=0$

记$h\left( x \right)=2-2{x^2}-\ln x$，易知：$h\left( x \right)$在区间$\left( 0,+\infty  \right)$上单调递减

$\textcircled{1}$ 当$x\in \left( 0,1 \right)$时，$2-2{x^2}>0$，$-\ln x>0$，可得：${f_1}\left( x \right)>0$；

$\textcircled{2}$ 当$x\in \left( 1,+\infty  \right)$时，$2-2{x^2}<0$，$-\ln x<0$，可得：${f_1}\left( x \right)<0$；

故函数$f\left( x \right)$的单调递增区间为$\left( 0,1 \right)$，单调递减区间为$\left( 1,+\infty  \right)$

（5）定义域为：$\left( 0,\pi  \right)$

记${f_1}\left( x \right)=f^{'} \left( x \right)=\cfrac{x\cos x-\sin x}{{x^{2}}}$

$\textcircled{1}$ 当$x\in ( \cfrac{\pi }{2},\pi  )$时，可得：$x\cos x<0$，$\sin x>0$

可得：${f_{1}}\left( x \right)<0$

$\textcircled{2}$ 当$x\in ( 0,\cfrac{\pi }{2} )$时，${f_1}\left( x \right)=\cfrac{x\cos x-\sin x}{{{x}^{2}}}=\cfrac{\cos x}{{{x}^{2}}}\left( x-\tan x \right)$

记${F_{2}}\left( x \right)=x-\tan x$，${F_{2}}\left( 0 \right)=0$

记${f_{2}}\left( x \right)={{F}_{2}}^{'} \left( x \right)=1-\cfrac{1}{{{\cos }^{2}}x}\le 0$

故${F_{2}}\left( x \right)$在区间$( 0,\cfrac{\pi }{2} )$上单调递减

可得：$x-\tan x<0$，$\cos x<0$

可得：${f_{1}}\left( x \right)<0$

综上，$f\left( x \right)$的单调递减区间为$\left( 0,\pi  \right)$

（6）定义域为：$\left( 0,+\infty  \right)$

记${f_{1}}( x )=f^{'} ( x )=\frac{\left( {{\operatorname{e}}^x}-\cfrac{\operatorname{e}}{x} \right)+\operatorname{e}( \ln x+x-1 )}{{{\operatorname{e}}^{x+1}}}$，${f_{1}}\left( 1 \right)=0$

记${F_{2}}( x )={{\operatorname{e}}^{x+1}}{f_{1}}( x)={{\operatorname{e}}^{x}}-\cfrac{\operatorname{e}}{x}+\ln x+x-1$，易知${F_{2}}\left( x \right)$在区间$\left( 0,+\infty  \right)$上单调递增

可得：当$0<x<1$时， ${f_{1}}\left( x \right)<0$；当$x>1$时，${f_{1}}\left( x \right)>0$；

故函数$f\left( x \right)$的单调递增区间为$\left( 1,+\infty  \right)$，单调递减区间为$\left( 0,1 \right)$

（7）定义域为：$\left( 0,\infty  \right)$

记${f_{1}}\left( x \right)=f^{'} \left( x \right)=\cfrac{{{x}^{2}}-1+\ln x}{{x^{2}}}$，${f_{1}}\left( 1 \right)=0$

记${F_{2}}\left( x \right)={{x}^{2}}-1+\ln x$，${F_{2}}\left( 1 \right)=0$

易知：${F_{2}}\left( x \right)$在区间$\left( 0,\infty  \right)$上单调递增

可得：当$x\in \left( 0,1 \right)$时，${f_{1}}\left( x \right)<0$；当$x\in \left( 1,+\infty  \right)$时，${f_{1}}\left( x \right)>0$；

综上，函数$f\left( x \right)$的单调递减区间为$\left( 0,1 \right)$，单调递增区间为$\left( 1,+\infty  \right)$













## 类型五：一阶讨论
{{<alert title="思路：" color="secondary">}}
- 定义域和对称性$\Rightarrow $二阶正负确定$\Rightarrow $一阶单调区间$\Rightarrow $一阶零点$\Rightarrow $原函数单调区间；

- 每次求导后的基本操作有：分式通分$\Rightarrow $根式有理化$\Rightarrow $因式分解

- 原则：尽可能提前讨论，也就是说不求导就能确定的单调区间就不要留到求导，同理第一次求导就能确定零点或单调或正负确定的情况就不要留到第二次求导
{{</alert>}}

【例题】求下列函数的单调区间

（1）$f\left( x \right)=\cos \left( x-1 \right)+x-x\ln x$


【解析】

（1）函数$f\left( x \right)$的定义域为：$\left( 0,\infty  \right)$

令${f_{1}}\left( x \right)=f^{'} \left( x \right)=-\sin \left( x-1 \right)-\ln x$，${f_{1}}\left( 1 \right)=0$

当$1<x<1+\pi $时，$-\sin \left( x-1 \right)<0$，$-\ln x<0$，可得：${f_{1}}\left( x \right)<0$

当$x>1+\pi $时，$-\sin \left( x-1 \right)\le 1$，$-\ln x<-1$，可得：${f_{1}}\left( x \right)<0$

当$0<x<1$时，令${f_{2}}\left( x \right)={f_{1}}^{'} \left( x \right)=-\cos \left( x-1 \right)-\frac{1}{x}$，易知：$-\cos \left( x-1 \right)\le 1$，$-\frac{1}{x}<-1$

可得：${f_{2}}\left( x \right)<0$

则有：${f_{1}}\left( x \right)$在区间$\left( 0,1 \right)$上单调递减

又${f_{1}}\left( 1 \right)=0$，可得：${f_{1}}\left( x \right)>0$

综上，函数$f\left( x \right)$的单调递增区间为$\left( 0,1 \right)$，单调递减区间为$\left( 1,+\infty  \right)$




## 类型六：二阶正负确定
{{<alert title="思路：" color="secondary">}}
- 定义域和对称性$\Rightarrow $二阶正负确定$\Rightarrow $一阶单调区间$\Rightarrow $一阶零点$\Rightarrow $原函数单调区间；

- 每次求导后的基本操作有：分式通分$\Rightarrow $根式有理化$\Rightarrow $因式分解

- 原则：尽可能提前讨论，也就是说不求导就能确定的单调区间就不要留到求导，同理第一次求导就能确定零点或单调或正负确定的情况就不要留到第二次求导
{{</alert>}}

【例题】求下列函数的单调区间

（1）$f\left( x \right)={{\operatorname{e}}^{x}}-2x+1+\sin x$

（2）$f\left( x \right)={{x}^{2}}-x\tan x$（$-\cfrac{\pi }{2}\le x\le \cfrac{\pi }{2}$）

【解析】

（1）定义域为：$\left( -\infty ,\infty  \right)$

记${f_{1}}\left( x \right)=f^{'} \left( x \right)={{\operatorname{e}}^{x}}+\cos x-2$，${f_{1}}\left( 0 \right)=0$

$\textcircled{1}$ 当$x\in \left( -\infty ,0 \right)$时，可得：${{\operatorname{e}}^{x}}<1$，$\cos x\le 1$，${{\operatorname{e}}^{x}}+\cos x-2<0$

可得：${f_{1}}\left( x \right)<0$

$\textcircled{2}$ 当$x\in \left( 0,+\infty  \right)$时，令${f_{2}}\left( x \right)={f_{1}}^{'} \left( x \right)={{\operatorname{e}}^{x}}-\sin x$，可得：${{\operatorname{e}}^{x}}-\sin x>0$

可得：${f_{1}}\left( x \right)$在区间$\left( 0,+\infty  \right)$上单调递增

又${f_{1}}\left( 0 \right)=0$，可得：${f_{1}}\left( x \right)>0$

综上，函数$f\left( x \right)$的单调递减区间为$\left( -\infty ,0 \right)$，单调递增区间为$\left( 0,+\infty  \right)$

（2）定义域为：$( -\cfrac{\pi }{2},\cfrac{\pi }{2})$

记${f_{1}}\left( x \right)=f^{'} \left( x \right)=2x-\tan x-\cfrac{x}{{{\cos }^{2}}x}=x( 1-\cfrac{1}{{{\cos }^{2}}x} )+\left( x-\tan x \right)$，${f_{1}}\left( 0 \right)=0$

记${F_{2}}\left( x \right)=x-\tan x$，${F_{2}}\left( 0 \right)=0$

记${f_{2}}\left( x \right)={{F}_{2}}^{'} \left( x \right)=1-\cfrac{1}{{{\cos }^{2}}x}\le 0$

故${F_{2}}\left( x \right)$在区间$( -\cfrac{\pi }{2},\cfrac{\pi }{2})$上单调递减

$\textcircled{1}$ 当$x\in ( -\cfrac{\pi }{2},0 )$时，可得：$x( 1-\cfrac{1}{{{\cos }^{2}}x} )>0$，$x-\tan x>0$

可得：${f_{1}}\left( x \right)>0$

$\textcircled{2}$ 当$x\in ( 0,\cfrac{\pi }{2} )$时，可得：$x( 1-\cfrac{1}{{{\cos }^{2}}x} )<0$，$x-\tan x<0$

可得：${f_{1}}\left( x \right)<0$

综上，函数$f\left( x \right)$的单调递增区间为$( -\cfrac{\pi }{2},0 )$，单调递减区间为$( 0,\cfrac{\pi }{2} )$







## 类型七：二阶零点

{{<alert title="思路：" color="secondary">}}
- 定义域和对称性$\Rightarrow $二阶零点$\Rightarrow $一阶单调区间$\Rightarrow $一阶零点$\Rightarrow $原函数单调区间；

- 每次求导后的基本操作有：分式通分$\Rightarrow $根式有理化$\Rightarrow $因式分解

- 原则：尽可能提前讨论，也就是说不求导就能确定的单调区间就不要留到求导，同理第一次求导就能确定零点或单调或正负确定的情况就不要留到第二次求导
{{</alert>}}

【例题】求下列函数的单调区间

（1）$f\left( x \right)=\cfrac{\ln x}{x}+\ln x+x$

（2）$f\left( x \right)={{\operatorname{e}}^{x-2}}-x\ln x+x+4$

（3）$f\left( x \right)=\cfrac{2\ln x+2}{{{\operatorname{e}}^{x}}}$

（4）${f_{k}}\left( x \right)={{\operatorname{e}}^{x}}- ( 1+x+\cfrac{1}{2!}{{x}^{2}}+\cfrac{1}{3!}{{x}^{3}}+\cdot \cdot \cdot +\cfrac{1}{k!}{{x}^{k}} )$（$k\in {{\operatorname{N}}_{+}}$）


【解析】
（1）定义域为：$\left( 0,+\infty  \right)$

记${f_{1}}\left( x \right)=f^{'} \left( x \right)=\cfrac{{x^{2}}+x-\ln x+1}{{x^{2}}}$

记${F_{2}}\left( x \right)={x^{2}}+x-\ln x+1$， ${F_{2}}( \cfrac{1}{2} )=\frac{7}{4}+\ln 2>0$

记${f_{2}}\left( x \right)={F_{2}}^{'} \left( x \right)=\cfrac{\left( 2x-1 \right)\left( x+1 \right)}{x}$

故${F_{2}}\left( x \right)$在区间$( 0,\cfrac{1}{2} )$上单调递减，在$( \cfrac{1}{2},+\infty )$上单调递增

又${F_{2}}( \cfrac{1}{2})>0$，可得：${f_{1}}\left( x \right)>0$

故函数$f\left( x \right)$的单调递增区间为$\left( 0,+\infty  \right)$

（2）定义域为：$\left( 0,+\infty  \right)$

记${f_{1}}\left( x \right)=f^{'} \left( x \right)={{\operatorname{e}}^{x-2}}-\ln x$

记${f_2}\left( x \right)={f_{1}}^{'} \left( x \right)={{\operatorname{e}}^{x-2}}-\cfrac{1}{x}$，${f_{2}}\left( 1 \right)=\cfrac{1}{\operatorname{e}}-1<0$，${f_{2}}\left( 2 \right)=\cfrac{1}{2}>0$

可得：${f_{2}}\left( x \right)$在区间$\left( 0,+\infty  \right)$上单调递增

记${f_{2}}\left( {x_{0}} \right)={{\operatorname{e}}^{{x_{0}}-2}}-\cfrac{1}{{x_{0}}}=0$，且$\cfrac{1}{2}<{x_{0}}<1$

则有：${f_{1}}\left( x \right)$在区间$( 0,{x_0})$上单调递减，在区间$\left( {x_{0}},+\infty  \right)$上单调递增

$f_1(x)_{\min }={{\operatorname{e}}^{{x_0}-2}}-\ln {x_0} = \cfrac{1}{x_0} - \ln {x_0}$

记$g\left( x \right)=\cfrac{1}{x}-\ln x$，则易知：$g\left( x \right)$在区间$\left( 0,+\infty  \right)$上单调递减

又$\cfrac{1}{2}<x<1$，则$g{{\left( x \right)}_{\min }}=g\left( 1 \right)=1>0$

则有：${f_{1}}{{\left( x \right)}_{\min }}>0$

可得：$f\left( x \right)$在区间$\left( 0,+\infty  \right)$上单调递增

故函数$f\left( x \right)$的单调递减区间为$\left( 0,+\infty  \right)$

（3）定义域为：$\left( 0,+\infty  \right)$

记${f_1}\left( x \right)=f^{'} \left( x \right)=\cfrac{2\left( 1-x-x\ln x \right)}{x{{\operatorname{e}}^x}}$，${f_{1}}\left( 1 \right)=0$

记${F_2}\left( x \right)=\cfrac{x{{\operatorname{e}}^{x}}}{2}{f_{1}}\left( x \right)=1-x-x\ln x$，${F_{2}}\left( 1 \right)=0$

记${f_2}\left( x \right)={F_{2}}^{'} \left( x \right)=-\left( \ln x+2 \right)$，${f_{2}}\left( {{\operatorname{e}}^{-2}} \right)=0$

故${F_2}\left( x \right)$在区间$\left( 0,{{\operatorname{e}}^{-2}} \right)$上单调递增，在$\left( {{\operatorname{e}}^{-2}},+\infty  \right)$上单调递减

又当$x$无限趋近$0$时，${F_{2}}\left( x \right)$趋近$1$，且${F_{2}}\left( 1 \right)=0$

则有：当$x\in \left( 0,1 \right)$时，${F_{2}}\left( x \right)>0$；当$x\in \left( 1,+\infty  \right)$时，${F_{2}}\left( x \right)<0$

故函数$f\left( x \right)$的单调递增区间为$\left( 0,1 \right)$，单调递减区间为$\left( 1,+\infty  \right)$

（4）定义域为：$\left( -\infty ,+\infty  \right)$

${f_{1}}\left( x \right)={{\operatorname{e}}^{x}}-1-x$，可得：${f_{1}}^{'} \left( x \right)={{\operatorname{e}}^{x}}-1$，${f_{1}}^{'} \left( 0 \right)=0$

故${f_{1}}\left( x \right)$在区间$\left( -\infty ,0 \right)$单调递减；在区间$\left( 0,+\infty  \right)$单调递增；

${f_{2}}\left( x \right)={{\operatorname{e}}^{x}}-1-x-\cfrac{{{x}^{2}}}{2}$，可得：${f_2}^{'} \left( x \right)={\operatorname{e}}^x-1-x={f_{1}}\left( x \right)$，${f_2}^{'} \left( 0 \right)=0$

故${f_2}^{'} {{\left( x \right)}_{\min }}={f_2}^{'} \left( 0 \right)=0$

故${f_{2}}\left( x \right)$在区间$\left( -\infty ,+\infty  \right)$单调递增；

${f_{k}}\left( x \right)$经过$k$次求导后为$y={{\operatorname{e}}^{x}}-1$

当$k$为奇数时，${f_{k}}\left( x \right)$在区间$\left( -\infty ,0 \right)$单调递减；在区间$\left( 0,+\infty  \right)$单调递增；

当$k$为偶数时，${f_{k}}\left( x \right)$在区间$\left( -\infty ,+\infty  \right)$单调递增；













## 类型八：三阶正负确定
{{<alert title="思路：" color="secondary">}}
- 定义域和对称性$\Rightarrow $三阶正负确定$\Rightarrow $二阶单调区间$\Rightarrow $二阶零点$\Rightarrow $一阶单调区间$\Rightarrow $一阶零点$\Rightarrow $原函数单调区间；

- 每次求导后的基本操作有：分式通分$\Rightarrow $根式有理化$\Rightarrow $因式分解

- 原则：尽可能提前讨论，也就是说不求导就能确定的单调区间就不要留到求导，同理第一次求导就能确定零点或单调或正负确定的情况就不要留到第二次求导
{{</alert>}}

【例题】求下列函数的单调区间

（1）$f\left( x \right)=\cfrac{x+1}{{{\operatorname{e}}^{x}}}+\ln x$

（2）$f\left( x \right)={{\ln }^{2}}\left( 1+x \right)-\cfrac{{{x}^{2}}}{1+x}$

【解析】
（1）定义域为：$\left( 0,\infty  \right)$

记${f_{1}}\left( x \right)=f^{'} \left( x \right)=\cfrac{{{\operatorname{e}}^{x}}-{{x}^{2}}}{x{{\operatorname{e}}^{x}}}$

记${F_{2}}\left( x \right)=x{{\operatorname{e}}^{x}}{{f}_{1}}\left( x \right)={{\operatorname{e}}^{x}}-{{x}^{2}}$

记${f_{2}}\left( x \right)={{F}_{2}}^{'} \left( x \right)={{\operatorname{e}}^{x}}-2x$

记${f_{3}}\left( x \right)={f_{2}}^{'} \left( x \right)={{\operatorname{e}}^{x}}-2$，${f_{3}}\left( \ln 2 \right)=0$

则有：当$x\in \left( 0,\ln 2 \right)$时，${f_{3}}\left( x \right)<0$；当$x\in( \ln 2,+\infty)$时，${f_3}(x)>0$

可得：${f_{2}}\left( x \right)$在区间$\left( \ln 2,+\infty  \right)$单调递增，在区间$\left( 0,\ln 2 \right)$上单调递减

可得：$f_2( x )_{\min} = f_2 ( \ln 2 ) = 2( 1 - \ln 2 ) > 0$，即${f_2}(x)>0$

可得：${F_{2}}\left( x \right)$在区间$\left( 0,\infty  \right)$上单调递增

又${F_{2}}\left( 0 \right)=1$，可得： ${F_{2}}\left( x \right)>0$，即${f_{1}}\left( x \right)>0$

综上，函数$f\left( x \right)$的单调递增区间为$\left( 0,+\infty  \right)$

（2）定义域为：$\left( -1,\infty  \right)$

记${f_{1}}\left( x \right)=f^{'} \left( x \right)=\cfrac{2\left( 1+x \right)\ln \left( 1+x \right)-{x^{2}}-2x}{{{\left( 1+x \right)}^{2}}}$，${f_{1}}\left( 0 \right)=0$

记${F_{2}}\left( x \right)=2\left( 1+x \right)\ln \left( 1+x \right)-{{x}^{2}}-2x$，${F_{2}}\left( 0 \right)=0$

记${f_{2}}\left( x \right)={F_{2}}^{'} \left( x \right)=2\ln \left( 1+x \right)-2x$，${f_{2}}\left( 0 \right)=0$

记${f_{3}}\left( x \right)={f_{2}}^{'} \left( x \right)=-\cfrac{2x}{1+x}$，${f_{3}}\left( 0 \right)=0$

则有：当$x\in \left( -1,0 \right)$时，${f_{3}}\left( x \right)>0$；当$x\in \left( 0,+\infty  \right)$时，${f_{3}}\left( x \right)<0$

可得：${{f}_{2}}\left( x \right)$在区间$\left( -1,0 \right)$单调递增，在区间$\left( 0,+\infty  \right)$上单调递减

可得：$f_2(x)_{\max} = f_2(0) = 0$，即$f_2 (x) \le 0$

可得：${F_{2}}\left( x \right)$在区间$\left( -1,\infty  \right)$上单调递减

又${F_{2}}\left( 0 \right)=0$，可得：当$x\in \left( -1,0 \right)$时，${F_{2}}\left( x \right)>0$；当$x\in \left( 0,+\infty  \right)$时，${F_{2}}\left( x \right)<0$

综上，函数$f\left( x \right)$的单调递增区间为$\left( -1,0 \right)$，单调递减区间为$\left( 0,+\infty  \right)$








