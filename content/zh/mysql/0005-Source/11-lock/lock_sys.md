---
title: lock_sys
description: lock_sys
date: 2023-10-30
weight: 10000


---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


{{<alert color="secondary">}}

本文基于 MySQL 8.0.32 源码，存储引擎为 InnoDB。

格式化：Markdown Table Prettifier

{{</alert>}}





## 全局变量：lock_sys
```c++
/* The lock system */
lock_sys_t *lock_sys = nullptr;

```
- 锁模块结构只有一个对象（lock_sys），在 InnoDB 中是全局唯一的。



## lock_sys_t


| lock_sys_t           | 数据类型                 | 功能                                                                                                                                |
| :------------------- | :----------------------- | :---------------------------------------------------------------------------------------------------------------------------------- |
| latches              | &#9989;                  | 用于保证同一时刻只有一个线程读写 rec_hash 属性的同一个格子对应的行锁结构链表，以及同一时刻只有一个线程读写同一个表对象的 locks 链表 |
| rec_hash             | hash_table_t             | 哈希表，分为很多小格子，每个格子管理一个行锁结构链表                                                                                |
| prdt_hash            | hash_table_t             | 谓词锁使用                                                                                                                          |
| prdt_page_hash       | hash_table_t             | 谓词锁使用                                                                                                                          |
| pad2                 | &#9989;                  | &#9989;                                                                                                                             |
| wait_mutex           |                          | 用于保证同一时刻只有一个线程读写 last_sot 属性                                                                                      |
| waiting_threads      |                          | 指向一片分为 102400 个 slot 的内存区域，每个等待获得锁的事务会占用其中一个 slot                                                     |
| last_slot            |                          | 用于减少检查锁等待超时需要遍历的 slot 数量，提升效率                                                                                |
| rollback_complete    |                          | 用于 MySQL 启动过程中，标识从 undo 日志中恢复出来的、需要回滚的事务是否已全部回滚完成                                               |
| n_lock_max_wait_time | Innodb_row_lock_time_max | MySQL本次启动以来，行锁的最长等待时间                                                                                               |
| timeout_event        |                          | 用于发生锁等待时，通知后台线程及时检查是否出现了死锁                                                                                |
| m_seq                |                          |                                                                                                                                     |

- rollback_complete 属性，用于 MySQL 启动过程中，标识从 undo 日志中恢复出来的、需要回滚的事务是否已全部回滚完成。
  - 如果 rollback_complete = false，说明从 undo 日志中恢复出来的、需要回滚的事务还没有全部回滚完成，InnoDB 会遍历读写事务链表（trx_sys->rw_trx_list），释放这些事务加的表锁和行锁。这些事务全部回滚完成之后，rollback_complete 会被修改为 true
- latches对象：有三个属性，分别为 global_latch、page_shards、table_shards
  - 事务提交或回滚时，释放所有行锁和表锁会用到 global_latch。
  - 事务加行锁时，会用到 page_shards。
  - 事务加表锁时，会用到 table_shards。



```c++
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


## lock_sys_create


```c++
/** Creates the lock system at database start. */
void lock_sys_create(ulint n_cells) /*!< in: number of slots in lock hash table */
{
        ulint lock_sys_sz;

        lock_sys_sz = sizeof(*lock_sys) + srv_max_n_threads * sizeof(srv_slot_t);

        lock_sys = static_cast<lock_sys_t *>(ut::zalloc_withkey(UT_NEW_THIS_FILE_PSI_KEY, lock_sys_sz));

        new (lock_sys) lock_sys_t{};

        void *ptr = &lock_sys[1];

        lock_sys->waiting_threads = static_cast<srv_slot_t *>(ptr);

        lock_sys->last_slot = lock_sys->waiting_threads;

        mutex_create(LATCH_ID_LOCK_SYS_WAIT, &lock_sys->wait_mutex);

        lock_sys->timeout_event = os_event_create();

        lock_sys->rec_hash       = ut::new_<hash_table_t>(n_cells);
        lock_sys->prdt_hash      = ut::new_<hash_table_t>(n_cells);
        lock_sys->prdt_page_hash = ut::new_<hash_table_t>(n_cells);

        if (!srv_read_only_mode) {
                lock_latest_err_file = os_file_create_tmpfile();
                ut_a(lock_latest_err_file);
        }
}

```

- 创建锁模块对象lock_sys，分配内存： `lock_sys = static_cast<lock_sys_t *>(ut::zalloc_withkey(UT_NEW_THIS_FILE_PSI_KEY, lock_sys_sz));`
- 创建哈希表对象rec_hash，分配内存：`lock_sys->rec_hash       = ut::new_<hash_table_t>(n_cells);`
- srv_start() 调用：lock_sys_create(srv_lock_table_size);
- `srv_lock_table_size = 5 * (srv_buf_pool_size / UNIV_PAGE_SIZE);`
- srv_buf_pool_size 是 buffer pool 的大小，UNIV_PAGE_SIZE 是一个数据页的大小，它们的单位都是字节。


