---
title: software
description: 安装软件
date: 2023-11-05
weight: 100
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}

- 安装的二进制文件
- 安装的动态库和静态库
- 安装的头文件
- 安装的配置文件
- 安装的文档文件


{{< /alert >}}



## 常见软件包和命令


#### binutils


静态编译

```bash
./configure  CFLAGS=-static LDFLAGS=-static

./configure  CFLAGS=-static --enable-static LDFLAGS=-static --disable-shared

make CFLAGS=-static LDFLAGS=-static


```

可执行程序

-`addr2line`
-`ar`
-`as`
-`c++filt`
-`elfedit`
-`gprof`
-`ld`
-`ld.bfd`
-`nm`
-`objcopy`
-`objdump`
-`ranlib`
-`readelf`
-`size`
-`strings`
-`strip`

动态库与静态库

-`libbfd-2.32.so`
-`libbfd.a`
-`libbfd.la`
-`libbfd.so->libbfd-2.32.so`
-`libopcodes-2.32.so`
-`libopcodes.a`
-`libopcodes.la`
-`libopcodes.so->libopcodes-2.32.so`


#### coretils

可执行程序



动态库与静态库




#### diffutils


可执行程序



动态库与静态库





## 软件库


| 名称               | 功能                   | 地址                                                                                     | 备注                                                     |
|:-------------------|:-----------------------|:-----------------------------------------------------------------------------------------|:---------------------------------------------------------|
| binutils           |                        | http://ftp.gnu.org/gnu/binutils/binutils-2.16.1.tar.bz2                                  |                                                          |
| gcc                |                        | http://ftp.gnu.org/gnu/gcc/gcc-4.0.3/gcc-4.0.3.tar.bz2                                   |                                                          |
| linux-libc-headers |                        | http://ep09.pld-linux.org/~mmazur/linux-libc-headers/linux-libc-headers-2.6.12.0.tar.bz2 |                                                          |
| glibc              |                        | http://ftp.gnu.org/gnu/glibc/glibc-2.3.6.tar.bz2                                         |                                                          |
| tcl                |                        | http://prdownloads.sourceforge.net/tcl/tcl8.4.13-src.tar.gz?download                     |                                                          |
| expect             |                        | http://expect.nist.gov/src/expect-5.43.0.tar.gz                                          |                                                          |
| dejagnu            |                        |                                                                                          |                                                          |
| cmake              |                        |                                                                                          |                                                          |
| automake           | 产生Makefile文件       | http://ftp.gnu.org/gnu/automake/automake-1.9.6.tar.bz2                                   |                                                          |
| autoconf           | 生成shell脚本          | http://ftp.gnu.org/gnu/autoconf/autoconf-2.59.tar.bz2                                    |                                                          |
| bison              | 语法分析器             | http://ftp.gnu.org/gnu/bison/bison-2.2.tar.bz2                                           |                                                          |
| openssl            |                        |                                                                                          |                                                          |
| libaio             |                        |                                                                                          |                                                          |
| libcurl            |                        |                                                                                          |                                                          |
| libev              |                        |                                                                                          |                                                          |
| libgcrypt          |                        |                                                                                          |                                                          |
| ncurses            | 字符终端处理库         | ftp://invisible-island.net/ncurses/ncurses-5.5.tar.gz                                    | `with-shared、without-debug、without-ada、enable-overwrite` |
| zstd               |                        |                                                                                          |                                                          |
| procps-ng-devel    |                        |                                                                                          |                                                          |
| python3-sphinx     |                        |                                                                                          |                                                          |
| coreutils          |                        | http://ftp.gnu.org/gnu/coreutils/coreutils-5.96.tar.bz2                                  |                                                          |
| diffutils          | 显示文件或目录差异     | http://ftp.gnu.org/gnu/diffutils/diffutils-2.8.1.tar.gz                                  |                                                          |
| findutils          | 查找文件               | http://ftp.gnu.org/gnu/findutils/findutils-4.2.27.tar.gz                                 |                                                          |
| bash               |                        | http://ftp.gnu.org/gnu/bash/bash-3.1.tar.gz                                              |                                                          |
| berkeley           |                        | http://downloads.sleepycat.com/db-4.4.20.tar.gz                                          |                                                          |
| bzip2              | 压缩和解压             | http://www.bzip.org/1.0.3/bzip2-1.0.3.tar.gz                                             |                                                          |
| file               | 判断文件类型           | ftp://ftp.gw.com/mirrors/pub/unix/file/file-4.17.tar.gz                                  |                                                          |
| flex               |                        | http://prdownloads.sourceforge.net/flex/flex-2.5.33.tar.bz2?download                     |                                                          |
| gawk               |                        | http://ftp.gnu.org/gnu/gawk/gawk-3.1.5.tar.bz2                                           |                                                          |
| gettext            | 系统国际化和本地化     | http://ftp.gnu.org/gnu/gettext/gettext-0.14.5.tar.gz                                     | `--disable-shared`                                       |
| grep               |                        | http://ftp.gnu.org/gnu/grep/grep-2.5.1a.tar.bz2                                          | `--disable-perl-regexp`                                  |
| groff              |                        | http://ftp.gnu.org/gnu/groff/groff-1.18.1.1.tar.gz                                       |                                                          |
| grub               | 统一引导装载程序       | ftp://alpha.gnu.org/gnu/grub/grub-0.97.tar.gz                                            |                                                          |
| gzip               |                        | ftp://alpha.gnu.org/gnu/gzip/gzip-1.3.5.tar.gz                                           |                                                          |
| inetutils          |                        | http://ftp.gnu.org/gnu/inetutils/inetutils-1.4.2.tar.gz                                  |                                                          |
| iproute-4          | IPv4网络的程序         | http://developer.osdl.org/dev/iproute2/download/iproute2-2.6.16-060323.tar.gz            |                                                          |
| kbd                | 键盘映射表和键盘工具   | http://www.kernel.org/pub/linux/utils/kbd/kbd-1.12.tar.bz2                               |                                                          |
| less               |                        | http://www.greenwoodsoftware.com/less/less-394.tar.gz                                    |                                                          |
| libtool            | 通用库支持脚本         | http://www.gnu.org/software/libtool/                                                     |                                                          |
| m4                 | 宏处理器               | http://www.gnu.org/software/m4/                                                          |                                                          |
| make               |                        | http://www.gnu.org/software/make/                                                        |                                                          |
| patch              |                        | http://ftp.gnu.org/gnu/patch/patch-2.5.4.tar.gz                                          |                                                          |
| sed                |                        | http://ftp.gnu.org/gnu/sed/sed-4.1.5.tar.gz                                              |                                                          |
| shadow             | 处理密码               | ftp://ftp.pld.org.pl/software/shadow/shadow-4.0.15.tar.bz2                               |                                                          |
| tar                | 归档                   | http://ftp.gnu.org/gnu/tar/tar-1.15.1.tar.bz2                                            |                                                          |
| texinfo            | 读取、写入、转换Info文档 | http://ftp.gnu.org/gnu/texinfo/texinfo-4.8.tar.bz2                                       |                                                          |
| udev               |                        | http://www.kernel.org/pub/linux/utils/kernel/hotplug/udev-096.tar.bz2                    |                                                          |
| util-linux         | 加载卸载格式化分区     | http://www.kernel.org/pub/linux/utils/util-linux/util-linux-2.12r.tar.bz2                |                                                          |
| vim                |                        | ftp://ftp.vim.org/pub/vim/unix/vim-7.0.tar.bz2                                           |                                                          |
| zlib               | 压缩与解压             | http://www.zlib.net/zlib-1.2.3.tar.gz                                                    |                                                          |
| perl               |                        | http://ftp.funet.fi/pub/CPAN/src/perl-5.8.8.tar.bz2                                      | `-Dstatic_ext='Data/Dumper Fcntl IO POSIX'`              |
| readline           | 命令行编辑和历史纪录   | http://ftp.gnu.org/gnu/readline/readline-5.1.tar.gz                                      |                                                          |
| procps             | 监视系统进程           |                                                                                          |                                                          |
| psmisc             | 显示进程信息           |                                                                                          |                                                          |
| systemd            | 控制系统启停           |                                                                                          |                                                          |



- zstd
- zlib
- vim
- util-linux
- udev
- texinfo
- tcl
- tar
- systemd
- shadow
- sed
- readline
- python3-sphinx
- psmisc
- procps-ng-devel
- procps
- perl
- patch
- openssl
- ncurses
- make
- m4
- linux-libc-headers
- libtool
- libgcrypt
- libev
- libcurl
- libaio
- less
- kbd
- iproute-4
- inetutils
- gzip
- grub
- groff
- grep
- glibc
- gettext
- gcc
- gawk
- flex
- findutils
- file
- expect
- diffutils
- dejagnu
- coreutils
- cmake
- bzip2
- bison
- binutils
- berkeley
- bash
- automake
- autoconf



以下是这些软件包的功能概述：

---

### **压缩与归档工具**
1. **`tar`**:
   - **功能**: 归档工具，用于将多个文件或目录打包成一个文件。
   - **用途**: 常用于备份和分发文件。

2. **`gzip`**:
   - **功能**: 文件压缩工具，使用 DEFLATE 算法压缩文件。
   - **用途**: 压缩文件以节省空间。

3. **`bzip2`**:
   - **功能**: 文件压缩工具，使用 Burrows-Wheeler 算法压缩文件。
   - **用途**: 提供比 `gzip` 更高的压缩率。

4. **`zlib`**:
   - **功能**: 压缩库，提供 DEFLATE 算法的实现。
   - **用途**: 被许多程序用于压缩数据。

5. **`zstd`**:
   - **功能**: 高效压缩工具，提供高压缩率和快速解压速度。
   - **用途**: 适用于需要快速压缩和解压的场景。

---

### **文本处理工具**
1. **`vim`**:
   - **功能**: 强大的文本编辑器，支持多种编程语言。
   - **用途**: 编辑配置文件、代码等。

2. **`less`**:
   - **功能**: 分页查看文件内容的工具。
   - **用途**: 查看大文件内容，支持上下滚动。

3. **`file`**:
   - **功能**: 检测文件类型的工具。
   - **用途**: 确定文件的格式和编码。

4. **`sed`**:
   - **功能**: 流编辑器，用于文本替换、删除、插入等操作。
   - **用途**: 批量处理文本文件。

5. **`grep`**:
   - **功能**: 文本搜索工具，用于在文件中查找匹配的字符串。
   - **用途**: 快速查找文件中的特定内容。

6. **`gawk`**:
   - **功能**: GNU 版本的 AWK，用于文本处理和模式扫描。
   - **用途**: 处理结构化文本数据。

7. **`groff`**:
   - **功能**: 文档格式化工具，用于生成手册页和其他文档。
   - **用途**: 生成格式化的文本输出。

8. **`diffutils`**:
   - **功能**: 包含 `diff`、`cmp` 等工具，用于比较文件差异。
   - **用途**: 比较文件或目录的内容。

9. **`findutils`**:
   - **功能**: 包含 `find`、`xargs` 等工具，用于查找文件。
   - **用途**: 在文件系统中搜索文件。

10. **`patch`**:
    - **功能**: 应用补丁文件的工具。
    - **用途**: 将 `diff` 生成的补丁应用到文件中。

---

### **系统工具**
1. **`systemd`**:
   - **功能**: 系统和服务管理器，用于初始化系统和管理服务。
   - **用途**: 现代 Linux 系统的初始化系统。

2. **`shadow`**:
   - **功能**: 提供用户和组管理的工具，如 `useradd`、`passwd`。
   - **用途**: 管理用户账户和密码。

3. **`procps`**:
   - **功能**: 提供进程管理工具，如 `ps`、`top`、`free`。
   - **用途**: 监控和管理系统进程。

4. **`psmisc`**:
   - **功能**: 包含 `killall`、`pstree` 等工具，用于管理进程。
   - **用途**: 终止进程或查看进程树。

5. **`util-linux`**:
   - **功能**: 提供一系列系统工具，如 `mount`、`fdisk`、`lsblk`。
   - **用途**: 管理磁盘、分区和文件系统。

6. **`udev`**:
   - **功能**: 设备管理器，用于管理设备的动态加载和卸载。
   - **用途**: 自动加载设备驱动和创建设备节点。

7. **`kbd`**:
   - **功能**: 键盘工具，用于管理键盘布局和控制台字体。
   - **用途**: 配置键盘映射和控制台显示。

8. **`inetutils`**:
   - **功能**: 提供网络工具，如 `ping`、`ftp`、`telnet`。
   - **用途**: 网络连接和调试。

9. **`iproute`**:
   - **功能**: 提供网络配置工具，如 `ip`、`ss`。
   - **用途**: 配置网络接口和路由。

10. **`grub`**:
    - **功能**: 引导加载程序，用于启动操作系统。
    - **用途**: 管理系统的启动过程。

---

### **开发工具**
1. **`gcc`**:
   - **功能**: GNU 编译器集合，支持 C、C++ 等语言。
   - **用途**: 编译源代码。

2. **`make`**:
   - **功能**: 构建工具，用于自动化编译和安装。
   - **用途**: 管理复杂的编译过程。

3. **`cmake`**:
   - **功能**: 跨平台的构建工具，生成 Makefile 或其他构建文件。
   - **用途**: 管理大型项目的构建过程。

4. **`autoconf`**:
   - **功能**: 生成配置脚本的工具。
   - **用途**: 自动检测系统环境并生成 `configure` 脚本。

5. **`automake`**:
   - **功能**: 生成 Makefile 的工具。
   - **用途**: 简化 Makefile 的编写。

6. **`libtool`**:
   - **功能**: 管理库的创建和链接。
   - **用途**: 简化共享库的构建过程。

7. **`bison`**:
   - **功能**: 语法分析器生成器。
   - **用途**: 生成解析器代码。

8. **`flex`**:
   - **功能**: 词法分析器生成器。
   - **用途**: 生成词法分析器代码。

9. **`binutils`**:
   - **功能**: 提供二进制工具，如 `ld`、`as`、`objdump`。
   - **用途**: 处理目标文件和可执行文件。

10. **`dejagnu`**:
    - **功能**: 测试框架，用于自动化测试。
    - **用途**: 测试软件的正确性。

11. **`expect`**:
    - **功能**: 自动化交互式工具，用于模拟用户输入。
    - **用途**: 自动化脚本中的交互式操作。

12. **`tcl`**:
    - **功能**: 脚本语言，常用于自动化任务。
    - **用途**: 编写自动化脚本。

---

### **库文件**
1. **`glibc`**:
   - **功能**: GNU C 库，提供标准 C 函数和系统调用。
   - **用途**: 所有 Linux 程序的基础库。

2. **`readline`**:
   - **功能**: 命令行编辑库，支持历史记录和自动补全。
   - **用途**: 增强命令行工具的用户体验。

3. **`libgcrypt`**:
   - **功能**: 加密库，提供多种加密算法。
   - **用途**: 实现数据加密和解密。

4. **`libev`**:
   - **功能**: 高性能事件循环库。
   - **用途**: 实现异步事件驱动编程。

5. **`libcurl`**:
   - **功能**: 网络传输库，支持多种协议（如 HTTP、FTP）。
   - **用途**: 实现网络数据传输。

6. **`libaio`**:
   - **功能**: 异步 I/O 库，提供异步文件操作支持。
   - **用途**: 提高文件 I/O 性能。

7. **`ncurses`**:
   - **功能**: 终端控制库，用于创建文本用户界面。
   - **用途**: 开发终端应用程序。

8. **`openssl`**:
   - **功能**: 加密库，提供 SSL/TLS 协议和加密算法。
   - **用途**: 实现安全的网络通信。

9. **`m4`**:
   - **功能**: 宏处理器，用于生成文本文件。
   - **用途**: 自动化生成配置文件。

10. **`berkeley`**:
    - **功能**: Berkeley DB，嵌入式数据库库。
    - **用途**: 提供轻量级的数据库支持。

---

### **其他工具**
1. **`coreutils`**:
   - **功能**: 提供核心工具，如 `ls`、`cp`、`mkdir`。
   - **用途**: 基本的文件操作和系统管理。

2. **`gettext`**:
   - **功能**: 国际化工具，用于多语言支持。
   - **用途**: 实现软件的本地化。

3. **`texinfo`**:
   - **功能**: 文档系统，用于生成在线文档。
   - **用途**: 编写和发布软件文档。

4. **`python3-sphinx`**:
   - **功能**: 文档生成工具，基于 Python。
   - **用途**: 生成高质量的文档。

5. **`perl`**:
   - **功能**: 脚本语言，广泛用于系统管理和文本处理。
   - **用途**: 编写自动化脚本。

6. **`bash`**:
   - **功能**: 命令行 shell，提供脚本编程功能。
   - **用途**: 系统管理和自动化任务。

---

### **总结**
这些软件包涵盖了系统管理、开发工具、文本处理、压缩归档、网络工具等多个领域。每个工具或库都有其特定的用途，共同构成了 Linux 系统的基础功能。如果你对某个工具或库有更详细的问题，可以进一步讨论！







## xxx与xxx-devel的区别

| 区别       | xxx | xxx-devel | 备注 |
|:---------|:----|:----------|:-----|
| 执行文件   | 有  | 无        |      |
| 动态库     | 有  | 无        |      |
| 配置文件   | 有  | 无        |      |
| 头文件     | 无  | 有        |      |
| 静态库     | 无  | 有        |      |
| 文档或示例 | 无  | 有        |      |



### 举例说明

> openssl
- openssl-1.0.2k-26.el7_9.x86_64.rpm 执行码部分
- openssl-devel-1.0.2k-26.el7_9.x86_64.rpm 这个就是包含了头文件，头文件参考，某些库文件等跟开发相关的东西。
- 对openssl进行源码编译安装后得到的东西就是openssl-1.0.2k-26.el7_9.x86_64.rpm和openssl-devel-1.0.2k-26.el7_9.x86_64.rpm一起的内容


























