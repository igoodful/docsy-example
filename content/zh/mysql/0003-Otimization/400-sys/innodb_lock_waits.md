---
title: innodb_lock_waits
description: innodb_lock_waits
date: 2024-10-09
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



```sql
CREATE algorithm=temptable definer=mysql.sys@localhost SQL security invoker view sys.innodb_lock_waits (wait_started,wait_age,wait_age_secs,locked_table,locked_table_schema,locked_table_name,locked_table_partition,locked_table_subpartition,locked_index,locked_type,waiting_trx_id,waiting_trx_started,waiting_trx_age,waiting_trx_rows_locked,waiting_trx_rows_modified,waiting_pid,waiting_query,waiting_lock_id,waiting_lock_mode,blocking_trx_id,blocking_pid,blocking_query,blocking_lock_id,blocking_lock_mode,blocking_trx_started,blocking_trx_age,blocking_trx_rows_locked,blocking_trx_rows_modified,sql_kill_blocking_query,sql_kill_blocking_connection)
AS
  SELECT   r.trx_wait_started                                                                               AS wait_started,
           timediff(now(),r.trx_wait_started)                                                               AS wait_age,
           timestampdiff(second,r.trx_wait_started,now())                                                   AS wait_age_secs,
                    concat(sys.quote_identifier(rl.object_schema),'.',sys.quote_identifier(rl.object_name)) AS locked_table,
           rl.object_schema                                                                                 AS locked_table_schema,
           rl.object_name                                                                                   AS locked_table_name,
           rl.partition_name                                                                                AS locked_table_partition,
           rl.subpartition_name                                                                             AS locked_table_subpartition,
           rl.index_name                                                                                    AS locked_index,
           rl.lock_type                                                                                     AS locked_type,
           r.trx_id                                                                                         AS waiting_trx_id,
           r.trx_started                                                                                    AS waiting_trx_started,
           timediff(now(),r.trx_started)                                                                    AS waiting_trx_age,
           r.trx_rows_locked                                                                                AS waiting_trx_rows_locked,
           r.trx_rows_modified                                                                              AS waiting_trx_rows_modified,
           r.trx_mysql_thread_id                                                                            AS waiting_pid,
           sys.format_statement(r.trx_query)                                                                AS waiting_query,
           rl.engine_lock_id                                                                                AS waiting_lock_id,
           rl.lock_mode                                                                                     AS waiting_lock_mode,
           b.trx_id                                                                                         AS blocking_trx_id,
           b.trx_mysql_thread_id                                                                            AS blocking_pid,
           sys.format_statement(b.trx_query)                                                                AS blocking_query,
           bl.engine_lock_id                                                                                AS blocking_lock_id,
           bl.lock_mode                                                                                     AS blocking_lock_mode,
           b.trx_started                                                                                    AS blocking_trx_started,
           timediff(now(),b.trx_started)                                                                    AS blocking_trx_age,
           b.trx_rows_locked                                                                                AS blocking_trx_rows_locked,
           b.trx_rows_modified                                                                              AS blocking_trx_rows_modified,
                    concat('KILL QUERY ',b.trx_mysql_thread_id)                                             AS sql_kill_blocking_query,
                    concat('KILL ',b.trx_mysql_thread_id)                                                   AS sql_kill_blocking_connection
  FROM     ((((data_lock_waits w
  JOIN     information_schema.innodb_trx b
  ON      ((
                             b.trx_id = cast(w.blocking_engine_transaction_id AS CHAR charset utf8mb4))))
  JOIN     information_schema.innodb_trx r
  ON      ((
                             r.trx_id = cast(w.requesting_engine_transaction_id AS CHAR charset utf8mb4))))
  JOIN     data_locks bl
  ON      ((
                             bl.engine_lock_id = w.blocking_engine_lock_id)))
  JOIN     data_locks rl
  ON      ((
                             rl.engine_lock_id = w.requesting_engine_lock_id)))
  ORDER BY r.trx_wait_started
```




```sql
mysql> select * from sys.innodb_lock_waits\G
*************************** 1. row ***************************
                wait_started: 2023-12-11 21:12:49
                    wait_age: 00:00:04
               wait_age_secs: 4
                locked_table: `apple`.`user`
         locked_table_schema: apple
           locked_table_name: user
      locked_table_partition: NULL
   locked_table_subpartition: NULL
                locked_index: PRIMARY
                 locked_type: RECORD
              waiting_trx_id: 3256133
         waiting_trx_started: 2023-12-11 20:51:22
             waiting_trx_age: 00:21:31
     waiting_trx_rows_locked: 6
   waiting_trx_rows_modified: 0
                 waiting_pid: 18
               waiting_query: delete from  user where id <5
             waiting_lock_id: 140603514388480:625:4:10:140603404926216
           waiting_lock_mode: X,REC_NOT_GAP
             blocking_trx_id: 3256128
                blocking_pid: 20
              blocking_query: NULL
            blocking_lock_id: 140603514387672:625:4:10:140603404918352
          blocking_lock_mode: X,REC_NOT_GAP
        blocking_trx_started: 2023-12-11 20:02:16
            blocking_trx_age: 01:10:37
    blocking_trx_rows_locked: 3
  blocking_trx_rows_modified: 3
     sql_kill_blocking_query: KILL QUERY 20
sql_kill_blocking_connection: KILL 20
1 row in set (0.00 sec)


```


