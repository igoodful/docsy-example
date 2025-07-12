---
title: 列表
description: 大括号
date: 2023-10-06
weight: 600


---
{{< alert >}}
如果要建立一些对象（如：点、线段、圆）的列表，可以使用大括号。
{{< /alert >}}

{{<alert>}}


{{</alert>}}

### 点列表
L={A,B,C}为包含三点 A，B，C 的列表

L={(1,2),(2,3),(4,5)}为包含三点 A，B，C 的列表



## 列表运算

1. 列表比较
```sql
list1 == list2

list1 != list2
```

2. 列表加减
```sql
list1 + list2  -- 将两列表中对应的元素相加，但要求两列表的长度相同。

list1 + number  -- 用列表中的每个元素加上某个数。

list1 - list2  -- 第一个列表内的元素减去第二个列表内的相应元素，也要求两列表的长度相同。

list1 - number  -- 用列表中的每个元素减去某个数。
```


3. 列表乘除
```sql
list1 * list2  -- 将两列表中对应的元素相乘，要求两列表的长度相同。如果列表内元素为矩阵，则要进行矩阵乘法运算。

list1 * number  -- 用列表内每个元素乘以某个数。

list1 / list2  -- 第一个列表内的元素除以第二次列表内对应的元素，也要求两列表的长度相同。

list1 / number  -- 用列表中每个元素除以某个数。

number / list1  -- 用此数除以列表内的每个元素。
```

4. 列表其他运算，对列表的每个元素都进行相同的函数运算
```sql
list1^2  -- 将列表内每个元素平方。

sin(list1) -- 列表内每个元素去 sin 函数。

```



## 列表添加元素
Append[List, Object]：将对象加入列表中成为新的列表的最后一个元素

Append[Object, List]：将对象加入列表中成为新的列表的第一个元素。

Insert[Object, List, Position]：将对象插入到列表的指定位置。

Join[List 1, List 2, ...]：将多个列表合并成一个列表。这种合并保留相同元素，不会重新排序。

Join[List of lists]：将子列表合并得到一个更大的列表。新列表包含所有元素，并保留相同元素，不会重新排序。

## 从列表提取元素

Element[List, Number n]：调用列表中的第 n 个元素。

Take[List, Start position m, End position n]：提取列表中第 m 个元素到第 n 个元素组成新的列表。

First[List]：得到列表中的第一个元素。

First[List, Number n of elements]：得到一个包含给定列表中前 n 个元素的新列表。

Last[List]：得到列表中的最后一个元素。

Last[List, Number n of elements]：得到一个包含给定列表中后 n 个元素的新列表。


## 列表集合运算
Intersection[List 1, List 2]：将两个列表的公共部分生成一个新的列表。

Union[List 1, List 2]：合并两列表并去除重复的元素。

## 列表统计与排序
Length[List]：计算列表的长度，也就是元素个数。

Min[List]：得到列表中最小的元素。

Max[List]：得到列表中最大的元素

Sort[List]：对列表中的数值、文字或点做排序。如：Sort[{3, 2, 1}]可得到列表{1, 2, 3}。

Reverse[List]：倒序排列列表。

Sum[List]：计算列表中所有元素之和。该命令可以用于数值、点、向量、文字和函数。

