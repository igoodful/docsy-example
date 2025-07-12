---
title: init_connect
description: init_connect
date: 2025-02-12
weight: 20000
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;

$ \rightleftharpoons $

{{< alert >}}

- https://bugs.mysql.com/bug.php?id=118529

{{< /alert >}}


## init_connect


```viz-dot
digraph "init_connect" {
        rankdir = TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
        ];

        "init_connect" -> "set names utf8"[label="set names utf8"];
        "init_connect" -> "记录用户登录信息";
}
```



## 使用场景


#### 记录不具有super权的用户登录信息

- 前提是不能具有super权限，否则将不会执行init_connect变量中的sql语句
```sql
create user  work@'%' identified with mysql_native_password by 'work';
grant create ,drop, select ,insert, update on *.* to work@'%';

create database auditdb default character set utf8mb4 collate utf8mb4_bin;
create table accesslog (
  id bigint unsigned not null auto_increment,
  connectionid bigint unsigned default 0,
  connuser varchar(30) not null default '',
  matchuser varchar(30) not null default '',
  logintime datetime default null,
  primary key (id)
) engine=innodb auto_increment=1 default charset=utf8mb4 collate=utf8mb4_bin;

set global init_connect='insert into auditdb.accesslog(connectionID,ConnUser,MatchUser,LoginTime) values(connection_id(),user(),current_user(),now());';

```

#### 设置连接的默认字符集

```sql
set names utf8


```

#### sql_safe_updates


```sql
[root@127.0.0.1:8032 12:00:15((none))]$  SET GLOBAL init_connect="SET sql_safe_updates=ON";
Query OK, 0 rows affected (0.00 sec)

[root@127.0.0.1:8032 12:00:04((none))]$ show global variables like 'sql_safe_updates';
+------------------+-------+
| Variable_name    | Value |
+------------------+-------+
| sql_safe_updates | OFF   |
+------------------+-------+
1 row in set (0.22 sec)

[root@127.0.0.1:8032 12:02:49((none))]$ show global variables like 'init_connect';
+---------------+-------------------------+
| Variable_name | Value                   |
+---------------+-------------------------+
| init_connect  | SET sql_safe_updates=ON |
+---------------+-------------------------+
1 row in set (0.01 sec)



```



