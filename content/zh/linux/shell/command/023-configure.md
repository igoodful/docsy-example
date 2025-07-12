---
title: 23. configure
description: configure
date: 2025-03-28
weight: 230
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}



{{< /alert >}}


在 GNU 软件包中，`configure` 脚本是一个由 **Autotools**（Autoconf、Automake 和 Libtool）生成的 shell 脚本，用于**自动化系统环境检测和编译配置**。它负责检查目标系统的编译环境、依赖库和头文件，并生成适配当前平台的 `Makefile` 文件。以下是其核心功能和实现细节的详细解析。

---

### **一、`configure` 脚本的核心功能**
1. **系统环境检测**
   - 检查 C/C++ 编译器、链接器、汇编器等工具链的可用性。
   - 检测系统架构（如 x86_64、ARM）、操作系统类型（Linux、BSD、macOS）及版本。
   - 验证标准头文件（如 `stdio.h`）和库函数（如 `malloc`）是否存在。

2. **依赖项检查**
   - 检查第三方库（如 OpenSSL、zlib）的安装路径、版本兼容性。
   - 验证动态库（`.so`、`.dylib`）或静态库（`.a`）的链接能力。

3. **生成构建配置**
   - 根据检测结果生成 `Makefile` 文件，定义编译选项（如 `CFLAGS`、`LDFLAGS`）。
   - 生成 `config.h` 头文件，包含平台特性宏（如 `HAVE_STDIO_H`）。

4. **自定义编译选项**
   - 支持通过命令行参数（如 `--prefix`、`--enable-debug`）定制安装路径和功能模块。

---

### **二、`configure` 脚本的生成过程**
`configure` 脚本由 `configure.ac`（Autoconf 输入文件）通过以下步骤生成：
```bash
# 步骤 1: 生成 aclocal.m4（宏定义集合）
aclocal

# 步骤 2: 生成 configure 脚本
autoconf

# 步骤 3: 生成 Makefile.in（模板文件）
automake --add-missing
```

---

### **三、`configure` 脚本的结构解析**
以下是一个典型 `configure` 脚本的逻辑结构：

#### **1. 初始化与参数解析**
```shell
#!/bin/sh

# 定义版本和基础变量
PACKAGE_NAME="hello_world"
PACKAGE_VERSION="1.0"
prefix="/usr/local"

# 解析命令行参数（如 --prefix=/opt）
for arg in "$@"; do
  case $arg in
    --prefix=*)
      prefix="${arg#*=}"
      ;;
    --enable-debug)
      enable_debug=yes
      ;;
    # 更多参数处理...
  esac
done
```

#### **2. 系统工具检测**
```shell
# 检查 C 编译器
if test -z "$CC"; then
  CC=gcc
fi

# 检查 make 工具
AC_PROG_MAKE_SET

# 检查 ranlib（静态库需要）
AC_PROG_RANLIB
```

#### **3. 头文件与库函数检查**
```shell
# 检查 stdio.h 是否存在
AC_CHECK_HEADER([stdio.h],
  [AC_DEFINE([HAVE_STDIO_H], [1], [Define to 1 if you have <stdio.h>.])],
  [AC_MSG_ERROR([stdio.h not found])]

# 检查 malloc 函数
AC_CHECK_FUNC([malloc], [],
  [AC_MSG_ERROR([malloc() not available])]
```

#### **4. 第三方库检测**
```shell
# 检查 zlib 库
AC_CHECK_LIB([z], [inflate],
  [LIBS="-lz $LIBS"],
  [AC_MSG_ERROR([zlib not found])]
```

#### **5. 生成输出文件**
```shell
# 生成 Makefile
AC_CONFIG_FILES([Makefile src/Makefile])

# 生成 config.h
AC_CONFIG_HEADERS([config.h])

# 完成配置
AC_OUTPUT
```

---

### **四、常用 `configure` 命令行参数**
| **参数**              | **作用**                          | **示例**                      |
| --------------------- | --------------------------------- | ----------------------------- |
| `--prefix=PATH`       | 指定安装路径（默认 `/usr/local`） | `--prefix=/opt/myapp`         |
| `--enable-FEATURE`    | 启用某个功能模块                  | `--enable-debug`              |
| `--disable-FEATURE`   | 禁用某个功能模块                  | `--disable-shared`            |
| `--with-LIBRARY=PATH` | 指定第三方库路径                  | `--with-openssl=/opt/openssl` |
| `--without-LIBRARY`   | 禁用对某个库的依赖                | `--without-zlib`              |
| `CFLAGS="FLAGS"`      | 自定义 C 编译器选项               | `CFLAGS="-O2 -g"`             |
| `LDFLAGS="FLAGS"`     | 自定义链接器选项                  | `LDFLAGS="-L/opt/lib"`        |

---

### **五、`configure` 的执行流程**
1. **初始化**
   - 解析命令行参数，设置默认路径和选项。
2. **系统检测**
   - 运行测试代码片段，检查编译器、库、头文件等。
3. **生成配置**
   - 将检测结果写入 `config.h` 和 `Makefile`。
4. **输出报告**
   - 显示摘要信息（如安装路径、启用的功能）。

---

### **六、调试与问题排查**
#### **1. 查看详细输出**
```bash
./configure --verbose  # 显示详细的检测过程
```

#### **2. 检查 `config.log`**
所有检测步骤的日志记录在 `config.log` 中：
```bash
less config.log  # 查找 "error" 或 "checking" 关键字
```

#### **3. 手动指定工具链**
```bash
# 指定交叉编译器
./configure --host=arm-linux-gnueabihf CC=arm-linux-gnueabihf-gcc
```

---

### **七、示例：一个典型的配置过程**
```bash
# 下载并解压源码
wget https://example.com/hello_world-1.0.tar.gz
tar -xzf hello_world-1.0.tar.gz
cd hello_world-1.0

# 生成 configure 脚本（如果未预先生成）
autoreconf -iv

# 配置项目
./configure \
  --prefix=/opt/hello \
  --enable-debug \
  CFLAGS="-O0 -g"

# 编译并安装
make && sudo make install
```

---

### **八、常见问题与解决**
| **问题**                                                 | **解决方案**                                                     |
| -------------------------------------------------------- | ---------------------------------------------------------------- |
| `configure: error: C compiler cannot create executables` | 检查编译器是否安装（如 `gcc`）、环境变量 `PATH` 是否正确。       |
| `library not found`                                      | 确保依赖库已安装，或通过 `--with-LIB=DIR` 指定路径。             |
| `undefined reference to symbol`                          | 确认链接库顺序（`LIBS` 变量）或添加 `-l<libname>` 到 `LDFLAGS`。 |
| 无法识别新硬件架构（如 `aarch64`）                       | 更新 `config.guess` 和 `config.sub` 到最新版本。                 |

---

### **总结**
`configure` 脚本是 GNU 软件跨平台编译的基石，通过自动化环境检测和配置，极大简化了软件移植和部署的复杂性。理解其工作原理和调试方法，能帮助开发者高效解决编译问题，并定制符合需求的构建选项。





## /usr/bin/ld: cannot find -lc


```bash
Supported LTO compression algorithms: zlib
gcc version 11.4.0 (GCC)
configure:3188: $? = 0
configure:3177: gcc -V >&5
gcc: error: unrecognized command-line option '-V'
gcc: fatal error: no input files
compilation terminated.
configure:3188: $? = 1
configure:3177: gcc -qversion >&5
gcc: error: unrecognized command-line option '-qversion'; did you mean '--version'?
gcc: fatal error: no input files
compilation terminated.
configure:3188: $? = 1
configure:3177: gcc -version >&5
gcc: error: unrecognized command-line option '-version'
gcc: fatal error: no input files
compilation terminated.
configure:3188: $? = 1
configure:3208: checking whether the C compiler works
configure:3230: gcc -O  -static -L/glc/glibc/app/glibc-2.28/my_static_lib conftest.c  >&5
/usr/bin/ld: cannot find -lc
collect2: error: ld returned 1 exit status
configure:3234: $? = 1
configure:3274: result: no
configure: failed program was:
| /* confdefs.h */
| #define PACKAGE_NAME ""
| #define PACKAGE_TARNAME ""
| #define PACKAGE_VERSION ""
| #define PACKAGE_STRING ""
| #define PACKAGE_BUGREPORT ""
| #define PACKAGE_URL ""
| /* end confdefs.h.  */
|
| int
| main (void)
| {
|
|   ;
|   return 0;
| }
configure:3279: error: in `/glc/socat/source/socat-1.8.0.3':
configure:3281: error: C compiler cannot create executables
See `config.log' for more details

```

- 原因：静态编译找不到c静态库，centos8默认没有静态c库
- 解决：先编译一个glibc库，且包含静态库









