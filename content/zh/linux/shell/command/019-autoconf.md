---
title: 19. autoconf
description: autoconf
date: 2025-03-28
weight: 190
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

configure.ac

{{< alert >}}



{{< /alert >}}


## 1. 原理



```viz-dot
digraph "autoconf" {
node [
        fontsize = "12"
];

    "configure.ac" [ label = "configure.ac", shape = octagon ];
    "configure" [ label = "configure", shape = octagon ];

    "configure.ac" -> "configure"[label="autoconf"];



}
```


## 1. configure.ac



以下是 `configure.ac` 文件的详细解析，它是 Autoconf 工具链的核心输入文件，用于生成 `configure` 脚本，最终实现跨平台编译配置。我们将分模块详解其内容及常用宏的作用。

---

### **1. 基础结构**
#### **(1) 初始化项目信息**
```m4
AC_INIT([项目名称], [版本号], [维护者邮箱], [项目URL], [源码包名称])
```
- **作用**：定义项目元数据，是文件的**必须宏**。
- **示例**：
  ```m4
  AC_INIT([hello_world], [1.0], [contact@example.com], [https://example.com], [hello_world])
  ```

#### **(2) 初始化 Automake**
```m4
AM_INIT_AUTOMAKE([选项列表])
```
- **作用**：启用 Automake 支持，指定构建规则模式（如 `foreign` 避免 GNU 严格检查）。
- **示例**：
  ```m4
  AM_INIT_AUTOMAKE([foreign subdir-objects -Wall -Werror])
  ```
  - `foreign`：宽松模式，无需 `NEWS`、`ChangeLog` 等文件。
  - `subdir-objects`：允许源码存放在子目录中。
  - `-Wall -Werror`：启用所有 Automake 警告并视警告为错误。

---

### **2. 检查编译工具链**
#### **(1) 检查 C 编译器**
```m4
AC_PROG_CC
```
- **作用**：检测系统可用的 C 编译器（如 `gcc`、`clang`），并设置 `CC` 变量。

#### **(2) 检查其他工具**
```m4
AC_PROG_RANLIB        # 检查 ranlib（静态库需要）
AC_PROG_INSTALL       # 检查 install 工具
AC_PROG_LEX           # 检查 lex/flex
AC_PROG_YACC          # 检查 yacc/bison
AC_PROG_MAKE_SET      # 确保 make 支持 $(MAKE)
```

---

### **3. 检查系统头文件和库**
#### **(1) 检查标准头文件**
```m4
AC_CHECK_HEADERS([头文件列表])
```
- **示例**：
  ```m4
  AC_CHECK_HEADERS([stdio.h stdlib.h unistd.h])
  ```

#### **(2) 检查库函数**
```m4
AC_CHECK_FUNCS([函数列表])
```
- **示例**：
  ```m4
  AC_CHECK_FUNCS([strdup malloc memset])
  ```

#### **(3) 检查第三方库**
```m4
AC_CHECK_LIB([库名], [函数名], [成功动作], [失败动作])
```
- **示例**：检查 `libm` 中的 `pow` 函数：
  ```m4
  AC_CHECK_LIB([m], [pow], [], [
    AC_MSG_ERROR([libm not found or missing pow()])
  ])
  ```

---

### **4. 定义自定义配置选项**
#### **(1) 添加 `--enable-feature` 选项**
```m4
AC_ARG_ENABLE([feature],
  [AS_HELP_STRING([--enable-feature], [启用某功能])],
  [enable_feature=$enableval],
  [enable_feature=no]
)
```
- **用法**：
  ```bash
  ./configure --enable-feature
  ```

#### **(2) 添加 `--with-library` 选项**
```m4
AC_ARG_WITH([library],
  [AS_HELP_STRING([--with-library=PATH], [指定库路径])],
  [LIB_PATH=$withval],
  [LIB_PATH=/usr/local]
)
```

---

### **5. 生成输出文件**
#### **(1) 指定生成的 Makefile**
```m4
AC_CONFIG_FILES([Makefile src/Makefile lib/Makefile])
```

#### **(2) 结束配置**
```m4
AC_OUTPUT
```

---

### **6. 完整示例**
```m4
# 初始化项目
AC_INIT([hello_world], [1.0], [contact@example.com])
AM_INIT_AUTOMAKE([foreign subdir-objects -Wall -Werror])

# 检查编译工具
AC_PROG_CC
AC_PROG_RANLIB

# 检查头文件和函数
AC_CHECK_HEADERS([stdio.h stdlib.h])
AC_CHECK_FUNCS([printf malloc])

# 检查数学库
AC_CHECK_LIB([m], [pow], [], [
  AC_MSG_ERROR([libm not found or missing pow()])
])

# 自定义选项
AC_ARG_ENABLE([debug],
  [AS_HELP_STRING([--enable-debug], [启用调试模式])],
  [CFLAGS="$CFLAGS -g -O0"],
  [CFLAGS="$CFLAGS -O2"]
)

# 生成文件
AC_CONFIG_FILES([Makefile src/Makefile])
AC_OUTPUT
```

---

### **7. 关键宏详解**
| **宏**              | **作用**                                    | **常见参数**           |
| ------------------- | ------------------------------------------- | ---------------------- |
| `AC_MSG_ERROR`      | 输出错误信息并终止配置过程                  | 错误描述字符串         |
| `AC_MSG_WARNING`    | 输出警告信息但继续执行                      | 警告描述字符串         |
| `AC_DEFINE`         | 定义 C 预处理器宏（写入 `config.h`）        | 宏名、值、描述（可选） |
| `AC_SUBST`          | 将变量导出到生成的 Makefile                 | 变量名                 |
| `PKG_CHECK_MODULES` | 检查 pkg-config 提供的依赖库（需 `pkg.m4`） | 包名、变量前缀         |

---

### **8. 调试与验证**
#### **(1) 生成并运行配置**
```bash
autoreconf -iv    # 重新生成配置脚本
./configure       # 执行配置检测
```

#### **(2) 查看生成的 `config.log`**
```bash
less config.log   # 排查配置失败原因
```

#### **(3) 检查 `config.h`**
生成的 `config.h` 包含所有检测到的系统特性宏：
```c
/* Define to 1 if you have the <stdio.h> header file. */
#define HAVE_STDIO_H 1

/* Define to 1 if you have the `malloc' function. */
#define HAVE_MALLOC 1
```

---

### **9. 常见问题**
#### **(1) 配置时报错 `undefined macro`**
- **原因**：缺少 `aclocal` 步骤或未安装依赖的宏文件。
- **解决**：
  ```bash
  aclocal && autoconf
  ```

#### **(2) 检测不到新安装的库**
- **原因**：`PKG_CONFIG_PATH` 未设置。
- **解决**：
  ```bash
  export PKG_CONFIG_PATH=/path/to/lib/pkgconfig:$PKG_CONFIG_PATH
  ```

#### **(3) 跨平台兼容性问题**
- **解决**：更新 `config.guess` 和 `config.sub` 至最新版本。

---

通过合理配置 `configure.ac`，可以确保项目在不同平台和环境中正确编译。建议结合 `automake` 和 `autoconf` 文档深入理解每个宏的细节。



## 2. 调试已经运行进程

```bash
gdb -p 1830

```


## 3. 调试coredump文件

```bash
gdb 可执行文件 coredump文件

```
