---
title: awk
description: awk
date: 2025-03-22
weight: 102
viz: true

---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


{{<note>}}
<!---->

- https://sourceforge.net/p/acl/
- https://github.com/acl-dev/acl/


{{</note>}}


## 1. 依赖

```viz-dot
digraph "awk" {
node [
        fontsize = "12"
];
    "awk" [ label = "awk", shape = octagon ];
    "readline" [ label = "readline", shape = octagon ];
    "libreadline" [ label = "libreadline", shape = octagon ];
    "ncurses-libs" [ label = "ncurses-libs", shape = octagon ];
    "libtinfo" [ label = "libtinfo", shape = octagon ];
    "glibc" [ label = "glibc", shape = octagon ];
    "libdl" [ label = "libdl", shape = octagon ];
    "libm" [ label = "libm", shape = octagon ];
    "libc" [ label = "libc", shape = octagon ];
     "/lib64/ld-linux-x86-64.so.2" [ label = "/lib64/ld-linux-x86-64.so.2", shape = octagon ];

    "awk" -> "readline";
    "readline" -> "libreadline";
    "awk" -> "ncurses-libs";
    "ncurses-libs" -> "libtinfo";
    "awk" -> "glibc";
    "glibc" -> "libdl";
    "glibc" -> "libm";
    "glibc" -> "libc";
    "glibc" -> "/lib64/ld-linux-x86-64.so.2";
}
```

## 2. 内容


### 2.1 bin

```viz-dot
digraph "awk" {
node [
        fontsize = "12"
];
    "awk" [ label = "awk", shape = octagon ];
    "gawk" [ label = "gawk", shape = octagon ];
    "getfacl" [ label = "getfacl", shape = octagon ];
    "setfacl" [ label = "setfacl", shape = octagon ];
    "awk.so" [ label = "awk.so", shape = octagon ];
    "acl/awk.h" [ label = "acl/awk.h", shape = octagon ];
    "sys/acl.h" [ label = "sys/acl.h", shape = octagon ];

    "awk" -> "gawk";
    "awk" -> "getfacl";
    "awk" -> "setfacl";
    "awk" -> "awk.so";
    "awk" -> "acl/awk.h";
    "awk" -> "sys/acl.h";
}
```




## 3. 安装


#### 3.1 yum安装
```bash


```




#### 3.2 源码安装

```bash
2025-03-24T19:35:21 [mysql@RDS-146 /glc/gawk/app/gawk-5.3.1/bin] $ ls |xargs ldd
awk:
	linux-vdso.so.1 =>  (0x00007ffc741af000)
	libreadline.so.6 => /lib64/libreadline.so.6 (0x00007fe120a00000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007fe1207fc000)
	libm.so.6 => /lib64/libm.so.6 (0x00007fe1204fa000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fe12012c000)
	libtinfo.so.5 => /lib64/libtinfo.so.5 (0x00007fe11ff02000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fe120c46000)
gawk:
	linux-vdso.so.1 =>  (0x00007ffcfd388000)
	libreadline.so.6 => /lib64/libreadline.so.6 (0x00007fc679016000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007fc678e12000)
	libm.so.6 => /lib64/libm.so.6 (0x00007fc678b10000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fc678742000)
	libtinfo.so.5 => /lib64/libtinfo.so.5 (0x00007fc678518000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fc67925c000)
gawk-5.3.1:
	linux-vdso.so.1 =>  (0x00007ffc3e3f3000)
	libreadline.so.6 => /lib64/libreadline.so.6 (0x00007f5652ecf000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f5652ccb000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f56529c9000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f56525fb000)
	libtinfo.so.5 => /lib64/libtinfo.so.5 (0x00007f56523d1000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f5653115000)
gawkbug:
	not a dynamic executable
2025-03-24T19:35:25 [mysql@RDS-146 /glc/gawk/app/gawk-5.3.1/bin] $ cd ../lib
lib/     libexec/
2025-03-24T19:35:25 [mysql@RDS-146 /glc/gawk/app/gawk-5.3.1/bin] $ cd ../lib/gawk/
2025-03-24T19:35:35 [mysql@RDS-146 /glc/gawk/app/gawk-5.3.1/lib/gawk] $ ls |xargs ldd
filefuncs.so:
	linux-vdso.so.1 =>  (0x00007ffc3a3a4000)
	libm.so.6 => /lib64/libm.so.6 (0x00007fcd0d1fb000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fcd0ce2d000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fcd0d705000)
fnmatch.so:
	linux-vdso.so.1 =>  (0x00007ffdc8580000)
	libm.so.6 => /lib64/libm.so.6 (0x00007fd2aa6d3000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fd2aa305000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fd2aabd8000)
fork.so:
	linux-vdso.so.1 =>  (0x00007ffef2b0e000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f00f2af8000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f00f272a000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f00f2ffd000)
inplace.so:
	linux-vdso.so.1 =>  (0x00007ffec1987000)
	libm.so.6 => /lib64/libm.so.6 (0x00007fe75ec3f000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fe75e871000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fe75f145000)
intdiv.so:
	linux-vdso.so.1 =>  (0x00007fff0a7f7000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f8dbf809000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f8dbf43b000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f8dbfd0e000)
ordchr.so:
	linux-vdso.so.1 =>  (0x00007fff92fb3000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f4e8517e000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f4e84db0000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f4e85682000)
readdir.so:
	linux-vdso.so.1 =>  (0x00007ffc741f8000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f26e8ba1000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f26e87d3000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f26e90a6000)
readfile.so:
	linux-vdso.so.1 =>  (0x00007ffe6c8fe000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f6e607a1000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f6e603d3000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f6e60ca6000)
revoutput.so:
	linux-vdso.so.1 =>  (0x00007ffda9fec000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f4fd865b000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f4fd828d000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f4fd8b60000)
revtwoway.so:
	linux-vdso.so.1 =>  (0x00007fffd7b87000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f75d30ec000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f75d2d1e000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f75d35f1000)
rwarray.so:
	linux-vdso.so.1 =>  (0x00007ffc89fbf000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f10bd6b1000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f10bd2e3000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f10bdbb7000)
time.so:
	linux-vdso.so.1 =>  (0x00007ffe1d7c8000)
	libm.so.6 => /lib64/libm.so.6 (0x00007fd89b5c4000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fd89b1f6000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fd89bac9000)
2025-03-24T19:35:38 [mysql@RDS-146 /glc/gawk/app/gawk-5.3.1/lib/gawk] $ cd ../../include/
2025-03-24T19:37:08 [mysql@RDS-146 /glc/gawk/app/gawk-5.3.1/include] $ ll
total 44
-rw-r--r-- 1 mysql mysql 42488 2025-03-23T10:24:05 gawkapi.h
2025-03-24T19:37:09 [mysql@RDS-146 /glc/gawk/app/gawk-5.3.1/include] $


```

