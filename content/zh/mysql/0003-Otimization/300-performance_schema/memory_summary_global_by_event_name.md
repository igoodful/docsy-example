---
title: memory_summary_global_by_event_name
description: 从Memory Event（内存事件）本身，统计全局的内存信息
date: 2024-10-09
weight: 20000
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



```sql
[root@127.0.0.1:8032 10:03:28(performance_schema)]$ show create table memory_summary_global_by_event_name\G
*************************** 1. row ***************************
       Table: memory_summary_global_by_event_name
Create Table: CREATE TABLE `memory_summary_global_by_event_name` (
  `EVENT_NAME` varchar(128) NOT NULL,
  `COUNT_ALLOC` bigint unsigned NOT NULL,
  `COUNT_FREE` bigint unsigned NOT NULL,
  `SUM_NUMBER_OF_BYTES_ALLOC` bigint unsigned NOT NULL,
  `SUM_NUMBER_OF_BYTES_FREE` bigint unsigned NOT NULL,
  `LOW_COUNT_USED` bigint NOT NULL,
  `CURRENT_COUNT_USED` bigint NOT NULL,
  `HIGH_COUNT_USED` bigint NOT NULL,
  `LOW_NUMBER_OF_BYTES_USED` bigint NOT NULL,
  `CURRENT_NUMBER_OF_BYTES_USED` bigint NOT NULL,
  `HIGH_NUMBER_OF_BYTES_USED` bigint NOT NULL,
  PRIMARY KEY (`EVENT_NAME`)
) ENGINE=PERFORMANCE_SCHEMA DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)

```



