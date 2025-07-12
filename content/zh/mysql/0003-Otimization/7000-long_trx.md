---
title: 7. 长事务
description: 长事务
date: 2023-10-13
weight: 7000


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


1. 开启的一个事务，一直没提交或回滚会怎样呢？
2. 出现事务等待情况应该如何处理？

**长事务**：就是运行时间比较长，长时间未提交的事务。

**长事务危害**：
1. 容易造成大量的阻塞和锁超时
2. 容易造成死锁
3. 容易造成主从延迟





显式开启事务，可用begin或start transaction


## 场景一：

1. **创建长事务**
```sql
begin;
select * from stu_tb where stu_id = 1006 for update;
-- 如果我们不及时提交上个事务，那么这个事务就变成了长事务，当其他会话要操作这条数据时，就会一直等待。
```
2. **找到长事务**

```sql
select t.*,to_seconds(now())-to_seconds(t.trx_started) idle_time from information_schema.innodb_trx t \G
```

3.







### innodb的dml操作的行级锁的等待时间
```sql
mysql> show global variables like 'innodb_lock_wait_timeout';
+--------------------------+-------+
| Variable_name            | Value |
+--------------------------+-------+
| innodb_lock_wait_timeout | 50    |
+--------------------------+-------+
1 row in set (0.00 sec)



```




### 查看长事务最后执行的一条SQL语句
```sql

mysql> select now(),(unix_timestamp(now()) - unix_timestamp(a.trx_started)) diff_sec,b.id,b.user,b.host,b.db,d.sql_text
from information_schema.innodb_trx a
inner join information_schema.processlist b on a.trx_mysql_thread_id=b.id and b.command = 'Sleep'
inner join performance_schema.threads c on b.id = c.processlist_id
inner join performance_schema.events_statements_current d on d.thread_id = c.thread_id;
+---------------------+----------+----+-------+-----------------+-------+------------------------------------------+
| now()               | diff_sec | id | user  | host            | db    | sql_text                                 |
+---------------------+----------+----+-------+-----------------+-------+------------------------------------------+
| 2023-12-11 16:31:21 |     1104 | 14 | admin | 127.0.0.1:44774 | apple | update  user set name='test' where id <3 |
+---------------------+----------+----+-------+-----------------+-------+------------------------------------------+
1 row in set (0.00 sec)


```

- diff_sec 代表此事务持续的秒数
- sql_text 表示该事务最后执行的SQL




### 查看长事务最后执行所有SQL语句
```sql
select ps.id,esh.event_id,ps.user,ps.host,trx.trx_started,esh.event_name,esh.sql_text,ps.time
from performance_schema.events_statements_history esh
join performance_schema.threads th on esh.thread_id = th.thread_id
join information_schema.processlist ps on ps.id = th.processlist_id
left join information_schema.innodb_trx trx on trx.trx_mysql_thread_id = ps.id
where trx.trx_id is not null and ps.user != 'SYSTEM_USER'
order by ps.id,esh.event_id;

+------------+-------+-----------------+----------+---------------------+-----------------------+------------------------------------------+------+
| id | user  | host            | event_id | trx_started         | event name            | sql                                      | time |
+------------+-------+-----------------+----------+---------------------+-----------------------+------------------------------------------+------+
|         14 | admin | 127.0.0.1:44774 |       20 | 2023-12-11 16:12:57 | statement/sql/select  | select @@version_comment limit 1         |  358 |
|         14 | admin | 127.0.0.1:44774 |       48 | 2023-12-11 16:12:57 | statement/sql/select  | SELECT DATABASE()                        |  358 |
|         14 | admin | 127.0.0.1:44774 |       73 | 2023-12-11 16:12:57 | statement/com/Init DB | NULL                                     |  358 |
|         14 | admin | 127.0.0.1:44774 |       85 | 2023-12-11 16:12:57 | statement/sql/begin   | begin                                    |  358 |
|         14 | admin | 127.0.0.1:44774 |      108 | 2023-12-11 16:12:57 | statement/sql/select  | select * from user where id <3           |  358 |
|         14 | admin | 127.0.0.1:44774 |      187 | 2023-12-11 16:12:57 | statement/sql/select  | select * from user where id <3           |  358 |
|         14 | admin | 127.0.0.1:44774 |      250 | 2023-12-11 16:12:57 | statement/sql/select  | select * from user where id <3           |  358 |
|         14 | admin | 127.0.0.1:44774 |      313 | 2023-12-11 16:12:57 | statement/sql/update  | update  user set name='test' where id <3 |  358 |
|         14 | admin | 127.0.0.1:44774 |      432 | 2023-12-11 16:12:57 | statement/sql/delete  | delete from user where id >4             |  358 |
+------------+-------+-----------------+----------+---------------------+-----------------------+------------------------------------------+------+
9 rows in set (0.00 sec)


```

## 模拟未提交事务

### session1：第一个长事务
```sql
[root@48 ~]$ mylogin_local
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 24
Server version: 8.0.26 MySQL Community Server - GPL

Copyright (c) 2000, 2021, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> use apple
Database changed
mysql> begin;
Query OK, 0 rows affected (0.00 sec)

mysql> update user set name='nb' where id < 4;
Query OK, 3 rows affected (0.00 sec)
Rows matched: 3  Changed: 3  Warnings: 0

mysql> delete from user where id=4;
Query OK, 1 row affected (0.00 sec)

mysql>


```

### session2：第二个长事务
```sql
[root@48 ~]$ mylogin_local
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 25
Server version: 8.0.26 MySQL Community Server - GPL

Copyright (c) 2000, 2021, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> begin;
Query OK, 0 rows affected (0.00 sec)

mysql> insert into apple.user values (null,'7');
Query OK, 1 row affected (0.01 sec)

mysql> delete from apple.user where id=6;
Query OK, 1 row affected (0.00 sec)

mysql>

```

### session3：模拟阻塞
```sql
[root@48 ~]$ mylogin_local
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 26
Server version: 8.0.26 MySQL Community Server - GPL

Copyright (c) 2000, 2021, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> use apple
Database changed
mysql> begin;
Query OK, 0 rows affected (0.00 sec)

mysql> delete from user where id >1;


```

### session4：查看是否有阻塞
```sql
mysql> select * from sys.innodb_lock_waits\G
*************************** 1. row ***************************
                wait_started: 2023-12-12 09:24:49
                    wait_age: 00:00:46
               wait_age_secs: 46
                locked_table: `apple`.`user`
         locked_table_schema: apple
           locked_table_name: user
      locked_table_partition: NULL
   locked_table_subpartition: NULL
                locked_index: PRIMARY
                 locked_type: RECORD
              waiting_trx_id: 3256141
         waiting_trx_started: 2023-12-12 09:24:49
             waiting_trx_age: 00:00:46
     waiting_trx_rows_locked: 1
   waiting_trx_rows_modified: 0
                 waiting_pid: 26
               waiting_query: delete from user where id >1
             waiting_lock_id: 140603514390096:625:4:14:140603404936656
           waiting_lock_mode: X,REC_NOT_GAP
             blocking_trx_id: 3256135
                blocking_pid: 24
              blocking_query: NULL
            blocking_lock_id: 140603514387672:625:4:14:140603404918352
          blocking_lock_mode: X,REC_NOT_GAP
        blocking_trx_started: 2023-12-12 09:14:16
            blocking_trx_age: 00:11:19
    blocking_trx_rows_locked: 4
  blocking_trx_rows_modified: 4
     sql_kill_blocking_query: KILL QUERY 24
sql_kill_blocking_connection: KILL 24
1 row in set (0.00 sec)

```
{{<note>}}
上述结果显示出被阻塞的SQL以及锁的类型，更强大的是杀掉会话的语句也给出来了。但是并没有找到阻塞会话执行的SQL，如果我们想找出更详细的信息，可以使用下面语句：

{{</note>}}

### session4：查看所有会话执行的历史SQL
```sql
mysql> select ps.id,esh.event_id,ps.user,ps.host,trx.trx_started,esh.event_name,esh.sql_text,ps.time from performance_schema.events_statements_history esh join performance_schema.threads th on esh.thread_id = th.thread_id join information_schema.processlist ps on ps.id = th.processlist_id left join information_schema.innodb_trx trx on trx.trx_mysql_thread_id = ps.id where trx.trx_id is not null and ps.user != 'SYSTEM_USER' order by ps.id,esh.event_id;
+----+----------+-------+-----------------+---------------------+-----------------------+------------------------------------------+------+
| id | event_id | user  | host            | trx_started         | event_name            | sql_text                                 | time |
+----+----------+-------+-----------------+---------------------+-----------------------+------------------------------------------+------+
| 24 |       20 | admin | 127.0.0.1:53092 | 2023-12-12 09:14:16 | statement/sql/select  | select @@version_comment limit 1         |  278 |
| 24 |       48 | admin | 127.0.0.1:53092 | 2023-12-12 09:14:16 | statement/sql/select  | SELECT DATABASE()                        |  278 |
| 24 |       71 | admin | 127.0.0.1:53092 | 2023-12-12 09:14:16 | statement/com/Init DB | NULL                                     |  278 |
| 24 |       83 | admin | 127.0.0.1:53092 | 2023-12-12 09:14:16 | statement/sql/begin   | begin                                    |  278 |
| 24 |      106 | admin | 127.0.0.1:53092 | 2023-12-12 09:14:16 | statement/sql/update  | update user set name='nb' where id < 4   |  278 |
| 24 |      261 | admin | 127.0.0.1:53092 | 2023-12-12 09:14:16 | statement/sql/delete  | delete from user where id=4              |  278 |
| 25 |       20 | admin | 127.0.0.1:53104 | 2023-12-12 09:16:22 | statement/sql/select  | select @@version_comment limit 1         |  144 |
| 25 |       48 | admin | 127.0.0.1:53104 | 2023-12-12 09:16:22 | statement/sql/begin   | begin                                    |  144 |
| 25 |       71 | admin | 127.0.0.1:53104 | 2023-12-12 09:16:22 | statement/sql/insert  | insert into apple.user values (null,'7') |  144 |
| 25 |      167 | admin | 127.0.0.1:53104 | 2023-12-12 09:16:22 | statement/sql/delete  | delete from apple.user where id=6        |  144 |
+----+----------+-------+-----------------+---------------------+-----------------------+------------------------------------------+------+
10 rows in set (0.00 sec)


```

### 监控长事务

```sql
#!/bin/bash
# -------------------------------------------------------------------------------
# FileName:    long_trx.sh
# Describe:    monitor long transaction
# Revision:    1.0
# Date:        2019/09/16
# Author:      wang

/usr/local/mysql/bin/mysql -N -uroot -pxxxxxx -e "select now(),(UNIX_TIMESTAMP(now()) - UNIX_TIMESTAMP(a.trx_started)) diff_sec,b.id,b.user,b.host,b.db,d.SQL_TEXT from information_schema.innodb_trx a inner join
information_schema.PROCESSLIST b
on a.TRX_MYSQL_THREAD_ID=b.id and b.command = 'Sleep'
inner join performance_schema.threads c ON b.id = c.PROCESSLIST_ID
inner join performance_schema.events_statements_current d ON d.THREAD_ID = c.THREAD_ID;" | while read A B C D E F G H
do
  if [ "$C" -gt 30 ]
      then
      echo $(date +"%Y-%m-%d %H:%M:%S")
      echo "processid[$D] $E@$F in db[$G] hold transaction time $C SQL:$H"
  fi
done >> /tmp/longtransaction.txt

```
> 定时任务：这里的-gt 30是30秒钟的意思，只要超过了30秒钟就认定是长事务，可以根据实际需要自定义。







## 模拟


### 一、建库建表并插入数据
```sql
mysql> use apple
Database changed

mysql> show create table document\G
*************************** 1. row ***************************
       Table: document
Create Table: CREATE TABLE `document` (
  `pk_id` bigint unsigned NOT NULL AUTO_INCREMENT COMMENT '自增主键',
  `id` int NOT NULL COMMENT '业务id',
  `title` varchar(100) NOT NULL COMMENT '标题',
  `version` varchar(20) NOT NULL COMMENT '最新版本号',
  PRIMARY KEY (`pk_id`),
  KEY `idx_id` (`id`),
  KEY `idx_title` (`title`)
) ENGINE=InnoDB AUTO_INCREMENT=11 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci COMMENT='文档表'
1 row in set (0.00 sec)

mysql> select * from document;
+-------+----+---------+-----------+
| pk_id | id | title   | version   |
+-------+----+---------+-----------+
|     1 |  1 | title1  | version1  |
|     2 |  2 | title2  | version2  |
|     3 |  3 | title3  | version3  |
|     4 |  4 | title4  | version4  |
|     5 |  5 | title5  | version5  |
|     6 |  6 | title6  | version6  |
|     7 |  7 | title7  | version7  |
|     8 |  8 | title8  | version8  |
|     9 |  9 | title9  | version9  |
|    10 | 10 | title10 | version10 |
+-------+----+---------+-----------+
10 rows in set (0.00 sec)

```


### session1
> 开启一个事务，但不提交

```sql
mysql> use apple;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> begin;
Query OK, 0 rows affected (0.00 sec)

mysql> insert into document values (null,11,'title11','version11');
Query OK, 1 row affected (0.01 sec)

mysql> update document set version='version2' where id <4;
Query OK, 2 rows affected (0.00 sec)
Rows matched: 3  Changed: 2  Warnings: 0


```

## session2
> 再开启一个事务，也不提交

```sql
mysql> use apple;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> begin;
Query OK, 0 rows affected (0.00 sec)

mysql> select * from document;
+-------+----+---------+-----------+
| pk_id | id | title   | version   |
+-------+----+---------+-----------+
|     1 |  1 | title1  | version1  |
|     2 |  2 | title2  | version2  |
|     3 |  3 | title3  | version3  |
|     4 |  4 | title4  | version4  |
|     5 |  5 | title5  | version5  |
|     6 |  6 | title6  | version6  |
|     7 |  7 | title7  | version7  |
|     8 |  8 | title8  | version8  |
|     9 |  9 | title9  | version9  |
|    10 | 10 | title10 | version10 |
+-------+----+---------+-----------+
10 rows in set (0.00 sec)

mysql> delete from document where id=6;
Query OK, 1 row affected (0.00 sec)

mysql> select * from document;
+-------+----+---------+-----------+
| pk_id | id | title   | version   |
+-------+----+---------+-----------+
|     1 |  1 | title1  | version1  |
|     2 |  2 | title2  | version2  |
|     3 |  3 | title3  | version3  |
|     4 |  4 | title4  | version4  |
|     5 |  5 | title5  | version5  |
|     7 |  7 | title7  | version7  |
|     8 |  8 | title8  | version8  |
|     9 |  9 | title9  | version9  |
|    10 | 10 | title10 | version10 |
+-------+----+---------+-----------+
9 rows in set (0.00 sec)


```


## session3
> 开启事务并更新数据，但由于session1事务没有提交，且占给id <4的数据加了排他锁
> 因此，执行`update document set version='version3' where id<3;`会等待锁，直到锁等待超时（由参数innodb_lock_wait_timeout决定）

```sql
mysql> use apple;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> begin;
Query OK, 0 rows affected (0.00 sec)

mysql> update document set version='version3' where id<3;

```

## session4
> 在前三个session均开启后，此时查看processlist的Info字段，只有被阻塞的session3的update语句，其余的线程均为Sleep状态（除Binlog Dump和Daemon线程外）
> 通过sys.innodb_lock_waits表查看当前是否有锁等待，此时查看确实能看到session3的update语句被阻塞，此时能够确定线程353822锁住了数据，但问题是看不到线程353822是什么sql语句导致的
> 通过执行：`select ps.id,esh.event_id,ps.user,ps.host,trx.trx_started,esh.event_name,esh.sql_text,ps.time from performance_schema.events_statements_history esh join performance_schema.threads th on esh.thread_id = th.thread_id join information_schema.processlist ps on ps.id = th.processlist_id left join information_schema.innodb_trx trx on trx.trx_mysql_thread_id = ps.id where trx.trx_id is not null and ps.user != 'SYSTEM_USER' order by ps.id,esh.event_id;`就能发现处于Sleep状态的会话执行的历史sql，进而发现哪些会话开启了事务却一直没有提交，此处便可看出线程353822的历史sql，得知事务未提交

```sql
mysql> show processlist;
+--------+------------------+---------------------+--------------------+------------------+--------+-----------------------------------------------------------------+---------------------------------------------------+
| Id     | User             | Host                | db                 | Command          | Time   | State                                                           | Info                                              |
+--------+------------------+---------------------+--------------------+------------------+--------+-----------------------------------------------------------------+---------------------------------------------------+
|      7 | event_scheduler  | localhost           | NULL               | Daemon           | 241194 | Waiting on empty queue                                          | NULL                                              |
|  16198 | dbscale_internal | 172.17.134.71:40388 | NULL               | Binlog Dump GTID |  82861 | Source has sent all binlog to replica; waiting for more updates | NULL                                              |
| 353815 | dbscale_internal | 172.17.134.77:58974 | NULL               | Sleep            |      2 |                                                                 | NULL                                              |
| 353818 | dbscale_internal | 172.17.134.77:57584 | information_schema | Sleep            |      4 |                                                                 | NULL                                              |
| 353819 | dbscale_internal | 172.17.134.77:57598 | information_schema | Sleep            |      3 |                                                                 | NULL                                              |
| 353820 | dbscale_internal | 172.17.134.77:57610 | information_schema | Sleep            |      3 |                                                                 | NULL                                              |
| 353821 | dbscale_internal | 172.17.134.77:57616 | information_schema | Sleep            |      2 |                                                                 | NULL                                              |
| 353822 | dbscale_internal | 172.17.134.77:57620 | apple              | Sleep            |    202 |                                                                 | NULL                                              |
| 353823 | dbscale_internal | 172.17.134.77:57630 | apple              | Sleep            |    157 |                                                                 | NULL                                              |
| 353824 | dbscale_internal | 172.17.134.77:57646 | information_schema | Sleep            |      1 |                                                                 | NULL                                              |
| 353825 | dbscale_internal | 172.17.134.77:57648 | information_schema | Sleep            |      1 |                                                                 | NULL                                              |
| 353826 | dbscale_internal | 172.17.134.77:57652 | apple              | Query            |     17 | updating                                                        | update document set version='version3' where id<3 |
| 353827 | dbscale_internal | 172.17.134.77:57654 | information_schema | Query            |      0 | init                                                            | show processlist                                  |
| 353828 | dbscale_internal | 172.17.134.77:57664 | NULL               | Binlog Dump GTID |    326 | Source has sent all binlog to replica; waiting for more updates | NULL                                              |
| 353838 | dbscale_internal | 172.17.134.76:52266 | NULL               | Sleep            |    319 |                                                                 | NULL                                              |
| 353839 | dbscale_internal | 172.17.134.71:44446 | NULL               | Sleep            |    318 |                                                                 | NULL                                              |
| 353843 | dbscale_internal | 172.17.134.76:52274 | information_schema | Sleep            |     10 |                                                                 | NULL                                              |
| 353844 | dbscale_internal | 172.17.134.76:52282 | information_schema | Sleep            |      5 |                                                                 | NULL                                              |
| 353845 | dbscale_internal | 172.17.134.76:52294 | information_schema | Sleep            |      5 |                                                                 | NULL                                              |
| 353846 | dbscale_internal | 172.17.134.76:52296 | information_schema | Sleep            |     25 |                                                                 | NULL                                              |
| 353847 | dbscale_internal | 172.17.134.76:52306 | information_schema | Sleep            |     25 |                                                                 | NULL                                              |
| 353848 | dbscale_internal | 172.17.134.76:52318 | information_schema | Sleep            |     20 |                                                                 | NULL                                              |
| 353849 | dbscale_internal | 172.17.134.76:52328 | information_schema | Sleep            |     20 |                                                                 | NULL                                              |
| 353850 | dbscale_internal | 172.17.134.76:52338 | information_schema | Sleep            |     15 |                                                                 | NULL                                              |
| 353851 | dbscale_internal | 172.17.134.76:52348 | information_schema | Sleep            |     15 |                                                                 | NULL                                              |
| 353852 | dbscale_internal | 172.17.134.76:52362 | information_schema | Sleep            |     10 |                                                                 | NULL                                              |
| 353869 | dbscale_internal | 172.17.134.71:44458 | information_schema | Sleep            |      5 |                                                                 | NULL                                              |
| 353870 | dbscale_internal | 172.17.134.71:44460 | information_schema | Sleep            |     25 |                                                                 | NULL                                              |
| 353871 | dbscale_internal | 172.17.134.71:44462 | information_schema | Sleep            |     25 |                                                                 | NULL                                              |
| 353873 | dbscale_internal | 172.17.134.71:44464 | information_schema | Sleep            |     20 |                                                                 | NULL                                              |
| 353874 | dbscale_internal | 172.17.134.71:44466 | information_schema | Sleep            |     20 |                                                                 | NULL                                              |
| 353875 | dbscale_internal | 172.17.134.71:44468 | information_schema | Sleep            |     15 |                                                                 | NULL                                              |
| 353877 | dbscale_internal | 172.17.134.71:44470 | information_schema | Sleep            |     15 |                                                                 | NULL                                              |
| 353878 | dbscale_internal | 172.17.134.71:44472 | information_schema | Sleep            |     10 |                                                                 | NULL                                              |
| 353879 | dbscale_internal | 172.17.134.71:44474 | information_schema | Sleep            |     10 |                                                                 | NULL                                              |
| 353880 | dbscale_internal | 172.17.134.71:44476 | information_schema | Sleep            |      5 |                                                                 | NULL                                              |
| 354290 | dbscale_internal | 172.17.134.77:58520 | information_schema | Sleep            |      2 |                                                                 | NULL                                              |
| 354482 | dbscale_internal | 172.17.134.77:46098 | information_schema | Sleep            |      1 |                                                                 | NULL                                              |
| 355100 | dbscale_internal | 172.17.134.77:47352 | NULL               | Sleep            |      0 |                                                                 | NULL                                              |
+--------+------------------+---------------------+--------------------+------------------+--------+-----------------------------------------------------------------+---------------------------------------------------+
39 rows in set (0.00 sec)

mysql> select * from sys.innodb_lock_waits\G
*************************** 1. row ***************************
                wait_started: 2023-12-15 15:53:38
                    wait_age: 00:00:21
               wait_age_secs: 21
                locked_table: `apple`.`document`
         locked_table_schema: apple
           locked_table_name: document
      locked_table_partition: NULL
   locked_table_subpartition: NULL
                locked_index: idx_id
                 locked_type: RECORD
              waiting_trx_id: 1644095
         waiting_trx_started: 2023-12-15 15:53:38
             waiting_trx_age: 00:00:21
     waiting_trx_rows_locked: 1
   waiting_trx_rows_modified: 0
                 waiting_pid: 353826
               waiting_query: update document set version='version3' where id<3
             waiting_lock_id: 140657202871456:40:5:2:140657092016816
           waiting_lock_mode: X,REC_NOT_GAP
             blocking_trx_id: 1644089
                blocking_pid: 353822
              blocking_query: NULL
            blocking_lock_id: 140657202868224:40:5:2:140657091992336
          blocking_lock_mode: X,REC_NOT_GAP
        blocking_trx_started: 2023-12-15 15:50:24
            blocking_trx_age: 00:03:35
    blocking_trx_rows_locked: 6
  blocking_trx_rows_modified: 3
     sql_kill_blocking_query: KILL QUERY 353822
sql_kill_blocking_connection: KILL 353822
1 row in set (0.00 sec)

mysql> select ps.id,esh.event_id,ps.user,ps.host,trx.trx_started,esh.event_name,esh.sql_text,ps.time from performance_schema.events_statements_history esh join performance_schema.threads th on esh.thread_id = th.thread_id join information_schema.processlist ps on ps.id = th.processlist_id left join information_schema.innodb_trx trx on trx.trx_mysql_thread_id = ps.id where trx.trx_id is not null and ps.user != 'SYSTEM_USER' order by ps.id,esh.event_id;
+--------+----------+------------------+---------------------+---------------------+----------------------------------+-------------------------------------------------------------+------+
| id     | event_id | user             | host                | trx_started         | event_name                       | sql_text                                                    | time |
+--------+----------+------------------+---------------------+---------------------+----------------------------------+-------------------------------------------------------------+------+
| 353822 |     3721 | dbscale_internal | 172.17.134.77:57620 | 2023-12-15 15:50:24 | statement/sql/show_master_status | SHOW MASTER STATUS                                          |  209 |
| 353822 |     3738 | dbscale_internal | 172.17.134.77:57620 | 2023-12-15 15:50:24 | statement/sql/select             | SELECT DATABASE()                                           |  209 |
| 353822 |     3761 | dbscale_internal | 172.17.134.77:57620 | 2023-12-15 15:50:24 | statement/sql/change_db          | USE `apple`                                                 |  209 |
| 353822 |     3777 | dbscale_internal | 172.17.134.77:57620 | 2023-12-15 15:50:24 | statement/com/Field List         | NULL                                                        |  209 |
| 353822 |     3806 | dbscale_internal | 172.17.134.77:57620 | 2023-12-15 15:50:24 | statement/sql/begin              | begin                                                       |  209 |
| 353822 |     3824 | dbscale_internal | 172.17.134.77:57620 | 2023-12-15 15:50:24 | statement/sql/select             | SELECT LAST_INSERT_ID()                                     |  209 |
| 353822 |     3847 | dbscale_internal | 172.17.134.77:57620 | 2023-12-15 15:50:24 | statement/sql/change_db          | USE `apple`                                                 |  209 |
| 353822 |     3863 | dbscale_internal | 172.17.134.77:57620 | 2023-12-15 15:50:24 | statement/sql/insert             | insert into document values (null,11,'title11','version11') |  209 |
| 353822 |     3966 | dbscale_internal | 172.17.134.77:57620 | 2023-12-15 15:50:24 | statement/sql/select             | SELECT LAST_INSERT_ID()                                     |  209 |
| 353822 |     3990 | dbscale_internal | 172.17.134.77:57620 | 2023-12-15 15:50:24 | statement/sql/update             | update document set version='version2' where id <4          |  209 |
| 353823 |     5226 | dbscale_internal | 172.17.134.77:57630 | 2023-12-15 15:51:07 | statement/sql/show_master_status | SHOW MASTER STATUS                                          |  164 |
| 353823 |     5243 | dbscale_internal | 172.17.134.77:57630 | 2023-12-15 15:51:07 | statement/sql/show_master_status | SHOW MASTER STATUS                                          |  164 |
| 353823 |     5260 | dbscale_internal | 172.17.134.77:57630 | 2023-12-15 15:51:07 | statement/sql/change_db          | USE `apple`                                                 |  164 |
| 353823 |     5276 | dbscale_internal | 172.17.134.77:57630 | 2023-12-15 15:51:07 | statement/sql/show_databases     | show databases                                              |  164 |
| 353823 |     5427 | dbscale_internal | 172.17.134.77:57630 | 2023-12-15 15:51:07 | statement/sql/show_master_status | SHOW MASTER STATUS                                          |  164 |
| 353823 |     5444 | dbscale_internal | 172.17.134.77:57630 | 2023-12-15 15:51:07 | statement/sql/show_master_status | SHOW MASTER STATUS                                          |  164 |
| 353823 |     5461 | dbscale_internal | 172.17.134.77:57630 | 2023-12-15 15:51:07 | statement/sql/begin              | begin                                                       |  164 |
| 353823 |     5479 | dbscale_internal | 172.17.134.77:57630 | 2023-12-15 15:51:07 | statement/sql/select             | select * from document                                      |  164 |
| 353823 |     5554 | dbscale_internal | 172.17.134.77:57630 | 2023-12-15 15:51:07 | statement/sql/delete             | delete from document where id=6                             |  164 |
| 353823 |     5661 | dbscale_internal | 172.17.134.77:57630 | 2023-12-15 15:51:07 | statement/sql/select             | select * from document                                      |  164 |
| 353826 |     2964 | dbscale_internal | 172.17.134.77:57652 | 2023-12-15 15:53:38 | statement/sql/show_master_status | SHOW MASTER STATUS                                          |   24 |
| 353826 |     2981 | dbscale_internal | 172.17.134.77:57652 | 2023-12-15 15:53:38 | statement/sql/show_master_status | SHOW MASTER STATUS                                          |   24 |
| 353826 |     2998 | dbscale_internal | 172.17.134.77:57652 | 2023-12-15 15:53:38 | statement/sql/show_master_status | SHOW MASTER STATUS                                          |   24 |
| 353826 |     3015 | dbscale_internal | 172.17.134.77:57652 | 2023-12-15 15:53:38 | statement/sql/select             | select @@version_comment limit 1                            |   24 |
| 353826 |     3039 | dbscale_internal | 172.17.134.77:57652 | 2023-12-15 15:53:38 | statement/sql/show_master_status | SHOW MASTER STATUS                                          |   24 |
| 353826 |     3056 | dbscale_internal | 172.17.134.77:57652 | 2023-12-15 15:53:38 | statement/sql/show_master_status | SHOW MASTER STATUS                                          |   24 |
| 353826 |     3073 | dbscale_internal | 172.17.134.77:57652 | 2023-12-15 15:53:38 | statement/sql/show_master_status | SHOW MASTER STATUS                                          |   24 |
| 353826 |     3090 | dbscale_internal | 172.17.134.77:57652 | 2023-12-15 15:53:38 | statement/sql/show_master_status | SHOW MASTER STATUS                                          |   24 |
| 353826 |     3107 | dbscale_internal | 172.17.134.77:57652 | 2023-12-15 15:53:38 | statement/sql/change_db          | USE `apple`                                                 |   24 |
| 353826 |     3123 | dbscale_internal | 172.17.134.77:57652 | 2023-12-15 15:53:38 | statement/sql/begin              | begin                                                       |   24 |
+--------+----------+------------------+---------------------+---------------------+----------------------------------+-------------------------------------------------------------+------+
30 rows in set (0.00 sec)



```


### 当session3的锁等待超时后，再次查看
> 此时没有锁等待了，但是我们仍然能够看到哪些事务一直没有提交，此处线程353822和353823均为提交事务

```sql
mysql> select * from sys.innodb_lock_waits\G
Empty set (0.01 sec)

mysql> select ps.id,esh.event_id,ps.user,ps.host,trx.trx_started,esh.event_name,esh.sql_text,ps.time from performance_schema.events_statements_history esh join performance_schema.threads th on esh.thread_id = th.thread_id join information_schema.processlist ps on ps.id = th.processlist_id left join information_schema.innodb_trx trx on trx.trx_mysql_thread_id = ps.id where trx.trx_id is not null and ps.user != 'SYSTEM_USER' order by ps.id,esh.event_id;
+--------+----------+------------------+---------------------+---------------------+----------------------------------+-------------------------------------------------------------+------+
| id     | event_id | user             | host                | trx_started         | event_name                       | sql_text                                                    | time |
+--------+----------+------------------+---------------------+---------------------+----------------------------------+-------------------------------------------------------------+------+
| 353822 |     3721 | dbscale_internal | 172.17.134.77:57620 | 2023-12-15 15:50:24 | statement/sql/show_master_status | SHOW MASTER STATUS                                          |  245 |
| 353822 |     3738 | dbscale_internal | 172.17.134.77:57620 | 2023-12-15 15:50:24 | statement/sql/select             | SELECT DATABASE()                                           |  245 |
| 353822 |     3761 | dbscale_internal | 172.17.134.77:57620 | 2023-12-15 15:50:24 | statement/sql/change_db          | USE `apple`                                                 |  245 |
| 353822 |     3777 | dbscale_internal | 172.17.134.77:57620 | 2023-12-15 15:50:24 | statement/com/Field List         | NULL                                                        |  245 |
| 353822 |     3806 | dbscale_internal | 172.17.134.77:57620 | 2023-12-15 15:50:24 | statement/sql/begin              | begin                                                       |  245 |
| 353822 |     3824 | dbscale_internal | 172.17.134.77:57620 | 2023-12-15 15:50:24 | statement/sql/select             | SELECT LAST_INSERT_ID()                                     |  245 |
| 353822 |     3847 | dbscale_internal | 172.17.134.77:57620 | 2023-12-15 15:50:24 | statement/sql/change_db          | USE `apple`                                                 |  245 |
| 353822 |     3863 | dbscale_internal | 172.17.134.77:57620 | 2023-12-15 15:50:24 | statement/sql/insert             | insert into document values (null,11,'title11','version11') |  245 |
| 353822 |     3966 | dbscale_internal | 172.17.134.77:57620 | 2023-12-15 15:50:24 | statement/sql/select             | SELECT LAST_INSERT_ID()                                     |  245 |
| 353822 |     3990 | dbscale_internal | 172.17.134.77:57620 | 2023-12-15 15:50:24 | statement/sql/update             | update document set version='version2' where id <4          |  245 |
| 353823 |     5226 | dbscale_internal | 172.17.134.77:57630 | 2023-12-15 15:51:07 | statement/sql/show_master_status | SHOW MASTER STATUS                                          |  200 |
| 353823 |     5243 | dbscale_internal | 172.17.134.77:57630 | 2023-12-15 15:51:07 | statement/sql/show_master_status | SHOW MASTER STATUS                                          |  200 |
| 353823 |     5260 | dbscale_internal | 172.17.134.77:57630 | 2023-12-15 15:51:07 | statement/sql/change_db          | USE `apple`                                                 |  200 |
| 353823 |     5276 | dbscale_internal | 172.17.134.77:57630 | 2023-12-15 15:51:07 | statement/sql/show_databases     | show databases                                              |  200 |
| 353823 |     5427 | dbscale_internal | 172.17.134.77:57630 | 2023-12-15 15:51:07 | statement/sql/show_master_status | SHOW MASTER STATUS                                          |  200 |
| 353823 |     5444 | dbscale_internal | 172.17.134.77:57630 | 2023-12-15 15:51:07 | statement/sql/show_master_status | SHOW MASTER STATUS                                          |  200 |
| 353823 |     5461 | dbscale_internal | 172.17.134.77:57630 | 2023-12-15 15:51:07 | statement/sql/begin              | begin                                                       |  200 |
| 353823 |     5479 | dbscale_internal | 172.17.134.77:57630 | 2023-12-15 15:51:07 | statement/sql/select             | select * from document                                      |  200 |
| 353823 |     5554 | dbscale_internal | 172.17.134.77:57630 | 2023-12-15 15:51:07 | statement/sql/delete             | delete from document where id=6                             |  200 |
| 353823 |     5661 | dbscale_internal | 172.17.134.77:57630 | 2023-12-15 15:51:07 | statement/sql/select             | select * from document                                      |  200 |
+--------+----------+------------------+---------------------+---------------------+----------------------------------+-------------------------------------------------------------+------+
20 rows in set (0.00 sec)


```







