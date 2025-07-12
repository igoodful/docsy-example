---
title: 5. analyze
description: analyze
date: 2023-10-31
weight: 5000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{<alert  color="secondary">}}



{{</alert>}}

| 配置                  | 参数          | 备注         |
| :-------------------- | :------------ | :----------- |
| version               | 8.0.32        | 版本         |
| engine                | innodb        | 存储引擎     |
| transaction_isolation | read commited | 事务隔离级别 |


以下情况会触发统计信息更新:
1. analyze table
2. 距离上一次更新统计信息，发生变化的行数超过一定数值时自动更新(transient: 1/16，persistent1/10)transient和persistent是两种采样方式
3. create table/truncate table 会初始化统计信息。





























