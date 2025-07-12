---
title: setup_instruments
description: setup_instruments
date: 2023-10-26
weight: 20000


---

{{< alert >}}
- **instruments**: 生产者，用于采集mysql中各种各样的操作产生的事件信息，对应配置表中的配置项我们可以称为监控采集配置项。
- **​consumers**：消费者，对应的消费者表用于存储来自instruments采集的数据，对应配置表中的配置项我们可以称为消费存储配置项。

- [setup_instruments](https://www.cnblogs.com/Courage129/p/14188422.html)
- [setup_instruments](https://www.percona.com/blog/deep-dive-into-mysqls-performance-schema/)

{{< /alert >}}




```sql
mysql> show create table threads\G
*************************** 1. row ***************************
mysql> show create table  setup_instruments\G
*************************** 1. row ***************************
       Table: setup_instruments
Create Table: CREATE TABLE `setup_instruments` (
  `NAME` varchar(128) NOT NULL,
  `ENABLED` enum('YES','NO') NOT NULL,
  `TIMED` enum('YES','NO') DEFAULT NULL,
  `PROPERTIES` set('singleton','progress','user','global_statistics','mutable','controlled_by_default') NOT NULL,
  `FLAGS` set('controlled') DEFAULT NULL,
  `VOLATILITY` int NOT NULL,
  `DOCUMENTATION` longtext,
  PRIMARY KEY (`NAME`)
) ENGINE=PERFORMANCE_SCHEMA DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)

```



```sql
[admin@127.0.0.1:3306 18:47:17((none))]$select distinct(substring_index(name,'/',1)) from performance_schema.setup_instruments;
+-------------------------------+
| (substring_index(name,'/',1)) |
+-------------------------------+
| wait                          |
| idle                          |
| stage                         |
| statement                     |
| transaction                   |
| memory                        |
| error                         |
+-------------------------------+
7 rows in set (0.08 sec)


[admin@127.0.0.1:3306 18:47:19((none))]$select distinct(substring_index(name,'/',1)) as instrument_name,count(1) from performance_schema.setup_instruments group by instrument_name;
+-----------------+----------+
| instrument_name | count(1) |
+-----------------+----------+
| wait            |      370 |
| idle            |        1 |
| stage           |      134 |
| statement       |      213 |
| transaction     |        1 |
| memory          |      499 |
| error           |        1 |
+-----------------+----------+
7 rows in set (0.00 sec)


[admin@127.0.0.1:3306 18:50:25(performance_schema)]$ select count(*) from performance_schema.setup_instruments where ENABLED='YES';
+----------+
| count(*) |
+----------+
|     1219 |
+----------+
1 row in set (0.01 sec)
```


update performance_schema.setup_consumers set ENABLED='YES' where NAME='events_stages_current';











