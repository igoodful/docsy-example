---
title: data_lock_waits
description: data_lock_waits
date: 2023-10-26
weight: 20000
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


{{<alert color="secondary">}}

本文基于 MySQL 8.0.32 源码，存储引擎为 InnoDB。

格式化：Markdown Table Prettifier

每个会话对应一个MySQL线程

每个MySQL线程最多开启一个事务

一个事务可有多个表锁和行锁

- [https://www.dpriver.com/pp/sqlformat.htm](https://www.dpriver.com/pp/sqlformat.htm)
- [https://dev.mysql.com/doc/refman/8.0/en/innodb-information-schema-examples.html?spm=a2c9r.14518950.0.0.7deb5e3bM4Z4PN](https://dev.mysql.com/doc/refman/8.0/en/innodb-information-schema-examples.html?spm=a2c9r.14518950.0.0.7deb5e3bM4Z4PN)

{{</alert>}}



### 表结构：data_lock_waits



```sql
[root@127.0.0.1:8032 11:16:20(performance_schema)]$ show create table data_lock_waits\G
*************************** 1. row ***************************
       Table: data_lock_waits
Create Table: CREATE TABLE `data_lock_waits` (
  `ENGINE` varchar(32) NOT NULL,
  `REQUESTING_ENGINE_LOCK_ID` varchar(128) NOT NULL,
  `REQUESTING_ENGINE_TRANSACTION_ID` bigint unsigned DEFAULT NULL,
  `REQUESTING_THREAD_ID` bigint unsigned DEFAULT NULL,
  `REQUESTING_EVENT_ID` bigint unsigned DEFAULT NULL,
  `REQUESTING_OBJECT_INSTANCE_BEGIN` bigint unsigned NOT NULL,
  `BLOCKING_ENGINE_LOCK_ID` varchar(128) NOT NULL,
  `BLOCKING_ENGINE_TRANSACTION_ID` bigint unsigned DEFAULT NULL,
  `BLOCKING_THREAD_ID` bigint unsigned DEFAULT NULL,
  `BLOCKING_EVENT_ID` bigint unsigned DEFAULT NULL,
  `BLOCKING_OBJECT_INSTANCE_BEGIN` bigint unsigned NOT NULL,
  KEY `REQUESTING_ENGINE_LOCK_ID` (`REQUESTING_ENGINE_LOCK_ID`,`ENGINE`),
  KEY `BLOCKING_ENGINE_LOCK_ID` (`BLOCKING_ENGINE_LOCK_ID`,`ENGINE`),
  KEY `REQUESTING_ENGINE_TRANSACTION_ID` (`REQUESTING_ENGINE_TRANSACTION_ID`,`ENGINE`),
  KEY `BLOCKING_ENGINE_TRANSACTION_ID` (`BLOCKING_ENGINE_TRANSACTION_ID`,`ENGINE`),
  KEY `REQUESTING_THREAD_ID` (`REQUESTING_THREAD_ID`,`REQUESTING_EVENT_ID`),
  KEY `BLOCKING_THREAD_ID` (`BLOCKING_THREAD_ID`,`BLOCKING_EVENT_ID`)
) ENGINE=PERFORMANCE_SCHEMA DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)

[root@127.0.0.1:8032 13:54:54(performance_schema)]$


```


















| 列名称                           | 功能 | 说明 |
| :------------------------------- | :--- | :--- |
| engine                           |      |      |
| requesting_engine_lock_id        |      |      |
| requesting_engine_transaction_id |      |      |
| requesting_thread_id             |      |      |
| requesting_event_id              |      |      |
| requesting_object_instance_begin |      |      |
| blocking_engine_lock_id          |      |      |
| blocking_engine_transaction_id   |      |      |
| blocking_thread_id               |      |      |
| blocking_event_id                |      |      |
| blocking_object_instance_begin   |      |      |




#### sys.innodb_lock_waits

```sql
CREATE ALGORITHM=TEMPTABLE DEFINER=`mysql.sys`@`localhost` SQL SECURITY INVOKER VIEW
`sys`.`innodb_lock_waits`
(`wait_started`,
`wait_age`,
`wait_age_secs`,
`locked_table`,
`locked_table_schema`,
`locked_table_name`,
`locked_table_partition`,
`locked_table_subpartition`,
`locked_index`,
`locked_type`,
`waiting_trx_id`,
`waiting_trx_started`,
`waiting_trx_age`,
`waiting_trx_rows_locked`,
`waiting_trx_rows_modified`,
`waiting_pid`,
`waiting_query`,
`waiting_lock_id`,
`waiting_lock_mode`,
`blocking_trx_id`,
`blocking_pid`,
`blocking_query`,
`blocking_lock_id`,
`blocking_lock_mode`,
`blocking_trx_started`,
`blocking_trx_age`,
`blocking_trx_rows_locked`,
`blocking_trx_rows_modified`,
`sql_kill_blocking_query`,
`sql_kill_blocking_connection`)
AS select `r`.`trx_wait_started` AS `wait_started`,
timediff(now(),`r`.`trx_wait_started`) AS `wait_age`,
timestampdiff(SECOND,`r`.`trx_wait_started`,now()) AS `wait_age_secs`,
concat(`sys`.`quote_identifier`(`rl`.`OBJECT_SCHEMA`),'.',`sys`.`quote_identifier`(`rl`.`OBJECT_NAME`)) AS `locked_table`,
`rl`.`OBJECT_SCHEMA` AS `locked_table_schema`,
`rl`.`OBJECT_NAME` AS `locked_table_name`,
`rl`.`PARTITION_NAME` AS `locked_table_partition`,
`rl`.`SUBPARTITION_NAME` AS `locked_table_subpartition`,
`rl`.`INDEX_NAME` AS `locked_index`,
`rl`.`LOCK_TYPE` AS `locked_type`,
`r`.`trx_id` AS `waiting_trx_id`,
`r`.`trx_started` AS `waiting_trx_started`,
timediff(now(),`r`.`trx_started`) AS `waiting_trx_age`,
`r`.`trx_rows_locked` AS `waiting_trx_rows_locked`,
`r`.`trx_rows_modified` AS `waiting_trx_rows_modified`,
`r`.`trx_mysql_thread_id` AS `waiting_pid`,
`sys`.`format_statement`(`r`.`trx_query`) AS `waiting_query`,
`rl`.`ENGINE_LOCK_ID` AS `waiting_lock_id`,
`rl`.`LOCK_MODE` AS `waiting_lock_mode`,
`b`.`trx_id` AS `blocking_trx_id`,
`b`.`trx_mysql_thread_id` AS `blocking_pid`,
`sys`.`format_statement`(`b`.`trx_query`) AS `blocking_query`,
`bl`.`ENGINE_LOCK_ID` AS `blocking_lock_id`,
`bl`.`LOCK_MODE` AS `blocking_lock_mode`,
`b`.`trx_started` AS `blocking_trx_started`,
timediff(now(),`b`.`trx_started`) AS `blocking_trx_age`,
`b`.`trx_rows_locked` AS `blocking_trx_rows_locked`,
`b`.`trx_rows_modified` AS `blocking_trx_rows_modified`,
concat('KILL QUERY ',`b`.`trx_mysql_thread_id`) AS `sql_kill_blocking_query`,
concat('KILL ',`b`.`trx_mysql_thread_id`) AS `sql_kill_blocking_connection`
from
(
      (
          (
             (`data_lock_waits` `w` join `information_schema`.`INNODB_TRX` `b` on((`b`.`trx_id` = cast(`w`.`BLOCKING_ENGINE_TRANSACTION_ID` as char charset utf8mb4))))
               join `information_schema`.`INNODB_TRX` `r` on((`r`.`trx_id` = cast(`w`.`REQUESTING_ENGINE_TRANSACTION_ID` as char charset utf8mb4)))
          )
          join `data_locks` `bl` on((`bl`.`ENGINE_LOCK_ID` = `w`.`BLOCKING_ENGINE_LOCK_ID`))
      )
      join `data_locks` `rl` on((`rl`.`ENGINE_LOCK_ID` = `w`.`REQUESTING_ENGINE_LOCK_ID`))
)
order by `r`.`trx_wait_started`


[root@127.0.0.1:8032 13:59:24(performance_schema)]$ desc sys.innodb_lock_waits;
+------------------------------+-----------------+------+-----+---------------------+-------+
| Field                        | Type            | Null | Key | Default             | Extra |
+------------------------------+-----------------+------+-----+---------------------+-------+
| wait_started                 | datetime        | YES  |     | NULL                |       |
| wait_age                     | time            | YES  |     | NULL                |       |
| wait_age_secs                | bigint          | YES  |     | NULL                |       |
| locked_table                 | mediumtext      | YES  |     | NULL                |       |
| locked_table_schema          | varchar(64)     | YES  |     | NULL                |       |
| locked_table_name            | varchar(64)     | YES  |     | NULL                |       |
| locked_table_partition       | varchar(64)     | YES  |     | NULL                |       |
| locked_table_subpartition    | varchar(64)     | YES  |     | NULL                |       |
| locked_index                 | varchar(64)     | YES  |     | NULL                |       |
| locked_type                  | varchar(32)     | NO   |     | NULL                |       |
| waiting_trx_id               | bigint unsigned | NO   |     | 0                   |       |
| waiting_trx_started          | datetime        | NO   |     | 0000-00-00 00:00:00 |       |
| waiting_trx_age              | time            | YES  |     | NULL                |       |
| waiting_trx_rows_locked      | bigint unsigned | NO   |     | 0                   |       |
| waiting_trx_rows_modified    | bigint unsigned | NO   |     | 0                   |       |
| waiting_pid                  | bigint unsigned | NO   |     | 0                   |       |
| waiting_query                | longtext        | YES  |     | NULL                |       |
| waiting_lock_id              | varchar(128)    | NO   |     | NULL                |       |
| waiting_lock_mode            | varchar(32)     | NO   |     | NULL                |       |
| blocking_trx_id              | bigint unsigned | NO   |     | 0                   |       |
| blocking_pid                 | bigint unsigned | NO   |     | 0                   |       |
| blocking_query               | longtext        | YES  |     | NULL                |       |
| blocking_lock_id             | varchar(128)    | NO   |     | NULL                |       |
| blocking_lock_mode           | varchar(32)     | NO   |     | NULL                |       |
| blocking_trx_started         | datetime        | NO   |     | 0000-00-00 00:00:00 |       |
| blocking_trx_age             | time            | YES  |     | NULL                |       |
| blocking_trx_rows_locked     | bigint unsigned | NO   |     | 0                   |       |
| blocking_trx_rows_modified   | bigint unsigned | NO   |     | 0                   |       |
| sql_kill_blocking_query      | varchar(33)     | NO   |     |                     |       |
| sql_kill_blocking_connection | varchar(27)     | NO   |     |                     |       |
+------------------------------+-----------------+------+-----+---------------------+-------+
30 rows in set (0.00 sec)

[root@127.0.0.1:8032 13:59:30(performance_schema)]$

```





