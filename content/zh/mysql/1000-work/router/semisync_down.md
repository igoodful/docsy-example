---
title: semisync down
date: 2023-10-24
description: semisync down


weight: 70000
---


> 主上 Rpl_semi_sync_slave_status 应该是 off ，Rpl_semi_sync_master_status 为on

> 从上 Rpl_semi_sync_slave_status 应该是 on ， Rpl_semi_sync_master_status 为off

不是的话，直接登录到后端直接执行以下命令:
master上:`set global Rpl_semi_sync_master_enabled=1;`

slave上在同一行上执行命令
```sql
set global Rpl_semi_sync_slave_enabled=1;

stop slave io_thread;

start slave io_thread;
```

如果还是存在问题,slave上执行以下命令
```sql
stop slave;

start slave;
```


然后再查看一下
```sql
dbscale show dataservers;
```

等catching master状态消失后,确认一下是否需要恢复拓扑.


### 主库出现：Server normal, semisync down

```sql

mysql> dbscale show dataservers\G
*************************** 1. row ***************************
                    servername: normal_0_0
                          host: 172.17.134.71
                          port: 16315
                      username: dbscale_internal
                        status: Server normal, semisync down
          master-online-status: Master_Online
                 master_backup: 1
                   remote_user: root
                   remote_port: 22
max_needed_conn/max_mysql_conn:
               master_priority: 0
*************************** 2. row ***************************
                    servername: normal_0_1
                          host: 172.17.134.77
                          port: 16315
                      username: dbscale_internal
                        status: Slave normal
          master-online-status: Not_A_Master
                 master_backup: 1
                   remote_user: root
                   remote_port: 22
max_needed_conn/max_mysql_conn:
               master_priority: 0
*************************** 3. row ***************************
                    servername: normal_0_2
                          host: 172.17.134.76
                          port: 16315
                      username: dbscale_internal
                        status: Slave normal
          master-online-status: Not_A_Master
                 master_backup: 1
                   remote_user: root
                   remote_port: 22
max_needed_conn/max_mysql_conn:
               master_priority: 0
3 rows in set (0.00 sec)


mysql> dbscale execute on  dataserver normal_0_0 "set global rpl_semi_sync_master_enabled=on;";
Query OK, 0 rows affected (0.00 sec)

mysql> dbscale show dataservers\G
*************************** 1. row ***************************
                    servername: normal_0_0
                          host: 172.17.134.71
                          port: 16315
                      username: dbscale_internal
                        status: Server normal
          master-online-status: Master_Online
                 master_backup: 1
                   remote_user: root
                   remote_port: 22
max_needed_conn/max_mysql_conn:
               master_priority: 0
*************************** 2. row ***************************
                    servername: normal_0_1
                          host: 172.17.134.77
                          port: 16315
                      username: dbscale_internal
                        status: Slave normal
          master-online-status: Not_A_Master
                 master_backup: 1
                   remote_user: root
                   remote_port: 22
max_needed_conn/max_mysql_conn:
               master_priority: 0
*************************** 3. row ***************************
                    servername: normal_0_2
                          host: 172.17.134.76
                          port: 16315
                      username: dbscale_internal
                        status: Slave normal
          master-online-status: Not_A_Master
                 master_backup: 1
                   remote_user: root
                   remote_port: 22
max_needed_conn/max_mysql_conn:
               master_priority: 0
3 rows in set (0.00 sec)



```


```sql
mysql> show global status like '%semi%';
+--------------------------------------------+--------+
| Variable_name                              | Value  |
+--------------------------------------------+--------+
| Rpl_semi_sync_master_clients               | 2      |
| Rpl_semi_sync_master_net_avg_wait_time     | 0      |
| Rpl_semi_sync_master_net_wait_time         | 0      |
| Rpl_semi_sync_master_net_waits             | 64     |
| Rpl_semi_sync_master_no_times              | 1      |
| Rpl_semi_sync_master_no_tx                 | 0      |
| Rpl_semi_sync_master_status                | ON     |
| Rpl_semi_sync_master_timefunc_failures     | 0      |
| Rpl_semi_sync_master_tx_avg_wait_time      | 20247  |
| Rpl_semi_sync_master_tx_wait_time          | 647906 |
| Rpl_semi_sync_master_tx_waits              | 32     |
| Rpl_semi_sync_master_wait_pos_backtraverse | 0      |
| Rpl_semi_sync_master_wait_sessions         | 0      |
| Rpl_semi_sync_master_yes_tx                | 32     |
| Rpl_semi_sync_slave_status                 | OFF    |
+--------------------------------------------+--------+
15 rows in set (0.00 sec)


```


### 从库出现 Server normal, semisync down


1. 登录router节点执行：`dbscale show dataservers;`
```sql
mysql> dbscale show dataservers;
+------------+-----------+-------+------------------+------------------------------+----------------------+---------------+-------------+-------------+--------------------------------+-----------------+
| servername | host      | port  | username         | status                       | master-online-status | master_backup | remote_user | remote_port | max_needed_conn/max_mysql_conn | master_priority |
+------------+-----------+-------+------------------+------------------------------+----------------------+---------------+-------------+-------------+--------------------------------+-----------------+
| database_1 | 10.5.43.1 | 16315 | dbscale_internal | Server normal                | Master_Online        | 1             | root        | 22          |                                | 0               |
| database_2 | 10.5.43.2 | 16315 | dbscale_internal | Server normal, semisync down | Not_A_Master         | 1             | root        | 22          |                                | 0               |
| database_3 | 10.5.43.3 | 16315 | dbscale_internal | Slave normal                 | Not_A_Master         | 1             | root        | 22          |                                | 0               |
+------------+-----------+-------+------------------+------------------------------+----------------------+---------------+-------------+-------------+--------------------------------+-----------------+
3 rows in set (0.00 sec)

```

2. 登录从库执行：`set global rpl_semi_sync_slave_enabled = on;stop slave;start slave;`
```sql
mysql> show global variables like '%semi%';
+-------------------------------------------+------------+
| Variable_name                             | Value      |
+-------------------------------------------+------------+
| rpl_semi_sync_master_enabled              | OFF        |
| rpl_semi_sync_master_timeout              | 10000      |
| rpl_semi_sync_master_trace_level          | 32         |
| rpl_semi_sync_master_wait_for_slave_count | 1          |
| rpl_semi_sync_master_wait_no_slave        | ON         |
| rpl_semi_sync_master_wait_point           | AFTER_SYNC |
| rpl_semi_sync_slave_enabled               | OFF        |
| rpl_semi_sync_slave_trace_level           | 32         |
+-------------------------------------------+------------+
8 rows in set (0.00 sec)

mysql> set global rpl_semi_sync_slave_enabled = on;
Query OK, 0 rows affected (0.00 sec)

mysql> show global variables like '%semi%';
+-------------------------------------------+------------+
| Variable_name                             | Value      |
+-------------------------------------------+------------+
| rpl_semi_sync_master_enabled              | OFF        |
| rpl_semi_sync_master_timeout              | 10000      |
| rpl_semi_sync_master_trace_level          | 32         |
| rpl_semi_sync_master_wait_for_slave_count | 1          |
| rpl_semi_sync_master_wait_no_slave        | ON         |
| rpl_semi_sync_master_wait_point           | AFTER_SYNC |
| rpl_semi_sync_slave_enabled               | ON         |
| rpl_semi_sync_slave_trace_level           | 32         |
+-------------------------------------------+------------+
8 rows in set (0.00 sec)

mysql> stop slave;start slave;
Query OK, 0 rows affected, 1 warning (0.01 sec)

Query OK, 0 rows affected, 1 warning (0.03 sec)


```

3. 登录router节点执行：`dbscale show dataservers;`
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

```



