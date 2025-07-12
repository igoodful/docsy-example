---
title: cmake文档
description: cmake doc
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
- [cmake-examples](https://github.com/ttroy50/cmake-examples/tree/master)
- [use_cmake](https://github.com/HuPengsheet/use_cmake)
- [cmake-demo](https://github.com/wzpan/cmake-demo)


**组件**：



{{%/pageinfo%}}



{{<note>}}
<!---->
依赖如下：
- gcc
- make
- glic

{{</note>}}


{{<note>}}
<!---->
选项：
- -S <path-to-source>
- -B <path-to-build>
- -D 变量>:<类型>=<值>
- \-\-strace
-

执行

cmake --build
{{</note>}}


```bash
cmake -S src -B build
```

### project
> 项目信息

```bash
project(project_name VERSION 1.2)
```


### message
> 打印信息
```cmake
message("xxx")  # 默认以STATUS模式输出消息
message(STATUS "This is a status message.")
message(WARNING "This is a warning message.")
message(FATAL_ERROR "This is a fatal error message.")
```

### add_subdirectory
> 添加子目录

## 包依赖管理
> 主要是头文件和库


### aux_source_directory
> 作用：查找当前目录下的所有源文件，并将名称保存到指定变量
>
> 语法：`aux_source_directory(<dir> <variable>)`
>
> 举例：`aux_source_directory(src SRC)`
>
说明：将src下面所有cpp源文件的名字，记录到变量`SRC`中，然后再通过`${SRC}`引用变量，避免我们一个个手动写上所有的源文件名称。同时我们也可以使用`message(STATUS ${SRC})`，将变量的值打印出来，下面是打印结果。

### find_package




### add_library
> 创建静态库和动态库



### target_link_libraries
> 添加链接库（静态库和动态库）



### add_executable
> 创建目标：可执行文件


### install
> 指定安装规则


## configure_file
[参考](https://blog.51cto.com/u_15357586/5226081)
> 生成头文件，不适合生成源文件。将 CMakeLists.txt 文件中的变量转变为 C/C++ 中可识别的宏定义，然后存入另一个文件中。
> 若输出文件为xxx.h，则输入文件通常为xxx.h.in
> configure file要放在变量定义之后(验证发现OPTION定义的变量可以在configure file之后)

```cmake
configure_file(<input> <output>
               [NO_SOURCE_PERMISSIONS | USE_SOURCE_PERMISSIONS |
                FILE_PERMISSIONS <permissions>...]
               [COPYONLY] [ESCAPE_QUOTES] [@ONLY]
               [NEWLINE_STYLE [UNIX|DOS|WIN32|LF|CRLF] ])
```

1. `hello.h.in`:
```c
#define projName_MAJOR_VERSION @projName_VERSION_MAJOR@
#define projName_MINOR_VERSION @projName_VERSION_MINOR@
#define PROJ_NAME "@PROJECT_NAME@"
#define MAX_NUM 100
```


2. `main.cpp`:
```c
#include <iostream>
#include "hello.h"

using namespace std;

int main() {
	cout << "Hello cmake!" << endl;
	cout << "project name: " << PROJ_NAME << endl;
	cout << "project major version: " << projName_MAJOR_VERSION << endl;
	cout << "project minor version: " << projName_MINOR_VERSION << endl;
	cout << "MAX NUM: " << MAX_NUM << endl;
	return 0;
}
```

3. `CMakeLists.txt`:
```cmake
cmake_minimum_required(VERSION 3.10)

project(Hello VERSION 1.0
	DESCRIPTION "Very Nice Project!"
	LANGUAGES CXX
	)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED True)

add_executable(hello main.cpp)

configure_file(hello.h.in hello.h)
target_include_directories(hello PUBLIC
	"${PROJECT_BINARY_DIR}"
)
```


[Findxxx.cmake](https://cmake.org/cmake/help/latest/manual/cmake-modules.7.html)
#### FindOpenSSL.cmake


- OPENSSL_FOUND
- OPENSSL_INCLUDE_DIR       --头文件目录
- OPENSSL_CRYPTO_LIBRARY    --加密库
- OPENSSL_CRYPTO_LIBRARIES  --加密库及其依赖项
- OPENSSL_SSL_LIBRARY       --SSL 库
- OPENSSL_SSL_LIBRARIES     --SSL 库及其依赖项
- OPENSSL_LIBRARIES
- OPENSSL_VERSION
- OPENSSL_APPLINK_SOURCE
- OPENSSL_ROOT_DIR          --设置为OpenSSL安装的根目录




#### FindCurses.cmake
- CURSES_FOUND
- CURSES_INCLUDE_DIRS
- CURSES_LIBRARIES
- CURSES_CFLAGS













