---
title: 函数模板
description: template func
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
在C++11之前，类模板和函数模板只能含有固定数量的模板参数。C++11增强了模板功能，允许模板定义中包含0到任意个模板参数，这就是可变参数模板。
{{< /alert >}}

{{<alert>}}

{{</alert>}}

## 一、固定参数模板


```c++

```




## 可变参数模板
在C++11之前，类模板和函数模板只能含有固定数量的模板参数。C++11增强了模板功能，允许模板定义中包含0到任意个模板参数，这就是可变参数模板。
```c++
template<typename... Types>
```
在函数模板中，可变参数模板最常见的使用场景是以递归的方法取出可用参数：

```c++
void print() {}

template<typename T, typename... Types>
void print(const T& firstArg, const Types&... args) {
        // sizeof...(args)获取参数个数
	std::cout << firstArg << " " << sizeof...(args) << std::endl;
	print(args...);
}


```
通过设置...，可以向print函数传递任意个数的参数，并且各个参数的类型也是任意。也就是说，可以允许模板参数接受任意多个不同类型的不同参数。这就是不定参数的模板，格外需要关注的是，...三次出现的位置。

如果如下调用print函数：
```c++
print(2, "hello", 1);
```
如此调用会递归将3个参数全部打印。细心的话会发现定义了一个空的print函数，这是因为当使用可变参数的模板，需要定义一个处理最后情况的函数，如果不写，会编译错误。这种递归的方式，是不是觉得很惊艳！






