---
title: threads
description: threads
date: 2023-10-26
weight: 20000


---

{{< alert >}}
MySQL
{{< /alert >}}


### threads


```sql
[admin@127.0.0.1:3306 22:39:40(performance_schema)]$ show create table threads\G
*************************** 1. row ***************************
       Table: threads
Create Table: CREATE TABLE `threads` (
  `THREAD_ID` bigint unsigned NOT NULL,
  `NAME` varchar(128) NOT NULL,
  `TYPE` varchar(10) NOT NULL,
  `PROCESSLIST_ID` bigint unsigned DEFAULT NULL,
  `PROCESSLIST_USER` varchar(32) DEFAULT NULL,
  `PROCESSLIST_HOST` varchar(255) CHARACTER SET ascii COLLATE ascii_general_ci DEFAULT NULL,
  `PROCESSLIST_DB` varchar(64) DEFAULT NULL,
  `PROCESSLIST_COMMAND` varchar(16) DEFAULT NULL,
  `PROCESSLIST_TIME` bigint DEFAULT NULL,
  `PROCESSLIST_STATE` varchar(64) DEFAULT NULL,
  `PROCESSLIST_INFO` longtext,
  `PARENT_THREAD_ID` bigint unsigned DEFAULT NULL,
  `ROLE` varchar(64) DEFAULT NULL,
  `INSTRUMENTED` enum('YES','NO') NOT NULL,
  `HISTORY` enum('YES','NO') NOT NULL,
  `CONNECTION_TYPE` varchar(16) DEFAULT NULL,
  `THREAD_OS_ID` bigint unsigned DEFAULT NULL,
  `RESOURCE_GROUP` varchar(64) DEFAULT NULL,
  `EXECUTION_ENGINE` enum('PRIMARY','SECONDARY') DEFAULT NULL,
  `CONTROLLED_MEMORY` bigint unsigned NOT NULL,
  `MAX_CONTROLLED_MEMORY` bigint unsigned NOT NULL,
  `TOTAL_MEMORY` bigint unsigned NOT NULL,
  `MAX_TOTAL_MEMORY` bigint unsigned NOT NULL,
  PRIMARY KEY (`THREAD_ID`),
  KEY `PROCESSLIST_ID` (`PROCESSLIST_ID`),
  KEY `THREAD_OS_ID` (`THREAD_OS_ID`),
  KEY `NAME` (`NAME`),
  KEY `PROCESSLIST_ACCOUNT` (`PROCESSLIST_USER`,`PROCESSLIST_HOST`),
  KEY `PROCESSLIST_HOST` (`PROCESSLIST_HOST`),
  KEY `RESOURCE_GROUP` (`RESOURCE_GROUP`)
) ENGINE=PERFORMANCE_SCHEMA DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)

```




```sql
[admin@127.0.0.1:3306 21:57:01(performance_schema)]$ select  THREAD_ID,NAME,TYPE,PROCESSLIST_ID from threads;
+-----------+---------------------------------------------+------------+----------------+
| THREAD_ID | NAME                                        | TYPE       | PROCESSLIST_ID |
+-----------+---------------------------------------------+------------+----------------+
|         1 | thread/sql/main                             | BACKGROUND |           NULL |
|         3 | thread/innodb/io_ibuf_thread                | BACKGROUND |           NULL |
|         4 | thread/innodb/io_log_thread                 | BACKGROUND |           NULL |
|         5 | thread/innodb/io_read_thread                | BACKGROUND |           NULL |
|         6 | thread/innodb/io_write_thread               | BACKGROUND |           NULL |
|         7 | thread/innodb/page_flush_coordinator_thread | BACKGROUND |           NULL |
|         8 | thread/innodb/page_flush_thread             | BACKGROUND |           NULL |
|         9 | thread/innodb/page_flush_thread             | BACKGROUND |           NULL |
|        10 | thread/innodb/page_flush_thread             | BACKGROUND |           NULL |
|        11 | thread/innodb/page_flush_thread             | BACKGROUND |           NULL |
|        12 | thread/innodb/page_flush_thread             | BACKGROUND |           NULL |
|        13 | thread/innodb/page_flush_thread             | BACKGROUND |           NULL |
|        14 | thread/innodb/page_flush_thread             | BACKGROUND |           NULL |
|        16 | thread/innodb/log_checkpointer_thread       | BACKGROUND |           NULL |
|        17 | thread/innodb/log_flush_notifier_thread     | BACKGROUND |           NULL |
|        18 | thread/innodb/log_flusher_thread            | BACKGROUND |           NULL |
|        19 | thread/innodb/log_write_notifier_thread     | BACKGROUND |           NULL |
|        20 | thread/innodb/log_writer_thread             | BACKGROUND |           NULL |
|        21 | thread/innodb/log_files_governor_thread     | BACKGROUND |           NULL |
|        26 | thread/innodb/srv_lock_timeout_thread       | BACKGROUND |           NULL |
|        27 | thread/innodb/srv_error_monitor_thread      | BACKGROUND |           NULL |
|        28 | thread/innodb/srv_monitor_thread            | BACKGROUND |           NULL |
|        29 | thread/innodb/buf_resize_thread             | BACKGROUND |           NULL |
|        30 | thread/innodb/srv_master_thread             | BACKGROUND |           NULL |
|        31 | thread/innodb/dict_stats_thread             | BACKGROUND |           NULL |
|        32 | thread/innodb/fts_optimize_thread           | BACKGROUND |           NULL |
|        36 | thread/innodb/buf_dump_thread               | BACKGROUND |           NULL |
|        37 | thread/innodb/clone_gtid_thread             | BACKGROUND |           NULL |
|        38 | thread/innodb/srv_purge_thread              | BACKGROUND |           NULL |
|        39 | thread/innodb/srv_worker_thread             | BACKGROUND |           NULL |
|        40 | thread/innodb/srv_worker_thread             | BACKGROUND |           NULL |
|        41 | thread/innodb/srv_worker_thread             | BACKGROUND |           NULL |
|        42 | thread/sql/signal_handler                   | BACKGROUND |           NULL |
|        43 | thread/sql/compress_gtid_table              | FOREGROUND |              7 |
|        44 | thread/sql/event_scheduler                  | FOREGROUND |              5 |
|        47 | thread/sql/one_connection                   | FOREGROUND |             10 |
+-----------+---------------------------------------------+------------+----------------+
36 rows in set (0.00 sec)

```













