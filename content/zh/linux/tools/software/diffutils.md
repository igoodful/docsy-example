---
title: diffutils
description: diffutils
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
digraph "diffutils" {
node [
        fontsize = "12"
];
    "diffutils" [ label = "diffutils", shape = octagon ];
    "diff" [ label = "diff", shape = octagon ];
    "diff3" [ label = "diff3", shape = octagon ];
    "sdiff" [ label = "sdiff", shape = octagon ];
    "cmp" [ label = "cmp", shape = octagon ];


    "diffutils" -> "diff";
    "diffutils" -> "diff3";
    "diffutils" -> "sdiff";
    "diffutils" -> "cmp";

}
```




## 3. 安装


#### 3.1 yum安装
```bash
yum -y install diffutils

```

```bash
2025-03-23T11:03:54 [root@x86centos7-101 /data/app/mysql-8.0.26/bin] # rpm -qa|grep diffutils
diffutils-3.3-4.el7.x86_64

2025-03-23T11:52:07 [root@x86centos7-101 /data/app/mysql-8.0.26/bin] # rpm -ql diffutils-3.3-4.el7.x86_64
/usr/bin/cmp
/usr/bin/diff
/usr/bin/diff3
/usr/bin/sdiff
/usr/share/doc/diffutils-3.3
/usr/share/doc/diffutils-3.3/COPYING
/usr/share/doc/diffutils-3.3/NEWS
/usr/share/doc/diffutils-3.3/README
/usr/share/info/diffutils.info.gz
/usr/share/locale/ca/LC_MESSAGES/diffutils.mo
/usr/share/locale/cs/LC_MESSAGES/diffutils.mo
/usr/share/locale/da/LC_MESSAGES/diffutils.mo
/usr/share/locale/de/LC_MESSAGES/diffutils.mo
/usr/share/locale/el/LC_MESSAGES/diffutils.mo
/usr/share/locale/eo/LC_MESSAGES/diffutils.mo
/usr/share/locale/es/LC_MESSAGES/diffutils.mo
/usr/share/locale/fi/LC_MESSAGES/diffutils.mo
/usr/share/locale/fr/LC_MESSAGES/diffutils.mo
/usr/share/locale/ga/LC_MESSAGES/diffutils.mo
/usr/share/locale/gl/LC_MESSAGES/diffutils.mo
/usr/share/locale/he/LC_MESSAGES/diffutils.mo
/usr/share/locale/hr/LC_MESSAGES/diffutils.mo
/usr/share/locale/hu/LC_MESSAGES/diffutils.mo
/usr/share/locale/id/LC_MESSAGES/diffutils.mo
/usr/share/locale/it/LC_MESSAGES/diffutils.mo
/usr/share/locale/ja/LC_MESSAGES/diffutils.mo
/usr/share/locale/lv/LC_MESSAGES/diffutils.mo
/usr/share/locale/ms/LC_MESSAGES/diffutils.mo
/usr/share/locale/nl/LC_MESSAGES/diffutils.mo
/usr/share/locale/pl/LC_MESSAGES/diffutils.mo
/usr/share/locale/pt_BR/LC_MESSAGES/diffutils.mo
/usr/share/locale/ro/LC_MESSAGES/diffutils.mo
/usr/share/locale/ru/LC_MESSAGES/diffutils.mo
/usr/share/locale/sr/LC_MESSAGES/diffutils.mo
/usr/share/locale/sv/LC_MESSAGES/diffutils.mo
/usr/share/locale/tr/LC_MESSAGES/diffutils.mo
/usr/share/locale/uk/LC_MESSAGES/diffutils.mo
/usr/share/locale/vi/LC_MESSAGES/diffutils.mo
/usr/share/locale/zh_CN/LC_MESSAGES/diffutils.mo
/usr/share/locale/zh_TW/LC_MESSAGES/diffutils.mo
/usr/share/man/man1/cmp.1.gz
/usr/share/man/man1/diff.1.gz
/usr/share/man/man1/diff3.1.gz
/usr/share/man/man1/sdiff.1.gz

```


#### 3.2 源码安装

```bash


```

