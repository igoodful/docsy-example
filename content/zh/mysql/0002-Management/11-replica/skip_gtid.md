---
title: 跳过gtid
description: gtid
date: 2023-10-25
weight: 20000


---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{<alert color="danger" title="注意" >}}



{{</alert>}}



## 跳过gtid


&#10060;

&#9989;


{{<alert color="danger" title="注意" >}}

- `gtid_next`


{{</alert>}}


```sql

set gtid_next='aaa-bbb-ccc-ddd:n';
begin;
commit;
set gtid_next='automatic';

```










