---
title: texinfo
description: texinfo
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

- https://ftp.gnu.org/gnu/texinfo/texinfo-7.2.tar.gz
- https://mirrors.tuna.tsinghua.edu.cn/gnu/texinfo/texinfo-7.2.tar.gz


{{</note>}}


## 1. 依赖



## 2. 内容


### 2.1 bin

```viz-dot
digraph "texinfo" {
node [
        fontsize = "12"
];
    "texinfo" [ label = "texinfo", shape = octagon ];
    "chacl" [ label = "chacl", shape = octagon ];
    "getfacl" [ label = "getfacl", shape = octagon ];
    "setfacl" [ label = "setfacl", shape = octagon ];
    "texinfo.so" [ label = "texinfo.so", shape = octagon ];
    "acl/texinfo.h" [ label = "acl/texinfo.h", shape = octagon ];
    "sys/acl.h" [ label = "sys/acl.h", shape = octagon ];

    "texinfo" -> "chacl";
    "texinfo" -> "getfacl";
    "texinfo" -> "setfacl";
    "texinfo" -> "texinfo.so";
    "texinfo" -> "acl/texinfo.h";
    "texinfo" -> "sys/acl.h";
}
```




## 3. 安装


#### 3.1 yum安装
```bash


```




#### 3.2 源码安装

```bash


```

