---
title: 容器管理
description: container
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
digraph "container" {
        rankdir=TB;
        edge [
                fontsize="12"
        ];
        node [
                shape = octagon;
                fontsize = "12"
        ];
        "container" -> "create";
        "container" -> "run";
        "container" -> "export";
        "container" -> "import";
        "container" -> "stop";
        "container" -> "rm";
        "container" -> "start";
        "container" -> "restart";

        "container" -> "ps";
        "container" -> "top";
        "container" -> "stats";
        "container" -> "kill";
        "container" -> "exec";
        "container" -> "port";
        "container" -> "logs";
        "container" -> "inspect";
        "container" -> "cp";


}
```



