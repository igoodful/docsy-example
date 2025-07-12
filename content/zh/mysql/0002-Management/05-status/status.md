---
title: status
description: status
date: 2023-10-25
weight: 20000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
- [root@127.0.0.1:8032 16:00:48(information_schema)]$ \s
- [root@127.0.0.1:8032 16:03:08(information_schema)]$ status;

{{< /alert >}}



## 示例
```sql
[root@127.0.0.1:8032 16:00:48(information_schema)]$ \s
--------------
/db/storage/guolicheng/local/mysql_3306/bin/mysql  Ver 8.0.35 for Linux on x86_64 (Source distribution)

Connection id:		1196
Current database:	information_schema
Current user:		root@127.0.0.1
SSL:			Not in use
Current pager:		stdout
Using outfile:		''
Using delimiter:	;
Server version:		8.0.32 MySQL Community Server - GPL
Protocol version:	10
Connection:		127.0.0.1 via TCP/IP
Server characterset:	utf8mb4
Db     characterset:	utf8mb3
Client characterset:	utf8mb4
Conn.  characterset:	utf8mb4
TCP port:		8032
Binary data as:		Hexadecimal
Uptime:			5 hours 55 min 31 sec

Threads: 20  Questions: 2470506  Slow queries: 591  Opens: 778  Flush tables: 4  Open tables: 542  Queries per second avg: 115.817
--------------

```


#### Connection id
`Connection id:		1196`:

```sql
[root@127.0.0.1:8032 16:05:06(information_schema)]$ select connection_id();
+-----------------+
| connection_id() |
+-----------------+
|            1196 |
+-----------------+
1 row in set (0.00 sec)


```


#### Queries per second avg
` Queries per second avg: 115.817`

计算方式： `Queries per second avg = Questions/Uptime`








