---
title: ticdc
description: TiDB增量数据同步工具
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

{{< /alert >}}


## ticdc


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





















