---
title: less
description: less
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

- https://ftp.gnu.org/gnu/less/


{{</note>}}


## 1. 依赖



## 2. 内容


### 2.1 bin

```viz-dot
digraph "less-package" {
node [
        fontsize = "12"
];
    "less-package" [ label = "less-package", shape = octagon ];
    "lessecho" [ label = "lessecho", shape = octagon ];
    "lesskey" [ label = "lesskey", shape = octagon ];
    "less" [ label = "less", shape = octagon ];

    "less-package" -> "lessecho";
    "less-package" -> "lesskey";
    "less-package" -> "less";

}
```




## 3. 安装


#### 3.1 yum安装
```bash
yum -y install less

```

```bash
2025-03-23T10:44:51 [root@x86centos7-101 /data/app/mysql-8.0.26/bin] # rpm -qf /usr/bin/less
less-458-9.el7.x86_64

2025-03-23T11:01:19 [root@x86centos7-101 /data/app/mysql-8.0.26/bin] # rpm -ql less-458-9.el7.x86_64
/etc/profile.d/less.csh
/etc/profile.d/less.sh
/usr/bin/less
/usr/bin/lessecho
/usr/bin/lesskey
/usr/bin/lesspipe.sh
/usr/share/doc/less-458
/usr/share/doc/less-458/LICENSE
/usr/share/man/man1/less.1.gz
/usr/share/man/man1/lessecho.1.gz
/usr/share/man/man1/lesskey.1.gz

2025-03-23T11:01:28 [root@x86centos7-101 /data/app/mysql-8.0.26/bin] # ldd /usr/bin/less
	linux-vdso.so.1 =>  (0x00007fffd07a7000)
	libtinfo.so.5 => /lib64/libtinfo.so.5 (0x00007fa8499c6000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fa8495f9000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fa849bf0000)


```


#### 3.2 源码安装

```bash
2025-03-23T11:05:53 [root@RDS-146 /glc/less/app/less-668/bin] # ll
total 868
-rwxr-xr-x 1 mysql mysql 800712 2025-03-23T11:00:37 less
-rwxr-xr-x 1 mysql mysql  22016 2025-03-23T11:00:37 lessecho
-rwxr-xr-x 1 mysql mysql  61000 2025-03-23T11:00:37 lesskey

2025-03-23T11:05:54 [root@RDS-146 /glc/less/app/less-668/bin] # ldd ./less
	linux-vdso.so.1 =>  (0x00007ffd40739000)
	libtinfo.so.5 => /lib64/libtinfo.so.5 (0x00007efff2c58000)
	libc.so.6 => /lib64/libc.so.6 (0x00007efff288a000)
	/lib64/ld-linux-x86-64.so.2 (0x00007efff2e82000)
2025-03-23T11:06:10 [root@RDS-146 /glc/less/app/less-668/bin] # 



```

