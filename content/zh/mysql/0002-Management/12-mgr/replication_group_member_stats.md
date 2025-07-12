---
title: replication_group_member_stats
description: replication_group_member_stats
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
[root@localhost:greatdb.sock 15:09:54((none))]$ select * from performance_schema.replication_group_member_stats\G
*************************** 1. row ***************************
                              CHANNEL_NAME: group_replication_applier
                                   VIEW_ID: 17357472457644909:31
                                 MEMBER_ID: 52763d77-c915-11ef-ae17-00163e85dd8c
               COUNT_TRANSACTIONS_IN_QUEUE: 2
                COUNT_TRANSACTIONS_CHECKED: 87556
                  COUNT_CONFLICTS_DETECTED: 2
        COUNT_TRANSACTIONS_ROWS_VALIDATING: 219
        TRANSACTIONS_COMMITTED_ALL_MEMBERS:
            LAST_CONFLICT_FREE_TRANSACTION: a33be252-2b71-11e6-b8f4-00212844f133:2376042
COUNT_TRANSACTIONS_REMOTE_IN_APPLIER_QUEUE: 53794
         COUNT_TRANSACTIONS_REMOTE_APPLIED: 33761
         COUNT_TRANSACTIONS_LOCAL_PROPOSED: 0
         COUNT_TRANSACTIONS_LOCAL_ROLLBACK: 0
*************************** 2. row ***************************
                              CHANNEL_NAME: group_replication_applier
                                   VIEW_ID: 17357472457644909:31
                                 MEMBER_ID: 68159daa-c6c1-11ef-8d9a-00163e4d7f76
               COUNT_TRANSACTIONS_IN_QUEUE: 0
                COUNT_TRANSACTIONS_CHECKED: 1972950
                  COUNT_CONFLICTS_DETECTED: 46
        COUNT_TRANSACTIONS_ROWS_VALIDATING: 84
        TRANSACTIONS_COMMITTED_ALL_MEMBERS: a33be252-2b71-11e6-b8f4-00212844f133:1-596935:1000007-2375997
            LAST_CONFLICT_FREE_TRANSACTION: a33be252-2b71-11e6-b8f4-00212844f133:596948
COUNT_TRANSACTIONS_REMOTE_IN_APPLIER_QUEUE: 0
         COUNT_TRANSACTIONS_REMOTE_APPLIED: 919313
         COUNT_TRANSACTIONS_LOCAL_PROPOSED: 1053626
         COUNT_TRANSACTIONS_LOCAL_ROLLBACK: 18
*************************** 3. row ***************************
                              CHANNEL_NAME: group_replication_applier
                                   VIEW_ID: 17357472457644909:31
                                 MEMBER_ID: c9ddd355-c6bb-11ef-abd1-00163e33c28f
               COUNT_TRANSACTIONS_IN_QUEUE: 0
                COUNT_TRANSACTIONS_CHECKED: 889596
                  COUNT_CONFLICTS_DETECTED: 24
        COUNT_TRANSACTIONS_ROWS_VALIDATING: 84
        TRANSACTIONS_COMMITTED_ALL_MEMBERS: a33be252-2b71-11e6-b8f4-00212844f133:1-596935:1000007-2375997
            LAST_CONFLICT_FREE_TRANSACTION: a33be252-2b71-11e6-b8f4-00212844f133:596948
COUNT_TRANSACTIONS_REMOTE_IN_APPLIER_QUEUE: 0
         COUNT_TRANSACTIONS_REMOTE_APPLIED: 608579
         COUNT_TRANSACTIONS_LOCAL_PROPOSED: 280998
         COUNT_TRANSACTIONS_LOCAL_ROLLBACK: 5
3 rows in set (0.06 sec)


```

