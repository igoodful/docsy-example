---
title: router管理mysql
description: dataserver管理
date: 2023-10-31
weight: 70000


---

{{< alert >}}


{{< /alert >}}



{{<alert title="router配置持久化" color="secondary">}}

dbscale disable dataserver server_name

dbscale enable dataserver server_name

{{</alert>}}
```sql
dbscale disable dataserver server_name

dbscale enable dataserver server_name
```


```sql
mysql> dbscale show dataservers;
+------------+-----------+-------+------------------+---------------+----------------------+---------------+-------------+-------------+--------------------------------+-----------------+
| servername | host      | port  | username         | status        | master-online-status | master_backup | remote_user | remote_port | max_needed_conn/max_mysql_conn | master_priority |
+------------+-----------+-------+------------------+---------------+----------------------+---------------+-------------+-------------+--------------------------------+-----------------+
| database_1 | 10.5.43.1 | 16315 | dbscale_internal | Server normal | Master_Online        | 1             | root        | 22          |                                | 0               |
| database_2 | 10.5.43.2 | 16315 | dbscale_internal | Slave normal  | Not_A_Master         | 1             | root        | 22          |                                | 0               |
| database_3 | 10.5.43.3 | 16315 | dbscale_internal | Slave normal  | Not_A_Master         | 1             | root        | 22          |                                | 0               |
+------------+-----------+-------+------------------+---------------+----------------------+---------------+-------------+-------------+--------------------------------+-----------------+
3 rows in set (0.00 sec)

mysql> dbscale disable dataserver database_2;
Query OK, 0 rows affected (0.04 sec)

mysql> dbscale show dataservers;
+------------+-----------+-------+------------------+-----------------+----------------------+---------------+-------------+-------------+--------------------------------+-----------------+
| servername | host      | port  | username         | status          | master-online-status | master_backup | remote_user | remote_port | max_needed_conn/max_mysql_conn | master_priority |
+------------+-----------+-------+------------------+-----------------+----------------------+---------------+-------------+-------------+--------------------------------+-----------------+
| database_1 | 10.5.43.1 | 16315 | dbscale_internal | Server normal   | Master_Online        | 1             | root        | 22          |                                | 0               |
| database_2 | 10.5.43.2 | 16315 | dbscale_internal | server disabled | Not_A_Master         | 1             | root        | 22          |                                | 0               |
| database_3 | 10.5.43.3 | 16315 | dbscale_internal | Slave normal    | Not_A_Master         | 1             | root        | 22          |                                | 0               |
+------------+-----------+-------+------------------+-----------------+----------------------+---------------+-------------+-------------+--------------------------------+-----------------+
3 rows in set (0.00 sec)



```


















