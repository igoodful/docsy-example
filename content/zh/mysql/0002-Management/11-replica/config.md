---
title: 配置
description: config
date: 2023-10-25
weight: 20000


---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{<alert color="danger" title="注意" >}}

MySQL 实例最多支持 127 个撤消表空间，其中包括 MySQL 实例初始化时创建的两个默认撤消表空间。

{{</alert>}}



## 配置


&#10060;


| 变量参数                         | 不需重启? | 默认值 | 建议值   | 描述                              |
| :------------------------------- | :-------- | :----- | :------- | :-------------------------------- |
| `log_bin`                        | &#9989;   | `on`   | 默认值   | 开启binlog日志                    |
| `log_replica_updates`            | &#9989;   | `on`   | 默认值   | 级联拓扑需要开启，`A -> B -> C`   |
| `server_id`                      | &#9989;   | `1`    | 互不相同 |                                   |
| `server_uuid`                    | &#9989;   | `on`   | 互不相同 |                                   |
| `sync_binlog`                    | &#9989;   | `1`    |          |                                   |
| `innodb_flush_log_at_trx_commit` | &#9989;   | `1`    |          |                                   |
| `log_bin_basename`               | &#9989;   | `1`    |          |                                   |
| `log_bin_index`                  | &#9989;   | `1`    |          |                                   |
| `replica_net_timeout`            | &#9989;   | `60`   |          | 单位为秒，8.0.26 开始，1秒到365天 |






- `binlog_format`,在 MySQL 的未来版本中，您可能binlog_format会被完全删除，并且基于行的格式将成为 MySQL 使用的唯一日志格式。



```toml
log_bin = on
log_replica_updates
server_id = 1
sync_binlog=1
innodb_flush_log_at_trx_commit=1

```

#### log_bin

> binlog日志必须开启


#### log_replica_updates

{{<alert color="danger" title="注意" >}}

- `8.0.26`版本开始使用 `log_replica_updates`
- `8.0.26`版本之前使用`log_slave_updates`

{{</alert>}}


#### server_id

> server_id必须不一样



#### server_uuid

> server_uuid 必须不一样






