---
title: 8. 元数据锁
description: mdl元数据锁
date: 2023-10-13
weight: 8000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{<alert  color="secondary">}}



{{</alert>}}

| 配置                  | 参数          | 备注         |
| :-------------------- | :------------ | :----------- |
| version               | 8.0.32        | 版本         |
| engine                | innodb        | 存储引擎     |
| transaction_isolation | read commited | 事务隔离级别 |
## MDL元数据锁

### ddl操作的锁的等待时间
```sql
mysql> show global variables like 'lock_wait_timeout';
+-------------------+----------+
| Variable_name     | Value    |
+-------------------+----------+
| lock_wait_timeout | 31536000 |
+-------------------+----------+
1 row in set (0.01 sec)
```

```sql
SELECT locked_schema,
        locked_table,
        locked_type,
        waiting_processlist_id,
        waiting_age,
        waiting_query,
        waiting_state,
        blocking_processlist_id,
        blocking_age,
        substring_index(sql_text,
        "transaction_begin;" ,
        -1) AS blocking_query,
        sql_kill_blocking_connection
FROM
    (SELECT b.owner_thread_id AS granted_thread_id,
        a.object_schema AS locked_schema,
        a.object_name AS locked_table,
        "Metadata Lock" AS locked_type,
         c.processlist_id AS waiting_processlist_id,
        c.processlist_time AS waiting_age,
        c.processlist_info AS waiting_query,
        c.processlist_state AS waiting_state,
         d.processlist_id AS blocking_processlist_id,
        d.processlist_time AS blocking_age,
        d.processlist_info AS blocking_query,
        concat('KILL ', d.PROCESSLIST_ID) AS sql_kill_blocking_connection
    FROM performance_schema.metadata_locks a
    JOIN performance_schema.metadata_locks b
        ON a.object_schema = b.object_schema
            AND a.object_name = b.object_name
            AND a.lock_status = 'PENDING'
            AND b.lock_status = 'GRANTED'
            AND a.owner_thread_id <> b.owner_thread_id
            AND a.lock_type = 'EXCLUSIVE'
    JOIN performance_schema.threads c
        ON a.owner_thread_id = c.thread_id
    JOIN performance_schema.threads d
        ON b.owner_thread_id = d.thread_id) t1,
    (SELECT thread_id,
         group_concat(
        CASE
        WHEN event_name = 'statement/sql/begin' THEN
        "transaction_begin"
        ELSE sql_text
        END
    ORDER BY  event_id separator ";" ) AS sql_text
    FROM performance_schema.events_statements_history
    GROUP BY  thread_id) t2
WHERE t1.granted_thread_id = t2.thread_id \G
```

### session1：开启一个长事务
```sql
mysql> use apple;
Database changed
mysql> select * from user;
+----+------+
| id | name |
+----+------+
|  1 | 1    |
|  2 | 2    |
|  3 | 3    |
|  4 | 4    |
|  5 | 5    |
|  6 | 6    |
+----+------+
6 rows in set (0.00 sec)

mysql> begin;
Query OK, 0 rows affected (0.00 sec)

mysql> delete from user where id <3;
Query OK, 2 rows affected (0.00 sec)

mysql> update user set name='test' where id=4;
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0


```

### session2：修改表结构的ddl语句
```sql
mysql> use apple
Database changed
mysql> alter table user add index idx_name(name);


```

### session3：查询谁阻塞了谁
```sql
mysql> SELECT locked_schema,
    ->         locked_table,
    ->         locked_type,
    ->         waiting_processlist_id,
    ->         waiting_age,
    ->         waiting_query,
    ->         waiting_state,
    ->         blocking_processlist_id,
    ->         blocking_age,
    ->         substring_index(sql_text,
    ->         "transaction_begin;" ,
    ->         -1) AS blocking_query,
    ->         sql_kill_blocking_connection
    -> FROM
    ->     (SELECT b.owner_thread_id AS granted_thread_id,
    ->         a.object_schema AS locked_schema,
    ->         a.object_name AS locked_table,
    ->         "Metadata Lock" AS locked_type,
    ->          c.processlist_id AS waiting_processlist_id,
    ->         c.processlist_time AS waiting_age,
    ->         c.processlist_info AS waiting_query,
    ->         c.processlist_state AS waiting_state,
    ->          d.processlist_id AS blocking_processlist_id,
    ->         d.processlist_time AS blocking_age,
    ->         d.processlist_info AS blocking_query,
    ->         concat('KILL ', d.PROCESSLIST_ID) AS sql_kill_blocking_connection
    ->     FROM performance_schema.metadata_locks a
    ->     JOIN performance_schema.metadata_locks b
    ->         ON a.object_schema = b.object_schema
    ->             AND a.object_name = b.object_name
    ->             AND a.lock_status = 'PENDING'
    ->             AND b.lock_status = 'GRANTED'
    ->             AND a.owner_thread_id <> b.owner_thread_id
    ->             AND a.lock_type = 'EXCLUSIVE'
    ->     JOIN performance_schema.threads c
    ->         ON a.owner_thread_id = c.thread_id
    ->     JOIN performance_schema.threads d
    ->         ON b.owner_thread_id = d.thread_id) t1,
    ->     (SELECT thread_id,
    ->          group_concat(
    ->         CASE
    ->         WHEN event_name = 'statement/sql/begin' THEN
    ->         "transaction_begin"
    ->         ELSE sql_text
    ->         END
    ->     ORDER BY  event_id separator ";" ) AS sql_text
    ->     FROM performance_schema.events_statements_history
    ->     GROUP BY  thread_id) t2
    -> WHERE t1.granted_thread_id = t2.thread_id \G
*************************** 1. row ***************************
               locked_schema: apple
                locked_table: user
                 locked_type: Metadata Lock
      waiting_processlist_id: 19
                 waiting_age: 1125
               waiting_query: alter table user add index idx_name(name)
               waiting_state: Waiting for table metadata lock
     blocking_processlist_id: 20
                blocking_age: 1761
              blocking_query: delete from user where id <3;update user set name='test' where id=4
sql_kill_blocking_connection: KILL 20
1 row in set, 1 warning (0.00 sec)


```
