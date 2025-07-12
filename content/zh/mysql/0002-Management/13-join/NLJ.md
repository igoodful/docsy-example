---
title: NLJ
description:  simple nested-loop join
date: 2024-11-04
weight: 20000
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{<alert color="danger" title="注意" >}}



{{</alert>}}


在MySQL中，不管Join使用的是NLJ还是BNL总是应该使用小表做驱动表。更准确地说，在决定哪个表做驱动表的时候，应该是两个表按照各自的条件过滤，过滤完成之后，计算参与join的各个字段的总数据量，数据量小的那个表，就是小表，应该作为驱动表。

另外应当尽量避免使用BNL算法，如果确认优化器会使用BNL算法，就需要做优化。优化的常见做法是，给被驱动表的join字段加上索引，把BNL算法转成BKA算法。对于不好在索引的情况，可以基于临时表的改进方案，提前过滤出小数据添加索引。




