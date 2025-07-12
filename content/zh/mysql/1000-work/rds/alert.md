---
title: 监控
description: 监控
date: 2023-10-23
weight: 20000



---

{{< alert >}}


{{< /alert >}}


## RDS 后台关闭某项告警指标

时间同步
```sql
update greatrds.alert_rule set status='down' where metric_name='greatdb_self_delta_time' and instance_uuid='044778f1-9f3a-4fed-8ddd-985f3ac610c7';

select metric_name,name,status from greatrds.alert_rule where metric_name='greatdb_self_delta_time';
```

## 监控promQL
`http://172.17.134.77:9092`


#### CPU使用百分比
- bash
```bash
free -h
```

- promQL
```c
100 - (sum by (instance, job, address) (irate(cpu_times_seconds_total{mode="idle"}[30s]) ) /sum by (instance, job, address)(irate(cpu_times_seconds_total{}[30s]) ) * 100)
```
#### 数据库进程正在占用的CPU

```c
mysql_process_info{field="cpu_percent"}
```

#### 内存使用百分比
- bash
```bash
free -h
```

- promQL
```c
100 - sum(mem_info{info="MemAvailable"}) by (instance, job, address)/sum(mem_info{info="MemTotal"}) by (instance, job, address) * 100
```

#### 数据库进程正在占用的内存百分比
- promQL
```c
mysql_process_info{field="memory_percent"}
```

#### 数据库进程正在占用的IO

- promQL
```c
irate(mysql_process_info{field=~"io_read|io_write"}[30s])/1024
```


#### 磁盘使用百分比

- promQL
```c
disk_used_info{field="percent"}
```


#### 数据目录使用量
- bash
```bash
du -sch /data/mysqldata/16315fff*
```

- promQL
```c
mysql_data_dir_size{instance=~"08cb3cd8-3eed-4771-a317-72ca3eeb8bf8", member_uuid=~"16315fff-04e5-4655-87fa-ffbe7981083b"}/1024/1024/1024
```

`{address="172.17.135.83", instance="08cb3cd8-3eed-4771-a317-72ca3eeb8bf8", job="mysql_node", member="datasource", member_uuid="16315fff-04e5-4655-87fa-ffbe7981083b", port="16315"}`


#### 节点当前活跃会话数
- SQL
```sql
show global status like 'Threads_running';
```

- promQL
```c
mysql_global_status{status="Threads_running"}
```

#### 大事务
目前大事务阈值如下：
- 10
- 60
- 180
- 600

- promQL
```c
mysql_long_transaction{time="60"}
```


#### QPS


- promQL
```c
rate(mysql_global_status{status="Questions"}[30s])
```






#### TPS

- promQL
```c
sum by (address,instance,job,member,port,member_uuid)(irate(mysql_global_status{status=~"Com_xa_commit|Com_xa_rollback|Com_commit|Com_rollback"}[30s]))
```






#### 数据节点运行时长


- SQL
```sql
mysql> show global status like 'Uptime';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| Uptime        | 84389 |
+---------------+-------+
1 row in set (0.00 sec)

mysql> status;
--------------
mysql  Ver 8.0.23 for Linux on aarch64 (Source distribution)

Connection id:		168125
Current database:	information_schema
Current user:		dbscale_internal@172.17.135.83
SSL:			Not in use
Current pager:		stdout
Using outfile:		''
Using delimiter:	;
Server version:		8.0.26 MySQL (GPL), Release 'GA', Revision 'e5991c4950c'
Protocol version:	10
Connection:		127.0.0.1 via TCP/IP
Server characterset:	utf8mb4
Db     characterset:	utf8mb3
Client characterset:	utf8mb4
Conn.  characterset:	utf8mb4
TCP port:		16310
Binary data as:		Hexadecimal
Uptime:			23 hours 26 min 37 sec

Threads: 18  Questions: 2515651  Slow queries: 48  Opens: 637  Flush tables: 4  Open tables: 446  Queries per second avg: 29.807
--------------

```

- promQL
```c
mysql_global_status{status="Uptime"}
```



#### 慢查询频率

- promQL
```c
mysql_global_status{status="Slow_queries"}

increase(mysql_global_status{status="Slow_queries"}[1m])
```










