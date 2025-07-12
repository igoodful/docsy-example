---
title: cmake
description: cmake
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
- [cmake doc](https://cmake.org/cmake/help/latest/manual/cmake.1.html#cmdoption-cmake-trace-expand)

**依赖如下**：
- gcc
- make
- glic
- pkg-config [PKG_CONFIG_PATH]


**支持平台**：



{{%/pageinfo%}}



{{<note>}}
<!---->
选项详解

1. --trace


{{</note>}}

## 安装结果
- cmake
- ccmake
- cmake-gui
- cpack
- ctest

```bash
[glc@48 cmake-3.28.1-linux-x86_64]$ pwd
/db/storage/glc/local/cmake-3.28.1-linux-x86_64
[glc@48 cmake-3.28.1-linux-x86_64]$ tree bin
bin
├── ccmake
├── cmake
├── cmake3 -> cmake
├── cmake-gui
├── cpack
└── ctest

# 该文件是cmake脚本根目录
/db/storage/glc/local/cmake-3.28.1-linux-x86_64/share/cmake-3.28/Modules
```







### 一、安装
#### 1、源码安装
```bash
wget https://cmake.org/files/v3.28/cmake-3.28.0.tar.gz

tar -xzvf cmake-3.28.0.tar.gz && cd  cmake-3.28.0

./bootstrap --prefix=$HOME/local && make && make install

```

#### 2、脚本安装
```bash
sh cmake-3.28.1-linux-x86_64.sh
sh cmake-3.28.1-linux-x86_64.sh --prefix=$HOME/local
```

### 二、环境变量配置
> cat ~/.bashrc
```bash
export PATH=$HOME/local/cmake-3.28.1-linux-x86_64/bin:$PATH
```

### 三、环境变量配置生效
```sql
source ~/.bashrc
```



#### glibc版本
```sql
time.c.o uv-src-unix-posix-poll.c.o uv-src-unix-process.c.o uv-src-unix-signal.c.o uv-src-unix-stream.c.o uv-src-unix-tcp.c.o uv-src-unix-tty.c.o rhash-librhash-algorithms.c.o rhash-librhash-byte_order.c.o rhash-librhash-hex.c.o rhash-librhash-md5.c.o rhash-librhash-rhash.c.o rhash-librhash-sha1.c.o rhash-librhash-sha256.c.o rhash-librhash-sha3.c.o rhash-librhash-sha512.c.o jsoncpp-src-lib_json-json_reader.cpp.o jsoncpp-src-lib_json-json_value.cpp.o jsoncpp-src-lib_json-json_writer.cpp.o  -ldl -lrt -o cmake
/root/cmake-3.28.0/Bootstrap.cmk/cmake: /lib64/libstdc++.so.6: version `GLIBCXX_3.4.26' not found (required by /root/cmake-3.28.0/Bootstrap.cmk/cmake)
/root/cmake-3.28.0/Bootstrap.cmk/cmake: /lib64/libstdc++.so.6: version `GLIBCXX_3.4.20' not found (required by /root/cmake-3.28.0/Bootstrap.cmk/cmake)
/root/cmake-3.28.0/Bootstrap.cmk/cmake: /lib64/libstdc++.so.6: version `GLIBCXX_3.4.21' not found (required by /root/cmake-3.28.0/Bootstrap.cmk/cmake)
---------------------------------------------
Error when bootstrapping CMake:
Problem while running initial CMake
---------------------------------------------
[root@k8s-node-76 cmake-3.28.0]# ldd --version
ldd (GNU libc) 2.17
Copyright (C) 2012 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
Written by Roland McGrath and Ulrich Drepper.
[root@k8s-node-76 cmake-3.28.0]# strings /lib64/libstdc++.so.6 |grep GLIBCXX
GLIBCXX_3.4
GLIBCXX_3.4.1
GLIBCXX_3.4.2
GLIBCXX_3.4.3
GLIBCXX_3.4.4
GLIBCXX_3.4.5
GLIBCXX_3.4.6
GLIBCXX_3.4.7
GLIBCXX_3.4.8
GLIBCXX_3.4.9
GLIBCXX_3.4.10
GLIBCXX_3.4.11
GLIBCXX_3.4.12
GLIBCXX_3.4.13
GLIBCXX_3.4.14
GLIBCXX_3.4.15
GLIBCXX_3.4.16
GLIBCXX_3.4.17
GLIBCXX_3.4.18
GLIBCXX_3.4.19
GLIBCXX_DEBUG_MESSAGE_LENGTH

```


### abi_check Error
```bash
[ 44%] Linking CXX static library libinnobase.a
[ 44%] Built target innobase
[ 44%] Building CXX object components/libminchassis/CMakeFiles/minchassis.dir/component_common.cc.o
[ 44%] Building CXX object components/libminchassis/CMakeFiles/minchassis.dir/dynamic_loader.cc.o
[ 44%] Building CXX object components/libminchassis/CMakeFiles/minchassis.dir/dynamic_loader_scheme_file.cc.o
[ 44%] Building CXX object components/libminchassis/CMakeFiles/minchassis.dir/mc_psi_system_service.cc.o
[ 44%] Building CXX object components/libminchassis/CMakeFiles/minchassis.dir/mc_rwlock_service.cc.o
[ 44%] Building CXX object components/libminchassis/CMakeFiles/minchassis.dir/minimal_chassis.cc.o
[ 44%] Building CXX object components/libminchassis/CMakeFiles/minchassis.dir/minimal_chassis_runtime_error_imp.cc.o
[ 44%] Building CXX object components/libminchassis/CMakeFiles/minchassis.dir/my_metadata.cc.o
[ 44%] Building CXX object components/libminchassis/CMakeFiles/minchassis.dir/my_ref_counted.cc.o
[ 44%] Building CXX object components/libminchassis/CMakeFiles/minchassis.dir/mysql_component.cc.o
[ 44%] Building CXX object components/libminchassis/CMakeFiles/minchassis.dir/mysql_service_implementation.cc.o
[ 44%] Building CXX object components/libminchassis/CMakeFiles/minchassis.dir/registry.cc.o
[ 44%] Building CXX object components/libminchassis/CMakeFiles/minchassis.dir/rwlock_scoped_lock.cc.o
[ 44%] Linking CXX static library libminchassis.a
[ 44%] Built target minchassis
[ 44%] Building C object CMakeFiles/server_unittest_library.dir/server_unittest_library_dummy.c.o
[ 44%] Linking CXX shared library library_output_directory/libserver_unittest_library.so
[ 44%] Built target server_unittest_library
467a468
> #include_next <stdint.h>
CMake Error at /db/storage/glc/targz/mysql-8.0.32/cmake/do_abi_check.cmake:121 (MESSAGE):
  ABI check found difference between
  /db/storage/glc/targz/mysql-8.0.32/include/mysql/services.h.pp and
  /db/storage/glc/build/abi_check.out


gmake[2]: *** [CMakeFiles/abi_check] Error 1
gmake[1]: *** [CMakeFiles/abi_check.dir/all] Error 2
gmake: *** [all] Error 2


```



