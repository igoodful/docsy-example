---
title: 仓库管理
description: repository
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


{{< /alert >}}




## 8字段
```viz-dot
digraph "repository" {
        rankdir=TB;
        edge [
                fontsize="12"
        ];
        node [
                shape = octagon;
                fontsize = "12"
        ];
        "repository" -> "login";
        "repository" -> "logout";
        "repository" -> "tag";
        "repository" -> "push";
        "repository" -> "search";




}
```