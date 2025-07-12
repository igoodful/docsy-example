---
title: 249. 颜色映射
description: matplotlib.colormaps
weight: 2490
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



### 1. 参数

| 参数       | 功能                 | 默认值         |
|:-----------|:---------------------|:---------------|
| xy         | 椭圆中心的xy坐标     | (float, float) |
| width      | 横轴的总长度（直径）   | float          |
| height     | 垂直轴的总长度（直径） | float          |
| `*`        |                      |                |
| angle      | 逆时针旋转           | float=0        |
| `**kwargs` |                      |                |

> 当椭圆的height和width设置一样时，椭圆就是圆了

- `**kwargs`
  - `color`  多边形的颜色，默认蓝色,forestgreen
  - `alpha` 0.5
  - `edgecolor` 边框颜色
  - `linewidth`
  - `facecolor` 填充颜色，none表示不填充颜色






### 2. 使用



```python
#!/usr/bin/env python
#!encoding=utf-8
import matplotlib as mpl
import matplotlib.pyplot as plt
import numpy as np

# 获取所有颜色映射
colormaps = mpl.colormaps
# 遍历所有颜色映射
for cmap_name in colormaps:
    # 创建一个数据数组用于展示颜色条
    data = np.random.rand(10, 10)
    
    # 创建一个新图形
    plt.figure()
    plt.imshow(data, cmap=cmap_name)
    
    # 添加颜色条
    cbar = plt.colorbar()
    cbar.set_label(cmap_name)
    
    # 展示颜色条并等待用户关闭
    plt.title(f'Colormap: {cmap_name}')
    plt.show(block=True)  # 阻塞直到关闭

```





### 3. 源码

- `matplotlib.__init__.py`
```python
from matplotlib.cm import _colormaps as colormaps

```

- `matplotlib.cm.py`
```python
_colormaps = ColormapRegistry(_gen_cmap_registry())

```










