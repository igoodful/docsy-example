---
title: max_binlog_size
description: 设置单个binlog日志大小的最大值
date: 2023-10-25
weight: 20000
viz: true

---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

- https://bugs.mysql.com/bug.php?id=104785
- https://cloud.tencent.com/developer/article/1899939
- https://www.cnblogs.com/kerrycode/p/18130054


{{< /alert >}}


##


```sql

flush binary logs;


```

max_binlog_size 的最小值是4096字节，最大值和默认值是 1GB (1073741824字节)。事务被写入到binlog的一个块中，所以它不会在几个二进制日志之间被拆分。因此，如果你有很大的事务，为了保证事务的完整性，不可能做切换日志的动作，只能将该事务的日志都记录到当前日志文件中，直到事务结束，你可能会看到bi









