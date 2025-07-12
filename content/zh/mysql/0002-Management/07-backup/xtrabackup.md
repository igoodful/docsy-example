---
title: xtrabackup
description: Percona XtraBackup 是一个强大的开源数据库备份工具，专门用于备份 MySQL 和 MariaDB 数据库。它的目标是创建一致性备份，而不会影响生产数据库的性能.
date: 2023-10-12
weight: 20000


viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;

{{< alert color="danger" title="XtraBackup" height="300">}}
**Percona XtraBackup**:

- **功能**：XtraBackup 允许你创建热备份（备份过程中数据库仍在运行）和高性能的数据库备份，可用于恢复、数据库迁移和复制。

- **一致性备份**：XtraBackup 可以创建一致性备份，这意味着备份的数据与备份开始时的数据库状态一致，不会包含不完整的事务。

- **免费且开源**：XtraBackup 是一个免费、开源的工具，可在 MySQL 和 MariaDB 数据库上使用。

- **快速备份**：它是一个高性能工具，可以在几分钟内完成大型数据库的备份。

- **增量备份**：支持增量备份，允许你在基础备份之后仅备份已更改的数据，以减少备份时间和存储空间的需求。

- **并行备份**：XtraBackup 允许并行备份，从而提高备份效率。

- **可靠性**：该工具经过广泛测试和使用，具有良好的可靠性和稳定性。

- **完整性检查**：提供完整性检查工具，以确保备份的完整性和可用性。

- **恢复功能**：支持从备份中恢复数据，包括完整备份和增量备份。

总之，Percona XtraBackup 是数据库管理员和开发人员的有力工具，用于创建高性能和一致性的数据库备份，以保护数据并支持灾难恢复和数据库维护操作。
{{< /alert >}}

参考：

- [https://opensource.actionsky.com/20210311-xtrabackup/](https://opensource.actionsky.com/20210311-xtrabackup/)
- [https://blog.frognew.com/2023/08/percona-xtrabackup-8.0-notes-04.html](https://blog.frognew.com/2023/08/percona-xtrabackup-8.0-notes-04.html)


## 选项
{{< alert color="secondary" title="XtraBackup 参数详解" height="300">}}


{{< /alert >}}


| 选项                        | 功能                                          | 示例                                               |
| :-------------------------- | :-------------------------------------------- | :------------------------------------------------- |
| `--defaults-file`           | 仅从给定文件中读取默认选项                    | `--defaults-file=/data/mysql_3306/my.cnf`          |
| `--backup`                  | 备份                                          |                                                    |
| `--incremental-basedir`     | 增量备份                                      |                                                    |
| `--target-dir`              | 备份路径                                      |                                                    |
| `--prepare`                 | 应用redo log日志                              |                                                    |
| `--slave-info`              | 二进制日志位置存储在`xtrabackup_slave_info`   |                                                    |
| `--stream`                  | 此选项仅支持 `xbstream` 格式                  | `--stream=xbstream`                                |
| `--parallel`                | 加速备份，并行拷贝ibd文件的线程数量           |                                                    |
| `--compress`                | 压缩备份                                      |                                                    |
| `--compress-threads`        | 加速备份，InnoDB数据文件进行压缩的线程数      |                                                    |
| `--compress-zstd-level`     | 指定ZSTD压缩级别，`8.0.30-22`版本添加         | 默认值为 1。允许的范围是 1 到 19                   |
| `--decompress`              | 解压缩                                        |                                                    |
| `--remove-original`         | 清理备份目录                                  |                                                    |
| `--decompress-threads`      | 指定数量的线程进行解压缩                      |                                                    |
| `--databases`               | 指定库                                        |                                                    |
| `--databases-exclude`       | 根据名称排除数据库，优先级比`--databases`更高 |                                                    |
| `--databases-file`          | 指定包含应备份的数据库和表列表的文件的路径    | 格式为`database_name1[.table_name1]`，每行一个元素 |
| `--tables`                  | 指定表`database_name.table_name`              |                                                    |
| `--tables-exclude`          | 匹配的名称将从备份中排除                      | 优先级高于`--tables`                               |
| `--tables-file`             | 每行包含一个表名的文件，格式为数据库名称.表名 | 备份将仅限于指定的表                               |
| `--no-backup-locks`         | 按照传统备份                                  | 用户只有innodb的表，就需要添加                     |
| `--no-server-version-check` | 禁用服务器版本检查， `8.0.21`版本添加         | 将源系统版本与 Percona XtraBackup 版本进行比较     |
| `--no-version-check`        | 禁用版本检查                                  | `--backup`模式下运行时会隐式启用自动版本检查       |
| `--core-file`               | 在发生致命信号时写入核心                      |                                                    |
| `--datadir`                 | 此目录应与 MySQL 服务器的数据目录相同         |                                                    |




- `--no-backup-locks`参数：`xtrabackup`备份数据库时不会对其上锁，不会阻塞DDL操作。如果备份过程中 MySQL 实例发生了创建索引的 DDL 操作，备份会中断。因为创建索引不会写 redo，若继续备份会引起数据不一致问题，所以 Xtrabackup 在这种场景中备份失败是预期行为。解决办法：不要在备份过程中创建索引，如果确实需要，建议在建表语句中直接带上索引，或者使用 lock-ddl 参数进行备份(阻塞实例上新的 DDL 操作)

- `xtrabackup`命令与`--host`指定的机器必须是同一台机器
















## 全量备份

```bash
# 1. 备份到本地目录：/home/work/tmp/backup/

xtrabackup --backup --slave-info --target-dir=/full/backup/

# 2. 应用本地目录/home/work/tmp/backup/中的备份日志，确保数据一致，提交完成的事务，回滚未完成的事务。

xtrabackup --prepare --target-dir=/home/work/tmp/backup/


# 远程备份

xtrabackup --defaults-file=/data/dbdata/3308/my3308.cnf \
--login-path=xtrlogin \
--user=admin \
--password='123456' \
--host=172.17.139.186 \
--port=3308 \
--backup \
--extra-lsndir=/data/dbdata/3308/tmp/lsn \
--tmpdir=/data/dbdata/3308/tmp \
--stream=xbstream \
2> /data/mysql_3308/full.log | gzip |  172.17.139.180  /backup/full.xbstream.gz



xtrabackup \
--defaults-file=${MY_CNF} \
--user=${slave_mysql_user} --password=${slave_mysql_pass} \
--ftwrl-wait-timeout=${pxb_ftwrl_wait_timeout} \
--ftwrl-wait-query-type=${pxb_ftwrl_wait_query_type} \
--ftwrl-wait-threshold=${pxb_ftwrl_wait_threshold} \
--kill-long-queries-timeout=${pxb_kill_long_queries_timeout} \
--kill-long-query-type=${pxb_kill_long_query_type} \
--slave-info \
--compress \
--compress-threads=${pxb_compress_threads} \
--parallel=10  \
--stream=${pxb_stream} \
--backup \
--no-backup-locks \
--target-dir=${MYSQL_BAKDIR} \
> ${MYSQL_BAKDIR}/mysql_backup_${SLAVE_IP}_${standbyport}.xbstream






```


## 增量备份与恢复


```bash
# 增量备份

xtrabackup --backup  --target-dir=/full/backup/dir/ --slave-info

xtrabackup --backup --target-dir=/incremental/dir1 --incremental-basedir=/full/backup/dir/

xtrabackup --backup --target-dir=/incremental/dir2 --incremental-basedir=/incremental/dir1

# 增量恢复

xtrabackup --prepare --apply-log-only --target-dir=/full/backup/dir

xtrabackup --prepare --apply-log-only --target-dir=/incremental/dir1 --incremental-basedir=/full/backup/dir

xtrabackup --prepare --apply-log-only --target-dir=/incremental/dir2 --incremental-basedir=/incremental/dir1

xtrabackup --prepare --target-dir=/full/backup/dir

```









## 原理

{{< figure src="/images/docs/xtrabackup.png" >}}


[xtrabackup.png](https://github.com/stranded-fish/XtraBackup-Source-Code-Analysis/tree/master)


从上述步骤可知，xtrabackup 8.0 与 2.4 的步骤主要区别为：

当只存在 InnoDB 引擎的表时，不再执行 FTWRL，





## 丢失数据

```viz-dot
digraph "M" {
        rankdir=TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = box;
                fontsize = "12"
                ];
        "M" -> "S1";
        "M" -> "S2";

}
```

> 逻辑备份或物理备份都可以


| 时间 | 步骤                                 | 示例                                                             |
| :--- | :----------------------------------- | :--------------------------------------------------------------- |
| `t1` | 一直往集群写入数据，此时先写入实例M  | sysbench                                                         |
| `t2` | 备份实例S1                           | `mysqldump --single-transaction --master-data=2 --all-databases` |
| `t3` | 主库实例M宕机                        | 关掉主库所在机器                                                 |
| `t4` | 集群选取实例S1作为主库               |                                                                  |
| `t5` | 一直往集群写入数据，此时先写入实例S1 | sysbench                                                         |
| `t6` | 宕机的实例M恢复，启动实例            |                                                                  |
| `t7` | 主库M change 到实例S1                | `change master to S1`                                            |
| `t8` | 丢失了部分的数据                     |                                                                  |





## 恢复

```bash
mkdir -p /home/mysql_3306/{data,etc,log,tmp,pid}


```



#### 若备份使用了xbstream

```bash
xbstream -xv --parallel=4 < backup.xbstream  -C  /home/mysql_3306/data

```
表示将备份文件backup.xbstream文件解压到/home/mysql_3306/data目录下


#### 若备份开启了compress压缩
> 注意它依赖qpress命令，可能需要单独安装

```bash
xtrabackup --decompress --parallel=4 --remove-original--target-dir=/home/mysql_3306/data

```
- `--decompress`  解压参数 这就不多说了
- `--remove-original` 这个参数的意义就是解压完成后并删除压缩文件
- `--parallel` 多线程解压 加速



#### prepare

```bash
xtrabackup --prepare --parallel=4 --target-dir=/home/mysql_3306/data

```
并发prepare



#### 启动
```bash
mysqld --defaults-file=/home/mysql_3306/etc/my.cnf &

```



## instance lock

```sql
admin@127.0.0.1:16315[(none)]>> select * from performance_schema.metadata_locks where object_type='backup lock'\G
*************************** 1. row ***************************
          OBJECT_TYPE: BACKUP LOCK
        OBJECT_SCHEMA: NULL
          OBJECT_NAME: NULL
          COLUMN_NAME: NULL
OBJECT_INSTANCE_BEGIN: 140526999572512
            LOCK_TYPE: SHARED
        LOCK_DURATION: EXPLICIT
          LOCK_STATUS: GRANTED
               SOURCE: sql_backup_lock.cc:100
      OWNER_THREAD_ID: 319408
       OWNER_EVENT_ID: 80
1 row in set (0.00 sec)


admin@127.0.0.1:16315[(none)]>> unlock instance;
```































