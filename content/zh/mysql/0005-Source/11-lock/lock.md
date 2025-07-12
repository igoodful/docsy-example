---
title: 06. my_init
description: my_init
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
- 事务的隔离级别
- 语句执行时使用的索引类型（比如聚簇索引、唯一二级索引、普通二级索引）
- 是否是精确匹配
- 是否是唯一性搜索
- 具体执行的语句类型（SELECT、INSERT、DELETE、UPDATE）
- 记录是否被标记删除
- 是否开启innodb_locks_unsafe_for_binlog系统变量


{{< /alert >}}


## 整体情况
```viz-dot
digraph "my_init" {
        rankdir=TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "my_init" -> "my_init_done";
        "my_init_done" -> "my_umask=0640";
        "my_umask=0640" -> "my_umask_dir=0750";
        "my_umask_dir=0750" -> "UMASK";
        "UMASK" -> "UMASK_DIR";
        "UMASK_DIR" -> "my_thread_global_init()" ;
        "my_thread_global_init()" -> "my_thread_init()" ;
        "my_thread_init()" -> "HOME" ;
        "HOME" -> "home_dir" ;
        "home_dir" -> "MyFileInit()" ;

}
```


```c++
  uint32_t type_mode;
```
| 24位rec_lock_type | 4位lock_type | 4位lock_mode |
| :---------------- | :----------- | :----------- |

> 这是一个32位的数，被分成了lock_mode、lock_type和rec_lock_type三个部分。lock_mode占用低4位，
>
> lock_type，第5到8位


锁的模式（lock_mode），占用低4位，可选的值如下：

- LOCK_IS（十进制的0）：表示共享意向锁，也就是IS锁。
- LOCK_IX（十进制的1）：表示独占意向锁，也就是IX锁。
- LOCK_S（十进制的2）：表示共享锁，也就是S锁。
- LOCK_X（十进制的3）：表示独占锁，也就是X锁。
- LOCK_AUTO_INC（十进制的4）：表示AUTO-INC锁。



锁的类型（lock_type），占用第5～8位，不过现阶段只有第5位和第6位被使用：

- LOCK_TABLE（十进制的16），也就是当第5个比特位置为1时，表示表级锁。
- LOCK_REC（十进制的32），也就是当第6个比特位置为1时，表示行级锁。


行锁的具体类型（rec_lock_type），使用其余的位来表示。只有在lock_type的值为LOCK_REC时，也就是只有在该锁为行级锁时，才会被细分为更多的类型：

- LOCK_ORDINARY（十进制的0）：表示next-key锁。
- LOCK_GAP（十进制的512）：也就是当第10个比特位置为1时，表示gap锁。
- LOCK_REC_NOT_GAP（十进制的1024）：也就是当第11个比特位置为1时，表示行锁。
- LOCK_INSERT_INTENTION（十进制的2048）：也就是当第12个比特位置为1时，表示插入意向锁。
其他的类型：还有一些不常用的类型。






```c++
/* Note: must match lock0types.h */
/** @enum ib_lck_mode_t InnoDB lock modes. */
typedef enum {
  IB_LOCK_IS = 0, /*!< Intention shared, an intention
                  lock should be used to lock tables */

  IB_LOCK_IX, /*!< Intention exclusive, an intention
              lock should be used to lock tables */

  IB_LOCK_S, /*!< Shared locks should be used to
             lock rows */

  IB_LOCK_X, /*!< Exclusive locks should be used to
             lock rows*/

  IB_LOCK_TABLE_X, /*!< exclusive table lock */

  IB_LOCK_NONE, /*!< This is used internally to note
                consistent read */

  IB_LOCK_NUM = IB_LOCK_NONE /*!< number of lock modes */
} ib_lck_mode_t;
```




```c++

#define LOCK_MODE_MASK  0xFUL
#define LOCK_TABLE 16 /*!< table lock */
#define LOCK_REC 32   /*!< record lock */
#define LOCK_TYPE_MASK 0xF0UL
#if LOCK_MODE_MASK & LOCK_TYPE_MASK
#error "LOCK_MODE_MASK & LOCK_TYPE_MASK"
#endif

#define LOCK_WAIT 256
#define LOCK_ORDINARY 0
#define LOCK_GAP 512
#define LOCK_REC_NOT_GAP 1024
#define LOCK_INSERT_INTENTION 2048
#define LOCK_PREDICATE 8192  /*!< Predicate lock */
#define LOCK_PRDT_PAGE 16384 /*!< Page lock */
```

- LOCK_TABLE 表级锁
- LOCK_REC 行级锁
  - LOCK_ORDINARY next-key锁
  - LOCK_GAP gap锁
  - LOCK_REC_NOT_GAP 正常记录锁
- LOCK_INSERT_INTENTION 插入意向锁
- LOCK_WAIT 表示is_waiting为false，表明当前事务获取锁成功




## 锁系统结构：lock_sys_t

```c++
typedef ib_mutex_t Lock_mutex;

/** The lock system struct */
struct lock_sys_t {
        /** The latches protecting queues of record and table locks */
        locksys::Latches latches;

        /** The hash table of the record (LOCK_REC) locks, except for predicate
        (LOCK_PREDICATE) and predicate page (LOCK_PRDT_PAGE) locks */
        hash_table_t *rec_hash;

        /** The hash table of predicate (LOCK_PREDICATE) locks */
        hash_table_t *prdt_hash;

        /** The hash table of the predicate page (LOCK_PRD_PAGE) locks */
        hash_table_t *prdt_page_hash;

        /** Padding to avoid false sharing of wait_mutex field */
        char pad2[ut::INNODB_CACHE_LINE_SIZE];

        /** The mutex protecting the next two fields */
        Lock_mutex wait_mutex;

        /** Array of user threads suspended while waiting for locks within InnoDB.
        Protected by the lock_sys->wait_mutex. */
        srv_slot_t *waiting_threads;

        /** The highest slot ever used in the waiting_threads array.
        Protected by lock_sys->wait_mutex. */
        srv_slot_t *last_slot;

        /** true if rollback of all recovered transactions is complete.
        Protected by exclusive global lock_sys latch. */
        bool rollback_complete;

        /** Max lock wait time observed, for innodb_row_lock_time_max reporting. */
        std::chrono::steady_clock::duration n_lock_max_wait_time;

        /** Set to the event that is created in the lock wait monitor thread. A value
        of 0 means the thread is not active */
        os_event_t timeout_event;

#ifdef UNIV_DEBUG
        /** Lock timestamp counter, used to assign lock->m_seq on creation. */
        std::atomic<uint64_t> m_seq;
#endif /* UNIV_DEBUG */
};

```


## 锁结构：lock_t

```viz-dot
digraph "lock_t" {
        rankdir=LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = rect;
                fontsize = "12"
                ];
        "lock_t" -> "trx" [label="trx_t"];
        "lock_t" -> "trx_locks" [label="ut_list_node<ib_lock_t>"];
        "lock_t" -> "index" [label="dict_index_t"];
        "lock_t" -> "hash" [label="lock_t"];
        "lock_t" -> "tab_lock" [label="lock_table_t" style="dashed"];
        "lock_t" -> "rec_lock" [label="lock_rec_t" style="dashed"];
        "lock_t" -> "m_psi_internal_thread_id" [label="ulonglong"];
        "lock_t" -> "m_psi_event_id" [label="ulonglong"];
        "lock_t" -> "type_mode" [label="uint32_t"];
        "lock_t" -> "m_seq" [label="uint64_t" style="dotted"];
        "trx" -> "isolation_level_t" [label="isolation_level_t"];
        "trx" -> "mutex" [label="TrxMutex"];
        "trx" -> "in_depth" [label="uint32_t"];
        "trx" -> "in_innodb" [label="uint32_t"];
        "trx" -> "abort" [label="bool"];
        "trx" -> "id" [label="trx_id_t"];
        "trx" -> "no" [label="trx_id_t"];
        "trx" -> "state" [label="std::atomic<trx_state_t>"];
        "trx" -> "skip_lock_inheritance" [label="bool"];
        "trx" -> "read_view" [label="ReadView *"];
        "trx" -> "trx_list" [label="ut_list_node<trx_t>"];
        "trx" -> "no_list" [label="ut_list_node<trx_t>"];
        "trx" -> "lock" [label="trx_lock_t"];
        "trx" -> "is_recovered" [label="bool"];
        "trx" -> "killed_by" [label="std::atomic<std::thread::id>"];
        "trx" -> "op_info" [label="const char *"];
        "trx" -> "isolation_level" [label="isolation_level_t"];
        "trx" -> "check_foreigns" [label="bool"];
        "trx" -> "is_registered" [label="bool"];
        "trx" -> "check_unique_secondary" [label="bool"];
        "trx" -> "flush_log_later" [label="bool"];
        "trx" -> "must_flush_log_later" [label="bool"];
        "trx" -> "has_search_latch" [label="bool"];
        "trx" -> "dict_operation" [label="trx_dict_op_t"];
        "trx" -> "ddl_operation" [label="bool"];
        "trx" -> "ddl_must_flush" [label="bool"];
        "trx" -> "in_truncate" [label="bool"];
        "trx" -> "declared_to_be_inside_innodb" [label="bool"];
        "trx" -> "n_tickets_to_enter_innodb" [label="uint32_t"];
        "trx" -> "dict_operation_lock_mode" [label="uint32_t"];
        "trx" -> "commit_lsn" [label="lsn_t"];
        "trx" -> "mysql_thd" [label="THD *"];
        "trx" -> "mysql_log_file_name" [label="const char *"];
        "trx" -> "mysql_log_offset" [label="uint64_t"];
        "trx" -> "n_mysql_tables_in_use" [label="uint32_t"];
        "trx" -> "mysql_n_tables_locked" [label="uint32_t"];
        "trx" -> "mysql_trx_list" [label="ut_list_node<trx_t>"];
        "trx" -> "error_state" [label="dberr_t"];
        "trx" -> "error_index" [label="const dict_index_t *"];
        "trx" -> "error_key_num" [label="ulint"];
        "trx" -> "sess" [label="sess_t *"];
        "trx" -> "graph" [label="que_t *"];
        "trx" -> "trx_savepoints";
        "trx" -> "undo_mutex" [label="UndoMutex"];
        "trx" -> "undo_no" [label="undo_no_t"];
        "trx" -> "undo_rseg_space" [label="space_id_t"];
        "trx" -> "last_sql_stat_start" [label="trx_savept_t"];
        "trx" -> "rsegs" [label="trx_rsegs_t"];
        "trx" -> "roll_limit" [label="undo_no_t"];
        "trx" -> "pages_undone" [label="ulint"];
        "trx" -> "n_autoinc_rows" [label="ulint"];
        "trx" -> "read_only" [label="bool"];
        "trx" -> "auto_commit" [label="bool"];
        "trx" -> "will_lock" [label="uint32_t"];
        "trx" -> "fts_trx" [label="fts_trx_t *"];
        "trx" -> "fts_next_doc_id" [label="doc_id_t"];
        "trx" -> "flush_tables" [label="uint32_t"];
        "trx" -> "internal" [label="bool"];
        "trx" -> "persists_gtid" [label="bool"];
        "trx" -> "n_ref" [label="ulint"];
        "trx" -> "version" [label="std::atomic_uint64_t"];
        "trx" -> "api_trx" [label="bool"];
        "trx" -> "api_auto_commit" [label="bool"];
        "trx" -> "read_write" [label="bool"];
        "trx" -> "purge_sys_trx" [label="bool"];
        "trx" -> "detailed_error" [label="char *"];
        "trx" -> "flush_observer" [label="Flush_observer *"];
        "trx" -> "magic_n" [label="ulint"];

        "index_id"[label="id"];
        "index" -> "index_id" [label="space_index_t"];
        "index" -> "heap" [label="mem_heap_t *"];
        "index" -> "name" [label="id_name_t"];
        "index" -> "table_name" [label="const char *"];
        "index" -> "table" [label="dict_table_t *"];
        "index" -> "srid" [label="uint32_t"];
        "index" -> "srid_is_valid" [label="bool"];
        "index" -> "rtr_srs" [label="std::unique_ptr<dd::Spatial_reference_system>"];

        "rec_lock" -> "n_bits";
        "rec_lock" -> "page_id" [label="page_id_t"];
        "page_id" -> "m_space" [label="space_id_t"];
        "page_id" -> "m_page_no" [label="page_no_t"];


}
```
        "tab_lock" -> "table"  [label="dict_table_t"];
        "tab_lock" -> "locks" [label="ut_list_node<ib_lock_t>"];
        "table" -> "id" [label="table_id_t"];
        "table" -> "heap" [label="mem_heap_t*"];
        "table" -> "name" [label="table_name_t"];
        "table" -> "trunc_name" [label="table_name_t"];
        "table" -> "data_dir_path";
        "table" -> "tablespace" [label="id_name_t"];
        "table" -> "space" [label="space_id_t"];
        "table" -> "dd_space_id" [label="dd::Object_id"];
        "table" -> "DICT_TF_BITS" [label="mem_heap_t*"];



```c++
#define UT_LIST_NODE_T(t) ut_list_node<t>

struct lock_t {

        trx_t *trx;
        // ut_list_node<ib_lock_t>
        UT_LIST_NODE_T(lock_t) trx_locks;
        dict_index_t *index;
        lock_t *hash;

        union {
                /** Table lock */
                lock_table_t tab_lock;

                /** Record lock */
                lock_rec_t rec_lock;
        };

#ifdef HAVE_PSI_THREAD_INTERFACE
        #ifdef HAVE_PSI_DATA_LOCK_INTERFACE
        /** Performance schema thread that created the lock. */
        ulonglong m_psi_internal_thread_id;

        /** Performance schema event that created the lock. */
        ulonglong m_psi_event_id;
        #endif /* HAVE_PSI_DATA_LOCK_INTERFACE */
#endif         /* HAVE_PSI_THREAD_INTERFACE */

        /** The lock type and mode bit flags.
        LOCK_GAP or LOCK_REC_NOT_GAP, LOCK_INSERT_INTENTION, wait flag, ORed */
        uint32_t type_mode;

#if defined(UNIV_DEBUG)
        /** Timestamp when it was created. */
        uint64_t m_seq;
#endif /* UNIV_DEBUG */

        /** Unlock the GAP Lock part of this Next Key Lock */
        void unlock_gap_lock() {
                ut_ad(!is_gap());
                ut_ad(!is_insert_intention());
                ut_ad(is_next_key_lock());

                type_mode |= LOCK_REC_NOT_GAP;
        }

        /** Determine if the lock object is a record lock.
        @return true if record lock, false otherwise. */
        bool is_record_lock() const {
                return (type() == LOCK_REC);
        }

        /** Determine if it is predicate lock.
        @return true if predicate lock, false otherwise. */
        bool is_predicate() const {
                return (type_mode & (LOCK_PREDICATE | LOCK_PRDT_PAGE));
        }

        /** @return true if the lock wait flag is set */
        bool is_waiting() const {
                return (type_mode & LOCK_WAIT);
        }

        /** @return true if the gap lock bit is set */
        bool is_gap() const {
                return (type_mode & LOCK_GAP);
        }

        /** @return true if the not gap lock bit is set */
        bool is_record_not_gap() const {
                return (type_mode & LOCK_REC_NOT_GAP);
        }

        /** @return true iff the lock is a Next Key Lock */
        bool is_next_key_lock() const {
                return is_record_lock() && lock_mode_is_next_key_lock(type_mode);
        }

        /** @return true if the insert intention bit is set */
        bool is_insert_intention() const {
                return (type_mode & LOCK_INSERT_INTENTION);
        }

        /** @return true iff this lock is (at least) on supremum pseudo-record */
        bool includes_supremum() const {
                return (lock_rec_get_nth_bit(this, PAGE_HEAP_NO_SUPREMUM));
        }

        /** @return the lock mode */
        uint32_t type() const {
                return (type_mode & LOCK_TYPE_MASK);
        }

        /** @return the precise lock mode */
        lock_mode mode() const {
                return (static_cast<lock_mode>(type_mode & LOCK_MODE_MASK));
        }

        /** Get lock hash table
        @return lock hash table */
        hash_table_t *hash_table() const {
                return (lock_hash_get(type_mode));
        }

        /** @return the transaction's query thread state. */
        trx_que_t trx_que_state() const {
                return (trx->lock.que_state);
        }

        /** Print the lock object into the given output stream.
        @param[in,out]        out     the output stream
        @return the given output stream. */
        std::ostream &print(std::ostream &out) const;

        /** Convert the member 'type_mode' into a human readable string.
        @return human readable string */
        std::string type_mode_string() const;

        /* @return the string/text representation of the record type. */
        const char *type_string() const {
                switch (type_mode & LOCK_TYPE_MASK) {
                        case LOCK_REC:
                                return ("LOCK_REC");
                        case LOCK_TABLE:
                                return ("LOCK_TABLE");
                        default:
                                ut_error;
                }
        }
};
```

## trx_t

```c++
struct trx_t {
        enum isolation_level_t {
                READ_UNCOMMITTED,
                READ_COMMITTED,
                REPEATABLE_READ,
                SERIALIZABLE
        };
        trx_t() = default;
        mutable TrxMutex mutex;
        uint32_t in_depth;
        uint32_t in_innodb;
        bool abort;
        trx_id_t id;
        trx_id_t no;
        std::atomic<trx_state_t> state;
        bool skip_lock_inheritance;
        ReadView *read_view;
        UT_LIST_NODE_T(trx_t)
        trx_list;
        UT_LIST_NODE_T(trx_t)
        no_list;
        trx_lock_t lock;
        bool is_recovered;
        std::atomic<std::thread::id> killed_by;
        const char *op_info;
        isolation_level_t isolation_level;
        bool check_foreigns;
        bool is_registered;
        bool check_unique_secondary;
        bool flush_log_later;
        bool must_flush_log_later;
        bool has_search_latch;
        trx_dict_op_t dict_operation; /**< @see enum trx_dict_op_t */

        bool ddl_operation;  /*!< True if this trx involves dd table
                              change */
        bool ddl_must_flush; /*!< True if this trx involves dd table
                             change, and must flush */
        bool in_truncate;    /* This trx is doing truncation */

        /* Fields protected by the srv_conc_mutex. */
        bool declared_to_be_inside_innodb;

        uint32_t n_tickets_to_enter_innodb;

        uint32_t dict_operation_lock_mode;

        std::atomic<std::chrono::system_clock::time_point> start_time{std::chrono::system_clock::time_point{}};
        static_assert(decltype(start_time)::is_always_lock_free);

        lsn_t commit_lsn; /*!< lsn at the time of the commit */

        /*------------------------------*/
        THD *mysql_thd; /*!< MySQL thread handle corresponding
                        to this trx, or NULL */

        const char *mysql_log_file_name;

        uint64_t mysql_log_offset;

        uint32_t n_mysql_tables_in_use;
        uint32_t mysql_n_tables_locked;

#ifdef UNIV_DEBUG
        /** True iff in trx_sys->rw_trx_list */
        bool in_rw_trx_list;

#endif /* UNIV_DEBUG */
        UT_LIST_NODE_T(trx_t)
        mysql_trx_list; /*!< list of transactions created for
                        MySQL; protected by trx_sys->mutex */
#ifdef UNIV_DEBUG
        bool in_mysql_trx_list;
                /*!< true if in
                trx_sys->mysql_trx_list */
#endif
        dberr_t error_state;

        const dict_index_t *error_index;
        ulint error_key_num;
        sess_t *sess;
        que_t *graph;
        UT_LIST_BASE_NODE_T_EXTERN(trx_named_savept_t, trx_savepoints)
        trx_savepoints{}; /*!< savepoints set with SAVEPOINT ..., oldest first */
        /*------------------------------*/
        UndoMutex undo_mutex;
        undo_no_t undo_no;
        space_id_t undo_rseg_space;
        /*!< space id where last undo record
        was written */
        trx_savept_t last_sql_stat_start;

        trx_rsegs_t rsegs;    /* rollback segments for undo logging */
        undo_no_t roll_limit; /*!< least undo number to undo during
                              a partial rollback; 0 otherwise */
#ifdef UNIV_DEBUG
        bool in_rollback;   /*!< true when the transaction is
                            executing a partial or full rollback */
#endif                      /* UNIV_DEBUG */
        ulint pages_undone;
        /*------------------------------*/
        ulint n_autoinc_rows;
        /*------------------------------*/
        bool read_only;
        bool auto_commit;   /*!< true if it is an autocommit */
        uint32_t will_lock;
#ifndef UNIV_HOTBACKUP
        /*------------------------------*/
        fts_trx_t *fts_trx;
        doc_id_t fts_next_doc_id; /* The document id used for updates */
        /*------------------------------*/
        uint32_t flush_tables;
        bool internal;
        bool persists_gtid;

        #ifdef UNIV_DEBUG
        ulint start_line;       /*!< Track where it was started from */
        const char *start_file; /*!< Filename where it was started */
        #endif                  /* UNIV_DEBUG */

        lint n_ref;
        std::atomic_uint64_t version;

        XID *xid;                    /*!< X/Open XA transaction
                                     identification to identify a
                                     transaction branch */
        trx_mod_tables_t mod_tables; /*!< List of tables that were modified
                                     by this transaction */
#endif                               /* !UNIV_HOTBACKUP */
                                     /*------------------------------*/
        bool api_trx;                /*!< trx started by InnoDB API */
        bool api_auto_commit;        /*!< automatic commit */
        bool read_write;             /*!< if read and write operation */


        bool purge_sys_trx;
        /*------------------------------*/
        char *detailed_error;
        Flush_observer *flush_observer;

#ifdef UNIV_DEBUG
        bool is_dd_trx;
#endif
        ulint magic_n;

        bool is_read_uncommitted() const {
                return (isolation_level == READ_UNCOMMITTED);
        }

        bool releases_gap_locks_at_prepare() const {
                return isolation_level <= READ_COMMITTED;
        }

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

        bool allow_semi_consistent() const {
                return (skip_gap_locks());
        }

        bool releases_non_matching_rows() const {
                return skip_gap_locks();
        }
};
```


## lock_rec_t

```viz-dot
digraph "lock_rec_t" {
        rankdir=LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "lock_rec_t" -> "n_bits";
        "lock_rec_t" -> "page_id" [label="page_id_t"];
        "page_id" -> "m_space" [label="space_id_t"];
        "page_id" -> "m_page_no" [label="page_no_t"];

}
```


```c++

typedef uint32_t page_no_t;

typedef uint32_t space_id_t;

class page_id_t {
public:
        page_id_t() = delete;
        page_id_t(space_id_t space, page_no_t page_no) : m_space(space), m_page_no(page_no) {
        }

        inline space_id_t space() const {
                return (m_space);
        }

        inline page_no_t page_no() const {
                return (m_page_no);
        }

        inline uint64_t hash() const {
                return ut::hash_uint64_pair(m_space, m_page_no);
        }


        inline void reset(space_id_t space, page_no_t page_no) {
                m_space   = space;
                m_page_no = page_no;
        }

        inline void set_page_no(page_no_t page_no) {
                m_page_no = page_no;
        }

        inline bool operator==(const page_id_t &a) const {
                return (a.space() == m_space && a.page_no() == m_page_no);
        }

        inline bool operator!=(const page_id_t &a) const {
                return !(*this == a);
        }

        inline bool operator<(const page_id_t &other) const {
                return m_space < other.space() || (m_space == other.space() && m_page_no < other.page_no());
        }

private:
        space_id_t m_space;
        page_no_t m_page_no;

        friend std::ostream &operator<<(std::ostream &out, const page_id_t &page_id);
};

struct lock_rec_t {
        page_id_t page_id;
        uint32_t n_bits;
        std::ostream &print(std::ostream &out) const;
};
```






## lock_table_t

```viz-dot
digraph "lock_table_t" {
        rankdir=LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "lock_table_t" -> "table"  [label="dict_table_t"];
        "lock_table_t" -> "locks" [label="ut_list_node<ib_lock_t>"];
        "table" -> "id" [label="table_id_t"];
        "table" -> "heap" [label="mem_heap_t*"];
        "table" -> "name" [label="table_name_t"];
        "table" -> "trunc_name" [label="table_name_t"];
        "table" -> "data_dir_path";
        "table" -> "tablespace" [label="id_name_t"];
        "table" -> "space" [label="space_id_t"];
        "table" -> "dd_space_id" [label="dd::Object_id"];
        "table" -> "DICT_TF_BITS" [label="mem_heap_t*"];

}
```



```c++
#define UT_LIST_NODE_T(t) ut_list_node<t>

struct lock_table_t {
        dict_table_t *table;
        // ut_list_node<ib_lock_t>
        UT_LIST_NODE_T(lock_t) locks;
        std::ostream &print(std::ostream &out) const;
};

struct dict_table_t {
        /** Check if the table is compressed.
        @return true if compressed, false otherwise. */
        bool is_compressed() const {
                return (DICT_TF_GET_ZIP_SSIZE(flags) != 0);
        }

        /** Get reference count.
        @return current value of n_ref_count */
        inline uint64_t get_ref_count() const;

        /** Acquire the table handle. */
        inline void acquire();

        inline void acquire_with_lock();

        /** Release the table handle. */
        inline void release();

        /** Lock the table handle. */
        inline void lock();

        /** Unlock the table handle. */
        inline void unlock();

#ifndef UNIV_HOTBACKUP
        /** Get schema and table name in system character set.
        @param[out]   schema  schema name
        @param[out]   table   table name */
        void get_table_name(std::string &schema, std::string &table) const;

        bool is_system_schema() const {
                std::string schema_name;
                std::string table_name;

                get_table_name(schema_name, table_name);

                if (0 == strcmp(schema_name.c_str(), MYSQL_SCHEMA_NAME.str) || 0 == strcmp(schema_name.c_str(), "sys") ||
                    0 == strcmp(schema_name.c_str(), PERFORMANCE_SCHEMA_DB_NAME.str) ||
                    0 == strcmp(schema_name.c_str(), INFORMATION_SCHEMA_NAME.str)) {
                        return (true);
                }
                return (false);
        }

        /** Mutex of the table for concurrency access. */
        ib_mutex_t *mutex;

        /** Creation state of mutex. */
        std::atomic<os_once::state_t> mutex_created;
#endif /* !UNIV_HOTBACKUP */

        /** Id of the table. */
        table_id_t id;

        mem_heap_t *heap;

        /** Table name. */
        table_name_t name;

        /** Truncate name. */
        table_name_t trunc_name;

        /** NULL or the directory path specified by DATA DIRECTORY. */
        char *data_dir_path;

        /** NULL or the tablespace name that this table is assigned to,
        specified by the TABLESPACE option.*/
        id_name_t tablespace;

        /** Space where the clustered index of the table is placed. */
        space_id_t space;

        /** dd::Tablespace::id of the table */
        dd::Object_id dd_space_id;


        unsigned flags : DICT_TF_BITS;


        unsigned flags2 : DICT_TF2_BITS;

        unsigned skip_alter_undo : 1;


        unsigned ibd_file_missing : 1;

        /** true if the table object has been added to the dictionary cache. */
        unsigned cached : 1;


        unsigned to_be_dropped : 1;

        /** Number of non-virtual columns defined so far. */
        unsigned n_def : 10;

        /** Number of non-virtual columns. */
        unsigned n_cols : 10;


        unsigned n_instant_cols : 10;

        /** Number of total columns (include virtual and non-virtual) */
        unsigned n_t_cols : 10;

        /** Number of total columns defined so far. */
        unsigned n_t_def : 10;

        /** Number of virtual columns defined so far. */
        unsigned n_v_def : 10;

        /** Number of virtual columns. */
        unsigned n_v_cols : 10;

        /** Number of multi-value virtual columns. */
        unsigned n_m_v_cols : 10;

        bool can_be_evicted : 1;

        unsigned ddl_not_evictable : 1;


        unsigned drop_aborted : 1;

        /** Array of column descriptions. */
        dict_col_t *cols;

        /** Array of virtual column descriptions. */
        dict_v_col_t *v_cols;


        dict_s_col_list *s_cols;


        const char *col_names;

        /** Virtual column names */
        const char *v_col_names;

        /** True if the table belongs to a system database (mysql, information_schema
        or performance_schema) */
        bool is_system_table;

        /** Hash chain node. */
        hash_node_t name_hash;

        /** Hash chain node. */
        hash_node_t id_hash;

        /** The FTS_DOC_ID_INDEX, or NULL if no fulltext indexes exist */
        dict_index_t *fts_doc_id_index;

        /** List of indexes of the table. */
        UT_LIST_BASE_NODE_T(dict_index_t, indexes) indexes;

        /** Node of the LRU list of tables. */
        UT_LIST_NODE_T(dict_table_t) table_LRU;

        /** metadata version number of dd::Table::se_private_data() */
        uint64_t version;

        /** Current row version in case columns are added/dropped INSTANTly */
        uint32_t current_row_version{0};

        /** Initial non-virtual column count */
        uint32_t initial_col_count{0};

        /** Current non-virtual column count */
        uint32_t current_col_count{0};

        /** Total non-virtual column count */
        uint32_t total_col_count{0};

        /** Set if table is upgraded instant table */
        unsigned m_upgraded_instant : 1;

        /** table dynamic metadata status, protected by dict_persist->mutex */
        std::atomic<table_dirty_status> dirty_status;

#ifndef UNIV_HOTBACKUP
        /** Node of the dirty table list of tables, which is protected
        by dict_persist->mutex */
        UT_LIST_NODE_T(dict_table_t) dirty_dict_tables;
#endif /* !UNIV_HOTBACKUP */

#ifdef UNIV_DEBUG

        bool in_dirty_dict_tables_list;
#endif /* UNIV_DEBUG */


        unsigned fk_max_recusive_level : 8;


        std::atomic<ulint> n_foreign_key_checks_running;

        trx_id_t def_trx_id;

        /*!< set of foreign key constraints in the table; these refer to
        columns in other tables */
        dict_foreign_set foreign_set;

        /*!< set of foreign key constraints which refer to this table */
        dict_foreign_set referenced_set;

#ifdef UNIV_DEBUG

        bool does_not_fit_in_memory;
#endif /* UNIV_DEBUG */

        unsigned big_rows : 1;

#ifndef UNIV_HOTBACKUP
        /** Statistics for query optimization. @{ */

        /** Creation state of 'stats_latch'. */
        std::atomic<os_once::state_t> stats_latch_created;


        rw_lock_t *stats_latch;


        unsigned stat_initialized : 1;

        /** Timestamp of last recalc of the stats. */
        std::chrono::steady_clock::time_point stats_last_recalc;


        #define DICT_STATS_PERSISTENT_ON (1 << 1)
        #define DICT_STATS_PERSISTENT_OFF (1 << 2)


        uint32_t stat_persistent;


        #define DICT_STATS_AUTO_RECALC_ON (1 << 1)
        #define DICT_STATS_AUTO_RECALC_OFF (1 << 2)


        uint32_t stats_auto_recalc;


        ulint stats_sample_pages;

        uint64_t stat_n_rows;

        /** Approximate clustered index size in database pages. */
        ulint stat_clustered_index_size;

        /** Approximate size of other indexes in database pages. */
        ulint stat_sum_of_other_index_sizes;

        /** If FTS AUX table, parent table id */
        table_id_t parent_id;


        uint64_t stat_modified_counter;

        /** Background stats thread is not working on this table. */
        #define BG_STAT_NONE 0

        #define BG_STAT_IN_PROGRESS (1 << 0)


        #define BG_STAT_SHOULD_QUIT (1 << 1)

        byte stats_bg_flag;

                /** @} */
#endif /* !UNIV_HOTBACKUP */

#ifndef UNIV_HOTBACKUP
        lock_t *autoinc_lock;

        /** Creation state of autoinc_mutex member */
        std::atomic<os_once::state_t> autoinc_mutex_created;
#endif /* !UNIV_HOTBACKUP */

        /** Mutex protecting the autoincrement counter. */
        ib_mutex_t *autoinc_mutex;

        /** Autoinc counter value to give to the next inserted row. */
        uint64_t autoinc;

        /** Mutex protecting the persisted autoincrement counter. */
        ib_mutex_t *autoinc_persisted_mutex;

        uint64_t autoinc_persisted;


        ulint autoinc_field_no;

        std::atomic<const trx_t *> autoinc_trx;

        /** @} */

#ifndef UNIV_HOTBACKUP
        /** FTS specific state variables. */
        fts_t *fts;
#endif /* !UNIV_HOTBACKUP */

        ib_quiesce_t quiesce;


        std::atomic<size_t> n_rec_locks;

#ifndef UNIV_DEBUG
private:
#endif

        std::atomic<uint64_t> n_ref_count;

public:
#ifndef UNIV_HOTBACKUP
        /** List of locks on the table. Protected by lock_sys shard latch. */
        table_lock_list_t locks;

        ulong count_by_mode[LOCK_NUM];
#endif /* !UNIV_HOTBACKUP */

        /** Timestamp of the last modification of this table. */
        std::atomic<std::chrono::system_clock::time_point> update_time;
        static_assert(decltype(update_time)::is_always_lock_free);

        uint64_t sess_row_id;


        uint64_t sess_trx_id;

#ifdef UNIV_DEBUG
        /** Magic number. */
        ulint magic_n;
#endif /* UNIV_DEBUG */
        /** mysql_row_templ_t for base columns used for compute the virtual
        columns */
        dict_vcol_templ_t *vc_templ;

        /** encryption key, it's only for export/import */
        byte *encryption_key;

        /** encryption iv, it's only for export/import */
        byte *encryption_iv;

        /** remove the dict_table_t from cache after DDL operation */
        bool discard_after_ddl;

        /** refresh/reload FK info */
        bool refresh_fk;

#ifndef UNIV_HOTBACKUP
        /** multiple cursors can be active on this temporary table */
        temp_prebuilt_vec *temp_prebuilt;
#endif /* !UNIV_HOTBACKUP */

        bool is_dd_table;

        bool explicitly_non_lru;

        /** @return the clustered index */
        const dict_index_t *first_index() const {
                ut_ad(magic_n == DICT_TABLE_MAGIC_N);
                const dict_index_t *first = UT_LIST_GET_FIRST(indexes);
                return (first);
        }
        /** @return the clustered index */
        dict_index_t *first_index() {
                return (const_cast<dict_index_t *>(const_cast<const dict_table_t *>(this)->first_index()));
        }

        /** @returns true if the table has row versions.. */
        bool has_row_versions() const {
                if (current_row_version > 0) {
                        ut_ad(has_instant_add_cols() || has_instant_drop_cols());
                        return (true);
                }

                return false;
        }

        bool has_instant_cols() const {
                if (is_upgraded_instant() || (n_instant_cols < n_cols)) {
                        /* Instant add col V1 */
                        return (true);
                }

                return false;
        }

        void set_instant_cols(uint16_t n_inst_cols) {
                n_instant_cols = static_cast<unsigned>(n_inst_cols) + get_n_sys_cols();
        }


        uint16_t get_instant_cols() const {
                return static_cast<uint16_t>(n_instant_cols - get_n_sys_cols());
        }

        size_t get_n_instant_added_col_v1() const {
                size_t n_cols_dropped       = get_n_instant_drop_cols();
                size_t n_cols_added         = get_n_instant_add_cols();
                size_t n_instant_added_cols = n_cols + n_cols_dropped - n_cols_added - n_instant_cols;

                return (n_instant_added_cols);
        }

        /** Get number of columns added instantly */
        uint32_t get_n_instant_add_cols() const {
                ut_ad(total_col_count >= initial_col_count);
                return total_col_count - initial_col_count;
        }

        /** Get number of columns dropped instantly */
        uint32_t get_n_instant_drop_cols() const {
                ut_ad(total_col_count >= current_col_count);
                return total_col_count - current_col_count;
        }

        bool has_instant_add_cols() const {
                return (get_n_instant_add_cols() > 0);
        }

        bool has_instant_drop_cols() const {
                return (get_n_instant_drop_cols() > 0);
        }

        void set_upgraded_instant() {
                m_upgraded_instant = 1;
        }


        bool is_upgraded_instant() const {
                return (m_upgraded_instant == 1) ? true : false;
        }

        bool is_corrupted() const {
                ut_ad(magic_n == DICT_TABLE_MAGIC_N);

                const dict_index_t *index = first_index();

                ut_ad(index == nullptr || index->is_clustered());

                return (index != nullptr && index->type & DICT_CORRUPT);
        }

        const char *get_col_name(ulint col_nr) const {
                ut_ad(col_nr < n_def);
                ut_ad(magic_n == DICT_TABLE_MAGIC_N);

                const char *s = col_names;
                if (s) {
                        for (ulint i = 0; i < col_nr; i++) {
                                s += strlen(s) + 1;
                        }
                }

                return (s);
        }

        dict_col_t *get_col(uint pos) const {
                ut_ad(pos < n_def);
                ut_ad(magic_n == DICT_TABLE_MAGIC_N);

                return (cols + pos);
        }

        dict_col_t *get_col_by_name(const char *name) const {
                ut_ad(name != nullptr);

                dict_col_t *ret = nullptr;

                const char *s = col_names;
                for (ulint i = 0; i < n_def; i++) {
                        if (strcmp(s, name) == 0) {
                                ret = get_col(i);
                        }
                        s += strlen(s) + 1;
                }

                return ret;
        }


        uint16_t get_n_user_cols() const {
                ut_ad(magic_n == DICT_TABLE_MAGIC_N);

                return (static_cast<uint16_t>(n_cols) - get_n_sys_cols());
        }

        uint16_t get_n_sys_cols() const {
                ut_ad(magic_n == DICT_TABLE_MAGIC_N);

                return (is_intrinsic() ? DATA_ITT_N_SYS_COLS : DATA_N_SYS_COLS);
        }

        ulint get_n_cols() const {
                ut_ad(magic_n == DICT_TABLE_MAGIC_N);

                return (n_cols);
        }

        ulint get_total_cols() const {
                if (!has_row_versions()) {
                        return n_cols;
                }

                ut_ad((total_col_count + get_n_sys_cols()) == (n_cols + get_n_instant_drop_cols()));
                return n_cols + get_n_instant_drop_cols();
        }

        dict_col_t *get_sys_col(ulint sys) const {
                dict_col_t *col;

                ut_ad(sys < get_n_sys_cols());
                ut_ad(magic_n == DICT_TABLE_MAGIC_N);

                col = get_col(n_cols - get_n_sys_cols() + sys);
                ut_ad(col->mtype == DATA_SYS);
                ut_ad(col->prtype == (sys | DATA_NOT_NULL));

                return (col);
        }

        /** Determine if this is a temporary table. */
        bool is_temporary() const {
                ut_ad(magic_n == DICT_TABLE_MAGIC_N);
                return (flags2 & DICT_TF2_TEMPORARY);
        }

        /** Determine if this is a FTS AUX table. */
        bool is_fts_aux() const {
                ut_ad(magic_n == DICT_TABLE_MAGIC_N);
                return (flags2 & DICT_TF2_AUX);
        }

        bool is_intrinsic() const {
                if (flags2 & DICT_TF2_INTRINSIC) {
                        ut_ad(is_temporary());
                        return (true);
                }

                return (false);
        }

        inline bool skip_gap_locks() const;

        inline bool support_instant_add_drop() const;
};
```





## 锁类型：

| 31-9 | 8 | 7-5 | 4 | 3-0 |
|:-----|:--|:----|:--|:----|
| 0    | 1 |     |   |     |
|      |   |     |   |     |

0 - 3 位表示锁模式，包括意向共享锁、意向排它锁、共享锁、排它锁还是自增锁

4 位表示锁是表类型，1 代表是表锁，0 代表不是

8 位表示是否锁等待

9 到 31 位表示是记录锁类型


```c++
/** Lock modes and types */
/** @{ */
/** mask used to extract mode from the  type_mode field in a lock */
constexpr uint32_t LOCK_MODE_MASK = 0xF;
/** Lock types */
/** table lock */
constexpr uint32_t LOCK_TABLE = 16;
/** record lock */
constexpr uint32_t LOCK_REC = 32;
/** mask used to extract lock type from the type_mode field in a lock */
constexpr uint32_t LOCK_TYPE_MASK = 0xF0UL;
static_assert((LOCK_MODE_MASK & LOCK_TYPE_MASK) == 0,
              "LOCK_MODE_MASK & LOCK_TYPE_MASK");

/** Waiting lock flag; when set, it  means that the lock has not yet been
 granted, it is just waiting for its  turn in the wait queue */
constexpr uint32_t LOCK_WAIT = 256;
/* Precise modes */
/** this flag denotes an ordinary next-key lock in contrast to LOCK_GAP or
 LOCK_REC_NOT_GAP */
constexpr uint32_t LOCK_ORDINARY = 0;
/** when this bit is set, it means that the lock holds only on the gap before
  the record; for instance, an x-lock on the gap does not give permission to
  modify the record on which the bit is set; locks of this type are created
  when records are removed from the index chain of records */
constexpr uint32_t LOCK_GAP = 512;
/** this bit means that the lock is only on the index record and does NOT
   block inserts to the gap before the index record; this is used in the case
   when we retrieve a record with a unique key, and is also used in locking
   plain SELECTs (not part of UPDATE or DELETE) when the user has set the READ
   COMMITTED isolation level */
constexpr uint32_t LOCK_REC_NOT_GAP = 1024;
/** this bit is set when we place a waiting gap type record lock request in
   order to let an insert of an index record to wait until there are no
   conflicting locks by other transactions on the gap; note that this flag
   remains set when the waiting lock is granted, or if the lock is inherited to
   a neighboring record */
constexpr uint32_t LOCK_INSERT_INTENTION = 2048;
/** Predicate lock */
constexpr uint32_t LOCK_PREDICATE = 8192;
/** Page lock */
constexpr uint32_t LOCK_PRDT_PAGE = 16384;
```



## 锁模式：lock_mode


| 锁模式(type_mode) | 锁表示法                              |
|:---------------|:--------------------------------------|
| 记录锁            | LOCK_X LOCK_REC_NO_GAP                |
| 间隙锁            | LOCK_X LOCK_GAP                       |
| 临键锁            | LOCK_X LOCK_ORDINARY                  |
| 插入意向锁        | LOCK_X LOCK_GAP LOCK_INSERT_INTENTION |



```c++
/* Basic lock modes */
enum lock_mode {
  LOCK_IS = 0,          /* intention shared */
  LOCK_IX,              /* intention exclusive */
  LOCK_S,               /* shared */
  LOCK_X,               /* exclusive */
  LOCK_AUTO_INC,        /* locks the auto-inc counter of a table
                        in an exclusive mode */
  LOCK_NONE,            /* this is used elsewhere to note consistent read */
  LOCK_NUM = LOCK_NONE, /* number of lock modes */
  LOCK_NONE_UNSET = 255
};
```





## 锁互斥矩阵和加锁强度矩阵

```c++
/* LOCK COMPATIBILITY MATRIX
    IS IX S  X  AI
 IS +    +  +  -  +
 IX +    +  -  -  +
 S  +    -  +  -  -
 X  -    -  -  -  -
 AI +    +  -  -  -
 *
 Note that for rows, InnoDB only acquires S or X locks.
 For tables, InnoDB normally acquires IS or IX locks.
 S or X table locks are only acquired for LOCK TABLES.
 Auto-increment (AI) locks are needed because of
 statement-level MySQL binlog.
 See also lock_mode_compatible().
 */
static const byte lock_compatibility_matrix[5][5] = {
    /**         IS     IX       S     X       AI */
    /* IS */ {true, true, true, false, true},
    /* IX */ {true, true, false, false, true},
    /* S  */ {true, false, true, false, false},
    /* X  */ {false, false, false, false, false},
    /* AI */ {true, true, false, false, false}};

/* STRONGER-OR-EQUAL RELATION (mode1=row, mode2=column)
    IS IX S  X  AI
 IS +  -  -  -  -
 IX +  +  -  -  -
 S  +  -  +  -  -
 X  +  +  +  +  +
 AI -  -  -  -  +
 See lock_mode_stronger_or_eq().
 */
static const byte lock_strength_matrix[5][5] = {
    /**         IS     IX       S     X       AI */
    /* IS */ {true, false, false, false, false},
    /* IX */ {true, true, false, false, false},
    /* S  */ {true, false, true, false, false},
    /* X  */ {true, true, true, true, true},
    /* AI */ {false, false, false, false, true}};

/** Maximum depth of the DFS stack. */
```










