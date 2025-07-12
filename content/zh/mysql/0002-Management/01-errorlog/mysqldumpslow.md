---
title: mysqldumpslow
date: 2023-10-13
weight: 20000
description: >
  MySQL慢查询日志包含有关需要很长时间执行的查询的信息，包含select、insert、delete、update.


---

{{< alert >}}
This is a placeholder page. Replace it with your own content.
{{< /alert >}}


## 慢日志解析

```bash

# Time: 2023-10-13T22:50:21.857901+08:00
# User@Host: admin[admin] @  [127.0.0.1]  Id:   131
# Query_time: 7.358835  Lock_time: 0.000276 Rows_sent: 1  Rows_examined: 5000000
SET timestamp=1697208614;
select count(*) from sbtest2 where pad <'c' limit 1000000;
# Time: 2023-10-13T23:03:09.013220+08:00
# User@Host: admin[admin] @  [127.0.0.1]  Id:   131
# Query_time: 5.614910  Lock_time: 0.000334 Rows_sent: 1  Rows_examined: 4000000
SET timestamp=1697209383;
select count(*) from sbtest2 where id > 1000000 limit 1000000;

```

- 第一行：记录时间

- 第二行：用户名 、用户的IP信息、线程ID号

- 第三行：执行花费的时间【单位：秒】、执行获得锁的时间、获得的结果行数、扫描的数据行数

- 第四行：这SQL执行的时间戳

- 第五行：具体的SQL语句









## 配置

```sql
mysql> show variables like '%slow_query%';
+---------------------+----------------------------------------+
| Variable_name       | Value                                  |
+---------------------+----------------------------------------+
| slow_query_log      | ON                                     |
| slow_query_log_file | /data/dbdata/3308/logfile/slow3308.log |
+---------------------+----------------------------------------+
2 rows in set (0.01 sec)

mysql> show variables like 'long_query_time';
+-----------------+-----------+
| Variable_name   | Value     |
+-----------------+-----------+
| long_query_time | 10.000000 |
+-----------------+-----------+
1 row in set (0.00 sec)

mysql> show variables like '%log_queries%';
+-------------------------------+-------+
| Variable_name                 | Value |
+-------------------------------+-------+
| log_queries_not_using_indexes | OFF   |
+-------------------------------+-------+
1 row in set (0.00 sec)


mysql> show global status like '%slow%';
+---------------------+-------+
| Variable_name       | Value |
+---------------------+-------+
| Slow_launch_threads | 0     |
| Slow_queries        | 3     |
+---------------------+-------+
2 rows in set (0.00 sec)


```


## mysqldumpslow

- 如何对输出进行排序，从以下列表中选择：-s sort_type

   1. t, at: 按查询时间或平均查询时间排序

   2. l, al: 按锁定时间或平均锁定时间排序

   3. r, ar: 按发送的行数或平均发送的行数排序

   4. c: 按数量排序

- 显示多少条数据：-t N

- 反转排序顺序：-r


## 举例说明

```bash

# 取出使用最多的10条慢查询
mysqldumpslow -s c -t 10 slow.log

# 取出查询时间最慢的10条慢查询
mysqldumpslow -s t -t 10 slow.log

# 取出返回数据最多的10条慢查询
mysqldumpslow -s r -t 10 slow.log

# 取出锁等待最长的10条慢查询
mysqldumpslow -s l -t 10 slow.log

# 得到按照时间排序的前10条里面含有左连接的查询语句
mysqldumpslow -s t -t 10 -g “left join” slow.log

# 按照扫描行数最多的
mysqldumpslow -s r -t 10 -g 'left join' slow.log


```


