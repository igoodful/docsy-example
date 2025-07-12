---
title: find系列
description: find_file find_path find_library find_package find_program
date: 2023-11-06
weight: 30000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{%pageinfo%}}
- [官网](https://cmake.org/cmake/help/latest/command/find_file.html)
{{%/pageinfo%}}

## 一、find_file
```cmake
find_file (
           <VAR>
           name | NAMES name1 [name2 ...]
           [HINTS [path | ENV var]... ]
           [PATHS [path | ENV var]... ]
           [REGISTRY_VIEW (64|32|64_32|32_64|HOST|TARGET|BOTH)]
           [PATH_SUFFIXES suffix1 [suffix2 ...]]
           [VALIDATOR function]
           [DOC "cache documentation string"]
           [NO_CACHE]
           [REQUIRED]
           [NO_DEFAULT_PATH]
           [NO_PACKAGE_ROOT_PATH]
           [NO_CMAKE_PATH]
           [NO_CMAKE_ENVIRONMENT_PATH]
           [NO_SYSTEM_ENVIRONMENT_PATH]
           [NO_CMAKE_SYSTEM_PATH]
           [NO_CMAKE_INSTALL_PREFIX]
           [CMAKE_FIND_ROOT_PATH_BOTH |
            ONLY_CMAKE_FIND_ROOT_PATH |
            NO_CMAKE_FIND_ROOT_PATH]
          )

find_file(NB NAMES nb.h PATHS ${CMAKE_SOURCE_DIR} NO_CACHE REQUIRED)

if(NB-NOTFOUND)

```




{{<alert>}}

- VAR：报错找到的文件绝对路径
  - 如果找到文件的完整路径，结果将存储在变量中，并且不会重复搜索，除非清除变量
  - 如果没有找到任何内容，结果将是<VAR>-NOTFOUND
- name：待找文件名称
- PATHS：不会在指定路径下进行递归查找
- DOC：指定<VAR>缓存项的文档字符串
- NO_CACHE：指定搜索结果将存储在普通变量中，默认保存在缓存中。如果在调用之前已经设置了变量（作为普通变量或缓存变量），则不会进行搜索。建议不要使用
- REQUIRED：如果未找到任何内容，请停止处理并显示错误消息，否则下次使用同一变量调用 find_file 时将再次尝试搜索
- NO_DEFAULT_PATH：指定不会将其他路径添加到搜索中，默认还会在以下路径查找：
  - <PackageName>_ROOT，<PACKAGENAME>_ROOT，<PackageName>_ROOT，<PACKAGENAME>_ROOT
  -
  -
  -

{{</alert>}}



## 二、find_path




## 三、find_package

## 四、find_program


## 五、find_library
```bash
# 添加静态库的搜索路径,

link_directories(/usr/local/lib)

# 使用静态库的名称链接静态库

target_link_libraries(app xxx)
```
/usr/local/lib是静态库的搜索路径，xxx是静态库的名称。通过使用 link_directories() 命令，可以避免在 target_link_libraries() 中指定完整的静态库路径。











