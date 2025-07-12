---
title: geogebra
description: geogebra
date: 2025-05-20
weight: 50
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


- https://geogebra.github.io/docs/manual/en/commands/Geometry_Commands/







## 平移：`translate`


```bash

```




## 旋转：`rotate`


```bash
rotate( <Object>, <Angle> )
rotate( <Object>, <Angle>, <Point> )
rotate( <Object>, <Angle>, <Axis of Rotation>)
rotate( <Object>, <Angle>, <Point on Axis>, <Axis Direction or Plane> )


```






## 几何


####  点：`point`

```bash
point( <Object> )
point( <Object>, <Parameter> )
point( <Point>, <Vector> )
point( <List> )

```

```viz-dot
digraph "point" {
        rankdir=TB;
node [
        fontsize = "12";
        shape = octagon;
];

"point"->"对象上任意一点";
"point"->"起点与向量平移所确定的终点";
"point"->"两个数的列表";
"point"->"过三点";

}
```


举例说明

```bash
point( <Object> )
point( <Object>, <Parameter> )
point( <Point>, <Vector> )
point({1,2})

```

#### 重心：`barycenter`

```bash
barycenter( <List of Points>, <List of Weights> )

```

举例说明
```bash
barycenter( {(2, 0), (0, 2), (-2, 0), (0, -2)}, {1, 1, 1, 1} )

barycenter( {(2, 0), (0, 2), (-2, 0), (0, -2)}, {2, 1, 1, 1} )


```



#### 中点：`midpoint`

```bash
midpoint( <Point>, <Point> )
midpoint( <Segment> )
midpoint( <Interval> )
midpoint( <Conic> )
midpoint( <Quadric> )

```

```viz-dot
digraph "midpoint" {
        rankdir=TB;
node [
        fontsize = "12";
        shape = octagon;
];

"midpoint"->"两个点之间的中点";
"midpoint"->"线段的中点";
"midpoint"->"区间之间的中点值";
"midpoint"->"圆锥曲线的中心坐标";
"midpoint"->"二次曲面的中点坐标";

}
```


```bash
# 两点之间的中点
midpoint((1, 1), (5, 1))

# 线段中点坐标
Let s = Segment((1, 1), (1, 5))
midpoint(s)

# 区间中点
midpoint(2 < x < 4)

# 圆锥曲线的中心坐标
midpoint(x^2 + y^2 = 4)

# 二次曲面的中点坐标
midpoint(x^2 + y^2 + z^2 = 1)



```


#### 交点：`intersect`



```bash
intersect( <Line> , <Object> )创建直线与平面、线段、多边形、圆锥曲线等的交点。

intersect( <Plane> , <Object> )创建平面与线段、多边形、圆锥曲线等的交点。

intersect( <Conic>, <Conic> )创建两个圆锥曲线的交点

intersect( <Plane>, <Plane> )创建两个平面的相交线

intersect( <Plane>, <Polyhedron> )创建平面和多面体的多边形交集。

intersect( <Sphere>, <Sphere> )创建两个球体的圆相交

intersect( <Plane>, <Quadric> )创建平面和二次曲面（球体、圆锥体、圆柱体……）的圆锥交点

intersect ( <函数>, <函数>, <起始 x 值>, <结束 x 值> )

```




#### 线段：`segment`

```bash
segment( <Point>, <Point> )
segment( <Point>, <Length> )

```

#### 射线：`Ray`

```bash
Ray( <Start Point>, <Point> )
Ray( <Start Point>, <Direction Vector> )

```

#### 直线：`line`

```bash
line( <Point>, <Point> )
line( <Point>, <Parallel Line> )
line( <Point>, <Direction Vector> )


```

```viz-dot
digraph "line" {
        rankdir=TB;
node [
        fontsize = "12";
        shape = octagon;
];

"line"->"过两点的直线";
"line"->"过一点且和已知直线平行的直线";
"line"->"过一点且和已知向量平行的直线";

}
```

#### 中垂线：`perpendicularBisector`

```bash
perpendicularBisector( <Segment> )
perpendicularBisector( <Point>, <Point> )
perpendicularBisector( <Point>, <Point>, <Direction>)


```

```viz-dot
digraph "perpendicularBisector" {
        rankdir=TB;
node [
        fontsize = "12";
        shape = octagon;
];

"perpendicularBisector"->"两点之间的中垂线";
"perpendicularBisector"->"线段的中垂线";
"perpendicularBisector"->"两点之间的中垂线，且规定方向";

}
```


#### 垂线：`perpendicularLine`

```bash
perpendicularLine( <Point>, <Line> )
perpendicularLine( <点>, <直线> )
perpendicularLine( <点>, <线段> )
perpendicularLine( <点>, <向量> )
perpendicularLine( <点>, <平面> )
perpendicularLine( <直线>, <直线> )

perpendicularLine( <点>, <方向>, <方向> )
perpendicularLine( <点>, <直线>, <平面xOy｜3D 空间> )


```

```viz-dot
digraph "perpendicularLine" {
        rankdir=TB;
node [
        fontsize = "12";
        shape = octagon;
];

"perpendicularLine"->"过点作直线的垂线";
"perpendicularLine"->"过点作线段的垂线";
"perpendicularLine"->"过点作向量的垂线";
"perpendicularLine"->"过点作平面的垂线";

}
```

#### 角平分线：`anglebisector`

```bash
anglebisector( <Line>, <Line> )
anglebisector( <Point>, <Point>, <Point> )


```

```viz-dot
digraph "anglebisector" {
        rankdir=TB;
node [
        fontsize = "12";
        shape = octagon;
];

"anglebisector"->"两条直线间的两条角平分线";
"anglebisector"->"三点之间夹角的角平分线";


}
```

举例说明
```bash
# 两条直线之间的两条角平分线
anglebisector( x + y = 1, x - y = 2 )

# 三点构成的一个角平分线
anglebisector( (1, 1), (4, 4), (7, 1) )


```

#### 切线：`tangent`

```bash
tangent( <点>, <圆锥曲线> )
tangent( <点>, <函数> )
tangent( <曲线上的点>, <曲线> )
tangent( <横坐标x值>, <函数> )
tangent( <直线>, <圆锥曲线> )
tangent( <圆锥曲线>, <圆锥曲线> )


```

```viz-dot
digraph "tangent" {
        rankdir=TB;
node [
        fontsize = "12";
        shape = octagon;
];

"tangent"->"两条直线间的两条角平分线";
"tangent"->"三点之间夹角的角平分线";


}
```


```bash

# 在x = x(A)处创建函数的切线
Tangent((1, 0), x^2)

# 创建两个圆的共同切线
Tangent(x^2 + y^2 = 4, (x - 6)^2 + y^2 = 4)

# 在给定点处创建隐式曲线的切线
Tangent((1,1), x^2+y^2=1))

# 创建与给定线平行的圆锥截面的所有切线
Tangent(y = 4, x^2 + y^2 = 4)

```




#### 斜率：`slope`

```bash
slope( <Line> )


```




#### 多边形：`polygon`



```bash
polygon( <点列> )
polygon( <点>, ..., <点> )
polygon( <点>, <点>, <顶点数> )
polygon( <点>, <点>, <顶点数>, <方向> )


```

举例说明

```bash
polygon((1, 1), (2, 1), 4)


```



####  距离：`distance`

```viz-dot
digraph "distance" {
        rankdir=TB;
node [
        fontsize = "12";
        shape = octagon;
];

"distance"->"点与对象";
"distance"->"线线距";
"distance"->"面面距";

}
```


```bash
distance( <Point>, <Object> )
distance( <Line>, <Line> )
distance( <Plane>, <Plane> )

```


####  面积：`area`

```viz-dot
digraph "distance" {
        rankdir=TB;
node [
        fontsize = "12";
        shape = octagon;
];

"area"->"点与对象";
"area"->"多边形面积";
"area"->"圆锥曲线的面积";

}
```


```bash
area( <Point>, ...​, <Point> )
area( <Polygon> )
area( <Conic> )


```


```bash


# 四个点构成的多边形的面积
area((0, 0), (3, 0), (3, 2), (0, 2))

# 圆面积
area(x^2 + y^2 = 2)


```

#### 角度：`Angle`

```viz-dot
digraph "Angle" {
        rankdir=TB;
node [
        fontsize = "12";
        shape = octagon;
];

"Angle"->"圆心与半径";
"Angle"->"圆心与线段半径";
"Angle"->"圆心与过点";
"Angle"->"过三点";

}
```


```bash
Angle<对象>）
Angle( <向量1>, <向量2> )
Angle(<线>，<线>)
Angle(<线>，<平面>）
Angle( <平面>, <平面> )
Angle(<点>，<顶点>，<点>)
Angle(<点>，<顶点>，<角度>)
Angle(<点>，<点>，<点>，<方向>)

```


```bash
# 返回圆锥截面长轴的扭曲角度,即默认长轴为x轴，逆时针旋转多少角度
Angle(x²/4+y²/9=1)

# 向量与x轴正半轴的角度
Angle(Vector((1, 1)))

# x轴正半轴到坐标原点与点之间的角度
Angle((1, 1))

# 弧度制转为角度
Angle(20)

# 多边形的每个内角
Angle(Polygon((4, 1), (2, 4), (1, 1)))

# 返回向量1到向量2的角度
Angle(Vector((1, 1)), Vector((2, 5)))

# 返回两条线的方向向量之间的角度
Angle(y = x + 2, y = 2x + 3)

# 返回线和平面之间的角度。
Angle(Line((1, 2, 3),(-2, -2, 0)), z = 0)

# 返回两个给定平面之间的角度
Angle(2x - y + z = 0, z = 0)

# 返回由给定点定义的角度
Angle((1, 1), (1, 4), (4, 2))

# 返回从点到顶点绘制的大小为α的角度
Angle((0, 0), (3, 3), 30°)

Angle((1, -1, 0),(0, 0, 0),(-1, -1, 0), zAxis)


```




## 圆锥曲线


####  圆：`circle`

```viz-dot
digraph "circle" {
        rankdir=TB;
node [
        fontsize = "12";
        shape = octagon;
];

"circle"->"圆心与半径";
"circle"->"圆心与线段半径";
"circle"->"圆心与过点";
"circle"->"过三点";

}
```


```bash
circle( <Point>, <Radius Number> )
circle( <Point>, <Segment> )
circle( <Point>, <Point> )
circle( <Point>, <Point>, <Point> )

```

#### 内切圆：`Incircle`

```bash
Incircle( <Point>, <Point>, <Point> )

```


#### 外接圆：`Semicircle`

```bash
Semicircle( <Point>, <Point> )

```



#### 半圆：`Semicircle`

```bash
Semicircle( <Point>, <Point> )

```



#### 圆弧：`circulararc`

```bash
circulararc( <圆心>, <点>, <点> )

```

默认逆时针从$A$到$B$且以圆心到A点为半径旋转所得的圆弧
```bash
circulararc((0,0), A, B )


```

#### 圆扇形区域：`circularsector`

```bash
circularsector( <圆心>, <点>, <点> )

```

默认逆时针从$A$到$B$且以圆心到A点为半径旋转所得的扇形
```bash
circularsector((0,0), A, B )

```


#### 椭圆：`Ellipse`

```bash
Ellipse( <Focus>, <Focus>, <Semimajor Axis Length> )
Ellipse( <Focus>, <Focus>, <Segment> )
Ellipse( <Focus>, <Focus>, <Point> )

#
椭圆( <焦点>, <焦点>, <主半轴长> )
椭圆( <焦点>, <焦点>, <线段> )
椭圆( <焦点>, <焦点>, <点> )

```

#### 双曲线：`Hyperbola`

```bash
Hyperbola( <焦点>, <焦点>, <主半轴长> )
Hyperbola( <焦点>, <焦点>, <线段> )
Hyperbola( <焦点>, <焦点>, <点> )

```

#### 抛物线：`Parabola`

```bash
Parabola( <点>, <直线> )

```


#### 中心：`center`

```bash
# 返回圆、椭圆或双曲线的中心。
center( <Conic> )

# 创建二次曲面（例如球体、圆锥体等）的中心
center( <Quadric> )

```

#### 焦点：`focus`

```bash
focus( <Conic> )

```

#### 顶点：`Vertex`

```bash
# 返回圆锥曲线的（所有）顶点
Vertex( <Conic> )

# 返回多边形的（所有）顶点
Vertex( <Polygon> )

# 返回多边形的第 n个顶点
Vertex( <Polygon>, <Index n> )

# 返回边界的交点
Vertex( <Inequality> )

```



#### 轴线：`axes`

- 长轴和短轴

```bash
axes( <Conic> )
axes( <Quadric> )

```


#### 长轴：`majoraxis`

- 长轴

```bash
majoraxis( <Conic> )

```

#### 短轴：`minoraxis`

- 短轴

```bash
minoraxis( <Conic> )

```


#### 准线：`directrix`

```bash
directrix( <Conic> )

```


#### 半径：`radius`

```bash

radius( <Conic> )
```

#### 周长：`Perimeter`

```bash
# 如果给定的圆锥曲线是圆或椭圆，此命令返回其周长
Perimeter( <Conic> )

# 返回多边形的周长
Perimeter( <Polygon> )

# 如果给定的轨迹是有限的，此命令返回其近似周长
Perimeter( <Locus> )

```

#### 周长：`Circumference`

```bash
# 如果给定的圆锥曲线是圆或椭圆，此命令返回其周长
Circumference(Conic)
```


#### 主半轴长：`SemiMajorAxisLength`

```bash
SemiMajorAxisLength( <Conic> )

```

#### 副半轴长：`SemiMinorAxisLength`

```bash
SemiMinorAxisLength( <Conic> )

```


## 代数


#### 二项式系数：`nCr`

```bash
nCr( <Number n>, <Number r> )

```

## 几何变换

#### 镜像

```bash
# 对称点
Reflect( <Object>, <Point> )

# 对称轴
Reflect( <Object>, <Line> )

# 对称圆
Reflect( <Object>, <Circle> )

# 对称平面
Reflect( <Object>, <Plane> )

对称( <对象>, <点> )
对称( <对象>, <直线> )
对称( <对象>, <平面> )
对称( <对象>, <圆> )

```

#### 平移: `Translate`


```bash
# 通过矢量平移几何对象,当平移多边形时，也会创建变换后的新顶点和线段。
Translate( <Object>, <Vector> )

# 将向量平移到起点。
Translate( <Vector>, <Start Point> )

平移( <对象>, <向量> )
平移( <向量>, <起始点> )

```





#### 旋转: `Rotate`

```bash
# 将几何对象绕轴原点旋转一定角度。
Rotate( <Object>, <Angle> )

# 围绕给定点按一定角度旋转几何对象。
Rotate( <Object>, <Angle>, <Point> )

# 围绕给定的旋转轴按一定角度旋转几何对象。
Rotate( <Object>, <Angle>, <Axis of Rotation>)

# 将几何对象绕给定点和轴方向或平面定义的轴旋转一定角度。
Rotate( <Object>, <Angle>, <Point on Axis>, <Axis Direction or Plane> )


旋转( <对象>, <度|弧度> )
旋转( <对象>, <度|弧度>, <点> )
旋转( <对象>, <度|弧度>, <旋转轴> )
旋转( <对象>, <度|弧度>, <轴上的点>, <轴向或平面> )

```




#### 伸缩

```bash
Stretch( <Object>, <Vector> )
Stretch( <Object>, <Line>, <Ratio> )

伸缩( <对象>, <向量> )
伸缩( <对象>, <直线>, <比> )
```


## 3D





####  球面：`rotate`


```bash
rotate( <Object>, <Angle> )
rotate( <Object>, <Angle>, <Point> )
rotate( <Object>, <Angle>, <Axis of Rotation>)
rotate( <Object>, <Angle>, <Point on Axis>, <Axis Direction or Plane> )


```

## 函数

#### 解方程：`Solve`


```bash
Solve( <Equation in x> )



```


#### 最小值：`Min`


```bash
Min( <Number>, <Number> )
Min( <List> )
Min( <Interval> )
Min( <Function>, <Start x-Value>, <End x-Value> )

最小值( <区间> )
最小值( <数字>, <数字> )
最小值( <列表> )
最小值( <函数>, <x起始值>, <x终止值> )
最小值( <数据列表>, <频数列表> )

```

```bash
Min(12, 15)
Min({-2, 12, -23, 17, 15})
Min(2 < x < 3)

# 函数应连续，且在区间内只有一个局部最小值点（无局部最大值）。
Min(exp(x) x^3,-4,-2)

```


#### 最大值：`Max`


```bash
Max( <Number>, <Number> )
Max( <List> )
Max( <Interval> )
Max( <Function>, <Start x-Value>, <End x-Value> )

最小值( <区间> )
最小值( <数字>, <数字> )
最小值( <列表> )
最小值( <函数>, <x起始值>, <x终止值> )
最小值( <数据列表>, <频数列表> )

```

```bash
Max(12, 15)
Max({-2, 12, -23, 17, 15})
Max(2 < x < 3)

# 该函数应连续，且在区间内只有一个局部最大值点（无局部最小值）。
Max(exp(x)x^2,-3,-1)

```



#### 泰勒展开式：`TaylorPolynomial`


```bash
TaylorPolynomial( <Function>, <x-Value>, <Order Number> )
TaylorPolynomial( <Expression>, <x-Value>, <Order Number> )
TaylorPolynomial( <Expression>, <Variable>, <Variable Value>, <Order Number> )


泰勒公式( <函数>, <横坐标x值>, <阶数> )
#CAS 专有指令:
泰勒公式( <代数式>, <横坐标x值>, <阶数> )
泰勒公式( <代数式>, <变量>, <变量值>, <阶数> )
```

```bash
TaylorPolynomial(x^2, 3, 1)

TaylorPolynomial(x^2, a, 1)

TaylorPolynomial(x^3 sin(y), x, 3, 2)

TaylorPolynomial(x^3 sin(y), y, 3, 2)

```


## if

```bash
If( <Condition>, <Then> )
If( <Condition>, <Then>, <Else> )
If( <Condition 1>, <Then 1>, <Condition 2>, <Then 2>, …​ , <Else (optional)> )


如果( <条件>, <结果> )
如果( <条件>, <结果>, <否则> )
```


```bash
# if
If(n==3, x + y = 4)

# if else
If(n==3, x + y = 4, x - y = 4)

# if elseif else
If(a == 1, "Matthew", a ==  2,"Larry", a ==  3, "Vivian", "Alex")

```


## 1. 序列：sequence

```bash
Sequence( <End Value > )
Sequence( <Start value k >, <End value n > )
Sequence( <Start value k>, <End value n>, <Increment> )

Sequence( <Expression>, <Variable k>, <Start Value a>, <End Value b> )
Sequence( <Expression>, <Variable k>, <Start Value a>, <End Value b>, <Increment> )

# 序列
序列( <终止值> )
序列( <起始值>, <终止值> )
序列( <起始值>, <终止值>, <增量> )
序列( <表达式>, <变量>, <起始值>, <终止值> )
序列( <表达式>, <变量>, <起始值>, <终止值>, <增量> )

```


- 举例说明

```bash
# {1, 2, 3, 4}
Sequence(4)

# {2, 4, 8, 16}
2^Sequence(4)

# { 7, 8, 9, 10, 11, 12, 13 }
Sequence(7,13)

# { 18, 17, 16, 15, 14 }
Sequence(18,14)

# { -5, -4, -3, -2, -1, 0, 1, 2, 3, 4, 5 }
Sequence(-5, 5)

# {7, 9, 11, 13}
Sequence(7,13,2)

# {7, 11}
Sequence(7,13,4)

# {(2, 1), (2, 2), (2, 3), (2, 4), (2, 5)}
Sequence((2, k), k, 1, 5)

# { x, x², x³, x⁴, x⁵, x⁶, x⁷, x⁸, x⁹, x¹⁰ }
Sequence(x^k, k, 1, 10)

# {(2, 1), (2, 1.5), (2, 2), (2, 2.5), (2, 3)}
Sequence((2, k), k, 1, 3, 0.5)


#########
sequence( <表达式>, <变量>, <起始值>, <终止值>, <增量> )

sequence((i,0),i,0,10,1)

sequence((n,n),n,0,10,1)

sequence(A + k (B - A) / 7, k, 1, 6)

sequence(多边形((k,0),(k+1,0),4),k,0,18,2)

sequence(圆周((i, 0), 1), i, 1, 19, 2)

sequence(平移((0,0),(i,0)),i,1,10,1)


```

#### 点

点坐标规律

```bash
sequence((n,n),n,0,10,1)

```

- s是一个区间为1到10, 增量为1的滑动条
```bash
sequence((i, i), i, 0, s)


```





#### 平移

平移规律

```bash
sequence(translate((0,0),(i,0)),i,1,10,1)


```

#### 线段

```bash
sequence(segment((0,0),(i,i+6)),i,1,10,1)

sequence(segment((i, 0), (0, i)), i, 1, 20, 1)

```


#### 等分线段

- $n$等分线段$AB$

```bash
sequence(A + k (B - A) / 7, k, 1, 6)


```


#### 等分圆

- A为圆山任意一点
- s是一个区间为1到20, 增量为1的滑动条
- k为变量
```bash
sequence(rotate(A,k 2pi/s),k,1,s)

```


#### 向量

- O为坐标原点
- A为圆山任意一点
- s是一个区间为1到20, 增量为1的滑动条
- k为变量
```bash
sequence(rotate(vector(O, A), k * 2π / s), k, 1, s)



```

#### 多边形

- `polygon((i, j), (i + 1, j), 6)`构造多边形
```bash
sequence(polygon((i, 0), (i + 1, 0), 6), i, 0, 18, 2)

```


#### 多边形序列

- O为坐标原点
- A为圆山任意一点
- `polygon((i, j), (i + 1, j), 6)`构造多边形
```bash
sequence(sequence(polygon((i, j), (i + 1, j), 6), i, 1, 18, 2), j, 1, 18, 2)


```

#### 球面

- `polygon((i, j), (i + 1, j), 6)`构造多边形
```bash
sequence(球面((i, j, k), 0.5), i, 0, 5)


```

序列(序列(序列(球面((i, j, k), 0.5), i, 0, 5), j, 0, 5), k, 0, 5)



## 迭代：`Iteration`

```bash
Iteration( <Function>, <Start Value>, <Number of Iterations> )
Iteration( <Expression>, <Variable Name>, …​, <Start Values>, <Number of Iterations> )

```

- 举例说明
```bash
# 函数、初始值、迭代次数
Iteration(x^2, 3, 2)

# 表达式、变量、初始值、迭代次数
Iteration(a^2+1,a,{1},5)

# 表达式、变量、初始值、迭代次数
Iteration(a+b,a,b,{1,1},19)

```


## 迭代序列：`IterationList`

```bash
IterationList( <Function>, <Start Value>, <Number of Iterations> )
IterationList( <Expression>, <Variable Name>, …​, <Start Values>, <Number of Iterations> )

```

- 举例说明
```bash
# 函数、初始值、迭代次数
IterationList(x^2, 3, 2)

#
f(k,a)=(k+1)*a
IterationList(f, {3, 6}, 4)

# 表达式、变量、初始值、迭代次数
A = (0,0)
B = (8,0)
IterationList(Midpoint(A, C), C, {B}, 3)

# 表达式、变量、初始值、迭代次数
IterationList(a + b, a, b, {1, 1}, 5)

# 表达式、变量、初始值、迭代次数
IterationList(a+b,a,b,{1,2},19)

```






## 映射：

























