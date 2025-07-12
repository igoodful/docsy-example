---
title: 18. 添加水平线（跨整个轴）
description: Axes.axhline
weight: 180
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


## 一、 Axes.axhline


### 1. 参数

| 参数       | 功能            | 默认值 |
|:-----------|:----------------|:-------|
| y          |                 | 0      |
| xmin       | 限制x轴的最小值 | 0      |
| xmax       | 限制x轴的最大值 | 1      |
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


```



### 3. 源码

```python
    @_docstring.dedent_interpd
    def axhline(self, y=0, xmin=0, xmax=1, **kwargs):

        self._check_no_units([xmin, xmax], ['xmin', 'xmax'])
        if "transform" in kwargs:
            raise ValueError("'transform' is not allowed as a keyword "
                             "argument; axhline generates its own transform.")
        ymin, ymax = self.get_ybound()

        # Strip away the units for comparison with non-unitized bounds.
        yy, = self._process_unit_info([("y", y)], kwargs)
        scaley = (yy < ymin) or (yy > ymax)

        trans = self.get_yaxis_transform(which='grid')
        l = mlines.Line2D([xmin, xmax], [y, y], transform=trans, **kwargs)
        self.add_line(l)
        l.get_path()._interpolation_steps = mpl.axis.GRIDLINE_INTERPOLATION_STEPS
        if scaley:
            self._request_autoscale_view("y")
        return l
```



