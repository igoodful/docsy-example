---
title: g++
description: g++
date: 2023-10-31
weight: 200000
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
[gcc](https://gcc.gnu.org/onlinedocs/gcc-11.4.0/gcc/Option-Summary.html)
{{< /alert >}}


{{< alert title=" g++参数详解" color="secondary">}}

{{< /alert >}}




| 选项                | 作用                                                                                             | 示例                          |
| :------------------ | :----------------------------------------------------------------------------------------------- | :---------------------------- |
| `-std`              | 指定`c/c++`的标准                                                                                | `-std=c++17`                  |
| `-o`                | 指定生成可执行文件的名称                                                                         | `-o dir/tmp/mi_open.cc.o`     |
| `-c`                | 只编译，不链接，只生成目标文件                                                                   |                               |
| `-g`                | 添加`gdb`调试选项                                                                                |                               |
| `-Wall`             | 编译后显示所有警告                                                                               |                               |
| `-Werror`           | 要求`g++`将所有的警告当成错误进行处理                                                            |                               |
| `-Wextra`           | 比`-Wall`更多的警告信息                                                                          |                               |
| `-MD`               | 生成.d依赖文件，记录头文件的依赖关系，通常会加上                                                 |                               |
| `-fPIC`             | 生成动态链接库的目标文件时，必须添加该选项。生成静态库，但被其它动态库时用时，也需要添加该选项。 |                               |
| `-I`                | 添加一个头文件搜索路径                                                                           | `-I/include2`  `-I=/include8` |
| `-L`                | 指定一个库文件搜索路径                                                                           | `-I/lib2`   `-I=/lib8`        |
| `--sysroot=dir`     | 设置目标平台根目录，改变系统默的认头文件、库文件搜索路径，尤其是交叉编译场景                     | `--sysroot=/root/arm64`       |
| `-Wformat-security` | 警告格式函数可能存在的安全问题                                                                   |                               |
| `-Wvla`             | 如果使用可变长度数组则发出警告                                                                   |                               |
| `-Wundef`           | 如果`#if`指令中使用了未定义的宏，则发出警告                                                      |                               |
| `-isysroot`         |                                                                                                  |                               |
| `-shared`           | 生成共享目标文件。通常用在建立共享库时                                                           |                               |
| `-static`           | 禁止使用共享连接                                                                                 |                               |
| `-w`                | 不生成任何警告信息                                                                               |                               |
| `--help`            | 帮助信息                                                                                         |                               |
| `-v`                | 显示编译器调用的程序                                                                             |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |
| ``                  |                                                                                                  |                               |





## 头文件搜索顺序
gcc 在编译时如何去寻找所需要的头文件 ：

header file的搜寻会从-I开始
然后找gcc的环境变量 C_INCLUDE_PATH,CPLUS_INCLUDE_PATH,OBJC_INCLUDE_PATH
再找内定目录 :
/usr/include
/usr/local/include
/usr/lib/gcc-lib/i386-linux/2.95.2/include
/usr/lib/gcc-lib/i386-linux/2.95.2/../../../../include/g++-3
/usr/lib/gcc-lib/i386-linux/2.95.2/../../../../i386-linux/include
库文件但是如果装gcc的时候，是有给定的prefix的话，那么就是
/usr/include
prefix/include
prefix/xxx-xxx-xxx-gnulibc/include
prefix/lib/gcc-lib/xxxx-xxx-xxx-gnulibc/2.8.1/include

1. -I
2. C_INCLUDE_PATH,CPLUS_INCLUDE_PATH,OBJC_INCLUDE_PATH
3. 系统目录：`/usr/include`,`/usr/local/include`,

```bash

header file的搜寻会从-I开始
然后找gcc的环境变量
再找内定目录 :


/usr/lib/gcc-lib/i386-linux/2.95.2/include
/usr/lib/gcc-lib/i386-linux/2.95.2/../../../../include/g++-3
/usr/lib/gcc-lib/i386-linux/2.95.2/../../../../i386-linux/include

```

## 库文件搜索顺序

cos()等函式库的选项要多加 -lm

编译的时候:gcc会去找-L
再找gcc的环境变量LIBRARY_PATH
再找内定目录 /lib /usr/lib /usr/local/lib 这是当初compile gcc时写在程序内的


> 查看默认动态库搜索路径
```bash

[root@dev /root]# g++ -print-search-dirs |  grep libraries | sed 's/libraries: =//g' | tr ':' '\n' | xargs readlink -f
/opt/rh/gcc-toolset-12/root/usr/lib/gcc/x86_64-redhat-linux/12
/opt/rh/gcc-toolset-12/root/usr/lib64
/usr/lib64
/usr/lib64
/opt/rh/gcc-toolset-12/root/usr/lib
/usr/lib
/usr/lib

[root@dev /root]# g++ --sysroot=/home/mysql/rootfs -print-search-dirs  |  grep libraries | sed 's/libraries: =//g' | tr ':' '\n' | xargs readlink -f


```


经过测试和验证发现，
- -Idir编译器只会从dir路径下搜索头文件；
- -I=dir或-I$SYSROOT/dir则会受--sysroot影响。

经过测试和验证，发现结果和-I选项类似
- -Ldir编译器只会从dir路径下搜索依赖库；
- -L=dir或-I$SYSROOT/dir则会受--sysroot影响。









```bash

[root@dev /root/build-8032]# which g++
/opt/rh/gcc-toolset-12/root/usr/bin/g++

[root@dev /root/build-8032]# g++ --version
g++ (GCC) 12.2.1 20221121 (Red Hat 12.2.1-7)
Copyright (C) 2022 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.














```

### sysroot

- 原本默认会从/usr/include目录中搜索头文件、从/usr/lib中搜索依赖库，
- 当设置了--sysroot=dir后则会从dir/usr/include搜索头文件、从dir/usr/lib中搜索依赖库








