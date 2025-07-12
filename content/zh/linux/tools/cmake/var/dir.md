---
title: 路径相关变量
description: dir
date: 2023-11-17
weight: 30000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

{{< /alert >}}


{{<alert>}}

{{</alert>}}


> 项目的顶层源目录为：`/root/mysql-server`
>


## PROJECT_SOURCE_DIR
包含最顶层 CMakeLists.txt文件的目录，即项目的源代码根目录
通过 project()命令激活的项目名称





## CMAKE_MODULE_PATH
> 指定额外的CMake模块搜索路径

```cmake
message(STATUS "Running cmake version ${CMAKE_VERSION}")

cmake_minimum_required(VERSION 3.5.1)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_SOURCE_DIR}/cmake)

```



## CMAKE_INCLUDE_CURRENT_DIR


## LIBRARY_OUTPUT_PATH
适用于动态库和静态库
```cmake

cmake_minimum_required(VERSION 3.0)

project(CALC)

include_directories(${PROJECT_SOURCE_DIR}/include)

file(GLOB SRC_LIST "${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp")

# 设置动态库/静态库生成路径
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)

# 生成动态库#
add_library(calc SHARED ${SRC_LIST})

# 生成静态库
add_library(calc STATIC ${SRC_LIST})

```


## CMAKE_LIBRARY_OUTPUT_DIRECTORY
> 库文件的输出目录



## CMAKE_RUNTIME_OUTPUT_DIRECTORY
> 可执行文件的输出目录




## CMAKE_SOURCE_DIR
项目的顶层源目录



## CMAKE_CURRENT_SOURCE_DIR



## CMAKE_CURRENT_LIST_DIR



## CMAKE_CURRENT_LIST_LINE



## CMAKE_CURRENT_BINARY_DIR
> 当前处理的CMakeLists.txt对应的构建目录





## CMAKE_BINARY_DIR


## PROJECT_BINARY_DIR
项目的构建目录，如果是外部构建，这将与 CMAKE_BINARY_DIR不同

## CMAKE_CURRENT_SOURCE_DIR
当前处理的CMakeLists.txt所在的目录









