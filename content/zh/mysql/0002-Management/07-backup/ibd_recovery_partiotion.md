---
title: 基于分区表ibd恢复数据
description: ibd_recovery_partiotion
date: 2023-10-12
weight: 20000


---

{{<alert color="danger" title="注意" >}}

1. 数据库版本相同
2. `innodb_file_per_table`必须启用


{{</alert>}}



## 一、在新实例上，创建库表

> 表名称和结构必须相同

```sql
[root@127.0.0.1:8032 20:12:46((none))]$ create database db8;
Query OK, 1 row affected (0.23 sec)

[root@127.0.0.1:8032 20:13:01((none))]$ use db8;
Database changed
[root@127.0.0.1:8032 20:13:10(db8)]$  create table t1 (
    ->          actor_id smallint unsigned not null auto_increment,
    ->          first_name varchar(45) not null,
    ->          last_name varchar(45) not null,
    ->          last_update timestamp not null default current_timestamp on update current_timestamp,
    ->          primary key  (actor_id),
    ->          key idx_actor_last_name (last_name)
    ->        )engine=innodb default charset=utf8mb4;
Query OK, 0 rows affected (0.51 sec)

[root@127.0.0.1:8032 20:14:06(db8)]$ show create table t1\G
*************************** 1. row ***************************
       Table: t1
Create Table: CREATE TABLE `t1` (
  `actor_id` smallint unsigned NOT NULL AUTO_INCREMENT,
  `first_name` varchar(45) NOT NULL,
  `last_name` varchar(45) NOT NULL,
  `last_update` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (`actor_id`),
  KEY `idx_actor_last_name` (`last_name`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_bin
1 row in set (0.00 sec)


```


## 二、在新实例上，弃新表的表空间

> 这会导致新表的ibd文件删除

```sql
[root@127.0.0.1:8032 20:14:12(db8)]$ alter table db8.t1 discard tablespace;
Query OK, 0 rows affected (0.10 sec)

[root@127.0.0.1:8032 20:14:44(db8)]$

```



## 三、拷贝ibd文件到新实例上的新表所在位置

> 确保该.ibd文件具有必要的文件权限。

```bash
scp t1.ibd mysql@10.10.10.10:/data/mysql_3306/data/db8/


```



## 四、在新实例上，导入.ibd表空间

> 将发出警告，指示InnoDB正在尝试导入未经架构验证的文件。


```sql
alter table db8.t1 import tablespace;

show warnings;

```


## 五、在新实例上，确认恢复是否已成功

```sql
select count(*) from db8.t1;

```



















