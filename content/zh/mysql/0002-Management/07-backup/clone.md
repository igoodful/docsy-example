---
title: clone
description: 克隆
date: 2023-10-12
weight: 20000


---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;

- 远程克隆命令

```sql
clone instance from 'user'@'host':port identified by 'password'
[data directory [=] 'clone_dir']
[require [no] ssl];

```


{{<alert color="danger" title="注意" >}}

克隆插件有一些限制，这里将对它们进行描述。在我看来，社区将面临两个主要限制。

1. 首先，它只能克隆 InnoDB 引擎的表。这意味着 MyISAM 和 CSV 引擎存储的任何表，都将被克隆为空表。
2. 关于 DDL，包括 TRUNCATE TABLE，这在克隆操作期间是不允许的。但是，允许并发 DML。
   - 如果 DDL 正在运行，则克隆操作将等待锁定：
   - 否则，如果克隆操作正在运行，则 DDL 将等待锁定：


[ERROR 1026 (HY000): Error writing file ‘./#innodb_redo.#clone/#ib_redo0’ (errno: 22 - Invalid argument)](https://bugs.mysql.com/bug.php?id=113548)



{{</alert>}}


## 一、安装

#### 1. 配置文件中配置
```ini
[mysqld]

plugin-load-add=mysql_clone.so
```

#### 2. 配置文件中配置
```sql
install plugin clone soname 'mysql_clone.so';


```




## 二、使用


#### 1. 准备

| 角色      | ip          | port |
| :-------- | :---------- | :--- |
| donor     | 10.10.10.10 | 3306 |
| recipient | 10.10.10.20 | 3306 |


#### 2. donor
- 实例为：`10.10.10.10:3306`

```sql
install plugin clone soname 'mysql_clone.so';

select plugin_name, plugin_status from information_schema.plugins where plugin_name like 'clone';

show plugins;

create user donor_user@'%' identified with mysql_native_password  by 'donor_password';

grant backup_admin on *.* to 'donor_user'@'%';


```
- `'donor_user'@'%'`用户至少要有`backup_admin`权限


#### 3. recipient
- 实例为：`10.10.10.20:3306`

```sql
install plugin clone soname 'mysql_clone.so';

select plugin_name, plugin_status from information_schema.plugins where plugin_name like 'clone';

show plugins;

create user 'recipient_user'@'%' identified by 'recipient_password';

grant clone_admin on *.* to 'recipient_user'@'%';

set global clone_valid_donor_list = '10.10.10.10:3306';

show variables like 'clone_valid_donor_list';

set global log_error_verbosity=3;

clone instance from 'donor_user'@10.10.10.10:3306 identified by 'donor_password';


```


#### 4. clone_status clone_progress

```sql
mysql> select * from performance_schema.clone_status;
+------+------+-------------+-------------------------+----------+-----------------+----------------+----------+---------------+-------------+-----------------+---------------+
| ID   | PID  | STATE       | BEGIN_TIME              | END_TIME | SOURCE          | DESTINATION    | ERROR_NO | ERROR_MESSAGE | BINLOG_FILE | BINLOG_POSITION | GTID_EXECUTED |
+------+------+-------------+-------------------------+----------+-----------------+----------------+----------+---------------+-------------+-----------------+---------------+
|    1 |  599 | In Progress | 2023-12-11 09:29:07.612 | NULL     | 10.5.43.1:16315 | LOCAL INSTANCE |        0 |               |             |               0 |               |
+------+------+-------------+-------------------------+----------+-----------------+----------------+----------+---------------+-------------+-----------------+---------------+
1 row in set (0.00 sec)

mysql> select * from performance_schema.clone_progress;
+------+-----------+-------------+----------------------------+----------------------------+---------+--------------+--------------+--------------+------------+---------------+
| ID   | STAGE     | STATE       | BEGIN_TIME                 | END_TIME                   | THREADS | ESTIMATE     | DATA         | NETWORK      | DATA_SPEED | NETWORK_SPEED |
+------+-----------+-------------+----------------------------+----------------------------+---------+--------------+--------------+--------------+------------+---------------+
|    1 | DROP DATA | Completed   | 2023-12-11 09:29:08.658200 | 2023-12-11 09:29:27.656438 |       1 |            0 |            0 |            0 |          0 |             0 |
|    1 | FILE COPY | In Progress | 2023-12-11 09:29:27.656691 | NULL                       |       4 | 200014069239 | 129055768055 | 129062956539 |  535776382 |     535804996 |
|    1 | PAGE COPY | Not Started | NULL                       | NULL                       |       0 |            0 |            0 |            0 |          0 |             0 |
|    1 | REDO COPY | Not Started | NULL                       | NULL                       |       0 |            0 |            0 |            0 |          0 |             0 |
|    1 | FILE SYNC | Not Started | NULL                       | NULL                       |       0 |            0 |            0 |            0 |          0 |             0 |
|    1 | RESTART   | Not Started | NULL                       | NULL                       |       0 |            0 |            0 |            0 |          0 |             0 |
|    1 | RECOVERY  | Not Started | NULL                       | NULL                       |       0 |            0 |            0 |            0 |          0 |             0 |
+------+-----------+-------------+----------------------------+----------------------------+---------+--------------+--------------+--------------+------------+---------------+
7 rows in set (0.00 sec)



--- 过一段时间后

mysql> select * from performance_schema.clone_status\G
*************************** 1. row ***************************
             ID: 1
            PID: 0
          STATE: Completed
     BEGIN_TIME: 2023-12-11 09:29:07.612
       END_TIME: 2023-12-11 09:35:55.931
         SOURCE: 10.5.43.1:16315
    DESTINATION: LOCAL INSTANCE
       ERROR_NO: 0
  ERROR_MESSAGE:
    BINLOG_FILE: binlog.000433
BINLOG_POSITION: 161935935
  GTID_EXECUTED: fbd5eca7-6652-11ee-8012-fa163e62dbcc:1-191076,
fbfcff3a-6652-11ee-a882-fa163eb474e5:1,
fc442213-6652-11ee-80ad-fa163eb537f3:1-10412833
1 row in set (0.00 sec)

mysql> select * from performance_schema.clone_progress;
+------+-----------+-----------+----------------------------+----------------------------+---------+--------------+--------------+--------------+------------+---------------+
| ID   | STAGE     | STATE     | BEGIN_TIME                 | END_TIME                   | THREADS | ESTIMATE     | DATA         | NETWORK      | DATA_SPEED | NETWORK_SPEED |
+------+-----------+-----------+----------------------------+----------------------------+---------+--------------+--------------+--------------+------------+---------------+
|    1 | DROP DATA | Completed | 2023-12-11 09:29:08.658200 | 2023-12-11 09:29:27.656438 |       1 |            0 |            0 |            0 |          0 |             0 |
|    1 | FILE COPY | Completed | 2023-12-11 09:29:27.656691 | 2023-12-11 09:35:36.577915 |       4 | 200014069239 | 200014069239 | 200025053715 |          0 |             0 |
|    1 | PAGE COPY | Completed | 2023-12-11 09:35:36.578318 | 2023-12-11 09:35:36.848773 |       4 |     48365568 |     48365568 |     48531273 |          0 |             0 |
|    1 | REDO COPY | Completed | 2023-12-11 09:35:36.849194 | 2023-12-11 09:35:37.105487 |       4 |    293455360 |    293455360 |    293471870 |          0 |             0 |
|    1 | FILE SYNC | Completed | 2023-12-11 09:35:37.105857 | 2023-12-11 09:35:41.092822 |       4 |            0 |            0 |            0 |          0 |             0 |
|    1 | RESTART   | Completed | 2023-12-11 09:35:41.092822 | 2023-12-11 09:35:46.257718 |       0 |            0 |            0 |            0 |          0 |             0 |
|    1 | RECOVERY  | Completed | 2023-12-11 09:35:46.257718 | 2023-12-11 09:35:55.930670 |       0 |            0 |            0 |            0 |          0 |             0 |
+------+-----------+-----------+----------------------------+----------------------------+---------+--------------+--------------+--------------+------------+---------------+
7 rows in set (0.00 sec)

```


#### 5. 搭建主从
```sql
change master to master_host='10.10.10.10',master_port=3306,master_user='repl',master_password='repl',master_auto_position=1;

```



## 三、远程克隆限制



| 编号 | 约束                                                                       |
| :--- | :------------------------------------------------------------------------- |
| 1    | 克隆插件在供方和接收方均处于活动状态                                       |
| 2    | donor用户权限至少`BACKUP_ADMIN`，recipient用户权限至少`CLONE_ADMIN`        |
| 3    | donor和recipient的mysql版本至少8.0.17，且版本相同                          |
| 4    | donor和recipient的实例所在操作系统、位数、架构必须相同                     |
| 5    | donor和recipient的服务器字符集和排序规则必须相同                           |
| 6    | donor和recipient的`innodb_page_size`、`innodb_data_file_path`必须相同      |
| 7    | 无论是Donor，还是Recipient，同一时间，一次只允许一个克隆操作               |
| 8    | recipient的`clone_valid_donor_list`系统变量必须包含donor实例的ip和port地址 |
| 9    | donor和recipient的`max_allowed_packet`必须大于1M                           |
| 10   | donor和recipient上均需要获取备份锁。备份锁和DDL互斥。                      |
| 11   | 不会拷贝Donor的配置参数                                                    |
| 12   | 不会拷贝Donor的Binlog                                                      |
| 13   | 只会拷贝InnoDB表的数据，对于其它存储引擎的表，只会拷贝表结构               |
| 14   | 不允许通过MySQL Router连接Donor实例                                        |
| 15   | 指定的Donor端口不能为X Protocol端口                                        |
| 16   | Recipient必须有足够的磁盘空间存储克隆数据                                  |


- 克隆期间，会阻塞DDL。同样，DDL也会阻塞克隆命令的执行。不过从MySQL 8.0.27开始，克隆命令不会阻塞Donor上的DDL。
- GTID下建立复制，Clone Plugin无需额外执行SET GLOBAL GTID_PURGED操作


## 四、本地克隆


```sql
[admin@127.0.0.1:16315 16:18:54((none))]$ select plugin_name, plugin_status from information_schema.plugins where plugin_name like 'clone';
+-------------+---------------+
| plugin_name | plugin_status |
+-------------+---------------+
| clone       | ACTIVE        |
+-------------+---------------+
1 row in set (0.01 sec)

[admin@127.0.0.1:16315 16:19:06((none))]$ clone local data directory='/home/mysql/mysql_3306';
Query OK, 0 rows affected (3.75 sec)

[admin@127.0.0.1:16315 16:19:33((none))]$

```


| 编号 | 约束                                                   |
| :--- | :----------------------------------------------------- |
| 1    | `directory='/home/mysql/mysql_3306'`路径必须是绝对路径 |
| 2    | `/home/mysql/`必须存在，且mysql实例有写入权限          |
| 3    | `mysql_3306`路径必须不存在                             |
| 4    | 相对于Xtrabackup，Clone Plugin无需Prepare阶段          |
|      |                                                        |
|      |                                                        |














