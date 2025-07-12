---
title: 表空间
description: space
date: 2023-10-12
weight: 20000


---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{<alert color="danger" title="注意" >}}


{{</alert>}}



| 变量参数                                       | 是否需要重启 | 默认值  | 建议值     | 可选值                                                                              | 描述                              |
| :--------------------------------------------- | :----------- | :------ | :--------- | :---------------------------------------------------------------------------------- | :-------------------------------- |
| `innodb_data_file_path`                        | 是           | `1024M` |            |                                                                                     |                                   |
| `innodb_file_per_table`                        | 否           | `on`    | `on`       | `on/off`                                                                            | on表示每个表一个表空间数据文件ibd |
| `innodb_flush_method`                          | 是           | `fsync` | `O_DIRECT` | fsync <br> O_DSYNC <br> littlesync <br> nosync <br> O_DIRECT <br> O_DIRECT_NO_FSYNC |                                   |
|                                                |              |         |            |                                                                                     |                                   |
|                                                |              |         |            |                                                                                     |                                   |
|                                                |              |         |            |                                                                                     |                                   |
| binlog_checksum                                |              |         |            |                                                                                     |                                   |
| binlog_row_metadata                            |              |         |            |                                                                                     |                                   |
| log_bin                                        |              | on      |            |                                                                                     |                                   |
| log_bin_basename                               |              |         |            |                                                                                     |                                   |
| log_bin_index                                  |              |         |            |                                                                                     |                                   |
| binlog_format                                  |              |         |            |                                                                                     |                                   |
| binlog_expire_logs_seconds                     |              |         |            |                                                                                     |                                   |
| binlog_row_image                               |              |         |            |                                                                                     |                                   |
| binlog_transaction_dependency_tracking         |              |         |            |                                                                                     |                                   |
| binlog_transaction_dependency_history_size     |              |         |            |                                                                                     |                                   |
| binlog_transaction_compression_level_zstd      |              |         |            |                                                                                     |                                   |
| binlog_transaction_compression                 |              |         |            |                                                                                     |                                   |
| binlog_rows_query_log_events                   |              |         |            |                                                                                     |                                   |
| binlog_row_value_options                       |              |         |            |                                                                                     |                                   |
| binlog_row_event_max_size                      |              |         |            |                                                                                     |                                   |
| binlog_rotate_encryption_master_key_at_startup |              |         |            |                                                                                     |                                   |
| binlog_order_commits                           |              | on      | on         |                                                                                     |                                   |
| binlog_max_flush_queue_time                    |              |         |            |                                                                                     |                                   |
| binlog_gtid_simple_recovery                    |              | on      | on         |                                                                                     |                                   |
| binlog_group_commit_sync_delay                 |              |         |            |                                                                                     |                                   |
| binlog_group_commit_sync_no_delay_count        |              |         |            |                                                                                     |                                   |
| binlog_error_action                            |              |         |            |                                                                                     |                                   |
| binlog_direct_non_transactional_updates        |              |         |            |                                                                                     |                                   |
| log_bin_use_v1_row_events                      |              |         |            |                                                                                     |                                   |
| log_bin_trust_function_creators                |              |         |            |                                                                                     |                                   |



#### innodb_data_file_path
> 系统表空间，您无法通过更改其大小属性来增加现有系统表空间数据文件的大小

增加系统表空间大小的最简单方法是将其配置为自动扩展`autoextend`

```sql
[root@127.0.0.1:8032 10:33:12(db8)]$ show global variables like 'innodb_data_file_path';
+-----------------------+--------------------------+
| Variable_name         | Value                    |
+-----------------------+--------------------------+
| innodb_data_file_path | ibdata1:1024M:autoextend |
+-----------------------+--------------------------+
1 row in set (0.00 sec)

[root@127.0.0.1:8032 10:35:49(db8)]$

```

新增系统表空间

`/ibdata/ibdata1:988M;/disk2/ibdata2:50M:autoextend`






#### innodb_file_per_table


{{<alert color="success" title="优势" >}}

1. 截断或删除在“每个表一个文件”表空间中创建的表后，磁盘空间将返回给操作系统。截断或删除存储在共享表空间中的表会在共享表空间数据文件内创建可用空间，这些空间只能用于 InnoDB数据。换句话说，截断或删除表后，共享表空间数据文件的大小不会缩小。

2. ALTER TABLE：对位于共享表空间中的表执行表复制ALTER TABLE 操作会增加表空间占用的磁盘空间量。此类操作可能需要与表中的数据加上索引一样多的额外空间。此空间不会像每个表一个文件的表空间那样释放回操作系统。

3. TRUNCATE TABLE：在驻留在每个表文件表空间中的表上执行时性能更佳。

4. 创建位置：可以在单独的存储设备上创建每个表一个文件的表空间数据文件，以进行 I/O 优化、空间管理或备份。请参见 第 17.6.1.2 节“在外部创建表”。

5. 导入：您可以从另一个 MySQL 实例导入位于 file-per-table 表空间中的表。请参见 第 17.6.1.3 节“导入 InnoDB 表”。

6. 行格式：在 file-per-table 表空间中创建的表支持与行格式相关的功能，DYNAMIC而 COMPRESSED系统表空间不支持这些功能。请参见 第 17.10 节“InnoDB 行格式”。

7. 数据损坏：存储在单独的表空间数据文件中的表可以节省时间并在发生数据损坏、备份或二进制日志不可用或无法重新启动 MySQL 服务器实例时提高成功恢复的机会。

8. 大小监控：每个表一个文件的表空间允许通过监视表空间数据文件的大小来监视文件系统上的表大小。

9. 性能：innodb_flush_method当设置为 时，常见的 Linux 文件系统不允许对单个文件（例如共享表空间数据文件）进行并发写入 O_DIRECT。因此，将 file-per-table 表空间与此设置结合使用时，可能会提高性能。

10. 64T大小限制：共享表空间中的表的大小受 64TB 表空间大小限制。相比之下，每个表文件表空间的大小限制为 64TB，这为单个表的大小增长提供了足够的空间。

{{</alert>}}


缺点：

1. fsync操作针对多个文件表数据文件执行，而不是单个共享表空间数据文件。由于 fsync操作针对每个文件，因此无法合并多个表的写入操作，这会导致操作总数增加fsync 。

2. mysqld必须为每个文件表表空间保留一个打开的文件句柄，如果文件表表空间中有大量表，这可能会影响性能。

3. 当每个表都有自己的数据文件时，需要更多的文件描述符。







