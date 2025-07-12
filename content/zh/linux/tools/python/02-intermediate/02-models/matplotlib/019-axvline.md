---
title: 19. 添加垂直线（跨整个轴）
description: Axes.axvline
weight: 190
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


## 一、 Axes.axvline


### 1. 参数

| 参数       | 功能            | 默认值 |
|:-----------|:----------------|:-------|
| x          |                 | 0      |
| ymin       | 限制x轴的最小值 | 0      |
| ymax       | 限制x轴的最大值 | 1      |
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
    def axvline(self, x=0, ymin=0, ymax=1, **kwargs):

        self._check_no_units([ymin, ymax], ['ymin', 'ymax'])
        if "transform" in kwargs:
            raise ValueError("'transform' is not allowed as a keyword "
                             "argument; axvline generates its own transform.")
        xmin, xmax = self.get_xbound()

        # Strip away the units for comparison with non-unitized bounds.
        xx, = self._process_unit_info([("x", x)], kwargs)
        scalex = (xx < xmin) or (xx > xmax)

        trans = self.get_xaxis_transform(which='grid')
        l = mlines.Line2D([x, x], [ymin, ymax], transform=trans, **kwargs)
        self.add_line(l)
        l.get_path()._interpolation_steps = mpl.axis.GRIDLINE_INTERPOLATION_STEPS
        if scalex:
            self._request_autoscale_view("x")
        return l
```




