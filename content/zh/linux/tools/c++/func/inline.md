---
title: inline
description: inline
date: 2023-11-17
weight: 30000


---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
作用：提高程序运行速度
{{< /alert >}}


{{<alert>}}


{{</alert>}}

C++要求对一般的内置函数要用关键字inline声明，但对类内定义的成员函数，可以省略inline，因为这些成员函数已经被隐含地指定为内置函数了。

应该注意的是：如果成员函数不在类体内定义，而在类体外定义，系统并不是把它默认为内置函数，调用这些成员函数的过程和调用一般函数的过程是相同的。如果想将这些成员函数指定为内置函数，则应该加inline关键字，如：
















