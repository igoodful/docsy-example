---
title: replication_group_members
description: replication_group_members
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
[root@localhost:greatdb.sock 15:16:24((none))]$ select * from performance_schema.replication_group_members\G
*************************** 1. row ***************************
              CHANNEL_NAME: group_replication_applier
                 MEMBER_ID: 52763d77-c915-11ef-ae17-00163e85dd8c
               MEMBER_HOST: 172.17.138.136
               MEMBER_PORT: 13000
              MEMBER_STATE: RECOVERING
               MEMBER_ROLE: PRIMARY
            MEMBER_VERSION: 8.0.32
MEMBER_COMMUNICATION_STACK: XCom
*************************** 2. row ***************************
              CHANNEL_NAME: group_replication_applier
                 MEMBER_ID: 68159daa-c6c1-11ef-8d9a-00163e4d7f76
               MEMBER_HOST: 172.17.135.250
               MEMBER_PORT: 13000
              MEMBER_STATE: ONLINE
               MEMBER_ROLE: PRIMARY
            MEMBER_VERSION: 8.0.32
MEMBER_COMMUNICATION_STACK: XCom
*************************** 3. row ***************************
              CHANNEL_NAME: group_replication_applier
                 MEMBER_ID: c9ddd355-c6bb-11ef-abd1-00163e33c28f
               MEMBER_HOST: 172.17.136.101
               MEMBER_PORT: 13000
              MEMBER_STATE: ONLINE
               MEMBER_ROLE: PRIMARY
            MEMBER_VERSION: 8.0.32
MEMBER_COMMUNICATION_STACK: XCom
3 rows in set (0.00 sec)

```

