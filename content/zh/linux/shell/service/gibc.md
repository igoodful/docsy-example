---
title: glibc源码编译安装
description: systemd
date: 2023-11-08
weight: 30000


---

{{< alert >}}
glibc下载地址：[https://ftp.gnu.org/gnu/glibc/](https://ftp.gnu.org/gnu/glibc/)


{{< /alert >}}





### 源码编译

```sql
cd glibc-2.17

mkdir build && cd build

../configure --prefix=/usr --disable-profile --enable-add-ons --with-headers=/usr/include --with-binutils=/usr/bin

make && make install
```


{{<alert>}}


{{</alert>}}












