---
title: 事务系统对象
description: trx_sys_t
date: 2023-10-30
weight: 110000


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





## 全局变量：trx_sys
```c++
/** The transaction system */
trx_sys_t *trx_sys = nullptr;

```
- trx_sys



## trx_sys_t


| 成员                       | 数据类型                                     | 功能                                            |
| :------------------------- | :------------------------------------------- | :---------------------------------------------- |
| `pad0`                     | `char [ut::INNODB_CACHE_LINE_SIZE]`          |                                                 |
| `mvcc`                     | `MVCC *`                                     | 存储所有的 readview，包括 active 和 free 的对象 |
| `rsegs`                    | `Rsegs`                                      | 所有的 undo 回滚段的内存对象                    |
| `tmp_rsegs`                | `Rsegs`                                      |                                                 |
| `rseg_history_len`         | `std::atomic<uint64_t>`                      | history list 的长度                             |
| `pad1`                     | `char [ut::INNODB_CACHE_LINE_SIZE]`          |                                                 |
| `next_trx_id_or_no`        | `std::atomic<trx_id_t>`                      | 最小的未分配 trx id                             |
| `pad2`                     | `char [ut::INNODB_CACHE_LINE_SIZE]`          |                                                 |
| `serialisation_mutex`      | `TrxSysMutex`                                |                                                 |
| `serialisation_list`       | `UT_LIST_BASE_NODE_T(trx_t, no_list)`        |                                                 |
| `rw_max_trx_no`            | `trx_id_t`                                   | 调试开启才有                                    |
| `pad3`                     | `char [ut::INNODB_CACHE_LINE_SIZE]`          |                                                 |
| `serialisation_min_trx_no` | `std::atomic<trx_id_t>`                      |                                                 |
| `pad4`                     | `char [ut::INNODB_CACHE_LINE_SIZE]`          |                                                 |
| `mutex`                    | `TrxSysMutex`                                |                                                 |
| `pad5`                     | `char [ut::INNODB_CACHE_LINE_SIZE]`          |                                                 |
| `rw_trx_list`              | `UT_LIST_BASE_NODE_T(trx_t, trx_list)`       | 读写事务队列                                    |
| `pad6`                     | `char [ut::INNODB_CACHE_LINE_SIZE]`          |                                                 |
| `mysql_trx_list`           | `UT_LIST_BASE_NODE_T(trx_t, mysql_trx_list)` | 所有开启事务队列                                |
| `rw_trx_ids`               | `trx_ids_t`                                  | 全局的活跃事务id数组                            |
| `pad7`                     | `char pad7[ut::INNODB_CACHE_LINE_SIZE]`      |                                                 |
| `shards`                   | `Trx_shard [TRX_SHARDS_N]`                   | 活跃事务对象哈希表                              |
| `n_prepared_trx`           | `ulint`                                      |                                                 |
| `found_prepared_trx`       | `bool`                                       |                                                 |
| `pad_after`                | `char [ut::INNODB_CACHE_LINE_SIZE]`          |                                                 |


- 全局的活跃事务id数组：trx_ids_t

```c++
typedef std::vector<trx_id_t, ut::allocator<trx_id_t>> trx_ids_t
```







```c++
/** The transaction system central memory data structure. */
struct trx_sys_t {
        /* Members protected by neither trx_sys_t::mutex nor serialisation_mutex. */
        char pad0[ut::INNODB_CACHE_LINE_SIZE];

        /** @{ */

        /** Multi version concurrency control manager */

        MVCC *mvcc;

        /** Vector of pointers to rollback segments. These rsegs are iterated
        and added to the end under a read lock. They are deleted under a write
        lock while the vector is adjusted. They are created and destroyed in
        single-threaded mode. */
        Rsegs rsegs;

        /** Vector of pointers to rollback segments within the temp tablespace;
        This vector is created and destroyed in single-threaded mode so it is not
        protected by any mutex because it is read-only during multi-threaded
        operation. */
        Rsegs tmp_rsegs;

        /** Length of the TRX_RSEG_HISTORY list (update undo logs for committed
        transactions). */
        std::atomic<uint64_t> rseg_history_len;

        /** @} */

        /* Members protected by either trx_sys_t::mutex or serialisation_mutex. */
        char pad1[ut::INNODB_CACHE_LINE_SIZE];

        /** @{ */

        /** The smallest number not yet assigned as a transaction id
        or transaction number. This is declared as atomic because it
        can be accessed without holding any mutex during AC-NL-RO
        view creation. When it is used for assignment of the trx->id,
        it is synchronized by the trx_sys_t::mutex. When it is used
        for assignment of the trx->no, it is synchronized by the
        trx_sys_t::serialisation_mutex. Note: it might be in parallel
        used for both trx->id and trx->no assignments (for different
        trx_t objects). */
        std::atomic<trx_id_t> next_trx_id_or_no;

        /** @} */

        /* Members protected by serialisation_mutex. */
        char pad2[ut::INNODB_CACHE_LINE_SIZE];

        /** @{ */

        /** Mutex to protect serialisation_list. */
        TrxSysMutex serialisation_mutex;

        /** Tracks minimal transaction id which has received trx->no, but has
        not yet finished commit for the mtr writing the trx commit. Protected
        by the serialisation_mutex. Ordered on the trx->no field. */
        UT_LIST_BASE_NODE_T(trx_t, no_list) serialisation_list;

        #ifdef UNIV_DEBUG
        /** Max trx number of read-write transactions added for purge. */
        trx_id_t rw_max_trx_no;
        #endif /* UNIV_DEBUG */

        char pad3[ut::INNODB_CACHE_LINE_SIZE];

        /* The minimum trx->no inside the serialisation_list. Protected by
        the serialisation_mutex. Might be read without the mutex. */
        std::atomic<trx_id_t> serialisation_min_trx_no;

        /** @} */

        /* Members protected by the trx_sys_t::mutex. */
        char pad4[ut::INNODB_CACHE_LINE_SIZE];

        /** @{ */

        /** Mutex protecting most fields in this structure (the default one). */
        TrxSysMutex mutex;

        char pad5[ut::INNODB_CACHE_LINE_SIZE];

        /** List of active and committed in memory read-write transactions, sorted
        on trx id, biggest first. Recovered transactions are always on this list. */
        UT_LIST_BASE_NODE_T(trx_t, trx_list) rw_trx_list;

        char pad6[ut::INNODB_CACHE_LINE_SIZE];

        /** List of transactions created for MySQL. All user transactions are
        on mysql_trx_list. The rw_trx_list can contain system transactions and
        recovered transactions that will not be in the mysql_trx_list.
        Additionally, mysql_trx_list may contain transactions that have not yet
        been started in InnoDB. */
        UT_LIST_BASE_NODE_T(trx_t, mysql_trx_list) mysql_trx_list;

        /** Array of Read write transaction IDs for MVCC snapshot. A ReadView would
        take a snapshot of these transactions whose changes are not visible to it.
        We should remove transactions from the list before committing in memory and
        releasing locks to ensure right order of removal and consistent snapshot. */
        trx_ids_t rw_trx_ids;

        char pad7[ut::INNODB_CACHE_LINE_SIZE];

        /** Mapping from transaction id to transaction instance. */
        Trx_shard shards[TRX_SHARDS_N];

        /** Number of transactions currently in the XA PREPARED state. */
        ulint n_prepared_trx;

        /** True if XA PREPARED trxs are found. */
        bool found_prepared_trx;

        /** @} */

        char pad_after[ut::INNODB_CACHE_LINE_SIZE];

        Trx_shard &get_shard_by_trx_id(trx_id_t trx_id) {
                return trx_sys->shards[trx_get_shard_no(trx_id)];
        }
        template <typename F>
        auto latch_and_execute_with_active_trx(trx_id_t trx_id, F &&f, const ut::Location &loc) {
                return get_shard_by_trx_id(trx_id).active_rw_trxs.latch_and_execute(
                    [&](Trx_by_id_with_min &trx_by_id_with_min) {
                            return std::forward<F>(f)(trx_by_id_with_min.get(trx_id));
                    },
                    loc);
        }
};

```




