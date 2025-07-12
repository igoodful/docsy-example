---
title: 启动日志
description: 启动日志
date: 2017-01-05
weight: 70000


---

{{< alert >}}

{{< /alert >}}



{{<warning>}}
<!---->


{{</warning>}}


```sql

[mysql@node77 dbscale]$ cat  /data/dbscalelog/dbscale.log
2024-01-18 14:38:36.579101 (140643674564800) [ INFO] <generate_config.cc:364> Generate config base on --config-file dbscale.conf.
2024-01-18 14:38:36.581294 (140643674564800) [ INFO] <zookeeper_tool.cc:116> Waiting for zookeeper connect...
2024-01-18 14:38:36.791381 (140643674564800) [ INFO] <multiple.cc:925> Stop multiple manager.
2024-01-18 14:38:36.791511 (140643556914944) [ INFO] <multiple.cc:4091> MultipleSyncTool thread stop.
2024-01-18 14:38:36.792228 (140643674564800) [ INFO] <multiple.cc:930> Finish stop sync tool.
2024-01-18 14:38:36.792265 (140643674564800) [ INFO] <multiple.cc:944> Finish node helper stop wait.
2024-01-18 14:38:36.795347 (140643674564800) [ INFO] <zookeeper_tool.cc:92> Close zookeeper handler.
2024-01-18 14:38:36.842311 (139665061951680) [ INFO] <main.cc:130> Initializing dbscale server.
2024-01-18 14:38:36.842435 (139665061951680) [ INFO] <main.cc:133> DBScale version: GreatDBRouter-6.0.0.6026.d9998cb6.with_spark.with_ssl
2024-01-18 14:38:36.842543 (139665061951680) [ INFO] <backend.cc:3170> Driver: MySQLDriver-1.0
2024-01-18 14:38:36.842612 (139665061951680) [ INFO] <pool.h:147> Start to init pool [Thread_Pool] with increase num [30].
2024-01-18 14:38:36.878437 (139665061951680) [ INFO] <pool.h:162> Pool [Thread_Pool] with num [30] init sccessfully, size [30].
2024-01-18 14:38:36.878525 (139665061951680) [ INFO] <pool.h:147> Start to init pool [Admin_pool] with increase num [30].
2024-01-18 14:38:36.879646 (139665061951680) [ INFO] <pool.h:162> Pool [Admin_pool] with num [30] init sccessfully, size [30].
2024-01-18 14:38:36.880462 (139665061951680) [ INFO] <backend.cc:7158> Init the server monitor.
2024-01-18 14:38:36.880766 (139665061951680) [ INFO] <backend.cc:7167> Init the data server.
2024-01-18 14:38:36.881990 (139665061951680) [ INFO] <data_server.cc:4499> dataserver [normal_0_0] start to fetch backend server version
2024-01-18 14:38:36.882360 (139665061951680) [ INFO] <mysql_connection.cc:3502> dbscale detect backend server version is 5
2024-01-18 14:38:36.882374 (139665061951680) [ INFO] <data_server.cc:4511> dataserver [normal_0_0] finish to fetch backend server version
2024-01-18 14:38:36.884384 (139665061951680) [ INFO] <backend.cc:7184> Init the data source.
2024-01-18 14:38:36.884835 (139665061951680) [ INFO] <backend.cc:7187> Init data space rep.
2024-01-18 14:38:36.884884 (139665061951680) [ INFO] <backend.cc:7191> Init the partition scheme.
2024-01-18 14:38:36.884967 (139665061951680) [ INFO] <backend.cc:7195> Init the data space.
2024-01-18 14:38:36.885071 (139665061951680) [ INFO] <backend.cc:7199> Init the basic variables.
2024-01-18 14:38:36.885089 (139665061951680) [ INFO] <backend.cc:7203> Init read only procedures.
2024-01-18 14:38:36.885104 (139665061951680) [ INFO] <backend.cc:7215> Init meta datasource.
2024-01-18 14:38:36.885119 (139665061951680) [ INFO] <backend.cc:7234> Init auth space.
2024-01-18 14:38:36.885217 (139665061951680) [ INFO] <backend.cc:7249> Init config source.
2024-01-18 14:38:36.885232 (139665061951680) [ INFO] <backend.cc:7268> Init multiple manager.
2024-01-18 14:38:36.886696 (139665061951680) [ INFO] <zookeeper_tool.cc:116> Waiting for zookeeper connect...
2024-01-18 14:38:37.092900 (139665061951680) [ INFO] <multiple.cc:1062> Start to acquire start config lock, the node info is: 172.17.134.77:16310_session_0
2024-01-18 14:38:37.096088 (139665061951680) [ INFO] <multiple.cc:1116> Finish acquire start config lock.
2024-01-18 14:38:37.096170 (139665061951680) [ INFO] <multiple.cc:1250> Start to fetch config node version from zookeeper.
2024-01-18 14:38:37.098962 (139665061951680) [ INFO] <multiple_message.cc:74> Start to replay message 6 1 1;38~[main]
driver = mysql
log-file = /data/dbscalelog/dbscale.log
pid-file = dbscale.pid
do-audit-log = NONE
admin-user = dbscale_internal
dbscale-internal-user = dbscale_internal
admin-password = IVFBWjJ3c3g=
datasource-in-one = 1
check-part-primary = 1
authenticate-source = normal_0
encode-password = 1
on-view = 0
lower-case-table-names = 1
use-partial-parse = 1
enable-acl = 1
do-auth-on-dbscale = 0
enable-xa-transaction = 0
enable-dead-lock-detect = 0
disable-parallel-modify = 0
conn-pool-num = 1
default-login-schema = information_schema
enable-session-swap = 0
enable-session-swap-during-execution = 1
support-show-warning = 1
support-tokudb = 0
supreme-admin-user = admin
support-log-warning-info = 1
max-fetchnode-ready-rows-size = 10000
max-mergenode-ready-rows-size = 2000000
dbscale-cluster-id = 13476
auto-master-failover-flashback = 1
unexpected-gtid-threshold-value = 5
conn-always-set-session-var = 1
monitor-interval = 2
insert-select-sql-size = 65536
enable-simplified-float-number = 1
spark-password = dbscalc=
max-dataserver-monitor = 6
connect-timeout = 2
monitor-net-timeout = 1
backend-sql-net-timeout = 60
dbscale-trx-strict-mode = 0
enable-super-readonly-on-slave = 1
mul-dbscale-forward-timeout = 10
wait-timeout = 172800
max-replication-delay = 30
max-slave-retrived-gtid-delay = 1000000
max-slave-retrived-binlog-pos-delay = 10000000
max-slave-retrived-binlog-pos-delay-for-wakeup = 10000000
max-replication-delay-potential-master = 10000000
migrate-write-thread = 10
migrate-write-packet-size = 16777216
enable-last-insert-id = 1
max-connection-cleanup-number = 100
backlog = 10000
node-host-addr = 172.17.134.71
multiple-mode = 1
zookeeper-password = ZGJzY2FsZQ==
zk-log-file = /data/dbscalelog/zookeeper.log
zookeeper-host = 172.17.134.76:16309,172.17.134.77:16309,172.17.134.71:16309
zookeeper-check-retry = 600
cluster-user = dbscale_internal
cluster-password = IVFBWjJ3c3g=
function-type-file = function_type.txt
check-auto-increment-value = 0
get-connection-retry-times = 2
monitor-retry-count = 2
monitor-retry-count-stable = 2
dbscale-acl-strict-mode = 0
max-load-ready-packets = 64
max-load-analysis-wait-size = 12
max-load-packet-size = 16777216
allow-modify-server-directly = 1
connection-pool-admin-interval = 60
thread-pool-max = 2000
default-session-variables = character_set_database:transaction_read_only
close-load-conn = 1
dbscale-hosts = %
catch-sigsegv = 0
authenticate-with-pool-conn = 0
transparent-mode = 0
max-cross-join-moved-rows = 10000
max-wise-group-size = 100000000
dbscale-master-rescramble-delay = 1
log-level = INFO


[driver mysql]
type = MySQLDriver
port = 16310
transparent-port = 23399
bind-address = 0.0.0.0


[catalog default]
data-source = normal_0





[data-source normal_0]
group-id = 100
user = dbscale_internal
password = IVFBWjJ3c3g=
type = replication
semi-sync-on = 1
load-balance-strategy = MASTER
master = normal_0_0-1-1000-400-800
slave = normal_0_1-1-1000-400-800
slave = normal_0_2-1-1000-400-800


[data-server normal_0_0]
host = 172.17.134.71
port = 16315
user = dbscale_internal
password = IVFBWjJ3c3g=
remote-user = root
remote-port = 22

[data-server normal_0_1]
host = 172.17.134.77
port = 16315
user = dbscale_internal
password = IVFBWjJ3c3g=
remote-user = root
remote-port = 22

[data-server normal_0_2]
host = 172.17.134.76
port = 16315
user = dbscale_internal
password = IVFBWjJ3c3g=
remote-user = root
remote-port = 22


#GreatDBRouter-6.0.0.6026.d9998cb6.with_spark.with_ssl

.
2024-01-18 14:38:37.099216 (139665061951680) [ INFO] <multiple.cc:1265> Finish fetch config node version 6 from zookeeper.
2024-01-18 14:38:42.101565 (139665061951680) [ INFO] <multiple.cc:2104> Waiting for master node message...
2024-01-18 14:38:42.101715 (139665061951680) [ INFO] <multiple.cc:2109> slave role dbscale master node message [inited_172.17.134.71:16310], start to check it...
2024-01-18 14:38:42.102131 (139665061951680) [ INFO] <multiple.cc:2112> master role dbscale has been inited.
2024-01-18 14:38:42.108412 (139665061951680) [ INFO] <multiple.cc:1119> Start to release start config lock.
2024-01-18 14:38:42.110394 (139665061951680) [ INFO] <multiple.cc:1123> Finish release start config lock.
2024-01-18 14:38:42.110517 (139665061951680) [ INFO] <backend.cc:7277> Init the SSL support.
2024-01-18 14:38:42.110597 (139665061951680) [ INFO] <ssl_tool.cc:145> Start to ssl_init.
2024-01-18 14:38:42.110609 (139665061951680) [ INFO] <ssl_tool.cc:147> SKIP SSLTool::ssl_init due to enable_ssl = 0 during start-up.
2024-01-18 14:38:42.110628 (139665061951680) [ INFO] <backend.cc:7281> Init conn pool manager.
2024-01-18 14:38:42.110702 (139665061951680) [ INFO] <backend.cc:7284> Init backend thread pool.
2024-01-18 14:38:42.110752 (139665061951680) [ INFO] <pool.h:147> Start to init pool [Backend_Thread_Pool_0] with increase num [30].
2024-01-18 14:38:42.111944 (139665061951680) [ INFO] <pool.h:162> Pool [Backend_Thread_Pool_0] with num [30] init sccessfully, size [30].
2024-01-18 14:38:42.112041 (139665061951680) [ INFO] <backend.cc:7286> Init cross node join threads.
2024-01-18 14:38:42.112112 (139665061951680) [ INFO] <backend.cc:7288> Init cross node join init thread.
2024-01-18 14:38:42.112360 (139665061951680) [ INFO] <backend.cc:7304> SlowLogTool thread start working.
2024-01-18 14:38:42.112436 (139665061951680) [ INFO] <backend.cc:7308> AuditLogTool thread start working.
2024-01-18 14:38:42.112501 (139665061951680) [ INFO] <backend.cc:7318> CrossNodeLogTool thread start working.
2024-01-18 14:38:42.112547 (139665061951680) [ INFO] <backend.cc:7324> ReloadInformationSchemaMirrorTableTool thread start working.
2024-01-18 14:38:42.112561 (139665061951680) [ INFO] <backend.cc:7331> Init event base manger threads.
2024-01-18 14:38:42.112175 (139664004048640) [ INFO] <cross_node_join.cc:94> Start cross node join clean thread.
2024-01-18 14:38:42.112752 (139665061951680) [ INFO] <pool.h:147> Start to init pool [Thread_one_pool_0] with increase num [2].
2024-01-18 14:38:42.112903 (139665061951680) [ INFO] <pool.h:162> Pool [Thread_one_pool_0] with num [2] init sccessfully, size [2].
2024-01-18 14:38:42.113044 (139665061951680) [ INFO] <backend.cc:11198> Start to init_shard_schema_string.
2024-01-18 14:38:42.114821 (139663995655936) [ INFO] <cross_node_join.cc:141> Start cross node join init thread.
2024-01-18 14:38:42.139786 (139665061951680) [ INFO] <backend.cc:11216> Finish to init_shard_schema_string.
2024-01-18 14:38:42.139850 (139665061951680) [ INFO] <keepalive.cc:561> Work as cluster slave, so ignore the start-up of message threads.
2024-01-18 14:38:42.142245 (139665061951680) [ INFO] <keepalive.cc:566> Multiple manager thread started.
2024-01-18 14:38:42.142365 (139663922689792) [ INFO] <multiple.cc:2590> Multiple manager thread start to run.
2024-01-18 14:38:42.142434 (139663914297088) [ INFO] <manager_thread.cc:178> Start manager thread.
2024-01-18 14:38:42.142526 (139665061951680) [ INFO] <backend.cc:1570> Config source is not working. sleep 1s to wait config source working
2024-01-18 14:38:42.146074 (139663922689792) [ INFO] <multiple.cc:2070> Waiting for get ka init message...
2024-01-18 14:38:42.146642 (139663922689792) [ INFO] <multiple.cc:369> Update node /dbscale/keepalive/keepalive_update_info has no value, so return 0 as biggest version of update info.
2024-01-18 14:38:42.148242 (139663922689792) [ INFO] <multiple.cc:2274> slave role dbscale get ka init message [1 15 1;30~0~normal_0_0 1 null 0 0 2 1 0 0 1 0 0;30~1~normal_0_0 0 2 0 1;30~2~normal_0_0 1 normal_0_normal_0_0 1;30~0~normal_0_1 3 normal_0_0 1 0 1 0 1 0 1 0 0;30~1~normal_0_1 0 2 16 1;30~2~normal_0_1 1 normal_0_normal_0_1 3;30~0~normal_0_2 3 normal_0_0 1 0 1 0 1 0 1 0 0;30~1~normal_0_2 0 2 16 1;30~2~normal_0_2 1 normal_0_normal_0_2 3;31~0~normal_0_normal_0_0 0 null null 0 ;31~0~normal_0_normal_0_1 0 normal_0_0 null 0 ;31~0~normal_0_normal_0_2 0 normal_0_0 null 0 ;31~0~normal_0_read 0 normal_0_0 null;31~0~normal_0 0 null null normal_0_normal_0_0 normal_0_normal_0_0 1 0 0;39~5], start to replay it...
2024-01-18 14:38:42.148280 (139663922689792) [ INFO] <multiple_message.cc:74> Start to replay message 1 15 1;30~0~normal_0_0 1 null 0 0 2 1 0 0 1 0 0;30~1~normal_0_0 0 2 0 1;30~2~normal_0_0 1 normal_0_normal_0_0 1;30~0~normal_0_1 3 normal_0_0 1 0 1 0 1 0 1 0 0;30~1~normal_0_1 0 2 16 1;30~2~normal_0_1 1 normal_0_normal_0_1 3;30~0~normal_0_2 3 normal_0_0 1 0 1 0 1 0 1 0 0;30~1~normal_0_2 0 2 16 1;30~2~normal_0_2 1 normal_0_normal_0_2 3;31~0~normal_0_normal_0_0 0 null null 0 ;31~0~normal_0_normal_0_1 0 normal_0_0 null 0 ;31~0~normal_0_normal_0_2 0 normal_0_0 null 0 ;31~0~normal_0_read 0 normal_0_0 null;31~0~normal_0 0 null null normal_0_normal_0_0 normal_0_normal_0_0 1 0 0;39~5.
2024-01-18 14:38:42.148556 (139663922689792) [ INFO] <data_server.cc:1147> server[normal_0_00x7f063b506000] set online_status to Master_Online with is_master 1.
2024-01-18 14:38:42.148582 (139663922689792) [ INFO] <data_server.h:1183> Setting server: [normal_0_0] is single slave failed: [0]
2024-01-18 14:38:42.148613 (139663922689792) [ INFO] <data_server.cc:1175> Server[normal_0_10x7f063b506700] set online_status to Not_A_Master.
2024-01-18 14:38:42.148623 (139663922689792) [ INFO] <data_server.h:1183> Setting server: [normal_0_1] is single slave failed: [0]
2024-01-18 14:38:42.148638 (139663922689792) [ INFO] <data_server.cc:1175> Server[normal_0_20x7f063b506e00] set online_status to Not_A_Master.
2024-01-18 14:38:42.148646 (139663922689792) [ INFO] <data_server.h:1183> Setting server: [normal_0_2] is single slave failed: [0]
2024-01-18 14:38:42.148662 (139663922689792) [ INFO] <keepalive_message.cc:1328> Source [normal_0_normal_0_00x7f063b51d000] with source_name normal_0_normal_0_0 replay attr message normal_0_normal_0_0 0 null null 0 .
2024-01-18 14:38:42.148693 (139663922689792) [ INFO] <data_source.cc:885> Source [normal_0_normal_0_00x7f063b51d000] with source_name normal_0_normal_0_0 set state to Working during message replay with cur_master_name null.
2024-01-18 14:38:42.148711 (139663922689792) [ INFO] <data_source.h:520> DataSource [0x7f063b51d000normal_0_normal_0_0] change status from [Unknown] to [Working]
2024-01-18 14:38:42.148742 (139663922689792) [ INFO] <pool.h:147> Start to init pool [normal_0_normal_0_0] with increase num [1].
2024-01-18 14:38:42.149713 (139663922689792) [ INFO] <pool.h:162> Pool [normal_0_normal_0_0] with num [1] init sccessfully, size [1].
2024-01-18 14:38:42.149762 (139663922689792) [ INFO] <data_source.cc:2050> ServerDataSource::do_replay_attr_message for source [normal_0_normal_0_00x7f063b51d000] with is_state_change 1 need_recover_for_xa 0 top_source_str .
2024-01-18 14:38:42.149789 (139663922689792) [ INFO] <keepalive_message.cc:1328> Source [normal_0_normal_0_10x7f063b51d380] with source_name normal_0_normal_0_1 replay attr message normal_0_normal_0_1 0 normal_0_0 null 0 .
2024-01-18 14:38:42.149801 (139663922689792) [ INFO] <data_source.cc:885> Source [normal_0_normal_0_10x7f063b51d380] with source_name normal_0_normal_0_1 set state to Working during message replay with cur_master_name normal_0_0.
2024-01-18 14:38:42.149810 (139663922689792) [ INFO] <data_source.h:520> DataSource [0x7f063b51d380normal_0_normal_0_1] change status from [Unknown] to [Working]
2024-01-18 14:38:42.149820 (139663922689792) [ INFO] <pool.h:147> Start to init pool [normal_0_normal_0_1] with increase num [1].
2024-01-18 14:38:42.151368 (139663922689792) [ INFO] <pool.h:162> Pool [normal_0_normal_0_1] with num [1] init sccessfully, size [1].
2024-01-18 14:38:42.151394 (139663922689792) [ INFO] <data_source.cc:2050> ServerDataSource::do_replay_attr_message for source [normal_0_normal_0_10x7f063b51d380] with is_state_change 1 need_recover_for_xa 0 top_source_str .
2024-01-18 14:38:42.151411 (139663922689792) [ INFO] <keepalive_message.cc:1328> Source [normal_0_normal_0_20x7f063b51d700] with source_name normal_0_normal_0_2 replay attr message normal_0_normal_0_2 0 normal_0_0 null 0 .
2024-01-18 14:38:42.151423 (139663922689792) [ INFO] <data_source.cc:885> Source [normal_0_normal_0_20x7f063b51d700] with source_name normal_0_normal_0_2 set state to Working during message replay with cur_master_name normal_0_0.
2024-01-18 14:38:42.151431 (139663922689792) [ INFO] <data_source.h:520> DataSource [0x7f063b51d700normal_0_normal_0_2] change status from [Unknown] to [Working]
2024-01-18 14:38:42.151440 (139663922689792) [ INFO] <pool.h:147> Start to init pool [normal_0_normal_0_2] with increase num [1].
2024-01-18 14:38:42.152322 (139663922689792) [ INFO] <pool.h:162> Pool [normal_0_normal_0_2] with num [1] init sccessfully, size [1].
2024-01-18 14:38:42.152344 (139663922689792) [ INFO] <data_source.cc:2050> ServerDataSource::do_replay_attr_message for source [normal_0_normal_0_20x7f063b51d700] with is_state_change 1 need_recover_for_xa 0 top_source_str .
2024-01-18 14:38:42.152380 (139663922689792) [ INFO] <keepalive_message.cc:1328> Source [normal_0_read0x7f0647655400] with source_name normal_0_read replay attr message normal_0_read 0 normal_0_0 null.
2024-01-18 14:38:42.152390 (139663922689792) [ INFO] <data_source.cc:885> Source [normal_0_read0x7f0647655400] with source_name normal_0_read set state to Working during message replay with cur_master_name normal_0_0.
2024-01-18 14:38:42.152397 (139663922689792) [ INFO] <data_source.h:520> DataSource [0x7f0647655400normal_0_read] change status from [Unknown] to [Working]
2024-01-18 14:38:42.152416 (139663922689792) [ INFO] <keepalive_message.cc:1328> Source [normal_00x7f063b51da80] with source_name normal_0 replay attr message normal_0 0 null null normal_0_normal_0_0 normal_0_normal_0_0 1 0 0.
2024-01-18 14:38:42.152425 (139663922689792) [ INFO] <data_source.cc:885> Source [normal_00x7f063b51da80] with source_name normal_0 set state to Working during message replay with cur_master_name null.
2024-01-18 14:38:42.152432 (139663922689792) [ INFO] <data_source.h:520> DataSource [0x7f063b51da80normal_0] change status from [Unknown] to [Working]
2024-01-18 14:38:42.152448 (139663922689792) [ INFO] <data_source.cc:7084> ReplicationDataSource::do_replay_attr_message with write_name normal_0_normal_0_0 write_source [normal_0_normal_0_00x7f063b51d000] with config_master_name normal_0_normal_0_0 write_source_inited 1 is_dynamic_change_master 0 is_in_failover 0.
2024-01-18 14:38:42.153068 (139663922689792) [ INFO] <multiple_message.cc:74> Start to replay message 7 1 1;33~2~31 8 7 .
2024-01-18 14:38:42.154178 (139663922689792) [ INFO] <multiple.cc:189> Find the last message version 7.
2024-01-18 14:38:42.154197 (139663922689792) [ WARN] <multiple.cc:207> There is no new message with last version 7.
2024-01-18 14:38:42.156264 (139663922689792) [ INFO] <multiple.cc:2948> Waiting for share list message.
2024-01-18 14:38:42.156315 (139663922689792) [ INFO] <multiple_message.cc:74> Start to replay message 2 1 1;35~0~allow_dot_in_ident '0' allow_modify_server_directly 'TRUE' always_reset_session_var '0' authenticate_with_pool_conn 'FALSE' auto_catchup_data 'FALSE' auto_purge_binlog '0' auto_purge_conn_pool '1' auto_space_level '0' auto_start_io_thread 'TRUE' backend_sql_net_timeout '60' catch_sigsegv 'FALSE' check_auto_increment_value '0' check_master_sync '1' check_part_primary '1' check_using_table_times '5' close_cross_node_transaction '0' close_foreign_key_check '1' close_load_conn '1' close_session_xa '0' close_xa_handle_conn_broken '0' conn_always_set_session_var '1' connect_timeout '2' count_server_dataflow 'FALSE' cross_node_join_method '0' cross_tmp_table_default_engine 'MyISAM' cursor_use_free_conn '0' dbscale_acl_strict_mode '0' dbscale_master_rescramble_delay '1' dbscale_safe_sql_mode '1' dbscale_trx_strict_mode 'FALSE' disable_parallel_modify '0' do_audit_log 'NONE' do_auth_on_dbscale '0' do_uncommon_sql_info_log 'FALSE' down_server_monitor_interval '500' dynamic_change_active_wait '2' dynamic_remove_slave_wait '2' enable_alter_table_sync '1' enable_auto_unlock_during_error '0' enable_block_table '0' enable_get_rep_connection '0' enable_info_schema_mirror_table '0' enable_listener_off '1' enable_monitor_point 'FALSE' enable_multiple_stmt_check '1' enable_outline '0' enable_read_only '0' enable_record_query_time '1' enable_session_get_max_inc_value '0' enable_silent_execute_prepare '0' enable_simplified_float_number '1' enable_slave_block_check '1' enable_slave_dbscale_server '1' enable_spark_invest '0' enable_ssl '0' enable_super_readonly_on_slave 'TRUE' encode_password 'TRUE' exiting_timeout '5' fetch_signal_batch '1000' flashback_allow_two_servers 'FALSE' flashback_retry_count '6' flow_control_gap '3600' flush_all_conn_in_group '0' force_create_table_engine '' force_execute_partial_set 'FALSE' get_connection_retry_times '2' handle_monitor_timeout_via_update_method '0' histogram_monitor_point_grad '5' ignore_dbscale_column 'dbscale_pattern_id' is_bridge_session 'FALSE' load_analysis_num '3' load_data_auto_inc_local_capacity '100000' load_data_quick_error '0' load_data_strict_mode '1' load_insert_select_fields_term '' load_insert_select_lines_term '' log_level 'INFO' master_change_back_conn_in_use_retry '1' master_change_back_wait '30' max_allowed_nested_sp_depth '8' max_catchup_binlog_time '600' max_conn_execute_time '0' max_conn_limit '10000' max_connect_by_result_num '100000' max_connection_cleanup_number '100' max_cross_join_moved_rows '10000' max_federated_cross_join_rows '10000' max_fetchnode_ready_rows_size '10000' max_load_analysis_wait_size '12' max_load_once_packet_num '0' max_load_packet_size '16777216' max_load_ready_packets '64' max_load_select_analysis_wait_size '20000' max_login_time '10' max_mergenode_ready_rows_size '2000000' max_prepare_cache_size '100' max_query_count_per_flow_control_gap '0' max_replication_delay '30' max_replication_delay_potential_master '10000000' max_rowmap_size '10000' max_single_sort_rows '1000000' max_slave_retrived_binlog_pos_delay '10000000' max_slave_retrived_binlog_pos_delay_for_wakeup '10000000' max_slave_retrived_gtid_delay '1000000' max_union_all_sqls '100' max_wait_consistent_lock_time '100' max_wise_group_size '100000000' migrate_async '0' migrate_async_parallel_task_num '5' migrate_direct_clean_data '0' migrate_load_packet_num '10000' migrate_method '1' migrate_timeout '600' migrate_wait_timeout '300' migrate_write_packet_size '16777216' migrate_write_thread '10' monitor_interval '2' monitor_method '1' monitor_net_timeout '1' monitor_retry_count '2' monitor_retry_count_stable '2' monitor_retry_gap '1' monitor_retry_gap_stable '2' mul_dbscale_forward_timeout '10' mysql_migrate_user 'mysql' need_all_dbscale_column_user 'informatic' need_show_last_select_master_time '1' need_validate_password '0' net_status '1' normal_admin_user 'dbscale' on_view '0' parallel_binlog_apply_threads '0' pool_reach_max_wait_times '10' pre_memory_pool_size '1024' purge_used_con2024-01-18 14:38:42.156691 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set allow_dot_in_ident = [0].
2024-01-18 14:38:42.156703 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set allow_modify_server_directly = [TRUE].
2024-01-18 14:38:42.156711 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set always_reset_session_var = [0].
2024-01-18 14:38:42.156721 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set authenticate_with_pool_conn = [FALSE].
2024-01-18 14:38:42.156728 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set auto_catchup_data = [FALSE].
2024-01-18 14:38:42.156736 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set auto_purge_binlog = [0].
2024-01-18 14:38:42.156744 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set auto_purge_conn_pool = [1].
2024-01-18 14:38:42.156752 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set auto_space_level = [0].
2024-01-18 14:38:42.156758 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set auto_start_io_thread = [TRUE].
2024-01-18 14:38:42.156766 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set backend_sql_net_timeout = [60].
2024-01-18 14:38:42.156779 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set catch_sigsegv = [FALSE].
2024-01-18 14:38:42.156786 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set check_auto_increment_value = [0].
2024-01-18 14:38:42.156794 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set check_master_sync = [1].
2024-01-18 14:38:42.156801 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set check_part_primary = [1].
2024-01-18 14:38:42.156808 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set check_using_table_times = [5].
2024-01-18 14:38:42.156821 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set close_cross_node_transaction = [0].
2024-01-18 14:38:42.156829 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set close_foreign_key_check = [1].
2024-01-18 14:38:42.156836 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set close_load_conn = [1].
2024-01-18 14:38:42.156843 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set close_session_xa = [0].
2024-01-18 14:38:42.156850 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set close_xa_handle_conn_broken = [0].
2024-01-18 14:38:42.156857 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set conn_always_set_session_var = [1].
2024-01-18 14:38:42.156864 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set connect_timeout = [2].
2024-01-18 14:38:42.156872 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set count_server_dataflow = [FALSE].
2024-01-18 14:38:42.156879 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set cross_node_join_method = [0].
2024-01-18 14:38:42.156886 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set cross_tmp_table_default_engine = [MyISAM].
2024-01-18 14:38:42.156894 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set cursor_use_free_conn = [0].
2024-01-18 14:38:42.156901 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set dbscale_acl_strict_mode = [0].
2024-01-18 14:38:42.156930 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set dbscale_master_rescramble_delay = [1].
2024-01-18 14:38:42.156938 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set dbscale_safe_sql_mode = [1].
2024-01-18 14:38:42.156945 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set dbscale_trx_strict_mode = [FALSE].
2024-01-18 14:38:42.156953 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set disable_parallel_modify = [0].
2024-01-18 14:38:42.156967 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set do_audit_log = [NONE].
2024-01-18 14:38:42.156975 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set do_auth_on_dbscale = [0].
2024-01-18 14:38:42.156982 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set do_uncommon_sql_info_log = [FALSE].
2024-01-18 14:38:42.156989 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set down_server_monitor_interval = [500].
2024-01-18 14:38:42.156996 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set dynamic_change_active_wait = [2].
2024-01-18 14:38:42.157003 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set dynamic_remove_slave_wait = [2].
2024-01-18 14:38:42.157011 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set enable_alter_table_sync = [1].
2024-01-18 14:38:42.157019 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set enable_auto_unlock_during_error = [0].
2024-01-18 14:38:42.157026 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set enable_block_table = [0].
2024-01-18 14:38:42.157033 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set enable_get_rep_connection = [0].
2024-01-18 14:38:42.157040 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set enable_info_schema_mirror_table = [0].
2024-01-18 14:38:42.157048 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set enable_listener_off = [1].
2024-01-18 14:38:42.157055 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set enable_monitor_point = [FALSE].
2024-01-18 14:38:42.157063 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set enable_multiple_stmt_check = [1].
2024-01-18 14:38:42.157075 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set enable_outline = [0].
2024-01-18 14:38:42.157082 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set enable_read_only = [0].
2024-01-18 14:38:42.157090 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set enable_record_query_time = [1].
2024-01-18 14:38:42.157097 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set enable_session_get_max_inc_value = [0].
2024-01-18 14:38:42.157105 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set enable_silent_execute_prepare = [0].
2024-01-18 14:38:42.157112 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set enable_simplified_float_number = [1].
2024-01-18 14:38:42.157120 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set enable_slave_block_check = [1].
2024-01-18 14:38:42.157127 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set enable_slave_dbscale_server = [1].
2024-01-18 14:38:42.157134 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set enable_spark_invest = [0].
2024-01-18 14:38:42.157141 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set enable_ssl = [0].
2024-01-18 14:38:42.157153 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set enable_super_readonly_on_slave = [TRUE].
2024-01-18 14:38:42.157160 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set encode_password = [TRUE].
2024-01-18 14:38:42.157167 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set exiting_timeout = [5].
2024-01-18 14:38:42.157174 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set fetch_signal_batch = [1000].
2024-01-18 14:38:42.157181 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set flashback_allow_two_servers = [FALSE].
2024-01-18 14:38:42.157189 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set flashback_retry_count = [6].
2024-01-18 14:38:42.157197 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set flow_control_gap = [3600].
2024-01-18 14:38:42.157204 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set flush_all_conn_in_group = [0].
2024-01-18 14:38:42.157211 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set force_create_table_engine = [].
2024-01-18 14:38:42.157218 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set force_execute_partial_set = [FALSE].
2024-01-18 14:38:42.157225 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set get_connection_retry_times = [2].
2024-01-18 14:38:42.157231 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set handle_monitor_timeout_via_update_method = [0].
2024-01-18 14:38:42.157239 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set histogram_monitor_point_grad = [5].
2024-01-18 14:38:42.157246 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set ignore_dbscale_column = [dbscale_pattern_id].
2024-01-18 14:38:42.157254 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set is_bridge_session = [FALSE].
2024-01-18 14:38:42.157261 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set load_analysis_num = [3].
2024-01-18 14:38:42.157268 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set load_data_auto_inc_local_capacity = [100000].
2024-01-18 14:38:42.157275 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set load_data_quick_error = [0].
2024-01-18 14:38:42.157282 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set load_data_strict_mode = [1].
2024-01-18 14:38:42.157294 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set load_insert_select_fields_term = [].
2024-01-18 14:38:42.157301 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set load_insert_select_lines_term = [].
2024-01-18 14:38:42.157360 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set log_level = [INFO].
2024-01-18 14:38:42.157369 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set master_change_back_conn_in_use_retry = [1].
2024-01-18 14:38:42.157377 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set master_change_back_wait = [30].
2024-01-18 14:38:42.157384 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set max_allowed_nested_sp_depth = [8].
2024-01-18 14:38:42.157391 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set max_catchup_binlog_time = [600].
2024-01-18 14:38:42.157399 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set max_conn_execute_time = [0].
2024-01-18 14:38:42.157406 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set max_conn_limit = [10000].
2024-01-18 14:38:42.157413 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set max_connect_by_result_num = [100000].
2024-01-18 14:38:42.157420 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set max_connection_cleanup_number = [100].
2024-01-18 14:38:42.157427 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set max_cross_join_moved_rows = [10000].
2024-01-18 14:38:42.157434 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set max_federated_cross_join_rows = [10000].
2024-01-18 14:38:42.157441 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set max_fetchnode_ready_rows_size = [10000].
2024-01-18 14:38:42.157448 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set max_load_analysis_wait_size = [12].
2024-01-18 14:38:42.157455 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set max_load_once_packet_num = [0].
2024-01-18 14:38:42.157463 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set max_load_packet_size = [16777216].
2024-01-18 14:38:42.157471 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set max_load_ready_packets = [64].
2024-01-18 14:38:42.157478 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set max_load_select_analysis_wait_size = [20000].
2024-01-18 14:38:42.157485 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set max_login_time = [10].
2024-01-18 14:38:42.157492 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set max_mergenode_ready_rows_size = [2000000].
2024-01-18 14:38:42.157499 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set max_prepare_cache_size = [100].
2024-01-18 14:38:42.157506 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set max_query_count_per_flow_control_gap = [0].
2024-01-18 14:38:42.157513 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set max_replication_delay = [30].
2024-01-18 14:38:42.157520 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set max_replication_delay_potential_master = [10000000].
2024-01-18 14:38:42.157528 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set max_rowmap_size = [10000].
2024-01-18 14:38:42.157535 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set max_single_sort_rows = [1000000].
2024-01-18 14:38:42.157542 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set max_slave_retrived_binlog_pos_delay = [10000000].
2024-01-18 14:38:42.157549 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set max_slave_retrived_binlog_pos_delay_for_wakeup = [10000000].
2024-01-18 14:38:42.157562 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set max_slave_retrived_gtid_delay = [1000000].
2024-01-18 14:38:42.157569 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set max_union_all_sqls = [100].
2024-01-18 14:38:42.157576 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set max_wait_consistent_lock_time = [100].
2024-01-18 14:38:42.157583 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set max_wise_group_size = [100000000].
2024-01-18 14:38:42.157591 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set migrate_async = [0].
2024-01-18 14:38:42.157598 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set migrate_async_parallel_task_num = [5].
2024-01-18 14:38:42.157605 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set migrate_direct_clean_data = [0].
2024-01-18 14:38:42.157613 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set migrate_load_packet_num = [10000].
2024-01-18 14:38:42.157620 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set migrate_method = [1].
2024-01-18 14:38:42.157627 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set migrate_timeout = [600].
2024-01-18 14:38:42.157634 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set migrate_wait_timeout = [300].
2024-01-18 14:38:42.157641 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set migrate_write_packet_size = [16777216].
2024-01-18 14:38:42.157648 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set migrate_write_thread = [10].
2024-01-18 14:38:42.157655 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set monitor_interval = [2].
2024-01-18 14:38:42.157662 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set monitor_method = [1].
2024-01-18 14:38:42.157669 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set monitor_net_timeout = [1].
2024-01-18 14:38:42.157676 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set monitor_retry_count = [2].
2024-01-18 14:38:42.157683 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set monitor_retry_count_stable = [2].
2024-01-18 14:38:42.157690 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set monitor_retry_gap = [1].
2024-01-18 14:38:42.157697 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set monitor_retry_gap_stable = [2].
2024-01-18 14:38:42.157705 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set mul_dbscale_forward_timeout = [10].
2024-01-18 14:38:42.157712 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set mysql_migrate_user = [mysql].
2024-01-18 14:38:42.157719 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set need_all_dbscale_column_user = [informatic].
2024-01-18 14:38:42.157726 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set need_show_last_select_master_time = [1].
2024-01-18 14:38:42.157734 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set need_validate_password = [0].
2024-01-18 14:38:42.157741 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set net_status = [1].
2024-01-18 14:38:42.157748 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set normal_admin_user = [dbscale].
2024-01-18 14:38:42.157756 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set on_view = [0].
2024-01-18 14:38:42.157763 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set parallel_binlog_apply_threads = [0].
2024-01-18 14:38:42.157771 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set pool_reach_max_wait_times = [10].
2024-01-18 14:38:42.157782 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set pre_memory_pool_size = [1024].
2024-01-18 14:38:42.157790 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set purge_used_connection_time = [18000].
2024-01-18 14:38:42.157797 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set ready_event_high = [0].
2024-01-18 14:38:42.157804 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set ready_event_low = [0].
2024-01-18 14:38:42.157811 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set real_time_queries = [2].
2024-01-18 14:38:42.157818 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set receive_timeout = [1314000].
2024-01-18 14:38:42.157825 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set record_transaction_sqls = [1].
2024-01-18 14:38:42.157832 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set redo_load_timeout = [1800].
2024-01-18 14:38:42.157840 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set restrict_create_table = [1].
2024-01-18 14:38:42.157846 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set restrict_modify_table = [FALSE].
2024-01-18 14:38:42.157854 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set send_server_retry_times = [3].
2024-01-18 14:38:42.157861 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set send_timeout = [3600].
2024-01-18 14:38:42.157868 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set sequence_retry_count = [0].
2024-01-18 14:38:42.157875 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set server_status_error_log_gap = [100].
2024-01-18 14:38:42.157883 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set session_cache_sqls = [0].
2024-01-18 14:38:42.157889 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set session_init_charset = [].
2024-01-18 14:38:42.157897 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set session_load_balance_strategy = [DEFAULT].
2024-01-18 14:38:42.157920 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set session_packet_pool_init_packet_count = [10000].
2024-01-18 14:38:42.157928 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set session_packet_pool_max_packet_count = [10000].
2024-01-18 14:38:42.157936 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set session_packet_pool_packet_bundle = [1000].
2024-01-18 14:38:42.157944 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set skip_informatic_reference_query = [0].
2024-01-18 14:38:42.157951 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set skip_slave_with_bad_gtid = [0].
2024-01-18 14:38:42.157958 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set skip_space_rep_build = [0].
2024-01-18 14:38:42.157965 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set slave_dbscale_mode = [0].
2024-01-18 14:38:42.157972 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set slow_query_time = [0].
2024-01-18 14:38:42.157979 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set socket_base_timeout = [5].
2024-01-18 14:38:42.157987 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set source_wait_write_lock_retry = [3].
2024-01-18 14:38:42.157994 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set spark_dst_schema = [dbscale_spark_tmp].
2024-01-18 14:38:42.158002 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set spark_executor_cores = [1].
2024-01-18 14:38:42.158008 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set spark_executor_memory = [2g].
2024-01-18 14:38:42.158020 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set spark_master = [local[2]].
2024-01-18 14:38:42.158027 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set spark_password = [u»jW].
2024-01-18 14:38:42.158035 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set spark_user = [root].
2024-01-18 14:38:42.158042 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set spark_without_insert = [0].
2024-01-18 14:38:42.158049 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set sparksql_agent_location = [127.0.0.1].
2024-01-18 14:38:42.158057 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set sparksql_agent_port = [19090].
2024-01-18 14:38:42.158064 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set ssl_ca_file = [./ssl-files/ca.pem].
2024-01-18 14:38:42.158071 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set ssl_ca_path = [./ssl-files].
2024-01-18 14:38:42.158078 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set ssl_cert_file = [./ssl-files/server-cert.pem].
2024-01-18 14:38:42.158086 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set ssl_key_file = [./ssl-files/server-key.pem].
2024-01-18 14:38:42.158093 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set stmt_max_threads = [1].
2024-01-18 14:38:42.158100 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set support_log_warning_info = [1].
2024-01-18 14:38:42.158107 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set support_prepare_rwsplit = [0].
2024-01-18 14:38:42.158114 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set support_show_warning = [1].
2024-01-18 14:38:42.158170 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set supreme_admin_user = [admin].
2024-01-18 14:38:42.158178 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set swap_handler_num = [256].
2024-01-18 14:38:42.158185 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set tcp_keepalive = [TRUE].
2024-01-18 14:38:42.158193 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set tcp_keepcnt = [3].
2024-01-18 14:38:42.158199 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set tcp_keepidle = [1].
2024-01-18 14:38:42.158207 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set tcp_keepintvl = [1].
2024-01-18 14:38:42.158214 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set tcp_multiple_times = [10].
2024-01-18 14:38:42.158221 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set tcp_user_timeout = [6000].
2024-01-18 14:38:42.158228 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set test_wakeup_error = [0].
2024-01-18 14:38:42.158235 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set trx_pk_duplicate_not_rollback = [FALSE].
2024-01-18 14:38:42.158243 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set unexpected_gtid_threshold_value = [5].
2024-01-18 14:38:42.158250 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set unsafe_redo_sql_action = [0].
2024-01-18 14:38:42.158257 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set update_delete_quick_limit = [0].
2024-01-18 14:38:42.158264 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set use_load_data_for_insert_select = [0].
2024-01-18 14:38:42.158271 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set use_partial_parse = [1].
2024-01-18 14:38:42.158279 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set use_spark = [0].
2024-01-18 14:38:42.158291 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set use_table_for_one_column_subquery = [1].
2024-01-18 14:38:42.158298 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set user_priority_high_access_pct = [25].
2024-01-18 14:38:42.158305 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set user_priority_mid_access_pct = [25].
2024-01-18 14:38:42.158312 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set wait_timeout = [172800].
2024-01-18 14:38:42.158320 (139663922689792) [ INFO] <keepalive_message.cc:702> Dynamic configuration set zookeeper_check_retry = [600].
2024-01-18 14:38:42.158350 (139663922689792) [ INFO] <multiple.cc:2948> Waiting for share list message.
2024-01-18 14:38:42.158360 (139663922689792) [ INFO] <multiple.cc:2948> Waiting for share list message.
2024-01-18 14:38:43.142726 (139665061951680) [ INFO] <data_source.h:250> datasource: [normal_0_normal_0_1] get working server num returned: [1]
2024-01-18 14:38:43.142814 (139665061951680) [ INFO] <data_source.h:250> datasource: [normal_0_normal_0_2] get working server num returned: [1]
2024-01-18 14:38:43.142833 (139665061951680) [ INFO] <data_source.h:250> datasource: [normal_0_read] get working server num returned: [2]
2024-01-18 14:38:43.142851 (139665061951680) [ INFO] <data_source.h:250> datasource: [normal_0_normal_0_0] get working server num returned: [1]
2024-01-18 14:38:43.142867 (139665061951680) [ INFO] <data_source.h:250> datasource: [normal_0] get working server num returned: [3]
2024-01-18 14:38:43.142884 (139665061951680) [ INFO] <data_source.cc:7182> In can_get_connection Rep source: [normal_0] is a valid semi sync ds now, setting is_in_failover to false
2024-01-18 14:38:43.143069 (139665061951680) [ INFO] <mysql_config.cc:87> Ignore init_backend_tables for slave dbscale.
2024-01-18 14:38:43.152061 (139665061951680) [ INFO] <config.cc:766> start init_partitioned_table_config
2024-01-18 14:38:43.152932 (139665061951680) [ INFO] <config.cc:881> end init_partitioned_table_config
2024-01-18 14:38:43.154435 (139665061951680) [ INFO] <config.cc:328> No view when init view config.
2024-01-18 14:38:43.154462 (139665061951680) [ INFO] <config.cc:1107> Init the mirror user.
2024-01-18 14:38:43.165450 (139663905904384) [ INFO] <message_service.cc:1395> There is no message task, no need to restore.
2024-01-18 14:38:43.181612 (139665061951680) [ INFO] <main.cc:169> Starting event loop.

```
> 初始化完成标志：`2024-01-18 14:38:43.181612 (139665061951680) [ INFO] <main.cc:169> Starting event loop.`

### 查看启动次数
```sql
[mysql@node76 ~]$ grep 'Starting event loop' /data/dbscalelog/dbscale.log
2024-01-16 16:38:32.220978 (139679270717632) [ INFO] <main.cc:169> Starting event loop.
2024-01-17 09:34:13.945048 (140071573919936) [ INFO] <main.cc:169> Starting event loop.
2024-01-17 14:15:34.531186 (140110942742720) [ INFO] <main.cc:169> Starting event loop.

```













