---
title: innodb_trx
description: 当前正在执行的事务
date: 2024-10-09
weight: 20000
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


## 事务信息临时表
> 是一个基于内存的临时表，事务对象

```sql
[dbscale_internal@172.17.137.95:16310 10:13:55(performance_schema)]$ show create table information_schema.innodb_trx\G
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
1 row in set (0.00 sec)

```



| 字段                       | 含义                             | 说明                       |
| :------------------------- | :------------------------------- | :------------------------- |
| trx_id                     | 事务ID                           | 每个事务都会分配一个唯一ID |
| trx_state                  | 事务状态                         |                            |
| trx_started                |                                  |                            |
| trx_requested_lock_id      |                                  |                            |
| trx_wait_started           | 事务开始时间                     |                            |
| trx_weight                 | 事务权重                         |                            |
| trx_mysql_thread_id        | 事务所属会话id                   |                            |
| trx_query                  | 事务正在执行的SQL语句            |                            |
| trx_operation_state        | 事务当前的状态                   |                            |
| trx_tables_in_use          | 事务执行SQL语句时使用的表的数量  |                            |
| trx_tables_locked          |                                  |                            |
| trx_lock_structs           |                                  |                            |
| trx_lock_memory_bytes      |                                  |                            |
| trx_rows_locked            |                                  |                            |
| trx_rows_modified          |                                  |                            |
| trx_concurrency_tickets    |                                  |                            |
| trx_isolation_level        | 事务隔离级别                     |                            |
| trx_unique_checks          |                                  |                            |
| trx_foreign_key_checks     |                                  |                            |
| trx_last_foreign_key_error |                                  |                            |
| trx_adaptive_hash_latched  | 自适应哈希索引是否被当前事务锁定 |                            |
| trx_adaptive_hash_timeout  |                                  |                            |
| trx_is_read_only           | 只读事务标识                     | 1代表只读事务              |
| trx_autocommit_non_locking |                                  |                            |
| trx_schedule_weight        |                                  |                            |





## 启动事务

1. 读写事务，不需立即创建一致性读视图，事务的启动将延迟至实际需要时
   1. begin
   2. begin work
   3. start transaction
   4. start transaction read write


2. 只读事务，不需立即创建一致性读视图，事务的启动将延迟至实际需要时
   1. start transaction read only

3. 开始一个新的读写事务，先启动事务，随后立即创建一致性读视图
   1. start transaction with consistent snapshot
   2. start transaction with consistent snapshot, read write

4. 开始一个新的只读事务，先启动事务，随后立即创建一致性读视图
   1. start transaction with consistent snapshot, read only


```sql
/*  1 */
/*  2 */ begin work
/*  3 */ start transaction
/*  4 */ start transaction read write

/*  5 */ start transaction read only

/*  6 */ start transaction with consistent snapshot
/*  7 */ start transaction with consistent snapshot, read write

/*  8 */ start transaction with consistent snapshot, read only

/*  9 */ start transaction with consistent snapshot, read write, read only
/* 10 */ start transaction read write, read only

```




## 内部事务与用户事务


InnoDB 有几种场景会使用内部事务，以下是其中主要的三种：

- 如果上次关闭MySQL时有未提交，或者正在提交但未提交完成的事务，启动过程中，InnoDB 会把这些事务恢复为内部事务，然后提交或者回滚。

- 后台线程执行一些操作时，需要在内部事务中执行内部 SQL。以 ib_dict_stats 线程为例，它计算各表、索引的统计信息之后，会使用内部事务执行内部 SQL，更新 mysql.innodb_table_stats、mysql.innodb_index_stats 表中的统计信息。

- 为了实现原子操作，DDL语句执行过程中，InnoDB 会使用内部事务执行内部 SQL，插入一些数据到 mysql.innodb_ddl_log 表中。



用事务池管理器来管理事务池，事务池来管理事务对象









```sql

[dbscale_internal@172.17.137.95:16310 10:13:47(performance_schema)]$ select * from  information_schema.innodb_trx\G
*************************** 1. row ***************************
                    trx_id: 12351
                 trx_state: RUNNING
               trx_started: 2024-10-18 09:31:20
     trx_requested_lock_id: NULL
          trx_wait_started: NULL
                trx_weight: 4
       trx_mysql_thread_id: 638055
                 trx_query: NULL
       trx_operation_state: NULL
         trx_tables_in_use: 0
         trx_tables_locked: 1
          trx_lock_structs: 3
     trx_lock_memory_bytes: 1128
           trx_rows_locked: 2
         trx_rows_modified: 1
   trx_concurrency_tickets: 0
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

[dbscale_internal@172.17.137.95:16310 10:13:53(performance_schema)]$ select * from  information_schema.innodb_trx;
+--------+-----------+---------------------+-----------------------+------------------+------------+---------------------+-----------+---------------------+-------------------+-------------------+------------------+-----------------------+-----------------+-------------------+-------------------------+---------------------+-------------------+------------------------+----------------------------+---------------------------+---------------------------+------------------+----------------------------+---------------------+
| trx_id | trx_state | trx_started         | trx_requested_lock_id | trx_wait_started | trx_weight | trx_mysql_thread_id | trx_query | trx_operation_state | trx_tables_in_use | trx_tables_locked | trx_lock_structs | trx_lock_memory_bytes | trx_rows_locked | trx_rows_modified | trx_concurrency_tickets | trx_isolation_level | trx_unique_checks | trx_foreign_key_checks | trx_last_foreign_key_error | trx_adaptive_hash_latched | trx_adaptive_hash_timeout | trx_is_read_only | trx_autocommit_non_locking | trx_schedule_weight |
+--------+-----------+---------------------+-----------------------+------------------+------------+---------------------+-----------+---------------------+-------------------+-------------------+------------------+-----------------------+-----------------+-------------------+-------------------------+---------------------+-------------------+------------------------+----------------------------+---------------------------+---------------------------+------------------+----------------------------+---------------------+
|  12351 | RUNNING   | 2024-10-18 09:31:20 | NULL                  | NULL             |          4 |              638055 | NULL      | NULL                |                 0 |                 1 |                3 |                  1128 |               2 |                 1 |                       0 | READ COMMITTED      |                 1 |                      0 | NULL                       |                         0 |                         0 |                0 |                          0 |                NULL |
+--------+-----------+---------------------+-----------------------+------------------+------------+---------------------+-----------+---------------------+-------------------+-------------------+------------------+-----------------------+-----------------+-------------------+-------------------------+---------------------+-------------------+------------------------+----------------------------+---------------------------+---------------------------+------------------+----------------------------+---------------------+
1 row in set (0.00 sec)




```















