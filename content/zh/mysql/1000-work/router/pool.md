---
title: pool
date: 2017-01-05
description: >
  workbench是一款可视化的数据库设计工具.


weight: 70000
---

{{< alert >}}

1. 免费开源，易于学习

2. 适用MySQL

3. 轻量级数据库客户端软件，拥有图形化界面
3.

7.2 在线课程和培训

7.3 MySQL 社区和论坛
{{< /alert >}}

> 刷新连接池版本
```sql
-- 查看连接池使用情况，依次查看每一个调度节点的连接池
mysql> dbscale show connection pool;
+-------------+---------------------+------+------+------+-------+------+---------+------------+---------+
| Server name | Pool name           | Used | Free | Dead | Total | Max  | Times_s | Times_tens | Times_m |
+-------------+---------------------+------+------+------+-------+------+---------+------------+---------+
| normal_0_0  | normal_0_normal_0_0 | 0    | 2    | 0    | 2     | 1000 | 0       | 8          | 46      |
| normal_0_1  | normal_0_normal_0_1 | 0    | 1    | 0    | 1     | 1000 | 0       | 0          | 0       |
| normal_0_2  | normal_0_normal_0_2 | 0    | 1    | 0    | 1     | 1000 | 0       | 0          | 0       |
+-------------+---------------------+------+------+------+-------+------+---------+------------+---------+
3 rows in set (0.00 sec)

-- 刷新连接池
mysql> dbscale flush connection pool;
Query OK, 0 rows affected (0.00 sec)

-- 清理指定连接池的Used连接
mysql> dbscale purge connection pool normal_0_normal_0_0;
Query OK, 0 rows affected (0.01 sec)

mysql> dbscale show connection pool;
+-------------+---------------------+------+------+------+-------+------+---------+------------+---------+
| Server name | Pool name           | Used | Free | Dead | Total | Max  | Times_s | Times_tens | Times_m |
+-------------+---------------------+------+------+------+-------+------+---------+------------+---------+
| normal_0_0  | normal_0_normal_0_0 | 0    | 1    | 0    | 1     | 2000 | 2       | 7          | 47      |
| normal_0_1  | normal_0_normal_0_1 | 0    | 1    | 0    | 1     | 1000 | 0       | 0          | 0       |
| normal_0_2  | normal_0_normal_0_2 | 0    | 1    | 0    | 1     | 1000 | 0       | 0          | 0       |
+-------------+---------------------+------+------+------+-------+------+---------+------------+---------+
3 rows in set (0.00 sec)

--- 修改连接池最大连接数
mysql> dbscale reset connection pool normal_0_normal_0_0 2000;
Query OK, 0 rows affected (0.21 sec)

mysql> dbscale show connection pool;
+-------------+---------------------+------+------+------+-------+------+---------+------------+---------+
| Server name | Pool name           | Used | Free | Dead | Total | Max  | Times_s | Times_tens | Times_m |
+-------------+---------------------+------+------+------+-------+------+---------+------------+---------+
| normal_0_0  | normal_0_normal_0_0 | 0    | 2    | 0    | 2     | 2000 | 0       | 8          | 46      |
| normal_0_1  | normal_0_normal_0_1 | 0    | 1    | 0    | 1     | 1000 | 0       | 0          | 0       |
| normal_0_2  | normal_0_normal_0_2 | 0    | 1    | 0    | 1     | 1000 | 0       | 0          | 0       |
+-------------+---------------------+------+------+------+-------+------+---------+------------+---------+
3 rows in set (0.00 sec)


```





















