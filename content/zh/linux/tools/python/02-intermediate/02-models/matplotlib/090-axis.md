---
title: 90. 设置坐标轴范围和可见性
description: Axes.axis
weight: 900
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


## 一、 Axes.axis



### 1. 参数

| 参数       | 功能                     | 默认值 |
|:-----------|:-------------------------|:-------|
| arg        |                          | None   |
| /          |                          |        |
| `*`        |                          | major  |
| emit       | 是否通知观察者轴限值变化 | True   |
| `**kwargs` |                          |        |


- 'off' or False 隐藏所有轴装饰，即轴标签、轴脊、刻度标记、刻度标签和网格线。
- 'on' or True 不隐藏所有轴装饰,即轴标签、脊线、刻度标记、刻度标签和网格线。
- 'equal' 通过更改轴限值来设置相等缩放比例
- 'scaled' 通过更改绘图框的尺寸来设置相等缩放比例
- 'tight' 设置足够大的限制以显示所有数据，然后禁用进一步的自动缩放。
- 'auto' 自动缩放（用数据填充绘图框）
- 'image' 缩放，轴限制等于数据限制。
- 'square' xmax-xmin == ymax-ymin


### 2. 使用

```python
ax.axis('equal')

ax.axis([55, 175, 0, 0.03])

xmin, xmax, ymin, ymax = axis()

# ax.set(xlim=(xmin, xmax), ylim=(ymin, ymax))
xmin, xmax, ymin, ymax = axis([xmin, xmax, ymin, ymax])


```



### 3. 源码

```python
    def set_axis_off(self):
        self.axison = False
        self.stale = True

    def set_axis_on(self):
        self.axison = True
        self.stale = True

    def axis(self, arg=None, /, *, emit=True, **kwargs):

        if isinstance(arg, (str, bool)):
            if arg is True:
                arg = 'on'
            if arg is False:
                arg = 'off'
            arg = arg.lower()
            if arg == 'on':
                self.set_axis_on()
            elif arg == 'off':
                self.set_axis_off()
            elif arg in [
                    'equal', 'tight', 'scaled', 'auto', 'image', 'square']:
                self.set_autoscale_on(True)
                self.set_aspect('auto')
                self.autoscale_view(tight=False)
                if arg == 'equal':
                    self.set_aspect('equal', adjustable='datalim')
                elif arg == 'scaled':
                    self.set_aspect('equal', adjustable='box', anchor='C')
                    self.set_autoscale_on(False)  # Req. by Mark Bakker
                elif arg == 'tight':
                    self.autoscale_view(tight=True)
                    self.set_autoscale_on(False)
                elif arg == 'image':
                    self.autoscale_view(tight=True)
                    self.set_autoscale_on(False)
                    self.set_aspect('equal', adjustable='box', anchor='C')
                elif arg == 'square':
                    self.set_aspect('equal', adjustable='box', anchor='C')
                    self.set_autoscale_on(False)
                    xlim = self.get_xlim()
                    ylim = self.get_ylim()
                    edge_size = max(np.diff(xlim), np.diff(ylim))[0]
                    self.set_xlim([xlim[0], xlim[0] + edge_size],
                                  emit=emit, auto=False)
                    self.set_ylim([ylim[0], ylim[0] + edge_size],
                                  emit=emit, auto=False)
            else:
                raise ValueError(f"Unrecognized string {arg!r} to axis; "
                                 "try 'on' or 'off'")
        else:
            if arg is not None:
                if len(arg) != 2*len(self._axis_names):
                    raise TypeError(
                        "The first argument to axis() must be an iterable of the form "
                        "[{}]".format(", ".join(
                            f"{name}min, {name}max" for name in self._axis_names)))
                limits = {
                    name: arg[2*i:2*(i+1)]
                    for i, name in enumerate(self._axis_names)
                }
            else:
                limits = {}
                for name in self._axis_names:
                    ax_min = kwargs.pop(f'{name}min', None)
                    ax_max = kwargs.pop(f'{name}max', None)
                    limits[name] = (ax_min, ax_max)
            for name, (ax_min, ax_max) in limits.items():
                ax_auto = (None  # Keep autoscale state as is.
                           if ax_min is None and ax_max is None
                           else False)  # Turn off autoscale.
                set_ax_lim = getattr(self, f'set_{name}lim')
                set_ax_lim(ax_min, ax_max, emit=emit, auto=ax_auto)
        if kwargs:
            raise _api.kwarg_error("axis", kwargs)
        lims = ()
        for name in self._axis_names:
            get_ax_lim = getattr(self, f'get_{name}lim')
            lims += get_ax_lim()
        return lims
```



