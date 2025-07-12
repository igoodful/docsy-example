---
title: constexpr
description: constexpr
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
- [模板](https://zhuanlan.zhihu.com/p/394184676)
{{< /alert >}}

{{<alert>}}

{{</alert>}}


## 概念

```c++
template <class ...Args>
void ShowList(Args... args){}
```

constexpr 是 C++11 引入的关键字，用于指示在编译期间可求值的常量表达式。它的作用是允许在编译期间执行计算，并将结果用作编译时常量，以提高程序的性能和效率。

constexpr 可以用于变量声明、函数声明以及构造函数声明，它的语法如下：

```c++
constexpr int variable = 42;

constexpr int functionName(int arg) {
    return arg * 2;
}

class MyClass {
public:
    constexpr MyClass(int value) : data(value) {}

    constexpr int getValue() const {
        return data;
    }

private:
    int data;
};
```
在上面的示例中：

- constexpr int variable = 42;：声明了一个 constexpr 变量 variable，它的值在编译期间就已经确定了。
- constexpr int functionName(int arg)：声明了一个 constexpr 函数 functionName，它接受一个整数参数，并返回参数的两倍值。这个函数可以在编译期间被求值，如果参数是常量表达式，则结果也是常量表达式。
- constexpr MyClass(int value) : data(value) {}：声明了一个 constexpr 构造函数，它接受一个整数参数，并将参数值赋给类的成员变量 data。这个构造函数可以在编译期间被调用，且在构造过程中会对参数进行求值。
- constexpr int getValue() const：声明了一个 constexpr 成员函数 getValue，它返回类的成员变量 data 的值。这个成员函数可以在编译期间被调用，并且返回的值也可以用作编译时常量。

使用 constexpr 关键字可以在编译期间执行更多的操作，提高程序的性能和效率，并且可以在一定程度上增加代码的可读性和可维护性。



