---
title: 4. 慢查询分析
description: slow sql
date: 2023-10-31
weight: 4000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{<alert  color="secondary">}}



{{</alert>}}

| 配置                  | 参数          | 备注         |
| :-------------------- | :------------ | :----------- |
| version               | 8.0.32        | 版本         |
| engine                | innodb        | 存储引擎     |
| transaction_isolation | read commited | 事务隔离级别 |





## 一、获取慢SQL


### 1. 相关参数

- slow_query_log
  控制慢查询日志的开启，ON  开启 [默认] ；OFF 关闭
- slow_query_log_file
  慢查询日志的文件名，如果云TicBase的 /greatdb/mysql/logfile/slow.log
- long_query_time
  慢查询的阈值，默认值：1秒 ，可以指定小数秒，如：0.1。
- log_output
  控制日志的输出形式，FILE[默认]--输出到 slow_query_log_file指定的文件；TABLE--输出到 mysql.slow_log。
- log_queries_not_using_indexes
  控制是否记录未使用索引的查询，ON  开启  ；OFF 关闭[默认]
- log_throttle_queries_not_using_indexes
  每分钟未使用索引的查询记录次数，0--不限制
- log_slow_admin_statements
  是否包括慢的管理语句如：ALTER TABLE, ANALYZE TABLE, CHECK TABLE, OPTIMIZE TABLE,  REPAIR TABLE，CREATE INDEX, DROP INDEX等，ON  开启  ；OFF 关闭[默认]。
- min_examined_row_limit
  检查行数限制，SQL检查行数少于这个数量，则不会被记录到慢查询日志中。[默认】0--不限制。
-  log_slow_extra
  在慢查询日志文件中，写入慢SQL的额外的字段。

记录到慢日志中的条件是：
       执行时间超过long_query_time 而且 至少检查了 min_examined_row_limit 行数据。
  如果是管理语句需log_slow_admin_statements=ON，且 满足前2个条件。
        或者
        log_queries_not_using_indexes=ON ，记录没有使用索引的语句（不看执行时间）。


### 2. 在线获取


```sql
show  full processlist;

explain analyze

explain

explain format=json

show warnings\G

```


### 3. 慢日志分析

基本的慢查询日志文件内容：


```sql
# Time: 2024-11-07T09:46:43.512637+08:00
# User@Host: admin[admin] @  [127.0.0.1]  Id: 5105295
# Query_time: 2.165031  Lock_time: 0.000001 Rows_sent: 5  Rows_examined: 5
SET timestamp=1730944001;
SELECT * from INFORMATION_SCHEMA.INNODB_FT_INDEX_TABLE LIMIT 5;

# Time: 2024-11-07T09:54:43.577889+08:00
# User@Host: admin[admin] @  [127.0.0.1]  Id: 5105295
# Query_time: 1.896118  Lock_time: 0.000014 Rows_sent: 1  Rows_examined: 99980
SET timestamp=1730944481;
SELECT COUNT(0)
    FROM r_tBl_third_Region
    WHERE third_id = 14
      AND MATCH (item_val) AGAINST ('440305');

```

log_slow_extra=ON时，慢查询日志文件内容：

```sql
# Time: 2024-08-27T16:41:42.257938+08:00
# User@Host: admin[admin] @  [127.0.0.1]  Id: 349955
# Query_time: 0.001508  Lock_time: 0.000865 Rows_sent: 1  Rows_examined: 100 Thread_id: 349955 Errno: 0 Killed: 0 Bytes_received: 0 Bytes_sent: 63 Read_first: 1 Read_last: 0 Read_key: 1 Read_next: 0 Read_prev: 0 Read_rnd: 0 Read_rnd_next: 101 Sort_merge_passes: 0 Sort_range_count: 0 Sort_rows: 0 Sort_scan_count: 0 Created_tmp_disk_tables: 0 Created_tmp_tables: 0 Start: 2024-08-27T16:41:42.256430+08:00 End: 2024-08-27T16:41:42.257938+08:00 Schema: testdb Rows_affected: 0
use testdb;
SET timestamp=1724748102;
select count(*) from sbtest99 where pad like '22%';

```


#### A. mysqldumpslow

```bash
mysqldumpslow -s r -t 10 /var/lib/mysql/atguigu-slow.log     #得到返回记录集最多的10个SQL

mysqldumpslow -s c -t 10 /var/lib/mysql/atguigu-slow.log     #得到访问次数最多的10个SQL

mysqldumpslow -s t -t 10 -g "LEFT JOIN" /var/lib/mysql/atguigu-slow.log   #得到按照时间排序的前10条里面含有左连接的查询语句

mysqldumpslow -s r -t 10 /var/lib/mysql/atguigu-slow.log | more      #结合| more使用，防止爆屏情况

```
- s：表示按何种方式排序
  - c：访问次数
  - l：锁定时间
  - r：返回记录
  - t：查询时间
  - al：平均锁定时间
  - ar：平均返回记录数
  - at：平均查询时间
- t：返回前面多少条的数据
- g：后边搭配一个正则匹配模式，大小写不敏感




#### B. pt-query-digest





## 二、优化慢SQL


### 1. 获取执行计划


```sql
explain format=tree

explain analyze

explain

explain format=json

show warnings\G

```

注意：执行explain有wanings提示时，留意warnings内容，会提示不能使用某种优化的原因。





### 2. 获取表的相关信息


```sql
show create table eiop_act_info\G

show index from eiop_act_info;

show table status like 'eiop_act_info';


```



### 3. profile分析资源消耗情况

从SQL执行的程序处理过程，分析SQL语句各阶段的执行时间，以及资源的消耗情况。



```sql
show variables like 'profil%';

set profiling=on;

select sum(k) from sbtest1 ;
select count(distinct k) from sbtest1;

show profiles;

show profile cpu,block io for query 2;

show profile all for query 4;


```

### 4. optimizer_trace优化器跟踪

优化器跟踪可以显示优化器的评估过程。
对于每个连接，都有一个join_prepare连接准备对象、一个join_optimization连接优化对象、一个join_execution连接执行对象。查询转换(IN->EXISTS，外部连接到内部连接)、简化(删除子句)、等式传播显示在子对象中。还显示了对范围优化器的调用、成本评估、为什么选择一个访问路径而不是另一个访问路径的原因，或者为什么选择一个排序方法而不是另一个排序方法。

```sql
--打开跟踪开关:
SET optimizer_trace="enabled=on",end_markers_in_json=on; # make readable
--执行被跟踪查询计划的语句
SELECT ...;
--查询优化器的跟踪
select * from information_schema.optimizer_trace\G
--关闭优化器跟踪
SET optimizer_trace="enabled=off";


```



### 5. SQL优化手段


#### A. 添加索引




#### B. hint提示




#### C. 添加索引



5. 调整系统变量，让SQL语句在特定系统变量下运行。
  - sort_buffer_size  为排序分配的内存；超出时，使用磁盘文件
  - join_buffer_size  为每个表连接分配的内存缓存；超出时，使用磁盘临时表。
  - tmp_table_size    单个内存内部临时表的大小；超出时，使用磁盘临时表。

















