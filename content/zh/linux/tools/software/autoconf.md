---
title: autoconf
description: autoconf
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



{{</note>}}


## 1. 依赖



## 2. 内容


### 2.1 bin

```viz-dot
digraph "autoconf-package" {
node [
        fontsize = "12"
];
    "autoconf-package" [ label = "autoconf-package", shape = octagon ];
    "autoconf" [ label = "autoconf", shape = octagon ];
    "autoheader" [ label = "autoheader", shape = octagon ];
    "autom4te" [ label = "autom4te", shape = octagon ];
    "autoreconf" [ label = "autoreconf", shape = octagon ];
    "autoscan" [ label = "autoscan", shape = octagon ];
    "autoupdate" [ label = "autoupdate", shape = octagon ];
    "ifnames" [ label = "ifnames", shape = octagon ];

    "autoconf-package" -> "autoconf";
    "autoconf-package" -> "autoheader";
    "autoconf-package" -> "autom4te";
    "autoconf-package" -> "autoreconf";
    "autoconf-package" -> "acl/autoconf-package.h";
    "autoconf-package" -> "autoupdate";
    "autoconf-package" -> "ifnames";
    "autoconf-package" -> "autoscan";
}
```




## 3. 安装


#### 3.1 yum安装
```bash
2025-03-24T12:24:30 [root@RDS-146 /glc/autoconf/app/autoconf-2.69] # tree
.
├── bin
│   ├── autoconf
│   ├── autoheader
│   ├── autom4te
│   ├── autoreconf
│   ├── autoscan
│   ├── autoupdate
│   └── ifnames


```




#### 3.2 源码安装

```bash


```

