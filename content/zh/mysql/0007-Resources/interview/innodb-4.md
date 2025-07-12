---
title: innodb 四大特性
description: innodb 四大特性
date: 2025-06-27
weight: 20000
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;

$ \rightleftharpoons $

{{< alert >}}

{{< /alert >}}


## 总览

```viz-dot
digraph "四大特性" {
rankdir = TB;
edge [
 fontsize="12"
];

node [
shape = rectangle;
fontsize = "12"
];

"四大特性" -> "插入缓冲";
"四大特性" -> "双写机制";
"四大特性" -> "自适应哈希索引";
"四大特性" -> "预读";

}

```








##  1. 插入缓冲

- https://github.com/asdbex1078/MySQL/blob/master/mysql-storage-engines/innodb/1.1.1.InnoDB%E2%80%94%E2%80%94%E5%85%B3%E9%94%AE%E7%89%B9%E6%80%A7.md

#### 1.1 问题

插入缓冲是为了解决非聚集索引随机写导致的效率低的问题，但是对于有唯一约束的非聚集索引也无能为力

往非聚簇索引中插入数据时：

（1）首先会看缓冲池中，是否有要插入的非聚簇索引页。如果有则直接插入，那个页变成脏页。

（2）如果没有，就把多次插入的数据先缓冲到插入缓冲中，然后合并多次操作，即把非聚集索引在一起的数据合并为一次IO（减少IO），再以一定的频率刷新到磁盘(将Insert Buffer和辅助索引页字节点进行merge操作)，但是插入缓冲只是针对非聚集索引没有非唯一约束的索引的插入有效。


```sql
[root@127.0.0.1:8032 14:06:40((none))]$ show variables like 'innodb_change_buffering';
+-------------------------+-------+
| Variable_name           | Value |
+-------------------------+-------+
| innodb_change_buffering | all   |
+-------------------------+-------+
1 row in set (0.07 sec)

[root@127.0.0.1:8032 14:06:42((none))]$ show variables like 'innodb_change_buffer_max_size';
+-------------------------------+-------+
| Variable_name                 | Value |
+-------------------------------+-------+
| innodb_change_buffer_max_size | 25    |
+-------------------------------+-------+
1 row in set (0.01 sec)


```



##  2. 双写机制

#### 2.1 问题

数据页为16K，磁盘一页是4K，磁盘io最小单元是512字节

MySQL中的页是16k，数据的校验是按照这个为单位进行的，而操作系统层面的数据单位肯定达不到16k（比如是4k），那么一旦发生断电时，只保留了部分写入

```sql
[root@127.0.0.1:8032 14:06:53((none))]$ show global status like '%dblwr%';
+----------------------------+-------+
| Variable_name              | Value |
+----------------------------+-------+
| Innodb_dblwr_pages_written | 431   |
| Innodb_dblwr_writes        | 431   |
+----------------------------+-------+
2 rows in set (0.19 sec)

[root@127.0.0.1:8032 14:11:23((none))]$ show variables like '%double%';
+-------------------------------+-------+
| Variable_name                 | Value |
+-------------------------------+-------+
| innodb_doublewrite            | ON    |
| innodb_doublewrite_batch_size | 0     |
| innodb_doublewrite_dir        |       |
| innodb_doublewrite_files      | 2     |
| innodb_doublewrite_pages      | 1     |
+-------------------------------+-------+
5 rows in set (0.06 sec)


```




##  3. 自适应哈希索引

#### 3.1 问题

innodb存储引擎会监控对表上的各索引页的查询，如果观察到建立哈希索引可以带来速度提升，则建立哈希索引，称之为自适应哈希索引（Adaptive hash index,AHI）.AHI是通过缓冲池的B+树构造而来，因此建立速度很快，innodb会自动根据访问的频率和模式自动的为某些热点页建立哈希索引。

为热点页建立哈希索引，来提高查询效率，提升辅助索引的查询性能

哈希索引只能用来搜索等值的查询，如`select * from table where index_col="xxx";`而对于其他查找类型，如范围查找不能使用哈希索引，




##  4. 预读


#### 4.1 问题


线性预读

随机预读

