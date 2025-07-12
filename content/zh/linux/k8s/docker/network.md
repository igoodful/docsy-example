---
title: 网络管理
description: network
date: 2023-10-26
weight: 20000


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

- [docker](https://docs.docker.com/reference/cli/docker/)

{{< /alert >}}




## 8字段
```viz-dot
digraph "network" {
        rankdir=TB;
        edge [
                fontsize="12"
        ];
        node [
                shape = octagon;
                fontsize = "12"
        ];
        "network" -> "connect";
        "network" -> "create";
        "network" -> "disconnect";
        "network" -> "ls";
        "network" -> "inspect";
        "network" -> "prune";
        "network" -> "rm";




}
```