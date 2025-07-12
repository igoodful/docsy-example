---
title: 基于普通表ibd恢复数据
description: ibd_recovery
date: 2023-10-12
weight: 20000


---

{{<alert color="danger" title="注意" >}}

1. 数据库版本相同
2. `innodb_file_per_table`必须启用


{{</alert>}}



## 一、在新实例上，创建库表

> 表名称和结构必须相同

```sql
create table t1 (i int,primary key(i)) engine = innodb partition by key (i) partitions 4;


```


## 二、在新实例上，弃新分区表的表空间

> 这会导致新表的ibd文件删除

```sql
alter table t1 discard partition p2, p3 tablespace;

```



## 三、拷贝ibd文件到新实例上的新表所在位置

> 在源实例上，运行 FLUSH TABLES ... FOR EXPORT以使分区表静止。当表静止时，仅允许在该表上进行只读事务
>
> 确保该.ibd文件具有必要的文件权限。


在源端上，执行：
```sql
use test;
flush tables t1 for export;

```

拷贝在源端上的`t1#p#p2.ibd t1#p#p2.cfg t1#p#p3.ibd t1#p#p3.cfg`到目标端
```bash
scp t1#p#p2.ibd t1#p#p2.cfg t1#p#p3.ibd t1#p#p3.cfg mysql@10.10.10.10:/data/mysql_3306/data/db8/

```


## 源端：释放锁
```sql
use test;
unlock tables;

```



## 四、在新实例上，导入.ibd表空间

> 将发出警告，指示InnoDB正在尝试导入未经架构验证的文件。


```sql
alter table db8.t1 import tablespace;

show warnings;

```


## 五、在新实例上，确认恢复是否已成功

```sql
select count(*) from db8.t1;

```



















