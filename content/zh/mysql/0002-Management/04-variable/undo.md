---
title: undo表空间
description: undo
date: 2023-10-12
weight: 20000
viz: true

---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{<alert color="danger" title="注意" >}}

MySQL 实例最多支持 127 个撤消表空间，其中包括 MySQL 实例初始化时创建的两个默认撤消表空间。

{{</alert>}}


```viz-dot
digraph "undo" {
rankdir = LR;
edge [
        fontsize="12"
        ];
node [
        shape = box;
        fontsize = "12"
];

"undo" -> "undo_001"
"undo" -> "undo_002"
"undo" -> "undo_..."
"undo" -> "undo_127"

"undo_001" -> "innodb_undo_log_truncate"
"undo_002" -> "innodb_undo_log_encrypt"
"undo_..." -> "innodb_rollback_segments"
"undo_127" -> "innodb_purge_rseg_truncate_frequency"

}
```



```viz-dot
digraph "undo" {
rankdir = LR;
edge [
        fontsize="12"
        ];
node [
        shape = box;
        fontsize = "12"
];

"undo" -> "innodb_undo_tablespaces"
"undo" -> "innodb_undo_directory"
"undo" -> "innodb_max_undo_log_size"
"undo" -> "innodb_undo_log_truncate"
"undo" -> "innodb_undo_log_encrypt"
"undo" -> "innodb_rollback_segments"
"undo" -> "innodb_purge_rseg_truncate_frequency"

}
```


| 变量参数                                       | 不需重启? | 默认值                  | 建议值                          | 描述                       |
| :--------------------------------------------- | :-------- | :---------------------- | :------------------------------ | :------------------------- |
| `innodb_undo_directory`                        | &#10060;  | `datadir`               | 默认值                          | 指定`undo`表空间位置       |
| `innodb_undo_log_encrypt`                      | &#9989;   | `off`                   | 默认值                          | 是否加密undo表空间         |
| `innodb_undo_log_truncate`                     | &#9989;   | `on`                    | 默认值                          | 是否截断undo表空间         |
| `innodb_purge_rseg_truncate_frequency`         | &#9989;   | `128`                   | `128`                           | 加快undo表空间的自动截断   |
| `innodb_max_undo_log_size`                     | &#9989;   | `1G`                    | `4G`                            | 定义`undo`表空间的阈值大小 |
| `innodb_rollback_segments`                     | &#9989;   | `128`                   | `128`                           |                            |
| `innodb_temp_data_file_path`                   | &#10060;  | `ibtmp1:12M:autoextend` | `ibtmp1:512M:autoextend:max:3G` |                            |



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


#### innodb_temp_data_file_path

`innodb_temp_data_file_path=ibtmp1:512M:autoextend:max:30720M`




## Undo表空间



#### 添加表空间

一个MySQL实例最多支持127个undo表空间，其中包括MySQL实例初始化时创建的两个默认undo表空间。

```sql
[root@127.0.0.1:8032 10:56:43((none))]$ show global variables like 'innodb_directories';
+--------------------+-------+
| Variable_name      | Value |
+--------------------+-------+
| innodb_directories |       |
+--------------------+-------+
1 row in set (0.01 sec)

[root@127.0.0.1:8032 10:47:46((none))]$ show global variables like 'innodb_undo_tablespaces';
+-------------------------+-------+
| Variable_name           | Value |
+-------------------------+-------+
| innodb_undo_tablespaces | 2     |
+-------------------------+-------+
1 row in set (0.03 sec)

[root@127.0.0.1:8032 10:53:57((none))]$ create undo tablespace tablespace_name add datafile 'file_name.ibu';
Query OK, 0 rows affected (3.63 sec)


[guolicheng@48 /db/storage/guolicheng/mysql_8032] $ cd data
[guolicheng@48 /db/storage/guolicheng/mysql_8032/data] $ ll
total 1919352
drwxr-x--- 2 guolicheng guolicheng       4096 2024-12-21T15:50:19  apple
-rw-r----- 1 guolicheng guolicheng         56 2024-11-26T09:18:17  auto.cnf
drwxr-x--- 2 guolicheng guolicheng       4096 2024-11-26T09:27:14  db1
drwxr-x--- 2 guolicheng guolicheng       4096 2024-11-26T09:24:25  db2
drwxr-x--- 2 guolicheng guolicheng       4096 2024-11-26T09:24:28  db3
-rw-r----- 1 guolicheng guolicheng   16777216 2025-07-01T10:54:32  file_name.ibu
-rw-r----- 1 guolicheng guolicheng     147456 2025-07-01T10:54:46 '#ib_16384_0.dblwr'
-rw-r----- 1 guolicheng guolicheng    8536064 2025-07-01T10:54:32 '#ib_16384_1.dblwr'
-rw-r----- 1 guolicheng guolicheng       5760 2024-11-26T09:18:29  ib_buffer_pool
-rw-r----- 1 guolicheng guolicheng 1073741824 2025-07-01T10:54:33  ibdata1
-rw-r----- 1 guolicheng guolicheng  536870912 2025-03-13T08:55:57  ibtmp1
drwxr-x--- 2 guolicheng guolicheng       4096 2025-07-01T10:54:32 '#innodb_redo'
drwxr-x--- 2 guolicheng guolicheng       4096 2025-03-13T08:55:56 '#innodb_temp'
drwxr-x--- 2 guolicheng guolicheng       4096 2024-11-26T09:18:28  mysql
-rw-r----- 1 guolicheng guolicheng   27262976 2025-07-01T10:54:35  mysql.ibd
drwxr-x--- 2 guolicheng guolicheng       4096 2025-03-13T08:55:57  performance_schema
-rw------- 1 guolicheng guolicheng       1680 2024-11-26T09:18:20  private_key.pem
-rw-r--r-- 1 guolicheng guolicheng        452 2024-11-26T09:18:20  public_key.pem
drwxr-x--- 2 guolicheng guolicheng       4096 2024-11-26T09:26:57  sbtest
drwxr-x--- 2 guolicheng guolicheng       4096 2024-11-26T09:18:28  sys
drwxr-x--- 2 guolicheng guolicheng       4096 2024-11-26T15:27:55  test
drwxr-x--- 2 guolicheng guolicheng       4096 2025-01-08T10:21:51  u1_database
-rw-r----- 1 guolicheng guolicheng  150994944 2025-07-01T10:54:46  undo_001
-rw-r----- 1 guolicheng guolicheng  150994944 2025-06-21T16:15:22  undo_002
[guolicheng@48 /db/storage/guolicheng/mysql_8032/data] $


```


#### 删除表空间
```sql
[root@127.0.0.1:8032 11:04:38((none))]$ select tablespace_name, file_name from information_schema.files where file_type like 'undo log';
+-----------------+-----------------+
| TABLESPACE_NAME | FILE_NAME       |
+-----------------+-----------------+
| innodb_undo_001 | ./undo_001      |
| innodb_undo_002 | ./undo_002      |
| tablespace_name | ./file_name.ibu |
+-----------------+-----------------+
3 rows in set (0.05 sec)

[root@127.0.0.1:8032 11:04:40((none))]$ alter undo tablespace tablespace_name set inactive;
Query OK, 0 rows affected (0.06 sec)

[root@127.0.0.1:8032 11:04:54((none))]$ drop undo tablespace tablespace_name;
Query OK, 0 rows affected (0.09 sec)

[root@127.0.0.1:8032 11:05:12((none))]$ select tablespace_name, file_name from information_schema.files where file_type like 'undo log';
+-----------------+------------+
| TABLESPACE_NAME | FILE_NAME  |
+-----------------+------------+
| innodb_undo_001 | ./undo_001 |
| innodb_undo_002 | ./undo_002 |
+-----------------+------------+
2 rows in set (0.02 sec)

[root@127.0.0.1:8032 11:05:18((none))]$

```




## 回滚段




## Undo段




## Undo页




