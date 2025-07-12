---
title: 区分度不大的字段真的不适合创建索引吗？
description: index
date: 2024-12-17
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
[dbscale_internal@172.17.135.250:16310 10:08:53(db4)]$ alter table sbtest1 add column sex int default 0;
Query OK, 0 rows affected (0.03 sec)
Records: 0  Duplicates: 0  Warnings: 0

[dbscale_internal@172.17.135.250:16310 10:47:06(db4)]$ update sbtest1 set sex=1 where id<100000;
Query OK, 99999 rows affected (1.45 sec)

[dbscale_internal@172.17.135.250:16310 10:50:15(db4)]$ update sbtest1 set sex=1 where id<300000 and id>100000;
Query OK, 199999 rows affected (3.72 sec)

[dbscale_internal@172.17.135.250:16310 10:50:42(db4)]$ update sbtest1 set sex=1 where id<500000 and id>300000;
Query OK, 199999 rows affected (2.84 sec)

[dbscale_internal@172.17.135.250:16310 10:50:54(db4)]$ update sbtest1 set sex=1 where id<700000 and id>500000;
Query OK, 199999 rows affected (7.53 sec)

[dbscale_internal@172.17.135.250:16310 10:51:09(db4)]$ update sbtest1 set sex=1 where id<900000 and id>700000;
Query OK, 199999 rows affected (7.74 sec)

[dbscale_internal@172.17.135.250:16310 10:51:32(db4)]$ update sbtest1 set sex=2 where id<1100000 and id>900000;
Query OK, 199999 rows affected (7.05 sec)

[dbscale_internal@172.17.135.250:16310 10:51:50(db4)]$ update sbtest1 set sex=2 where id<1300000 and id>1100000;
Query OK, 199999 rows affected (7.64 sec)

[dbscale_internal@172.17.135.250:16310 10:52:04(db4)]$ update sbtest1 set sex=2 where id<1500000 and id>1300000;
Query OK, 199999 rows affected (6.96 sec)

[dbscale_internal@172.17.135.250:16310 10:52:26(db4)]$ update sbtest1 set sex=2 where id<1700000 and id>1500000;
Query OK, 199999 rows affected (6.42 sec)

[dbscale_internal@172.17.135.250:16310 10:52:43(db4)]$ select count(*) from sbtest1 where sex=3;
+----------+
| count(*) |
+----------+
|        0 |
+----------+
1 row in set (8.80 sec)

[dbscale_internal@172.17.135.250:16310 10:53:20(db4)]$ select count(*) from sbtest1;
+----------+
| count(*) |
+----------+
|  5000000 |
+----------+
1 row in set (0.32 sec)

[dbscale_internal@172.17.135.250:16310 10:53:27(db4)]$ select count(*) from sbtest1 where sex=1;
+----------+
| count(*) |
+----------+
|   899995 |
+----------+
1 row in set (1.54 sec)

[dbscale_internal@172.17.135.250:16310 10:53:39(db4)]$ select count(*) from sbtest1 where sex=2;
+----------+
| count(*) |
+----------+
|   799996 |
+----------+
1 row in set (0.94 sec)

[dbscale_internal@172.17.135.250:16310 10:53:49(db4)]$ select count(*) from sbtest1 where sex=0;
+----------+
| count(*) |
+----------+
|  3300009 |
+----------+
1 row in set (1.60 sec)

[dbscale_internal@172.17.135.250:16310 10:53:54(db4)]$ alter table sbtest1 add index idx_sex(sex);
Query OK, 0 rows affected (15.36 sec)
Records: 0  Duplicates: 0  Warnings: 0

[dbscale_internal@172.17.135.250:16310 10:54:49(db4)]$ select count(*) from sbtest1 where sex=3;
+----------+
| count(*) |
+----------+
|        0 |
+----------+
1 row in set (0.00 sec)

[dbscale_internal@172.17.135.250:16310 10:55:05(db4)]$ select count(*) from sbtest1 where sex=1;
+----------+
| count(*) |
+----------+
|   899995 |
+----------+
1 row in set (0.40 sec)

[dbscale_internal@172.17.135.250:16310 10:55:11(db4)]$ select count(*) from sbtest1 where sex=2;
+----------+
| count(*) |
+----------+
|   799996 |
+----------+
1 row in set (0.26 sec)

[dbscale_internal@172.17.135.250:16310 10:55:17(db4)]$ select count(*) from sbtest1 where sex=0;
+----------+
| count(*) |
+----------+
|  3300009 |
+----------+
1 row in set (1.42 sec)

[dbscale_internal@172.17.135.250:16310 10:55:21(db4)]$ select sex,count(*) from sbtest1 group by sex;
+------+----------+
| sex  | count(*) |
+------+----------+
|    0 |  3300009 |
|    1 |   899995 |
|    2 |   799996 |
+------+----------+
3 rows in set (1.43 sec)

[dbscale_internal@172.17.135.250:16310 10:56:05(db4)]$ select count(*) from sbtest1;
+----------+
| count(*) |
+----------+
|  5000000 |
+----------+
1 row in set (0.43 sec)

[dbscale_internal@172.17.135.250:16310 10:56:23(db4)]$ show create table sbtest1\G
*************************** 1. row ***************************
       Table: sbtest1
Create Table: CREATE TABLE `sbtest1` (
  `id` int NOT NULL AUTO_INCREMENT,
  `k` int NOT NULL DEFAULT '0',
  `c` char(120) NOT NULL DEFAULT '',
  `pad` char(60) NOT NULL DEFAULT '',
  `sex` int DEFAULT '0',
  PRIMARY KEY (`id`),
  KEY `k_1` (`k`),
  KEY `idx_sex` (`sex`)
) ENGINE=InnoDB AUTO_INCREMENT=5000001 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)

[dbscale_internal@172.17.135.250:16310 10:56:38(db4)]$

```


- 不加索引时，耗时8.80秒
- 加上索引时，耗时0.0秒
















