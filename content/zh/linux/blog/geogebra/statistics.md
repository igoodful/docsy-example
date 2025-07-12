---
title: 统计
description: statistics
date: 2023-10-06
weight: 900


---
{{< alert >}}



{{< /alert >}}

{{<alert>}}


{{</alert>}}



## 求和
SigmaXX[List of numbers]：计算给定数值的平方和。
SigmaXX[List of points]：计算给定点列的 x 坐标的平方和。
SigmaXY[List of x-coordinates, List of y-coordinates]：计算两点列的 x 与 y坐标乘积的和。
SigmaXY[List of points]：计算 x 坐标和 y 坐标的乘积和。
SigmaYY[List of Points]：计算给定点列 y 坐标的平方和。

## 中位数
Median[List of numbers]：得到列表中元素的中位数。

## 众数
Mode[List of numbers]：得到列表中元素的众数。


## 平均数
Mean[List of numbers]：计算列表中元素的平均数。
MeanX[List of points]：计算列表中元素 x 坐标的平均数。
MeanY[List of points]：计算列表中元素 y 坐标的平均数。

## 方差
Variance[List of numbers]：计算列表元素的方差。

## 标准差
SD[List of Numbers]：计算列表中数值的标准差


## 四分位数
Q1[List of numbers]：得到列表元素中的第一四分位数。
Q3[List of numbers]：得到列表元素中的第三四分位数。






## 正态分布函数
Normal[Mean, Standard deviation, Variable value]：计算函数(Φ(x)-mean)/(standard
deviation)其中Φ(x)是符合 N(0,1)分布的概率密度函数。




## 条形图
BarChart[Start value, End value, List of heights]：建立指定区间内的条形图，条形个数取
决于表格长度，高度取决于元素大小。如：BarChart[10, 20, {1,2,3,4,5}]得到如下图所示


## 直方图
[Histogram](https://wiki.geogebra.org/s/zh/index.php?title=Histogram_%E6%8C%87%E4%BB%A4&variant=zh-cn)
- Histogram[List of class boundaries, List of heights]：由给定的高度建立直方图。

- Histogram[List of class boundaries, List of raw data]：使用给定的数据建立直方图。如：

- Histogram[{1, 2, 3, 4},{1.0, 1.1, 1.1, 1.2, 1.7, 2.2, 2.5, 4.0}]得到如下图所示。

直方图[<类边界列表>,<高度列表>] 创建具有给定高度的条形的直方图。类边界确定直方图每个条形的宽度和位置。

示例： Histogram[{0, 1, 2, 3, 4, 5}, {2, 6, 8, 3, 1}]创建一个包含 5 个给定高度的条形的直方图。第一个条形图位于间隔 [ 0, 1 ] 处，第二条形图位于间隔 [ 1, 2 ] 处，依此类推。





