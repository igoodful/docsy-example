---
title: 73. 设置图形标题
description: Axes.set_title
weight: 730
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


## 一、 Axes.set_title


### 1. 参数

| 参数       | 功能                                                          | 默认值   |
|:-----------|:--------------------------------------------------------------|:---------|
| label      | 设置子图标题                                                  |          |
| fontdict   | 控制标题文本外观的字典                                        | rcParams |
| loc        | 位置`center,left,right`，与轴左中右对齐                        | center   |
| pad        | 标题与轴顶部的偏移量，以点为单位,太大则跑出范围，太小则与轴相交 | 6.0      |
| `*`        |                                                               |          |
| y          | 标题的垂直轴位置（1.0 为顶部）                                  | None     |
| `**kwargs` |                                                               |          |



- backgroundcolor 
- fontweight 
- ha
- va


### 2. 使用

> 建议直接写label

```python
fig, ax = plt.subplots()
# 会将第一个参数当做label
ax.set_title("Number of penguins with above average body mass")
```





### 3. 源码
```python
    def set_title(self, label, fontdict=None, loc=None, pad=None, *, y=None, **kwargs):

        if loc is None:
            loc = mpl.rcParams['axes.titlelocation']

        if y is None:
            y = mpl.rcParams['axes.titley']
        if y is None:
            y = 1.0
        else:
            self._autotitlepos = False
        kwargs['y'] = y

        titles = {'left': self._left_title,
                  'center': self.title,
                  'right': self._right_title}
        title = _api.check_getitem(titles, loc=loc.lower())
        default = {
            'fontsize': mpl.rcParams['axes.titlesize'],
            'fontweight': mpl.rcParams['axes.titleweight'],
            'verticalalignment': 'baseline',
            'horizontalalignment': loc.lower()}
        titlecolor = mpl.rcParams['axes.titlecolor']
        if not cbook._str_lower_equal(titlecolor, 'auto'):
            default["color"] = titlecolor
        if pad is None:
            pad = mpl.rcParams['axes.titlepad']
        self._set_title_offset_trans(float(pad))
        title.set_text(label)
        title.update(default)
        if fontdict is not None:
            title.update(fontdict)
        title._internal_update(kwargs)
        return title
```



