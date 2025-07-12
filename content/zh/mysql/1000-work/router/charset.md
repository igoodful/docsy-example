---
title: router字符乱码
description: router字符乱码
date: 2023-10-31
weight: 10000


---

{{< alert >}}


{{< /alert >}}

## 字符乱码
```sql
dbscale show options like 'session-init-charset';
dbscale set global session_init_charset='';
dbscale flush connection pool;
dbscale flush all config to file;
dbscale show options like 'session-init-charset';

```




















