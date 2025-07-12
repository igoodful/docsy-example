---
title: processlist
date: 2017-01-05
description: processlist


weight: 70000
---

{{< alert >}}

1. 免费开源，易于学习

2. 适用MySQL、MariaDB、Microsoft SQL、PostgreSQL、SQLite

3. 轻量级数据库客户端软件，拥有图形化界面
3.

7.2 在线课程和培训

7.3 MySQL 社区和论坛
{{< /alert >}}
 # 安装



## show processlist
```sql
mysql> show processlist;
+------+------------------+---------------------+--------------------+------------------+-------+-----------------------------------------------------------------+-----------------------+
| Id   | User             | Host                | db                 | Command          | Time  | State                                                           | Info                  |
+------+------------------+---------------------+--------------------+------------------+-------+-----------------------------------------------------------------+-----------------------+
|    5 | event_scheduler  | localhost           | NULL               | Daemon           | 69088 | Waiting on empty queue                                          | NULL                  |
|    8 | dbscale_internal | 172.17.138.54:46854 | NULL               | Sleep            |     1 |                                                                 | NULL                  |
|   62 | dbscale_internal | 172.17.138.52:41948 | information_schema | Sleep            |    16 |                                                                 | NULL                  |                 |
|  195 | dbscale_internal | 172.17.138.52:46548 | NULL               | Binlog Dump GTID | 68480 | Source has sent all binlog to replica; waiting for more updates | NULL                  |
|  233 | dbscale_internal | 172.17.138.54:50310 | apple              | Query            |     3 | Sending to client                                               | select * from sbtest2 |
|  234 | dbscale_internal | 172.17.138.54:50312 | information_schema | Sleep            |     2 |                                                                 | NULL                  |
|  235 | dbscale_internal | 172.17.138.54:50314 | apple              | Sleep            | 67056 |                                                                 | NULL                  |
|  236 | dbscale_internal | 172.17.138.54:50316 | apple              | Sleep            | 67052 |                                                                 | NULL                  |
|  237 | dbscale_internal | 172.17.138.54:50318 | apple              | Sleep            | 67670 |                                                                 | NULL                  |
|  238 | dbscale_internal | 172.17.138.54:50320 | information_schema | Sleep            |     8 |                                                                 | NULL                  |
|  239 | dbscale_internal | 172.17.138.54:50322 | apple              | Sleep            | 67056 |                                                                 | NULL                  |
|  242 | dbscale_internal | 172.17.138.54:50328 | information_schema | Query            |     0 | init                                                            | show processlist      |
                 |
+------+------------------+---------------------+--------------------+------------------+-------+-----------------------------------------------------------------+-----------------------+
69 rows in set (0.00 sec)

```

## dbscale show user status
```sql
mysql> dbscale show user status;
+-------------------+---------+--------------------+--------------------------------------+-------------------------------------+--------------------+---------------+---------------------+----------------+--------------+---------------+------------+
| Thread_id_Handler | User_id | Cur_schema         | User_info                            | Executing SQL                       | Executing time(ms) | Working State | Login time          | using_conn_num | net_in(Byte) | net_out(Byte) | Cluster id |
+-------------------+---------+--------------------+--------------------------------------+-------------------------------------+--------------------+---------------+---------------------+----------------+--------------+---------------+------------+
| 0x7fe2b6e583c0    | 1109    | information_schema | dbscale_internal@172.17.138.51:41038 | Idle                                | 0                  | none          | 2023-10-25 11:13:03 | 0              | 92           | 1276          | 1          |
| 0x7fe2b6c75680    | 669891  | information_schema | admin@127.0.0.1:58404                | dbscale show user status            | 3                  | none          | 2023-10-31 08:56:45 | 0              | 1516         | 2914          | 1          |
| 0x7fe2ad56c4c0    | 672171  | information_schema | dbscale_internal@172.17.138.52:54736 | Idle                                | 0                  | none          | 2023-10-31 09:25:31 | 0              | 17814        | 1276          | 1          |
| 0x7fe2ad545ec0    | 672829  | apple              | admin@127.0.0.1:41976                | Idle                                | 0                  | none          | 2023-10-31 09:33:51 | 0              | 257          | 1995559936    | 1          |
| 0x7fe2ad508d40    | 673251  | information_schema | dbscale_internal@172.17.138.54:60382 | dbscale request cluster user status | 0                  | none          | 2023-10-31 09:39:06 | 0              | 40           | 1276          | 1          |
| 0x7f7dc1bb0400    | 713376  | information_schema | admin@172.17.138.47:50568            | Idle                                | 0                  | none          | 2023-10-31 07:16:35 | 0              | 11136        | 4150          | 2          |
| 0x7f7dc1830000    | 722541  | information_schema | admin@172.17.138.47:55784            | Idle                                | 0                  | none          | 2023-10-31 09:04:34 | 0              | 2048         | 1807          | 2          |
| 0x7f7dc1a14640    | 725511  | information_schema | dbscale_internal@172.17.138.54:38196 | dbscale request cluster user status | 0                  | none          | 2023-10-31 09:39:06 | 0              | 40           | 1276          | 2          |
| 0x7febba325f80    | 672633  | information_schema | dbscale_internal@172.17.138.54:55862 | dbscale request cluster user status | 0                  | none          | 2023-10-31 09:39:06 | 0              | 40           | 1276          | 3          |
+-------------------+---------+--------------------+--------------------------------------+-------------------------------------+--------------------+---------------+---------------------+----------------+--------------+---------------+------------+
9 rows in set (0.01 sec)

```
## dbscale show user status running
```sql
mysql> dbscale show user status running;
+-------------------+---------+--------------------+--------------------------------------+---------------------------------------------+--------------------+---------------+---------------------+----------------+--------------+---------------+------------+
| Thread_id_Handler | User_id | Cur_schema         | User_info                            | Executing SQL                               | Executing time(ms) | Working State | Login time          | using_conn_num | net_in(Byte) | net_out(Byte) | Cluster id |
+-------------------+---------+--------------------+--------------------------------------+---------------------------------------------+--------------------+---------------+---------------------+----------------+--------------+---------------+------------+
| 0x7fe2b6c75680    | 669891  | information_schema | admin@127.0.0.1:58404                | dbscale show user status running            | 3                  | none          | 2023-10-31 08:56:45 | 0              | 1487         | 2914          | 1          |
| 0x7fe2ad545ec0    | 672829  | apple              | admin@127.0.0.1:41976                | select * from sbtest2                       | 18061              | none          | 2023-10-31 09:33:51 | 1              | 257          | 1574042437    | 1          |
| 0x7fe2b6c1c400    | 672989  | information_schema | dbscale_internal@172.17.138.54:59336 | dbscale request cluster user status running | 0                  | none          | 2023-10-31 09:35:45 | 0              | 48           | 1276          | 1          |
| 0x7f7dc37d0ac0    | 725227  | information_schema | dbscale_internal@172.17.138.54:37150 | dbscale request cluster user status running | 0                  | none          | 2023-10-31 09:35:45 | 0              | 48           | 1276          | 2          |
| 0x7febba254400    | 672371  | information_schema | dbscale_internal@172.17.138.54:54816 | dbscale request cluster user status running | 0                  | none          | 2023-10-31 09:35:45 | 0              | 48           | 1276          | 3          |
+-------------------+---------+--------------------+--------------------------------------+---------------------------------------------+--------------------+---------------+---------------------+----------------+--------------+---------------+------------+
5 rows in set (0.01 sec)


```

## kill cluster_id user_id


```sql
-- 需要通过dbscale show user status 命令返回的 User_id和cluster_id

kill cluster_id user_id
```














