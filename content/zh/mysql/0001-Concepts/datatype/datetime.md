---
title: 日期时间
description: datetime
date: 2023-10-25
weight: 3000


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}


{{< /alert >}}



## 一、时间单位换算

| 单位 | 换算           | 备注             |
|:----|:---------------|:---------------|
| 秒   | `1s  = 1000ms` | 1秒   = 1000毫秒 |
| 毫秒 | `1ms = 1000μs` | 1毫秒 = 1000微秒 |
| 微秒 | `1us = 1000ns` | 1微秒 = 1000纳秒 |
| 纳秒 | `1ns = 1000ps` | 1纳秒 = 1000皮秒 |



## 二、五大日期时间类型

| 数据类型  | 零值                  | 范围                                                        | 示例                      |
|:----------|:----------------------|:------------------------------------------------------------|:--------------------------|
| date      | '0000-00-00'          | '1000-01-01' ~ '9999-12-31'                                 | 年份只要四位数字即可      |
| time      | '00:00:00'            | '-838:59:59.000000' ~ '838:59:59.000000'                    | time(0),time(6)           |
| datetime  | '0000-00-00 00:00:00' | '1000-01-01 00:00:00.000000' ~ '9999-12-31 23:59:59.499999' | datetime(0),datetime(6)   |
| timestamp | '0000-00-00 00:00:00' | '1970-01-01 00:00:00' ~ ''2038-01-19 03:14:07.499999''      | timestamp(0),timestamp(6) |
| year      | '0000'                |                                                             |                           |



## 三、准备
```sql
[root@127.0.0.1:8032 22:29:42(db1)]$ show global variables like 'explicit_defaults_for_timestamp';
+---------------------------------+-------+
| Variable_name                   | Value |
+---------------------------------+-------+
| explicit_defaults_for_timestamp | ON    |
+---------------------------------+-------+
1 row in set (0.00 sec)

[root@127.0.0.1:8032 21:55:42(db1)]$ show create table t2\G
*************************** 1. row ***************************
       Table: t2
Create Table: CREATE TABLE `t2` (
  `id` bigint unsigned NOT NULL AUTO_INCREMENT,
  `d` date NOT NULL,
  `t` time(6) NOT NULL,
  `dt` datetime(6) NOT NULL,
  `ts` timestamp(6) NOT NULL,
  `y` year NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)

[root@127.0.0.1:8032 21:55:45(db1)]$ insert into t2 values (null,'2024-05-31','21:21:21',now(),now(),'2024');
Query OK, 1 row affected (0.00 sec)

[root@127.0.0.1:8032 21:55:59(db1)]$ insert into t2 values (null,'2024-05-31','22:22:22',now(),now(),'2024');
Query OK, 1 row affected (0.01 sec)

[root@127.0.0.1:8032 21:56:04(db1)]$ select * from t2;
+----+------------+-----------------+----------------------------+----------------------------+------+
| id | d          | t               | dt                         | ts                         | y    |
+----+------------+-----------------+----------------------------+----------------------------+------+
|  1 | 2024-05-31 | 21:21:21.000000 | 2024-05-31 21:55:59.000000 | 2024-05-31 21:55:59.000000 | 2024 |
|  2 | 2024-05-31 | 22:22:22.000000 | 2024-05-31 21:56:04.000000 | 2024-05-31 21:56:04.000000 | 2024 |
+----+------------+-----------------+----------------------------+----------------------------+------+
2 rows in set (0.00 sec)

[root@127.0.0.1:8032 21:56:09(db1)]$


```


## 四、数据类型

explicit_defaults_for_timestamp


#### 1. date





#### 2. time

精度最高可达微秒，即小数点后6位数字，如果省略，则默认精度为0





#### 3. datetime

- current_timestamp,current_timestamp()
- localtime,localtime()
- localtimestamp,localtimestamp()
- now()

精度最高可达微秒，即小数点后6位数字，如果省略，则默认精度为0


自动更新：`dt datetime default current_timestamp on update current_timestamp`

对于未指定列值的插入行，自动初始化列将被设置为当前时间戳

当行中任何其他列的值从其当前值更改时，自动更新列会自动更新为当前时间戳。

如果所有其他列都设置为其当前值，则自动更新列保持不变。


#### 4. timestamp

精度最高可达微秒，即小数点后6位数字，如果省略，则默认精度为0

自动更新：`ts timestamp default current_timestamp on update current_timestamp`

对于未指定列值的插入行，自动初始化列将被设置为当前时间戳

当行中任何其他列的值从其当前值更改时，自动更新列会自动更新为当前时间戳。

如果所有其他列都设置为其当前值，则自动更新列保持不变。


#### 5. year











