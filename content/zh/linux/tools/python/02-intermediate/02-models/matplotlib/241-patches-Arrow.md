---
title: 241. 箭头
description: matplotlib.patches.Arrow
weight: 2410
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



### 1. 参数

| 参数       | 功能                                           | 默认值 |
|:-----------|:-----------------------------------------------|:-------|
| x          | 箭头的起点X坐标                                | float  |
| y          | 箭头的起点Y坐标                                | float  |
| dx         | 箭头的终点X坐标偏移起点X坐标的值               | float  |
| dy         | 箭头的终点Y坐标偏移起点Y坐标的值               | float  |
| `*`        |                                                |        |
| width      | 箭头宽度的比例因子,尾部宽度为0.2，头部宽度为0.6 | 1      |
| `**kwargs` |                                                |        |

> 其实就是起点坐标(x, y)，终点坐标(x+dx, y+dy)

- `**kwargs`
  - `color`  圆弧的颜色，默认蓝色
  - `alpha` 0.5
  - `edgecolor` 边框颜色
  - `linewidth`
  - `facecolor` 填充颜色，none表示不填充颜色
  - `linewidth` 圆弧的粗细







### 2. 使用



```python
import matplotlib.patches as mpatches

#
ax.add_patch(mpatches.Arrow(0, 0, 2, 2))


# width,color
ax.add_patch(mptch.Arrow(0.1, 0.2, 0.2, 0.2,width=0.2, color="lightblue"))

```





### 3. 源码
```python
class Arrow(Patch):
    """An arrow patch."""

    def __str__(self):
        return "Arrow()"

    _path = Path._create_closed([
        [0.0, 0.1], [0.0, -0.1], [0.8, -0.1], [0.8, -0.3], [1.0, 0.0],
        [0.8, 0.3], [0.8, 0.1]])

    @_docstring.dedent_interpd
    def __init__(self, x, y, dx, dy, *, width=1.0, **kwargs):

        super().__init__(**kwargs)
        self.set_data(x, y, dx, dy, width)

    def get_path(self):
        return self._path

    def get_patch_transform(self):
        return self._patch_transform

    def set_data(self, x=None, y=None, dx=None, dy=None, width=None):

        if x is not None:
            self._x = x
        if y is not None:
            self._y = y
        if dx is not None:
            self._dx = dx
        if dy is not None:
            self._dy = dy
        if width is not None:
            self._width = width
        self._patch_transform = (
            transforms.Affine2D()
            .scale(np.hypot(self._dx, self._dy), self._width)
            .rotate(np.arctan2(self._dy, self._dx))
            .translate(self._x, self._y)
            .frozen())

```













