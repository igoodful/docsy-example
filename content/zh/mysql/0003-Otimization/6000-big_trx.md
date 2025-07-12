---
title: 6. 大事务
description: 大事务
date: 2023-10-13
weight: 6000


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

### innodb的dml操作的行级锁的等待时间
```sql
mysql> show global variables like 'innodb_lock_wait_timeout';
+--------------------------+-------+
| Variable_name            | Value |
+--------------------------+-------+
| innodb_lock_wait_timeout | 50    |
+--------------------------+-------+
1 row in set (0.00 sec)



```