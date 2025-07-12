---
title: innodb_max_undo_log_size
description: innodb_max_undo_log_size
date: 2025-02-12
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

- https://opensource.actionsky.com/20211111-undolog/
- https://bugs.mysql.com/bug.php?id=104573

{{< /alert >}}


## innodb_max_undo_log_size


```viz-dot
digraph "innodb_max_undo_log_size" {
        rankdir = TB;

        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
        ];

"innodb_max_undo_log_size" -> "handle_connection";



}
```


此功能可能会导致一系列严重的数据问题，例如

- 唯一键失败：允许插入两个同一个uk的数据；

- 复制中断：主服务器成功插入的数据可能无法插入到从服务器。

- 通过uk和pk查询结果不一致。






