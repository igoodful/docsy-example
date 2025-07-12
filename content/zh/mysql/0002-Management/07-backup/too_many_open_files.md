---
title: too many open files
description: too many open files
date: 2023-10-26
weight: 40000


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}

- [too many open files](https://opensource.actionsky.com/20220816-xtrabackup/)
- [percona](https://www.percona.com/blog/2016/12/28/using-percona-xtrabackup-mysql-instance-large-number-tables/)

{{< /alert >}}


## 问题

```bash
# xtrabackup --defaults-file=/opt/mysql/etc/3306/my.cnf --user=root --password=1 --socket=/opt/mysql/data/3306/mysqld.sock --backup --target-dir=/opt/mysql/backup/3306

xtrabackup: recognized server arguments: --server-id=1224415609 --
datadir=/opt/mysql/data/3306 --log_bin=/opt/mysql/log/binlog/3306/mysql-bin --
tmpdir=/opt/mysql/tmp/3306 --
innodb_log_group_home_dir=/opt/mysql/log/redolog/3306 --
innodb_buffer_pool_size=1G --innodb_data_file_path=ibdata1:1G:autoextend --
innodb_file_per_table=1 --innodb_flush_log_at_trx_commit=1 --
innodb_flush_method=O_DIRECT --innodb_io_capacity=1000 --
innodb_log_buffer_size=64M --innodb_log_file_size=2G --
innodb_log_files_in_group=2 --innodb_max_dirty_pages_pct=60 --
innodb_undo_tablespaces=3 --innodb_read_io_threads=8 --innodb_write_io_threads=8
--innodb_open_files=4096 --open_files_limit=20000
...
xtrabackup: open files limit requested 20000, set to 10240
...
220413 13:58:50 >> log scanned up to (6550086912)
InnoDB: Operating system error number 24 in a file operation.
InnoDB: Error number 24 means 'Too many open files'
InnoDB: Some operating system error numbers are described at
http://dev.mysql.com/doc/refman/5.7/en/operating-system-error-codes.ht ml
InnoDB: File ./testdb/sbtest4576.ibd: 'open' returned OS error 124. Cannot
continue operation
InnoDB: Cannot continue operation.
```



这说明在 root 用户下，直接以 mysql 配置文件中的 open files limit为 实际允许打开文件数量的限制。


## 解决

（1）在非root用户下使用xtrabackup备份时，用户的open files值需要大于备份时打开的文件数量。建议设置的值比配置文件中的open_files_limit参数值大，确保备份用户的open files是够备份时打开的文件数量。可在/etc/security/limits.d下修改nofile的值永久生效，也可以直接在用户下ulimit -n 修改open files 使其临时生效。

#### 修改用户的soft、hard的nofile值永久生效
```bash
* soft nofile 20000
* hard nofile 20000
```
#### 或者登录到用户下ulimit -n临时生效
（2）在 root 用户下使用 xtrabackup 备份时，因为 root 用户默认对 open files 不做限制，这时需要确保配置文件中的 ope_files_limit 的运行值是满足需求的，其备份会直接以配置文件中的参数值来限制允许打开的文件数量。也可以直接命令行选项传递符合需求的值，或直接在执行备份前设置 ulimit -n 20000 使 open files 临时生效。

###### 1.修改配置文件中的open_files_limit值
```ini
[mysqld]
open_files_limit=20000
```

###### 2.执行指定命令行选项传递
`--open-files-limit=20000`

###### 3、在执行备份前设置ulimit值
```bash
ulimit -n 20000
```

（3）确保系统中保持打开的最大文件数。如果这个数字太小，Percona Xtrabackup 可能会独占系统中打开的文件，导致其他进程尝试打开文件时失败。这可能会导致 MySQL 服务器崩溃，以及其他进程失败。

#### 查看系统的file-max
```bash
[root@test ~]# cat /proc/sys/fs/file-max
379560
```
####  修改file-max
```bash
[root@test ~]# sysctl -w fs.file-max=5000000
[root@test ~]# echo "fs.file-max=5000000" >> /etc/sysctl.conf
```





