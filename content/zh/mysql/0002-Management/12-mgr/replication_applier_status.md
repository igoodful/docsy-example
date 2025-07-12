---
title: replication_applier_status
description: replication_applier_status
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
[root@localhost:greatdb.sock 15:16:57((none))]$ select * from performance_schema.replication_applier_status\G
*************************** 1. row ***************************
              CHANNEL_NAME: group_replication_applier
             SERVICE_STATE: ON
           REMAINING_DELAY: NULL
COUNT_TRANSACTIONS_RETRIES: 0
*************************** 2. row ***************************
              CHANNEL_NAME: group_replication_recovery
             SERVICE_STATE: OFF
           REMAINING_DELAY: NULL
COUNT_TRANSACTIONS_RETRIES: 0
2 rows in set (0.03 sec)

```


