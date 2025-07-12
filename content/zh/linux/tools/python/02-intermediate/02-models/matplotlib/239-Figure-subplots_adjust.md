---
title: 239. 调整子图布局
description: Figure.subplots_adjust
weight: 2390
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

## Figure.subplots_adjust

> 调整子图布局参数

### 1. 参数

|        | 功能                    | 默认值 |
|:-------|:----------------------|:-------|
| left   | 子图左边缘位置，分数     | None   |
| bottom | 子图右边缘位置，分数     | None   |
| right  | 子图底部边缘位置，分数   | None   |
| top    | 子图顶部边缘位置，分数   | None   |
| wspace | 子图之间填充的宽度，分数 | None   |
| hspace | 子图之间填充的高度，分数 | None   |



### 2. 使用


```python

fig.subplots_adjust(bottom=0.2)

fig.subplots_adjust(bottom=0.15, left=0.2)


```





### 3. 源码
```python
    def subplots_adjust(self, left=None, bottom=None, right=None, top=None, wspace=None, hspace=None):

        if (self.get_layout_engine() is not None and
                not self.get_layout_engine().adjust_compatible):
            _api.warn_external(
                "This figure was using a layout engine that is "
                "incompatible with subplots_adjust and/or tight_layout; "
                "not calling subplots_adjust.")
            return
        self.subplotpars.update(left, bottom, right, top, wspace, hspace)
        for ax in self.axes:
            if ax.get_subplotspec() is not None:
                ax._set_position(ax.get_subplotspec().get_position(self))
        self.stale = True
```







