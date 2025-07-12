---
title: mysql_random_data_load
description: mysql_random_data_load
date: 2023-10-13
weight: 20000


---

{{< alert >}}
-[mysql_random_data_load](https://github.com/Percona-Lab/mysql_random_data_load)
{{< /alert >}}

{{< alert color="secondary" >}}


{{< /alert >}}


```sql
mysql> show create table t9\G
*************************** 1. row ***************************
       Table: t9
Create Table: CREATE TABLE `t9` (
  `id` int NOT NULL AUTO_INCREMENT,
  `k` int NOT NULL DEFAULT '0',
  `c` char(120) NOT NULL DEFAULT '',
  `pad` char(60) NOT NULL DEFAULT '',
  `name` varchar(256) NOT NULL,
  `d` datetime NOT NULL DEFAULT '2024-04-04 00:00:00',
  PRIMARY KEY (`id`),
  UNIQUE KEY `idx_name` (`name`),
  KEY `k_1` (`k`)
) ENGINE=InnoDB AUTO_INCREMENT=10129532 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.02 sec)


```

```bash
./mysql_random_data_load apple t9  10000000 --user=admin --password='123456' --host=172.17.135.83 --port=16310 --debug
```







