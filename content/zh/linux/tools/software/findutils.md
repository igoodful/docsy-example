---
title: findutils
description: findutils
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



## 2. 内容


### 2.1 bin

```viz-dot
digraph "findutils" {
node [
        fontsize = "12"
];
    "findutils" [ label = "findutils", shape = octagon ];
    "find" [ label = "find", shape = octagon ];
    "xargs" [ label = "xargs", shape = octagon ];
    "oldfind" [ label = "oldfind", shape = octagon ];
    "findutils" -> "find";
    "findutils" -> "xargs";
    "findutils" -> "oldfind";


}
```




## 3. 安装


#### 3.1 yum安装
```bash
yum -y install findutils

```

```bash
2025-03-23T11:55:13 [root@x86centos7-101 /data/app/mysql-8.0.26/bin] # ldd /usr/bin/find
	linux-vdso.so.1 =>  (0x00007ffd63bfc000)
	libselinux.so.1 => /lib64/libselinux.so.1 (0x00007f4a3de4e000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f4a3db4c000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f4a3d77f000)
	libpcre.so.1 => /lib64/libpcre.so.1 (0x00007f4a3d51d000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f4a3d319000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f4a3e075000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f4a3d0fd000)

2025-03-23T12:00:46 [root@x86centos7-101 /data/app/mysql-8.0.26/bin] # rpm -qf /usr/bin/find
findutils-4.5.11-6.el7.x86_64

2025-03-23T12:01:06 [root@x86centos7-101 /data/app/mysql-8.0.26/bin] # rpm -ql findutils-4.5.11-6.el7.x86_64
/usr/bin/find
/usr/bin/oldfind
/usr/bin/xargs
/usr/share/doc/findutils-4.5.11
/usr/share/doc/findutils-4.5.11/AUTHORS
/usr/share/doc/findutils-4.5.11/COPYING
/usr/share/doc/findutils-4.5.11/ChangeLog
/usr/share/doc/findutils-4.5.11/NEWS
/usr/share/doc/findutils-4.5.11/README
/usr/share/doc/findutils-4.5.11/THANKS
/usr/share/doc/findutils-4.5.11/TODO
/usr/share/info/find-maint.info.gz
/usr/share/info/find.info.gz
/usr/share/locale/be/LC_MESSAGES/findutils.mo
/usr/share/locale/bg/LC_MESSAGES/findutils.mo
/usr/share/locale/ca/LC_MESSAGES/findutils.mo
/usr/share/locale/cs/LC_MESSAGES/findutils.mo
/usr/share/locale/da/LC_MESSAGES/findutils.mo
/usr/share/locale/de/LC_MESSAGES/findutils.mo
/usr/share/locale/el/LC_MESSAGES/findutils.mo
/usr/share/locale/eo/LC_MESSAGES/findutils.mo
/usr/share/locale/es/LC_MESSAGES/findutils.mo
/usr/share/locale/et/LC_MESSAGES/findutils.mo
/usr/share/locale/fi/LC_MESSAGES/findutils.mo
/usr/share/locale/fr/LC_MESSAGES/findutils.mo
/usr/share/locale/ga/LC_MESSAGES/findutils.mo
/usr/share/locale/gl/LC_MESSAGES/findutils.mo
/usr/share/locale/hr/LC_MESSAGES/findutils.mo
/usr/share/locale/hu/LC_MESSAGES/findutils.mo
/usr/share/locale/id/LC_MESSAGES/findutils.mo
/usr/share/locale/it/LC_MESSAGES/findutils.mo
/usr/share/locale/ja/LC_MESSAGES/findutils.mo
/usr/share/locale/ko/LC_MESSAGES/findutils.mo
/usr/share/locale/lg/LC_MESSAGES/findutils.mo
/usr/share/locale/lt/LC_MESSAGES/findutils.mo
/usr/share/locale/ms/LC_MESSAGES/findutils.mo
/usr/share/locale/nl/LC_MESSAGES/findutils.mo
/usr/share/locale/pl/LC_MESSAGES/findutils.mo
/usr/share/locale/pt/LC_MESSAGES/findutils.mo
/usr/share/locale/pt_BR/LC_MESSAGES/findutils.mo
/usr/share/locale/ro/LC_MESSAGES/findutils.mo
/usr/share/locale/ru/LC_MESSAGES/findutils.mo
/usr/share/locale/rw/LC_MESSAGES/findutils.mo
/usr/share/locale/sk/LC_MESSAGES/findutils.mo
/usr/share/locale/sl/LC_MESSAGES/findutils.mo
/usr/share/locale/sr/LC_MESSAGES/findutils.mo
/usr/share/locale/sv/LC_MESSAGES/findutils.mo
/usr/share/locale/tr/LC_MESSAGES/findutils.mo
/usr/share/locale/uk/LC_MESSAGES/findutils.mo
/usr/share/locale/vi/LC_MESSAGES/findutils.mo
/usr/share/locale/zh_CN/LC_MESSAGES/findutils.mo
/usr/share/locale/zh_TW/LC_MESSAGES/findutils.mo
/usr/share/man/man1/find.1.gz
/usr/share/man/man1/oldfind.1.gz
/usr/share/man/man1/xargs.1.gz


```


#### 3.2 源码安装

```bash


```

