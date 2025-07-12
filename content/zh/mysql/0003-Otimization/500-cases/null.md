---
title: null问题
description: null问题
date: 2023-10-13
weight: 20000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


{{<alert color="secondary">}}


{{</alert>}}




```sql
[dbscale_internal@172.17.134.76:16310 17:20:58(sbtest)]$ show create table app\G
*************************** 1. row ***************************
       Table: app
Create Table: CREATE TABLE `app` (
  `id` int NOT NULL AUTO_INCREMENT,
  `c` varchar(120) DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `c_1` (`c`)
) ENGINE=InnoDB AUTO_INCREMENT=11 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.01 sec)

[dbscale_internal@172.17.134.76:16310 17:21:09(sbtest)]$ select * from app;
+----+------+
| id | c    |
+----+------+
|  4 | NULL |
|  5 | NULL |
|  6 | NULL |
|  2 | 1    |
|  1 | 1111 |
|  3 | 2    |
|  7 | 4    |
|  8 | 5    |
|  9 | 6    |
| 10 | null |
+----+------+
10 rows in set (0.00 sec)

[dbscale_internal@172.17.134.76:16310 17:21:18(sbtest)]$ insert into app values (null,null);
Query OK, 1 row affected (0.01 sec)

[dbscale_internal@172.17.134.76:16310 17:21:55(sbtest)]$ select * from app;
+----+------+
| id | c    |
+----+------+
|  4 | NULL |
|  5 | NULL |
|  6 | NULL |
| 11 | NULL |
|  2 | 1    |
|  1 | 1111 |
|  3 | 2    |
|  7 | 4    |
|  8 | 5    |
|  9 | 6    |
| 10 | null |
+----+------+
11 rows in set (0.00 sec)

[dbscale_internal@172.17.134.76:16310 17:21:56(sbtest)]$ select * from app;
+----+------+
| id | c    |
+----+------+
|  4 | NULL |
|  5 | NULL |
|  6 | NULL |
| 11 | NULL |
|  2 | 1    |
|  1 | 1111 |
|  3 | 2    |
|  7 | 4    |
|  8 | 5    |
|  9 | 6    |
| 10 | null |
+----+------+
11 rows in set (0.00 sec)

[dbscale_internal@172.17.134.76:16310 17:22:01(sbtest)]$ show create table app\G
*************************** 1. row ***************************
       Table: app
Create Table: CREATE TABLE `app` (
  `id` int NOT NULL AUTO_INCREMENT,
  `c` varchar(120) DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `c_1` (`c`)
) ENGINE=InnoDB AUTO_INCREMENT=12 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)

[dbscale_internal@172.17.134.76:16310 17:22:04(sbtest)]$ select * from app;
+----+------+
| id | c    |
+----+------+
|  4 | NULL |
|  5 | NULL |
|  6 | NULL |
| 11 | NULL |
|  2 | 1    |
|  1 | 1111 |
|  3 | 2    |
|  7 | 4    |
|  8 | 5    |
|  9 | 6    |
| 10 | null |
+----+------+
11 rows in set (0.00 sec)

[dbscale_internal@172.17.134.76:16310 17:22:06(sbtest)]$ select * from app where c is null;
+----+------+
| id | c    |
+----+------+
|  4 | NULL |
|  5 | NULL |
|  6 | NULL |
| 11 | NULL |
+----+------+
4 rows in set (0.00 sec)

[dbscale_internal@172.17.134.76:16310 17:22:23(sbtest)]$ select * from app where c is not null;
+----+------+
| id | c    |
+----+------+
|  2 | 1    |
|  1 | 1111 |
|  3 | 2    |
|  7 | 4    |
|  8 | 5    |
|  9 | 6    |
| 10 | null |
+----+------+
7 rows in set (0.00 sec)

[dbscale_internal@172.17.134.76:16310 17:22:30(sbtest)]$ select * from app where c = null;
Empty set (0.00 sec)

[dbscale_internal@172.17.134.76:16310 17:22:49(sbtest)]$ select * from app where c != null;
Empty set (0.00 sec)

[dbscale_internal@172.17.134.76:16310 17:22:55(sbtest)]$ select * from app where c <> null;
Empty set (0.00 sec)

[dbscale_internal@172.17.134.76:16310 17:23:02(sbtest)]$ desc select * from app where c is null;
+----+-------------+-------+------------+------+---------------+------+---------+-------+------+----------+--------------------------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref   | rows | filtered | Extra                    |
+----+-------------+-------+------------+------+---------------+------+---------+-------+------+----------+--------------------------+
|  1 | SIMPLE      | app   | NULL       | ref  | c_1           | c_1  | 483     | const |    4 |   100.00 | Using where; Using index |
+----+-------------+-------+------------+------+---------------+------+---------+-------+------+----------+--------------------------+
1 row in set, 1 warning (0.00 sec)

[dbscale_internal@172.17.134.76:16310 17:23:15(sbtest)]$ desc select * from app where c is not null;
+----+-------------+-------+------------+-------+---------------+------+---------+------+------+----------+--------------------------+
| id | select_type | table | partitions | type  | possible_keys | key  | key_len | ref  | rows | filtered | Extra                    |
+----+-------------+-------+------------+-------+---------------+------+---------+------+------+----------+--------------------------+
|  1 | SIMPLE      | app   | NULL       | range | c_1           | c_1  | 483     | NULL |    7 |   100.00 | Using where; Using index |
+----+-------------+-------+------------+-------+---------------+------+---------+------+------+----------+--------------------------+
1 row in set, 1 warning (0.00 sec)

[dbscale_internal@172.17.134.76:16310 17:23:27(sbtest)]$ desc select * from app where c = 'null';
+----+-------------+-------+------------+------+---------------+------+---------+-------+------+----------+-------------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref   | rows | filtered | Extra       |
+----+-------------+-------+------------+------+---------------+------+---------+-------+------+----------+-------------+
|  1 | SIMPLE      | app   | NULL       | ref  | c_1           | c_1  | 483     | const |    1 |   100.00 | Using index |
+----+-------------+-------+------------+------+---------------+------+---------+-------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)

[dbscale_internal@172.17.134.76:16310 17:26:26(sbtest)]$

```




















