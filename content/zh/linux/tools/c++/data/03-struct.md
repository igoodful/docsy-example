---
title: 结构体
description: struct
date: 2023-10-30
weight: 300


viz: true
---

{{< alert >}}


{{< /alert >}}



```viz-dot
digraph "const" {
        rankdir=TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "const" -> "指针变量";
        "指针变量" -> "不可通过指针变量修改指向的内容";
        "不可通过指针变量修改指向的内容" -> "指向const变量";
        "不可通过指针变量修改指向的内容" -> "指向常规变量";
        "指针变量" -> "指针变量不可指向其他地址";
        "const" -> "基本数据类型变量";

}
```






































