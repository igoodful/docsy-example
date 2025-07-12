---
title: 组复制相关变量
description: group_replication
date: 2024-11-08
weight: 20000
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{<alert color="danger" title="注意" >}}

MySQL 实例最多支持 127 个撤消表空间，其中包括 MySQL 实例初始化时创建的两个默认撤消表空间。

{{</alert>}}




| Variable_name                                                | Value                                                          | 备注                        |
| :----------------------------------------------------------- | :------------------------------------------------------------- | :-------------------------- |
| group_replication_advertise_recovery_endpoints               | DEFAULT                                                        |                             |
| group_replication_allow_local_lower_version_join             | OFF                                                            |                             |
| group_replication_applier_batch_size_threshold               | 100000                                                         |                             |
| group_replication_arbitrator                                 | OFF                                                            |                             |
| group_replication_async_auto_failover_channel_read_only_mode | OFF                                                            |                             |
| group_replication_auto_evict_timeout                         | 0                                                              |                             |
| group_replication_auto_increment_increment                   | 7                                                              |                             |
| group_replication_autorejoin_tries                           | 3                                                              |                             |
| group_replication_bootstrap_group                            | OFF                                                            |                             |
| group_replication_broadcast_gtid_executed_period             | 1000                                                           |                             |
| group_replication_clone_threshold                            | 9223372036854775807                                            |                             |
| group_replication_communication_debug_options                | GCS_DEBUG_NONE                                                 |                             |
| group_replication_communication_flp_timeout                  | 5                                                              |                             |
| group_replication_communication_max_message_size             | 1048576                                                        |                             |
| group_replication_communication_stack                        | XCOM                                                           |                             |
| group_replication_components_stop_timeout                    | 300                                                            |                             |
| group_replication_compression_threshold                      | 1000000                                                        |                             |
| group_replication_consistency                                | BEFORE_ON_PRIMARY_FAILOVER                                     | 控制组提供的事务一致性保证  |
| group_replication_donor_threshold                            | 9223372036854775807                                            |                             |
| group_replication_enforce_update_everywhere_checks           | ON                                                             |                             |
| group_replication_exit_state_action                          | READ_ONLY                                                      |                             |
| group_replication_flow_control_applier_threshold             | 25000                                                          |                             |
| group_replication_flow_control_certifier_threshold           | 25000                                                          |                             |
| group_replication_flow_control_hold_percent                  | 10                                                             |                             |
| group_replication_flow_control_max_quota                     | 0                                                              |                             |
| group_replication_flow_control_max_wait_time                 | 3600                                                           |                             |
| group_replication_flow_control_member_quota_percent          | 0                                                              |                             |
| group_replication_flow_control_min_quota                     | 0                                                              |                             |
| group_replication_flow_control_min_recovery_quota            | 0                                                              |                             |
| group_replication_flow_control_mode                          | QUOTA                                                          |                             |
| group_replication_flow_control_period                        | 1                                                              |                             |
| group_replication_flow_control_release_percent               | 50                                                             |                             |
| group_replication_flow_control_replay_lag_behind             | 600                                                            |                             |
| group_replication_force_members                              |                                                                |                             |
| group_replication_group_name                                 | a33be252-2b71-11e6-b8f4-00212844f133                           | `select uuid();`            |
| group_replication_group_seeds                                | 172.17.136.101:18032,172.17.135.250:18032,172.17.138.136:18032 |                             |
| group_replication_gtid_assignment_block_size                 | 1000000                                                        |                             |
| group_replication_ip_allowlist                               | AUTOMATIC                                                      |                             |
| group_replication_ip_whitelist                               | AUTOMATIC                                                      |                             |
| group_replication_local_address                              | 172.17.138.136:18032                                           | 本地ip和节点间的tcp通信端口 |
| group_replication_majority_after_mode                        | ON                                                             |                             |
| group_replication_member_expel_timeout                       | 5                                                              |                             |
| group_replication_member_weight                              | 50                                                             |                             |
| group_replication_message_cache_size                         | 1073741824                                                     |                             |
| group_replication_paxos_single_leader                        | OFF                                                            |                             |
| group_replication_poll_spin_loops                            | 0                                                              |                             |
| group_replication_primary_election_mode                      | WEIGHT_ONLY                                                    |                             |
| group_replication_recovery_complete_at                       | TRANSACTIONS_APPLIED                                           |                             |
| group_replication_recovery_compression_algorithms            | uncompressed                                                   |                             |
| group_replication_recovery_get_public_key                    | OFF                                                            |                             |
| group_replication_recovery_public_key_path                   |                                                                |                             |
| group_replication_recovery_reconnect_interval                | 60                                                             |                             |
| group_replication_recovery_retry_count                       | 10                                                             |                             |
| group_replication_recovery_ssl_ca                            |                                                                |                             |
| group_replication_recovery_ssl_capath                        |                                                                |                             |
| group_replication_recovery_ssl_cert                          |                                                                |                             |
| group_replication_recovery_ssl_cipher                        |                                                                |                             |
| group_replication_recovery_ssl_crl                           |                                                                |                             |
| group_replication_recovery_ssl_crlpath                       |                                                                |                             |
| group_replication_recovery_ssl_key                           |                                                                |                             |
| group_replication_recovery_ssl_verify_server_cert            | OFF                                                            |                             |
| group_replication_recovery_tls_ciphersuites                  |                                                                |                             |
| group_replication_recovery_tls_version                       | TLSv1.2,TLSv1.3                                                |                             |
| group_replication_recovery_use_ssl                           | OFF                                                            |                             |
| group_replication_recovery_zstd_compression_level            | 3                                                              |                             |
| group_replication_request_time_threshold                     | 0                                                              |                             |
| group_replication_single_primary_fast_mode                   | 0                                                              |                             |
| group_replication_single_primary_mode                        | OFF                                                            |                             |
| group_replication_ssl_mode                                   | DISABLED                                                       |                             |
| group_replication_start_on_boot                              | OFF                                                            |                             |
| group_replication_tls_source                                 | MYSQL_MAIN                                                     |                             |
| group_replication_transaction_size_limit                     | 150000000                                                      |                             |
| group_replication_unreachable_majority_timeout               | 0                                                              |                             |
| group_replication_view_change_uuid                           | AUTOMATIC                                                      |                             |
| group_replication_xcom_cache_mode                            | 2                                                              |                             |
| group_replication_zone_id                                    | 0                                                              |                             |
| group_replication_zone_id_sync_mode                          | ON                                                             |                             |






### group_replication_consistency
控制组提供的事务一致性保证

#### EVENTUAL


#### BEFORE_ON_PRIMARY_FAILOVER


#### BEFORE


#### AFTER


#### BEFORE_AND_AFTER




