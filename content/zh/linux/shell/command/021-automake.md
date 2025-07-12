---
title: 21. automake
description: Automake（GNU Automake）是 GNU 构建工具链的一部分，主要用于自动生成符合 GNU 标准的 Makefile 文件
date: 2025-03-28
weight: 210
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}



{{< /alert >}}


## config.guess config.sub

- https://ftp.gnu.org/gnu/config/README

```bash
From now on, please fetch new versions of these scripts via the
following URLs:

 http://git.savannah.gnu.org/gitweb/?p=config.git;a=blob_plain;f=config.guess;hb=HEAD
 http://git.savannah.gnu.org/gitweb/?p=config.git;a=blob_plain;f=config.sub;hb=HEAD

Thanks,

Ben Elliston <bje@gnu.org>

```

## 2. 调试已经运行进程

```bash
gdb -p 1830

```


## 3. 调试coredump文件

```bash
gdb 可执行文件 coredump文件

```



#### config.guess


检查 config.guess 版本
```bash
./build-aux/config.guess --version
```


#### config.sub




## 4. 问题



#### 4.1 build-aux/config.guess: unable to guess system type

config.guess 版本过旧，需要手动更新config.guess 和 config.sub


**现象**

```bash
2025-03-28 21:30:00,398 p=24558 u=guolicheng n=ansible | changed: [node2]
2025-03-28 21:30:05,986 p=24558 u=guolicheng n=ansible | TASK [bison : cd /glc/bison/source/bison-2.4/build_20250328T212943;../configure --prefix=/glc/bison/app/bison-2.4 --enable-shared --enable-static] ******************************************************************************************************************************************
2025-03-28 21:30:05,998 p=24558 u=guolicheng n=ansible | fatal: [node2]: FAILED! => changed=true
  cmd: ../configure --prefix=/glc/bison/app/bison-2.4
  delta: '0:00:05.189883'
  end: '2025-03-28 21:30:05.941855'
  msg: non-zero return code
  rc: 1
  start: '2025-03-28 21:30:00.751972'
  stderr: |-
    ../build-aux/config.guess: unable to guess system type

    This script, last modified 2008-09-28, has failed to recognize
    the operating system you are using. It is advised that you
    download the most up to date version of the config scripts from

      http://git.savannah.gnu.org/gitweb/?p=config.git;a=blob_plain;f=config.guess;hb=HEAD
    and
      http://git.savannah.gnu.org/gitweb/?p=config.git;a=blob_plain;f=config.sub;hb=HEAD

    If the version you run (../build-aux/config.guess) is already up to date, please
    send the following data and any information you think might be
    pertinent to <config-patches@gnu.org> in order to provide the needed
    information to handle your system.

    config.guess timestamp = 2008-09-28

    uname -m = aarch64
    uname -r = 4.19.90-17.ky10.aarch64
    uname -s = Linux
    uname -v = #1 SMP Sun Jun 28 14:27:40 CST 2020

    /usr/bin/uname -p = aarch64
    /bin/uname -X     =

    hostinfo               =
    /bin/universe          =
    /usr/bin/arch -k       =
    /bin/arch              = aarch64
    /usr/bin/oslevel       =
    /usr/convex/getsysinfo =

    UNAME_MACHINE = aarch64
    UNAME_RELEASE = 4.19.90-17.ky10.aarch64
    UNAME_SYSTEM  = Linux
    UNAME_VERSION = #1 SMP Sun Jun 28 14:27:40 CST 2020
    configure: error: cannot guess build type; you must specify one
  stderr_lines: <omitted>
  stdout: |-
    checking for a BSD-compatible install... /usr/bin/install -c
    checking whether build environment is sane... yes
    checking for a thread-safe mkdir -p... /usr/bin/mkdir -p
    checking for gawk... gawk
    checking whether make sets $(MAKE)... yes
    checking for style of include used by make... GNU
    checking for gcc... gcc
    checking for C compiler default output file name... a.out
    checking whether the C compiler works... yes
    checking whether we are cross compiling... no
    checking for suffix of executables...
    checking for suffix of object files... o
    checking whether we are using the GNU C compiler... yes
    checking whether gcc accepts -g... yes
    checking for gcc option to accept ISO C89... none needed
    checking dependency style of gcc... gcc3
    checking for gcc option to accept ISO C99... none needed
    checking for gcc option to accept ISO Standard C... (cached) none needed
    checking for ranlib... ranlib
    checking whether gcc and cc understand -c and -o together... yes
    checking how to run the C preprocessor... gcc -E
    checking for grep that handles long lines and -e... /usr/bin/grep
    checking for egrep... /usr/bin/grep -E
    checking for ANSI C header files... yes
    checking for sys/types.h... yes
    checking for sys/stat.h... yes
    checking for stdlib.h... yes
    checking for string.h... yes
    checking for memory.h... yes
    checking for strings.h... yes
    checking for inttypes.h... yes
    checking for stdint.h... yes
    checking for unistd.h... yes
    checking minix/config.h usability... no
    checking minix/config.h presence... no
    checking for minix/config.h... no
    checking whether it is safe to define __EXTENSIONS__... yes
    checking for flex... no
    checking for lex... no
    checking for bison... no
    checking for byacc... no
    checking for ranlib... (cached) ranlib
    checking for gm4... no
    checking for gnum4... no
    checking for m4... /usr/bin/m4
    checking whether m4 supports frozen files... yes
    checking for xsltproc... /usr/bin/xsltproc
    checking locale.h usability... yes
    checking locale.h presence... yes
    checking for locale.h... yes
    checking errno.h usability... yes
    checking errno.h presence... yes
    checking for errno.h... yes
    checking for stdint.h... (cached) yes
    checking wchar.h usability... yes
    checking wchar.h presence... yes
    checking for wchar.h... yes
    checking for inttypes.h... (cached) yes
    checking for stdlib.h... (cached) yes
    checking sys/socket.h usability... yes
    checking sys/socket.h presence... yes
    checking for sys/socket.h... yes
    checking for string.h... (cached) yes
    checking for unistd.h... (cached) yes
    checking wctype.h usability... yes
    checking wctype.h presence... yes
    checking for wctype.h... yes
    checking for inline... inline
    checking whether system is Windows or MSDOS... no
    checking build system type...
  stdout_lines: <omitted>
2025-03-28 21:30:06,000 p=24558 u=guolicheng n=ansible | PLAY RECAP **********************************************************************************************************************************************************************************************************************************************************************************
2025-03-28 21:30:06,001 p=24558 u=guolicheng n=ansible | node2                      : ok=18   changed=9    unreachable=0    failed=1    skipped=8    rescued=0    ignored=0

```



**解决**

```bash
# 下载最新版脚本
wget -O config.guess "https://git.savannah.gnu.org/gitweb/?p=config.git;a=blob_plain;f=config.guess"
wget -O config.sub "https://git.savannah.gnu.org/gitweb/?p=config.git;a=blob_plain;f=config.sub"

# 将脚本放入项目目录（如 build-aux/）
mkdir -p build-aux
mv config.guess config.sub build-aux/

```


