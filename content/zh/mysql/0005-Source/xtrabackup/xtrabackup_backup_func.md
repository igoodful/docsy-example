---
title: xtrabackup_backup_func
description: /root/percona-xtrabackup-release-8.0.32-26/storage/innobase/xtrabackup/src/xtrabackup.cc
date: 2023-10-30
weight: 10000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}



{{< /alert >}}

### xtrabackup_backup_func
```c++
void xtrabackup_backup_func(void) {
  MY_STAT stat_info;
  uint i;
  uint count;
  ib_mutex_t count_mutex;
  data_thread_ctxt_t *data_threads;

  recv_is_making_a_backup = true;
  bool data_copying_error = false;
  std::shared_ptr<xb::dd_tablespaces> xb_dd_space;
  init_mysql_environment();

  if (print_instant_versioned_tables(mysql_connection)) exit(EXIT_FAILURE);

  if (opt_dump_innodb_buffer_pool) {
    dump_innodb_buffer_pool(mysql_connection);
  }

#ifdef USE_POSIX_FADVISE
  xb::info() << "uses posix_fadvise().";
#endif

  /* cd to datadir */

  if (my_setwd(mysql_real_data_home, MYF(MY_WME))) {
    xb::error() << "cannot my_setwd " << mysql_real_data_home;
    exit(EXIT_FAILURE);
  }
  xb::info() << "cd to " << mysql_real_data_home;

  xb::info() << "open files limit requested " << xb_open_files_limit
             << ", set to " << xb_set_max_open_files(xb_open_files_limit);

  mysql_data_home = mysql_data_home_buff;
  mysql_data_home[0] = FN_CURLIB;  // all paths are relative from here
  mysql_data_home[1] = 0;

  srv_read_only_mode = true;

  srv_backup_mode = true;

  if (opt_lock_ddl) {
    xb_dd_space = xb::build_space_id_set(mysql_connection);
    ut_ad(xb_dd_space->size());
  }

  /* We can safely close files if we don't allow DDL during the
  backup */
  srv_close_files = xb_close_files || opt_lock_ddl;

  if (xb_close_files)
    xb::warn()
        << "close-files specified. Use it "
           "at your own risk. If there are DDL operations like table DROP "
           "TABLE "
           "or RENAME TABLE during the backup, inconsistent backup will be "
           "produced.";

  /* initialize components */
  if (innodb_init_param()) exit(EXIT_FAILURE);

  xb_normalize_init_values();

#ifndef _WIN32
  srv_unix_file_flush_method =
      static_cast<srv_unix_flush_t>(innodb_flush_method);
  ut_ad(innodb_flush_method <= SRV_UNIX_O_DIRECT_NO_FSYNC);
#else
  srv_win_file_flush_method = static_cast<srv_win_flush_t>(innodb_flush_method);
  ut_ad(innodb_flush_method <= SRV_WIN_IO_NORMAL);
#endif
  switch (srv_unix_file_flush_method) {
    case SRV_UNIX_O_DIRECT:
      xb::info() << "using O_DIRECT";
      break;
    case SRV_UNIX_O_DIRECT_NO_FSYNC:
      xb::info() << "using O_DIRECT_NO_FSYNC";
      break;
    default:
      break;
  }

  if (srv_buf_pool_size >= 1000 * 1024 * 1024) {
    /* Here we still have srv_pool_size counted
    in kilobytes (in 4.0 this was in bytes)
    srv_boot() converts the value to
    pages; if buffer pool is less than 1000 MB,
    assume fewer threads. */
    srv_max_n_threads = 50000;

  } else if (srv_buf_pool_size >= 8 * 1024 * 1024) {
    srv_max_n_threads = 10000;
  } else {
    srv_max_n_threads = 1000; /* saves several MB of memory,
                              especially in 64-bit
                              computers */
  }

  os_event_global_init();
  srv_general_init();
  ut_crc32_init();
  crc_init();

  xb_filters_init();
  if (opt_component_keyring_file_config != nullptr) {
    xb::warn() << "--component-keyring-file-config will be ignored "
                  "for --backup operation";
  }

  if (opt_component_keyring_config != nullptr) {
    xb::warn() << "--component-keyring-config will be ignored "
                  "for --backup operation";
  }

  if (have_keyring_component &&
      !xtrabackup::components::keyring_init_online(mysql_connection)) {
    xb::error() << "failed to init keyring component";
    exit(EXIT_FAILURE);
  }

  if (!xtrabackup::components::keyring_component_initialized &&
      !xb_keyring_init_for_backup(mysql_connection)) {
    xb::error() << "failed to init keyring plugin";
    exit(EXIT_FAILURE);
  }
  xtrabackup::components::inititialize_service_handles();

  if (opt_tables_compatibility_check) {
    xb_tables_compatibility_check();
  }

  srv_is_being_started = true;

  os_create_block_cache();

  if (!xb_fil_io_init()) {
    exit(EXIT_FAILURE);
  }

  Redo_Log_Data_Manager redo_mgr;

  dict_persist_init();

  srv_log_n_files = (ulint)innobase_log_files_in_group;
  srv_log_file_size = (ulint)innobase_log_file_size;

  clone_init();

  lock_sys_create(srv_lock_table_size);

  redo_mgr.set_copy_interval(xtrabackup_log_copy_interval);
  if (!redo_mgr.init()) {
    exit(EXIT_FAILURE);
  }

  debug_sync_point("after_redo_log_manager_init");

  if (!validate_options("my", orig_argc, orig_argv)) {
    exit(EXIT_FAILURE);
  }
  /* create extra LSN dir if it does not exist. */
  if (xtrabackup_extra_lsndir &&
      !my_stat(xtrabackup_extra_lsndir, &stat_info, MYF(0)) &&
      (my_mkdir(xtrabackup_extra_lsndir, 0777, MYF(0)) < 0)) {
    xb::error() << "cannot mkdir: " << my_errno() << " "
                << xtrabackup_extra_lsndir;
    exit(EXIT_FAILURE);
  }

  /* create target dir if not exist */
  if (!my_stat(xtrabackup_target_dir, &stat_info, MYF(0)) &&
      (my_mkdir(xtrabackup_target_dir, 0777, MYF(0)) < 0)) {
    xb::error() << "cannot mkdir: " << my_errno() << " "
                << xtrabackup_target_dir;
    exit(EXIT_FAILURE);
  }

  xtrabackup_init_datasinks();

  if (!select_history()) {
    exit(EXIT_FAILURE);
  }

  io_ticket = xtrabackup_throttle;
  wait_throttle = os_event_create();
  os_thread_create(PFS_NOT_INSTRUMENTED, 0, io_watching_thread).start();

  if (!redo_mgr.start()) {
    exit(EXIT_FAILURE);
  }

  Tablespace_map::instance().scan(mysql_connection);

  /* Populate fil_system with tablespaces to copy */
  dberr_t err = xb_load_tablespaces();
  if (err != DB_SUCCESS) {
    xb::error() << "xb_load_tablespaces() failed with error code " << err;
    exit(EXIT_FAILURE);
  }

  debug_sync_point("xtrabackup_suspend_at_start");

  lsn_t page_tracking_start_lsn = 0;
  if (opt_page_tracking &&
      pagetracking::start(mysql_connection, &page_tracking_start_lsn)) {
    xb::info() << "pagetracking is started on the server with LSN "
               << page_tracking_start_lsn;
  }

  if (xtrabackup_incremental) {
    incremental_start_checkpoint_lsn = redo_mgr.get_start_checkpoint_lsn();
    if (!xtrabackup_incremental_force_scan && opt_page_tracking) {
      changed_page_tracking = pagetracking::init(
          redo_mgr.get_start_checkpoint_lsn(), mysql_connection);
    }

    if (changed_page_tracking) {
      xb::info() << "Using pagetracking feature for incremental backup";
    } else {
      xb::info() << "using the full scan for incremental backup";
    }
  }

  ut_a(xtrabackup_parallel > 0);

  if (xtrabackup_parallel > 1) {
    xb::info() << "Starting " << xtrabackup_parallel
               << " threads for parallel data files transfer";
  }

  auto it = datafiles_iter_new(xb_dd_space);
  if (it == NULL) {
    xb::error() << "datafiles_iter_new() failed.";
    exit(EXIT_FAILURE);
  }

  /* Create data copying threads */
  data_threads = (data_thread_ctxt_t *)ut::malloc_withkey(
      UT_NEW_THIS_FILE_PSI_KEY,
      sizeof(data_thread_ctxt_t) * xtrabackup_parallel);
  count = xtrabackup_parallel;
  mutex_create(LATCH_ID_XTRA_COUNT_MUTEX, &count_mutex);

  for (i = 0; i < (uint)xtrabackup_parallel; i++) {
    data_threads[i].it = it;
    data_threads[i].num = i + 1;
    data_threads[i].count = &count;
    data_threads[i].count_mutex = &count_mutex;
    data_threads[i].error = &data_copying_error;
    os_thread_create(PFS_NOT_INSTRUMENTED, i, data_copy_thread_func,
                     data_threads + i)
        .start();
  }

  /* Wait for threads to exit */
  while (1) {
    std::this_thread::sleep_for(std::chrono::seconds(1));
    mutex_enter(&count_mutex);
    if (count == 0) {
      mutex_exit(&count_mutex);
      break;
    }
    if (redo_mgr.is_error()) {
      xb::error() << "log copying failed.";
      exit(EXIT_FAILURE);
    }
    mutex_exit(&count_mutex);
  }

  mutex_free(&count_mutex);
  ut::free(data_threads);
  datafiles_iter_free(it);

  if (data_copying_error) {
    exit(EXIT_FAILURE);
  }

  if (changed_page_tracking) {
    pagetracking::deinit(changed_page_tracking);
  }

  Backup_context backup_ctxt;
  backup_ctxt.redo_mgr = &redo_mgr;
  if (!backup_start(backup_ctxt)) {
    exit(EXIT_FAILURE);
  }

  if (opt_debug_sleep_before_unlock) {
    xb::info() << "Debug sleep for " << opt_debug_sleep_before_unlock
               << " seconds";
    std::this_thread::sleep_for(
        std::chrono::seconds(opt_debug_sleep_before_unlock));
  }

  if (redo_mgr.is_error()) {
    xb::error() << "log copying failed.";
    exit(EXIT_FAILURE);
  }

  if (!redo_mgr.stop_at(log_status.lsn, log_status.lsn_checkpoint)) {
    xb::error() << "Error stopping copy thread at LSN " << log_status.lsn;
    exit(EXIT_FAILURE);
  }

  io_watching_thread_stop = true;

  /* smart memory estimation */
  if (xtrabackup_estimate_memory) {
    auto redo_memory_requirements = xtrabackup::recv_backup_heap_used();
    redo_memory = redo_memory_requirements.first;
    redo_frames = redo_memory_requirements.second;
  }

  if (!validate_missing_encryption_tablespaces()) {
    exit(EXIT_FAILURE);
  }

  if (!xtrabackup_incremental) {
    strcpy(metadata_type_str, "full-backuped");
    metadata_from_lsn = 0;
  } else {
    strcpy(metadata_type_str, "incremental");
    metadata_from_lsn = incremental_lsn;
  }
  metadata_to_lsn = redo_mgr.get_last_checkpoint_lsn();
  metadata_last_lsn = redo_mgr.get_stop_lsn();

  if (!xtrabackup_stream_metadata(ds_meta)) {
    xb::error() << "failed to stream metadata.";
    exit(EXIT_FAILURE);
  }

  if (!backup_finish(backup_ctxt)) {
    exit(EXIT_FAILURE);
  }

  if (xtrabackup_extra_lsndir) {
    char filename[FN_REFLEN];

    sprintf(filename, "%s/%s", xtrabackup_extra_lsndir,
            XTRABACKUP_METADATA_FILENAME);
    if (!xtrabackup_write_metadata(filename)) {
      xb::error() << "failed to write metadata to " << filename;
      exit(EXIT_FAILURE);
    }

    sprintf(filename, "%s/%s", xtrabackup_extra_lsndir, XTRABACKUP_INFO);
    if (!xtrabackup_write_info(filename)) {
      xb::error() << "failed to write info to " << filename;
      exit(EXIT_FAILURE);
    }
  }

  if (opt_lock_ddl_per_table) {
    mdl_unlock_all();
  }

  Tablespace_map::instance().serialize(ds_data);

  if (opt_transition_key != NULL || opt_generate_transition_key) {
    if (!xb_tablespace_keys_dump(
            ds_data, opt_transition_key,
            opt_transition_key != NULL ? strlen(opt_transition_key) : 0)) {
      xb::error() << "failed to dump tablespace keys.";
      exit(EXIT_FAILURE);
    }
  }

  xtrabackup_destroy_datasinks();

  if (wait_throttle) {
    /* wait for io_watching_thread completion */
    while (io_watching_thread_running) {
      std::this_thread::sleep_for(std::chrono::seconds(1));
    }
    os_event_destroy(wait_throttle);
    wait_throttle = NULL;
  }

  xb::info() << "Transaction log of lsn ("
             << redo_mgr.get_start_checkpoint_lsn() << ") to ("
             << redo_mgr.get_scanned_lsn() << ") was copied.";

  xb_filters_free();

  xb_data_files_close();

  recv_sys_free();

  recv_sys_close();

  clone_free();

  trx_pool_close();

  lock_sys_close();

  os_thread_close();

  row_mysql_close();

  redo_mgr.close();

  dict_persist_close();

  sync_check_close();

  os_event_global_destroy();

  xb_keyring_shutdown();

  cleanup_mysql_environment();
}

```


###


