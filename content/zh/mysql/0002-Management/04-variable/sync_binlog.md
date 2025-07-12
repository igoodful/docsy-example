---
title: sync_binlog
description: sync_binlog
date: 2025-06-16
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


## sync_binlog


```viz-dot
digraph "sync_binlog" {
        rankdir = TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
        ];

        "sync_binlog" -> "0";
        "sync_binlog" -> "1"[label="在事务提交前将二进制日志同步到磁盘，默认值"];
        "sync_binlog" -> "N";
}
```






