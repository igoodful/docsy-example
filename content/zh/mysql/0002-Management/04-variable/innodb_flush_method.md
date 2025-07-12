---
title: innodb_flush_method
description: innodb_flush_method
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

{{< /alert >}}


## innodb_flush_method


```viz-dot
digraph "innodb_flush_method" {
rankdir = TB;
edge [
        fontsize="12"
        ];
node [
        shape = octagon;
        fontsize = "12"
];

"innodb_flush_method" -> "fsync";
"innodb_flush_method" -> "O_DSYNC";
"innodb_flush_method" -> "littlesync";
"innodb_flush_method" -> "nosync";
"innodb_flush_method" -> "O_DIRECT";
"innodb_flush_method" -> "O_DIRECT_NO_FSYNC";
}
```


## bug

```sql
clone instance from root@'10.10.10.10' identified by 'root'

```
- 报错为: `ERROR 1026(HY000): Error writing file './#innodb_redo.#clone/#ib_redo0' (errno: 22 - Invalid argument) `
- 解决：`innodb_flush_method=O_DIRECT` 改为`innodb_flush_method=fsync`

