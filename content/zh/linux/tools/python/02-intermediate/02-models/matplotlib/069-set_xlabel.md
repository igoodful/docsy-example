---
title: 69. 设置X轴标签
description: Axes.set_xlabel
weight: 690
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


## 一、 Axes.set_xlabel


### 1. 参数

| 参数       | 功能                                   | 默认值   |
|:-----------|:---------------------------------------|:---------|
| xlabel     | 设置子图标题                           |          |
| fontdict   | 控制标题文本外观的字典                 | rcParams |
| labelpad   |                                        | 4.0      |
| `*`        |                                        |          |
| loc        | 位置`center,left,right`，与轴左中右对齐 | center   |
| y          | 标题的垂直轴位置（1.0 为顶部）           | None     |
| `**kwargs` |                                        |          |


- `**kwargs`
  - `fontsize` large
  - `fontweight` :bold
  - `fontproperties`


### 2. 使用

> 建议直接写xlabel

```python
fig,ax = plt.subplots()
ax.set_xlabel('Time (s)')

ax.set(ylabel='pints sold', title='Gelato sales by flavor', ylim=(0, 8000))
```



### 3. 源码

```python
    def set_xlabel(self, xlabel, fontdict=None, labelpad=None, *, loc=None, **kwargs):
        if labelpad is not None:
            self.xaxis.labelpad = labelpad
        protected_kw = ['x', 'horizontalalignment', 'ha']
        if {*kwargs} & {*protected_kw}:
            if loc is not None:
                raise TypeError(f"Specifying 'loc' is disallowed when any of "
                                f"its corresponding low level keyword "
                                f"arguments ({protected_kw}) are also "
                                f"supplied")

        else:
            loc = (loc if loc is not None
                   else mpl.rcParams['xaxis.labellocation'])
            _api.check_in_list(('left', 'center', 'right'), loc=loc)

            x = {
                'left': 0,
                'center': 0.5,
                'right': 1,
            }[loc]
            kwargs.update(x=x, horizontalalignment=loc)

        return self.xaxis.set_label_text(xlabel, fontdict, **kwargs)
```



