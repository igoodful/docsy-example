---
title: 248. 椭圆
description: matplotlib.patches.Ellipse
weight: 2480
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



### 1. 参数

| 参数       | 功能                 | 默认值         |
|:-----------|:---------------------|:---------------|
| xy         | 椭圆中心的xy坐标     | (float, float) |
| width      | 横轴的总长度（直径）   | float          |
| height     | 垂直轴的总长度（直径） | float          |
| `*`        |                      |                |
| angle      | 逆时针旋转           | float=0        |
| `**kwargs` |                      |                |

> 当椭圆的height和width设置一样时，椭圆就是圆了

- `**kwargs`
  - `color`  多边形的颜色，默认蓝色,forestgreen
  - `alpha` 0.5
  - `edgecolor` 边框颜色
  - `linewidth`
  - `facecolor` 填充颜色，none表示不填充颜色






### 2. 使用



```python
from matplotlib.patches import Ellipse

ellipse = Ellipse(
    xy=(2, 4),
    width=30,
    height=20,
    angle=35,
    facecolor="none",
    edgecolor="b"
)

ax.add_patch(ellipse)

#
e1 = patches.Ellipse((0, 0), 2, 4, angle=30, linewidth=2, fill=False, zorder=2)

ax.add_patch(e1)








```





### 3. 源码
```python
class Ellipse(Patch):
    """A scale-free ellipse."""

    def __str__(self):
        pars = (self._center[0], self._center[1],
                self.width, self.height, self.angle)
        fmt = "Ellipse(xy=(%s, %s), width=%s, height=%s, angle=%s)"
        return fmt % pars

    @_docstring.dedent_interpd
    def __init__(self, xy, width, height, *, angle=0, **kwargs):

        super().__init__(**kwargs)

        self._center = xy
        self._width, self._height = width, height
        self._angle = angle
        self._path = Path.unit_circle()
        # Required for EllipseSelector with axes aspect ratio != 1
        # The patch is defined in data coordinates and when changing the
        # selector with square modifier and not in data coordinates, we need
        # to correct for the aspect ratio difference between the data and
        # display coordinate systems.
        self._aspect_ratio_correction = 1.0
        # Note: This cannot be calculated until this is added to an Axes
        self._patch_transform = transforms.IdentityTransform()

    def _recompute_transform(self):

        center = (self.convert_xunits(self._center[0]),
                  self.convert_yunits(self._center[1]))
        width = self.convert_xunits(self._width)
        height = self.convert_yunits(self._height)
        self._patch_transform = transforms.Affine2D() \
            .scale(width * 0.5, height * 0.5 * self._aspect_ratio_correction) \
            .rotate_deg(self.angle) \
            .scale(1, 1 / self._aspect_ratio_correction) \
            .translate(*center)

    def get_path(self):
        """Return the path of the ellipse."""
        return self._path

    def get_patch_transform(self):
        self._recompute_transform()
        return self._patch_transform

    def set_center(self, xy):

        self._center = xy
        self.stale = True

    def get_center(self):
        """Return the center of the ellipse."""
        return self._center

    center = property(get_center, set_center)

    def set_width(self, width):

        self._width = width
        self.stale = True

    def get_width(self):

        return self._width

    width = property(get_width, set_width)

    def set_height(self, height):

        self._height = height
        self.stale = True

    def get_height(self):
        """Return the height of the ellipse."""
        return self._height

    height = property(get_height, set_height)

    def set_angle(self, angle):

        self._angle = angle
        self.stale = True

    def get_angle(self):
        """Return the angle of the ellipse."""
        return self._angle

    angle = property(get_angle, set_angle)

    def get_corners(self):

        return self.get_patch_transform().transform(
            [(-1, -1), (1, -1), (1, 1), (-1, 1)])

    def get_vertices(self):

        if self.width < self.height:
            ret = self.get_patch_transform().transform([(0, 1), (0, -1)])
        else:
            ret = self.get_patch_transform().transform([(1, 0), (-1, 0)])
        return [tuple(x) for x in ret]

    def get_co_vertices(self):

        if self.width < self.height:
            ret = self.get_patch_transform().transform([(1, 0), (-1, 0)])
        else:
            ret = self.get_patch_transform().transform([(0, 1), (0, -1)])
        return [tuple(x) for x in ret]

```













