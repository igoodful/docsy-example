---
title: namespace
description: 名称空间
date: 2023-11-17
weight: 30000


viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

- 包含文件：由于它们被包含在其他文件中
- 头文件：由于它们被包含在文件起始处
- c头文件以.h结尾，c++头文件没有拓展名，且可包含名称空间
- c头文件转换为c++头文件：math.h》cmath

{{< /alert >}}


{{<alert>}}

1. xxx.h和xxx.c都存在，若xxx.c包含xxx.h，则必须先包含xxx.h，再包含其他头文件
2. 查看预处理后的文件：`g++ -E my.cc -o my.i`

{{</alert>}}



```viz-dot
digraph "namespace" {
        rankdir=TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
        ];
        "namespace" -> "避免名称冲突\n sb::func\n nb::func";
        "namespace" -> "1.包含头文件 \n #include<zzz>";
        "namespace" -> "2.使用";
        "2.使用" -> "函数外\n using namespace xxx;";
        "2.使用" -> "函数内\n using namespace xxx;";
        "2.使用" -> "using xxx::yyy;";
        "2.使用" -> "xxx::yyy";



}
```






## using

















