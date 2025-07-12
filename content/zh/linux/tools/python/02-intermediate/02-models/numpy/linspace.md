---
title: 2. 创建数组
description: numpy.linspace
weight: 10000
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

## numpy.linspace

{{<note>}}
创建一个等差数列
{{</note>}}


```python
np.linspace(start, stop, num=50, endpoint=True, retstep=False, dtype=None)

```
- start	起始值
- stop	终止值（如果endpoint为true，该值包含于数列中，否则不包含）
- num	步长，默认为50，要生成的等步长的样本数量
- endpoint，默认为True，表示包含终止值stop
- retstep=False，如果为 True 时，生成的数组中会显示间距，反之不显示
- dtype	返回ndarray的数据类型，如果没有提供，则会使用输入数据的类型。

左闭右开区间，按照step间隔


### 1. 参数




### 2. 使用



```python
# [ 1.  2.  3.  4.  5.  6.  7.  8.  9. 10.]
a = np.linspace(1,10,10)

# [1. 1. 1. 1. 1. 1. 1. 1. 1. 1.]
a = np.linspace(1,1,10)

# [10. 12. 14. 16. 18.]
a = np.linspace(10, 20,  5, endpoint =  False)  


# (array([ 1.,  2.,  3.,  4.,  5.,  6.,  7.,  8.,  9., 10.]), 1.0)
a =np.linspace(1,10,10,retstep= True)






```


### 3. 源码



```python

```
