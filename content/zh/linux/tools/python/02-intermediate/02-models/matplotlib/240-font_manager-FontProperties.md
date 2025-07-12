---
title: 240. 字体
description: matplotlib.font_manager.FontProperties
weight: 2400
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



### 1. 参数

字体

- family
  - `sans-serif` 默认值
  - dd

字体大小

- fontsize （默认10.0）
  - xx-small
  - x-small
  - small
  - medium
  - xx-large
  - x-large
  - large

字体样式

- style
  - normal 默认值
  - italic
  - oblique

字体大小的首选用法是使用相对值

- weight （`0-1000`）
  - ultralight
  - light
  - normal  默认值
  - regular
  - book
  - medium
  - roman
  - semibold
  - demibold
  - demi
  - bold
  - heavy
  - extra bold
  - black


用于呈现数学文本的字体系列

- math_fontfamily
  - dejavusans 默认值
  - dejavuserif
  - cm
  - stix
  - stixsans
  - custom








### 2. 使用



```python
from matplotlib.font_manager import FontProperties

font = FontProperties()
font.set_family('serif')
font.set_name('Times New Roman')
font.set_style('italic')

fig, ax = plt.subplots(figsize=(5, 3))
fig.subplots_adjust(bottom=0.15, left=0.2)
ax.plot(x1, y1)
ax.set_xlabel('Time [s]', fontsize='large', fontweight='bold')
ax.set_ylabel('Damped oscillation [V]', fontproperties=font)

plt.show()

```




### 3. 源码
```python

```













