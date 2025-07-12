---
title: srv_start
description: srv_start
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



## srv_start

```c++
dberr_t srv_start(bool create_new_db) {
        lsn_t flushed_lsn;

        page_no_t sum_of_data_file_sizes;
        page_no_t tablespace_size_in_header;
        dberr_t err;
        mtr_t mtr;
        purge_pq_t *purge_queue;

        assert(srv_dict_metadata == nullptr);
        /* Reset the start state. */
        srv_start_state = SRV_START_STATE_NONE;

#ifdef UNIV_LINUX
        #ifdef HAVE_FALLOC_PUNCH_HOLE_AND_KEEP_SIZE
        ib::info(ER_IB_MSG_1107);
        #else
        ib::info(ER_IB_MSG_1108);
        #endif /* HAVE_FALLOC_PUNCH_HOLE_AND_KEEP_SIZE */
#endif         /* UNIV_LINUX */

        static_assert(sizeof(ulint) == sizeof(void *),
                      "Size of InnoDB's ulint is not the same as size of void*. The "
                      "sizes should be the same so that on a 64-bit platforms you "
                      "can allocate more than 4 GB of memory.");

        if (srv_is_upgrade_mode) {
                if (srv_force_recovery != 0) {
                        ib::error(ER_IB_MSG_1111);
                        return (srv_init_abort(DB_ERROR));
                }
                if (srv_read_only_mode) {
                        ib::error(ER_IB_MSG_1110);
                        return (srv_init_abort(DB_ERROR));
                }
        }

#ifdef UNIV_DEBUG
        ib::info(ER_IB_MSG_1112) << "!!!!!!!! UNIV_DEBUG switched on !!!!!!!!!";
#endif

#ifdef UNIV_IBUF_DEBUG
        ib::info(ER_IB_MSG_1113) << "!!!!!!!! UNIV_IBUF_DEBUG switched on !!!!!!!!!";
        #ifdef UNIV_IBUF_COUNT_DEBUG
        ib::info(ER_IB_MSG_1114) << "!!!!!!!! UNIV_IBUF_COUNT_DEBUG switched on !!!!!!!!!";
        ib::error(ER_IB_MSG_1115) << "Crash recovery will fail with UNIV_IBUF_COUNT_DEBUG";
        #endif
#endif

#ifdef UNIV_LOG_LSN_DEBUG
        ib::info(ER_IB_MSG_1116) << "!!!!!!!! UNIV_LOG_LSN_DEBUG switched on !!!!!!!!!";
#endif /* UNIV_LOG_LSN_DEBUG */

#if defined(COMPILER_HINTS_ENABLED)
        ib::info(ER_IB_MSG_1117) << "Compiler hints enabled.";
#endif /* defined(COMPILER_HINTS_ENABLED) */

        ib::info(ER_IB_MSG_1119) << MUTEX_TYPE;
        ib::info(ER_IB_MSG_1120) << IB_MEMORY_BARRIER_STARTUP_MSG;

        if (srv_force_recovery > 0) {
                ib::info(ER_IB_MSG_1121) << "!!! innodb_force_recovery is set to " << srv_force_recovery << " !!!";
        }

#ifndef HAVE_MEMORY_BARRIER
        #if defined __i386__ || defined __x86_64__ || defined _M_IX86 || defined _M_X64 || defined _WIN32
        #else
        ib::warn(ER_IB_MSG_1122);
        #endif /* IA32 or AMD64 */
#endif         /* HAVE_MEMORY_BARRIER */

#ifdef UNIV_ZIP_DEBUG
        ib::info(ER_IB_MSG_1123, ZLIB_VERSION) << " with validation";
#else
        ib::info(ER_IB_MSG_1123, ZLIB_VERSION);
#endif /* UNIV_ZIP_DEBUG */

#ifdef UNIV_ZIP_COPY
        ib::info(ER_IB_MSG_1124) << "and extra copying";
#endif /* UNIV_ZIP_COPY */

        /* Since InnoDB does not currently clean up all its internal data
        structures in MySQL Embedded Server Library server_end(), we
        print an error message if someone tries to start up InnoDB a
        second time during the process lifetime. */

        if (srv_start_has_been_called) {
                ib::error(ER_IB_MSG_1125);
        }

        srv_start_has_been_called = true;

        srv_is_being_started = true;

#ifdef HAVE_PSI_STAGE_INTERFACE
        /* Register performance schema stages before any real work has been
        started which may need to be instrumented. */
        mysql_stage_register("innodb", srv_stages, UT_ARR_SIZE(srv_stages));
#endif /* HAVE_PSI_STAGE_INTERFACE */

        /* Switch latching order checks on in sync0debug.cc, if
        --innodb-sync-debug=false (default) */
        ut_d(sync_check_enable());

        srv_boot();

        ib::info(ER_IB_MSG_1126) << "Using "
                                 << (ut_crc32_cpu_enabled ? (ut_poly_mul_cpu_enabled ? "hardware accelerated crc32 and "
                                                                                       "polynomial multiplication."
                                                                                     : "hardware accelerated crc32 and "
                                                                                       "software polynomial multiplication.")
                                                          : "software crc32.");

        os_create_block_cache();

        fil_init(innobase_get_open_files_limit());

        /* This is the default directory for IBD and IBU files. Put it first
        in the list of known directories. */
        fil_set_scan_dir(MySQL_datadir_path.path());

        /* Add --innodb-data-home-dir as a known location for IBD and IBU files
        if it is not already there. */
        ut_ad(srv_data_home != nullptr && *srv_data_home != '\0');
        fil_set_scan_dir(Fil_path::remove_quotes(srv_data_home));

        /* Add --innodb-directories as known locations for IBD and IBU files. */
        if (srv_innodb_directories != nullptr && *srv_innodb_directories != 0) {
                fil_set_scan_dirs(Fil_path::remove_quotes(srv_innodb_directories));
        }

        /* Note whether the undo path is different (not the same or under)
        from all other known directories. If so, this will allow us to keep
        IBD files out of this unique undo location.*/
        MySQL_undo_path_is_unique = !fil_path_is_known(MySQL_undo_path.path());

        /* For the purpose of file discovery at startup, we need to scan
        --innodb-undo-directory also if it is different from the locations above. */
        if (MySQL_undo_path_is_unique) {
                fil_set_scan_dir(Fil_path::remove_quotes(MySQL_undo_path));
        }

        ib::info(ER_IB_MSG_378) << "Directories to scan '" << fil_get_dirs() << "'";

        /* Must replace clone files before scanning directories. When
        clone replaces current database, cloned files are moved to data files
        at this stage. */
        err = clone_init();

        if (err != DB_SUCCESS) {
                return (srv_init_abort(err));
        }

        err = fil_scan_for_tablespaces();

        if (err != DB_SUCCESS) {
                return (srv_init_abort(err));
        }

        if (!srv_read_only_mode) {
                mutex_create(LATCH_ID_SRV_MONITOR_FILE, &srv_monitor_file_mutex);

                if (srv_innodb_status) {
                        srv_monitor_file_name =
                            static_cast<char *>(ut::malloc_withkey(UT_NEW_THIS_FILE_PSI_KEY,
                                                                   MySQL_datadir_path.len() + 20 + sizeof "/innodb_status."));

                        sprintf(srv_monitor_file_name,
                                "%s/innodb_status." ULINTPF,
                                static_cast<const char *>(MySQL_datadir_path),
                                os_proc_get_number());

                        srv_monitor_file = fopen(srv_monitor_file_name, "w+");

                        if (!srv_monitor_file) {
                                ib::error(ER_IB_MSG_1127, srv_monitor_file_name, strerror(errno));

                                return (srv_init_abort(DB_ERROR));
                        }
                } else {
                        srv_monitor_file_name = nullptr;
                        srv_monitor_file      = os_file_create_tmpfile();

                        if (!srv_monitor_file) {
                                return (srv_init_abort(DB_ERROR));
                        }
                }

                mutex_create(LATCH_ID_SRV_MISC_TMPFILE, &srv_misc_tmpfile_mutex);

                srv_misc_tmpfile = os_file_create_tmpfile();

                if (!srv_misc_tmpfile) {
                        return (srv_init_abort(DB_ERROR));
                }
        }

        srv_n_file_io_threads = srv_n_read_io_threads;

        srv_n_file_io_threads += srv_n_write_io_threads;

        if (!srv_read_only_mode) {
                /* Add the log and ibuf IO threads. */
                srv_n_file_io_threads += 2;
        } else {
                ib::info(ER_IB_MSG_1128);
        }

        ut_a(srv_n_file_io_threads <= SRV_MAX_N_IO_THREADS);

        if (!os_aio_init(srv_n_read_io_threads, srv_n_write_io_threads)) {
                ib::error(ER_IB_MSG_1129);

                return (srv_init_abort(DB_ERROR));
        }

        double size;
        char unit;

        if (srv_buf_pool_size >= 1024 * 1024 * 1024) {
                size = ((double)srv_buf_pool_size) / (1024 * 1024 * 1024);
                unit = 'G';
        } else {
                size = ((double)srv_buf_pool_size) / (1024 * 1024);
                unit = 'M';
        }

        double chunk_size;
        char chunk_unit;

        if (srv_buf_pool_chunk_unit >= 1024 * 1024 * 1024) {
                chunk_size = srv_buf_pool_chunk_unit / 1024.0 / 1024 / 1024;
                chunk_unit = 'G';
        } else {
                chunk_size = srv_buf_pool_chunk_unit / 1024.0 / 1024;
                chunk_unit = 'M';
        }

        ib::info(ER_IB_MSG_1130, size, unit, srv_buf_pool_instances, chunk_size, chunk_unit);

        err = buf_pool_init(srv_buf_pool_size, srv_buf_pool_instances);

        if (err != DB_SUCCESS) {
                ib::error(ER_IB_MSG_1131);

                return (srv_init_abort(DB_ERROR));
        }

        ib::info(ER_IB_MSG_1132);

#ifdef UNIV_DEBUG
        /* We have observed deadlocks with a 5MB buffer pool but
        the actual lower limit could very well be a little higher. */

        if (srv_buf_pool_size <= 5 * 1024 * 1024) {
                ib::info(ER_IB_MSG_1133, ulonglong{srv_buf_pool_size / 1024 / 1024});
        }
#endif /* UNIV_DEBUG */

        fsp_init();
        pars_init();
        recv_sys_create();
        recv_sys_init();
        trx_sys_create();
        lock_sys_create(srv_lock_table_size);

        /* Create i/o-handler threads: */

        /* For read only mode, we don't need ibuf and log I/O thread.
        Please see innobase_start_or_create_for_mysql() */
        ulint start = (srv_read_only_mode) ? 0 : 2;

        /* Sequence number displayed in the thread os name. */
        PSI_thread_seqnum pfs_seqnum [[maybe_unused]];

        for (ulint t = 0; t < srv_n_file_io_threads; ++t) {
                IB_thread thread;
                if (t < start) {
                        if (t == 0) {
                                thread = os_thread_create(io_ibuf_thread_key, 0, io_handler_thread, t);
                        } else {
                                ut_ad(t == 1);
                                thread = os_thread_create(io_log_thread_key, 0, io_handler_thread, t);
                        }
                } else if (t >= start && t < (start + srv_n_read_io_threads)) {
                        /* Numbering for ib_io_rd-NN starts with N=1. */
                        pfs_seqnum = t + 1 - start;
                        thread     = os_thread_create(io_read_thread_key, pfs_seqnum, io_handler_thread, t);

                } else if (t >= (start + srv_n_read_io_threads) &&
                           t < (start + srv_n_read_io_threads + srv_n_write_io_threads)) {
                        /* Numbering for ib_io_wr-NN starts with N=1. */
                        pfs_seqnum = t + 1 - start - srv_n_read_io_threads;
                        thread     = os_thread_create(io_write_thread_key, pfs_seqnum, io_handler_thread, t);
                } else {
                        /* Dead code ? */
                        thread = os_thread_create(io_handler_thread_key, t, io_handler_thread, t);
                }
                thread.start();
        }

        /* Even in read-only mode there could be flush job generated by
        intrinsic table operations. */
        buf_flush_page_cleaner_init();

        srv_start_state_set(SRV_START_STATE_IO);

        srv_startup_is_before_trx_rollback_phase = !create_new_db;

        if (create_new_db) {
                recv_sys_free();
        }

        /* Open or create the data files. */
        page_no_t sum_of_new_sizes;

        err = srv_sys_space.open_or_create(false, create_new_db, &sum_of_new_sizes, &flushed_lsn);

        if (flushed_lsn < LOG_START_LSN) {
                ut_ad(!create_new_db);
                /* Data directory hasn't been initialized yet. */
                ib::error(ER_IB_MSG_DATA_DIRECTORY_NOT_INITIALIZED_OR_CORRUPTED);
                return DB_ERROR;
        }

        /* FIXME: This can be done earlier, but we now have to wait for
        checking of system tablespace. */
        dict_persist_init();

        switch (err) {
                case DB_SUCCESS:
                        break;
                case DB_CANNOT_OPEN_FILE:
                        ib::error(ER_IB_MSG_1134);
                        [[fallthrough]];
                default:

                        /* Other errors might come from
                        Datafile::validate_first_page() */

                        return (srv_init_abort(err));
        }

        mtr_t::s_logging.init();

        if (dblwr::is_enabled() && ((err = dblwr::open()) != DB_SUCCESS)) {
                return srv_init_abort(err);
        }

        lsn_t new_files_lsn;

        err = log_sys_init(create_new_db, flushed_lsn, new_files_lsn);

        if (err != DB_SUCCESS) {
                return srv_init_abort(err);
        }

        ut_a(log_sys != nullptr);

        arch_init();

        if (create_new_db) {
                ut_a(buf_are_flush_lists_empty_validate());

                ut_a(!srv_read_only_mode);

                ut_a(log_sys->last_checkpoint_lsn.load() == LOG_START_LSN + LOG_BLOCK_HDR_SIZE);

                ut_a(new_files_lsn == LOG_START_LSN + LOG_BLOCK_HDR_SIZE);

                err = log_start(*log_sys, new_files_lsn, new_files_lsn, nullptr);

                if (err != DB_SUCCESS) {
                        return srv_init_abort(err);
                }

                log_start_background_threads(*log_sys);

                err = srv_undo_tablespaces_init(true);

                if (err != DB_SUCCESS) {
                        return (srv_init_abort(err));
                }

                mtr_start(&mtr);

                bool ret = fsp_header_init(0, sum_of_new_sizes, &mtr);

                mtr_commit(&mtr);

                if (!ret) {
                        return (srv_init_abort(DB_ERROR));
                }

                /* To maintain backward compatibility we create only
                the first rollback segment before the double write buffer.
                All the remaining rollback segments will be created later,
                after the double write buffers haves been created. */
                trx_sys_create_sys_pages();

                trx_purge_sys_mem_create();

                purge_queue = trx_sys_init_at_db_start();

                /* The purge system needs to create the purge view and
                therefore requires that the trx_sys is inited. */

                trx_purge_sys_initialize(srv_threads.m_purge_workers_n, purge_queue);

                err = dict_create();

                if (err != DB_SUCCESS) {
                        return (srv_init_abort(err));
                }

                srv_create_sdi_indexes();

                /* We always create the legacy double write buffer to preserve the
                expected page ordering of the system tablespace.
                FIXME: Try and remove this requirement. */
                err = dblwr::v1::create();

                if (err != DB_SUCCESS) {
                        return srv_init_abort(err);
                }

        } else {
                bool log_upgrade = log_sys->m_format < Log_format::CURRENT;
                DBUG_EXECUTE_IF("log_force_upgrade", log_upgrade = true;);

                if (log_upgrade && srv_read_only_mode) {
                        ib::error(ER_IB_MSG_LOG_UPGRADE_IN_READ_ONLY_MODE, ulong{to_int(log_sys->m_format)});
                        return srv_init_abort(DB_ERROR);
                }

                /* Load the reserved boundaries of the legacy dblwr buffer, this is
                required to check for stray reads and writes trying to access this
                reserved region in the sys tablespace.
                FIXME: Try and remove this requirement. */
                err = dblwr::v1::init();

                if (err != DB_SUCCESS) {
                        return srv_init_abort(err);
                }

                /* Invalidate the buffer pool to ensure that we reread
                the page that we read above, during recovery.
                Note that this is not as heavy weight as it seems. At
                this point there will be only ONE page in the buf_LRU
                and there must be no page in the buf_flush list. */
                buf_pool_invalidate();

                /* Open all data files in the system tablespace:
                we keep them open until database shutdown. */
                fil_open_system_tablespace_files();

                /* We always try to do a recovery, even if the database had
                been shut down normally: this is the normal startup path */
                RECOVERY_CRASH(1);

                if (new_files_lsn != 0) {
                        /* This means that either no log files have been found
                        or the existing log files were marked as uninitialized. */
                        flushed_lsn = new_files_lsn;
                }

                err = recv_recovery_from_checkpoint_start(*log_sys, flushed_lsn);

                if (err == DB_SUCCESS) {
                        arch_page_sys->post_recovery_init();

                        /* Initialize the change buffer. */
                        err = dict_boot();
                }

                if (err != DB_SUCCESS) {
                        return (srv_init_abort(err));
                }

                ut_ad(clone_check_recovery_crashpoint(recv_sys->is_cloned_db));

                const bool redo_writes_allowed = !srv_read_only_mode && !log_upgrade;

                ut_a(srv_force_recovery < SRV_FORCE_NO_LOG_REDO || !redo_writes_allowed);

                if (redo_writes_allowed) {
                        /* We need to start log threads now, because recovery
                        could result in execution of ibuf merges. These merges
                        could result in new redo records. In the read-only mode
                        we do not need log threads, because we disallow new redo
                        records in such mode. If upgrade was forced, or the data
                        directory was cloned, we will start redo threads later. */
                        log_start_background_threads(*log_sys);
                }

                if (srv_force_recovery < SRV_FORCE_NO_LOG_REDO) {
                        /* Apply the hashed log records to the
                        respective file pages, for the last batch of
                        recv_group_scan_log_recs(). */

                        RECOVERY_CRASH(2);

                        /* Don't allow IBUF operations for cloned database
                        recovery as it would add extra redo log and we may
                        not have enough margin.

                        Don't allow IBUF operations when redo is written
                        in the older format than the current, because we
                        would write new redo records in the current fmt,
                        and end up with file in both formats = invalid. */

                        err = recv_apply_hashed_log_recs(*log_sys, !recv_sys->is_cloned_db && !log_upgrade);

                        if (recv_sys->found_corrupt_log || err != DB_SUCCESS) {
                                err = DB_ERROR;
                                return (srv_init_abort(err));
                        }

                        DBUG_PRINT("ib_log", ("apply completed"));

                        /* Check and print if there were any tablespaces
                        which had redo log records but we couldn't apply
                        them because the filenames were missing. */

                        /* Recovery complete, start verifying the
                        page LSN on read. */
                        recv_lsn_checks_on = true;
                }

                /* We have gone through the redo log, now check if all the
                tablespaces were found and recovered. */

                if (srv_force_recovery == 0 && fil_check_missing_tablespaces()) {
                        ib::error(ER_IB_MSG_1139);
                        RECOVERY_CRASH(3);

                        /* Set the abort flag to true. */
                        auto p = recv_recovery_from_checkpoint_finish(true);

                        ut_a(p == nullptr);

                        return (srv_init_abort(DB_ERROR));
                }

                /* We have successfully recovered from the redo log. The
                data dictionary should now be readable. */

                if (recv_sys->found_corrupt_log) {
                        ib::warn(ER_IB_MSG_RECOVERY_CORRUPT);
                }

                if (!srv_force_recovery && !srv_read_only_mode) {
                        buf_flush_sync_all_buf_pools();
                }

                RECOVERY_CRASH(3);

                srv_dict_metadata = recv_recovery_from_checkpoint_finish(false);

                if (recv_sys->is_cloned_db && srv_dict_metadata != nullptr) {
                        ut::delete_(srv_dict_metadata);
                        srv_dict_metadata = nullptr;
                }

                /* We need to save the dynamic metadata collected from redo log to DD
                buffer table here. This is to make sure that the dynamic metadata is not
                lost by any future checkpoint. Since DD and data dictionary in memory
                objects are not fully initialized at this point, the usual mechanism to
                persist dynamic metadata at checkpoint wouldn't work. */

                DBUG_EXECUTE_IF("log_first_rec_group_test", {
                        const lsn_t end_lsn = mtr_commit_mlog_test();
                        log_write_up_to(*log_sys, end_lsn, true);
                        DBUG_SUICIDE();
                });

                if (srv_dict_metadata != nullptr && !srv_dict_metadata->empty()) {
                        ut_a(redo_writes_allowed);

                        /* Open this table in case srv_dict_metadata should be applied to this
                        table before checkpoint. And because DD is not fully up yet, the table
                        can be opened by internal APIs. */

                        fil_space_t *space = fil_space_acquire_silent(dict_sys_t::s_dict_space_id);
                        if (space == nullptr) {
                                dberr_t error = fil_ibd_open(true,
                                                             FIL_TYPE_TABLESPACE,
                                                             dict_sys_t::s_dict_space_id,
                                                             predefined_flags,
                                                             dict_sys_t::s_dd_space_name,
                                                             dict_sys_t::s_dd_space_file_name,
                                                             true,
                                                             false);
                                if (error != DB_SUCCESS) {
                                        ib::error(ER_IB_MSG_1142);
                                        return (srv_init_abort(DB_ERROR));
                                }
                        } else {
                                fil_space_release(space);
                        }

                        dict_persist->table_buffer = ut::new_withkey<DDTableBuffer>(UT_NEW_THIS_FILE_PSI_KEY);
                        /* We write redo log here. We assume that there should be enough room in
                        log files, supposing log_free_check() works fine before crash. */
                        srv_dict_metadata->store();

                        /* Flush logs to persist the changes. */
                        log_buffer_flush_to_disk(*log_sys);
                }

                RECOVERY_CRASH(4);

                log_sys->m_allow_checkpoints.store(true, std::memory_order_release);

                bool log_downsize_requested =
                    log_sys->m_capacity.target_physical_capacity() < log_sys->m_capacity.current_physical_capacity();

                DBUG_EXECUTE_IF("log_force_resize", log_downsize_requested = true;);

                const bool need_to_recreate_log_files =
                    log_upgrade || (log_downsize_requested && !srv_force_recovery && !recv_sys->found_corrupt_log);

                if (need_to_recreate_log_files) {
                        /* Prepare to replace the redo log files. */

                        if (log_upgrade) {
                                ut_a(!srv_read_only_mode);

                                if (recv_sys->is_cloned_db) {
                                        ib::error(ER_IB_MSG_LOG_UPGRADE_CLONED_DB, ulong{to_int(log_sys->m_format)});
                                        return srv_init_abort(DB_ERROR);
                                }

                                /* For non-empty redo log, upgrade is rejected, so there is even
                                no attempt to parse it, so no way to discover it's corrupted. */
                                if (recv_sys->found_corrupt_log) {
                                        ib::error(ER_IB_MSG_LOG_UPGRADE_CORRUPTION__UNEXPECTED,
                                                  ulong{to_int(log_sys->m_format)});
                                        ut_d(ut_error);
                                        ut_o(return srv_init_abort(DB_ERROR));
                                }

                                /* For non-empty redo log, upgrade is rejected, so there is even
                                no way to reconstruct non empty srv_dict_metadata. */
                                if (srv_dict_metadata != nullptr && !srv_dict_metadata->empty()) {
                                        ib::error(ER_IB_MSG_LOG_UPGRADE_NON_PERSISTED_DD_METADATA__UNEXPECTED,
                                                  ulong{to_int(log_sys->m_format)});
                                        ut_d(ut_error);
                                        ut_o(return srv_init_abort(DB_ERROR));
                                }

                        } else {
                                ut_a(srv_force_recovery == 0);
                                if (srv_read_only_mode) {
                                        const os_offset_t min_capacity_in_M =
                                            log_sys->m_capacity.current_physical_capacity() / (1024 * 1024UL);
                                        ib::error(ER_IB_MSG_LOG_FILES_RESIZE_ON_START_IN_READ_ONLY_MODE,
                                                  ulonglong{min_capacity_in_M});
                                        return srv_init_abort(DB_ERROR);
                                }
                        }

                        buf_pool_wait_for_no_pending_io();

                        if (redo_writes_allowed) {
                                /* Create checkpoint to ensure that the checkpoint header is flushed
                                to the newest redo log file, before log_files_remove() is called,
                                because otherwise crash after the first file removal could lead to
                                the state without a checkpoint. */
                                log_make_empty_and_stop_background_threads(*log_sys);
                        }

                        flushed_lsn = log_sys->flushed_to_disk_lsn.load();

                        ut_ad(buf_pool_pending_io_reads_count() == 0);

                        ut_d(log_sys->disable_redo_writes = true);

                        {
                                /* Emit a message to the error log. */
                                const auto target_size      = log_sys->m_capacity.target_physical_capacity();
                                const auto target_size_in_M = target_size / (1024 * 1024UL);
                                if (log_upgrade) {
                                        ib::info(ER_IB_MSG_LOG_FILES_UPGRADE,
                                                 ulonglong{target_size_in_M},
                                                 ulonglong{flushed_lsn});

                                } else {
                                        const auto current_size      = log_files_size_of_existing_files(log_sys->m_files);
                                        const auto current_size_in_M = current_size / (1024 * 1024UL);
                                        ib::info(ER_IB_MSG_LOG_FILES_RESIZE_ON_START,
                                                 ulonglong{current_size_in_M},
                                                 ulonglong{target_size_in_M},
                                                 ulonglong{flushed_lsn});
                                }
                        }

                        /* Prepare to delete the old redo log files. */
                        buf_flush_sync_all_buf_pools();

                        RECOVERY_CRASH(5);

                        if (flushed_lsn < log_get_lsn(*log_sys) || buf_pool_get_oldest_modification_lwm() != 0) {
                                if (log_upgrade) {
                                        ib::error(ER_IB_MSG_LOG_UPGRADE_FLUSH_FAILED__UNEXPECTED,
                                                  ulong{to_int(log_sys->m_format)});
                                } else {
                                        ib::error(ER_IB_MSG_LOG_FILES_RESIZE_ON_START_FAILED__UNEXPECTED,
                                                  ulong{to_int(log_sys->m_format)});
                                }
                                ut_d(ut_error);
                                ut_o(return srv_init_abort(DB_ERROR));
                        }

                        /* Stamp the LSN to the data files. */
                        err = fil_write_flushed_lsn(flushed_lsn);
                        ut_a(err == DB_SUCCESS);

                        RECOVERY_CRASH(6);

                        ib::info(ER_IB_MSG_LOG_FILES_REWRITING);

                        /* Remove all existing log files. */
                        log_files_remove(*log_sys);

                        log_sys_close();
                        ut_a(log_sys == nullptr);

                        /* Finish clone file recovery before creating new log files. We
                        roll forward to remove any intermediate files here. */
                        clone_files_recovery(true);

                        /* This is to provide the property that data byte at given lsn never
                        changes and avoid the need to rewrite the block with flushed_lsn. */
                        flushed_lsn = ut_uint64_align_up(flushed_lsn, OS_FILE_LOG_BLOCK_SIZE) + LOG_BLOCK_HDR_SIZE;

                        err = log_sys_init(true, flushed_lsn, flushed_lsn);

                        if (err != DB_SUCCESS) {
                                return srv_init_abort(err);
                        }

                        ut_d(log_sys->disable_redo_writes = false);

                        fil_open_system_tablespace_files();

                        err = log_start(*log_sys, flushed_lsn, flushed_lsn, nullptr);

                        if (err != DB_SUCCESS) {
                                return srv_init_abort(err);
                        }

                        log_start_background_threads(*log_sys);

                } else if (recv_sys->is_cloned_db || recv_sys->is_meb_db) {
                        buf_pool_wait_for_no_pending_io();

                        /* Reset creator for log */

                        if (redo_writes_allowed) {
                                log_stop_background_threads(*log_sys);
                        }

                        ut_ad(buf_pool_pending_io_reads_count() == 0);

                        err = log_files_reset_creator_and_set_full(*log_sys);
                        if (err != DB_SUCCESS) {
                                return srv_init_abort(err);
                        }

                        log_start_background_threads(*log_sys);

                } else {
                        ut_a(redo_writes_allowed || srv_read_only_mode);
                }

                if (sum_of_new_sizes > 0) {
                        ut_a(!srv_read_only_mode);

                        /* New data file(s) were added */
                        mtr_start(&mtr);

                        fsp_header_inc_size(0, sum_of_new_sizes, &mtr);

                        mtr_commit(&mtr);

                        /* Immediately write the log record about
                        increased tablespace size to disk, so that it
                        is durable even if mysqld would crash
                        quickly */

                        log_buffer_flush_to_disk(*log_sys);
                }

                err = srv_undo_tablespaces_init(false);

                if (err != DB_SUCCESS && srv_force_recovery < SRV_FORCE_NO_UNDO_LOG_SCAN) {
                        return (srv_init_abort(err));
                }

                trx_purge_sys_mem_create();

                /* The purge system needs to create the purge view and
                therefore requires that the trx_sys is inited. */
                purge_queue = trx_sys_init_at_db_start();

                if (srv_is_upgrade_mode) {
                        if (!purge_queue->empty()) {
                                ib::info(ER_IB_MSG_1144);
                                srv_upgrade_old_undo_found = true;
                        }
                        /* Either the old or new undo tablespaces will
                        be deleted later depending on the value of
                        'failed_upgrade' in dd_upgrade_finish(). */
                } else {
                        /* New undo tablespaces have been created.
                        Delete the old undo tablespaces and the references
                        to them in the TRX_SYS page. */
                        srv_undo_tablespaces_upgrade();
                }

                DBUG_EXECUTE_IF("check_no_undo", ut_ad(purge_queue->empty()););

                /* The purge system needs to create the purge view and
                therefore requires that the trx_sys and trx lists were
                initialized in trx_sys_init_at_db_start(). */
                trx_purge_sys_initialize(srv_threads.m_purge_workers_n, purge_queue);
        }

        /* Open temp-tablespace and keep it open until shutdown. */
        err = srv_open_tmp_tablespace(create_new_db, &srv_tmp_space);
        if (err != DB_SUCCESS) {
                return (srv_init_abort(err));
        }

        err = ibt::open_or_create(create_new_db);
        if (err != DB_SUCCESS) {
                return (srv_init_abort(err));
        }

        /* Here the double write buffer has already been created and so
        any new rollback segments will be allocated after the double
        write buffer. The default segment should already exist.
        We create the new segments only if it's a new database or
        the database was shutdown cleanly. */

        /* Note: When creating the extra rollback segments during an upgrade
        we violate the latching order, even if the change buffer is empty.
        We make an exception in sync0sync.cc and check srv_is_being_started
        for that violation. It cannot create a deadlock because we are still
        running in single threaded mode essentially. Only the IO threads
        should be running at this stage. */

        ut_a(srv_rollback_segments > 0);
        ut_a(srv_rollback_segments <= TRX_SYS_N_RSEGS);

        /* Make sure there are enough rollback segments in each tablespace
        and that each rollback segment has an associated memory object.
        If any of these rollback segments contain undo logs, load them into
        the purge queue */
        if (!trx_rseg_adjust_rollback_segments(srv_rollback_segments)) {
                return (srv_init_abort(DB_ERROR));
        }

        /* Any undo tablespaces under construction are now fully built
        with all needed rsegs. Delete the trunc.log files and clear the
        construction list. */
        srv_undo_tablespaces_mark_construction_done();

        /* Now that all rsegs are ready for use, make them active. */
        undo::spaces->s_lock();
        for (auto undo_space : undo::spaces->m_spaces) {
                if (!undo_space->is_empty()) {
                        undo_space->set_active();
                }
        }
        undo::spaces->s_unlock();

        /* Undo Tablespaces and Rollback Segments are ready. */
        srv_startup_is_before_trx_rollback_phase = false;

        if (!srv_read_only_mode) {
                if (create_new_db) {
                        srv_buffer_pool_load_at_startup = false;
                }

                /* Create the thread which watches the timeouts
                for lock waits */
                srv_threads.m_lock_wait_timeout = os_thread_create(srv_lock_timeout_thread_key, 0, lock_wait_timeout_thread);

                srv_threads.m_lock_wait_timeout.start();

                /* Create the thread which warns of long semaphore waits */
                srv_threads.m_error_monitor = os_thread_create(srv_error_monitor_thread_key, 0, srv_error_monitor_thread);

                srv_threads.m_error_monitor.start();

                /* Create the thread which prints InnoDB monitor info */
                srv_threads.m_monitor = os_thread_create(srv_monitor_thread_key, 0, srv_monitor_thread);

                srv_threads.m_monitor.start();
        }

        srv_sys_tablespaces_open = true;

        /* Rotate the encryption key for recovery. It's because
        server could crash in middle of key rotation. Some tablespace
        didn't complete key rotation. Here, we will resume the
        rotation. */
        if (!srv_read_only_mode && !create_new_db && srv_force_recovery < SRV_FORCE_NO_LOG_REDO) {
                size_t fail_count = fil_encryption_rotate();
                if (fail_count > 0) {
                        ib::info(ER_IB_MSG_1146)
                            << "During recovery, fil_encryption_rotate() failed for " << fail_count << " tablespace(s).";
                }
        }

        srv_is_being_started = false;

        ut_a(trx_purge_state() == PURGE_STATE_INIT);

        /* wake main loop of page cleaner up */
        os_event_set(buf_flush_event);

        sum_of_data_file_sizes = srv_sys_space.get_sum_of_sizes();
        ut_a(sum_of_new_sizes != FIL_NULL);

        tablespace_size_in_header = fsp_header_get_tablespace_size();

        if (!srv_read_only_mode && !srv_sys_space.can_auto_extend_last_file() &&
            sum_of_data_file_sizes != tablespace_size_in_header) {
                ib::error(ER_IB_MSG_1147, ulong{tablespace_size_in_header}, ulong{sum_of_data_file_sizes});

                if (srv_force_recovery == 0 && sum_of_data_file_sizes < tablespace_size_in_header) {
                        /* This is a fatal error, the tail of a tablespace is
                        missing */

                        ib::error(ER_IB_MSG_1148);

                        return (srv_init_abort(DB_ERROR));
                }
        }

        if (!srv_read_only_mode && srv_sys_space.can_auto_extend_last_file() &&
            sum_of_data_file_sizes < tablespace_size_in_header) {
                ib::error(ER_IB_MSG_1149, ulong{tablespace_size_in_header}, ulong{sum_of_data_file_sizes});

                if (srv_force_recovery == 0) {
                        ib::error(ER_IB_MSG_1150);

                        return (srv_init_abort(DB_ERROR));
                }
        }

        /* Finish clone files recovery. This call is idempotent and is no op
        if it is already done before creating new log files. */
        clone_files_recovery(true);

        ib::info(ER_IB_MSG_1151, INNODB_VERSION_STR, ulonglong{log_get_lsn(*log_sys)});

        return (DB_SUCCESS);
}

/** Applier of dynamic metadata */
struct metadata_applier {
        /** Default constructor */
        metadata_applier() = default;
        /** Visitor.
        @param[in]      table   table to visit */
        void operator()(dict_table_t *table) const {
                ut_ad(dict_sys->dynamic_metadata != nullptr);
                uint64_t autoinc = table->autoinc;
                dict_table_load_dynamic_metadata(table);
                /* For those tables which were not opened by
                ha_innobase::open() and not initialized by
                innobase_initialize_autoinc(), the next counter should be
                advanced properly */
                if (autoinc != table->autoinc && table->autoinc != ~0ULL) {
                        ++table->autoinc;
                }
        }
};


```













