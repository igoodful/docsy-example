---
title: 组复制要求
description: group replica
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


1. 存储引擎只能是innodb

disabled_storage_engines="MyISAM,BLACKHOLE,FEDERATED,ARCHIVE,MEMORY"


2. 每张表必须有主键或非空唯一键


sql_require_primary_key = ON


3. 网络性能要求较高


4. 组复制成员个数最大值为9


5. 事务大小必须严格控制




