---
title: 61. 在指定位置添加文本
description: Axes.text
weight: 610
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


## 一、 Axes.text

### 1. 参数

| 参数       | 功能     | 默认值     |
|:-----------|:---------|:-----------|
| x          | x坐标    | float      |
| y          | y坐标    | float      |
| s          | 文本内容 | str        |
| fontdict   |          | dict, None |
| `**kwargs` |          |            |



| `**kwargs`          | 功能                                                       | 默认值    |
|:--------------------|:-----------------------------------------------------------|:----------|
| color，c             | 颜色                                                       |           |
| backgroundcolor     |                                                            |           |
| linestyle，ls        | 线型`-,--,-.,:`                                            | `-`       |
| linewidth，lw        | 线宽                                                       | float,1.5 |
| marker              | 标记，`.,o,s,p,v,+,*,^`                                     | `.`       |
| markersize,mz       | 标记大小                                                   | float     |
| label               | 标签                                                       | object    |
| fontfamily          |                                                            |           |
| fontstyle           |                                                            |           |
| fontsize            |                                                            |           |
| fontweight          |                                                            |           |
| visible             |                                                            |           |
| horizontalalignment | {'left', 'center', 'right'}                                |           |
| verticalalignment   | {'baseline', 'bottom', 'center', 'center_baseline', 'top'} |           |
| bbox                |                                                            |           |
| transform           |                                                            |           |
|                     |                                                            |           |
|                     |                                                            |           |






### 2. 使用



```python
# 只提供一个列表，x轴上默认0..N-1
text(y)

#
text(x, y)

text(x, y, color='green', marker='o', linestyle='dashed', linewidth=2, markersize=12)

# 指定位置上放置latex表达式
ax.text(4, 0.5, r'$f(x)=\tan x$')

# 指定位置上放置latex表达式,但逆时针旋转45度
ax.text(0.2, 0.8, r'$f(x)=\tan x$', rotation=45)

```

绘制多组数据的方法有很多种
- 最直接的方法就是text多次调用
- x和/或y是二维数组
- 指定多组[x]、y、[fmt]组



### 3. 源码
```python
    @_docstring.dedent_interpd
    def text(self, x, y, s, fontdict=None, **kwargs):
        effective_kwargs = {
            'verticalalignment': 'baseline',
            'horizontalalignment': 'left',
            'transform': self.transData,
            'clip_on': False,
            **(fontdict if fontdict is not None else {}),
            **kwargs,
        }
        t = mtext.Text(x, y, text=s, **effective_kwargs)
        if t.get_clip_path() is None:
            t.set_clip_path(self.patch)
        self._add_text(t)
        return t
```




