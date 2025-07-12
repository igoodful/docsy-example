---
title: acl
description: acl
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

- https://sourceforge.net/p/acl/
- https://github.com/acl-dev/acl/


{{</note>}}


## 1. 依赖



## 2. 内容


### 2.1 bin

```viz-dot
digraph "libacl" {
node [
        fontsize = "12"
];
    "libacl" [ label = "libacl", shape = octagon ];
    "chacl" [ label = "chacl", shape = octagon ];
    "getfacl" [ label = "getfacl", shape = octagon ];
    "setfacl" [ label = "setfacl", shape = octagon ];
    "libacl.so" [ label = "libacl.so", shape = octagon ];
    "acl/libacl.h" [ label = "acl/libacl.h", shape = octagon ];
    "sys/acl.h" [ label = "sys/acl.h", shape = octagon ];

    "libacl" -> "chacl";
    "libacl" -> "getfacl";
    "libacl" -> "setfacl";
    "libacl" -> "libacl.so";
    "libacl" -> "acl/libacl.h";
    "libacl" -> "sys/acl.h";
}
```




## 3. 安装


#### 3.1 yum安装
```bash


```




#### 3.2 源码安装

```bash


```

