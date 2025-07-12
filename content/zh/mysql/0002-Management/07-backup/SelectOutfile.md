---
title: select outfile
date: 2023-10-12
description: A short lead description about this content page. It can be **bold** or _italic_ and can be split over multiple paragraphs.


weight: 20000
---

{{< alert >}}
1. 支持where过滤

2. 支持导出自定义导出部分列

3. 最常用

{{< /alert >}}



## 问题现象

CCB集群（ISAG系统）的主库CPU资源使用瞬间激增，持续时间为2分钟。




## 业务影响范围

该问题暂时对业务无影响。


## 问题分析过程

### 问题发生时间
发现该问题时间为2023-10-12日，周四。

### 问题处理过程
在收到主库cpu使用率超过80阈值告警后，登录数据库后台，查看当前数据库状态。经初步确认，没有发现高耗时的SQL，即当前数据库状态良好。
接着查看当前系统主库的慢查询日志，分析发现这个时间段内仅有少量的慢SQL语句，且执行时间在20s左右，而实测实际，因此慢查询不是
随后，检查了详细分析了该时间段内的binlog日志，发现高并发批次插入大量数据，导致数据库性能下降，执行计划请参见图二。
根据上述输出信息，我们可以得出以下结论：表的统计信息不准确，导致优化器选择了错误的执行计划，从而导致了查询性能下降。。

## 问题原因分析



## 问题

```sql
mysql> select * from sbtest3 into outfile '/tmp/sbtest3.txt';
ERROR 1290 (HY000): The MySQL server is running with the --secure-file-priv option so it cannot execute this statement

mysql> show global variables like 'secure%';
+------------------+-------+
| Variable_name    | Value |
+------------------+-------+
| secure_file_priv | NULL  |
+------------------+-------+
1 row in set (0.00 sec)

mysql> set global secure_file_priv='';
ERROR 1238 (HY000): Variable 'secure_file_priv' is a read only variable

```

## 解决
{{<alert title="问题" color="secondary">}}
1. 在MySQL配置文件中添加配置项：secure_file_priv = ''

2. 重启MySQL实例
{{</alert>}}
```sql

mysql> show global variables like 'secure%';
+------------------+-------+
| Variable_name    | Value |
+------------------+-------+
| secure_file_priv |       |
+------------------+-------+
1 row in set (0.00 sec)


```
































