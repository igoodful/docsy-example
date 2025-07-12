---
title: 线
description: Line
date: 2023-10-06
weight: 200


---
{{< alert >}}

1. Line( <Point>, <Point> )

2. Line( <Point>, <Parallel Line> )

3. Line( <Point>, <Direction Vector> )

{{< /alert >}}

{{<alert>}}


{{</alert>}}

## 创建直线

- Line[Point A, Point B]：建立过点 A 和 B 的直线。

- Line[Point, Line]：建立过点 A 且平行于直线 g 的直线。

- Line[Point, Vector v]：建立过点 A 且方向为 v 的直线。

## 线段

Segment[Point A, Point B]：建立点 A 和点 B 之间的线段。

Segment[Point A, Number a]：建立以点 A 为起点，线段长为 a 的线段。

## 向量

Vector[Point A, Point B]：从点 A 到点 B 的向量。

Vector[Point]：点 A 的位置向量。

## 射线
Ray[Point A, Point B]：建立起点为 A 过 B 点的射线。

Ray[Point, Vector v]：建立起点为 A 并方向向量为 v 的射线。


## 垂线
Perpendicular[Point, Line]：建立过点 A 且垂直于直线 g 的直线。

Perpendicular[Point, Vector]：建立过点 A 且垂直于向量 v 的直线。

## 中垂线
PerpendicularBisector[Point A, Point B]：建立线段 AB 的垂直平分线。

PerpendicularBisector[Segment]：建立线段的垂直平分线。

## 角平分线
AngleBisector[Point A, Point B, Point C]：生成角 ABC 的角平分线。

AngleBisector[Line g, Line h]：生成直线 g 和 h 的角平分线。

## 渐近线
Asymptote[Hyperbola]：建立双曲线 h 的两条渐近线。

## 切线
Tangent[Point, Conic]：建立圆锥曲线 c 过点 A 的所有切线。

Tangent[Line, Conic]：建立圆锥曲线 c 的所有平行于直线 g 的切线。

Tangent[Number a, Function]：建立 f(x)在 x=a 的切线。

Tangent[Point A, Function]：建立 f(x)在 x=x(A)的切线。

## 对称轴
Axes[Conic]：建立圆锥曲线 c 的对称轴。

## 多边形
Polygon[Point A, Point B, Point C,...]：生成由给定点 A，B，C……所围成的多边形。

Polygon[Point A, Point B, Number n]：以线段 AB 为边长，生成由 n 个顶点的正多边形。


**直线斜率**：Slope(Line)





## 射线

1. Ray( <Start Point>, <Point> )

2. Ray( <Start Point>, <Direction Vector> )






