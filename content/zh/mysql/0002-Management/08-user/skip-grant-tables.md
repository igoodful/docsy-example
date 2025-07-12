---
title: skip-grant-tables
description: "ERROR 1290 (HY000): The MySQL server is running with the --skip-grant-tables option so it cannot execute this statement"
date: 2023-10-25
weight: 20000


---

{{<note>}}
<!---->
Server version: 8.0.26 MySQL Community Server - GPL



{{</note>}}



## 问题

`2024-04-11T10:27:01 [igoodful@48 /db/storage/guolicheng/mysql_3311] $ mysql -uroot -S ./tmp/mysql.sock`
```sql

mysql> alter user root@localhost identified by 'root';
ERROR 1290 (HY000): The MySQL server is running with the --skip-grant-tables option so it cannot execute this statement
mysql> select user,host from mysql.user;
+------------------+-----------+
| user             | host      |
+------------------+-----------+
| adm              | %         |
| adm_arm          | %         |
| admin            | %         |
| clone_user       | %         |
| dts              | %         |
| root             | %         |
| mysql.infoschema | localhost |
| mysql.session    | localhost |
| mysql.sys        | localhost |
| root             | localhost |
+------------------+-----------+
10 rows in set (0.01 sec)

mysql> alter user root@'%' identified by 'root';
ERROR 1290 (HY000): The MySQL server is running with the --skip-grant-tables option so it cannot execute this statement
mysql> grant all on *.* to admin@'%' with grant option;
ERROR 1290 (HY000): The MySQL server is running with the --skip-grant-tables option so it cannot execute this statement
mysql> flush privileges;
Query OK, 0 rows affected (0.01 sec)

mysql> grant all on *.* to admin@'%' with grant option;
Query OK, 0 rows affected (0.00 sec)

mysql> alter user root@'%' identified by 'root';
Query OK, 0 rows affected (0.00 sec)

```

## 解决

```sql
flush privileges;
```
















