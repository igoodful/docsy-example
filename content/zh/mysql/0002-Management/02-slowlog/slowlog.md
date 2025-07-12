---
title: 慢日志详解
description: slowlog
date: 2024-10-31
weight: 1000
viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{<alert  color="secondary">}}



{{</alert>}}

慢日志示例

```bash
# Time: 2024-12-17T10:56:05.750526+08:00
# User@Host: dbscale_internal[dbscale_internal] @  [172.17.135.250]  Id:    11
# Query_time: 1.435436  Lock_time: 0.000178 Rows_sent: 3  Rows_examined: 5000000
SET timestamp=1734404164;
select sex,count(*) from sbtest1 group by sex;
# Time: 2024-12-20T11:13:15.258070+08:00
# User@Host: dbscale_internal[dbscale_internal] @  [172.17.135.250]  Id: 976595
# Query_time: 1.588147  Lock_time: 0.000802 Rows_sent: 1  Rows_examined: 198
SET timestamp=1734664393;
select count(*) from sbtest1 as t1 inner join sbtest2 as t2 on t1.k=t2.id where t1.id<100;
# Time: 2024-12-22T09:35:35.523732+08:00
# User@Host: admin[admin] @  [127.0.0.1]  Id: 1542200
# Query_time: 82.466460  Lock_time: 0.000186 Rows_sent: 1  Rows_examined: 102000 Thread_id: 1542200 Errno: 0 Killed: 0 Bytes_received: 0 Bytes_sent: 64 Read_first: 2 Read_last: 0 Read_key: 2 Read_next: 0 Read_prev: 0 Read_rnd: 0 Read_rnd_next: 102002 Sort_merge_passes: 0 Sort_range_count: 0 Sort_rows: 0 Sort_scan_count: 0 Created_tmp_disk_tables: 0 Created_tmp_tables: 0 Start: 2024-12-22T09:34:13.057272+08:00 End: 2024-12-22T09:35:35.523732+08:00
use db1;
SET timestamp=1734831253;
select count(*) from sbtest1 t1 inner join sbtest2 t2 on t1.c<t2.c;

```








## vim /etc/profile

```bash
PS1='\[\033[01;37m\]\D{%Y-%m-%d}T\t \[\033[01;37m\][\[\033[01;35m\]\u@\H \[\033[01;34m\]$(pwd)\[\033[01;37m\]] \[\033[01;36m\]$ \[\033[00m\]'
export MYSQL_PS1="[\U:\p \R:\m:\s(\d)]$ "
export LANG="en_US.UTF-8"

```

## 参数配置

```sql
[dbscale_internal@172.17.135.250:16310 09:47:46((none))]$ show global variables like '%query%';
+------------------------------+------------------------------------------------------------------------------+
| Variable_name                | Value                                                                        |
+------------------------------+------------------------------------------------------------------------------+
| binlog_rows_query_log_events | ON                                                                           |
| ft_query_expansion_limit     | 20                                                                           |
| have_query_cache             | NO                                                                           |
| long_query_time              | 1.000000                                                                     |
| query_alloc_block_size       | 8192                                                                         |
| query_prealloc_size          | 8192                                                                         |
| slow_query_log               | ON                                                                           |
| slow_query_log_file          | /data/mysqldata/16315fff-2bb1-4e4e-ac0e-4f7b5d22693e/logfile/mysqld-slow.log |
+------------------------------+------------------------------------------------------------------------------+
8 rows in set (0.00 sec)

[dbscale_internal@172.17.135.250:16310 09:47:58((none))]$ show global variables like '%queries%';
+----------------------------------------+-------+
| Variable_name                          | Value |
+----------------------------------------+-------+
| log_queries_not_using_indexes          | OFF   |
| log_throttle_queries_not_using_indexes | 0     |
+----------------------------------------+-------+
2 rows in set (0.01 sec)

[dbscale_internal@172.17.135.250:16310 09:48:31((none))]$ show global variables like '%slow%';
+-----------------------------+------------------------------------------------------------------------------+
| Variable_name               | Value                                                                        |
+-----------------------------+------------------------------------------------------------------------------+
| log_slow_admin_statements   | OFF                                                                          |
| log_slow_extra              | ON                                                                           |
| log_slow_replica_statements | OFF                                                                          |
| log_slow_slave_statements   | OFF                                                                          |
| slow_launch_time            | 2                                                                            |
| slow_query_log              | ON                                                                           |
| slow_query_log_file         | /data/mysqldata/16315fff-2bb1-4e4e-ac0e-4f7b5d22693e/logfile/mysqld-slow.log |
+-----------------------------+------------------------------------------------------------------------------+
7 rows in set (0.00 sec)

[dbscale_internal@172.17.135.250:16310 09:48:41((none))]$


```

- slow_query_log=on 慢日志开启
- log_output=file 慢日志记录方式为文件
- slow_query_log_file=/data/mysqldata/16315fff-2bb1-4e4e-ac0e-4f7b5d22693e/logfile/mysqld-slow.log 慢日志文件路径
- long_query_time=1 慢日志阈值为1秒
- log_slow_extra=on 开启慢日志记录更详细，在慢查询日志文件中，写入慢SQL的额外的字段。
- log_queries_not_using_indexes=off 不记录未使用索引的查询
- log_throttle_queries_not_using_indexes=0  不限制每分钟未使用索引的查询记录次数
- log_slow_admin_statements=off 不包括慢的管理语
- min_examined_row_limit=0 不限制检查行数限制。SQL检查行数少于这个数量，则不会被记录到慢查询日志中。




## 测试SQL


### 1. `# Time: `时间含义

> `# Time: `是慢SQL结束的时间，不是慢SQL的开始时间，值等于`log_slow_extra=on`开启时的额外字段`End: `


```sql
[dbscale_internal@172.17.135.250:16310 09:39:11((none))]$ select sleep(60);
+-----------+
| sleep(60) |
+-----------+
|         0 |
+-----------+
1 row in set (1 min 0.01 sec)

[dbscale_internal@172.17.135.250:16310 09:40:12((none))]$

```


```sql
# Time: 2024-12-22T09:40:12.443324+08:00
# User@Host: dbscale_internal[dbscale_internal] @  [172.17.135.250]  Id: 977035
# Query_time: 60.000403  Lock_time: 0.000000 Rows_sent: 1  Rows_examined: 1 Thread_id: 977035 Errno: 0 Killed: 0 Bytes_received: 0 Bytes_sent: 64 Read_first: 0 Read_last: 0 Read_key: 0 Read_next: 0 Read_prev: 0 Read_rnd: 0 Read_rnd_next: 0 Sort_merge_passes: 0 Sort_range_count: 0 Sort_rows: 0 Sort_scan_count: 0 Created_tmp_disk_tables: 0 Created_tmp_tables: 0 Start: 2024-12-22T09:39:12.442921+08:00 End: 2024-12-22T09:40:12.443324+08:00
SET timestamp=1734831552;
select sleep(60);

```

### 2. `SET timestamp=`时间含义

> `# Time: `是慢SQL的开始时间，不是慢SQL的结束时间，值等于`log_slow_extra=on`开启时的额外字段`Start: `


```sql
[dbscale_internal@172.17.135.250:16310 09:39:11((none))]$ select sleep(60);
+-----------+
| sleep(60) |
+-----------+
|         0 |
+-----------+
1 row in set (1 min 0.01 sec)

[dbscale_internal@172.17.135.250:16310 09:40:12((none))]$

```


```sql
# Time: 2024-12-22T09:40:12.443324+08:00
# User@Host: dbscale_internal[dbscale_internal] @  [172.17.135.250]  Id: 977035
# Query_time: 60.000403  Lock_time: 0.000000 Rows_sent: 1  Rows_examined: 1 Thread_id: 977035 Errno: 0 Killed: 0 Bytes_received: 0 Bytes_sent: 64 Read_first: 0 Read_last: 0 Read_key: 0 Read_next: 0 Read_prev: 0 Read_rnd: 0 Read_rnd_next: 0 Sort_merge_passes: 0 Sort_range_count: 0 Sort_rows: 0 Sort_scan_count: 0 Created_tmp_disk_tables: 0 Created_tmp_tables: 0 Start: 2024-12-22T09:39:12.442921+08:00 End: 2024-12-22T09:40:12.443324+08:00
SET timestamp=1734831552;
select sleep(60);

```

### 3. `User@Host: `含义
> performance_schema.processlist表的user和host字段，表示执行慢SQL的用户和IP地址


### 4. `Id: `含义

> performance_schema.processlist表的id或performance_schema.threads表的processlist_id


### 5. `Query_time: `含义

> 慢SQL执行消耗的时间（8.0.26不包含锁等待时间）



### 6. `Lock_time: `含义

> 慢SQL执行时锁等待时间




### 7. `Rows_sent: `含义

> 慢SQL返回的行数




### 8. `Rows_examined: `含义

> 慢SQL需要扫描的行数




### 81. `Created_tmp_disk_tables: `含义

> 这个字段表示查询在执行过程中创建了多少个磁盘临时表。磁盘临时表通常是在内存临时表不够用时创建的（例如，当临时表的数据超出了 tmp_table_size 和 max_heap_table_size 配置的内存限制）。


### 81. `Created_tmp_tables: `含义

> 这个字段表示查询在执行过程中创建了多少个临时表，无论这些临时表是存储在内存中还是磁盘上。它包括所有类型的临时表（内存临时表和磁盘临时表）


### 71. `Start: `含义

> 慢SQL查询的开始时间




### 81. `End: `含义

> 慢SQL查询的结束时间



### 81. `select sleep(60);`含义

> 慢查询的SQL语句









