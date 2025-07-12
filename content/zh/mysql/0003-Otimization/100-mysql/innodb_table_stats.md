---
title: innodb_table_stats
description: 表统计信息
date: 2024-10-09
weight: 20000
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{<note>}}

<br/>

表统计信息是数据库基于成本的优化器最重要的参考信息；统计信息不准确，优化器可能给出不够优化的执行计划或者是错误的执行计划。

{{</note>}}

持久优化器统计信息功能通过将统计信息存储到磁盘并使其在服务器重启后持久保持

默认数据量变化超过10%以上自动默认采样为20%




| 变量名称                             | 默认值   | 描述                                                                                                                                             |
| :----------------------------------- | :------- | :----------------------------------------------------------------------------------------------------------------------------------------------- |
| innodb_stats_persistent              | ON       | 是否开启统计信息持久化                                                                                                                           |
| innodb_stats_auto_recalc             | ON       | 是否自动重新计算持久化统计信息                                                                                                                   |
| innodb_stats_include_delete_marked   | OFF      | 更新持久化统计信息时，是否会计算已经标记为删除的行，默认是关闭的，会获取未提交的脏数据。开启这个选项，MySQL 计算统计信息时只会考虑已经提交的数据 |
| innodb_stats_persistent_sample_pages | 20       | 用于更新持久化索引分布或者其他统计信息的随机基数页，页数越多，统计信息也就越准确，也就有助于查询优化器选择最优的查询计划                         |
|                                      | 默认开启 | 是否自动重新计算持久化统计信息                                                                                                                   |



什么时候考虑更改这个值呢？

1. 当查询计划不是很准确时。比如对比指定表在系统表 mysql.innodb_index_stats 的数据跟 distinct 查询的结果，如果相差太大，可以考虑增加这个值。

2. 当 analyze table 变的非常慢时，可能是这个值设置的太大了，此时要考虑减小这个值。



### analyze table




### flush table






```sql
[dbscale_internal@172.17.138.136:16310 09:30:53((none))]$ analyze table db1.sbtest1;
+-------------+---------+----------+----------+
| Table       | Op      | Msg_type | Msg_text |
+-------------+---------+----------+----------+
| db1.sbtest1 | analyze | status   | OK       |
+-------------+---------+----------+----------+
1 row in set (0.01 sec)

[dbscale_internal@172.17.138.136:16310 09:30:57((none))]$ flush table db1.sbtest1;
Query OK, 0 rows affected (0.00 sec)

```












## mysql.innodb_table_stats

```sql
[dbscale_internal@172.17.138.136:16310 09:04:31((none))]$ show create table mysql.innodb_table_stats \G
*************************** 1. row ***************************
       Table: innodb_table_stats
Create Table: CREATE TABLE `innodb_table_stats` (
  `database_name` varchar(64) COLLATE utf8_bin NOT NULL,
  `table_name` varchar(199) COLLATE utf8_bin NOT NULL,
  `last_update` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  `n_rows` bigint unsigned NOT NULL,
  `clustered_index_size` bigint unsigned NOT NULL,
  `sum_of_other_index_sizes` bigint unsigned NOT NULL,
  PRIMARY KEY (`database_name`,`table_name`)
) /*!50100 TABLESPACE `mysql` */ ENGINE=InnoDB DEFAULT CHARSET=utf8mb3 COLLATE=utf8_bin STATS_PERSISTENT=0 ROW_FORMAT=DYNAMIC
1 row in set (0.06 sec)


```


| 列名称                   | 描述                                     |
| :----------------------- | :--------------------------------------- |
| database_name            | 数据库名称                               |
| table_name               | 表名称/分区名称/子分区名称               |
| last_update              | 表示上次InnoDB更新此行的时间的时间戳     |
| n_rows                   | 表中的估算行数，是不准确的，是一个估计值 |
| clustered_index_size     | 主索引的大小（以页为单位）               |
| sum_of_other_index_sizes | 其他（非主）索引的总大小（以页为单位）   |







## 配置 InnoDB 优化器统计的采样页数


```sql
[dbscale_internal@172.17.138.136:16310 09:02:27((none))]$ show global variables like 'innodb_stats_persistent_sample_pages';
+--------------------------------------+-------+
| Variable_name                        | Value |
+--------------------------------------+-------+
| innodb_stats_persistent_sample_pages | 20    |
+--------------------------------------+-------+
1 row in set (0.00 sec)

```


## 案例


```sql
[dbscale_internal@172.17.138.136:16310 09:33:53((none))]$ show create table db1.sbtest1\G
*************************** 1. row ***************************
       Table: sbtest1
Create Table: CREATE TABLE `sbtest1` (
  `id` int NOT NULL AUTO_INCREMENT,
  `k` int NOT NULL DEFAULT '0',
  `c` char(120) NOT NULL DEFAULT '',
  `pad` char(60) NOT NULL DEFAULT '',
  PRIMARY KEY (`id`),
  KEY `k_1` (`k`)
) ENGINE=InnoDB AUTO_INCREMENT=1000001 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)

[dbscale_internal@172.17.138.136:16310 09:34:22((none))]$ select count(*) from db1.sbtest1;
+----------+
| count(*) |
+----------+
|  1000000 |
+----------+
1 row in set (0.16 sec)

[dbscale_internal@172.17.138.136:16310 09:34:35((none))]$ analyze table db1.sbtest1;
+-------------+---------+----------+----------+
| Table       | Op      | Msg_type | Msg_text |
+-------------+---------+----------+----------+
| db1.sbtest1 | analyze | status   | OK       |
+-------------+---------+----------+----------+
1 row in set (0.02 sec)

[dbscale_internal@172.17.138.136:16310 09:34:41((none))]$ flush table db1.sbtest1;
Query OK, 0 rows affected (0.00 sec)

[dbscale_internal@172.17.138.136:16310 09:34:45((none))]$ select * from mysql.innodb_table_stats where database_name='db1' and table_name='sbtest1';
+---------------+------------+---------------------+--------+----------------------+--------------------------+
| database_name | table_name | last_update         | n_rows | clustered_index_size | sum_of_other_index_sizes |
+---------------+------------+---------------------+--------+----------------------+--------------------------+
| db1           | sbtest1    | 2024-10-25 09:34:41 | 986400 |                13741 |                     1116 |
+---------------+------------+---------------------+--------+----------------------+--------------------------+
1 row in set (0.00 sec)

[dbscale_internal@172.17.138.136:16310 09:34:52((none))]$ select * from mysql.innodb_index_stats where database_name='db1' and table_name='sbtest1';
+---------------+------------+------------+---------------------+--------------+------------+-------------+-----------------------------------+
| database_name | table_name | index_name | last_update         | stat_name    | stat_value | sample_size | stat_description                  |
+---------------+------------+------------+---------------------+--------------+------------+-------------+-----------------------------------+
| db1           | sbtest1    | PRIMARY    | 2024-10-25 09:34:41 | n_diff_pfx01 |     986400 |          20 | id                                |
| db1           | sbtest1    | PRIMARY    | 2024-10-25 09:34:41 | n_leaf_pages |      13700 |        NULL | Number of leaf pages in the index |
| db1           | sbtest1    | PRIMARY    | 2024-10-25 09:34:41 | size         |      13741 |        NULL | Number of pages in the index      |
| db1           | sbtest1    | k_1        | 2024-10-25 09:34:41 | n_diff_pfx01 |     616788 |          20 | k                                 |
| db1           | sbtest1    | k_1        | 2024-10-25 09:34:41 | n_diff_pfx02 |     999264 |          20 | k,id                              |
| db1           | sbtest1    | k_1        | 2024-10-25 09:34:41 | n_leaf_pages |        916 |        NULL | Number of leaf pages in the index |
| db1           | sbtest1    | k_1        | 2024-10-25 09:34:41 | size         |       1116 |        NULL | Number of pages in the index      |
+---------------+------------+------------+---------------------+--------------+------------+-------------+-----------------------------------+
7 rows in set (0.00 sec)


```

## show table status like

```sql
[dbscale_internal@172.17.138.136:16310 09:52:24((none))]$ use db1;
Database changed
[dbscale_internal@172.17.138.136:16310 09:52:28(db1)]$ show table status like 'sbtest1';
+---------+--------+---------+------------+--------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+---------------------+------------+--------------------+----------+----------------+---------+
| Name    | Engine | Version | Row_format | Rows   | Avg_row_length | Data_length | Max_data_length | Index_length | Data_free | Auto_increment | Create_time         | Update_time         | Check_time | Collation          | Checksum | Create_options | Comment |
+---------+--------+---------+------------+--------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+---------------------+------------+--------------------+----------+----------------+---------+
| sbtest1 | InnoDB |      10 | Dynamic    | 986400 |            228 |   225132544 |               0 |     18284544 |   6291456 |        1000001 | 2024-10-24 17:02:31 | 2024-10-24 21:55:57 | NULL       | utf8mb4_0900_ai_ci |     NULL |                |         |
+---------+--------+---------+------------+--------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+---------------------+------------+--------------------+----------+----------------+---------+
1 row in set (0.00 sec)
```







## 修改表的统计信息



### 1. update mysql.innodb_table_stats

> show table status from 'XXX' like 'YYY' $\Longrightarrow$  update mysql.innodb_table_stats $\Longrightarrow$  flush table $\Longrightarrow$   show table status from 'XXX' like 'YYY'

```sql
[dbscale_internal@172.17.138.136:16310 09:59:45(db1)]$ select count(*) from db1.sbtest1;
+----------+
| count(*) |
+----------+
|  1000000 |
+----------+
1 row in set (0.10 sec)

[dbscale_internal@172.17.138.136:16310 09:59:56(db1)]$ select * from mysql.innodb_table_stats where database_name='db1' and table_name='sbtest1';
+---------------+------------+---------------------+--------+----------------------+--------------------------+
| database_name | table_name | last_update         | n_rows | clustered_index_size | sum_of_other_index_sizes |
+---------------+------------+---------------------+--------+----------------------+--------------------------+
| db1           | sbtest1    | 2024-10-25 09:34:41 | 986400 |                13741 |                     1116 |
+---------------+------------+---------------------+--------+----------------------+--------------------------+
1 row in set (0.00 sec)

[dbscale_internal@172.17.138.136:16310 10:00:08(db1)]$ update mysql.innodb_table_stats set n_rows=1000000 where database_name='db1' and table_name='sbtest1';
Query OK, 1 row affected (0.01 sec)
Rows matched: 1  Changed: 1  Warnings: 0

[dbscale_internal@172.17.138.136:16310 10:00:48(db1)]$ select * from mysql.innodb_table_stats where database_name='db1' and table_name='sbtest1';
+---------------+------------+---------------------+---------+----------------------+--------------------------+
| database_name | table_name | last_update         | n_rows  | clustered_index_size | sum_of_other_index_sizes |
+---------------+------------+---------------------+---------+----------------------+--------------------------+
| db1           | sbtest1    | 2024-10-25 10:00:48 | 1000000 |                13741 |                     1116 |
+---------------+------------+---------------------+---------+----------------------+--------------------------+
1 row in set (0.00 sec)

[dbscale_internal@172.17.138.136:16310 10:00:57(db1)]$ show table status like 'sbtest1';
+---------+--------+---------+------------+--------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+---------------------+------------+--------------------+----------+----------------+---------+
| Name    | Engine | Version | Row_format | Rows   | Avg_row_length | Data_length | Max_data_length | Index_length | Data_free | Auto_increment | Create_time         | Update_time         | Check_time | Collation          | Checksum | Create_options | Comment |
+---------+--------+---------+------------+--------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+---------------------+------------+--------------------+----------+----------------+---------+
| sbtest1 | InnoDB |      10 | Dynamic    | 986400 |            228 |   225132544 |               0 |     18284544 |   6291456 |        1000001 | 2024-10-24 17:02:31 | 2024-10-24 21:55:57 | NULL       | utf8mb4_0900_ai_ci |     NULL |                |         |
+---------+--------+---------+------------+--------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+---------------------+------------+--------------------+----------+----------------+---------+
1 row in set (0.00 sec)

[dbscale_internal@172.17.138.136:16310 10:01:30(db1)]$ flush table db1.sbtest1;
Query OK, 0 rows affected (0.00 sec)

```

### 2. analyze table




```sql
[dbscale_internal@172.17.138.136:16310 09:34:22((none))]$ select count(*) from db1.sbtest1;
+----------+
| count(*) |
+----------+
|  1000000 |
+----------+
1 row in set (0.16 sec)

[dbscale_internal@172.17.138.136:16310 09:34:35((none))]$ analyze table db1.sbtest1;
+-------------+---------+----------+----------+
| Table       | Op      | Msg_type | Msg_text |
+-------------+---------+----------+----------+
| db1.sbtest1 | analyze | status   | OK       |
+-------------+---------+----------+----------+
1 row in set (0.02 sec)

[dbscale_internal@172.17.138.136:16310 09:34:41((none))]$ flush table db1.sbtest1;
Query OK, 0 rows affected (0.00 sec)

[dbscale_internal@172.17.138.136:16310 09:34:45((none))]$ select * from mysql.innodb_table_stats where database_name='db1' and table_name='sbtest1';
+---------------+------------+---------------------+--------+----------------------+--------------------------+
| database_name | table_name | last_update         | n_rows | clustered_index_size | sum_of_other_index_sizes |
+---------------+------------+---------------------+--------+----------------------+--------------------------+
| db1           | sbtest1    | 2024-10-25 09:34:41 | 986400 |                13741 |                     1116 |
+---------------+------------+---------------------+--------+----------------------+--------------------------+
1 row in set (0.00 sec)


```








