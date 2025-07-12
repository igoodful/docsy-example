---
title: USE INDEX，FORCE INDEX，IGNORE INDEX
description: USE INDEX，FORCE INDEX，IGNORE INDEX
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



## FROM table_name USE INDEX (index_name)

建议优化器优先使用指定的索引，但不强制。


## FROM table_name FORCE INDEX (index_name)

强制优化器使用指定的索引，即使这样做效率较低。


## FROM table_name IGNORE INDEX (index_name)

建议优化器忽略使用指定的索引，但不强制。















