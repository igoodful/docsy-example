---
title: mvcc
description: mvcc
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
digraph "触发redolog写文件" {
rankdir = TB;
edge [
 fontsize="12"
];

node [
shape = rectangle;
fontsize = "12"
];

"触发redolog写文件" -> "Redo log buffer空间不足";
"触发redolog写文件" -> "binlog切换";
"触发redolog写文件" -> "事务提交";
"触发redolog写文件" -> "checkpoint";
"触发redolog写文件" -> "shutdown";


}

```

