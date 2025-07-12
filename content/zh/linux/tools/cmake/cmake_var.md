---
title: cmake变量
description: cmake_var
date: 2023-11-06
weight: 30000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{%pageinfo%}}
- [cmake](https://cmake.org/download/)
- [cmake download](https://cmake.org/files)

{{%/pageinfo%}}



{{<note>}}
<!---->
目录作用域的启用一般是在父目录下的CmakeList.txt中有add_subdirectory(“子目录路径”)指令，而在子目录的CMakeLists.txt会将父目录的所有变量拷贝到当前CMakeLists.txt中，当前CMakeLists.txt中的变量的作用域仅在当前子目录有效
{{</note>}}

### 一、环境变量

### 二、缓存变量

### 三、普通变量






列举一些常见的CMake内置变量，需要注意的是内置变量都是CMAKE开头

- CMAKE_SOURCE_DIR: CMakeLists.txt所在的顶级源代码目录的路径。
- CMAKE_BINARY_DIR: 构建目录的路径，即执行cmake命令时生成的Makefile或其他构建系统文件所在的目录。
- CMAKE_CURRENT_SOURCE_DIR: 当前处理的CMakeLists.txt所在的目录的路径。
- CMAKE_CURRENT_BINARY_DIR: 当前处理的CMakeLists.txt生成的目标文件所在的目录的路径。
- CMAKE_CURRENT_LIST_FILE: 当前正在处理的CMakeLists.txt的完整路径和文件名。
- CMAKE_CURRENT_LIST_DIR: 当前正在处理的CMakeLists.txt所在的目录的路径。
- CMAKE_MODULE_PATH: 用于指定额外的CMake模块的路径。
- CMAKE_INCLUDE_PATH: 用于指定额外的包含文件的路径。
- CMAKE_LIBRARY_PATH: 用于指定额外的库文件的路径。
- CMAKE_SYSTEM: 当前操作系统的名称。
- CMAKE_SYSTEM_NAME: 当前操作系统的名称，与CMAKE_SYSTEM相同。
- CMAKE_SYSTEM_VERSION: 当前操作系统的版本号。
- CMAKE_C_COMPILER: C编译器的路径。
- CMAKE_CXX_COMPILER: C++编译器的路径。
- CMAKE_BUILD_TYPE: 构建类型，如Debug、Release等。
- CMAKE_INSTALL_PREFIX: 安装目录的路径。



### CMAKE_BUILD_TYPE
另外有一些内置变量是没有值的，以CMAKE_BUILD_TYPE为例。CMAKE_BUILD_TYPE是控制构建类型的，有这些选项值，

- **Debug**：用于开发和调试的构建类型。此模式通常包含调试符号信息，并启用额外的调试功能，例如断言检查和运行时错误检查。此模式下通常关闭优化，以便更容易进行调试。

- **Release**：用于发布最终产品的构建类型。此模式通常启用各种优化选项，以提高代码的执行速度和减小最终产品的大小。同时，此模式通常不包含调试符号信息，以减小可执行文件的大小。

- **RelWithDebInfo**：结合了Release和Debug的特点。此模式启用了优化选项，同时保留了调试符号信息。这样可以在需要进行调试时获得更多的信息。

- **MinSizeRel**：用于最小化生成的可执行文件大小的构建类型。此模式启用了各种优化选项，以减小最终产品的大小。通常不包含调试符号信息。

在我们默认什么都没做的的情况下，CMAKE_BUILD_TYPE为空，CMake默认编译为Debug模式，但是通常我们发布在github上时我们是Release版本，所以我们经常会在别人的CMakeLists.txt文件中看见如下代码片段
```cmake
if(NOT CMAKE_BUILD_TYPE)
	set(CMAKE_BUILD_TYPE "Release")
endif()
```
即如果用户没有指定这个变量，则把CMAKE_BUILD_TYPE设置为Release，编译的时候就会为Release版本，而不是Debug版本。

```bash
cmake .. -DCMAKE_BUILD_TYPE=MinSizeRel
```
同时用户也可以在执行cmake命令的时候，加-DCMAKE_BUILD_TYPE，来手动指定构建的类型。








```cmake
#CMakeLists.txt

cmake_minimum_required(VERSION 3.10)
project(course_01 VERSION 0.0.1)

set(CMAKE_CXX_STANDARD 11)  # 将 C++ 标准设置为 C++ 11
set(CMAKE_CXX_STANDARD_REQUIRED ON)  # C++ 11 是强制要求，不会衰退至低版本
set(CMAKE_CXX_EXTENSIONS OFF)  # 禁止使用编译器特有扩展

if(NOT CMAKE_BUILD_TYPE)
	message(WARNING "NOT SET CMAKE_BUILD_TYPE")
    set(CMAKE_BUILD_TYPE "Release")
endif()

message(STATUS "CMAKE_SOURCE_DIR: ${CMAKE_SOURCE_DIR}")
message(STATUS "CMAKE_BINARY_DIR: ${CMAKE_BINARY_DIR}")
message(STATUS "CMAKE_CURRENT_SOURCE_DIR: ${CMAKE_CURRENT_SOURCE_DIR}")
message(STATUS "CMAKE_CURRENT_BINARY_DIR: ${CMAKE_CURRENT_BINARY_DIR}")
message(STATUS "CMAKE_CURRENT_LIST_FILE: ${CMAKE_CURRENT_LIST_FILE}")
message(STATUS "CMAKE_CURRENT_LIST_DIR: ${CMAKE_CURRENT_LIST_DIR}")
message(STATUS "CMAKE_MODULE_PATH: ${CMAKE_MODULE_PATH}")
message(STATUS "CMAKE_INCLUDE_PATH: ${CMAKE_INCLUDE_PATH}")
message(STATUS "CMAKE_LIBRARY_PATH: ${CMAKE_LIBRARY_PATH}")
message(STATUS "CMAKE_SYSTEM: ${CMAKE_SYSTEM}")
message(STATUS "CMAKE_SYSTEM_NAME: ${CMAKE_SYSTEM_NAME}")
message(STATUS "CMAKE_SYSTEM_VERSION: ${CMAKE_SYSTEM_VERSION}")
message(STATUS "CMAKE_C_COMPILER: ${CMAKE_C_COMPILER}")
message(STATUS "CMAKE_CXX_COMPILER: ${CMAKE_CXX_COMPILER}")
message(STATUS "CMAKE_BUILD_TYPE: ${CMAKE_BUILD_TYPE}")
message(STATUS "CMAKE_INSTALL_PREFIX: ${CMAKE_INSTALL_PREFIX}")


message(FATAL_ERROR "This is a fatal error message.")

set(SRC ${CMAKE_CURRENT_SOURCE_DIR}/src/main.cpp)
add_executable(run ${SRC})

```








