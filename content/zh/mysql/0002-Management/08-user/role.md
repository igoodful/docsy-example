---
title: role
description: 角色
date: 2023-10-25
weight: 20000

---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{<note>}}
<!---->
MySQL 8.0才引入了角色这个概念：

1. 角色就是一组针对各种数据库权限的集合。把一个角色分配给一个用户，那这个用户就拥有了这个角色包含的所有权限。

2. 用户与角色两者是多对多的关系。一个角色可以分配给多个用户，另外一个用户也可以拥有多个角色，

3. MySQL目前还没有系统预分配的角色。

[角色](https://opensource.actionsky.com/20200318-mysql/)

{{</note>}}




`2024-04-11T10:27:01 [igoodful@48 /db/storage/guolicheng/mysql_3311] $ mysql -uroot -S ./tmp/mysql.sock`
```sql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 7
Server version: 8.0.26 MySQL Community Server - GPL

Copyright (c) 2000, 2021, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

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

## authentication_policy

```sql
mysql> show global variables like '%auth%';
+-------------------------------+-----------------------+
| Variable_name                 | Value                 |
+-------------------------------+-----------------------+
| authentication_policy         | *,,                   |
| default_authentication_plugin | mysql_native_password |
+-------------------------------+-----------------------+
2 rows in set (0.00 sec)

```

## default_authentication_plugin


## auth_socket

```sql
mysql> install plugin auth_socket soname 'auth_socket.so';
Query OK, 0 rows affected (0.02 sec)

```








