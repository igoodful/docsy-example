---
title: 242. 扇形
description: matplotlib.patches.Wedge
weight: 2420
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



### 1. 参数

| 参数       | 功能                      | 默认值 |
|:-----------|:--------------------------|:-------|
| center     | 扇形的圆心坐标            | float  |
| r          | 扇形的半径                | float  |
| theta1     | 扇形开始的角度，逆时针旋转 | str    |
| theta2     | 扇形结束的角度，逆时针旋转 |        |
| `*`        |                           |        |
| width      |                           | None   |
| `**kwargs` |                           |        |









### 2. 使用



```python


```





### 3. 源码
```python

class Wedge(Patch):
    """Wedge shaped patch."""

    def __str__(self):
        pars = (self.center[0], self.center[1], self.r,
                self.theta1, self.theta2, self.width)
        fmt = "Wedge(center=(%g, %g), r=%g, theta1=%g, theta2=%g, width=%s)"
        return fmt % pars

    @_docstring.dedent_interpd
    def __init__(self, center, r, theta1, theta2, *, width=None, **kwargs):

        super().__init__(**kwargs)
        self.center = center
        self.r, self.width = r, width
        self.theta1, self.theta2 = theta1, theta2
        self._patch_transform = transforms.IdentityTransform()
        self._recompute_path()

    def _recompute_path(self):
        # Inner and outer rings are connected unless the annulus is complete
        if abs((self.theta2 - self.theta1) - 360) <= 1e-12:
            theta1, theta2 = 0, 360
            connector = Path.MOVETO
        else:
            theta1, theta2 = self.theta1, self.theta2
            connector = Path.LINETO

        # Form the outer ring
        arc = Path.arc(theta1, theta2)

        if self.width is not None:
            # Partial annulus needs to draw the outer ring
            # followed by a reversed and scaled inner ring
            v1 = arc.vertices
            v2 = arc.vertices[::-1] * (self.r - self.width) / self.r
            v = np.concatenate([v1, v2, [(0, 0)]])
            c = [*arc.codes, connector, *arc.codes[1:], Path.CLOSEPOLY]
        else:
            # Wedge doesn't need an inner ring
            v = np.concatenate([arc.vertices, [(0, 0), (0, 0)]])
            c = [*arc.codes, connector, Path.CLOSEPOLY]

        # Shift and scale the wedge to the final location.
        self._path = Path(v * self.r + self.center, c)

    def set_center(self, center):
        self._path = None
        self.center = center
        self.stale = True

    def set_radius(self, radius):
        self._path = None
        self.r = radius
        self.stale = True

    def set_theta1(self, theta1):
        self._path = None
        self.theta1 = theta1
        self.stale = True

    def set_theta2(self, theta2):
        self._path = None
        self.theta2 = theta2
        self.stale = True

    def set_width(self, width):
        self._path = None
        self.width = width
        self.stale = True

    def get_path(self):
        if self._path is None:
            self._recompute_path()
        return self._path
```













