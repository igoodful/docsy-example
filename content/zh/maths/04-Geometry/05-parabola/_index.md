---
title: 5. 抛物线
description: 抛物线
date: 2023-10-30
weight: 50
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



{{<alert title="总结" >}}

数形结合：画图

点在抛物线方程

{{</alert>}}



```viz-dot
digraph "抛物线" {
rankdir = TB;
graph [nodesep=0.5 ranksep=0.5]
edge [
        fontsize="10"
];

node [
shape = rectangle fontname="Microsoft YaHei" fontsize = 12
];

"抛物线" -> "画图"
"画图" -> "方程"
"方程" -> "定义》几何方程"
"方程" -> "设坐标》直线与抛物线联立"
"方程" -> "设角》建立三角函数"


}
```



## 点在抛物线方程

```viz-dot
digraph "点在抛物线方程" {
rankdir = TB;
graph [nodesep=0.5 ranksep=0.5]
edge [
        fontsize="10"
];

node [
shape = rectangle fontname="Microsoft YaHei" fontsize = 12
];

"点在抛物线方程" -> "定义方程"
"点在抛物线方程" -> "焦半径方程"
"点在抛物线方程" -> "点带入方程"

}
```
- 这三个方程等价，可根据需要灵活选择


---

| **方法**     |                                                                     |   |
|:-----------|:--------------------------------------------------------------------|:--|
| 分式通分     |                                                                     |   |
| 因式分解     |                                                                     |   |
| 同时取对数   |                                                                     |   |
| 同时取指数   |                                                                     |   |
| 分离对数     |                                                                     |   |
| 换元         |                                                                     |   |
| 单调不等式   |                                                                     |   |
| 图像交点     |                                                                     |   |
| 放缩         | 放缩切线；放缩最值极限；放缩结论；放缩指数：放缩对数：放缩正弦：放缩余弦； |   |
| 零阶单调     |                                                                     |   |
| 零阶讨论     |                                                                     |   |
| 一阶正负确定 |                                                                     |   |
| 一阶零点     |                                                                     |   |
| 一阶单调     |                                                                     |   |
| 一阶讨论     |                                                                     |   |



## 初中数学基础

```viz-dot
digraph "两直线平行" {
rankdir = TB;
graph [nodesep=0.5 ranksep=0.5]
edge [
        fontsize="10"
];

node [
shape = rectangle fontname="Microsoft YaHei" fontsize = 12
];

"两直线平行" -> "同位角相等"
"两直线平行" -> "内错角相等"
"两直线平行" -> "同旁内角互补"
"两直线平行" -> "线段成比例"

}
```

## 面积

```viz-dot
digraph "三角形面积" {
rankdir = TB;
graph [nodesep=0.5 ranksep=0.5]
edge [
        fontsize="10"
];

node [
shape = rectangle fontname="Microsoft YaHei" fontsize = 12
];

"三角形面积" -> "底乘高》弦长与点线距"
"三角形面积" -> "正弦"
"三角形面积" -> "和差"

}
```

## 角平分线
```viz-dot
digraph "角平分线" {
rankdir = TB;
graph [nodesep=0.5 ranksep=0.5]
edge [
        fontsize="10"
];

node [
shape = rectangle fontname="Microsoft YaHei" fontsize = 12
];

"角平分线" -> "角相等》向量方程"
"角平分线" -> "线段比例方程"
"角平分线" -> "点线距相等"
"角平分线" -> "斜率方程和为0》倾斜角互补"
"角平分线" -> "中垂线方程"

}
```



## 三点共线

```viz-dot
digraph "三点共线" {
rankdir = TB;
graph [nodesep=0.5 ranksep=0.5]
edge [
        fontsize="10"
];

node [

shape = rectangle fontname="Microsoft YaHei" fontsize = 12

];

"三点共线" -> "向量共线"
"三点共线" -> "斜率"

}
```


## 中点

```viz-dot
digraph "中点" {
rankdir = TB;
graph [nodesep=0.5 ranksep=0.5]
edge [
        fontsize="10"
];

node [

shape = rectangle fontname="Microsoft YaHei" fontsize = 12

];

"中点" -> "中位线"
"中点" -> "中垂线"
"中点" -> "中点坐标方程"

}
```

## 已知角


```viz-dot
digraph "已知角" {
rankdir = TB;
graph [nodesep=0.5 ranksep=0.5]
edge [
        fontsize="10"
];

node [

shape = rectangle fontname="Microsoft YaHei" fontsize = 12

];

"已知角" -> "余弦方程"
"已知角" -> "直角中的三角函数"

}
```


## 最值


```viz-dot
digraph "最值" {
rankdir = TB;
graph [nodesep=0.5 ranksep=0.5]
edge [
        fontsize="10"
];

node [

shape = rectangle fontname="Microsoft YaHei" fontsize = 12

];

"最值" -> "均值放缩"
"最值" -> "函数"

}
```

## 重心


```viz-dot
digraph "重心" {
rankdir = TB;
graph [nodesep=0.5 ranksep=0.5]
edge [
        fontsize="10"
];

node [

shape = rectangle fontname="Microsoft YaHei" fontsize = 12

];

"重心" -> "向量坐标方程"
"重心" -> "线段比例方程"
"重心" -> "定义方程》焦半径方程"
"重心" -> "三角形边不等式放缩"

}
```


## 距离


```viz-dot
digraph "距离" {
rankdir = LR;
graph [nodesep=0.5 ranksep=0.5]
edge [
        fontsize="10"
];

node [

shape = rectangle fontname="Microsoft YaHei" fontsize = 12

];

"距离" -> "点点距方程"
"距离" -> "点线距方程"
"距离" -> "斜率已知的坐标方程"
"距离" -> "定义方程》焦半径方程"
"距离" -> "弦长方程》直线与抛物线"
"距离" -> "相似比例方程"
"距离" -> "直角勾股方程"
"距离" -> "角度余弦方程"
"距离" -> "三角形边不等式放缩"


}
```



## 面积


```viz-dot
digraph "面积" {
rankdir = TB;
graph [nodesep=0.5 ranksep=0.5]
edge [
        fontsize="10"
];

node [

shape = rectangle fontname="Microsoft YaHei" fontsize = 12

];

"面积" -> "底乘高"
"底乘高" -> "弦长+点线距"
"底乘高" -> "水平底边》点点距+y轴坐标"
"底乘高" -> "垂直底边》点点距+x轴坐标"
"底乘高" -> "特殊三角形"

"面积" -> "水平底边拆"
"水平底边拆" -> "点点距+y轴坐标差"


"面积" -> "正弦方程"





}
```

## 面积比值方程

```viz-dot
digraph "面积比值方程" {
rankdir = TB;
graph [nodesep=0.5 ranksep=0.5]
edge [
        fontsize="10"
];

node [

shape = rectangle fontname="Microsoft YaHei" fontsize = 12

];

"面积比值方程" -> "等高》边比值"
"面积比值方程" -> "平行相似》底边平方"


}
```






━━━━━━━━━━━━━━

## 题目-01

🟩已知$AB$两点所在直线$l$的斜率为$k$，且$A(x_1,y_1)$，$B(x_2,y_2)$，求$ \left| AB \right| $

---

🟦 **【解析】**

不妨设直线的倾斜角为$  \theta $，可得：$ k = \tan \theta $

易知：$ \left| AB \right| = \cfrac{ \left| x_2 - x_1 \right| }{ \cos \theta } $

又有：$ \sec^2 \theta - \tan^2 \theta = 1 $

可得：$ \left| AB \right| = \left| x_2 - x_1 \right| \cdot \sqrt{ 1+k^2 } $


同理，可得：$ \left| AB \right| = \cfrac{ \left| y_2 - y_1 \right| }{ \sin \theta } $

又有：$ \cos^2 \theta - \cot^2 \theta = 1 $

可得：$ \left| AB \right| = \left| y_2 - y_1 \right| \cdot \cfrac{1}{ \sqrt{ 1+k^2 } } $


{{<myhr >}}

{{</myhr>}}

## 例题 3.2

🟩已知$AB$两点所在直线$l$的斜率为$k$，且$A(x_1,y_1)$，$B(x_2,y_2)$，求$ \left| AB \right| $

---

🟦 **【解析】**

不妨设直线的倾斜角为$  \theta $，可得：$ k = \tan \theta $

易知：$ \left| AB \right| = \cfrac{ \left| x_2 - x_1 \right| }{ \cos \theta } $

又有：$ \sec^2 \theta - \tan^2 \theta = 1 $

可得：$ \left| AB \right| = \left| x_2 - x_1 \right| \cdot \sqrt{ 1+k^2 } $


同理，可得：$ \left| AB \right| = \cfrac{ \left| y_2 - y_1 \right| }{ \sin \theta } $

又有：$ \cos^2 \theta - \cot^2 \theta = 1 $

可得：$ \left| AB \right| = \left| y_2 - y_1 \right| \cdot \cfrac{1}{ \sqrt{ 1+k^2 } } $


---


🟥



#### 例题 3.2
已知 `α, β ∈ (0, π)`，且 `cos α + cos β = 3/5`，`sin α + sin β = 4/5`，求 `cos(α - β)`

---

### 🔍 思路导航
1. 目标：`cos(α - β) = cos α cos β + sin α sin β`
2. 已知条件为 **同角名和**，考虑对两式平方相加消去交叉项

---

### 📝 步骤详解
1. **条件平方**
   $$
   \begin{cases}
   (\cos \alpha + \cos \beta)^2 = \cos^2 \alpha + 2\cos\alpha\cos\beta + \cos^2 \beta = \left(\frac{3}{5}\right)^2 \\
   (\sin \alpha + \sin \beta)^2 = \sin^2 \alpha + 2\sin\alpha\sin\beta + \sin^2 \beta = \left(\frac{4}{5}\right)^2
   \end{cases}
   $$

2. **两式相加**
   $$
   \begin{align*}
   &(\cos^2 \alpha + \sin^2 \alpha) + (\cos^2 \beta + \sin^2 \beta) + 2(\cos\alpha\cos\beta + \sin\alpha\sin\beta) \\
   &= 1 + 1 + 2\cos(\alpha - \beta) = \frac{9}{25} + \frac{16}{25} = 1
   \end{align*}
   $$

3. **解目标式**
   `2 + 2cos(α - β) = 1` → `2cos(α - β) = -1` →
   `\boxed{\cos(\alpha - \beta) = -\dfrac{1}{2}}`

---

### ⚠️ 易错警示
- 平方后未将 `(a+b)²` 展开为 `a² + 2ab + b²`
- 忽略 `cos²x + sin²x = 1` 的隐含使用条件

---

### 💡 方法总结
| 类型             | 解法                       |
|----------------|--------------------------|
| 三角和差条件求值 | 1. 平方相加  2. 构造方程组 |






