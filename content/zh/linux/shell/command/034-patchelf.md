---
title: 34. patchelf
description: 修改RUNPATH
date: 2025-03-28
weight: 340
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}

装载器的搜索顺序有优先级，依次为：LD_PRELOAD（环境变量）、RUNPATH（写在ELF文件内）、LD_LIBRARY_PATH（环境变量）、/etc/ld.so.conf（配置文件）、/lib、/usr/lib。很显然，只能从前三项入手，因为后面几项都需要root权限。前三项的两个环境变量有很大的副作用，会导致其他依赖glibc的程序无法运行，甚至影响到ls这样基本的命令，因此最好是对第二项下手。

现在本文的主角——patchelf终于要出场了，顾名思义，这是一个修改elf文件的工具，有了它，才能对ELF的RUNPATH做修改。

对于动态链接器来说，$ORIGIN 的含义是“包含应用程序的目录”。

- https://github.com/NixOS/patchelf

{{< /alert >}}

| 选项                          | 作用             | 使用场景               |
|-------------------------------|----------------|--------------------|
| `--set-interpreter`           | 只编译不链接     | 分步构建时             |
| `--set-rpath`                 | 定义宏           | 条件包含 `config.h`    |
| `-I<dir>`                     | 添加头文件路径   | 头文件在非标准路径时   |
| `-DRL_LIBRARY_VERSION`        | 定义版本宏       | 代码中嵌入版本信息     |
| `-DBRACKETED_PASTE_DEFAULT=1` | 定义功能控制宏   | 启用/禁用特定功能      |
| `-g`                          | 添加调试信息     | 调试阶段               |
| `-O2`                         | 优化级别 2       | 发布版本性能优化       |
| `-Wno-xxx`                    | 禁用特定警告     | 处理已知无害警告       |
| `-fPIC`                       | 生成位置无关代码 | 动态库编译必需         |
| `-o <file>`                   | 指定输出文件     | 控制输出文件名         |
| `../../xmalloc.c`             | 指定源文件       | 编译非当前目录的源文件 |


## 1. 更改可执行文件的动态加载器`ld-linux-x86-64.so.2`或`ld-linux-aarch64.so.1`: `--set-interpreter`

```bash
# ld-linux-x86-64.so.2
patchelf --set-interpreter /db/storage/guolicheng/local/glibc-2.28/lib/ld-linux-x86-64.so.2 ./node

```





## 2. 更改可执行文件和库的rpath：`--set-rpath`、`--force-rpath`

```bash
# glibc gcc
patchelf --set-rpath '/db/storage/guolicheng/local/glibc-2.28/lib:/db/storage/guolicheng/local/gcc-11.4.0/lib64' ./node

patchelf --set-rpath 'ORIGIN:../lib/:/usr/local/gcc-11.4.0/lib64' my_exe

patchelf --force-rpath --set-rpath '$ORIGIN/../lib64/:../lib/:/usr/local/mysql-8.0.32/lib' my_exe

```
若直接使用patchelf --set-rpath只是修改成了runpath却不是rpath，可使用`--force-rpath`
- https://github.com/NixOS/patchelf/issues/265


## 3. 删除RPATH中不必要的路径：`--shrink-rpath`、`--shrink-rpath --allowed-rpath-prefixes`

`-rpath》runpath`

```bash
[root@centos8-25 /glc/mysql/app/mysql-8.0.32/bin] # ldd mysqld.test
	linux-vdso.so.1 (0x00007ffeffdf9000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f01cba4d000)
	librt.so.1 => /lib64/librt.so.1 (0x00007f01cb845000)
	libssl.so.1.1 => /lib64/libssl.so.1.1 (0x00007f01cb5b1000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x00007f01cb0c8000)
	libprotobuf-lite.so.3.19.4 => /glc/mysql/app/mysql-8.0.32/bin/./../lib/private/libprotobuf-lite.so.3.19.4 (0x00007f01cad95000)
	libaio.so.1 => /lib64/libaio.so.1 (0x00007f01cab92000)
	libnuma.so.1 => /lib64/libnuma.so.1 (0x00007f01ca986000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f01ca782000)
	libstdc++.so.6 => /lib64/libstdc++.so.6 (0x00007f01ca36c000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f01c9fea000)
	libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x00007f01c9dd1000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f01c9a0c000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f01cbc6d000)
	libz.so.1 => /lib64/libz.so.1 (0x00007f01c97f5000)
[root@centos8-25 /glc/mysql/app/mysql-8.0.32/bin] # readelf -d mysqld.test

Dynamic section at offset 0x84a59e0 contains 37 entries:
  Tag        Type                         Name/Value
 0x0000000000000001 (NEEDED)             Shared library: [libpthread.so.0]
 0x0000000000000001 (NEEDED)             Shared library: [librt.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libssl.so.1.1]
 0x0000000000000001 (NEEDED)             Shared library: [libcrypto.so.1.1]
 0x0000000000000001 (NEEDED)             Shared library: [libprotobuf-lite.so.3.19.4]
 0x0000000000000001 (NEEDED)             Shared library: [libaio.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libnuma.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libdl.so.2]
 0x0000000000000001 (NEEDED)             Shared library: [libstdc++.so.6]
 0x0000000000000001 (NEEDED)             Shared library: [libm.so.6]
 0x0000000000000001 (NEEDED)             Shared library: [libgcc_s.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libc.so.6]
 0x000000000000000f (RPATH)              Library rpath: [$ORIGIN/../lib/private:$ORIGIN/../lib/private]
 0x000000000000000c (INIT)               0x34617d0
 0x000000000000000d (FINI)               0x5e88cb4
 0x0000000000000019 (INIT_ARRAY)         0x88a1878
 0x000000000000001b (INIT_ARRAYSZ)       8128 (bytes)
 0x000000000000001a (FINI_ARRAY)         0x88a3838
 0x000000000000001c (FINI_ARRAYSZ)       8 (bytes)
 0x0000000000000004 (HASH)               0x4002d0
 0x000000006ffffef5 (GNU_HASH)           0x532718
 0x0000000000000005 (STRTAB)             0xd11410
 0x0000000000000006 (SYMTAB)             0x6a3ad8
 0x000000000000000a (STRSZ)              40408585 (bytes)
 0x000000000000000b (SYMENT)             24 (bytes)
 0x0000000000000015 (DEBUG)              0x0
 0x0000000000000003 (PLTGOT)             0x8aa6000
 0x0000000000000002 (PLTRELSZ)           22992 (bytes)
 0x0000000000000014 (PLTREL)             RELA
 0x0000000000000017 (JMPREL)             0x345be00
 0x0000000000000007 (RELA)               0x3424048
 0x0000000000000008 (RELASZ)             228792 (bytes)
 0x0000000000000009 (RELAENT)            24 (bytes)
 0x000000006ffffffe (VERNEED)            0x3423c38
 0x000000006fffffff (VERNEEDNUM)         11
 0x000000006ffffff0 (VERSYM)             0x339aa1a
 0x0000000000000000 (NULL)               0x0
[root@centos8-25 /glc/mysql/app/mysql-8.0.32/bin] # patchelf --shrink-rpath mysqld.test
[root@centos8-25 /glc/mysql/app/mysql-8.0.32/bin] # readelf -d mysqld.test

Dynamic section at offset 0x84a59e0 contains 37 entries:
  Tag        Type                         Name/Value
 0x0000000000000001 (NEEDED)             Shared library: [libpthread.so.0]
 0x0000000000000001 (NEEDED)             Shared library: [librt.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libssl.so.1.1]
 0x0000000000000001 (NEEDED)             Shared library: [libcrypto.so.1.1]
 0x0000000000000001 (NEEDED)             Shared library: [libprotobuf-lite.so.3.19.4]
 0x0000000000000001 (NEEDED)             Shared library: [libaio.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libnuma.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libdl.so.2]
 0x0000000000000001 (NEEDED)             Shared library: [libstdc++.so.6]
 0x0000000000000001 (NEEDED)             Shared library: [libm.so.6]
 0x0000000000000001 (NEEDED)             Shared library: [libgcc_s.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libc.so.6]
 0x000000000000001d (RUNPATH)            Library runpath: [$ORIGIN/../lib/private:$ORIGIN/../lib/private]
 0x000000000000000c (INIT)               0x34617d0
 0x000000000000000d (FINI)               0x5e88cb4
 0x0000000000000019 (INIT_ARRAY)         0x88a1878
 0x000000000000001b (INIT_ARRAYSZ)       8128 (bytes)
 0x000000000000001a (FINI_ARRAY)         0x88a3838
 0x000000000000001c (FINI_ARRAYSZ)       8 (bytes)
 0x0000000000000004 (HASH)               0x4002d0
 0x000000006ffffef5 (GNU_HASH)           0x532718
 0x0000000000000005 (STRTAB)             0xd11410
 0x0000000000000006 (SYMTAB)             0x6a3ad8
 0x000000000000000a (STRSZ)              40408585 (bytes)
 0x000000000000000b (SYMENT)             24 (bytes)
 0x0000000000000015 (DEBUG)              0x0
 0x0000000000000003 (PLTGOT)             0x8aa6000
 0x0000000000000002 (PLTRELSZ)           22992 (bytes)
 0x0000000000000014 (PLTREL)             RELA
 0x0000000000000017 (JMPREL)             0x345be00
 0x0000000000000007 (RELA)               0x3424048
 0x0000000000000008 (RELASZ)             228792 (bytes)
 0x0000000000000009 (RELAENT)            24 (bytes)
 0x000000006ffffffe (VERNEED)            0x3423c38
 0x000000006fffffff (VERNEEDNUM)         11
 0x000000006ffffff0 (VERSYM)             0x339aa1a
 0x0000000000000000 (NULL)               0x0
[root@centos8-25 /glc/mysql/app/mysql-8.0.32/bin] # ldd mysqld.test
	linux-vdso.so.1 (0x00007ffe8f1db000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007fabec03c000)
	librt.so.1 => /lib64/librt.so.1 (0x00007fabebe34000)
	libssl.so.1.1 => /lib64/libssl.so.1.1 (0x00007fabebba0000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x00007fabeb6b7000)
	libprotobuf-lite.so.3.19.4 => /glc/mysql/app/mysql-8.0.32/bin/./../lib/private/libprotobuf-lite.so.3.19.4 (0x00007fabeb384000)
	libaio.so.1 => /lib64/libaio.so.1 (0x00007fabeb181000)
	libnuma.so.1 => /lib64/libnuma.so.1 (0x00007fabeaf75000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007fabead71000)
	libstdc++.so.6 => /lib64/libstdc++.so.6 (0x00007fabea95b000)
	libm.so.6 => /lib64/libm.so.6 (0x00007fabea5d9000)
	libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x00007fabea3c0000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fabe9ffb000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fabec25c000)
	libz.so.1 => /lib64/libz.so.1 (0x00007fabe9de4000)
[root@centos8-25 /glc/mysql/app/mysql-8.0.32/bin] # 

```









## 3. 查看或修改动态库的soname：`--print-soname`、`--set-soname`

```bash
[root@centos8-25 /glc/mysql/app/mysql-8.0.32/lib] # ll
total 41268
-rw-r--r-- 1 root root 27737986 2025-04-16T21:46:40 libmysqlclient.a
lrwxrwxrwx 1 root root       20 2025-04-16T23:04:38 libmysqlclient.so -> libmysqlclient.so.21
lrwxrwxrwx 1 root root       25 2025-04-16T23:04:38 libmysqlclient.so.21 -> libmysqlclient.so.21.2.32
-rwxr-xr-x 1 root root 14455816 2025-04-16T21:46:10 libmysqlclient.so.21.2.32
-rw-r--r-- 1 root root    41558 2025-04-16T21:25:45 libmysqlservices.a
drwxr-xr-x 3 root root     4096 2025-04-16T23:04:42 mysqlrouter
drwxr-xr-x 2 root root       28 2025-04-16T23:04:38 pkgconfig
drwxr-xr-x 2 root root     8192 2025-04-16T23:04:38 plugin
drwxr-xr-x 3 root root       85 2025-04-16T23:04:38 private
[root@centos8-25 /glc/mysql/app/mysql-8.0.32/lib] # patchelf --print-soname libmysqlclient.so.21.2.32
libmysqlclient.so.21

```


```bash
[root@centos8-25 /tmp] # patchelf --print-soname libmysqlclient.so.21.2.32
libmysqlclient.so.21
[root@centos8-25 /tmp] # patchelf --set-soname libglc.so.222 libmysqlclient.so.21.2.32
[root@centos8-25 /tmp] # patchelf --print-soname libmysqlclient.so.21.2.32
libglc.so.222
[root@centos8-25 /tmp] # patchelf --set-soname libmysqlclient.so.21 libmysqlclient.so.21.2.32
[root@centos8-25 /tmp] # patchelf --print-soname libmysqlclient.so.21.2.32
libmysqlclient.so.21
[root@centos8-25 /tmp] # 
 

```



## 4. 添加或删除或替换依赖包NEEDED：`--add-needed`、`--remove-needed` 、`--replace-needed`


```bash
[root@centos8-25 /tmp] # ldd  mysqld
	linux-vdso.so.1 (0x00007fffa23dc000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f5b338db000)
	librt.so.1 => /lib64/librt.so.1 (0x00007f5b336d3000)
	libssl.so.1.1 => /lib64/libssl.so.1.1 (0x00007f5b3343f000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x00007f5b32f56000)
	libprotobuf-lite.so.3.19.4 => not found
	libaio.so.1 => /lib64/libaio.so.1 (0x00007f5b32d53000)
	libnuma.so.1 => /lib64/libnuma.so.1 (0x00007f5b32b47000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f5b32943000)
	libstdc++.so.6 => /lib64/libstdc++.so.6 (0x00007f5b3252d000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f5b321ab000)
	libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x00007f5b31f92000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f5b31bcd000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f5b33afb000)
	libz.so.1 => /lib64/libz.so.1 (0x00007f5b319b6000)
[root@centos8-25 /tmp] # readelf -d mysqld

Dynamic section at offset 0x84a59e0 contains 37 entries:
  Tag        Type                         Name/Value
 0x0000000000000001 (NEEDED)             Shared library: [libpthread.so.0]
 0x0000000000000001 (NEEDED)             Shared library: [librt.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libssl.so.1.1]
 0x0000000000000001 (NEEDED)             Shared library: [libcrypto.so.1.1]
 0x0000000000000001 (NEEDED)             Shared library: [libprotobuf-lite.so.3.19.4]
 0x0000000000000001 (NEEDED)             Shared library: [libaio.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libnuma.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libdl.so.2]
 0x0000000000000001 (NEEDED)             Shared library: [libstdc++.so.6]
 0x0000000000000001 (NEEDED)             Shared library: [libm.so.6]
 0x0000000000000001 (NEEDED)             Shared library: [libgcc_s.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libc.so.6]
 0x000000000000000f (RPATH)              Library rpath: [$ORIGIN/../lib/private:$ORIGIN/../lib/private]
 0x000000000000000c (INIT)               0x34617d0
 0x000000000000000d (FINI)               0x5e88cb4
 0x0000000000000019 (INIT_ARRAY)         0x88a1878
 0x000000000000001b (INIT_ARRAYSZ)       8128 (bytes)
 0x000000000000001a (FINI_ARRAY)         0x88a3838
 0x000000000000001c (FINI_ARRAYSZ)       8 (bytes)
 0x0000000000000004 (HASH)               0x4002d0
 0x000000006ffffef5 (GNU_HASH)           0x532718
 0x0000000000000005 (STRTAB)             0xd11410
 0x0000000000000006 (SYMTAB)             0x6a3ad8
 0x000000000000000a (STRSZ)              40408585 (bytes)
 0x000000000000000b (SYMENT)             24 (bytes)
 0x0000000000000015 (DEBUG)              0x0
 0x0000000000000003 (PLTGOT)             0x8aa6000
 0x0000000000000002 (PLTRELSZ)           22992 (bytes)
 0x0000000000000014 (PLTREL)             RELA
 0x0000000000000017 (JMPREL)             0x345be00
 0x0000000000000007 (RELA)               0x3424048
 0x0000000000000008 (RELASZ)             228792 (bytes)
 0x0000000000000009 (RELAENT)            24 (bytes)
 0x000000006ffffffe (VERNEED)            0x3423c38
 0x000000006fffffff (VERNEEDNUM)         11
 0x000000006ffffff0 (VERSYM)             0x339aa1a
 0x0000000000000000 (NULL)               0x0
[root@centos8-25 /tmp] # patchelf --remove-needed libprotobuf-lite.so.3.19.4 mysqld
[root@centos8-25 /tmp] # ldd  mysqld
	linux-vdso.so.1 (0x00007ffd7e1cb000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f7dfa76a000)
	librt.so.1 => /lib64/librt.so.1 (0x00007f7dfa562000)
	libssl.so.1.1 => /lib64/libssl.so.1.1 (0x00007f7dfa2ce000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x00007f7df9de5000)
	libaio.so.1 => /lib64/libaio.so.1 (0x00007f7df9be2000)
	libnuma.so.1 => /lib64/libnuma.so.1 (0x00007f7df99d6000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f7df97d2000)
	libstdc++.so.6 => /lib64/libstdc++.so.6 (0x00007f7df93bc000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f7df903a000)
	libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x00007f7df8e21000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f7df8a5c000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f7dfa98a000)
	libz.so.1 => /lib64/libz.so.1 (0x00007f7df8845000)
[root@centos8-25 /tmp] # readelf -d mysqld

Dynamic section at offset 0x84a59e0 contains 36 entries:
  Tag        Type                         Name/Value
 0x0000000000000001 (NEEDED)             Shared library: [libpthread.so.0]
 0x0000000000000001 (NEEDED)             Shared library: [librt.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libssl.so.1.1]
 0x0000000000000001 (NEEDED)             Shared library: [libcrypto.so.1.1]
 0x0000000000000001 (NEEDED)             Shared library: [libaio.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libnuma.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libdl.so.2]
 0x0000000000000001 (NEEDED)             Shared library: [libstdc++.so.6]
 0x0000000000000001 (NEEDED)             Shared library: [libm.so.6]
 0x0000000000000001 (NEEDED)             Shared library: [libgcc_s.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libc.so.6]
 0x000000000000000f (RPATH)              Library rpath: [$ORIGIN/../lib/private:$ORIGIN/../lib/private]
 0x000000000000000c (INIT)               0x34617d0
 0x000000000000000d (FINI)               0x5e88cb4
 0x0000000000000019 (INIT_ARRAY)         0x88a1878
 0x000000000000001b (INIT_ARRAYSZ)       8128 (bytes)
 0x000000000000001a (FINI_ARRAY)         0x88a3838
 0x000000000000001c (FINI_ARRAYSZ)       8 (bytes)
 0x0000000000000004 (HASH)               0x4002d0
 0x000000006ffffef5 (GNU_HASH)           0x532718
 0x0000000000000005 (STRTAB)             0xd11410
 0x0000000000000006 (SYMTAB)             0x6a3ad8
 0x000000000000000a (STRSZ)              40408585 (bytes)
 0x000000000000000b (SYMENT)             24 (bytes)
 0x0000000000000015 (DEBUG)              0x0
 0x0000000000000003 (PLTGOT)             0x8aa6000
 0x0000000000000002 (PLTRELSZ)           22992 (bytes)
 0x0000000000000014 (PLTREL)             RELA
 0x0000000000000017 (JMPREL)             0x345be00
 0x0000000000000007 (RELA)               0x3424048
 0x0000000000000008 (RELASZ)             228792 (bytes)
 0x0000000000000009 (RELAENT)            24 (bytes)
 0x000000006ffffffe (VERNEED)            0x3423c38
 0x000000006fffffff (VERNEEDNUM)         11
 0x000000006ffffff0 (VERSYM)             0x339aa1a
 0x0000000000000000 (NULL)               0x0
[root@centos8-25 /tmp] # patchelf --add-needed libprotobuf-lite.so.3.19.4 mysqld
[root@centos8-25 /tmp] # ldd  mysqld
	linux-vdso.so.1 (0x00007fff0c5cd000)
	libprotobuf-lite.so.3.19.4 => not found
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f148ce4b000)
	librt.so.1 => /lib64/librt.so.1 (0x00007f148cc43000)
	libssl.so.1.1 => /lib64/libssl.so.1.1 (0x00007f148c9af000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x00007f148c4c6000)
	libaio.so.1 => /lib64/libaio.so.1 (0x00007f148c2c3000)
	libnuma.so.1 => /lib64/libnuma.so.1 (0x00007f148c0b7000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f148beb3000)
	libstdc++.so.6 => /lib64/libstdc++.so.6 (0x00007f148ba9d000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f148b71b000)
	libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x00007f148b502000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f148b13d000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f148d06b000)
	libz.so.1 => /lib64/libz.so.1 (0x00007f148af26000)
[root@centos8-25 /tmp] # readelf -d mysqld

Dynamic section at offset 0x2e0 contains 37 entries:
  Tag        Type                         Name/Value
 0x0000000000000001 (NEEDED)             Shared library: [libprotobuf-lite.so.3.19.4]
 0x0000000000000001 (NEEDED)             Shared library: [libpthread.so.0]
 0x0000000000000001 (NEEDED)             Shared library: [librt.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libssl.so.1.1]
 0x0000000000000001 (NEEDED)             Shared library: [libcrypto.so.1.1]
 0x0000000000000001 (NEEDED)             Shared library: [libaio.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libnuma.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libdl.so.2]
 0x0000000000000001 (NEEDED)             Shared library: [libstdc++.so.6]
 0x0000000000000001 (NEEDED)             Shared library: [libm.so.6]
 0x0000000000000001 (NEEDED)             Shared library: [libgcc_s.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libc.so.6]
 0x000000000000000f (RPATH)              Library rpath: [$ORIGIN/../lib/private:$ORIGIN/../lib/private]
 0x000000000000000c (INIT)               0x34617d0
 0x000000000000000d (FINI)               0x5e88cb4
 0x0000000000000019 (INIT_ARRAY)         0x88a1878
 0x000000000000001b (INIT_ARRAYSZ)       8128 (bytes)
 0x000000000000001a (FINI_ARRAY)         0x88a3838
 0x000000000000001c (FINI_ARRAYSZ)       8 (bytes)
 0x0000000000000004 (HASH)               0x32678b0
 0x000000006ffffef5 (GNU_HASH)           0x30f64f0
 0x0000000000000005 (STRTAB)             0x3ff590
 0x0000000000000006 (SYMTAB)             0x2a88bb8
 0x000000000000000a (STRSZ)              40408613 (bytes)
 0x000000000000000b (SYMENT)             24 (bytes)
 0x0000000000000015 (DEBUG)              0x0
 0x0000000000000003 (PLTGOT)             0x8aa6000
 0x0000000000000002 (PLTRELSZ)           22992 (bytes)
 0x0000000000000014 (PLTREL)             RELA
 0x0000000000000017 (JMPREL)             0x345be00
 0x0000000000000007 (RELA)               0x3424048
 0x0000000000000008 (RELASZ)             228792 (bytes)
 0x0000000000000009 (RELAENT)            24 (bytes)
 0x000000006ffffffe (VERNEED)            0x3423c38
 0x000000006fffffff (VERNEEDNUM)         11
 0x000000006ffffff0 (VERSYM)             0x339aa1a
 0x0000000000000000 (NULL)               0x0

[root@centos8-25 /tmp] # patchelf --replace-needed libprotobuf-lite.so.3.19.4 libprotobuf-lite.so.2.2.2 mysqld 
[root@centos8-25 /tmp] # ldd  mysqld
	linux-vdso.so.1 (0x00007ffedc499000)
	libprotobuf-lite.so.2.2.2 => not found
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007fae9f5e8000)
	librt.so.1 => /lib64/librt.so.1 (0x00007fae9f3e0000)
	libssl.so.1.1 => /lib64/libssl.so.1.1 (0x00007fae9f14c000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x00007fae9ec63000)
	libaio.so.1 => /lib64/libaio.so.1 (0x00007fae9ea60000)
	libnuma.so.1 => /lib64/libnuma.so.1 (0x00007fae9e854000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007fae9e650000)
	libstdc++.so.6 => /lib64/libstdc++.so.6 (0x00007fae9e23a000)
	libm.so.6 => /lib64/libm.so.6 (0x00007fae9deb8000)
	libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x00007fae9dc9f000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fae9d8da000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fae9f808000)
	libz.so.1 => /lib64/libz.so.1 (0x00007fae9d6c3000)
[root@centos8-25 /tmp] # readelf -d mysqld

Dynamic section at offset 0x318 contains 37 entries:
  Tag        Type                         Name/Value
 0x0000000000000001 (NEEDED)             Shared library: [libprotobuf-lite.so.2.2.2]
 0x0000000000000001 (NEEDED)             Shared library: [libpthread.so.0]
 0x0000000000000001 (NEEDED)             Shared library: [librt.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libssl.so.1.1]
 0x0000000000000001 (NEEDED)             Shared library: [libcrypto.so.1.1]
 0x0000000000000001 (NEEDED)             Shared library: [libaio.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libnuma.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libdl.so.2]
 0x0000000000000001 (NEEDED)             Shared library: [libstdc++.so.6]
 0x0000000000000001 (NEEDED)             Shared library: [libm.so.6]
 0x0000000000000001 (NEEDED)             Shared library: [libgcc_s.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libc.so.6]
 0x000000000000000f (RPATH)              Library rpath: [$ORIGIN/../lib/private:$ORIGIN/../lib/private]
 0x000000000000000c (INIT)               0x34617d0
 0x000000000000000d (FINI)               0x5e88cb4
 0x0000000000000019 (INIT_ARRAY)         0x88a1878
 0x000000000000001b (INIT_ARRAYSZ)       8128 (bytes)
 0x000000000000001a (FINI_ARRAY)         0x88a3838
 0x000000000000001c (FINI_ARRAYSZ)       8 (bytes)
 0x0000000000000004 (HASH)               0x32678b0
 0x000000006ffffef5 (GNU_HASH)           0x30f64f0
 0x0000000000000005 (STRTAB)             0x3fe5c8
 0x0000000000000006 (SYMTAB)             0x2a88bb8
 0x000000000000000a (STRSZ)              40408639 (bytes)
 0x000000000000000b (SYMENT)             24 (bytes)
 0x0000000000000015 (DEBUG)              0x0
 0x0000000000000003 (PLTGOT)             0x8aa6000
 0x0000000000000002 (PLTRELSZ)           22992 (bytes)
 0x0000000000000014 (PLTREL)             RELA
 0x0000000000000017 (JMPREL)             0x345be00
 0x0000000000000007 (RELA)               0x3424048
 0x0000000000000008 (RELASZ)             228792 (bytes)
 0x0000000000000009 (RELAENT)            24 (bytes)
 0x000000006ffffffe (VERNEED)            0x3423c38
 0x000000006fffffff (VERNEEDNUM)         11
 0x000000006ffffff0 (VERSYM)             0x339aa1a
 0x0000000000000000 (NULL)               0x0
[root@centos8-25 /tmp] # patchelf --replace-needed libprotobuf-lite.so.2.2.2 libprotobuf-lite.so.3.19.4 mysqld
[root@centos8-25 /tmp] # ldd  mysqld
	linux-vdso.so.1 (0x00007ffe955fa000)
	libprotobuf-lite.so.3.19.4 => not found
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007fb4ebbf4000)
	librt.so.1 => /lib64/librt.so.1 (0x00007fb4eb9ec000)
	libssl.so.1.1 => /lib64/libssl.so.1.1 (0x00007fb4eb758000)
	libcrypto.so.1.1 => /lib64/libcrypto.so.1.1 (0x00007fb4eb26f000)
	libaio.so.1 => /lib64/libaio.so.1 (0x00007fb4eb06c000)
	libnuma.so.1 => /lib64/libnuma.so.1 (0x00007fb4eae60000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007fb4eac5c000)
	libstdc++.so.6 => /lib64/libstdc++.so.6 (0x00007fb4ea846000)
	libm.so.6 => /lib64/libm.so.6 (0x00007fb4ea4c4000)
	libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x00007fb4ea2ab000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fb4e9ee6000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fb4ebe14000)
	libz.so.1 => /lib64/libz.so.1 (0x00007fb4e9ccf000)
[root@centos8-25 /tmp] # readelf -d mysqld

Dynamic section at offset 0x318 contains 37 entries:
  Tag        Type                         Name/Value
 0x0000000000000001 (NEEDED)             Shared library: [libprotobuf-lite.so.3.19.4]
 0x0000000000000001 (NEEDED)             Shared library: [libpthread.so.0]
 0x0000000000000001 (NEEDED)             Shared library: [librt.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libssl.so.1.1]
 0x0000000000000001 (NEEDED)             Shared library: [libcrypto.so.1.1]
 0x0000000000000001 (NEEDED)             Shared library: [libaio.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libnuma.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libdl.so.2]
 0x0000000000000001 (NEEDED)             Shared library: [libstdc++.so.6]
 0x0000000000000001 (NEEDED)             Shared library: [libm.so.6]
 0x0000000000000001 (NEEDED)             Shared library: [libgcc_s.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libc.so.6]
 0x000000000000000f (RPATH)              Library rpath: [$ORIGIN/../lib/private:$ORIGIN/../lib/private]
 0x000000000000000c (INIT)               0x34617d0
 0x000000000000000d (FINI)               0x5e88cb4
 0x0000000000000019 (INIT_ARRAY)         0x88a1878
 0x000000000000001b (INIT_ARRAYSZ)       8128 (bytes)
 0x000000000000001a (FINI_ARRAY)         0x88a3838
 0x000000000000001c (FINI_ARRAYSZ)       8 (bytes)
 0x0000000000000004 (HASH)               0x32678b0
 0x000000006ffffef5 (GNU_HASH)           0x30f64f0
 0x0000000000000005 (STRTAB)             0x3fe5c8
 0x0000000000000006 (SYMTAB)             0x2a88bb8
 0x000000000000000a (STRSZ)              40408666 (bytes)
 0x000000000000000b (SYMENT)             24 (bytes)
 0x0000000000000015 (DEBUG)              0x0
 0x0000000000000003 (PLTGOT)             0x8aa6000
 0x0000000000000002 (PLTRELSZ)           22992 (bytes)
 0x0000000000000014 (PLTREL)             RELA
 0x0000000000000017 (JMPREL)             0x345be00
 0x0000000000000007 (RELA)               0x3424048
 0x0000000000000008 (RELASZ)             228792 (bytes)
 0x0000000000000009 (RELAENT)            24 (bytes)
 0x000000006ffffffe (VERNEED)            0x3423c38
 0x000000006fffffff (VERNEEDNUM)         11
 0x000000006ffffff0 (VERSYM)             0x339aa1a
 0x0000000000000000 (NULL)               0x0

```
















## 案例分析



#### 1. 修改vscode的node的glibc


```bash
# ld-linux-x86-64.so.2
patchelf --set-interpreter /db/storage/guolicheng/local/glibc-2.28/lib/ld-linux-x86-64.so.2 ./node

# glibc gcc
patchelf --set-rpath '/db/storage/guolicheng/local/glibc-2.28/lib:/db/storage/guolicheng/local/gcc-11.4.0/lib64' ./node


```


```bash

2025-04-18T21:28:10 [igoodfu@48 /db/storage/guolicheng/.vscode-server] $ tar -xzf vscode-server-linux-x64.tar.gz 
2025-04-18T21:28:17 [igoodfu@48 /db/storage/guolicheng/.vscode-server] $ ll
total 80544
drwxrwxr-x 3 guolicheng guolicheng     4096 2025-04-18T20:56:16 cli
-rwxrwxr-x 1 guolicheng guolicheng 21861176 2025-04-16T07:20:04 code-17baf841131aa23349f217ca7c570c76ee87b957
drwxr-xr-x 6 guolicheng guolicheng     4096 2025-04-16T07:42:12 vscode-server-linux-x64
-rw-r--r-- 1 guolicheng guolicheng 60601853 2025-04-18T21:20:57 vscode-server-linux-x64.tar.gz
2025-04-18T21:28:18 [igoodfu@48 /db/storage/guolicheng/.vscode-server] $ cd vscode-server-linux-x64
2025-04-18T21:28:22 [igoodfu@48 /db/storage/guolicheng/.vscode-server/vscode-server-linux-x64] $ ll
total 96440
drwxr-xr-x  4 guolicheng guolicheng     4096 2025-04-16T07:42:12 bin
drwxr-xr-x 35 guolicheng guolicheng     4096 2025-04-16T07:42:13 extensions
-rw-r--r--  1 guolicheng guolicheng    13380 2025-04-16T07:41:05 LICENSE
-rwxr-xr-x  1 guolicheng guolicheng 98657032 2025-04-16T07:42:09 node
drwxr-xr-x 97 guolicheng guolicheng     4096 2025-04-16T07:42:16 node_modules
drwxr-xr-x  3 guolicheng guolicheng     4096 2025-04-16T07:42:12 out
-rw-rw-r--  1 guolicheng guolicheng      197 2025-04-16T07:35:29 package.json
-rw-rw-r--  1 guolicheng guolicheng    54841 2025-04-16T07:41:05 product.json
2025-04-18T21:28:23 [igoodfu@48 /db/storage/guolicheng/.vscode-server/vscode-server-linux-x64] $ ldd -r ./node
./node: /lib64/libm.so.6: version `GLIBC_2.27' not found (required by ./node)
./node: /lib64/libc.so.6: version `GLIBC_2.28' not found (required by ./node)
./node: /lib64/libc.so.6: version `GLIBC_2.25' not found (required by ./node)
	linux-vdso.so.1 (0x00007ffc815a8000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007fdf6ad2c000)
	libstdc++.so.6 => /db/storage/guolicheng/local/gcc-11.4.0/lib64/libstdc++.so.6 (0x00007fdf6a926000)
	libm.so.6 => /lib64/libm.so.6 (0x00007fdf6a624000)
	libgcc_s.so.1 => /db/storage/guolicheng/local/gcc-11.4.0/lib64/libgcc_s.so.1 (0x00007fdf6a40c000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007fdf6a1ee000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fdf69e42000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fdf6af30000)
symbol getentropy, version GLIBC_2.25 not defined in file libc.so.6 with link time reference	(./node)
symbol fcntl64, version GLIBC_2.28 not defined in file libc.so.6 with link time reference	(./node)
symbol powf, version GLIBC_2.27 not defined in file libm.so.6 with link time reference	(./node)
symbol expf, version GLIBC_2.27 not defined in file libm.so.6 with link time reference	(./node)
symbol getrandom, version GLIBC_2.25 not defined in file libc.so.6 with link time reference	(./node)
2025-04-18T21:28:28 [igoodfu@48 /db/storage/guolicheng/.vscode-server/vscode-server-linux-x64] $ patchelf --set-rpath '/db/storage/guolicheng/local/glibc-2.28/lib:/db/storage/guolicheng/local/gcc-11.4.0/lib64' ./node
2025-04-18T21:29:10 [igoodfu@48 /db/storage/guolicheng/.vscode-server/vscode-server-linux-x64] $ ll
total 96448
drwxr-xr-x  4 guolicheng guolicheng     4096 2025-04-16T07:42:12 bin
drwxr-xr-x 35 guolicheng guolicheng     4096 2025-04-16T07:42:13 extensions
-rw-r--r--  1 guolicheng guolicheng    13380 2025-04-16T07:41:05 LICENSE
-rwxr-xr-x  1 guolicheng guolicheng 98665224 2025-04-18T21:29:10 node
drwxr-xr-x 97 guolicheng guolicheng     4096 2025-04-16T07:42:16 node_modules
drwxr-xr-x  3 guolicheng guolicheng     4096 2025-04-16T07:42:12 out
-rw-rw-r--  1 guolicheng guolicheng      197 2025-04-16T07:35:29 package.json
-rw-rw-r--  1 guolicheng guolicheng    54841 2025-04-16T07:41:05 product.json
2025-04-18T21:29:12 [igoodfu@48 /db/storage/guolicheng/.vscode-server/vscode-server-linux-x64] $ ldd -r ./node
	linux-vdso.so.1 (0x00007ffd2ea81000)
	libdl.so.2 => /db/storage/guolicheng/local/glibc-2.28/lib/libdl.so.2 (0x00007f45cd5b3000)
	libstdc++.so.6 => /db/storage/guolicheng/local/gcc-11.4.0/lib64/libstdc++.so.6 (0x00007f45cd1ad000)
	libm.so.6 => /db/storage/guolicheng/local/glibc-2.28/lib/libm.so.6 (0x00007f45cce30000)
	libgcc_s.so.1 => /db/storage/guolicheng/local/gcc-11.4.0/lib64/libgcc_s.so.1 (0x00007f45ccc18000)
	libpthread.so.0 => /db/storage/guolicheng/local/glibc-2.28/lib/libpthread.so.0 (0x00007f45cc9f9000)
	libc.so.6 => /db/storage/guolicheng/local/glibc-2.28/lib/libc.so.6 (0x00007f45cc641000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f45cd7b7000)
symbol _dl_exception_create, version GLIBC_PRIVATE not defined in file ld-linux-x86-64.so.2 with link time reference	(/db/storage/guolicheng/local/glibc-2.28/lib/libc.so.6)
symbol __tunable_get_val, version GLIBC_PRIVATE not defined in file ld-linux-x86-64.so.2 with link time reference	(/db/storage/guolicheng/local/glibc-2.28/lib/libc.so.6)
symbol __tunable_get_val, version GLIBC_PRIVATE not defined in file ld-linux-x86-64.so.2 with link time reference	(/db/storage/guolicheng/local/glibc-2.28/lib/libpthread.so.0)
2025-04-18T21:29:15 [igoodfu@48 /db/storage/guolicheng/.vscode-server/vscode-server-linux-x64] $ patchelf --set-interpreter /db/storage/guolicheng/local/glibc-2.28/lib/ld-linux-x86-64.so.2 ./node
2025-04-18T21:29:39 [igoodfu@48 /db/storage/guolicheng/.vscode-server/vscode-server-linux-x64] $ ldd -r ./node
	linux-vdso.so.1 (0x00007ffcc01d7000)
	libdl.so.2 => /db/storage/guolicheng/local/glibc-2.28/lib/libdl.so.2 (0x00007f44be158000)
	libstdc++.so.6 => /db/storage/guolicheng/local/gcc-11.4.0/lib64/libstdc++.so.6 (0x00007f44bdd52000)
	libm.so.6 => /db/storage/guolicheng/local/glibc-2.28/lib/libm.so.6 (0x00007f44bd9d5000)
	libgcc_s.so.1 => /db/storage/guolicheng/local/gcc-11.4.0/lib64/libgcc_s.so.1 (0x00007f44bd7bd000)
	libpthread.so.0 => /db/storage/guolicheng/local/glibc-2.28/lib/libpthread.so.0 (0x00007f44bd59e000)
	libc.so.6 => /db/storage/guolicheng/local/glibc-2.28/lib/libc.so.6 (0x00007f44bd1e6000)
	/db/storage/guolicheng/local/glibc-2.28/lib/ld-linux-x86-64.so.2 (0x00007f44be35c000)
2025-04-18T21:29:41 [igoodfu@48 /db/storage/guolicheng/.vscode-server/vscode-server-linux-x64] $ ./node --version
v20.18.3
2025-04-18T21:29:51 [igoodfu@48 /db/storage/guolicheng/.vscode-server/vscode-server-linux-x64] cd ..
2025-04-18T21:30:41 [igoodfu@48 /db/storage/guolicheng/.vscode-server] $ ll
total 80544
drwxrwxr-x 3 guolicheng guolicheng     4096 2025-04-18T20:56:16 cli
-rwxrwxr-x 1 guolicheng guolicheng 21861176 2025-04-16T07:20:04 code-17baf841131aa23349f217ca7c570c76ee87b957
drwxr-xr-x 6 guolicheng guolicheng     4096 2025-04-16T07:42:12 vscode-server-linux-x64
-rw-r--r-- 1 guolicheng guolicheng 60601853 2025-04-18T21:20:57 vscode-server-linux-x64.tar.gz
2025-04-18T21:30:43 [igoodfu@48 /db/storage/guolicheng/.vscode-server] $ mv vscode-server-linux-x64 server
2025-04-18T21:31:00 [igoodfu@48 /db/storage/guolicheng/.vscode-server] $ ll
total 80544
drwxrwxr-x 3 guolicheng guolicheng     4096 2025-04-18T20:56:16 cli
-rwxrwxr-x 1 guolicheng guolicheng 21861176 2025-04-16T07:20:04 code-17baf841131aa23349f217ca7c570c76ee87b957
drwxr-xr-x 6 guolicheng guolicheng     4096 2025-04-16T07:42:12 server
-rw-r--r-- 1 guolicheng guolicheng 60601853 2025-04-18T21:20:57 vscode-server-linux-x64.tar.gz
2025-04-18T21:31:01 [igoodfu@48 /db/storage/guolicheng/.vscode-server] $ ls cli/servers/
lru.json                        vscode-server-linux-x64/        vscode-server-linux-x64.tar.gz  
2025-04-18T21:31:01 [igoodfu@48 /db/storage/guolicheng/.vscode-server] $ mkdir  cli/servers/Stable-17baf841131aa23349f217ca7c570c76ee87b957
2025-04-18T21:32:08 [igoodfu@48 /db/storage/guolicheng/.vscode-server] $ mv server cli/servers/Stable-17baf841131aa23349f217ca7c570c76ee87b957/
2025-04-18T21:32:24 [igoodfu@48 /db/storage/guolicheng/.vscode-server] $ cd cli/servers/Stable-17baf841131aa23349f217ca7c570c76ee87b957/
2025-04-18T21:32:30 [igoodfu@48 /db/storage/guolicheng/.vscode-server/cli/servers/Stable-17baf841131aa23349f217ca7c570c76ee87b957] $ ll
total 4
drwxr-xr-x 6 guolicheng guolicheng 4096 2025-04-16T07:42:12 server
2025-04-18T21:32:31 [igoodfu@48 /db/storage/guolicheng/.vscode-server/cli/servers/Stable-17baf841131aa23349f217ca7c570c76ee87b957] $ 

```



## chrpath

- chrpath更改rpath的一个需要注意的是，新路径的长度不能大于旧路径的长度
- patchelf的–set-rpath 选项会将二进制文件中的rpath更改为指定的rpath。我们还必须提供–force-rpath选项，否则它会更改另一个名为runpath的动态符号。这与rpath类似，但动态链接器在runpath之前使用LD_LIBRARY_PATH来定位共享库。

```bash
[root@centos8-25 /glc/mysql/app/mysql-8.0.32/bin] # chrpath -l  mysqld.test
mysqld.test: RUNPATH=$ORIGIN/../lib/private:$ORIGIN/../lib/private
[root@centos8-25 /glc/mysql/app/mysql-8.0.32/bin] # chrpath -l  mysqld
mysqld: RPATH=$ORIGIN/../lib/private:$ORIGIN/../lib/private
[root@centos8-25 /glc/mysql/app/mysql-8.0.32/bin] # chrpath -r ./foo2 mysqld.test
mysqld.test: RUNPATH=$ORIGIN/../lib/private:$ORIGIN/../lib/private
mysqld.test: new RUNPATH: ./foo2
[root@centos8-25 /glc/mysql/app/mysql-8.0.32/bin] # chrpath -l  mysqld.test
mysqld.test: RUNPATH=./foo2
[root@centos8-25 /glc/mysql/app/mysql-8.0.32/bin] # chrpath -r $ORIGIN/../lib/private:$ORIGIN/../lib/private  mysqld.test
mysqld.test: RUNPATH=./foo2
mysqld.test: new RUNPATH: /../lib/private:/../lib/private
[root@centos8-25 /glc/mysql/app/mysql-8.0.32/bin] # chrpath -l  mysqld.test
mysqld.test: RUNPATH=/../lib/private:/../lib/private


```






## 参考


- https://zhuanlan.zhihu.com/p/551770477
- https://blog.csdn.net/qq_33968692/article/details/146191922
- https://www.baeldung.com/linux/rpath-change-in-binary








