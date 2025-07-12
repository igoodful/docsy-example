---
title: link_directories
description: 添加到库搜索路径中
date: 2023-11-17
weight: 30000


---

{{< alert >}}
可以通过 link_directories() 命令来指定静态库的搜索路径
{{< /alert >}}

```cmake
link_directories(${PROJECT_SOURCE_DIR}/libs)
```


