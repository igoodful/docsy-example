---
title: Threads_connected
description: Threads_connected
date: 2023-10-25
weight: 20000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

**监控指标**: `mysql_global_status_threads_connected`

{{< /alert >}}

- `show global status like 'Threads_connected';`


## Threads_connected
当前处于连接状态的线程个数，等于show processlist减去一个event_scheduler线程

- `Daemon`的`event_scheduler`用户开启后，不算在Threads_connected中
- 每个`system user`用户的同步线程不会增加Threads_connected
- 每个`Binlog Dump GTID`线程（代表他的一个从库）会增加一个Threads_running
- 执行`show global status like 'Threads_connected';`时，自己本身也处于running，因此会加一


#### 1. 示例
```sql
[root@127.0.0.1:8032 11:05:14((none))]$ show global status like 'Threads_connected';
+-------------------+-------+
| Variable_name     | Value |
+-------------------+-------+
| Threads_connected | 2     |
+-------------------+-------+
1 row in set (0.00 sec)

[root@127.0.0.1:8032 11:05:44((none))]$ show processlist;
+--------+-----------------+-----------------+------+---------+-------+------------------------+------------------+
| Id     | User            | Host            | db   | Command | Time  | State                  | Info             |
+--------+-----------------+-----------------+------+---------+-------+------------------------+------------------+
| 166018 | event_scheduler | localhost       | NULL | Daemon  | 89796 | Waiting on empty queue | NULL             |
| 190377 | root            | 127.0.0.1:54790 | NULL | Query   |     0 | init                   | show processlist |
| 190402 | root            | 127.0.0.1:54974 | NULL | Sleep   |     8 |                        | NULL             |
+--------+-----------------+-----------------+------+---------+-------+------------------------+------------------+
3 rows in set (0.01 sec)

[root@127.0.0.1:8032 11:05:47((none))]$


```














