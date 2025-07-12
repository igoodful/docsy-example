---
title: zlib
description: zlib
date: 2023-11-06
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

- [zlib github](https://github.com/madler/zlib)
- [zlib](https://www.zlib.net/)


{{</note>}}


## 1. 依赖

```viz-dot
digraph "zlib" {
node [
        fontsize = "12"
];
    "zlib" [ label = "zlib", shape = octagon ];
    "glibc" [ label = "glibc", shape = octagon ];
    "libc.so.6" [ label = "libc.so.6", shape = octagon ];
    "/lib64/ld-linux-x86-64.so.2" [ label = "/lib64/ld-linux-x86-64.so.2", shape = octagon ];


    "zlib" -> "glibc";
    "glibc" -> "libc.so.6";
    "glibc" -> "/lib64/ld-linux-x86-64.so.2";


}
```


## 2. 内容


```viz-dot
digraph "zlib" {
node [
        fontsize = "12"
];
    "zlib" [ label = "zlib", shape = octagon ];
    "libz.so.1.3.1" [ label = "libz.so.1.3.1", shape = octagon ];
    "zconf.h" [ label = "zconf.h", shape = octagon ];
    "zlib.h" [ label = "zlib.h", shape = octagon ];

    "zlib" -> "libz.so.1.3.1";
    "zlib" -> "zconf.h";
    "zlib" -> "zlib.h";



}
```







## 3. 安装






```bash
wget http://www.zlib.net/zlib-1.2.8.tar.gz
tar -xvzf zlib-1.2.8.tar.gz
cd zlib-1.2.8.tar.gz
./configure
make
make install

```

```bash
2025-03-24T19:59:15 [root@RDS-146 /glc/zlib/app/zlib-1.3.1/lib] # ll
total 276
-rw-r--r-- 1 mysql mysql 152316 2025-03-24T18:46:14 libz.a
lrwxrwxrwx 1 mysql mysql     13 2025-03-24T18:46:14 libz.so -> libz.so.1.3.1
lrwxrwxrwx 1 mysql mysql     13 2025-03-24T18:46:14 libz.so.1 -> libz.so.1.3.1
-rwxr-xr-x 1 mysql mysql 121656 2025-03-24T18:46:14 libz.so.1.3.1
drwxrwxr-x 2 mysql mysql   4096 2025-03-24T18:46:14 pkgconfig

2025-03-24T19:59:16 [root@RDS-146 /glc/zlib/app/zlib-1.3.1/lib] # ldd libz.so.1.3.1
	linux-vdso.so.1 =>  (0x00007fff90ec0000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f558eabe000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f558f0a8000)
2025-03-24T20:03:58 [root@RDS-146 /glc/zlib/app/zlib-1.3.1/lib] # cd ..
2025-03-24T20:04:01 [root@RDS-146 /glc/zlib/app/zlib-1.3.1] # ll
total 12
drwxrwxr-x 2 mysql mysql 4096 2025-03-24T18:46:14 include
drwxrwxr-x 3 mysql mysql 4096 2025-03-24T18:46:14 lib
drwxrwxr-x 3 mysql mysql 4096 2025-03-24T18:46:14 share
2025-03-24T20:04:03 [root@RDS-146 /glc/zlib/app/zlib-1.3.1] # cd include/

2025-03-24T20:04:06 [root@RDS-146 /glc/zlib/app/zlib-1.3.1/include] # ll
total 116
-rw-r--r-- 1 mysql mysql 16464 2025-03-24T18:46:14 zconf.h
-rw-r--r-- 1 mysql mysql 96829 2025-03-24T18:46:14 zlib.h


```
