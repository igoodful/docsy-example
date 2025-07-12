---
title: 238. 创建图形和子图
description: matplotlib.pyplot.subplots
weight: 2380
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


## 一、 matplotlib.pyplot.subplots


### 1. 参数

|               | 功能                                                          | 默认值 |
|:--------------|:------------------------------------------------------------|:-------|
| nrows         | 子图网格的行数                                                | 1      |
| ncols         | 子图网格的列数                                                | 1      |
| sharex        | 子图是否共享x轴                                               | False  |
| sharey        | 子图是否共享y轴                                               | False  |
| squeeze       | False表示始终返回Axes数组，True表示1*1时返回单个Axes对象       | True   |
| width_ratios  | 每列子图的宽度比例，[2,1]表示第一列子图是第二列子图宽度比为2：1 | None   |
| height_ratios | 每行子图的高度比例                                            | None   |
| subplot_kw    | 每个子图的附加参数                                            | None   |
| gridspec_kw   | 子图网格布局类，子图水平间距和垂直间距，wpsace,hspace           | None   |
| `**fig_kw `   | 可变关键字Figure属性参数，figsize,dpi                          |        |




- sharex,sharey: 控制x轴之间的属性共享, `bool | {'none', 'all', 'row', 'col'}`
  - True,all：x 轴或 y 轴将在所有子图之间共享。
  - False,none：每个子图 x 轴或 y 轴将是独立的。
  - row：每个子图行将共享一个 x 轴或 y 轴。
  - col：每个子图列将共享一个 x 轴或 y 轴。







### 2. 使用


```python
import matplotlib.pyplot as plt
import numpy as np

fig, ax = plt.subplots()

fig, axs = plt.subplots(2, 2)
#
fig, axs = plt.subplots(2, 2, subplot_kw=dict(projection="polar"))

fig, (ax1, ax2) = plt.subplots(1, 2)
fig, ((ax1, ax2), (ax3, ax4)) = plt.subplots(2, 2)

# Constrained layout makes sure the labels don't overlap the Axes.
fig, (ax0, ax1, ax2) = plt.subplots(nrows=3, layout='constrained')

fig, axs = plt.subplots(2, 3, sharex=True sharey=True,width_ratios=[2,1,1],height_ratios=[2,1], layout="constrained")

# 极坐标
fig, ax = plt.subplots(subplot_kw={'projection': 'polar'})

ax.axis('equal')


```


### 3. 源码
```python
def subplots(
    nrows: int = 1, ncols: int = 1, *,
    sharex: bool | Literal["none", "all", "row", "col"] = False,
    sharey: bool | Literal["none", "all", "row", "col"] = False,
    squeeze: bool = True,
    width_ratios: Sequence[float] | None = None,
    height_ratios: Sequence[float] | None = None,
    subplot_kw: dict[str, Any] | None = None,
    gridspec_kw: dict[str, Any] | None = None,
    **fig_kw
) -> tuple[Figure, Any]:

    fig = figure(**fig_kw)
    axs = fig.subplots(nrows=nrows, ncols=ncols, sharex=sharex, sharey=sharey,
                       squeeze=squeeze, subplot_kw=subplot_kw,
                       gridspec_kw=gridspec_kw, height_ratios=height_ratios,
                       width_ratios=width_ratios)
    return fig, axs
```




