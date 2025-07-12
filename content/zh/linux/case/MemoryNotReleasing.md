---
title: MySQL不释放内存
description: >
  libjemalloc版本问题导致
date: 2023-10-17
weight: 4
---

{{< alert >}}



{{< /alert >}}



{{< alert title="配置信息">}}

| 硬件名称       | 配置                |
|:---------------|:--------------------|
| Arch           | aarch64             |
| OS             | Kylin-v10           |
| Kernel Version | 4.19.90-23.29.v2101 |
| Hugepagesize   | 524288 KB           |
| CPU            | 64C                 |
| Memory         | 256G                |
| Space          | 4T                  |
| MySQL Version  | 8.0.26              |

{{< /alert >}}


### free

```sql
[xxx ~]# free -g
              total        used        free      shared  buff/cache   available
Mem:          254Gi       209Gi       1.5Gi       9.0Mi        42Gi        42Gi
Swap:          15Gi       2.4Gi        13Gi

```

### top
{{< alert title="top" color="secondary" >}}
- 先执行top

- 再按shift+M 进行排序
{{< /alert >}}
```sql
[xxx ~]# top
  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
 3495 mysql     20   0 463.3.1g 196.6g 34112 S 111.3 77.4  27:47.35 mysqld
 1001 mysql     20   0 4631084  57980   4612 S   0.0  0.7  27:44.97 java
 2490 root      20   0 1379204  23572    904 S   0.0  0.3   1:57.46 dockerd

```


### 查看MySQL的IBP实际占用内存
```sql
select format_bytes(sum(variable_value) * @@innodb_page_size)

from performance_schema.global_status

where variable_name in ('innodb_buffer_pool_pages_data', 'innodb_buffer_pool_pages_misc');

127.50GiB
```

### 检查透明大页是否关闭
{{< alert title="透明大页" color="secondary" >}}
- always madvise [never]  表示关闭

- [always] madvise never  表示开启
{{< /alert >}}
```sql
cat /sys/kernel/mm/transparent_hugepage/enabled
[xxx ~]# cat /sys/kernel/mm/transparent_hugepage/enabled
always madvise [never]

```


### 检查numa是否关闭
{{< alert title="numa" color="secondary" >}}
- available: 1 nodes (0)  表示关闭

- available: xx nodes (0)  表示开启
{{< /alert >}}
```sql
[xxx ~]# numactl --hardware
available: 1 nodes (0)
```

### 检查MySQL使用的内存分配器

```sql
ps aux|grep mysqld

lsof -p xxx |grep libjemalloc.so
```

### 检查MySQL使用的内存分配器版本与md5
```sql
strings /usr/lib/libjemalloc.so |grep libjemalloc

md5sum /usr/lib/libjemalloc.so
```

