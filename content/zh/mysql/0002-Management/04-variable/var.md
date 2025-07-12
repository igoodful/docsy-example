---
title: mysql-8.0.32
description: 8.0.32所有变量
date: 2023-10-25
weight: 2000


---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{<alert>}}

配置文件

{{</alert>}}




## 一、变量作用范围

- GLOBAL ：全局变量，影响服务器的整体操作。
- SESSION ：会话变量，影响某个客户端连接的操作。



#### 1. 只全局变量



#### 2. 只会话变量


#### 3. 全局变量和会话变量


#### 4. 只读变量


#### 可动态修改



#### 可修改但需重启


## 二、8.0.32所有变量

| Variable_name                                            | Value                                     |
| :------------------------------------------------------- | :---------------------------------------- |
| activate_all_roles_on_login                              | OFF                                       |
| admin_address                                            |                                           |
| admin_port                                               | 33062                                     |
| admin_ssl_ca                                             |                                           |
| admin_ssl_capath                                         |                                           |
| admin_ssl_cert                                           |                                           |
| admin_ssl_cipher                                         |                                           |
| admin_ssl_crl                                            |                                           |
| admin_ssl_crlpath                                        |                                           |
| admin_ssl_key                                            |                                           |
| admin_tls_ciphersuites                                   |                                           |
| admin_tls_version                                        | TLSv1.2,TLSv1.3                           |
| authentication_policy                                    | *,,                                       |
| auto_generate_certs                                      | ON                                        |
| auto_increment_increment                                 | 1                                         |
| auto_increment_offset                                    | 1                                         |
| autocommit                                               | ON                                        |
| automatic_sp_privileges                                  | ON                                        |
| avoid_temporal_upgrade                                   | OFF                                       |
| back_log                                                 | 5000                                      |
| basedir                                                  | /home/work/mysql_3306/                    |
| big_tables                                               | OFF                                       |
| bind_address                                             | *                                         |
| binlog_cache_size                                        | 2097152                                   |
| binlog_checksum                                          | NONE                                      |
| binlog_direct_non_transactional_updates                  | OFF                                       |
| binlog_encryption                                        | OFF                                       |
| binlog_error_action                                      | ABORT_SERVER                              |
| binlog_expire_logs_auto_purge                            | ON                                        |
| binlog_expire_logs_seconds                               | 1296000                                   |
| binlog_format                                            | ROW                                       |
| binlog_group_commit_sync_delay                           | 0                                         |
| binlog_group_commit_sync_no_delay_count                  | 0                                         |
| binlog_gtid_simple_recovery                              | ON                                        |
| binlog_max_flush_queue_time                              | 0                                         |
| binlog_order_commits                                     | ON                                        |
| binlog_rotate_encryption_master_key_at_startup           | OFF                                       |
| binlog_row_event_max_size                                | 8192                                      |
| binlog_row_image                                         | FULL                                      |
| binlog_row_metadata                                      | MINIMAL                                   |
| binlog_row_value_options                                 |                                           |
| binlog_rows_query_log_events                             | ON                                        |
| binlog_stmt_cache_size                                   | 32768                                     |
| binlog_transaction_compression                           | OFF                                       |
| binlog_transaction_compression_level_zstd                | 3                                         |
| binlog_transaction_dependency_history_size               | 25000                                     |
| binlog_transaction_dependency_tracking                   | WRITESET                                  |
| block_encryption_mode                                    | aes-128-ecb                               |
| build_id                                                 | 1425bacc6b18c65b3de5d3837d7022862ee3e6c5  |
| bulk_insert_buffer_size                                  | 8388608                                   |
| caching_sha2_password_auto_generate_rsa_keys             | ON                                        |
| caching_sha2_password_digest_rounds                      | 5000                                      |
| caching_sha2_password_private_key_path                   | private_key.pem                           |
| caching_sha2_password_public_key_path                    | public_key.pem                            |
| character_set_client                                     | utf8mb4                                   |
| character_set_connection                                 | utf8mb4                                   |
| character_set_database                                   | utf8mb4                                   |
| character_set_filesystem                                 | binary                                    |
| character_set_results                                    | utf8mb4                                   |
| character_set_server                                     | utf8mb4                                   |
| character_set_system                                     | utf8mb3                                   |
| character_sets_dir                                       | /home/work/mysql_3306/share/charsets/     |
| check_proxy_users                                        | OFF                                       |
| clone_autotune_concurrency                               | ON                                        |
| clone_block_ddl                                          | OFF                                       |
| clone_buffer_size                                        | 4194304                                   |
| clone_ddl_timeout                                        | 300                                       |
| clone_delay_after_data_drop                              | 0                                         |
| clone_donor_timeout_after_network_failure                | 5                                         |
| clone_enable_compression                                 | OFF                                       |
| clone_max_concurrency                                    | 16                                        |
| clone_max_data_bandwidth                                 | 0                                         |
| clone_max_network_bandwidth                              | 0                                         |
| clone_ssl_ca                                             |                                           |
| clone_ssl_cert                                           |                                           |
| clone_ssl_key                                            |                                           |
| clone_valid_donor_list                                   |                                           |
| collation_connection                                     | utf8mb4_0900_ai_ci                        |
| collation_database                                       | utf8mb4_0900_ai_ci                        |
| collation_server                                         | utf8mb4_0900_ai_ci                        |
| completion_type                                          | NO_CHAIN                                  |
| concurrent_insert                                        | AUTO                                      |
| connect_timeout                                          | 10                                        |
| connection_memory_chunk_size                             | 8912                                      |
| connection_memory_limit                                  | 18446744073709551615                      |
| core_file                                                | OFF                                       |
| create_admin_listener_thread                             | OFF                                       |
| cte_max_recursion_depth                                  | 1000                                      |
| datadir                                                  | /home/work/mysql_3306/data/               |
| default_authentication_plugin                            | mysql_native_password                     |
| default_collation_for_utf8mb4                            | utf8mb4_0900_ai_ci                        |
| default_password_lifetime                                | 0                                         |
| default_storage_engine                                   | InnoDB                                    |
| default_table_encryption                                 | OFF                                       |
| default_tmp_storage_engine                               | InnoDB                                    |
| default_week_format                                      | 0                                         |
| delay_key_write                                          | ON                                        |
| delayed_insert_limit                                     | 100                                       |
| delayed_insert_timeout                                   | 300                                       |
| delayed_queue_size                                       | 1000                                      |
| disabled_storage_engines                                 |                                           |
| disconnect_on_expired_password                           | ON                                        |
| div_precision_increment                                  | 4                                         |
| end_markers_in_json                                      | OFF                                       |
| enforce_gtid_consistency                                 | ON                                        |
| eq_range_index_dive_limit                                | 200                                       |
| event_scheduler                                          | ON                                        |
| expire_logs_days                                         | 0                                         |
| explain_format                                           | TRADITIONAL                               |
| explicit_defaults_for_timestamp                          | ON                                        |
| flush                                                    | OFF                                       |
| flush_time                                               | 0                                         |
| foreign_key_checks                                       | ON                                        |
| ft_boolean_syntax                                        | + -><()~*:""&                             |  |
| ft_max_word_len                                          | 84                                        |
| ft_min_word_len                                          | 4                                         |
| ft_query_expansion_limit                                 | 20                                        |
| ft_stopword_file                                         | (built-in)                                |
| general_log                                              | OFF                                       |
| general_log_file                                         | /home/work/mysql_3306/log/general.log     |
| generated_random_password_length                         | 20                                        |
| global_connection_memory_limit                           | 18446744073709551615                      |
| global_connection_memory_tracking                        | OFF                                       |
| group_concat_max_len                                     | 102400                                    |
| group_replication_consistency                            | EVENTUAL                                  |
| gtid_executed                                            | c0b23b93-4400-11ef-a959-00163e6d1d38:1-54 |
| gtid_executed_compression_period                         | 0                                         |
| gtid_mode                                                | ON                                        |
| gtid_owned                                               |                                           |
| gtid_purged                                              |                                           |
| have_compress                                            | YES                                       |
| have_dynamic_loading                                     | YES                                       |
| have_geometry                                            | YES                                       |
| have_openssl                                             | DISABLED                                  |
| have_profiling                                           | YES                                       |
| have_query_cache                                         | NO                                        |
| have_rtree_keys                                          | YES                                       |
| have_ssl                                                 | DISABLED                                  |
| have_statement_timeout                                   | YES                                       |
| have_symlink                                             | DISABLED                                  |
| histogram_generation_max_mem_size                        | 20000000                                  |
| host_cache_size                                          | 803                                       |
| hostname                                                 | node172.17.134.137                        |
| information_schema_stats_expiry                          | 86400                                     |
| init_connect                                             | SET NAMES utf8                            |
| init_file                                                |                                           |
| init_replica                                             |                                           |
| init_slave                                               |                                           |
| innodb_adaptive_flushing                                 | ON                                        |
| innodb_adaptive_flushing_lwm                             | 10                                        |
| innodb_adaptive_hash_index                               | OFF                                       |
| innodb_adaptive_hash_index_parts                         | 8                                         |
| innodb_adaptive_max_sleep_delay                          | 150000                                    |
| innodb_api_bk_commit_interval                            | 5                                         |
| innodb_api_disable_rowlock                               | OFF                                       |
| innodb_api_enable_binlog                                 | OFF                                       |
| innodb_api_enable_mdl                                    | OFF                                       |
| innodb_api_trx_level                                     | 0                                         |
| innodb_autoextend_increment                              | 64                                        |
| innodb_autoinc_lock_mode                                 | 2                                         |
| innodb_buffer_pool_chunk_size                            | 134217728                                 |
| innodb_buffer_pool_dump_at_shutdown                      | ON                                        |
| innodb_buffer_pool_dump_now                              | OFF                                       |
| innodb_buffer_pool_dump_pct                              | 25                                        |
| innodb_buffer_pool_filename                              | ib_buffer_pool                            |
| innodb_buffer_pool_in_core_file                          | ON                                        |
| innodb_buffer_pool_instances                             | 8                                         |
| innodb_buffer_pool_load_abort                            | OFF                                       |
| innodb_buffer_pool_load_at_startup                       | ON                                        |
| innodb_buffer_pool_load_now                              | OFF                                       |
| innodb_buffer_pool_size                                  | 4294967296                                |
| innodb_change_buffer_max_size                            | 25                                        |
| innodb_change_buffering                                  | all                                       |
| innodb_checksum_algorithm                                | crc32                                     |
| innodb_cmp_per_index_enabled                             | OFF                                       |
| innodb_commit_concurrency                                | 0                                         |
| innodb_compression_failure_threshold_pct                 | 5                                         |
| innodb_compression_level                                 | 6                                         |
| innodb_compression_pad_pct_max                           | 50                                        |
| innodb_concurrency_tickets                               | 5000                                      |
| innodb_data_file_path                                    | ibdata1:1024M:autoextend                  |
| innodb_data_home_dir                                     |                                           |
| innodb_ddl_buffer_size                                   | 1048576                                   |
| innodb_ddl_threads                                       | 4                                         |
| innodb_deadlock_detect                                   | ON                                        |
| innodb_dedicated_server                                  | OFF                                       |
| innodb_default_row_format                                | dynamic                                   |
| innodb_directories                                       |                                           |
| innodb_disable_sort_file_cache                           | OFF                                       |
| innodb_doublewrite                                       | ON                                        |
| innodb_doublewrite_batch_size                            | 0                                         |
| innodb_doublewrite_dir                                   |                                           |
| innodb_doublewrite_files                                 | 2                                         |
| innodb_doublewrite_pages                                 | 8                                         |
| innodb_extend_and_initialize                             | ON                                        |
| innodb_fast_shutdown                                     | 1                                         |
| innodb_file_per_table                                    | ON                                        |
| innodb_fill_factor                                       | 100                                       |
| innodb_flush_log_at_timeout                              | 1                                         |
| innodb_flush_log_at_trx_commit                           | 1                                         |
| innodb_flush_method                                      | O_DIRECT                                  |
| innodb_flush_neighbors                                   | 0                                         |
| innodb_flush_sync                                        | ON                                        |
| innodb_flushing_avg_loops                                | 30                                        |
| innodb_force_load_corrupted                              | OFF                                       |
| innodb_force_recovery                                    | 0                                         |
| innodb_fsync_threshold                                   | 0                                         |
| innodb_ft_aux_table                                      |                                           |
| innodb_ft_cache_size                                     | 8000000                                   |
| innodb_ft_enable_diag_print                              | OFF                                       |
| innodb_ft_enable_stopword                                | ON                                        |
| innodb_ft_max_token_size                                 | 84                                        |
| innodb_ft_min_token_size                                 | 3                                         |
| innodb_ft_num_word_optimize                              | 2000                                      |
| innodb_ft_result_cache_limit                             | 2000000000                                |
| innodb_ft_server_stopword_table                          |                                           |
| innodb_ft_sort_pll_degree                                | 2                                         |
| innodb_ft_total_cache_size                               | 640000000                                 |
| innodb_ft_user_stopword_table                            |                                           |
| innodb_idle_flush_pct                                    | 100                                       |
| innodb_io_capacity                                       | 20000                                     |
| innodb_io_capacity_max                                   | 40000                                     |
| innodb_lock_wait_timeout                                 | 20                                        |
| innodb_log_buffer_size                                   | 33554432                                  |
| innodb_log_checksums                                     | ON                                        |
| innodb_log_compressed_pages                              | ON                                        |
| innodb_log_file_size                                     | 1073741824                                |
| innodb_log_files_in_group                                | 4                                         |
| innodb_log_group_home_dir                                | ./                                        |
| innodb_log_spin_cpu_abs_lwm                              | 80                                        |
| innodb_log_spin_cpu_pct_hwm                              | 50                                        |
| innodb_log_wait_for_flush_spin_hwm                       | 400                                       |
| innodb_log_write_ahead_size                              | 8192                                      |
| innodb_log_writer_threads                                | ON                                        |
| innodb_lru_scan_depth                                    | 1024                                      |
| innodb_max_dirty_pages_pct                               | 90.000000                                 |
| innodb_max_dirty_pages_pct_lwm                           | 10.000000                                 |
| innodb_max_purge_lag                                     | 0                                         |
| innodb_max_purge_lag_delay                               | 0                                         |
| innodb_max_undo_log_size                                 | 2147483648                                |
| innodb_monitor_disable                                   |                                           |
| innodb_monitor_enable                                    | all                                       |
| innodb_monitor_reset                                     |                                           |
| innodb_monitor_reset_all                                 |                                           |
| innodb_numa_interleave                                   | ON                                        |
| innodb_old_blocks_pct                                    | 37                                        |
| innodb_old_blocks_time                                   | 1000                                      |
| innodb_online_alter_log_max_size                         | 2147483648                                |
| innodb_open_files                                        | 40960                                     |
| innodb_optimize_fulltext_only                            | OFF                                       |
| innodb_page_cleaners                                     | 8                                         |
| innodb_page_size                                         | 16384                                     |
| innodb_parallel_read_threads                             | 16                                        |
| innodb_print_all_deadlocks                               | ON                                        |
| innodb_print_ddl_logs                                    | OFF                                       |
| innodb_purge_batch_size                                  | 300                                       |
| innodb_purge_rseg_truncate_frequency                     | 16                                        |
| innodb_purge_threads                                     | 4                                         |
| innodb_random_read_ahead                                 | OFF                                       |
| innodb_read_ahead_threshold                              | 56                                        |
| innodb_read_io_threads                                   | 8                                         |
| innodb_read_only                                         | OFF                                       |
| innodb_redo_log_archive_dirs                             |                                           |
| innodb_redo_log_capacity                                 | 20971520                                  |
| innodb_redo_log_encrypt                                  | OFF                                       |
| innodb_replication_delay                                 | 0                                         |
| innodb_rollback_on_timeout                               | ON                                        |
| innodb_rollback_segments                                 | 128                                       |
| innodb_segment_reserve_factor                            | 12.500000                                 |
| innodb_sort_buffer_size                                  | 1048576                                   |
| innodb_spin_wait_delay                                   | 6                                         |
| innodb_spin_wait_pause_multiplier                        | 50                                        |
| innodb_stats_auto_recalc                                 | ON                                        |
| innodb_stats_include_delete_marked                       | OFF                                       |
| innodb_stats_method                                      | nulls_equal                               |
| innodb_stats_on_metadata                                 | OFF                                       |
| innodb_stats_persistent                                  | ON                                        |
| innodb_stats_persistent_sample_pages                     | 20                                        |
| innodb_stats_transient_sample_pages                      | 8                                         |
| innodb_status_output                                     | OFF                                       |
| innodb_status_output_locks                               | OFF                                       |
| innodb_strict_mode                                       | ON                                        |
| innodb_sync_array_size                                   | 1                                         |
| innodb_sync_spin_loops                                   | 30                                        |
| innodb_table_locks                                       | ON                                        |
| innodb_temp_data_file_path                               | ibtmp1:512M:autoextend:max:30720M         |
| innodb_temp_tablespaces_dir                              | ./#innodb_temp/                           |
| innodb_thread_concurrency                                | 8                                         |
| innodb_thread_sleep_delay                                | 0                                         |
| innodb_tmpdir                                            |                                           |
| innodb_undo_directory                                    | ./                                        |
| innodb_undo_log_encrypt                                  | OFF                                       |
| innodb_undo_log_truncate                                 | ON                                        |
| innodb_undo_tablespaces                                  | 2                                         |
| innodb_use_fdatasync                                     | OFF                                       |
| innodb_use_native_aio                                    | ON                                        |
| innodb_validate_tablespace_paths                         | ON                                        |
| innodb_version                                           | 8.0.32                                    |
| innodb_write_io_threads                                  | 8                                         |
| interactive_timeout                                      | 172800                                    |
| internal_tmp_mem_storage_engine                          | MEMORY                                    |
| join_buffer_size                                         | 2097152                                   |
| keep_files_on_create                                     | OFF                                       |
| key_buffer_size                                          | 2097152                                   |
| key_cache_age_threshold                                  | 300                                       |
| key_cache_block_size                                     | 1024                                      |
| key_cache_division_limit                                 | 100                                       |
| keyring_operations                                       | ON                                        |
| large_files_support                                      | ON                                        |
| large_page_size                                          | 0                                         |
| large_pages                                              | OFF                                       |
| lc_messages                                              | en_US                                     |
| lc_messages_dir                                          | /home/work/mysql_3306/share/              |
| lc_time_names                                            | en_US                                     |
| license                                                  | GPL                                       |
| local_infile                                             | OFF                                       |
| lock_wait_timeout                                        | 5                                         |
| locked_in_memory                                         | OFF                                       |
| log_bin                                                  | ON                                        |
| log_bin_basename                                         | /home/work/mysql_3306/data/bin            |
| log_bin_index                                            | /home/work/mysql_3306/data/bin.index      |
| log_bin_trust_function_creators                          | ON                                        |
| log_bin_use_v1_row_events                                | OFF                                       |
| log_error                                                | /home/work/mysql_3306/log/error.log       |
| log_error_services                                       | log_filter_internal; log_sink_internal    |
| log_error_suppression_list                               |                                           |
| log_error_verbosity                                      | 2                                         |
| log_output                                               | FILE                                      |
| log_queries_not_using_indexes                            | OFF                                       |
| log_raw                                                  | OFF                                       |
| log_replica_updates                                      | ON                                        |
| log_slave_updates                                        | ON                                        |
| log_slow_admin_statements                                | OFF                                       |
| log_slow_extra                                           | OFF                                       |
| log_slow_replica_statements                              | OFF                                       |
| log_slow_slave_statements                                | OFF                                       |
| log_statements_unsafe_for_binlog                         | ON                                        |
| log_throttle_queries_not_using_indexes                   | 0                                         |
| log_timestamps                                           | SYSTEM                                    |
| long_query_time                                          | 1.000000                                  |
| low_priority_updates                                     | OFF                                       |
| lower_case_file_system                                   | OFF                                       |
| lower_case_table_names                                   | 1                                         |
| mandatory_roles                                          |                                           |
| master_info_repository                                   | TABLE                                     |
| master_verify_checksum                                   | OFF                                       |
| max_allowed_packet                                       | 134217728                                 |
| max_binlog_cache_size                                    | 1073741824                                |
| max_binlog_size                                          | 1073741824                                |
| max_binlog_stmt_cache_size                               | 18446744073709547520                      |
| max_connect_errors                                       | 18446744073709500000                      |
| max_connections                                          | 4000                                      |
| max_delayed_threads                                      | 20                                        |
| max_digest_length                                        | 1024                                      |
| max_error_count                                          | 1024                                      |
| max_execution_time                                       | 0                                         |
| max_heap_table_size                                      | 536870912                                 |
| max_insert_delayed_threads                               | 20                                        |
| max_join_size                                            | 18446744073709551615                      |
| max_length_for_sort_data                                 | 4096                                      |
| max_points_in_geometry                                   | 65536                                     |
| max_prepared_stmt_count                                  | 1048576                                   |
| max_relay_log_size                                       | 0                                         |
| max_seeks_for_key                                        | 18446744073709551615                      |
| max_sort_length                                          | 1024                                      |
| max_sp_recursion_depth                                   | 0                                         |
| max_user_connections                                     | 0                                         |
| max_write_lock_count                                     | 18446744073709551615                      |
| min_examined_row_limit                                   | 0                                         |
| myisam_data_pointer_size                                 | 6                                         |
| myisam_max_sort_file_size                                | 9223372036853727232                       |
| myisam_mmap_size                                         | 18446744073709551615                      |
| myisam_recover_options                                   | OFF                                       |
| myisam_sort_buffer_size                                  | 8388608                                   |
| myisam_stats_method                                      | nulls_unequal                             |
| myisam_use_mmap                                          | OFF                                       |
| mysql_native_password_proxy_users                        | OFF                                       |
| net_buffer_length                                        | 16384                                     |
| net_read_timeout                                         | 10000                                     |
| net_retry_count                                          | 10                                        |
| net_write_timeout                                        | 10000                                     |
| new                                                      | OFF                                       |
| ngram_token_size                                         | 2                                         |
| offline_mode                                             | OFF                                       |
| old                                                      | OFF                                       |
| old_alter_table                                          | OFF                                       |
| open_files_limit                                         | 1024000                                   |
| optimizer_max_subgraph_pairs                             | 100000                                    |
| optimizer_prune_level                                    | 1                                         |
| optimizer_search_depth                                   | 62                                        |
| optimizer_trace                                          | enabled=off,one_line=off                  |
| optimizer_trace_limit                                    | 1                                         |
| optimizer_trace_max_mem_size                             | 1048576                                   |
| optimizer_trace_offset                                   | -1                                        |
| parser_max_mem_size                                      | 18446744073709551615                      |
| partial_revokes                                          | OFF                                       |
| password_history                                         | 0                                         |
| password_require_current                                 | OFF                                       |
| password_reuse_interval                                  | 0                                         |
| performance_schema                                       | ON                                        |
| performance_schema_accounts_size                         | -1                                        |
| performance_schema_digests_size                          | 10000                                     |
| performance_schema_error_size                            | 5242                                      |
| performance_schema_events_stages_history_long_size       | 10000                                     |
| performance_schema_events_stages_history_size            | 10                                        |
| performance_schema_events_statements_history_long_size   | 10000                                     |
| performance_schema_events_statements_history_size        | 10                                        |
| performance_schema_events_transactions_history_long_size | 10000                                     |
| performance_schema_events_transactions_history_size      | 10                                        |
| performance_schema_events_waits_history_long_size        | 10000                                     |
| performance_schema_events_waits_history_size             | 10                                        |
| performance_schema_hosts_size                            | -1                                        |
| performance_schema_max_cond_classes                      | 150                                       |
| performance_schema_max_cond_instances                    | -1                                        |
| performance_schema_max_digest_length                     | 1024                                      |
| performance_schema_max_digest_sample_age                 | 60                                        |
| performance_schema_max_file_classes                      | 80                                        |
| performance_schema_max_file_handles                      | 32768                                     |
| performance_schema_max_file_instances                    | -1                                        |
| performance_schema_max_index_stat                        | -1                                        |
| performance_schema_max_memory_classes                    | 450                                       |
| performance_schema_max_metadata_locks                    | -1                                        |
| performance_schema_max_mutex_classes                     | 350                                       |
| performance_schema_max_mutex_instances                   | -1                                        |
| performance_schema_max_prepared_statements_instances     | -1                                        |
| performance_schema_max_program_instances                 | -1                                        |
| performance_schema_max_rwlock_classes                    | 60                                        |
| performance_schema_max_rwlock_instances                  | -1                                        |
| performance_schema_max_socket_classes                    | 10                                        |
| performance_schema_max_socket_instances                  | -1                                        |
| performance_schema_max_sql_text_length                   | 1024                                      |
| performance_schema_max_stage_classes                     | 175                                       |
| performance_schema_max_statement_classes                 | 219                                       |
| performance_schema_max_statement_stack                   | 10                                        |
| performance_schema_max_table_handles                     | -1                                        |
| performance_schema_max_table_instances                   | -1                                        |
| performance_schema_max_table_lock_stat                   | -1                                        |
| performance_schema_max_thread_classes                    | 100                                       |
| performance_schema_max_thread_instances                  | -1                                        |
| performance_schema_session_connect_attrs_size            | 512                                       |
| performance_schema_setup_actors_size                     | -1                                        |
| performance_schema_setup_objects_size                    | -1                                        |
| performance_schema_show_processlist                      | OFF                                       |
| performance_schema_users_size                            | -1                                        |
| persist_only_admin_x509_subject                          |                                           |
| persist_sensitive_variables_in_plaintext                 | ON                                        |
| persisted_globals_load                                   | ON                                        |
| pid_file                                                 | /home/work/mysql_3306/pid/mysqld.pid      |
| plugin_dir                                               | /home/work/mysql_3306/lib/plugin/         |
| port                                                     | 3306                                      |
| preload_buffer_size                                      | 32768                                     |
| print_identified_with_as_hex                             | OFF                                       |
| profiling                                                | OFF                                       |
| profiling_history_size                                   | 15                                        |
| protocol_compression_algorithms                          | zlib,zstd,uncompressed                    |
| protocol_version                                         | 10                                        |
| query_alloc_block_size                                   | 8192                                      |
| query_prealloc_size                                      | 8192                                      |
| range_alloc_block_size                                   | 4096                                      |
| range_optimizer_max_mem_size                             | 8388608                                   |
| rbr_exec_mode                                            | STRICT                                    |
| read_buffer_size                                         | 131072                                    |
| read_only                                                | OFF                                       |
| read_rnd_buffer_size                                     | 16777216                                  |
| regexp_stack_limit                                       | 8000000                                   |
| regexp_time_limit                                        | 32                                        |
| relay_log                                                | relay                                     |
| relay_log_basename                                       | /home/work/mysql_3306/data/relay          |
| relay_log_index                                          | /home/work/mysql_3306/data/relay.index    |
| relay_log_info_file                                      | relay-log.info                            |
| relay_log_info_repository                                | TABLE                                     |
| relay_log_purge                                          | ON                                        |
| relay_log_recovery                                       | ON                                        |
| relay_log_space_limit                                    | 0                                         |
| replica_allow_batching                                   | ON                                        |
| replica_checkpoint_group                                 | 512                                       |
| replica_checkpoint_period                                | 300                                       |
| replica_compressed_protocol                              | OFF                                       |
| replica_exec_mode                                        | STRICT                                    |
| replica_load_tmpdir                                      | /home/work/mysql_3306/tmp                 |
| replica_max_allowed_packet                               | 1073741824                                |
| replica_net_timeout                                      | 60                                        |
| replica_parallel_type                                    | LOGICAL_CLOCK                             |
| replica_parallel_workers                                 | 32                                        |
| replica_pending_jobs_size_max                            | 134217728                                 |
| replica_preserve_commit_order                            | ON                                        |
| replica_skip_errors                                      | OFF                                       |
| replica_sql_verify_checksum                              | ON                                        |
| replica_transaction_retries                              | 10                                        |
| replica_type_conversions                                 |                                           |
| replication_optimize_for_static_plugin_config            | OFF                                       |
| replication_sender_observe_commit_only                   | OFF                                       |
| report_host                                              |                                           |
| report_password                                          |                                           |
| report_port                                              | 16315                                     |
| report_user                                              |                                           |
| require_secure_transport                                 | OFF                                       |
| rpl_read_size                                            | 8192                                      |
| rpl_semi_sync_master_enabled                             | ON                                        |
| rpl_semi_sync_master_timeout                             | 31536000000                               |
| rpl_semi_sync_master_trace_level                         | 32                                        |
| rpl_semi_sync_master_wait_for_slave_count                | 1                                         |
| rpl_semi_sync_master_wait_no_slave                       | ON                                        |
| rpl_semi_sync_master_wait_point                          | AFTER_SYNC                                |
| rpl_semi_sync_slave_enabled                              | OFF                                       |
| rpl_semi_sync_slave_trace_level                          | 32                                        |
| rpl_stop_replica_timeout                                 | 31536000                                  |
| rpl_stop_slave_timeout                                   | 31536000                                  |
| schema_definition_cache                                  | 256                                       |
| secondary_engine_cost_threshold                          | 100000.000000                             |
| secure_file_priv                                         |                                           |
| select_into_buffer_size                                  | 131072                                    |
| select_into_disk_sync                                    | OFF                                       |
| select_into_disk_sync_delay                              | 0                                         |
| server_id                                                | 37068905                                  |
| server_id_bits                                           | 32                                        |
| server_uuid                                              | c0b23b93-4400-11ef-a959-00163e6d1d38      |
| session_track_gtids                                      | OFF                                       |
| session_track_schema                                     | ON                                        |
| session_track_state_change                               | OFF                                       |
| session_track_transaction_info                           | OFF                                       |
| sha256_password_auto_generate_rsa_keys                   | ON                                        |
| sha256_password_private_key_path                         | private_key.pem                           |
| sha256_password_proxy_users                              | OFF                                       |
| sha256_password_public_key_path                          | public_key.pem                            |
| show_create_table_verbosity                              | OFF                                       |
| show_gipk_in_create_table_and_information_schema         | ON                                        |
| show_old_temporals                                       | OFF                                       |
| skip_external_locking                                    | ON                                        |
| skip_name_resolve                                        | ON                                        |
| skip_networking                                          | OFF                                       |
| skip_replica_start                                       | ON                                        |
| skip_show_database                                       | OFF                                       |
| skip_slave_start                                         | ON                                        |
| slave_allow_batching                                     | ON                                        |
| slave_checkpoint_group                                   | 512                                       |
| slave_checkpoint_period                                  | 300                                       |
| slave_compressed_protocol                                | OFF                                       |
| slave_exec_mode                                          | STRICT                                    |
| slave_load_tmpdir                                        | /home/work/mysql_3306/tmp                 |
| slave_max_allowed_packet                                 | 1073741824                                |
| slave_net_timeout                                        | 60                                        |
| slave_parallel_type                                      | LOGICAL_CLOCK                             |
| slave_parallel_workers                                   | 32                                        |
| slave_pending_jobs_size_max                              | 134217728                                 |
| slave_preserve_commit_order                              | ON                                        |
| slave_rows_search_algorithms                             | INDEX_SCAN,HASH_SCAN                      |
| slave_skip_errors                                        | OFF                                       |
| slave_sql_verify_checksum                                | ON                                        |
| slave_transaction_retries                                | 10                                        |
| slave_type_conversions                                   |                                           |
| slow_launch_time                                         | 2                                         |
| slow_query_log                                           | ON                                        |
| slow_query_log_file                                      | /home/work/mysql_3306/log/slow.log        |
| socket                                                   | /home/work/mysql_3306/tmp/mysql.sock      |
| sort_buffer_size                                         | 8388608                                   |
| source_verify_checksum                                   | OFF                                       |
| sql_auto_is_null                                         | OFF                                       |
| sql_big_selects                                          | ON                                        |
| sql_buffer_result                                        | OFF                                       |
| sql_generate_invisible_primary_key                       | OFF                                       |
| sql_log_off                                              | OFF                                       |
| sql_notes                                                | ON                                        |
| sql_quote_show_create                                    | ON                                        |
| sql_replica_skip_counter                                 | 0                                         |
| sql_require_primary_key                                  | ON                                        |
| sql_safe_updates                                         | OFF                                       |
| sql_select_limit                                         | 18446744073709551615                      |
| sql_slave_skip_counter                                   | 0                                         |
| sql_warnings                                             | OFF                                       |
| ssl_ca                                                   |                                           |
| ssl_capath                                               |                                           |
| ssl_cert                                                 |                                           |
| ssl_cipher                                               |                                           |
| ssl_crl                                                  |                                           |
| ssl_crlpath                                              |                                           |
| ssl_fips_mode                                            | OFF                                       |
| ssl_key                                                  |                                           |
| ssl_session_cache_mode                                   | ON                                        |
| ssl_session_cache_timeout                                | 300                                       |
| stored_program_cache                                     | 256                                       |
| stored_program_definition_cache                          | 256                                       |
| super_read_only                                          | OFF                                       |
| sync_binlog                                              | 1                                         |
| sync_master_info                                         | 10000                                     |
| sync_relay_log                                           | 10000                                     |
| sync_relay_log_info                                      | 10000                                     |
| sync_source_info                                         | 10000                                     |
| system_time_zone                                         | CST                                       |
| table_definition_cache                                   | 10240                                     |
| table_encryption_privilege_check                         | OFF                                       |
| table_open_cache                                         | 20480                                     |
| table_open_cache_instances                               | 16                                        |
| tablespace_definition_cache                              | 256                                       |
| temptable_max_mmap                                       | 1073741824                                |
| temptable_max_ram                                        | 1073741824                                |
| temptable_use_mmap                                       | ON                                        |
| terminology_use_previous                                 | NONE                                      |
| thread_cache_size                                        | 3000                                      |
| thread_handling                                          | one-thread-per-connection                 |
| thread_stack                                             | 1048576                                   |
| time_zone                                                | +08:00                                    |
| tls_ciphersuites                                         |                                           |
| tls_version                                              | TLSv1.2,TLSv1.3                           |
| tmp_table_size                                           | 536870912                                 |
| tmpdir                                                   | /home/work/mysql_3306/tmp                 |
| transaction_alloc_block_size                             | 8192                                      |
| transaction_isolation                                    | READ-COMMITTED                            |
| transaction_prealloc_size                                | 4096                                      |
| transaction_read_only                                    | OFF                                       |
| transaction_write_set_extraction                         | XXHASH64                                  |
| unique_checks                                            | ON                                        |
| updatable_views_with_limit                               | YES                                       |
| version                                                  | 8.0.32                                    |
| version_comment                                          | MySQL Community Server - GPL              |
| version_compile_machine                                  | aarch64                                   |
| version_compile_os                                       | Linux                                     |
| version_compile_zlib                                     | 1.2.13                                    |
| wait_timeout                                             | 172800                                    |
| windowing_use_high_precision                             | ON                                        |
| xa_detach_on_prepare                                     | ON                                        |


- `optimizer_switch = index_merge=on,index_merge_union=on,index_merge_sort_union=on,index_merge_intersection=on,engine_condition_pushdown=on,index_condition_pushdown=on,mrr=on,mrr_cost_based=on,block_nested_loop=on,batched_key_access=off,materialization=on,semijoin=on,loosescan=on,firstmatch=on,duplicateweedout=on,subquery_materialization_cost_based=on,use_index_extensions=on,condition_fanout_filter=on,derived_merge=on,use_invisible_indexes=off,skip_scan=on,hash_join=on,subquery_to_derived=off,prefer_ordering_index=on,hypergraph_optimizer=off,derived_condition_pushdown=on`
- `sql_mode = ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION`
- `optimizer_trace_features = greedy_search=on,range_optimizer=on,dynamic_range=on,repeated_subselect=on`
- `session_track_system_variables = time_zone,autocommit,character_set_client,character_set_results,character_set_connection`














