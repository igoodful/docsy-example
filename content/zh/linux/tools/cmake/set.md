---
title: set
description: 定义变量
date: 2023-11-17
weight: 300


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
指定C++标准

{{< /alert >}}

这些命令设置了C++标准版本为C++17，并且指定这一标准是必须的。
```cmake

set(CMAKE_CXX_STANDARD 17)

set(CMAKE_CXX_STANDARD_REQUIRED True)


```
{{<alert>}}
**普通变量的传播规则:**

1. 父子
2. 子不传父
3. 子加上`PARENT_SCOPE`则表示只给父CMakeLists.txt设置变量，而当前CMakeLists.txt中没有设置该变量



{{</alert>}}




## 普通变量

结构
```bash
[guolicheng@48 /db/storage/guolicheng/tmp]$ tree
.
├── CMakeLists.txt
└── son
    └── CMakeLists.txt

1 directory, 2 files
```


1. **CMakeLists.txt**
```cmake
cmake_minimum_required (VERSION 2.0)
project (father)

set(var0 value0_father)
set(var1 value1_father)
set(var2 value2_father)
set(var3 value3_father)
add_subdirectory (son)
message ("father---------------var0: ${var0}")
message ("father---------------var1: ${var1}")
message ("father---------------var2: ${var2}")
message ("father---------------var3: ${var3}")
message ("father---------------var4: ${var4}")

```

2. **son/CMakeLists.txt**
```cmake
cmake_minimum_required (VERSION 2.0)
project (son)

set(var1 value1_son)
set(var2 value2_son PARENT_SCOPE)
set(var3 value3_sonA)
set(var3 value3_sonB PARENT_SCOPE)
set(var4 value4_son PARENT_SCOPE)
message ("son---------------var0: ${var0}")
message ("son---------------var1: ${var1}")
message ("son---------------var2: ${var2}")
message ("son---------------var3: ${var3}")
message ("son---------------var4: ${var4}")

```

执行：`[guolicheng@48 /db/storage/guolicheng/tmp]$ cmake .`
```sql
-- The C compiler identification is GNU 10.4.0
-- The CXX compiler identification is GNU 10.4.0
-- Check for working C compiler: /db/storage/guolicheng/local/bin/gcc
-- Check for working C compiler: /db/storage/guolicheng/local/bin/gcc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working CXX compiler: /db/storage/guolicheng/local/bin/g++
-- Check for working CXX compiler: /db/storage/guolicheng/local/bin/g++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
son---------------var0: value0_father
son---------------var1: value1_son
son---------------var2: value2_father
son---------------var3: value3_sonA
son---------------var4:
father---------------var0: value0_father
father---------------var1: value1_father
father---------------var2: value2_son
father---------------var3: value3_sonB
father---------------var4: value4_son
-- Configuring done
-- Generating done
-- Build files have been written to: /db/storage/guolicheng/tmp

```


## 缓存变量





## 环境变量








