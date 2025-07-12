---
title: 搭建单主MGR集群
description: group replica
date: 2024-11-04
weight: 20000
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{<alert color="danger" title="注意" >}}

MySQL 实例最多支持 127 个撤消表空间，其中包括 MySQL 实例初始化时创建的两个默认撤消表空间。

{{</alert>}}


## 一、主节点

## 1. 配置MGR
```ini
[mysqld]
innodb_redo_log_capacity = 20971520
basedir = /root/mysql_8032
datadir = /root/mysql_8032/data
port = 8032
socket = /root/mysql_8032/tmp/mysql.sock
server-id = 19716134
tmpdir = /root/mysql_8032/tmp
general-log-file = /root/mysql_8032/log/general.log
slow-query-log-file = /root/mysql_8032/log/slow.log
pid-file = /root/mysql_8032/tmp/mysqld.pid
log-error = /root/mysql_8032/log/error.log
slow_query_log = on
long_query_time = 1
log_bin = /root/mysql_8032/log/bin
relay_log = /root/mysql_8032/log/relay

binlog_format = row
binlog_cache_size = 2097152
binlog_checksum = NONE
binlog_expire_logs_seconds = 1296000
sync_binlog = 1
innodb_flush_log_at_trx_commit = 1
innodb_file_per_table = 1
gtid_mode = on
enforce_gtid_consistency = on
log_replica_updates = 1
binlog_rows_query_log_events = ON
binlog_transaction_dependency_tracking = WRITESET
innodb_buffer_pool_size = 2G
innodb_buffer_pool_instances = 8
innodb_parallel_read_threads = 1
innodb_read_io_threads = 1
innodb_write_io_threads = 1
innodb_thread_concurrency = 4
innodb_change_buffering = all
innodb_data_file_path = ibdata1:1024M:autoextend
innodb_doublewrite = 1
innodb_flush_method = O_DIRECT
innodb_io_capacity = 20000
innodb_io_capacity_max = 40000
innodb_log_buffer_size = 33554432
innodb_log_file_size = 1G
innodb_log_files_in_group = 8
innodb_max_undo_log_size = 2G
innodb_monitor_enable = all
innodb_numa_interleave = ON
innodb_online_alter_log_max_size = 2G
innodb_open_files = 40960
innodb_page_cleaners = 8

innodb_print_all_deadlocks = on
innodb_purge_rseg_truncate_frequency = 16
innodb_rollback_on_timeout = ON
innodb_temp_data_file_path = ibtmp1:512M:autoextend:max:30720M
innodb_undo_log_truncate = ON
innodb_use_native_aio = ON
federated = 1
transaction_isolation = READ-COMMITTED
init_connect = SET NAMES utf8
character_set_server = utf8mb4
collation_server = utf8mb4_0900_ai_ci
default_authentication_plugin = mysql_native_password
default-time-zone = +08:00
local_infile = OFF
secure_file_priv = ''
ssl = off
lower_case_table_names = 1
skip_replica_start = 1
innodb_lock_wait_timeout = 20
wait_timeout = 31536000
innodb_adaptive_hash_index = OFF
sql_require_primary_key = ON
plugin-load = rpl_semi_sync_master=semisync_master.so;rpl_semi_sync_slave=semisync_slave.so
internal_tmp_mem_storage_engine = MEMORY

max_allowed_packet = 134217728
max_connect_errors = 18446744073709500000
max_connections = 4000
max_heap_table_size = 536870912
max_prepared_stmt_count = 1048576
lock_wait_timeout = 5
interactive_timeout = 31536000

mysqlx = 0
back_log = 5000
group_concat_max_len = 102400
join_buffer_size = 2097152
key_buffer_size = 2097152
sort_buffer_size = 8388608
log_bin_trust_function_creators = 1
log_timestamps = SYSTEM
net_read_timeout = 10000
net_write_timeout = 10000
open_files_limit = 1024000
read_rnd_buffer_size = 16777216
performance-schema-consumer-events_stages_current = on
performance-schema-consumer-events_stages_history = on
performance-schema-consumer-events_statements_current = on
performance-schema-consumer-events_statements_history = on
performance-schema-consumer-events_transactions_current = on
performance-schema-consumer-events_transactions_history = on
performance-schema-consumer-events_waits_current = on
performance-schema-consumer-events_waits_history = on
performance-schema-instrument = %=ON
relay_log_recovery = ON
replica_parallel_type = LOGICAL_CLOCK
replica_parallel_workers = 32
replica_pending_jobs_size_max = 134217728
replica_preserve_commit_order = 1
skip_external_locking = 1
skip_name_resolve = 1
table_definition_cache = 10240
table_open_cache = 20480
table_open_cache_instances = 16
thread_cache_size = 3000
tmp_table_size = 536870912

plugin_load_add='group_replication.so'
group_replication_group_name="ce9be252-2b71-11e6-b8f4-00212844f888"
group_replication_local_address= "172.17.138.136:18032"
group_replication_group_seeds= "172.17.138.136:18032,172.17.136.101:18032,172.17.137.95:18032"
report-host=172.17.138.136
disabled_storage_engines="MyISAM,BLACKHOLE,FEDERATED,ARCHIVE,MEMORY"

```

### 2. 创建复制账号
> 启动该节点后登录
>
>确保各个节点的server-id和 server_uuid 互不相同

```sql
install plugin group_replication soname 'group_replication.so';
show plugins;

set session sql_log_bin=0;
create user root@'%' identified with mysql_native_password by 'root';
grant backup_admin, replication slave on *.* to root@`%`;
set session sql_log_bin=1;
change master to master_user='root', master_password='root' for channel 'group_replication_recovery';
```

### 3. 启动MGR

```sql
set global group_replication_bootstrap_group=on;
start group_replication;
set global group_replication_bootstrap_group=off;
```

### 4. 查看MGR

```sql
select * from performance_schema.replication_group_members;
```


## 二、从节点


## 1. 配置MGR
```ini
[mysqld]
innodb_redo_log_capacity = 20971520
basedir = /root/mysql_8032
datadir = /root/mysql_8032/data
port = 8032
socket = /root/mysql_8032/tmp/mysql.sock
server-id = 101
tmpdir = /root/mysql_8032/tmp
general-log-file = /root/mysql_8032/log/general.log
slow-query-log-file = /root/mysql_8032/log/slow.log
pid-file = /root/mysql_8032/tmp/mysqld.pid
log-error = /root/mysql_8032/log/error.log
slow_query_log = on
long_query_time = 1
log_bin = /root/mysql_8032/log/bin
relay_log = /root/mysql_8032/log/relay

binlog_format = row
binlog_cache_size = 2097152
binlog_checksum = NONE
binlog_expire_logs_seconds = 1296000
sync_binlog = 1
innodb_flush_log_at_trx_commit = 1
innodb_file_per_table = 1
gtid_mode = on
enforce_gtid_consistency = on
log_replica_updates = 1
binlog_rows_query_log_events = ON
binlog_transaction_dependency_tracking = WRITESET
innodb_buffer_pool_size = 2G
innodb_buffer_pool_instances = 8
innodb_parallel_read_threads = 1
innodb_read_io_threads = 1
innodb_write_io_threads = 1
innodb_thread_concurrency = 4
innodb_change_buffering = all
innodb_data_file_path = ibdata1:1024M:autoextend
innodb_doublewrite = 1
innodb_flush_method = O_DIRECT
innodb_io_capacity = 20000
innodb_io_capacity_max = 40000
innodb_log_buffer_size = 33554432
innodb_log_file_size = 1G
innodb_log_files_in_group = 8
innodb_max_undo_log_size = 2G
innodb_monitor_enable = all
innodb_numa_interleave = ON
innodb_online_alter_log_max_size = 2G
innodb_open_files = 40960
innodb_page_cleaners = 8

innodb_print_all_deadlocks = on
innodb_purge_rseg_truncate_frequency = 16
innodb_rollback_on_timeout = ON
innodb_temp_data_file_path = ibtmp1:512M:autoextend:max:30720M
innodb_undo_log_truncate = ON
innodb_use_native_aio = ON
federated = 1
transaction_isolation = READ-COMMITTED
init_connect = SET NAMES utf8
character_set_server = utf8mb4
collation_server = utf8mb4_0900_ai_ci
default_authentication_plugin = mysql_native_password
default-time-zone = +08:00
local_infile = OFF
secure_file_priv = ''
ssl = off
lower_case_table_names = 1
skip_replica_start = 1
innodb_lock_wait_timeout = 20
wait_timeout = 31536000
innodb_adaptive_hash_index = OFF
sql_require_primary_key = ON
plugin-load = rpl_semi_sync_master=semisync_master.so;rpl_semi_sync_slave=semisync_slave.so
internal_tmp_mem_storage_engine = MEMORY

max_allowed_packet = 134217728
max_connect_errors = 18446744073709500000
max_connections = 4000
max_heap_table_size = 536870912
max_prepared_stmt_count = 1048576
lock_wait_timeout = 5
interactive_timeout = 31536000

mysqlx = 0
back_log = 5000
group_concat_max_len = 102400
join_buffer_size = 2097152
key_buffer_size = 2097152
sort_buffer_size = 8388608
log_bin_trust_function_creators = 1
log_timestamps = SYSTEM
net_read_timeout = 10000
net_write_timeout = 10000
open_files_limit = 1024000
read_rnd_buffer_size = 16777216
performance-schema-consumer-events_stages_current = on
performance-schema-consumer-events_stages_history = on
performance-schema-consumer-events_statements_current = on
performance-schema-consumer-events_statements_history = on
performance-schema-consumer-events_transactions_current = on
performance-schema-consumer-events_transactions_history = on
performance-schema-consumer-events_waits_current = on
performance-schema-consumer-events_waits_history = on
performance-schema-instrument = %=ON
relay_log_recovery = ON
replica_parallel_type = LOGICAL_CLOCK
replica_parallel_workers = 32
replica_pending_jobs_size_max = 134217728
replica_preserve_commit_order = 1
skip_external_locking = 1
skip_name_resolve = 1
table_definition_cache = 10240
table_open_cache = 20480
table_open_cache_instances = 16
thread_cache_size = 3000
tmp_table_size = 536870912

plugin_load_add='group_replication.so'
group_replication_group_name="ce9be252-2b71-11e6-b8f4-00212844f888"
group_replication_local_address= "172.17.136.101:18032"
group_replication_group_seeds= "172.17.138.136:18032,172.17.136.101:18032,172.17.137.95:18032"
report-host=172.17.136.101


```

### 2. 创建复制账号
> 启动该节点后登录

```sql
set session sql_log_bin=0;
create user root@'%' identified with mysql_native_password by 'root';
grant backup_admin, replication slave on *.* to root@`%`;
set session sql_log_bin=1;
change master to master_user='root', master_password='root' for channel 'group_replication_recovery';
```

### 3. 启动MGR

```sql
change master to master_user='root', master_password='root' for channel 'group_replication_recovery';
start group_replication;
```

### 4. 查看MGR

```sql
select * from performance_schema.replication_group_members;
```


## 三、查看状态


### performance_schema.replication_group_members
```sql
[root@127.0.0.1:8032 15:57:45((none))]$ select * from performance_schema.replication_group_members;
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
| CHANNEL_NAME              | MEMBER_ID                            | MEMBER_HOST    | MEMBER_PORT | MEMBER_STATE | MEMBER_ROLE | MEMBER_VERSION | MEMBER_COMMUNICATION_STACK |
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
| group_replication_applier | 80ca4c8a-1649-11ef-ba14-00163eebf101 | 172.17.136.101 |        8032 | ONLINE       | SECONDARY   | 8.0.32         | XCom                       |
| group_replication_applier | 80ca4c8a-1649-11ef-ba14-00163eebf904 | 172.17.138.136 |        8032 | ONLINE       | PRIMARY     | 8.0.32         | XCom                       |
| group_replication_applier | 80ca4c8a-1649-11ef-ba14-00163eebf995 | 172.17.137.95  |        8032 | ONLINE       | SECONDARY   | 8.0.32         | XCom                       |
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
3 rows in set (0.00 sec)


```


## 四、单主切换为多主

```sql
-- 所有节点都执行
stop group_replication;
set global group_replication_single_primary_mode=off;
set global group_replication_enforce_update_everywhere_checks=on;


-- 随便选择某个节点执行
set global group_replication_bootstrap_group=on;
start group_replication;
set global group_replication_bootstrap_group=off;

-- 其他节点执行
start group_replication;
select * from performance_schema.replication_group_members;
show status like  'group_replication_primary_member';
```















