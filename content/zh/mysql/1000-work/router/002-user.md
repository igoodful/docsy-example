---
title: 02. 用户管理
description: 仅以用户名作为用户的唯一标识，HOST部分不参与用户标识组成，HOST值将会被添加为该用户的白名单
date: 2017-01-05
weight: 200


---

{{< alert >}}
在MySQL中，以下用户可以使用不同的密码，但在金科MySQL集群中以下用户均为同一用户，使用相同的密码登录

user1@\'192.168.100.4\'

user1@\'192.168.100.5\'

user1@\'192.168.101.%\'

{{< /alert >}}


{{< alert color="secondary">}}


create user nb@\'%\' identified with mysql_native_password by \'123456\';

grant all on \*.\* to nb@\'%\';

dbscale show white list;

set password for user1='123456'

set password='123456';

{{< /alert >}}


```sql
mysql> create user nb@'%' identified with mysql_native_password by '123456';
Query OK, 0 rows affected (0.47 sec)

mysql> grant all on *.* to nb@'%';
Query OK, 0 rows affected (0.02 sec)

mysql> dbscale show white list;
+-----------+----+---------+
| user_name | ip | comment |
+-----------+----+---------+
| admin     | %  |         |
| nb        | %  |         |
| sb        | %  |         |
+-----------+----+---------+
3 rows in set (0.00 sec)

mysql> drop user sb;
Query OK, 0 rows affected (0.18 sec)

mysql> drop user nb@'%';
Query OK, 0 rows affected (0.21 sec)

mysql> dbscale show white list;
+-----------+----+---------+
| user_name | ip | comment |
+-----------+----+---------+
| admin     | %  |         |
+-----------+----+---------+
1 row in set (0.00 sec)

mysql> create user nb@'10.10.%' identified with mysql_native_password by '123456';
Query OK, 0 rows affected (0.08 sec)

mysql> dbscale show white list;
+-----------+---------+---------+
| user_name | ip      | comment |
+-----------+---------+---------+
| admin     | %       |         |
| nb        | 10.10.% |         |
+-----------+---------+---------+
2 rows in set (0.00 sec)

```



```sql
mysql> select * from white_user_info;
+------------------+----+---------+--------------------+
| user_name        | ip | comment | dbscale_cluster_id |
+------------------+----+---------+--------------------+
| admin            | %  |         |              13476 |
| dbscale_internal | %  |         |              13476 |
+------------------+----+---------+--------------------+
2 rows in set (0.00 sec)

```


## 添加白名单

```sql
dbscale dynamic add white to 'admin'@'%';
dbscale dynamic add white to 'dbscale_internal'@'%';
```

## 查看预编译语句个数

```sql

```

