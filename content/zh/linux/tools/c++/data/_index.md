---
title: 2. 数据结构
description: base
date: 2023-10-30
weight: 10000


viz: true
---

{{< alert >}}


{{< /alert >}}



```viz-dot
digraph "数据类型" {
        rankdir=TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "数据类型" -> "内置数据类型";
        "内置数据类型" -> "bool";
        "内置数据类型" -> "char";
        "内置数据类型" -> "int";
        "内置数据类型" -> "float";
        "数据类型" -> "内存大小";
        "my_umask=0640" -> "my_umask_dir=0750";
        "my_umask_dir=0750" -> "UMASK";
        "UMASK" -> "UMASK_DIR";
        "UMASK_DIR" -> "my_thread_global_init()" ;
        "my_thread_global_init()" -> "my_thread_init()" ;
        "my_thread_init()" -> "HOME" ;
        "HOME" -> "home_dir" ;
        "home_dir" -> "MyFileInit()" ;

}
```


































