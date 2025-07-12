---
title: init
description: init
date: 2023-10-30
weight: 10000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


| 配置                  | 参数          | 备注         |
| :-------------------- | :------------ | :----------- |
| version               | 8.0.32        | 版本         |
| engine                | innodb        | 存储引擎     |
| transaction_isolation | read commited | 事务隔离级别 |


{{<alert>}}

- `innobase_hton` 对象初始化
- 初始化`innodb`参数：`innodb_init_params`

{{</alert>}}


## innodb_init
```c++
/** Initialize the InnoDB storage engine plugin.
@param[in,out]  p       InnoDB handlerton
@return error code
@retval 0 on success */
static int innodb_init(void *p) {
  DBUG_TRACE;

  acquire_plugin_services();

  handlerton *innobase_hton = (handlerton *)p;
  innodb_hton_ptr = innobase_hton;

  innobase_hton->state = SHOW_OPTION_YES;
  innobase_hton->db_type = DB_TYPE_INNODB;
  innobase_hton->savepoint_offset = sizeof(trx_named_savept_t);
  innobase_hton->close_connection = innobase_close_connection;
  innobase_hton->kill_connection = innobase_kill_connection;
  innobase_hton->savepoint_set = innobase_savepoint;
  innobase_hton->savepoint_rollback = innobase_rollback_to_savepoint;

  innobase_hton->savepoint_rollback_can_release_mdl =
      innobase_rollback_to_savepoint_can_release_mdl;

  innobase_hton->savepoint_release = innobase_release_savepoint;
  innobase_hton->commit = innobase_commit;
  innobase_hton->rollback = innobase_rollback;
  innobase_hton->prepare = innobase_xa_prepare;
  innobase_hton->recover = innobase_xa_recover;
  innobase_hton->recover_prepared_in_tc = innobase_xa_recover_prepared_in_tc;
  innobase_hton->commit_by_xid = innobase_commit_by_xid;
  innobase_hton->rollback_by_xid = innobase_rollback_by_xid;
  innobase_hton->set_prepared_in_tc = innobase_set_prepared_in_tc;
  innobase_hton->set_prepared_in_tc_by_xid = innobase_set_prepared_in_tc_by_xid;
  innobase_hton->create = innobase_create_handler;
  innobase_hton->is_valid_tablespace_name = innobase_is_valid_tablespace_name;
  innobase_hton->alter_tablespace = innobase_alter_tablespace;
  innobase_hton->get_tablespace_filename_ext =
      innobase_get_tablespace_filename_ext;
  innobase_hton->upgrade_tablespace = dd_upgrade_tablespace;
  innobase_hton->upgrade_space_version = upgrade_space_version;
  innobase_hton->upgrade_logs = dd_upgrade_logs;
  innobase_hton->finish_upgrade = dd_upgrade_finish;
  innobase_hton->pre_dd_shutdown = innodb_pre_dd_shutdown;
  innobase_hton->panic = innodb_shutdown;
  innobase_hton->partition_flags = innobase_partition_flags;

  innobase_hton->start_consistent_snapshot =
      innobase_start_trx_and_assign_read_view;

  innobase_hton->flush_logs = innobase_flush_logs;
  innobase_hton->show_status = innobase_show_status;
  innobase_hton->lock_hton_log = innobase_lock_hton_log;
  innobase_hton->unlock_hton_log = innobase_unlock_hton_log;
  innobase_hton->collect_hton_log_info = innobase_collect_hton_log_info;
  innobase_hton->fill_is_table = innobase_fill_i_s_table;
  innobase_hton->flags = HTON_SUPPORTS_EXTENDED_KEYS |
                         HTON_SUPPORTS_FOREIGN_KEYS | HTON_SUPPORTS_ATOMIC_DDL |
                         HTON_CAN_RECREATE | HTON_SUPPORTS_SECONDARY_ENGINE |
                         HTON_SUPPORTS_TABLE_ENCRYPTION |
                         HTON_SUPPORTS_GENERATED_INVISIBLE_PK;

  innobase_hton->replace_native_transaction_in_thd = innodb_replace_trx_in_thd;
  innobase_hton->file_extensions = ha_innobase_exts;
  innobase_hton->data = &innodb_api_cb;

  innobase_hton->ddse_dict_init = innobase_ddse_dict_init;

  innobase_hton->dict_register_dd_table_id = innobase_dict_register_dd_table_id;

  innobase_hton->dict_cache_reset = innobase_dict_cache_reset;
  innobase_hton->dict_cache_reset_tables_and_tablespaces =
      innobase_dict_cache_reset_tables_and_tablespaces;

  innobase_hton->dict_recover = innobase_dict_recover;
  innobase_hton->dict_get_server_version = innobase_dict_get_server_version;
  innobase_hton->dict_set_server_version = innobase_dict_set_server_version;

  innobase_hton->post_recover = innobase_post_recover;

  innobase_hton->is_supported_system_table = innobase_is_supported_system_table;

  innobase_hton->get_table_statistics = innobase_get_table_statistics;

  innobase_hton->get_index_column_cardinality =
      innobase_get_index_column_cardinality;

  innobase_hton->get_tablespace_statistics = innobase_get_tablespace_statistics;
  innobase_hton->get_tablespace_type = innobase_get_tablespace_type;
  innobase_hton->get_tablespace_type_by_name =
      innobase_get_tablespace_type_by_name;

  innobase_hton->is_dict_readonly = innobase_is_dict_readonly;

  innobase_hton->sdi_create = dict_sdi_create;
  innobase_hton->sdi_drop = dict_sdi_drop;
  innobase_hton->sdi_get_keys = dict_sdi_get_keys;
  innobase_hton->sdi_get = dict_sdi_get;
  innobase_hton->sdi_set = dict_sdi_set;
  innobase_hton->sdi_delete = dict_sdi_delete;

  innobase_hton->rotate_encryption_master_key =
      innobase_encryption_key_rotation;

  innobase_hton->redo_log_set_state = innobase_redo_set_state;

  innobase_hton->post_ddl = innobase_post_ddl;

  /* Initialize handler clone interfaces for. */

  innobase_hton->clone_interface.clone_capability = innodb_clone_get_capability;
  innobase_hton->clone_interface.clone_begin = innodb_clone_begin;
  innobase_hton->clone_interface.clone_copy = innodb_clone_copy;
  innobase_hton->clone_interface.clone_ack = innodb_clone_ack;
  innobase_hton->clone_interface.clone_end = innodb_clone_end;

  innobase_hton->clone_interface.clone_apply_begin = innodb_clone_apply_begin;
  innobase_hton->clone_interface.clone_apply = innodb_clone_apply;
  innobase_hton->clone_interface.clone_apply_end = innodb_clone_apply_end;

  innobase_hton->foreign_keys_flags =
      HTON_FKS_WITH_PREFIX_PARENT_KEYS |
      HTON_FKS_NEED_DIFFERENT_PARENT_AND_SUPPORTING_KEYS |
      HTON_FKS_WITH_EXTENDED_PARENT_KEYS;

  innobase_hton->check_fk_column_compat = innodb_check_fk_column_compat;
  innobase_hton->fk_name_suffix = {STRING_WITH_LEN("_ibfk_")};

  innobase_hton->is_reserved_db_name = innobase_check_reserved_file_name;

  innobase_hton->page_track.start = innobase_page_track_start;
  innobase_hton->page_track.stop = innobase_page_track_stop;
  innobase_hton->page_track.purge = innobase_page_track_purge;
  innobase_hton->page_track.get_page_ids = innobase_page_track_get_page_ids;
  innobase_hton->page_track.get_num_page_ids =
      innobase_page_track_get_num_page_ids;
  innobase_hton->page_track.get_status = innobase_page_track_get_status;

  static_assert(DATA_MYSQL_TRUE_VARCHAR == (ulint)MYSQL_TYPE_VARCHAR);

  os_file_set_umask(my_umask);

  /* Setup the memory alloc/free tracing mechanisms before calling
  any functions that could possibly allocate memory. */
  ut_new_boot();

#ifdef HAVE_PSI_INTERFACE
  /* Register keys with MySQL performance schema */
  int count;

#ifdef UNIV_DEBUG
  /** Count of Performance Schema keys that have been registered. */
  int global_count = 0;
#endif /* UNIV_DEBUG */

  count = static_cast<int>(array_elements(all_pthread_mutexes));
  mysql_mutex_register("innodb", all_pthread_mutexes, count);

#ifdef UNIV_DEBUG
  global_count += count;
#endif /* UNIV_DEBUG */

#ifdef UNIV_PFS_MEMORY
  count = static_cast<int>(array_elements(pfs_instrumented_innodb_memory));
  mysql_memory_register("innodb", pfs_instrumented_innodb_memory, count);
#endif /* UNIV_PFS_MEMORY */

#ifdef UNIV_PFS_MUTEX
  count = static_cast<int>(array_elements(all_innodb_mutexes));
  mysql_mutex_register("innodb", all_innodb_mutexes, count);

#ifdef UNIV_DEBUG
  global_count += count;
#endif /* UNIV_DEBUG */

#endif /* UNIV_PFS_MUTEX */

#ifdef UNIV_PFS_RWLOCK
  count = static_cast<int>(array_elements(all_innodb_rwlocks));
  mysql_rwlock_register("innodb", all_innodb_rwlocks, count);

#ifdef UNIV_DEBUG
  global_count += count;
#endif /* UNIV_DEBUG */

#endif /* UNIV_PFS_MUTEX */

#ifdef UNIV_PFS_THREAD
  count = static_cast<int>(array_elements(all_innodb_threads));
  mysql_thread_register("innodb", all_innodb_threads, count);

#ifdef UNIV_DEBUG
  global_count += count;
#endif /* UNIV_DEBUG */

#endif /* UNIV_PFS_THREAD */

#ifdef UNIV_PFS_IO
  count = static_cast<int>(array_elements(all_innodb_files));
  mysql_file_register("innodb", all_innodb_files, count);

#ifdef UNIV_DEBUG
  global_count += count;
#endif /* UNIV_DEBUG */

#endif /* UNIV_PFS_IO */

  count = static_cast<int>(array_elements(all_innodb_conds));
  mysql_cond_register("innodb", all_innodb_conds, count);

#ifdef UNIV_DEBUG
  global_count += count;
#endif /* UNIV_DEBUG */

  mysql_data_lock_register(&innodb_data_lock_inspector);

#ifdef UNIV_DEBUG
  if (mysql_pfs_key_t::get_count() != global_count) {
    ib::error(ER_IB_MSG_544) << "You have created new InnoDB PFS key(s) but "
                             << mysql_pfs_key_t::get_count() - global_count
                             << " key(s) is/are not registered with PFS. Please"
                             << " register the keys in PFS arrays in"
                             << " ha_innodb.cc.";

    return HA_ERR_INITIALIZATION;
  }
#endif /* UNIV_DEBUG */

#endif /* HAVE_PSI_INTERFACE */

  os_event_global_init();

  if (int error = innodb_init_params()) {
    return error;
  }

  /* After this point, error handling has to use
  innodb_init_abort(). */

  /* Initialize component service handles */
  if (innobase::component_services::intitialize_service_handles() == false) {
    return innodb_init_abort();
  }

  if (!srv_sys_space.parse_params(innobase_data_file_path, true)) {
    ib::error(ER_IB_MSG_545)
        << "Unable to parse innodb_data_file_path=" << innobase_data_file_path;
    return innodb_init_abort();
  }

  if (!srv_tmp_space.parse_params(innobase_temp_data_file_path, false)) {
    ib::error(ER_IB_MSG_546) << "Unable to parse innodb_temp_data_file_path="
                             << innobase_temp_data_file_path;
    return innodb_init_abort();
  }

  /* Perform all sanity check before we take action of deleting files*/
  if (srv_sys_space.intersection(&srv_tmp_space)) {
    log_errlog(ERROR_LEVEL, ER_INNODB_FILES_SAME, srv_tmp_space.name(),
               srv_sys_space.name());
    return innodb_init_abort();
  }

  /* Check for keyring plugin if UNDO/REDO logs are intended to be encrypted */
  if ((srv_undo_log_encrypt || srv_redo_log_encrypt) &&
      Encryption::check_keyring() == false) {
    return innodb_init_abort();
  }

  return 0;
}
```






### innodb_init_params
> 初始化innodb参数

```c++
/** Initialize, validate and normalize the InnoDB startup parameters.
@return failure code
@retval 0 on success
@retval HA_ERR_OUT_OF_MEM       when out of memory
@retval HA_ERR_INITIALIZATION   when some parameters are out of range */
static int innodb_init_params() {
  DBUG_TRACE;

  static char current_dir[3];
  char *default_path;

  /* First calculate the default path for innodb_data_home_dir etc.,
  in case the user has not given any value. */

  /* It's better to use current lib, to keep paths short */
  current_dir[0] = FN_CURLIB;
  current_dir[1] = FN_LIBCHAR;
  current_dir[2] = 0;
  default_path = current_dir;

  std::string mysqld_datadir{default_path};

  MySQL_datadir_path = Fil_path{mysqld_datadir};

  /* Validate, normalize and interpret the InnoDB start-up parameters. */

  /* The default dir for data files is the datadir of MySQL */

  srv_data_home =
      (innobase_data_home_dir == nullptr || *innobase_data_home_dir == '\0')
          ? default_path
          : innobase_data_home_dir;
  Fil_path::normalize(srv_data_home);

  /* Validate the undo directory. */
  if (srv_undo_dir == nullptr || srv_undo_dir[0] == 0) {
    srv_undo_dir = default_path;
  } else {
    Fil_path::normalize(srv_undo_dir);
  }

  MySQL_undo_path = Fil_path{srv_undo_dir};

  if (MySQL_undo_path.is_ancestor(default_path)) {
    log_errlog(ERROR_LEVEL, ER_INNODB_INVALID_INNODB_UNDO_DIRECTORY_LOCATION);
    return HA_ERR_INITIALIZATION;
  }

  /* Validate the temp directory */
  if (ibt::srv_temp_dir == nullptr) {
    ibt::srv_temp_dir = default_path;
  } else {
    os_file_type_t type;
    bool exists;
    os_file_status(ibt::srv_temp_dir, &exists, &type);
    if (!exists || type != OS_FILE_TYPE_DIR) {
      ib::error(ER_IB_ERR_TEMP_TABLESPACE_DIR_DOESNT_EXIST)
          << "Invalid innodb_temp_tablespaces_dir: " << ibt::srv_temp_dir
          << ". Directory doesn't exist or not valid";
      return HA_ERR_INITIALIZATION;
    }

    Fil_path temp_dir(ibt::srv_temp_dir);
    if (temp_dir.path().empty()) {
      ib::error(ER_IB_ERR_TEMP_TABLESPACE_DIR_EMPTY)
          << "Invalid innodb_temp_tablespaces dir: " << ibt::srv_temp_dir
          << ". Path cannot be empty";
      return HA_ERR_INITIALIZATION;
    }

    if (strchr(ibt::srv_temp_dir, ';')) {
      ib::error(ER_IB_ERR_TEMP_TABLESPACE_DIR_CONTAINS_SEMICOLON)
          << "Invalid innodb_temp_tablespaces dir: " << ibt::srv_temp_dir
          << ". Path cannot contain ;";
      return HA_ERR_INITIALIZATION;
    }

    if (MySQL_datadir_path.is_ancestor(
            Fil_path::get_real_path(temp_dir.path()))) {
      ib::error(ER_IB_ERR_TEMP_TABLESPACE_DIR_SUBDIR_OF_DATADIR)
          << "Invalid innodb_temp_tablespaces_dir=" << ibt::srv_temp_dir
          << ". This path should not be a subdirectory of the datadir.";
      return HA_ERR_INITIALIZATION;
    }
  }

  Fil_path::normalize(ibt::srv_temp_dir);

  /* The default dir for log files is the datadir of MySQL */

  if (srv_log_group_home_dir == nullptr) {
    srv_log_group_home_dir = default_path;
  }
  Fil_path::normalize(srv_log_group_home_dir);

  if (strchr(srv_log_group_home_dir, ';')) {
    log_errlog(ERROR_LEVEL, ER_INNODB_INVALID_LOG_GROUP_HOME_DIR);
    return HA_ERR_INITIALIZATION;
  }

  if (strchr(srv_undo_dir, ';')) {
    log_errlog(ERROR_LEVEL, ER_INNODB_INVALID_INNODB_UNDO_DIRECTORY);
    return HA_ERR_INITIALIZATION;
  }

  if (!is_filename_allowed(srv_buf_dump_filename, strlen(srv_buf_dump_filename),
                           false)) {
    log_errlog(ERROR_LEVEL, ER_INNODB_ILLEGAL_COLON_IN_POOL);
    return HA_ERR_INITIALIZATION;
  }

  /* Check that the value of system variable innodb_page_size was
  set correctly.  Its value was put into srv_page_size. If valid,
  return the associated srv_page_size_shift. */
  srv_page_size_shift = page_size_validate(srv_page_size);
  if (!srv_page_size_shift) {
    log_errlog(ERROR_LEVEL, ER_INNODB_INVALID_PAGE_SIZE, srv_page_size);
    return HA_ERR_INITIALIZATION;
  }

  ut_a(srv_log_buffer_size % OS_FILE_LOG_BLOCK_SIZE == 0);
  ut_a(srv_log_buffer_size > 0);

  ut_a(srv_log_write_ahead_size % OS_FILE_LOG_BLOCK_SIZE == 0);
  ut_a(srv_log_write_ahead_size > 0);

  assert(innodb_change_buffering <= IBUF_USE_ALL);

  /* Check that interdependent parameters have sane values. */
  if (srv_max_buf_pool_modified_pct < srv_max_dirty_pages_pct_lwm) {
    log_errlog(WARNING_LEVEL, ER_INNODB_DIRTY_WATER_MARK_NOT_LOW,
               srv_max_buf_pool_modified_pct);
    srv_max_dirty_pages_pct_lwm = srv_max_buf_pool_modified_pct;
  }

  if (srv_max_io_capacity == SRV_MAX_IO_CAPACITY_DUMMY_DEFAULT) {
    if (srv_io_capacity >= SRV_MAX_IO_CAPACITY_LIMIT / 2) {
      /* Avoid overflow. */
      srv_max_io_capacity = SRV_MAX_IO_CAPACITY_LIMIT;
    } else {
      /* The user has not set the value. We should
      set it based on innodb_io_capacity. */
      srv_max_io_capacity = std::max(2 * srv_io_capacity, 2000UL);
    }

  } else if (srv_max_io_capacity < srv_io_capacity) {
    log_errlog(WARNING_LEVEL, ER_INNODB_IO_CAPACITY_EXCEEDS_MAX,
               srv_max_io_capacity);
    srv_io_capacity = srv_max_io_capacity;
  }

  if (UNIV_PAGE_SIZE_DEF != srv_page_size) {
    ib::warn(ER_IB_MSG_538)
        << "innodb-page-size has been changed from the"
           " default value "
        << UNIV_PAGE_SIZE_DEF << " to " << srv_page_size << ".";
  }

  if (srv_log_write_ahead_size > srv_page_size) {
    srv_log_write_ahead_size = srv_page_size;
  } else {
    ulong srv_log_write_ahead_size_tmp = OS_FILE_LOG_BLOCK_SIZE;

    while (srv_log_write_ahead_size_tmp < srv_log_write_ahead_size) {
      srv_log_write_ahead_size_tmp = srv_log_write_ahead_size_tmp * 2;
    }
    if (srv_log_write_ahead_size_tmp != srv_log_write_ahead_size) {
      srv_log_write_ahead_size = srv_log_write_ahead_size_tmp / 2;
    }
  }

  srv_buf_pool_size = srv_buf_pool_curr_size;

  innodb_log_checksums_func_update(srv_log_checksums);

#ifdef HAVE_LINUX_LARGE_PAGES
  if ((os_use_large_pages = opt_large_pages)) {
    os_large_page_size = opt_large_page_size;
  }
#endif /* HAVE_LINUX_LARGE_PAGES */

  row_rollback_on_timeout = innobase_rollback_on_timeout;

  if (innobase_open_files < 10) {
    innobase_open_files = 300;
    if (srv_file_per_table && table_cache_size > 300) {
      innobase_open_files = table_cache_size;
    }
  }

  if (innobase_open_files > (long)open_files_limit) {
    ib::warn(ER_IB_MSG_539) << "innodb_open_files should not be greater"
                               " than the open_files_limit.\n";
    if (innobase_open_files > (long)table_cache_size) {
      innobase_open_files = table_cache_size;
    }
  }

  srv_innodb_status = innobase_create_status_file;

  /* Round up ddl:fts_parser_threads to nearest power of 2 number */
  {
    ulong n_parser_threads = 1;

    while (n_parser_threads < ddl::fts_parser_threads) {
      n_parser_threads <<= 1;
    }

    ddl::fts_parser_threads = n_parser_threads;
  }

  /* Store the default charset-collation number of this MySQL
  installation */

  data_mysql_default_charset_coll = (ulint)default_charset_info->number;

  innobase_commit_concurrency_init_default();

  if (srv_force_recovery == SRV_FORCE_NO_LOG_REDO) {
    srv_read_only_mode = true;
  }

  high_level_read_only =
      srv_read_only_mode || srv_force_recovery > SRV_FORCE_NO_TRX_UNDO;

  if (srv_read_only_mode) {
    ib::info(ER_IB_MSG_540) << "Started in read only mode";

    /* There is no write except to intrinsic table and so turn-off
    doublewrite mechanism completely. */
    dblwr::g_mode = dblwr::Mode::OFF;
  }

#ifdef LINUX_NATIVE_AIO
  if (srv_use_native_aio) {
    ib::info(ER_IB_MSG_541) << "Using Linux native AIO";
  }
#elif !defined _WIN32
  /* Currently native AIO is supported only on Windows and Linux
  and that also when the support is compiled in. In all other
  cases, we ignore the setting of innodb_use_native_aio. */
  srv_use_native_aio = false;
#endif

#ifndef _WIN32
  /* Check if innodb_dedicated_server == ON and O_DIRECT is supported */
  if (srv_dedicated_server && sysvar_source_svc != nullptr &&
      os_is_o_direct_supported()) {
    static const char *variable_name = "innodb_flush_method";
    enum enum_variable_source source;

    if (!sysvar_source_svc->get(variable_name, strlen(variable_name),
                                &source)) {
      /* If innodb_flush_method is not specified explicitly */
      if (source == COMPILED) {
        innodb_flush_method = static_cast<ulong>(SRV_UNIX_O_DIRECT_NO_FSYNC);
      } else {
        ib::warn(ER_IB_MSG_542)
            << "Option innodb_dedicated_server"
               " is ignored for innodb_flush_method"
               "because innodb_flush_method="
            << innodb_flush_method_names[innodb_flush_method]
            << " is specified explicitly.";
      }
    }
  }

  srv_unix_file_flush_method =
      static_cast<srv_unix_flush_t>(innodb_flush_method);
  ut_ad(innodb_flush_method <= SRV_UNIX_O_DIRECT_NO_FSYNC);
#else
  srv_win_file_flush_method = static_cast<srv_win_flush_t>(innodb_flush_method);
  ut_ad(innodb_flush_method <= SRV_WIN_IO_NORMAL);
  if (srv_use_native_aio) {
    ib::info(ER_IB_MSG_541) << "Using Windows native AIO";
  }
#endif /* !_WIN32 */

  /* Set the maximum number of threads which can wait for a semaphore
  inside InnoDB: this is the 'sync wait array' size, as well as the
  maximum number of threads that can wait in the 'srv_conc array' for
  their time to enter InnoDB. */

  srv_max_n_threads = 100 * 1024;

  /* This is the first time univ_page_size is used.
  It was initialized to 16k pages before srv_page_size was set */
  univ_page_size.copy_from(page_size_t(srv_page_size, srv_page_size, false));

  srv_sys_space.set_space_id(TRX_SYS_SPACE);

  /* Create the filespace flags. */
  predefined_flags = fsp_flags_init(univ_page_size, false, false, true, false);
  fsp_flags_set_sdi(predefined_flags);

  srv_sys_space.set_flags(predefined_flags);

  srv_sys_space.set_name(dict_sys_t::s_sys_space_name);
  srv_sys_space.set_path(srv_data_home);

  /* We set the temporary tablspace id later, after recovery.
  The temp tablespace doesn't support raw devices.
  Set the name and path. */
  srv_tmp_space.set_name(dict_sys_t::s_temp_space_name);
  srv_tmp_space.set_path(srv_data_home);

  /* Create the filespace flags with the temp flag set. */
  uint32_t fsp_flags =
      fsp_flags_init(univ_page_size, false, false, false, true);
  srv_tmp_space.set_flags(fsp_flags);

  /* Set buffer pool size to default for fast startup when mysqld is
  run with --help --verbose options. */
  ulint srv_buf_pool_size_org = 0;
  if (opt_help && opt_verbose && opt_validate_config &&
      srv_buf_pool_size > srv_buf_pool_def_size) {
    ib::warn(ER_IB_MSG_543) << "Setting innodb_buf_pool_size to "
                            << srv_buf_pool_def_size << " for fast startup, "
                            << "when running with --help --verbose options.";
    srv_buf_pool_size_org = srv_buf_pool_size;
    srv_buf_pool_size = srv_buf_pool_def_size;
  }

  innodb_buffer_pool_size_init();

  innodb_undo_tablespaces_deprecate();

  innodb_redo_log_capacity_init();

  /* Set the original value back to show in help. */
  if (srv_buf_pool_size_org != 0) {
    srv_buf_pool_size_org = buf_pool_size_align(srv_buf_pool_size_org);
    srv_buf_pool_curr_size = srv_buf_pool_size_org;
  }

  if (srv_n_page_cleaners > srv_buf_pool_instances) {
    /* limit of page_cleaner parallelizability
    is number of buffer pool instances. */
    srv_n_page_cleaners = srv_buf_pool_instances;
  }

  srv_lock_table_size = 5 * (srv_buf_pool_size / UNIV_PAGE_SIZE);

  return 0;
}
```



### innodb_init_abort
```c++
/** Free any resources that were allocated and return failure.
@return always return 1 */
static int innodb_init_abort() {
  DBUG_TRACE;
  srv_shutdown_exit_threads();
  innodb_space_shutdown();
  innobase::component_services::deinitialize_service_handles();
  release_plugin_services();
  return 1;
}
```

















