---
title: 圆锥曲线
description: Conic
date: 2023-10-06
weight: 300


---







## 创建圆

- Circle[Point M, Number r]：建立圆心为 M 半径为 r 的圆。

- Circle[Point M, Segment]：建立圆心为 M 半径为 s 的圆（参考先确定半径再画圆的绘图工具）。

- Circle[Point M, Point A]：建立圆心为 M 且过点 A 的圆。

- Circle[Point A, Point B, Point C]：建立过三点 A，B，C 的圆。

1. 给定圆心和半径：Circle( <Point>, <Radius Number> )

2. 给定圆心和半径等于给定线段的长度：Circle( <Point>, <Segment> )

3. 给定圆心和过某点：Circle( <Point>, <Point> )

4. 给定不共线的三点：Circle( <Point>, <Point>, <Point> )

5. Circle( <Line>, <Point> )

6. Circle( <Point>, <Radius>, <Direction> )

7. Circle( <Point>, <Point>, <Direction> )

**半圆**：Semicircle[Point A, Point B]：建立线段 AB 上的半圆。

### 圆弧
CircularArc[Point M, Point A, Point B]：建立以 M 点为圆心，起点为 A 终点为 B 的圆弧。

CircumcircularArc[Point A, Point B, Point C]：建立通过 A，B，C 三点的圆弧。




## 椭圆
- Ellipse[Point F, Point G, Number a]：建立焦点为 F 和 G 且半长轴长为 a 的椭圆。

- Ellipse[Point F, Point G, Segment]：建立焦点为 F 和 G 且半长轴长等于线段 s 长度的椭圆。

- Ellipse[ Point A, Point B, Point C]：建立焦点为 A 和 B 且过点 C 的圆。

1. 两个焦点和半长轴长度：Ellipse( <Focus>, <Focus>, <Semimajor Axis Length> )

2. 两个焦点和长半轴的长度等于给定线段的长度：Ellipse( <Focus>, <Focus>, <Segment> )

3. 两个焦点和过某点：Ellipse( <Focus>, <Focus>, <Point> )



## 双曲线

- Hyperbola[Point F, Point G, Number a]：建立焦点为 F 和 G 且半长轴长为 a 的双曲线。

- Hyperbola[Point F, Point G, Segment]：建立焦点为 F 和 G 且半长轴长等于线段 s 长度的

- Hyperbola[Point A, Point B, Point C]：建立焦点为 A 和 B 且过点 C 的双曲线。

1. 给定焦点和半长轴长度：Hyperbola( <Focus>, <Focus>, <Semimajor Axis Length> )

2. 给定焦点和长半轴的长度等于线段的长度：Hyperbola( <Focus>, <Focus>, <Segment> )

3. 给定焦点和过某点：Hyperbola( <Focus>, <Focus>, <Point> )



## 抛物线

- Parabola[Point F, Line g]：建立焦点为 F 准线为 g 的抛物线。

## 过5个点的圆锥曲线
Conic[Point A, Point B, Point C, Point D, Point E]：得到过五点 A，B，C，D，E 的圆锥曲线。



## 弧

- Arc[Conic, Point A, Point B]：得到介于圆锥曲线 c 上两点 A 和 B 之间的弧。

- Arc[Conic, Number t1, Number t2]：得到介于圆锥曲线 c 上梁参数 t1 和 t2 之间的弧。

## 扇形

CircularSector[Point M, Point A, Point B]：建立 M 点为圆心，起点为 A 终点为 B 的扇形。

CircumcircularSector[Point A, Point B, Point C]：建立通过 A，B，C 三点的扇形。

Sector[Conic, Point A, Point B]：建立介于圆锥曲线 c 上的两点 A 和 B 之间的圆锥曲线扇形区域。

Sector[Conic, Number t1, Number t2]：建立介于圆锥曲线 c 上的两参数 t1 和 t2 之间的圆锥曲线扇形区域。












