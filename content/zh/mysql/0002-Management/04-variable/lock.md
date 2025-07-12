---
title: lock
description: lock
date: 2023-10-25
weight: 20000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

> [max_allowed_packet](https://opensource.actionsky.com/20220712-mysql/)



## 参数


| 参数                             | 全局动态        | 建议值：默认值：可选值                       | 描述                                                       |
| :------------------------------- | :-------------- | :------------------------------------------- | :--------------------------------------------------------- |
| slave_parallel_type              | &#9989;         | 内存一半：128M ：`5M-2^64-1`                 | database缓冲池的字节大小，InnoDB缓存表和索引数据的内存区域 |
| slave_parallel_workers           | &#9989;         | 8：8：`1~64`                                 | database开启多个内存缓冲池                                 |
| slave_pending_jobs_size_max      | &#9989;         | CPU核数：0：`0-1000`                         | 0表示无限并发                                              |
| slave_preserve_commit_order      | &#10060;&#9989; | ON：ON：`ON/OFF`                             | version≥8.0.30时为动态，否则为静态                         |
| slave_rows_search_algorithms     | &#9989;         | ON：ON：`ON/OFF`                             |                                                            |
| log_slave_updates                | &#9989;         | 1：1：`0/1/2 `                               |                                                            |
| gtid_mode                        | &#9989;         | 1：1：`0-4294967295`                         |                                                            |
| enforce_gtid_consistency         | &#10060;        | O_DIRECT：fsync：                            | fsync,O_DSYNC,littlesync,nosync,O_DIRECT,O_DIRECT_NO_FSYNC |
| relay_log_info_repository        | &#9989;         | 8000：200：`100-2^64-1`                      |                                                            |
| innodb_io_capacity_max           | &#9989;         |                                              |                                                            |
| innodb_log_buffer_size           | &#9989;         | 128M：16M：`1M-4G `                          |                                                            |
| innodb_log_file_size             | &#10060;        | 1G：48M：`4M-512G/innodb_log_files_in_group` |                                                            |
| innodb_log_files_in_group        | &#10060;        | 4：2：`2-100`                                |                                                            |
| innodb_print_all_deadlocks       | &#9989;         | ON：OFF：`ON/OFF`                            | 事务中所有死锁的InnoDB信息都会记录在mysqld错误日志中       |
| innodb_read_io_threads           | &#10060;        | 4：4：`1-64 `                                | 读取操​​作的 I/O 线程数                                    |
| innodb_write_io_threads          | &#10060;        | 4：4：`1-64`                                 | 写入操作的 I/O 线程数                                      |
| innodb_temp_data_file_path       | &#10060;        | `ibtmp1:12M:autoextend:max:81920M`：         | 默认值： `ibtmp1:12M:autoextend`                           |
| transaction_isolation            | &#9989;session  | READCOMMITTED                                | READCOMMITTED,REPEATABLEREAD database 设置默认事务隔离级别 |
| transaction_write_set_extraction | &#9989;session  | XXHASH64：XXHASH64：`XXHASH64/OFF/MURMUR32`  |                                                            |
| innodb_rollback_on_timeout       | &#9989;         | on                                           | 锁等待超时后是否回滚事务                                   |
|                                  |                 |                                              |                                                            |
|                                  |                 |                                              |                                                            |


### Lock wait timeout exceeded;try restarting transaction

> innodb_lock_wait_timeout




> innodb_rollback_on_timeout


因此，如果您希望事务在遇到 InnoDB 锁等待错误时自动回滚，类似于死锁中发生的情况，请在 MySQL 配置文件中设置以下选项：

innodb_rollback_on_timeout=1

需要重新启动 MySQL。当部署基于MySQL的集群时，ClusterControl将始终在每个节点上设置innodb_rollback_on_timeout = 1 。如果没有此选项，您的应用程序必须重试失败的语句，或显式执行 ROLLBACK 以维护事务原子性。

验证配置是否正确加载：




> show engine innodb status\G

> show full processlist;

> select * from performance_schema.data_lock_waits;

> select * from performance_schema.events_statements_history where thread_id = xx;

> update performance_schema.setup_consumers set enabled = 'YES' where name = 'events_statements_history_long';

> select thread_id,event_id,event_name, current_schema,sql_text from events_statements_history_long where thread_id = xx order by event_id;

`cat track_lockwait.sh`
```sql
#!/bin/bash
## track_lockwait.sh
## Print out the blocking statements that causing InnoDB lock wait

INTERVAL=5
DIR=/root/lockwait/

[ -d $dir ] || mkdir -p $dir

while true; do
  check_query=$(mysql -A -Bse 'select thread_id,event_id,event_name,current_schema,sql_text from events_statements_history_long where thread_id in (select blocking_thread_id from data_lock_waits) order by event_id')

  # if $check_query is not empty
  if [[ ! -z $check_query ]]; then
    timestamp=$(date +%s)
    echo $check_query > $DIR/innodb_lockwait_report_${timestamp}
  fi

  sleep $INTERVAL
done
```





