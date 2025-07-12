---
title: 1. 绘制线图
description: Axes.plot
weight: 10
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


## 一、 Axes.plot

### 1. 参数

| 参数       | 功能                        | 默认值 |
|:-----------|:----------------------------|:-------|
| `*args`    |                             |        |
| scalex     | 控制x轴是否根据数据自动调整 | True   |
| scaley     | 控制y轴是否根据数据自动调整 | True   |
| data       |                             | None   |
| `**kwargs` |                             |        |



| `**kwargs`    | 功能                                | 默认值    |
|:--------------|:------------------------------------|:----------|
| color，c       | 颜色                                |           |
| linestyle，ls  | 线型`-,--,-.,:`                     | `-`       |
| linewidth，lw  | 线宽                                | float,1.5 |
| marker        | 标记，`.,o,s,p,v,+,*,^`              | `.`       |
| markersize,mz | 标记大小                            | float     |
| label         | 标签                                | object    |
| path_effects  | `[patheffects.withTickedStroke()])` |           |




### 2. 使用



```python
# 只提供一个列表，x轴上默认0..N-1
plot(y)

#
plot(x, y)

plot(x, y, color='green', marker='o', linestyle='dashed', linewidth=2, markersize=12)

# 装饰线条
# import matplotlib.patheffects as patheffects
# path_effects
ax.plot([0, 1], [0, 1], label="Line",path_effects=[patheffects.withTickedStroke(spacing=7, angle=135)])
ax.plot(x, y, label="Curve", path_effects=[patheffects.withTickedStroke()])


```

绘制多组数据的方法有很多种
- 最直接的方法就是plot多次调用
- x和/或y是二维数组
- 指定多组[x]、y、[fmt]组



### 3. 源码
```python
def plot(self, *args, scalex=True, scaley=True, data=None, **kwargs):
        kwargs = cbook.normalize_kwargs(kwargs, mlines.Line2D)
        lines = [*self._get_lines(self, *args, data=data, **kwargs)]
        for line in lines:
            self.add_line(line)
        if scalex:
            self._request_autoscale_view("x")
        if scaley:
            self._request_autoscale_view("y")
        return lines
```







## 3d plot




### 1. 使用

```python
#!/usr/bin/env python
#!encoding=utf-8
import matplotlib.pyplot as plt
import numpy as np

# Make data
n = 1000
z = np.linspace(0, 30, n)
x = np.sin(z)
y = np.cos(z)

# Plot
fig, ax = plt.subplots(subplot_kw={"projection": "3d"})
ax.plot(x,y,z)

ax.set(xticklabels=[],yticklabels=[],zticklabels=[])
plt.show()

```












