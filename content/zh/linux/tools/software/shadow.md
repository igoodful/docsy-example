---
title: shadow
description: shadow
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

- https://github.com/shadow-maint/shadow/archive/refs/tags/4.17.4.tar.gz


{{</note>}}


## 1. 依赖



## 2. 内容


### 2.1 bin

```viz-dot
digraph "libshadow" {
node [
        fontsize = "12"
];
    "libshadow" [ label = "libshadow", shape = octagon ];
    "chshadow" [ label = "chshadow", shape = octagon ];
    "getfshadow" [ label = "getfshadow", shape = octagon ];
    "setfshadow" [ label = "setfshadow", shape = octagon ];
    "libshadow.so" [ label = "libshadow.so", shape = octagon ];
    "shadow/libshadow.h" [ label = "shadow/libshadow.h", shape = octagon ];
    "sys/shadow.h" [ label = "sys/shadow.h", shape = octagon ];

    "libshadow" -> "chshadow";
    "libshadow" -> "getfshadow";
    "libshadow" -> "setfshadow";
    "libshadow" -> "libshadow.so";
    "libshadow" -> "shadow/libshadow.h";
    "libshadow" -> "sys/shadow.h";
}
```




## 3. 安装


#### 3.1 yum安装
```bash


```




#### 3.2 源码安装

```bash


```

