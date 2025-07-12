---
title: br
description: 一个对 TiDB 进行分布式备份和恢复的工具，可以高效地对大数据量的 TiDB 集群进行数据备份和恢复。
date: 2025-05-24
weight: 100
viz: true
---


<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;

$ \rightleftharpoons $

{{< alert >}}

Backup & Restore (BR) 是一个对 TiDB 进行分布式备份和恢复的工具，可以高效地对大数据量的 TiDB 集群进行数据备份和恢复。

{{< /alert >}}


## br


TiDB Lightning 的输入：
- Dumpling 输出文件
- 其他格式兼容的 CSV 文件
- 从 Aurora、Hive 或 Snowflake 导出的 Parquet 文件
- 适用 TiDB 版本：v2.1 及以上
- Kubernetes 支持：使用 TiDB Lightning 快速恢复 Kubernetes 上的 TiDB 集群数据


```viz-dot
digraph "deploy" {
        rankdir = TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
        ];

        "deploy" -> "set names utf8"[label="set names utf8"];
        "deploy" -> "记录用户登录信息";
}
```


## br


#### 在线部署`tiup`

```bash
curl --proto '=https' --tlsv1.2 -sSf https://tiup-mirrors.pingcap.com/install.sh | sh

source ~/.bash_profile

which tiup

tiup cluster

tiup update --self && tiup update cluster

```





















