---
title: innodb_index_stats
description: 索引统计信息
date: 2024-10-09
weight: 20000
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



## mysql.innodb_index_stats

**表结构**

```sql
[dbscale_internal@172.17.138.136:16310 17:01:27((none))]$ show create table mysql.innodb_index_stats\G
*************************** 1. row ***************************
       Table: innodb_index_stats
Create Table: CREATE TABLE `innodb_index_stats` (
  `database_name` varchar(64) COLLATE utf8_bin NOT NULL,
  `table_name` varchar(199) COLLATE utf8_bin NOT NULL,
  `index_name` varchar(64) COLLATE utf8_bin NOT NULL,
  `last_update` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  `stat_name` varchar(64) COLLATE utf8_bin NOT NULL,
  `stat_value` bigint unsigned NOT NULL,
  `sample_size` bigint unsigned DEFAULT NULL,
  `stat_description` varchar(1024) COLLATE utf8_bin NOT NULL,
  PRIMARY KEY (`database_name`,`table_name`,`index_name`,`stat_name`)
) /*!50100 TABLESPACE `mysql` */ ENGINE=InnoDB DEFAULT CHARSET=utf8mb3 COLLATE=utf8_bin STATS_PERSISTENT=0 ROW_FORMAT=DYNAMIC
1 row in set (0.01 sec)

```


| 列名称           | 描述                                 |
| :--------------- | :----------------------------------- |
| database_name    | 数据库名称                           |
| table_name       | 表名称、分区名称或子分区名称         |
| index_name       | 索引名称                             |
| last_update      | 表示行上次更新时间的时间戳           |
| stat_name        | 统计项的名称                         |
| stat_value       | 统计项的值                           |
| sample_size      | stat_value列中提供的估算所抽样的页数 |
| stat_description | 统计项的描述                         |



- stat_name

| stat_name    | stat_value                               |
| :----------- | :--------------------------------------- |
| size         | 该索引使用页面的总数量                   |
| n_leaf_pages | 该索引的叶子节点使用的页面数量           |
| n_diff_pfxNN | stat_description列出的索引中不同值的行数 |




### n_diff_pfxNN

```sql
[dbscale_internal@172.17.138.136:16310 10:06:57(db1)]$ select * from db1.sbtest1 limit 1;
+----+-------+-------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------+
| id | k     | c                                                                                                                       | pad                                                         |
+----+-------+-------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------+
|  1 | 82084 | 84694055936-48793657667-95127487433-35490502186-54086379318-10730770590-73458814381-78006955375-74889149346-24419516692 | 57209070398-54273821973-43256470936-31700834553-55682038840 |
+----+-------+-------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------+
1 row in set (0.00 sec)

[dbscale_internal@172.17.138.136:16310 10:07:29(db1)]$ select count(distinct k) from db1.sbtest1 limit 1;
+-------------------+
| count(distinct k) |
+-------------------+
|            621407 |
+-------------------+
1 row in set (0.40 sec)

[dbscale_internal@172.17.138.136:16310 10:08:15(db1)]$ select count(distinct k,id) from db1.sbtest1 limit 1;
+----------------------+
| count(distinct k,id) |
+----------------------+
|              1000000 |
+----------------------+
1 row in set (0.29 sec)

[dbscale_internal@172.17.138.136:16310 10:08:46(db1)]$ select * from mysql.innodb_index_stats where database_name='db1' and table_name='sbtest1';
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








## 案例


```sql
[dbscale_internal@172.17.138.136:16310 09:27:17((none))]$ select count(*) from db1.sbtest1;
+----------+
| count(*) |
+----------+
|  1000000 |
+----------+
1 row in set (0.05 sec)

[dbscale_internal@172.17.138.136:16310 09:27:22((none))]$ show create table db1.sbtest1\G
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

[dbscale_internal@172.17.138.136:16310 09:27:33((none))]$ select * from mysql.innodb_index_stats where database_name='db1' and table_name='sbtest1';
+---------------+------------+------------+---------------------+--------------+------------+-------------+-----------------------------------+
| database_name | table_name | index_name | last_update         | stat_name    | stat_value | sample_size | stat_description                  |
+---------------+------------+------------+---------------------+--------------+------------+-------------+-----------------------------------+
| db1           | sbtest1    | PRIMARY    | 2024-10-25 09:17:58 | n_diff_pfx01 |     986400 |          20 | id                                |
| db1           | sbtest1    | PRIMARY    | 2024-10-25 09:17:58 | n_leaf_pages |      13700 |        NULL | Number of leaf pages in the index |
| db1           | sbtest1    | PRIMARY    | 2024-10-25 09:17:58 | size         |      13741 |        NULL | Number of pages in the index      |
| db1           | sbtest1    | k_1        | 2024-10-25 09:17:58 | n_diff_pfx01 |     617613 |          20 | k                                 |
| db1           | sbtest1    | k_1        | 2024-10-25 09:17:58 | n_diff_pfx02 |    1003707 |          20 | k,id                              |
| db1           | sbtest1    | k_1        | 2024-10-25 09:17:58 | n_leaf_pages |        916 |        NULL | Number of leaf pages in the index |
| db1           | sbtest1    | k_1        | 2024-10-25 09:17:58 | size         |       1116 |        NULL | Number of pages in the index      |
+---------------+------------+------------+---------------------+--------------+------------+-------------+-----------------------------------+
7 rows in set (0.00 sec)

[dbscale_internal@172.17.138.136:16310 09:52:30(db1)]$ show index from sbtest1;
+---------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
| Table   | Non_unique | Key_name | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment | Visible | Expression |
+---------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
| sbtest1 |          0 | PRIMARY  |            1 | id          | A         |      986400 |     NULL |   NULL |      | BTREE      |         |               | YES     | NULL       |
| sbtest1 |          1 | k_1      |            1 | k           | A         |      616788 |     NULL |   NULL |      | BTREE      |         |               | YES     | NULL       |
+---------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
2 rows in set (0.01 sec)


```



## 统计索引大小


```sql
[dbscale_internal@172.17.138.136:16310 09:43:20((none))]$ select sum(stat_value) pages, index_name,sum(stat_value)*@@innodb_page_size/1024/1024 `size(M)` from mysql.innodb_index_stats where table_name='sbtest1' and stat_name = 'size' group by index_name;
+-------+------------+--------------+
| pages | index_name | size(M)      |
+-------+------------+--------------+
| 13741 | PRIMARY    | 214.70312500 |
|  1116 | k_1        |  17.43750000 |
+-------+------------+--------------+
2 rows in set (0.00 sec)

```



