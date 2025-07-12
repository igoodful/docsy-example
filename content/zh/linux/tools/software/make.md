---
title: make
description: make
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
依赖如下：
- gcc
- make
- glic

{{</note>}}


### 一、安装
#### 1、源码安装
```bash
wget https://cmake.org/files/v3.28/cmake-3.28.0.tar.gz

tar -xzvf cmake-3.28.0.tar.gz && cd  cmake-3.28.0

./configure --prefix=$HOME/local

make

make check

make install

```


```bash
ln -s $HOME/local/bin/make $HOME/local/bin/gmake

```



## 问题记录




#### 1. These critical programs are missing or too old: make


```bash
TASK [glibc : cd /glc/glibc/source/glibc-2.40/build_20250416T024835;../configure --prefix=/glc/glibc/app/glibc-2.40] *******************************************************************************************************************************************************************************
fatal: [node1]: FAILED! => changed=true 
  cmd: '../configure --prefix=/glc/glibc/app/glibc-2.40 '
  delta: '0:00:03.083408'
  end: '2025-04-16 02:50:29.027181'
  msg: non-zero return code
  rc: 1
  start: '2025-04-16 02:50:25.943773'
  stderr: |-
    configure: error:
    *** These critical programs are missing or too old: make
    *** Check the INSTALL file for required versions.
  stderr_lines: <omitted>
  stdout: |-
    checking build system type... x86_64-pc-linux-gnu
    checking host system type... x86_64-pc-linux-gnu
    checking for gcc... gcc
    checking for suffix of object files... o
    checking whether the compiler supports GNU C... yes
    checking whether gcc accepts -g... yes
    checking for gcc option to enable C11 features... none needed
    checking for g++... g++
    checking whether the compiler supports GNU C++... yes
    checking whether g++ accepts -g... yes
    checking for g++ option to enable C++11 features... none needed
    checking whether g++ can link programs... yes
    checking for sysdeps preconfigure fragments... aarch64 alpha arc arm csky hppa i386 loongarch m68k microblaze checking for grep that handles long lines and -e... /usr/bin/grep
    checking for egrep... /usr/bin/grep -E
    mips nios2 or1k powerpc riscv s390 sh checking for grep that handles long lines and -e... (cached) /usr/bin/grep
    checking for egrep... (cached) /usr/bin/grep -E
    sparc x86_64 checking whether gcc compiles in -mx32 mode by default... no
  
    checking for a BSD-compatible install... /usr/bin/install -c
    checking whether ln -s works... yes
    checking for ld... ld
    checking version of ld... 2.27, ok
    checking for gnumake... no
    checking for gmake... gmake
    checking version of gmake... 3.82, bad
    checking for gnumsgfmt... no
    checking for gmsgfmt... no
    checking for msgfmt... msgfmt
    checking version of msgfmt... 0.19.8.1, ok
    checking for makeinfo... makeinfo
    checking version of makeinfo... 5.1, ok
    checking for sed... sed
    checking version of sed... 4.2.2, ok
    checking for gawk... gawk
    checking version of gawk... 4.0.2, ok
    checking for bison... bison
    checking version of bison... 3.0.4, ok
    checking if gcc is sufficient to build libc... yes
    checking for python3... python3
    checking version of python3... 3.9.13, ok
  stdout_lines: <omitted>

```

- 原因：make版本太低
- 解决：升级到make-4.2.1



