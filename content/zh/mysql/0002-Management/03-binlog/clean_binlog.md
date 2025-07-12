---
title: 清理binlog
description: 清理binlog
date: 2023-10-23
weight: 1000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

---

{{< alert >}}

{{< /alert >}}


### 推荐删除方法
```sql
show master logs;
purge binary logs to 'mysql-bin.000001';
purge binary logs before '2024-04-28 23:59:59';
show variables like 'expire_logs_days';
set global expire_logs_days = 60;

flush logs;
reset master;
```


### 手动清理binlog
- 先删除mysql-bin.index里面的对应项

- 再删除binlog文件

```bash

```








