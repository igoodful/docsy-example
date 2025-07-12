---
title: processlist
description: processlist
date: 2023-10-26
weight: 20000


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

- [processlist](https://it-blog-cn.com/blogs/db/processlist.html#%E4%BA%8C%E3%80%81show-processlist-%E5%8F%82%E6%95%B0%E5%88%86%E6%9E%90)

{{< /alert >}}


## 使用
- `show processlist` :100字符
- `show full processlist` :会显示所有信息，但展现太占空间、不利于批量分析
- `select * from information_schema.processlist`

`processlist`表的位置因版本不同而有所区别：
- 8.0.26：information_schema
- 8.0.32：performance_schema

相关：
- 查询ID：processlist.id
- MySQL内部线程ID：THREAD_ID
- 查询ID对应操作系统的线程ID：THREAD_OS_ID


```sql
ps -Lef | grep THREAD_OS_ID
```

## 表结构

```sql
mysql> show create table information_schema.processlist\G
*************************** 1. row ***************************
       Table: PROCESSLIST
Create Table: CREATE TEMPORARY TABLE `PROCESSLIST` (
  `ID` bigint unsigned NOT NULL DEFAULT '0',
  `USER` varchar(32) NOT NULL DEFAULT '',
  `HOST` varchar(261) NOT NULL DEFAULT '',
  `DB` varchar(64) DEFAULT NULL,
  `COMMAND` varchar(16) NOT NULL DEFAULT '',
  `TIME` int NOT NULL DEFAULT '0',
  `STATE` varchar(64) DEFAULT NULL,
  `INFO` longtext
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb3
1 row in set (0.00 sec)

```

## 8字段
```viz-dot
digraph "processlist" {
        rankdir=TB;
        edge [
                fontsize="12"
        ];
        node [
                shape = octagon;
                fontsize = "12"
        ];
        "processlist" -> "id";
        "processlist" -> "user";
        "processlist" -> "host";
        "processlist" -> "db";
        "processlist" -> "command";
        "processlist" -> "time";
        "processlist" -> "state";
        "processlist" -> "info";


}
```


#### 1. id


#### 2. user


#### 3. host


#### 4. db


#### 5. command


| command         | 说明                                                     | 备注                                      |
|:----------------|:-------------------------------------------------------|:------------------------------------------|
| **Sleep**       | 正在等待客户端向它发送执行语句                           |                                           |
| **Query**       | 该线程正在执行一个语句                                   |                                           |
| Kill            | 正在执行 kill 语句，杀死指定线程                          |                                           |
| Quit            | 该线程正在退出                                           |                                           |
| Shutdown        | 正在关闭服务器                                           |                                           |
| **Connect**     | 一个从节点连上了主节点                                   | 判断是否为从库                            |
| **Connect Out** | 一个从节点正在连主节点                                   | 判断是否为从库                            |
| Table Dump      | 正在发送表的内容到从服务器                               |                                           |
| **Daemon**      | 服务器内部线程，而不是来自客户端的链接                    |                                           |
| **Binlog Dump** | 主节点正在将二进制日志同步到从节点                       | 判断是否有从库连接它，主库或级联的中间节点 |
| Register Slave  | 正在注册从节点                                           |                                           |
| Prepare         | 该线程正在准备一个 Prepared Statement                    |                                           |
| Execute         | 正在执行一个 Prepared Statement                          |                                           |
| Fetch           | 正在从 Prepared Statement 中获取执行结果                 |                                           |
| Long Data       | 正在从 Prepared Statement 中检索 long data               |                                           |
| Close Stmt      | 正在关闭一个 Prepared Statement 对象                     |                                           |
| Reset Stmt      | 正在重置 Prepared Statement                              |                                           |
| Statistics      | 该线程正在生成 server-status 信息                        |                                           |
| Field List      | 正在获取表的列信息                                       |                                           |
| Refresh         | 该线程正在刷表或日志或缓存或重置状态变量或复制服务器信息 |                                           |
| ProcessList     | 该线程正在生成服务器线程相关信息                         |                                           |
| Ping            | 正在处理 server-ping 的请求                              |                                           |
| Init DB         | 该线程正在选取一个默认的数据库                           |                                           |
| Create DB       | 正在执行一个 create-database 的操作                      |                                           |
| Drop DB         | 正在执行一个 drop-database 的操作                        |                                           |
| Debug           | 线程正在生成调试信息                                     |                                           |
| Delayed Insert  | 该线程是一个延迟插入的处理程序                           |                                           |
| Change User     | 正在执行一个 change-user 的操作                          |                                           |
| Set Option      | 正在设置或重置客户端的 statement-execution 选项          |                                           |
| Time            | 未使用                                                   |                                           |



`/root/mysql-server-8032-google-02/sql/sql_parse.cc`
```c++
const std::string Command_names::m_names[] = {
    "Sleep",
    "Quit",
    "Init DB",
    "Query",
    "Field List",
    "Create DB",
    "Drop DB",
    "Refresh",
    "Shutdown",
    "Statistics",
    "Processlist",
    "Connect",
    "Kill",
    "Debug",
    "Ping",
    "Time",
    "Delayed insert",
    "Change user",
    "Binlog Dump",
    "Table Dump",
    "Connect Out",
    "Register Replica",
    "Prepare",
    "Execute",
    "Long Data",
    "Close stmt",
    "Reset stmt",
    "Set option",
    "Fetch",
    "Daemon",
    "Binlog Dump GTID",
    "Reset Connection",
    "clone",
    "Group Replication Data Stream subscription",
    "Error"  // Last command number
};
```


#### 6. time



#### 7. state


| state              | 说明                                                      | 备注     |
|:-------------------|:----------------------------------------------------------|:---------|
| User sleep         | 使用了sleep函数                                           |          |
| Closing tables     | 正在将表中修改的数据刷新到磁盘中，同时正在关闭已经用完的表 | 正常很快 |
| Creating tmp table | 正在创建临时表以存放部分查询结果                          |          |
|                    |                                                           |          |
|                    |                                                           |          |
|                    |                                                           |          |
|                    |                                                           |          |
|                    |                                                           |          |
|                    |                                                           |          |
|                    |                                                           |          |
|                    |                                                           |          |
|                    |                                                           |          |
|                    |                                                           |          |
|                    |                                                           |          |
|                    |                                                           |          |
|                    |                                                           |          |
|                    |                                                           |          |
|                    |                                                           |          |
|                    |                                                           |          |
|                    |                                                           |          |
|                    |                                                           |          |
|                    |                                                           |          |
|                    |                                                           |          |
|                    |                                                           |          |
|                    |                                                           |          |
|                    |                                                           |          |
|                    |                                                           |          |
|                    |                                                           |          |
|                    |                                                           |          |
|                    |                                                           |          |




源码：`/root/mysql-server-8032-google-02/sql/mysqld.cc`
```c++
#define PSI_DOCUMENT_ME ""

/* clang-format off */
PSI_stage_info stage_after_create= { 0, "After create", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_alter_inplace_prepare= { 0, "preparing for alter table", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_alter_inplace= { 0, "altering table", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_alter_inplace_commit= { 0, "committing alter table to storage engine", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_changing_source= { 0, "Changing replication source", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_checking_source_version= { 0, "Checking source version", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_checking_permissions= { 0, "checking permissions", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_cleaning_up= { 0, "cleaning up", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_closing_tables= { 0, "closing tables", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_compressing_gtid_table= { 0, "Compressing gtid_executed table", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_connecting_to_source= { 0, "Connecting to source", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_converting_heap_to_ondisk= { 0, "converting HEAP to ondisk", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_copy_to_tmp_table= { 0, "copy to tmp table", PSI_FLAG_STAGE_PROGRESS, PSI_DOCUMENT_ME};
PSI_stage_info stage_creating_table= { 0, "creating table", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_creating_tmp_table= { 0, "Creating tmp table", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_deleting_from_main_table= { 0, "deleting from main table", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_deleting_from_reference_tables= { 0, "deleting from reference tables", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_discard_or_import_tablespace= { 0, "discard_or_import_tablespace", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_end= { 0, "end", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_executing= { 0, "executing", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_execution_of_init_command= { 0, "Execution of init_command", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_explaining= { 0, "explaining", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_finished_reading_one_binlog_switching_to_next_binlog= { 0, "Finished reading one binlog; switching to next binlog", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_flushing_relay_log_and_source_info_repository= { 0, "Flushing relay log and source info repository.", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_flushing_relay_log_info_file= { 0, "Flushing relay-log info file.", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_freeing_items= { 0, "freeing items", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_fulltext_initialization= { 0, "FULLTEXT initialization", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_init= { 0, "init", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_killing_replica= { 0, "Killing replica", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_logging_slow_query= { 0, "logging slow query", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_making_temp_file_append_before_load_data= { 0, "Making temporary file (append) before replaying LOAD DATA INFILE", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_manage_keys= { 0, "manage keys", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_source_has_sent_all_binlog_to_replica= { 0, "Source has sent all binlog to replica; waiting for more updates", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_opening_tables= { 0, "Opening tables", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_optimizing= { 0, "optimizing", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_preparing= { 0, "preparing", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_purging_old_relay_logs= { 0, "Purging old relay logs", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_query_end= { 0, "query end", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_queueing_source_event_to_the_relay_log= { 0, "Queueing source event to the relay log", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_reading_event_from_the_relay_log= { 0, "Reading event from the relay log", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_registering_replica_on_source= { 0, "Registering replica on source", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_removing_tmp_table= { 0, "removing tmp table", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_rename= { 0, "rename", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_rename_result_table= { 0, "rename result table", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_requesting_binlog_dump= { 0, "Requesting binlog dump", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_searching_rows_for_update= { 0, "Searching rows for update", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_sending_binlog_event_to_replica= { 0, "Sending binlog event to replica", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_setup= { 0, "setup", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_replica_has_read_all_relay_log= { 0, "Replica has read all relay log; waiting for more updates", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_replica_reconnecting_after_failed_binlog_dump_request{ 0, "Reconnecting after a failed binlog dump request", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_replica_reconnecting_after_failed_event_read{ 0, "Reconnecting after a failed source event read", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_replica_reconnecting_after_failed_registration_on_source{ 0, "Reconnecting after a failed registration on source", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_replica_waiting_event_from_coordinator= { 0, "Waiting for an event from Coordinator", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_replica_waiting_for_workers_to_process_queue= { 0, "Waiting for replica workers to process their queues", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_replica_waiting_to_reconnect_after_failed_binlog_dump_request{ 0, "Waiting to reconnect after a failed binlog dump request", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_replica_waiting_to_reconnect_after_failed_event_read{ 0, "Waiting to reconnect after a failed source event read", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_replica_waiting_to_reconnect_after_failed_registration_on_source{ 0, "Waiting to reconnect after a failed registration on source", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_replica_waiting_worker_queue= { 0, "Waiting for Replica Worker queue", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_replica_waiting_worker_to_free_events= { 0, "Waiting for Replica Workers to free pending events", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_replica_waiting_worker_to_release_partition= { 0, "Waiting for Replica Worker to release partition", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_replica_waiting_workers_to_exit= { 0, "Waiting for workers to exit", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_rpl_apply_row_evt_write= { 0, "Applying batch of row changes (write)", PSI_FLAG_STAGE_PROGRESS, PSI_DOCUMENT_ME};
PSI_stage_info stage_rpl_apply_row_evt_update= { 0, "Applying batch of row changes (update)", PSI_FLAG_STAGE_PROGRESS, PSI_DOCUMENT_ME};
PSI_stage_info stage_rpl_apply_row_evt_delete= { 0, "Applying batch of row changes (delete)", PSI_FLAG_STAGE_PROGRESS, PSI_DOCUMENT_ME};
PSI_stage_info stage_statistics= { 0, "statistics", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_sql_thd_waiting_until_delay= { 0, "Waiting until SOURCE_DELAY seconds after source executed event", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_system_lock= { 0, "System lock", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_update= { 0, "update", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_updating= { 0, "updating", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_updating_main_table= { 0, "updating main table", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_updating_reference_tables= { 0, "updating reference tables", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_user_sleep= { 0, "User sleep", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_verifying_table= { 0, "verifying table", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_waiting_for_gtid_to_be_committed= { 0, "Waiting for GTID to be committed", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_waiting_for_handler_commit= { 0, "waiting for handler commit", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_waiting_for_source_to_send_event= { 0, "Waiting for source to send event", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_waiting_for_source_update= { 0, "Waiting for source update", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_waiting_for_relay_log_space= { 0, "Waiting for the replica SQL thread to free relay log space", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_waiting_for_replica_mutex_on_exit= { 0, "Waiting for replica mutex on exit", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_waiting_for_replica_thread_to_start= { 0, "Waiting for replica thread to start", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_waiting_for_table_flush= { 0, "Waiting for table flush", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_waiting_for_the_next_event_in_relay_log= { 0, "Waiting for the next event in relay log", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_waiting_for_the_replica_thread_to_advance_position= { 0, "Waiting for the replica SQL thread to advance position", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_waiting_to_finalize_termination= { 0, "Waiting to finalize termination", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_worker_waiting_for_its_turn_to_commit= { 0, "Waiting for preceding transaction to commit", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_worker_waiting_for_commit_parent= { 0, "Waiting for dependent transaction to commit", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_suspending= { 0, "Suspending", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_starting= { 0, "starting", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_waiting_for_no_channel_reference= { 0, "Waiting for no channel reference.", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_hook_begin_trans= { 0, "Executing hook on transaction begin.", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_binlog_transaction_compress= { 0, "Compressing transaction changes.", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_binlog_transaction_decompress= { 0, "Decompressing transaction changes.", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_rpl_failover_fetching_source_member_details= { 0, "Fetching source member details from connected source", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_rpl_failover_updating_source_member_details= { 0, "Updating fetched source member details on receiver", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_rpl_failover_wait_before_next_fetch= { 0, "Wait before trying to fetch next membership changes from source", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_communication_delegation= { 0, "Connection delegated to Group Replication", 0, PSI_DOCUMENT_ME};
/* clang-format on */
```




`/root/mysql-server-8032-google-02/sql/events.cc`

```c++
PSI_stage_info stage_waiting_on_empty_queue        = {0, "Waiting on empty queue", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_waiting_for_next_activation   = {0, "Waiting for next activation", 0, PSI_DOCUMENT_ME};
PSI_stage_info stage_waiting_for_scheduler_to_stop = {0, "Waiting for the scheduler to stop", 0, PSI_DOCUMENT_ME};
```


```c++
typedef unsigned int PSI_stage_key;

struct PSI_stage_info_v1 {
        /** The registered stage key. */
        PSI_stage_key m_key{0};
        /** The name of the stage instrument to register. */
        const char *m_name{nullptr};
        /**
          The flags of the stage instrument to register.
          @sa PSI_FLAG_PROGRESS
        */
        unsigned int m_flags{0};
        /** Documentation. */
        const char *m_documentation{nullptr};
};
typedef struct PSI_stage_info_v1 PSI_stage_info_v1;

typedef struct PSI_stage_info_v1 PSI_stage_info;

```


#### 8. info



