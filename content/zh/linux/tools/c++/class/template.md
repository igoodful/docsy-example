---
title: template
description: template
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
- Args是一个模板参数包
- args是一个函数形参参数包
- 声明一个参数包Args...args,这个参数包中可以包含0到任意个模板参数




## 一、固定参数模板


```c++
template<类型 ... data>

template<int ... data> xxxxxx;
```
类型选择一个固定的类型，args其实是一个可修改的参数名

注意，这个固定的类型是有限制的，标准c++规定，只能为：
- 整型

- 指针

- 引用

但是这个形参包该怎么用呢，有这样一个例子，比如我想统计这个幼儿园的小朋友们的年龄总和，但是目前并不知道总共有多少个小朋友，那么此时就可以用这个非类型模板形参包，代码如下：


```c++
#include <iostream>
using namespace std;

//这里加一个空模板函数是为了编译可以通过，否则编译期间调用printAmt<int>(int&)就会找不到可匹配的函数
//模板参数第一个类型实际上是用不到的，但是这里必须要加上，否则就是调用printAmt<>(int&)，模板实参为空，但是模板形参列表是不能为空的
template<class type>
void printAmt(int &iSumAge){
        return;
}

template<class type, int age0, int ... age>
void printAmt(int &iSumAge){
        iSumAge += age0;
        //这里sizeof ... (age)是计算形参包里的形参个数，返回类型是std::size_t，后续同理
    if ( (sizeof ... (age)) > 0 ){
                //这里的age...其实就是语法中的一种包展开，这个后续会具体说明
                printAmt<type, age...>(iSumAge);
    }
}

int main(){
        int sumAge = 0;
        printAmt<int,1,2,3,4,5,7,6,8>(sumAge);
        cout << "the sum of age is " << sumAge << endl;
        return 0;
}
```
根据语法和代码的使用情况，我们对非类型模板形参包总结如下：

- 非类型模板形参包类型是固定的，但参数名跟普通函数参数一样，是可以修改的；

- 传递给非类型模板形参包的实参不是类型，而是实际的值。








