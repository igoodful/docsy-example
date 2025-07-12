---
title: 91. 设置坐标轴边距
description: Axes.margins
weight: 910
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


## 一、 Axes.margins


### 1. 参数

| 参数       | 功能                                              | 默认值              |
|:-----------|:--------------------------------------------------|:--------------------|
| `*margins` | 单个值表示同时设置x、y轴边距，两个值表示分别设置x、y | float               |
| x          | x轴边距值                                         | float = 0.05        |
| y          | y轴边距值                                         | float = 0.05        |
| tight      |                                                   | bool \| None = True |






### 2. 使用



```python
#!/usr/bin/env python
#!encoding=utf-8
import matplotlib.pyplot as plt
import numpy as np
x = np.linspace(-5, 5, 10000)
y = np.sin(x)
fig, ax = plt.subplots()
ax.plot(x, y)
ax.grid()
ax.margins(y=0.5)
plt.savefig('./matplotlib-margins-001.svg',dpi=300,facecolor='white', edgecolor='black', bbox_inches='tight', pad_inches=0.05)
plt.show()

```

![](/matplotlib/matplotlib-margins-001.svg)



### 3. 源码
```python
    def margins(self, *margins, x=None, y=None, tight=True):

        if margins and (x is not None or y is not None):
            raise TypeError('Cannot pass both positional and keyword '
                            'arguments for x and/or y.')
        elif len(margins) == 1:
            x = y = margins[0]
        elif len(margins) == 2:
            x, y = margins
        elif margins:
            raise TypeError('Must pass a single positional argument for all '
                            'margins, or one for each margin (x, y).')

        if x is None and y is None:
            if tight is not True:
                _api.warn_external(f'ignoring tight={tight!r} in get mode')
            return self._xmargin, self._ymargin

        if tight is not None:
            self._tight = tight
        if x is not None:
            self.set_xmargin(x)
        if y is not None:
            self.set_ymargin(y)
#
    def get_xmargin(self):
        return self._xmargin
#
    def get_ymargin(self):
        return self._ymargin
#
    def set_xmargin(self, m):
        if m <= -0.5:
            raise ValueError("margin must be greater than -0.5")
        self._xmargin = m
        self._request_autoscale_view("x")
        self.stale = True
#
    def set_ymargin(self, m):
        if m <= -0.5:
            raise ValueError("margin must be greater than -0.5")
        self._ymargin = m
        self._request_autoscale_view("y")
        self.stale = True
```




