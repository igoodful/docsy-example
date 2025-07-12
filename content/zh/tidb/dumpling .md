---
title: dumpling
description: 一个用于从 MySQL/TiDB 进行全量逻辑导出的工具
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

将 MySQL/MariaDB 数据迁移到 TiDB 的工具，支持全量数据的迁移和增量数据的复制

{{< /alert >}}


## dumpling


基本信息：

- Dumpling 的输入：MySQL/TiDB 集群
- Dumpling 的输出：SQL/CSV 文件
- 适用 TiDB 版本：所有版本
- Kubernetes 支持：尚未支持


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


## Dumpling


#### 在线部署`tiup`

```bash
curl --proto '=https' --tlsv1.2 -sSf https://tiup-mirrors.pingcap.com/install.sh | sh

source ~/.bash_profile

which tiup

tiup cluster

tiup update --self && tiup update cluster

```





















