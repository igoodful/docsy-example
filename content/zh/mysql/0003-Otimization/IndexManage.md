---
title: 索引管理
date: 2023-10-31
weight: 70000
description: 索引
---

{{< alert >}}
- 创建索引

- 删除索引


{{< /alert >}}




{{< alert color="warning" title="Warning" >}}
创建、删除索引时，有MDL锁
{{< /alert >}}


```sql
create table … index k1(c1);

alter table t add index k1(c1);

create index idx on k1(c1);

alter table t drop index k1;

drop index idx on k1;


# 可以 建议 优化器使用索引 USE INDEX(k1)
SELECT … FROM t1 USE INDEX(k1) WHERE …
# 还可以 强制 优化器使用索引 FORCE INDEX(k1)
SELECT … FROM t1 FORCE INDEX(k1) WHERE …
#查看索引统计信息
show index from t3;
```



更新索引统计信息
```sql
analyze table t1;
analyze table t update histogram on c1, c2, c3 with 10 buckets;
```
执行ANALYZE TABLE会加共享MDL锁，执行前要检查是否有其他事务阻塞
索引统计信息会自动更新，除非执行计划出现异常，否则没必要手动更新
如果不放心，可以选择业务低谷时，定时更新统计信息