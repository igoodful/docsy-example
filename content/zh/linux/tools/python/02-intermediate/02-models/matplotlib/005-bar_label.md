---
title: 5. 给条形图添加标签
description: Axes.bar_label
weight: 50
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


## 一、 Axes.bar_label



### 1. 参数

| 参数       | 功能                            | 默认值 |
|:-----------|:--------------------------------|:-------|
| container  | bar、barh的返回值                |        |
| labels     | 标签文本列表                    | None   |
| `*`        |                                 |        |
| fmt        |                                 | %g     |
| label_type | 标签类型：edge，center            | edge   |
| padding    | 以点为单位，标签与条形末端的距离 | 0      |
| `**kwargs` |                                 |        |



- `**kwargs`
  - color 文本标签的颜色
  - fontsize 文本标签的字体大小



### 2. 使用



```python



# 将条形图的高度值height放在条形图的中心
ax.bar_label(p, label_type='center')

# 将条形图的高度值height放在条形图的顶部边缘上方，且按照指定格式
ax.bar_label(bar_container, fmt='{:,.0f}')

# 将条形图的高度值height放在条形图的顶部边缘上方，且按照指定格式
ax.bar_label(bar_container, fmt=lambda x: f'{x * 1.61:.1f} km/h')

# padding 让数值标签远离条形图边沿
ax.bar_label(hbars, labels=[f'±{e:.2f}' for e in error], padding=8, color='b', fontsize=14)



```



### 3. 源码

```python
    def bar_label(self, container, labels=None, *, fmt="%g", label_type="edge", padding=0, **kwargs):

        for key in ['horizontalalignment', 'ha', 'verticalalignment', 'va']:
            if key in kwargs:
                raise ValueError(
                    f"Passing {key!r} to bar_label() is not supported.")

        a, b = self.yaxis.get_view_interval()
        y_inverted = a > b
        c, d = self.xaxis.get_view_interval()
        x_inverted = c > d

        # want to know whether to put label on positive or negative direction
        # cannot use np.sign here because it will return 0 if x == 0
        def sign(x):
            return 1 if x >= 0 else -1

        _api.check_in_list(['edge', 'center'], label_type=label_type)

        bars = container.patches
        errorbar = container.errorbar
        datavalues = container.datavalues
        orientation = container.orientation

        if errorbar:
            # check "ErrorbarContainer" for the definition of these elements
            lines = errorbar.lines  # attribute of "ErrorbarContainer" (tuple)
            barlinecols = lines[2]  # 0: data_line, 1: caplines, 2: barlinecols
            barlinecol = barlinecols[0]  # the "LineCollection" of error bars
            errs = barlinecol.get_segments()
        else:
            errs = []

        if labels is None:
            labels = []

        annotations = []

        for bar, err, dat, lbl in itertools.zip_longest(
                bars, errs, datavalues, labels
        ):
            (x0, y0), (x1, y1) = bar.get_bbox().get_points()
            xc, yc = (x0 + x1) / 2, (y0 + y1) / 2

            if orientation == "vertical":
                extrema = max(y0, y1) if dat >= 0 else min(y0, y1)
                length = abs(y0 - y1)
            else:  # horizontal
                extrema = max(x0, x1) if dat >= 0 else min(x0, x1)
                length = abs(x0 - x1)

            if err is None or np.size(err) == 0:
                endpt = extrema
            elif orientation == "vertical":
                endpt = err[:, 1].max() if dat >= 0 else err[:, 1].min()
            else:  # horizontal
                endpt = err[:, 0].max() if dat >= 0 else err[:, 0].min()

            if label_type == "center":
                value = sign(dat) * length
            else:  # edge
                value = extrema

            if label_type == "center":
                xy = (0.5, 0.5)
                kwargs["xycoords"] = (
                    lambda r, b=bar:
                        mtransforms.Bbox.intersection(
                            b.get_window_extent(r), b.get_clip_box()
                        ) or mtransforms.Bbox.null()
                )
            else:  # edge
                if orientation == "vertical":
                    xy = xc, endpt
                else:  # horizontal
                    xy = endpt, yc

            if orientation == "vertical":
                y_direction = -1 if y_inverted else 1
                xytext = 0, y_direction * sign(dat) * padding
            else:  # horizontal
                x_direction = -1 if x_inverted else 1
                xytext = x_direction * sign(dat) * padding, 0

            if label_type == "center":
                ha, va = "center", "center"
            else:  # edge
                if orientation == "vertical":
                    ha = 'center'
                    if y_inverted:
                        va = 'top' if dat > 0 else 'bottom'  # also handles NaN
                    else:
                        va = 'top' if dat < 0 else 'bottom'  # also handles NaN
                else:  # horizontal
                    if x_inverted:
                        ha = 'right' if dat > 0 else 'left'  # also handles NaN
                    else:
                        ha = 'right' if dat < 0 else 'left'  # also handles NaN
                    va = 'center'

            if np.isnan(dat):
                lbl = ''

            if lbl is None:
                if isinstance(fmt, str):
                    lbl = cbook._auto_format_str(fmt, value)
                elif callable(fmt):
                    lbl = fmt(value)
                else:
                    raise TypeError("fmt must be a str or callable")
            annotation = self.annotate(lbl,
                                       xy, xytext, textcoords="offset points",
                                       ha=ha, va=va, **kwargs)
            annotations.append(annotation)

        return annotations

```





