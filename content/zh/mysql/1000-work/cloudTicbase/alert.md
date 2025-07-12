---
title: 告警
description: alert
date: 2023-10-12
weight: 60000


---

{{< alert >}}


{{< /alert >}}



### 数据节点当前行锁等待个数较多
```sql
show global status;
```
- alertkey：TicBase_mysql.gdb_global_status_innodb_row_lock_current_waits
- expr：gdb_global_status_innodb_row_lock_current_waits{}> 20
- sql：show global status;
- eventid：110001
- message：开阳集群{{ $labels.cluster }}中数据节点address{{ $labels.address }},port{{ $labels.port }},name{{ $labels.name }}当前行锁等待个数超过20














