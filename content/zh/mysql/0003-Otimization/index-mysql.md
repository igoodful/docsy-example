---
title: 最左匹配
description: 最左匹配
date: 2023-10-13
weight: 20000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

{{< /alert >}}

### innodb的dml操作的行级锁的等待时间
```sql
mysql> show create table test\G
*************************** 1. row ***************************
       Table: test
Create Table: CREATE TABLE `test` (
  `id` int NOT NULL AUTO_INCREMENT,
  `a` datetime NOT NULL,
  `b` varchar(10) NOT NULL,
  `c` varchar(10) NOT NULL,
  `d` varchar(10) NOT NULL,
  `e` varchar(10) NOT NULL,
  PRIMARY KEY (`id`,`a`)
) ENGINE=InnoDB AUTO_INCREMENT=155375 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
/*!50500 PARTITION BY RANGE  COLUMNS(a)
(PARTITION p1 VALUES LESS THAN ('2024-04-17 11:55:00') ENGINE = InnoDB,
 PARTITION p2 VALUES LESS THAN ('2024-04-17 12:00:00') ENGINE = InnoDB,
 PARTITION p3 VALUES LESS THAN ('2024-04-17 12:05:00') ENGINE = InnoDB,
 PARTITION p4 VALUES LESS THAN ('2024-04-17 12:10:00') ENGINE = InnoDB,
 PARTITION p5 VALUES LESS THAN ('2024-04-17 12:15:00') ENGINE = InnoDB,
 PARTITION p6 VALUES LESS THAN ('2024-04-17 12:20:00') ENGINE = InnoDB,
 PARTITION p7 VALUES LESS THAN ('2024-04-17 12:25:00') ENGINE = InnoDB,
 PARTITION p8 VALUES LESS THAN ('2024-04-17 12:30:00') ENGINE = InnoDB,
 PARTITION p9 VALUES LESS THAN ('2024-04-17 12:40:00') ENGINE = InnoDB,
 PARTITION p10 VALUES LESS THAN ('2024-04-17 13:20:00') ENGINE = InnoDB) */
1 row in set (0.01 sec)

mysql> select count(*) from test;
+----------+
| count(*) |
+----------+
|   155374 |
+----------+
1 row in set (0.02 sec)

mysql> desc select a from test where a='2024-04-17 12:59:51';
+----+-------------+-------+------------+-------+---------------+---------+---------+------+-------+----------+--------------------------+
| id | select_type | table | partitions | type  | possible_keys | key     | key_len | ref  | rows  | filtered | Extra                    |
+----+-------------+-------+------------+-------+---------------+---------+---------+------+-------+----------+--------------------------+
|  1 | SIMPLE      | test  | p10        | index | PRIMARY       | PRIMARY | 9       | NULL | 61823 |    10.00 | Using where; Using index |
+----+-------------+-------+------------+-------+---------------+---------+---------+------+-------+----------+--------------------------+
1 row in set, 1 warning (0.01 sec)

mysql> desc select id,a from test where a='2024-04-17 12:59:51';
+----+-------------+-------+------------+-------+---------------+---------+---------+------+-------+----------+--------------------------+
| id | select_type | table | partitions | type  | possible_keys | key     | key_len | ref  | rows  | filtered | Extra                    |
+----+-------------+-------+------------+-------+---------------+---------+---------+------+-------+----------+--------------------------+
|  1 | SIMPLE      | test  | p10        | index | PRIMARY       | PRIMARY | 9       | NULL | 61823 |    10.00 | Using where; Using index |
+----+-------------+-------+------------+-------+---------------+---------+---------+------+-------+----------+--------------------------+
1 row in set, 1 warning (0.01 sec)

mysql> desc select id,a,b from test where a='2024-04-17 12:59:51';
+----+-------------+-------+------------+------+---------------+------+---------+------+-------+----------+-------------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows  | filtered | Extra       |
+----+-------------+-------+------------+------+---------------+------+---------+------+-------+----------+-------------+
|  1 | SIMPLE      | test  | p10        | ALL  | NULL          | NULL | NULL    | NULL | 61823 |    10.00 | Using where |
+----+-------------+-------+------------+------+---------------+------+---------+------+-------+----------+-------------+
1 row in set, 1 warning (0.01 sec)

```
