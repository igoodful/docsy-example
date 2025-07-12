---
title: events_statements_history
description: events_statements_history
date: 2023-10-26
weight: 20000


---

{{< alert >}}
MySQL
{{< /alert >}}


### 表结构：events_statements_history

```sql
mysql> show create table performance_schema.events_statements_history\G
*************************** 1. row ***************************
       Table: events_statements_history
Create Table: CREATE TABLE `events_statements_history` (
  `THREAD_ID` bigint unsigned NOT NULL,
  `EVENT_ID` bigint unsigned NOT NULL,
  `END_EVENT_ID` bigint unsigned DEFAULT NULL,
  `EVENT_NAME` varchar(128) NOT NULL,
  `SOURCE` varchar(64) DEFAULT NULL,
  `TIMER_START` bigint unsigned DEFAULT NULL,
  `TIMER_END` bigint unsigned DEFAULT NULL,
  `TIMER_WAIT` bigint unsigned DEFAULT NULL,
  `LOCK_TIME` bigint unsigned NOT NULL,
  `SQL_TEXT` longtext,
  `DIGEST` varchar(64) DEFAULT NULL,
  `DIGEST_TEXT` longtext,
  `CURRENT_SCHEMA` varchar(64) DEFAULT NULL,
  `OBJECT_TYPE` varchar(64) DEFAULT NULL,
  `OBJECT_SCHEMA` varchar(64) DEFAULT NULL,
  `OBJECT_NAME` varchar(64) DEFAULT NULL,
  `OBJECT_INSTANCE_BEGIN` bigint unsigned DEFAULT NULL,
  `MYSQL_ERRNO` int DEFAULT NULL,
  `RETURNED_SQLSTATE` varchar(5) DEFAULT NULL,
  `MESSAGE_TEXT` varchar(128) DEFAULT NULL,
  `ERRORS` bigint unsigned NOT NULL,
  `WARNINGS` bigint unsigned NOT NULL,
  `ROWS_AFFECTED` bigint unsigned NOT NULL,
  `ROWS_SENT` bigint unsigned NOT NULL,
  `ROWS_EXAMINED` bigint unsigned NOT NULL,
  `CREATED_TMP_DISK_TABLES` bigint unsigned NOT NULL,
  `CREATED_TMP_TABLES` bigint unsigned NOT NULL,
  `SELECT_FULL_JOIN` bigint unsigned NOT NULL,
  `SELECT_FULL_RANGE_JOIN` bigint unsigned NOT NULL,
  `SELECT_RANGE` bigint unsigned NOT NULL,
  `SELECT_RANGE_CHECK` bigint unsigned NOT NULL,
  `SELECT_SCAN` bigint unsigned NOT NULL,
  `SORT_MERGE_PASSES` bigint unsigned NOT NULL,
  `SORT_RANGE` bigint unsigned NOT NULL,
  `SORT_ROWS` bigint unsigned NOT NULL,
  `SORT_SCAN` bigint unsigned NOT NULL,
  `NO_INDEX_USED` bigint unsigned NOT NULL,
  `NO_GOOD_INDEX_USED` bigint unsigned NOT NULL,
  `NESTING_EVENT_ID` bigint unsigned DEFAULT NULL,
  `NESTING_EVENT_TYPE` enum('TRANSACTION','STATEMENT','STAGE','WAIT') DEFAULT NULL,
  `NESTING_EVENT_LEVEL` int DEFAULT NULL,
  `STATEMENT_ID` bigint unsigned DEFAULT NULL,
  PRIMARY KEY (`THREAD_ID`,`EVENT_ID`)
) ENGINE=PERFORMANCE_SCHEMA DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)

```





### 案例

### 根据data_locks获取：THREAD_ID
```sql
mysql> select * from performance_schema.data_locks\G
*************************** 1. row ***************************
               ENGINE: INNODB
       ENGINE_LOCK_ID: 140603514388480:1686:140603404927600
ENGINE_TRANSACTION_ID: 3256133
            THREAD_ID: 60
             EVENT_ID: 7612
        OBJECT_SCHEMA: apple
          OBJECT_NAME: user
       PARTITION_NAME: NULL
    SUBPARTITION_NAME: NULL
           INDEX_NAME: NULL
OBJECT_INSTANCE_BEGIN: 140603404927600
            LOCK_TYPE: TABLE
            LOCK_MODE: IX
          LOCK_STATUS: GRANTED
            LOCK_DATA: NULL
*************************** 2. row ***************************
               ENGINE: INNODB
       ENGINE_LOCK_ID: 140603514387672:1686:140603404921456
ENGINE_TRANSACTION_ID: 3256128
            THREAD_ID: 62
             EVENT_ID: 240
        OBJECT_SCHEMA: apple
          OBJECT_NAME: user
       PARTITION_NAME: NULL
    SUBPARTITION_NAME: NULL
           INDEX_NAME: NULL
OBJECT_INSTANCE_BEGIN: 140603404921456
            LOCK_TYPE: TABLE
            LOCK_MODE: IX
          LOCK_STATUS: GRANTED
            LOCK_DATA: NULL
*************************** 3. row ***************************
               ENGINE: INNODB
       ENGINE_LOCK_ID: 140603514387672:625:4:10:140603404918352
ENGINE_TRANSACTION_ID: 3256128
            THREAD_ID: 62
             EVENT_ID: 245
        OBJECT_SCHEMA: apple
          OBJECT_NAME: user
       PARTITION_NAME: NULL
    SUBPARTITION_NAME: NULL
           INDEX_NAME: PRIMARY
OBJECT_INSTANCE_BEGIN: 140603404918352
            LOCK_TYPE: RECORD
            LOCK_MODE: X,REC_NOT_GAP
          LOCK_STATUS: GRANTED
            LOCK_DATA: 1
*************************** 4. row ***************************
               ENGINE: INNODB
       ENGINE_LOCK_ID: 140603514387672:625:4:11:140603404918352
ENGINE_TRANSACTION_ID: 3256128
            THREAD_ID: 62
             EVENT_ID: 245
        OBJECT_SCHEMA: apple
          OBJECT_NAME: user
       PARTITION_NAME: NULL
    SUBPARTITION_NAME: NULL
           INDEX_NAME: PRIMARY
OBJECT_INSTANCE_BEGIN: 140603404918352
            LOCK_TYPE: RECORD
            LOCK_MODE: X,REC_NOT_GAP
          LOCK_STATUS: GRANTED
            LOCK_DATA: 2
*************************** 5. row ***************************
               ENGINE: INNODB
       ENGINE_LOCK_ID: 140603514387672:625:4:12:140603404918352
ENGINE_TRANSACTION_ID: 3256128
            THREAD_ID: 62
             EVENT_ID: 245
        OBJECT_SCHEMA: apple
          OBJECT_NAME: user
       PARTITION_NAME: NULL
    SUBPARTITION_NAME: NULL
           INDEX_NAME: PRIMARY
OBJECT_INSTANCE_BEGIN: 140603404918352
            LOCK_TYPE: RECORD
            LOCK_MODE: X,REC_NOT_GAP
          LOCK_STATUS: GRANTED
            LOCK_DATA: 4
5 rows in set (0.00 sec)


```


### 根据THREAD_ID获取events_statements_history中执行的历史SQL

```sql
mysql> select * from performance_schema.events_statements_history where thread_id=62 order by event_id\G
*************************** 1. row ***************************
              THREAD_ID: 62
               EVENT_ID: 26
           END_EVENT_ID: 49
             EVENT_NAME: statement/sql/select
                 SOURCE: init_net_server_extension.cc:96
            TIMER_START: 447875592288186000
              TIMER_END: 447875592458581000
             TIMER_WAIT: 170395000
              LOCK_TIME: 0
               SQL_TEXT: select @@version_comment limit 1
                 DIGEST: 44e35cee979ba420eb49a8471f852bbe15b403c89742704817dfbaace0d99dbb
            DIGEST_TEXT: SELECT @@`version_comment` LIMIT ?
         CURRENT_SCHEMA: NULL
            OBJECT_TYPE: NULL
          OBJECT_SCHEMA: NULL
            OBJECT_NAME: NULL
  OBJECT_INSTANCE_BEGIN: NULL
            MYSQL_ERRNO: 0
      RETURNED_SQLSTATE: NULL
           MESSAGE_TEXT: NULL
                 ERRORS: 0
               WARNINGS: 0
          ROWS_AFFECTED: 0
              ROWS_SENT: 1
          ROWS_EXAMINED: 1
CREATED_TMP_DISK_TABLES: 0
     CREATED_TMP_TABLES: 0
       SELECT_FULL_JOIN: 0
 SELECT_FULL_RANGE_JOIN: 0
           SELECT_RANGE: 0
     SELECT_RANGE_CHECK: 0
            SELECT_SCAN: 0
      SORT_MERGE_PASSES: 0
             SORT_RANGE: 0
              SORT_ROWS: 0
              SORT_SCAN: 0
          NO_INDEX_USED: 0
     NO_GOOD_INDEX_USED: 0
       NESTING_EVENT_ID: NULL
     NESTING_EVENT_TYPE: NULL
    NESTING_EVENT_LEVEL: 0
           STATEMENT_ID: 138
*************************** 2. row ***************************
              THREAD_ID: 62
               EVENT_ID: 54
           END_EVENT_ID: 74
             EVENT_NAME: statement/sql/select
                 SOURCE: init_net_server_extension.cc:96
            TIMER_START: 447882604872480000
              TIMER_END: 447882605063489000
             TIMER_WAIT: 191009000
              LOCK_TIME: 0
               SQL_TEXT: SELECT DATABASE()
                 DIGEST: ce04eda080d3f5605113e1d0d47c2f20e23b091e2487ba586f07b32cda423942
            DIGEST_TEXT: SELECT SCHEMA ( )
         CURRENT_SCHEMA: NULL
            OBJECT_TYPE: NULL
          OBJECT_SCHEMA: NULL
            OBJECT_NAME: NULL
  OBJECT_INSTANCE_BEGIN: NULL
            MYSQL_ERRNO: 0
      RETURNED_SQLSTATE: NULL
           MESSAGE_TEXT: NULL
                 ERRORS: 0
               WARNINGS: 0
          ROWS_AFFECTED: 0
              ROWS_SENT: 1
          ROWS_EXAMINED: 1
CREATED_TMP_DISK_TABLES: 0
     CREATED_TMP_TABLES: 0
       SELECT_FULL_JOIN: 0
 SELECT_FULL_RANGE_JOIN: 0
           SELECT_RANGE: 0
     SELECT_RANGE_CHECK: 0
            SELECT_SCAN: 0
      SORT_MERGE_PASSES: 0
             SORT_RANGE: 0
              SORT_ROWS: 0
              SORT_SCAN: 0
          NO_INDEX_USED: 0
     NO_GOOD_INDEX_USED: 0
       NESTING_EVENT_ID: NULL
     NESTING_EVENT_TYPE: NULL
    NESTING_EVENT_LEVEL: 0
           STATEMENT_ID: 139
*************************** 3. row ***************************
              THREAD_ID: 62
               EVENT_ID: 77
           END_EVENT_ID: 84
             EVENT_NAME: statement/com/Init DB
                 SOURCE: init_net_server_extension.cc:96
            TIMER_START: 447882605139907000
              TIMER_END: 447882605310026000
             TIMER_WAIT: 170119000
              LOCK_TIME: 0
               SQL_TEXT: NULL
                 DIGEST: NULL
            DIGEST_TEXT: NULL
         CURRENT_SCHEMA: NULL
            OBJECT_TYPE: NULL
          OBJECT_SCHEMA: NULL
            OBJECT_NAME: NULL
  OBJECT_INSTANCE_BEGIN: NULL
            MYSQL_ERRNO: 0
      RETURNED_SQLSTATE: 00000
           MESSAGE_TEXT: NULL
                 ERRORS: 0
               WARNINGS: 0
          ROWS_AFFECTED: 0
              ROWS_SENT: 0
          ROWS_EXAMINED: 0
CREATED_TMP_DISK_TABLES: 0
     CREATED_TMP_TABLES: 0
       SELECT_FULL_JOIN: 0
 SELECT_FULL_RANGE_JOIN: 0
           SELECT_RANGE: 0
     SELECT_RANGE_CHECK: 0
            SELECT_SCAN: 0
      SORT_MERGE_PASSES: 0
             SORT_RANGE: 0
              SORT_ROWS: 0
              SORT_SCAN: 0
          NO_INDEX_USED: 0
     NO_GOOD_INDEX_USED: 0
       NESTING_EVENT_ID: NULL
     NESTING_EVENT_TYPE: NULL
    NESTING_EVENT_LEVEL: 0
           STATEMENT_ID: 140
*************************** 4. row ***************************
              THREAD_ID: 62
               EVENT_ID: 89
           END_EVENT_ID: 172
             EVENT_NAME: statement/sql/select
                 SOURCE: init_net_server_extension.cc:96
            TIMER_START: 447888504775063000
              TIMER_END: 447888505728159000
             TIMER_WAIT: 953096000
              LOCK_TIME: 669000000
               SQL_TEXT: select * from user
                 DIGEST: 4681397545a73eb8e3b6bb1e918323c06d906d4114619ef72013aa442fced2ba
            DIGEST_TEXT: SELECT * FROM SYSTEM_USER
         CURRENT_SCHEMA: apple
            OBJECT_TYPE: NULL
          OBJECT_SCHEMA: NULL
            OBJECT_NAME: NULL
  OBJECT_INSTANCE_BEGIN: NULL
            MYSQL_ERRNO: 0
      RETURNED_SQLSTATE: NULL
           MESSAGE_TEXT: NULL
                 ERRORS: 0
               WARNINGS: 0
          ROWS_AFFECTED: 0
              ROWS_SENT: 6
          ROWS_EXAMINED: 6
CREATED_TMP_DISK_TABLES: 0
     CREATED_TMP_TABLES: 0
       SELECT_FULL_JOIN: 0
 SELECT_FULL_RANGE_JOIN: 0
           SELECT_RANGE: 0
     SELECT_RANGE_CHECK: 0
            SELECT_SCAN: 1
      SORT_MERGE_PASSES: 0
             SORT_RANGE: 0
              SORT_ROWS: 0
              SORT_SCAN: 0
          NO_INDEX_USED: 1
     NO_GOOD_INDEX_USED: 0
       NESTING_EVENT_ID: NULL
     NESTING_EVENT_TYPE: NULL
    NESTING_EVENT_LEVEL: 0
           STATEMENT_ID: 141
*************************** 5. row ***************************
              THREAD_ID: 62
               EVENT_ID: 177
           END_EVENT_ID: 195
             EVENT_NAME: statement/sql/begin
                 SOURCE: init_net_server_extension.cc:96
            TIMER_START: 447893344961222000
              TIMER_END: 447893345171944000
             TIMER_WAIT: 210722000
              LOCK_TIME: 0
               SQL_TEXT: begin
                 DIGEST: 55fa5810fbb2760e86d578526176c1497b134d4ef3dd0863dd78b1c5e819848c
            DIGEST_TEXT: BEGIN
         CURRENT_SCHEMA: apple
            OBJECT_TYPE: NULL
          OBJECT_SCHEMA: NULL
            OBJECT_NAME: NULL
  OBJECT_INSTANCE_BEGIN: NULL
            MYSQL_ERRNO: 0
      RETURNED_SQLSTATE: 00000
           MESSAGE_TEXT: NULL
                 ERRORS: 0
               WARNINGS: 0
          ROWS_AFFECTED: 0
              ROWS_SENT: 0
          ROWS_EXAMINED: 0
CREATED_TMP_DISK_TABLES: 0
     CREATED_TMP_TABLES: 0
       SELECT_FULL_JOIN: 0
 SELECT_FULL_RANGE_JOIN: 0
           SELECT_RANGE: 0
     SELECT_RANGE_CHECK: 0
            SELECT_SCAN: 0
      SORT_MERGE_PASSES: 0
             SORT_RANGE: 0
              SORT_ROWS: 0
              SORT_SCAN: 0
          NO_INDEX_USED: 0
     NO_GOOD_INDEX_USED: 0
       NESTING_EVENT_ID: NULL
     NESTING_EVENT_TYPE: NULL
    NESTING_EVENT_LEVEL: 0
           STATEMENT_ID: 142
*************************** 6. row ***************************
              THREAD_ID: 62
               EVENT_ID: 200
           END_EVENT_ID: 297
             EVENT_NAME: statement/sql/delete
                 SOURCE: init_net_server_extension.cc:96
            TIMER_START: 447914874977528000
              TIMER_END: 447914875546619000
             TIMER_WAIT: 569091000
              LOCK_TIME: 202000000
               SQL_TEXT: delete from user where id <3
                 DIGEST: 765af6384939d5da7bcb9092d7a9ee83e1088b7df84f0943c4e1b33f5b0fa061
            DIGEST_TEXT: DELETE FROM SYSTEM_USER WHERE `id` < ?
         CURRENT_SCHEMA: apple
            OBJECT_TYPE: NULL
          OBJECT_SCHEMA: NULL
            OBJECT_NAME: NULL
  OBJECT_INSTANCE_BEGIN: NULL
            MYSQL_ERRNO: 0
      RETURNED_SQLSTATE: 00000
           MESSAGE_TEXT: NULL
                 ERRORS: 0
               WARNINGS: 0
          ROWS_AFFECTED: 2
              ROWS_SENT: 0
          ROWS_EXAMINED: 2
CREATED_TMP_DISK_TABLES: 0
     CREATED_TMP_TABLES: 0
       SELECT_FULL_JOIN: 0
 SELECT_FULL_RANGE_JOIN: 0
           SELECT_RANGE: 0
     SELECT_RANGE_CHECK: 0
            SELECT_SCAN: 0
      SORT_MERGE_PASSES: 0
             SORT_RANGE: 0
              SORT_ROWS: 0
              SORT_SCAN: 0
          NO_INDEX_USED: 0
     NO_GOOD_INDEX_USED: 0
       NESTING_EVENT_ID: 188
     NESTING_EVENT_TYPE: TRANSACTION
    NESTING_EVENT_LEVEL: 0
           STATEMENT_ID: 143
*************************** 7. row ***************************
              THREAD_ID: 62
               EVENT_ID: 302
           END_EVENT_ID: 371
             EVENT_NAME: statement/sql/update
                 SOURCE: init_net_server_extension.cc:96
            TIMER_START: 447935365057316000
              TIMER_END: 447935365546181000
             TIMER_WAIT: 488865000
              LOCK_TIME: 206000000
               SQL_TEXT: update user set name='test' where id=4
                 DIGEST: 3fead6c0f1bffd27fd12a2119d5a6dee1dcf05403a616b3ea0b1579c2f9417eb
            DIGEST_TEXT: UPDATE SYSTEM_USER SET NAME = ? WHERE `id` = ?
         CURRENT_SCHEMA: apple
            OBJECT_TYPE: NULL
          OBJECT_SCHEMA: NULL
            OBJECT_NAME: NULL
  OBJECT_INSTANCE_BEGIN: NULL
            MYSQL_ERRNO: 0
      RETURNED_SQLSTATE: 00000
           MESSAGE_TEXT: Rows matched: 1  Changed: 1  Warnings: 0
                 ERRORS: 0
               WARNINGS: 0
          ROWS_AFFECTED: 1
              ROWS_SENT: 0
          ROWS_EXAMINED: 1
CREATED_TMP_DISK_TABLES: 0
     CREATED_TMP_TABLES: 0
       SELECT_FULL_JOIN: 0
 SELECT_FULL_RANGE_JOIN: 0
           SELECT_RANGE: 0
     SELECT_RANGE_CHECK: 0
            SELECT_SCAN: 0
      SORT_MERGE_PASSES: 0
             SORT_RANGE: 0
              SORT_ROWS: 0
              SORT_SCAN: 0
          NO_INDEX_USED: 0
     NO_GOOD_INDEX_USED: 0
       NESTING_EVENT_ID: 188
     NESTING_EVENT_TYPE: TRANSACTION
    NESTING_EVENT_LEVEL: 0
           STATEMENT_ID: 144
7 rows in set (0.00 sec)

```








## 按照THREAD_ID、EVENT_ID分组的会话历史SQL

```sql
mysql> select * from performance_schema.events_statements_history order by THREAD_ID, event_id;
+-----------+----------+--------------+---------------------------------+---------------------------------+--------------------+--------------------+------------------+----------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------+-------------+---------------+-------------+-----------------------+-------------+-------------------+--------------------------------------------------------+--------+----------+---------------+-----------+---------------+-------------------------+--------------------+------------------+------------------------+--------------+--------------------+-------------+-------------------+------------+-----------+-----------+---------------+--------------------+------------------+--------------------+---------------------+--------------+
| THREAD_ID | EVENT_ID | END_EVENT_ID | EVENT_NAME                      | SOURCE                          | TIMER_START        | TIMER_END          | TIMER_WAIT       | LOCK_TIME      | SQL_TEXT                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  | DIGEST                                                           | DIGEST_TEXT                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | CURRENT_SCHEMA     | OBJECT_TYPE | OBJECT_SCHEMA | OBJECT_NAME | OBJECT_INSTANCE_BEGIN | MYSQL_ERRNO | RETURNED_SQLSTATE | MESSAGE_TEXT                                           | ERRORS | WARNINGS | ROWS_AFFECTED | ROWS_SENT | ROWS_EXAMINED | CREATED_TMP_DISK_TABLES | CREATED_TMP_TABLES | SELECT_FULL_JOIN | SELECT_FULL_RANGE_JOIN | SELECT_RANGE | SELECT_RANGE_CHECK | SELECT_SCAN | SORT_MERGE_PASSES | SORT_RANGE | SORT_ROWS | SORT_SCAN | NO_INDEX_USED | NO_GOOD_INDEX_USED | NESTING_EVENT_ID | NESTING_EVENT_TYPE | NESTING_EVENT_LEVEL | STATEMENT_ID |
+-----------+----------+--------------+---------------------------------+---------------------------------+--------------------+--------------------+------------------+----------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------+-------------+---------------+-------------+-----------------------+-------------+-------------------+--------------------------------------------------------+--------+----------+---------------+-----------+---------------+-------------------------+--------------------+------------------+------------------------+--------------+--------------------+-------------+-------------------+------------+-----------+-----------+---------------+--------------------+------------------+--------------------+---------------------+--------------+
|        59 |    12189 |        12222 | statement/sql/select            | init_net_server_extension.cc:96 | 452463504563128000 | 452463504911604000 |        348476000 |      152000000 | select * from performance_schema.events_statements_current where thread_id=62                                                                                                                                                                                                                                                                                                                                                                                                             | 2784183eba6173f7c82e280e0e825d8f33cbb22fd8f8db06418452784ea7115f | SELECT * FROM `performance_schema` . `events_statements_current` WHERE `thread_id` = ?                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  | performance_schema | NULL        | NULL          | NULL        |                  NULL |           0 | NULL              | NULL                                                   |      0 |        0 |             0 |         1 |             1 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         0 |         0 |             0 |                  0 |             NULL | NULL               |                   0 |          280 |
|        59 |    12227 |        12263 | statement/sql/select            | init_net_server_extension.cc:96 | 452508544403809000 | 452508544985149000 |        581340000 |      283000000 | select * from performance_schema.events_statements_history where thread_id=62 order by event_id                                                                                                                                                                                                                                                                                                                                                                                           | 7d2fc3c71e65d4c02d68a57e27cf14c0e41b9f1097cd2310f7d240eb1a55a8cd | SELECT * FROM `performance_schema` . `events_statements_history` WHERE `thread_id` = ? ORDER BY `event_id`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | performance_schema | NULL        | NULL          | NULL        |                  NULL |           0 | NULL              | NULL                                                   |      0 |        0 |             0 |         7 |            14 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         7 |         1 |             0 |                  0 |             NULL | NULL               |                   0 |          281 |
|        59 |    12268 |        12301 | statement/sql/select            | init_net_server_extension.cc:96 | 452512325166297000 | 452512325619289000 |        452992000 |      163000000 | select * from performance_schema.events_statements_history where thread_id=62 order by event_id                                                                                                                                                                                                                                                                                                                                                                                           | 7d2fc3c71e65d4c02d68a57e27cf14c0e41b9f1097cd2310f7d240eb1a55a8cd | SELECT * FROM `performance_schema` . `events_statements_history` WHERE `thread_id` = ? ORDER BY `event_id`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | performance_schema | NULL        | NULL          | NULL        |                  NULL |           0 | NULL              | NULL                                                   |      0 |        0 |             0 |         7 |            14 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         7 |         1 |             0 |                  0 |             NULL | NULL               |                   0 |          282 |
|        59 |    12306 |        12339 | statement/sql/select            | init_net_server_extension.cc:96 | 452525404563634000 | 452525405007084000 |        443450000 |      159000000 | select * from performance_schema.events_statements_history where thread_id=62 order by event_id                                                                                                                                                                                                                                                                                                                                                                                           | 7d2fc3c71e65d4c02d68a57e27cf14c0e41b9f1097cd2310f7d240eb1a55a8cd | SELECT * FROM `performance_schema` . `events_statements_history` WHERE `thread_id` = ? ORDER BY `event_id`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | performance_schema | NULL        | NULL          | NULL        |                  NULL |           0 | NULL              | NULL                                                   |      0 |        0 |             0 |         7 |            14 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         7 |         1 |             0 |                  0 |             NULL | NULL               |                   0 |          284 |
|        59 |    12344 |        12377 | statement/sql/select            | init_net_server_extension.cc:96 | 452531754678329000 | 452531755108575000 |        430246000 |      160000000 | select * from performance_schema.events_statements_history where thread_id=62 order by event_id                                                                                                                                                                                                                                                                                                                                                                                           | 7d2fc3c71e65d4c02d68a57e27cf14c0e41b9f1097cd2310f7d240eb1a55a8cd | SELECT * FROM `performance_schema` . `events_statements_history` WHERE `thread_id` = ? ORDER BY `event_id`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | performance_schema | NULL        | NULL          | NULL        |                  NULL |           0 | NULL              | NULL                                                   |      0 |        0 |             0 |         7 |            14 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         7 |         1 |             0 |                  0 |             NULL | NULL               |                   0 |          285 |
|        59 |    12382 |        12401 | statement/sql/show_create_table | init_net_server_extension.cc:96 | 452724591555874000 | 452724592004130000 |        448256000 |              0 | show create table performance_schema.events_statements_history                                                                                                                                                                                                                                                                                                                                                                                                                            | 7ad58df4087703f6ced2d0328dc4c22bd594f7d4ad61e97af30ebb73e69009a0 | SHOW CREATE TABLE `performance_schema` . `events_statements_history`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | performance_schema | NULL        | NULL          | NULL        |                  NULL |           0 | NULL              | NULL                                                   |      0 |        0 |             0 |         0 |             0 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         0 |         0 |             0 |                  0 |             NULL | NULL               |                   0 |          286 |
|        59 |    12406 |        12569 | statement/sql/select            | init_net_server_extension.cc:96 | 452809292255347000 | 452809292668715000 |        413368000 |      151000000 | select * from performance_schema.data_locks                                                                                                                                                                                                                                                                                                                                                                                                                                               | fa23af4384156dfeeaf94302cd926ea91db08d5742d92a435c2e9472f1eda94e | SELECT * FROM `performance_schema` . `data_locks`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | performance_schema | NULL        | NULL          | NULL        |                  NULL |           0 | NULL              | NULL                                                   |      0 |        0 |             0 |         5 |             5 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           1 |                 0 |          0 |         0 |         0 |             1 |                  0 |             NULL | NULL               |                   0 |          287 |
|        59 |    12574 |        12737 | statement/sql/select            | init_net_server_extension.cc:96 | 452812801634994000 | 452812801968154000 |        333160000 |      135000000 | select * from performance_schema.data_locks                                                                                                                                                                                                                                                                                                                                                                                                                                               | fa23af4384156dfeeaf94302cd926ea91db08d5742d92a435c2e9472f1eda94e | SELECT * FROM `performance_schema` . `data_locks`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | performance_schema | NULL        | NULL          | NULL        |                  NULL |           0 | NULL              | NULL                                                   |      0 |        0 |             0 |         5 |             5 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           1 |                 0 |          0 |         0 |         0 |             1 |                  0 |             NULL | NULL               |                   0 |          288 |
|        59 |    12742 |        12773 | statement/sql/select            | init_net_server_extension.cc:96 | 452982491708057000 | 452982493092301000 |       1384244000 |      167000000 | select * from performance_schema.events_statements_history order by THREAD_ID, event_id                                                                                                                                                                                                                                                                                                                                                                                                   | 77a2c0829569fc506bea72be51643633258df1216e219197c85632b4787e2cff | SELECT * FROM `performance_schema` . `events_statements_history` ORDER BY `THREAD_ID` , `event_id`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | performance_schema | NULL        | NULL          | NULL        |                  NULL |           0 | NULL              | NULL                                                   |      0 |        0 |             0 |        33 |            66 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           1 |                 0 |          0 |        33 |         1 |             1 |                  0 |             NULL | NULL               |                   0 |          289 |
|        59 |    12778 |        12809 | statement/sql/select            | init_net_server_extension.cc:96 | 452992181609608000 | 452992182342951000 |        733343000 |      206000000 | select * from performance_schema.events_statements_history order by THREAD_ID, event_id                                                                                                                                                                                                                                                                                                                                                                                                   | 77a2c0829569fc506bea72be51643633258df1216e219197c85632b4787e2cff | SELECT * FROM `performance_schema` . `events_statements_history` ORDER BY `THREAD_ID` , `event_id`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | performance_schema | NULL        | NULL          | NULL        |                  NULL |           0 | NULL              | NULL                                                   |      0 |        0 |             0 |        33 |            66 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           1 |                 0 |          0 |        33 |         1 |             1 |                  0 |             NULL | NULL               |                   0 |          291 |
|        60 |     7519 |         7533 | statement/sql/begin             | init_net_server_extension.cc:96 | 450827764596569000 | 450827764703114000 |        106545000 |              0 | begin                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | 55fa5810fbb2760e86d578526176c1497b134d4ef3dd0863dd78b1c5e819848c | BEGIN                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | apple              | NULL        | NULL          | NULL        |                  NULL |           0 | 00000             | NULL                                                   |      0 |        0 |             0 |         0 |             0 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         0 |         0 |             0 |                  0 |             7459 | TRANSACTION        |                   0 |          206 |
|        60 |     7538 |         7655 | statement/sql/delete            | init_net_server_extension.cc:96 | 450845444767400000 | 450911711257981000 |   66266490581000 | 15260628000000 | delete from  user where id <5                                                                                                                                                                                                                                                                                                                                                                                                                                                             | 765af6384939d5da7bcb9092d7a9ee83e1088b7df84f0943c4e1b33f5b0fa061 | DELETE FROM SYSTEM_USER WHERE `id` < ?                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  | apple              | NULL        | NULL          | NULL        |                  NULL |        1205 | HY000             | Lock wait timeout exceeded; try restarting transaction |      1 |        0 |             0 |         0 |             0 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         0 |         0 |             0 |                  0 |             7526 | TRANSACTION        |                   0 |          207 |
|        60 |     7660 |         7735 | statement/sql/delete            | init_net_server_extension.cc:96 | 451001055162773000 | 451052060975015000 |   51005812242000 |      195000000 | delete from  user where id <5                                                                                                                                                                                                                                                                                                                                                                                                                                                             | 765af6384939d5da7bcb9092d7a9ee83e1088b7df84f0943c4e1b33f5b0fa061 | DELETE FROM SYSTEM_USER WHERE `id` < ?                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  | apple              | NULL        | NULL          | NULL        |                  NULL |        1205 | HY000             | Lock wait timeout exceeded; try restarting transaction |      1 |        0 |             0 |         0 |             0 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         0 |         0 |             0 |                  0 |             7526 | TRANSACTION        |                   0 |          216 |
|        60 |     7740 |         7815 | statement/sql/delete            | init_net_server_extension.cc:96 | 451385545270809000 | 451436550729096000 |   51005458287000 |      130000000 | delete from  user where id <5                                                                                                                                                                                                                                                                                                                                                                                                                                                             | 765af6384939d5da7bcb9092d7a9ee83e1088b7df84f0943c4e1b33f5b0fa061 | DELETE FROM SYSTEM_USER WHERE `id` < ?                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  | apple              | NULL        | NULL          | NULL        |                  NULL |        1205 | HY000             | Lock wait timeout exceeded; try restarting transaction |      1 |        0 |             0 |         0 |             0 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         0 |         0 |             0 |                  0 |             7526 | TRANSACTION        |                   0 |          228 |
|        60 |     7820 |         7895 | statement/sql/delete            | init_net_server_extension.cc:96 | 451805414586869000 | 451856420766494000 |   51006179625000 |      172000000 | delete from  user where id <5                                                                                                                                                                                                                                                                                                                                                                                                                                                             | 765af6384939d5da7bcb9092d7a9ee83e1088b7df84f0943c4e1b33f5b0fa061 | DELETE FROM SYSTEM_USER WHERE `id` < ?                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  | apple              | NULL        | NULL          | NULL        |                  NULL |        1205 | HY000             | Lock wait timeout exceeded; try restarting transaction |      1 |        0 |             0 |         0 |             0 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         0 |         0 |             0 |                  0 |             7526 | TRANSACTION        |                   0 |          233 |
|        60 |     7900 |         7975 | statement/sql/delete            | init_net_server_extension.cc:96 | 452082894778797000 | 452133900683207000 |   51005904410000 |      139000000 | delete from  user where id <5                                                                                                                                                                                                                                                                                                                                                                                                                                                             | 765af6384939d5da7bcb9092d7a9ee83e1088b7df84f0943c4e1b33f5b0fa061 | DELETE FROM SYSTEM_USER WHERE `id` < ?                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  | apple              | NULL        | NULL          | NULL        |                  NULL |        1205 | HY000             | Lock wait timeout exceeded; try restarting transaction |      1 |        0 |             0 |         0 |             0 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         0 |         0 |             0 |                  0 |             7526 | TRANSACTION        |                   0 |          237 |
|        60 |     7980 |         8055 | statement/sql/delete            | init_net_server_extension.cc:96 | 452147324629375000 | 452198330517603000 |   51005888228000 |      127000000 | delete from  user where id <5                                                                                                                                                                                                                                                                                                                                                                                                                                                             | 765af6384939d5da7bcb9092d7a9ee83e1088b7df84f0943c4e1b33f5b0fa061 | DELETE FROM SYSTEM_USER WHERE `id` < ?                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  | apple              | NULL        | NULL          | NULL        |                  NULL |        1205 | HY000             | Lock wait timeout exceeded; try restarting transaction |      1 |        0 |             0 |         0 |             0 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         0 |         0 |             0 |                  0 |             7526 | TRANSACTION        |                   0 |          265 |
|        60 |     8060 |         8135 | statement/sql/delete            | init_net_server_extension.cc:96 | 452399684804510000 | 452450691368449000 |   51006563939000 |      129000000 | delete from  user where id <5                                                                                                                                                                                                                                                                                                                                                                                                                                                             | 765af6384939d5da7bcb9092d7a9ee83e1088b7df84f0943c4e1b33f5b0fa061 | DELETE FROM SYSTEM_USER WHERE `id` < ?                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  | apple              | NULL        | NULL          | NULL        |                  NULL |        1205 | HY000             | Lock wait timeout exceeded; try restarting transaction |      1 |        0 |             0 |         0 |             0 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         0 |         0 |             0 |                  0 |             7526 | TRANSACTION        |                   0 |          275 |
|        60 |     8140 |         8215 | statement/sql/delete            | init_net_server_extension.cc:96 | 452456204698788000 | 452507211236027000 |   51006537239000 |      129000000 | delete from  user where id <5                                                                                                                                                                                                                                                                                                                                                                                                                                                             | 765af6384939d5da7bcb9092d7a9ee83e1088b7df84f0943c4e1b33f5b0fa061 | DELETE FROM SYSTEM_USER WHERE `id` < ?                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  | apple              | NULL        | NULL          | NULL        |                  NULL |        1205 | HY000             | Lock wait timeout exceeded; try restarting transaction |      1 |        0 |             0 |         0 |             0 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         0 |         0 |             0 |                  0 |             7526 | TRANSACTION        |                   0 |          278 |
|        60 |     8220 |         8295 | statement/sql/delete            | init_net_server_extension.cc:96 | 452517310653018000 | 452568318161992000 |   51007508974000 |      130000000 | delete from  user where id <5                                                                                                                                                                                                                                                                                                                                                                                                                                                             | 765af6384939d5da7bcb9092d7a9ee83e1088b7df84f0943c4e1b33f5b0fa061 | DELETE FROM SYSTEM_USER WHERE `id` < ?                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  | apple              | NULL        | NULL          | NULL        |                  NULL |        1205 | HY000             | Lock wait timeout exceeded; try restarting transaction |      1 |        0 |             0 |         0 |             0 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         0 |         0 |             0 |                  0 |             7526 | TRANSACTION        |                   0 |          283 |
|        61 |       20 |           43 | statement/sql/select            | init_net_server_extension.cc:96 | 447872161376803000 | 447872161533894000 |        157091000 |              0 | select @@version_comment limit 1                                                                                                                                                                                                                                                                                                                                                                                                                                                          | 44e35cee979ba420eb49a8471f852bbe15b403c89742704817dfbaace0d99dbb | SELECT @@`version_comment` LIMIT ?                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | NULL               | NULL        | NULL          | NULL        |                  NULL |           0 | NULL              | NULL                                                   |      0 |        0 |             0 |         1 |             1 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         0 |         0 |             0 |                  0 |             NULL | NULL               |                   0 |          137 |
|        61 |       48 |          365 | statement/sql/select            | init_net_server_extension.cc:96 | 448226494870784000 | 448226496266137000 |       1395353000 |      710000000 | select ps.id 'process id',ps.user,ps.host,esh.event_id,trx.trx_started,esh.event_name 'event name',esh.sql_text 'sql',ps.time
from performance_schema.events_statements_history esh
join performance_schema.threads th on esh.thread_id = th.thread_id
join information_schema.processlist ps on ps.id = th.processlist_id
left join information_schema.innodb_trx trx on trx.trx_mysql_thread_id = ps.id
where trx.trx_id is not null and ps.user != 'SYSTEM_USER'
order by esh.event_id | e7050143b1632b52a310223411e9642fa7f0a1d30ca7eea9e4dba690b5a67224 | SELECT `ps` . `id` ? , `ps` . `user` , `ps` . `host` , `esh` . `event_id` , `trx` . `trx_started` , `esh` . `event_name` ? , `esh` . `sql_text` ? , `ps` . `time` FROM `performance_schema` . `events_statements_history` `esh` JOIN `performance_schema` . `threads` `th` ON `esh` . `thread_id` = `th` . `thread_id` JOIN `information_schema` . `processlist` `ps` ON `ps` . `id` = `th` . `processlist_id` LEFT JOIN `information_schema` . `innodb_trx` `trx` ON `trx` . `trx_mysql_thread_id` = `ps` . `id` WHERE `trx` . `trx_id` IS NOT NULL AND `ps` . `user` != ? ORDER BY `esh` . `event_id` | NULL               | NULL        | NULL          | NULL        |                  NULL |           0 | NULL              | NULL                                                   |      0 |        0 |             0 |         7 |            21 |                       1 |                  3 |                1 |                      0 |            0 |                  0 |           1 |                 0 |          0 |         7 |         1 |             1 |                  0 |             NULL | NULL               |                   0 |          157 |
|        61 |      370 |          676 | statement/sql/select            | init_net_server_extension.cc:96 | 448340355506898000 | 448340358161167000 |       2654269000 |     1195000000 | select ps.id 'process id',ps.user,ps.host,esh.event_id,trx.trx_started,esh.event_name 'event name',esh.sql_text 'sql',ps.time from performance_schema.events_statements_history esh join performance_schema.threads th on esh.thread_id = th.thread_id join information_schema.processlist ps on ps.id = th.processlist_id left join information_schema.innodb_trx trx on trx.trx_mysql_thread_id = ps.id where trx.trx_id is not null and ps.user != 'SYSTEM_USER' order by esh.event_id | e7050143b1632b52a310223411e9642fa7f0a1d30ca7eea9e4dba690b5a67224 | SELECT `ps` . `id` ? , `ps` . `user` , `ps` . `host` , `esh` . `event_id` , `trx` . `trx_started` , `esh` . `event_name` ? , `esh` . `sql_text` ? , `ps` . `time` FROM `performance_schema` . `events_statements_history` `esh` JOIN `performance_schema` . `threads` `th` ON `esh` . `thread_id` = `th` . `thread_id` JOIN `information_schema` . `processlist` `ps` ON `ps` . `id` = `th` . `processlist_id` LEFT JOIN `information_schema` . `innodb_trx` `trx` ON `trx` . `trx_mysql_thread_id` = `ps` . `id` WHERE `trx` . `trx_id` IS NOT NULL AND `ps` . `user` != ? ORDER BY `esh` . `event_id` | NULL               | NULL        | NULL          | NULL        |                  NULL |           0 | NULL              | NULL                                                   |      0 |        0 |             0 |         7 |            21 |                       1 |                  3 |                1 |                      0 |            0 |                  0 |           1 |                 0 |          0 |         7 |         1 |             1 |                  0 |             NULL | NULL               |                   0 |          158 |
|        61 |      681 |          701 | statement/sql/select            | init_net_server_extension.cc:96 | 448529304732421000 | 448529304949731000 |        217310000 |              0 | SELECT DATABASE()                                                                                                                                                                                                                                                                                                                                                                                                                                                                         | ce04eda080d3f5605113e1d0d47c2f20e23b091e2487ba586f07b32cda423942 | SELECT SCHEMA ( )                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | NULL               | NULL        | NULL          | NULL        |                  NULL |           0 | NULL              | NULL                                                   |      0 |        0 |             0 |         1 |             1 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         0 |         0 |             0 |                  0 |             NULL | NULL               |                   0 |          159 |
|        61 |      704 |          711 | statement/com/Init DB           | init_net_server_extension.cc:96 | 448529305011232000 | 448529305151525000 |        140293000 |              0 | NULL                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | NULL                                                             | NULL                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | NULL               | NULL        | NULL          | NULL        |                  NULL |           0 | 00000             | NULL                                                   |      0 |        0 |             0 |         0 |             0 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         0 |         0 |             0 |                  0 |             NULL | NULL               |                   0 |          160 |
|        61 |      716 |        14715 | statement/sql/alter_table       | init_net_server_extension.cc:96 | 448572225361906000 | 450860705378147000 | 2288480016241000 |      470000000 | NULL                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | 7e032d08c8e06451e89c81700ec50c04d1bce895b4b0ef2b54101b2a3726bc09 | ALTER TABLE SYSTEM_USER ADD INDEX `idx_name` ( NAME )                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | apple              | NULL        | NULL          | NULL        |                  NULL |        1317 | 70100             | Query execution was interrupted                        |      1 |        0 |             0 |         0 |             0 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         0 |         0 |             0 |                  0 |             NULL | NULL               |                   0 |          161 |
|        62 |       26 |           49 | statement/sql/select            | init_net_server_extension.cc:96 | 447875592288186000 | 447875592458581000 |        170395000 |              0 | select @@version_comment limit 1                                                                                                                                                                                                                                                                                                                                                                                                                                                          | 44e35cee979ba420eb49a8471f852bbe15b403c89742704817dfbaace0d99dbb | SELECT @@`version_comment` LIMIT ?                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | NULL               | NULL        | NULL          | NULL        |                  NULL |           0 | NULL              | NULL                                                   |      0 |        0 |             0 |         1 |             1 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         0 |         0 |             0 |                  0 |             NULL | NULL               |                   0 |          138 |
|        62 |       54 |           74 | statement/sql/select            | init_net_server_extension.cc:96 | 447882604872480000 | 447882605063489000 |        191009000 |              0 | SELECT DATABASE()                                                                                                                                                                                                                                                                                                                                                                                                                                                                         | ce04eda080d3f5605113e1d0d47c2f20e23b091e2487ba586f07b32cda423942 | SELECT SCHEMA ( )                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | NULL               | NULL        | NULL          | NULL        |                  NULL |           0 | NULL              | NULL                                                   |      0 |        0 |             0 |         1 |             1 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         0 |         0 |             0 |                  0 |             NULL | NULL               |                   0 |          139 |
|        62 |       77 |           84 | statement/com/Init DB           | init_net_server_extension.cc:96 | 447882605139907000 | 447882605310026000 |        170119000 |              0 | NULL                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | NULL                                                             | NULL                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | NULL               | NULL        | NULL          | NULL        |                  NULL |           0 | 00000             | NULL                                                   |      0 |        0 |             0 |         0 |             0 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         0 |         0 |             0 |                  0 |             NULL | NULL               |                   0 |          140 |
|        62 |       89 |          172 | statement/sql/select            | init_net_server_extension.cc:96 | 447888504775063000 | 447888505728159000 |        953096000 |      669000000 | select * from user                                                                                                                                                                                                                                                                                                                                                                                                                                                                        | 4681397545a73eb8e3b6bb1e918323c06d906d4114619ef72013aa442fced2ba | SELECT * FROM SYSTEM_USER                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | apple              | NULL        | NULL          | NULL        |                  NULL |           0 | NULL              | NULL                                                   |      0 |        0 |             0 |         6 |             6 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           1 |                 0 |          0 |         0 |         0 |             1 |                  0 |             NULL | NULL               |                   0 |          141 |
|        62 |      177 |          195 | statement/sql/begin             | init_net_server_extension.cc:96 | 447893344961222000 | 447893345171944000 |        210722000 |              0 | begin                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | 55fa5810fbb2760e86d578526176c1497b134d4ef3dd0863dd78b1c5e819848c | BEGIN                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | apple              | NULL        | NULL          | NULL        |                  NULL |           0 | 00000             | NULL                                                   |      0 |        0 |             0 |         0 |             0 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         0 |         0 |             0 |                  0 |             NULL | NULL               |                   0 |          142 |
|        62 |      200 |          297 | statement/sql/delete            | init_net_server_extension.cc:96 | 447914874977528000 | 447914875546619000 |        569091000 |      202000000 | delete from user where id <3                                                                                                                                                                                                                                                                                                                                                                                                                                                              | 765af6384939d5da7bcb9092d7a9ee83e1088b7df84f0943c4e1b33f5b0fa061 | DELETE FROM SYSTEM_USER WHERE `id` < ?                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  | apple              | NULL        | NULL          | NULL        |                  NULL |           0 | 00000             | NULL                                                   |      0 |        0 |             2 |         0 |             2 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         0 |         0 |             0 |                  0 |              188 | TRANSACTION        |                   0 |          143 |
|        62 |      302 |          371 | statement/sql/update            | init_net_server_extension.cc:96 | 447935365057316000 | 447935365546181000 |        488865000 |      206000000 | update user set name='test' where id=4                                                                                                                                                                                                                                                                                                                                                                                                                                                    | 3fead6c0f1bffd27fd12a2119d5a6dee1dcf05403a616b3ea0b1579c2f9417eb | UPDATE SYSTEM_USER SET NAME = ? WHERE `id` = ?                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          | apple              | NULL        | NULL          | NULL        |                  NULL |           0 | 00000             | Rows matched: 1  Changed: 1  Warnings: 0               |      0 |        0 |             1 |         0 |             1 |                       0 |                  0 |                0 |                      0 |            0 |                  0 |           0 |                 0 |          0 |         0 |         0 |             0 |                  0 |              188 | TRANSACTION        |                   0 |          144 |
+-----------+----------+--------------+---------------------------------+---------------------------------+--------------------+--------------------+------------------+----------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------+-------------+---------------+-------------+-----------------------+-------------+-------------------+--------------------------------------------------------+--------+----------+---------------+-----------+---------------+-------------------------+--------------------+------------------+------------------------+--------------+--------------------+-------------+-------------------+------------+-----------+-----------+---------------+--------------------+------------------+--------------------+---------------------+--------------+
33 rows in set (0.00 sec)


```












