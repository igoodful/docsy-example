---
title: binlog
description: 设置binlog日志过期时间精确到秒
date: 2023-10-25
weight: 20000
viz: true

---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

- https://bugs.mysql.com/bug.php?id=104785
- https://cloud.tencent.com/developer/article/1899939
- https://www.cnblogs.com/kerrycode/p/18130054

其实 binlog 的写入逻辑比较简单：事务执行过程中，先把日志写到 binlog cache（用于缓存 binlog 的内存缓冲区）中，等到事务提交时，再把 binlog cache 写到 binlog 文件中。注意，这里是每个事务线程都有一个自己的缓冲区。一个事务的 binlog 不能被拆分，因此不论这个事务多大，也会确保一个事务中产生的 binlog 要被一次性写入到磁盘中，所以一个事务的 binlog 是完整的，中间不会插入其他事务的 binlog。
系统给 binlog cache 分配了一片内存，每个线程一个，参数 binlog_cache_size 用于控制单个线程内 binlog cache 所占内存的大小。如果超过了这个参数规定的大小，就要暂存到磁盘。事务提交时，执行器会把 binlog cache 里的完整事务写入到 binlog 中，并清空 binlog cache。

{{< /alert >}}


```bash
set global  binlog_expire_logs_seconds=60*60*24;

flush logs;


```
binlog大小超过max_binlog_size
手动执行flush logs
重新启动时(MySQL将会new一个新文件用于记录binlog）
如果binlog非常多，不要轻易设置改参数，有可能导致io争用，所以我先使用purge命令清除老年的binlog。
过期时间设置的要适当，对于主从复制，要看从库的延迟决定过期时间，避免主库binlog还未传到从库便因过期而删除，导致主从不一致！！！


之前是天，并且参数名称发生变化. 在8.0版本之前，binlog日志过期时间设置都是设置expire_logs_days参数，而在8.0版本中，MySQL默认使用binlog_expire_logs_seconds参数。

1. `set global binlog_expire_logs_seconds=10`
2. `flush binary logs;flush binary logs;flush binary logs;`
3. wait 10s
4. `LOCK INSTANCE FOR BACKUP;`
5. `show master logs;`
6. `flush binary logs;`
7. `show master logs;`

you can see the expired binary log has not been removed.





### log_bin

### binlog-format
binlog-format=STATEMENT、binlog-format=ROW、binlog-format=MIXED。


### sync_binlog

### max_binlog_sizes
                          nlog文件大于 max_binlog_size 的情况。


### binlog_expire_logs_seconds
设置binlog日志过期时间精确到秒
expire_logs_days ：Binlog 过期删除不是服务定时执行，是需要借助事件触发才执行，事件包括：

- 服务器重启或停止
- 服务器被更新
- 日志达到了最大日志长度 `max_binlog_size`
- 日志被刷新：`flush logs`


### binlog_rows_query_log_events
通常在 binlog_format =  ROW 的环境下，我们可以通过 binlog 获取历史的 SQL 执行记录，前提是必须开启 binlog_rows_query_log_events 参数（默认关闭，建议开启），该参数可以通过rows_query_event 事件记录原始的 SQL，如果不开启的话，则只能获取 SQL 对应的行数据。




### 删除当前的binlog文件
reset master;

### 删除slave的中继日志
reset slave;

### 删除指定日期前的日志索引中binlog日志文件
purge master logs before '2019-03-09 14:00:00';

### 删除指定日志文件
purge master logs to 'master.000003';


| key                 | value                         |
|:--------------------|:------------------------------|
| Command-Line Format | --max-binlog-stmt-cache-size= |
| System Variable     | max_binlog_stmt_cache_size    |
| Scope               | Global                        |
| Dynamic             | Yes                           |
| Type                | Integer                       |
| Default Value       | 18446744073709547520          |
| Minimum Value       | 4096                          |
| Maximum Value       | 18446744073709547520          |
| Unit                | bytes                         |
| Block Size          | 4096                          |



- max_binlog_cache_size

| key                 | value                     |
|:--------------------|:--------------------------|
| Command-Line Format | --max-binlog-cache-size=# |
| System Variable     | max_binlog_cache_size     |
| Scope               | Global                    |
| Dynamic             | Yes                       |
| Type                | Integer                   |
| Default Value       | 18446744073709547520      |
| Minimum Value       | 4096                      |
| Maximum Value       | 18446744073709547520      |
| Unit                | bytes                     |
| Block Size          | 4096                      |





- binlog_stmt_cache_size 是每个语句的缓存大小
- binlog_cache_size 是整体二进制日志缓存的大小



### max_binlog_size

MySQL 的 max_binlog_size 参数用于控制每个二进制日志文件的最大大小。如果一个事务的大小超过了 max_binlog_size 的设定值，该事务会继续写入当前的二进制日志文件，直到该事务完成。因此，假设 max_binlog_size 设置为1G，而事务的大小为4G，那么该事务会在当前的二进制日志文件 mysql-bin.000062 中写入。

在事务完成后，MySQL 将检测到二进制日志文件大小超过了 max_binlog_size，会自动创建一个新的二进制日志文件。新文件的名称通常是 mysql-bin.000063，并且后续的写入将在新文件中进行。因此，该事务不会导致创建多个小于 max_binlog_size 大小的文件。



### max_binlog_cache_size 和 max_binlog_size
max_binlog_cache_size 和 max_binlog_size 是 MySQL 中两个不同的参数，它们分别用于控制不同的方面：

- max_binlog_cache_size：
  - 作用：控制每个线程在写入二进制日志缓存时的最大缓存大小。
  - 描述：当一个线程在执行写入二进制日志的操作时，相关的数据会首先存储在二进制日志缓存中。max_binlog_cache_size 限制了这个缓存的大小，当达到这个大小时，MySQL 会将数据刷入二进制日志文件。
  - 如果一个事务的大小超过了 max_binlog_cache_size，MySQL 将尝试将事务写入磁盘，以释放缓存。假设max_binlog_cache_size 设置为1G，而事务的大小为4G，MySQL 可能会在事务写入过程中多次将缓存写入磁盘，以确保不超过设定的缓存大小。

### binlog_cache_size
用于事务性更新

如果每个事务的大小为64M，而 binlog_cache_size 仅为1M，会发生以下情况：

1. 缓存不足： 由于每个事务需要的空间远远超过 binlog_cache_size 的大小，因此 binlog 缓存无法容纳整个事务。这将导致 binlog 缓存频繁被刷新和重用。

2. Binlog_cache_use 变化： 每次一个事务无法完全容纳在 binlog 缓存中时，Binlog_cache_use 状态变量都会递增。这是因为每次写入操作都会导致对 binlog 缓存的使用，而由于缓存不足，写入操作可能会多次发生。

3. 性能问题： 由于频繁地刷新和重用 binlog 缓存，性能可能会受到影响。这是因为写入到二进制日志是一个相对较慢的操作，尤其是在频繁进行 I/O 操作时。

为了避免这种情况，你可以考虑调整 binlog_cache_size 的大小，使其足够大，能够容纳事务的典型大小。这有助于减少频繁的缓存刷新和提高性能。在生产环境中，通常需要仔细选择合适的参数值，以适应实际的工作负载和事务模式。

### max_binlog_cache_size
用于事务性更新

#### Multi-statement transaction required more than 'max_binlog_cache_size' bytes of storage
```sql
flush status;
mysql> begin;
Query OK, 0 rows affected (0.00 sec)

mysql> update sbtest1 set pad='123456' where id<10000;
Query OK, 9999 rows affected (0.14 sec)
Rows matched: 9999  Changed: 9999  Warnings: 0

mysql> update sbtest2 set pad='123456' where id<1000000;
ERROR 1197 (HY000): Multi-statement transaction required more than 'max_binlog_cache_size' bytes of storage; increase this mysqld variable and try again


```






- max_binlog_size：
  - 作用：控制每个二进制日志文件的最大大小。
  - 描述：当一个二进制日志文件达到 max_binlog_size 指定的大小时，MySQL 会自动创建一个新的二进制日志文件，以便继续写入日志。

这两个参数的设置可以影响 MySQL 的性能和管理。合理地配置这些参数可以确保二进制日志系统在运行时具有良好的性能，并且可以更灵活地管理二进制日志文件的大小。



### max_binlog_stmt_cache_size
用于非事务性更新




### binlog_stmt_cache_size
用于非事务性更新







