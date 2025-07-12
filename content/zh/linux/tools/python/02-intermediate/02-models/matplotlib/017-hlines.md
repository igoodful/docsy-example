---
title: 17. 绘制水平线
description: Axes.hlines
weight: 170
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


## 一、 Axes.hlines


### 1. 参数

| 参数       | 功能            | 默认值 |
|:-----------|:----------------|:-------|
| y          |                 |        |
| xmin       | 限制x轴的最小值 |        |
| xmax       | 限制x轴的最大值 |        |
| colors     |                 | None   |
| linestyles |                 | solid  |
| label      |                 | ''     |
| `**kwargs` |                 |        |



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
ax.hlines(range(n_rows), 3*g, 3*g + 2.8, color='0.7', linewidth=1)

```



### 3. 源码

```python
    @_preprocess_data(replace_names=["y", "xmin", "xmax", "colors"], label_namer="y")
    def hlines(self, y, xmin, xmax, colors=None, linestyles='solid', label='', **kwargs):

        # We do the conversion first since not all unitized data is uniform
        xmin, xmax, y = self._process_unit_info(
            [("x", xmin), ("x", xmax), ("y", y)], kwargs)

        if not np.iterable(y):
            y = [y]
        if not np.iterable(xmin):
            xmin = [xmin]
        if not np.iterable(xmax):
            xmax = [xmax]

        # Create and combine masked_arrays from input
        y, xmin, xmax = cbook._combine_masks(y, xmin, xmax)
        y = np.ravel(y)
        xmin = np.ravel(xmin)
        xmax = np.ravel(xmax)

        masked_verts = np.ma.empty((len(y), 2, 2))
        masked_verts[:, 0, 0] = xmin
        masked_verts[:, 0, 1] = y
        masked_verts[:, 1, 0] = xmax
        masked_verts[:, 1, 1] = y

        lines = mcoll.LineCollection(masked_verts, colors=colors,
                                     linestyles=linestyles, label=label)
        self.add_collection(lines, autolim=False)
        lines._internal_update(kwargs)

        if len(y) > 0:
            # Extreme values of xmin/xmax/y.  Using masked_verts here handles
            # the case of y being a masked *object* array (as can be generated
            # e.g. by errorbar()), which would make nanmin/nanmax stumble.
            updatex = True
            updatey = True
            if self.name == "rectilinear":
                datalim = lines.get_datalim(self.transData)
                t = lines.get_transform()
                updatex, updatey = t.contains_branch_seperately(self.transData)
                minx = np.nanmin(datalim.xmin)
                maxx = np.nanmax(datalim.xmax)
                miny = np.nanmin(datalim.ymin)
                maxy = np.nanmax(datalim.ymax)
            else:
                minx = np.nanmin(masked_verts[..., 0])
                maxx = np.nanmax(masked_verts[..., 0])
                miny = np.nanmin(masked_verts[..., 1])
                maxy = np.nanmax(masked_verts[..., 1])

            corners = (minx, miny), (maxx, maxy)
            self.update_datalim(corners, updatex, updatey)
            self._request_autoscale_view()
        return lines
```



