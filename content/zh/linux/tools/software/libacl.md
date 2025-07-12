---
title: libacl
description: libacl
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
digraph "libacl" {
node [
        fontsize = "12"
];
    "libacl" [ label = "libacl", shape = octagon ];
    "libattr" [ label = "libattr", shape = octagon ];
    "libacl" -> "libattr"
}
```

## **2. 内容**


#### 2.1 bin、lib、include

```viz-dot
digraph "libacl" {
node [
        fontsize = "12"
];
    "libacl" [ label = "libacl", shape = octagon ];
    "chacl" [ label = "chacl", shape = octagon ];
    "getfacl" [ label = "getfacl", shape = octagon ];
    "setfacl" [ label = "setfacl", shape = octagon ];
    "libacl.so" [ label = "libacl.so", shape = octagon ];
    "acl/libacl.h" [ label = "acl/libacl.h", shape = octagon ];
    "sys/acl.h" [ label = "sys/acl.h", shape = octagon ];

    "libacl" -> "chacl";
    "libacl" -> "getfacl";
    "libacl" -> "setfacl";
    "libacl" -> "libacl.so";
    "libacl" -> "acl/libacl.h";
    "libacl" -> "sys/acl.h";
}
```





### 2.2 示例
```bash
Installed:
  libacl-devel.x86_64 0:2.2.51-15.el7                                                                                                                                                                                                                                                               

Dependency Installed:
  libattr-devel.x86_64 0:2.4.46-13.el7                                                                                                                                                                                                                                                              

Dependency Updated:
  acl.x86_64 0:2.2.51-15.el7                                                                                                                      libacl.x86_64 0:2.2.51-15.el7                                                                                                                     

Complete!
2025-03-23T08:58:25 [root@RDS-146 /glc/tar/source/tar-1.35] # rpm -ql acl.x86_64 0:2.2.51-15.el7
/usr/bin/chacl
/usr/bin/getfacl
/usr/bin/setfacl
/usr/share/doc/acl-2.2.51
/usr/share/doc/acl-2.2.51/CHANGES.gz
/usr/share/doc/acl-2.2.51/COPYING
/usr/share/doc/acl-2.2.51/COPYING.LGPL
/usr/share/doc/acl-2.2.51/PORTING
/usr/share/doc/acl-2.2.51/README
/usr/share/locale/de/LC_MESSAGES/acl.mo
/usr/share/locale/es/LC_MESSAGES/acl.mo
/usr/share/locale/fr/LC_MESSAGES/acl.mo
/usr/share/locale/gl/LC_MESSAGES/acl.mo
/usr/share/locale/pl/LC_MESSAGES/acl.mo
/usr/share/locale/sv/LC_MESSAGES/acl.mo
/usr/share/man/man1/chacl.1.gz
/usr/share/man/man1/getfacl.1.gz
/usr/share/man/man1/setfacl.1.gz
/usr/share/man/man5/acl.5.gz
package 0:2.2.51-15.el7 is not installed

2025-03-23T09:03:31 [root@RDS-146 /glc/tar/source/tar-1.35] # rpm -ql libacl-devel.x86_64 0:2.2.51-15.el7
/usr/include/acl
/usr/include/acl/libacl.h
/usr/include/sys/acl.h
/usr/lib64/libacl.so
/usr/share/man/man3/acl_add_perm.3.gz
/usr/share/man/man3/acl_calc_mask.3.gz
/usr/share/man/man3/acl_check.3.gz
/usr/share/man/man3/acl_clear_perms.3.gz
/usr/share/man/man3/acl_cmp.3.gz
/usr/share/man/man3/acl_copy_entry.3.gz
/usr/share/man/man3/acl_copy_ext.3.gz
/usr/share/man/man3/acl_copy_int.3.gz
/usr/share/man/man3/acl_create_entry.3.gz
/usr/share/man/man3/acl_delete_def_file.3.gz
/usr/share/man/man3/acl_delete_entry.3.gz
/usr/share/man/man3/acl_delete_perm.3.gz
/usr/share/man/man3/acl_dup.3.gz
/usr/share/man/man3/acl_entries.3.gz
/usr/share/man/man3/acl_equiv_mode.3.gz
/usr/share/man/man3/acl_error.3.gz
/usr/share/man/man3/acl_extended_fd.3.gz
/usr/share/man/man3/acl_extended_file.3.gz
/usr/share/man/man3/acl_extended_file_nofollow.3.gz
/usr/share/man/man3/acl_free.3.gz
/usr/share/man/man3/acl_from_mode.3.gz
/usr/share/man/man3/acl_from_text.3.gz
/usr/share/man/man3/acl_get_entry.3.gz
/usr/share/man/man3/acl_get_fd.3.gz
/usr/share/man/man3/acl_get_file.3.gz
/usr/share/man/man3/acl_get_perm.3.gz
/usr/share/man/man3/acl_get_permset.3.gz
/usr/share/man/man3/acl_get_qualifier.3.gz
/usr/share/man/man3/acl_get_tag_type.3.gz
/usr/share/man/man3/acl_init.3.gz
/usr/share/man/man3/acl_set_fd.3.gz
/usr/share/man/man3/acl_set_file.3.gz
/usr/share/man/man3/acl_set_permset.3.gz
/usr/share/man/man3/acl_set_qualifier.3.gz
/usr/share/man/man3/acl_set_tag_type.3.gz
/usr/share/man/man3/acl_size.3.gz
/usr/share/man/man3/acl_to_any_text.3.gz
/usr/share/man/man3/acl_to_text.3.gz
/usr/share/man/man3/acl_valid.3.gz
package 0:2.2.51-15.el7 is not installed

2025-03-23T09:03:47 [root@RDS-146 /glc/tar/source/tar-1.35] # 


```




## 3. 安装



```bash
yum install libacl libacl-devel -y

```


```bash


```


