---
title: 4. 绘制水平条形图
description: Axes.barh
weight: 40
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


## 一、 Axes.barh


### 1. 参数

| 参数       | 功能                                   | 默认值 |
|:-----------|:---------------------------------------|:-------|
| y          | y轴的刻度标签                          |        |
| height     | 矩形的纵向高度                         | 0.8    |
| left       | 矩形的横向宽度。为1时则相邻，大于1则重叠 | None   |
| `*`        |                                        |        |
| align      | 对齐x轴标签刻度方式：`center,edge`      | center |
| data       |                                        | None   |
| `**kwargs` |                                        |        |



| `*`           | 功能                   | 默认值    |
|:--------------|:---------------------|:----------|
| color，c       | 颜色                   |           |
| edgecolor     | 线型`-,--,-.,:`        | `-`       |
| linewidth，lw  | 线宽                   | float,1.5 |
| marker        | 标记，`.,o,s,p,v,+,*,^` | `.`       |
| markersize,mz | 标记大小               | float     |
| label         | 标签                   | object    |



| `**kwargs`    | 功能                   | 默认值    |
|:--------------|:---------------------|:----------|
| color，c       | 颜色                   |           |
| linestyle，ls  | 线型`-,--,-.,:`        | `-`       |
| linewidth，lw  | 线宽                   | float,1.5 |
| marker        | 标记，`.,o,s,p,v,+,*,^` | `.`       |
| markersize,mz | 标记大小               | float     |
| label         | 标签                   | object    |




### 2. 使用



```python

ax.barh(y_pos, performance, xerr=error, align='center')



```





### 3. 源码

```python
    @_docstring.dedent_interpd
    def barh(self, y, width, height=0.8, left=None, *, align="center", data=None, **kwargs):

        kwargs.setdefault('orientation', 'horizontal')
        patches = self.bar(x=left, height=height, width=width, bottom=y,
                           align=align, data=data, **kwargs)
        return patches

```




