---
title: data_locks
description: data_locks
date: 2023-10-26
weight: 20000


---

{{< alert >}}
MySQL
{{< /alert >}}


### 表结构：data_locks

```sql
mysql> show create table data_locks\G
*************************** 1. row ***************************
       Table: data_locks
Create Table: CREATE TABLE `data_locks` (
  `ENGINE` varchar(32) NOT NULL,
  `ENGINE_LOCK_ID` varchar(128) NOT NULL,
  `ENGINE_TRANSACTION_ID` bigint unsigned DEFAULT NULL,
  `THREAD_ID` bigint unsigned DEFAULT NULL,
  `EVENT_ID` bigint unsigned DEFAULT NULL,
  `OBJECT_SCHEMA` varchar(64) DEFAULT NULL,
  `OBJECT_NAME` varchar(64) DEFAULT NULL,
  `PARTITION_NAME` varchar(64) DEFAULT NULL,
  `SUBPARTITION_NAME` varchar(64) DEFAULT NULL,
  `INDEX_NAME` varchar(64) DEFAULT NULL,
  `OBJECT_INSTANCE_BEGIN` bigint unsigned NOT NULL,
  `LOCK_TYPE` varchar(32) NOT NULL,
  `LOCK_MODE` varchar(32) NOT NULL,
  `LOCK_STATUS` varchar(32) NOT NULL,
  `LOCK_DATA` varchar(8192) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci DEFAULT NULL,
  PRIMARY KEY (`ENGINE_LOCK_ID`,`ENGINE`),
  KEY `ENGINE_TRANSACTION_ID` (`ENGINE_TRANSACTION_ID`,`ENGINE`),
  KEY `THREAD_ID` (`THREAD_ID`,`EVENT_ID`),
  KEY `OBJECT_SCHEMA` (`OBJECT_SCHEMA`,`OBJECT_NAME`,`PARTITION_NAME`,`SUBPARTITION_NAME`)
) ENGINE=PERFORMANCE_SCHEMA DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)



```
### 字段详解
- ENGINE: 存储引擎（INNODB）
- ENGINE_LOCK_ID 存储引擎内部的锁ID，该值会发生动态变化，外部系统不应该依赖该值
- ENGINE_TRANSACTION_ID: 2578 (information_schema.innodb_trx中的trx_id)
- THREAD_ID: 持有锁的线程ID
- EVENT_ID: 29 会话ID
- OBJECT_SCHEMA: 数据库名（lock_test）
- OBJECT_NAME: 表名（first_table）
- PARTITION_NAME: 分区名
- SUBPARTITION_NAME: 子分区名
- INDEX_NAME: 索引名
- OBJECT_INSTANCE_BEGIN: 锁的内存空间起始地址（140373282389696）
- LOCK_TYPE: 锁类型（TABLE/RECORD）
- LOCK_MODE: 锁模式（IX: 表意向排它锁， X： NextKey-Lock, X, REC_NOT_GAP: 行锁， X,GAP: 间隙锁，LOCK_INSERT_INTENTION插入意向锁）
- **LOCK_STATUS**: GRANTED、WAITING
- LOCK_DATA： 锁的数据，当LOCK_TYPE为RECORD时才会有值（如果是聚族索引则直接显示主键，如果是非聚族索引则是，当前数据以及主键数据）






### 案例
```sql
mysql> select * from data_locks\G
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
       ENGINE_LOCK_ID: 140603514388480:625:4:10:140603404925528
ENGINE_TRANSACTION_ID: 3256133
            THREAD_ID: 60
             EVENT_ID: 7865
        OBJECT_SCHEMA: apple
          OBJECT_NAME: user
       PARTITION_NAME: NULL
    SUBPARTITION_NAME: NULL
           INDEX_NAME: PRIMARY
OBJECT_INSTANCE_BEGIN: 140603404925528
            LOCK_TYPE: RECORD
            LOCK_MODE: X,REC_NOT_GAP
          LOCK_STATUS: WAITING
            LOCK_DATA: 1
*************************** 3. row ***************************
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
*************************** 4. row ***************************
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
*************************** 5. row ***************************
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
*************************** 6. row ***************************
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
6 rows in set (0.00 sec)



```














