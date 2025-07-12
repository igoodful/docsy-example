---
title: 247. 圆
description: matplotlib.patches.Circle
weight: 2470
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



### 1. 参数

| 参数       | 功能 | 默认值 |
|:-----------|:-----|:-------|
| xy         | 圆心 |        |
| radius     | 半径 |        |
| `**kwargs` |      |        |


- `**kwargs`
  - `color`  多边形的颜色，默认蓝色,forestgreen
  - `alpha` 0.5
  - `edgecolor`
  - `linewidth`
  - `facecolor`






### 2. 使用



```python
import matplotlib.patches as patches

patch = patches.Circle((260, 200), radius=200, transform=ax.transData)

#
circle = Circle((0, 0), 1, facecolor='none',edgecolor=(0, 0.8, 0.8), linewidth=3, alpha=0.5)
ax.add_patch(circle)

```





### 3. 源码
```python
class Circle(Ellipse):

    def __str__(self):
        pars = self.center[0], self.center[1], self.radius
        fmt = "Circle(xy=(%g, %g), radius=%g)"
        return fmt % pars

    @_docstring.dedent_interpd
    def __init__(self, xy, radius=5, **kwargs):

        super().__init__(xy, radius * 2, radius * 2, **kwargs)
        self.radius = radius

    def set_radius(self, radius):

        self.width = self.height = 2 * radius
        self.stale = True

    def get_radius(self):
        """Return the radius of the circle."""
        return self.width / 2.

    radius = property(get_radius, set_radius)
```













