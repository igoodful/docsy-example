---
title: max_binlog_cache_size
description: max_binlog_cache_size
date: 2023-10-25
weight: 20000


---

{{% caution %}}
<!---->
在日常的运维工作中，偶尔会遇到一些意想不到的线上问题，多数因为设置不当导致故障。前段时间，报错主从复制 SQL 线程断开，就是因为一个参数设置异常导致的。




{{% /caution %}}


## 一、故障描述

DMP 收到告警 MySQL 从库的 SQL 线程停止了工作，去从库后台执行
```sql
show slave status\G：
```


可以看到 SQL 线程确实停止工作了，根据提示查看 ：
```sql
select * from performance_schema.replication_applier_status_by_worker;
```

报错为：

```none
Worker 1 failed executing transaction ’44bbb836-19b4-11eb-aae3-98f2b315b1a5:216718523′ at master log mysqlbin.000492, end_log_pos 533198991;
Could not execute Delete_rows event on table cmbc_msearch.search_hotword_item; Multi-statement transaction required
more than ‘max_binlog_cache_size’ bytes of storage; increase this mysqld variable and try again, Error_code: 1197;
handler error HA_ERR_RBR_LOGGING_FAILED; the event’s master log mysqlbin.000492, end_log_pos 533198991
```


我们可以看到报错信息比较明显，是 max_binlog_cache_size 参数设置出现了问题。

查看主从的 max_binlog_cache_size 的大小，主库为 10G，从库为 10M。

binlog 是 MySQL 用来记录所有修改数据库数据的操作的二进制日志。在主从复制中，主库会把自己的 binlog 传输给从库，供从库执行相同的操作，以保证数据的一致性。而 max_binlog_cache_size 则是 MySQL 设置的最大 binlog 缓存大小。当事务过于复杂，多语句事务执行，需要写入 binlog 的数据量超过了这个值时，就会出现上述错误。

此时还要注意另一个参数 binlog_cache_size，这个参数给每个客户端分配用来存储二进制日志的缓存，而 max_binlog_cache_size 则表示所有客户端缓存使用的最大值。


## 二、问题解决
{{<alert>}}
<!---->
**注意：**

该参数修改后，并不会立即生效，需要重启复制线程，即执行：`set global max_binlog_cache_size=1073741824;stop slave;start slave;`


{{</alert>}}
max_binlog_cache_size 参数是可以通过在线动态修改的，现场解决方案，将从库的该值调大：
```sql
mysql> show slave status\G
*************************** 1. row ***************************
               Slave_IO_State: Waiting for source to send event
                  Master_Host: 172.17.135.84
                  Master_User: dbscale_internal
                  Master_Port: 16315
                Connect_Retry: 60
              Master_Log_File: greatdb-bin.000002
          Read_Master_Log_Pos: 716617980
               Relay_Log_File: mysql-relay.000009
                Relay_Log_Pos: 865
        Relay_Master_Log_File: greatdb-bin.000002
             Slave_IO_Running: Yes
            Slave_SQL_Running: No
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 1197
                   Last_Error: Coordinator stopped because there were error(s) in the worker(s). The most recent failure being: Worker 1 failed executing transaction '82ddfeaa-ea51-11ee-88eb-00163e311b0d:618216' at master log greatdb-bin.000002, end_log_pos 703852416. See error log and/or performance_schema.replication_applier_status_by_worker table for more details about this failure or others, if any.
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 697545568
              Relay_Log_Space: 19073855
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: NULL
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 1197
               Last_SQL_Error: Coordinator stopped because there were error(s) in the worker(s). The most recent failure being: Worker 1 failed executing transaction '82ddfeaa-ea51-11ee-88eb-00163e311b0d:618216' at master log greatdb-bin.000002, end_log_pos 703852416. See error log and/or performance_schema.replication_applier_status_by_worker table for more details about this failure or others, if any.
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 794916315
                  Master_UUID: 82ddfeaa-ea51-11ee-88eb-00163e311b0d
             Master_Info_File: mysql.slave_master_info
                    SQL_Delay: 0
          SQL_Remaining_Delay: NULL
      Slave_SQL_Running_State:
           Master_Retry_Count: 86400
                  Master_Bind:
      Last_IO_Error_Timestamp:
     Last_SQL_Error_Timestamp: 240411 16:32:20
               Master_SSL_Crl:
           Master_SSL_Crlpath:
           Retrieved_Gtid_Set: 82ddfeaa-ea51-11ee-88eb-00163e311b0d:1-618216,
c78a42f5-ea64-11ee-86be-00163e4e3cd6:1-14826
            Executed_Gtid_Set: 82c404e1-ea51-11ee-a1b2-00163e0bbef4:1-27,
82ddfeaa-ea51-11ee-88eb-00163e311b0d:1-618215,
c78a42f5-ea64-11ee-86be-00163e4e3cd6:1-14826
                Auto_Position: 1
         Replicate_Rewrite_DB:
                 Channel_Name:
           Master_TLS_Version:
       Master_public_key_path:
        Get_master_public_key: 0
            Network_Namespace:
1 row in set, 1 warning (0.02 sec)

mysql> select * from performance_schema.replication_applier_status_by_worker where LAST_ERROR_NUMBER>0\G
*************************** 1. row ***************************
                                           CHANNEL_NAME:
                                              WORKER_ID: 1
                                              THREAD_ID: NULL
                                          SERVICE_STATE: OFF
                                      LAST_ERROR_NUMBER: 1197
                                     LAST_ERROR_MESSAGE: Worker 1 failed executing transaction '82ddfeaa-ea51-11ee-88eb-00163e311b0d:618216' at master log greatdb-bin.000002, end_log_pos 703852416; Could not execute Write_rows event on table apple.t2; Multi-statement transaction required more than 'max_binlog_cache_size' bytes of storage; increase this mysqld variable and try again, Error_code: 1197; Writing one row to the row-based binary log failed, Error_code: 1534; handler error HA_ERR_RBR_LOGGING_FAILED; the event''s master log FIRST, end_log_pos 703852416
                                   LAST_ERROR_TIMESTAMP: 2024-04-11 16:32:20.126627
                               LAST_APPLIED_TRANSACTION:
     LAST_APPLIED_TRANSACTION_ORIGINAL_COMMIT_TIMESTAMP: 0000-00-00 00:00:00.000000
    LAST_APPLIED_TRANSACTION_IMMEDIATE_COMMIT_TIMESTAMP: 0000-00-00 00:00:00.000000
         LAST_APPLIED_TRANSACTION_START_APPLY_TIMESTAMP: 0000-00-00 00:00:00.000000
           LAST_APPLIED_TRANSACTION_END_APPLY_TIMESTAMP: 0000-00-00 00:00:00.000000
                                   APPLYING_TRANSACTION: 82ddfeaa-ea51-11ee-88eb-00163e311b0d:618216
         APPLYING_TRANSACTION_ORIGINAL_COMMIT_TIMESTAMP: 2024-04-11 16:31:47.212595
        APPLYING_TRANSACTION_IMMEDIATE_COMMIT_TIMESTAMP: 2024-04-11 16:31:47.212595
             APPLYING_TRANSACTION_START_APPLY_TIMESTAMP: 2024-04-11 16:32:19.104127
                 LAST_APPLIED_TRANSACTION_RETRIES_COUNT: 0
   LAST_APPLIED_TRANSACTION_LAST_TRANSIENT_ERROR_NUMBER: 0
  LAST_APPLIED_TRANSACTION_LAST_TRANSIENT_ERROR_MESSAGE:
LAST_APPLIED_TRANSACTION_LAST_TRANSIENT_ERROR_TIMESTAMP: 0000-00-00 00:00:00.000000
                     APPLYING_TRANSACTION_RETRIES_COUNT: 0
       APPLYING_TRANSACTION_LAST_TRANSIENT_ERROR_NUMBER: 0
      APPLYING_TRANSACTION_LAST_TRANSIENT_ERROR_MESSAGE:
    APPLYING_TRANSACTION_LAST_TRANSIENT_ERROR_TIMESTAMP: 0000-00-00 00:00:00.000000
1 row in set (0.01 sec)

mysql> set global max_binlog_cache_size=1073741824;
Query OK, 0 rows affected (0.00 sec)

mysql> start slave;
Query OK, 0 rows affected, 1 warning (0.17 sec)

mysql> show slave status\G
*************************** 1. row ***************************
               Slave_IO_State: Waiting for source to send event
                  Master_Host: 172.17.135.84
                  Master_User: dbscale_internal
                  Master_Port: 16315
                Connect_Retry: 60
              Master_Log_File: greatdb-bin.000002
          Read_Master_Log_Pos: 716617980
               Relay_Log_File: mysql-relay.000010
                Relay_Log_Pos: 488
        Relay_Master_Log_File: greatdb-bin.000002
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 716617980
              Relay_Log_Space: 19073810
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: 0
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 794916315
                  Master_UUID: 82ddfeaa-ea51-11ee-88eb-00163e311b0d
             Master_Info_File: mysql.slave_master_info
                    SQL_Delay: 0
          SQL_Remaining_Delay: NULL
      Slave_SQL_Running_State: Replica has read all relay log; waiting for more updates
           Master_Retry_Count: 86400
                  Master_Bind:
      Last_IO_Error_Timestamp:
     Last_SQL_Error_Timestamp:
               Master_SSL_Crl:
           Master_SSL_Crlpath:
           Retrieved_Gtid_Set: 82ddfeaa-ea51-11ee-88eb-00163e311b0d:1-618216,
c78a42f5-ea64-11ee-86be-00163e4e3cd6:1-14826
            Executed_Gtid_Set: 82c404e1-ea51-11ee-a1b2-00163e0bbef4:1-27,
82ddfeaa-ea51-11ee-88eb-00163e311b0d:1-618216,
c78a42f5-ea64-11ee-86be-00163e4e3cd6:1-14826
                Auto_Position: 1
         Replicate_Rewrite_DB:
                 Channel_Name:
           Master_TLS_Version:
       Master_public_key_path:
        Get_master_public_key: 0
            Network_Namespace:
1 row in set, 1 warning (0.00 sec)

```
然后再开启主从复制，就正常了。




## 源码跟着调试

#### handle_slave_worker
> `error = slave_worker_exec_job_group(w, rli);`
```c++
extern "C" {
static void *handle_slave_worker(void *arg) {
        THD *thd; /* needs to be first for thread_stack */
        bool thd_added       = false;
        int error            = 0;
        Slave_worker *w      = (Slave_worker *)arg;
        Relay_log_info *rli  = w->c_rli;
        ulong purge_cnt      = 0;
        ulonglong purge_size = 0;
        struct slave_job_item _item, *job_item = &_item;
        Global_THD_manager *thd_manager = Global_THD_manager::get_instance();
#ifdef HAVE_PSI_THREAD_INTERFACE
        struct PSI_thread *psi;
#endif

        my_thread_init();
        DBUG_TRACE;

        thd = new THD;
        if (!thd) {
                LogErr(ERROR_LEVEL, ER_RPL_SLAVE_CANT_INITIALIZE_SLAVE_WORKER, rli->get_for_channel_str());
                goto err;
        }
        mysql_mutex_lock(&w->info_thd_lock);
        w->info_thd = thd;
        mysql_mutex_unlock(&w->info_thd_lock);
        thd->thread_stack = (char *)&thd;

#ifdef HAVE_PSI_THREAD_INTERFACE
        // save the instrumentation for worker thread in w->info_thd
        psi = PSI_THREAD_CALL(get_thread)();
        thd_set_psi(w->info_thd, psi);
#endif
        mysql_thread_set_psi_THD(thd);

        if (init_replica_thread(thd, SLAVE_THD_WORKER)) {
                // todo make SQL thread killed
                LogErr(ERROR_LEVEL, ER_RPL_SLAVE_CANT_INITIALIZE_SLAVE_WORKER, rli->get_for_channel_str());
                goto err;
        }
        thd->rli_slave = w;
        thd->init_query_mem_roots();

        if (channel_map.is_group_replication_channel_name(rli->get_channel())) {
                if (channel_map.is_group_replication_channel_name(rli->get_channel(), true)) {
                        thd->rpl_thd_ctx.set_rpl_channel_type(GR_APPLIER_CHANNEL);
                } else {
                        thd->rpl_thd_ctx.set_rpl_channel_type(GR_RECOVERY_CHANNEL);
                }
        } else {
                thd->rpl_thd_ctx.set_rpl_channel_type(RPL_STANDARD_CHANNEL);
        }

        w->set_filter(rli->rpl_filter);

        if ((w->deferred_events_collecting = w->rpl_filter->is_on()))
                w->deferred_events = new Deferred_log_events();
        assert(thd->rli_slave->info_thd == thd);

        /* Set applier thread InnoDB priority */
        set_thd_tx_priority(thd, rli->get_thd_tx_priority());
        /* Set write set related options */
        set_thd_write_set_options(thd, rli->get_ignore_write_set_memory_limit(), rli->get_allow_drop_write_set());

        thd->variables.require_row_format = rli->is_row_format_required();

        if (Relay_log_info::PK_CHECK_STREAM != rli->get_require_table_primary_key_check())
                thd->variables.sql_require_primary_key =
                    (rli->get_require_table_primary_key_check() == Relay_log_info::PK_CHECK_ON);
        w->set_require_table_primary_key_check(rli->get_require_table_primary_key_check());

        thd_manager->add_thd(thd);
        thd_added = true;

        if (w->update_is_transactional()) {
                rli->report(ERROR_LEVEL,
                            ER_SLAVE_FATAL_ERROR,
                            ER_THD(thd, ER_SLAVE_FATAL_ERROR),
                            "Error checking if the worker repository is transactional.");
                goto err;
        }

        if (rli->get_commit_order_manager() != nullptr)
                rli->get_commit_order_manager()->init_worker_context(
                    *w);  // Initialize worker context within Commit_order_manager

        mysql_mutex_lock(&w->jobs_lock);
        w->running_status = Slave_worker::RUNNING;
        mysql_cond_signal(&w->jobs_cond);

        mysql_mutex_unlock(&w->jobs_lock);

        assert(thd->is_slave_error == 0);

        w->stats_exec_time = w->stats_read_time = 0;
        set_timespec_nsec(&w->ts_exec[0], 0);
        set_timespec_nsec(&w->ts_exec[1], 0);
        set_timespec_nsec(&w->stats_begin, 0);

        // No need to report anything, all error handling will be performed in the
        // slave SQL thread.
        if (!rli->check_privilege_checks_user())
                rli->initialize_security_context(w->info_thd);  // Worker security context
                                                                // initialization with
                                                                // `PRIVILEGE_CHECKS_USER`

        while (!error) {
                error = slave_worker_exec_job_group(w, rli);
        }

        /*
           Cleanup after an error requires clear_error() go first.
           Otherwise assert(!all) in binlog_rollback()
        */
        thd->clear_error();
        w->cleanup_context(thd, error);

        mysql_mutex_lock(&w->jobs_lock);

        while (de_queue(&w->jobs, job_item)) {
                purge_cnt++;
                purge_size += job_item->data->common_header->data_written;
                assert(job_item->data);
                delete job_item->data;
        }

        assert(w->jobs.len == 0);

        mysql_mutex_unlock(&w->jobs_lock);

        mysql_mutex_lock(&rli->pending_jobs_lock);
        rli->pending_jobs -= purge_cnt;
        rli->mts_pending_jobs_size -= purge_size;
        assert(rli->mts_pending_jobs_size < rli->mts_pending_jobs_size_max);

        mysql_mutex_unlock(&rli->pending_jobs_lock);

        /*
           In MTS case cleanup_after_session() has be called explicitly.
           TODO: to make worker thd be deleted before Slave_worker instance.
        */
        if (thd->rli_slave) {
                w->cleanup_after_session();
                thd->rli_slave = nullptr;
        }
        mysql_mutex_lock(&w->jobs_lock);

        struct timespec stats_end;
        set_timespec_nsec(&stats_end, 0);
        DBUG_PRINT("info",
                   ("Worker %lu statistics: "
                    "events processed = %lu "
                    "online time = %llu "
                    "events exec time = %llu "
                    "events read time = %llu "
                    "hungry waits = %lu "
                    "priv queue overfills = %llu ",
                    w->id,
                    w->events_done,
                    diff_timespec(&stats_end, &w->stats_begin),
                    w->stats_exec_time,
                    w->stats_read_time,
                    w->wq_empty_waits,
                    w->jobs.waited_overfill));

        w->running_status = Slave_worker::NOT_RUNNING;
        mysql_cond_signal(&w->jobs_cond);  // famous last goodbye

        mysql_mutex_unlock(&w->jobs_lock);

err:

        if (thd) {
                /*
                   The slave code is very bad. Notice that it is missing
                   several clean up calls here. I've just added what was
                   necessary to avoid valgrind errors.

                   /Alfranio
                */
                thd->get_protocol_classic()->end_net();

                /*
                  to avoid close_temporary_tables() closing temp tables as those
                  are Coordinator's burden.
                */
                thd->system_thread = NON_SYSTEM_THREAD;
                thd->release_resources();

                THD_CHECK_SENTRY(thd);
                if (thd_added)
                        thd_manager->remove_thd(thd);
                mysql_thread_set_psi_THD(nullptr);
                delete thd;
        }

        my_thread_end();
#if OPENSSL_VERSION_NUMBER < 0x10100000L
        ERR_remove_thread_state(0);
#endif /* OPENSSL_VERSION_NUMBER < 0x10100000L */
        my_thread_exit(nullptr);
        return nullptr;
}
}  // extern "C"
```



#### slave_worker_exec_event
> `error = worker->slave_worker_exec_event(ev);`
```c++
int slave_worker_exec_job_group(Slave_worker *worker, Relay_log_info *rli) {
        struct slave_job_item item      = {nullptr, 0, 0};
        struct slave_job_item *job_item = &item;
        THD *thd                        = worker->info_thd;
        bool seen_gtid                  = false;
        bool seen_begin                 = false;
        int error                       = 0;
        Log_event *ev                   = nullptr;
        uint start_relay_number;
        my_off_t start_relay_pos;

        DBUG_TRACE;

        if (unlikely(worker->trans_retries > 0))
                worker->trans_retries = 0;

        job_item           = pop_jobs_item(worker, job_item);
        start_relay_number = job_item->relay_number;
        start_relay_pos    = job_item->relay_pos;

        /* Current event with Worker associator. */
        RLI_current_event_raii worker_curr_ev(worker, ev);

        while (true) {
                Slave_job_group *ptr_g;

                if (unlikely(thd->killed || worker->running_status == Slave_worker::STOP_ACCEPTED)) {
                        assert(worker->running_status != Slave_worker::ERROR_LEAVING);
                        // de-queueing and decrement counters is in the caller's exit branch
                        error = -1;
                        goto err;
                }

                ev = job_item->data;
                assert(ev != nullptr);
                DBUG_PRINT("info", ("W_%lu <- job item: %p data: %p thd: %p", worker->id, job_item, ev, thd));
                /*
                  Associate the freshly read event with worker.
                  The binding also remains when the loop breaks at the group end event
                  so a DDL Query_log_event as such a breaker would remain pinned to
                  the Worker by the slave info table update and commit time,
                  see slave_worker_ends_group().
                */
                worker_curr_ev.set_current_event(ev);

                if (is_gtid_event(ev))
                        seen_gtid = true;
                if (!seen_begin && ev->starts_group()) {
                        seen_begin                     = true;  // The current group is started with B-event
                        worker->end_group_sets_max_dbs = true;
                }
                set_timespec_nsec(&worker->ts_exec[0], 0);  // pre-exec
                worker->stats_read_time += diff_timespec(&worker->ts_exec[0], &worker->ts_exec[1]);
                /* Adapting to possible new Format_description_log_event */
                ptr_g = rli->gaq->get_job_group(ev->mts_group_idx);
                if (ptr_g->new_fd_event) {
                        error = worker->set_rli_description_event(ptr_g->new_fd_event);
                        if (unlikely(error))
                                goto err;
                        ptr_g->new_fd_event = nullptr;
                }

                error = worker->slave_worker_exec_event(ev);

                set_timespec_nsec(&worker->ts_exec[1], 0);  // pre-exec
                worker->stats_exec_time += diff_timespec(&worker->ts_exec[1], &worker->ts_exec[0]);
                if (error || worker->found_commit_order_deadlock()) {
                        worker->prepare_for_retry(*ev);
                        error = worker->retry_transaction(start_relay_number,
                                                          start_relay_pos,
                                                          job_item->relay_number,
                                                          job_item->relay_pos);
                        if (error)
                                goto err;
                }
                /*
                  p-event or any other event of B-free (malformed) group can
                  "commit" with logical clock scheduler. In that case worker id
                  points to the only active "exclusive" Worker that processes such
                  malformed group events one by one.
                  WL#7592 refines the original assert disjunction formula
                  with the final disjunct.
                */
                assert(seen_begin || is_gtid_event(ev) || ev->get_type_code() == binary_log::QUERY_EVENT ||
                       is_mts_db_partitioned(rli) || worker->id == 0 || seen_gtid);

                if (ev->ends_group() || (!seen_begin && !is_gtid_event(ev) &&
                                         (ev->get_type_code() == binary_log::QUERY_EVENT ||
                                          /* break through by LC only in GTID off */
                                          (!seen_gtid && !is_mts_db_partitioned(rli)))))
                        break;

                remove_item_from_jobs(job_item, worker, rli);
                /* The event will be used later if worker is NULL, so it is not freed */
                if (ev->worker != nullptr)
                        delete ev;

                job_item = pop_jobs_item(worker, job_item);
        }

        DBUG_PRINT("info", (" commits GAQ index %lu, last committed  %lu", ev->mts_group_idx, worker->last_group_done_index));
        /* The group is applied successfully, so error should be 0 */
        worker->slave_worker_ends_group(ev, 0);

        /*
          Check if the finished group started with a Gtid_log_event to update the
          monitoring information
        */
        if (current_thd->rli_slave->is_processing_trx()) {
                DBUG_EXECUTE_IF("rpl_ps_tables", {
                        const char act[] =
                            "now SIGNAL signal.rpl_ps_tables_apply_before "
                            "WAIT_FOR signal.rpl_ps_tables_apply_finish";
                        assert(opt_debug_sync_timeout > 0);
                        assert(!debug_sync_set_action(current_thd, STRING_WITH_LEN(act)));
                };);
                if (ev->get_type_code() == binary_log::QUERY_EVENT && ((Query_log_event *)ev)->rollback_injected_by_coord) {
                        /*
                          If this was a rollback event injected by the coordinator because of a
                          partial transaction in the relay log, we must not consider this
                          transaction completed and, instead, clear the monitoring info.
                        */
                        current_thd->rli_slave->clear_processing_trx();
                } else {
                        current_thd->rli_slave->finished_processing();
                }
                DBUG_EXECUTE_IF("rpl_ps_tables", {
                        const char act[] =
                            "now SIGNAL signal.rpl_ps_tables_apply_after_finish "
                            "WAIT_FOR signal.rpl_ps_tables_apply_continue";
                        assert(opt_debug_sync_timeout > 0);
                        assert(!debug_sync_set_action(current_thd, STRING_WITH_LEN(act)));
                };);
        }

#ifndef NDEBUG
        DBUG_PRINT("mts",
                   ("Check_slave_debug_group worker %lu mta_checkpoint_group"
                    " %u processed %lu debug %d\n",
                    worker->id,
                    opt_mta_checkpoint_group,
                    worker->groups_done,
                    DBUG_EVALUATE_IF("check_replica_debug_group", 1, 0)));

        if (DBUG_EVALUATE_IF("check_replica_debug_group", 1, 0) && opt_mta_checkpoint_group == worker->groups_done) {
                DBUG_PRINT("mts", ("Putting worker %lu in busy wait.", worker->id));
                while (true)
                        my_sleep(6000000);
        }
#endif

        remove_item_from_jobs(job_item, worker, rli);
        delete ev;

        return 0;
err:
        if (error) {
                report_error_to_coordinator(worker);
                DBUG_PRINT("info",
                           ("Worker %lu is exiting: killed %i, error %i, "
                            "running_status %d",
                            worker->id,
                            thd->killed.load(),
                            thd->is_error(),
                            worker->running_status));
                worker->slave_worker_ends_group(ev, error); /* last done sets post exec */
        }
        return error;
}

```


#### slave_worker_exec_event
> `ret = ev->do_apply_event_worker(this);`

```c++
int Slave_worker::slave_worker_exec_event(Log_event *ev) {
        Relay_log_info *rli = c_rli;
        THD *thd            = info_thd;
        int ret             = 0;

        DBUG_TRACE;

        thd->server_id = ev->server_id;
        thd->set_time();
        thd->lex->set_current_query_block(nullptr);
        if (!ev->common_header->when.tv_sec)
                ev->common_header->when.tv_sec = static_cast<long>(my_time(0));
        ev->thd    = thd;  // todo: assert because up to this point, ev->thd == 0
        ev->worker = this;

#ifndef NDEBUG
        if (!is_mts_db_partitioned(rli) && may_have_timestamp(ev) && !curr_group_seen_sequence_number) {
                curr_group_seen_sequence_number = true;

                longlong lwm_estimate =
                    static_cast<Mts_submode_logical_clock *>(rli->current_mts_submode)->estimate_lwm_timestamp();
                int64 last_committed  = get_last_committed(ev);
                int64 sequence_number = get_sequence_number(ev);
                /*
                  The commit timestamp waiting condition:

                    lwm_estimate < last_committed  <=>  last_committed  \not <= lwm_estimate

                  must have been satisfied by Coordinator.
                  The first scheduled transaction does not have to wait for anybody.
                */
                assert(rli->gaq->entry == ev->mts_group_idx ||
                       Mts_submode_logical_clock::clock_leq(last_committed, lwm_estimate));
                assert(lwm_estimate != SEQ_UNINIT || rli->gaq->entry == ev->mts_group_idx);
                /*
                  The current transaction's timestamp can't be less that lwm.
                */
                assert(sequence_number == SEQ_UNINIT ||
                       !Mts_submode_logical_clock::clock_leq(
                           sequence_number,
                           static_cast<Mts_submode_logical_clock *>(rli->current_mts_submode)->estimate_lwm_timestamp()));
        }
#endif

        // Address partioning only in database mode
        if (!is_gtid_event(ev) && is_mts_db_partitioned(rli)) {
                if (ev->contains_partition_info(end_group_sets_max_dbs)) {
                        uint num_dbs = ev->mts_number_dbs();

                        if (num_dbs == OVER_MAX_DBS_IN_EVENT_MTS)
                                num_dbs = 1;

                        assert(num_dbs > 0);

                        for (uint k = 0; k < num_dbs; k++) {
                                bool found = false;

                                for (size_t i = 0; i < curr_group_exec_parts.size() && !found; i++) {
                                        found = curr_group_exec_parts[i] == ev->mts_assigned_partitions[k];
                                }
                                if (!found) {
                                        /*
                                          notice, can't assert
                                          assert(ev->mts_assigned_partitions[k]->worker == worker);
                                          since entry could be marked as wanted by other worker.
                                        */
                                        curr_group_exec_parts.push_back(ev->mts_assigned_partitions[k]);
                                }
                        }
                        end_group_sets_max_dbs = false;
                }
        }

        set_future_event_relay_log_pos(ev->future_event_relay_log_pos);
        set_master_log_pos(static_cast<ulong>(ev->common_header->log_pos));
        set_gaq_index(ev->mts_group_idx);
        ret = ev->do_apply_event_worker(this);
        return ret;
}
```

#### Log_event::do_apply_event_worker
> `return do_apply_event(w);`

```c++
#ifdef MYSQL_SERVER
inline int Log_event::do_apply_event_worker(Slave_worker *w) {
        DBUG_EXECUTE_IF("crash_in_a_worker", {
                /* we will crash a worker after waiting for
                2 seconds to make sure that other transactions are
                scheduled and completed */
                if (w->id == 2) {
                        DBUG_SET("-d,crash_in_a_worker");
                        my_sleep(2000000);
                        DBUG_SUICIDE();
                }
        });
        return do_apply_event(w);
}
```

#### Rows_log_event::do_apply_event
> `error = (this->*do_apply_row_ptr)(rli);`

```c++
int Rows_log_event::do_apply_event(Relay_log_info const *rli) {
        DBUG_TRACE;
        TABLE *table = nullptr;
        int error    = 0;

        /*
          'thd' has been set by exec_relay_log_event(), just before calling
          do_apply_event(). We still check here to prevent future coding
          errors.
        */
        assert(rli->info_thd == thd);

        /*
          If there is no locks taken, this is the first binrow event seen
          after the table map events.  We should then lock all the tables
          used in the transaction and proceed with execution of the actual
          event.
        */
        if (!thd->lock) {
                /*
                  Lock_tables() reads the contents of thd->lex, so they must be
                  initialized.

                  We also call the mysql_reset_thd_for_next_command(), since this
                  is the logical start of the next "statement". Note that this
                  call might reset the value of current_stmt_binlog_format, so
                  we need to do any changes to that value after this function.
                */
                lex_start(thd);
                mysql_reset_thd_for_next_command(thd);

                enum_gtid_statement_status state = gtid_pre_statement_checks(thd);
                if (state == GTID_STATEMENT_EXECUTE) {
                        if (gtid_pre_statement_post_implicit_commit_checks(thd))
                                state = GTID_STATEMENT_CANCEL;
                }

                if (state == GTID_STATEMENT_CANCEL) {
                        uint mysql_error = thd->get_stmt_da()->mysql_errno();
                        assert(mysql_error != 0);
                        rli->report(ERROR_LEVEL,
                                    mysql_error,
                                    "Error executing row event: '%s'",
                                    thd->get_stmt_da()->message_text());
                        thd->is_slave_error = true;
                        return -1;
                } else if (state == GTID_STATEMENT_SKIP)
                        goto end;

                /*
                  The current statement is just about to begin and
                  has not yet modified anything. Note, all.modified is reset
                  by mysql_reset_thd_for_next_command.
                */
                thd->get_transaction()->reset_unsafe_rollback_flags(Transaction_ctx::STMT);
                /*
                  This is a row injection, so we flag the "statement" as
                  such. Note that this code is called both when the slave does row
                  injections and when the BINLOG statement is used to do row
                  injections.
                */
                thd->lex->set_stmt_row_injection();

                /*
                  There are a few flags that are replicated with each row event.
                  Make sure to set/clear them before executing the main body of
                  the event.
                */
                if (get_flags(NO_FOREIGN_KEY_CHECKS_F))
                        thd->variables.option_bits |= OPTION_NO_FOREIGN_KEY_CHECKS;
                else
                        thd->variables.option_bits &= ~OPTION_NO_FOREIGN_KEY_CHECKS;

                if (get_flags(RELAXED_UNIQUE_CHECKS_F))
                        thd->variables.option_bits |= OPTION_RELAXED_UNIQUE_CHECKS;
                else
                        thd->variables.option_bits &= ~OPTION_RELAXED_UNIQUE_CHECKS;

                thd->binlog_row_event_extra_data = m_extra_row_info.get_ndb_info();

                /* A small test to verify that objects have consistent types */
                assert(sizeof(thd->variables.option_bits) == sizeof(OPTION_RELAXED_UNIQUE_CHECKS));
                DBUG_EXECUTE_IF("rows_log_event_before_open_table", {
                        const char action[] = "now SIGNAL before_open_table WAIT_FOR go_ahead_sql";
                        assert(!debug_sync_set_action(thd, STRING_WITH_LEN(action)));
                };);
                if (open_and_lock_tables(thd, rli->tables_to_lock, 0)) {
                        if (thd->is_error()) {
                                uint actual_error = thd->get_stmt_da()->mysql_errno();
                                if (ignored_error_code(actual_error)) {
                                        if (log_error_verbosity >= 2)
                                                rli->report(WARNING_LEVEL,
                                                            actual_error,
                                                            "Error executing row event: '%s'",
                                                            thd->get_stmt_da()->message_text());
                                        thd->get_stmt_da()->reset_condition_info(thd);
                                        clear_all_errors(thd, const_cast<Relay_log_info *>(rli));
                                        error = 0;
                                        goto end;
                                } else {
                                        rli->report(ERROR_LEVEL,
                                                    actual_error,
                                                    "Error executing row event: '%s'",
                                                    thd->get_stmt_da()->message_text());
                                        thd->is_slave_error = true;
                                }
                        }
                        return 1;
                }

                /*
                  When the open and locking succeeded, we check all tables to
                  ensure that they still have the correct type.
                */

                {
                        DBUG_PRINT("debug",
                                   ("Checking compability of tables to lock - tables_to_lock: %p", rli->tables_to_lock));

                        /**
                          When using RBR and MyISAM MERGE tables the base tables that make
                          up the MERGE table can be appended to the list of tables to lock.

                          Thus, we just check compatibility for those that tables that have
                          a correspondent table map event (ie, those that are actually going
                          to be accessed while applying the event). That's why the loop stops
                          at rli->tables_to_lock_count .

                          NOTE: The base tables are added here are removed when
                                close_thread_tables is called.
                         */
                        TABLE_LIST *table_list_ptr = rli->tables_to_lock;
                        for (uint i         = 0; table_list_ptr && (i < rli->tables_to_lock_count);
                             table_list_ptr = table_list_ptr->next_global, i++) {
                                /*
                                  Below if condition takes care of skipping base tables that
                                  make up the MERGE table (which are added by open_tables()
                                  call). They are added next to the merge table in the list.
                                  For eg: If RPL_TABLE_LIST is t3->t1->t2 (where t1 and t2
                                  are base tables for merge table 't3'), open_tables will modify
                                  the list by adding t1 and t2 again immediately after t3 in the
                                  list (*not at the end of the list*). New table_to_lock list will
                                  look like t3->t1'->t2'->t1->t2 (where t1' and t2' are TABLE_LIST
                                  objects added by open_tables() call). There is no flag(or logic) in
                                  open_tables() that can skip adding these base tables to the list.
                                  So the logic here should take care of skipping them.

                                  tables_to_lock_count logic will take care of skipping base tables
                                  that are added at the end of the list.
                                  For eg: If RPL_TABLE_LIST is t1->t2->t3, open_tables will modify
                                  the list into t1->t2->t3->t1'->t2'. t1' and t2' will be skipped
                                  because tables_to_lock_count logic in this for loop.
                                */
                                if (table_list_ptr->parent_l)
                                        continue;
                                /*
                                  We can use a down cast here since we know that every table added
                                  to the tables_to_lock is a RPL_TABLE_LIST (or child table which is
                                  skipped above).
                                */
                                RPL_TABLE_LIST *ptr = static_cast<RPL_TABLE_LIST *>(table_list_ptr);
                                assert(ptr->m_tabledef_valid);
                                TABLE *conv_table;
                                if (!ptr->m_tabledef.compatible_with(thd,
                                                                     const_cast<Relay_log_info *>(rli),
                                                                     ptr->table,
                                                                     &conv_table)) {
                                        DBUG_PRINT("debug",
                                                   ("Table: %s.%s is not compatible with master",
                                                    ptr->table->s->db.str,
                                                    ptr->table->s->table_name.str));
                                        if (thd->is_slave_error) {
                                                const_cast<Relay_log_info *>(rli)->slave_close_thread_tables(thd);
                                                return ERR_BAD_TABLE_DEF;
                                        } else {
                                                thd->get_stmt_da()->reset_condition_info(thd);
                                                clear_all_errors(thd, const_cast<Relay_log_info *>(rli));
                                                error = 0;
                                                goto end;
                                        }
                                }
                                DBUG_PRINT("debug",
                                           ("Table: %s.%s is compatible with master"
                                            " - conv_table: %p",
                                            ptr->table->s->db.str,
                                            ptr->table->s->table_name.str,
                                            conv_table));
                                ptr->m_conv_table = conv_table;
                        }
                }

                /*
                  ... and then we add all the tables to the table map and but keep
                  them in the tables to lock list.
                 */
                TABLE_LIST *ptr = rli->tables_to_lock;
                for (uint i = 0; ptr && (i < rli->tables_to_lock_count); ptr = ptr->next_global, i++) {
                        /*
                          Please see comment in above 'for' loop to know the reason
                          for this if condition
                        */
                        if (ptr->parent_l)
                                continue;
                        const_cast<Relay_log_info *>(rli)->m_table_map.set_table(ptr->table_id, ptr->table);
                }

                /*
                  Validate applied binlog events with plugin requirements.
                */
                int out_value  = 0;
                int hook_error = RUN_HOOK(binlog_relay_io, applier_log_event, (thd, out_value));
                if (hook_error || out_value) {
                        char buf[256];
                        uint applier_error = ER_APPLIER_LOG_EVENT_VALIDATION_ERROR;

                        if (hook_error) {
                                applier_error = ER_RUN_HOOK_ERROR;
                                strcpy(buf, "applier_log_event");
                        } else {
                                if (!thd->owned_gtid_is_empty() && thd->owned_gtid.sidno > 0) {
                                        thd->owned_gtid.to_string(thd->owned_sid, buf);
                                } else {
                                        strcpy(buf, "ANONYMOUS");
                                }
                        }

                        if (thd->slave_thread) {
                                rli->report(ERROR_LEVEL, applier_error, ER_THD_NONCONST(thd, applier_error), buf);
                                thd->is_slave_error = true;
                                const_cast<Relay_log_info *>(rli)->slave_close_thread_tables(thd);
                        } else {
                                /*
                                  For the cases in which a 'BINLOG' statement is set to
                                  execute in a user session
                                */
                                my_printf_error(applier_error, ER_THD_NONCONST(thd, applier_error), MYF(0), buf);
                        }
                        return applier_error;
                }
        }

        table = m_table = const_cast<Relay_log_info *>(rli)->m_table_map.get_table(m_table_id);

        DBUG_PRINT("debug", ("m_table: %p, m_table_id: %llu", m_table, m_table_id.id()));

        /*
          A row event comprising of a P_S table
          - should not be replicated (i.e executed) by the slave SQL thread.
          - should not be executed by the client in the  form BINLOG '...' stmts.
        */
        if (table && table->s->table_category == TABLE_CATEGORY_PERFORMANCE)
                table = nullptr;

        if (table) {
                this->m_fields.set_thd(thd).set_table(table);

                /*
                 Translate received replicated column bitmaps into local table column
                 bitmaps. This is needed when the table has columns that are to be excluded
                 from replication - hidden generated columns, for instance.
                */
                this->m_fields.translate_bitmap(this->m_cols, this->m_local_cols);
                if (this->m_cols.bitmap != this->m_cols_ai.bitmap)
                        this->m_fields.translate_bitmap(this->m_cols_ai, this->m_local_cols_ai);
                else
                        this->m_local_cols_ai.bitmap = this->m_local_cols.bitmap;

                /*
                  table == NULL means that this table should not be replicated
                  (this was set up by Table_map_log_event::do_apply_event()
                  which tested replicate-* rules).
                */

                Applier_security_context_guard security_context{rli, thd};
                const char *privilege_missing = nullptr;
                if (!security_context.skip_priv_checks()) {
                        std::vector<std::tuple<ulong, const TABLE *, Rows_log_event *>> l;
                        switch (get_general_type_code()) {
                                case binary_log::WRITE_ROWS_EVENT: {
                                        l.push_back(std::make_tuple(INSERT_ACL, this->m_table, this));
                                        if (!security_context.has_access(l)) {
                                                privilege_missing = "INSERT";
                                        }
                                        break;
                                }
                                case binary_log::DELETE_ROWS_EVENT: {
                                        l.push_back(std::make_tuple(DELETE_ACL, this->m_table, this));
                                        if (!security_context.has_access(l)) {
                                                privilege_missing = "DELETE";
                                        }
                                        break;
                                }
                                case binary_log::UPDATE_ROWS_EVENT:
                                case binary_log::PARTIAL_UPDATE_ROWS_EVENT: {
                                        l.push_back(std::make_tuple(UPDATE_ACL, this->m_table, this));
                                        if (!security_context.has_access(l)) {
                                                privilege_missing = "UPDATE";
                                        }
                                        break;
                                }
                                default: {
                                        assert(false);
                                }
                        }
                }
                if (privilege_missing != nullptr) {
                        rli->report(ERROR_LEVEL,
                                    ER_TABLEACCESS_DENIED_ERROR,
                                    ER_THD(thd, ER_TABLEACCESS_DENIED_ERROR),
                                    privilege_missing,
                                    security_context.get_username().data(),
                                    security_context.get_hostname().data(),
                                    table->s->table_name.str);
                        return ER_TABLEACCESS_DENIED_ERROR;
                }

                bool no_columns_to_update = false;
                // set the database
                LEX_CSTRING thd_db;
                LEX_CSTRING current_db_name_saved = thd->db();
                thd_db.str                        = table->s->db.str;
                thd_db.length                     = table->s->db.length;
                thd->reset_db(thd_db);
                thd->set_command(COM_QUERY);
                PSI_stage_info *stage = nullptr;

                /*
                  It's not needed to set_time() but
                  1) it continues the property that "Time" in SHOW PROCESSLIST shows how
                  much slave is behind
                  2) it will be needed when we allow replication from a table with no
                  TIMESTAMP column to a table with one.
                  So we call set_time(), like in SBR. Presently it changes nothing.
                */
                thd->set_time(&(common_header->when));

                thd->binlog_row_event_extra_data = m_extra_row_info.get_ndb_info();

                /*
                  Now we are in a statement and will stay in a statement until we
                  see a STMT_END_F.

                  We set this flag here, before actually applying any rows, in
                  case the SQL thread is stopped and we need to detect that we're
                  inside a statement and halting abruptly might cause problems
                  when restarting.
                 */
                const_cast<Relay_log_info *>(rli)->set_flag(Relay_log_info::IN_STMT);

                if (m_width == this->m_fields.filtered_size() && bitmap_is_set_all(&m_cols))
                        set_flags(COMPLETE_ROWS_F);

                /*
                  Set tables write and read sets.

                  Read_set contains all slave columns (in case we are going to fetch
                  a complete record from slave)

                  Write_set equals the m_cols bitmap sent from master but it can be
                  longer if slave has extra columns.
                */

                bitmap_set_all(table->read_set);
                bitmap_set_all(table->write_set);

                /*
                  Call mark_generated_columns() to set read_set/write_set bits of the
                  virtual generated columns as required in order to get these computed.
                  This is needed since all columns need to have a value in the before
                  image for the record when doing the update (some storage engines will
                  use this for maintaining of secondary indexes). This call is required
                  even for DELETE events to set write_set bit in order to satisfy
                  ASSERTs in Field_*::store functions.

                  binlog_prepare_row_image() function, which will be called from
                  binlogging functions (binlog_update_row() and binlog_delete_row())
                  will take care of removing these spurious fields required during
                  execution but not needed for binlogging. In case of inserts, there
                  are no spurious fields (all generated columns are required to be written
                  into the binlog).
                */
                switch (get_general_type_code()) {
                        case binary_log::DELETE_ROWS_EVENT:
                                bitmap_intersect(table->read_set, &this->m_local_cols);
                                stage = &stage_rpl_apply_row_evt_delete;
                                if (m_table->vfield)
                                        m_table->mark_generated_columns(false);
                                break;
                        case binary_log::UPDATE_ROWS_EVENT:
                                bitmap_intersect(table->read_set, &this->m_local_cols);
                                bitmap_intersect(table->write_set, &this->m_local_cols_ai);
                                if (m_table->vfield)
                                        m_table->mark_generated_columns(true);
                                /* Skip update rows events that don't have data for this server's table.
                                 */
                                if (!is_any_column_signaled_for_table(table, &this->m_local_cols_ai))
                                        no_columns_to_update = true;
                                stage = &stage_rpl_apply_row_evt_update;
                                break;
                        case binary_log::WRITE_ROWS_EVENT:
                                /*
                                  For 'WRITE_ROWS_EVENT, the execution order for 'mark_generated_rows()'
                                  and bitset intersection between 'write_set' and 'm_cols', is inverted.
                                  This behaviour is necessary due to an inconsistency, between storage
                                  engines, regarding the 'm_cols' bitset and generated columns: while
                                  non-NDB engines always include the generated columns for write-rows
                                  events, NDB doesnot if not necessary. The previous execution order
                                  would set all generated columns bits to '1' in 'write_set', since
                                  'mark_generated_columns()' is expecting that every column is present
                                  in the log event. This would break replication of generated columns
                                  for NDB.

                                  For engines that include every column in write-rows events, this order
                                  makes no difference, assuming that the master uses the same engine,
                                  since the master will include all the bits in the image.

                                  For use-cases that use different storage engines, specifically NDB
                                  and some other, this order may break replication due to the
                                  differences in behaviour regarding generated columns bits, in
                                  wrote-rows event bitsets. This issue should be further addressed by
                                  storage engines handlers, by converging behaviour regarding such use
                                  cases.
                                */
                                /* WRITE ROWS EVENTS store the bitmap in the m_cols bitmap */
                                if (m_table->vfield)
                                        m_table->mark_generated_columns(false);
                                bitmap_intersect(table->write_set, &this->m_local_cols);
                                stage = &stage_rpl_apply_row_evt_write;
                                break;
                        default:
                                assert(false);
                }

                if (thd->slave_thread)  // set the mode for slave
                        this->rbr_exec_mode = replica_exec_mode_options;
                else  // set the mode for user thread
                        this->rbr_exec_mode = thd->variables.rbr_exec_mode_options;

                // Do event specific preparations
                error = do_before_row_operations(rli);

                /*
                  Bug#56662 Assertion failed: next_insert_id == 0, file handler.cc
                  Don't allow generation of auto_increment value when processing
                  rows event by setting 'MODE_NO_AUTO_VALUE_ON_ZERO'. The exception
                  to this rule happens when the auto_inc column exists on some
                  extra columns on the slave. In that case, do not force
                  MODE_NO_AUTO_VALUE_ON_ZERO.
                */
                sql_mode_t saved_sql_mode = thd->variables.sql_mode;
                if (!is_auto_inc_in_extra_columns())
                        thd->variables.sql_mode = MODE_NO_AUTO_VALUE_ON_ZERO;

                // row processing loop

                /*
                  set the initial time of this ROWS statement if it was not done
                  before in some other ROWS event.
                 */
                const_cast<Relay_log_info *>(rli)->set_row_stmt_start_timestamp();

                const uchar *saved_m_curr_row = m_curr_row;

                int (Rows_log_event::*do_apply_row_ptr)(Relay_log_info const *) = nullptr;

                /**
                   Skip update rows events that don't have data for this slave's
                   table.
                 */
                if (no_columns_to_update)
                        goto AFTER_MAIN_EXEC_ROW_LOOP;

                /**
                   If there are no columns marked in the read_set for this table,
                   that means that we cannot lookup any row using the available BI
                   in the binarr log. Thence, we immediatly raise an error:
                   HA_ERR_END_OF_FILE.
                 */

                if ((m_rows_lookup_algorithm != ROW_LOOKUP_NOT_NEEDED) &&
                    !is_any_column_signaled_for_table(table, &this->m_local_cols)) {
                        error = HA_ERR_END_OF_FILE;
                        goto AFTER_MAIN_EXEC_ROW_LOOP;
                }
                switch (m_rows_lookup_algorithm) {
                        case ROW_LOOKUP_HASH_SCAN:
                                do_apply_row_ptr = &Rows_log_event::do_hash_scan_and_update;
                                break;

                        case ROW_LOOKUP_INDEX_SCAN:
                                do_apply_row_ptr = &Rows_log_event::do_index_scan_and_update;
                                break;

                        case ROW_LOOKUP_TABLE_SCAN:
                                do_apply_row_ptr = &Rows_log_event::do_table_scan_and_update;
                                break;

                        case ROW_LOOKUP_NOT_NEEDED:
                                assert(get_general_type_code() == binary_log::WRITE_ROWS_EVENT);

                                /* No need to scan for rows, just apply it */
                                do_apply_row_ptr = &Rows_log_event::do_apply_row;
                                break;

                        default:
                                assert(0);
                                error = 1;
                                goto AFTER_MAIN_EXEC_ROW_LOOP;
                                break;
                }

                assert(stage != nullptr);
                THD_STAGE_INFO(thd, *stage);

        #ifdef HAVE_PSI_STAGE_INTERFACE
                m_psi_progress.set_progress(mysql_set_stage(stage->m_key));
        #endif

                do {
                        DBUG_PRINT("info", ("calling do_apply_row_ptr"));

                        error = (this->*do_apply_row_ptr)(rli);

                        if (handle_idempotent_and_ignored_errors(rli, &error))
                                break;

                        /* this advances m_curr_row */
                        do_post_row_operations(rli, error);

                } while (!error && (m_curr_row != m_rows_end));

        #ifdef HAVE_PSI_STAGE_INTERFACE
                m_psi_progress.end_work();
        #endif

        AFTER_MAIN_EXEC_ROW_LOOP:

                if (saved_m_curr_row != m_curr_row && !table->file->has_transactions()) {
                        /*
                          Usually, the trans_commit_stmt() propagates unsafe_rollback_flags
                          from statement to transaction level. However, we cannot rely on
                          this when row format is in use as several events can be processed
                          before calling this function. This happens because it is called
                          only when the latest event generated by a statement is processed.

                          There are however upper level functions that execute per event
                          and check transaction's status. So if the unsafe_rollback_flags
                          are not propagated here, this can lead to errors.

                          For example, a transaction that updates non-transactional tables
                          may be stopped in the middle thus leading to inconsistencies
                          after a restart.
                        */
                        thd->get_transaction()->mark_modified_non_trans_table(Transaction_ctx::STMT);
                        thd->get_transaction()->merge_unsafe_rollback_flags();
                }

                /*
                  Restore the sql_mode after the rows event is processed.
                */
                thd->variables.sql_mode = saved_sql_mode;

                { /*
                      The following failure injecion works in cooperation with tests
                      setting @@global.debug= 'd,stop_replica_middle_group'.
                      The sql thread receives the killed status and will proceed
                      to shutdown trying to finish incomplete events group.
                  */
                        DBUG_EXECUTE_IF("stop_replica_middle_group",
                                        if (thd->get_transaction()->cannot_safely_rollback(
                                                Transaction_ctx::SESSION)) const_cast<Relay_log_info *>(rli)
                                            ->abort_slave = 1;);
                }

                if ((error = do_after_row_operations(rli, error)) &&
                    ignored_error_code(convert_handler_error(error, thd, table))) {
                        slave_rows_error_report(INFORMATION_LEVEL,
                                                error,
                                                rli,
                                                thd,
                                                table,
                                                get_type_str(),
                                                const_cast<Relay_log_info *>(rli)->get_rpl_log_name(),
                                                (ulong)common_header->log_pos);
                        thd->get_stmt_da()->reset_condition_info(thd);
                        clear_all_errors(thd, const_cast<Relay_log_info *>(rli));
                        error = 0;
                }

                // reset back the db
                thd->reset_db(current_db_name_saved);
        }  // if (table)

        if (error) {
                slave_rows_error_report(ERROR_LEVEL,
                                        error,
                                        rli,
                                        thd,
                                        table,
                                        get_type_str(),
                                        const_cast<Relay_log_info *>(rli)->get_rpl_log_name(),
                                        (ulong)common_header->log_pos);
                /*
                  @todo We should probably not call
                  reset_current_stmt_binlog_format_row() from here.
                  /Sven
                */
                thd->reset_current_stmt_binlog_format_row();
                thd->is_slave_error = true;
                return error;
        }

end:
        if (get_flags(STMT_END_F)) {
                if ((error = rows_event_stmt_cleanup(rli, thd))) {
                        if (table)
                                slave_rows_error_report(ERROR_LEVEL,
                                                        thd->is_error() ? 0 : error,
                                                        rli,
                                                        thd,
                                                        table,
                                                        get_type_str(),
                                                        const_cast<Relay_log_info *>(rli)->get_rpl_log_name(),
                                                        (ulong)common_header->log_pos);
                        else {
                                rli->report(ERROR_LEVEL,
                                            thd->is_error() ? thd->get_stmt_da()->mysql_errno() : error,
                                            "Error in cleaning up after an event of type:%s; %s; the group"
                                            " log file/position: %s %lu",
                                            get_type_str(),
                                            thd->is_error() ? thd->get_stmt_da()->message_text() : "unexpected error",
                                            const_cast<Relay_log_info *>(rli)->get_rpl_log_name(),
                                            (ulong)common_header->log_pos);
                        }
                }
                /* We are at end of the statement (STMT_END_F flag), lets clean
                  the memory which was used from thd's mem_root now.
                  This needs to be done only if we are here in SQL thread context.
                  In other flow ( in case of a regular thread which can happen
                  when the thread is applying BINLOG'...' row event) we should
                  *not* try to free the memory here. It will be done latter
                  in dispatch_command() after command execution is completed.
                 */
                if (thd->slave_thread)
                        free_root(thd->mem_root, MYF(MY_KEEP_PREALLOC));
        }
        return error;
}
```


#### Rows_log_event::do_apply_row
> `error = do_exec_row(rli);`

```c++
int Rows_log_event::do_apply_row(Relay_log_info const *rli) {
        DBUG_TRACE;

        int error = 0;

        /* in_use can have been set to NULL in close_tables_for_reopen */
        THD *old_thd = m_table->in_use;
        if (!m_table->in_use)
                m_table->in_use = thd;

        error = do_exec_row(rli);

        if (error) {
                DBUG_PRINT("info", ("error: %s", HA_ERR(error)));
                assert(error != HA_ERR_RECORD_DELETED);
        }
        m_table->in_use = old_thd;

        return error;
}
```

#### Write_rows_log_event::do_exec_row
> `int error = write_row(rli, rbr_exec_mode == RBR_EXEC_MODE_IDEMPOTENT);`

```c++
int Write_rows_log_event::do_exec_row(const Relay_log_info *const rli) {
        assert(m_table != nullptr);
        int error = write_row(rli, rbr_exec_mode == RBR_EXEC_MODE_IDEMPOTENT);

        if (error && !thd->is_error()) {
                assert(0);
                my_error(ER_UNKNOWN_ERROR, MYF(0));
        }

        return error;
}
```


#### Write_rows_log_event::write_row
> `while ((error = table->file->ha_write_row(table->record[0]))) {`


```c++
int Write_rows_log_event::write_row(const Relay_log_info *const rli, const bool overwrite) {
        DBUG_TRACE;
        assert(m_table != nullptr && thd != nullptr);

        TABLE *table = m_table;  // pointer to event's table
        int error;
        int keynum = 0;
        char *key  = nullptr;

        prepare_record(table, &this->m_local_cols, table->file->ht->db_type != DB_TYPE_NDBCLUSTER);

        /* unpack row into table->record[0] */
        if ((error = unpack_current_row(rli, &m_cols, true /*is AI*/)))
                return error;

        /*
          When m_curr_row == m_curr_row_end, it means a row that contains nothing,
          so all the pointers shall be pointing to the same address, or else
          we have corrupt data and shall throw the error.
        */
        DBUG_PRINT("debug", ("m_rows_buf= %p, m_rows_cur= %p, m_rows_end= %p", m_rows_buf, m_rows_cur, m_rows_end));
        DBUG_PRINT("debug", ("m_curr_row= %p, m_curr_row_end= %p", m_curr_row, m_curr_row_end));
        if (m_curr_row == m_curr_row_end && !((m_rows_buf == m_rows_cur) && (m_rows_cur == m_rows_end))) {
                my_error(ER_SLAVE_CORRUPT_EVENT, MYF(0));
                return ER_SLAVE_CORRUPT_EVENT;
        }

        // Invoke check constraints on the unpacked row.
        if (invoke_table_check_constraints(thd, table))
                return ER_CHECK_CONSTRAINT_VIOLATED;

        if (m_curr_row == m_rows_buf) {
                /* this is the first row to be inserted, we estimate the rows with
                   the size of the first row and use that value to initialize
                   storage engine for bulk insertion */
                assert(!(m_curr_row > m_curr_row_end));
                ulong estimated_rows = 0;
                if (m_curr_row < m_curr_row_end)
                        estimated_rows = (m_rows_end - m_curr_row) / (m_curr_row_end - m_curr_row);
                else if (m_curr_row == m_curr_row_end)
                        estimated_rows = 1;

                m_table->file->ha_start_bulk_insert(estimated_rows);
        }

        /*
          Explicitly set the auto_inc to null to make sure that
          it gets an auto_generated value.
        */
        if (is_auto_inc_in_extra_columns())
                m_table->next_number_field->set_null();

        #ifndef NDEBUG
        DBUG_DUMP("record[0]", table->record[0], table->s->reclength);
        DBUG_PRINT_BITSET("debug", "write_set = %s", table->write_set);
        DBUG_PRINT_BITSET("debug", "read_set = %s", table->read_set);
        #endif

        /*
          Try to write record. If a corresponding record already exists in the table,
          we try to change it using ha_update_row() if possible. Otherwise we delete
          it and repeat the whole process again.

          TODO: Add safety measures against infinite looping.
         */

        m_table->mark_columns_per_binlog_row_image(thd);

        while ((error = table->file->ha_write_row(table->record[0]))) {
                if (error == HA_ERR_LOCK_DEADLOCK || error == HA_ERR_LOCK_WAIT_TIMEOUT ||
                    (keynum = table->file->get_dup_key(error)) < 0 || !overwrite) {
                        DBUG_PRINT("info", ("get_dup_key returns %d)", keynum));
                        /*
                          Deadlock, waiting for lock or just an error from the handler
                          such as HA_ERR_FOUND_DUPP_KEY when overwrite is false.
                          Retrieval of the duplicate key number may fail
                          - either because the error was not "duplicate key" error
                          - or because the information which key is not available
                        */
                        table->file->print_error(error, MYF(0));
                        goto error;
                }
                /*
                  key index value is either valid in the range [0-MAX_KEY) or
                  has value MAX_KEY as a marker for the case when no information
                  about key can be found. In the last case we have to require
                  that storage engine has the flag HA_DUPLICATE_POS turned on.
                  If this invariant is false then assert will crash
                  the server built in debug mode. For the server that was built
                  without DEBUG we have additional check for the value of key index
                  in the code below in order to report about error in any case.
                */
                assert(keynum != MAX_KEY || (keynum == MAX_KEY && (table->file->ha_table_flags() & HA_DUPLICATE_POS)));
                /*
                   We need to retrieve the old row into record[1] to be able to
                   either update or delete the offending record.  We either:

                   - use ha_rnd_pos() with a row-id (available as dupp_row) to the
                     offending row, if that is possible (MyISAM and Blackhole), or else

                   - use ha_index_read_idx_map() with the key that is duplicated, to
                     retrieve the offending row.
                 */
                if (table->file->ha_table_flags() & HA_DUPLICATE_POS) {
                        DBUG_PRINT("info", ("Locating offending record using ha_rnd_pos()"));

                        if (table->file->inited && (error = table->file->ha_index_end())) {
                                table->file->print_error(error, MYF(0));
                                goto error;
                        }
                        if ((error = table->file->ha_rnd_init(false))) {
                                table->file->print_error(error, MYF(0));
                                goto error;
                        }

                        error = table->file->ha_rnd_pos(table->record[1], table->file->dup_ref);

                        table->file->ha_rnd_end();
                        if (error) {
                                DBUG_PRINT("info", ("ha_rnd_pos() returns error %d", error));
                                if (error == HA_ERR_RECORD_DELETED)
                                        error = HA_ERR_KEY_NOT_FOUND;
                                table->file->print_error(error, MYF(0));
                                goto error;
                        }
                } else {
                        DBUG_PRINT("info", ("Locating offending record using index_read_idx()"));

                        if (key == nullptr) {
                                key = static_cast<char *>(my_alloca(table->s->max_unique_length));
                                if (key == nullptr) {
                                        DBUG_PRINT("info", ("Can't allocate key buffer"));
                                        error = ENOMEM;
                                        goto error;
                                }
                        }

                        if ((uint)keynum < MAX_KEY) {
                                key_copy((uchar *)key, table->record[0], table->key_info + keynum, 0);
                                error = table->file->ha_index_read_idx_map(table->record[1],
                                                                           keynum,
                                                                           (const uchar *)key,
                                                                           HA_WHOLE_KEY,
                                                                           HA_READ_KEY_EXACT);
                        } else
                                /*
                                  For the server built in non-debug mode returns error if
                                  handler::get_dup_key() returned MAX_KEY as the value of key index.
                                */
                                error = HA_ERR_FOUND_DUPP_KEY;

                        if (error) {
                                DBUG_PRINT("info", ("ha_index_read_idx_map() returns %s", HA_ERR(error)));
                                if (error == HA_ERR_RECORD_DELETED)
                                        error = HA_ERR_KEY_NOT_FOUND;
                                table->file->print_error(error, MYF(0));
                                goto error;
                        }
                }

                /*
                   Now, record[1] should contain the offending row.  That
                   will enable us to update it or, alternatively, delete it (so
                   that we can insert the new row afterwards).
                 */

                /*
                  If row is incomplete we will use the record found to fill
                  missing columns.
                */
                if (!get_flags(COMPLETE_ROWS_F)) {
                        restore_record(table, record[1]);
                        error = unpack_current_row(rli, &m_cols, true /*is AI*/);
                }

        #ifndef NDEBUG
                DBUG_PRINT("debug", ("preparing for update: before and after image"));
                DBUG_DUMP("record[1] (before)", table->record[1], table->s->reclength);
                DBUG_DUMP("record[0] (after)", table->record[0], table->s->reclength);
        #endif

                /*
                   REPLACE is defined as either INSERT or DELETE + INSERT.  If
                   possible, we can replace it with an UPDATE, but that will not
                   work on InnoDB if FOREIGN KEY checks are necessary.

                   I (Matz) am not sure of the reason for the last_uniq_key()
                   check as, but I'm guessing that it's something along the
                   following lines.

                   Suppose that we got the duplicate key to be a key that is not
                   the last unique key for the table and we perform an update:
                   then there might be another key for which the unique check will
                   fail, so we're better off just deleting the row and inserting
                   the correct row.
                 */
                if (last_uniq_key(table, keynum) && !table->s->is_referenced_by_foreign_key()) {
                        DBUG_PRINT("info", ("Updating row using ha_update_row()"));
                        error = table->file->ha_update_row(table->record[1], table->record[0]);
                        switch (error) {
                                case HA_ERR_RECORD_IS_THE_SAME:
                                        DBUG_PRINT("info",
                                                   ("ignoring HA_ERR_RECORD_IS_THE_SAME error from"
                                                    " ha_update_row()"));
                                        error = 0;

                                case 0:
                                        break;

                                default:
                                        DBUG_PRINT("info", ("ha_update_row() returns error %d", error));
                                        table->file->print_error(error, MYF(0));
                        }

                        goto error;
                } else {
                        DBUG_PRINT("info", ("Deleting offending row and trying to write new one again"));
                        if ((error = table->file->ha_delete_row(table->record[1]))) {
                                DBUG_PRINT("info", ("ha_delete_row() returns error %d", error));
                                table->file->print_error(error, MYF(0));
                                goto error;
                        }
                        /* Will retry ha_write_row() with the offending row removed. */
                }
        }

error:
        m_table->default_column_bitmaps();
        return error;
}
```

#### handler::ha_write_row
> `if (unlikely((error = binlog_log_row(table, nullptr, buf, log_func))))`

```c++
int handler::ha_write_row(uchar *buf) {
        int error;
        Log_func *log_func = Write_rows_log_event::binlog_row_logging_function;
        assert(table_share->tmp_table != NO_TMP_TABLE || m_lock_type == F_WRLCK);

        DBUG_TRACE;
        DBUG_EXECUTE_IF("inject_error_ha_write_row", return HA_ERR_INTERNAL_ERROR;);
        DBUG_EXECUTE_IF("simulate_storage_engine_out_of_memory", return HA_ERR_SE_OUT_OF_MEMORY;);
        mark_trx_read_write();

        DBUG_EXECUTE_IF("handler_crashed_table_on_usage",
                        my_error(HA_ERR_CRASHED, MYF(ME_ERRORLOG), table_share->table_name.str);
                        set_my_errno(HA_ERR_CRASHED);
                        return HA_ERR_CRASHED;);

        MYSQL_TABLE_IO_WAIT(PSI_TABLE_WRITE_ROW, MAX_KEY, error, { error = write_row(buf); })

        if (unlikely(error))
                return error;

        if (unlikely((error = binlog_log_row(table, nullptr, buf, log_func))))
                return error; /* purecov: inspected */

        DEBUG_SYNC_C("ha_write_row_end");
        return 0;
}
```


#### binlog_log_row
> `error                = (*log_func)(thd, table, has_trans, before_record, after_record);`

```c++
int binlog_log_row(TABLE *table, const uchar *before_record, const uchar *after_record, Log_func *log_func) {
        bool error     = false;
        THD *const thd = table->in_use;

        if (check_table_binlog_row_based(thd, table)) {
                if (thd->variables.transaction_write_set_extraction != HASH_ALGORITHM_OFF) {
                        try {
                                MY_BITMAP save_read_set;
                                MY_BITMAP save_write_set;
                                if (bitmap_init(&save_read_set, NULL, table->s->fields) ||
                                    bitmap_init(&save_write_set, NULL, table->s->fields)) {
                                        my_error(ER_OUT_OF_RESOURCES, MYF(0));
                                        return HA_ERR_RBR_LOGGING_FAILED;
                                }

                                Binlog_log_row_cleanup cleanup_sentry(*table, save_read_set, save_write_set);

                                if (thd->variables.binlog_row_image == 0) {
                                        for (uint key_number = 0; key_number < table->s->keys; ++key_number) {
                                                if (((table->key_info[key_number].flags & (HA_NOSAME)) == HA_NOSAME)) {
                                                        table->mark_columns_used_by_index_no_reset(key_number, table->read_set);
                                                        table->mark_columns_used_by_index_no_reset(key_number,
                                                                                                   table->write_set);
                                                }
                                        }
                                }
                                std::array<const uchar *, 2> records{after_record, before_record};
                                for (auto rec : records) {
                                        if (rec != nullptr) {
                                                assert(rec == table->record[0] || rec == table->record[1]);
                                                bool res = add_pke(table, thd, rec);
                                                if (res)
                                                        return HA_ERR_RBR_LOGGING_FAILED;
                                        }
                                }
                        } catch (const std::bad_alloc &) {
                                my_error(ER_OUT_OF_RESOURCES, MYF(0));
                                return HA_ERR_RBR_LOGGING_FAILED;
                        }
                }
                if (table->in_use->is_error())
                        return error ? HA_ERR_RBR_LOGGING_FAILED : 0;

                DBUG_DUMP("read_set 10", (uchar *)table->read_set->bitmap, (table->s->fields + 7) / 8);

                /*
                  If there are no table maps written to the binary log, this is
                  the first row handled in this statement. In that case, we need
                  to write table maps for all locked tables to the binary log.
                */
                if (likely(!(error = write_locked_table_maps(thd)))) {
                        /*
                          We need to have a transactional behavior for SQLCOM_CREATE_TABLE
                          (i.e. CREATE TABLE... SELECT * FROM TABLE) in order to keep a
                          compatible behavior with the STMT based replication even when
                          the table is not transactional. In other words, if the operation
                          fails while executing the insert phase nothing is written to the
                          binlog.
                        */
                        bool const has_trans = thd->lex->sql_command == SQLCOM_CREATE_TABLE || table->file->has_transactions();
                        error                = (*log_func)(thd, table, has_trans, before_record, after_record);
                }
        }

        return error ? HA_ERR_RBR_LOGGING_FAILED : 0;
}
```

#### Write_rows_log_event::binlog_row_logging_function
> `return thd_arg->binlog_write_row(table, is_transactional, after_record, nullptr);`


```c++
#if defined(MYSQL_SERVER)
Write_rows_log_event::Write_rows_log_event(THD *thd_arg,
                                           TABLE *tbl_arg,
                                           const Table_id &tid_arg,
                                           bool is_transactional,
                                           const unsigned char *extra_row_ndb_info)
    : binary_log::Rows_event(log_bin_use_v1_row_events ? binary_log::WRITE_ROWS_EVENT_V1 : binary_log::WRITE_ROWS_EVENT),
      Rows_log_event(thd_arg,
                     tbl_arg,
                     tid_arg,
                     tbl_arg->write_set,
                     is_transactional,
                     log_bin_use_v1_row_events ? binary_log::WRITE_ROWS_EVENT_V1 : binary_log::WRITE_ROWS_EVENT,
                     extra_row_ndb_info) {
        common_header->type_code = m_type;
}

bool Write_rows_log_event::binlog_row_logging_function(THD *thd_arg,
                                                       TABLE *table,
                                                       bool is_transactional,
                                                       const uchar *before_record MY_ATTRIBUTE((unused)),
                                                       const uchar *after_record) {
        return thd_arg->binlog_write_row(table, is_transactional, after_record, nullptr);
}
#endif
```



#### THD::binlog_write_row
> `binlog_prepare_pending_rows_event<Write_rows_log_event>(table, server_id, len, is_trans, extra_row_info);`

```c++
int THD::binlog_write_row(TABLE *table, bool is_trans, uchar const *record, const unsigned char *extra_row_info) {
        assert(is_current_stmt_binlog_format_row() && mysql_bin_log.is_open());

        /*
          Pack records into format for transfer. We are allocating more
          memory than needed, but that doesn't matter.
        */
        Row_data_memory memory(table, record);
        if (!memory.has_memory())
                return HA_ERR_OUT_OF_MEM;

        uchar *row_data = memory.slot(0);

        size_t const len = pack_row(table, table->write_set, row_data, record, enum_row_image_type::WRITE_AI);

        Rows_log_event *const ev =
            binlog_prepare_pending_rows_event<Write_rows_log_event>(table, server_id, len, is_trans, extra_row_info);

        if (unlikely(ev == nullptr))
                return HA_ERR_OUT_OF_MEM;

        return ev->add_row_data(row_data, len);
}
```



#### THD::binlog_prepare_pending_rows_event
> `if (unlikely(mysql_bin_log.flush_and_set_pending_rows_event(this, ev, is_transactional))) {`

```c++
template <class RowsEventT>
Rows_log_event *THD::binlog_prepare_pending_rows_event(TABLE *table,
                                                       uint32 serv_id,
                                                       size_t needed,
                                                       bool is_transactional,
                                                       const unsigned char *extra_row_info,
                                                       uint32 source_part_id) {
        DBUG_TRACE;

        DBUG_EXECUTE_IF("simulate_null_pending_rows_event", { return nullptr; });

        /* Fetch the type code for the RowsEventT template parameter */
        int const general_type_code = RowsEventT::TYPE_CODE;

        partition_info *part_info = table->part_info;
        auto part_id              = get_rpl_part_id(part_info);

        Rows_log_event *pending = binlog_get_pending_rows_event(is_transactional);

        if (unlikely(pending && !pending->is_valid()))
                return nullptr;

        /*
          Check if the current event is non-NULL and a write-rows
          event. Also check if the table provided is mapped: if it is not,
          then we have switched to writing to a new table.
          If there is no pending event, we need to create one. If there is a pending
          event, but it's not about the same table id, or not of the same type
          (between Write, Update and Delete), or not the same affected columns, or
          going to be too big, flush this event to disk and create a new pending
          event.

          We do not need to check that the pending event and the new event
          have the same setting for partial json updates, because
          partialness of json can only be changed outside transactions.
        */
        if (!pending || pending->server_id != serv_id || pending->get_table_id() != table->s->table_map_id ||
            pending->get_general_type_code() != general_type_code ||
            pending->get_data_size() + needed > binlog_row_event_max_size || pending->read_write_bitmaps_cmp(table) == false ||
            !(pending->m_extra_row_info.compare_extra_row_info(extra_row_info, part_id, source_part_id))) {
                /* Create a new RowsEventT... */
                Rows_log_event *const ev =
                    new RowsEventT(this, table, table->s->table_map_id, is_transactional, extra_row_info);
                if (unlikely(!ev))
                        return nullptr;
                ev->server_id = serv_id;  // I don't like this, it's too easy to forget.
                /*
                  flush the pending event and replace it with the newly created
                  event...
                */
                if (unlikely(mysql_bin_log.flush_and_set_pending_rows_event(this, ev, is_transactional))) {
                        delete ev;
                        return nullptr;
                }

                return ev; /* This is the new pending event */
        }
        return pending; /* This is the current pending event */
}
```


#### MYSQL_BIN_LOG::flush_and_set_pending_rows_event
> `report_cache_write_error(thd, is_transactional);`

```c++
int MYSQL_BIN_LOG::flush_and_set_pending_rows_event(THD *thd, Rows_log_event *event, bool is_transactional) {
        DBUG_TRACE;
        assert(mysql_bin_log.is_open());
        DBUG_PRINT("enter", ("event: %p", event));

        int error                           = 0;
        binlog_cache_mngr *const cache_mngr = thd_get_cache_mngr(thd);

        assert(cache_mngr);

        binlog_cache_data *cache_data = cache_mngr->get_binlog_cache_data(is_transactional);

        DBUG_PRINT("info", ("cache_mngr->pending(): %p", cache_data->pending()));

        if (Rows_log_event *pending = cache_data->pending()) {
                /*
                  Write pending event to the cache.
                */
                if (cache_data->write_event(pending)) {
                        report_cache_write_error(thd, is_transactional);
                        if (check_write_error(thd) && cache_data && stmt_cannot_safely_rollback(thd))
                                cache_data->set_incident();
                        delete pending;
                        cache_data->set_pending(nullptr);
                        return 1;
                }

                delete pending;
        }

        cache_data->set_pending(event);

        return error;
}
```

##### binlog_cache_data::write_event

```c++
int binlog_cache_data::write_event(Log_event *ev) {
        DBUG_TRACE;

        if (ev != nullptr) {
                DBUG_EXECUTE_IF("simulate_disk_full_at_flush_pending", { DBUG_SET("+d,simulate_file_write_error"); });

                if (binary_event_serialize(ev, &m_cache)) {
                        DBUG_EXECUTE_IF("simulate_disk_full_at_flush_pending", {
                                DBUG_SET("-d,simulate_file_write_error");
                                DBUG_SET("-d,simulate_disk_full_at_flush_pending");
                                /*
                                   after +d,simulate_file_write_error the local cache
                                   is in unsane state. Since -d,simulate_file_write_error
                                   revokes the first simulation do_write_cache()
                                   can't be run without facing an assert.
                                   So it's blocked with the following 2nd simulation:
                                */
                                DBUG_SET("+d,simulate_do_write_cache_failure");
                        });
                        return 1;
                }
                if (ev->get_type_code() == binary_log::XID_EVENT)
                        flags.with_xid = true;
                if (ev->is_using_immediate_logging())
                        flags.immediate = true;
                /* DDL gets marked as xid-requiring at its caching. */
                if (is_atomic_ddl_event(ev))
                        flags.with_xid = true;
                /* With respect to the event type being written */
                if (ev->is_sbr_logging_format())
                        flags.with_sbr = true;
                if (ev->is_rbr_logging_format())
                        flags.with_rbr = true;
                /* With respect to empty transactions */
                if (ev->starts_group())
                        flags.with_start = true;
                if (ev->ends_group())
                        flags.with_end = true;
                if (!ev->starts_group() && !ev->ends_group())
                        flags.with_content = true;
                event_counter++;
                DBUG_PRINT("debug", ("event_counter= %lu", static_cast<ulong>(event_counter)));
        }
        return 0;
}
```


#### MYSQL_BIN_LOG::report_cache_write_error
> `my_error(ER_TRANS_CACHE_FULL, MYF(MY_WME));`

```c++
void MYSQL_BIN_LOG::report_cache_write_error(THD *thd, bool is_transactional) {
        DBUG_TRACE;

        write_error = true;

        if (check_write_error(thd))
                return;

        if (my_errno() == EFBIG) {
                if (is_transactional) {
                        my_error(ER_TRANS_CACHE_FULL, MYF(MY_WME));
                } else {
                        my_error(ER_STMT_CACHE_FULL, MYF(MY_WME));
                }
        } else {
                char errbuf[MYSYS_STRERROR_SIZE];
                my_error(ER_ERROR_ON_WRITE, MYF(MY_WME), name, errno, my_strerror(errbuf, sizeof(errbuf), errno));
        }
}
```



#### my_error
>

```c++
void my_error(int nr, myf MyFlags, ...) {
        const char *format;
        char ebuff[ERRMSGSIZE];
        DBUG_TRACE;
        DBUG_PRINT("my", ("nr: %d  MyFlags: %d  errno: %d", nr, MyFlags, errno));

        if (!(format = my_get_err_msg(nr)))
                (void)snprintf(ebuff, sizeof(ebuff), "Unknown error %d", nr);
        else {
                va_list args;
                va_start(args, MyFlags);
                (void)vsnprintf(ebuff, sizeof(ebuff), format, args);
                va_end(args);
        }

        /*
          Since this function is an error function, it will frequently be given
          values that are too long (and thus truncated on byte boundaries,
          not code point or grapheme boundaries), values that are binary, etc..
          Go through and replace every malformed UTF-8 byte with a question mark,
          so that the result is safe to send to the client and makes sense to read
          for the user.
        */
        for (char *ptr = ebuff, *end = ebuff + strlen(ebuff); ptr != end;) {
                my_wc_t ignored;
                int len = my_mb_wc_utf8mb4(&ignored, pointer_cast<const uchar *>(ptr), pointer_cast<const uchar *>(end));
                if (len > 0) {
                        ptr += len;
                } else {
                        *ptr++ = '?';
                }
        }

        (*error_handler_hook)(nr, ebuff, MyFlags);
}
```

## my_block_write
8.0.32

```c++
int my_block_write(IO_CACHE *info, const uchar *Buffer, size_t Count, my_off_t pos) {
        size_t length;
        int error = 0;

        /*
          Assert that we cannot come here with a shared cache. If we do one
          day, we might need to add a call to copy_to_read_buffer().
        */
        assert(!info->share);

        if (pos < info->pos_in_file) {
                /* Of no overlap, write everything without buffering */
                if (pos + Count <= info->pos_in_file)
                        return (int)mysql_file_pwrite(info->file, Buffer, Count, pos, info->myflags | MY_NABP);
                /* Write the part of the block that is before buffer */
                length = (uint)(info->pos_in_file - pos);
                if (mysql_file_pwrite(info->file, Buffer, length, pos, info->myflags | MY_NABP))
                        info->error = error = -1;
                Buffer += length;
                pos += length;
                Count -= length;
#ifdef _WIN32
                info->seek_not_done = true;
#endif
        }

        /* Check if we want to write inside the used part of the buffer.*/
        length = (size_t)(info->write_end - info->buffer);
        if (pos < info->pos_in_file + length) {
                size_t offset = (size_t)(pos - info->pos_in_file);
                length -= offset;
                if (length > Count)
                        length = Count;
                memcpy(info->buffer + offset, Buffer, length);
                Buffer += length;
                Count -= length;
                /* Fix length of buffer if the new data was larger */
                if (info->buffer + length > info->write_pos)
                        info->write_pos = info->buffer + length;
                if (!Count)
                        return (error);
        }
        /* Write at the end of the current buffer; This is the normal case */
        if (_my_b_write(info, Buffer, Count))
                error = -1;
        return error;
}
```


## _my_b_write
8.0.32

```c++
int _my_b_write(IO_CACHE *info, const uchar *Buffer, size_t Count) {
        size_t rest_length, length;
        my_off_t pos_in_file = info->pos_in_file;

        DBUG_EXECUTE_IF("simulate_huge_load_data_file", { pos_in_file = (my_off_t)(5000000000ULL); });
        if (pos_in_file + info->buffer_length > info->end_of_file) {
                errno = EFBIG;
                set_my_errno(EFBIG);
                return info->error = -1;
        }

        rest_length = (size_t)(info->write_end - info->write_pos);
        memcpy(info->write_pos, Buffer, (size_t)rest_length);
        Buffer += rest_length;
        Count -= rest_length;
        info->write_pos += rest_length;

        if (my_b_flush_io_cache(info, 1))
                return 1;
        if (Count >= IO_SIZE) { /* Fill first intern buffer */
                length = Count & (size_t) ~(IO_SIZE - 1);
                if (info->seek_not_done) {
                        /*
                          Whenever a function which operates on IO_CACHE flushes/writes
                          some part of the IO_CACHE to disk it will set the property
                          "seek_not_done" to indicate this to other functions operating
                          on the IO_CACHE.
                        */
                        if (mysql_encryption_file_seek(info, info->pos_in_file, MY_SEEK_SET, MYF(0))) {
                                info->error = -1;
                                return (1);
                        }
                        info->seek_not_done = false;
                }

                /*
                  Verify that the correct number of bytes are written by
                  mysql_encryption_file_write(...) if both MY_NABP and
                  MY_FNABP are not set.
                */
                DBUG_EXECUTE_IF("verify_mysql_encryption_file_write_bytes",
                                size_t write_bytes = mysql_encryption_file_write(info, Buffer, length, info->myflags);
                                assert(write_bytes == length););

                if (DBUG_EVALUATE_IF("verify_mysql_encryption_file_write_bytes", false, true)) {
                        if (mysql_encryption_file_write(info, Buffer, length, info->myflags | MY_NABP))
                                return info->error = -1;
                }

                /*
                  In case of a shared I/O cache with a writer we normally do direct
                  write cache to read cache copy. Simulate this here by direct
                  caller buffer to read cache copy. Do it after the write so that
                  the cache readers actions on the flushed part can go in parallel
                  with the write of the extra stuff. copy_to_read_buffer()
                  synchronizes writer and readers so that after this call the
                  readers can act on the extra stuff while the writer can go ahead
                  and prepare the next output. copy_to_read_buffer() relies on
                  info->pos_in_file.
                */
                if (info->share)
                        copy_to_read_buffer(info, Buffer, length);

                Count -= length;
                Buffer += length;
                info->pos_in_file += length;
        }
        memcpy(info->write_pos, Buffer, (size_t)Count);
        info->write_pos += Count;
        return 0;
}
```




## IO_CACHE
8.0.32
- [IO_CACHE](https://www.cnblogs.com/jkin/p/16215620.html)
- [IO_CACHE](http://mysql.taobao.org/monthly/2018/09/03/)
- 常用的 general log, slow log, err log, binlog 主要使用 `READ_CACHE, WRITE_CACHE, SEQ_READ_APPEND` 几种类型
- SEQ_READ_APPEND 这种类型在 MySQL 复制模块使用，IO 线程负责 append 数据到 relay log，SQL 线程负责 read 出来应用

```c++
// READ_CACHE      是读缓冲
// WRITE_CACHE     是写缓冲
// SEQ_READ_APPEND 同时支持读写,写线程不断 append 数据到文件尾，读线程去 read 数据; MySQL 复制模块使用
enum cache_type {
        TYPE_NOT_SET = 0,
        READ_CACHE,
        WRITE_CACHE,
        SEQ_READ_APPEND /* sequential read or append */,
        READ_FIFO,
        READ_NET,
        WRITE_NET
};

struct IO_CACHE /* Used when caching files */
{
        /* Offset in file corresponding to the first byte of uchar* buffer. */
        my_off_t pos_in_file{0};
        /*
          The offset of end of file for READ_CACHE and WRITE_CACHE.
          For SEQ_READ_APPEND it the maximum of the actual end of file and
          the position represented by read_end.
        */
        my_off_t end_of_file{0};
        /* Points to current read position in the buffer */
        uchar *read_pos{nullptr};
        /* the non-inclusive boundary in the buffer for the currently valid read */
        uchar *read_end{nullptr};
        // 读缓存
        uchar *buffer{nullptr}; /* The read buffer */
        /* Used in ASYNC_IO */
        uchar *request_pos{nullptr};

        // 写缓存
        uchar *write_buffer{nullptr};
        /*
          Only used in SEQ_READ_APPEND, and points to the current read position
          in the write buffer. Note that reads in SEQ_READ_APPEND caches can
          happen from both read buffer (uchar* buffer) and write buffer
          (uchar* write_buffer).
        */
        uchar *append_read_pos{nullptr};
        /* Points to current write position in the write buffer */
        uchar *write_pos{nullptr};
        /* The non-inclusive boundary of the valid write area */
        uchar *write_end{nullptr};

        /*
          Current_pos and current_end are convenience variables used by
          my_b_tell() and other routines that need to know the current offset
          current_pos points to &write_pos, and current_end to &write_end in a
          WRITE_CACHE, and &read_pos and &read_end respectively otherwise
        */
        uchar **current_pos{nullptr}, **current_end{nullptr};

        /*
          The lock is for append buffer used in SEQ_READ_APPEND cache
          need mutex copying from append buffer to read buffer.
        */
        mysql_mutex_t append_buffer_lock;
        /*
          The following is used when several threads are reading the
          same file in parallel. They are synchronized on disk
          accesses reading the cached part of the file asynchronously.
          It should be set to NULL to disable the feature.  Only
          READ_CACHE mode is supported.
        */
        IO_CACHE_SHARE *share{nullptr};

        /*
          A caller will use my_b_read() macro to read from the cache
          if the data is already in cache, it will be simply copied with
          memcpy() and internal variables will be accordinging updated with
          no functions invoked. However, if the data is not fully in the cache,
          my_b_read() will call read_function to fetch the data. read_function
          must never be invoked directly.
        */
        int (*read_function)(IO_CACHE *, uchar *, size_t){nullptr};
        /*
          Same idea as in the case of read_function, except my_b_write() needs to
          be replaced with my_b_append() for a SEQ_READ_APPEND cache
        */
        int (*write_function)(IO_CACHE *, const uchar *, size_t){nullptr};

        // IO_CACHE类型
        cache_type type{TYPE_NOT_SET};
        /*
          Callbacks when the actual read I/O happens. These were added and
          are currently used for binary logging of LOAD DATA INFILE - when a
          block is read from the file, we create a block create/append event, and
          when IO_CACHE is closed, we create an end event. These functions could,
          of course be used for other things
        */
        IO_CACHE_CALLBACK pre_read{nullptr};
        IO_CACHE_CALLBACK post_read{nullptr};
        IO_CACHE_CALLBACK pre_close{nullptr};
        /*
          Counts the number of times, when we were forced to use disk. We use it to
          increase the binlog_cache_disk_use and binlog_stmt_cache_disk_use status
          variables.
        */
        ulong disk_writes{0};
        void *arg{nullptr};       /* for use by pre/post_read */
        char *file_name{nullptr}; /* if used with 'open_cached_file' */
        char *dir{nullptr}, *prefix{nullptr};
        // 物理文件描述符
        File file{-1};
        PSI_file_key file_key{PSI_NOT_INSTRUMENTED}; /* instrumented file key */

        /*
          seek_not_done is set by my_b_seek() to inform the upcoming read/write
          operation that a seek needs to be performed prior to the actual I/O
          error is 0 if the cache operation was successful, -1 if there was a
          "hard" error, and the actual number of I/O-ed bytes if the read/write was
          partial.
        */
        bool seek_not_done{false};
        int error{0};
        /* buffer_length is memory size allocated for buffer or write_buffer */
        size_t buffer_length{0};
        /* read_length is the same as buffer_length except when we use async io */
        size_t read_length{0};
        myf myflags{0}; /* Flags used to my_read/my_write */
        /*
          alloced_buffer is 1 if the buffer was allocated by init_io_cache() and
          0 if it was supplied by the user.
          Currently READ_NET is the only one that will use a buffer allocated
          somewhere else
        */
        bool alloced_buffer{false};
        // This is an encryptor for encrypting the temporary file of the IO cache.
        Stream_cipher *m_encryptor = nullptr;
        // This is a decryptor for decrypting the temporary file of the IO cache.
        Stream_cipher *m_decryptor = nullptr;
        // Synchronize flushed buffer with disk.
        bool disk_sync{false};
        // Delay in milliseconds after disk synchronization of the flushed buffer.
        // Requires disk_sync = true.
        uint disk_sync_delay{0};
};

// 真正初始化一个IO_CACHE对象
// 传入的参数 cache_size：分配缓冲空间大小，一般传入的空间都不算大，例如 Binlog 的 IO_CACHE 初始化传入的大小就是 IO_SIZE（4KB）
// 因为文件系统本身是有 page cache 的，只有调用 fsync 操作才会保证数据落盘，所以 IO_CACHE 就没必要缓冲太多的数据，只做把数据对齐写入的活
// 但并不是传进来多大空间就分配多大空间
int init_io_cache_ext(IO_CACHE *info,
                      File file,
                      size_t cachesize,
                      enum cache_type type,
                      my_off_t seek_offset,
                      bool use_async_io,
                      myf cache_myflags,
                      PSI_file_key file_key) {
        size_t min_cache;
        my_off_t pos;
        my_off_t end_of_file = ~(my_off_t)0;
        DBUG_TRACE;
        DBUG_PRINT("enter", ("cache: %p  type: %d  pos: %ld", info, (int)type, (ulong)seek_offset));

        DBUG_EXECUTE_IF("simulate_init_io_cache_failure", return 1;);
        // 初始化 IO_CACHE 中各种变量
        info->file        = file;
        info->file_key    = file_key;
        info->type        = TYPE_NOT_SET; /* Don't set it until mutex are created */
        info->pos_in_file = seek_offset;
        info->pre_close = info->pre_read = info->post_read = nullptr;
        info->arg                                          = nullptr;
        info->alloced_buffer                               = false;
        info->buffer                                       = nullptr;
        info->seek_not_done                                = false;

        if (file >= 0) {
                pos = mysql_file_tell(file, MYF(0));
                if ((pos == (my_off_t)-1) && (my_errno() == ESPIPE)) {
                        /*
                           This kind of object doesn't support seek() or tell(). Don't set a
                           flag that will make us again try to seek() later and fail.
                        */
                        info->seek_not_done = false;
                        /*
                          Additionally, if we're supposed to start somewhere other than the
                          the beginning of whatever this file is, then somebody made a bad
                          assumption.
                        */
                        assert(seek_offset == 0);
                } else
                        info->seek_not_done = (seek_offset != pos);
        }

        info->disk_writes = 0;
        info->share       = nullptr;

        if (!cachesize && !(cachesize = my_default_record_cache_size))
                return 1; /* No cache requested */
        min_cache = use_async_io ? IO_SIZE * 4 : IO_SIZE * 2;
        if (type == READ_CACHE || type == SEQ_READ_APPEND) { /* Assume file isn't growing */
                if (!(cache_myflags & MY_DONT_CHECK_FILESIZE)) {
                        /* Calculate end of file to avoid allocating oversized buffers */
                        end_of_file = mysql_encryption_file_seek(info, 0L, MY_SEEK_END, MYF(0));
                        /* Need to reset seek_not_done now that we just did a seek. */
                        info->seek_not_done = !(end_of_file == seek_offset);
                        if (end_of_file < seek_offset)
                                end_of_file = seek_offset;
                        /* Trim cache size if the file is very small */
                        if ((my_off_t)cachesize > end_of_file - seek_offset + IO_SIZE * 2 - 1) {
                                cachesize    = (size_t)(end_of_file - seek_offset) + IO_SIZE * 2 - 1;
                                use_async_io = false; /* No need to use async */
                        }
                }
        }
        cache_myflags &= ~MY_DONT_CHECK_FILESIZE;
        if (type != READ_NET && type != WRITE_NET) {
                /* Retry allocating memory in smaller blocks until we get one */
                cachesize = ((cachesize + min_cache - 1) & ~(min_cache - 1));
                for (;;) {
                        size_t buffer_block;
                        /*
                          Unset MY_WAIT_IF_FULL bit if it is set, to prevent conflict with
                          MY_ZEROFILL.
                        */
                        myf flags = (myf)(cache_myflags & ~(MY_WME | MY_WAIT_IF_FULL));

                        if (cachesize < min_cache)
                                cachesize = min_cache;
                        buffer_block = cachesize;
                        if (type == SEQ_READ_APPEND)
                                buffer_block *= 2;
                        if (cachesize == min_cache)
                                flags |= (myf)MY_WME;
                        // 分配 write_buffer 和 read_buffer 的空间
                        if ((info->buffer = (uchar *)my_malloc(key_memory_IO_CACHE, buffer_block, flags)) != nullptr) {
                                info->write_buffer = info->buffer;
                                if (type == SEQ_READ_APPEND)
                                        info->write_buffer = info->buffer + cachesize;
                                info->alloced_buffer = true;
                                break; /* Enough memory found */
                        }
                        if (cachesize == min_cache)
                                return 2; /* Can't alloc cache */
                        /* Try with less memory */
                        cachesize = (cachesize * 3 / 4 & ~(min_cache - 1));
                }
        }

        DBUG_PRINT("info", ("init_io_cache: cachesize = %lu", (ulong)cachesize));
        info->read_length = info->buffer_length = cachesize;
        info->myflags                           = cache_myflags & ~(MY_NABP | MY_FNABP);
        info->request_pos = info->read_pos = info->write_pos = info->buffer;
        if (type == SEQ_READ_APPEND) {
                info->append_read_pos = info->write_pos = info->write_buffer;
                info->write_end                         = info->write_buffer + info->buffer_length;
                // 初始化互斥变量 append_buffer_lock. (对于 SEQ_READ_APPEND 类型而言)
                mysql_mutex_init(key_IO_CACHE_append_buffer_lock, &info->append_buffer_lock, MY_MUTEX_INIT_FAST);
        }
#if defined(SAFE_MUTEX)
        else {
                /* Clear mutex so that safe_mutex will notice that it's not initialized */
                new (&info->append_buffer_lock) mysql_mutex_t();
        }
#endif

        if (type == WRITE_CACHE)
                info->write_end = info->buffer + info->buffer_length - (seek_offset & (IO_SIZE - 1));
        else
                info->read_end = info->buffer; /* Nothing in cache */

        /* End_of_file may be changed by user later */
        info->end_of_file = end_of_file;
        info->error       = 0;
        info->type        = type;
        // init_functions 初始化对应的文件读写函数
        // init_functions 是根据 IO_CACHE 的类型初始化 IO_CACHE::read_function 和 IO_CACHE::write_function，当缓冲大小没法满足文件 IO 请求的时候就会调用这两个函数去文件中交换数据。
        init_functions(info);
        return 0;
}


// 初始化一个IO_CACHE对象的封装
int init_io_cache(IO_CACHE *info,
                  File file,
                  size_t cachesize,
                  enum cache_type type,
                  my_off_t seek_offset,
                  bool use_async_io,
                  myf cache_myflags) {
        return init_io_cache_ext(info,
                                 file,
                                 cachesize,
                                 type,
                                 seek_offset,
                                 use_async_io,
                                 cache_myflags,
                                 info->file_key != PSI_NOT_INSTRUMENTED ? info->file_key : key_file_io_cache);
}

// init_functions 是根据IO_CACHE的类型初始化 IO_CACHE::read_function 和 IO_CACHE::write_function
// 当缓冲大小没法满足文件 IO 请求的时候就会调用这两个函数去文件中交换数据
static void init_functions(IO_CACHE *info) {
        enum cache_type type = info->type;
        switch (type) {
                case READ_NET:
                        /*
                          Must be initialized by the caller. The problem is that
                          _my_b_net_read has to be defined in sql directory because of
                          the dependency on THD, and therefore cannot be visible to
                          programs that link against mysys but know nothing about THD, such
                          as myisamchk
                        */
                        break;
                case SEQ_READ_APPEND:
                        info->read_function  = _my_b_seq_read;
                        info->write_function = nullptr; /* Force a core if used */
                        break;
                default:
                        info->read_function  = info->share ? _my_b_read_r : _my_b_read;
                        info->write_function = _my_b_write;
        }

        setup_io_cache(info);
}

// SEQ_READ_APPEND
int _my_b_seq_read(IO_CACHE *info, uchar *Buffer, size_t Count) {
        size_t length, diff_length, left_length, save_count, max_length;
        my_off_t pos_in_file;
        save_count = Count;

        /* first, read the regular buffer */
        if ((left_length = (size_t)(info->read_end - info->read_pos))) {
                assert(Count > left_length); /* User is not using my_b_read() */
                memcpy(Buffer, info->read_pos, left_length);
                Buffer += left_length;
                Count -= left_length;
        }
        lock_append_buffer(info);

        /* pos_in_file always point on where info->buffer was read */
        if ((pos_in_file = info->pos_in_file + (size_t)(info->read_end - info->buffer)) >= info->end_of_file)
                goto read_append_buffer;

        /*
          With read-append cache we must always do a seek before we read,
          because the write could have moved the file pointer astray
        */
        if (mysql_encryption_file_seek(info, pos_in_file, MY_SEEK_SET, MYF(0)) == MY_FILEPOS_ERROR) {
                info->error = -1;
                unlock_append_buffer(info);
                return (1);
        }
        info->seek_not_done = false;

        diff_length = (size_t)(pos_in_file & (IO_SIZE - 1));

        /* now the second stage begins - read from file descriptor */
        if (Count >= (size_t)(IO_SIZE + (IO_SIZE - diff_length))) {
                /* Fill first intern buffer */
                size_t read_length;

                length = (Count & (size_t) ~(IO_SIZE - 1)) - diff_length;
                if ((read_length = mysql_encryption_file_read(info, Buffer, length, info->myflags)) == (size_t)-1) {
                        info->error = -1;
                        unlock_append_buffer(info);
                        return 1;
                }
                Count -= read_length;
                Buffer += read_length;
                pos_in_file += read_length;

                if (read_length != length) {
                        /*
                          We only got part of data;  Read the rest of the data from the
                          write buffer
                        */
                        goto read_append_buffer;
                }
                left_length += length;
                diff_length = 0;
        }

        max_length = info->read_length - diff_length;
        if (max_length > (info->end_of_file - pos_in_file))
                max_length = (size_t)(info->end_of_file - pos_in_file);
        if (!max_length) {
                if (Count)
                        goto read_append_buffer;
                length = 0; /* Didn't read any more chars */
        } else {
                length = mysql_encryption_file_read(info, info->buffer, max_length, info->myflags);
                if (length == (size_t)-1) {
                        info->error = -1;
                        unlock_append_buffer(info);
                        return 1;
                }
                if (length < Count) {
                        memcpy(Buffer, info->buffer, length);
                        Count -= length;
                        Buffer += length;

                        /*
                           added the line below to make
                           assert(pos_in_file==info->end_of_file) pass.
                           otherwise this does not appear to be needed
                        */
                        pos_in_file += length;
                        goto read_append_buffer;
                }
        }
        unlock_append_buffer(info);
        info->read_pos    = info->buffer + Count;
        info->read_end    = info->buffer + length;
        info->pos_in_file = pos_in_file;
        memcpy(Buffer, info->buffer, (size_t)Count);
        return 0;

read_append_buffer:

        /*
           Read data from the current write buffer.
           Count should never be == 0 here (The code will work even if count is 0)
        */

        {
                /* First copy the data to Count */
                size_t len_in_buff = (size_t)(info->write_pos - info->append_read_pos);
                size_t copy_len;
                size_t transfer_len;

                assert(info->append_read_pos <= info->write_pos);
                /*
                  TODO: figure out if the assert below is needed or correct.
                */
                assert(pos_in_file == info->end_of_file);
                copy_len = std::min(Count, len_in_buff);
                memcpy(Buffer, info->append_read_pos, copy_len);
                info->append_read_pos += copy_len;
                Count -= copy_len;
                if (Count)
                        info->error = (int)(save_count - Count);

                /* Fill read buffer with data from write buffer */
                memcpy(info->buffer, info->append_read_pos, (size_t)(transfer_len = len_in_buff - copy_len));
                info->read_pos        = info->buffer;
                info->read_end        = info->buffer + transfer_len;
                info->append_read_pos = info->write_pos;
                info->pos_in_file     = pos_in_file + copy_len;
                info->end_of_file += len_in_buff;
        }
        unlock_append_buffer(info);
        return Count ? 1 : 0;
}
```















