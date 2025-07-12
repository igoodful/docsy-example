---
title: work
description: 万里开源
date: 2023-10-24
weight: 90000


---

{{< alert >}}
stop slave;
set session gtid_next='61b59ec4-6653-11ee-bfbf-fa163e55b859:1636884';
begin;commit;
set session gtid_next='automatic';
start slave;






use paasx
drop table if exists t1, t1f;
# 不会写binlog
create server fedlink foreign data wrapper mysql options (user 'regulations', host '172.17.134.70', database 'paasx', port 3333,password '123456');

mysql> select * from servers;select * from servers;
+-------------+---------------+-------+-------------+----------+------+--------+---------+-------+
| Server_name | Host          | Db    | Username    | Password | Port | Socket | Wrapper | Owner |
+-------------+---------------+-------+-------------+----------+------+--------+---------+-------+
| fedlink     | 172.17.134.70 | paasx | regulations | 123456 | 3333 |        | mysql   |       |
+-------------+---------------+-------+-------------+----------+------+--------+---------+-------+
1 row in set (0.00 sec)



create table t1 (id int);
create table t1f (id int) engine = federated connection= 'fedlink/t1';
{{< /alert >}}

