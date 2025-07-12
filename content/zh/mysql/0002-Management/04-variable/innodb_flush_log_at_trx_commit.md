---
title: innodb_flush_log_at_trx_commit
description: innodb_flush_log_at_trx_commit
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


## innodb_flush_log_at_trx_commit


```viz-dot
digraph "innodb_flush_log_at_trx_commit" {
        rankdir = TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
        ];

        "innodb_flush_log_at_trx_commit" -> "0"[label="日志每秒写入并刷新到磁盘一次"];
        "innodb_flush_log_at_trx_commit" -> "1"[label="每次事务提交时日志都会写入磁盘并刷新，默认值"];
        "innodb_flush_log_at_trx_commit" -> "2";
}
```






