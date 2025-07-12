---
title: 175. 控制网格线显示
description: Axes.grid
weight: 1750
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


## 一、 Axes.grid


### 1. 参数

| 参数    | 功能     | 默认值            |
|:--------|:---------|:------------------|
| visible |          | bool \| None=None |
| which   |          | major             |
| axis    | both,x,y | both              |



| `**kwargs`    | 功能 | 默认值 |
|:--------------|:-----|:-------|
| color         |      |        |
| linestyle     |      |        |
| linewidth     |      |        |
| scatterpoints |      |        |
| framealpha    |      |        |
| facecolor     |      |        |
| labelspacing  |      |        |
| facecolor     |      |        |

### 2. 使用

```python

ax.grid()

ax.grid(True)

ax.grid(True, linestyle='-.')

# 降低网格透明度
ax.grid(c='k', ls='-', alpha=0.3)


```



### 3. 源码

```python
    @_docstring.dedent_interpd
    def grid(self, visible=None, which='major', axis='both', **kwargs):

        _api.check_in_list(['x', 'y', 'both'], axis=axis)
        if axis in ['x', 'both']:
            self.xaxis.grid(visible, which=which, **kwargs)
        if axis in ['y', 'both']:
            self.yaxis.grid(visible, which=which, **kwargs)
```



