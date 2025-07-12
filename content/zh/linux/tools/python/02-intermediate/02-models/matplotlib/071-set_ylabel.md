---
title: 71. 设置Y轴标签
description: Axes.set_ylabel
weight: 710
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


## 一、 Axes.set_ylabel


### 1. 参数

| 参数       | 功能                                   | 默认值   |
|:-----------|:---------------------------------------|:---------|
| ylabel     | 设置子图标题                           |          |
| fontdict   | 控制标题文本外观的字典                 | rcParams |
| labelpad   |                                        | 4.0      |
| `*`        |                                        |          |
| loc        | 位置`center,left,right`，与轴左中右对齐 | center   |
| y          | 标题的垂直轴位置（1.0 为顶部）           | None     |
| `**kwargs` |                                        |          |




### 2. 使用

> 建议直接写ylabel

```python
fig, (ax0, ax1) = plt.subplots(2, 1, layout='constrained')
ax0.set_ylabel('Signal')

```



### 3. 源码

```python
    def set_ylabel(self, ylabel, fontdict=None, labelpad=None, *, loc=None, **kwargs):

        if labelpad is not None:
            self.yaxis.labelpad = labelpad
        protected_kw = ['y', 'horizontalalignment', 'ha']
        if {*kwargs} & {*protected_kw}:
            if loc is not None:
                raise TypeError(f"Specifying 'loc' is disallowed when any of "
                                f"its corresponding low level keyword "
                                f"arguments ({protected_kw}) are also "
                                f"supplied")

        else:
            loc = (loc if loc is not None
                   else mpl.rcParams['yaxis.labellocation'])
            _api.check_in_list(('bottom', 'center', 'top'), loc=loc)

            y, ha = {
                'bottom': (0, 'left'),
                'center': (0.5, 'center'),
                'top': (1, 'right')
            }[loc]
            kwargs.update(y=y, horizontalalignment=ha)

        return self.yaxis.set_label_text(ylabel, fontdict, **kwargs)
```




