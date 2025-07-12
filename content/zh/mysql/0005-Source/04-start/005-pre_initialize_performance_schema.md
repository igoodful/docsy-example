---
title: 05. pre_initialize_performance_schema
description: pre_initialize_performance_schema
date: 2023-10-30
weight: 50


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
8.0.32
{{< /alert >}}


## 整体情况
```viz-dot
digraph "PFS_builtin_memory_class" {
        rankdir=LR;
        edge [
                fontsize="11"
                ];
        node [
                shape = rect;
                fontsize = "11"
                ];
        "pre_initialize_performance_schema" -> "record_main_thread_id()";
        "pre_initialize_performance_schema" -> "pfs_initialized = false";
        "pre_initialize_performance_schema" -> "init_all_builtin_memory_class()";
        "pre_initialize_performance_schema" -> "PFS_table_stat::g_reset_template.reset()";
        "pre_initialize_performance_schema" -> "global_idle_stat.reset()" ;
        "pre_initialize_performance_schema" -> "global_table_io_stat.reset()" ;
        "pre_initialize_performance_schema" -> "global_table_lock_stat.reset()" ;
        "pre_initialize_performance_schema" -> "g_histogram_pico_timers.init()" ;
        "pre_initialize_performance_schema" -> "global_statements_histogram.reset()" ;
        "pre_initialize_performance_schema" -> "THR_PFS = nullptr" ;
}
```





```sql
mysql> show create table mutex_instances\G
*************************** 1. row ***************************
       Table: mutex_instances
Create Table: CREATE TABLE `mutex_instances` (
  `NAME` varchar(128) NOT NULL,
  `OBJECT_INSTANCE_BEGIN` bigint unsigned NOT NULL,
  `LOCKED_BY_THREAD_ID` bigint unsigned DEFAULT NULL,
  PRIMARY KEY (`OBJECT_INSTANCE_BEGIN`),
  KEY `NAME` (`NAME`),
  KEY `LOCKED_BY_THREAD_ID` (`LOCKED_BY_THREAD_ID`)
) ENGINE=PERFORMANCE_SCHEMA DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)


mysql> select * from mutex_instances where LOCKED_BY_THREAD_ID is not null;
+-----------------------------------------------+-----------------------+---------------------+
| NAME                                          | OBJECT_INSTANCE_BEGIN | LOCKED_BY_THREAD_ID |
+-----------------------------------------------+-----------------------+---------------------+
| wait/synch/mutex/sql/LOCK_compress_gtid_table |              66502240 |                  49 |
| wait/synch/mutex/sql/LOCK_event_queue         |             135920096 |                  47 |
+-----------------------------------------------+-----------------------+---------------------+
2 rows in set (0.00 sec)

```

{{<alert>}}

- `static my_thread_t main_thread_id;` 初始化全局静态变量
- `pfs_initialized = false;` 初始化全局变量


{{</alert>}}


## pre_initialize_performance_schema
> Performance Schema数据库的存储引擎是内存
```c++
void pre_initialize_performance_schema() {
        // 记录主线程ID。在多线程环境下，每个线程都有一个唯一的线程ID。这个函数可能会在 Performance Schema 的初始化过程中用到，以确保主线程的信息被正确记录和跟踪
        // 调用pthread库的pthread_self函数获取
        record_main_thread_id();
        // 这一行代码将变量 pfs_initialized 设为 false，表明 Performance Schema 尚未初始化。在 Performance Schema 初始化完成后，这个变量会被设置为 true，表示 PFS 已经准备好被使用
        pfs_initialized = false;
        //  这个函数用于初始化所有内置的内存类别。Performance Schema 在内部使用内存来存储性能数据，因此在使用 Performance Schema 之前，需要初始化一些内存类别以备后用
        init_all_builtin_memory_class();
        // 这一行代码调用了 PFS_table_stat 类的 reset() 函数，用于重置 PFS 中的表统计信息。这可能在 PFS 初始化之前清理旧的数据，以确保性能数据从一个干净的状态开始收集
        PFS_table_stat::g_reset_template.reset();
        // 用于重置全局的空闲统计信息。Performance Schema 可能会跟踪数据库中各种资源的空闲情况，这个操作可能是为了在初始化之前清理旧的空闲统计数据
        global_idle_stat.reset();
        // 这一行代码用于重置全局的表I/O统计信息。这可能包括表的读取和写入操作的计数，以及相关的性能指标
        global_table_io_stat.reset();
        // 用于重置全局表锁统计信息。Performance Schema 可能会跟踪数据库中各种锁的使用情况，这个操作可能是为了在初始化之前清理旧的锁统计数据
        global_table_lock_stat.reset();
        // 用于初始化全局直方图（histogram）的微秒级定时器。这些定时器可能会用于测量某些操作的执行时间
        g_histogram_pico_timers.init();
        // 这个函数用于重置全局的语句直方图。在 Performance Schema 中，语句直方图可以跟踪不同类型语句的执行情况，包括执行次数和执行时间
        global_statements_histogram.reset();

        /*
          There is no automatic cleanup. Please either use:
          - my_thread_end()
          - or PSI_server->delete_current_thread()
          in the instrumented code, to explicitly cleanup the instrumentation.
        */

        // THR_PFS 设置为 nullptr，表明当前线程不处于 Performance Schema 的仪器中
        THR_PFS = nullptr;
}
```


### 1. record_main_thread_id
> 主线程id为全局静态变量
```c++
static my_thread_t main_thread_id;

void record_main_thread_id() {
        main_thread_id = my_thread_self();
}
```


```c++
static inline my_thread_t my_thread_self() {
#ifdef _WIN32
        return GetCurrentThreadId();
#else
        return pthread_self();
#endif
}
```


```c++
#   define __THROW	throw ()
extern pthread_t pthread_self (void) __THROW __attribute__ ((__const__));
```
不会抛出异常，并且是一个纯函数:

`__THROW`作用：
- 用于指示函数不会引发异常。在一些编译器中，这个宏可能会影响函数的异常处理机制。
- 当函数被声明为`__THROW`时，编译器会假定该函数不会抛出异常，从而可以进行一些优化。
- 如果函数声明中没有`__THROW`，则编译器会认为该函数可能会引发异常，并在必要时生成相应的代码来处理异常情况。

`__attribute__ ((__const__))`优化作用：
- 用于指示函数是一个纯函数，即该函数没有副作用，不会修改全局变量或静态变量的值，并且只取决于它的参数
- 当函数被声明为纯函数时，编译器可以进行更多的优化，例如对函数的返回值进行缓存，减少对函数的重复调用
- 如果函数没有`__attribute__ ((__const__))`修饰，则编译器不会认为该函数是一个纯函数，可能不会进行相应的优化




### 2. init_all_builtin_memory_class
> performance_schema存储引擎

以全局变量形式初始化的就是下面这些表的内存结构
#### performance_schema.tables
```sql
[dbscale_internal@172.17.134.77 08:15:35((none))]$use performance_schema
Database changed

[dbscale_internal@172.17.134.77 08:15:41(performance_schema)]$show tables;
+------------------------------------------------------+
| Tables_in_performance_schema                         |
+------------------------------------------------------+
| accounts                                             |
| binary_log_transaction_compression_stats             |
| cond_instances                                       |
| data_lock_waits                                      |
| data_locks                                           |
| error_log                                            |
| events_errors_summary_by_account_by_error            |
| events_errors_summary_by_host_by_error               |
| events_errors_summary_by_thread_by_error             |
| events_errors_summary_by_user_by_error               |
| events_errors_summary_global_by_error                |
| events_stages_current                                |
| events_stages_history                                |
| events_stages_history_long                           |
| events_stages_summary_by_account_by_event_name       |
| events_stages_summary_by_host_by_event_name          |
| events_stages_summary_by_thread_by_event_name        |
| events_stages_summary_by_user_by_event_name          |
| events_stages_summary_global_by_event_name           |
| events_statements_current                            |
| events_statements_histogram_by_digest                |
| events_statements_histogram_global                   |
| events_statements_history                            |
| events_statements_history_long                       |
| events_statements_summary_by_account_by_event_name   |
| events_statements_summary_by_digest                  |
| events_statements_summary_by_host_by_event_name      |
| events_statements_summary_by_program                 |
| events_statements_summary_by_thread_by_event_name    |
| events_statements_summary_by_user_by_event_name      |
| events_statements_summary_global_by_event_name       |
| events_transactions_current                          |
| events_transactions_history                          |
| events_transactions_history_long                     |
| events_transactions_summary_by_account_by_event_name |
| events_transactions_summary_by_host_by_event_name    |
| events_transactions_summary_by_thread_by_event_name  |
| events_transactions_summary_by_user_by_event_name    |
| events_transactions_summary_global_by_event_name     |
| events_waits_current                                 |
| events_waits_history                                 |
| events_waits_history_long                            |
| events_waits_summary_by_account_by_event_name        |
| events_waits_summary_by_host_by_event_name           |
| events_waits_summary_by_instance                     |
| events_waits_summary_by_thread_by_event_name         |
| events_waits_summary_by_user_by_event_name           |
| events_waits_summary_global_by_event_name            |
| file_instances                                       |
| file_summary_by_event_name                           |
| file_summary_by_instance                             |
| global_status                                        |
| global_variables                                     |
| host_cache                                           |
| hosts                                                |
| innodb_redo_log_files                                |
| keyring_component_status                             |
| keyring_keys                                         |
| log_status                                           |
| memory_summary_by_account_by_event_name              |
| memory_summary_by_host_by_event_name                 |
| memory_summary_by_thread_by_event_name               |
| memory_summary_by_user_by_event_name                 |
| memory_summary_global_by_event_name                  |
| metadata_locks                                       |
| mutex_instances                                      |
| objects_summary_global_by_type                       |
| performance_timers                                   |
| persisted_variables                                  |
| prepared_statements_instances                        |
| processlist                                          |
| replication_applier_configuration                    |
| replication_applier_filters                          |
| replication_applier_global_filters                   |
| replication_applier_status                           |
| replication_applier_status_by_coordinator            |
| replication_applier_status_by_worker                 |
| replication_asynchronous_connection_failover         |
| replication_asynchronous_connection_failover_managed |
| replication_connection_configuration                 |
| replication_connection_status                        |
| replication_group_member_stats                       |
| replication_group_members                            |
| rwlock_instances                                     |
| session_account_connect_attrs                        |
| session_connect_attrs                                |
| session_status                                       |
| session_variables                                    |
| setup_actors                                         |
| setup_consumers                                      |
| setup_instruments                                    |
| setup_objects                                        |
| setup_threads                                        |
| socket_instances                                     |
| socket_summary_by_event_name                         |
| socket_summary_by_instance                           |
| status_by_account                                    |
| status_by_host                                       |
| status_by_thread                                     |
| status_by_user                                       |
| table_handles                                        |
| table_io_waits_summary_by_index_usage                |
| table_io_waits_summary_by_table                      |
| table_lock_waits_summary_by_table                    |
| threads                                              |
| tls_channel_status                                   |
| user_defined_functions                               |
| user_variables_by_thread                             |
| users                                                |
| variables_by_thread                                  |
| variables_info                                       |
+------------------------------------------------------+
111 rows in set (0.00 sec)

```


#### 创建`75`个全局对象
> performance_schema.tables中对应的表结构对象
```viz-dot
digraph "PFS_builtin_memory_class" {
        rankdir=LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = rect;
                fontsize = "12"
                ];
        "memory/performance_schema/" -> "mutex_instances";
        "memory/performance_schema/" -> "rwlock_instances";
        "memory/performance_schema/" -> "cond_instances";
        "memory/performance_schema/" -> "file_instances";
        "memory/performance_schema/" -> "socket_instances";
        "memory/performance_schema/" -> "metadata_locks";
        "memory/performance_schema/" -> "file_handle";
        "memory/performance_schema/" -> "accounts";
        "memory/performance_schema/" -> "events_waits_summary_by_account_by_event_name";
        "memory/performance_schema/" -> "events_stages_summary_by_account_by_event_name";
        "memory/performance_schema/" -> "events_statements_summary_by_account_by_event_name";
        "memory/performance_schema/" -> "events_transactions_summary_by_account_by_event_name";
        "memory/performance_schema/" -> "events_errors_summary_by_account_by_error";
        "memory/performance_schema/" -> "memory_summary_by_account_by_event_name";
        "memory/performance_schema/" -> "events_stages_summary_global_by_event_name";
        "memory/performance_schema/" -> "events_statements_summary_global_by_event_name";
        "memory/performance_schema/" -> "memory_summary_global_by_event_name";
        "memory/performance_schema/" -> "events_errors_summary_global_by_error";
        "memory/performance_schema/" -> "hosts";
        "memory/performance_schema/" -> "events_waits_summary_by_host_by_event_name";
        "memory/performance_schema/" -> "events_stages_summary_by_host_by_event_name";
        "memory/performance_schema/" -> "events_statements_summary_by_host_by_event_name";
        "memory/performance_schema/" -> "events_transactions_summary_by_host_by_event_name";
        "memory/performance_schema/" -> "events_errors_summary_by_host_by_error";
        "memory/performance_schema/" -> "memory_summary_by_host_by_event_name";
        "memory/performance_schema/" -> "threads";
        "memory/performance_schema/" -> "events_waits_summary_by_thread_by_event_name";
        "memory/performance_schema/" -> "events_stages_summary_by_thread_by_event_name";
        "memory/performance_schema/" -> "events_statements_summary_by_thread_by_event_name";
        "memory/performance_schema/" -> "events_transactions_summary_by_thread_by_event_name";
        "memory/performance_schema/" -> "events_errors_summary_by_thread_by_error";
        "memory/performance_schema/" -> "memory_summary_by_thread_by_event_name";
        "memory/performance_schema/" -> "events_waits_history";
        "memory/performance_schema/" -> "events_stages_history";
        "memory/performance_schema/" -> "events_statements_history";
        "memory/performance_schema/" -> "events_statements_history.digest_text";
        "memory/performance_schema/" -> "events_statements_history.sql_text";
        "memory/performance_schema/" -> "events_statements_current";
        "memory/performance_schema/" -> "events_statements_current.digest_text";
        "memory/performance_schema/" -> "events_statements_current.sql_text";
        "memory/performance_schema/" -> "events_transactions_history";
        "memory/performance_schema/" -> "session_connect_attrs";
        "memory/performance_schema/" -> "users";
        "memory/performance_schema/" -> "events_waits_summary_by_user_by_event_name";
        "memory/performance_schema/" -> "events_stages_summary_by_user_by_event_name";
        "memory/performance_schema/" -> "events_statements_summary_by_user_by_event_name";
        "memory/performance_schema/" -> "events_transactions_summary_by_user_by_event_name";
        "memory/performance_schema/" -> "events_errors_summary_by_user_by_error";
        "memory/performance_schema/" -> "memory_summary_by_user_by_event_name";
        "memory/performance_schema/" -> "mutex_class";
        "memory/performance_schema/" -> "rwlock_class";
        "memory/performance_schema/" -> "cond_class";
        "memory/performance_schema/" -> "thread_class";
        "memory/performance_schema/" -> "file_class";
        "memory/performance_schema/" -> "socket_class";
        "memory/performance_schema/" -> "stage_class";
        "memory/performance_schema/" -> "statement_class";
        "memory/performance_schema/" -> "memory_class";
        "memory/performance_schema/" -> "setup_actors";
        "memory/performance_schema/" -> "setup_objects";
        "memory/performance_schema/" -> "events_statements_summary_by_digest";
        "memory/performance_schema/" -> "events_statements_summary_by_digest.digest_text";
        "memory/performance_schema/" -> "events_stages_history_long";
        "memory/performance_schema/" -> "events_statements_history_long";
        "memory/performance_schema/" -> "events_statements_history_long.digest_text";
        "memory/performance_schema/" -> "events_statements_history_long.sql_text";
        "memory/performance_schema/" -> "events_transactions_history_long";
        "memory/performance_schema/" -> "events_waits_history_long";
        "memory/performance_schema/" -> "table_handles";
        "memory/performance_schema/" -> "table_shares";
        "memory/performance_schema/" -> "table_io_waits_summary_by_index_usage";
        "memory/performance_schema/" -> "table_lock_waits_summary_by_table";
        "memory/performance_schema/" -> "events_statements_summary_by_program";
        "memory/performance_schema/" -> "prepared_statements_instances";
        "memory/performance_schema/" -> "scalable_buffer";
}
```
memory/performance_schema/mutex_instances
memory/performance_schema/rwlock_instances
#### PFS_builtin_memory_class结构
```viz-dot
digraph "PFS_builtin_memory_class" {
        rankdir=LR;
        edge [
                fontsize="11"
                ];
        node [
                shape = rect;
                fontsize = "11"
                ];
        "PFS_builtin_memory_class" -> "PFS_memory_class:m_class";
        "PFS_builtin_memory_class" -> "PFS_memory_shared_stat:m_stat";
        "PFS_memory_shared_stat:m_stat" -> "m_used";
        "PFS_memory_shared_stat:m_stat" -> "m_alloc_count" ;
        "PFS_memory_shared_stat:m_stat" -> "m_free_count";
        "PFS_memory_shared_stat:m_stat" -> "m_alloc_size";
        "PFS_memory_shared_stat:m_stat" -> "m_free_size";
        "PFS_memory_shared_stat:m_stat" -> "m_alloc_count_capacity";
        "PFS_memory_shared_stat:m_stat" -> "m_free_count_capacity" ;
        "PFS_memory_shared_stat:m_stat" -> "m_alloc_size_capacity";
        "PFS_memory_shared_stat:m_stat" -> "m_free_size_capacity";
        "PFS_memory_shared_stat:m_stat" -> "reset()"[label="所有数据成员置零"];
        "PFS_memory_class:m_class" -> "PFS_instr_class";
        "PFS_instr_class" -> "m_type";
        "PFS_instr_class" -> "m_enabled";
        "PFS_instr_class" -> "m_timed";
        "PFS_instr_class" -> "m_flags" ;
        "PFS_instr_class" -> "m_enforced_flags" ;
        "PFS_instr_class" -> "m_volatility" ;
        "PFS_instr_class" -> "m_event_name_index" ;
        "PFS_instr_class" -> "PFS_instr_name:m_name" ;
        "PFS_instr_class" -> "m_documentation" ;
        "PFS_instr_name:m_name" -> "m_private_name[128-1]" ;
        "PFS_instr_name:m_name" -> "m_private_name_length";
        "PFS_instr_name:m_name" -> "m_private_old_name" ;
        "PFS_instr_name:m_name" -> "m_private_old_name_length" ;
}
```
- m_type
  - PFS_CLASS_NONE        = 0,
  - PFS_CLASS_MUTEX       = 1,
  - PFS_CLASS_RWLOCK      = 2,
  - PFS_CLASS_COND        = 3,
  - PFS_CLASS_FILE        = 4,
  - PFS_CLASS_TABLE       = 5,
  - PFS_CLASS_STAGE       = 6,
  - PFS_CLASS_STATEMENT   = 7,
  - PFS_CLASS_TRANSACTION = 8,
  - PFS_CLASS_SOCKET      = 9,
  - PFS_CLASS_TABLE_IO    = 10,
  - PFS_CLASS_TABLE_LOCK  = 11,
  - PFS_CLASS_IDLE        = 12,
  - PFS_CLASS_MEMORY      = 13,
  - PFS_CLASS_METADATA    = 14,
  - PFS_CLASS_ERROR       = 15,
  - PFS_CLASS_THREAD      = 16,
  - PFS_CLASS_LAST        = PFS_CLASS_THREAD,
  - PFS_CLASS_MAX         = PFS_CLASS_LAST + 1


#### 初始化内存`PFS_builtin_memory_class`类
```c++
//全局变量

// 表：performance_schema.mutex_instances
PFS_builtin_memory_class builtin_memory_mutex;
// 表：performance_schema.rwlock_instances
PFS_builtin_memory_class builtin_memory_rwlock;
// 表：performance_schema.cond_instances
PFS_builtin_memory_class builtin_memory_cond;
// file_instances
PFS_builtin_memory_class builtin_memory_file;
// socket_instances
PFS_builtin_memory_class builtin_memory_socket;
// metadata_locks
PFS_builtin_memory_class builtin_memory_mdl;
// file_handle
PFS_builtin_memory_class builtin_memory_file_handle;

// accounts
PFS_builtin_memory_class builtin_memory_account;
// events_waits_summary_by_account_by_event_name
PFS_builtin_memory_class builtin_memory_account_waits;
// events_stages_summary_by_account_by_event_name
PFS_builtin_memory_class builtin_memory_account_stages;
// events_statements_summary_by_account_by_event_name
PFS_builtin_memory_class builtin_memory_account_statements;
// events_transactions_summary_by_account_by_event_name
PFS_builtin_memory_class builtin_memory_account_transactions;
// events_errors_summary_by_account_by_error
PFS_builtin_memory_class builtin_memory_account_errors;
// memory_summary_by_account_by_event_name
PFS_builtin_memory_class builtin_memory_account_memory;

// events_stages_summary_global_by_event_name
PFS_builtin_memory_class builtin_memory_global_stages;
// events_statements_summary_global_by_event_name
PFS_builtin_memory_class builtin_memory_global_statements;
// memory_summary_global_by_event_name
PFS_builtin_memory_class builtin_memory_global_memory;
// events_errors_summary_global_by_error
PFS_builtin_memory_class builtin_memory_global_errors;

// hosts
PFS_builtin_memory_class builtin_memory_host;
// events_waits_summary_by_host_by_event_name
PFS_builtin_memory_class builtin_memory_host_waits;
// events_stages_summary_by_host_by_event_name
PFS_builtin_memory_class builtin_memory_host_stages;
// events_statements_summary_by_host_by_event_name
PFS_builtin_memory_class builtin_memory_host_statements;
// events_transactions_summary_by_host_by_event_name
PFS_builtin_memory_class builtin_memory_host_transactions;
// events_errors_summary_by_host_by_error
PFS_builtin_memory_class builtin_memory_host_errors;
// memory_summary_by_host_by_event_name
PFS_builtin_memory_class builtin_memory_host_memory;

// threads
PFS_builtin_memory_class builtin_memory_thread;
// events_waits_summary_by_thread_by_event_name
PFS_builtin_memory_class builtin_memory_thread_waits;
// events_stages_summary_by_thread_by_event_name
PFS_builtin_memory_class builtin_memory_thread_stages;
// events_statements_summary_by_thread_by_event_name
PFS_builtin_memory_class builtin_memory_thread_statements;
// events_transactions_summary_by_thread_by_event_name
PFS_builtin_memory_class builtin_memory_thread_transactions;
// events_errors_summary_by_thread_by_error
PFS_builtin_memory_class builtin_memory_thread_errors;
// memory_summary_by_thread_by_event_name
PFS_builtin_memory_class builtin_memory_thread_memory;

// events_waits_history
PFS_builtin_memory_class builtin_memory_thread_waits_history;
// events_stages_history
PFS_builtin_memory_class builtin_memory_thread_stages_history;
// events_statements_history
PFS_builtin_memory_class builtin_memory_thread_statements_history;
// events_statements_history digest_text
PFS_builtin_memory_class builtin_memory_thread_statements_history_tokens;
// events_statements_history sql_text
PFS_builtin_memory_class builtin_memory_thread_statements_history_sqltext;
// events_statements_current
PFS_builtin_memory_class builtin_memory_thread_statements_stack;
// events_statements_current digest_text
PFS_builtin_memory_class builtin_memory_thread_statements_stack_tokens;
// events_statements_current sql_text
PFS_builtin_memory_class builtin_memory_thread_statements_stack_sqltext;
// events_transactions_history
PFS_builtin_memory_class builtin_memory_thread_transaction_history;
// session_connect_attrs
PFS_builtin_memory_class builtin_memory_thread_session_connect_attrs;

// users
PFS_builtin_memory_class builtin_memory_user;
// events_waits_summary_by_user_by_event_name
PFS_builtin_memory_class builtin_memory_user_waits;
// events_stages_summary_by_user_by_event_name
PFS_builtin_memory_class builtin_memory_user_stages;
// events_statements_summary_by_user_by_event_name
PFS_builtin_memory_class builtin_memory_user_statements;
// events_transactions_summary_by_user_by_event_name
PFS_builtin_memory_class builtin_memory_user_transactions;
// events_errors_summary_by_user_by_error
PFS_builtin_memory_class builtin_memory_user_errors;
// memory_summary_by_user_by_event_name
PFS_builtin_memory_class builtin_memory_user_memory;

// mutex_class mutex instrument classes
PFS_builtin_memory_class builtin_memory_mutex_class;
// rwlock_class rwlock instrument classes
PFS_builtin_memory_class builtin_memory_rwlock_class;
// cond_class cond instrument classes
PFS_builtin_memory_class builtin_memory_cond_class;
// thread_class thread instrument classes
PFS_builtin_memory_class builtin_memory_thread_class;
PFS_builtin_memory_class builtin_memory_file_class;
PFS_builtin_memory_class builtin_memory_socket_class;
PFS_builtin_memory_class builtin_memory_stage_class;
PFS_builtin_memory_class builtin_memory_statement_class;
PFS_builtin_memory_class builtin_memory_memory_class;

// setup_actors
PFS_builtin_memory_class builtin_memory_setup_actor;
//  setup_objects
PFS_builtin_memory_class builtin_memory_setup_object;

// events_statements_summary_by_digest
PFS_builtin_memory_class builtin_memory_digest;
// events_statements_summary_by_digest digest_text
PFS_builtin_memory_class builtin_memory_digest_tokens;
// events_statements_summary_by_digest
PFS_builtin_memory_class builtin_memory_digest_sample_sqltext;

// events_stages_history_long
PFS_builtin_memory_class builtin_memory_stages_history_long;
// events_statements_history_long
PFS_builtin_memory_class builtin_memory_statements_history_long;
// events_statements_history_long digest_text
PFS_builtin_memory_class builtin_memory_statements_history_long_tokens;
// events_statements_history_long sql_text
PFS_builtin_memory_class builtin_memory_statements_history_long_sqltext;
// events_transactions_history_long
PFS_builtin_memory_class builtin_memory_transactions_history_long;
// events_waits_history_long
PFS_builtin_memory_class builtin_memory_waits_history_long;

// table_handles
PFS_builtin_memory_class builtin_memory_table;
// table_shares
PFS_builtin_memory_class builtin_memory_table_share;
// table_io_waits_summary_by_index_usage
PFS_builtin_memory_class builtin_memory_table_share_index;
// table_lock_waits_summary_by_table
PFS_builtin_memory_class builtin_memory_table_share_lock;

// events_statements_summary_by_program
PFS_builtin_memory_class builtin_memory_program;
// prepared_statements_instances
PFS_builtin_memory_class builtin_memory_prepared_stmt;

PFS_builtin_memory_class builtin_memory_scalable_buffer;

static void init_builtin_memory_class(PFS_builtin_memory_class *klass, const char *name, const char *documentation) {
        klass->m_class.m_type             = PFS_CLASS_MEMORY;
        klass->m_class.m_enabled          = true;  /* Immutable */
        klass->m_class.m_timed            = false; /* N/A */
        klass->m_class.m_flags            = PSI_FLAG_ONLY_GLOBAL_STAT;
        klass->m_class.m_volatility       = PSI_VOLATILITY_PERMANENT;
        klass->m_class.m_documentation    = const_cast<char *>(documentation);
        klass->m_class.m_event_name_index = 0;
        klass->m_class.m_name.set(PFS_CLASS_MEMORY, name);
        assert(klass->m_class.m_name.length() <= klass->m_class.m_name.max_length);
        //
        klass->m_stat.reset();
}

#define PREFIX "memory/performance_schema/"
#define TABLE_DOC(X) PREFIX X, "Memory used for table performance_schema." X
#define COL_DOC(X, Y) \
  PREFIX X "." Y, "Memory used for table performance_schema." X ", column " Y
#define GEN_DOC(X, Y) PREFIX X, "Memory used for " Y

/* clang-format off */
void
init_all_builtin_memory_class()
{
  init_builtin_memory_class(&builtin_memory_mutex,
                            TABLE_DOC("mutex_instances"));

  init_builtin_memory_class(&builtin_memory_rwlock,
                            TABLE_DOC("rwlock_instances"));

  init_builtin_memory_class(&builtin_memory_cond,
                            TABLE_DOC("cond_instances"));

  init_builtin_memory_class(&builtin_memory_file,
                            TABLE_DOC("file_instances"));

  init_builtin_memory_class(&builtin_memory_socket,
                            TABLE_DOC("socket_instances"));

  init_builtin_memory_class(&builtin_memory_mdl,
                            TABLE_DOC("metadata_locks"));

  init_builtin_memory_class(&builtin_memory_file_handle,
                            TABLE_DOC("file_handle"));

  init_builtin_memory_class(&builtin_memory_account,
                            TABLE_DOC("accounts"));

  init_builtin_memory_class(&builtin_memory_account_waits,
                            TABLE_DOC("events_waits_summary_by_account_by_event_name"));

  init_builtin_memory_class(&builtin_memory_account_stages,
                            TABLE_DOC("events_stages_summary_by_account_by_event_name"));

  init_builtin_memory_class(&builtin_memory_account_statements,
                            TABLE_DOC("events_statements_summary_by_account_by_event_name"));

  init_builtin_memory_class(&builtin_memory_account_transactions,
                            TABLE_DOC("events_transactions_summary_by_account_by_event_name"));

  init_builtin_memory_class(&builtin_memory_account_errors,
                            TABLE_DOC("events_errors_summary_by_account_by_error"));

  init_builtin_memory_class(&builtin_memory_account_memory,
                            TABLE_DOC("memory_summary_by_account_by_event_name"));

  init_builtin_memory_class(&builtin_memory_global_stages,
                            TABLE_DOC("events_stages_summary_global_by_event_name"));

  init_builtin_memory_class(&builtin_memory_global_statements,
                            TABLE_DOC("events_statements_summary_global_by_event_name"));

  init_builtin_memory_class(&builtin_memory_global_memory,
                            TABLE_DOC("memory_summary_global_by_event_name"));

  init_builtin_memory_class(&builtin_memory_global_errors,
                            TABLE_DOC("events_errors_summary_global_by_error"));

  init_builtin_memory_class(&builtin_memory_host,
                            TABLE_DOC("hosts"));

  init_builtin_memory_class(&builtin_memory_host_waits,
                            TABLE_DOC("events_waits_summary_by_host_by_event_name"));

  init_builtin_memory_class(&builtin_memory_host_stages,
                            TABLE_DOC("events_stages_summary_by_host_by_event_name"));

  init_builtin_memory_class(&builtin_memory_host_statements,
                            TABLE_DOC("events_statements_summary_by_host_by_event_name"));

  init_builtin_memory_class(&builtin_memory_host_transactions,
                            TABLE_DOC("events_transactions_summary_by_host_by_event_name"));

  init_builtin_memory_class(&builtin_memory_host_errors,
                            TABLE_DOC("events_errors_summary_by_host_by_error"));

  init_builtin_memory_class(&builtin_memory_host_memory,
                            TABLE_DOC("memory_summary_by_host_by_event_name"));

  init_builtin_memory_class(&builtin_memory_thread,
                            TABLE_DOC("threads"));

  init_builtin_memory_class(&builtin_memory_thread_waits,
                            TABLE_DOC("events_waits_summary_by_thread_by_event_name"));

  init_builtin_memory_class(&builtin_memory_thread_stages,
                            TABLE_DOC("events_stages_summary_by_thread_by_event_name"));

  init_builtin_memory_class(&builtin_memory_thread_statements,
                            TABLE_DOC("events_statements_summary_by_thread_by_event_name"));

  init_builtin_memory_class(&builtin_memory_thread_transactions,
                            TABLE_DOC("events_transactions_summary_by_thread_by_event_name"));

  init_builtin_memory_class(&builtin_memory_thread_errors,
                            TABLE_DOC("events_errors_summary_by_thread_by_error"));

  init_builtin_memory_class(&builtin_memory_thread_memory,
                            TABLE_DOC("memory_summary_by_thread_by_event_name"));

  init_builtin_memory_class(&builtin_memory_thread_waits_history,
                            TABLE_DOC("events_waits_history"));

  init_builtin_memory_class(&builtin_memory_thread_stages_history,
                            TABLE_DOC("events_stages_history"));

  init_builtin_memory_class(&builtin_memory_thread_statements_history,
                            TABLE_DOC("events_statements_history"));

  init_builtin_memory_class(&builtin_memory_thread_statements_history_tokens,
                            COL_DOC("events_statements_history", "digest_text"));

  init_builtin_memory_class(&builtin_memory_thread_statements_history_sqltext,
                            COL_DOC("events_statements_history", "sql_text"));

  init_builtin_memory_class(&builtin_memory_thread_statements_stack,
                            TABLE_DOC("events_statements_current"));

  init_builtin_memory_class(&builtin_memory_thread_statements_stack_tokens,
                            COL_DOC("events_statements_current", "digest_text"));

  init_builtin_memory_class(&builtin_memory_thread_statements_stack_sqltext,
                            COL_DOC("events_statements_current", "sql_text"));

  init_builtin_memory_class(&builtin_memory_thread_transaction_history,
                            TABLE_DOC("events_transactions_history"));

  init_builtin_memory_class(&builtin_memory_thread_session_connect_attrs,
                            TABLE_DOC("session_connect_attrs"));

  init_builtin_memory_class(&builtin_memory_user,
                            TABLE_DOC("users"));

  init_builtin_memory_class(&builtin_memory_user_waits,
                            TABLE_DOC("events_waits_summary_by_user_by_event_name"));

  init_builtin_memory_class(&builtin_memory_user_stages,
                            TABLE_DOC("events_stages_summary_by_user_by_event_name"));

  init_builtin_memory_class(&builtin_memory_user_statements,
                            TABLE_DOC("events_statements_summary_by_user_by_event_name"));

  init_builtin_memory_class(&builtin_memory_user_transactions,
                            TABLE_DOC("events_transactions_summary_by_user_by_event_name"));

  init_builtin_memory_class(&builtin_memory_user_errors,
                            TABLE_DOC("events_errors_summary_by_user_by_error"));

  init_builtin_memory_class(&builtin_memory_user_memory,
                            TABLE_DOC("memory_summary_by_user_by_event_name"));

  init_builtin_memory_class(&builtin_memory_mutex_class,
                            GEN_DOC("mutex_class", "mutex instrument classes"));

  init_builtin_memory_class(&builtin_memory_rwlock_class,
                            GEN_DOC("rwlock_class", "rwlock instrument classes"));

  init_builtin_memory_class(&builtin_memory_cond_class,
                            GEN_DOC("cond_class", "cond instrument classes"));

  init_builtin_memory_class(&builtin_memory_thread_class,
                            GEN_DOC("thread_class", "thread instrument classes"));

  init_builtin_memory_class(&builtin_memory_file_class,
                            GEN_DOC("file_class", "file instrument classes"));

  init_builtin_memory_class(&builtin_memory_socket_class,
                            GEN_DOC("socket_class", "socket instrument classes"));

  init_builtin_memory_class(&builtin_memory_stage_class,
                            GEN_DOC("stage_class", "stage instrument classes"));

  init_builtin_memory_class(&builtin_memory_statement_class,
                            GEN_DOC("statement_class", "statement instrument classes"));

  init_builtin_memory_class(&builtin_memory_memory_class,
                            GEN_DOC("memory_class", "memory instrument classes"));

  init_builtin_memory_class(&builtin_memory_setup_actor,
                            TABLE_DOC("setup_actors"));

  init_builtin_memory_class(&builtin_memory_setup_object,
                            TABLE_DOC("setup_objects"));

  init_builtin_memory_class(&builtin_memory_digest,
                            TABLE_DOC("events_statements_summary_by_digest"));

  init_builtin_memory_class(&builtin_memory_digest_tokens,
                            COL_DOC("events_statements_summary_by_digest", "digest_text"));

  init_builtin_memory_class(&builtin_memory_stages_history_long,
                            TABLE_DOC("events_stages_history_long"));

  init_builtin_memory_class(&builtin_memory_statements_history_long,
                            TABLE_DOC("events_statements_history_long"));

  init_builtin_memory_class(&builtin_memory_statements_history_long_tokens,
                            COL_DOC("events_statements_history_long", "digest_text"));

  init_builtin_memory_class(&builtin_memory_statements_history_long_sqltext,
                            COL_DOC("events_statements_history_long", "sql_text"));

  init_builtin_memory_class(&builtin_memory_transactions_history_long,
                            TABLE_DOC("events_transactions_history_long"));

  init_builtin_memory_class(&builtin_memory_waits_history_long,
                            TABLE_DOC("events_waits_history_long"));

  init_builtin_memory_class(&builtin_memory_table,
                            TABLE_DOC("table_handles"));

  init_builtin_memory_class(&builtin_memory_table_share,
                            TABLE_DOC("table_shares"));

  init_builtin_memory_class(&builtin_memory_table_share_index,
                            TABLE_DOC("table_io_waits_summary_by_index_usage"));

  init_builtin_memory_class(&builtin_memory_table_share_lock,
                            TABLE_DOC("table_lock_waits_summary_by_table"));

  init_builtin_memory_class(&builtin_memory_program,
                            TABLE_DOC("events_statements_summary_by_program"));

  init_builtin_memory_class(&builtin_memory_prepared_stmt,
                            TABLE_DOC("prepared_statements_instances"));

  init_builtin_memory_class(&builtin_memory_scalable_buffer,
                            GEN_DOC("scalable_buffer", "scalable buffers"));
}
/* clang-format off */

enum PFS_class_type {
        PFS_CLASS_NONE        = 0,
        PFS_CLASS_MUTEX       = 1,
        PFS_CLASS_RWLOCK      = 2,
        PFS_CLASS_COND        = 3,
        PFS_CLASS_FILE        = 4,
        PFS_CLASS_TABLE       = 5,
        PFS_CLASS_STAGE       = 6,
        PFS_CLASS_STATEMENT   = 7,
        PFS_CLASS_TRANSACTION = 8,
        PFS_CLASS_SOCKET      = 9,
        PFS_CLASS_TABLE_IO    = 10,
        PFS_CLASS_TABLE_LOCK  = 11,
        PFS_CLASS_IDLE        = 12,
        PFS_CLASS_MEMORY      = 13,
        PFS_CLASS_METADATA    = 14,
        PFS_CLASS_ERROR       = 15,
        PFS_CLASS_THREAD      = 16,
        PFS_CLASS_LAST        = PFS_CLASS_THREAD,
        PFS_CLASS_MAX         = PFS_CLASS_LAST + 1
};

struct PFS_builtin_memory_class {
        PFS_memory_class m_class;
        PFS_memory_shared_stat m_stat;

        inline void count_alloc(size_t size) { m_stat.count_builtin_alloc(size); }

        inline void count_free(size_t size) { m_stat.count_builtin_free(size); }
};

struct PFS_ALIGNED PFS_memory_class : public PFS_instr_class {};


/** Information for all instrumentation. */
struct PFS_instr_class {

        PFS_class_type m_type;

        bool m_enabled;

        bool m_timed;

        uint m_flags;

        uint m_enforced_flags;

        int m_volatility;

        uint m_event_name_index;

        PFS_instr_name m_name;

        char *m_documentation;

        bool is_singleton() const { return m_flags & PSI_FLAG_SINGLETON; }

        bool is_mutable() const { return m_flags & PSI_FLAG_MUTABLE; }

        bool is_progress() const { return m_flags & PSI_FLAG_STAGE_PROGRESS; }

        bool is_shared_exclusive() const { return m_flags & PSI_FLAG_RWLOCK_SX; }

        bool is_priority() const { return m_flags & PSI_FLAG_RWLOCK_PR; }

        bool is_transferable() const { return m_flags & PSI_FLAG_TRANSFER; }

        bool is_user() const { return m_flags & PSI_FLAG_USER; }

        bool is_system_thread() const { return m_flags & PSI_FLAG_THREAD_SYSTEM; }

        bool is_global() const { return m_flags & PSI_FLAG_ONLY_GLOBAL_STAT; }

        bool has_seqnum() const { return (m_flags & (PSI_FLAG_SINGLETON | PSI_FLAG_NO_SEQNUM)) == 0; }

        bool has_auto_seqnum() const { return m_flags & PSI_FLAG_AUTO_SEQNUM; }

        bool has_default_memory_cnt() const { return m_flags & PSI_FLAG_MEM_COLLECT; }

        bool has_enforced_memory_cnt() const { return m_enforced_flags & PSI_FLAG_MEM_COLLECT; }

        void set_enforced_flags(uint flags) { m_enforced_flags = flags; }

        void enforce_valid_flags(uint allowed_flags) {

                allowed_flags |= PSI_FLAG_THREAD | PSI_FLAG_TRANSFER;

                uint valid_flags = m_flags & allowed_flags;

                assert(valid_flags == m_flags);
                m_flags = valid_flags;
        }

        static void set_enabled(PFS_instr_class *pfs, bool enabled);
        static void set_timed(PFS_instr_class *pfs, bool timed);

        bool is_deferred() const {
                switch (m_type) {
                        case PFS_CLASS_SOCKET:
                                return true;
                                break;
                        default:
                                return false;
                                break;
                };
        }

        bool can_be_timed() const {
                switch (m_type) {
                        case PFS_CLASS_MEMORY:
                        case PFS_CLASS_ERROR:
                        case PFS_CLASS_THREAD:
                                return false;
                        default:
                                return true;
                };
        }

        bool can_be_enforced() const {
                switch (m_type) {
                        case PFS_CLASS_MEMORY:
                                return true;
                        default:
                                return false;
                };
        }
};

struct PFS_memory_shared_stat {
        std::atomic<bool> m_used;

        std::atomic<size_t> m_alloc_count;
        std::atomic<size_t> m_free_count;
        std::atomic<size_t> m_alloc_size;
        std::atomic<size_t> m_free_size;

        std::atomic<size_t> m_alloc_count_capacity;
        std::atomic<size_t> m_free_count_capacity;
        std::atomic<size_t> m_alloc_size_capacity;
        std::atomic<size_t> m_free_size_capacity;

        void reset();

        void rebase();

        void count_builtin_alloc(size_t size);

        void count_builtin_free(size_t size);

        inline void count_global_alloc(size_t size) { count_builtin_alloc(size); }

        inline void count_global_free(size_t size) { count_builtin_free(size); }

        PFS_memory_stat_alloc_delta *count_alloc(size_t size, PFS_memory_stat_alloc_delta *delta);

        PFS_memory_stat_free_delta *count_free(size_t size, PFS_memory_stat_free_delta *delta);

        /**
          Expand the high water marks.
          @param [in] delta High watermark increments to apply
          @param [in] delta_buffer Working buffer
          @return High watermark increments to carry to the parent if any, or
      nullptr.
        */
        PFS_memory_stat_alloc_delta *apply_alloc_delta(const PFS_memory_stat_alloc_delta *delta,
                                                       PFS_memory_stat_alloc_delta *delta_buffer);

        /**
          Expand the low water marks.
          @param [in] delta Low watermark decrements to apply
          @param [in] delta_buffer Working buffer
          @return Low watermark decrements to carry to the parent if any, or
      nullptr.
        */
        PFS_memory_stat_free_delta *apply_free_delta(const PFS_memory_stat_free_delta *delta,
                                                     PFS_memory_stat_free_delta *delta_buffer);
};


class PFS_instr_name {
public:
        static constexpr uint max_length = PFS_MAX_INFO_NAME_LENGTH - 1;

public:

        char m_private_name[max_length + 1];

        uint m_private_name_length;

        const char *m_private_old_name;

        uint m_private_old_name_length;

        terminology_use_previous::enum_compatibility_version m_private_version;

public:
        const char *str() const;
        uint length() const;
        void set(PFS_class_type class_type, const char *name, uint max_length_arg = max_length);
};

static uint safe_strlen(const char *s, uint max_len) {
        const char *end = static_cast<const char *>(memchr(s, '\0', static_cast<size_t>(max_len)));
        return end == nullptr ? max_len : static_cast<uint>(end - s);
}

void PFS_instr_name::set(PFS_class_type class_type, const char *name, uint max_length_arg) {
        // Copy the given name to the member.
        uint length = safe_strlen(name, std::min(max_length, max_length_arg));
        memcpy(m_private_name, name, length);
        m_private_name[length] = '\0';
        m_private_name_length  = length;

        // Check if there is an alternative name to use when
        // @@terminology_use_previous is enabled.
        auto compatible_name      = terminology_use_previous::lookup(class_type, std::string{name, length});
        m_private_old_name        = compatible_name.old_name;
        m_private_old_name_length = compatible_name.old_name ? strlen(compatible_name.old_name) : 0;
        m_private_version         = compatible_name.version;
}

void PFS_memory_shared_stat::reset() {
        m_used = false;

        m_alloc_count = 0;
        m_free_count  = 0;
        m_alloc_size  = 0;
        m_free_size   = 0;

        m_alloc_count_capacity = 0;
        m_free_count_capacity  = 0;
        m_alloc_size_capacity  = 0;
        m_free_size_capacity   = 0;
}
```

### PFS_table_stat::g_reset_template.reset()



```c++
/** Statistics for TABLE usage. */
struct PFS_table_stat {
        /**
          Statistics, per index.
          Each index stat is in [0, MAX_INDEXES-1],
          stats when using no index are in [MAX_INDEXES].
        */
        PFS_table_io_stat m_index_stat[MAX_INDEXES + 1];

        /**
          Statistics, per lock type.
        */
        PFS_table_lock_stat m_lock_stat;

        /** Reset table I/O statistic. */
        inline void reset_io(void) {
                PFS_table_io_stat *stat      = &m_index_stat[0];
                PFS_table_io_stat *stat_last = &m_index_stat[MAX_INDEXES + 1];
                for (; stat < stat_last; stat++) {
                        stat->reset();
                }
        }

        /** Reset table lock statistic. */
        inline void reset_lock(void) {
                m_lock_stat.reset();
        }

        /** Reset table statistic. */
        inline void reset(void) {
                reset_io();
                reset_lock();
        }

        inline void fast_reset_io(void) {
                memcpy(&m_index_stat, &g_reset_template.m_index_stat, sizeof(m_index_stat));
        }

        inline void fast_reset_lock(void) {
                memcpy(&m_lock_stat, &g_reset_template.m_lock_stat, sizeof(m_lock_stat));
        }

        inline void fast_reset(void) {
                memcpy(this, &g_reset_template, sizeof(*this));
        }

        inline void aggregate_io(const PFS_table_stat *stat, uint key_count) {
                PFS_table_io_stat *to_stat;
                PFS_table_io_stat *to_stat_last;
                const PFS_table_io_stat *from_stat;

                assert(key_count <= MAX_INDEXES);

                /* Aggregate stats for each index, if any */
                to_stat      = &m_index_stat[0];
                to_stat_last = to_stat + key_count;
                from_stat    = &stat->m_index_stat[0];
                for (; to_stat < to_stat_last; from_stat++, to_stat++) {
                        to_stat->aggregate(from_stat);
                }

                /* Aggregate stats for the table */
                to_stat   = &m_index_stat[MAX_INDEXES];
                from_stat = &stat->m_index_stat[MAX_INDEXES];
                to_stat->aggregate(from_stat);
        }

        inline void aggregate_lock(const PFS_table_stat *stat) {
                m_lock_stat.aggregate(&stat->m_lock_stat);
        }

        inline void aggregate(const PFS_table_stat *stat, uint key_count) {
                aggregate_io(stat, key_count);
                aggregate_lock(stat);
        }

        inline void sum_io(PFS_single_stat *result, uint key_count) {
                PFS_table_io_stat *stat;
                PFS_table_io_stat *stat_last;

                assert(key_count <= MAX_INDEXES);

                /* Sum stats for each index, if any */
                stat      = &m_index_stat[0];
                stat_last = stat + key_count;
                for (; stat < stat_last; stat++) {
                        stat->sum(result);
                }

                /* Sum stats for the table */
                m_index_stat[MAX_INDEXES].sum(result);
        }

        inline void sum_lock(PFS_single_stat *result) {
                m_lock_stat.sum(result);
        }

        inline void sum(PFS_single_stat *result, uint key_count) {
                sum_io(result, key_count);
                sum_lock(result);
        }

        static struct PFS_table_stat g_reset_template;
};

// 定义全局变量
PFS_table_stat PFS_table_stat::g_reset_template;
```
这句代码是对 PFS_table_stat 类的静态成员变量 g_reset_template 进行定义。静态成员变量必须在类外部进行定义和初始化，以确保它在程序的全局静态存储区中分配内存。

具体来说，这句代码的作用是为 PFS_table_stat 类的静态成员变量 g_reset_template 分配内存，并调用该类的默认构造函数进行初始化。由于这是一个全局静态变量，在程序启动时就会被初始化，而不是在类的实例被创建时进行初始化。

这样，就可以在整个程序中通过 PFS_table_stat::g_reset_template 来访问该静态成员变量，而不必担心其初始化顺序或多次初始化的问题。这种定义和初始化方式确保了静态成员变量在程序的生命周期内只有一个实例，并且可以在任何地方被访问和使用。



在这段代码中，g_reset_template 被定义为 static 的主要原因是为了确保它只有一个全局实例，并且可以在不实例化 PFS_table_stat 类的情况下直接访问。

由于 g_reset_template 是一个模板用于初始化 PFS_table_stat 结构体的默认值，将其定义为 static 可以确保它在程序的整个生命周期内只有一个实例。这样，在任何地方都可以通过 PFS_table_stat::g_reset_template 来访问它，而不必担心重复定义或多次实例化的问题。

另外，将 g_reset_template 定义为 static 还有助于节省内存空间，因为它只需要在程序的全局静态存储区中分配一次内存，而不是每次实例化 PFS_table_stat 类时都分配一次内存。这样可以减少不必要的内存开销，并提高程序的性能。

总之，将 g_reset_template 定义为 static 可以确保它在全局范围内只有一个实例，并且能够在不实例化 PFS_table_stat 类的情况下直接访问，同时也有助于节省内存空间


####

```viz-dot
digraph "PFS_table_stat" {
        rankdir=LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = rect;
                fontsize = "12"
                ];
        "PFS_table_stat" -> "PFS_table_io_stat"[label="[64+1]"];
        "PFS_table_stat" -> "PFS_table_lock_stat";
        "PFS_table_stat" -> "PFS_table_stat";
        "PFS_table_io_stat" -> "m_has_data";
        "PFS_table_io_stat" -> "PFS_single_stat" ;
        "PFS_single_stat" -> "m_count" ;
        "PFS_single_stat" -> "m_sum";
        "PFS_single_stat" -> "m_min";
        "PFS_single_stat" -> "m_max";

        "PFS_table_lock_stat" -> "PFS_single_stat"[label="[10]"];

}
```

- PFS_table_stat
  - PFS_table_io_stat m_index_stat[MAX_INDEXES + 1]
    - bool m_has_data
    - PFS_single_stat m_fetch/m_insert/m_update/m_delete
      - ulonglong m_count
      - ulonglong m_sum
      - ulonglong m_min
      - ulonglong m_max
    - PFS_table_io_stat()
    - inline void reset(void)
    - inline void aggregate(const PFS_table_io_stat *stat)
    - inline void sum(PFS_single_stat *result)
  - PFS_table_lock_stat m_lock_stat
    - PFS_single_stat m_stat[COUNT_PFS_TL_LOCK_TYPE]
    - inline void reset(void)
    - inline void aggregate(const PFS_table_lock_stat *stat)
    - inline void sum(PFS_single_stat *result)
  - inline void reset_io(void)
  - inline void reset_lock(void)
  - inline void reset(void)
  - inline void fast_reset_io(void)
  - inline void fast_reset_lock(void)
  - inline void fast_reset(void)
  - inline void aggregate_io(const PFS_table_stat *stat, uint key_count)
  - inline void aggregate_lock(const PFS_table_stat *stat)
  - inline void aggregate(const PFS_table_stat *stat, uint key_count)
  - inline void sum_io(PFS_single_stat *result, uint key_count)
  - inline void sum_lock(PFS_single_stat *result)
  - inline void sum(PFS_single_stat *result, uint key_count)


##### PFS_table_stat
```c++
// 存储表的使用统计信息
struct PFS_table_stat {
        // 存储表的 I/O 统计信息。每个索引表示不同的索引统计信息，而最后一个索引（MAX_INDEXES）表示没有使用索引时的统计信息
        // 这里MAX_INDEXES表示一张表的最大二级索引数量为：64个
        PFS_table_io_stat m_index_stat[MAX_INDEXES + 1];

        /**
          Statistics, per lock type.
        */
        PFS_table_lock_stat m_lock_stat;

        // 重置所有表的 I/O 统计信息为初始状态，即调用每个 PFS_table_io_stat 对象的 reset 方法将其统计信息重置
        inline void reset_io(void) {
                PFS_table_io_stat *stat      = &m_index_stat[0];
                PFS_table_io_stat *stat_last = &m_index_stat[MAX_INDEXES + 1];
                for (; stat < stat_last; stat++) {
                        stat->reset();
                }
        }

        // 重置表的锁统计信息为初始状态，即调用 PFS_table_lock_stat 对象的 reset 方法将其统计信息重置
        inline void reset_lock(void) {
                m_lock_stat.reset();
        }

        // 重置所有表的统计信息为初始状态，即调用 reset_io 和 reset_lock 方法
        inline void reset(void) {
                reset_io();
                reset_lock();
        }
        // 快速重置所有表的 I/O 统计信息为初始状态，通过将模板对象 g_reset_template 的索引统计信息复制到当前对象
        inline void fast_reset_io(void) {
                memcpy(&m_index_stat, &g_reset_template.m_index_stat, sizeof(m_index_stat));
        }
        //  快速重置表的锁统计信息为初始状态，通过将模板对象 g_reset_template 的锁统计信息复制到当前对象
        inline void fast_reset_lock(void) {
                memcpy(&m_lock_stat, &g_reset_template.m_lock_stat, sizeof(m_lock_stat));
        }
        // 快速重置所有表的统计信息为初始状态，通过将模板对象 g_reset_template 的所有统计信息复制到当前对象
        inline void fast_reset(void) {
                memcpy(this, &g_reset_template, sizeof(*this));
        }
        // 将另一个 PFS_table_stat 结构体的数据合并到当前结构体中。具体来说，遍历每个索引的统计信息，并调用 PFS_table_io_stat 对象的 aggregate 方法将其数据合并到当前对象中
        inline void aggregate_io(const PFS_table_stat *stat, uint key_count) {
                PFS_table_io_stat *to_stat;
                PFS_table_io_stat *to_stat_last;
                const PFS_table_io_stat *from_stat;

                assert(key_count <= MAX_INDEXES);

                /* Aggregate stats for each index, if any */
                to_stat      = &m_index_stat[0];
                to_stat_last = to_stat + key_count;
                from_stat    = &stat->m_index_stat[0];
                for (; to_stat < to_stat_last; from_stat++, to_stat++) {
                        to_stat->aggregate(from_stat);
                }

                /* Aggregate stats for the table */
                to_stat   = &m_index_stat[MAX_INDEXES];
                from_stat = &stat->m_index_stat[MAX_INDEXES];
                to_stat->aggregate(from_stat);
        }
        // 将另一个 PFS_table_stat 结构体的锁统计信息合并到当前结构体中
        inline void aggregate_lock(const PFS_table_stat *stat) {
                m_lock_stat.aggregate(&stat->m_lock_stat);
        }
        // 将另一个 PFS_table_stat 结构体的数据合并到当前结构体中，包括索引统计信息和锁统计信息
        inline void aggregate(const PFS_table_stat *stat, uint key_count) {
                aggregate_io(stat, key_count);
                aggregate_lock(stat);
        }
        // 对所有表的 I/O 统计信息进行求和，并将结果存储在给定的 PFS_single_stat 对象中
        inline void sum_io(PFS_single_stat *result, uint key_count) {
                PFS_table_io_stat *stat;
                PFS_table_io_stat *stat_last;

                assert(key_count <= MAX_INDEXES);

                /* Sum stats for each index, if any */
                stat      = &m_index_stat[0];
                stat_last = stat + key_count;
                for (; stat < stat_last; stat++) {
                        stat->sum(result);
                }

                /* Sum stats for the table */
                m_index_stat[MAX_INDEXES].sum(result);
        }
        // 对表的锁统计信息进行求和，并将结果存储在给定的 PFS_single_stat 对象中
        inline void sum_lock(PFS_single_stat *result) {
                m_lock_stat.sum(result);
        }
        // 对所有表的统计信息进行求和，并将结果存储在给定的 PFS_single_stat 对象中，包括索引统计信息和锁统计信息
        inline void sum(PFS_single_stat *result, uint key_count) {
                sum_io(result, key_count);
                sum_lock(result);
        }
        // 定义了一个静态全局变量,静态变量意味着它在程序的生命周期内只会被初始化一次，而全局变量则意味着它在整个程序中可见，可以在任何地方使用
        // 在这里，g_reset_template 扮演的角色是一个模板，存储了用于重置表统计信息的初始状态
        // 在结构体中，存在诸如 reset、reset_io、reset_lock 等方法，这些方法可以通过复制 g_reset_template 对象的数据来将表的统计信息重置为初始状态
        // 因此，g_reset_template 可以被视为一个蓝图或者模板，定义了表统计信息的初始状态，其他对象可以根据需要使用它来初始化或者重置自己的数据
        static struct PFS_table_stat g_reset_template;
};


```

##### PFS_table_lock_stat
```c++
// 表锁最多10种
#define COUNT_PFS_TL_LOCK_TYPE 10
// 表锁统计信息
struct PFS_table_lock_stat {
        // 存储不同类型的表锁的统计信息
        PFS_single_stat m_stat[COUNT_PFS_TL_LOCK_TYPE];
        // 重置所有表锁的统计信息为初始状态
        inline void reset(void) {
                PFS_single_stat *pfs      = &m_stat[0];
                PFS_single_stat *pfs_last = &m_stat[COUNT_PFS_TL_LOCK_TYPE];
                for (; pfs < pfs_last; pfs++) {
                        pfs->reset();
                }
        }
        // 将另一个 PFS_table_lock_stat 结构体的数据合并到当前结构体中。具体来说，遍历每个表锁的统计信息，并调用对应位置的 PFS_single_stat 对象的 aggregate 方法将其数据合并到当前对象中
        inline void aggregate(const PFS_table_lock_stat *stat) {
                PFS_single_stat *pfs            = &m_stat[0];
                PFS_single_stat *pfs_last       = &m_stat[COUNT_PFS_TL_LOCK_TYPE];
                const PFS_single_stat *pfs_from = &stat->m_stat[0];
                for (; pfs < pfs_last; pfs++, pfs_from++) {
                        pfs->aggregate(pfs_from);
                }
        }
        // 对所有表锁的统计信息进行求和，并将结果存储在给定的 PFS_single_stat 对象中。遍历每个表锁的统计信息，并将其与给定的 result 对象进行求和
        inline void sum(PFS_single_stat *result) {
                PFS_single_stat *pfs      = &m_stat[0];
                PFS_single_stat *pfs_last = &m_stat[COUNT_PFS_TL_LOCK_TYPE];
                for (; pfs < pfs_last; pfs++) {
                        result->aggregate(pfs);
                }
        }
};
```


#####

```c++
// 单表 I/O 统计信息
struct PFS_table_io_stat {
        // 是否有有效的统计数据
        bool m_has_data;
        // 存储 FETCH 操作的统计信息
        PFS_single_stat m_fetch;
        // 存储 INSERT 操作的统计信息
        PFS_single_stat m_insert;
        // 存储 UPDATE 操作的统计信息
        PFS_single_stat m_update;
        // 存储 DELETE 操作的统计信息
        PFS_single_stat m_delete;
        // 默认构造函数,初始化 m_has_data 为 false，表示没有有效数据
        PFS_table_io_stat() {
                m_has_data = false;
        }
        // 重置所有数据成员为初始状态
        inline void reset(void) {
                m_has_data = false;
                m_fetch.reset();
                m_insert.reset();
                m_update.reset();
                m_delete.reset();
        }
        // 将另一个 PFS_table_io_stat 结构体的数据合并到当前结构体中。具体来说，合并了所有操作的统计信息，并设置 m_has_data 为 true，表示有有效数据
        inline void aggregate(const PFS_table_io_stat *stat) {
                if (stat->m_has_data) {
                        m_has_data = true;
                        m_fetch.aggregate(&stat->m_fetch);
                        m_insert.aggregate(&stat->m_insert);
                        m_update.aggregate(&stat->m_update);
                        m_delete.aggregate(&stat->m_delete);
                }
        }
        // 对所有操作的统计信息进行求和，并将结果存储在给定的 PFS_single_stat 对象中。只有在 m_has_data 为 true 时才执行求和操作
        inline void sum(PFS_single_stat *result) {
                if (m_has_data) {
                        result->aggregate(&m_fetch);
                        result->aggregate(&m_insert);
                        result->aggregate(&m_update);
                        result->aggregate(&m_delete);
                }
        }
};
```

##### PFS_single_stat

| 成员变量 | 默认值               | 说明       |
|:---------|:---------------------|:-----------|
| m_count  | 0                    |            |
| m_sum    | 0                    |            |
| m_min    | 18446744073709551615 | ULLONG_MAX |
| m_max    | 0                    |            |


```c++
// 用于存储单个统计信息的数据
struct PFS_single_stat {
        // 存储值的数量或计数
        ulonglong m_count;
        // 存储所有值的总和
        ulonglong m_sum;
        // 存储最小值
        ulonglong m_min;
        // 存储最大值
        ulonglong m_max;
        // 默认构造函数，用于初始化所有数据成员
        PFS_single_stat() {
                m_count = 0;
                m_sum   = 0;
                m_min   = ULLONG_MAX;
                m_max   = 0;
        }
        //  重置所有数据成员为默认值
        inline void reset(void) {
                m_count = 0;
                m_sum   = 0;
                m_min   = ULLONG_MAX;
                m_max   = 0;
        }
        // 判断是否存在时间相关的统计信息
        inline bool has_timed_stats() const {
                return (m_min <= m_max);
        }
        // 将另一个 PFS_single_stat 结构体的数据合并到当前结构体中,合并时会检查被合并的结构体中的计数是否为零，如果不为零，则将其数据合并到当前结构体中
        inline void aggregate(const PFS_single_stat *stat) {
                if (stat->m_count != 0) {
                        m_count += stat->m_count;
                        m_sum += stat->m_sum;
                        if (unlikely(m_min > stat->m_min)) {
                                m_min = stat->m_min;
                        }
                        if (unlikely(m_max < stat->m_max)) {
                                m_max = stat->m_max;
                        }
                }
        }
        // 将另一个 PFS_single_stat 结构体的数据合并到当前结构体中,但不进行计数是否为零的检查，直接将数据合并到当前结构体中
        inline void aggregate_no_check(const PFS_single_stat *stat) {
                m_count += stat->m_count;
                m_sum += stat->m_sum;
                if (unlikely(m_min > stat->m_min)) {
                        m_min = stat->m_min;
                }
                if (unlikely(m_max < stat->m_max)) {
                        m_max = stat->m_max;
                }
        }
        // 将计数值加一，用于对数量进行简单的计数
        inline void aggregate_counted() {
                m_count++;
        }
        // 增加计数值。将给定的计数值加到当前计数值上
        inline void aggregate_counted(ulonglong count) {
                m_count += count;
        }
        // 将给定的值加到总和中，并更新最小值和最大值
        inline void aggregate_value(ulonglong value) {
                m_count++;
                m_sum += value;
                if (unlikely(m_min > value)) {
                        m_min = value;
                }
                if (unlikely(m_max < value)) {
                        m_max = value;
                }
        }
        // 增加多个值。将给定值的多个副本的总和加到当前总和中，并更新最小值和最大值
        inline void aggregate_many_value(ulonglong value, ulonglong count) {
                m_count += count;
                m_sum += value;
                if (unlikely(m_min > value)) {
                        m_min = value;
                }
                if (unlikely(m_max < value)) {
                        m_max = value;
                }
        }
};
```

###
- global_idle_stat.reset()
- global_table_io_stat.reset()
- global_table_lock_stat.reset()

```c++
// 定义内存对齐的宏，提高内存访问的效率
#if defined(HAVE_POSIX_MEMALIGN) || defined(HAVE_MEMALIGN) || defined(HAVE_ALIGNED_MALLOC)
        #define PFS_ALIGNEMENT 64
        #define PFS_ALIGNED alignas(PFS_ALIGNEMENT)
#else

        #define PFS_ALIGNED
#endif
// 定义全局变量
// 空闲状态相关的统计信息
PFS_ALIGNED PFS_single_stat global_idle_stat;
// 存储表的 I/O 统计信息,包括各种类型的 I/O 操作（例如 FETCH、INSERT、UPDATE、DELETE）的统计信息
PFS_ALIGNED PFS_table_io_stat global_table_io_stat;
// 存储表锁的统计信息，包括不同类型表锁（如 Metadata Locks、Table Locks、Intent Locks 等）的统计信息
PFS_ALIGNED PFS_table_lock_stat global_table_lock_stat;
PFS_ALIGNED PFS_single_stat global_metadata_stat;
PFS_ALIGNED PFS_transaction_stat global_transaction_stat;
PFS_ALIGNED PFS_error_stat global_error_stat;
PFS_ALIGNED PFS_instr_class global_table_io_class;
PFS_ALIGNED PFS_instr_class global_table_lock_class;
PFS_ALIGNED PFS_instr_class global_idle_class;
PFS_ALIGNED PFS_instr_class global_metadata_class;
PFS_ALIGNED PFS_error_class global_error_class;
PFS_ALIGNED PFS_transaction_class global_transaction_class;

/** Single statistic. */
struct PFS_single_stat {
        /** Count of values. */
        ulonglong m_count;
        /** Sum of values. */
        ulonglong m_sum;
        /** Minimum value. */
        ulonglong m_min;
        /** Maximum value. */
        ulonglong m_max;

        PFS_single_stat() {
                m_count = 0;
                m_sum   = 0;
                m_min   = ULLONG_MAX;
                m_max   = 0;
        }

        inline void reset(void) {
                m_count = 0;
                m_sum   = 0;
                m_min   = ULLONG_MAX;
                m_max   = 0;
        }

        inline bool has_timed_stats() const {
                return (m_min <= m_max);
        }

        inline void aggregate(const PFS_single_stat *stat) {
                if (stat->m_count != 0) {
                        m_count += stat->m_count;
                        m_sum += stat->m_sum;
                        if (unlikely(m_min > stat->m_min)) {
                                m_min = stat->m_min;
                        }
                        if (unlikely(m_max < stat->m_max)) {
                                m_max = stat->m_max;
                        }
                }
        }

        inline void aggregate_no_check(const PFS_single_stat *stat) {
                m_count += stat->m_count;
                m_sum += stat->m_sum;
                if (unlikely(m_min > stat->m_min)) {
                        m_min = stat->m_min;
                }
                if (unlikely(m_max < stat->m_max)) {
                        m_max = stat->m_max;
                }
        }

        inline void aggregate_counted() {
                m_count++;
        }

        inline void aggregate_counted(ulonglong count) {
                m_count += count;
        }

        inline void aggregate_value(ulonglong value) {
                m_count++;
                m_sum += value;
                if (unlikely(m_min > value)) {
                        m_min = value;
                }
                if (unlikely(m_max < value)) {
                        m_max = value;
                }
        }

        inline void aggregate_many_value(ulonglong value, ulonglong count) {
                m_count += count;
                m_sum += value;
                if (unlikely(m_min > value)) {
                        m_min = value;
                }
                if (unlikely(m_max < value)) {
                        m_max = value;
                }
        }
};

struct PFS_table_io_stat {
        bool m_has_data;
        /** FETCH statistics */
        PFS_single_stat m_fetch;
        /** INSERT statistics */
        PFS_single_stat m_insert;
        /** UPDATE statistics */
        PFS_single_stat m_update;
        /** DELETE statistics */
        PFS_single_stat m_delete;

        PFS_table_io_stat() {
                m_has_data = false;
        }

        inline void reset(void) {
                m_has_data = false;
                m_fetch.reset();
                m_insert.reset();
                m_update.reset();
                m_delete.reset();
        }

        inline void aggregate(const PFS_table_io_stat *stat) {
                if (stat->m_has_data) {
                        m_has_data = true;
                        m_fetch.aggregate(&stat->m_fetch);
                        m_insert.aggregate(&stat->m_insert);
                        m_update.aggregate(&stat->m_update);
                        m_delete.aggregate(&stat->m_delete);
                }
        }

        inline void sum(PFS_single_stat *result) {
                if (m_has_data) {
                        result->aggregate(&m_fetch);
                        result->aggregate(&m_insert);
                        result->aggregate(&m_update);
                        result->aggregate(&m_delete);
                }
        }
};

struct PFS_table_lock_stat {
        PFS_single_stat m_stat[COUNT_PFS_TL_LOCK_TYPE];

        inline void reset(void) {
                PFS_single_stat *pfs      = &m_stat[0];
                PFS_single_stat *pfs_last = &m_stat[COUNT_PFS_TL_LOCK_TYPE];
                for (; pfs < pfs_last; pfs++) {
                        pfs->reset();
                }
        }

        inline void aggregate(const PFS_table_lock_stat *stat) {
                PFS_single_stat *pfs            = &m_stat[0];
                PFS_single_stat *pfs_last       = &m_stat[COUNT_PFS_TL_LOCK_TYPE];
                const PFS_single_stat *pfs_from = &stat->m_stat[0];
                for (; pfs < pfs_last; pfs++, pfs_from++) {
                        pfs->aggregate(pfs_from);
                }
        }

        inline void sum(PFS_single_stat *result) {
                PFS_single_stat *pfs      = &m_stat[0];
                PFS_single_stat *pfs_last = &m_stat[COUNT_PFS_TL_LOCK_TYPE];
                for (; pfs < pfs_last; pfs++) {
                        result->aggregate(pfs);
                }
        }
};
```











### 直方图统计初始化与置零

- g_histogram_pico_timers.init()
- global_statements_histogram.reset()

```c++
// 这种直方图可以用于记录和分析 MySQL 服务器中各种操作的耗时分布情况
// 450个桶
#define NUMBER_OF_BUCKETS 450
// 基本值：1000万
#define BUCKET_BASE_TIMER (10 * 1000 * 1000)
// 倍率系数为：1.0471285480508996
#define BUCKET_BASE_FACTOR 1.0471285480508996

struct PFS_histogram_timers {
        // 直方图中的桶的数量：450+1
        ulonglong m_bucket_timer[NUMBER_OF_BUCKETS + 1];

        void init();
};

// 定义了一个全局变量 g_histogram_pico_timers用于在整个 MySQL 服务器中跟踪性能统计直方图的定时器信息
PFS_histogram_timers g_histogram_pico_timers;
//
PFS_histogram global_statements_histogram;

// 函数用于重置直方图中各个桶的计数值
// 在函数中，通过循环遍历直方图中的每个桶，并将其计数值重置为 0。这样做的目的是清空之前收集的统计信息，以便开始新的统计周期
void PFS_histogram::reset() {
        ulong bucket_index;

        for (bucket_index = 0; bucket_index < NUMBER_OF_BUCKETS; bucket_index++) {
                m_bucket[bucket_index] = 0;
        }
}

// 初始化直方图定时器
// 该函数也通过循环遍历直方图中的每个桶，并设置相应的计时器值。每个桶的计时器值通过一个递增的倍率系数 BUCKET_BASE_FACTOR 与前一个桶的计时器值相乘得到。
// 直到最后一个桶的计时器值被设置为最大整数值 UINT64_MAX，表示该桶的计时器值将不再递增
// 0,10000000,....,9549925860214686,18446744073709551615UL
void PFS_histogram_timers::init() {
        ulong bucket_index;
        double current_bucket_timer = BUCKET_BASE_TIMER;

        m_bucket_timer[0] = 0;

        for (bucket_index = 1; bucket_index < NUMBER_OF_BUCKETS; bucket_index++) {
                m_bucket_timer[bucket_index] = current_bucket_timer;
                current_bucket_timer *= BUCKET_BASE_FACTOR;
        }
        // UINT64_MAX = 18446744073709551615UL
        m_bucket_timer[NUMBER_OF_BUCKETS] = UINT64_MAX;
}

```












