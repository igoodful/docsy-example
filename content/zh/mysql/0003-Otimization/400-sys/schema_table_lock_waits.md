---
title: schema_table_lock_waits
description: schema_table_lock_waits
date: 2024-10-09
weight: 20000
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< note >}}

1. performance_schema.processlist;
2. sys.schema_table_lock_waits;
3. performance_schema.processlist;
4. performance_schema.events_statements_history;
5. information_schema.innodb_trx;
6. show engine innodb status\G



{{< /note >}}


## schema_table_lock_waits

三表关联查询视图
- performance_schema.threads
- performance_schema.metadata_locks
- performance_schema.events_statements_current



```sql
CREATE ALGORITHM = TEMPTABLE DEFINER = `mysql.sys` @`localhost` SQL SECURITY INVOKER VIEW `sys`.`schema_table_lock_waits` (
    `object_schema`,
    `object_name`,
    `waiting_thread_id`,
    `waiting_pid`,
    `waiting_account`,
    `waiting_lock_type`,
    `waiting_lock_duration`,
    `waiting_query`,
    `waiting_query_secs`,
    `waiting_query_rows_affected`,
    `waiting_query_rows_examined`,
    `blocking_thread_id`,
    `blocking_pid`,
    `blocking_account`,
    `blocking_lock_type`,
    `blocking_lock_duration`,
    `sql_kill_blocking_query`,
    `sql_kill_blocking_connection`
) AS
select
    `g`.`OBJECT_SCHEMA` AS `object_schema`,
    `g`.`OBJECT_NAME` AS `object_name`,
    `pt`.`THREAD_ID` AS `waiting_thread_id`,
    `pt`.`PROCESSLIST_ID` AS `waiting_pid`,
    `sys`.`ps_thread_account`(`p`.`OWNER_THREAD_ID`) AS `waiting_account`,
    `p`.`LOCK_TYPE` AS `waiting_lock_type`,
    `p`.`LOCK_DURATION` AS `waiting_lock_duration`,
    `sys`.`format_statement`(`pt`.`PROCESSLIST_INFO`) AS `waiting_query`,
    `pt`.`PROCESSLIST_TIME` AS `waiting_query_secs`,
    `ps`.`ROWS_AFFECTED` AS `waiting_query_rows_affected`,
    `ps`.`ROWS_EXAMINED` AS `waiting_query_rows_examined`,
    `gt`.`THREAD_ID` AS `blocking_thread_id`,
    `gt`.`PROCESSLIST_ID` AS `blocking_pid`,
    `sys`.`ps_thread_account`(`g`.`OWNER_THREAD_ID`) AS `blocking_account`,
    `g`.`LOCK_TYPE` AS `blocking_lock_type`,
    `g`.`LOCK_DURATION` AS `blocking_lock_duration`,
    concat('KILL QUERY ', `gt`.`PROCESSLIST_ID`) AS `sql_kill_blocking_query`,
    concat('KILL ', `gt`.`PROCESSLIST_ID`) AS `sql_kill_blocking_connection`
from
    (
        (
            (
                (
                    (
                        `performance_schema`.`metadata_locks` `g`
                        join `performance_schema`.`metadata_locks` `p` on(
                            (
                                (`g`.`OBJECT_TYPE` = `p`.`OBJECT_TYPE`)
                                and (`g`.`OBJECT_SCHEMA` = `p`.`OBJECT_SCHEMA`)
                                and (`g`.`OBJECT_NAME` = `p`.`OBJECT_NAME`)
                                and (`g`.`LOCK_STATUS` = 'GRANTED')
                                and (`p`.`LOCK_STATUS` = 'PENDING')
                            )
                        )
                    )
                    join `performance_schema`.`threads` `gt` on((`g`.`OWNER_THREAD_ID` = `gt`.`THREAD_ID`))
                )
                join `performance_schema`.`threads` `pt` on((`p`.`OWNER_THREAD_ID` = `pt`.`THREAD_ID`))
            )
            left join `performance_schema`.`events_statements_current` `gs` on((`g`.`OWNER_THREAD_ID` = `gs`.`THREAD_ID`))
        )
        left join `performance_schema`.`events_statements_current` `ps` on((`p`.`OWNER_THREAD_ID` = `ps`.`THREAD_ID`))
    )
where
    (`g`.`OBJECT_TYPE` = 'TABLE')

```




```sql
[dbscale_internal@172.17.137.95:16310 16:37:05(performance_schema)]$ select * from sys.schema_table_lock_waits\G
*************************** 1. row ***************************
               object_schema: db1
                 object_name: sbtest1
           waiting_thread_id: 622865
                 waiting_pid: 622815
             waiting_account: dbscale_internal@172.17.138.136
           waiting_lock_type: SHARED_READ
       waiting_lock_duration: TRANSACTION
               waiting_query: select * from sbtest1 limit 2
          waiting_query_secs: 40
 waiting_query_rows_affected: 0
 waiting_query_rows_examined: 0
          blocking_thread_id: 1133116
                blocking_pid: 1133066
            blocking_account: dbscale_internal@172.17.138.136
          blocking_lock_type: SHARED_UPGRADABLE
      blocking_lock_duration: TRANSACTION
     sql_kill_blocking_query: KILL QUERY 1133066
sql_kill_blocking_connection: KILL 1133066
*************************** 2. row ***************************
               object_schema: db1
                 object_name: sbtest1
           waiting_thread_id: 625327
                 waiting_pid: 625277
             waiting_account: dbscale_internal@172.17.136.101
           waiting_lock_type: SHARED_WRITE
       waiting_lock_duration: TRANSACTION
               waiting_query: update sbtest1 set k=1 where k<100
          waiting_query_secs: 3
 waiting_query_rows_affected: 0
 waiting_query_rows_examined: 0
          blocking_thread_id: 1133116
                blocking_pid: 1133066
            blocking_account: dbscale_internal@172.17.138.136
          blocking_lock_type: SHARED_UPGRADABLE
      blocking_lock_duration: TRANSACTION
     sql_kill_blocking_query: KILL QUERY 1133066
sql_kill_blocking_connection: KILL 1133066
*************************** 3. row ***************************
               object_schema: db1
                 object_name: sbtest1
           waiting_thread_id: 1204994
                 waiting_pid: 1204944
             waiting_account: admin@127.0.0.1
           waiting_lock_type: SHARED_WRITE
       waiting_lock_duration: TRANSACTION
               waiting_query: delete from sbtest1 limit 1
          waiting_query_secs: 37
 waiting_query_rows_affected: 0
 waiting_query_rows_examined: 0
          blocking_thread_id: 1133116
                blocking_pid: 1133066
            blocking_account: dbscale_internal@172.17.138.136
          blocking_lock_type: SHARED_UPGRADABLE
      blocking_lock_duration: TRANSACTION
     sql_kill_blocking_query: KILL QUERY 1133066
sql_kill_blocking_connection: KILL 1133066
*************************** 4. row ***************************
               object_schema: db1
                 object_name: sbtest1
           waiting_thread_id: 1133116
                 waiting_pid: 1133066
             waiting_account: dbscale_internal@172.17.138.136
           waiting_lock_type: EXCLUSIVE
       waiting_lock_duration: TRANSACTION
               waiting_query: alter table sbtest1 add column md varchar(32)
          waiting_query_secs: 4156
 waiting_query_rows_affected: 0
 waiting_query_rows_examined: 0
          blocking_thread_id: 1133116
                blocking_pid: 1133066
            blocking_account: dbscale_internal@172.17.138.136
          blocking_lock_type: SHARED_UPGRADABLE
      blocking_lock_duration: TRANSACTION
     sql_kill_blocking_query: KILL QUERY 1133066
sql_kill_blocking_connection: KILL 1133066
*************************** 5. row ***************************
               object_schema: db1
                 object_name: sbtest1
           waiting_thread_id: 622865
                 waiting_pid: 622815
             waiting_account: dbscale_internal@172.17.138.136
           waiting_lock_type: SHARED_READ
       waiting_lock_duration: TRANSACTION
               waiting_query: select * from sbtest1 limit 2
          waiting_query_secs: 40
 waiting_query_rows_affected: 0
 waiting_query_rows_examined: 0
          blocking_thread_id: 638105
                blocking_pid: 638055
            blocking_account: dbscale_internal@172.17.138.136
          blocking_lock_type: SHARED_WRITE
      blocking_lock_duration: TRANSACTION
     sql_kill_blocking_query: KILL QUERY 638055
sql_kill_blocking_connection: KILL 638055
*************************** 6. row ***************************
               object_schema: db1
                 object_name: sbtest1
           waiting_thread_id: 625327
                 waiting_pid: 625277
             waiting_account: dbscale_internal@172.17.136.101
           waiting_lock_type: SHARED_WRITE
       waiting_lock_duration: TRANSACTION
               waiting_query: update sbtest1 set k=1 where k<100
          waiting_query_secs: 3
 waiting_query_rows_affected: 0
 waiting_query_rows_examined: 0
          blocking_thread_id: 638105
                blocking_pid: 638055
            blocking_account: dbscale_internal@172.17.138.136
          blocking_lock_type: SHARED_WRITE
      blocking_lock_duration: TRANSACTION
     sql_kill_blocking_query: KILL QUERY 638055
sql_kill_blocking_connection: KILL 638055
*************************** 7. row ***************************
               object_schema: db1
                 object_name: sbtest1
           waiting_thread_id: 1204994
                 waiting_pid: 1204944
             waiting_account: admin@127.0.0.1
           waiting_lock_type: SHARED_WRITE
       waiting_lock_duration: TRANSACTION
               waiting_query: delete from sbtest1 limit 1
          waiting_query_secs: 37
 waiting_query_rows_affected: 0
 waiting_query_rows_examined: 0
          blocking_thread_id: 638105
                blocking_pid: 638055
            blocking_account: dbscale_internal@172.17.138.136
          blocking_lock_type: SHARED_WRITE
      blocking_lock_duration: TRANSACTION
     sql_kill_blocking_query: KILL QUERY 638055
sql_kill_blocking_connection: KILL 638055
*************************** 8. row ***************************
               object_schema: db1
                 object_name: sbtest1
           waiting_thread_id: 1133116
                 waiting_pid: 1133066
             waiting_account: dbscale_internal@172.17.138.136
           waiting_lock_type: EXCLUSIVE
       waiting_lock_duration: TRANSACTION
               waiting_query: alter table sbtest1 add column md varchar(32)
          waiting_query_secs: 4156
 waiting_query_rows_affected: 0
 waiting_query_rows_examined: 0
          blocking_thread_id: 638105
                blocking_pid: 638055
            blocking_account: dbscale_internal@172.17.138.136
          blocking_lock_type: SHARED_WRITE
      blocking_lock_duration: TRANSACTION
     sql_kill_blocking_query: KILL QUERY 638055
sql_kill_blocking_connection: KILL 638055
8 rows in set (0.01 sec)

[dbscale_internal@172.17.137.95:16310 16:37:51(performance_schema)]$ select * from sys.schema_table_lock_waits;
+---------------+-------------+-------------------+-------------+---------------------------------+-------------------+-----------------------+-----------------------------------------------+--------------------+-----------------------------+-----------------------------+--------------------+--------------+---------------------------------+--------------------+------------------------+-------------------------+------------------------------+
| object_schema | object_name | waiting_thread_id | waiting_pid | waiting_account                 | waiting_lock_type | waiting_lock_duration | waiting_query                                 | waiting_query_secs | waiting_query_rows_affected | waiting_query_rows_examined | blocking_thread_id | blocking_pid | blocking_account                | blocking_lock_type | blocking_lock_duration | sql_kill_blocking_query | sql_kill_blocking_connection |
+---------------+-------------+-------------------+-------------+---------------------------------+-------------------+-----------------------+-----------------------------------------------+--------------------+-----------------------------+-----------------------------+--------------------+--------------+---------------------------------+--------------------+------------------------+-------------------------+------------------------------+
| db1           | sbtest1     |            622865 |      622815 | dbscale_internal@172.17.138.136 | SHARED_READ       | TRANSACTION           | select * from sbtest1 limit 2                 |                 65 |                           0 |                           0 |            1133116 |      1133066 | dbscale_internal@172.17.138.136 | SHARED_UPGRADABLE  | TRANSACTION            | KILL QUERY 1133066      | KILL 1133066                 |
| db1           | sbtest1     |            625327 |      625277 | dbscale_internal@172.17.136.101 | SHARED_WRITE      | TRANSACTION           | update sbtest1 set k=1 where k<100            |                 28 |                           0 |                           0 |            1133116 |      1133066 | dbscale_internal@172.17.138.136 | SHARED_UPGRADABLE  | TRANSACTION            | KILL QUERY 1133066      | KILL 1133066                 |
| db1           | sbtest1     |           1204994 |     1204944 | admin@127.0.0.1                 | SHARED_WRITE      | TRANSACTION           | delete from sbtest1 limit 1                   |                 62 |                           0 |                           0 |            1133116 |      1133066 | dbscale_internal@172.17.138.136 | SHARED_UPGRADABLE  | TRANSACTION            | KILL QUERY 1133066      | KILL 1133066                 |
| db1           | sbtest1     |           1133116 |     1133066 | dbscale_internal@172.17.138.136 | EXCLUSIVE         | TRANSACTION           | alter table sbtest1 add column md varchar(32) |               4181 |                           0 |                           0 |            1133116 |      1133066 | dbscale_internal@172.17.138.136 | SHARED_UPGRADABLE  | TRANSACTION            | KILL QUERY 1133066      | KILL 1133066                 |
| db1           | sbtest1     |            622865 |      622815 | dbscale_internal@172.17.138.136 | SHARED_READ       | TRANSACTION           | select * from sbtest1 limit 2                 |                 65 |                           0 |                           0 |             638105 |       638055 | dbscale_internal@172.17.138.136 | SHARED_WRITE       | TRANSACTION            | KILL QUERY 638055       | KILL 638055                  |
| db1           | sbtest1     |            625327 |      625277 | dbscale_internal@172.17.136.101 | SHARED_WRITE      | TRANSACTION           | update sbtest1 set k=1 where k<100            |                 28 |                           0 |                           0 |             638105 |       638055 | dbscale_internal@172.17.138.136 | SHARED_WRITE       | TRANSACTION            | KILL QUERY 638055       | KILL 638055                  |
| db1           | sbtest1     |           1204994 |     1204944 | admin@127.0.0.1                 | SHARED_WRITE      | TRANSACTION           | delete from sbtest1 limit 1                   |                 62 |                           0 |                           0 |             638105 |       638055 | dbscale_internal@172.17.138.136 | SHARED_WRITE       | TRANSACTION            | KILL QUERY 638055       | KILL 638055                  |
| db1           | sbtest1     |           1133116 |     1133066 | dbscale_internal@172.17.138.136 | EXCLUSIVE         | TRANSACTION           | alter table sbtest1 add column md varchar(32) |               4181 |                           0 |                           0 |             638105 |       638055 | dbscale_internal@172.17.138.136 | SHARED_WRITE       | TRANSACTION            | KILL QUERY 638055       | KILL 638055                  |
+---------------+-------------+-------------------+-------------+---------------------------------+-------------------+-----------------------+-----------------------------------------------+--------------------+-----------------------------+-----------------------------+--------------------+--------------+---------------------------------+--------------------+------------------------+-------------------------+------------------------------+
8 rows in set (0.01 sec)




```




## 案例分享

1. 获取当前被阻塞的DDL语句：

```sql
select * from performance_schema.processlist where state ='Waiting for table metadata lock';

select * from information_schema.processlist where state = 'waiting for table metadata lock' order by time desc;

```

2. 获取阻塞当前DDL语句的线程ID

```sql
select * from sys.schema_table_lock_waits where blocking_lock_type !='SHARED_UPGRADABLE';

```
阻塞DDL的不会是SHARED_UPGRADABLE

3. 阻塞当前DDL语句的线程的历史SQL语句

```sql
select * from performance_schema.processlist where id=638055;

select * from performance_schema.events_statements_history where thread_id=638105;

```

4. 查看获取阻塞当前DDL语句的线程的事务开始时间

```sql
select * from information_schema.innodb_trx where trx_mysql_thread_id=638055;

```

5. 查看INOODB状态信息

```sql
show engine innodb status\G

```



完整案例

```sql
[dbscale_internal@172.17.137.95:16310 15:41:14(performance_schema)]$ select * from performance_schema.processlist where state ='Waiting for table metadata lock';
+---------+------------------+----------------------+------+---------+------+---------------------------------+-----------------------------------------------+
| ID      | USER             | HOST                 | DB   | COMMAND | TIME | STATE                           | INFO                                          |
+---------+------------------+----------------------+------+---------+------+---------------------------------+-----------------------------------------------+
| 1133066 | dbscale_internal | 172.17.138.136:45244 | db1  | Query   |  780 | Waiting for table metadata lock | alter table sbtest1 add column md varchar(32) |
| 1204944 | admin            | 127.0.0.1:60390      | db1  | Query   |    7 | Waiting for table metadata lock | delete from sbtest1 limit 1                   |
|  622815 | dbscale_internal | 172.17.138.136:43094 | db1  | Query   |   11 | Waiting for table metadata lock | select * from sbtest1 limit 2                 |
+---------+------------------+----------------------+------+---------+------+---------------------------------+-----------------------------------------------+
3 rows in set (0.00 sec)

[dbscale_internal@172.17.137.95:16310 15:41:35(performance_schema)]$ select * from sys.schema_table_lock_waits where blocking_lock_type !='SHARED_UPGRADABLE';
+---------------+-------------+-------------------+-------------+---------------------------------+-------------------+-----------------------+-----------------------------------------------+--------------------+-----------------------------+-----------------------------+--------------------+--------------+---------------------------------+--------------------+------------------------+-------------------------+------------------------------+
| object_schema | object_name | waiting_thread_id | waiting_pid | waiting_account                 | waiting_lock_type | waiting_lock_duration | waiting_query                                 | waiting_query_secs | waiting_query_rows_affected | waiting_query_rows_examined | blocking_thread_id | blocking_pid | blocking_account                | blocking_lock_type | blocking_lock_duration | sql_kill_blocking_query | sql_kill_blocking_connection |
+---------------+-------------+-------------------+-------------+---------------------------------+-------------------+-----------------------+-----------------------------------------------+--------------------+-----------------------------+-----------------------------+--------------------+--------------+---------------------------------+--------------------+------------------------+-------------------------+------------------------------+
| db1           | sbtest1     |           1133116 |     1133066 | dbscale_internal@172.17.138.136 | EXCLUSIVE         | TRANSACTION           | alter table sbtest1 add column md varchar(32) |                810 |                           0 |                           0 |             638105 |       638055 | dbscale_internal@172.17.138.136 | SHARED_WRITE       | TRANSACTION            | KILL QUERY 638055       | KILL 638055                  |
| db1           | sbtest1     |            622865 |      622815 | dbscale_internal@172.17.138.136 | SHARED_READ       | TRANSACTION           | select * from sbtest1 limit 2                 |                 41 |                           0 |                           0 |             638105 |       638055 | dbscale_internal@172.17.138.136 | SHARED_WRITE       | TRANSACTION            | KILL QUERY 638055       | KILL 638055                  |
| db1           | sbtest1     |           1204994 |     1204944 | admin@127.0.0.1                 | SHARED_WRITE      | TRANSACTION           | delete from sbtest1 limit 1                   |                 37 |                           0 |                           0 |             638105 |       638055 | dbscale_internal@172.17.138.136 | SHARED_WRITE       | TRANSACTION            | KILL QUERY 638055       | KILL 638055                  |
+---------------+-------------+-------------------+-------------+---------------------------------+-------------------+-----------------------+-----------------------------------------------+--------------------+-----------------------------+-----------------------------+--------------------+--------------+---------------------------------+--------------------+------------------------+-------------------------+------------------------------+
3 rows in set (0.01 sec)

[dbscale_internal@172.17.137.95:16310 15:42:05(performance_schema)]$ select * from performance_schema.processlist where id=638055;
+--------+------------------+----------------------+------+---------+------+-------+------+
| ID     | USER             | HOST                 | DB   | COMMAND | TIME | STATE | INFO |
+--------+------------------+----------------------+------+---------+------+-------+------+
| 638055 | dbscale_internal | 172.17.138.136:59198 | db1  | Sleep   |  917 |       | NULL |
+--------+------------------+----------------------+------+---------+------+-------+------+
1 row in set (0.00 sec)

[dbscale_internal@172.17.137.95:16310 15:42:49(performance_schema)]$ select * from performance_schema.threads where processlist_id=638055;
+-----------+---------------------------+------------+----------------+------------------+------------------+----------------+---------------------+------------------+-------------------+------------------+------------------+------+--------------+---------+-----------------+--------------+----------------+
| THREAD_ID | NAME                      | TYPE       | PROCESSLIST_ID | PROCESSLIST_USER | PROCESSLIST_HOST | PROCESSLIST_DB | PROCESSLIST_COMMAND | PROCESSLIST_TIME | PROCESSLIST_STATE | PROCESSLIST_INFO | PARENT_THREAD_ID | ROLE | INSTRUMENTED | HISTORY | CONNECTION_TYPE | THREAD_OS_ID | RESOURCE_GROUP |
+-----------+---------------------------+------------+----------------+------------------+------------------+----------------+---------------------+------------------+-------------------+------------------+------------------+------+--------------+---------+-----------------+--------------+----------------+
|    638105 | thread/sql/one_connection | FOREGROUND |         638055 | dbscale_internal | 172.17.138.136   | db1            | Sleep               |              925 | NULL              | NULL             |             NULL | NULL | YES          | YES     | TCP/IP          |        17368 | USR_default    |
+-----------+---------------------------+------------+----------------+------------------+------------------+----------------+---------------------+------------------+-------------------+------------------+------------------+------+--------------+---------+-----------------+--------------+----------------+
1 row in set (0.00 sec)

[dbscale_internal@172.17.137.95:16310 15:42:57(performance_schema)]$ select * from performance_schema.events_statements_history where thread_id=638105;
+-----------+----------+--------------+----------------------------------+---------------------------------+--------------------+--------------------+----------------+-----------+-----------------------------------+------------------------------------------------------------------+------------------------------------+--------------------+-------------+---------------+-------------+-----------------------+-------------+-------------------+--------------+--------+----------+---------------+-----------+---------------+-------------------------+--------------------+------------------+------------------------+--------------+--------------------+-------------+-------------------+------------+-----------+-----------+---------------+--------------------+------------------+--------------------+---------------------+--------------+
| THREAD_ID | EVENT_ID | END_EVENT_ID | EVENT_NAME                       | SOURCE                          | TIMER_START        | TIMER_END          | TIMER_WAIT     | LOCK_TIME | SQL_TEXT                          | DIGEST                                                           | DIGEST_TEXT                        | CURRENT_SCHEMA     | OBJECT_TYPE | OBJECT_SCHEMA | OBJECT_NAME | OBJECT_INSTANCE_BEGIN | MYSQL_ERRNO | RETURNED_SQLSTATE | MESSAGE_TEXT | ERRORS | WARNINGS | ROWS_AFFECTED | ROWS_SENT | ROWS_EXAMINED | CREATED_TMP_DISK_TABLES | CREATED_TMP_TABLES | SELECT_FULL_JOIN | SELECT_FULL_RANGE_JOIN | SELECT_RANGE | SELECT_RANGE_CHECK | SELECT_SCAN | SORT_MERGE_PASSES | SORT_RANGE | SORT_ROWS | SORT_SCAN | NO_INDEX_USED | NO_GOOD_INDEX_USED | NESTING_EVENT_ID | NESTING_EVENT_TYPE | NESTING_EVENT_LEVEL | STATEMENT_ID |
+-----------+----------+--------------+----------------------------------+---------------------------------+--------------------+--------------------+----------------+-----------+-----------------------------------+------------------------------------------------------------------+------------------------------------+--------------------+-------------+---------------+-------------+-----------------------+-------------+-------------------+--------------+--------+----------+---------------+-----------+---------------+-------------------------+--------------------+------------------+------------------------+--------------+--------------------+-------------+-------------------+------------+-----------+-----------+---------------+--------------------+------------------+--------------------+---------------------+--------------+
|    638105 |  2493786 |      2493800 | statement/sql/show_master_status | init_net_server_extension.cc:94 | 584173293347376000 | 584173293699906000 |      352530000 |         0 | SHOW MASTER STATUS                | fe219218c4249a2b7359e16fe5d3b48ae7c93e3e412600bedbae516b9bea4803 | SHOW MASTER STATUS                 | information_schema | NULL        | NULL          | NULL        |                  NULL |           0 | NULL              | NULL         |      0 |        0 |             0 |         0 |             0 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         0 |         0 |             0 |                  0 |             NULL | NULL               |                   0 |      8103729 |
|    638105 |  2493803 |      2493817 | statement/sql/show_master_status | init_net_server_extension.cc:94 | 584174026296798000 | 584174026408298000 |      111500000 |         0 | SHOW MASTER STATUS                | fe219218c4249a2b7359e16fe5d3b48ae7c93e3e412600bedbae516b9bea4803 | SHOW MASTER STATUS                 | information_schema | NULL        | NULL          | NULL        |                  NULL |           0 | NULL              | NULL         |      0 |        0 |             0 |         0 |             0 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         0 |         0 |             0 |                  0 |             NULL | NULL               |                   0 |      8103774 |
|    638105 |  2493820 |      2493834 | statement/sql/show_master_status | init_net_server_extension.cc:94 | 584175304129437000 | 584175304243015000 |      113578000 |         0 | SHOW MASTER STATUS                | fe219218c4249a2b7359e16fe5d3b48ae7c93e3e412600bedbae516b9bea4803 | SHOW MASTER STATUS                 | information_schema | NULL        | NULL          | NULL        |                  NULL |           0 | NULL              | NULL         |      0 |        0 |             0 |         0 |             0 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         0 |         0 |             0 |                  0 |             NULL | NULL               |                   0 |      8103781 |
|    638105 |  2493837 |      2493850 | statement/sql/change_db          | init_net_server_extension.cc:94 | 584175511305286000 | 584175511512751000 |      207465000 |         0 | USE `db1`                         | a35916dfafd536ceb0ac95427355ffc747bfa9d78f881ef787148e50943702a4 | USE `db1`                          | information_schema | NULL        | NULL          | NULL        |                  NULL |           0 | 00000             | NULL         |      0 |        0 |             0 |         0 |             0 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         0 |         0 |             0 |                  0 |             NULL | NULL               |                   0 |      8103784 |
|    638105 |  2493853 |      2493870 | statement/sql/begin              | init_net_server_extension.cc:94 | 584175511573602000 | 584175511635060000 |       61458000 |         0 | begin;delete from sbtest1 limit 1 | 55fa5810fbb2760e86d578526176c1497b134d4ef3dd0863dd78b1c5e819848c | BEGIN                              | db1                | NULL        | NULL          | NULL        |                  NULL |           0 | 00000             | NULL         |      0 |        0 |             0 |         0 |             0 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         0 |         0 |             0 |                  0 |             NULL | NULL               |                   0 |      8103785 |
|    638105 |  2493871 |      2493954 | statement/sql/delete             | sql_parse.cc:1933               | 584175511645968000 | 584175512581463000 |      935495000 | 116000000 | delete from sbtest1 limit 1       | 82545b32d38278637a2aab22810c768836d200800b7d02840c08538f9325eddc | DELETE FROM `sbtest1` LIMIT ?      | db1                | NULL        | NULL          | NULL        |                  NULL |           0 | 00000             | NULL         |      0 |        0 |             1 |         0 |             1 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         0 |         0 |             0 |                  0 |          2493865 | TRANSACTION        |                   0 |      8103786 |
|    638105 |  2493957 |      2493996 | statement/sql/select             | init_net_server_extension.cc:94 | 586003661746480000 | 586033662560411000 | 30000813931000 |         0 | select sleep(30)                  | 0b1b1c39d4ee2dda7df2a532d0a23406d86bd34e2cd7f22e3f7e9dedadff9b69 | SELECT `sleep` (?)                 | db1                | NULL        | NULL          | NULL        |                  NULL |           0 | NULL              | NULL         |      0 |        0 |             0 |         1 |             1 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         0 |         0 |             0 |                  0 |          2493865 | TRANSACTION        |                   0 |      8138601 |
|    638105 |  2493717 |      2493731 | statement/sql/show_master_status | init_net_server_extension.cc:94 | 584172016073208000 | 584172016181043000 |      107835000 |         0 | SHOW MASTER STATUS                | fe219218c4249a2b7359e16fe5d3b48ae7c93e3e412600bedbae516b9bea4803 | SHOW MASTER STATUS                 | information_schema | NULL        | NULL          | NULL        |                  NULL |           0 | NULL              | NULL         |      0 |        0 |             0 |         0 |             0 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         0 |         0 |             0 |                  0 |             NULL | NULL               |                   0 |      8103706 |
|    638105 |  2493734 |      2493757 | statement/sql/select             | init_net_server_extension.cc:94 | 584172598640541000 | 584172598706632000 |       66091000 |         0 | select @@version_comment limit 1  | 44e35cee979ba420eb49a8471f852bbe15b403c89742704817dfbaace0d99dbb | SELECT @@`version_comment` LIMIT ? | information_schema | NULL        | NULL          | NULL        |                  NULL |           0 | NULL              | NULL         |      0 |        0 |             0 |         1 |             1 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         0 |         0 |             0 |                  0 |             NULL | NULL               |                   0 |      8103720 |
|    638105 |  2493760 |      2493783 | statement/sql/select             | init_net_server_extension.cc:94 | 584172607627571000 | 584172607711359000 |       83788000 |         0 | select @@version_comment limit 1  | 44e35cee979ba420eb49a8471f852bbe15b403c89742704817dfbaace0d99dbb | SELECT @@`version_comment` LIMIT ? | information_schema | NULL        | NULL          | NULL        |                  NULL |           0 | NULL              | NULL         |      0 |        0 |             0 |         1 |             1 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         0 |         0 |             0 |                  0 |             NULL | NULL               |                   0 |      8103724 |
+-----------+----------+--------------+----------------------------------+---------------------------------+--------------------+--------------------+----------------+-----------+-----------------------------------+------------------------------------------------------------------+------------------------------------+--------------------+-------------+---------------+-------------+-----------------------+-------------+-------------------+--------------+--------+----------+---------------+-----------+---------------+-------------------------+--------------------+------------------+------------------------+--------------+--------------------+-------------+-------------------+------------+-----------+-----------+---------------+--------------------+------------------+--------------------+---------------------+--------------+
10 rows in set (0.00 sec)

[dbscale_internal@172.17.137.95:16310 15:44:24(performance_schema)]$ select * from information_schema.innodb_trx where trx_mysql_thread_id=638055;
+--------+-----------+---------------------+-----------------------+------------------+------------+---------------------+-----------+---------------------+-------------------+-------------------+------------------+-----------------------+-----------------+-------------------+-------------------------+---------------------+-------------------+------------------------+----------------------------+---------------------------+---------------------------+------------------+----------------------------+---------------------+
| trx_id | trx_state | trx_started         | trx_requested_lock_id | trx_wait_started | trx_weight | trx_mysql_thread_id | trx_query | trx_operation_state | trx_tables_in_use | trx_tables_locked | trx_lock_structs | trx_lock_memory_bytes | trx_rows_locked | trx_rows_modified | trx_concurrency_tickets | trx_isolation_level | trx_unique_checks | trx_foreign_key_checks | trx_last_foreign_key_error | trx_adaptive_hash_latched | trx_adaptive_hash_timeout | trx_is_read_only | trx_autocommit_non_locking | trx_schedule_weight |
+--------+-----------+---------------------+-----------------------+------------------+------------+---------------------+-----------+---------------------+-------------------+-------------------+------------------+-----------------------+-----------------+-------------------+-------------------------+---------------------+-------------------+------------------------+----------------------------+---------------------------+---------------------------+------------------+----------------------------+---------------------+
|  12364 | RUNNING   | 2024-10-18 14:57:04 | NULL                  | NULL             |          3 |              638055 | NULL      | NULL                |                 0 |                 1 |                2 |                  1128 |               1 |                 1 |                       0 | READ COMMITTED      |                 1 |                      0 | NULL                       |                         0 |                         0 |                0 |                          0 |                NULL |
+--------+-----------+---------------------+-----------------------+------------------+------------+---------------------+-----------+---------------------+-------------------+-------------------+------------------+-----------------------+-----------------+-------------------+-------------------------+---------------------+-------------------+------------------------+----------------------------+---------------------------+---------------------------+------------------+----------------------------+---------------------+
1 row in set (0.01 sec)



```


- show engine innodb status

```sql
[dbscale_internal@172.17.137.95:16310 15:54:22(performance_schema)]$ show engine innodb status\G
*************************** 1. row ***************************
  Type: InnoDB
  Name:
Status:
=====================================
2024-10-18 16:02:35 140694108632832 INNODB MONITOR OUTPUT
=====================================
Per second averages calculated from the last 1 seconds
-----------------
BACKGROUND THREAD
-----------------
srv_master_thread loops: 61353 srv_active, 0 srv_shutdown, 526470 srv_idle
srv_master_thread log flush and writes: 0
----------
SEMAPHORES
----------
OS WAIT ARRAY INFO: reservation count 1138
OS WAIT ARRAY INFO: signal count 1021
RW-shared spins 0, rounds 0, OS waits 0
RW-excl spins 0, rounds 0, OS waits 0
RW-sx spins 0, rounds 0, OS waits 0
Spin rounds per wait: 0.00 RW-shared, 0.00 RW-excl, 0.00 RW-sx
------------
TRANSACTIONS
------------
Trx id counter 12365
Purge done for trx's n:o < 12363 undo n:o < 0 state: running but idle
History list length 0
LIST OF TRANSACTIONS FOR EACH SESSION:
---TRANSACTION 422176503736640, not started
0 lock struct(s), heap size 1128, 0 row lock(s)
---TRANSACTION 422176503735832, not started
mysql tables in use 1, locked 1
0 lock struct(s), heap size 1128, 0 row lock(s)
---TRANSACTION 422176503735024, not started
0 lock struct(s), heap size 1128, 0 row lock(s)
---TRANSACTION 422176503734216, not started
0 lock struct(s), heap size 1128, 0 row lock(s)
---TRANSACTION 422176503733408, not started
0 lock struct(s), heap size 1128, 0 row lock(s)
---TRANSACTION 422176503730176, not started
0 lock struct(s), heap size 1128, 0 row lock(s)
---TRANSACTION 422176503731792, not started
0 lock struct(s), heap size 1128, 0 row lock(s)
---TRANSACTION 422176503730984, not started
0 lock struct(s), heap size 1128, 0 row lock(s)
---TRANSACTION 422176503729368, not started
0 lock struct(s), heap size 1128, 0 row lock(s)
---TRANSACTION 422176503728560, not started
0 lock struct(s), heap size 1128, 0 row lock(s)
---TRANSACTION 422176503727752, not started
0 lock struct(s), heap size 1128, 0 row lock(s)
---TRANSACTION 12364, ACTIVE 3931 sec
2 lock struct(s), heap size 1128, 1 row lock(s), undo log entries 1
MySQL thread id 638055, OS thread handle 140694102734592, query id 8138601 172.17.138.136 dbscale_internal
--------
FILE I/O
--------
I/O thread 0 state: waiting for completed aio requests (insert buffer thread)
I/O thread 1 state: waiting for completed aio requests (log thread)
I/O thread 2 state: waiting for completed aio requests (read thread)
I/O thread 3 state: waiting for completed aio requests (read thread)
I/O thread 4 state: waiting for completed aio requests (read thread)
I/O thread 5 state: waiting for completed aio requests (read thread)
I/O thread 6 state: waiting for completed aio requests (read thread)
I/O thread 7 state: waiting for completed aio requests (read thread)
I/O thread 8 state: waiting for completed aio requests (read thread)
I/O thread 9 state: waiting for completed aio requests (read thread)
I/O thread 10 state: waiting for completed aio requests (write thread)
I/O thread 11 state: waiting for completed aio requests (write thread)
I/O thread 12 state: waiting for completed aio requests (write thread)
I/O thread 13 state: waiting for completed aio requests (write thread)
I/O thread 14 state: waiting for completed aio requests (write thread)
I/O thread 15 state: waiting for completed aio requests (write thread)
I/O thread 16 state: waiting for completed aio requests (write thread)
I/O thread 17 state: waiting for completed aio requests (write thread)
Pending normal aio reads: [0, 0, 0, 0, 0, 0, 0, 0] , aio writes: [0, 0, 0, 0, 0, 0, 0, 0] ,
 ibuf aio reads:, log i/o's:, sync i/o's:
Pending flushes (fsync) log: 0; buffer pool: 1
29560 OS file reads, 127872 OS file writes, 59141 OS fsyncs
0.00 reads/s, 0 avg bytes/read, 1.00 writes/s, 1.00 fsyncs/s
-------------------------------------
INSERT BUFFER AND ADAPTIVE HASH INDEX
-------------------------------------
Ibuf: size 1, free list len 0, seg size 2, 4 merges
merged operations:
 insert 1, delete mark 3, delete 0
discarded operations:
 insert 0, delete mark 0, delete 0
Hash table size 1106407, node heap has 0 buffer(s)
Hash table size 1106407, node heap has 0 buffer(s)
Hash table size 1106407, node heap has 0 buffer(s)
Hash table size 1106407, node heap has 0 buffer(s)
Hash table size 1106407, node heap has 0 buffer(s)
Hash table size 1106407, node heap has 0 buffer(s)
Hash table size 1106407, node heap has 0 buffer(s)
Hash table size 1106407, node heap has 0 buffer(s)
0.00 hash searches/s, 210.79 non-hash searches/s
---
LOG
---
Log sequence number          2602076460
Log buffer assigned up to    2602076460
Log buffer completed up to   2602076460
Log written up to            2602076460
Log flushed up to            2602076460
Added dirty pages up to      2602076460
Pages flushed up to          2602076460
Last checkpoint at           2602076460
374 log i/o's done, 0.00 log i/o's/second
----------------------
BUFFER POOL AND MEMORY
----------------------
Total large memory allocated 4385275904
Dictionary memory allocated 634165
Buffer pool size   262120
Free buffers       232419
Database pages     29701
Old database pages 10926
Modified db pages  3
Pending reads      0
Pending writes: LRU 0, flush list 0, single page 0
Pages made young 284677, not young 32931
0.00 youngs/s, 0.00 non-youngs/s
Pages read 29473, created 175989, written 68195
0.00 reads/s, 0.00 creates/s, 0.00 writes/s
Buffer pool hit rate 1000 / 1000, young-making rate 3 / 1000 not 0 / 1000
Pages read ahead 0.00/s, evicted without access 0.00/s, Random read ahead 0.00/s
LRU len: 29701, unzip_LRU len: 0
I/O sum[20]:cur[0], unzip sum[0]:cur[0]
----------------------
INDIVIDUAL BUFFER POOL INFO
----------------------
---BUFFER POOL 0
Buffer pool size   65528
Free buffers       58177
Database pages     7351
Old database pages 2696
Modified db pages  0
Pending reads      0
Pending writes: LRU 0, flush list 0, single page 0
Pages made young 85808, not young 9849
0.00 youngs/s, 0.00 non-youngs/s
Pages read 7342, created 11574, written 4392
0.00 reads/s, 0.00 creates/s, 0.00 writes/s
Buffer pool hit rate 1000 / 1000, young-making rate 0 / 1000 not 0 / 1000
Pages read ahead 0.00/s, evicted without access 0.00/s, Random read ahead 0.00/s
LRU len: 7351, unzip_LRU len: 0
I/O sum[5]:cur[0], unzip sum[0]:cur[0]
---BUFFER POOL 1
Buffer pool size   65528
Free buffers       58150
Database pages     7378
Old database pages 2703
Modified db pages  0
Pending reads      0
Pending writes: LRU 0, flush list 0, single page 0
Pages made young 81673, not young 12692
0.00 youngs/s, 0.00 non-youngs/s
Pages read 7285, created 72045, written 28124
0.00 reads/s, 0.00 creates/s, 0.00 writes/s
Buffer pool hit rate 1000 / 1000, young-making rate 0 / 1000 not 0 / 1000
Pages read ahead 0.00/s, evicted without access 0.00/s, Random read ahead 0.00/s
LRU len: 7378, unzip_LRU len: 0
I/O sum[5]:cur[0], unzip sum[0]:cur[0]
---BUFFER POOL 2
Buffer pool size   65535
Free buffers       57893
Database pages     7642
Old database pages 2803
Modified db pages  0
Pending reads      0
Pending writes: LRU 0, flush list 0, single page 0
Pages made young 116397, not young 10390
0.00 youngs/s, 0.00 non-youngs/s
Pages read 7529, created 91159, written 35070
0.00 reads/s, 0.00 creates/s, 0.00 writes/s
Buffer pool hit rate 1000 / 1000, young-making rate 0 / 1000 not 0 / 1000
Pages read ahead 0.00/s, evicted without access 0.00/s, Random read ahead 0.00/s
LRU len: 7642, unzip_LRU len: 0
I/O sum[5]:cur[0], unzip sum[0]:cur[0]
---BUFFER POOL 3
Buffer pool size   65529
Free buffers       58199
Database pages     7330
Old database pages 2724
Modified db pages  3
Pending reads      0
Pending writes: LRU 0, flush list 0, single page 0
Pages made young 799, not young 0
0.00 youngs/s, 0.00 non-youngs/s
Pages read 7317, created 1211, written 609
0.00 reads/s, 0.00 creates/s, 0.00 writes/s
Buffer pool hit rate 1000 / 1000, young-making rate 8 / 1000 not 0 / 1000
Pages read ahead 0.00/s, evicted without access 0.00/s, Random read ahead 0.00/s
LRU len: 7330, unzip_LRU len: 0
I/O sum[5]:cur[0], unzip sum[0]:cur[0]
--------------
ROW OPERATIONS
--------------
0 queries inside InnoDB, 0 queries in queue
0 read views open inside InnoDB
Process ID=20965, Main thread ID=140696364762880 , state=sleeping
Number of rows inserted 64278, updated 0, deleted 5, read 444873154
1.00 inserts/s, 0.00 updates/s, 0.00 deletes/s, 1.00 reads/s
Number of system rows inserted 66, updated 389, deleted 40, read 140878390
0.00 inserts/s, 0.00 updates/s, 0.00 deletes/s, 2428.57 reads/s
----------------------------
END OF INNODB MONITOR OUTPUT
============================

1 row in set (0.00 sec)

[dbscale_internal@172.17.137.95:16310 16:02:35(performance_schema)]$

```












