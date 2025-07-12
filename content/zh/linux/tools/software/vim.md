---
title: vim
description: vim
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

- https://github.com/vim/vim/archive/refs/tags/v9.1.1230.tar.gz


{{</note>}}


## 1. 依赖



## 2. 内容


### 2.1 bin

```viz-dot
digraph "vim-package" {
node [
        fontsize = "12"
];
    "vim-package" [ label = "vim-package", shape = octagon ];
    "xxd" [ label = "xxd", shape = octagon ];
    "vimtutor" [ label = "vimtutor", shape = octagon ];
    "vim" [ label = "vim", shape = octagon ];

    "vim-package" -> "xxd";
    "vim-package" -> "vimtutor";
    "vim-package" -> "vim";

}
```




## 3. 安装


#### 3.1 yum安装
```bash
yum -y install vim

```

```bash
2025-03-23T10:34:18 [root@x86centos7-101 /data/app/mysql-8.0.26/bin] # ldd  /usr/bin/vim 
	linux-vdso.so.1 =>  (0x00007ffd4a100000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f4dd72c9000)
	libselinux.so.1 => /lib64/libselinux.so.1 (0x00007f4dd70a2000)
	libtinfo.so.5 => /lib64/libtinfo.so.5 (0x00007f4dd6e78000)
	vim.so.1 => /lib64/vim.so.1 (0x00007f4dd6c6f000)
	libgpm.so.2 => /lib64/libgpm.so.2 (0x00007f4dd6a68000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f4dd6864000)
	libperl.so => /usr/lib64/perl5/CORE/libperl.so (0x00007f4dd64d6000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f4dd62ba000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f4dd5eed000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f4dd75cb000)
	libpcre.so.1 => /lib64/libpcre.so.1 (0x00007f4dd5c8b000)
	libattr.so.1 => /lib64/libattr.so.1 (0x00007f4dd5a86000)
	libncurses.so.5 => /lib64/libncurses.so.5 (0x00007f4dd585f000)
	libresolv.so.2 => /lib64/libresolv.so.2 (0x00007f4dd5646000)
	libnsl.so.1 => /lib64/libnsl.so.1 (0x00007f4dd542c000)
	libcrypt.so.1 => /lib64/libcrypt.so.1 (0x00007f4dd51f5000)
	libutil.so.1 => /lib64/libutil.so.1 (0x00007f4dd4ff2000)
	libfreebl3.so => /lib64/libfreebl3.so (0x00007f4dd4def000)

2025-03-23T10:44:11 [root@x86centos7-101 /data/app/mysql-8.0.26/bin] # rpm -qf /usr/bin/vim
vim-enhanced-7.4.160-5.el7.x86_64

2025-03-23T10:44:37 [root@x86centos7-101 /data/app/mysql-8.0.26/bin] # rpm -ql vim-enhanced-7.4.160-5.el7.x86_64
/etc/profile.d/vim.csh
/etc/profile.d/vim.sh
/usr/bin/rvim
/usr/bin/vim
/usr/bin/vimdiff
/usr/bin/vimtutor

```


#### 3.2 源码安装

```bash
2025-03-23T10:49:46 [root@RDS-146 /glc/vim/app/vim-9.1.1230/bin] # ll
total 3324
lrwxrwxrwx 1 mysql mysql       3 2025-03-23T10:47:53 ex -> vim
lrwxrwxrwx 1 mysql mysql       3 2025-03-23T10:47:53 rview -> vim
lrwxrwxrwx 1 mysql mysql       3 2025-03-23T10:47:53 rvim -> vim
lrwxrwxrwx 1 mysql mysql       3 2025-03-23T10:47:53 view -> vim
-rwxr-xr-x 1 mysql mysql 3367784 2025-03-23T10:47:52 vim
lrwxrwxrwx 1 mysql mysql       3 2025-03-23T10:47:53 vimdiff -> vim
-rwxr-xr-x 1 mysql mysql    4858 2025-03-23T10:47:52 vimtutor
-rwxr-xr-x 1 mysql mysql   22992 2025-03-23T10:47:54 xxd

2025-03-23T10:49:46 [root@RDS-146 /glc/vim/app/vim-9.1.1230/bin] # ldd ./vim
	linux-vdso.so.1 =>  (0x00007ffc73ba3000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f25396f6000)
	libtinfo.so.5 => /lib64/libtinfo.so.5 (0x00007f25394cc000)
	libselinux.so.1 => /lib64/libselinux.so.1 (0x00007f25392a5000)
	vim.so.1 => /lib64/vim.so.1 (0x00007f253909c000)
	libattr.so.1 => /lib64/libattr.so.1 (0x00007f2538e97000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f2538c93000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f25388c5000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f25399f8000)
	libpcre.so.1 => /lib64/libpcre.so.1 (0x00007f2538663000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f2538447000)
2025-03-23T10:49:58 [root@RDS-146 /glc/vim/app/vim-9.1.1230/bin] # 


```

