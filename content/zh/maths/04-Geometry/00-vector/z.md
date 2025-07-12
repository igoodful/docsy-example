---
title: 复数
description: complex
date: 2025-06-19
weight: 200
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;

$ \rightleftharpoons $


{{<alert color="danger" title="注意" >}}




{{</alert>}}



## 复数概念

```viz-dot
digraph "复数概念" {
rankdir = TB;
edge [
        fontsize="12"
        ];
node [
        shape = box;
        fontsize = "12"
];

"复数概念" -> "虚数单位";
"复数概念" -> "实部";
"复数概念" -> "虚部";
"复数概念" -> "相等";
"复数概念" -> "共轭";

}
```

$ i = \sqrt{-1} $，$ i $虚数单位

- 虚数单位
- 实部
- 虚部
- 相等
- 共轭

复数与实数不同，两个复数(虚部不为零)不能比较大小，它们之间只有相等与不相等的关系。

## 复数运算

```viz-dot
digraph "运算" {
rankdir = TB;
edge [
        fontsize="12"
        ];
node [
        shape = box;
        fontsize = "12"
];

"运算" -> "加";
"运算" -> "减";
"运算" -> "乘";
"运算" -> "除";

}
```

加减乘除


## 复数表示




#### 复平面

$ z = x+iy $  $ \rightleftharpoons $ $ (x,y) $



在复平面上，从原点到点$ z = x+iy $所引的向量与该复数z也构成一一对应关系(复数零对应零向量)。

复数的加减法等同于向量的平行四边形法则。




