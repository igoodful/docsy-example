---
title: while语句
description: 循环
date: 2023-10-30
weight: 500


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

{{< /alert >}}


```viz-dot
digraph "my_init" {
        rankdir=TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "my_init" -> "my_init_done";
        "my_init_done" -> "my_umask=0640";
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

## 算术运算

## 逻辑运算

## 其他


