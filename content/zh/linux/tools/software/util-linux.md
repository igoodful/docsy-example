---
title: util-linux
description: util-linux
date: 2025-03-22
weight: 102
viz: true

---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


{{<note>}}
<!---->

- https://github.com/util-linux/util-linux/archive/refs/tags/v2.40.4.tar.gz



{{</note>}}


## 1. 依赖



## 2. 内容


### 2.1 bin

```viz-dot
digraph "util-linux" {
node [
        fontsize = "12"
];
    "util-linux" [ label = "util-linux", shape = octagon ];
    "chacl" [ label = "chacl", shape = octagon ];
    "getfacl" [ label = "getfacl", shape = octagon ];
    "setfacl" [ label = "setfacl", shape = octagon ];
    "util-linux.so" [ label = "util-linux.so", shape = octagon ];
    "acl/util-linux.h" [ label = "acl/util-linux.h", shape = octagon ];
    "sys/acl.h" [ label = "sys/acl.h", shape = octagon ];

    "util-linux" -> "chacl";
    "util-linux" -> "getfacl";
    "util-linux" -> "setfacl";
    "util-linux" -> "util-linux.so";
    "util-linux" -> "acl/util-linux.h";
    "util-linux" -> "sys/acl.h";
}
```




## 3. 安装


#### 3.1 yum安装
```bash


```




#### 3.2 源码安装

- CFLAGS: -std=c99"

```bash


```

