---
title: CPU
description: 最近一段时间线上某实例频繁报警CPU飙高，每次都捕获到同一种 SQL
date: 2023-10-12
weight: 60000


---

{{< alert >}}
最近一段时间线上某实例频繁报警CPU飙高，每次都捕获到同一种 SQL
{{< /alert >}}


### 一、环境信息

{{< note >}}

| 名称          | 配置       |
| :------------ | :--------- |
| Arch          | x64        |
| OS            | CentOS 7.9 |
| CPU           | 32C        |
| Memory        | 128G       |
| Space         | 2.6T       |
| MySQL Version | 8.0.26     |

{{< /note >}}



## 问题现象

SQL 使用到了索引idx_msg_uid_time，单条执行可以秒级完成，但是并发执行会遭遇执行时间过长（超过1个小时）且CPU过高的问题。



## 排查


1. mpstat -P ALL 1，查看cpu使用情况，主要消耗在sys即os系统调用上

2. perf top，cpu主要消耗在_spin_lock

3. 生成perf report查看详细情况

4. 采用pt-pmp跟踪mysqld执行情况，热点主要集中在mem_heap_alloc和mem_heap_free上。



## 解决
将mysqld的内存库函数替换成tcmalloc，相比ptmalloc，tcmalloc可以更好的支持高并发调用。

修改my.cnf，添加如下参数并重启

[mysqld_safe]
malloc-lib=tcmalloc
上周五早上7点执行的操作，到现在超过72小时，期间该实例没有再出现cpu长期飙高的情形。


## 排查那个SQL消耗CPU最高？

{{<alert>}}

1. top
2. top -p -H
3. select a.thread_os_id,b.user,b.host,b.db,b.command,b.time,b.state,b.info from performance_schema.threads a,information_schema.processlist b where b.id = a.processlist_id where a.thread_os_id IN('xx', 'xx',);


{{</alert>}}


### 1. 模拟一个慢SQL


```sql

[dbscale_internal@172.17.137.95:16310 10:43:10(db1)]$ select * from sbtest1 t1, sbtest2 t2, sbtest3 t3 where t1.c=t2.c limit 1;

```


### 2. top -H找到mysql消耗最高的系统线程ID

```bash
2024-10-16T10:43:57 [root@x86centos7-136 /root] $ top -p 20965  -H
top - 10:44:06 up 7 days, 16:44,  1 user,  load average: 0.50, 0.33, 0.19
Threads:  57 total,   1 running,  56 sleeping,   0 stopped,   0 zombie
%Cpu(s): 14.2 us,  0.5 sy,  0.0 ni, 73.6 id,  1.7 wa,  0.0 hi,  0.0 si,  9.9 st
KiB Mem :  8008992 total,  2320224 free,  1895348 used,  3793420 buff/cache
KiB Swap:        0 total,        0 free,        0 used.  5758820 avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
27150 mysql     20   0 7266944   1.3g  16976 R 52.7 16.9   5:05.74 mysqld
18898 mysql     20   0 7266944   1.3g  16976 S  1.2 16.9   6:17.77 mysqld
21001 mysql     20   0 7266944   1.3g  16976 S  0.4 16.9   2:48.77 mysqld
21042 mysql     20   0 7266944   1.3g  16976 S  0.4 16.9  23:55.08 mysqld
 7202 mysql     20   0 7266944   1.3g  16976 S  0.4 16.9   3:16.59 mysqld
20965 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:34.05 mysqld
20975 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:27.75 mysqld
20976 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:28.35 mysqld
20977 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:28.40 mysqld
20978 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:28.90 mysqld
20979 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:28.39 mysqld
20980 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:28.29 mysqld
20981 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:28.67 mysqld
20982 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:28.13 mysqld
20983 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:28.42 mysqld
20984 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:28.48 mysqld
20985 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:27.20 mysqld
20986 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:27.17 mysqld
20987 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:26.83 mysqld
20988 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:27.14 mysqld
20989 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:28.06 mysqld
20990 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:26.87 mysqld
20991 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:26.92 mysqld
20992 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:26.76 mysqld
20993 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:35.07 mysqld
20994 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:02.37 mysqld
20995 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:02.36 mysqld
20996 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:02.30 mysqld
20997 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:19.00 mysqld
20998 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   2:32.72 mysqld
20999 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   2:32.97 mysqld
21000 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   2:33.09 mysqld
21006 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:17.43 mysqld
21007 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:20.41 mysqld
21008 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:03.47 mysqld
21026 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:00.00 mysqld
21027 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:50.91 mysqld
21028 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:01.91 mysqld
21029 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:04.33 mysqld
21033 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:00.00 mysqld
21034 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   3:19.09 mysqld
21035 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:00.01 mysqld
21036 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:00.00 mysqld
21037 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:00.00 mysqld
21038 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:00.00 mysqld
21039 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:00.00 mysqld
21040 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:00.00 mysqld
21041 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:00.00 mysqld
21043 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:18.54 mysqld
21044 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:13.11 mysqld
21045 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:07.47 mysqld
21082 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   9:43.97 mysqld
18891 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   2:05.96 mysqld
27153 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   1:49.94 mysqld
16848 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:10.73 mysqld
17337 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   4:06.74 mysqld
19210 mysql     20   0 7266944   1.3g  16976 S  0.0 16.9   0:05.79 mysqld
```


### 3. 根据系统线程ID找到mysql会话线程ID和SQL语句

```sql
[dbscale_internal@172.17.136.101:16310 10:41:12(db1)]$ select a.thread_os_id,b.user,b.host,b.db,b.command,b.time,b.state,b.info from performance_schema.threads a,information_schema.processlist b where b.id = a.processlist_id;
+--------------+------------------+----------------------+--------------------+------------------+--------+-----------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| THREAD_OS_ID | user             | host                 | db                 | command          | time   | state                                                           | info                                                                                                                                                                      |
+--------------+------------------+----------------------+--------------------+------------------+--------+-----------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|        21042 | dbscale_internal | 172.17.137.95:47578  | NULL               | Sleep            |      1 |                                                                 | NULL                                                                                                                                                                      |
|        21043 | dbscale_internal | 172.17.137.95:47584  | information_schema | Sleep            |      2 |                                                                 | NULL                                                                                                                                                                      |
|        21045 | dbscale_internal | 172.17.138.136:60528 | information_schema | Sleep            |      1 |                                                                 | NULL                                                                                                                                                                      |
|        18891 | dbscale_internal | 172.17.136.101:60968 | NULL               | Sleep            |   1584 |                                                                 | NULL                                                                                                                                                                      |
|        27150 | dbscale_internal | 172.17.137.95:59106  | db1                | Query            |     21 | executing                                                       | select * from sbtest1 t1, sbtest2 t2, sbtest3 t3 where t1.c=t2.c limit 1                                                                                                  |
|        21039 | event_scheduler  | localhost            | NULL               | Daemon           | 396198 | Waiting on empty queue                                          | NULL                                                                                                                                                                      |
|        19210 | dbscale_internal | 172.17.136.101:53568 | NULL               | Binlog Dump GTID | 154526 | Source has sent all binlog to replica; waiting for more updates | NULL                                                                                                                                                                      |
|        21044 | dbscale_internal | 172.17.137.95:58560  | NULL               | Binlog Dump GTID | 156540 | Source has sent all binlog to replica; waiting for more updates | NULL                                                                                                                                                                      |
|        21082 | dbscale_internal | 172.17.136.101:52780 | information_schema | Sleep            |    955 |                                                                 | NULL                                                                                                                                                                      |
|        27153 | dbscale_internal | 172.17.136.101:60982 | db1                | Query            |      0 | executing                                                       | select a.thread_os_id,b.user,b.host,b.db,b.command,b.time,b.state,b.info from performance_schema.threads a,information_schema.processlist b where b.id = a.processlist_id; |
+--------------+------------------+----------------------+--------------------+------------------+--------+-----------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
10 rows in set (0.01 sec)


```







