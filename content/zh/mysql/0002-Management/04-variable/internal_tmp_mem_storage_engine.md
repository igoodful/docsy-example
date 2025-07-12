---
title: internal_tmp_mem_storage_engine
description: internal_tmp_mem_storage_engine
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


## internal_tmp_mem_storage_engine


```viz-dot
digraph "internal_tmp_mem_storage_engine" {
        rankdir = TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
        ];

        "internal_tmp_mem_storage_engine" -> "TempTable"[label="TempTable"];
        "internal_tmp_mem_storage_engine" -> "MEMORY";
}
```


MySQL8.0.13 开始引入新的临时内存表引擎 TempTable，并将其作为内存中创建临时表的默认存储引擎


- `TempTable` 为默认值。错误日志出现"The table /home/work/mysql_3306/tmp/#sqla2b_298b06_4d is fu11!"
  - `tmp_table_size` 默认值为`16M`,从`mysql-8.0.28` 开始，tmp_table_size定义由`TempTable`存储引擎创建的任何单个内存内部临时表的最大大小。达到此限制时，MySQL会自动将内存内部临时表转换为InnoDB磁盘上的内部临时表。
  - `temptable_max_ram` 默认值为`1G`
  - `temptable_max_mmap` 默认值为`1G`。MySQL 8.0.23 中引入。
  - `temptable_use_mmap`(弃用) 默认值为`on`,该变量在 MySQL 8.0.16 中引入，并在 MySQL 8.0.26 中弃用；

- `MEMORY`


