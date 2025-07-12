---
title: dm
description: 将MySQL/MariaDB 数据迁移到TiDB的工具
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

TiDB Data Migration

将MySQL/MariaDB 数据迁移到TiDB的工具，支持全量数据的迁移和增量数据的复制

{{< /alert >}}


## dm


基本信息：

- TiDB DM 的输入：MySQL/MariaDB
- TiDB DM 的输出：TiDB 集群
- 适用 TiDB 版本：所有版本
- Kubernetes 支持：使用 TiDB Operator 在 Kubernetes 上部署 TiDB DM。


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


## TiCDC


#### 在线部署`tiup`

```bash
curl --proto '=https' --tlsv1.2 -sSf https://tiup-mirrors.pingcap.com/install.sh | sh

source ~/.bash_profile

which tiup

tiup cluster

tiup update --self && tiup update cluster

```





















