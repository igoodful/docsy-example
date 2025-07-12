---
title: 类模板
description: template class
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

可变参数模板和普通模板的语义是一样的，只是写法上稍有区别，声明可变参数模板时需要在typename或class后面带上省略号...，比如：`template<typename... Types>`

其中，...可接纳的模板参数个数是0个及以上的任意数量，需要注意包括0个

```c++
template <typename...>
class tuple;

```

若不希望产生模板参数个数为0的变长参数模板，则可以采用以下的定义：
```c++
template<typename Head, typename... Tail>
```
本质上，...可接纳的模板参数个数仍然是0个及以上的任意数量，但由于多了一个Head类型，由此该模板可以接纳1个及其以上的模板参数。





