---
title: libgpg-error
description: libgpg-error
date: 2023-11-06
weight: 102


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{%pageinfo%}}

- [libgpg-error 官网](https://gnupg.org/download/index.html)


{{%/pageinfo%}}



{{<note>}}
<!---->
只依赖：libc


{{</note>}}


### 安装
```bash
wget https://gnupg.org/ftp/gcrypt/libgpg-error/libgpg-error-1.47.tar.bz2
tar -xzvf libgpg-error-1.47.tar.bz2 && cd libgpg-error-1.47
./configure --prefix=$HOME/local && make && make install
```


## 查看安装结果
```bash
2025-03-23T16:29:56 [root@RDS-146 /glc/libgpg_error/app/libgpg_error-1.50/lib] # ll
total 660
-rwxr-xr-x 1 mysql mysql    984 2025-03-23T16:26:28 libgpg-error.la
lrwxrwxrwx 1 mysql mysql     22 2025-03-23T16:26:28 libgpg-error.so -> libgpg-error.so.0.37.0
lrwxrwxrwx 1 mysql mysql     22 2025-03-23T16:26:28 libgpg-error.so.0 -> libgpg-error.so.0.37.0
-rwxr-xr-x 1 mysql mysql 667440 2025-03-23T16:26:28 libgpg-error.so.0.37.0
drwxr-xr-x 2 mysql mysql   4096 2025-03-23T16:26:28 pkgconfig
2025-03-23T16:29:57 [root@RDS-146 /glc/libgpg_error/app/libgpg_error-1.50/lib] # ld  libgpg-error.so.0.37.0
ld: warning: cannot find entry symbol _start; not setting start address
2025-03-23T16:30:06 [root@RDS-146 /glc/libgpg_error/app/libgpg_error-1.50/lib] # ldd libgpg-error.so.0.37.0
	linux-vdso.so.1 =>  (0x00007fffda08a000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f6091ff4000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f60925e7000)
2025-03-23T16:30:14 [root@RDS-146 /glc/libgpg_error/app/libgpg_error-1.50/lib] # cd ..
2025-03-23T16:30:21 [root@RDS-146 /glc/libgpg_error/app/libgpg_error-1.50] # cd bin/
2025-03-23T16:30:24 [root@RDS-146 /glc/libgpg_error/app/libgpg_error-1.50/bin] # ll
total 212
-rwxr-xr-x 1 mysql mysql  83352 2025-03-23T16:26:28 gpg-error
-rwxr-xr-x 1 mysql mysql  16302 2025-03-23T16:26:28 gpgrt-config
-rwxr-xr-x 1 mysql mysql 111136 2025-03-23T16:26:28 yat2m
2025-03-23T16:30:24 [root@RDS-146 /glc/libgpg_error/app/libgpg_error-1.50/bin] # ldd yat2m
	linux-vdso.so.1 =>  (0x00007ffe7e146000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f313916f000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f313953d000)
2025-03-23T16:30:29 [root@RDS-146 /glc/libgpg_error/app/libgpg_error-1.50/bin] # ldd gpgrt-config
	not a dynamic executable
2025-03-23T16:30:35 [root@RDS-146 /glc/libgpg_error/app/libgpg_error-1.50/bin] # ldd gpg-error
	linux-vdso.so.1 =>  (0x00007ffc7efa8000)
	libgpg-error.so.0 => /glc/libgpg_error/app/libgpg_error-1.50/lib/libgpg-error.so.0 (0x00007fc2c5f6c000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fc2c5b9e000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fc2c6191000)
2025-03-23T16:30:41 [root@RDS-146 /glc/libgpg_error/app/libgpg_error-1.50/bin] # cd ..
2025-03-23T16:30:49 [root@RDS-146 /glc/libgpg_error/app/libgpg_error-1.50] # ll
total 16
drwxr-xr-x 2 mysql mysql 4096 2025-03-23T16:26:28 bin
drwxr-xr-x 2 mysql mysql 4096 2025-03-23T16:26:28 include
drwxr-xr-x 3 mysql mysql 4096 2025-03-23T16:30:06 lib
drwxr-xr-x 8 mysql mysql 4096 2025-03-23T16:26:29 share
2025-03-23T16:30:50 [root@RDS-146 /glc/libgpg_error/app/libgpg_error-1.50] # cd include/
2025-03-23T16:30:53 [root@RDS-146 /glc/libgpg_error/app/libgpg_error-1.50/include] # ll
total 152
-rw-r--r-- 1 mysql mysql 76121 2025-03-23T16:26:28 gpg-error.h
-rw-r--r-- 1 mysql mysql 76121 2025-03-23T16:26:28 gpgrt.h


```



### 创建gpg-error-config到gpgrt-config的软连接
> 新版gpg-error没有gpg-error-config
```bash
ln -s  gpgrt-config gpg-error-config
```

