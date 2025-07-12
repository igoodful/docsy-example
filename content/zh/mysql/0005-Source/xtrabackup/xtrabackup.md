---
title: xtrabackup
description: xtrabackup
date: 2023-10-30
weight: 10000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}



### 压缩 --compress
1. --compress
   1. XtraBackup 8.0.33-28之前默认压缩算法为QuickLZ，8.0.34-29中，默认压缩算法为：ZSTD。ZSTD是一种快速无损压缩算法，针对实时压缩场景和更好的压缩比，生成*.zst文件，默认为1， 开始版本为：8.0.30。
   2. 8.0.33-28之前举例说明：`xtrabackup --backup --compress --target-dir=/data/backup`
   3. 8.0.34-29开始举例说明：`xtrabackup --backup --compress --target-dir=/data/backup` 和 `xtrabackup --backup --compress=zstd --target-dir=/data/backup`
   4. 指定ZSTD压缩级别：`--compress-zstd-level=1~19`（压缩级别提供了压缩速度和实现的压缩量之间的精细权衡。较低的压缩级别提供更快的速度，但文件大小更大。例如，如果速度最重要，则可以使用级别 1；如果尺寸最重要，则可以使用级别 19），参考：[compress-zstd-level](https://forums.percona.com/t/what-does-the-zstd-level-parameter-actually-do/24788)
   5. 8.0.34-29开始举例说明：`xtrabackup --backup --compress --compress-zstd-level=1 --target-dir=/data/backup`
   6. 开启并行压缩：`--compress-threads=8`，`xtrabackup --backup --compress=zstd --parallel=16 --compress-threads=8 --target-dir=/backup`
   7. 并行备份、并行压缩：`xtrabackup --backup --compress=zstd --parallel=16 --compress-threads=8 --target-dir=/data/backup` 参考：[zstd](https://www.percona.com/blog/get-your-backup-to-half-of-its-size-introducing-zstd-support-in-percona-xtrabackup/)
2. --compress=lz4 （要使用LZ4压缩算法压缩文件） `xtrabackup --backup --compress=lz4 --target-dir=/data/backup`
3. --decompress （要解压缩备份中的所有文件） `xtrabackup --decompress --target-dir=/data/compressed/`
   1. `xtrabackup --decompress --parallel=16 --target-dir=/backup`




{{< /alert >}}
### 全备
```sql
[root@dev backup]# /root/local/xtrabackup_8032/bin/xtrabackup --user=root --password=root --host=172.17.137.12 --port=7032 --backup --target-dir=/root/local/backup/
2024-02-02T00:47:18.464910+08:00 0 [Note] [MY-011825] [Xtrabackup] recognized client arguments: --user=root --password=* --host=172.17.137.12 --port=7032 --backup=1 --target-dir=/root/local/backup/
/root/local/xtrabackup_8032/bin/xtrabackup version 8.0.32-26 based on MySQL server 8.0.32 Linux (x86_64) (revision id: )
240202 00:47:18  version_check Connecting to MySQL server with DSN 'dbi:mysql:;mysql_read_default_group=xtrabackup;host=172.17.137.12;port=7032' as 'root'  (using password: YES).
Failed to connect to MySQL server as DBD::mysql module is not installed at - line 1548.
2024-02-02T00:47:18.614255+08:00 0 [Note] [MY-011825] [Xtrabackup] Connecting to MySQL server host: 172.17.137.12, user: root, password: set, port: 7032, socket: not set
2024-02-02T00:47:18.661526+08:00 0 [Note] [MY-011825] [Xtrabackup] Using server version 8.0.32-debug
2024-02-02T00:47:18.685829+08:00 0 [Note] [MY-011825] [Xtrabackup] Executing LOCK INSTANCE FOR BACKUP ...
2024-02-02T00:47:18.891070+08:00 0 [Note] [MY-011825] [Xtrabackup] uses posix_fadvise().
2024-02-02T00:47:18.891174+08:00 0 [Note] [MY-011825] [Xtrabackup] cd to /root/local/mysql_7032/data/
2024-02-02T00:47:18.891208+08:00 0 [Note] [MY-011825] [Xtrabackup] open files limit requested 0, set to 1024000
2024-02-02T00:47:18.895661+08:00 0 [Note] [MY-011825] [Xtrabackup] using the following InnoDB configuration:
2024-02-02T00:47:18.895713+08:00 0 [Note] [MY-011825] [Xtrabackup] innodb_data_home_dir = .
2024-02-02T00:47:18.895725+08:00 0 [Note] [MY-011825] [Xtrabackup] innodb_data_file_path = ibdata1:12M:autoextend
2024-02-02T00:47:18.895814+08:00 0 [Note] [MY-011825] [Xtrabackup] innodb_log_group_home_dir = ./
2024-02-02T00:47:18.895834+08:00 0 [Note] [MY-011825] [Xtrabackup] innodb_log_files_in_group = 2
2024-02-02T00:47:18.895846+08:00 0 [Note] [MY-011825] [Xtrabackup] innodb_log_file_size = 1073741824
2024-02-02T00:47:18.909541+08:00 0 [Note] [MY-011825] [Xtrabackup] inititialize_service_handles suceeded
2024-02-02T00:47:19.248926+08:00 0 [Note] [MY-011825] [Xtrabackup] Connecting to MySQL server host: 172.17.137.12, user: root, password: set, port: 7032, socket: not set
2024-02-02T00:47:19.283401+08:00 0 [Note] [MY-011825] [Xtrabackup] Redo Log Archiving is not set up.
2024-02-02T00:47:19.428944+08:00 1 [Note] [MY-011825] [Xtrabackup] >> log scanned up to (30759344)
2024-02-02T00:47:19.651970+08:00 0 [Note] [MY-011825] [Xtrabackup] Generating a list of tablespaces
2024-02-02T00:47:19.652442+08:00 0 [Note] [MY-012204] [InnoDB] Scanning './'
2024-02-02T00:47:19.656728+08:00 0 [Note] [MY-012208] [InnoDB] Completed space ID check of 2 files.
2024-02-02T00:47:19.658653+08:00 0 [Warning] [MY-012091] [InnoDB] Allocated tablespace ID 1 for sys/sys_config, old maximum was 0
2024-02-02T00:47:19.663118+08:00 0 [Note] [MY-013252] [InnoDB] Using undo tablespace './undo_001'.
2024-02-02T00:47:19.663895+08:00 0 [Note] [MY-013252] [InnoDB] Using undo tablespace './undo_002'.
2024-02-02T00:47:19.665637+08:00 0 [Note] [MY-012910] [InnoDB] Opened 2 existing undo tablespaces.
2024-02-02T00:47:19.666924+08:00 2 [Note] [MY-011825] [Xtrabackup] Copying ./ibdata1 to /root/local/backup/ibdata1
2024-02-02T00:47:19.788329+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Copying ./ibdata1 to /root/local/backup/ibdata1
2024-02-02T00:47:19.796925+08:00 2 [Note] [MY-011825] [Xtrabackup] Copying ./sys/sys_config.ibd to /root/local/backup/sys/sys_config.ibd
2024-02-02T00:47:19.797893+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Copying ./sys/sys_config.ibd to /root/local/backup/sys/sys_config.ibd
2024-02-02T00:47:19.799921+08:00 2 [Note] [MY-011825] [Xtrabackup] Copying ./apple/info.ibd to /root/local/backup/apple/info.ibd
2024-02-02T00:47:19.800827+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Copying ./apple/info.ibd to /root/local/backup/apple/info.ibd
2024-02-02T00:47:19.802708+08:00 2 [Note] [MY-011825] [Xtrabackup] Copying ./apple/sbtest2.ibd to /root/local/backup/apple/sbtest2.ibd
2024-02-02T00:47:19.879545+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Copying ./apple/sbtest2.ibd to /root/local/backup/apple/sbtest2.ibd
2024-02-02T00:47:19.900948+08:00 2 [Note] [MY-011825] [Xtrabackup] Copying ./apple/sbtest3.ibd to /root/local/backup/apple/sbtest3.ibd
2024-02-02T00:47:20.024001+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Copying ./apple/sbtest3.ibd to /root/local/backup/apple/sbtest3.ibd
2024-02-02T00:47:20.044897+08:00 2 [Note] [MY-011825] [Xtrabackup] Copying ./apple/sbtest1.ibd to /root/local/backup/apple/sbtest1.ibd
2024-02-02T00:47:20.156961+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Copying ./apple/sbtest1.ibd to /root/local/backup/apple/sbtest1.ibd
2024-02-02T00:47:20.173670+08:00 2 [Note] [MY-011825] [Xtrabackup] Copying ./apple/sbtest4.ibd to /root/local/backup/apple/sbtest4.ibd
2024-02-02T00:47:20.290701+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Copying ./apple/sbtest4.ibd to /root/local/backup/apple/sbtest4.ibd
2024-02-02T00:47:20.305028+08:00 2 [Note] [MY-011825] [Xtrabackup] Copying ./mysql.ibd to /root/local/backup/mysql.ibd
2024-02-02T00:47:20.429409+08:00 1 [Note] [MY-011825] [Xtrabackup] >> log scanned up to (30759344)
2024-02-02T00:47:20.554985+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Copying ./mysql.ibd to /root/local/backup/mysql.ibd
2024-02-02T00:47:20.566395+08:00 2 [Note] [MY-011825] [Xtrabackup] Copying ./undo_002 to /root/local/backup/undo_002
2024-02-02T00:47:20.748397+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Copying ./undo_002 to /root/local/backup/undo_002
2024-02-02T00:47:20.767163+08:00 2 [Note] [MY-011825] [Xtrabackup] Copying ./undo_001 to /root/local/backup/undo_001
2024-02-02T00:47:20.914214+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Copying ./undo_001 to /root/local/backup/undo_001
2024-02-02T00:47:21.429879+08:00 1 [Note] [MY-011825] [Xtrabackup] >> log scanned up to (30759344)
2024-02-02T00:47:21.666334+08:00 0 [Note] [MY-011825] [Xtrabackup] Starting to backup non-InnoDB tables and files
2024-02-02T00:47:21.667301+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying mysql/general_log_213.sdi to /root/local/backup/mysql/general_log_213.sdi
2024-02-02T00:47:21.667857+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying mysql/general_log_213.sdi to /root/local/backup/mysql/general_log_213.sdi
2024-02-02T00:47:21.669807+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying mysql/general_log.CSM to /root/local/backup/mysql/general_log.CSM
2024-02-02T00:47:21.670158+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying mysql/general_log.CSM to /root/local/backup/mysql/general_log.CSM
2024-02-02T00:47:21.671541+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying mysql/general_log.CSV to /root/local/backup/mysql/general_log.CSV
2024-02-02T00:47:21.671576+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying mysql/general_log.CSV to /root/local/backup/mysql/general_log.CSV
2024-02-02T00:47:21.672738+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying mysql/slow_log_214.sdi to /root/local/backup/mysql/slow_log_214.sdi
2024-02-02T00:47:21.673037+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying mysql/slow_log_214.sdi to /root/local/backup/mysql/slow_log_214.sdi
2024-02-02T00:47:21.674444+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying mysql/slow_log.CSM to /root/local/backup/mysql/slow_log.CSM
2024-02-02T00:47:21.674738+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying mysql/slow_log.CSM to /root/local/backup/mysql/slow_log.CSM
2024-02-02T00:47:21.675974+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying mysql/slow_log.CSV to /root/local/backup/mysql/slow_log.CSV
2024-02-02T00:47:21.676004+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying mysql/slow_log.CSV to /root/local/backup/mysql/slow_log.CSV
2024-02-02T00:47:21.677505+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_statement_123.sdi to /root/local/backup/performance_schema/events_statement_123.sdi
2024-02-02T00:47:21.678303+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_statement_123.sdi to /root/local/backup/performance_schema/events_statement_123.sdi
2024-02-02T00:47:21.679904+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_statement_124.sdi to /root/local/backup/performance_schema/events_statement_124.sdi
2024-02-02T00:47:21.680296+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_statement_124.sdi to /root/local/backup/performance_schema/events_statement_124.sdi
2024-02-02T00:47:21.681709+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/cond_instances_82.sdi to /root/local/backup/performance_schema/cond_instances_82.sdi
2024-02-02T00:47:21.682028+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/cond_instances_82.sdi to /root/local/backup/performance_schema/cond_instances_82.sdi
2024-02-02T00:47:21.683423+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/error_log_83.sdi to /root/local/backup/performance_schema/error_log_83.sdi
2024-02-02T00:47:21.683759+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/error_log_83.sdi to /root/local/backup/performance_schema/error_log_83.sdi
2024-02-02T00:47:21.684956+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_waits_cur_84.sdi to /root/local/backup/performance_schema/events_waits_cur_84.sdi
2024-02-02T00:47:21.685332+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_waits_cur_84.sdi to /root/local/backup/performance_schema/events_waits_cur_84.sdi
2024-02-02T00:47:21.686667+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_waits_his_85.sdi to /root/local/backup/performance_schema/events_waits_his_85.sdi
2024-02-02T00:47:21.687071+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_waits_his_85.sdi to /root/local/backup/performance_schema/events_waits_his_85.sdi
2024-02-02T00:47:21.688283+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_waits_his_86.sdi to /root/local/backup/performance_schema/events_waits_his_86.sdi
2024-02-02T00:47:21.688654+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_waits_his_86.sdi to /root/local/backup/performance_schema/events_waits_his_86.sdi
2024-02-02T00:47:21.689915+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_waits_sum_87.sdi to /root/local/backup/performance_schema/events_waits_sum_87.sdi
2024-02-02T00:47:21.690199+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_waits_sum_87.sdi to /root/local/backup/performance_schema/events_waits_sum_87.sdi
2024-02-02T00:47:21.691445+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_waits_sum_88.sdi to /root/local/backup/performance_schema/events_waits_sum_88.sdi
2024-02-02T00:47:21.691731+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_waits_sum_88.sdi to /root/local/backup/performance_schema/events_waits_sum_88.sdi
2024-02-02T00:47:21.692915+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_waits_sum_89.sdi to /root/local/backup/performance_schema/events_waits_sum_89.sdi
2024-02-02T00:47:21.693200+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_waits_sum_89.sdi to /root/local/backup/performance_schema/events_waits_sum_89.sdi
2024-02-02T00:47:21.694426+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_waits_sum_90.sdi to /root/local/backup/performance_schema/events_waits_sum_90.sdi
2024-02-02T00:47:21.694714+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_waits_sum_90.sdi to /root/local/backup/performance_schema/events_waits_sum_90.sdi
2024-02-02T00:47:21.695806+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_waits_sum_91.sdi to /root/local/backup/performance_schema/events_waits_sum_91.sdi
2024-02-02T00:47:21.696088+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_waits_sum_91.sdi to /root/local/backup/performance_schema/events_waits_sum_91.sdi
2024-02-02T00:47:21.697283+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_waits_sum_92.sdi to /root/local/backup/performance_schema/events_waits_sum_92.sdi
2024-02-02T00:47:21.697601+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_waits_sum_92.sdi to /root/local/backup/performance_schema/events_waits_sum_92.sdi
2024-02-02T00:47:21.698809+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/file_instances_93.sdi to /root/local/backup/performance_schema/file_instances_93.sdi
2024-02-02T00:47:21.699081+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/file_instances_93.sdi to /root/local/backup/performance_schema/file_instances_93.sdi
2024-02-02T00:47:21.700179+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/file_summary_by__94.sdi to /root/local/backup/performance_schema/file_summary_by__94.sdi
2024-02-02T00:47:21.700574+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/file_summary_by__94.sdi to /root/local/backup/performance_schema/file_summary_by__94.sdi
2024-02-02T00:47:21.701912+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/file_summary_by__95.sdi to /root/local/backup/performance_schema/file_summary_by__95.sdi
2024-02-02T00:47:21.702246+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/file_summary_by__95.sdi to /root/local/backup/performance_schema/file_summary_by__95.sdi
2024-02-02T00:47:21.703497+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/host_cache_96.sdi to /root/local/backup/performance_schema/host_cache_96.sdi
2024-02-02T00:47:21.703846+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/host_cache_96.sdi to /root/local/backup/performance_schema/host_cache_96.sdi
2024-02-02T00:47:21.704995+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/mutex_instances_97.sdi to /root/local/backup/performance_schema/mutex_instances_97.sdi
2024-02-02T00:47:21.705265+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/mutex_instances_97.sdi to /root/local/backup/performance_schema/mutex_instances_97.sdi
2024-02-02T00:47:21.706504+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/objects_summary__98.sdi to /root/local/backup/performance_schema/objects_summary__98.sdi
2024-02-02T00:47:21.706762+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/objects_summary__98.sdi to /root/local/backup/performance_schema/objects_summary__98.sdi
2024-02-02T00:47:21.707724+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/performance_time_99.sdi to /root/local/backup/performance_schema/performance_time_99.sdi
2024-02-02T00:47:21.708005+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/performance_time_99.sdi to /root/local/backup/performance_schema/performance_time_99.sdi
2024-02-02T00:47:21.709175+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/processlist_100.sdi to /root/local/backup/performance_schema/processlist_100.sdi
2024-02-02T00:47:21.709448+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/processlist_100.sdi to /root/local/backup/performance_schema/processlist_100.sdi
2024-02-02T00:47:21.710578+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/rwlock_instances_101.sdi to /root/local/backup/performance_schema/rwlock_instances_101.sdi
2024-02-02T00:47:21.710835+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/rwlock_instances_101.sdi to /root/local/backup/performance_schema/rwlock_instances_101.sdi
2024-02-02T00:47:21.712068+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/setup_actors_102.sdi to /root/local/backup/performance_schema/setup_actors_102.sdi
2024-02-02T00:47:21.712394+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/setup_actors_102.sdi to /root/local/backup/performance_schema/setup_actors_102.sdi
2024-02-02T00:47:21.713520+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/setup_consumers_103.sdi to /root/local/backup/performance_schema/setup_consumers_103.sdi
2024-02-02T00:47:21.713767+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/setup_consumers_103.sdi to /root/local/backup/performance_schema/setup_consumers_103.sdi
2024-02-02T00:47:21.714779+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/setup_instrument_104.sdi to /root/local/backup/performance_schema/setup_instrument_104.sdi
2024-02-02T00:47:21.715040+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/setup_instrument_104.sdi to /root/local/backup/performance_schema/setup_instrument_104.sdi
2024-02-02T00:47:21.716379+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/setup_objects_105.sdi to /root/local/backup/performance_schema/setup_objects_105.sdi
2024-02-02T00:47:21.716653+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/setup_objects_105.sdi to /root/local/backup/performance_schema/setup_objects_105.sdi
2024-02-02T00:47:21.717757+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/setup_threads_106.sdi to /root/local/backup/performance_schema/setup_threads_106.sdi
2024-02-02T00:47:21.718014+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/setup_threads_106.sdi to /root/local/backup/performance_schema/setup_threads_106.sdi
2024-02-02T00:47:21.719099+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/table_io_waits_s_107.sdi to /root/local/backup/performance_schema/table_io_waits_s_107.sdi
2024-02-02T00:47:21.719456+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/table_io_waits_s_107.sdi to /root/local/backup/performance_schema/table_io_waits_s_107.sdi
2024-02-02T00:47:21.720535+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/table_io_waits_s_108.sdi to /root/local/backup/performance_schema/table_io_waits_s_108.sdi
2024-02-02T00:47:21.720839+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/table_io_waits_s_108.sdi to /root/local/backup/performance_schema/table_io_waits_s_108.sdi
2024-02-02T00:47:21.721777+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/table_lock_waits_109.sdi to /root/local/backup/performance_schema/table_lock_waits_109.sdi
2024-02-02T00:47:21.722163+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/table_lock_waits_109.sdi to /root/local/backup/performance_schema/table_lock_waits_109.sdi
2024-02-02T00:47:21.723594+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/threads_110.sdi to /root/local/backup/performance_schema/threads_110.sdi
2024-02-02T00:47:21.723883+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/threads_110.sdi to /root/local/backup/performance_schema/threads_110.sdi
2024-02-02T00:47:21.724964+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_stages_cu_111.sdi to /root/local/backup/performance_schema/events_stages_cu_111.sdi
2024-02-02T00:47:21.725228+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_stages_cu_111.sdi to /root/local/backup/performance_schema/events_stages_cu_111.sdi
2024-02-02T00:47:21.726407+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_stages_hi_112.sdi to /root/local/backup/performance_schema/events_stages_hi_112.sdi
2024-02-02T00:47:21.726686+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_stages_hi_112.sdi to /root/local/backup/performance_schema/events_stages_hi_112.sdi
2024-02-02T00:47:21.727805+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_stages_hi_113.sdi to /root/local/backup/performance_schema/events_stages_hi_113.sdi
2024-02-02T00:47:21.728045+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_stages_hi_113.sdi to /root/local/backup/performance_schema/events_stages_hi_113.sdi
2024-02-02T00:47:21.728884+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_stages_su_114.sdi to /root/local/backup/performance_schema/events_stages_su_114.sdi
2024-02-02T00:47:21.729192+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_stages_su_114.sdi to /root/local/backup/performance_schema/events_stages_su_114.sdi
2024-02-02T00:47:21.730645+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_stages_su_115.sdi to /root/local/backup/performance_schema/events_stages_su_115.sdi
2024-02-02T00:47:21.731026+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_stages_su_115.sdi to /root/local/backup/performance_schema/events_stages_su_115.sdi
2024-02-02T00:47:21.732228+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_stages_su_116.sdi to /root/local/backup/performance_schema/events_stages_su_116.sdi
2024-02-02T00:47:21.732657+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_stages_su_116.sdi to /root/local/backup/performance_schema/events_stages_su_116.sdi
2024-02-02T00:47:21.733706+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_stages_su_117.sdi to /root/local/backup/performance_schema/events_stages_su_117.sdi
2024-02-02T00:47:21.733997+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_stages_su_117.sdi to /root/local/backup/performance_schema/events_stages_su_117.sdi
2024-02-02T00:47:21.735201+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_stages_su_118.sdi to /root/local/backup/performance_schema/events_stages_su_118.sdi
2024-02-02T00:47:21.735513+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_stages_su_118.sdi to /root/local/backup/performance_schema/events_stages_su_118.sdi
2024-02-02T00:47:21.736448+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_statement_119.sdi to /root/local/backup/performance_schema/events_statement_119.sdi
2024-02-02T00:47:21.736877+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_statement_119.sdi to /root/local/backup/performance_schema/events_statement_119.sdi
2024-02-02T00:47:21.738029+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_statement_120.sdi to /root/local/backup/performance_schema/events_statement_120.sdi
2024-02-02T00:47:21.738409+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_statement_120.sdi to /root/local/backup/performance_schema/events_statement_120.sdi
2024-02-02T00:47:21.739496+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_statement_121.sdi to /root/local/backup/performance_schema/events_statement_121.sdi
2024-02-02T00:47:21.739845+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_statement_121.sdi to /root/local/backup/performance_schema/events_statement_121.sdi
2024-02-02T00:47:21.740879+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_statement_122.sdi to /root/local/backup/performance_schema/events_statement_122.sdi
2024-02-02T00:47:21.741201+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_statement_122.sdi to /root/local/backup/performance_schema/events_statement_122.sdi
2024-02-02T00:47:21.742786+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_statement_125.sdi to /root/local/backup/performance_schema/events_statement_125.sdi
2024-02-02T00:47:21.743443+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_statement_125.sdi to /root/local/backup/performance_schema/events_statement_125.sdi
2024-02-02T00:47:21.744624+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_statement_126.sdi to /root/local/backup/performance_schema/events_statement_126.sdi
2024-02-02T00:47:21.745080+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_statement_126.sdi to /root/local/backup/performance_schema/events_statement_126.sdi
2024-02-02T00:47:21.746115+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_statement_127.sdi to /root/local/backup/performance_schema/events_statement_127.sdi
2024-02-02T00:47:21.746515+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_statement_127.sdi to /root/local/backup/performance_schema/events_statement_127.sdi
2024-02-02T00:47:21.747692+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_statement_128.sdi to /root/local/backup/performance_schema/events_statement_128.sdi
2024-02-02T00:47:21.748045+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_statement_128.sdi to /root/local/backup/performance_schema/events_statement_128.sdi
2024-02-02T00:47:21.748954+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_statement_129.sdi to /root/local/backup/performance_schema/events_statement_129.sdi
2024-02-02T00:47:21.749216+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_statement_129.sdi to /root/local/backup/performance_schema/events_statement_129.sdi
2024-02-02T00:47:21.750410+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_statement_130.sdi to /root/local/backup/performance_schema/events_statement_130.sdi
2024-02-02T00:47:21.750771+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_statement_130.sdi to /root/local/backup/performance_schema/events_statement_130.sdi
2024-02-02T00:47:21.751716+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_transacti_131.sdi to /root/local/backup/performance_schema/events_transacti_131.sdi
2024-02-02T00:47:21.752137+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_transacti_131.sdi to /root/local/backup/performance_schema/events_transacti_131.sdi
2024-02-02T00:47:21.753203+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_transacti_132.sdi to /root/local/backup/performance_schema/events_transacti_132.sdi
2024-02-02T00:47:21.753520+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_transacti_132.sdi to /root/local/backup/performance_schema/events_transacti_132.sdi
2024-02-02T00:47:21.754517+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_transacti_133.sdi to /root/local/backup/performance_schema/events_transacti_133.sdi
2024-02-02T00:47:21.754902+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_transacti_133.sdi to /root/local/backup/performance_schema/events_transacti_133.sdi
2024-02-02T00:47:21.755815+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_transacti_134.sdi to /root/local/backup/performance_schema/events_transacti_134.sdi
2024-02-02T00:47:21.756070+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_transacti_134.sdi to /root/local/backup/performance_schema/events_transacti_134.sdi
2024-02-02T00:47:21.757193+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_transacti_135.sdi to /root/local/backup/performance_schema/events_transacti_135.sdi
2024-02-02T00:47:21.757619+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_transacti_135.sdi to /root/local/backup/performance_schema/events_transacti_135.sdi
2024-02-02T00:47:21.758597+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_transacti_136.sdi to /root/local/backup/performance_schema/events_transacti_136.sdi
2024-02-02T00:47:21.758996+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_transacti_136.sdi to /root/local/backup/performance_schema/events_transacti_136.sdi
2024-02-02T00:47:21.759979+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_transacti_137.sdi to /root/local/backup/performance_schema/events_transacti_137.sdi
2024-02-02T00:47:21.760281+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_transacti_137.sdi to /root/local/backup/performance_schema/events_transacti_137.sdi
2024-02-02T00:47:21.761401+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_transacti_138.sdi to /root/local/backup/performance_schema/events_transacti_138.sdi
2024-02-02T00:47:21.761753+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_transacti_138.sdi to /root/local/backup/performance_schema/events_transacti_138.sdi
2024-02-02T00:47:21.762681+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_errors_su_139.sdi to /root/local/backup/performance_schema/events_errors_su_139.sdi
2024-02-02T00:47:21.762944+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_errors_su_139.sdi to /root/local/backup/performance_schema/events_errors_su_139.sdi
2024-02-02T00:47:21.764147+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_errors_su_140.sdi to /root/local/backup/performance_schema/events_errors_su_140.sdi
2024-02-02T00:47:21.764493+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_errors_su_140.sdi to /root/local/backup/performance_schema/events_errors_su_140.sdi
2024-02-02T00:47:21.765458+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_errors_su_141.sdi to /root/local/backup/performance_schema/events_errors_su_141.sdi
2024-02-02T00:47:21.765818+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_errors_su_141.sdi to /root/local/backup/performance_schema/events_errors_su_141.sdi
2024-02-02T00:47:21.766851+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_errors_su_142.sdi to /root/local/backup/performance_schema/events_errors_su_142.sdi
2024-02-02T00:47:21.767105+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_errors_su_142.sdi to /root/local/backup/performance_schema/events_errors_su_142.sdi
2024-02-02T00:47:21.768188+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/events_errors_su_143.sdi to /root/local/backup/performance_schema/events_errors_su_143.sdi
2024-02-02T00:47:21.768458+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/events_errors_su_143.sdi to /root/local/backup/performance_schema/events_errors_su_143.sdi
2024-02-02T00:47:21.769384+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/users_144.sdi to /root/local/backup/performance_schema/users_144.sdi
2024-02-02T00:47:21.769722+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/users_144.sdi to /root/local/backup/performance_schema/users_144.sdi
2024-02-02T00:47:21.770932+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/accounts_145.sdi to /root/local/backup/performance_schema/accounts_145.sdi
2024-02-02T00:47:21.771200+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/accounts_145.sdi to /root/local/backup/performance_schema/accounts_145.sdi
2024-02-02T00:47:21.772698+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/hosts_146.sdi to /root/local/backup/performance_schema/hosts_146.sdi
2024-02-02T00:47:21.773012+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/hosts_146.sdi to /root/local/backup/performance_schema/hosts_146.sdi
2024-02-02T00:47:21.774054+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/socket_instances_147.sdi to /root/local/backup/performance_schema/socket_instances_147.sdi
2024-02-02T00:47:21.774329+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/socket_instances_147.sdi to /root/local/backup/performance_schema/socket_instances_147.sdi
2024-02-02T00:47:21.775411+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/socket_summary_b_148.sdi to /root/local/backup/performance_schema/socket_summary_b_148.sdi
2024-02-02T00:47:21.775721+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/socket_summary_b_148.sdi to /root/local/backup/performance_schema/socket_summary_b_148.sdi
2024-02-02T00:47:21.776776+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/socket_summary_b_149.sdi to /root/local/backup/performance_schema/socket_summary_b_149.sdi
2024-02-02T00:47:21.777146+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/socket_summary_b_149.sdi to /root/local/backup/performance_schema/socket_summary_b_149.sdi
2024-02-02T00:47:21.778109+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/session_connect__150.sdi to /root/local/backup/performance_schema/session_connect__150.sdi
2024-02-02T00:47:21.778356+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/session_connect__150.sdi to /root/local/backup/performance_schema/session_connect__150.sdi
2024-02-02T00:47:21.779283+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/session_account__151.sdi to /root/local/backup/performance_schema/session_account__151.sdi
2024-02-02T00:47:21.779549+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/session_account__151.sdi to /root/local/backup/performance_schema/session_account__151.sdi
2024-02-02T00:47:21.780355+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/keyring_keys_152.sdi to /root/local/backup/performance_schema/keyring_keys_152.sdi
2024-02-02T00:47:21.780628+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/keyring_keys_152.sdi to /root/local/backup/performance_schema/keyring_keys_152.sdi
2024-02-02T00:47:21.781544+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/memory_summary_g_153.sdi to /root/local/backup/performance_schema/memory_summary_g_153.sdi
2024-02-02T00:47:21.781802+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/memory_summary_g_153.sdi to /root/local/backup/performance_schema/memory_summary_g_153.sdi
2024-02-02T00:47:21.782844+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/memory_summary_b_154.sdi to /root/local/backup/performance_schema/memory_summary_b_154.sdi
2024-02-02T00:47:21.783242+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/memory_summary_b_154.sdi to /root/local/backup/performance_schema/memory_summary_b_154.sdi
2024-02-02T00:47:21.784298+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/memory_summary_b_155.sdi to /root/local/backup/performance_schema/memory_summary_b_155.sdi
2024-02-02T00:47:21.784626+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/memory_summary_b_155.sdi to /root/local/backup/performance_schema/memory_summary_b_155.sdi
2024-02-02T00:47:21.785641+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/memory_summary_b_156.sdi to /root/local/backup/performance_schema/memory_summary_b_156.sdi
2024-02-02T00:47:21.785925+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/memory_summary_b_156.sdi to /root/local/backup/performance_schema/memory_summary_b_156.sdi
2024-02-02T00:47:21.786697+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/memory_summary_b_157.sdi to /root/local/backup/performance_schema/memory_summary_b_157.sdi
2024-02-02T00:47:21.786965+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/memory_summary_b_157.sdi to /root/local/backup/performance_schema/memory_summary_b_157.sdi
2024-02-02T00:47:21.788042+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/table_handles_158.sdi to /root/local/backup/performance_schema/table_handles_158.sdi
2024-02-02T00:47:21.788385+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/table_handles_158.sdi to /root/local/backup/performance_schema/table_handles_158.sdi
2024-02-02T00:47:21.789210+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/metadata_locks_159.sdi to /root/local/backup/performance_schema/metadata_locks_159.sdi
2024-02-02T00:47:21.789575+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/metadata_locks_159.sdi to /root/local/backup/performance_schema/metadata_locks_159.sdi
2024-02-02T00:47:21.790485+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/data_locks_160.sdi to /root/local/backup/performance_schema/data_locks_160.sdi
2024-02-02T00:47:21.790761+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/data_locks_160.sdi to /root/local/backup/performance_schema/data_locks_160.sdi
2024-02-02T00:47:21.791742+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/data_lock_waits_161.sdi to /root/local/backup/performance_schema/data_lock_waits_161.sdi
2024-02-02T00:47:21.792019+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/data_lock_waits_161.sdi to /root/local/backup/performance_schema/data_lock_waits_161.sdi
2024-02-02T00:47:21.792886+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/replication_conn_162.sdi to /root/local/backup/performance_schema/replication_conn_162.sdi
2024-02-02T00:47:21.793197+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/replication_conn_162.sdi to /root/local/backup/performance_schema/replication_conn_162.sdi
2024-02-02T00:47:21.794355+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/replication_grou_163.sdi to /root/local/backup/performance_schema/replication_grou_163.sdi
2024-02-02T00:47:21.794637+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/replication_grou_163.sdi to /root/local/backup/performance_schema/replication_grou_163.sdi
2024-02-02T00:47:21.795518+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/replication_conn_164.sdi to /root/local/backup/performance_schema/replication_conn_164.sdi
2024-02-02T00:47:21.795802+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/replication_conn_164.sdi to /root/local/backup/performance_schema/replication_conn_164.sdi
2024-02-02T00:47:21.796675+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/replication_appl_165.sdi to /root/local/backup/performance_schema/replication_appl_165.sdi
2024-02-02T00:47:21.796927+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/replication_appl_165.sdi to /root/local/backup/performance_schema/replication_appl_165.sdi
2024-02-02T00:47:21.797834+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/replication_appl_166.sdi to /root/local/backup/performance_schema/replication_appl_166.sdi
2024-02-02T00:47:21.798079+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/replication_appl_166.sdi to /root/local/backup/performance_schema/replication_appl_166.sdi
2024-02-02T00:47:21.798835+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/replication_appl_167.sdi to /root/local/backup/performance_schema/replication_appl_167.sdi
2024-02-02T00:47:21.799082+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/replication_appl_167.sdi to /root/local/backup/performance_schema/replication_appl_167.sdi
2024-02-02T00:47:21.800116+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/replication_appl_168.sdi to /root/local/backup/performance_schema/replication_appl_168.sdi
2024-02-02T00:47:21.800422+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/replication_appl_168.sdi to /root/local/backup/performance_schema/replication_appl_168.sdi
2024-02-02T00:47:21.801261+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/replication_grou_169.sdi to /root/local/backup/performance_schema/replication_grou_169.sdi
2024-02-02T00:47:21.801620+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/replication_grou_169.sdi to /root/local/backup/performance_schema/replication_grou_169.sdi
2024-02-02T00:47:21.802519+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/replication_appl_170.sdi to /root/local/backup/performance_schema/replication_appl_170.sdi
2024-02-02T00:47:21.802756+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/replication_appl_170.sdi to /root/local/backup/performance_schema/replication_appl_170.sdi
2024-02-02T00:47:21.803680+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/replication_appl_171.sdi to /root/local/backup/performance_schema/replication_appl_171.sdi
2024-02-02T00:47:21.803958+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/replication_appl_171.sdi to /root/local/backup/performance_schema/replication_appl_171.sdi
2024-02-02T00:47:21.804814+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/replication_asyn_172.sdi to /root/local/backup/performance_schema/replication_asyn_172.sdi
2024-02-02T00:47:21.805026+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/replication_asyn_172.sdi to /root/local/backup/performance_schema/replication_asyn_172.sdi
2024-02-02T00:47:21.805898+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/replication_asyn_173.sdi to /root/local/backup/performance_schema/replication_asyn_173.sdi
2024-02-02T00:47:21.806147+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/replication_asyn_173.sdi to /root/local/backup/performance_schema/replication_asyn_173.sdi
2024-02-02T00:47:21.807134+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/log_status_174.sdi to /root/local/backup/performance_schema/log_status_174.sdi
2024-02-02T00:47:21.807408+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/log_status_174.sdi to /root/local/backup/performance_schema/log_status_174.sdi
2024-02-02T00:47:21.808356+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/prepared_stateme_175.sdi to /root/local/backup/performance_schema/prepared_stateme_175.sdi
2024-02-02T00:47:21.808672+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/prepared_stateme_175.sdi to /root/local/backup/performance_schema/prepared_stateme_175.sdi
2024-02-02T00:47:21.809710+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/user_variables_b_176.sdi to /root/local/backup/performance_schema/user_variables_b_176.sdi
2024-02-02T00:47:21.809980+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/user_variables_b_176.sdi to /root/local/backup/performance_schema/user_variables_b_176.sdi
2024-02-02T00:47:21.810861+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/status_by_accoun_177.sdi to /root/local/backup/performance_schema/status_by_accoun_177.sdi
2024-02-02T00:47:21.811095+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/status_by_accoun_177.sdi to /root/local/backup/performance_schema/status_by_accoun_177.sdi
2024-02-02T00:47:21.812024+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/status_by_host_178.sdi to /root/local/backup/performance_schema/status_by_host_178.sdi
2024-02-02T00:47:21.812252+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/status_by_host_178.sdi to /root/local/backup/performance_schema/status_by_host_178.sdi
2024-02-02T00:47:21.813085+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/status_by_thread_179.sdi to /root/local/backup/performance_schema/status_by_thread_179.sdi
2024-02-02T00:47:21.813293+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/status_by_thread_179.sdi to /root/local/backup/performance_schema/status_by_thread_179.sdi
2024-02-02T00:47:21.814125+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/status_by_user_180.sdi to /root/local/backup/performance_schema/status_by_user_180.sdi
2024-02-02T00:47:21.814332+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/status_by_user_180.sdi to /root/local/backup/performance_schema/status_by_user_180.sdi
2024-02-02T00:47:21.815217+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/global_status_181.sdi to /root/local/backup/performance_schema/global_status_181.sdi
2024-02-02T00:47:21.815424+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/global_status_181.sdi to /root/local/backup/performance_schema/global_status_181.sdi
2024-02-02T00:47:21.816200+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/session_status_182.sdi to /root/local/backup/performance_schema/session_status_182.sdi
2024-02-02T00:47:21.816421+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/session_status_182.sdi to /root/local/backup/performance_schema/session_status_182.sdi
2024-02-02T00:47:21.817396+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/variables_by_thr_183.sdi to /root/local/backup/performance_schema/variables_by_thr_183.sdi
2024-02-02T00:47:21.817606+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/variables_by_thr_183.sdi to /root/local/backup/performance_schema/variables_by_thr_183.sdi
2024-02-02T00:47:21.818458+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/global_variables_184.sdi to /root/local/backup/performance_schema/global_variables_184.sdi
2024-02-02T00:47:21.818676+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/global_variables_184.sdi to /root/local/backup/performance_schema/global_variables_184.sdi
2024-02-02T00:47:21.819468+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/session_variable_185.sdi to /root/local/backup/performance_schema/session_variable_185.sdi
2024-02-02T00:47:21.819665+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/session_variable_185.sdi to /root/local/backup/performance_schema/session_variable_185.sdi
2024-02-02T00:47:21.820536+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/variables_info_186.sdi to /root/local/backup/performance_schema/variables_info_186.sdi
2024-02-02T00:47:21.820774+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/variables_info_186.sdi to /root/local/backup/performance_schema/variables_info_186.sdi
2024-02-02T00:47:21.821563+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/persisted_variab_187.sdi to /root/local/backup/performance_schema/persisted_variab_187.sdi
2024-02-02T00:47:21.821844+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/persisted_variab_187.sdi to /root/local/backup/performance_schema/persisted_variab_187.sdi
2024-02-02T00:47:21.822879+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/user_defined_fun_188.sdi to /root/local/backup/performance_schema/user_defined_fun_188.sdi
2024-02-02T00:47:21.823124+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/user_defined_fun_188.sdi to /root/local/backup/performance_schema/user_defined_fun_188.sdi
2024-02-02T00:47:21.824205+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/binary_log_trans_189.sdi to /root/local/backup/performance_schema/binary_log_trans_189.sdi
2024-02-02T00:47:21.824483+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/binary_log_trans_189.sdi to /root/local/backup/performance_schema/binary_log_trans_189.sdi
2024-02-02T00:47:21.825416+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/tls_channel_stat_190.sdi to /root/local/backup/performance_schema/tls_channel_stat_190.sdi
2024-02-02T00:47:21.825663+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/tls_channel_stat_190.sdi to /root/local/backup/performance_schema/tls_channel_stat_190.sdi
2024-02-02T00:47:21.826466+08:00 3 [Note] [MY-011825] [Xtrabackup] Copying performance_schema/keyring_componen_191.sdi to /root/local/backup/performance_schema/keyring_componen_191.sdi
2024-02-02T00:47:21.826712+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Copying performance_schema/keyring_componen_191.sdi to /root/local/backup/performance_schema/keyring_componen_191.sdi
2024-02-02T00:47:21.827660+08:00 3 [Note] [MY-011825] [Xtrabackup] Writing /root/local/backup/sbtest/db.opt
2024-02-02T00:47:21.827702+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Writing file /root/local/backup/sbtest/db.opt
2024-02-02T00:47:21.867983+08:00 0 [Note] [MY-011825] [Xtrabackup] Finished backing up non-InnoDB tables and files
2024-02-02T00:47:21.868072+08:00 0 [Note] [MY-011825] [Xtrabackup] Executing FLUSH NO_WRITE_TO_BINLOG BINARY LOGS
2024-02-02T00:47:21.877831+08:00 0 [Note] [MY-011825] [Xtrabackup] Selecting LSN and binary log position from p_s.log_status
2024-02-02T00:47:21.898720+08:00 0 [Note] [MY-011825] [Xtrabackup] Copying /root/local/mysql_7032/log/bin.000009 to /root/local/backup/bin.000009 up to position 237
2024-02-02T00:47:21.898945+08:00 0 [Note] [MY-011825] [Xtrabackup] Done: Copying /root/local/mysql_7032/log/bin.000009 to /root/local/backup/bin.000009
2024-02-02T00:47:21.900596+08:00 0 [Note] [MY-011825] [Xtrabackup] Writing /root/local/backup/bin.index
2024-02-02T00:47:21.900776+08:00 0 [Note] [MY-011825] [Xtrabackup] Done: Writing file /root/local/backup/bin.index
2024-02-02T00:47:21.918048+08:00 0 [Note] [MY-011825] [Xtrabackup] Writing /root/local/backup/xtrabackup_binlog_info
2024-02-02T00:47:21.918287+08:00 0 [Note] [MY-011825] [Xtrabackup] Done: Writing file /root/local/backup/xtrabackup_binlog_info
2024-02-02T00:47:21.919620+08:00 0 [Note] [MY-011825] [Xtrabackup] Executing FLUSH NO_WRITE_TO_BINLOG ENGINE LOGS...
2024-02-02T00:47:21.952398+08:00 0 [Note] [MY-011825] [Xtrabackup] The latest check point (for incremental): '30759344'
2024-02-02T00:47:21.952472+08:00 0 [Note] [MY-011825] [Xtrabackup] Stopping log copying thread at LSN 30759344
2024-02-02T00:47:21.952837+08:00 1 [Note] [MY-011825] [Xtrabackup] Starting to parse redo log at lsn = 30758942
2024-02-02T00:47:21.957030+08:00 0 [Note] [MY-011825] [Xtrabackup] Executing UNLOCK INSTANCE
2024-02-02T00:47:21.957531+08:00 0 [Note] [MY-011825] [Xtrabackup] All tables unlocked
2024-02-02T00:47:21.957670+08:00 0 [Note] [MY-011825] [Xtrabackup] Copying ib_buffer_pool to /root/local/backup/ib_buffer_pool
2024-02-02T00:47:21.958002+08:00 0 [Note] [MY-011825] [Xtrabackup] Done: Copying ib_buffer_pool to /root/local/backup/ib_buffer_pool
2024-02-02T00:47:21.963842+08:00 0 [Note] [MY-011825] [Xtrabackup] Backup created in directory '/root/local/backup/'
2024-02-02T00:47:21.963904+08:00 0 [Note] [MY-011825] [Xtrabackup] MySQL binlog position: filename 'bin.000009', position '237', GTID of the last change '662a2811-c03c-11ee-affc-00163e62ca8a:1-28,6d1f356f-bdeb-11ee-b3aa-00163e62ca8a:1-11'
2024-02-02T00:47:21.964045+08:00 0 [Note] [MY-011825] [Xtrabackup] Writing /root/local/backup/backup-my.cnf
2024-02-02T00:47:21.964129+08:00 0 [Note] [MY-011825] [Xtrabackup] Done: Writing file /root/local/backup/backup-my.cnf
2024-02-02T00:47:21.965990+08:00 0 [Note] [MY-011825] [Xtrabackup] Writing /root/local/backup/xtrabackup_info
2024-02-02T00:47:21.966076+08:00 0 [Note] [MY-011825] [Xtrabackup] Done: Writing file /root/local/backup/xtrabackup_info
2024-02-02T00:47:22.968156+08:00 0 [Note] [MY-011825] [Xtrabackup] Transaction log of lsn (30759344) to (30763732) was copied.
2024-02-02T00:47:23.354945+08:00 0 [Note] [MY-011825] [Xtrabackup] completed OK!

```



### compress=lz4
> 所有非目录压缩为.lz4格式
```sql
[root@dev backup]# /root/local/xtrabackup_8032/bin/xtrabackup --user=root --password=root --host=172.17.137.12 --port=7032 --backup --target-dir=/root/local/backup/ --compress=lz4
2024-02-02T01:01:45.825853+08:00 0 [Note] [MY-011825] [Xtrabackup] recognized client arguments: --user=root --password=* --host=172.17.137.12 --port=7032 --backup=1 --target-dir=/root/local/backup/ --compress=lz4
/root/local/xtrabackup_8032/bin/xtrabackup version 8.0.32-26 based on MySQL server 8.0.32 Linux (x86_64) (revision id: )
240202 01:01:45  version_check Connecting to MySQL server with DSN 'dbi:mysql:;mysql_read_default_group=xtrabackup;host=172.17.137.12;port=7032' as 'root'  (using password: YES).
Failed to connect to MySQL server as DBD::mysql module is not installed at - line 1548.
2024-02-02T01:01:45.961533+08:00 0 [Note] [MY-011825] [Xtrabackup] Connecting to MySQL server host: 172.17.137.12, user: root, password: set, port: 7032, socket: not set
2024-02-02T01:01:45.988256+08:00 0 [Note] [MY-011825] [Xtrabackup] Using server version 8.0.32-debug
2024-02-02T01:01:46.008176+08:00 0 [Note] [MY-011825] [Xtrabackup] Executing LOCK INSTANCE FOR BACKUP ...
2024-02-02T01:01:46.214244+08:00 0 [Note] [MY-011825] [Xtrabackup] uses posix_fadvise().
2024-02-02T01:01:46.214378+08:00 0 [Note] [MY-011825] [Xtrabackup] cd to /root/local/mysql_7032/data/
2024-02-02T01:01:46.214408+08:00 0 [Note] [MY-011825] [Xtrabackup] open files limit requested 0, set to 1024000
2024-02-02T01:01:46.217865+08:00 0 [Note] [MY-011825] [Xtrabackup] using the following InnoDB configuration:
2024-02-02T01:01:46.217922+08:00 0 [Note] [MY-011825] [Xtrabackup] innodb_data_home_dir = .
2024-02-02T01:01:46.217933+08:00 0 [Note] [MY-011825] [Xtrabackup] innodb_data_file_path = ibdata1:12M:autoextend
2024-02-02T01:01:46.218072+08:00 0 [Note] [MY-011825] [Xtrabackup] innodb_log_group_home_dir = ./
2024-02-02T01:01:46.218090+08:00 0 [Note] [MY-011825] [Xtrabackup] innodb_log_files_in_group = 2
2024-02-02T01:01:46.218115+08:00 0 [Note] [MY-011825] [Xtrabackup] innodb_log_file_size = 1073741824
2024-02-02T01:01:46.234197+08:00 0 [Note] [MY-011825] [Xtrabackup] inititialize_service_handles suceeded
2024-02-02T01:01:46.625507+08:00 0 [Note] [MY-011825] [Xtrabackup] Connecting to MySQL server host: 172.17.137.12, user: root, password: set, port: 7032, socket: not set
2024-02-02T01:01:46.645013+08:00 0 [Note] [MY-011825] [Xtrabackup] Redo Log Archiving is not set up.
2024-02-02T01:01:46.856468+08:00 0 [Note] [MY-011825] [Xtrabackup] Starting to parse redo log at lsn = 30763548
2024-02-02T01:01:46.865742+08:00 0 [Note] [MY-012564] [InnoDB] Recovery parsing buffer extended to 4194304.
2024-02-02T01:01:46.874905+08:00 0 [Note] [MY-012564] [InnoDB] Recovery parsing buffer extended to 8388608.
2024-02-02T01:01:51.276601+08:00 1 [Note] [MY-011825] [Xtrabackup] >> log scanned up to (55809839)
2024-02-02T01:01:51.484602+08:00 0 [Note] [MY-011825] [Xtrabackup] Generating a list of tablespaces
2024-02-02T01:01:51.484828+08:00 0 [Note] [MY-012204] [InnoDB] Scanning './'
2024-02-02T01:01:51.487919+08:00 0 [Note] [MY-012208] [InnoDB] Completed space ID check of 2 files.
2024-02-02T01:01:51.489074+08:00 0 [Warning] [MY-012091] [InnoDB] Allocated tablespace ID 1 for sys/sys_config, old maximum was 0
2024-02-02T01:01:51.491649+08:00 0 [Note] [MY-013252] [InnoDB] Using undo tablespace './undo_001'.
2024-02-02T01:01:51.492181+08:00 0 [Note] [MY-013252] [InnoDB] Using undo tablespace './undo_002'.
2024-02-02T01:01:51.493491+08:00 0 [Note] [MY-012910] [InnoDB] Opened 2 existing undo tablespaces.
2024-02-02T01:01:51.494471+08:00 2 [Note] [MY-011825] [Xtrabackup] Compressing ./ibdata1 to /root/local/backup/ibdata1.lz4
2024-02-02T01:01:51.656405+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Compressing ./ibdata1 to /root/local/backup/ibdata1.lz4
2024-02-02T01:01:51.659010+08:00 2 [Note] [MY-011825] [Xtrabackup] Compressing ./sys/sys_config.ibd to /root/local/backup/sys/sys_config.ibd.lz4
2024-02-02T01:01:51.659790+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Compressing ./sys/sys_config.ibd to /root/local/backup/sys/sys_config.ibd.lz4
2024-02-02T01:01:51.661401+08:00 2 [Note] [MY-011825] [Xtrabackup] Compressing ./apple/info.ibd to /root/local/backup/apple/info.ibd.lz4
2024-02-02T01:01:51.662239+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Compressing ./apple/info.ibd to /root/local/backup/apple/info.ibd.lz4
2024-02-02T01:01:51.663805+08:00 2 [Note] [MY-011825] [Xtrabackup] Compressing ./apple/sbtest2.ibd to /root/local/backup/apple/sbtest2.ibd.lz4
2024-02-02T01:01:51.804350+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Compressing ./apple/sbtest2.ibd to /root/local/backup/apple/sbtest2.ibd.lz4
2024-02-02T01:01:51.810078+08:00 2 [Note] [MY-011825] [Xtrabackup] Compressing ./apple/sbtest3.ibd to /root/local/backup/apple/sbtest3.ibd.lz4
2024-02-02T01:01:51.926854+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Compressing ./apple/sbtest3.ibd to /root/local/backup/apple/sbtest3.ibd.lz4
2024-02-02T01:01:51.933279+08:00 2 [Note] [MY-011825] [Xtrabackup] Compressing ./apple/sbtest1.ibd to /root/local/backup/apple/sbtest1.ibd.lz4
2024-02-02T01:01:52.118055+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Compressing ./apple/sbtest1.ibd to /root/local/backup/apple/sbtest1.ibd.lz4
2024-02-02T01:01:52.125243+08:00 2 [Note] [MY-011825] [Xtrabackup] Compressing ./apple/sbtest4.ibd to /root/local/backup/apple/sbtest4.ibd.lz4
2024-02-02T01:01:52.244581+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Compressing ./apple/sbtest4.ibd to /root/local/backup/apple/sbtest4.ibd.lz4
2024-02-02T01:01:52.250898+08:00 2 [Note] [MY-011825] [Xtrabackup] Compressing ./mysql.ibd to /root/local/backup/mysql.ibd.lz4
2024-02-02T01:01:52.292225+08:00 1 [Note] [MY-011825] [Xtrabackup] >> log scanned up to (55929611)
2024-02-02T01:01:52.543543+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Compressing ./mysql.ibd to /root/local/backup/mysql.ibd.lz4
2024-02-02T01:01:52.549599+08:00 2 [Note] [MY-011825] [Xtrabackup] Compressing ./undo_002 to /root/local/backup/undo_002.lz4
2024-02-02T01:01:52.681239+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Compressing ./undo_002 to /root/local/backup/undo_002.lz4
2024-02-02T01:01:52.684142+08:00 2 [Note] [MY-011825] [Xtrabackup] Compressing ./undo_001 to /root/local/backup/undo_001.lz4
2024-02-02T01:01:52.951303+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Compressing ./undo_001 to /root/local/backup/undo_001.lz4
2024-02-02T01:01:53.302069+08:00 1 [Note] [MY-011825] [Xtrabackup] >> log scanned up to (55995519)
2024-02-02T01:01:53.494599+08:00 0 [Note] [MY-011825] [Xtrabackup] Starting to backup non-InnoDB tables and files
2024-02-02T01:01:53.496354+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing mysql/general_log_213.sdi to /root/local/backup/mysql/general_log_213.sdi.lz4
2024-02-02T01:01:53.497338+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing mysql/general_log_213.sdi to /root/local/backup/mysql/general_log_213.sdi.lz4
2024-02-02T01:01:53.500093+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing mysql/general_log.CSM to /root/local/backup/mysql/general_log.CSM.lz4
2024-02-02T01:01:53.500564+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing mysql/general_log.CSM to /root/local/backup/mysql/general_log.CSM.lz4
2024-02-02T01:01:53.502432+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing mysql/general_log.CSV to /root/local/backup/mysql/general_log.CSV.lz4
2024-02-02T01:01:53.502527+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing mysql/general_log.CSV to /root/local/backup/mysql/general_log.CSV.lz4
2024-02-02T01:01:53.504485+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing mysql/slow_log_214.sdi to /root/local/backup/mysql/slow_log_214.sdi.lz4
2024-02-02T01:01:53.505039+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing mysql/slow_log_214.sdi to /root/local/backup/mysql/slow_log_214.sdi.lz4
2024-02-02T01:01:53.506592+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing mysql/slow_log.CSM to /root/local/backup/mysql/slow_log.CSM.lz4
2024-02-02T01:01:53.507041+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing mysql/slow_log.CSM to /root/local/backup/mysql/slow_log.CSM.lz4
2024-02-02T01:01:53.508722+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing mysql/slow_log.CSV to /root/local/backup/mysql/slow_log.CSV.lz4
2024-02-02T01:01:53.508782+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing mysql/slow_log.CSV to /root/local/backup/mysql/slow_log.CSV.lz4
2024-02-02T01:01:53.510703+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_statement_123.sdi to /root/local/backup/performance_schema/events_statement_123.sdi.lz4
2024-02-02T01:01:53.511339+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_statement_123.sdi to /root/local/backup/performance_schema/events_statement_123.sdi.lz4
2024-02-02T01:01:53.513018+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_statement_124.sdi to /root/local/backup/performance_schema/events_statement_124.sdi.lz4
2024-02-02T01:01:53.513560+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_statement_124.sdi to /root/local/backup/performance_schema/events_statement_124.sdi.lz4
2024-02-02T01:01:53.515124+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/cond_instances_82.sdi to /root/local/backup/performance_schema/cond_instances_82.sdi.lz4
2024-02-02T01:01:53.515529+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/cond_instances_82.sdi to /root/local/backup/performance_schema/cond_instances_82.sdi.lz4
2024-02-02T01:01:53.517144+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/error_log_83.sdi to /root/local/backup/performance_schema/error_log_83.sdi.lz4
2024-02-02T01:01:53.517732+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/error_log_83.sdi to /root/local/backup/performance_schema/error_log_83.sdi.lz4
2024-02-02T01:01:53.519171+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_waits_cur_84.sdi to /root/local/backup/performance_schema/events_waits_cur_84.sdi.lz4
2024-02-02T01:01:53.519803+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_waits_cur_84.sdi to /root/local/backup/performance_schema/events_waits_cur_84.sdi.lz4
2024-02-02T01:01:53.521436+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_waits_his_85.sdi to /root/local/backup/performance_schema/events_waits_his_85.sdi.lz4
2024-02-02T01:01:53.521982+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_waits_his_85.sdi to /root/local/backup/performance_schema/events_waits_his_85.sdi.lz4
2024-02-02T01:01:53.523365+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_waits_his_86.sdi to /root/local/backup/performance_schema/events_waits_his_86.sdi.lz4
2024-02-02T01:01:53.523936+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_waits_his_86.sdi to /root/local/backup/performance_schema/events_waits_his_86.sdi.lz4
2024-02-02T01:01:53.525463+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_waits_sum_87.sdi to /root/local/backup/performance_schema/events_waits_sum_87.sdi.lz4
2024-02-02T01:01:53.525844+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_waits_sum_87.sdi to /root/local/backup/performance_schema/events_waits_sum_87.sdi.lz4
2024-02-02T01:01:53.527786+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_waits_sum_88.sdi to /root/local/backup/performance_schema/events_waits_sum_88.sdi.lz4
2024-02-02T01:01:53.528238+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_waits_sum_88.sdi to /root/local/backup/performance_schema/events_waits_sum_88.sdi.lz4
2024-02-02T01:01:53.529856+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_waits_sum_89.sdi to /root/local/backup/performance_schema/events_waits_sum_89.sdi.lz4
2024-02-02T01:01:53.530243+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_waits_sum_89.sdi to /root/local/backup/performance_schema/events_waits_sum_89.sdi.lz4
2024-02-02T01:01:53.531805+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_waits_sum_90.sdi to /root/local/backup/performance_schema/events_waits_sum_90.sdi.lz4
2024-02-02T01:01:53.532183+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_waits_sum_90.sdi to /root/local/backup/performance_schema/events_waits_sum_90.sdi.lz4
2024-02-02T01:01:53.533790+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_waits_sum_91.sdi to /root/local/backup/performance_schema/events_waits_sum_91.sdi.lz4
2024-02-02T01:01:53.534176+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_waits_sum_91.sdi to /root/local/backup/performance_schema/events_waits_sum_91.sdi.lz4
2024-02-02T01:01:53.535855+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_waits_sum_92.sdi to /root/local/backup/performance_schema/events_waits_sum_92.sdi.lz4
2024-02-02T01:01:53.536385+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_waits_sum_92.sdi to /root/local/backup/performance_schema/events_waits_sum_92.sdi.lz4
2024-02-02T01:01:53.538009+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/file_instances_93.sdi to /root/local/backup/performance_schema/file_instances_93.sdi.lz4
2024-02-02T01:01:53.538382+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/file_instances_93.sdi to /root/local/backup/performance_schema/file_instances_93.sdi.lz4
2024-02-02T01:01:53.539839+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/file_summary_by__94.sdi to /root/local/backup/performance_schema/file_summary_by__94.sdi.lz4
2024-02-02T01:01:53.540253+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/file_summary_by__94.sdi to /root/local/backup/performance_schema/file_summary_by__94.sdi.lz4
2024-02-02T01:01:53.541823+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/file_summary_by__95.sdi to /root/local/backup/performance_schema/file_summary_by__95.sdi.lz4
2024-02-02T01:01:53.542344+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/file_summary_by__95.sdi to /root/local/backup/performance_schema/file_summary_by__95.sdi.lz4
2024-02-02T01:01:53.543832+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/host_cache_96.sdi to /root/local/backup/performance_schema/host_cache_96.sdi.lz4
2024-02-02T01:01:53.544266+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/host_cache_96.sdi to /root/local/backup/performance_schema/host_cache_96.sdi.lz4
2024-02-02T01:01:53.545839+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/mutex_instances_97.sdi to /root/local/backup/performance_schema/mutex_instances_97.sdi.lz4
2024-02-02T01:01:53.546299+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/mutex_instances_97.sdi to /root/local/backup/performance_schema/mutex_instances_97.sdi.lz4
2024-02-02T01:01:53.547677+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/objects_summary__98.sdi to /root/local/backup/performance_schema/objects_summary__98.sdi.lz4
2024-02-02T01:01:53.548133+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/objects_summary__98.sdi to /root/local/backup/performance_schema/objects_summary__98.sdi.lz4
2024-02-02T01:01:53.549515+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/performance_time_99.sdi to /root/local/backup/performance_schema/performance_time_99.sdi.lz4
2024-02-02T01:01:53.549948+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/performance_time_99.sdi to /root/local/backup/performance_schema/performance_time_99.sdi.lz4
2024-02-02T01:01:53.559252+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/processlist_100.sdi to /root/local/backup/performance_schema/processlist_100.sdi.lz4
2024-02-02T01:01:53.559726+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/processlist_100.sdi to /root/local/backup/performance_schema/processlist_100.sdi.lz4
2024-02-02T01:01:53.561016+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/rwlock_instances_101.sdi to /root/local/backup/performance_schema/rwlock_instances_101.sdi.lz4
2024-02-02T01:01:53.561482+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/rwlock_instances_101.sdi to /root/local/backup/performance_schema/rwlock_instances_101.sdi.lz4
2024-02-02T01:01:53.562757+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/setup_actors_102.sdi to /root/local/backup/performance_schema/setup_actors_102.sdi.lz4
2024-02-02T01:01:53.563107+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/setup_actors_102.sdi to /root/local/backup/performance_schema/setup_actors_102.sdi.lz4
2024-02-02T01:01:53.564427+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/setup_consumers_103.sdi to /root/local/backup/performance_schema/setup_consumers_103.sdi.lz4
2024-02-02T01:01:53.564776+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/setup_consumers_103.sdi to /root/local/backup/performance_schema/setup_consumers_103.sdi.lz4
2024-02-02T01:01:53.566009+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/setup_instrument_104.sdi to /root/local/backup/performance_schema/setup_instrument_104.sdi.lz4
2024-02-02T01:01:53.566389+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/setup_instrument_104.sdi to /root/local/backup/performance_schema/setup_instrument_104.sdi.lz4
2024-02-02T01:01:53.567769+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/setup_objects_105.sdi to /root/local/backup/performance_schema/setup_objects_105.sdi.lz4
2024-02-02T01:01:53.568168+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/setup_objects_105.sdi to /root/local/backup/performance_schema/setup_objects_105.sdi.lz4
2024-02-02T01:01:53.569485+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/setup_threads_106.sdi to /root/local/backup/performance_schema/setup_threads_106.sdi.lz4
2024-02-02T01:01:53.569836+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/setup_threads_106.sdi to /root/local/backup/performance_schema/setup_threads_106.sdi.lz4
2024-02-02T01:01:53.570984+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/table_io_waits_s_107.sdi to /root/local/backup/performance_schema/table_io_waits_s_107.sdi.lz4
2024-02-02T01:01:53.571515+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/table_io_waits_s_107.sdi to /root/local/backup/performance_schema/table_io_waits_s_107.sdi.lz4
2024-02-02T01:01:53.572698+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/table_io_waits_s_108.sdi to /root/local/backup/performance_schema/table_io_waits_s_108.sdi.lz4
2024-02-02T01:01:53.573141+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/table_io_waits_s_108.sdi to /root/local/backup/performance_schema/table_io_waits_s_108.sdi.lz4
2024-02-02T01:01:53.574305+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/table_lock_waits_109.sdi to /root/local/backup/performance_schema/table_lock_waits_109.sdi.lz4
2024-02-02T01:01:53.574889+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/table_lock_waits_109.sdi to /root/local/backup/performance_schema/table_lock_waits_109.sdi.lz4
2024-02-02T01:01:53.575984+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/threads_110.sdi to /root/local/backup/performance_schema/threads_110.sdi.lz4
2024-02-02T01:01:53.576467+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/threads_110.sdi to /root/local/backup/performance_schema/threads_110.sdi.lz4
2024-02-02T01:01:53.577594+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_stages_cu_111.sdi to /root/local/backup/performance_schema/events_stages_cu_111.sdi.lz4
2024-02-02T01:01:53.577963+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_stages_cu_111.sdi to /root/local/backup/performance_schema/events_stages_cu_111.sdi.lz4
2024-02-02T01:01:53.579031+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_stages_hi_112.sdi to /root/local/backup/performance_schema/events_stages_hi_112.sdi.lz4
2024-02-02T01:01:53.579483+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_stages_hi_112.sdi to /root/local/backup/performance_schema/events_stages_hi_112.sdi.lz4
2024-02-02T01:01:53.580558+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_stages_hi_113.sdi to /root/local/backup/performance_schema/events_stages_hi_113.sdi.lz4
2024-02-02T01:01:53.580891+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_stages_hi_113.sdi to /root/local/backup/performance_schema/events_stages_hi_113.sdi.lz4
2024-02-02T01:01:53.582075+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_stages_su_114.sdi to /root/local/backup/performance_schema/events_stages_su_114.sdi.lz4
2024-02-02T01:01:53.582471+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_stages_su_114.sdi to /root/local/backup/performance_schema/events_stages_su_114.sdi.lz4
2024-02-02T01:01:53.583515+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_stages_su_115.sdi to /root/local/backup/performance_schema/events_stages_su_115.sdi.lz4
2024-02-02T01:01:53.583854+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_stages_su_115.sdi to /root/local/backup/performance_schema/events_stages_su_115.sdi.lz4
2024-02-02T01:01:53.584898+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_stages_su_116.sdi to /root/local/backup/performance_schema/events_stages_su_116.sdi.lz4
2024-02-02T01:01:53.585220+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_stages_su_116.sdi to /root/local/backup/performance_schema/events_stages_su_116.sdi.lz4
2024-02-02T01:01:53.586306+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_stages_su_117.sdi to /root/local/backup/performance_schema/events_stages_su_117.sdi.lz4
2024-02-02T01:01:53.586693+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_stages_su_117.sdi to /root/local/backup/performance_schema/events_stages_su_117.sdi.lz4
2024-02-02T01:01:53.587643+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_stages_su_118.sdi to /root/local/backup/performance_schema/events_stages_su_118.sdi.lz4
2024-02-02T01:01:53.587927+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_stages_su_118.sdi to /root/local/backup/performance_schema/events_stages_su_118.sdi.lz4
2024-02-02T01:01:53.589280+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_statement_119.sdi to /root/local/backup/performance_schema/events_statement_119.sdi.lz4
2024-02-02T01:01:53.589814+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_statement_119.sdi to /root/local/backup/performance_schema/events_statement_119.sdi.lz4
2024-02-02T01:01:53.590923+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_statement_120.sdi to /root/local/backup/performance_schema/events_statement_120.sdi.lz4
2024-02-02T01:01:53.591363+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_statement_120.sdi to /root/local/backup/performance_schema/events_statement_120.sdi.lz4
2024-02-02T01:01:53.592342+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_statement_121.sdi to /root/local/backup/performance_schema/events_statement_121.sdi.lz4
2024-02-02T01:01:53.592807+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_statement_121.sdi to /root/local/backup/performance_schema/events_statement_121.sdi.lz4
2024-02-02T01:01:53.593898+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_statement_122.sdi to /root/local/backup/performance_schema/events_statement_122.sdi.lz4
2024-02-02T01:01:53.594213+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_statement_122.sdi to /root/local/backup/performance_schema/events_statement_122.sdi.lz4
2024-02-02T01:01:53.595103+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_statement_125.sdi to /root/local/backup/performance_schema/events_statement_125.sdi.lz4
2024-02-02T01:01:53.595525+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_statement_125.sdi to /root/local/backup/performance_schema/events_statement_125.sdi.lz4
2024-02-02T01:01:53.596473+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_statement_126.sdi to /root/local/backup/performance_schema/events_statement_126.sdi.lz4
2024-02-02T01:01:53.596797+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_statement_126.sdi to /root/local/backup/performance_schema/events_statement_126.sdi.lz4
2024-02-02T01:01:53.597697+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_statement_127.sdi to /root/local/backup/performance_schema/events_statement_127.sdi.lz4
2024-02-02T01:01:53.598033+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_statement_127.sdi to /root/local/backup/performance_schema/events_statement_127.sdi.lz4
2024-02-02T01:01:53.599188+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_statement_128.sdi to /root/local/backup/performance_schema/events_statement_128.sdi.lz4
2024-02-02T01:01:53.599622+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_statement_128.sdi to /root/local/backup/performance_schema/events_statement_128.sdi.lz4
2024-02-02T01:01:53.600634+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_statement_129.sdi to /root/local/backup/performance_schema/events_statement_129.sdi.lz4
2024-02-02T01:01:53.600907+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_statement_129.sdi to /root/local/backup/performance_schema/events_statement_129.sdi.lz4
2024-02-02T01:01:53.601902+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_statement_130.sdi to /root/local/backup/performance_schema/events_statement_130.sdi.lz4
2024-02-02T01:01:53.602177+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_statement_130.sdi to /root/local/backup/performance_schema/events_statement_130.sdi.lz4
2024-02-02T01:01:53.603174+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_transacti_131.sdi to /root/local/backup/performance_schema/events_transacti_131.sdi.lz4
2024-02-02T01:01:53.603525+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_transacti_131.sdi to /root/local/backup/performance_schema/events_transacti_131.sdi.lz4
2024-02-02T01:01:53.604668+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_transacti_132.sdi to /root/local/backup/performance_schema/events_transacti_132.sdi.lz4
2024-02-02T01:01:53.604999+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_transacti_132.sdi to /root/local/backup/performance_schema/events_transacti_132.sdi.lz4
2024-02-02T01:01:53.606046+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_transacti_133.sdi to /root/local/backup/performance_schema/events_transacti_133.sdi.lz4
2024-02-02T01:01:53.606375+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_transacti_133.sdi to /root/local/backup/performance_schema/events_transacti_133.sdi.lz4
2024-02-02T01:01:53.607433+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_transacti_134.sdi to /root/local/backup/performance_schema/events_transacti_134.sdi.lz4
2024-02-02T01:01:53.607823+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_transacti_134.sdi to /root/local/backup/performance_schema/events_transacti_134.sdi.lz4
2024-02-02T01:01:53.608964+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_transacti_135.sdi to /root/local/backup/performance_schema/events_transacti_135.sdi.lz4
2024-02-02T01:01:53.609277+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_transacti_135.sdi to /root/local/backup/performance_schema/events_transacti_135.sdi.lz4
2024-02-02T01:01:53.610308+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_transacti_136.sdi to /root/local/backup/performance_schema/events_transacti_136.sdi.lz4
2024-02-02T01:01:53.610604+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_transacti_136.sdi to /root/local/backup/performance_schema/events_transacti_136.sdi.lz4
2024-02-02T01:01:53.611524+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_transacti_137.sdi to /root/local/backup/performance_schema/events_transacti_137.sdi.lz4
2024-02-02T01:01:53.611792+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_transacti_137.sdi to /root/local/backup/performance_schema/events_transacti_137.sdi.lz4
2024-02-02T01:01:53.612806+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_transacti_138.sdi to /root/local/backup/performance_schema/events_transacti_138.sdi.lz4
2024-02-02T01:01:53.613064+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_transacti_138.sdi to /root/local/backup/performance_schema/events_transacti_138.sdi.lz4
2024-02-02T01:01:53.614600+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_errors_su_139.sdi to /root/local/backup/performance_schema/events_errors_su_139.sdi.lz4
2024-02-02T01:01:53.614979+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_errors_su_139.sdi to /root/local/backup/performance_schema/events_errors_su_139.sdi.lz4
2024-02-02T01:01:53.615938+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_errors_su_140.sdi to /root/local/backup/performance_schema/events_errors_su_140.sdi.lz4
2024-02-02T01:01:53.616185+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_errors_su_140.sdi to /root/local/backup/performance_schema/events_errors_su_140.sdi.lz4
2024-02-02T01:01:53.617281+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_errors_su_141.sdi to /root/local/backup/performance_schema/events_errors_su_141.sdi.lz4
2024-02-02T01:01:53.617569+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_errors_su_141.sdi to /root/local/backup/performance_schema/events_errors_su_141.sdi.lz4
2024-02-02T01:01:53.618537+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_errors_su_142.sdi to /root/local/backup/performance_schema/events_errors_su_142.sdi.lz4
2024-02-02T01:01:53.618859+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_errors_su_142.sdi to /root/local/backup/performance_schema/events_errors_su_142.sdi.lz4
2024-02-02T01:01:53.619636+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_errors_su_143.sdi to /root/local/backup/performance_schema/events_errors_su_143.sdi.lz4
2024-02-02T01:01:53.619885+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_errors_su_143.sdi to /root/local/backup/performance_schema/events_errors_su_143.sdi.lz4
2024-02-02T01:01:53.620922+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/users_144.sdi to /root/local/backup/performance_schema/users_144.sdi.lz4
2024-02-02T01:01:53.621148+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/users_144.sdi to /root/local/backup/performance_schema/users_144.sdi.lz4
2024-02-02T01:01:53.622071+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/accounts_145.sdi to /root/local/backup/performance_schema/accounts_145.sdi.lz4
2024-02-02T01:01:53.622336+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/accounts_145.sdi to /root/local/backup/performance_schema/accounts_145.sdi.lz4
2024-02-02T01:01:53.623101+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/hosts_146.sdi to /root/local/backup/performance_schema/hosts_146.sdi.lz4
2024-02-02T01:01:53.623336+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/hosts_146.sdi to /root/local/backup/performance_schema/hosts_146.sdi.lz4
2024-02-02T01:01:53.624405+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/socket_instances_147.sdi to /root/local/backup/performance_schema/socket_instances_147.sdi.lz4
2024-02-02T01:01:53.624673+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/socket_instances_147.sdi to /root/local/backup/performance_schema/socket_instances_147.sdi.lz4
2024-02-02T01:01:53.625535+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/socket_summary_b_148.sdi to /root/local/backup/performance_schema/socket_summary_b_148.sdi.lz4
2024-02-02T01:01:53.625823+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/socket_summary_b_148.sdi to /root/local/backup/performance_schema/socket_summary_b_148.sdi.lz4
2024-02-02T01:01:53.626710+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/socket_summary_b_149.sdi to /root/local/backup/performance_schema/socket_summary_b_149.sdi.lz4
2024-02-02T01:01:53.626976+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/socket_summary_b_149.sdi to /root/local/backup/performance_schema/socket_summary_b_149.sdi.lz4
2024-02-02T01:01:53.628029+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/session_connect__150.sdi to /root/local/backup/performance_schema/session_connect__150.sdi.lz4
2024-02-02T01:01:53.628258+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/session_connect__150.sdi to /root/local/backup/performance_schema/session_connect__150.sdi.lz4
2024-02-02T01:01:53.629127+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/session_account__151.sdi to /root/local/backup/performance_schema/session_account__151.sdi.lz4
2024-02-02T01:01:53.629466+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/session_account__151.sdi to /root/local/backup/performance_schema/session_account__151.sdi.lz4
2024-02-02T01:01:53.630367+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/keyring_keys_152.sdi to /root/local/backup/performance_schema/keyring_keys_152.sdi.lz4
2024-02-02T01:01:53.630712+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/keyring_keys_152.sdi to /root/local/backup/performance_schema/keyring_keys_152.sdi.lz4
2024-02-02T01:01:53.631913+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/memory_summary_g_153.sdi to /root/local/backup/performance_schema/memory_summary_g_153.sdi.lz4
2024-02-02T01:01:53.632254+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/memory_summary_g_153.sdi to /root/local/backup/performance_schema/memory_summary_g_153.sdi.lz4
2024-02-02T01:01:53.633218+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/memory_summary_b_154.sdi to /root/local/backup/performance_schema/memory_summary_b_154.sdi.lz4
2024-02-02T01:01:53.633624+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/memory_summary_b_154.sdi to /root/local/backup/performance_schema/memory_summary_b_154.sdi.lz4
2024-02-02T01:01:53.634620+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/memory_summary_b_155.sdi to /root/local/backup/performance_schema/memory_summary_b_155.sdi.lz4
2024-02-02T01:01:53.634911+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/memory_summary_b_155.sdi to /root/local/backup/performance_schema/memory_summary_b_155.sdi.lz4
2024-02-02T01:01:53.636061+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/memory_summary_b_156.sdi to /root/local/backup/performance_schema/memory_summary_b_156.sdi.lz4
2024-02-02T01:01:53.636353+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/memory_summary_b_156.sdi to /root/local/backup/performance_schema/memory_summary_b_156.sdi.lz4
2024-02-02T01:01:53.637250+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/memory_summary_b_157.sdi to /root/local/backup/performance_schema/memory_summary_b_157.sdi.lz4
2024-02-02T01:01:53.637561+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/memory_summary_b_157.sdi to /root/local/backup/performance_schema/memory_summary_b_157.sdi.lz4
2024-02-02T01:01:53.638590+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/table_handles_158.sdi to /root/local/backup/performance_schema/table_handles_158.sdi.lz4
2024-02-02T01:01:53.638858+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/table_handles_158.sdi to /root/local/backup/performance_schema/table_handles_158.sdi.lz4
2024-02-02T01:01:53.639871+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/metadata_locks_159.sdi to /root/local/backup/performance_schema/metadata_locks_159.sdi.lz4
2024-02-02T01:01:53.640159+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/metadata_locks_159.sdi to /root/local/backup/performance_schema/metadata_locks_159.sdi.lz4
2024-02-02T01:01:53.641137+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/data_locks_160.sdi to /root/local/backup/performance_schema/data_locks_160.sdi.lz4
2024-02-02T01:01:53.641461+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/data_locks_160.sdi to /root/local/backup/performance_schema/data_locks_160.sdi.lz4
2024-02-02T01:01:53.642424+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/data_lock_waits_161.sdi to /root/local/backup/performance_schema/data_lock_waits_161.sdi.lz4
2024-02-02T01:01:53.642738+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/data_lock_waits_161.sdi to /root/local/backup/performance_schema/data_lock_waits_161.sdi.lz4
2024-02-02T01:01:53.643681+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/replication_conn_162.sdi to /root/local/backup/performance_schema/replication_conn_162.sdi.lz4
2024-02-02T01:01:53.644113+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/replication_conn_162.sdi to /root/local/backup/performance_schema/replication_conn_162.sdi.lz4
2024-02-02T01:01:53.645037+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/replication_grou_163.sdi to /root/local/backup/performance_schema/replication_grou_163.sdi.lz4
2024-02-02T01:01:53.645323+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/replication_grou_163.sdi to /root/local/backup/performance_schema/replication_grou_163.sdi.lz4
2024-02-02T01:01:53.646487+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/replication_conn_164.sdi to /root/local/backup/performance_schema/replication_conn_164.sdi.lz4
2024-02-02T01:01:53.646844+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/replication_conn_164.sdi to /root/local/backup/performance_schema/replication_conn_164.sdi.lz4
2024-02-02T01:01:53.647802+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/replication_appl_165.sdi to /root/local/backup/performance_schema/replication_appl_165.sdi.lz4
2024-02-02T01:01:53.648118+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/replication_appl_165.sdi to /root/local/backup/performance_schema/replication_appl_165.sdi.lz4
2024-02-02T01:01:53.649270+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/replication_appl_166.sdi to /root/local/backup/performance_schema/replication_appl_166.sdi.lz4
2024-02-02T01:01:53.649555+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/replication_appl_166.sdi to /root/local/backup/performance_schema/replication_appl_166.sdi.lz4
2024-02-02T01:01:53.650533+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/replication_appl_167.sdi to /root/local/backup/performance_schema/replication_appl_167.sdi.lz4
2024-02-02T01:01:53.650836+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/replication_appl_167.sdi to /root/local/backup/performance_schema/replication_appl_167.sdi.lz4
2024-02-02T01:01:53.651606+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/replication_appl_168.sdi to /root/local/backup/performance_schema/replication_appl_168.sdi.lz4
2024-02-02T01:01:53.652021+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/replication_appl_168.sdi to /root/local/backup/performance_schema/replication_appl_168.sdi.lz4
2024-02-02T01:01:53.652972+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/replication_grou_169.sdi to /root/local/backup/performance_schema/replication_grou_169.sdi.lz4
2024-02-02T01:01:53.653245+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/replication_grou_169.sdi to /root/local/backup/performance_schema/replication_grou_169.sdi.lz4
2024-02-02T01:01:53.654382+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/replication_appl_170.sdi to /root/local/backup/performance_schema/replication_appl_170.sdi.lz4
2024-02-02T01:01:53.654688+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/replication_appl_170.sdi to /root/local/backup/performance_schema/replication_appl_170.sdi.lz4
2024-02-02T01:01:53.655422+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/replication_appl_171.sdi to /root/local/backup/performance_schema/replication_appl_171.sdi.lz4
2024-02-02T01:01:53.655661+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/replication_appl_171.sdi to /root/local/backup/performance_schema/replication_appl_171.sdi.lz4
2024-02-02T01:01:53.656643+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/replication_asyn_172.sdi to /root/local/backup/performance_schema/replication_asyn_172.sdi.lz4
2024-02-02T01:01:53.656892+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/replication_asyn_172.sdi to /root/local/backup/performance_schema/replication_asyn_172.sdi.lz4
2024-02-02T01:01:53.657768+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/replication_asyn_173.sdi to /root/local/backup/performance_schema/replication_asyn_173.sdi.lz4
2024-02-02T01:01:53.658096+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/replication_asyn_173.sdi to /root/local/backup/performance_schema/replication_asyn_173.sdi.lz4
2024-02-02T01:01:53.658893+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/log_status_174.sdi to /root/local/backup/performance_schema/log_status_174.sdi.lz4
2024-02-02T01:01:53.659112+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/log_status_174.sdi to /root/local/backup/performance_schema/log_status_174.sdi.lz4
2024-02-02T01:01:53.660002+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/prepared_stateme_175.sdi to /root/local/backup/performance_schema/prepared_stateme_175.sdi.lz4
2024-02-02T01:01:53.660371+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/prepared_stateme_175.sdi to /root/local/backup/performance_schema/prepared_stateme_175.sdi.lz4
2024-02-02T01:01:53.661281+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/user_variables_b_176.sdi to /root/local/backup/performance_schema/user_variables_b_176.sdi.lz4
2024-02-02T01:01:53.661562+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/user_variables_b_176.sdi to /root/local/backup/performance_schema/user_variables_b_176.sdi.lz4
2024-02-02T01:01:53.662604+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/status_by_accoun_177.sdi to /root/local/backup/performance_schema/status_by_accoun_177.sdi.lz4
2024-02-02T01:01:53.662847+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/status_by_accoun_177.sdi to /root/local/backup/performance_schema/status_by_accoun_177.sdi.lz4
2024-02-02T01:01:53.663861+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/status_by_host_178.sdi to /root/local/backup/performance_schema/status_by_host_178.sdi.lz4
2024-02-02T01:01:53.664084+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/status_by_host_178.sdi to /root/local/backup/performance_schema/status_by_host_178.sdi.lz4
2024-02-02T01:01:53.665061+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/status_by_thread_179.sdi to /root/local/backup/performance_schema/status_by_thread_179.sdi.lz4
2024-02-02T01:01:53.665325+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/status_by_thread_179.sdi to /root/local/backup/performance_schema/status_by_thread_179.sdi.lz4
2024-02-02T01:01:53.666083+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/status_by_user_180.sdi to /root/local/backup/performance_schema/status_by_user_180.sdi.lz4
2024-02-02T01:01:53.666316+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/status_by_user_180.sdi to /root/local/backup/performance_schema/status_by_user_180.sdi.lz4
2024-02-02T01:01:53.667217+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/global_status_181.sdi to /root/local/backup/performance_schema/global_status_181.sdi.lz4
2024-02-02T01:01:53.667435+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/global_status_181.sdi to /root/local/backup/performance_schema/global_status_181.sdi.lz4
2024-02-02T01:01:53.668248+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/session_status_182.sdi to /root/local/backup/performance_schema/session_status_182.sdi.lz4
2024-02-02T01:01:53.668514+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/session_status_182.sdi to /root/local/backup/performance_schema/session_status_182.sdi.lz4
2024-02-02T01:01:53.669293+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/variables_by_thr_183.sdi to /root/local/backup/performance_schema/variables_by_thr_183.sdi.lz4
2024-02-02T01:01:53.669558+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/variables_by_thr_183.sdi to /root/local/backup/performance_schema/variables_by_thr_183.sdi.lz4
2024-02-02T01:01:53.670392+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/global_variables_184.sdi to /root/local/backup/performance_schema/global_variables_184.sdi.lz4
2024-02-02T01:01:53.670620+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/global_variables_184.sdi to /root/local/backup/performance_schema/global_variables_184.sdi.lz4
2024-02-02T01:01:53.671322+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/session_variable_185.sdi to /root/local/backup/performance_schema/session_variable_185.sdi.lz4
2024-02-02T01:01:53.671650+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/session_variable_185.sdi to /root/local/backup/performance_schema/session_variable_185.sdi.lz4
2024-02-02T01:01:53.672404+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/variables_info_186.sdi to /root/local/backup/performance_schema/variables_info_186.sdi.lz4
2024-02-02T01:01:53.672686+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/variables_info_186.sdi to /root/local/backup/performance_schema/variables_info_186.sdi.lz4
2024-02-02T01:01:53.673584+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/persisted_variab_187.sdi to /root/local/backup/performance_schema/persisted_variab_187.sdi.lz4
2024-02-02T01:01:53.673812+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/persisted_variab_187.sdi to /root/local/backup/performance_schema/persisted_variab_187.sdi.lz4
2024-02-02T01:01:53.674550+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/user_defined_fun_188.sdi to /root/local/backup/performance_schema/user_defined_fun_188.sdi.lz4
2024-02-02T01:01:53.674774+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/user_defined_fun_188.sdi to /root/local/backup/performance_schema/user_defined_fun_188.sdi.lz4
2024-02-02T01:01:53.675578+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/binary_log_trans_189.sdi to /root/local/backup/performance_schema/binary_log_trans_189.sdi.lz4
2024-02-02T01:01:53.675835+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/binary_log_trans_189.sdi to /root/local/backup/performance_schema/binary_log_trans_189.sdi.lz4
2024-02-02T01:01:53.676765+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/tls_channel_stat_190.sdi to /root/local/backup/performance_schema/tls_channel_stat_190.sdi.lz4
2024-02-02T01:01:53.677012+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/tls_channel_stat_190.sdi to /root/local/backup/performance_schema/tls_channel_stat_190.sdi.lz4
2024-02-02T01:01:53.677766+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/keyring_componen_191.sdi to /root/local/backup/performance_schema/keyring_componen_191.sdi.lz4
2024-02-02T01:01:53.677984+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/keyring_componen_191.sdi to /root/local/backup/performance_schema/keyring_componen_191.sdi.lz4
2024-02-02T01:01:53.679085+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing /root/local/backup/sbtest/db.opt.lz4
2024-02-02T01:01:53.679114+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing file /root/local/backup/sbtest/db.opt.lz4
2024-02-02T01:01:53.697143+08:00 0 [Note] [MY-011825] [Xtrabackup] Finished backing up non-InnoDB tables and files
2024-02-02T01:01:53.697199+08:00 0 [Note] [MY-011825] [Xtrabackup] Executing FLUSH NO_WRITE_TO_BINLOG BINARY LOGS
2024-02-02T01:01:53.708024+08:00 0 [Note] [MY-011825] [Xtrabackup] Selecting LSN and binary log position from p_s.log_status
2024-02-02T01:01:53.725210+08:00 0 [Note] [MY-011825] [Xtrabackup] Compressing /root/local/mysql_7032/log/bin.000010 to /root/local/backup/bin.000010.lz4 up to position 237
2024-02-02T01:01:53.725399+08:00 0 [Note] [MY-011825] [Xtrabackup] Done: Compressing /root/local/mysql_7032/log/bin.000010 to /root/local/backup/bin.000010.lz4
2024-02-02T01:01:53.726643+08:00 0 [Note] [MY-011825] [Xtrabackup] Compressing /root/local/backup/bin.index.lz4
2024-02-02T01:01:53.726766+08:00 0 [Note] [MY-011825] [Xtrabackup] Done: Compressing file /root/local/backup/bin.index.lz4
2024-02-02T01:01:53.741741+08:00 0 [Note] [MY-011825] [Xtrabackup] Compressing /root/local/backup/xtrabackup_binlog_info.lz4
2024-02-02T01:01:53.741841+08:00 0 [Note] [MY-011825] [Xtrabackup] Done: Compressing file /root/local/backup/xtrabackup_binlog_info.lz4
2024-02-02T01:01:53.742916+08:00 0 [Note] [MY-011825] [Xtrabackup] Executing FLUSH NO_WRITE_TO_BINLOG ENGINE LOGS...
2024-02-02T01:01:53.750615+08:00 0 [Note] [MY-011825] [Xtrabackup] The latest check point (for incremental): '30775185'
2024-02-02T01:01:53.750653+08:00 0 [Note] [MY-011825] [Xtrabackup] Stopping log copying thread at LSN 56004956
2024-02-02T01:01:53.752005+08:00 1 [Note] [MY-011825] [Xtrabackup] >> log scanned up to (56006238)
2024-02-02T01:01:54.819752+08:00 0 [Note] [MY-011825] [Xtrabackup] Executing UNLOCK INSTANCE
2024-02-02T01:01:54.820308+08:00 0 [Note] [MY-011825] [Xtrabackup] All tables unlocked
2024-02-02T01:01:54.820494+08:00 0 [Note] [MY-011825] [Xtrabackup] Compressing ib_buffer_pool to /root/local/backup/ib_buffer_pool.lz4
2024-02-02T01:01:54.820801+08:00 0 [Note] [MY-011825] [Xtrabackup] Done: Compressing ib_buffer_pool to /root/local/backup/ib_buffer_pool.lz4
2024-02-02T01:01:54.822078+08:00 0 [Note] [MY-011825] [Xtrabackup] Backup created in directory '/root/local/backup/'
2024-02-02T01:01:54.822111+08:00 0 [Note] [MY-011825] [Xtrabackup] MySQL binlog position: filename 'bin.000010', position '237', GTID of the last change '662a2811-c03c-11ee-affc-00163e62ca8a:1-5075,6d1f356f-bdeb-11ee-b3aa-00163e62ca8a:1-11'
2024-02-02T01:01:54.822264+08:00 0 [Note] [MY-011825] [Xtrabackup] Compressing /root/local/backup/backup-my.cnf.lz4
2024-02-02T01:01:54.822386+08:00 0 [Note] [MY-011825] [Xtrabackup] Done: Compressing file /root/local/backup/backup-my.cnf.lz4
2024-02-02T01:01:54.824329+08:00 0 [Note] [MY-011825] [Xtrabackup] Compressing /root/local/backup/xtrabackup_info.lz4
2024-02-02T01:01:54.824492+08:00 0 [Note] [MY-011825] [Xtrabackup] Done: Compressing file /root/local/backup/xtrabackup_info.lz4
2024-02-02T01:01:55.826602+08:00 0 [Note] [MY-011825] [Xtrabackup] Transaction log of lsn (30763770) to (56296740) was copied.
2024-02-02T01:01:56.278193+08:00 0 [Note] [MY-011825] [Xtrabackup] completed OK!

```

### compress=zstd
> 所有非目录压缩为.zstd格式
```sql
[root@dev backup]# /root/local/xtrabackup_8032/bin/xtrabackup --user=root --password=root --host=172.17.137.12 --port=7032 --backup --target-dir=/root/local/backup/ --compress=zstd
2024-02-02T01:05:46.169799+08:00 0 [Note] [MY-011825] [Xtrabackup] recognized client arguments: --user=root --password=* --host=172.17.137.12 --port=7032 --backup=1 --target-dir=/root/local/backup/ --compress=zstd
/root/local/xtrabackup_8032/bin/xtrabackup version 8.0.32-26 based on MySQL server 8.0.32 Linux (x86_64) (revision id: )
240202 01:05:46  version_check Connecting to MySQL server with DSN 'dbi:mysql:;mysql_read_default_group=xtrabackup;host=172.17.137.12;port=7032' as 'root'  (using password: YES).
Failed to connect to MySQL server as DBD::mysql module is not installed at - line 1548.
2024-02-02T01:05:46.284016+08:00 0 [Note] [MY-011825] [Xtrabackup] Connecting to MySQL server host: 172.17.137.12, user: root, password: set, port: 7032, socket: not set
2024-02-02T01:05:46.334706+08:00 0 [Note] [MY-011825] [Xtrabackup] Using server version 8.0.32-debug
2024-02-02T01:05:46.361846+08:00 0 [Note] [MY-011825] [Xtrabackup] Executing LOCK INSTANCE FOR BACKUP ...
2024-02-02T01:05:46.536271+08:00 0 [Note] [MY-011825] [Xtrabackup] uses posix_fadvise().
2024-02-02T01:05:46.536405+08:00 0 [Note] [MY-011825] [Xtrabackup] cd to /root/local/mysql_7032/data/
2024-02-02T01:05:46.536449+08:00 0 [Note] [MY-011825] [Xtrabackup] open files limit requested 0, set to 1024000
2024-02-02T01:05:46.538768+08:00 0 [Note] [MY-011825] [Xtrabackup] using the following InnoDB configuration:
2024-02-02T01:05:46.538825+08:00 0 [Note] [MY-011825] [Xtrabackup] innodb_data_home_dir = .
2024-02-02T01:05:46.538851+08:00 0 [Note] [MY-011825] [Xtrabackup] innodb_data_file_path = ibdata1:12M:autoextend
2024-02-02T01:05:46.539030+08:00 0 [Note] [MY-011825] [Xtrabackup] innodb_log_group_home_dir = ./
2024-02-02T01:05:46.539058+08:00 0 [Note] [MY-011825] [Xtrabackup] innodb_log_files_in_group = 2
2024-02-02T01:05:46.539092+08:00 0 [Note] [MY-011825] [Xtrabackup] innodb_log_file_size = 1073741824
2024-02-02T01:05:46.547552+08:00 0 [Note] [MY-011825] [Xtrabackup] inititialize_service_handles suceeded
2024-02-02T01:05:46.972401+08:00 0 [Note] [MY-011825] [Xtrabackup] Connecting to MySQL server host: 172.17.137.12, user: root, password: set, port: 7032, socket: not set
2024-02-02T01:05:46.997353+08:00 0 [Note] [MY-011825] [Xtrabackup] Redo Log Archiving is not set up.
2024-02-02T01:05:47.151062+08:00 0 [Note] [MY-011825] [Xtrabackup] Starting to parse redo log at lsn = 50202252
2024-02-02T01:05:47.160455+08:00 0 [Note] [MY-012564] [InnoDB] Recovery parsing buffer extended to 4194304.
2024-02-02T01:05:47.169650+08:00 0 [Note] [MY-012564] [InnoDB] Recovery parsing buffer extended to 8388608.
2024-02-02T01:05:53.799395+08:00 1 [Note] [MY-011825] [Xtrabackup] >> log scanned up to (82144119)
2024-02-02T01:05:53.991450+08:00 0 [Note] [MY-011825] [Xtrabackup] Generating a list of tablespaces
2024-02-02T01:05:53.991982+08:00 0 [Note] [MY-012204] [InnoDB] Scanning './'
2024-02-02T01:05:53.998838+08:00 0 [Note] [MY-012208] [InnoDB] Completed space ID check of 2 files.
2024-02-02T01:05:54.000713+08:00 0 [Warning] [MY-012091] [InnoDB] Allocated tablespace ID 1 for sys/sys_config, old maximum was 0
2024-02-02T01:05:54.005011+08:00 0 [Note] [MY-013252] [InnoDB] Using undo tablespace './undo_001'.
2024-02-02T01:05:54.005922+08:00 0 [Note] [MY-013252] [InnoDB] Using undo tablespace './undo_002'.
2024-02-02T01:05:54.008217+08:00 0 [Note] [MY-012910] [InnoDB] Opened 2 existing undo tablespaces.
2024-02-02T01:05:54.009676+08:00 2 [Note] [MY-011825] [Xtrabackup] Compressing ./ibdata1 to /root/local/backup/ibdata1.zst
2024-02-02T01:05:54.165373+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Compressing ./ibdata1 to /root/local/backup/ibdata1.zst
2024-02-02T01:05:54.168394+08:00 2 [Note] [MY-011825] [Xtrabackup] Compressing ./sys/sys_config.ibd to /root/local/backup/sys/sys_config.ibd.zst
2024-02-02T01:05:54.169828+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Compressing ./sys/sys_config.ibd to /root/local/backup/sys/sys_config.ibd.zst
2024-02-02T01:05:54.172599+08:00 2 [Note] [MY-011825] [Xtrabackup] Compressing ./apple/info.ibd to /root/local/backup/apple/info.ibd.zst
2024-02-02T01:05:54.173962+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Compressing ./apple/info.ibd to /root/local/backup/apple/info.ibd.zst
2024-02-02T01:05:54.175127+08:00 2 [Note] [MY-011825] [Xtrabackup] Compressing ./apple/sbtest2.ibd to /root/local/backup/apple/sbtest2.ibd.zst
2024-02-02T01:05:54.378707+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Compressing ./apple/sbtest2.ibd to /root/local/backup/apple/sbtest2.ibd.zst
2024-02-02T01:05:54.391178+08:00 2 [Note] [MY-011825] [Xtrabackup] Compressing ./apple/sbtest3.ibd to /root/local/backup/apple/sbtest3.ibd.zst
2024-02-02T01:05:54.611754+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Compressing ./apple/sbtest3.ibd to /root/local/backup/apple/sbtest3.ibd.zst
2024-02-02T01:05:54.617295+08:00 2 [Note] [MY-011825] [Xtrabackup] Compressing ./apple/sbtest1.ibd to /root/local/backup/apple/sbtest1.ibd.zst
2024-02-02T01:05:54.807436+08:00 1 [Note] [MY-011825] [Xtrabackup] >> log scanned up to (82205591)
2024-02-02T01:05:54.809966+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Compressing ./apple/sbtest1.ibd to /root/local/backup/apple/sbtest1.ibd.zst
2024-02-02T01:05:54.822754+08:00 2 [Note] [MY-011825] [Xtrabackup] Compressing ./apple/sbtest4.ibd to /root/local/backup/apple/sbtest4.ibd.zst
2024-02-02T01:05:55.002345+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Compressing ./apple/sbtest4.ibd to /root/local/backup/apple/sbtest4.ibd.zst
2024-02-02T01:05:55.007216+08:00 2 [Note] [MY-011825] [Xtrabackup] Compressing ./mysql.ibd to /root/local/backup/mysql.ibd.zst
2024-02-02T01:05:55.447271+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Compressing ./mysql.ibd to /root/local/backup/mysql.ibd.zst
2024-02-02T01:05:55.454695+08:00 2 [Note] [MY-011825] [Xtrabackup] Compressing ./undo_002 to /root/local/backup/undo_002.zst
2024-02-02T01:05:55.847257+08:00 1 [Note] [MY-011825] [Xtrabackup] >> log scanned up to (82367163)
2024-02-02T01:05:55.910328+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Compressing ./undo_002 to /root/local/backup/undo_002.zst
2024-02-02T01:05:55.916138+08:00 2 [Note] [MY-011825] [Xtrabackup] Compressing ./undo_001 to /root/local/backup/undo_001.zst
2024-02-02T01:05:56.141717+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Compressing ./undo_001 to /root/local/backup/undo_001.zst
2024-02-02T01:05:56.890648+08:00 1 [Note] [MY-011825] [Xtrabackup] >> log scanned up to (82541486)
2024-02-02T01:05:57.009342+08:00 0 [Note] [MY-011825] [Xtrabackup] Starting to backup non-InnoDB tables and files
2024-02-02T01:05:57.011296+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing mysql/general_log_213.sdi to /root/local/backup/mysql/general_log_213.sdi.zst
2024-02-02T01:05:57.011869+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing mysql/general_log_213.sdi to /root/local/backup/mysql/general_log_213.sdi.zst
2024-02-02T01:05:57.013900+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing mysql/general_log.CSM to /root/local/backup/mysql/general_log.CSM.zst
2024-02-02T01:05:57.014075+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing mysql/general_log.CSM to /root/local/backup/mysql/general_log.CSM.zst
2024-02-02T01:05:57.014991+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing mysql/general_log.CSV to /root/local/backup/mysql/general_log.CSV.zst
2024-02-02T01:05:57.015027+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing mysql/general_log.CSV to /root/local/backup/mysql/general_log.CSV.zst
2024-02-02T01:05:57.015849+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing mysql/slow_log_214.sdi to /root/local/backup/mysql/slow_log_214.sdi.zst
2024-02-02T01:05:57.016261+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing mysql/slow_log_214.sdi to /root/local/backup/mysql/slow_log_214.sdi.zst
2024-02-02T01:05:57.017068+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing mysql/slow_log.CSM to /root/local/backup/mysql/slow_log.CSM.zst
2024-02-02T01:05:57.017232+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing mysql/slow_log.CSM to /root/local/backup/mysql/slow_log.CSM.zst
2024-02-02T01:05:57.017918+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing mysql/slow_log.CSV to /root/local/backup/mysql/slow_log.CSV.zst
2024-02-02T01:05:57.017950+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing mysql/slow_log.CSV to /root/local/backup/mysql/slow_log.CSV.zst
2024-02-02T01:05:57.019044+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_statement_123.sdi to /root/local/backup/performance_schema/events_statement_123.sdi.zst
2024-02-02T01:05:57.019604+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_statement_123.sdi to /root/local/backup/performance_schema/events_statement_123.sdi.zst
2024-02-02T01:05:57.020741+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_statement_124.sdi to /root/local/backup/performance_schema/events_statement_124.sdi.zst
2024-02-02T01:05:57.021116+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_statement_124.sdi to /root/local/backup/performance_schema/events_statement_124.sdi.zst
2024-02-02T01:05:57.022059+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/cond_instances_82.sdi to /root/local/backup/performance_schema/cond_instances_82.sdi.zst
2024-02-02T01:05:57.022358+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/cond_instances_82.sdi to /root/local/backup/performance_schema/cond_instances_82.sdi.zst
2024-02-02T01:05:57.023128+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/error_log_83.sdi to /root/local/backup/performance_schema/error_log_83.sdi.zst
2024-02-02T01:05:57.023450+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/error_log_83.sdi to /root/local/backup/performance_schema/error_log_83.sdi.zst
2024-02-02T01:05:57.024245+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_waits_cur_84.sdi to /root/local/backup/performance_schema/events_waits_cur_84.sdi.zst
2024-02-02T01:05:57.024602+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_waits_cur_84.sdi to /root/local/backup/performance_schema/events_waits_cur_84.sdi.zst
2024-02-02T01:05:57.025345+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_waits_his_85.sdi to /root/local/backup/performance_schema/events_waits_his_85.sdi.zst
2024-02-02T01:05:57.025682+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_waits_his_85.sdi to /root/local/backup/performance_schema/events_waits_his_85.sdi.zst
2024-02-02T01:05:57.026454+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_waits_his_86.sdi to /root/local/backup/performance_schema/events_waits_his_86.sdi.zst
2024-02-02T01:05:57.026799+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_waits_his_86.sdi to /root/local/backup/performance_schema/events_waits_his_86.sdi.zst
2024-02-02T01:05:57.027676+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_waits_sum_87.sdi to /root/local/backup/performance_schema/events_waits_sum_87.sdi.zst
2024-02-02T01:05:57.028000+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_waits_sum_87.sdi to /root/local/backup/performance_schema/events_waits_sum_87.sdi.zst
2024-02-02T01:05:57.028817+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_waits_sum_88.sdi to /root/local/backup/performance_schema/events_waits_sum_88.sdi.zst
2024-02-02T01:05:57.029117+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_waits_sum_88.sdi to /root/local/backup/performance_schema/events_waits_sum_88.sdi.zst
2024-02-02T01:05:57.030164+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_waits_sum_89.sdi to /root/local/backup/performance_schema/events_waits_sum_89.sdi.zst
2024-02-02T01:05:57.030654+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_waits_sum_89.sdi to /root/local/backup/performance_schema/events_waits_sum_89.sdi.zst
2024-02-02T01:05:57.031723+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_waits_sum_90.sdi to /root/local/backup/performance_schema/events_waits_sum_90.sdi.zst
2024-02-02T01:05:57.032152+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_waits_sum_90.sdi to /root/local/backup/performance_schema/events_waits_sum_90.sdi.zst
2024-02-02T01:05:57.033123+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_waits_sum_91.sdi to /root/local/backup/performance_schema/events_waits_sum_91.sdi.zst
2024-02-02T01:05:57.033709+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_waits_sum_91.sdi to /root/local/backup/performance_schema/events_waits_sum_91.sdi.zst
2024-02-02T01:05:57.034703+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_waits_sum_92.sdi to /root/local/backup/performance_schema/events_waits_sum_92.sdi.zst
2024-02-02T01:05:57.035113+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_waits_sum_92.sdi to /root/local/backup/performance_schema/events_waits_sum_92.sdi.zst
2024-02-02T01:05:57.036171+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/file_instances_93.sdi to /root/local/backup/performance_schema/file_instances_93.sdi.zst
2024-02-02T01:05:57.036589+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/file_instances_93.sdi to /root/local/backup/performance_schema/file_instances_93.sdi.zst
2024-02-02T01:05:57.037517+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/file_summary_by__94.sdi to /root/local/backup/performance_schema/file_summary_by__94.sdi.zst
2024-02-02T01:05:57.037970+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/file_summary_by__94.sdi to /root/local/backup/performance_schema/file_summary_by__94.sdi.zst
2024-02-02T01:05:57.038768+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/file_summary_by__95.sdi to /root/local/backup/performance_schema/file_summary_by__95.sdi.zst
2024-02-02T01:05:57.039198+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/file_summary_by__95.sdi to /root/local/backup/performance_schema/file_summary_by__95.sdi.zst
2024-02-02T01:05:57.040124+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/host_cache_96.sdi to /root/local/backup/performance_schema/host_cache_96.sdi.zst
2024-02-02T01:05:57.040638+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/host_cache_96.sdi to /root/local/backup/performance_schema/host_cache_96.sdi.zst
2024-02-02T01:05:57.041575+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/mutex_instances_97.sdi to /root/local/backup/performance_schema/mutex_instances_97.sdi.zst
2024-02-02T01:05:57.042037+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/mutex_instances_97.sdi to /root/local/backup/performance_schema/mutex_instances_97.sdi.zst
2024-02-02T01:05:57.042889+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/objects_summary__98.sdi to /root/local/backup/performance_schema/objects_summary__98.sdi.zst
2024-02-02T01:05:57.043275+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/objects_summary__98.sdi to /root/local/backup/performance_schema/objects_summary__98.sdi.zst
2024-02-02T01:05:57.044053+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/performance_time_99.sdi to /root/local/backup/performance_schema/performance_time_99.sdi.zst
2024-02-02T01:05:57.044495+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/performance_time_99.sdi to /root/local/backup/performance_schema/performance_time_99.sdi.zst
2024-02-02T01:05:57.045458+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/processlist_100.sdi to /root/local/backup/performance_schema/processlist_100.sdi.zst
2024-02-02T01:05:57.045959+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/processlist_100.sdi to /root/local/backup/performance_schema/processlist_100.sdi.zst
2024-02-02T01:05:57.046781+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/rwlock_instances_101.sdi to /root/local/backup/performance_schema/rwlock_instances_101.sdi.zst
2024-02-02T01:05:57.047124+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/rwlock_instances_101.sdi to /root/local/backup/performance_schema/rwlock_instances_101.sdi.zst
2024-02-02T01:05:57.047910+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/setup_actors_102.sdi to /root/local/backup/performance_schema/setup_actors_102.sdi.zst
2024-02-02T01:05:57.048276+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/setup_actors_102.sdi to /root/local/backup/performance_schema/setup_actors_102.sdi.zst
2024-02-02T01:05:57.049206+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/setup_consumers_103.sdi to /root/local/backup/performance_schema/setup_consumers_103.sdi.zst
2024-02-02T01:05:57.049549+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/setup_consumers_103.sdi to /root/local/backup/performance_schema/setup_consumers_103.sdi.zst
2024-02-02T01:05:57.050547+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/setup_instrument_104.sdi to /root/local/backup/performance_schema/setup_instrument_104.sdi.zst
2024-02-02T01:05:57.050957+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/setup_instrument_104.sdi to /root/local/backup/performance_schema/setup_instrument_104.sdi.zst
2024-02-02T01:05:57.051800+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/setup_objects_105.sdi to /root/local/backup/performance_schema/setup_objects_105.sdi.zst
2024-02-02T01:05:57.052169+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/setup_objects_105.sdi to /root/local/backup/performance_schema/setup_objects_105.sdi.zst
2024-02-02T01:05:57.053085+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/setup_threads_106.sdi to /root/local/backup/performance_schema/setup_threads_106.sdi.zst
2024-02-02T01:05:57.053493+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/setup_threads_106.sdi to /root/local/backup/performance_schema/setup_threads_106.sdi.zst
2024-02-02T01:05:57.054297+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/table_io_waits_s_107.sdi to /root/local/backup/performance_schema/table_io_waits_s_107.sdi.zst
2024-02-02T01:05:57.054862+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/table_io_waits_s_107.sdi to /root/local/backup/performance_schema/table_io_waits_s_107.sdi.zst
2024-02-02T01:05:57.055781+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/table_io_waits_s_108.sdi to /root/local/backup/performance_schema/table_io_waits_s_108.sdi.zst
2024-02-02T01:05:57.056228+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/table_io_waits_s_108.sdi to /root/local/backup/performance_schema/table_io_waits_s_108.sdi.zst
2024-02-02T01:05:57.057269+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/table_lock_waits_109.sdi to /root/local/backup/performance_schema/table_lock_waits_109.sdi.zst
2024-02-02T01:05:57.057890+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/table_lock_waits_109.sdi to /root/local/backup/performance_schema/table_lock_waits_109.sdi.zst
2024-02-02T01:05:57.058692+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/threads_110.sdi to /root/local/backup/performance_schema/threads_110.sdi.zst
2024-02-02T01:05:57.059250+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/threads_110.sdi to /root/local/backup/performance_schema/threads_110.sdi.zst
2024-02-02T01:05:57.060107+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_stages_cu_111.sdi to /root/local/backup/performance_schema/events_stages_cu_111.sdi.zst
2024-02-02T01:05:57.060674+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_stages_cu_111.sdi to /root/local/backup/performance_schema/events_stages_cu_111.sdi.zst
2024-02-02T01:05:57.061599+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_stages_hi_112.sdi to /root/local/backup/performance_schema/events_stages_hi_112.sdi.zst
2024-02-02T01:05:57.062042+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_stages_hi_112.sdi to /root/local/backup/performance_schema/events_stages_hi_112.sdi.zst
2024-02-02T01:05:57.062891+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_stages_hi_113.sdi to /root/local/backup/performance_schema/events_stages_hi_113.sdi.zst
2024-02-02T01:05:57.063331+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_stages_hi_113.sdi to /root/local/backup/performance_schema/events_stages_hi_113.sdi.zst
2024-02-02T01:05:57.064248+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_stages_su_114.sdi to /root/local/backup/performance_schema/events_stages_su_114.sdi.zst
2024-02-02T01:05:57.064692+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_stages_su_114.sdi to /root/local/backup/performance_schema/events_stages_su_114.sdi.zst
2024-02-02T01:05:57.065845+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_stages_su_115.sdi to /root/local/backup/performance_schema/events_stages_su_115.sdi.zst
2024-02-02T01:05:57.066267+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_stages_su_115.sdi to /root/local/backup/performance_schema/events_stages_su_115.sdi.zst
2024-02-02T01:05:57.067102+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_stages_su_116.sdi to /root/local/backup/performance_schema/events_stages_su_116.sdi.zst
2024-02-02T01:05:57.067460+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_stages_su_116.sdi to /root/local/backup/performance_schema/events_stages_su_116.sdi.zst
2024-02-02T01:05:57.068283+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_stages_su_117.sdi to /root/local/backup/performance_schema/events_stages_su_117.sdi.zst
2024-02-02T01:05:57.068710+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_stages_su_117.sdi to /root/local/backup/performance_schema/events_stages_su_117.sdi.zst
2024-02-02T01:05:57.069842+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_stages_su_118.sdi to /root/local/backup/performance_schema/events_stages_su_118.sdi.zst
2024-02-02T01:05:57.070186+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_stages_su_118.sdi to /root/local/backup/performance_schema/events_stages_su_118.sdi.zst
2024-02-02T01:05:57.071100+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_statement_119.sdi to /root/local/backup/performance_schema/events_statement_119.sdi.zst
2024-02-02T01:05:57.071657+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_statement_119.sdi to /root/local/backup/performance_schema/events_statement_119.sdi.zst
2024-02-02T01:05:57.072494+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_statement_120.sdi to /root/local/backup/performance_schema/events_statement_120.sdi.zst
2024-02-02T01:05:57.073037+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_statement_120.sdi to /root/local/backup/performance_schema/events_statement_120.sdi.zst
2024-02-02T01:05:57.073927+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_statement_121.sdi to /root/local/backup/performance_schema/events_statement_121.sdi.zst
2024-02-02T01:05:57.074486+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_statement_121.sdi to /root/local/backup/performance_schema/events_statement_121.sdi.zst
2024-02-02T01:05:57.075328+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_statement_122.sdi to /root/local/backup/performance_schema/events_statement_122.sdi.zst
2024-02-02T01:05:57.075758+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_statement_122.sdi to /root/local/backup/performance_schema/events_statement_122.sdi.zst
2024-02-02T01:05:57.076490+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_statement_125.sdi to /root/local/backup/performance_schema/events_statement_125.sdi.zst
2024-02-02T01:05:57.076964+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_statement_125.sdi to /root/local/backup/performance_schema/events_statement_125.sdi.zst
2024-02-02T01:05:57.077675+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_statement_126.sdi to /root/local/backup/performance_schema/events_statement_126.sdi.zst
2024-02-02T01:05:57.078078+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_statement_126.sdi to /root/local/backup/performance_schema/events_statement_126.sdi.zst
2024-02-02T01:05:57.078805+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_statement_127.sdi to /root/local/backup/performance_schema/events_statement_127.sdi.zst
2024-02-02T01:05:57.079241+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_statement_127.sdi to /root/local/backup/performance_schema/events_statement_127.sdi.zst
2024-02-02T01:05:57.080125+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_statement_128.sdi to /root/local/backup/performance_schema/events_statement_128.sdi.zst
2024-02-02T01:05:57.080667+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_statement_128.sdi to /root/local/backup/performance_schema/events_statement_128.sdi.zst
2024-02-02T01:05:57.081520+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_statement_129.sdi to /root/local/backup/performance_schema/events_statement_129.sdi.zst
2024-02-02T01:05:57.081893+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_statement_129.sdi to /root/local/backup/performance_schema/events_statement_129.sdi.zst
2024-02-02T01:05:57.082674+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_statement_130.sdi to /root/local/backup/performance_schema/events_statement_130.sdi.zst
2024-02-02T01:05:57.083042+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_statement_130.sdi to /root/local/backup/performance_schema/events_statement_130.sdi.zst
2024-02-02T01:05:57.083911+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_transacti_131.sdi to /root/local/backup/performance_schema/events_transacti_131.sdi.zst
2024-02-02T01:05:57.084394+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_transacti_131.sdi to /root/local/backup/performance_schema/events_transacti_131.sdi.zst
2024-02-02T01:05:57.085205+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_transacti_132.sdi to /root/local/backup/performance_schema/events_transacti_132.sdi.zst
2024-02-02T01:05:57.085691+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_transacti_132.sdi to /root/local/backup/performance_schema/events_transacti_132.sdi.zst
2024-02-02T01:05:57.086516+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_transacti_133.sdi to /root/local/backup/performance_schema/events_transacti_133.sdi.zst
2024-02-02T01:05:57.086964+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_transacti_133.sdi to /root/local/backup/performance_schema/events_transacti_133.sdi.zst
2024-02-02T01:05:57.087726+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_transacti_134.sdi to /root/local/backup/performance_schema/events_transacti_134.sdi.zst
2024-02-02T01:05:57.088215+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_transacti_134.sdi to /root/local/backup/performance_schema/events_transacti_134.sdi.zst
2024-02-02T01:05:57.089058+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_transacti_135.sdi to /root/local/backup/performance_schema/events_transacti_135.sdi.zst
2024-02-02T01:05:57.089436+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_transacti_135.sdi to /root/local/backup/performance_schema/events_transacti_135.sdi.zst
2024-02-02T01:05:57.090221+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_transacti_136.sdi to /root/local/backup/performance_schema/events_transacti_136.sdi.zst
2024-02-02T01:05:57.090636+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_transacti_136.sdi to /root/local/backup/performance_schema/events_transacti_136.sdi.zst
2024-02-02T01:05:57.091388+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_transacti_137.sdi to /root/local/backup/performance_schema/events_transacti_137.sdi.zst
2024-02-02T01:05:57.091790+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_transacti_137.sdi to /root/local/backup/performance_schema/events_transacti_137.sdi.zst
2024-02-02T01:05:57.092678+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_transacti_138.sdi to /root/local/backup/performance_schema/events_transacti_138.sdi.zst
2024-02-02T01:05:57.093065+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_transacti_138.sdi to /root/local/backup/performance_schema/events_transacti_138.sdi.zst
2024-02-02T01:05:57.093986+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_errors_su_139.sdi to /root/local/backup/performance_schema/events_errors_su_139.sdi.zst
2024-02-02T01:05:57.094336+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_errors_su_139.sdi to /root/local/backup/performance_schema/events_errors_su_139.sdi.zst
2024-02-02T01:05:57.095093+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_errors_su_140.sdi to /root/local/backup/performance_schema/events_errors_su_140.sdi.zst
2024-02-02T01:05:57.095434+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_errors_su_140.sdi to /root/local/backup/performance_schema/events_errors_su_140.sdi.zst
2024-02-02T01:05:57.096227+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_errors_su_141.sdi to /root/local/backup/performance_schema/events_errors_su_141.sdi.zst
2024-02-02T01:05:57.096617+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_errors_su_141.sdi to /root/local/backup/performance_schema/events_errors_su_141.sdi.zst
2024-02-02T01:05:57.097511+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_errors_su_142.sdi to /root/local/backup/performance_schema/events_errors_su_142.sdi.zst
2024-02-02T01:05:57.097935+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_errors_su_142.sdi to /root/local/backup/performance_schema/events_errors_su_142.sdi.zst
2024-02-02T01:05:57.098682+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/events_errors_su_143.sdi to /root/local/backup/performance_schema/events_errors_su_143.sdi.zst
2024-02-02T01:05:57.098995+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/events_errors_su_143.sdi to /root/local/backup/performance_schema/events_errors_su_143.sdi.zst
2024-02-02T01:05:57.099769+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/users_144.sdi to /root/local/backup/performance_schema/users_144.sdi.zst
2024-02-02T01:05:57.100075+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/users_144.sdi to /root/local/backup/performance_schema/users_144.sdi.zst
2024-02-02T01:05:57.101096+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/accounts_145.sdi to /root/local/backup/performance_schema/accounts_145.sdi.zst
2024-02-02T01:05:57.101496+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/accounts_145.sdi to /root/local/backup/performance_schema/accounts_145.sdi.zst
2024-02-02T01:05:57.102282+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/hosts_146.sdi to /root/local/backup/performance_schema/hosts_146.sdi.zst
2024-02-02T01:05:57.102606+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/hosts_146.sdi to /root/local/backup/performance_schema/hosts_146.sdi.zst
2024-02-02T01:05:57.103401+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/socket_instances_147.sdi to /root/local/backup/performance_schema/socket_instances_147.sdi.zst
2024-02-02T01:05:57.103969+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/socket_instances_147.sdi to /root/local/backup/performance_schema/socket_instances_147.sdi.zst
2024-02-02T01:05:57.105102+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/socket_summary_b_148.sdi to /root/local/backup/performance_schema/socket_summary_b_148.sdi.zst
2024-02-02T01:05:57.105567+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/socket_summary_b_148.sdi to /root/local/backup/performance_schema/socket_summary_b_148.sdi.zst
2024-02-02T01:05:57.106400+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/socket_summary_b_149.sdi to /root/local/backup/performance_schema/socket_summary_b_149.sdi.zst
2024-02-02T01:05:57.106842+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/socket_summary_b_149.sdi to /root/local/backup/performance_schema/socket_summary_b_149.sdi.zst
2024-02-02T01:05:57.107698+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/session_connect__150.sdi to /root/local/backup/performance_schema/session_connect__150.sdi.zst
2024-02-02T01:05:57.108079+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/session_connect__150.sdi to /root/local/backup/performance_schema/session_connect__150.sdi.zst
2024-02-02T01:05:57.108794+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/session_account__151.sdi to /root/local/backup/performance_schema/session_account__151.sdi.zst
2024-02-02T01:05:57.109109+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/session_account__151.sdi to /root/local/backup/performance_schema/session_account__151.sdi.zst
2024-02-02T01:05:57.109935+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/keyring_keys_152.sdi to /root/local/backup/performance_schema/keyring_keys_152.sdi.zst
2024-02-02T01:05:57.110211+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/keyring_keys_152.sdi to /root/local/backup/performance_schema/keyring_keys_152.sdi.zst
2024-02-02T01:05:57.110982+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/memory_summary_g_153.sdi to /root/local/backup/performance_schema/memory_summary_g_153.sdi.zst
2024-02-02T01:05:57.111355+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/memory_summary_g_153.sdi to /root/local/backup/performance_schema/memory_summary_g_153.sdi.zst
2024-02-02T01:05:57.112223+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/memory_summary_b_154.sdi to /root/local/backup/performance_schema/memory_summary_b_154.sdi.zst
2024-02-02T01:05:57.112594+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/memory_summary_b_154.sdi to /root/local/backup/performance_schema/memory_summary_b_154.sdi.zst
2024-02-02T01:05:57.113503+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/memory_summary_b_155.sdi to /root/local/backup/performance_schema/memory_summary_b_155.sdi.zst
2024-02-02T01:05:57.113880+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/memory_summary_b_155.sdi to /root/local/backup/performance_schema/memory_summary_b_155.sdi.zst
2024-02-02T01:05:57.114640+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/memory_summary_b_156.sdi to /root/local/backup/performance_schema/memory_summary_b_156.sdi.zst
2024-02-02T01:05:57.114989+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/memory_summary_b_156.sdi to /root/local/backup/performance_schema/memory_summary_b_156.sdi.zst
2024-02-02T01:05:57.115781+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/memory_summary_b_157.sdi to /root/local/backup/performance_schema/memory_summary_b_157.sdi.zst
2024-02-02T01:05:57.116137+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/memory_summary_b_157.sdi to /root/local/backup/performance_schema/memory_summary_b_157.sdi.zst
2024-02-02T01:05:57.116939+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/table_handles_158.sdi to /root/local/backup/performance_schema/table_handles_158.sdi.zst
2024-02-02T01:05:57.117295+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/table_handles_158.sdi to /root/local/backup/performance_schema/table_handles_158.sdi.zst
2024-02-02T01:05:57.118065+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/metadata_locks_159.sdi to /root/local/backup/performance_schema/metadata_locks_159.sdi.zst
2024-02-02T01:05:57.118436+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/metadata_locks_159.sdi to /root/local/backup/performance_schema/metadata_locks_159.sdi.zst
2024-02-02T01:05:57.119207+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/data_locks_160.sdi to /root/local/backup/performance_schema/data_locks_160.sdi.zst
2024-02-02T01:05:57.119611+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/data_locks_160.sdi to /root/local/backup/performance_schema/data_locks_160.sdi.zst
2024-02-02T01:05:57.120369+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/data_lock_waits_161.sdi to /root/local/backup/performance_schema/data_lock_waits_161.sdi.zst
2024-02-02T01:05:57.120732+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/data_lock_waits_161.sdi to /root/local/backup/performance_schema/data_lock_waits_161.sdi.zst
2024-02-02T01:05:57.127589+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/replication_conn_162.sdi to /root/local/backup/performance_schema/replication_conn_162.sdi.zst
2024-02-02T01:05:57.128083+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/replication_conn_162.sdi to /root/local/backup/performance_schema/replication_conn_162.sdi.zst
2024-02-02T01:05:57.128831+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/replication_grou_163.sdi to /root/local/backup/performance_schema/replication_grou_163.sdi.zst
2024-02-02T01:05:57.129176+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/replication_grou_163.sdi to /root/local/backup/performance_schema/replication_grou_163.sdi.zst
2024-02-02T01:05:57.129896+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/replication_conn_164.sdi to /root/local/backup/performance_schema/replication_conn_164.sdi.zst
2024-02-02T01:05:57.130279+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/replication_conn_164.sdi to /root/local/backup/performance_schema/replication_conn_164.sdi.zst
2024-02-02T01:05:57.131042+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/replication_appl_165.sdi to /root/local/backup/performance_schema/replication_appl_165.sdi.zst
2024-02-02T01:05:57.131424+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/replication_appl_165.sdi to /root/local/backup/performance_schema/replication_appl_165.sdi.zst
2024-02-02T01:05:57.132188+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/replication_appl_166.sdi to /root/local/backup/performance_schema/replication_appl_166.sdi.zst
2024-02-02T01:05:57.132496+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/replication_appl_166.sdi to /root/local/backup/performance_schema/replication_appl_166.sdi.zst
2024-02-02T01:05:57.133242+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/replication_appl_167.sdi to /root/local/backup/performance_schema/replication_appl_167.sdi.zst
2024-02-02T01:05:57.133614+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/replication_appl_167.sdi to /root/local/backup/performance_schema/replication_appl_167.sdi.zst
2024-02-02T01:05:57.134331+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/replication_appl_168.sdi to /root/local/backup/performance_schema/replication_appl_168.sdi.zst
2024-02-02T01:05:57.134730+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/replication_appl_168.sdi to /root/local/backup/performance_schema/replication_appl_168.sdi.zst
2024-02-02T01:05:57.135441+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/replication_grou_169.sdi to /root/local/backup/performance_schema/replication_grou_169.sdi.zst
2024-02-02T01:05:57.135798+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/replication_grou_169.sdi to /root/local/backup/performance_schema/replication_grou_169.sdi.zst
2024-02-02T01:05:57.136813+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/replication_appl_170.sdi to /root/local/backup/performance_schema/replication_appl_170.sdi.zst
2024-02-02T01:05:57.137117+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/replication_appl_170.sdi to /root/local/backup/performance_schema/replication_appl_170.sdi.zst
2024-02-02T01:05:57.138020+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/replication_appl_171.sdi to /root/local/backup/performance_schema/replication_appl_171.sdi.zst
2024-02-02T01:05:57.138311+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/replication_appl_171.sdi to /root/local/backup/performance_schema/replication_appl_171.sdi.zst
2024-02-02T01:05:57.139142+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/replication_asyn_172.sdi to /root/local/backup/performance_schema/replication_asyn_172.sdi.zst
2024-02-02T01:05:57.139476+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/replication_asyn_172.sdi to /root/local/backup/performance_schema/replication_asyn_172.sdi.zst
2024-02-02T01:05:57.140605+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/replication_asyn_173.sdi to /root/local/backup/performance_schema/replication_asyn_173.sdi.zst
2024-02-02T01:05:57.140910+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/replication_asyn_173.sdi to /root/local/backup/performance_schema/replication_asyn_173.sdi.zst
2024-02-02T01:05:57.141655+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/log_status_174.sdi to /root/local/backup/performance_schema/log_status_174.sdi.zst
2024-02-02T01:05:57.141924+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/log_status_174.sdi to /root/local/backup/performance_schema/log_status_174.sdi.zst
2024-02-02T01:05:57.142768+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/prepared_stateme_175.sdi to /root/local/backup/performance_schema/prepared_stateme_175.sdi.zst
2024-02-02T01:05:57.143214+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/prepared_stateme_175.sdi to /root/local/backup/performance_schema/prepared_stateme_175.sdi.zst
2024-02-02T01:05:57.144189+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/user_variables_b_176.sdi to /root/local/backup/performance_schema/user_variables_b_176.sdi.zst
2024-02-02T01:05:57.144541+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/user_variables_b_176.sdi to /root/local/backup/performance_schema/user_variables_b_176.sdi.zst
2024-02-02T01:05:57.145514+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/status_by_accoun_177.sdi to /root/local/backup/performance_schema/status_by_accoun_177.sdi.zst
2024-02-02T01:05:57.145870+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/status_by_accoun_177.sdi to /root/local/backup/performance_schema/status_by_accoun_177.sdi.zst
2024-02-02T01:05:57.147005+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/status_by_host_178.sdi to /root/local/backup/performance_schema/status_by_host_178.sdi.zst
2024-02-02T01:05:57.147318+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/status_by_host_178.sdi to /root/local/backup/performance_schema/status_by_host_178.sdi.zst
2024-02-02T01:05:57.148290+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/status_by_thread_179.sdi to /root/local/backup/performance_schema/status_by_thread_179.sdi.zst
2024-02-02T01:05:57.148613+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/status_by_thread_179.sdi to /root/local/backup/performance_schema/status_by_thread_179.sdi.zst
2024-02-02T01:05:57.149570+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/status_by_user_180.sdi to /root/local/backup/performance_schema/status_by_user_180.sdi.zst
2024-02-02T01:05:57.149858+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/status_by_user_180.sdi to /root/local/backup/performance_schema/status_by_user_180.sdi.zst
2024-02-02T01:05:57.150835+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/global_status_181.sdi to /root/local/backup/performance_schema/global_status_181.sdi.zst
2024-02-02T01:05:57.151159+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/global_status_181.sdi to /root/local/backup/performance_schema/global_status_181.sdi.zst
2024-02-02T01:05:57.152059+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/session_status_182.sdi to /root/local/backup/performance_schema/session_status_182.sdi.zst
2024-02-02T01:05:57.152361+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/session_status_182.sdi to /root/local/backup/performance_schema/session_status_182.sdi.zst
2024-02-02T01:05:57.153342+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/variables_by_thr_183.sdi to /root/local/backup/performance_schema/variables_by_thr_183.sdi.zst
2024-02-02T01:05:57.153648+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/variables_by_thr_183.sdi to /root/local/backup/performance_schema/variables_by_thr_183.sdi.zst
2024-02-02T01:05:57.154570+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/global_variables_184.sdi to /root/local/backup/performance_schema/global_variables_184.sdi.zst
2024-02-02T01:05:57.154853+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/global_variables_184.sdi to /root/local/backup/performance_schema/global_variables_184.sdi.zst
2024-02-02T01:05:57.155682+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/session_variable_185.sdi to /root/local/backup/performance_schema/session_variable_185.sdi.zst
2024-02-02T01:05:57.155959+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/session_variable_185.sdi to /root/local/backup/performance_schema/session_variable_185.sdi.zst
2024-02-02T01:05:57.156777+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/variables_info_186.sdi to /root/local/backup/performance_schema/variables_info_186.sdi.zst
2024-02-02T01:05:57.157117+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/variables_info_186.sdi to /root/local/backup/performance_schema/variables_info_186.sdi.zst
2024-02-02T01:05:57.158041+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/persisted_variab_187.sdi to /root/local/backup/performance_schema/persisted_variab_187.sdi.zst
2024-02-02T01:05:57.158322+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/persisted_variab_187.sdi to /root/local/backup/performance_schema/persisted_variab_187.sdi.zst
2024-02-02T01:05:57.159157+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/user_defined_fun_188.sdi to /root/local/backup/performance_schema/user_defined_fun_188.sdi.zst
2024-02-02T01:05:57.159482+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/user_defined_fun_188.sdi to /root/local/backup/performance_schema/user_defined_fun_188.sdi.zst
2024-02-02T01:05:57.160341+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/binary_log_trans_189.sdi to /root/local/backup/performance_schema/binary_log_trans_189.sdi.zst
2024-02-02T01:05:57.160787+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/binary_log_trans_189.sdi to /root/local/backup/performance_schema/binary_log_trans_189.sdi.zst
2024-02-02T01:05:57.161648+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/tls_channel_stat_190.sdi to /root/local/backup/performance_schema/tls_channel_stat_190.sdi.zst
2024-02-02T01:05:57.162030+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/tls_channel_stat_190.sdi to /root/local/backup/performance_schema/tls_channel_stat_190.sdi.zst
2024-02-02T01:05:57.162873+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing performance_schema/keyring_componen_191.sdi to /root/local/backup/performance_schema/keyring_componen_191.sdi.zst
2024-02-02T01:05:57.163200+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing performance_schema/keyring_componen_191.sdi to /root/local/backup/performance_schema/keyring_componen_191.sdi.zst
2024-02-02T01:05:57.164366+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing /root/local/backup/sbtest/db.opt.zst
2024-02-02T01:05:57.164406+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing file /root/local/backup/sbtest/db.opt.zst
2024-02-02T01:05:57.212381+08:00 0 [Note] [MY-011825] [Xtrabackup] Finished backing up non-InnoDB tables and files
2024-02-02T01:05:57.212475+08:00 0 [Note] [MY-011825] [Xtrabackup] Executing FLUSH NO_WRITE_TO_BINLOG BINARY LOGS
2024-02-02T01:05:57.229810+08:00 0 [Note] [MY-011825] [Xtrabackup] Selecting LSN and binary log position from p_s.log_status
2024-02-02T01:05:57.246962+08:00 0 [Note] [MY-011825] [Xtrabackup] Compressing /root/local/mysql_7032/log/bin.000011 to /root/local/backup/bin.000011.zst up to position 237
2024-02-02T01:05:57.247120+08:00 0 [Note] [MY-011825] [Xtrabackup] Done: Compressing /root/local/mysql_7032/log/bin.000011 to /root/local/backup/bin.000011.zst
2024-02-02T01:05:57.248306+08:00 0 [Note] [MY-011825] [Xtrabackup] Compressing /root/local/backup/bin.index.zst
2024-02-02T01:05:57.248375+08:00 0 [Note] [MY-011825] [Xtrabackup] Done: Compressing file /root/local/backup/bin.index.zst
2024-02-02T01:05:57.265356+08:00 0 [Note] [MY-011825] [Xtrabackup] Compressing /root/local/backup/xtrabackup_binlog_info.zst
2024-02-02T01:05:57.265454+08:00 0 [Note] [MY-011825] [Xtrabackup] Done: Compressing file /root/local/backup/xtrabackup_binlog_info.zst
2024-02-02T01:05:57.266714+08:00 0 [Note] [MY-011825] [Xtrabackup] Executing FLUSH NO_WRITE_TO_BINLOG ENGINE LOGS...
2024-02-02T01:05:57.300774+08:00 0 [Note] [MY-011825] [Xtrabackup] The latest check point (for incremental): '50963884'
2024-02-02T01:05:57.300853+08:00 0 [Note] [MY-011825] [Xtrabackup] Stopping log copying thread at LSN 82657509
2024-02-02T01:05:57.320856+08:00 1 [Note] [MY-011825] [Xtrabackup] >> log scanned up to (82665220)
2024-02-02T01:05:58.356592+08:00 0 [Note] [MY-011825] [Xtrabackup] Executing UNLOCK INSTANCE
2024-02-02T01:05:58.357121+08:00 0 [Note] [MY-011825] [Xtrabackup] All tables unlocked
2024-02-02T01:05:58.357305+08:00 0 [Note] [MY-011825] [Xtrabackup] Compressing ib_buffer_pool to /root/local/backup/ib_buffer_pool.zst
2024-02-02T01:05:58.358190+08:00 0 [Note] [MY-011825] [Xtrabackup] Done: Compressing ib_buffer_pool to /root/local/backup/ib_buffer_pool.zst
2024-02-02T01:05:58.359276+08:00 0 [Note] [MY-011825] [Xtrabackup] Backup created in directory '/root/local/backup/'
2024-02-02T01:05:58.359329+08:00 0 [Note] [MY-011825] [Xtrabackup] MySQL binlog position: filename 'bin.000011', position '237', GTID of the last change '662a2811-c03c-11ee-affc-00163e62ca8a:1-14162,6d1f356f-bdeb-11ee-b3aa-00163e62ca8a:1-11'
2024-02-02T01:05:58.359527+08:00 0 [Note] [MY-011825] [Xtrabackup] Compressing /root/local/backup/backup-my.cnf.zst
2024-02-02T01:05:58.359647+08:00 0 [Note] [MY-011825] [Xtrabackup] Done: Compressing file /root/local/backup/backup-my.cnf.zst
2024-02-02T01:05:58.361686+08:00 0 [Note] [MY-011825] [Xtrabackup] Compressing /root/local/backup/xtrabackup_info.zst
2024-02-02T01:05:58.361800+08:00 0 [Note] [MY-011825] [Xtrabackup] Done: Compressing file /root/local/backup/xtrabackup_info.zst
2024-02-02T01:05:59.364039+08:00 0 [Note] [MY-011825] [Xtrabackup] Transaction log of lsn (50185294) to (82736960) was copied.
2024-02-02T01:05:59.775487+08:00 0 [Note] [MY-011825] [Xtrabackup] completed OK!
[root@dev backup]#

```


### stream=xbstream
```sql
[root@dev backup]# /root/local/xtrabackup_8032/bin/xtrabackup --user=root --password=root --host=172.17.137.12 --port=7032 --backup --target-dir=/root/local/backup/ --compress=zstd --stream=xbstream|gzip > xb.xbstream.gz
2024-02-02T01:10:48.272695+08:00 0 [Note] [MY-011825] [Xtrabackup] recognized client arguments: --user=root --password=* --host=172.17.137.12 --port=7032 --backup=1 --target-dir=/root/local/backup/ --compress=zstd --stream=xbstream
/root/local/xtrabackup_8032/bin/xtrabackup version 8.0.32-26 based on MySQL server 8.0.32 Linux (x86_64) (revision id: )
240202 01:10:48  version_check Connecting to MySQL server with DSN 'dbi:mysql:;mysql_read_default_group=xtrabackup;host=172.17.137.12;port=7032' as 'root'  (using password: YES).
Failed to connect to MySQL server as DBD::mysql module is not installed at - line 1548.
2024-02-02T01:10:48.422290+08:00 0 [Note] [MY-011825] [Xtrabackup] Connecting to MySQL server host: 172.17.137.12, user: root, password: set, port: 7032, socket: not set
2024-02-02T01:10:48.470137+08:00 0 [Note] [MY-011825] [Xtrabackup] Using server version 8.0.32-debug
2024-02-02T01:10:48.485161+08:00 0 [Note] [MY-011825] [Xtrabackup] Executing LOCK INSTANCE FOR BACKUP ...
2024-02-02T01:10:48.694128+08:00 0 [Note] [MY-011825] [Xtrabackup] uses posix_fadvise().
2024-02-02T01:10:48.694246+08:00 0 [Note] [MY-011825] [Xtrabackup] cd to /root/local/mysql_7032/data/
2024-02-02T01:10:48.694280+08:00 0 [Note] [MY-011825] [Xtrabackup] open files limit requested 0, set to 1024000
2024-02-02T01:10:48.697282+08:00 0 [Note] [MY-011825] [Xtrabackup] using the following InnoDB configuration:
2024-02-02T01:10:48.697341+08:00 0 [Note] [MY-011825] [Xtrabackup] innodb_data_home_dir = .
2024-02-02T01:10:48.697359+08:00 0 [Note] [MY-011825] [Xtrabackup] innodb_data_file_path = ibdata1:12M:autoextend
2024-02-02T01:10:48.697527+08:00 0 [Note] [MY-011825] [Xtrabackup] innodb_log_group_home_dir = ./
2024-02-02T01:10:48.697551+08:00 0 [Note] [MY-011825] [Xtrabackup] innodb_log_files_in_group = 2
2024-02-02T01:10:48.697576+08:00 0 [Note] [MY-011825] [Xtrabackup] innodb_log_file_size = 1073741824
2024-02-02T01:10:48.706833+08:00 0 [Note] [MY-011825] [Xtrabackup] inititialize_service_handles suceeded
2024-02-02T01:10:49.127618+08:00 0 [Note] [MY-011825] [Xtrabackup] Connecting to MySQL server host: 172.17.137.12, user: root, password: set, port: 7032, socket: not set
2024-02-02T01:10:49.152989+08:00 0 [Note] [MY-011825] [Xtrabackup] Redo Log Archiving is not set up.
2024-02-02T01:10:49.317986+08:00 0 [Note] [MY-011825] [Xtrabackup] Starting to parse redo log at lsn = 80574515
2024-02-02T01:10:49.327648+08:00 0 [Note] [MY-012564] [InnoDB] Recovery parsing buffer extended to 4194304.
2024-02-02T01:10:49.337129+08:00 0 [Note] [MY-012564] [InnoDB] Recovery parsing buffer extended to 8388608.
2024-02-02T01:10:55.162685+08:00 0 [Warning] [MY-011825] [Xtrabackup] Log block checksum mismatch (block no 0 at lsn 109279744): expected 0, calculated checksum 3965168067 block epoch no: 0
2024-02-02T01:10:55.162846+08:00 0 [Warning] [MY-011825] [Xtrabackup] this is possible when the log block has not been fully written by the server, will retry later.
2024-02-02T01:10:56.004872+08:00 1 [Note] [MY-011825] [Xtrabackup] >> log scanned up to (109324469)
2024-02-02T01:10:56.213711+08:00 0 [Note] [MY-011825] [Xtrabackup] Generating a list of tablespaces
2024-02-02T01:10:56.213962+08:00 0 [Note] [MY-012204] [InnoDB] Scanning './'
2024-02-02T01:10:56.217074+08:00 0 [Note] [MY-012208] [InnoDB] Completed space ID check of 2 files.
2024-02-02T01:10:56.218126+08:00 0 [Warning] [MY-012091] [InnoDB] Allocated tablespace ID 1 for sys/sys_config, old maximum was 0
2024-02-02T01:10:56.221118+08:00 0 [Note] [MY-013252] [InnoDB] Using undo tablespace './undo_001'.
2024-02-02T01:10:56.221824+08:00 0 [Note] [MY-013252] [InnoDB] Using undo tablespace './undo_002'.
2024-02-02T01:10:56.223233+08:00 0 [Note] [MY-012910] [InnoDB] Opened 2 existing undo tablespaces.
2024-02-02T01:10:56.223937+08:00 2 [Note] [MY-011825] [Xtrabackup] Compressing and streaming ./ibdata1
2024-02-02T01:10:56.364806+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming ./ibdata1
2024-02-02T01:10:56.367302+08:00 2 [Note] [MY-011825] [Xtrabackup] Compressing and streaming ./sys/sys_config.ibd
2024-02-02T01:10:56.368241+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming ./sys/sys_config.ibd
2024-02-02T01:10:56.369751+08:00 2 [Note] [MY-011825] [Xtrabackup] Compressing and streaming ./apple/info.ibd
2024-02-02T01:10:56.370684+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming ./apple/info.ibd
2024-02-02T01:10:56.371850+08:00 2 [Note] [MY-011825] [Xtrabackup] Compressing and streaming ./apple/sbtest2.ibd
2024-02-02T01:10:56.627110+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming ./apple/sbtest2.ibd
2024-02-02T01:10:56.700535+08:00 2 [Note] [MY-011825] [Xtrabackup] Compressing and streaming ./apple/sbtest3.ibd
2024-02-02T01:10:56.922715+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming ./apple/sbtest3.ibd
2024-02-02T01:10:57.005804+08:00 2 [Note] [MY-011825] [Xtrabackup] Compressing and streaming ./apple/sbtest1.ibd
2024-02-02T01:10:57.012393+08:00 1 [Note] [MY-011825] [Xtrabackup] >> log scanned up to (109391426)
2024-02-02T01:10:57.221683+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming ./apple/sbtest1.ibd
2024-02-02T01:10:57.328112+08:00 2 [Note] [MY-011825] [Xtrabackup] Compressing and streaming ./apple/sbtest4.ibd
2024-02-02T01:10:57.572990+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming ./apple/sbtest4.ibd
2024-02-02T01:10:57.623648+08:00 2 [Note] [MY-011825] [Xtrabackup] Compressing and streaming ./mysql.ibd
2024-02-02T01:10:58.017878+08:00 1 [Note] [MY-011825] [Xtrabackup] >> log scanned up to (109439131)
2024-02-02T01:10:58.054325+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming ./mysql.ibd
2024-02-02T01:10:58.130449+08:00 2 [Note] [MY-011825] [Xtrabackup] Compressing and streaming ./undo_002
2024-02-02T01:10:58.332598+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming ./undo_002
2024-02-02T01:10:58.364279+08:00 2 [Note] [MY-011825] [Xtrabackup] Compressing and streaming ./undo_001
2024-02-02T01:10:58.664803+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming ./undo_001
2024-02-02T01:10:59.032442+08:00 1 [Note] [MY-011825] [Xtrabackup] >> log scanned up to (109517380)
2024-02-02T01:10:59.224015+08:00 0 [Note] [MY-011825] [Xtrabackup] Starting to backup non-InnoDB tables and files
2024-02-02T01:10:59.232692+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming mysql/general_log_213.sdi to <STDOUT>
2024-02-02T01:10:59.233162+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming mysql/general_log_213.sdi to <STDOUT>
2024-02-02T01:10:59.234598+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming mysql/general_log.CSM to <STDOUT>
2024-02-02T01:10:59.234758+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming mysql/general_log.CSM to <STDOUT>
2024-02-02T01:10:59.235634+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming mysql/general_log.CSV to <STDOUT>
2024-02-02T01:10:59.235662+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming mysql/general_log.CSV to <STDOUT>
2024-02-02T01:10:59.236249+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming mysql/slow_log_214.sdi to <STDOUT>
2024-02-02T01:10:59.236615+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming mysql/slow_log_214.sdi to <STDOUT>
2024-02-02T01:10:59.237223+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming mysql/slow_log.CSM to <STDOUT>
2024-02-02T01:10:59.237383+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming mysql/slow_log.CSM to <STDOUT>
2024-02-02T01:10:59.238005+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming mysql/slow_log.CSV to <STDOUT>
2024-02-02T01:10:59.238033+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming mysql/slow_log.CSV to <STDOUT>
2024-02-02T01:10:59.238646+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_statement_123.sdi to <STDOUT>
2024-02-02T01:10:59.239167+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_statement_123.sdi to <STDOUT>
2024-02-02T01:10:59.239825+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_statement_124.sdi to <STDOUT>
2024-02-02T01:10:59.240212+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_statement_124.sdi to <STDOUT>
2024-02-02T01:10:59.240841+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/cond_instances_82.sdi to <STDOUT>
2024-02-02T01:10:59.241096+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/cond_instances_82.sdi to <STDOUT>
2024-02-02T01:10:59.241723+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/error_log_83.sdi to <STDOUT>
2024-02-02T01:10:59.242006+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/error_log_83.sdi to <STDOUT>
2024-02-02T01:10:59.242601+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_waits_cur_84.sdi to <STDOUT>
2024-02-02T01:10:59.242933+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_waits_cur_84.sdi to <STDOUT>
2024-02-02T01:10:59.243511+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_waits_his_85.sdi to <STDOUT>
2024-02-02T01:10:59.243869+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_waits_his_85.sdi to <STDOUT>
2024-02-02T01:10:59.244430+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_waits_his_86.sdi to <STDOUT>
2024-02-02T01:10:59.244737+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_waits_his_86.sdi to <STDOUT>
2024-02-02T01:10:59.245294+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_waits_sum_87.sdi to <STDOUT>
2024-02-02T01:10:59.245609+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_waits_sum_87.sdi to <STDOUT>
2024-02-02T01:10:59.246144+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_waits_sum_88.sdi to <STDOUT>
2024-02-02T01:10:59.246424+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_waits_sum_88.sdi to <STDOUT>
2024-02-02T01:10:59.246995+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_waits_sum_89.sdi to <STDOUT>
2024-02-02T01:10:59.247230+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_waits_sum_89.sdi to <STDOUT>
2024-02-02T01:10:59.247818+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_waits_sum_90.sdi to <STDOUT>
2024-02-02T01:10:59.248082+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_waits_sum_90.sdi to <STDOUT>
2024-02-02T01:10:59.248658+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_waits_sum_91.sdi to <STDOUT>
2024-02-02T01:10:59.248954+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_waits_sum_91.sdi to <STDOUT>
2024-02-02T01:10:59.249525+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_waits_sum_92.sdi to <STDOUT>
2024-02-02T01:10:59.249751+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_waits_sum_92.sdi to <STDOUT>
2024-02-02T01:10:59.250336+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/file_instances_93.sdi to <STDOUT>
2024-02-02T01:10:59.250624+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/file_instances_93.sdi to <STDOUT>
2024-02-02T01:10:59.251215+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/file_summary_by__94.sdi to <STDOUT>
2024-02-02T01:10:59.251604+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/file_summary_by__94.sdi to <STDOUT>
2024-02-02T01:10:59.252147+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/file_summary_by__95.sdi to <STDOUT>
2024-02-02T01:10:59.252468+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/file_summary_by__95.sdi to <STDOUT>
2024-02-02T01:10:59.253008+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/host_cache_96.sdi to <STDOUT>
2024-02-02T01:10:59.253329+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/host_cache_96.sdi to <STDOUT>
2024-02-02T01:10:59.253892+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/mutex_instances_97.sdi to <STDOUT>
2024-02-02T01:10:59.254126+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/mutex_instances_97.sdi to <STDOUT>
2024-02-02T01:10:59.254712+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/objects_summary__98.sdi to <STDOUT>
2024-02-02T01:10:59.255028+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/objects_summary__98.sdi to <STDOUT>
2024-02-02T01:10:59.255655+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/performance_time_99.sdi to <STDOUT>
2024-02-02T01:10:59.255880+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/performance_time_99.sdi to <STDOUT>
2024-02-02T01:10:59.256455+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/processlist_100.sdi to <STDOUT>
2024-02-02T01:10:59.256723+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/processlist_100.sdi to <STDOUT>
2024-02-02T01:10:59.257276+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/rwlock_instances_101.sdi to <STDOUT>
2024-02-02T01:10:59.257565+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/rwlock_instances_101.sdi to <STDOUT>
2024-02-02T01:10:59.258117+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/setup_actors_102.sdi to <STDOUT>
2024-02-02T01:10:59.258363+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/setup_actors_102.sdi to <STDOUT>
2024-02-02T01:10:59.258924+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/setup_consumers_103.sdi to <STDOUT>
2024-02-02T01:10:59.259221+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/setup_consumers_103.sdi to <STDOUT>
2024-02-02T01:10:59.259886+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/setup_instrument_104.sdi to <STDOUT>
2024-02-02T01:10:59.260288+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/setup_instrument_104.sdi to <STDOUT>
2024-02-02T01:10:59.261047+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/setup_objects_105.sdi to <STDOUT>
2024-02-02T01:10:59.261358+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/setup_objects_105.sdi to <STDOUT>
2024-02-02T01:10:59.261997+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/setup_threads_106.sdi to <STDOUT>
2024-02-02T01:10:59.262386+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/setup_threads_106.sdi to <STDOUT>
2024-02-02T01:10:59.262996+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/table_io_waits_s_107.sdi to <STDOUT>
2024-02-02T01:10:59.263384+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/table_io_waits_s_107.sdi to <STDOUT>
2024-02-02T01:10:59.263958+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/table_io_waits_s_108.sdi to <STDOUT>
2024-02-02T01:10:59.264336+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/table_io_waits_s_108.sdi to <STDOUT>
2024-02-02T01:10:59.264948+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/table_lock_waits_109.sdi to <STDOUT>
2024-02-02T01:10:59.265445+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/table_lock_waits_109.sdi to <STDOUT>
2024-02-02T01:10:59.266127+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/threads_110.sdi to <STDOUT>
2024-02-02T01:10:59.266530+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/threads_110.sdi to <STDOUT>
2024-02-02T01:10:59.267122+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_stages_cu_111.sdi to <STDOUT>
2024-02-02T01:10:59.267455+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_stages_cu_111.sdi to <STDOUT>
2024-02-02T01:10:59.268057+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_stages_hi_112.sdi to <STDOUT>
2024-02-02T01:10:59.268406+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_stages_hi_112.sdi to <STDOUT>
2024-02-02T01:10:59.269010+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_stages_hi_113.sdi to <STDOUT>
2024-02-02T01:10:59.269287+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_stages_hi_113.sdi to <STDOUT>
2024-02-02T01:10:59.269863+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_stages_su_114.sdi to <STDOUT>
2024-02-02T01:10:59.270126+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_stages_su_114.sdi to <STDOUT>
2024-02-02T01:10:59.270687+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_stages_su_115.sdi to <STDOUT>
2024-02-02T01:10:59.270973+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_stages_su_115.sdi to <STDOUT>
2024-02-02T01:10:59.271537+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_stages_su_116.sdi to <STDOUT>
2024-02-02T01:10:59.271863+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_stages_su_116.sdi to <STDOUT>
2024-02-02T01:10:59.272414+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_stages_su_117.sdi to <STDOUT>
2024-02-02T01:10:59.272745+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_stages_su_117.sdi to <STDOUT>
2024-02-02T01:10:59.273282+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_stages_su_118.sdi to <STDOUT>
2024-02-02T01:10:59.273544+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_stages_su_118.sdi to <STDOUT>
2024-02-02T01:10:59.274091+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_statement_119.sdi to <STDOUT>
2024-02-02T01:10:59.274516+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_statement_119.sdi to <STDOUT>
2024-02-02T01:10:59.275116+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_statement_120.sdi to <STDOUT>
2024-02-02T01:10:59.275556+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_statement_120.sdi to <STDOUT>
2024-02-02T01:10:59.276108+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_statement_121.sdi to <STDOUT>
2024-02-02T01:10:59.276517+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_statement_121.sdi to <STDOUT>
2024-02-02T01:10:59.277047+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_statement_122.sdi to <STDOUT>
2024-02-02T01:10:59.277427+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_statement_122.sdi to <STDOUT>
2024-02-02T01:10:59.277990+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_statement_125.sdi to <STDOUT>
2024-02-02T01:10:59.278370+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_statement_125.sdi to <STDOUT>
2024-02-02T01:10:59.278915+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_statement_126.sdi to <STDOUT>
2024-02-02T01:10:59.279235+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_statement_126.sdi to <STDOUT>
2024-02-02T01:10:59.279827+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_statement_127.sdi to <STDOUT>
2024-02-02T01:10:59.280201+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_statement_127.sdi to <STDOUT>
2024-02-02T01:10:59.280798+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_statement_128.sdi to <STDOUT>
2024-02-02T01:10:59.281160+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_statement_128.sdi to <STDOUT>
2024-02-02T01:10:59.281764+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_statement_129.sdi to <STDOUT>
2024-02-02T01:10:59.282014+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_statement_129.sdi to <STDOUT>
2024-02-02T01:10:59.282605+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_statement_130.sdi to <STDOUT>
2024-02-02T01:10:59.282864+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_statement_130.sdi to <STDOUT>
2024-02-02T01:10:59.283476+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_transacti_131.sdi to <STDOUT>
2024-02-02T01:10:59.283894+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_transacti_131.sdi to <STDOUT>
2024-02-02T01:10:59.284471+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_transacti_132.sdi to <STDOUT>
2024-02-02T01:10:59.284816+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_transacti_132.sdi to <STDOUT>
2024-02-02T01:10:59.285401+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_transacti_133.sdi to <STDOUT>
2024-02-02T01:10:59.285818+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_transacti_133.sdi to <STDOUT>
2024-02-02T01:10:59.286401+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_transacti_134.sdi to <STDOUT>
2024-02-02T01:10:59.286773+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_transacti_134.sdi to <STDOUT>
2024-02-02T01:10:59.287350+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_transacti_135.sdi to <STDOUT>
2024-02-02T01:10:59.287679+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_transacti_135.sdi to <STDOUT>
2024-02-02T01:10:59.288294+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_transacti_136.sdi to <STDOUT>
2024-02-02T01:10:59.288651+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_transacti_136.sdi to <STDOUT>
2024-02-02T01:10:59.289238+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_transacti_137.sdi to <STDOUT>
2024-02-02T01:10:59.289533+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_transacti_137.sdi to <STDOUT>
2024-02-02T01:10:59.290119+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_transacti_138.sdi to <STDOUT>
2024-02-02T01:10:59.290434+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_transacti_138.sdi to <STDOUT>
2024-02-02T01:10:59.291012+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_errors_su_139.sdi to <STDOUT>
2024-02-02T01:10:59.291332+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_errors_su_139.sdi to <STDOUT>
2024-02-02T01:10:59.291932+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_errors_su_140.sdi to <STDOUT>
2024-02-02T01:10:59.292185+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_errors_su_140.sdi to <STDOUT>
2024-02-02T01:10:59.292779+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_errors_su_141.sdi to <STDOUT>
2024-02-02T01:10:59.293072+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_errors_su_141.sdi to <STDOUT>
2024-02-02T01:10:59.293667+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_errors_su_142.sdi to <STDOUT>
2024-02-02T01:10:59.293952+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_errors_su_142.sdi to <STDOUT>
2024-02-02T01:10:59.294546+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/events_errors_su_143.sdi to <STDOUT>
2024-02-02T01:10:59.294801+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/events_errors_su_143.sdi to <STDOUT>
2024-02-02T01:10:59.295378+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/users_144.sdi to <STDOUT>
2024-02-02T01:10:59.295660+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/users_144.sdi to <STDOUT>
2024-02-02T01:10:59.296235+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/accounts_145.sdi to <STDOUT>
2024-02-02T01:10:59.296513+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/accounts_145.sdi to <STDOUT>
2024-02-02T01:10:59.297065+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/hosts_146.sdi to <STDOUT>
2024-02-02T01:10:59.297294+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/hosts_146.sdi to <STDOUT>
2024-02-02T01:10:59.297876+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/socket_instances_147.sdi to <STDOUT>
2024-02-02T01:10:59.298227+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/socket_instances_147.sdi to <STDOUT>
2024-02-02T01:10:59.298811+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/socket_summary_b_148.sdi to <STDOUT>
2024-02-02T01:10:59.299124+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/socket_summary_b_148.sdi to <STDOUT>
2024-02-02T01:10:59.299740+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/socket_summary_b_149.sdi to <STDOUT>
2024-02-02T01:10:59.300062+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/socket_summary_b_149.sdi to <STDOUT>
2024-02-02T01:10:59.300675+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/session_connect__150.sdi to <STDOUT>
2024-02-02T01:10:59.300934+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/session_connect__150.sdi to <STDOUT>
2024-02-02T01:10:59.301528+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/session_account__151.sdi to <STDOUT>
2024-02-02T01:10:59.301779+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/session_account__151.sdi to <STDOUT>
2024-02-02T01:10:59.302364+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/keyring_keys_152.sdi to <STDOUT>
2024-02-02T01:10:59.302606+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/keyring_keys_152.sdi to <STDOUT>
2024-02-02T01:10:59.303160+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/memory_summary_g_153.sdi to <STDOUT>
2024-02-02T01:10:59.303440+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/memory_summary_g_153.sdi to <STDOUT>
2024-02-02T01:10:59.304000+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/memory_summary_b_154.sdi to <STDOUT>
2024-02-02T01:10:59.304321+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/memory_summary_b_154.sdi to <STDOUT>
2024-02-02T01:10:59.304892+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/memory_summary_b_155.sdi to <STDOUT>
2024-02-02T01:10:59.305165+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/memory_summary_b_155.sdi to <STDOUT>
2024-02-02T01:10:59.305720+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/memory_summary_b_156.sdi to <STDOUT>
2024-02-02T01:10:59.305983+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/memory_summary_b_156.sdi to <STDOUT>
2024-02-02T01:10:59.306556+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/memory_summary_b_157.sdi to <STDOUT>
2024-02-02T01:10:59.306833+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/memory_summary_b_157.sdi to <STDOUT>
2024-02-02T01:10:59.307382+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/table_handles_158.sdi to <STDOUT>
2024-02-02T01:10:59.307676+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/table_handles_158.sdi to <STDOUT>
2024-02-02T01:10:59.308233+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/metadata_locks_159.sdi to <STDOUT>
2024-02-02T01:10:59.308517+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/metadata_locks_159.sdi to <STDOUT>
2024-02-02T01:10:59.309096+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/data_locks_160.sdi to <STDOUT>
2024-02-02T01:10:59.309432+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/data_locks_160.sdi to <STDOUT>
2024-02-02T01:10:59.310092+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/data_lock_waits_161.sdi to <STDOUT>
2024-02-02T01:10:59.310427+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/data_lock_waits_161.sdi to <STDOUT>
2024-02-02T01:10:59.311044+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/replication_conn_162.sdi to <STDOUT>
2024-02-02T01:10:59.311509+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/replication_conn_162.sdi to <STDOUT>
2024-02-02T01:10:59.312152+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/replication_grou_163.sdi to <STDOUT>
2024-02-02T01:10:59.312422+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/replication_grou_163.sdi to <STDOUT>
2024-02-02T01:10:59.313055+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/replication_conn_164.sdi to <STDOUT>
2024-02-02T01:10:59.313459+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/replication_conn_164.sdi to <STDOUT>
2024-02-02T01:10:59.314057+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/replication_appl_165.sdi to <STDOUT>
2024-02-02T01:10:59.314368+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/replication_appl_165.sdi to <STDOUT>
2024-02-02T01:10:59.314967+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/replication_appl_166.sdi to <STDOUT>
2024-02-02T01:10:59.315211+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/replication_appl_166.sdi to <STDOUT>
2024-02-02T01:10:59.315841+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/replication_appl_167.sdi to <STDOUT>
2024-02-02T01:10:59.316156+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/replication_appl_167.sdi to <STDOUT>
2024-02-02T01:10:59.316755+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/replication_appl_168.sdi to <STDOUT>
2024-02-02T01:10:59.317132+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/replication_appl_168.sdi to <STDOUT>
2024-02-02T01:10:59.317725+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/replication_grou_169.sdi to <STDOUT>
2024-02-02T01:10:59.318046+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/replication_grou_169.sdi to <STDOUT>
2024-02-02T01:10:59.318630+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/replication_appl_170.sdi to <STDOUT>
2024-02-02T01:10:59.318892+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/replication_appl_170.sdi to <STDOUT>
2024-02-02T01:10:59.319459+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/replication_appl_171.sdi to <STDOUT>
2024-02-02T01:10:59.319716+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/replication_appl_171.sdi to <STDOUT>
2024-02-02T01:10:59.320270+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/replication_asyn_172.sdi to <STDOUT>
2024-02-02T01:10:59.320555+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/replication_asyn_172.sdi to <STDOUT>
2024-02-02T01:10:59.321103+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/replication_asyn_173.sdi to <STDOUT>
2024-02-02T01:10:59.321355+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/replication_asyn_173.sdi to <STDOUT>
2024-02-02T01:10:59.321915+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/log_status_174.sdi to <STDOUT>
2024-02-02T01:10:59.322154+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/log_status_174.sdi to <STDOUT>
2024-02-02T01:10:59.322723+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/prepared_stateme_175.sdi to <STDOUT>
2024-02-02T01:10:59.323179+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/prepared_stateme_175.sdi to <STDOUT>
2024-02-02T01:10:59.323771+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/user_variables_b_176.sdi to <STDOUT>
2024-02-02T01:10:59.324055+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/user_variables_b_176.sdi to <STDOUT>
2024-02-02T01:10:59.324641+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/status_by_accoun_177.sdi to <STDOUT>
2024-02-02T01:10:59.324924+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/status_by_accoun_177.sdi to <STDOUT>
2024-02-02T01:10:59.325506+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/status_by_host_178.sdi to <STDOUT>
2024-02-02T01:10:59.325739+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/status_by_host_178.sdi to <STDOUT>
2024-02-02T01:10:59.326328+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/status_by_thread_179.sdi to <STDOUT>
2024-02-02T01:10:59.326578+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/status_by_thread_179.sdi to <STDOUT>
2024-02-02T01:10:59.327155+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/status_by_user_180.sdi to <STDOUT>
2024-02-02T01:10:59.327410+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/status_by_user_180.sdi to <STDOUT>
2024-02-02T01:10:59.328012+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/global_status_181.sdi to <STDOUT>
2024-02-02T01:10:59.328246+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/global_status_181.sdi to <STDOUT>
2024-02-02T01:10:59.328873+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/session_status_182.sdi to <STDOUT>
2024-02-02T01:10:59.329100+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/session_status_182.sdi to <STDOUT>
2024-02-02T01:10:59.329736+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/variables_by_thr_183.sdi to <STDOUT>
2024-02-02T01:10:59.330001+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/variables_by_thr_183.sdi to <STDOUT>
2024-02-02T01:10:59.330614+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/global_variables_184.sdi to <STDOUT>
2024-02-02T01:10:59.330848+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/global_variables_184.sdi to <STDOUT>
2024-02-02T01:10:59.331503+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/session_variable_185.sdi to <STDOUT>
2024-02-02T01:10:59.331757+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/session_variable_185.sdi to <STDOUT>
2024-02-02T01:10:59.332364+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/variables_info_186.sdi to <STDOUT>
2024-02-02T01:10:59.332712+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/variables_info_186.sdi to <STDOUT>
2024-02-02T01:10:59.333305+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/persisted_variab_187.sdi to <STDOUT>
2024-02-02T01:10:59.333636+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/persisted_variab_187.sdi to <STDOUT>
2024-02-02T01:10:59.334197+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/user_defined_fun_188.sdi to <STDOUT>
2024-02-02T01:10:59.334453+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/user_defined_fun_188.sdi to <STDOUT>
2024-02-02T01:10:59.335012+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/binary_log_trans_189.sdi to <STDOUT>
2024-02-02T01:10:59.335330+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/binary_log_trans_189.sdi to <STDOUT>
2024-02-02T01:10:59.335886+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/tls_channel_stat_190.sdi to <STDOUT>
2024-02-02T01:10:59.336141+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/tls_channel_stat_190.sdi to <STDOUT>
2024-02-02T01:10:59.336699+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming performance_schema/keyring_componen_191.sdi to <STDOUT>
2024-02-02T01:10:59.336949+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming performance_schema/keyring_componen_191.sdi to <STDOUT>
2024-02-02T01:10:59.337560+08:00 3 [Note] [MY-011825] [Xtrabackup] Compressing and streaming <STDOUT>
2024-02-02T01:10:59.337590+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming file <STDOUT>
2024-02-02T01:10:59.426377+08:00 0 [Note] [MY-011825] [Xtrabackup] Finished backing up non-InnoDB tables and files
2024-02-02T01:10:59.426471+08:00 0 [Note] [MY-011825] [Xtrabackup] Executing FLUSH NO_WRITE_TO_BINLOG BINARY LOGS
2024-02-02T01:10:59.443138+08:00 0 [Note] [MY-011825] [Xtrabackup] Selecting LSN and binary log position from p_s.log_status
2024-02-02T01:10:59.455211+08:00 0 [Note] [MY-011825] [Xtrabackup] Compressing and streaming /root/local/mysql_7032/log/bin.000012 to <STDOUT> up to position 237
2024-02-02T01:10:59.455337+08:00 0 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming /root/local/mysql_7032/log/bin.000012 to <STDOUT>
2024-02-02T01:10:59.455555+08:00 0 [Note] [MY-011825] [Xtrabackup] Compressing and streaming <STDOUT>
2024-02-02T01:10:59.455593+08:00 0 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming file <STDOUT>
2024-02-02T01:10:59.464477+08:00 0 [Note] [MY-011825] [Xtrabackup] Compressing and streaming <STDOUT>
2024-02-02T01:10:59.464574+08:00 0 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming file <STDOUT>
2024-02-02T01:10:59.464643+08:00 0 [Note] [MY-011825] [Xtrabackup] Executing FLUSH NO_WRITE_TO_BINLOG ENGINE LOGS...
2024-02-02T01:10:59.466011+08:00 0 [Note] [MY-011825] [Xtrabackup] The latest check point (for incremental): '82519647'
2024-02-02T01:10:59.466036+08:00 0 [Note] [MY-011825] [Xtrabackup] Stopping log copying thread at LSN 109587739
2024-02-02T01:10:59.476534+08:00 1 [Note] [MY-011825] [Xtrabackup] >> log scanned up to (109588473)
2024-02-02T01:11:00.653186+08:00 0 [Note] [MY-011825] [Xtrabackup] Executing UNLOCK INSTANCE
2024-02-02T01:11:00.653733+08:00 0 [Note] [MY-011825] [Xtrabackup] All tables unlocked
2024-02-02T01:11:00.653984+08:00 0 [Note] [MY-011825] [Xtrabackup] Compressing and streaming ib_buffer_pool to <STDOUT>
2024-02-02T01:11:00.654654+08:00 0 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming ib_buffer_pool to <STDOUT>
2024-02-02T01:11:00.654802+08:00 0 [Note] [MY-011825] [Xtrabackup] Backup created in directory '/root/local/backup/'
2024-02-02T01:11:00.654835+08:00 0 [Note] [MY-011825] [Xtrabackup] MySQL binlog position: filename 'bin.000012', position '237', GTID of the last change '662a2811-c03c-11ee-affc-00163e62ca8a:1-25267,6d1f356f-bdeb-11ee-b3aa-00163e62ca8a:1-11'
2024-02-02T01:11:00.654969+08:00 0 [Note] [MY-011825] [Xtrabackup] Compressing and streaming <STDOUT>
2024-02-02T01:11:00.655055+08:00 0 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming file <STDOUT>
2024-02-02T01:11:00.656157+08:00 0 [Note] [MY-011825] [Xtrabackup] Compressing and streaming <STDOUT>
2024-02-02T01:11:00.656283+08:00 0 [Note] [MY-011825] [Xtrabackup] Done: Compressing and streaming file <STDOUT>
2024-02-02T01:11:01.656873+08:00 0 [Note] [MY-011825] [Xtrabackup] Transaction log of lsn (80574725) to (109652268) was copied.
2024-02-02T01:11:02.088189+08:00 0 [Note] [MY-011825] [Xtrabackup] completed OK!


[root@dev backup]# ll
total 19380
-rw-r--r--. 1 root root 19844695 Feb  2 01:11 xb.xbstream.gz
[root@dev backup]#

```

### throttle=8
```sql

[root@dev backup]# /root/local/xtrabackup_8032/bin/xtrabackup --user=root --password=root --host=172.17.137.12 --port=7032 --backup --target-dir=/root/local/backup/  --throttle=8 --stream=xbstream|gzip > xb.xbstream.gz
2024-02-02T01:16:56.831089+08:00 0 [Note] [MY-011825] [Xtrabackup] recognized client arguments: --user=root --password=* --host=172.17.137.12 --port=7032 --backup=1 --target-dir=/root/local/backup/ --throttle=8 --stream=xbstream
/root/local/xtrabackup_8032/bin/xtrabackup version 8.0.32-26 based on MySQL server 8.0.32 Linux (x86_64) (revision id: )
240202 01:16:56  version_check Connecting to MySQL server with DSN 'dbi:mysql:;mysql_read_default_group=xtrabackup;host=172.17.137.12;port=7032' as 'root'  (using password: YES).
Failed to connect to MySQL server as DBD::mysql module is not installed at - line 1548.
2024-02-02T01:16:56.983436+08:00 0 [Note] [MY-011825] [Xtrabackup] Connecting to MySQL server host: 172.17.137.12, user: root, password: set, port: 7032, socket: not set
2024-02-02T01:16:57.017648+08:00 0 [Note] [MY-011825] [Xtrabackup] Using server version 8.0.32-debug
2024-02-02T01:16:57.036801+08:00 0 [Note] [MY-011825] [Xtrabackup] Executing LOCK INSTANCE FOR BACKUP ...
2024-02-02T01:16:57.292719+08:00 0 [Note] [MY-011825] [Xtrabackup] uses posix_fadvise().
2024-02-02T01:16:57.292843+08:00 0 [Note] [MY-011825] [Xtrabackup] cd to /root/local/mysql_7032/data/
2024-02-02T01:16:57.292887+08:00 0 [Note] [MY-011825] [Xtrabackup] open files limit requested 0, set to 1024000
2024-02-02T01:16:57.296618+08:00 0 [Note] [MY-011825] [Xtrabackup] using the following InnoDB configuration:
2024-02-02T01:16:57.296704+08:00 0 [Note] [MY-011825] [Xtrabackup] innodb_data_home_dir = .
2024-02-02T01:16:57.296725+08:00 0 [Note] [MY-011825] [Xtrabackup] innodb_data_file_path = ibdata1:12M:autoextend
2024-02-02T01:16:57.296946+08:00 0 [Note] [MY-011825] [Xtrabackup] innodb_log_group_home_dir = ./
2024-02-02T01:16:57.296976+08:00 0 [Note] [MY-011825] [Xtrabackup] innodb_log_files_in_group = 2
2024-02-02T01:16:57.297003+08:00 0 [Note] [MY-011825] [Xtrabackup] innodb_log_file_size = 1073741824
2024-02-02T01:16:57.310944+08:00 0 [Note] [MY-011825] [Xtrabackup] inititialize_service_handles suceeded
2024-02-02T01:16:58.053435+08:00 0 [Note] [MY-011825] [Xtrabackup] Connecting to MySQL server host: 172.17.137.12, user: root, password: set, port: 7032, socket: not set
2024-02-02T01:16:58.096517+08:00 0 [Note] [MY-011825] [Xtrabackup] Redo Log Archiving is not set up.
2024-02-02T01:16:58.218977+08:00 0 [Note] [MY-011825] [Xtrabackup] Starting to parse redo log at lsn = 113652862
2024-02-02T01:16:58.228688+08:00 0 [Note] [MY-012564] [InnoDB] Recovery parsing buffer extended to 4194304.
2024-02-02T01:16:58.238463+08:00 0 [Note] [MY-012564] [InnoDB] Recovery parsing buffer extended to 8388608.
2024-02-02T01:17:07.061403+08:00 1 [Note] [MY-011825] [Xtrabackup] >> log scanned up to (143301995)
2024-02-02T01:17:07.280778+08:00 0 [Note] [MY-011825] [Xtrabackup] Generating a list of tablespaces
2024-02-02T01:17:07.281216+08:00 0 [Note] [MY-012204] [InnoDB] Scanning './'
2024-02-02T01:17:07.287047+08:00 0 [Note] [MY-012208] [InnoDB] Completed space ID check of 2 files.
2024-02-02T01:17:07.289590+08:00 0 [Warning] [MY-012091] [InnoDB] Allocated tablespace ID 1 for sys/sys_config, old maximum was 0
2024-02-02T01:17:07.295919+08:00 0 [Note] [MY-013252] [InnoDB] Using undo tablespace './undo_001'.
2024-02-02T01:17:07.297059+08:00 0 [Note] [MY-013252] [InnoDB] Using undo tablespace './undo_002'.
2024-02-02T01:17:07.299582+08:00 0 [Note] [MY-012910] [InnoDB] Opened 2 existing undo tablespaces.
2024-02-02T01:17:07.300846+08:00 2 [Note] [MY-011825] [Xtrabackup] Streaming ./ibdata1
2024-02-02T01:17:07.566171+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Streaming ./ibdata1
2024-02-02T01:17:07.589671+08:00 2 [Note] [MY-011825] [Xtrabackup] Streaming ./sys/sys_config.ibd
2024-02-02T01:17:07.590377+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Streaming ./sys/sys_config.ibd
2024-02-02T01:17:07.598804+08:00 2 [Note] [MY-011825] [Xtrabackup] Streaming ./apple/info.ibd
2024-02-02T01:17:07.599922+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Streaming ./apple/info.ibd
2024-02-02T01:17:07.603478+08:00 2 [Note] [MY-011825] [Xtrabackup] Streaming ./apple/sbtest2.ibd
2024-02-02T01:17:07.994714+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Streaming ./apple/sbtest2.ibd
2024-02-02T01:17:07.996681+08:00 2 [Note] [MY-011825] [Xtrabackup] Streaming ./apple/sbtest3.ibd
2024-02-02T01:17:08.068468+08:00 1 [Note] [MY-011825] [Xtrabackup] >> log scanned up to (143354934)
2024-02-02T01:17:08.360180+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Streaming ./apple/sbtest3.ibd
2024-02-02T01:17:08.363525+08:00 2 [Note] [MY-011825] [Xtrabackup] Streaming ./apple/sbtest1.ibd
2024-02-02T01:17:08.871899+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Streaming ./apple/sbtest1.ibd
2024-02-02T01:17:08.873861+08:00 2 [Note] [MY-011825] [Xtrabackup] Streaming ./apple/sbtest4.ibd
2024-02-02T01:17:09.073763+08:00 1 [Note] [MY-011825] [Xtrabackup] >> log scanned up to (143410984)
2024-02-02T01:17:09.241792+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Streaming ./apple/sbtest4.ibd
2024-02-02T01:17:09.244833+08:00 2 [Note] [MY-011825] [Xtrabackup] Streaming ./mysql.ibd
2024-02-02T01:17:09.842762+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Streaming ./mysql.ibd
2024-02-02T01:17:09.902729+08:00 2 [Note] [MY-011825] [Xtrabackup] Streaming ./undo_002
2024-02-02T01:17:10.092851+08:00 1 [Note] [MY-011825] [Xtrabackup] >> log scanned up to (143506390)
2024-02-02T01:17:10.182344+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Streaming ./undo_002
2024-02-02T01:17:10.274350+08:00 2 [Note] [MY-011825] [Xtrabackup] Streaming ./undo_001
2024-02-02T01:17:10.624758+08:00 2 [Note] [MY-011825] [Xtrabackup] Done: Streaming ./undo_001
2024-02-02T01:17:11.105610+08:00 1 [Note] [MY-011825] [Xtrabackup] >> log scanned up to (143609622)
2024-02-02T01:17:11.300758+08:00 0 [Note] [MY-011825] [Xtrabackup] Starting to backup non-InnoDB tables and files
2024-02-02T01:17:11.308481+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming mysql/general_log_213.sdi to <STDOUT>
2024-02-02T01:17:11.308953+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming mysql/general_log_213.sdi to <STDOUT>
2024-02-02T01:17:11.310626+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming mysql/general_log.CSM to <STDOUT>
2024-02-02T01:17:11.310883+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming mysql/general_log.CSM to <STDOUT>
2024-02-02T01:17:11.311880+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming mysql/general_log.CSV to <STDOUT>
2024-02-02T01:17:11.311926+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming mysql/general_log.CSV to <STDOUT>
2024-02-02T01:17:11.312767+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming mysql/slow_log_214.sdi to <STDOUT>
2024-02-02T01:17:11.313068+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming mysql/slow_log_214.sdi to <STDOUT>
2024-02-02T01:17:11.314028+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming mysql/slow_log.CSM to <STDOUT>
2024-02-02T01:17:11.314475+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming mysql/slow_log.CSM to <STDOUT>
2024-02-02T01:17:11.315368+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming mysql/slow_log.CSV to <STDOUT>
2024-02-02T01:17:11.315411+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming mysql/slow_log.CSV to <STDOUT>
2024-02-02T01:17:11.316224+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_statement_123.sdi to <STDOUT>
2024-02-02T01:17:11.317002+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_statement_123.sdi to <STDOUT>
2024-02-02T01:17:11.318001+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_statement_124.sdi to <STDOUT>
2024-02-02T01:17:11.318409+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_statement_124.sdi to <STDOUT>
2024-02-02T01:17:11.319377+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/cond_instances_82.sdi to <STDOUT>
2024-02-02T01:17:11.319755+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/cond_instances_82.sdi to <STDOUT>
2024-02-02T01:17:11.320583+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/error_log_83.sdi to <STDOUT>
2024-02-02T01:17:11.320850+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/error_log_83.sdi to <STDOUT>
2024-02-02T01:17:11.321722+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_waits_cur_84.sdi to <STDOUT>
2024-02-02T01:17:12.157714+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_waits_cur_84.sdi to <STDOUT>
2024-02-02T01:17:12.164225+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_waits_his_85.sdi to <STDOUT>
2024-02-02T01:17:12.164569+08:00 1 [Note] [MY-011825] [Xtrabackup] >> log scanned up to (143706493)
2024-02-02T01:17:12.164813+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_waits_his_85.sdi to <STDOUT>
2024-02-02T01:17:12.166710+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_waits_his_86.sdi to <STDOUT>
2024-02-02T01:17:12.167157+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_waits_his_86.sdi to <STDOUT>
2024-02-02T01:17:12.168260+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_waits_sum_87.sdi to <STDOUT>
2024-02-02T01:17:12.168584+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_waits_sum_87.sdi to <STDOUT>
2024-02-02T01:17:12.169448+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_waits_sum_88.sdi to <STDOUT>
2024-02-02T01:17:12.169820+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_waits_sum_88.sdi to <STDOUT>
2024-02-02T01:17:12.170727+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_waits_sum_89.sdi to <STDOUT>
2024-02-02T01:17:12.171181+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_waits_sum_89.sdi to <STDOUT>
2024-02-02T01:17:12.171991+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_waits_sum_90.sdi to <STDOUT>
2024-02-02T01:17:12.172247+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_waits_sum_90.sdi to <STDOUT>
2024-02-02T01:17:12.173042+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_waits_sum_91.sdi to <STDOUT>
2024-02-02T01:17:12.173251+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_waits_sum_91.sdi to <STDOUT>
2024-02-02T01:17:12.174054+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_waits_sum_92.sdi to <STDOUT>
2024-02-02T01:17:12.174297+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_waits_sum_92.sdi to <STDOUT>
2024-02-02T01:17:12.175103+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/file_instances_93.sdi to <STDOUT>
2024-02-02T01:17:13.157370+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/file_instances_93.sdi to <STDOUT>
2024-02-02T01:17:13.169964+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/file_summary_by__94.sdi to <STDOUT>
2024-02-02T01:17:13.170574+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/file_summary_by__94.sdi to <STDOUT>
2024-02-02T01:17:13.172699+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/file_summary_by__95.sdi to <STDOUT>
2024-02-02T01:17:13.173261+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/file_summary_by__95.sdi to <STDOUT>
2024-02-02T01:17:13.174624+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/host_cache_96.sdi to <STDOUT>
2024-02-02T01:17:13.175186+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/host_cache_96.sdi to <STDOUT>
2024-02-02T01:17:13.176152+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/mutex_instances_97.sdi to <STDOUT>
2024-02-02T01:17:13.176757+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/mutex_instances_97.sdi to <STDOUT>
2024-02-02T01:17:13.177658+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/objects_summary__98.sdi to <STDOUT>
2024-02-02T01:17:13.178034+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/objects_summary__98.sdi to <STDOUT>
2024-02-02T01:17:13.178056+08:00 1 [Note] [MY-011825] [Xtrabackup] >> log scanned up to (143824884)
2024-02-02T01:17:13.178905+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/performance_time_99.sdi to <STDOUT>
2024-02-02T01:17:13.179253+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/performance_time_99.sdi to <STDOUT>
2024-02-02T01:17:13.180147+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/processlist_100.sdi to <STDOUT>
2024-02-02T01:17:13.180463+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/processlist_100.sdi to <STDOUT>
2024-02-02T01:17:13.181365+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/rwlock_instances_101.sdi to <STDOUT>
2024-02-02T01:17:14.157339+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/rwlock_instances_101.sdi to <STDOUT>
2024-02-02T01:17:14.162363+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/setup_actors_102.sdi to <STDOUT>
2024-02-02T01:17:14.162797+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/setup_actors_102.sdi to <STDOUT>
2024-02-02T01:17:14.164585+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/setup_consumers_103.sdi to <STDOUT>
2024-02-02T01:17:14.164913+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/setup_consumers_103.sdi to <STDOUT>
2024-02-02T01:17:14.165960+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/setup_instrument_104.sdi to <STDOUT>
2024-02-02T01:17:14.166214+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/setup_instrument_104.sdi to <STDOUT>
2024-02-02T01:17:14.167205+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/setup_objects_105.sdi to <STDOUT>
2024-02-02T01:17:14.167451+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/setup_objects_105.sdi to <STDOUT>
2024-02-02T01:17:14.168374+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/setup_threads_106.sdi to <STDOUT>
2024-02-02T01:17:14.168694+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/setup_threads_106.sdi to <STDOUT>
2024-02-02T01:17:14.169610+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/table_io_waits_s_107.sdi to <STDOUT>
2024-02-02T01:17:14.170242+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/table_io_waits_s_107.sdi to <STDOUT>
2024-02-02T01:17:14.171280+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/table_io_waits_s_108.sdi to <STDOUT>
2024-02-02T01:17:14.172099+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/table_io_waits_s_108.sdi to <STDOUT>
2024-02-02T01:17:14.173092+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/table_lock_waits_109.sdi to <STDOUT>
2024-02-02T01:17:14.173440+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/table_lock_waits_109.sdi to <STDOUT>
2024-02-02T01:17:14.174577+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/threads_110.sdi to <STDOUT>
2024-02-02T01:17:15.157510+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/threads_110.sdi to <STDOUT>
2024-02-02T01:17:15.162633+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_stages_cu_111.sdi to <STDOUT>
2024-02-02T01:17:15.163812+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_stages_cu_111.sdi to <STDOUT>
2024-02-02T01:17:15.165896+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_stages_hi_112.sdi to <STDOUT>
2024-02-02T01:17:15.166636+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_stages_hi_112.sdi to <STDOUT>
2024-02-02T01:17:15.168105+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_stages_hi_113.sdi to <STDOUT>
2024-02-02T01:17:15.168405+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_stages_hi_113.sdi to <STDOUT>
2024-02-02T01:17:15.169650+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_stages_su_114.sdi to <STDOUT>
2024-02-02T01:17:15.170027+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_stages_su_114.sdi to <STDOUT>
2024-02-02T01:17:15.171153+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_stages_su_115.sdi to <STDOUT>
2024-02-02T01:17:15.171401+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_stages_su_115.sdi to <STDOUT>
2024-02-02T01:17:15.172461+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_stages_su_116.sdi to <STDOUT>
2024-02-02T01:17:15.172819+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_stages_su_116.sdi to <STDOUT>
2024-02-02T01:17:15.173901+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_stages_su_117.sdi to <STDOUT>
2024-02-02T01:17:15.174154+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_stages_su_117.sdi to <STDOUT>
2024-02-02T01:17:15.174993+08:00 1 [Note] [MY-011825] [Xtrabackup] >> log scanned up to (143958274)
2024-02-02T01:17:15.175209+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_stages_su_118.sdi to <STDOUT>
2024-02-02T01:17:15.175554+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_stages_su_118.sdi to <STDOUT>
2024-02-02T01:17:15.176651+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_statement_119.sdi to <STDOUT>
2024-02-02T01:17:16.157470+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_statement_119.sdi to <STDOUT>
2024-02-02T01:17:16.161503+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_statement_120.sdi to <STDOUT>
2024-02-02T01:17:16.162004+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_statement_120.sdi to <STDOUT>
2024-02-02T01:17:16.164279+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_statement_121.sdi to <STDOUT>
2024-02-02T01:17:16.164674+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_statement_121.sdi to <STDOUT>
2024-02-02T01:17:16.166147+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_statement_122.sdi to <STDOUT>
2024-02-02T01:17:16.166644+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_statement_122.sdi to <STDOUT>
2024-02-02T01:17:16.167686+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_statement_125.sdi to <STDOUT>
2024-02-02T01:17:16.168116+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_statement_125.sdi to <STDOUT>
2024-02-02T01:17:16.169073+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_statement_126.sdi to <STDOUT>
2024-02-02T01:17:16.169374+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_statement_126.sdi to <STDOUT>
2024-02-02T01:17:16.176945+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_statement_127.sdi to <STDOUT>
2024-02-02T01:17:16.177197+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_statement_127.sdi to <STDOUT>
2024-02-02T01:17:16.178192+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_statement_128.sdi to <STDOUT>
2024-02-02T01:17:16.178519+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_statement_128.sdi to <STDOUT>
2024-02-02T01:17:16.179407+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_statement_129.sdi to <STDOUT>
2024-02-02T01:17:16.182749+08:00 1 [Note] [MY-011825] [Xtrabackup] >> log scanned up to (144049874)
2024-02-02T01:17:17.157540+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_statement_129.sdi to <STDOUT>
2024-02-02T01:17:17.160631+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_statement_130.sdi to <STDOUT>
2024-02-02T01:17:17.160851+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_statement_130.sdi to <STDOUT>
2024-02-02T01:17:17.162242+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_transacti_131.sdi to <STDOUT>
2024-02-02T01:17:17.162414+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_transacti_131.sdi to <STDOUT>
2024-02-02T01:17:17.163222+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_transacti_132.sdi to <STDOUT>
2024-02-02T01:17:17.163409+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_transacti_132.sdi to <STDOUT>
2024-02-02T01:17:17.164121+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_transacti_133.sdi to <STDOUT>
2024-02-02T01:17:17.164286+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_transacti_133.sdi to <STDOUT>
2024-02-02T01:17:17.164998+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_transacti_134.sdi to <STDOUT>
2024-02-02T01:17:17.165143+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_transacti_134.sdi to <STDOUT>
2024-02-02T01:17:17.165782+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_transacti_135.sdi to <STDOUT>
2024-02-02T01:17:17.165926+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_transacti_135.sdi to <STDOUT>
2024-02-02T01:17:17.166596+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_transacti_136.sdi to <STDOUT>
2024-02-02T01:17:17.166772+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_transacti_136.sdi to <STDOUT>
2024-02-02T01:17:17.167435+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_transacti_137.sdi to <STDOUT>
2024-02-02T01:17:17.167591+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_transacti_137.sdi to <STDOUT>
2024-02-02T01:17:17.168241+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_transacti_138.sdi to <STDOUT>
2024-02-02T01:17:18.157824+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_transacti_138.sdi to <STDOUT>
2024-02-02T01:17:18.161091+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_errors_su_139.sdi to <STDOUT>
2024-02-02T01:17:18.161604+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_errors_su_139.sdi to <STDOUT>
2024-02-02T01:17:18.163395+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_errors_su_140.sdi to <STDOUT>
2024-02-02T01:17:18.163837+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_errors_su_140.sdi to <STDOUT>
2024-02-02T01:17:18.164921+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_errors_su_141.sdi to <STDOUT>
2024-02-02T01:17:18.165268+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_errors_su_141.sdi to <STDOUT>
2024-02-02T01:17:18.166241+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_errors_su_142.sdi to <STDOUT>
2024-02-02T01:17:18.166618+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_errors_su_142.sdi to <STDOUT>
2024-02-02T01:17:18.167584+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/events_errors_su_143.sdi to <STDOUT>
2024-02-02T01:17:18.167862+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/events_errors_su_143.sdi to <STDOUT>
2024-02-02T01:17:18.168792+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/users_144.sdi to <STDOUT>
2024-02-02T01:17:18.169043+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/users_144.sdi to <STDOUT>
2024-02-02T01:17:18.169920+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/accounts_145.sdi to <STDOUT>
2024-02-02T01:17:18.170206+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/accounts_145.sdi to <STDOUT>
2024-02-02T01:17:18.171047+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/hosts_146.sdi to <STDOUT>
2024-02-02T01:17:18.171333+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/hosts_146.sdi to <STDOUT>
2024-02-02T01:17:18.172169+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/socket_instances_147.sdi to <STDOUT>
2024-02-02T01:17:18.190454+08:00 1 [Note] [MY-011825] [Xtrabackup] >> log scanned up to (144233164)
2024-02-02T01:17:19.157971+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/socket_instances_147.sdi to <STDOUT>
2024-02-02T01:17:19.161911+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/socket_summary_b_148.sdi to <STDOUT>
2024-02-02T01:17:19.162409+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/socket_summary_b_148.sdi to <STDOUT>
2024-02-02T01:17:19.164178+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/socket_summary_b_149.sdi to <STDOUT>
2024-02-02T01:17:19.164525+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/socket_summary_b_149.sdi to <STDOUT>
2024-02-02T01:17:19.165726+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/session_connect__150.sdi to <STDOUT>
2024-02-02T01:17:19.166113+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/session_connect__150.sdi to <STDOUT>
2024-02-02T01:17:19.167116+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/session_account__151.sdi to <STDOUT>
2024-02-02T01:17:19.167356+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/session_account__151.sdi to <STDOUT>
2024-02-02T01:17:19.168339+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/keyring_keys_152.sdi to <STDOUT>
2024-02-02T01:17:19.168562+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/keyring_keys_152.sdi to <STDOUT>
2024-02-02T01:17:19.169524+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/memory_summary_g_153.sdi to <STDOUT>
2024-02-02T01:17:19.169735+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/memory_summary_g_153.sdi to <STDOUT>
2024-02-02T01:17:19.170683+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/memory_summary_b_154.sdi to <STDOUT>
2024-02-02T01:17:19.170889+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/memory_summary_b_154.sdi to <STDOUT>
2024-02-02T01:17:19.171841+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/memory_summary_b_155.sdi to <STDOUT>
2024-02-02T01:17:19.172035+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/memory_summary_b_155.sdi to <STDOUT>
2024-02-02T01:17:19.172900+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/memory_summary_b_156.sdi to <STDOUT>
2024-02-02T01:17:20.158085+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/memory_summary_b_156.sdi to <STDOUT>
2024-02-02T01:17:20.161062+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/memory_summary_b_157.sdi to <STDOUT>
2024-02-02T01:17:20.161434+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/memory_summary_b_157.sdi to <STDOUT>
2024-02-02T01:17:20.163279+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/table_handles_158.sdi to <STDOUT>
2024-02-02T01:17:20.163513+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/table_handles_158.sdi to <STDOUT>
2024-02-02T01:17:20.163868+08:00 1 [Note] [MY-011825] [Xtrabackup] >> log scanned up to (144344232)
2024-02-02T01:17:20.164704+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/metadata_locks_159.sdi to <STDOUT>
2024-02-02T01:17:20.164913+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/metadata_locks_159.sdi to <STDOUT>
2024-02-02T01:17:20.165935+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/data_locks_160.sdi to <STDOUT>
2024-02-02T01:17:20.166252+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/data_locks_160.sdi to <STDOUT>
2024-02-02T01:17:20.167365+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/data_lock_waits_161.sdi to <STDOUT>
2024-02-02T01:17:20.167649+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/data_lock_waits_161.sdi to <STDOUT>
2024-02-02T01:17:20.169154+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/replication_conn_162.sdi to <STDOUT>
2024-02-02T01:17:20.169664+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/replication_conn_162.sdi to <STDOUT>
2024-02-02T01:17:20.171839+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/replication_grou_163.sdi to <STDOUT>
2024-02-02T01:17:20.172169+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/replication_grou_163.sdi to <STDOUT>
2024-02-02T01:17:20.173573+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/replication_conn_164.sdi to <STDOUT>
2024-02-02T01:17:20.173850+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/replication_conn_164.sdi to <STDOUT>
2024-02-02T01:17:20.175228+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/replication_appl_165.sdi to <STDOUT>
2024-02-02T01:17:21.158230+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/replication_appl_165.sdi to <STDOUT>
2024-02-02T01:17:21.160518+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/replication_appl_166.sdi to <STDOUT>
2024-02-02T01:17:21.160971+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/replication_appl_166.sdi to <STDOUT>
2024-02-02T01:17:21.162164+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/replication_appl_167.sdi to <STDOUT>
2024-02-02T01:17:21.162430+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/replication_appl_167.sdi to <STDOUT>
2024-02-02T01:17:21.163145+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/replication_appl_168.sdi to <STDOUT>
2024-02-02T01:17:21.163426+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/replication_appl_168.sdi to <STDOUT>
2024-02-02T01:17:21.164114+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/replication_grou_169.sdi to <STDOUT>
2024-02-02T01:17:21.164306+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/replication_grou_169.sdi to <STDOUT>
2024-02-02T01:17:21.164914+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/replication_appl_170.sdi to <STDOUT>
2024-02-02T01:17:21.165163+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/replication_appl_170.sdi to <STDOUT>
2024-02-02T01:17:21.165760+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/replication_appl_171.sdi to <STDOUT>
2024-02-02T01:17:21.165920+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/replication_appl_171.sdi to <STDOUT>
2024-02-02T01:17:21.166522+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/replication_asyn_172.sdi to <STDOUT>
2024-02-02T01:17:21.166688+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/replication_asyn_172.sdi to <STDOUT>
2024-02-02T01:17:21.167279+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/replication_asyn_173.sdi to <STDOUT>
2024-02-02T01:17:21.184420+08:00 1 [Note] [MY-011825] [Xtrabackup] >> log scanned up to (144476041)
2024-02-02T01:17:22.158289+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/replication_asyn_173.sdi to <STDOUT>
2024-02-02T01:17:22.161602+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/log_status_174.sdi to <STDOUT>
2024-02-02T01:17:22.162103+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/log_status_174.sdi to <STDOUT>
2024-02-02T01:17:22.163842+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/prepared_stateme_175.sdi to <STDOUT>
2024-02-02T01:17:22.164305+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/prepared_stateme_175.sdi to <STDOUT>
2024-02-02T01:17:22.165716+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/user_variables_b_176.sdi to <STDOUT>
2024-02-02T01:17:22.165948+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/user_variables_b_176.sdi to <STDOUT>
2024-02-02T01:17:22.166914+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/status_by_accoun_177.sdi to <STDOUT>
2024-02-02T01:17:22.167093+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/status_by_accoun_177.sdi to <STDOUT>
2024-02-02T01:17:22.167929+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/status_by_host_178.sdi to <STDOUT>
2024-02-02T01:17:22.168099+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/status_by_host_178.sdi to <STDOUT>
2024-02-02T01:17:22.168895+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/status_by_thread_179.sdi to <STDOUT>
2024-02-02T01:17:22.169056+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/status_by_thread_179.sdi to <STDOUT>
2024-02-02T01:17:22.169850+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/status_by_user_180.sdi to <STDOUT>
2024-02-02T01:17:22.170013+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/status_by_user_180.sdi to <STDOUT>
2024-02-02T01:17:22.170809+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/global_status_181.sdi to <STDOUT>
2024-02-02T01:17:22.170973+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/global_status_181.sdi to <STDOUT>
2024-02-02T01:17:22.171759+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/session_status_182.sdi to <STDOUT>
2024-02-02T01:17:23.158438+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/session_status_182.sdi to <STDOUT>
2024-02-02T01:17:23.161377+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/variables_by_thr_183.sdi to <STDOUT>
2024-02-02T01:17:23.161803+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/variables_by_thr_183.sdi to <STDOUT>
2024-02-02T01:17:23.163355+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/global_variables_184.sdi to <STDOUT>
2024-02-02T01:17:23.163801+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/global_variables_184.sdi to <STDOUT>
2024-02-02T01:17:23.164763+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/session_variable_185.sdi to <STDOUT>
2024-02-02T01:17:23.164964+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/session_variable_185.sdi to <STDOUT>
2024-02-02T01:17:23.165763+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/variables_info_186.sdi to <STDOUT>
2024-02-02T01:17:23.165931+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/variables_info_186.sdi to <STDOUT>
2024-02-02T01:17:23.166588+08:00 1 [Note] [MY-011825] [Xtrabackup] >> log scanned up to (144587831)
2024-02-02T01:17:23.166624+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/persisted_variab_187.sdi to <STDOUT>
2024-02-02T01:17:23.167108+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/persisted_variab_187.sdi to <STDOUT>
2024-02-02T01:17:23.167799+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/user_defined_fun_188.sdi to <STDOUT>
2024-02-02T01:17:23.168013+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/user_defined_fun_188.sdi to <STDOUT>
2024-02-02T01:17:23.168708+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/binary_log_trans_189.sdi to <STDOUT>
2024-02-02T01:17:23.168906+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/binary_log_trans_189.sdi to <STDOUT>
2024-02-02T01:17:23.169539+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/tls_channel_stat_190.sdi to <STDOUT>
2024-02-02T01:17:23.169903+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/tls_channel_stat_190.sdi to <STDOUT>
2024-02-02T01:17:23.170657+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming performance_schema/keyring_componen_191.sdi to <STDOUT>
2024-02-02T01:17:24.158749+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming performance_schema/keyring_componen_191.sdi to <STDOUT>
2024-02-02T01:17:24.164691+08:00 3 [Note] [MY-011825] [Xtrabackup] Streaming <STDOUT>
2024-02-02T01:17:24.164745+08:00 3 [Note] [MY-011825] [Xtrabackup] Done: Streaming file <STDOUT>
2024-02-02T01:17:24.175893+08:00 1 [Note] [MY-011825] [Xtrabackup] >> log scanned up to (144669163)
2024-02-02T01:17:24.226873+08:00 0 [Note] [MY-011825] [Xtrabackup] Finished backing up non-InnoDB tables and files
2024-02-02T01:17:24.227042+08:00 0 [Note] [MY-011825] [Xtrabackup] Executing FLUSH NO_WRITE_TO_BINLOG BINARY LOGS
2024-02-02T01:17:24.259183+08:00 0 [Note] [MY-011825] [Xtrabackup] Selecting LSN and binary log position from p_s.log_status
2024-02-02T01:17:24.301807+08:00 0 [Note] [MY-011825] [Xtrabackup] Streaming /root/local/mysql_7032/log/bin.000013 to <STDOUT> up to position 1978
2024-02-02T01:17:24.301899+08:00 0 [Note] [MY-011825] [Xtrabackup] Done: Streaming /root/local/mysql_7032/log/bin.000013 to <STDOUT>
2024-02-02T01:17:24.302285+08:00 0 [Note] [MY-011825] [Xtrabackup] Streaming <STDOUT>
2024-02-02T01:17:24.302350+08:00 0 [Note] [MY-011825] [Xtrabackup] Done: Streaming file <STDOUT>
2024-02-02T01:17:24.339756+08:00 0 [Note] [MY-011825] [Xtrabackup] Streaming <STDOUT>
2024-02-02T01:17:24.339836+08:00 0 [Note] [MY-011825] [Xtrabackup] Done: Streaming file <STDOUT>
2024-02-02T01:17:24.339897+08:00 0 [Note] [MY-011825] [Xtrabackup] Executing FLUSH NO_WRITE_TO_BINLOG ENGINE LOGS...
2024-02-02T01:17:24.404844+08:00 0 [Note] [MY-011825] [Xtrabackup] The latest check point (for incremental): '115922086'
2024-02-02T01:17:24.404994+08:00 0 [Note] [MY-011825] [Xtrabackup] Stopping log copying thread at LSN 144682291
2024-02-02T01:17:24.407020+08:00 1 [Note] [MY-011825] [Xtrabackup] >> log scanned up to (144685879)
2024-02-02T01:17:25.592198+08:00 0 [Note] [MY-011825] [Xtrabackup] Executing UNLOCK INSTANCE
2024-02-02T01:17:25.593034+08:00 0 [Note] [MY-011825] [Xtrabackup] All tables unlocked
2024-02-02T01:17:25.593576+08:00 0 [Note] [MY-011825] [Xtrabackup] Streaming ib_buffer_pool to <STDOUT>
2024-02-02T01:17:25.594228+08:00 0 [Note] [MY-011825] [Xtrabackup] Done: Streaming ib_buffer_pool to <STDOUT>
2024-02-02T01:17:25.595718+08:00 0 [Note] [MY-011825] [Xtrabackup] Backup created in directory '/root/local/backup/'
2024-02-02T01:17:25.595773+08:00 0 [Note] [MY-011825] [Xtrabackup] MySQL binlog position: filename 'bin.000013', position '1978', GTID of the last change '662a2811-c03c-11ee-affc-00163e62ca8a:1-39225,6d1f356f-bdeb-11ee-b3aa-00163e62ca8a:1-11'
2024-02-02T01:17:25.595969+08:00 0 [Note] [MY-011825] [Xtrabackup] Streaming <STDOUT>
2024-02-02T01:17:25.596004+08:00 0 [Note] [MY-011825] [Xtrabackup] Done: Streaming file <STDOUT>
2024-02-02T01:17:25.616600+08:00 0 [Note] [MY-011825] [Xtrabackup] Streaming <STDOUT>
2024-02-02T01:17:25.616659+08:00 0 [Note] [MY-011825] [Xtrabackup] Done: Streaming file <STDOUT>
2024-02-02T01:17:26.618540+08:00 0 [Note] [MY-011825] [Xtrabackup] Transaction log of lsn (113652852) to (144769071) was copied.
2024-02-02T01:17:27.184149+08:00 0 [Note] [MY-011825] [Xtrabackup] completed OK!
[root@dev backup]#


```
















