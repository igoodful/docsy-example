---
title: 01. 参数管理
description: parameter
date: 2023-10-31
weight: 100


---

{{<caution>}}
<!---->
```sql
dbscale show session | instance | global options like '%option_name%';

dbscale set global 'log-level' = 'DEBUG';

dbscale flush config to file 'file.cfg';
```
{{</caution>}}




{{<alert title="router配置持久化" color="secondary">}}
```sql
dbscale flush config to file 'file.cfg';
```
{{</alert>}}

## 一、管理router参数

#### 查看router参数
```sql
mysql> dbscale show options like '%max-replication-delay%';
+----------------------------------------+----------+
| option_name                            | value    |
+----------------------------------------+----------+
| max-replication-delay                  | 30       |
| max-replication-delay-potential-master | 10000000 |
+----------------------------------------+----------+
2 rows in set (0.00 sec)

```
{{<alert>}}
<!---->
**说明**：

若执行dbscale show dataservers;的时候没有在dbscale show dataservers的时候体现出延迟来

实际情况是：后端MySQL主节点正常，而从节点全部有几千秒的延迟（IO/SQL线程均为YES，senconds behind 延迟几千秒）

此时与max-replication-delay配置有关，默认30秒，比如你将此值配置5000s，那么主从延迟小于5000s时，无法在dbscale show dataservers的时候体现出延迟来
{{</alert>}}

#### 修改router参数
> router开启debug
```sql
mysql> dbscale show options like 'log-level';
+-------------+-------+
| option_name | value |
+-------------+-------+
| log-level   | INFO  |
+-------------+-------+
1 row in set (0.01 sec)

mysql> dbscale set global 'log-level' = 'DEBUG';
Query OK, 0 rows affected (0.04 sec)

mysql> dbscale show options like 'log-level';
+-------------+-------+
| option_name | value |
+-------------+-------+
| log-level   | DEBUG |
+-------------+-------+
1 row in set (0.01 sec)
```


#### session 参数

```sql
[dbscale_internal@172.17.134.76:16310 16:05:40(db2)]$ dbscale set session 'max-connect-by-result-num'=1000000;
Query OK, 0 rows affected (0.01 sec)

[dbscale_internal@172.17.134.76:16310 16:07:29(db2)]$ dbscale show options like '%max-connect-by%';
+---------------------------+---------+
| option_name               | value   |
+---------------------------+---------+
| max-connect-by-result-num | 1000000 |
+---------------------------+---------+
1 row in set (0.01 sec)

[dbscale_internal@172.17.134.76:16310 16:07:50(db2)]$

```




#### 持久化router配置
```sql
dbscale flush config to file;

dbscale flush all config to file;

-- 持久化到dbscale的默认路径：/data/app/dbscale/router.cfg
mysql> dbscale flush all config to file './router.cfg';
Query OK, 0 rows affected (0.00 sec)

-- 持久化到：/tmp/
mysql> dbscale flush all config to file '/tmp/router.cfg';
Query OK, 0 rows affected (0.00 sec)
```









## 二、管理MySQL参数

> 金科MySQL集群的set语句不支持直接使用set global, 需要使用金科MySQL集群集群的替代实现


#### 1. 动态修改参数
> 所有节点生效

```sql
dbscale backend server execute "set global max_binlog_cache_size=1073741824";

dbscale backend server execute "set global sync_binlog=0;"
```
再去数据节点修改配置文件内容进行持久化


#### 2. 直接持久化
> 所有节点生效

只需一句SQL即可：
```sql
dbscale backend server execute "set persist max_binlog_cache_size=1073741824";
```

查看持久化后的文件：`cat /data/mysqldata/16315fff-1f47-49d9-92a9-e3854a927de8/dbdata/mysqld-auto.cnf`
```json
{ "Version" : 1 , "mysql_server" : { "max_binlog_cache_size" : { "Value" : "1073741824" , "Metadata" : { "Timestamp" : 1711959644764779 , "User" : "dbscale_internal" , "Host" : "" } } } }
```



#### 3. 指定节点执行
- 指定节点执行
```sql
dbscale execute on  dataserver normal_0_0 "set global rpl_semi_sync_master_enabled=OFF;"
```






