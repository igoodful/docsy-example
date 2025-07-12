---
title: my.cnf
description: my.cnf
date: 2023-10-25
weight: 2000


---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{<alert>}}

配置文件

{{</alert>}}

## 配置优先级
mysql 客户端除了会按照命令 mysql --verbose --help|grep my.cnf输出的常规的顺序读取配置外，在最后还会额外的读取 .mylogin.cnf 文件中配置

| 配置文件                | 功能                                                   | 备注                                           |
| :---------------------- | :----------------------------------------------------- | :--------------------------------------------- |
| `/etc/my.cnf`           | 仅限服务器                                             |                                                |
| `/etc/mysql/my.cnf`     | 仅限服务器                                             |                                                |
| `SYSCONFDIR/my.cnf`     | 仅限服务器, CMake构建MySQL                             | `cmake -DSYSCONFDIR=/home/work/mysql_3306/etc` |
| `$MYSQL_HOME/my.cnf`    | 仅限服务器,环境变量`MYSQL_HOME`                        |                                                |
| `--defaults-extra-file` | 命令行指定的额外配置文件路径                           | 命令行指定的额外配置文件路径                   |
| `--defaults-file`       | 命令行指定的配置文件路径                               | 只在该路径下搜索配置文件，最佳配置方式         |
| `--xxx`                 | 命令行指定启动选项                                     | 命令行指定启动选项，优先于`--defaults-file`    |
| `~/.mylogin.cnf`        | `mysql_config_editor`生成,`mysql --login-path=mylogin` | 加密，仅用于`mysql`客户端                      |
| `~/.my.cnf`             | `mysql`                                                | 明文，仅用于`mysql`客户端                      |


- /etc/my.cnf
- /etc/mysql/my.cnf
- /usr/local/mysql/etc/my.cnf
- /data/mysql/3306/base/my.cnf
- ~/.my.cnf
- ~/.mylogin.cnf



尝试用 no-defaults 不读取任何配置文件排除配置文件的干扰,看是否会恢复正常
```bash
mysql --help|grep no-defaults
```


```ini
# 当super_read_only=ON时，避免xtrabackup出现undo_1_trunc.log导致备份失败,innodb_max_undo_log_size较大，innodb_purge_rseg_truncate_frequency较大
innodb_max_undo_log_size = 4294967296
innodb_purge_rseg_truncate_frequency = 128
```


## 可配置性
> 查看系统变量是否能够在配置文件配置

```sql
[dbscale_internal@172.17.135.83:16310 09:16:53((none))]$ show global variables like '%foreign%';
+--------------------+-------+
| Variable_name      | Value |
+--------------------+-------+
| foreign_key_checks | ON    |
+--------------------+-------+
1 row in set (0.13 sec)

[dbscale_internal@172.17.135.83:16310 09:17:11((none))]$ show global variables like '%innodb_buffer_pool_size%';
+-------------------------+------------+
| Variable_name           | Value      |
+-------------------------+------------+
| innodb_buffer_pool_size | 4294967296 |
+-------------------------+------------+
1 row in set (0.01 sec)

[dbscale_internal@172.17.135.83:16310 09:21:01((none))]$ show global variables like 'autocommit';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| autocommit    | ON    |
+---------------+-------+
1 row in set (0.01 sec)

[dbscale_internal@172.17.135.83:16310 09:21:18((none))]$

```

方法：
```bash
mysqld --verbose --help | grep "xxx"
```

示例
```bash
[root@node71 /root] $  /data/mysql_3306/bin/mysqld --verbose --help | grep "innodb-buffer-pool-size"
  --innodb-buffer-pool-size=#
innodb-buffer-pool-size 134217728
[root@node71 /root] $  /data/mysql_3306/bin/mysqld --verbose --help | grep "foreign-key-checks"
[root@node71 /root] $
[root@node71 /root] $  /data/mysql_3306/bin/mysqld --verbose --help | grep "autocommit"
  --autocommit        Set default value for autocommit (0 or 1)
                      (Defaults to on; use --skip-autocommit to disable.)
autocommit  TRUE
session-track-system-variables  time_zone,autocommit,character_set_client,character_set_results,character_set_connection
[root@node71 /root] $

```

- 执行命令有输出中有`--innodb-buffer-pool-size`,`--autocommit`，则说明这些变量均可在配置文件中配置

- 执行命令没有任何输出，说明 foreign_key_checks 不能通过配置文件配置

- 注意：grep 后面系统变量名中的下划线需要替换为中划线，例如`foreign_key_checks`要替换为`foreign-key-checks`,`innodb_buffer_pool_size`要替换为`innodb-buffer-pool-size`

对于官方文档里说明 MySQL 支持的系统变量，如果我们在配置文件（my.cnf）中增加了一个系统变量，MySQL 启动时报 unknown variable xxx 错误，说明这个系统变量不支持通过配置文件配置。

另一种分辨方法是执行 /path/mysqld --verbose --help | grep "xxx" 命令，如果输出结果中没有 --xxx .... 这样的信息，也可以说明这个系统变量不支持通过配置文件配置。





## 源码

- 全局链表：all_sys_vars

`/root/mysql-server-8032-google-02/sql/set_var.cc`
```c++
struct sys_var_chain {
        sys_var *first;
        sys_var *last;
};

sys_var_chain all_sys_vars = {nullptr, nullptr};
```





可以看到 default_storage_engine 和 max_connections 这两个系统变量的值已经被修改了。有一点需要注意的
是，对于启动选项来说，如果启动选项名由多个单词组成，各个单词之间用短划线 - 或者下划线 _ 连接起来都可
以，但是对应的系统变量之间必须使用下划线 _ 连接起来。







