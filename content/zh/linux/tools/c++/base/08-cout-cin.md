---
title: 输入输出
description: cout cin
date: 2023-11-17
weight: 800


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}



{{< /alert >}}


{{<alert>}}


{{</alert>}}

## 总体

```viz-dot
digraph "const" {
        rankdir=TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "const" -> "指针变量";
        "指针变量" -> "不可通过指针变量修改指向的内容";
        "不可通过指针变量修改指向的内容" -> "指向const变量";
        "不可通过指针变量修改指向的内容" -> "指向常规变量";
        "指针变量" -> "指针变量不可指向其他地址";
        "const" -> "基本数据类型变量";

}
```
- 不允许将const地址赋给常规指针，但可通过const_cast运算符突破这种限制



## 一、cout
```viz-dot
digraph "cout" {
        rankdir=TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "cout" -> "预定义对象";
        "cout" -> "左移运算符重载：<<";
        "cout" -> "拼接
        ";
        "cout" -> "<<endl";
        "<<endl" -> "定义在头文件：iostream";
        "<<endl" -> "且定义在名称空间：std";
        "<<endl" -> "等价于\\n";
        "cout" -> "字符串";
        "cout" -> "字符";
        "cout" -> "数字";



}
```




```c++
const double pi = 3.14159;
const int a     = 7;
int b           = a; // 正确
a               = 8; // 错误，不能改变
```

## 二、cin

```viz-dot
digraph "cin" {
        rankdir=LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "cin" -> "预定义对象";
        "cin" -> "左移运算符重载：>>";
        "<<endl" -> "定义在头文件：iostream";
        "<<endl" -> "且定义在名称空间：std";
        "cin" -> "字符串";
        "cin" -> "字符";
        "cin" -> "数字";



}
```





## cin.get(xx, yy)







## cin.get(xx)










