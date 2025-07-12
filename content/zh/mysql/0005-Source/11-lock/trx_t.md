---
title: 事务对象
description: trx_t
date: 2023-10-30
weight: 11000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;

{{<alert color="danger" title="注意" >}}

1. 在Innodb中，每次开启一个事务时，都会为该session分配一个事务对象`trx_t`。每个连接（`session`/会话）最多持有一个`trx_t`对象

2. 为了对全局所有的事务进行控制和协调，事务启动后不管读写，把这个`trx_t`对象加入到全局事务链表中 (`trx_sys->mysql_trx_list`)，对trx_sys相关成员的操作需要trx_sys->mutex锁；

3. 如果转换为读写事务，还会加入到全局读写事务链表中 (`trx_sys->rw_trx_list`)

4. 读写事务在开启时（更确切的说是在分配回滚段时）通过全局 id 产生器产生以区分不同的写事务（这里 trx_id 只读事务为0，只读事务只需要通过指针地址来获取区分，如果只读事务需要写临时表，也会分配）；同时，还会分配回滚段给 trx_t 以供记录 undo record。









{{</alert>}}




## 事务状态：trx_state_t
```c++
/** Transaction states (trx_t::state) */
enum trx_state_t {
        TRX_STATE_NOT_STARTED,

        /** Same as not started but with additional semantics that it
        was rolled back asynchronously the last time it was active. */
        TRX_STATE_FORCED_ROLLBACK,

        TRX_STATE_ACTIVE,

        /** Support for 2PC/XA */
        TRX_STATE_PREPARED,

        TRX_STATE_COMMITTED_IN_MEMORY
};

```


事务状态：
- **TRX_STATE_ACTIVE：** 未提交事务，也就是活跃事务。崩溃恢复过程中，这种状态的事务是需要直接回滚的。
- **TRX_STATE_PREPARED：** PREPARE 事务。这种状态的事务比较特殊，在崩溃恢复过程中，既有可能被提交，也有可能被回滚。PREPARE 事务提交还是回滚，取决于这个事务的 XID 是否已经写入到 binlog 日志文件中。
  - 如果事务 XID 在集合中，说明 MySQL 崩溃之前，事务 XID_EVENT 就已经写入 binlog 日志文件了。XID_EVENT 有可能已经同步给从服务器，从服务器上可能已经重放了这个事务。这种情况下，为了保证主从数据的一致性，事务在主服务器上也需要提交。
  - 如果事务 XID 不在集合中，说明 MySQL 崩溃之前，事务 XID_EVENT 没有写入 binlog 日志文件。XID_EVENT 肯定也就没有同步给从服务器了，同样为了保证主从数据的一致性，事务在主服务器上也不能提交，而是需要回滚。
- **TRX_STATE_COMMITTED_IN_MEMORY：** 已提交事务，包含 DDL 和 DML 事务。这种状态的事务已经完成二阶段提交的 PREPARE 和 COMMIT 阶段，是已经提交成功的事务，只差最后一点点清理工作，它们修改的数据已经能被其它事务看见了。


有效的状态转移模式:
    1. Regular transactions:
      * NOT_STARTED -> ACTIVE -> COMMITTED -> NOT_STARTED
    2. Auto-commit non-locking read-only:
      * NOT_STARTED -> ACTIVE -> NOT_STARTED
    3. XA (2PC):
      * NOT_STARTED -> ACTIVE -> PREPARED -> COMMITTED -> NOT_STARTED
    4. Recovered XA:
      * NOT_STARTED -> PREPARED -> COMMITTED -> (freed)
    5. XA (2PC) (shutdown or disconnect before ROLLBACK or COMMIT):
      * NOT_STARTED -> PREPARED -> (freed)
    6. Disconnected XA can become recovered:
      * ... -> ACTIVE -> PREPARED (connected) -> PREPARED (disconnected)













## 事务对象：trx_t






| 成员                           | 类型                                                             | 功能                                     |
| :----------------------------- | :--------------------------------------------------------------- | :--------------------------------------- |
| `mutex`                        | `TrxMutex`                                                       | 互斥锁，多线程环境下保护`state`和`lock`  |
| `in_depth`                     | `uint32_t`                                                       | 事务递归深度                             |
| `in_innodb`                    | `uint32_t`                                                       | 事务在 InnoDB 中的状态                   |
| `abort`                        | `bool`                                                           | 是否需要中止事务                         |
| `id`                           | `trx_id_t`                                                       | 事务id，在开启读写事务或分配回滚段时赋值 |
| `no`                           | `trx_id_t`                                                       | 事务number，在事务提交阶段赋值           |
| `state`                        | `std::atomic<trx_state_t>`                                       |                                          |
| `skip_lock_inheritance`        | `bool`                                                           |                                          |
| `read_view`                    | `ReadView *`                                                     |                                          |
| `trx_list`                     | `UT_LIST_NODE_T(trx_t)`                                          | 读写事务                                 |
| `no_list`                      | `UT_LIST_NODE_T(trx_t)`                                          | 提交事务                                 |
| `lock`                         | `trx_lock_t`                                                     | 事务锁信息                               |
| `is_recovered`                 | `bool`                                                           |                                          |
| `killed_by`                    | `std::atomic<std::thread::id>`                                   |                                          |
| `op_info`                      | `const char *`                                                   |                                          |
| `isolation_level`              | `isolation_level_t`                                              | 隔离等级                                 |
| `check_foreigns`               | `bool`                                                           |                                          |
| `is_registered`                | `bool`                                                           |                                          |
| `check_unique_secondary`       | `bool`                                                           |                                          |
| `flush_log_later`              | `bool`                                                           |                                          |
| `must_flush_log_later`         | `bool`                                                           |                                          |
| `has_search_latch`             | `bool`                                                           |                                          |
| `dict_operation`               | `trx_dict_op_t`                                                  |                                          |
| `ddl_operation`                | `bool`                                                           |                                          |
| `ddl_must_flush`               | `bool`                                                           |                                          |
| `in_truncate`                  | `bool`                                                           |                                          |
| `declared_to_be_inside_innodb` | `bool`                                                           |                                          |
| `n_tickets_to_enter_innodb`    | `uint32_t`                                                       | 进入`InnoDB`的门票数量                   |
| `dict_operation_lock_mode`     | `uint32_t`                                                       |                                          |
| `start_time`                   | `std::atomic<std::chrono::system_clock::time_point>`             |                                          |
| `commit_lsn`                   | `lsn_t`                                                          |                                          |
| `mysql_thd`                    | `THD *`                                                          |                                          |
| `mysql_log_file_name`          | `const char *`                                                   |                                          |
| `mysql_log_offset`             | `uint64_t`                                                       |                                          |
| `n_mysql_tables_in_use`        | `uint32_t`                                                       |                                          |
| `mysql_n_tables_locked`        | `uint32_t`                                                       |                                          |
| `in_rw_trx_list`               | `bool`                                                           | 调试开启时，才有                         |
| `mysql_trx_list`               | `UT_LIST_NODE_T(trx_t)`                                          | 所有事务                                 |
| `in_mysql_trx_list`            | `bool`                                                           | 调试开启时，才有                         |
| `error_state`                  | `dberr_t`                                                        |                                          |
| `error_index`                  | `const dict_index_t *`                                           |                                          |
| `error_key_num`                | `ulint`                                                          |                                          |
| `sess`                         | `sess_t *`                                                       |                                          |
| `graph`                        | `que_t *`                                                        |                                          |
| `trx_savepoints`               | `UT_LIST_BASE_NODE_T_EXTERN(trx_named_savept_t, trx_savepoints)` |                                          |
| `undo_mutex`                   | `UndoMutex`                                                      |                                          |
| `undo_no`                      | `undo_no_t`                                                      |                                          |
| `undo_rseg_space`              | `space_id_t`                                                     |                                          |
| `last_sql_stat_start`          | `trx_savept_t`                                                   |                                          |
| `rsegs`                        | `trx_rsegs_t`                                                    |                                          |
| `roll_limit`                   | `undo_no_t`                                                      |                                          |
| `in_rollback`                  | `bool`                                                           | 调试开启时，才有                         |
| `pages_undone`                 | `ulint`                                                          |                                          |
| `n_autoinc_rows`               | `ulint`                                                          |                                          |
| `read_only`                    | `bool`                                                           |                                          |
| `auto_commit`                  | `bool`                                                           |                                          |
| `will_lock`                    | `uint32_t`                                                       |                                          |
| `fts_trx`                      | `fts_trx_t *`                                                    |                                          |
| `fts_next_doc_id`              | `doc_id_t`                                                       |                                          |
| `flush_tables`                 | `uint32_t`                                                       |                                          |
| `internal`                     | `bool`                                                           |                                          |
| `persists_gtid`                | `bool`                                                           |                                          |
| `start_line`                   | `bool`                                                           | 调试开启时，才有                         |
| `start_file`                   | `const char *`                                                   | 调试开启时，才有                         |
| `n_ref`                        | `lint`                                                           |                                          |
| `version`                      | `std::atomic_uint64_t`                                           |                                          |
| `xid`                          | `XID *`                                                          |                                          |
| `mod_tables`                   | `trx_mod_tables_t`                                               |                                          |
| `api_trx`                      | `bool`                                                           |                                          |
| `api_auto_commit`              | `bool`                                                           |                                          |
| `read_write`                   | `bool`                                                           |                                          |
| `purge_sys_trx`                | `bool`                                                           |                                          |
| `detailed_error`               | `char *`                                                         |                                          |
| `flush_observer`               | `Flush_observer`                                                 |                                          |
| `is_dd_trx`                    | `bool`                                                           | 调试开启时，才有                         |
| `magic_n`                      | `ulint`                                                          |                                          |




```c++
struct trx_t {
        // 定义了事务的隔离级别
        enum isolation_level_t {
                READ_UNCOMMITTED,
                READ_COMMITTED,
                SERIALIZABLE
        };
        //
        trx_t() = default;
        // 用于保护事务的互斥锁，确保多线程环境下的访问安全
        mutable TrxMutex mutex;
        // 事务递归深度
        uint32_t in_depth;
        // 事务在 InnoDB 中的状态
        uint32_t in_innodb;
        // 是否需要中止事务
        bool abort;
        // 事务的唯一标识符:id+no
        trx_id_t id;
        trx_id_t no;
        // 事务的状态，使用原子类型确保多线程环境下的安全访问
        std::atomic<trx_state_t> state;
        // 是否跳过锁继承
        bool skip_lock_inheritance;
        // 读视图
        ReadView *read_view;
        // 双向链表节点，用于连接事务列表
        UT_LIST_NODE_T(trx_t)
        trx_list;
        UT_LIST_NODE_T(trx_t)
        no_list;
        // 事务锁
        trx_lock_t lock;
        // 是否是恢复的事务
        bool is_recovered;
        // 杀死事务的线程
        std::atomic<std::thread::id> killed_by;

        // SQL操作类型信息，innodb_trx表中的字段trx_operation_state
        const char *op_info;

        // 事务的隔离级别
        isolation_level_t isolation_level;

        // 用于检查外键、注册和检查唯一性
        bool check_foreigns;
        bool is_registered;
        bool check_unique_secondary;

        // 用于延迟日志刷新和搜索锁
        bool flush_log_later;
        bool must_flush_log_later;
        bool has_search_latch;
        // 数据字典操作
        trx_dict_op_t dict_operation;
        // DDL 操作相关的标志
        bool ddl_operation;
        bool ddl_must_flush;
        bool in_truncate;

        bool declared_to_be_inside_innodb;
        // 进入 InnoDB 的门票数量
        uint32_t n_tickets_to_enter_innodb;

        uint32_t dict_operation_lock_mode;
        // 事务开始时间
        std::atomic<std::chrono::system_clock::time_point> start_time{
            std::chrono::system_clock::time_point{}};
        static_assert(decltype(start_time)::is_always_lock_free);
        // 提交 LSN
        lsn_t commit_lsn;
        // MySQL 相关的线程、日志文件名和偏移量。
        THD *mysql_thd;
        const char *mysql_log_file_name;
        uint64_t mysql_log_offset;
        // MySQL 使用的表数和锁定的表数
        uint32_t n_mysql_tables_in_use;
        uint32_t mysql_n_tables_locked;

#ifdef UNIV_DEBUG

        bool in_rw_trx_list;

#endif
        UT_LIST_NODE_T(trx_t)
        mysql_trx_list;
#ifdef UNIV_DEBUG
        bool in_mysql_trx_list;

#endif
        // 错误状态、错误索引和错误键数
        dberr_t error_state;
        const dict_index_t *error_index;
        ulint error_key_num;
        // 会话和图
        sess_t *sess;
        que_t *graph;
        // 事务保存点
        UT_LIST_BASE_NODE_T_EXTERN(trx_named_savept_t, trx_savepoints)
        trx_savepoints{};
        // 用于 UNDO 相关的互斥锁和空间
        UndoMutex undo_mutex;
        undo_no_t undo_no;
        space_id_t undo_rseg_space;
        // 最后的 SQL 语句开始时间、RSEG、回滚限制、已撤销页数、自增行数
        trx_savept_t last_sql_stat_start;
        trx_rsegs_t rsegs;
        undo_no_t roll_limit;
#ifdef UNIV_DEBUG
        bool in_rollback;
#endif
        ulint pages_undone;
        ulint n_autoinc_rows;
        // 读写属性、自动提交和锁定
        bool read_only;
        bool auto_commit;
        uint32_t will_lock;
#ifndef UNIV_HOTBACKUP
        fts_trx_t *fts_trx;
        doc_id_t fts_next_doc_id;
        uint32_t flush_tables;
        bool internal;
        bool persists_gtid;

#ifdef UNIV_DEBUG
        ulint start_line;
        const char *start_file;
#endif

        lint n_ref;
        std::atomic_uint64_t version;

        XID *xid;
        trx_mod_tables_t mod_tables;
#endif
        // API 属性和读写
        bool api_trx;
        bool api_auto_commit;
        bool read_write;
        // 是否为系统事务
        bool purge_sys_trx;
        // 详细错误和刷新观察者
        char *detailed_error;
        Flush_observer *flush_observer;

#ifdef UNIV_DEBUG
        bool is_dd_trx;
#endif
        // 魔术数字
        ulint magic_n;
        // 是否读取未提交数据
        bool is_read_uncommitted() const {
                return (isolation_level == READ_UNCOMMITTED);
        }
        // 是否在准备时释放间隙锁
        bool releases_gap_locks_at_prepare() const {
                return isolation_level <= READ_COMMITTED;
        }
        // 是否跳过间隙锁
        bool skip_gap_locks() const {
                switch (isolation_level) {
                case READ_UNCOMMITTED:
                case READ_COMMITTED:
                        return (true);
                case REPEATABLE_READ:
                case SERIALIZABLE:
                        return (false);
                }
                ut_d(ut_error);
                ut_o(return (false));
        }
        // 允许半一致性
        bool allow_semi_consistent() const { return (skip_gap_locks()); }
        // 释放不匹配的行
        bool releases_non_matching_rows() const { return skip_gap_locks(); }
};

```


## 创建事务对象：trx_allocate_for_mysql

```c++
/** Creates a transaction object for MySQL.
 @return own: transaction object */
trx_t *trx_allocate_for_mysql(void) {
        trx_t *trx;

        trx = trx_allocate_for_background();

        trx_sys_mutex_enter();

        ut_d(trx->in_mysql_trx_list = true);
        UT_LIST_ADD_FIRST(trx_sys->mysql_trx_list, trx);

        trx_sys_mutex_exit();

        return (trx);
}

```


## innobase_trx_allocate

```c++
/** Allocates an InnoDB transaction for a MySQL handler object for DML.
 @return InnoDB transaction handle */
trx_t *innobase_trx_allocate(THD *thd) /*!< in: user thread handle */
{
        trx_t *trx;

        DBUG_TRACE;
        assert(thd != nullptr);
        assert(EQ_CURRENT_THD(thd));

        MONITOR_ATOMIC_INC(MONITOR_TRX_ALLOCATIONS);
        trx = trx_allocate_for_mysql();

        rw_lock_s_lock(&purge_sys->latch, UT_LOCATION_HERE);

        if (purge_sys->thds.find(thd) != purge_sys->thds.end()) {
                trx->purge_sys_trx = true;
        }

        rw_lock_s_unlock(&purge_sys->latch);

        trx->mysql_thd = thd;

        innobase_trx_init(thd, trx);

        return trx;
}

```





