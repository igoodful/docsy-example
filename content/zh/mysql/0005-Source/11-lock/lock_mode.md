---
title: lock_mode
description: lock_mode
date: 2023-10-30
weight: 50


viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


{{<alert color="secondary">}}

本文基于 MySQL 8.0.32 源码，存储引擎为 InnoDB。

格式化：Markdown Table Prettifier

{{</alert>}}



## 一、锁模式兼容表

| 锁模式     | 意向共享锁 | 意向排他锁 | 共享锁   | 排他锁   | AUTO-INC锁 |
| :--------- | :--------- | :--------- | :------- | :------- | :--------- |
| 意向共享锁 | &#9989;    | &#9989;    | &#9989;  | &#10060; | &#9989;    |
| 意向排他锁 | &#9989;    | &#9989;    | &#10060; | &#10060; | &#9989;    |
| 共享锁     | &#9989;    | &#10060;   | &#9989;  | &#10060; | &#10060;   |
| 排他锁     | &#10060;   | &#10060;   | &#10060; | &#10060; | &#10060;   |
| AUTO-INC锁 | &#9989;    | &#9989;    | &#10060; | &#10060; | &#10060;   |

- &#9989; 表示锁兼容
- &#10060; 表示锁不兼容


数据页大小

```c++
/** The universal page size of the database */
#define UNIV_PAGE_SIZE ((ulint)srv_page_size)
```
