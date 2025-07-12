---
title: 安装
description: Matplotlib
weight: 1
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


## 1. 参考

1. https://matplotlib.org/
2. https://github.com/matplotlib/matplotlib


## 2. 安装
```sh
python3 -m pip install -U matplotlib
```



## 3. 使用


```python
import matplotlib.pyplot as plt
import mpl_toolkits.axisartist as axisartist

```


#### 标记 marker

- '.' 点
- ',' 像素点
- 'o' 实心圆
- 'v' 下三角
- '^' 上三角
- '<' 左三角
- '>' 右三角
- '1' 下三叉
- '2' 上三叉
- '3' 左三叉
- '4' 右三叉
- '8' 八角形
- 's' 正方形
- 'p' 五边形
- 'P' 加号（填充）
- '*' 星号
- 'h' 六边形 1
- 'H' 六边形 2
- '+' 加号
- 'x' 乘号 x
- 'X' 乘号 x (填充)
- 'D' 菱形
- 'd' 瘦菱形
- '|' 竖线
- '_' 横线


#### 线型 linestyle
> 简写为ls

- '-' 实线(默认) solid
- '--' 破折线 dashed
- '-.' 点划线 dashdot
- ':' 点虚线 dotted

```python
import matplotlib.pyplot as plt
import numpy as np

ypoints = np.array([6, 2, 13, 10])
plt.plot(ypoints, linestyle = 'dotted')
plt.show()

```

#### 颜色 color
> 简写为 c


| 符号 | r    | g    | b    | c    | m    | y    | k    | w    |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 颜色 | 红色 | 绿色 | 蓝色 | 青色 | 品红 | 黄色 | 黑色 | 白色 |  |




#### 线宽 linewidth
> 简写为 lw

```python
import matplotlib.pyplot as plt
import numpy as np

ypoints = np.array([6, 2, 13, 10])
plt.plot(ypoints, linewidth = '12.5')
plt.show()
```



















