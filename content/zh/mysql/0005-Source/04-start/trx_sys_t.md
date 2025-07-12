---
title: trx_sys_t
description: trx_sys_t
date: 2023-10-30
weight: 10000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
这个结构体用来维护系统的事务信息，全局只有一个，在数据库启动的时候初始化。
{{< /alert >}}


### trx_sys_t





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
  auto latch_and_execute_with_active_trx(trx_id_t trx_id, F &&f,
                                         const ut::Location &loc) {
    return get_shard_by_trx_id(trx_id).active_rw_trxs.latch_and_execute(
        [&](Trx_by_id_with_min &trx_by_id_with_min) {
          return std::forward<F>(f)(trx_by_id_with_min.get(trx_id));
        },
        loc);
  }
};
```


