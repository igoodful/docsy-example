---
title: 1. 正态分布
description: numpy.random.normal
weight: 10000
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

## numpy.random.normal


正太分布的概率密度函数：

$ p(x) = \cfrac{1}{\sqrt{2\pi \sigma {^2}}} \text{e}^{- \frac{(x - \mu)^2}{2 \sigma {^2}}} $


### 1. 参数

|       | 功能        | 默认值 |
|:------|:----------|:-------|
| loc   | 均值        | 0      |
| scale | 标准差，非负 | 1      |
| size  | 点数        | None   |



### 2. 使用



```python
mu, sigma = 0, 0.1
s = np.random.normal(mu, sigma, 1000)

```


### 3. 源码



```python
random.normal(loc=0.0, scale=1.0, size=None)
```
