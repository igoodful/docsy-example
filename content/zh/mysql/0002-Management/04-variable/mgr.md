---
title: mgr协议
description: mgr
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


## mgr


```viz-dot
digraph "mgr" {
        rankdir = TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
        ];

        "mgr" -> "AFTER_SYNC";
        "mgr" -> "off";

```


```sql
[root@127.0.0.1:8032 12:00:15((none))]$  SET GLOBAL init_connect="SET sql_safe_updates=ON";
Query OK, 0 rows affected (0.00 sec)

[root@127.0.0.1:8032 12:00:04((none))]$ show global variables like 'sql_safe_updates';
+------------------+-------+
| Variable_name    | Value |
+------------------+-------+
| sql_safe_updates | OFF   |
+------------------+-------+
1 row in set (0.22 sec)

[root@127.0.0.1:8032 12:02:49((none))]$ show global variables like 'init_connect';
+---------------+-------------------------+
| Variable_name | Value                   |
+---------------+-------------------------+
| init_connect  | SET sql_safe_updates=ON |
+---------------+-------------------------+
1 row in set (0.01 sec)


```

上面查询命令实例表示当前mysql处于安全模式关闭的状态。

`set sql_safe_updates=1; ` 安全模式打开状态

`set sql_safe_updates=0; ` 安全模式关闭状态

在update操作中：当where条件中列（column）没有索引可用且无limit限制时会拒绝更新。where条件为常量且无limit限制时会拒绝更新。

在delete操作中： 当①where条件为常量，②或where条件为空，③或where条件中 列（column）没有索引可用且无limit限制时拒绝删除。



## 半同步和增强半同步的区别







