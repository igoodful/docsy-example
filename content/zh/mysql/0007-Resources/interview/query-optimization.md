---
title: 查询优化
description: query optimization
date: 2025-06-27
weight: 20000
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

{{< alert >}}

{{< /alert >}}


## 总览

```viz-dot
digraph "慢查问题" {
rankdir = LR;
edge [
 fontsize="12"
];

node [
shape = rectangle;
fontsize = "12"
];

"慢查问题" -> "没有合理的索引";
"没有合理的索引" -> "过滤性更强的字段上缺失索引";
"没有合理的索引" -> "没有将等值条件列用做组合索引前缀";
"没有合理的索引" -> "索引区分度低";
"慢查问题" -> "无法利用已有索引";
"无法利用已有索引" -> "索引列类型和常量类型不一致";
"无法利用已有索引" -> "关联字段类型不一致";
"无法利用已有索引" -> "关联字段字符集不一致";
"无法利用已有索引" -> "模糊查询LIKE通配符为前缀";
"无法利用已有索引" -> "索引列上有表达式计算";
"无法利用已有索引" -> "range optimizer限制";


}

```

