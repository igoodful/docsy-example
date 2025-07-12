---
title: attr
description: attr
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



{{</note>}}


## 1. 依赖

```viz-dot
digraph "attr" {
node [
        fontsize = "12"
];
    "attr" [ label = "attr", shape = octagon ];
    "libattr" [ label = "libattr", shape = octagon ];
    "glibc" [ label = "glibc", shape = octagon ];
    "/lib64/ld-linux-x86-64.so.2" [ label = "/lib64/ld-linux-x86-64.so.2", shape = octagon ];
    "libc" [ label = "libc", shape = octagon ];

    "attr" -> "libattr";
    "attr" -> "glibc";
    "glibc" -> "libc";
    "glibc" -> "/lib64/ld-linux-x86-64.so.2";


}
```


## 2. 内容


### 2.1 bin



### 2.2 示例
```bash




```




## 3. 安装



```bash
yum -y install attr libattr libattr-devel


```

#### 3.1 yum安装

```bash
2025-03-23T09:44:12 [root@x86centos7-101 /data/app/mysql-8.0.26/bin] # rpm -ql attr-2.4.46-13.el7.x86_64
/usr/bin/attr
/usr/bin/getfattr
/usr/bin/setfattr
/usr/share/doc/attr-2.4.46
/usr/share/doc/attr-2.4.46/CHANGES.gz
/usr/share/doc/attr-2.4.46/COPYING
/usr/share/doc/attr-2.4.46/PORTING
/usr/share/doc/attr-2.4.46/README
/usr/share/doc/attr-2.4.46/doc
/usr/share/doc/attr-2.4.46/doc/CHANGES
/usr/share/doc/attr-2.4.46/doc/CHANGES.gz
/usr/share/doc/attr-2.4.46/doc/COPYING
/usr/share/doc/attr-2.4.46/doc/INSTALL
/usr/share/doc/attr-2.4.46/doc/Makefile
/usr/share/doc/attr-2.4.46/doc/PORTING
/usr/share/locale/cs/LC_MESSAGES/attr.mo
/usr/share/locale/de/LC_MESSAGES/attr.mo
/usr/share/locale/es/LC_MESSAGES/attr.mo
/usr/share/locale/fr/LC_MESSAGES/attr.mo
/usr/share/locale/gl/LC_MESSAGES/attr.mo
/usr/share/locale/nl/LC_MESSAGES/attr.mo
/usr/share/locale/pl/LC_MESSAGES/attr.mo
/usr/share/locale/sv/LC_MESSAGES/attr.mo
/usr/share/man/man1/attr.1.gz
/usr/share/man/man1/getfattr.1.gz
/usr/share/man/man1/setfattr.1.gz
/usr/share/man/man5/attr.5.gz


2025-03-23T09:50:48 [root@x86centos7-101 /data/app/mysql-8.0.26/bin] # rpm -ql libattr-2.4.46-13.el7.x86_64
/usr/lib64/libattr.so.1
/usr/lib64/libattr.so.1.1.0

2025-03-23T09:51:00 [root@x86centos7-101 /data/app/mysql-8.0.26/bin] # rpm -ql libattr-devel-2.4.46-13.el7.x86_64
/usr/include/attr
/usr/include/attr/attributes.h
/usr/include/attr/error_context.h
/usr/include/attr/libattr.h
/usr/include/attr/xattr.h
/usr/lib64/libattr.so
/usr/share/man/man2/fgetxattr.2.gz
/usr/share/man/man2/flistxattr.2.gz
/usr/share/man/man2/fremovexattr.2.gz
/usr/share/man/man2/fsetxattr.2.gz
/usr/share/man/man2/getxattr.2.gz
/usr/share/man/man2/lgetxattr.2.gz
/usr/share/man/man2/listxattr.2.gz
/usr/share/man/man2/llistxattr.2.gz
/usr/share/man/man2/lremovexattr.2.gz
/usr/share/man/man2/lsetxattr.2.gz
/usr/share/man/man2/removexattr.2.gz
/usr/share/man/man2/setxattr.2.gz
/usr/share/man/man3/attr_get.3.gz
/usr/share/man/man3/attr_getf.3.gz
/usr/share/man/man3/attr_list.3.gz
/usr/share/man/man3/attr_listf.3.gz
/usr/share/man/man3/attr_multi.3.gz
/usr/share/man/man3/attr_multif.3.gz
/usr/share/man/man3/attr_remove.3.gz
/usr/share/man/man3/attr_removef.3.gz
/usr/share/man/man3/attr_set.3.gz
/usr/share/man/man3/attr_setf.3.gz


```


#### 3.2 源码安装

```bash
2025-03-23T09:52:37 [root@RDS-146 /glc/attr/app/attr-2.5.2] # tree
.
├── bin
│   ├── attr
│   ├── getfattr
│   └── setfattr
├── etc
│   └── xattr.conf
├── include
│   └── attr
│       ├── attributes.h
│       ├── error_context.h
│       └── libattr.h
├── lib
│   ├── libattr.a
│   ├── libattr.la
│   ├── libattr.so -> libattr.so.1.1.2502
│   ├── libattr.so.1 -> libattr.so.1.1.2502
│   ├── libattr.so.1.1.2502
│   └── pkgconfig
│       └── libattr.pc
└── share
    ├── doc
    │   └── attr
    │       ├── CHANGES
    │       ├── COPYING
    │       └── COPYING.LGPL
    ├── locale
    │   ├── cs
    │   │   └── LC_MESSAGES
    │   │       └── attr.mo
    │   ├── de
    │   │   └── LC_MESSAGES
    │   │       └── attr.mo
    │   ├── en@boldquot
    │   │   └── LC_MESSAGES
    │   │       └── attr.mo
    │   ├── en@quot
    │   │   └── LC_MESSAGES
    │   │       └── attr.mo
    │   ├── es
    │   │   └── LC_MESSAGES
    │   │       └── attr.mo
    │   ├── fr
    │   │   └── LC_MESSAGES
    │   │       └── attr.mo
    │   ├── gl
    │   │   └── LC_MESSAGES
    │   │       └── attr.mo
    │   ├── ka
    │   │   └── LC_MESSAGES
    │   │       └── attr.mo
    │   ├── nl
    │   │   └── LC_MESSAGES
    │   │       └── attr.mo
    │   ├── pl
    │   │   └── LC_MESSAGES
    │   │       └── attr.mo
    │   └── sv
    │       └── LC_MESSAGES
    │           └── attr.mo
    └── man
        ├── man1
        │   ├── attr.1
        │   ├── getfattr.1
        │   └── setfattr.1
        └── man3
            ├── attr_get.3
            ├── attr_getf.3 -> attr_get.3
            ├── attr_list.3
            ├── attr_listf.3 -> attr_list.3
            ├── attr_multi.3
            ├── attr_multif.3 -> attr_multi.3
            ├── attr_remove.3
            ├── attr_removef.3 -> attr_remove.3
            ├── attr_set.3
            └── attr_setf.3 -> attr_set.3

35 directories, 40 files


```


