---
title: 系统管理
description: system
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
digraph "system" {
        rankdir=TB;
        edge [
                fontsize="12"
        ];
        node [
                shape = octagon;
                fontsize = "12"
        ];
        "system" -> "version";
        "system" -> "info";



}
```


## 查看磁盘占用情况

```bash
docker system df

docker system df -v
```











