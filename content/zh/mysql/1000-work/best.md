---
title: 常见mysql运维操作
description: 常见mysql运维操作
date: 2025-05-24
weight: 70000
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

{{< /alert >}}




{{< alert title="OS" color="secondary">}}

#### 一个集群的zookeeper、MySQL、router、备份服务器

- 所有节点的操作系统版本要求一致；

- 所有节点关闭CPU的超线程功能；

- 所有节点关闭透明大页；

- 所有节点关闭防火墙及SELINUX;

- 操作系统与数据库单独划分RAID;

- 文件系统采用XFS，且4K对齐;

- 配备2块万兆光纤网卡，并进行绑定，网卡绑定采用主备单活（active、standby）的模式，提供高可用性；

- 网络延时小于5毫秒;

{{< /alert >}}



## 搭建主从

```sql
stop slave;
reset slave;
reset master;
set @@global.gtid_purged='6aadd490-f0b7-11e9-bd17-246e96923e78:1,772c2f87-f0bf-11e9-87a6-246e9698b380:1-44400';
change master to master_host='10.10.10.10',master_port=3306,master_user='mysqlsync',master_password='123456',master_auto_position=1;
start slave;

```


## 补齐gtid

```sql
set next.gtid='6aadd490-f0b7-11e9-bd17-246e96923e78:123456';
begin;commit;

```


## 清理二进制日志


```bash
# 1. purge binary logs before
purge binary logs before '2025-05-24 00:00:00';

# 2. purge binary logs to
purge binary logs to 'mysql-bin.000002';

# 3. 删除文件
rm -f mysql-bin.000009
vim mysql-bin.index # 删除其中的./mysql-bin.000009

```




## 误删数据恢复处理


#### 1. 传统复制
```sql
stop slave;

reset slave all;

reset master;

change master to master_host='10.10.10.10',master_port=3306,master_user='mysqlsync',master_password='123456', master_log_file='mysql-bin.009044',master_log_pos=517587984;

start slave io_thread;

start slave sql_thread until master_log_file='mysql-bin.009161',master_log_pos=224619836;


```


#### 2. gtid复制
```sql
stop slave;

reset slave all;

reset master;

set @@global.gtid_purged='1751c709-9e64-11ea-855e-e4434b034ad8:1-4301373';

change master to master_host='10.10.10.10',master_port=3306,master_user='sync',master_password='123456',master_auto_position=1;

start slave io_thread;

start slave sql_thread until sql_before_gtids='1751c709-9e64-11ea-855e-e4434b034ad8:4315229';


```


## 恢复单个ibd文件

- 前提是数据库实例开启了每张表都是一个独立表空间，即查看这个全局变量：`innodb_file_per_table=1`

#### 1. 正常节点
```sql
show create table apple.ipad \G;

select count(*)  from apple.ipad ;


```

#### 2. 异常节点
```sql
create  database apple;

use apple;

create table ipad (id bigint(20) NOT NULL AUTO_INCREMENT,name varchar(255) NOT NULL,address varchar(255) NOT NULL,blob_name blob,PRIMARY KEY (id),KEY idx_name (name)) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8mb4;

alter table ipad discard tablespace;

```

#### 3. 异常节点
```bash
scp  work@xxx:/home/work/2021/data/apple/ipad.ibd   /home/work/mysql_3306/data/apple/

chown -R work:work   /home/work/mysql_3306

```

#### 4. 异常节点
```sql
alter table apple.ipad  import tablespace;

```

## gdb在线修改mysql只读变量

```bash
ps aux|grep mysqld

gdb  -p   ${pidof mysqld} -ex "set opt_log_slave_updates=1" -batch


```





