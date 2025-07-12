---
title: 2.4 变量
description: variable
date: 2025-06-16
weight: 4000
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{<alert>}}

配置文件

{{</alert>}}



```viz-dot
digraph "变量" {
rankdir = TB;
edge [
fontsize="12"
];
node [
shape = octagon;
fontsize = "12"
 ];

"变量" -> "全局变量";
"变量" -> "会话变量";

}
```






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






## my.cnf


```toml
[server]
(具体的启动选项...)
[mysqld]
(具体的启动选项...)
[mysqld_safe]
(具体的启动选项...)
[client]
(具体的启动选项...)
[mysql]
(具体的启动选项...)
[mysqladmin]
(具体的启动选项...)



```
与在命令行中指定启动选项不同的是，配置文件中的启动选项被划分为若干个组，每个组有一个组名，用中括号
[] 扩起来，像这样：像这个配置文件里就定义了许多个组，组名分别是 server 、 mysqld 、 mysqld_safe 、 client 、 mysql 、
mysqladmin 。每个组下边可以定义若干个启动选项



`2024-07-11T22:09:36 [mysql@node70 /home/mysql] $ cat .my.cnf `

```toml
[client]
user=root
password=root
port=3306
socket=/home/work/mysql_3306/tmp/mysql.sock

```


如果同一个启动选项既出现在命令行中，又出现在配置文件中，那么以命令行中的启

动选项为准！比如我们在配置文件中写了：

[server]

default-storage-engine=InnoDB

而我们的启动命令是：

mysql.server start --default-storage-engine=MyISAM

那最后 default-storage-engine 的值就是 MyISAM ！



## 核心参数



```viz-dot
digraph "核心参数" {

rankdir = LR;
edge [
 fontsize="12"
];

node [
shape = octagon;
fontsize = "12"
];

"核心参数" -> "innodb_buffer_pool_size";
"核心参数" -> "innodb_sort_buffer_size";
"核心参数" -> "innodb_log_file_size";
"核心参数" -> "innodb_max_undo_log_size";
"核心参数" -> "max_binlog_size";
"核心参数" -> "sort_buffer_size";
"核心参数" -> "join_buffer_size";
"核心参数" -> "init_connect";
"核心参数" -> "max_allowed_packet";
"核心参数" -> "max_connections";
"核心参数" -> "max_user_connections";
"核心参数" -> "open_files_limit";
"核心参数" -> "innodb_thread_concurrency";
"核心参数" -> "back_log";
"核心参数" -> "connect_timeout";
"核心参数" -> "wait_timeout";
"核心参数" -> "interactive_timeout";
"核心参数" -> "slave_net_timeout";
"核心参数" -> "lock_wait_timeout";
"核心参数" -> "innodb_lock_wait_timeout";
"核心参数" -> "long_query_time";
"核心参数" -> "max_connect_errors";
"核心参数" -> "expire_logs_days";
"核心参数" -> "sql_mode";



"核心参数" -> "sync_binlog";
"核心参数" -> "innodb_flush_log_at_trx_commit";
"核心参数" -> "default_storage_engine";
"核心参数" -> "innodb_file_per_table";
"核心参数" -> "binlog_format";
"核心参数" -> "binlog_row_image";
"核心参数" -> "character_set_server";
"核心参数" -> "lower_case_table_names";
"核心参数" -> "innodb_adaptive_hash_index";
"核心参数" -> "internal_tmp_mem_storage_engine";
"核心参数" -> "time_zone";
"核心参数" -> "transaction_isolation";
"核心参数" -> "log_slave_updates";


}


```










