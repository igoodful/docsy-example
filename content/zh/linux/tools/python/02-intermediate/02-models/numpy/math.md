---
title: 3. 数学运算
description: numpy.sin，numpy.bitwise_or，numpy.bitwise_xor，numpy.bitwise_not，numpy.invert，numpy.left_shift，numpy.right_shift
weight: 10000
date: 2024-09-02
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>





| 函数      | 功能                                      | 备注          |   |
|:----------|:------------------------------------------|:--------------|---|
| sin       |                                           |               |   |
| arcsin    | 控制x轴是否根据数据自动调整               |               |   |
| asin      | 控制y轴是否根据数据自动调整               | [-pi/2, pi/2] |   |
| cos       |                                           |               |   |
| arccos    |                                           |               |   |
| acos      |                                           | $[0,\pi]$     |   |
| tan       |                                           |               |   |
| arctan    |                                           |               |   |
| atan      |                                           |               |   |
| degrees   | 弧度转角度                                | 等价于rad2deg |   |
| radians   |                                           | 等价于deg2rad |   |
| deg2rad   | 角度转弧度                                |               |   |
| rad2deg   | 弧度转角度                                |               |   |
| sinh      |                                           |               |   |
| cosh      |                                           |               |   |
| tanh      |                                           |               |   |
| arcsinh   |                                           |               |   |
| arccosh   |                                           |               |   |
| arctanh   |                                           |               |   |
| ceil      |                                           |               |   |
| floor     |                                           |               |   |
| fix       |                                           |               |   |
| around    |                                           |               |   |
| round     |                                           |               |   |
| exp       | 计算输入数组中所有元素的指数              |               |   |
| exp2      | 计算输入数组中所有 p 的 2**p              |               |   |
| log       |                                           |               |   |
| log2      | 以 2 为底的对数                           |               |   |
| log10     | 返回输入数组的以 10 为底的对数，逐元素计算 |               |   |
| sinc      |                                           |               |   |
| i0        | 第一种修改的Bessel函数，阶数为0            |               |   |
| lcm       | 最小公倍数                                |               |   |
| gcd       | 最大公约数                                |               |   |
| angle     | 返回复杂参数的角度                        |               |   |
| real      | 返回复杂参数的实部                        |               |   |
| imag      | 返回复杂参数的虚部                        |               |   |
| conj      |                                           |               |   |
| conjugate | 返回复共轭                                |               |   |
| sqrt      | 返回数组的非负平方根                      |               |   |
| cbrt      | 逐元素返回数组的立方根                    |               |   |
| square    | 返回输入的逐元素平方                      |               |   |
| absolute  | 逐元素计算绝对值，输入数组                 |               |   |
| fabs      | 计算绝对值                                |               |   |
| sign      |                                           |               |   |
| maximum   | 数组元素的逐元素最大值                    |               |   |
| fmax      | 数组元素的逐元素最大值                    |               |   |
| minimum   | 数组元素的按元素最小值                    |               |   |
| fmin      | 数组元素的按元素最小值                    |               |   |
| power     | 第一阵列元素从第二阵列提升为幂            |               |   |
| pow       |                                           |               |   |
| trunc     | 截断值                                    |               |   |
| heaviside | 阶跃函数                                  |               |   |
| max       |                                           |               |   |
| min       |                                           |               |   |
|           |                                           |               |   |
|           |                                           |               |   |
|           |                                           |               |   |
|           |                                           |               |   |
|           |                                           |               |   |
|           |                                           |               |   |






## heaviside

$ heaviside(x) =
    \begin{cases}
        1,& \text{$x \ge 0$ } \\\\[2ex]
        0,& \text{$x < 0$} \\\\
    \end{cases} $


## numpy.sign


$sign(x) = \cfrac{x}{|x|}$



## sinc

$sinc(x) = \cfrac{\sin(x)}{x} $










{{<note>}}
位运算
{{</note>}}


```python


```


### 1. 参数




### 2. 使用



```python


```


### 3. 源码



```python

```
