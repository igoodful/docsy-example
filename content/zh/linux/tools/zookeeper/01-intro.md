---
title: 1. 简介
description: intro
date: 2023-11-05
weight: 100
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
占用端口

{{< /alert >}}

| 端口 | 使用说明   |
| :--- | :--------- |
| 2181 | 访问       |
| 2888 | 内部端口， |
| 3888 | 内部端口， |



```viz-dot
digraph "cmd" {
rankdir = LR;
edge [
        fontsize="12"
        ];
node [
        shape = box;
        fontsize = "12"
];

"cmd" -> "ls";
"cmd" -> "get";
"cmd" -> "set";
"cmd" -> "create";
"cmd" -> "delete";

}
```































