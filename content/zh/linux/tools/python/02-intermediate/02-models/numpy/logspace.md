---
title: 2. 创建数组
description: numpy.logspace
weight: 10000
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

## numpy.logspace

{{<note>}}
创建一个于等比数列
{{</note>}}


```python
np.logspace(start, stop, num=50, endpoint=True, base=10.0, dtype=None)

```
- start	起始值
- stop	终止值（如果endpoint为true，该值包含于数列中，否则不包含）
- num	步长，默认为50，要生成的等步长的样本数量
- endpoint，默认为True，表示包含终止值stop
- base 对数 log 的底数
- dtype	ndarray 的数据类型

左闭右开区间，按照step间隔


### 1. 参数




### 2. 使用



```python
# [  1.   2.   4.   8.  16.  32.  64. 128. 256. 512.]
a = np.logspace(0,9,10,base=2)


```


### 3. 源码



```python

```
