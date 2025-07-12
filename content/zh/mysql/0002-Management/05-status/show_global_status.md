---
title: show global status
description: show global status
date: 2023-10-25
weight: 20000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

{{< /alert >}}


### 累计慢查询个数：Slow_queries
| 监控项                       | SQL语句                                                 | PromQL                              |
|:-----------------------------|:--------------------------------------------------------|:------------------------------------|
| 数据节点慢SQL个数较多        | show global status like 'Slow_queries'                  | irate(slow_queries{}[5m]) > 20      |
| 数据节点活动会话数较多       | show global status like '%Threads_running%'             | threads_running{} > 10              |
| 数据节点当前行锁等待个数较多 | show global status like 'Innodb_row_lock_current_waits' | innodb_row_lock_current_waits{}> 20 |
|                              |                                                         |                                     |



select count(*) from performance_schema.events_transactions_current where state='active';

## 慢日志数量统计
```sql
mysql> show global status like 'Slow_queries';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| Slow_queries  | 6     |
+---------------+-------+
1 row in set (0.01 sec)

mysql> show global status like 'Threads_running';
+-----------------+-------+
| Variable_name   | Value |
+-----------------+-------+
| Threads_running | 3     |
+-----------------+-------+
1 row in set (0.00 sec)

mysql> show global status like 'Innodb_row_lock_current_waits';
+-------------------------------+-------+
| Variable_name                 | Value |
+-------------------------------+-------+
| Innodb_row_lock_current_waits | 0     |
+-------------------------------+-------+
1 row in set (0.01 sec)

mysql> select count(*) from performance_schema.events_transactions_current where state='active';
+----------+
| count(*) |
+----------+
|        2 |
+----------+
1 row in set (0.00 sec)


```

## 预编译相关语句数量统计
```sql
mysql> show global status like '%com_stm%';
+-------------------------+------------+
| Variable_name           | Value      |
+-------------------------+------------+
| Com_stmt_execute        | 0          |
| Com_stmt_close          | 58519      |
| Com_stmt_fetch          | 0          |
| Com_stmt_prepare        | 188564     |
| Com_stmt_reset          | 0          |
| Com_stmt_send_long_data | 0          |
| Com_stmt_reprepare      | 0          |
+-------------------------+------------+
7 rows in set (0.01 sec)
```









