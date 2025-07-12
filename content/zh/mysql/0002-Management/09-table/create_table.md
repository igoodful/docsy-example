title: 建表
description: crate table
date: 2023-10-12
weight: 60000


---





## 建表











## 日期时间



## from_unixtime unix_timestamp

```sql

[root@localhost:mysql.sock 21:49:51(auth)]$ select from_unixtime('1717940978');
+-----------------------------+
| from_unixtime('1717940978') |
+-----------------------------+
| 2024-06-09 13:49:38.000000  |
+-----------------------------+
1 row in set (0.00 sec)

[root@localhost:mysql.sock 21:50:19(auth)]$ select unix_timestamp('2024-05-10 10:10:10');
+---------------------------------------+
| unix_timestamp('2024-05-10 10:10:10') |
+---------------------------------------+
|                            1715335810 |
+---------------------------------------+
1 row in set (0.00 sec)

```


## time_zone
```sql
[root@localhost:mysql.sock 10:11:59(auth)]$ show variables like '%time_zone%';
+------------------+--------+
| Variable_name    | Value  |
+------------------+--------+
| system_time_zone | UTC    |
| time_zone        | +08:00 |
+------------------+--------+
2 rows in set (0.00 sec)

[root@localhost:mysql.sock 10:12:11(auth)]$ select now();
+---------------------+
| now()               |
+---------------------+
| 2024-05-11 10:12:20 |
+---------------------+
1 row in set (0.00 sec)

[root@localhost:mysql.sock 10:12:20(auth)]$ select curtime();
+-----------+
| curtime() |
+-----------+
| 10:12:22  |
+-----------+
1 row in set (0.00 sec)

```



## connection

- https://bugs.mysql.com/bug.php?id=118611

不应允许使用 CONNECTION 子句创建 InnoDB 表。目前，该子句可以正常工作，并且 InnoDB 表可以正常工作，并且不会向 DML 语句的错误日志中发送任何明显的错误或警告。


```sql
 create table test_table (
  `id` int not null auto_increment,
  `c` char(10) default null,
  primary key (`id`)
) engine=innodb auto_increment=1 default charset=utf8mb4 collate=utf8mb4_0900_ai_ci connection='mysql://federated:federated@198.19.249.193:3306/federated/test_table'


```




