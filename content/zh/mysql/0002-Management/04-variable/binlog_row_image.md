---
title: binlog_row_image
description: binlog_row_image
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


## binlog_row_image


```viz-dot
digraph "binlog_row_image" {
        rankdir = TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
        ];

        "binlog_row_image" -> "full"[label="记录所有列，默认值"];
        "binlog_row_image" -> "minimal";
        "binlog_row_image" -> "noblob"[label="记录所有列，除了不需要的BLOB和TEXT列"];
}
```






