---
title: 17. autoscan
description: autoscan扫描源码生成configure.scan文件
date: 2025-03-28
weight: 170
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}

GNU Autotools  项目结构

- NEWS
- README
- AUTHORS
- ChangeLog

{{< /alert >}}

autoscan 命令会扫描指定的源代码目录（默认为当前目录），并生成一个名为 configure.scan 的文件。这个文件包含了 autoconf 脚本所需的大部分 m4 宏调用，以及可能需要的 Makefile.am 文件和 Makefile.in 文件的框架


## 1. 图解



```viz-dot
digraph "autoscan" {
node [
        fontsize = "12"
];

"autoscan" [ label = "autoscan",style="filled",fontcolor="navy",color="beige" ];
"autoscan.log" [ label = "autoscan.log", shape = octagon ];
"configure.scan" [ label = "configure.scan", shape = octagon ];
"configure.ac" [ label = "configure.ac", shape = octagon ];
"aclocal" [ label = "aclocal",style="filled",fontcolor="navy",color="beige"  ];
"autoheader" [ label = "autoheader",style="filled",fontcolor="navy",color="beige" ];
"Makefile.am" [ label = "Makefile.am", shape = octagon ];
"Makefile.in" [ label = "Makefile.in", shape = octagon ];
"aclocal.m4" [ label = "aclocal.m4", shape = octagon ];
"autom4te.cache" [ label = "autom4te.cache", shape = octagon ];
"config.h.in" [ label = "config.h.in", shape = octagon ];
"autoconf" [ label = "autoconf",style="filled",fontcolor="navy",color="beige"  ];
"automake" [ label = "automake --add-missing" ,style="filled",fontcolor="navy",color="beige" ];
"configure" [ label = "configure" ,style="filled",fontcolor="navy",color="beige" ];
"depcomp" [ label = "depcomp", shape = octagon ];
"missing" [ label = "missing", shape = octagon ];
"stamp-h1" [ label = "stamp-h1", shape = octagon ];
"Makefile" [ label = "Makefile", shape = octagon ];
"config.h" [ label = "config.h", shape = octagon ];
"install-sh" [ label = "install-sh", shape = octagon ];
"make" [ label = "make",style="filled",fontcolor="navy",color="beige"  ];



"autoscan" -> "autoscan.log";
"autoscan" -> "configure.scan";
"configure.scan" -> "configure.ac";
"configure.ac" -> "aclocal";
"configure.ac" -> "autoheader";
"configure.ac" -> "automake";
"configure.ac" -> "autoconf";
"aclocal" -> "aclocal.m4";
"aclocal" -> "autom4te.cache";
"aclocal.m4" -> "autoconf";
"autoheader" -> "config.h.in";
"config.h.in" -> "automake";
"autoconf" -> "configure";
"Makefile.am" -> "automake";
"automake" -> "Makefile.in";
"Makefile.in" -> "configure";
"configure" -> "Makefile";
"configure" -> "config.h";
"configure" -> "depcomp";
"configure" -> "missing";
"configure" -> "stamp-h1";
"configure" -> "install-sh";
"Makefile" -> "make";
"config.h" -> "make";
}

```


autoscan会默认扫描当前目录

```bash





```

## 2. 调试已经运行进程

```bash
gdb -p 1830

```


## 3. 调试coredump文件

```bash
gdb 可执行文件 coredump文件

```
