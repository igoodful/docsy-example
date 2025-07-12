---
title: libgcrypt
description: libgcrypt
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

- https://www.gnupg.org/ftp/gcrypt/libgcrypt/libgcrypt-1.11.0.tar.gz


{{</note>}}



## 1. 依赖

```viz-dot
digraph "libgcrypt.so.20.5.0" {
node [
        fontsize = "12"
];
    "libgcrypt.so.20.5.0" [ label = "libgcrypt.so.20.5.0", shape = octagon ];
    "libgpg-error" [ label = "libgpg-error", shape = octagon ];

    "libgcrypt.so.20.5.0" -> "libgpg-error";

}
```


## 2. 内容


```viz-dot
digraph "libgcrypt" {
node [
        fontsize = "12"
];
    "libgcrypt" [ label = "libgcrypt", shape = octagon ];
    "libgcrypt.so.20.5.0" [ label = "libgcrypt.so.20.5.0", shape = octagon ];
    "dumpsexp" [ label = "dumpsexp", shape = octagon ];
    "hmac256" [ label = "hmac256", shape = octagon ];
    "libgcrypt-config" [ label = "libgcrypt-config", shape = octagon ];
    "mpicalc" [ label = "mpicalc", shape = octagon ];
    "gcrypt.h" [ label = "gcrypt.h", shape = octagon ];

    "libgcrypt" -> "libgcrypt.so.20.5.0";
    "libgcrypt" -> "dumpsexp";
    "libgcrypt" -> "hmac256";
    "libgcrypt" -> "libgcrypt-config";
    "libgcrypt" -> "mpicalc";
    "libgcrypt" -> "gcrypt.h";
}
```




## 3. 安装


#### 3.1 yum安装


```bash
2025-03-23T19:05:01 [root@RDS-146 /glc/libgcrypt/app/libgcrypt-1.11.0] # ll
total 16
drwxrwxr-x 2 mysql mysql 4096 2025-03-23T18:57:38 bin
drwxrwxr-x 2 mysql mysql 4096 2025-03-23T18:57:38 include
drwxrwxr-x 3 mysql mysql 4096 2025-03-23T18:57:38 lib
drwxrwxr-x 5 mysql mysql 4096 2025-03-23T18:57:38 share
2025-03-23T19:05:02 [root@RDS-146 /glc/libgcrypt/app/libgcrypt-1.11.0] # cd bin/
2025-03-23T19:05:04 [root@RDS-146 /glc/libgcrypt/app/libgcrypt-1.11.0/bin] # ll
total 148
-rwxr-xr-x 1 mysql mysql 38304 2025-03-23T18:57:38 dumpsexp
-rwxr-xr-x 1 mysql mysql 41584 2025-03-23T18:57:38 hmac256
-rwxr-xr-x 1 mysql mysql  4595 2025-03-23T18:57:38 libgcrypt-config
-rwxr-xr-x 1 mysql mysql 54672 2025-03-23T18:57:38 mpicalc
2025-03-23T19:05:05 [root@RDS-146 /glc/libgcrypt/app/libgcrypt-1.11.0/bin] # cd ..
2025-03-23T19:05:08 [root@RDS-146 /glc/libgcrypt/app/libgcrypt-1.11.0] # cd lib/
2025-03-23T19:05:11 [root@RDS-146 /glc/libgcrypt/app/libgcrypt-1.11.0/lib] # ll
total 6284
-rwxr-xr-x 1 mysql mysql    1070 2025-03-23T18:57:38 libgcrypt.la
lrwxrwxrwx 1 mysql mysql      19 2025-03-23T18:57:38 libgcrypt.so -> libgcrypt.so.20.5.0
lrwxrwxrwx 1 mysql mysql      19 2025-03-23T18:57:38 libgcrypt.so.20 -> libgcrypt.so.20.5.0
-rwxr-xr-x 1 mysql mysql 6424600 2025-03-23T18:57:38 libgcrypt.so.20.5.0
drwxrwxr-x 2 mysql mysql    4096 2025-03-23T18:57:38 pkgconfig
2025-03-23T19:05:12 [root@RDS-146 /glc/libgcrypt/app/libgcrypt-1.11.0/lib] # cd ..
2025-03-23T19:05:15 [root@RDS-146 /glc/libgcrypt/app/libgcrypt-1.11.0] # cd include/
2025-03-23T19:05:22 [root@RDS-146 /glc/libgcrypt/app/libgcrypt-1.11.0/include] # ll
total 84
-rw-r--r-- 1 mysql mysql 83204 2025-03-23T18:57:38 gcrypt.h
2025-03-23T19:05:23 [root@RDS-146 /glc/libgcrypt/app/libgcrypt-1.11.0/include] # cd ../lib/
2025-03-23T19:05:40 [root@RDS-146 /glc/libgcrypt/app/libgcrypt-1.11.0/lib] # ll
total 6284
-rwxr-xr-x 1 mysql mysql    1070 2025-03-23T18:57:38 libgcrypt.la
lrwxrwxrwx 1 mysql mysql      19 2025-03-23T18:57:38 libgcrypt.so -> libgcrypt.so.20.5.0
lrwxrwxrwx 1 mysql mysql      19 2025-03-23T18:57:38 libgcrypt.so.20 -> libgcrypt.so.20.5.0
-rwxr-xr-x 1 mysql mysql 6424600 2025-03-23T18:57:38 libgcrypt.so.20.5.0
drwxrwxr-x 2 mysql mysql    4096 2025-03-23T18:57:38 pkgconfig
2025-03-23T19:05:41 [root@RDS-146 /glc/libgcrypt/app/libgcrypt-1.11.0/lib] # ldd libgcrypt.so.20.5.0
	linux-vdso.so.1 =>  (0x00007fff68599000)
	libgpg-error.so.0 => /glc/libgpg-error/app/libgpg-error-1.50/lib/libgpg-error.so.0 (0x00007f82afdd2000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f82afa04000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f82afff7000)
2025-03-23T19:05:48 [root@RDS-146 /glc/libgcrypt/app/libgcrypt-1.11.0/lib] # cd ../bin/
2025-03-23T19:05:57 [root@RDS-146 /glc/libgcrypt/app/libgcrypt-1.11.0/bin] # ll
total 148
-rwxr-xr-x 1 mysql mysql 38304 2025-03-23T18:57:38 dumpsexp
-rwxr-xr-x 1 mysql mysql 41584 2025-03-23T18:57:38 hmac256
-rwxr-xr-x 1 mysql mysql  4595 2025-03-23T18:57:38 libgcrypt-config
-rwxr-xr-x 1 mysql mysql 54672 2025-03-23T18:57:38 mpicalc
2025-03-23T19:05:58 [root@RDS-146 /glc/libgcrypt/app/libgcrypt-1.11.0/bin] # ldd dumpsexp
	linux-vdso.so.1 =>  (0x00007ffdaae9c000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f9419e34000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f941a202000)
2025-03-23T19:06:02 [root@RDS-146 /glc/libgcrypt/app/libgcrypt-1.11.0/bin] # ldd hmac256
	linux-vdso.so.1 =>  (0x00007ffdf89b2000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f50ada1c000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f50addea000)
2025-03-23T19:06:07 [root@RDS-146 /glc/libgcrypt/app/libgcrypt-1.11.0/bin] # ldd mpicalc
	linux-vdso.so.1 =>  (0x00007ffc3edcc000)
	libgcrypt.so.20 => /glc/libgcrypt/app/libgcrypt-1.11.0/lib/libgcrypt.so.20 (0x00007fcd37ccb000)
	libgpg-error.so.0 => /glc/libgpg-error/app/libgpg-error-1.50/lib/libgpg-error.so.0 (0x00007fcd37a13000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fcd37645000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fcd37c38000)
2025-03-23T19:06:12 [root@RDS-146 /glc/libgcrypt/app/libgcrypt-1.11.0/bin] # ldd libgcrypt-config
	not a dynamic executable
2025-03-23T19:06:20 [root@RDS-146 /glc/libgcrypt/app/libgcrypt-1.11.0/bin] # 
```





#### 3.2 源码安装

```bash
wget https://gnupg.org/ftp/gcrypt/libgcrypt/libgcrypt-1.8.11.tar.bz2
tar -xzvf libgcrypt-1.8.11.tar.bz2 && cd libgcrypt-1.8.11
./configure --prefix=$HOME/local && make && make install
```
