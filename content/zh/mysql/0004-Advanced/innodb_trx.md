---
title: innodb_trx
description: innodb_trx
date: 2023-10-13
weight: 20000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}

**当前运行的所有事务：**
- 正在运行的事务

{{< /alert >}}


### 表结构：innodb_trx


|                       |                          |   |   |
|:----------------------|:-------------------------|:--|:--|
| trx_id                | 事务ID                   |   |   |
| trx_state             | 事务状态                 |   |   |
| trx_started           | 事务开始时间             |   |   |
| trx_requested_lock_id | 事务当前正在等待锁的标识 |   |   |
| trx_wait_started      | 事务开始等待的时间       |   |   |
|                       |                          |   |   |
|                       |                          |   |   |
|                       |                          |   |   |
|                       |                          |   |   |
|                       |                          |   |   |
|                       |                          |   |   |
|                       |                          |   |   |
|                       |                          |   |   |
|                       |                          |   |   |
|                       |                          |   |   |
|                       |                          |   |   |
|                       |                          |   |   |
|                       |                          |   |   |
|                       |                          |   |   |
|                       |                          |   |   |
|                       |                          |   |   |
|                       |                          |   |   |
|                       |                          |   |   |
|                       |                          |   |   |
|                       |                          |   |   |



- trx_id：事务ID。
- **trx_state**：事务状态。有以下几种状态：RUNNING、LOCK WAIT、ROLLING BACK 和 COMMITTING。
- **trx_started**：事务开始时间。将当前时间减去开始时间就可得到事务执行时间
- trx_requested_lock_id：事务当前正在等待锁的标识，可以和 INNODB_LOCKS 表 JOIN 以得到更多详细信息。
- trx_wait_started：事务开始等待的时间。
- trx_weight：事务的权重。
- trx_mysql_thread_id：事务线程 ID，可以和 PROCESSLIST 表 JOIN。
- trx_query：事务正在执行的 SQL 语句。但事务的trx_query是NUL，这并不是说事务什么也没执行，一个事务可能包含多个SQL，如果SQL执行完毕就不再显示了。当前事务正在执行，innodb也不知道这个事务后续还有没有sql,啥时候会commit。 因此trx_query不能提供有意义的信息。
- trx_operation_state：事务当前操作状态。
- trx_tables_in_use：当前事务执行的 SQL 中使用的表的个数。
- trx_tables_locked：当前执行 SQL 的行锁数量。
- trx_lock_structs：事务保留的锁数量。
- trx_lock_memory_bytes：事务锁住的内存大小，单位为 BYTES。
- trx_rows_locked：事务锁住的记录数。包含标记为 DELETED，并且已经保存到磁盘但对事务不可见的行。
- trx_rows_modified：事务更改的行数。
- trx_concurrency_tickets：事务并发票数。
- trx_isolation_level：当前事务的隔离级别。
- trx_unique_checks：是否打开唯一性检查的标识。
- trx_foreign_key_checks：是否打开外键检查的标识。
- trx_last_foreign_key_error：最后一次的外键错误信息。
- trx_adaptive_hash_latched：自适应散列索引是否被当前事务锁住的标识。
- trx_adaptive_hash_timeout：是否立刻放弃为自适应散列索引搜索 LATCH 的标识。

在结果中idle_time是计算产生的，也是事务的持续时间。








```sql
[root@127.0.0.1:8032 15:35:09(information_schema)]$ show create table innodb_trx\G
*************************** 1. row ***************************
       Table: INNODB_TRX
Create Table: CREATE TEMPORARY TABLE `INNODB_TRX` (
  `trx_id` bigint unsigned NOT NULL DEFAULT '0',
  `trx_state` varchar(13) NOT NULL DEFAULT '',
  `trx_started` datetime NOT NULL DEFAULT '0000-00-00 00:00:00',
  `trx_requested_lock_id` varchar(105) DEFAULT NULL,
  `trx_wait_started` datetime DEFAULT NULL,
  `trx_weight` bigint unsigned NOT NULL DEFAULT '0',
  `trx_mysql_thread_id` bigint unsigned NOT NULL DEFAULT '0',
  `trx_query` varchar(1024) DEFAULT NULL,
  `trx_operation_state` varchar(64) DEFAULT NULL,
  `trx_tables_in_use` bigint unsigned NOT NULL DEFAULT '0',
  `trx_tables_locked` bigint unsigned NOT NULL DEFAULT '0',
  `trx_lock_structs` bigint unsigned NOT NULL DEFAULT '0',
  `trx_lock_memory_bytes` bigint unsigned NOT NULL DEFAULT '0',
  `trx_rows_locked` bigint unsigned NOT NULL DEFAULT '0',
  `trx_rows_modified` bigint unsigned NOT NULL DEFAULT '0',
  `trx_concurrency_tickets` bigint unsigned NOT NULL DEFAULT '0',
  `trx_isolation_level` varchar(16) NOT NULL DEFAULT '',
  `trx_unique_checks` int NOT NULL DEFAULT '0',
  `trx_foreign_key_checks` int NOT NULL DEFAULT '0',
  `trx_last_foreign_key_error` varchar(256) DEFAULT NULL,
  `trx_adaptive_hash_latched` int NOT NULL DEFAULT '0',
  `trx_adaptive_hash_timeout` bigint unsigned NOT NULL DEFAULT '0',
  `trx_is_read_only` int NOT NULL DEFAULT '0',
  `trx_autocommit_non_locking` int NOT NULL DEFAULT '0',
  `trx_schedule_weight` bigint unsigned DEFAULT NULL
) ENGINE=MEMORY DEFAULT CHARSET=utf8mb3
1 row in set (0.01 sec)

```









```sql
mysql> select * from innodb_trx\G
*************************** 1. row ***************************
                    trx_id: 3256133
                 trx_state: LOCK WAIT
               trx_started: 2023-12-11 20:51:22
     trx_requested_lock_id: 140603514388480:625:4:10:140603404924840
          trx_wait_started: 2023-12-11 20:53:42
                trx_weight: 2
       trx_mysql_thread_id: 18
                 trx_query: delete from  user where id <5
       trx_operation_state: starting index read
         trx_tables_in_use: 1
         trx_tables_locked: 1
          trx_lock_structs: 2
     trx_lock_memory_bytes: 1128
           trx_rows_locked: 2
         trx_rows_modified: 0
   trx_concurrency_tickets: 0
       trx_isolation_level: READ COMMITTED
         trx_unique_checks: 1
    trx_foreign_key_checks: 1
trx_last_foreign_key_error: NULL
 trx_adaptive_hash_latched: 0
 trx_adaptive_hash_timeout: 0
          trx_is_read_only: 0
trx_autocommit_non_locking: 0
       trx_schedule_weight: 1
*************************** 2. row ***************************
                    trx_id: 3256128
                 trx_state: RUNNING
               trx_started: 2023-12-11 20:02:16
     trx_requested_lock_id: NULL
          trx_wait_started: NULL
                trx_weight: 5
       trx_mysql_thread_id: 20
                 trx_query: NULL
       trx_operation_state: NULL
         trx_tables_in_use: 0
         trx_tables_locked: 1
          trx_lock_structs: 2
     trx_lock_memory_bytes: 1128
           trx_rows_locked: 3
         trx_rows_modified: 3
   trx_concurrency_tickets: 0
       trx_isolation_level: READ COMMITTED
         trx_unique_checks: 1
    trx_foreign_key_checks: 1
trx_last_foreign_key_error: NULL
 trx_adaptive_hash_latched: 0
 trx_adaptive_hash_timeout: 0
          trx_is_read_only: 0
trx_autocommit_non_locking: 0
       trx_schedule_weight: NULL
2 rows in set (0.00 sec)


```



```sql
[root@127.0.0.1:8032 15:38:47(information_schema)]$ select * from information_schema.innodb_trx limit 2\G
*************************** 1. row ***************************
                    trx_id: 45630
                 trx_state: RUNNING
               trx_started: 2024-05-20 15:38:52
     trx_requested_lock_id: NULL
          trx_wait_started: NULL
                trx_weight: 3
       trx_mysql_thread_id: 1356
                 trx_query: UPDATE sbtest7 SET c='77583971716-27519458351-78255210243-17302086038-25081638578-77806016790-27805454800-20949010080-88506697709-37094886558' WHERE id=1480311
       trx_operation_state: sleeping before entering InnoDB
         trx_tables_in_use: 1
         trx_tables_locked: 1
          trx_lock_structs: 2
     trx_lock_memory_bytes: 1128
           trx_rows_locked: 1
         trx_rows_modified: 1
   trx_concurrency_tickets: 0
       trx_isolation_level: READ COMMITTED
         trx_unique_checks: 1
    trx_foreign_key_checks: 1
trx_last_foreign_key_error: NULL
 trx_adaptive_hash_latched: 0
 trx_adaptive_hash_timeout: 0
          trx_is_read_only: 0
trx_autocommit_non_locking: 0
       trx_schedule_weight: NULL
*************************** 2. row ***************************
                    trx_id: 45629
                 trx_state: RUNNING
               trx_started: 2024-05-20 15:38:51
     trx_requested_lock_id: NULL
          trx_wait_started: NULL
                trx_weight: 9
       trx_mysql_thread_id: 1358
                 trx_query: INSERT INTO sbtest1 (id, k, c, pad) VALUES (666811, 4948416, '64838046081-33221581153-61908727159-05912984702-11791589951-49484826330-63528045939-15798972566-13868863421-13476976359', '97505044137-58271750926-16285018360-06182139430-44408155593')
       trx_operation_state: sleeping before entering InnoDB
         trx_tables_in_use: 1
         trx_tables_locked: 3
          trx_lock_structs: 6
     trx_lock_memory_bytes: 1128
           trx_rows_locked: 3
         trx_rows_modified: 3
   trx_concurrency_tickets: 0
       trx_isolation_level: READ COMMITTED
         trx_unique_checks: 1
    trx_foreign_key_checks: 1
trx_last_foreign_key_error: NULL
 trx_adaptive_hash_latched: 0
 trx_adaptive_hash_timeout: 0
          trx_is_read_only: 0
trx_autocommit_non_locking: 0
       trx_schedule_weight: NULL
2 rows in set (0.00 sec)

[root@127.0.0.1:8032 15:38:52(information_schema)]$

```




```sql

[dbscale_internal@172.17.134.77:16310 14:04:54(apple)]$ select * from information_schema.innodb_trx\G
*************************** 1. row ***************************
                    trx_id: 303320
                 trx_state: RUNNING
               trx_started: 2024-06-06 10:53:57
     trx_requested_lock_id: NULL
          trx_wait_started: NULL
                trx_weight: 1231842
       trx_mysql_thread_id: 512010
                 trx_query: update sbtest1 set k=1232345 where id > 1000
       trx_operation_state: updating or deleting
         trx_tables_in_use: 1
         trx_tables_locked: 3
          trx_lock_structs: 84220
     trx_lock_memory_bytes: 9527416
           trx_rows_locked: 6147622
         trx_rows_modified: 1147622
   trx_concurrency_tickets: 4205
       trx_isolation_level: READ COMMITTED
         trx_unique_checks: 1
    trx_foreign_key_checks: 0
trx_last_foreign_key_error: NULL
 trx_adaptive_hash_latched: 0
 trx_adaptive_hash_timeout: 0
          trx_is_read_only: 0
trx_autocommit_non_locking: 0
       trx_schedule_weight: NULL
1 row in set (0.00 sec)

[dbscale_internal@172.17.134.77:16310 14:05:18(apple)]$ select * from information_schema.innodb_trx\G
*************************** 1. row ***************************
                    trx_id: 303320
                 trx_state: RUNNING
               trx_started: 2024-06-06 10:53:57
     trx_requested_lock_id: NULL
          trx_wait_started: NULL
                trx_weight: 1303796
       trx_mysql_thread_id: 512010
                 trx_query: update sbtest1 set k=1232345 where id > 1000
       trx_operation_state: fetching rows
         trx_tables_in_use: 1
         trx_tables_locked: 3
          trx_lock_structs: 85193
     trx_lock_memory_bytes: 9625720
           trx_rows_locked: 6218603
         trx_rows_modified: 1218603
   trx_concurrency_tickets: 2270
       trx_isolation_level: READ COMMITTED
         trx_unique_checks: 1
    trx_foreign_key_checks: 0
trx_last_foreign_key_error: NULL
 trx_adaptive_hash_latched: 0
 trx_adaptive_hash_timeout: 0
          trx_is_read_only: 0
trx_autocommit_non_locking: 0
       trx_schedule_weight: NULL
1 row in set (0.00 sec)

```



