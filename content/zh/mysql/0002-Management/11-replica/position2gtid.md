---
title: 传统复制转gtid复制
description: gtid
date: 2023-10-25
weight: 20000


---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;





```sql

set gtid_next='aaa-bbb-ccc-ddd:n';
begin;
commit;
set gtid_next='automatic';

```


#### 1. enforce_gtid_consistency=warn

让服务器在正常工作负载下运行一段时间并监控日志。如果此步骤导致日志中出现任何警告，请调整应用程序，使其仅使用与 GTID 兼容的功能并且不会生成任何警告。

{{<alert color="danger" title="注意" >}}

- 在每台服务器上执行
- 在进行下一步之前，您必须确保错误日志中没有生成任何警告

{{</alert>}}


```sql
set @@global.enforce_gtid_consistency = warn;

```


#### 2. enforce_gtid_consistency=on
在每台服务器上执行

```sql
set @@global.enforce_gtid_consistency = on;

```

#### 3. gtid_mode=off_permissive
在每台服务器上执行

```sql
set @@global.gtid_mode = off_permissive;

```


#### 4. gtid_mode=on_permissive
在每台服务器上执行

```sql
set @@global.gtid_mode = on_permissive;

```


#### 5. ongoing_anonymous_transaction_count


{{<alert color="danger" title="注意" >}}

- 在每台服务器上执行
- 在每个服务器上，等待状态变量`ongoing_anonymous_transaction_count`为零
- 在副本上，理论上有可能显示零，然后再次显示非零。这不是问题，显示一次零就足够了

{{</alert>}}


```sql
show status like 'ongoing_anonymous_transaction_count';

```


#### 6. gtid_mode=on
在每台服务器上执行

```sql
set @@global.gtid_mode = on;

```



#### 7. 持久化gtid配置

在每个服务器上，将`gtid_mode=ON`和 `enforce_gtid_consistency=ON`添加到 `my.cnf`

- `my.cnf`
```toml
[mysqld]
gtid_mode=ON
enforce_gtid_consistency=ON
```






#### 8. change master

```sql
stop slave [for channel 'channel'];
change master to master_auto_position = 1 [for channel 'channel'];
start slave [for channel 'channel'];

or from mysql 8.0.22 / 8.0.23:
stop replica [for channel 'channel'];
change replication source to source_auto_position = 1 [for channel 'channel'];
start replica [for channel 'channel'];
```
