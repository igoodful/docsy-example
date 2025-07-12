---
title: 监控
description: monitor
date: 2023-10-30
weight: 20000


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
8.0.32


{{< /alert >}}





## 一、进程资源消耗监控

为什么需要进程的资源监控？

1. 因为只有系统资源监控，那么出现系统资源告警时，同时每个数据库相关服务的组件监控看着没有任何问题，此时你连哪个组件造成资源告警的都无法确定，甚至可能是其他服务造成都有可能，但是你没有进程相关监控，那么无从下手

2. 需要监控mysqld、router等数据库相关组件的内存、cpu消耗，出问题，出问题时不一定是监控中的组件问题，但至少可以排除组件问题



















## 整体情况
```viz-dot
digraph "monitor" {
        rankdir=LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "monitor" -> "SELECT @@version";
        "monitor" -> "SHOW ENGINE INNODB STATUS";
        "monitor" -> "SHOW ENGINE TOKUDB STATUS";
        "monitor" -> "SHOW GLOBAL STATUS";
        "monitor" -> "SHOW GLOBAL VARIABLES";
        "monitor" -> "information_schema.processlist";
        "monitor" -> "information_schema.TABLE_STATISTICS";
        "monitor" -> "SHOW ALL SLAVES STATUS \n SHOW SLAVE STATUS \n SHOW REPLICA STATUS \n ";
        "monitor" -> "performance_schema.memory_summary_global_by_event_name";
        "monitor" -> "performance_schema.replication_applier_status_by_worker";
        "monitor" -> "monitor";
        "monitor" -> "monitor";
        "monitor" -> "monitor";
        "monitor" -> "monitor";
        "monitor" -> "monitor";
        "monitor" -> "monitor";
        "monitor" -> "monitor";
        "monitor" -> "monitor";


}
```




#### mysql_global_status_threads_running

```sql
[root@127.0.0.1:8032 13:42:55(apple)]$ show global status like '%running%';
+-----------------+-------+
| Variable_name   | Value |
+-----------------+-------+
| Threads_running | 2     |
+-----------------+-------+
1 row in set (0.01 sec)

```







#### mysql_global_status_aborted_connects

```sql
[root@127.0.0.1:8032 13:53:31(apple)]$ show global status like 'Aborted_connects';
+------------------+-------+
| Variable_name    | Value |
+------------------+-------+
| Aborted_connects | 4     |
+------------------+-------+
1 row in set (0.00 sec)

```

表示尝试连接到mysql服务器的失败次数，可以结合host_cache来分析问题，引起的原因：
  1. 密码错误
  2. 没有权限，包括库表等权限变更后无权限
  3. 连接信息不对
  4. 连接超时



###### A. 密码错误
```bash
2024-05-20T13:53:11 [igoodfu@48] $ mysql -uroot -p45455 -h127.0.0.1 -P8032
mysql: [Warning] Using a password on the command line interface can be insecure.
ERROR 1045 (28000): Access denied for user 'root'@'127.0.0.1' (using password: YES)
2024-05-20T13:53:24 [igoodfu@48] $ mysql -uroot -p -h127.0.0.1 -P8032
Enter password:
ERROR 1045 (28000): Access denied for user 'root'@'127.0.0.1' (using password: YES)

```

###### B. 没有权限
```bash
2024-05-20T13:57:42 [igoodfu@48 ] $ mysql -uddddd -p -h127.0.0.1 -P8032
Enter password:
ERROR 2061 (HY000): Authentication plugin 'caching_sha2_password' reported error: Authentication requires secure connection.
2024-05-20T13:57:56 [igoodfu@48 ] $
```

###### C. 连接信息不对
```bash
2024-05-20T13:59:38 [igoodfu@48 ] $ curl 127.0.0.1:8032
8.0.32¦8Yg7Nÿ𽃿.y012=<yZ4mysql_native_password!ÿ#08S01Got packets out of order2024-05-20T13:59:50

```

###### D. 连接超时
```sql
[root@127.0.0.1:8032 14:14:19((none))]$ show global variables like 'connect_timeout';
+-----------------+-------+
| Variable_name   | Value |
+-----------------+-------+
| connect_timeout | 10    |
+-----------------+-------+
1 row in set (0.00 sec)

[root@127.0.0.1:8032 14:14:32((none))]$

```
默认是10s，基本上，除非网络环境极端不好，一般不会超时。



#### mysql_global_status_aborted_clients

```sql
[root@127.0.0.1:8032 14:06:41(apple)]$ show global status like 'Aborted_clients';
+-----------------+-------+
| Variable_name   | Value |
+-----------------+-------+
| Aborted_clients | 6     |
+-----------------+-------+
1 row in set (0.00 sec)

```

表示客户端没有正确的关闭连接，而被终止的连接数，引起的原因
  1. 客户端程序退出之前未调用mysql_close()来关闭mysql连接
  2. 客户端的休眠时间超过了mysql系统变量wait_timeout和interactive_timeout的值，导致连接被mysql进程终止
  3. 客户端程序在数据传输过程中突然结束


###### A. 未调用mysql_close()来关闭mysql连接


###### B. 客户端的休眠超时
```bash
[root@127.0.0.1:8032 14:10:16(apple)]$ set global interactive_timeout=5;
Query OK, 0 rows affected (0.00 sec)

[root@127.0.0.1:8032 14:10:21(apple)]$ set global wait_timeout=5;
Query OK, 0 rows affected (0.00 sec)

[root@127.0.0.1:8032 14:10:29(apple)]$

```


```sql
[root@127.0.0.1:8032 14:06:11((none))]$ show global status like 'Aborted_connects';
ERROR 4031 (HY000): The client was disconnected by the server because of inactivity. See wait_timeout and interactive_timeout for configuring this behavior.
No connection. Trying to reconnect...
Connection id:    968
Current database: *** NONE ***

+------------------+-------+
| Variable_name    | Value |
+------------------+-------+
| Aborted_connects | 9     |
+------------------+-------+
1 row in set (0.00 sec)

```


###### C. 客户端程序在数据传输过程中突然结束

  - 模拟一个大表的查询
```sql
[root@127.0.0.1:8032 14:32:33(apple)]$ select * from apple.sbtest1;
```

  - 将mysql连接给kill
```bash
2024-05-20T14:27:21 [igoodfu@48] $ ps aux|grep mysql
guolich+ 101667  0.4  0.0 349028 185168 pts/12  S+   14:27   0:00 /home/work/mysql_3306/bin/mysql -uroot -px xx -h127.0.0.1 -P8032
guolich+ 103683  0.0  0.0 112680   952 pts/16   S+   14:31   0:00 grep --color=auto mysql
2024-05-20T14:31:11 [igoodfu@48] $ kill -9 101667

```


































