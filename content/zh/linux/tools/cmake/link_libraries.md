---
title: link_libraries
description: link_libraries
date: 2023-11-17
weight: 30000


---

{{< alert >}}
可以通过 link_directories() 命令来指定静态库的搜索路径
{{< /alert >}}

```bash
# 添加静态库的搜索路径,

link_directories(/usr/local/lib)

# 使用静态库的名称链接静态库

target_link_libraries(app xxx)
```
/usr/local/lib是静态库的搜索路径，xxx是静态库的名称。通过使用 link_directories() 命令，可以避免在 target_link_libraries() 中指定完整的静态库路径。

