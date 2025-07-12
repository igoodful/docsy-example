---
title: 基本结论证明
description: vector
date: 2025-05-18
weight: 100
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;

$ \rightleftharpoons $


{{<alert color="danger" title="注意" >}}




{{</alert>}}





## 1. 概念

方向：

大小：



我们在物理中已经学过速度的有关知识，知道表征速度需要两个参数：（1）速度的大小。（2）速度的方向。

一些物理量虽然具有方向性，但只能使用算术加法，不符合向量加法，所以不是向量，如角移、电压及电流。

电流之所以不是矢量，是因为虽然电流有大小和方向，但它不满足矢量的“平行四边形法则”加法运算。简单来说，电流的叠加不是像矢量那样按照平行四边形定则来计算的。“电流”有方向但并不遵守平行四边形法则，故不能称作矢量。电流不是矢量，是因为它不能像矢量那样相加减。


两个起点不一样的向量，只要数值相等，方向相同，就可以称为是同一个向量。这样的向量被称为自由向量。在数学中，一般只研究自由向量，并且数学中所指的向量就是指自由向量。也就是只要数值以及方向一样，即可视为同一向量，与向量的起始点并无关系。


#### 1.1 向量的概念

（1）同时有大小和方向的量我们称为**向量**（也称为矢量）。

举例说明：力
、速度
、加速度
、位移
、动量
、冲量
、电场强度
、磁场强度

向量有两个本质属性：

- 向量的大小，也叫向量的模；

- 向量的方向。

（2）只有大小没有方向的量叫做**标量**。

举例说明：功
、功率
、动能
、势能
、路程
、电流
、电压
、电阻
、质量
、密度
、长度
、时间

#### 1.2 向量的表示


几何表示：用带箭头的线段(即**有向线段**) 来直观地表示向量
- 有向线段的长度表示向量的大小
- 有向线段的方向表示向量的方向
- 有向线段不带箭头的端点称为向量的始点(或起点)
- 有向线段带箭头的端点称为向量的终点

![](/maths_geometry/7.png)

（1）用两个大写字母表示：将始点为$A$终点为$B$的有向线段所表示的向量记为$\overrightarrow{AB}$

（2）用一个小写字母来表示向量
- 在书写时，用带箭头的小写字母如$\overrightarrow{a}、\overrightarrow{b}$等来表示向量
- 在印刷时，通常用加粗的斜体小写字母如$\bm{a}、\bm{b}$等来表示向量;



坐标表示



## 特殊向量


#### 1.3 零向量

因为零向量的方向不确定，所以通常规定零向量与任意向量平行

我们约定，所有的零向量相等。


#### 1.4 单位向量

长度（或模）为$1$的向量叫做单位向量。每个方向上都有一个单位向量

与非零向量$\overrightarrow{a}$同方向的单位向量叫做向量$\overrightarrow{a}$的单位向量，记作：$\overrightarrow{e} = \cfrac{ \overrightarrow{a} }{ |\overrightarrow{a} | } $


对于任意向量$ \overrightarrow{a} $，不论方向如何，若其数值为单位长度，则称其为$ \overrightarrow{a} $方向上的单位向量

#### 1.5 相反向量


#### 1.6 相等向量


#### 1.7 方向向量




## 向量运算与平面向量基本定理


#### 加、减、数乘、数量积


#### 数量积


向量的夹角

投影


&#128311;证明如下结论：

（1）$ \overrightarrow{a}^2 = |\overrightarrow{a}|^2 $

（2）$ (\lambda	 \overrightarrow{a}) \cdot \overrightarrow{b} = \lambda	 ( \overrightarrow{a} \cdot \overrightarrow{b} ) $

（3）$ \overrightarrow{a} \cdot \overrightarrow{b} = \overrightarrow{b} \cdot \overrightarrow{a} $

（4）$ (\overrightarrow{a} + \overrightarrow{b}) \cdot \overrightarrow{c} = \overrightarrow{a} \cdot \overrightarrow{c} + \overrightarrow{b} \cdot \overrightarrow{c} $


{{<alert color="primary">}}

【**思路**】

（1） 向量数量积方程: $ \overrightarrow{a} \cdot \overrightarrow{b} = \left|\overrightarrow{a} \right|  \left|\overrightarrow{b} \right|  \cos \theta $

{{</alert>}}

【**解析**】（1）根据向量数量积定义，可得：$  \overrightarrow{a}^2 = \overrightarrow{a} \cdot \overrightarrow{a} = \left|\overrightarrow{a} \right| \cdot \left|\overrightarrow{a} \right|  \cos 0 = \left|\overrightarrow{a} \right|^2 $

（2）根据向量数量积定义，可得：

$\begin{aligned}
(\lambda \overrightarrow{a}) \cdot \overrightarrow{b} &=  \left| \lambda \overrightarrow{a} \right| \cdot \left|\overrightarrow{b} \right|  \cos \theta \\\\[2ex]
&=  \left| \lambda  \right| \cdot \left|  \overrightarrow{a} \right| \cdot \left|\overrightarrow{b} \right|  \cos \theta \\\\
\end{aligned}
$

同理，可得：$\lambda ( \overrightarrow{a}\cdot \overrightarrow{b} )  = \lambda \left|\overrightarrow{a} \right|  \left|\overrightarrow{b} \right|  \cos \theta $


（3）根据向量数量积定义，可得： $ \overrightarrow{a} \cdot \overrightarrow{b} = \left|\overrightarrow{a} \right|  \left|\overrightarrow{b} \right|  \cos \theta $

同理，可得： $ \overrightarrow{b} \cdot \overrightarrow{a} = \left|\overrightarrow{b} \right|  \left|\overrightarrow{a} \right|  \cos \theta $


#### 平面向量基本定理


## 应用


向量的夹角

#### 向量平行

也叫共线


#### 向量垂直


#### 定比分点


#### 方向向量


#### 法向量





















$ e = \cfrac{c}{a}  $

又$ a^2 = b^2 + c^2 $，可得：$ e = \cfrac{c}{a} = \sqrt{1 - \cfrac{b^2}{a^2} } $


- 离心率$e$越接近$1$，椭圆就越扁。
- 离心率$e$越接近$0$，椭圆就越接近于圆。
- 若$c=0$时，则$a=b$，此时椭圆就变成了圆。
- 椭圆离心率范围：$(0,1)$


离心率相同，则形状相似


---

&#128311;已知椭圆$\Omega$：$ \cfrac{x^2}{a^2} + \cfrac{y^2}{b^2} = 1 $（其中$0<b<a$）

![1.svg](/maths_geometry/1.svg)

（1）若点$P(x_0,y_0)$在椭圆$\Omega$上

（2）若点$P(x_0,y_0)$在椭圆$\Omega$内

（3）若点$P(x_0,y_0)$在椭圆$\Omega$外

{{<alert title="提示" >}}

- 点在椭圆上
  - $ \left| x_2 - x_1 \right| = \sqrt{ (x_1 + x_2)^2 - 4 x_1 x_2 }  $
  - $ \left| y_2 - y_1 \right| = \sqrt{ (y_1 + y_2)^2 - 4 y_1 y_2 }  $
  - $ a^2 = b^2 + c^2 $：只需两个独立的关于这些变量的方程即可求出，只需一个方程即可求出离心率。（三个未知数三个方程，但这里已经有了一个方程，故只需两个独立的方程即可）
  - 如何刻画点在椭圆上

{{</alert>}}


【**解析**】（1）若点$P(x_0,y_0)$在椭圆$\Omega$上，则点满足椭圆方程，可得：$ \cfrac{{x_0}^2}{a^2} + \cfrac{{y_0}^2}{b^2} = 1 $



























