---
title: add_library
description: add_library
date: 2023-11-17
weight: 30000


---

{{< alert >}}


{{< /alert >}}

```cmake
# 添加静态库

add_library(xx STATIC src/xx.cpp)

# 添加共享库

add_library(xx SHARED src/xx.cpp)

# 添加模块库

add_library(xx MODULE src/xx.cpp)

# 添加对象库

add_library(xx OBJECT src/xx.cpp)

```
