---
title: 磁盘IO利用率高
description: 磁盘IO利用率高
date: 2023-10-12
weight: 60000


---

{{< alert >}}
最近一段时间线上某实例频繁报警CPU飙高，每次都捕获到同一种 SQL
{{< /alert >}}


### 一、环境信息

{{< note >}}

| 名称          | 配置       |
|:--------------|:-----------|
| Arch          | x64        |
| OS            | CentOS 7.9 |
| CPU           | 32C        |
| Memory        | 128G       |
| Space         | 2.6T       |
| MySQL Version | 8.0.26     |

{{< /note >}}



## 解决问题

### 查看当前所有磁盘的繁忙度
```sql
iostat -m -x 1
```

### 查看压力大的磁盘块设备的io读写压力
```sql
iostat -d /dev/sdc -m -x 1 3
```

### 分析/dev/sdc磁盘中哪个进程占用的io比较高
```sql
pidstat -d 1 5
```


### 分析该进程是否有线程，分析哪些线程占用的io高
```sql
pidstat -d -t -p 9674 1
```


```sql
perf trace -t 9669 -o /tmp/ooo.perftrace.log
```


















