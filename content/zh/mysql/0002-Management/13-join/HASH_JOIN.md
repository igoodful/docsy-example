---
title: hash join
description: hash join（mysql-8.0.18）
date: 2024-11-04
weight: 20000
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{<alert color="danger" title="注意" >}}

默认情况下，MySQL 会尽可能地使用哈希连接。

哈希连接也适用于笛卡尔积 - 即未指定连接条件时

join_buffer_size

{{</alert>}}

MySQL 对任何查询都采用哈希连接，对于这些查询，每个连接都有一个等值连接条件，并且没有可以应用于任何连接条件的索引


## Hash Join

Hash Join 是针对等值连接场景的优化方法，其基本原则是将驱动表的数据加载到内存中，并构建哈希表，这样只需遍历一次非驱动表，然后通过哈希查找在哈希表中寻找匹配的行，就能完成连接操作。




## build 构建阶段


## probe 探测阶段













