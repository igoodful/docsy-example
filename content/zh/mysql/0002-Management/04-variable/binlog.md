---
title: binlog
description: binlog
date: 2023-10-25
weight: 20000


---

{{< alert >}}

{{< /alert >}}


## binlog相关参数



| 参数                                           | 是否需要重启 | 默认值 | 建议值 | 可选值 | 描述 |
|:-----------------------------------------------|:-------------|:-------|:-------|:-------|:-----|
| binlog_cache_size                              |              |        |        |        |      |
| binlog_stmt_cache_size                         |              |        |        |        |      |
| max_binlog_cache_size                          |              |        |        |        |      |
| max_binlog_size                                |              |        |        |        |      |
| sync_binlog                                    |              |        |        |        |      |
| binlog_cache_size                              |              |        |        |        |      |
| binlog_checksum                                |              |        |        |        |      |
| binlog_row_metadata                            |              |        |        |        |      |
| log_bin                                        |              | on     |        |        |      |
| log_bin_basename                               |              |        |        |        |      |
| log_bin_index                                  |              |        |        |        |      |
| binlog_format                                  |              |        |        |        |      |
| binlog_expire_logs_seconds                     |              |        |        |        |      |
| binlog_row_image                               |              |        |        |        |      |
| binlog_transaction_dependency_tracking         |              |        |        |        |      |
| binlog_transaction_dependency_history_size     |              |        |        |        |      |
| binlog_transaction_compression_level_zstd      |              |        |        |        |      |
| binlog_transaction_compression                 |              |        |        |        |      |
| binlog_rows_query_log_events                   |              |        |        |        |      |
| binlog_row_value_options                       |              |        |        |        |      |
| binlog_row_event_max_size                      |              |        |        |        |      |
| binlog_rotate_encryption_master_key_at_startup |              |        |        |        |      |
| binlog_order_commits                           |              | on     | on     |        |      |
| binlog_max_flush_queue_time                    |              |        |        |        |      |
| binlog_gtid_simple_recovery                    |              | on     | on     |        |      |
| binlog_group_commit_sync_delay                 |              |        |        |        |      |
| binlog_group_commit_sync_no_delay_count        |              |        |        |        |      |
| binlog_error_action                            |              |        |        |        |      |
| binlog_direct_non_transactional_updates        |              |        |        |        |      |
| log_bin_use_v1_row_events                      |              |        |        |        |      |
| log_bin_trust_function_creators                |              |        |        |        |      |





#### binlog_row_metadata

```sql
[admin@127.0.0.1:16315 14:12:21((none))]$ SET GLOBAL binlog_row_metadata = full;
Query OK, 0 rows affected (0.00 sec)

[admin@127.0.0.1:16315 14:12:24((none))]$ show global variables like '%binlog_row_metadata%';
+---------------------+-------+
| Variable_name       | Value |
+---------------------+-------+
| binlog_row_metadata | FULL  |
+---------------------+-------+
1 row in set (0.00 sec)

[admin@127.0.0.1:16315 14:12:27((none))]$ SET GLOBAL binlog_row_metadata = MINIMAL;
Query OK, 0 rows affected (0.00 sec)

[admin@127.0.0.1:16315 14:12:41((none))]$ show global variables like '%binlog_row_metadata%';
+---------------------+---------+
| Variable_name       | Value   |
+---------------------+---------+
| binlog_row_metadata | MINIMAL |
+---------------------+---------+
1 row in set (0.00 sec)

[admin@127.0.0.1:16315 14:12:43((none))]$

```
- 再通过命令mysqlbinlog ，再加入新参数--print-table-metadata 就能打印出每条记录的列名和主键信息了

