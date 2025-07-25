---
title: 236. 颜色
description: color
weight: 2360
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


## 一、Cn

- `C0`
- `C1`

> Cn颜色规范，即C后面跟着一个数字，该数字从0到9，它是默认颜色属性循环的索引

- `#1f77b4`
- `#ff7f0e`
- `#2ca02c`
- `#d62728`
- `#9467bd`
- `#8c564b`
- `#e377c2`
- `#7f7f7f`
- `#bcbd22`
- `#17becf`








## 二、tab10

> 这是默认的颜色循环

- `tab:blue`
- `tab:orange`
- `tab:green`
- `tab:red`
- `tab:purple`
- `tab:brown`
- `tab:pink`
- `tab:gray`
- `tab:olive`
- `tab:cyan`



## 三、xkcd

- `xkcd:sky blue`
- `xkcd:eggshell`

## 四、X11/CSS4

- https://xkcd.com/color/rgb/

> html中的颜色名称

- 'blue'
- 'aquamarine'
- 'mediumseagreen'




## 五、单个字母简写颜色
> 绿色、青色、洋红色和黄色与 X11/CSS4 颜色不一致。选择这些特定色调是为了在典型背景下更好地显示彩色线条。

- `b` 蓝色
- `g` 绿色
- `r` 红色
- `c` 青色
- `m` 洋红色
- `y` 黄色
- `k` 黑色
- `w` 白色

## 六、灰度级的浮点值

> 灰度值的闭区间内浮点值的字符串表示，取值范围为[0, 1]

- '0' 像黑色
- '1' 像白色
- '0.8' 浅灰色

包含灰度级的浮点值的字符串表示形式


## 七、RGB、RGBA
> RGBA 是红色、绿色、蓝色、Alpha 的缩写

- [0, 1]
- `(0.1, 0.2, 0.5)`  RGB
- `(0.1, 0.2, 0.5, 0.3)` RGBA


## 八、十六进制、简写十六进制

> 不区分大小写的 RGB 或 RGBA 字符串等效于重复字符的十六进制简写
>
> 不区分大小写的十六进制 RGB 或 RGBA 字符串

- `#0F0F0F`    十六进制RGB
- `#0F0F0F0F`  十六进制RGBA
- `#abc`       十六进制RGB `#aabbcc`
- `#abcd`      十六进制RGBA `#aabbccdd`



## 九、颜色+alpha

{{<alert color="danger">}}

上述颜色格式之一的元组和一个 alpha 浮点数。

**在 3.8 版本中添加**。

{{</alert>}}



- `('green', 0.3)`
- `('#f00', 0.9)`




## 十、十六进制与十进制转换

十六进制颜色和 RGB 颜色表示方式都是用来定义颜色的标准格式。它们之间的转换关系如下：

### 十六进制颜色
十六进制颜色以 `#RRGGBB` 形式表示，其中：
- `RR` 表示红色的值，范围是 `00`（最小，表示 0）到 `FF`（最大，表示 255）。
- `GG` 表示绿色的值，范围也是 `00` 到 `FF`。
- `BB` 表示蓝色的值，范围同样是 `00` 到 `FF`。

### RGB 颜色
RGB 颜色表示为 `(R, G, B)`，其中：
- `R` 是红色的数值，范围是 `0` 到 `255`。
- `G` 是绿色的数值，范围是 `0` 到 `255`。
- `B` 是蓝色的数值，范围是 `0` 到 `255`。

### 转换步骤
1. 从十六进制颜色中去掉 `#` 符号。
2. 将剩余的字符串按每两个字符分成三部分，分别表示红、绿、蓝三个颜色通道。
3. 将每一部分的十六进制数转换为十进制数。

### 例子：将 `#4CAF50` 转换为 RGB

#### 1. 去掉 `#`
得到字符串 `4CAF50`。

#### 2. 分成三部分
- `4C` -> 红色通道
- `AF` -> 绿色通道
- `50` -> 蓝色通道

#### 3. 转换为十进制
- `4C`（十六进制） = 4 × 16 + 12 = 64 + 12 = **76**（十进制）
- `AF`（十六进制） = 10 × 16 + 15 = 160 + 15 = **175**（十进制）
- `50`（十六进制） = 5 × 16 + 0 = 80 + 0 = **80**（十进制）

#### 4. 得到的 RGB 值
- 红色通道：76
- 绿色通道：175
- 蓝色通道：80

因此，`#4CAF50` 对应的 RGB 颜色为 `rgb(76, 175, 80)`。

### 另一个例子：将 `#FF5733` 转换为 RGB

#### 1. 去掉 `#`
得到 `FF5733`。

#### 2. 分成三部分
- `FF` -> 红色通道
- `57` -> 绿色通道
- `33` -> 蓝色通道

#### 3. 转换为十进制
- `FF`（十六进制） = 255（十进制）
- `57`（十六进制） = 5 × 16 + 7 = 80 + 7 = 87（十进制）
- `33`（十六进制） = 3 × 16 + 3 = 48 + 3 = 51（十进制）

#### 4. 得到的 RGB 值
- 红色通道：255
- 绿色通道：87
- 蓝色通道：51

因此，`#FF5733` 对应的 RGB 颜色为 `rgb(255, 87, 51)`。

### 总结
十六进制颜色转换为 RGB 的步骤：
1. 去掉 `#`，按两个字符一组分割为红、绿、蓝三部分。
2. 将每部分的十六进制数转换为十进制数。
3. 得到 `(R, G, B)` 的数值，范围是 `0` 到 `255`。












