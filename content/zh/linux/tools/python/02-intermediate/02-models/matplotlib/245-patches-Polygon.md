---
title: 245. 任意多边形
description: matplotlib.patches.Polygon
weight: 2450
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



### 1. 参数

| 参数       | 功能                 | 默认值       |
|:-----------|:---------------------|:-------------|
| xy         | 多边形各个顶点的列表 | array=(N, 2) |
| `*`        |                      |              |
| closed     |                      | bool=True    |
| `**kwargs` |                      |              |



- `**kwargs`
  - `color`  多边形的颜色，默认蓝色,forestgreen
  - `alpha` 0.5




### 2. 使用



```python
poly_coords = [
    (0.25, 2.75), (3.25, 2.75),
    (2.25, 0.75), (0.25, 0.75)
]

ax.add_patch(Polygon(poly_coords, color='forestgreen', alpha=0.5))

ax.add_patch(Polygon(xy=[(0.5, 0.5), (0.9, 0.5),  (0.9, 0.1), (0.6, 0.2)], color='lightgreen'))


```





### 3. 源码
```python
class Polygon(Patch):

    def __str__(self):
        if len(self._path.vertices):
            s = "Polygon%d((%g, %g) ...)"
            return s % (len(self._path.vertices), *self._path.vertices[0])
        else:
            return "Polygon0()"

    @_docstring.dedent_interpd
    def __init__(self, xy, *, closed=True, **kwargs):

        super().__init__(**kwargs)
        self._closed = closed
        self.set_xy(xy)

    def get_path(self):
        """Get the `.Path` of the polygon."""
        return self._path

    def get_closed(self):
        """Return whether the polygon is closed."""
        return self._closed

    def set_closed(self, closed):

        if self._closed == bool(closed):
            return
        self._closed = bool(closed)
        self.set_xy(self.get_xy())
        self.stale = True

    def get_xy(self):

        return self._path.vertices

    def set_xy(self, xy):

        xy = np.asarray(xy)
        nverts, _ = xy.shape
        if self._closed:
            # if the first and last vertex are the "same", then we assume that
            # the user explicitly passed the CLOSEPOLY vertex. Otherwise, we
            # have to append one since the last vertex will be "ignored" by
            # Path
            if nverts == 1 or nverts > 1 and (xy[0] != xy[-1]).any():
                xy = np.concatenate([xy, [xy[0]]])
        else:
            # if we aren't closed, and the last vertex matches the first, then
            # we assume we have an unnecessary CLOSEPOLY vertex and remove it
            if nverts > 2 and (xy[0] == xy[-1]).all():
                xy = xy[:-1]
        self._path = Path(xy, closed=self._closed)
        self.stale = True

    xy = property(get_xy, set_xy,
                  doc='The vertices of the path as a (N, 2) array.')
```













