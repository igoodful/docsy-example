---
title: sys
description: sys
date: 2023-10-30
weight: 400


---

{{< alert >}}

{{< /alert >}}



```sql
mysql> show tables;
+-----------------------------------------------+
| Tables_in_sys                                 |
+-----------------------------------------------+
| host_summary                                  |
| host_summary_by_file_io                       |
| host_summary_by_file_io_type                  |
| host_summary_by_stages                        |
| host_summary_by_statement_latency             |
| host_summary_by_statement_type                |
| innodb_buffer_stats_by_schema                 |
| innodb_buffer_stats_by_table                  |
| innodb_lock_waits                             |
| io_by_thread_by_latency                       |
| io_global_by_file_by_bytes                    |
| io_global_by_file_by_latency                  |
| io_global_by_wait_by_bytes                    |
| io_global_by_wait_by_latency                  |
| latest_file_io                                |
| memory_by_host_by_current_bytes               |
| memory_by_thread_by_current_bytes             |
| memory_by_user_by_current_bytes               |
| memory_global_by_current_bytes                |
| memory_global_total                           |
| metrics                                       |
| processlist                                   |
| ps_check_lost_instrumentation                 |
| schema_auto_increment_columns                 |
| schema_index_statistics                       |
| schema_object_overview                        |
| schema_redundant_indexes                      |
| schema_table_lock_waits                       |
| schema_table_statistics                       |
| schema_table_statistics_with_buffer           |
| schema_tables_with_full_table_scans           |
| schema_unused_indexes                         |
| session                                       |
| session_ssl_status                            |
| statement_analysis                            |
| statements_with_errors_or_warnings            |
| statements_with_full_table_scans              |
| statements_with_runtimes_in_95th_percentile   |
| statements_with_sorting                       |
| statements_with_temp_tables                   |
| sys_config                                    |
| user_summary                                  |
| user_summary_by_file_io                       |
| user_summary_by_file_io_type                  |
| user_summary_by_stages                        |
| user_summary_by_statement_latency             |
| user_summary_by_statement_type                |
| version                                       |
| wait_classes_global_by_avg_latency            |
| wait_classes_global_by_latency                |
| waits_by_host_by_latency                      |
| waits_by_user_by_latency                      |
| waits_global_by_latency                       |
| x$host_summary                                |
| x$host_summary_by_file_io                     |
| x$host_summary_by_file_io_type                |
| x$host_summary_by_stages                      |
| x$host_summary_by_statement_latency           |
| x$host_summary_by_statement_type              |
| x$innodb_buffer_stats_by_schema               |
| x$innodb_buffer_stats_by_table                |
| x$innodb_lock_waits                           |
| x$io_by_thread_by_latency                     |
| x$io_global_by_file_by_bytes                  |
| x$io_global_by_file_by_latency                |
| x$io_global_by_wait_by_bytes                  |
| x$io_global_by_wait_by_latency                |
| x$latest_file_io                              |
| x$memory_by_host_by_current_bytes             |
| x$memory_by_thread_by_current_bytes           |
| x$memory_by_user_by_current_bytes             |
| x$memory_global_by_current_bytes              |
| x$memory_global_total                         |
| x$processlist                                 |
| x$ps_digest_95th_percentile_by_avg_us         |
| x$ps_digest_avg_latency_distribution          |
| x$ps_schema_table_statistics_io               |
| x$schema_flattened_keys                       |
| x$schema_index_statistics                     |
| x$schema_table_lock_waits                     |
| x$schema_table_statistics                     |
| x$schema_table_statistics_with_buffer         |
| x$schema_tables_with_full_table_scans         |
| x$session                                     |
| x$statement_analysis                          |
| x$statements_with_errors_or_warnings          |
| x$statements_with_full_table_scans            |
| x$statements_with_runtimes_in_95th_percentile |
| x$statements_with_sorting                     |
| x$statements_with_temp_tables                 |
| x$user_summary                                |
| x$user_summary_by_file_io                     |
| x$user_summary_by_file_io_type                |
| x$user_summary_by_stages                      |
| x$user_summary_by_statement_latency           |
| x$user_summary_by_statement_type              |
| x$wait_classes_global_by_avg_latency          |
| x$wait_classes_global_by_latency              |
| x$waits_by_host_by_latency                    |
| x$waits_by_user_by_latency                    |
| x$waits_global_by_latency                     |
+-----------------------------------------------+
101 rows in set (0.08 sec)

```
