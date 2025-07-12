---
title: 镜像管理
description: images
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
- https://graphviz.org/download/
- https://hub.docker.com/explore

{{< /alert >}}


## 图解

```viz-dot
digraph "images" {
rankdir=TB;
edge [
        fontsize="12"
];
node [
        shape = octagon;
        fontsize = "12"
];
"images" -> "search";
"images" -> "images";
"images" -> "pull";
"images" -> "history";
"images" -> "rmi";
"images" -> "tag";
"images" -> "inspect";
"images" -> "build";
"images" -> "commit";
"images" -> "load";
"images" -> "save";

}
```

