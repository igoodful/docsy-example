---
title: sync-diff-inspector
description: 一个用于校验 MySQL/TiDB 中两份数据是否一致的工具
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

sync-diff-inspector 是一个用于校验 MySQL/TiDB 中两份数据是否一致的工具。该工具还提供了修复数据的功能，可用于修复少量不一致的数据。

基本信息：

- sync-diff-inspector 的输入：TiDB、MySQL
- sync-diff-inspector 的输出：TiDB、MySQL
- 适用 TiDB 版本：所有版本

{{< /alert >}}


## sync-diff-inspector


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


## sync-diff-inspector


#### 在线部署`tiup`

```bash
curl --proto '=https' --tlsv1.2 -sSf https://tiup-mirrors.pingcap.com/install.sh | sh

source ~/.bash_profile

which tiup

tiup cluster

tiup update --self && tiup update cluster

```





















