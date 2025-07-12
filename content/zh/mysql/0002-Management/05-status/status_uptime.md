---
title: Uptime
description: Uptime
date: 2023-10-25
weight: 20000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

**监控指标**: `mysql_global_status_uptime`

{{< /alert >}}

- `show global status like 'Uptime';` 单位为妙
- `\s` 人性化显示

```sql
[root@127.0.0.1:8032 08:59:03((none))]$ show global status like 'Uptime';
+---------------+--------+
| Variable_name | Value  |
+---------------+--------+
| Uptime        | 773490 |
+---------------+--------+
1 row in set (0.00 sec)

[root@127.0.0.1:8032 08:59:07((none))]$ \s
--------------
/db/storage/guolicheng/local/mysql_3306/bin/mysql  Ver 8.0.35 for Linux on x86_64 (Source distribution)

Connection id:		164700
Current database:
Current user:		root@127.0.0.1
SSL:			Not in use
Current pager:		stdout
Using outfile:		''
Using delimiter:	;
Server version:		8.0.32 MySQL Community Server - GPL
Protocol version:	10
Connection:		127.0.0.1 via TCP/IP
Server characterset:	utf8mb4
Db     characterset:	utf8mb4
Client characterset:	utf8mb4
Conn.  characterset:	utf8mb4
TCP port:		8032
Binary data as:		Hexadecimal
Uptime:			8 days 22 hours 51 min 31 sec

Threads: 37  Questions: 5866666  Slow queries: 595  Opens: 854  Flush tables: 4  Open tables: 610  Queries per second avg: 7.584
--------------

[root@127.0.0.1:8032 08:59:08((none))]$

```



