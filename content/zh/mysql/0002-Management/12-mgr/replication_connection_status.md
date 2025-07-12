---
title: replication_connection_status
description: replication_connection_status
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

```sql
[root@localhost:greatdb.sock 15:16:40((none))]$ select * from performance_schema.replication_connection_status\G
*************************** 1. row ***************************
                                      CHANNEL_NAME: group_replication_applier
                                        GROUP_NAME: a33be252-2b71-11e6-b8f4-00212844f133
                                       SOURCE_UUID: a33be252-2b71-11e6-b8f4-00212844f133
                                         THREAD_ID: NULL
                                     SERVICE_STATE: ON
                         COUNT_RECEIVED_HEARTBEATS: 0
                          LAST_HEARTBEAT_TIMESTAMP: 0000-00-00 00:00:00.000000
                          RECEIVED_TRANSACTION_SET: a33be252-2b71-11e6-b8f4-00212844f133:1-599124:1000007-2378325
                                 LAST_ERROR_NUMBER: 0
                                LAST_ERROR_MESSAGE:
                              LAST_ERROR_TIMESTAMP: 0000-00-00 00:00:00.000000
                           LAST_QUEUED_TRANSACTION: a33be252-2b71-11e6-b8f4-00212844f133:599124
 LAST_QUEUED_TRANSACTION_ORIGINAL_COMMIT_TIMESTAMP: 2025-01-03 15:16:57.926096
LAST_QUEUED_TRANSACTION_IMMEDIATE_COMMIT_TIMESTAMP: 2025-01-03 15:16:57.926096
     LAST_QUEUED_TRANSACTION_START_QUEUE_TIMESTAMP: 2025-01-03 15:16:57.924449
       LAST_QUEUED_TRANSACTION_END_QUEUE_TIMESTAMP: 2025-01-03 15:16:57.924477
                              QUEUEING_TRANSACTION:
    QUEUEING_TRANSACTION_ORIGINAL_COMMIT_TIMESTAMP: 0000-00-00 00:00:00.000000
   QUEUEING_TRANSACTION_IMMEDIATE_COMMIT_TIMESTAMP: 0000-00-00 00:00:00.000000
        QUEUEING_TRANSACTION_START_QUEUE_TIMESTAMP: 0000-00-00 00:00:00.000000
*************************** 2. row ***************************
                                      CHANNEL_NAME: group_replication_recovery
                                        GROUP_NAME:
                                       SOURCE_UUID:
                                         THREAD_ID: NULL
                                     SERVICE_STATE: OFF
                         COUNT_RECEIVED_HEARTBEATS: 0
                          LAST_HEARTBEAT_TIMESTAMP: 0000-00-00 00:00:00.000000
                          RECEIVED_TRANSACTION_SET:
                                 LAST_ERROR_NUMBER: 0
                                LAST_ERROR_MESSAGE:
                              LAST_ERROR_TIMESTAMP: 0000-00-00 00:00:00.000000
                           LAST_QUEUED_TRANSACTION:
 LAST_QUEUED_TRANSACTION_ORIGINAL_COMMIT_TIMESTAMP: 0000-00-00 00:00:00.000000
LAST_QUEUED_TRANSACTION_IMMEDIATE_COMMIT_TIMESTAMP: 0000-00-00 00:00:00.000000
     LAST_QUEUED_TRANSACTION_START_QUEUE_TIMESTAMP: 0000-00-00 00:00:00.000000
       LAST_QUEUED_TRANSACTION_END_QUEUE_TIMESTAMP: 0000-00-00 00:00:00.000000
                              QUEUEING_TRANSACTION:
    QUEUEING_TRANSACTION_ORIGINAL_COMMIT_TIMESTAMP: 0000-00-00 00:00:00.000000
   QUEUEING_TRANSACTION_IMMEDIATE_COMMIT_TIMESTAMP: 0000-00-00 00:00:00.000000
        QUEUEING_TRANSACTION_START_QUEUE_TIMESTAMP: 0000-00-00 00:00:00.000000
2 rows in set (0.01 sec)

```


