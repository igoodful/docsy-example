---
title: 排查问题
description: 排查问题
date: 2023-10-31
weight: 10000


---

{{<caution>}}
<!---->
```sql
dbscale show session | instance | global options like '%option_name%';

dbscale set global 'log-level' = 'DEBUG';

dbscale flush config to file 'file.cfg';
```
{{</caution>}}




{{<alert title="router配置持久化" color="secondary">}}
```sql
dbscale flush config to file 'file.cfg';
```
{{</alert>}}



```sql
show global variables like '%rpl_semi_sync%';
show global variables like '%read_only%';


```



## master

```sql
mysql> show global variables like '%read_only%';
+-----------------------+-------+
| Variable_name         | Value |
+-----------------------+-------+
| innodb_read_only      | OFF   |
| read_only             | OFF   |
| super_read_only       | OFF   |
| transaction_read_only | OFF   |
+-----------------------+-------+
4 rows in set (0.01 sec)

```



```sql
mysql> dbscale show dataservers\G
*************************** 1. row ***************************
                    servername: m
                          host: 10.5.58.178
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
                    servername: database_2
                          host: 10.5.58.180
                          port: 16315
                      username: dbscale_internal
                        status: Server normal, semisync down
          master-online-status: Not_A_Master
                 master_backup: 1
                   remote_user: root
                   remote_port: 22
max_needed_conn/max_mysql_conn:
               master_priority: 0
*************************** 3. row ***************************
                    servername: database_3
                          host: 10.5.58.179
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

mysql> dbscale disable dataserver database_2;
Query OK, 0 rows affected (0.04 sec)

mysql> dbscale enable dataserver database_2;
Query OK, 0 rows affected (0.02 sec)

mysql> dbscale show dataservers\G
*************************** 1. row ***************************
                    servername: database_1
                          host: 10.5.58.178
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
                    servername: database_2
                          host: 10.5.58.180
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
                    servername: database_3
                          host: 10.5.58.179
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


