---
title: 243. 圆弧
description: matplotlib.patches.Arc
weight: 2430
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



### 1. 参数

| 参数       | 功能                      | 默认值         |
|:-----------|:--------------------------|:---------------|
| xy         | 椭圆的中心                | (float, float) |
| width      | 横轴的总长度（直径）        | float          |
| height     | 垂直轴的总长度（直径）      | float          |
| `*`        |                           |                |
| angle      | 椭圆的旋转角度（逆时针）    | float=0        |
| theta1     | 圆弧开始的角度，逆时针旋转 | float=0        |
| theta2     | 圆弧结束的角度，逆时针旋转 | float=360      |
| `**kwargs` |                           |                |

> width和height相等时，圆弧相当于是圆的一段，不相等时，圆弧相当于是椭圆的一段。
>
> 圆弧开始的角度其实就是`angle+theta1`，结束的角度是`angle+theta2`

- `**kwargs`
  - `color`  圆弧的颜色，默认蓝色
  - `alpha` 0.5
  - `edgecolor` 边框颜色
  - `linewidth`
  - `facecolor` 填充颜色，none表示不填充颜色
  - `linewidth` 圆弧的粗细






### 2. 使用



```python

Arc(xy=(0.4, 0.7), width=0.5, height=0.5, angle=270, theta1=0, theta2=120, linewidth=10, color="lightblue")

```





### 3. 源码
```python
class Arc(Ellipse):

    def __str__(self):
        pars = (self.center[0], self.center[1], self.width,
                self.height, self.angle, self.theta1, self.theta2)
        fmt = ("Arc(xy=(%g, %g), width=%g, "
               "height=%g, angle=%g, theta1=%g, theta2=%g)")
        return fmt % pars

    @_docstring.dedent_interpd
    def __init__(self, xy, width, height, *,
                 angle=0.0, theta1=0.0, theta2=360.0, **kwargs):

        fill = kwargs.setdefault('fill', False)
        if fill:
            raise ValueError("Arc objects cannot be filled")

        super().__init__(xy, width, height, angle=angle, **kwargs)

        self.theta1 = theta1
        self.theta2 = theta2
        (self._theta1, self._theta2, self._stretched_width,
         self._stretched_height) = self._theta_stretch()
        self._path = Path.arc(self._theta1, self._theta2)

    @artist.allow_rasterization
    def draw(self, renderer):

        if not self.get_visible():
            return

        self._recompute_transform()

        self._update_path()
        # Get width and height in pixels we need to use
        # `self.get_data_transform` rather than `self.get_transform`
        # because we want the transform from dataspace to the
        # screen space to estimate how big the arc will be in physical
        # units when rendered (the transform that we get via
        # `self.get_transform()` goes from an idealized unit-radius
        # space to screen space).
        data_to_screen_trans = self.get_data_transform()
        pwidth, pheight = (
            data_to_screen_trans.transform((self._stretched_width,
                                            self._stretched_height)) -
            data_to_screen_trans.transform((0, 0)))
        inv_error = (1.0 / 1.89818e-6) * 0.5

        if pwidth < inv_error and pheight < inv_error:
            return Patch.draw(self, renderer)

        def line_circle_intersect(x0, y0, x1, y1):
            dx = x1 - x0
            dy = y1 - y0
            dr2 = dx * dx + dy * dy
            D = x0 * y1 - x1 * y0
            D2 = D * D
            discrim = dr2 - D2
            if discrim >= 0.0:
                sign_dy = np.copysign(1, dy)  # +/-1, never 0.
                sqrt_discrim = np.sqrt(discrim)
                return np.array(
                    [[(D * dy + sign_dy * dx * sqrt_discrim) / dr2,
                      (-D * dx + abs(dy) * sqrt_discrim) / dr2],
                     [(D * dy - sign_dy * dx * sqrt_discrim) / dr2,
                      (-D * dx - abs(dy) * sqrt_discrim) / dr2]])
            else:
                return np.empty((0, 2))

        def segment_circle_intersect(x0, y0, x1, y1):
            epsilon = 1e-9
            if x1 < x0:
                x0e, x1e = x1, x0
            else:
                x0e, x1e = x0, x1
            if y1 < y0:
                y0e, y1e = y1, y0
            else:
                y0e, y1e = y0, y1
            xys = line_circle_intersect(x0, y0, x1, y1)
            xs, ys = xys.T
            return xys[
                (x0e - epsilon < xs) & (xs < x1e + epsilon)
                & (y0e - epsilon < ys) & (ys < y1e + epsilon)
            ]

        # Transform the Axes (or figure) box_path so that it is relative to
        # the unit circle in the same way that it is relative to the desired
        # ellipse.
        box_path_transform = (
            transforms.BboxTransformTo((self.axes or self.figure).bbox)
            - self.get_transform())
        box_path = Path.unit_rectangle().transformed(box_path_transform)

        thetas = set()
        # For each of the point pairs, there is a line segment
        for p0, p1 in zip(box_path.vertices[:-1], box_path.vertices[1:]):
            xy = segment_circle_intersect(*p0, *p1)
            x, y = xy.T
            # arctan2 return [-pi, pi), the rest of our angles are in
            # [0, 360], adjust as needed.
            theta = (np.rad2deg(np.arctan2(y, x)) + 360) % 360
            thetas.update(
                theta[(self._theta1 < theta) & (theta < self._theta2)])
        thetas = sorted(thetas) + [self._theta2]
        last_theta = self._theta1
        theta1_rad = np.deg2rad(self._theta1)
        inside = box_path.contains_point(
            (np.cos(theta1_rad), np.sin(theta1_rad))
        )

        # save original path
        path_original = self._path
        for theta in thetas:
            if inside:
                self._path = Path.arc(last_theta, theta, 8)
                Patch.draw(self, renderer)
                inside = False
            else:
                inside = True
            last_theta = theta

        # restore original path
        self._path = path_original

    def _update_path(self):
        # Compute new values and update and set new _path if any value changed
        stretched = self._theta_stretch()
        if any(a != b for a, b in zip(
                stretched, (self._theta1, self._theta2, self._stretched_width,
                            self._stretched_height))):
            (self._theta1, self._theta2, self._stretched_width,
             self._stretched_height) = stretched
            self._path = Path.arc(self._theta1, self._theta2)

    def _theta_stretch(self):
        # If the width and height of ellipse are not equal, take into account
        # stretching when calculating angles to draw between
        def theta_stretch(theta, scale):
            theta = np.deg2rad(theta)
            x = np.cos(theta)
            y = np.sin(theta)
            stheta = np.rad2deg(np.arctan2(scale * y, x))
            # arctan2 has the range [-pi, pi], we expect [0, 2*pi]
            return (stheta + 360) % 360

        width = self.convert_xunits(self.width)
        height = self.convert_yunits(self.height)
        if (
            # if we need to stretch the angles because we are distorted
            width != height
            # and we are not doing a full circle.
            #
            # 0 and 360 do not exactly round-trip through the angle
            # stretching (due to both float precision limitations and
            # the difference between the range of arctan2 [-pi, pi] and
            # this method [0, 360]) so avoid doing it if we don't have to.
            and not (self.theta1 != self.theta2 and
                     self.theta1 % 360 == self.theta2 % 360)
        ):
            theta1 = theta_stretch(self.theta1, width / height)
            theta2 = theta_stretch(self.theta2, width / height)
            return theta1, theta2, width, height
        return self.theta1, self.theta2, width, height
```













