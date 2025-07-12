---
title: 函数重载
description: operator
date: 2023-10-30
weight: 10


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
operator是C++的关键字，它和运算符一起使用，表示一个运算符函数，理解时应将operator=整体上视为一个函数名
{{< /alert >}}

{{<alert>}}

{{</alert>}}

## 一、为什么使用操作符重载

对于系统的所有操作符，一般情况下，只支持基本数据类型和标准库中提供的class，对于用户自己定义的class，如果想支持基本操作，比如比较大小，判断是否相等，等等，则需要用户自己来定义关于这个操作符的具体实现。

```c++

```

## 二、如何声明一个重载的操作符


重载的运算符是带有特殊名称的函数，函数名是由关键字 operator 和其后要重载的运算符符号构成的。与其他函数一样，重载运算符有一个返回类型和一个参数列表。


- 类型与类型引用是相同的类型：



```viz-dot
digraph "func" {
        rankdir=TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "函数重载" -> "参数类型不同";
        "函数重载" -> "参数个数不同";
        "函数重载" -> "参数顺序不同";
}
```









