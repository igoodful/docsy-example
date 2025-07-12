---
title: 复制参数
description: replica
date: 2023-10-25
weight: 20000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
> [max_allowed_packet](https://opensource.actionsky.com/20220712-mysql/)
{{< /alert >}}


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
|                                  |                 |                                              |                                                            |
|                                  |                 |                                              |                                                            |
|                                  |                 |                                              |                                                            |






- replica_parallel_type
  - LOGICAL_CLOCK
  - DATABASE
  - 引入和弃用版本：`[8.0.26,8.0.29]`


- replica_parallel_workers
  - 0 `默认值（8.0.26）`
  - 4 `默认值（>=8.0.27）`
  - 1 表示禁用并行执行
  - 最大值与最小值：`[0,1024]`
  - 引入版本：`[8.0.26,]`
  - 修改该参数后不会立即生效，而是会应用于所有后续 START REPLICA语句


- replica_net_timeout
  - 引入：`[8.0.26,]`
  - 全局动态变量
  - 范围：`[1,31536000]`秒
  - 默认值：60

指定在副本认为连接中断、中止读取并尝试重新连接之前等待更多数据或来自源的心跳信号的秒数。

设置此变量不会立即生效。变量的状态适用于所有后续 START REPLICA命令。













