---
title: 日志
description: 日志
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
digraph "四大特性" {
rankdir = TB;
edge [
 fontsize="12"
];

node [
shape = rectangle;
fontsize = "12"
];

"四大特性" -> "插入缓冲";
"四大特性" -> "双写机制";
"四大特性" -> "自适应哈希索引";
"四大特性" -> "预读";

}

```

