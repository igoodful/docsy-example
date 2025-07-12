---
title: 246. 正多边形
description: matplotlib.patches.CirclePolygon
weight: 2460
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



### 1. 参数

| 参数       | 功能           | 默认值 |
|:-----------|:---------------|:-------|
| xy         | 圆心           | float  |
| radius     | 圆半径         | 5      |
| `*`        |                |        |
| resolution | 正多边形的边数 | 20     |
| `**kwargs` |                |        |


- `**kwargs`
  - `color`  多边形的颜色，默认蓝色,forestgreen
  - `alpha` 0.5





### 2. 使用



```python

CirclePolygon(xy=(0.6, 0.6), radius=0.2, resolution=8, color='lightblue')

```





### 3. 源码
```python
class CirclePolygon(RegularPolygon):

    def __str__(self):
        s = "CirclePolygon((%g, %g), radius=%g, resolution=%d)"
        return s % (self.xy[0], self.xy[1], self.radius, self.numvertices)

    @_docstring.dedent_interpd
    def __init__(self, xy, radius=5, *,
                 resolution=20,  # the number of vertices
                 ** kwargs):

        super().__init__(
            xy, resolution, radius=radius, orientation=0, **kwargs)
```













