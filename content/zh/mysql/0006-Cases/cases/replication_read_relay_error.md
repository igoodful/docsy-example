---
title: 10596
description: ER_RPL_SLAVE_ERROR_READING_RELAY_LOG_EVENTS
date: 2023-10-12
weight: 60000


---

{{< alert >}}
最近一段时间线上某实例频繁报警CPU飙高，每次都捕获到同一种 SQL
{{< /alert >}}


### 一、环境信息

{{< note >}}

| 名称          | 配置       |
|:--------------|:-----------|
| Arch          | x64        |
| OS            | CentOS 8.5 |
| CPU           | 8C         |
| Memory        | 16G        |
| Space         | 200G       |
| MySQL Version | 8.0.26     |

{{< /note >}}



```bash
dnf -y install sysstat
```





## 错误码位置
错误码位置：`/usr/include/mysql/mysqld_error.h`
```c++
#define ER_RPL_SLAVE_ERROR_READING_RELAY_LOG_EVENTS 10596
```




## messages_to_error_log.txt

`/root/mysql-server-8032-google-02/share/messages_to_error_log.txt`
```
ER_RPL_SLAVE_ERROR_READING_RELAY_LOG_EVENTS
  eng "Error reading relay log event%s: %s"
```




## read_next_event

报错位置：`/root/mysql-server-8032-google-02/sql/rpl_applier_reader.cc`

```c++
enum loglevel {
        SYSTEM_LEVEL      = 0,
        ERROR_LEVEL       = 1,
        WARNING_LEVEL     = 2,
        INFORMATION_LEVEL = 3
};


Log_event *Rpl_applier_reader::read_next_event() {
        DBUG_TRACE;
        Log_event *ev = nullptr;

        /*
          data_lock is needed when accessing members of Relay_log_info, and this
          function temporarily releases it while waiting.
        */
        mysql_mutex_assert_owner(&m_rli->data_lock);

        DBUG_EXECUTE_IF("block_applier_updates", {
                const char act[] = "now SIGNAL applier_read_blocked WAIT_FOR resume_applier_read";
                assert(!debug_sync_set_action(current_thd, STRING_WITH_LEN(act)));
        });
        DBUG_EXECUTE_IF("force_sql_thread_error", return nullptr;);

        if (m_reading_active_log && m_relaylog_file_reader.position() >= m_log_end_pos) {
                while (true) {
                        if (sql_slave_killed(m_rli->info_thd, m_rli))
                                return nullptr;

#ifndef NDEBUG
                        debug_print_next_event_positions();
#endif

                        if (read_active_log_end_pos())
                                break;

                        /*
                          At this point the coordinator has no job to delegate to workers.
                          However, workers are executing their assigned jobs and as such
                          the checkpoint routine must be periodically invoked.

                          mta_checkpoint_routine has to be called before enter_stage().
                          Otherwise, it will cause a deadlock with STOP SLAVE or other
                          thread has the same lock pattern.
                          STOP SLAVE Thread                   Coordinator Thread
                          =================                   ==================
                          lock LOCK_thd_data                  lock LOCK_binlog_end_pos
                                                              enter_stage(LOCK_binlog_end_pos)
                          lock LOCK_binlog_end_pos
                          in THD::awake
                                                              lock LOCK_thd_data in
                                                              mta_checkpoint_routine()
                                                                flush_info()
                                                                  ...
                                                                  close_thread_table()
                        */
                        mysql_mutex_unlock(&m_rli->data_lock);
                        if ((m_rli->is_time_for_mta_checkpoint() || DBUG_EVALUATE_IF("check_replica_debug_group", 1, 0)) &&
                            mta_checkpoint_routine(m_rli, false)) {
                                m_errmsg = "Failed to compute mts checkpoint";
                                mysql_mutex_lock(&m_rli->data_lock);
                                return nullptr;
                        }
                        mysql_mutex_lock(&m_rli->data_lock);

                        /* Lock LOCK_binlog_end_pos before wait */
                        Stage_controller stage_controller(m_rli->info_thd,
                                                          m_rli->relay_log.get_binlog_end_pos_lock(),
                                                          m_rli->relay_log.get_log_cond(),
                                                          stage_replica_has_read_all_relay_log,
                                                          Stage_controller::LOCKED);

                        /* Check it again to avoid missing update signals from receiver thread */
                        if (read_active_log_end_pos())
                                break;

                        reset_seconds_behind_master();
                        /* It should be protected by relay_log.LOCK_binlog_end_pos */
                        if (m_rli->ign_master_log_name_end[0])
                                return generate_rotate_event();

                        stage_controller.enter_stage();
                        if (sql_slave_killed(m_rli->info_thd, m_rli))
                                return nullptr;

                        if (wait_for_new_event())
                                return nullptr;
                }
        }

        m_rli->set_event_start_pos(m_relaylog_file_reader.position());
        ev = m_relaylog_file_reader.read_event_object();
        if (ev != nullptr) {
                m_rli->set_future_event_relay_log_pos(m_relaylog_file_reader.position());
                ev->future_event_relay_log_pos = m_rli->get_future_event_relay_log_pos();
                return ev;
        }

        if (m_relaylog_file_reader.get_error_type() == Binlog_read_error::READ_EOF && !m_reading_active_log) {
                if (!move_to_next_log())
                        return read_next_event();
        }

        LogErr(ERROR_LEVEL,
               ER_RPL_SLAVE_ERROR_READING_RELAY_LOG_EVENTS,
               m_rli->get_for_channel_str(),
               m_errmsg ? m_errmsg : m_relaylog_file_reader.get_error_str());
        return nullptr;
}
```


## exec_relay_log_event
```c++
static int exec_relay_log_event(THD *thd, Relay_log_info *rli, Rpl_applier_reader *applier_reader, Log_event *in) {
        DBUG_TRACE;

        /*
           We acquire this mutex since we need it for all operations except
           event execution. But we will release it in places where we will
           wait for something for example inside of next_event().
         */
        mysql_mutex_lock(&rli->data_lock);

        Log_event *ev = nullptr;
#ifndef NDEBUG
        if (!abort_slave_event_count || rli->events_until_exit--)
#endif
                ev = in;

        Log_event **ptr_ev = nullptr;
        RLI_current_event_raii rli_c_ev(rli, ev);

        if (ev != nullptr) {
                /*
                  To avoid assigned event groups exceeding rli->checkpoint_group, it
                  need force to compute checkpoint.
                */
                bool force = rli->rli_checkpoint_seqno >= rli->checkpoint_group;
                if (force || rli->is_time_for_mta_checkpoint()) {
                        mysql_mutex_unlock(&rli->data_lock);
                        if (mta_checkpoint_routine(rli, force)) {
                                delete ev;
                                return 1;
                        }
                        mysql_mutex_lock(&rli->data_lock);
                }
        }

        /*
          It should be checked after calling mta_checkpoint_routine(), because that
          function could be interrupted by kill while 'force' is true.
        */
        if (sql_slave_killed(thd, rli)) {
                mysql_mutex_unlock(&rli->data_lock);
                delete ev;

                LogErr(INFORMATION_LEVEL,
                       ER_RPL_SLAVE_ERROR_READING_RELAY_LOG_EVENTS,
                       rli->get_for_channel_str(),
                       "slave SQL thread was killed");
                return 1;
        }

        if (ev) {
                enum enum_slave_apply_event_and_update_pos_retval exec_res;

                ptr_ev = &ev;
                /*
                  Even if we don't execute this event, we keep the master timestamp,
                  so that seconds behind master shows correct delta (there are events
                  that are not replayed, so we keep falling behind).

                  If it is an artificial event, or a relay log event (IO thread generated
                  event) or ev->when is set to 0, or a FD from master, or a heartbeat
                  event with server_id '0' then  we don't update the last_master_timestamp.

                  In case of parallel execution last_master_timestamp is only updated when
                  a job is taken out of GAQ. Thus when last_master_timestamp is 0 (which
                  indicates that GAQ is empty, all slave workers are waiting for events from
                  the Coordinator), we need to initialize it with a timestamp from the first
                  event to be executed in parallel.
                */
                if ((!rli->is_parallel_exec() || rli->last_master_timestamp == 0) &&
                    !(ev->is_artificial_event() || ev->is_relay_log_event() ||
                      ev->get_type_code() == binary_log::FORMAT_DESCRIPTION_EVENT || ev->server_id == 0)) {
                        rli->last_master_timestamp = ev->common_header->when.tv_sec + (time_t)ev->exec_time;
                        assert(rli->last_master_timestamp >= 0);
                }

                if (rli->is_until_satisfied_before_dispatching_event(ev)) {
                        /*
                          Setting abort_slave flag because we do not want additional message about
                          error in query execution to be printed.
                        */
                        rli->abort_slave = true;
                        mysql_mutex_unlock(&rli->data_lock);
                        delete ev;
                        return SLAVE_APPLY_EVENT_UNTIL_REACHED;
                }

                {
                        /**
                          The following failure injecion works in cooperation
                          with tests setting @@global.debug= 'd,incomplete_group_in_relay_log'.
                          Xid or Commit events are not executed to force the slave sql read
                          hanging if the relay log does not have any more events.
                         */
                        DBUG_EXECUTE_IF(
                            "incomplete_group_in_relay_log",
                            if ((ev->get_type_code() == binary_log::XID_EVENT) ||
                                ((ev->get_type_code() == binary_log::QUERY_EVENT) &&
                                 strcmp("COMMIT", ((Query_log_event *)ev)->query) == 0)) {
                                    rli->abort_slave = 1;
                                    mysql_mutex_unlock(&rli->data_lock);
                                    delete ev;
                                    rli->inc_event_relay_log_pos();
                                    return 0;
                            };);
                }

                /*
                  GTID protocol will put a FORMAT_DESCRIPTION_EVENT from the master with
                  log_pos != 0 after each (re)connection if auto positioning is enabled.
                  This means that the SQL thread might have already started to apply the
                  current group but, as the IO thread had to reconnect, it left this
                  group incomplete and will start it again from the beginning.
                  So, before applying this FORMAT_DESCRIPTION_EVENT, we must let the
                  worker roll back the current group and gracefully finish its work,
                  before starting to apply the new (complete) copy of the group.
                */
                if (ev->get_type_code() == binary_log::FORMAT_DESCRIPTION_EVENT && ev->server_id != ::server_id &&
                    ev->common_header->log_pos != 0 && rli->is_parallel_exec() && rli->curr_group_seen_gtid) {
                        if (coord_handle_partial_binlogged_transaction(rli, ev))
                                /*
                                  In the case of an error, coord_handle_partial_binlogged_transaction
                                  will not try to get the rli->data_lock again.
                                */
                                return 1;
                }

                /* ptr_ev can change to NULL indicating MTS coorinator passed to a Worker */
                exec_res = apply_event_and_update_pos(ptr_ev, thd, rli);
                /*
                  Note: the above call to apply_event_and_update_pos executes
                  mysql_mutex_unlock(&rli->data_lock);
                */

                /* For deferred events, the ptr_ev is set to NULL
                    in Deferred_log_events::add() function.
                    Hence deferred events won't be deleted here.
                    They will be deleted in Deferred_log_events::rewind() function.
                */
                if (*ptr_ev) {
                        assert(*ptr_ev == ev);  // event remains to belong to Coordinator

                        DBUG_EXECUTE_IF("dbug.calculate_sbm_after_previous_gtid_log_event", {
                                if (ev->get_type_code() == binary_log::PREVIOUS_GTIDS_LOG_EVENT) {
                                        rpl_replica_debug_point(DBUG_RPL_S_SBM_AFTER_PREVIOUS_GTID_EV, thd);
                                }
                        };);
                        DBUG_EXECUTE_IF("dbug.calculate_sbm_after_fake_rotate_log_event", {
                                if (ev->get_type_code() == binary_log::ROTATE_EVENT && ev->is_artificial_event()) {
                                        rpl_replica_debug_point(DBUG_RPL_S_SBM_AFTER_FAKE_ROTATE_EV, thd);
                                }
                        };);
                        /*
                          Format_description_log_event should not be deleted because it will be
                          used to read info about the relay log's format; it will be deleted when
                          the SQL thread does not need it, i.e. when this thread terminates.
                          ROWS_QUERY_LOG_EVENT is destroyed at the end of the current statement
                          clean-up routine.
                        */
                        if (ev->get_type_code() != binary_log::FORMAT_DESCRIPTION_EVENT &&
                            ev->get_type_code() != binary_log::ROWS_QUERY_LOG_EVENT) {
                                DBUG_PRINT("info", ("Deleting the event after it has been executed"));
                                delete ev;
                                /*
                                  Raii guard is explicitly instructed to invalidate
                                  otherwise bogus association of the execution context with the being
                                  destroyed above event.
                                */
                                ev = rli->current_event = nullptr;
                        }
                }

                /*
                  exec_res == SLAVE_APPLY_EVENT_AND_UPDATE_POS_UPDATE_POS_ERROR
                              update_log_pos failed: this should not happen, so we
                              don't retry.
                  exec_res == SLAVE_APPLY_EVENT_AND_UPDATE_POS_APPEND_JOB_ERROR
                              append_item_to_jobs() failed, this happened because
                              thread was killed while waiting for enqueue on worker.
                */
                if (exec_res >= SLAVE_APPLY_EVENT_AND_UPDATE_POS_UPDATE_POS_ERROR) {
                        delete ev;
                        return 1;
                }

                if (slave_trans_retries) {
                        int temp_err = 0;
                        bool silent  = false;
                        if (exec_res && !is_mts_worker(thd) /* no reexecution in MTS mode */ &&
                            (temp_err = rli->has_temporary_error(thd, 0, &silent)) &&
                            !thd->get_transaction()->cannot_safely_rollback(Transaction_ctx::SESSION)) {
                                const char *errmsg;
                                /*
                                  We were in a transaction which has been rolled back because of a
                                  temporary error;
                                  let's seek back to BEGIN log event and retry it all again.
                                  Note, if lock wait timeout (innodb_lock_wait_timeout exceeded)
                                  there is no rollback since 5.0.13 (ref: manual).
                                  We have to not only seek but also
                                  a) init_info(), to seek back to hot relay log's start for later
                                  (for when we will come back to this hot log after re-processing the
                                  possibly existing old logs where BEGIN is: applier_reader will
                                  then need the cache to be at position 0 (see comments at beginning of
                                  init_info()).
                                  b) init_relay_log_pos(), because the BEGIN may be an older relay log.
                                */
                                if (rli->trans_retries < slave_trans_retries) {
                                        /*
                                          The transactions has to be rolled back before
                                          load_mi_and_rli_from_repositories is called. Because
                                          load_mi_and_rli_from_repositories will starts a new
                                          transaction if master_info_repository is TABLE.
                                        */
                                        rli->cleanup_context(thd, true);
                                        /*
                                          Temporary error status is both unneeded and harmful for following
                                          open-and-lock slave system tables but store its number first for
                                          monitoring purposes.
                                        */
                                        uint temp_trans_errno = thd->get_stmt_da()->mysql_errno();
                                        thd->clear_error();
                                        applier_reader->close();
                                        /*
                                           We need to figure out if there is a test case that covers
                                           this part. \Alfranio.
                                        */
                                        if (load_mi_and_rli_from_repositories(rli->mi, false, SLAVE_SQL, false, true))
                                                LogErr(ERROR_LEVEL,
                                                       ER_RPL_SLAVE_FAILED_TO_INIT_MASTER_INFO_STRUCTURE,
                                                       rli->get_for_channel_str());
                                        else if (applier_reader->open(&errmsg))
                                                LogErr(ERROR_LEVEL,
                                                       ER_RPL_SLAVE_CANT_INIT_RELAY_LOG_POSITION,
                                                       rli->get_for_channel_str(),
                                                       errmsg);
                                        else {
                                                exec_res = SLAVE_APPLY_EVENT_RETRY;
                                                /* chance for concurrent connection to get more locks */
                                                slave_sleep(thd,
                                                            min<ulong>(rli->trans_retries, MAX_SLAVE_RETRY_PAUSE),
                                                            sql_slave_killed,
                                                            rli);
                                                mysql_mutex_lock(&rli->data_lock);  // because of SHOW STATUS
                                                if (!silent) {
                                                        rli->trans_retries++;
                                                        if (rli->is_processing_trx()) {
                                                                rli->retried_processing(temp_trans_errno,
                                                                                        ER_THD_NONCONST(thd, temp_trans_errno),
                                                                                        rli->trans_retries);
                                                        }
                                                }
                                                rli->retried_trans++;
                                                mysql_mutex_unlock(&rli->data_lock);
#ifndef NDEBUG
                                                if (rli->trans_retries == 2 || rli->trans_retries == 6)
                                                        DBUG_EXECUTE_IF("rpl_ps_tables_worker_retry", {
                                                                rpl_replica_debug_point(DBUG_RPL_S_PS_TABLE_WORKER_RETRY);
                                                        };);

#endif
                                                DBUG_PRINT("info",
                                                           ("Slave retries transaction "
                                                            "rli->trans_retries: %lu",
                                                            rli->trans_retries));
                                        }
                                } else {
                                        thd->fatal_error();
                                        rli->report(ERROR_LEVEL,
                                                    thd->get_stmt_da()->mysql_errno(),
                                                    "Slave SQL thread retried transaction %lu time(s) "
                                                    "in vain, giving up. Consider raising the value of "
                                                    "the replica_transaction_retries variable.",
                                                    rli->trans_retries);
                                }
                        } else if ((exec_res && !temp_err) || (opt_using_transactions && rli->get_group_relay_log_pos() ==
                                                                                             rli->get_event_relay_log_pos())) {
                                /*
                                  Only reset the retry counter if the entire group succeeded
                                  or failed with a non-transient error.  On a successful
                                  event, the execution will proceed as usual; in the case of a
                                  non-transient error, the slave will stop with an error.
                                 */
                                rli->trans_retries = 0;  // restart from fresh
                                DBUG_PRINT("info", ("Resetting retry counter, rli->trans_retries: %lu", rli->trans_retries));
                        }
                }
                if (exec_res) {
                        delete ev;
                        /* Raii object is explicitly updated 'cos this branch doesn't end func */
                        rli->current_event = nullptr;
                } else if (rli->is_until_satisfied_after_dispatching_event()) {
                        mysql_mutex_lock(&rli->data_lock);
                        rli->abort_slave = true;
                        mysql_mutex_unlock(&rli->data_lock);
                        return SLAVE_APPLY_EVENT_UNTIL_REACHED;
                }
                return exec_res;
        }

        /*
          It is impossible to read next event to finish the event group whenever a
          read event error happens. So MTS group status is set to MTS_KILLED_GROUP to
          force stop.
        */
        if (rli->mts_group_status == Relay_log_info::MTS_IN_GROUP)
                rli->mts_group_status = Relay_log_info::MTS_KILLED_GROUP;

        mysql_mutex_unlock(&rli->data_lock);
        rli->report(ERROR_LEVEL,
                    ER_SLAVE_RELAY_LOG_READ_FAILURE,
                    ER_THD(thd, ER_SLAVE_RELAY_LOG_READ_FAILURE),
                    "\
Could not parse relay log event entry. The possible reasons are: the master's \
binary log is corrupted (you can check this by running 'mysqlbinlog' on the \
binary log), the slave's relay log is corrupted (you can check this by running \
'mysqlbinlog' on the relay log), a network problem, the server was unable to \
fetch a keyring key required to open an encrypted relay log file, or a bug in \
the master's or slave's MySQL code. If you want to check the master's binary \
log or slave's relay log, you will be able to know their names by issuing \
'SHOW SLAVE STATUS' on this slave.\
");
        return SLAVE_APPLY_EVENT_AND_UPDATE_POS_APPLY_ERROR;
}
```


在MySQL 8.0.32源码中，执行rli->report(ERROR_LEVEL, ER_SLAVE_RELAY_LOG_READ_FAILURE, ER_THD(thd, ER_SLAVE_RELAY_LOG_READ_FAILURE), ...)的情况是在无法解析中继日志事件条目时。这种情况可能由以下几个原因导致：

- 主服务器的二进制日志损坏：中继日志是从主服务器的二进制日志复制而来的。
- 如果主服务器的二进制日志损坏，那么中继日志中的事件可能无法正确解析。
- 中继日志损坏：在复制过程中，中继日志本身可能会损坏。这可能是由于磁盘故障、权限问题或其他I/O错误导致的。
- 网络问题：复制过程中的网络问题可能导致中继日志的传输中断或损坏。
- 密钥环问题：如果使用了加密的中继日志文件，并且服务器无法获取解密所需的密钥环密钥，则无法打开中继日志文件，导致解析失败。
MySQL代码中的错误：在MySQL代码中存在错误可能导致中继日志解析失败。这可能是由于bug或不一致的状态等问题引起的。
当发生这些情况之一时，MySQL会记录错误并尝试通过报告错误来提供有关问题的信息。在这种情况下，ER_SLAVE_RELAY_LOG_READ_FAILURE错误代码用于指示中继日志读取失败，ER_THD(thd, ER_SLAVE_RELAY_LOG_READ_FAILURE)用于获取与错误相关的线程处理器（THD）对象，并在错误报告中提供更多信息。


- 某台机器的磁盘被写满








