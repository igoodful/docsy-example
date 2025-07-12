---
title: errorlog
description: errorlog
date: 2023-10-30
weight: 10000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}


{{< /alert >}}



### mysqld启动信息
```sql
2024-01-27T15:50:19.172495+08:00 0 [Warning] [MY-011068] [Server] The syntax 'expire-logs-days' is deprecated and will be removed in a future release. Please use binlog_expire_logs_seconds instead.
2024-01-27T15:50:19.172634+08:00 0 [Warning] [MY-011068] [Server] The syntax 'log_slave_updates' is deprecated and will be removed in a future release. Please use log_replica_updates instead.
2024-01-27T15:50:19.172684+08:00 0 [Warning] [MY-011069] [Server] The syntax '--master-info-repository' is deprecated and will be removed in a future release.
2024-01-27T15:50:19.172750+08:00 0 [Warning] [MY-011069] [Server] The syntax '--relay-log-info-repository' is deprecated and will be removed in a future release.
2024-01-27T15:50:19.172783+08:00 0 [Warning] [MY-011068] [Server] The syntax 'skip_slave_start' is deprecated and will be removed in a future release. Please use skip_replica_start instead.
2024-01-27T15:50:19.172814+08:00 0 [Warning] [MY-011068] [Server] The syntax 'slave_parallel_type' is deprecated and will be removed in a future release. Please use replica_parallel_type instead.
2024-01-27T15:50:19.172834+08:00 0 [Warning] [MY-011068] [Server] The syntax 'slave_parallel_workers' is deprecated and will be removed in a future release. Please use replica_parallel_workers instead.
2024-01-27T15:50:19.172857+08:00 0 [Warning] [MY-011068] [Server] The syntax 'slave_pending_jobs_size_max' is deprecated and will be removed in a future release. Please use replica_pending_jobs_size_max instead.
2024-01-27T15:50:19.172878+08:00 0 [Warning] [MY-011068] [Server] The syntax 'slave_preserve_commit_order' is deprecated and will be removed in a future release. Please use replica_preserve_commit_order instead.
2024-01-27T15:50:19.172903+08:00 0 [Warning] [MY-011069] [Server] The syntax '--slave-rows-search-algorithms' is deprecated and will be removed in a future release.
2024-01-27T15:50:19.172929+08:00 0 [Warning] [MY-011068] [Server] The syntax '--ssl=off' is deprecated and will be removed in a future release. Please use --tls-version=invalid instead.
2024-01-27T15:50:19.172969+08:00 0 [Warning] [MY-011069] [Server] The syntax '--transaction-write-set-extraction' is deprecated and will be removed in a future release.
2024-01-27T15:50:19.173310+08:00 0 [System] [MY-010116] [Server] /root/local/mysql_8026/bin/mysqld (mysqld 8.0.26-debug) starting as process 230950
2024-01-27T15:50:19.242811+08:00 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2024-01-27T15:50:21.256862+08:00 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2024-01-27T15:50:23.977312+08:00 0 [Warning] [MY-010918] [Repl] 'rpl_semi_sync_master' is deprecated and will be removed in a future release. Please use rpl_semi_sync_source instead.
2024-01-27T15:50:23.978135+08:00 0 [Warning] [MY-010918] [Repl] 'rpl_semi_sync_slave' is deprecated and will be removed in a future release. Please use rpl_semi_sync_replica instead.
2024-01-27T15:50:24.167326+08:00 0 [System] [MY-010229] [Server] Starting XA crash recovery...
2024-01-27T15:50:24.189417+08:00 0 [System] [MY-010232] [Server] XA crash recovery finished.
2024-01-27T15:50:24.673308+08:00 0 [System] [MY-010931] [Server] /root/local/mysql_8026/bin/mysqld: ready for connections. Version: '8.0.26-debug'  socket: '/root/local/mysql_8026/tmp/mysql.sock'  port: 8026  Source distribution.
```
1. `/root/local/mysql_8026/bin/mysqld (mysqld 8.0.26-debug) starting as process 230950` mysqld的进程id、版本号、可执行文件路径

2. `[InnoDB] InnoDB initialization has started.`和`[InnoDB] InnoDB initialization has ended.` innodb的启动时间

3. `Starting XA crash recovery...`和`XA crash recovery finished.` XA恢复时间

4. `/root/local/mysql_8026/bin/mysqld: ready for connections. Version: '8.0.26-debug'  socket: '/root/local/mysql_8026/tmp/mysql.sock'  port: 8026` mysqld服务启动完成时间、可执行文件路径、版本号、socket文件路径、端口号




### kill -15
```sql
2024-01-27T21:59:18.083324+08:00 0 [System] [MY-013172] [Server] Received SHUTDOWN from user <via user signal>. Shutting down mysqld (Version: 8.0.26-debug).
2024-01-27T21:59:20.292080+08:00 0 [Warning] [MY-010909] [Server] /root/local/mysql_8026/bin/mysqld: Forcing close of thread 8  user: 'root'.
2024-01-27T21:59:22.604302+08:00 0 [System] [MY-010910] [Server] /root/local/mysql_8026/bin/mysqld: Shutdown complete (mysqld 8.0.26-debug)  Source distribution.

User time 24.23, System time 3.56
                              Maximum resident set size 1339760, Integral resident set size 0
Non-physical pagefaults 108606, Physical pagefaults 2, Swaps 0
Blocks in 219248 out 143249, Messages in 0 out 0, Signals 0
Voluntary context switches 247841, Involuntary context switches 307

```


### kill
```sql


User time 5.96, System time 1.87
                              Maximum resident set size 1339760, Integral resident set size 0
Non-physical pagefaults 71273, Physical pagefaults 26, Swaps 0
Blocks in 275083 out 138201, Messages in 0 out 0, Signals 0
Voluntary context switches 31635, Involuntary context switches 93

```

### kill -11 生成coredump文件

```sql
没有错误日志，生成coredump文件
```





