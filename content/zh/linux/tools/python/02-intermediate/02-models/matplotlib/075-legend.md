---
title: 75. 显示图例
description: Axes.legend
weight: 750
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


## 一、 Axes.legend


### 1. 参数

| 参数       | 功能 | 默认值 |
|:-----------|:-----|:-------|
| `*args`    |      |        |
| `**kwargs` |      |        |


- `**kwargs`
  - loc
    - best
    - right
    - upper center
    - upper left
    - upper right
    - lower center
    - lower left
    - lower right
    - center
    - center left
    - center right
  - ncols `int=1`
  - fontsize
  - labelcolor `str | list`
  - facecolor
  - edgecolor
  - title `str | None = None` 给图例加上边框且有自己的标题
  - title_fontsize
  - bbox_to_anchor 指定图例放置位置和宽高`2-tuple, or 4-tuple of floats`: `(x, y, width, height)`,`(x, y)`
  - shadow `bool`




### 2. 使用



```python

plt.legend(fontsize=14)
legend(handles=handles)
legend(handles, labels)
legend(labels)

# 自动检测图例中要显示的元素label
ax.plot([1, 2, 3], label='Inline label')
ax.legend()

# 自动检测图例中要显示的元素label
line, = ax.plot([1, 2, 3])
line.set_label('Label via method')
ax.legend()

# 给指定对象添加图例
line1, = ax.plot([1, 2, 3], label='label1')
line2, = ax.plot([1, 2, 3], label='label2')
ax.legend(handles=[line1, line2])

# 给指定对象添加图例
ax.legend([line1, line2, line3], ['label1', 'label2', 'label3'])

#
ax.legend(loc='upper left', ncols=3)

```



### 3. 源码

```python
    @_docstring.dedent_interpd
    def legend(self, *args, **kwargs):
        handles, labels, kwargs = mlegend._parse_legend_args([self], *args, **kwargs)
        self.legend_ = mlegend.Legend(self, handles, labels, **kwargs)
        self.legend_._remove_method = self._remove_legend
        return self.legend_

    def get_legend(self):

        return self.legend_
```



