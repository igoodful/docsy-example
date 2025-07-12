---
title: 2. 创建数组
description: numpy.arange
weight: 10000
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

## numpy.arange

```python
numpy.arange(start, stop, step, dtype)

```
- start	起始值，默认为0
- stop	终止值（不包含）
- step	步长，默认为1
- dtype	返回ndarray的数据类型，如果没有提供，则会使用输入数据的类型。

左闭右开区间，按照step间隔


### 1. 参数




### 2. 使用



```python
# [0  1  2  3  4]
x = np.arange(5)

# [0.  1.  2.  3.  4.]
x = np.arange(5, dtype = float)




```


### 3. 源码



```python

```
