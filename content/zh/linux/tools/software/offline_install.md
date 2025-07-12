---
title: 离线安装
description: 离线安装
date: 2023-11-06
weight: 30000


---
{{%pageinfo%}}
离线安装
{{%/pageinfo%}}

{{<note>}}
<!---->

{{</note>}}


## 根据可执行程序反向查找依赖库
```sql
[root@k8s-node-76 bin]# objdump -x ./mysqld |grep NEEDED
  NEEDED               libpthread.so.0
  NEEDED               libcrypto.so.1.1
  NEEDED               libssl.so.1.1
  NEEDED               librt.so.1
  NEEDED               libprotobuf-lite.so.3.19.4
  NEEDED               libaio.so.1
  NEEDED               libnuma.so.1
  NEEDED               libdl.so.2
  NEEDED               libstdc++.so.6
  NEEDED               libm.so.6
  NEEDED               libgcc_s.so.1
  NEEDED               libc.so.6

[root@k8s-node-76 ~]# ldd /data/app/mysql-8.0.32-linux-glibc2.17-x86_64/bin/mysqld
	linux-vdso.so.1 =>  (0x00007ffe2edcb000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f8481c54000)
	libcrypto.so.1.1 => /data/app/mysql-8.0.32-linux-glibc2.17-x86_64/bin/../lib/private/libcrypto.so.1.1 (0x00007f8481785000)
	libssl.so.1.1 => /data/app/mysql-8.0.32-linux-glibc2.17-x86_64/bin/../lib/private/libssl.so.1.1 (0x00007f84814f3000)
	librt.so.1 => /lib64/librt.so.1 (0x00007f84812eb000)
	libprotobuf-lite.so.3.19.4 => /data/app/mysql-8.0.32-linux-glibc2.17-x86_64/bin/../lib/private/libprotobuf-lite.so.3.19.4 (0x00007f8481fd8000)
	libaio.so.1 => /lib64/libaio.so.1 (0x00007f84810e9000)
	libnuma.so.1 => /lib64/libnuma.so.1 (0x00007f8480edd000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f8480cd9000)
	libstdc++.so.6 => /lib64/libstdc++.so.6 (0x00007f84809d1000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f84806cf000)
	libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x00007f84804b9000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f84800ec000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f8481e70000)

[root@k8s-node-76 bin]#  readelf  -d ./mysqld

Dynamic section at offset 0x389b828 contains 36 entries:
  Tag        Type                         Name/Value
 0x0000000000000001 (NEEDED)             Shared library: [libpthread.so.0]
 0x0000000000000001 (NEEDED)             Shared library: [libcrypto.so.1.1]
 0x0000000000000001 (NEEDED)             Shared library: [libssl.so.1.1]
 0x0000000000000001 (NEEDED)             Shared library: [librt.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libprotobuf-lite.so.3.19.4]
 0x0000000000000001 (NEEDED)             Shared library: [libaio.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libnuma.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libdl.so.2]
 0x0000000000000001 (NEEDED)             Shared library: [libstdc++.so.6]
 0x0000000000000001 (NEEDED)             Shared library: [libm.so.6]
 0x0000000000000001 (NEEDED)             Shared library: [libgcc_s.so.1]
 0x0000000000000001 (NEEDED)             Shared library: [libc.so.6]
 0x000000000000000f (RPATH)              Library rpath: [$ORIGIN/../lib/private]
 0x000000000000000c (INIT)               0xc56000
 0x000000000000000d (FINI)               0x29d34a4
 0x0000000000000019 (INIT_ARRAY)         0x3b113e0
 0x000000000000001b (INIT_ARRAYSZ)       7736 (bytes)
 0x000000000000001a (FINI_ARRAY)         0x3b13218
 0x000000000000001c (FINI_ARRAYSZ)       8 (bytes)
 0x000000006ffffef5 (GNU_HASH)           0x400340
 0x0000000000000005 (STRTAB)             0x665930
 0x0000000000000006 (SYMTAB)             0x480308
 0x000000000000000a (STRSZ)              5841367 (bytes)
 0x000000000000000b (SYMENT)             24 (bytes)
 0x0000000000000015 (DEBUG)              0x0
 0x0000000000000003 (PLTGOT)             0x3c9d000
 0x0000000000000002 (PLTRELSZ)           19776 (bytes)
 0x0000000000000014 (PLTREL)             RELA
 0x0000000000000017 (JMPREL)             0xc50f58
 0x0000000000000007 (RELA)               0xc20598
 0x0000000000000008 (RELASZ)             199104 (bytes)
 0x0000000000000009 (RELAENT)            24 (bytes)
 0x000000006ffffffe (VERNEED)            0xc20238
 0x000000006fffffff (VERNEEDNUM)         11
 0x000000006ffffff0 (VERSYM)             0xbf7b08
 0x0000000000000000 (NULL)               0x0


```

#### 根据动态库名称查找位置
1. `ldconfig -p`:
```bash
[root@k8s-node-70 ~]# ldconfig -p|grep libpthread
	libpthread.so.0 (libc6,x86-64, OS ABI: Linux 2.6.32) => /lib64/libpthread.so.0
```





> `yum deplist openssl-devel`:
```sql
Loaded plugins: fastestmirror, langpacks
Determining fastest mirrors
epel/x86_64/metalink                                                                                                                                                                                                                                              | 7.3 kB  00:00:00
 * base: mirrors.tuna.tsinghua.edu.cn
 * elrepo: mirrors.tuna.tsinghua.edu.cn
 * epel: mirrors.tuna.tsinghua.edu.cn
 * extras: mirrors.tuna.tsinghua.edu.cn
 * updates: mirrors.bupt.edu.cn
base                                                                                                                                                                                                                                                              | 3.6 kB  00:00:00
centos7-iso                                                                                                                                                                                                                                                       | 1.3 kB  00:00:00
Trying other mirror.
elrepo                                                                                                                                                                                                                                                            | 3.0 kB  00:00:00
endpoint                                                                                                                                                                                                                                                          | 2.9 kB  00:00:00
epel                                                                                                                                                                                                                                                              | 4.7 kB  00:00:00
extras                                                                                                                                                                                                                                                            | 2.9 kB  00:00:00
kubernetes/signature                                                                                                                                                                                                                                              |  454 B  00:00:00
kubernetes/signature                                                                                                                                                                                                                                              | 1.4 kB  00:00:03 !!!
updates                                                                                                                                                                                                                                                           | 2.9 kB  00:00:00
(1/4): updates/7/x86_64/primary_db                                                                                                                                                                                                                                |  24 MB  00:00:13
(2/4): epel/x86_64/updateinfo                                                                                                                                                                                                                                     | 1.0 MB  00:00:15
(3/4): elrepo/primary_db                                                                                                                                                                                                                                          | 367 kB  00:00:27
(4/4): epel/x86_64/primary_db                                                                                                                                                                                                                                     | 7.0 MB  00:00:35
package: openssl-devel.i686 1:1.0.2k-26.el7_9
  dependency: /usr/bin/pkg-config
   provider: pkgconfig.x86_64 1:0.27.1-4.el7
   provider: pkgconfig.i686 1:0.27.1-4.el7
  dependency: krb5-devel(x86-32)
   provider: krb5-devel.i686 1.15.1-55.el7_9
  dependency: libcrypto.so.10
   provider: openssl-libs.i686 1:1.0.2k-26.el7_9
  dependency: libssl.so.10
   provider: openssl-libs.i686 1:1.0.2k-26.el7_9
  dependency: openssl-libs(x86-32) = 1:1.0.2k-26.el7_9
   provider: openssl-libs.i686 1:1.0.2k-26.el7_9
  dependency: pkgconfig
   provider: pkgconfig.x86_64 1:0.27.1-4.el7
   provider: pkgconfig.i686 1:0.27.1-4.el7
  dependency: zlib-devel(x86-32)
   provider: zlib-devel.i686 1.2.7-21.el7_9
package: openssl-devel.x86_64 1:1.0.2k-26.el7_9
  dependency: /usr/bin/pkg-config
   provider: pkgconfig.x86_64 1:0.27.1-4.el7
   provider: pkgconfig.i686 1:0.27.1-4.el7
  dependency: krb5-devel(x86-64)
   provider: krb5-devel.x86_64 1.15.1-55.el7_9
  dependency: libcrypto.so.10()(64bit)
   provider: openssl-libs.x86_64 1:1.0.2k-26.el7_9
  dependency: libssl.so.10()(64bit)
   provider: openssl-libs.x86_64 1:1.0.2k-26.el7_9
  dependency: openssl-libs(x86-64) = 1:1.0.2k-26.el7_9
   provider: openssl-libs.x86_64 1:1.0.2k-26.el7_9
  dependency: pkgconfig
   provider: pkgconfig.x86_64 1:0.27.1-4.el7
   provider: pkgconfig.i686 1:0.27.1-4.el7
  dependency: zlib-devel(x86-64)
   provider: zlib-devel.x86_64 1.2.7-21.el7_9

```

> `rpm -qR openssl-devel`:
```sql
/usr/bin/pkg-config
krb5-devel(x86-64)
libcrypto.so.10()(64bit)
libssl.so.10()(64bit)
openssl-libs(x86-64) = 1:1.0.2k-25.el7_9
pkgconfig
pkgconfig(libcrypto)
pkgconfig(libssl)
rpmlib(CompressedFileNames) <= 3.0.4-1
rpmlib(FileDigests) <= 4.6.0-1
rpmlib(PayloadFilesHavePrefix) <= 4.0-1
zlib-devel(x86-64)
rpmlib(PayloadIsXz) <= 5.2-1

```



### yum-plugin-downloadonly

```sql

yum -y install yum-plugin-downloadonly

yum install --downloadonly --downloaddir=<directory> <package-name>
```



```sql
yum install --downloadonly --downloaddir=/root/mypackages/ httpd

yum install --downloadonly --downloaddir=/root/mypackages/ httpd-2.2.6-40.el7

yum install --downloadonly --downloaddir=/root/mypackages/ httpd vsftpd

```


### yumdownloader
```sql
yum -y install yum-utils
```

``sql
yumdownloader httpd


yumdownloader --resolve httpd


yumdownloader --resolve --destdir=/root/mypackages/ httpd

yumdownloader --resolve --destdir /root/mypackages/ httpd


yumdownloader "@Development Tools" --resolve --destdir /root/mypackages/



 rpm -qpi linuxqq-v1.0.2-beta1.i386.rpm | head

```



on ubuntu

apt-get install libudev-dev



----------------------

on centos

yum install systemd-devel





###
```sql
[root@k8s-node-77 openssl]# yumdownloader --resolve --destdir=./  openssl-devel
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirrors.tuna.tsinghua.edu.cn
 * elrepo: mirrors.tuna.tsinghua.edu.cn
 * epel: mirrors.tuna.tsinghua.edu.cn
 * extras: mirrors.tuna.tsinghua.edu.cn
 * updates: mirrors.bupt.edu.cn
centos7-iso                                                                                                                                                                                                                                                       | 1.3 kB  00:00:00
http://172.17.130.48/iso-centos-7.6.1810/repodata/repomd.xml: [Errno -1] Error importing repomd.xml for centos7-iso: Damaged repomd.xml file
Trying other mirror.
--> Running transaction check
---> Package openssl-devel.i686 1:1.0.2k-26.el7_9 will be installed
--> Processing Dependency: openssl-libs(x86-32) = 1:1.0.2k-26.el7_9 for package: 1:openssl-devel-1.0.2k-26.el7_9.i686
--> Processing Dependency: zlib-devel(x86-32) for package: 1:openssl-devel-1.0.2k-26.el7_9.i686
--> Processing Dependency: libssl.so.10 for package: 1:openssl-devel-1.0.2k-26.el7_9.i686
--> Processing Dependency: libcrypto.so.10 for package: 1:openssl-devel-1.0.2k-26.el7_9.i686
--> Processing Dependency: krb5-devel(x86-32) for package: 1:openssl-devel-1.0.2k-26.el7_9.i686
---> Package openssl-devel.x86_64 1:1.0.2k-26.el7_9 will be installed
--> Running transaction check
---> Package krb5-devel.x86_64 0:1.15.1-54.el7_9 will be updated
---> Package krb5-devel.i686 0:1.15.1-55.el7_9 will be installed
--> Processing Dependency: libkadm5(x86-32) = 1.15.1-55.el7_9 for package: krb5-devel-1.15.1-55.el7_9.i686
--> Processing Dependency: krb5-libs(x86-32) = 1.15.1-55.el7_9 for package: krb5-devel-1.15.1-55.el7_9.i686
--> Processing Dependency: libselinux.so.1 for package: krb5-devel-1.15.1-55.el7_9.i686
--> Processing Dependency: libresolv.so.2 for package: krb5-devel-1.15.1-55.el7_9.i686
--> Processing Dependency: libkrb5support.so.0 for package: krb5-devel-1.15.1-55.el7_9.i686
--> Processing Dependency: libkrb5.so.3(krb5_3_MIT) for package: krb5-devel-1.15.1-55.el7_9.i686
--> Processing Dependency: libkrb5.so.3 for package: krb5-devel-1.15.1-55.el7_9.i686
--> Processing Dependency: libkrad.so.0 for package: krb5-devel-1.15.1-55.el7_9.i686
--> Processing Dependency: libkeyutils.so.1 for package: krb5-devel-1.15.1-55.el7_9.i686
--> Processing Dependency: libkdb5.so.8 for package: krb5-devel-1.15.1-55.el7_9.i686
--> Processing Dependency: libk5crypto.so.3 for package: krb5-devel-1.15.1-55.el7_9.i686
--> Processing Dependency: libgssrpc.so.4 for package: krb5-devel-1.15.1-55.el7_9.i686
--> Processing Dependency: libgssapi_krb5.so.2(gssapi_krb5_2_MIT) for package: krb5-devel-1.15.1-55.el7_9.i686
--> Processing Dependency: libgssapi_krb5.so.2 for package: krb5-devel-1.15.1-55.el7_9.i686
--> Processing Dependency: libdl.so.2 for package: krb5-devel-1.15.1-55.el7_9.i686
--> Processing Dependency: libcom_err.so.2 for package: krb5-devel-1.15.1-55.el7_9.i686
--> Processing Dependency: libc.so.6(GLIBC_2.15) for package: krb5-devel-1.15.1-55.el7_9.i686
---> Package krb5-devel.x86_64 0:1.15.1-55.el7_9 will be an update
---> Package openssl-libs.x86_64 1:1.0.2k-25.el7_9 will be updated
--> Processing Dependency: openssl-libs(x86-64) = 1:1.0.2k-25.el7_9 for package: 1:openssl-1.0.2k-25.el7_9.x86_64
--> Processing Dependency: openssl-libs(x86-64) = 1:1.0.2k-25.el7_9 for package: 1:openssl-devel-1.0.2k-25.el7_9.x86_64
---> Package openssl-libs.i686 1:1.0.2k-26.el7_9 will be installed
--> Processing Dependency: libz.so.1 for package: 1:openssl-libs-1.0.2k-26.el7_9.i686
---> Package openssl-libs.x86_64 1:1.0.2k-26.el7_9 will be an update
---> Package zlib-devel.x86_64 0:1.2.7-20.el7_9 will be updated
---> Package zlib-devel.i686 0:1.2.7-21.el7_9 will be installed
---> Package zlib-devel.x86_64 0:1.2.7-21.el7_9 will be an update
--> Running transaction check
---> Package glibc.x86_64 0:2.17-260.el7 will be updated
--> Processing Dependency: glibc = 2.17-260.el7 for package: glibc-common-2.17-260.el7.x86_64
--> Processing Dependency: glibc = 2.17-260.el7 for package: glibc-headers-2.17-260.el7.x86_64
--> Processing Dependency: glibc = 2.17-260.el7 for package: glibc-devel-2.17-260.el7.x86_64
---> Package glibc.i686 0:2.17-326.el7_9 will be installed
--> Processing Dependency: libfreebl3.so(NSSRAWHASH_3.12.3) for package: glibc-2.17-326.el7_9.i686
--> Processing Dependency: libfreebl3.so for package: glibc-2.17-326.el7_9.i686
---> Package glibc.x86_64 0:2.17-326.el7_9 will be an update
---> Package keyutils-libs.i686 0:1.5.8-3.el7 will be installed
---> Package krb5-libs.x86_64 0:1.15.1-54.el7_9 will be updated
--> Processing Dependency: krb5-libs(x86-64) = 1.15.1-54.el7_9 for package: krb5-workstation-1.15.1-54.el7_9.x86_64
---> Package krb5-libs.i686 0:1.15.1-55.el7_9 will be installed
--> Processing Dependency: libverto.so.1 for package: krb5-libs-1.15.1-55.el7_9.i686
---> Package krb5-libs.x86_64 0:1.15.1-55.el7_9 will be an update
---> Package libcom_err.i686 0:1.42.9-19.el7 will be installed
---> Package libkadm5.x86_64 0:1.15.1-54.el7_9 will be updated
---> Package libkadm5.i686 0:1.15.1-55.el7_9 will be installed
---> Package libkadm5.x86_64 0:1.15.1-55.el7_9 will be an update
---> Package libselinux.i686 0:2.5-15.el7 will be installed
--> Processing Dependency: libsepol(x86-32) >= 2.5-10 for package: libselinux-2.5-15.el7.i686
--> Processing Dependency: libsepol.so.1(LIBSEPOL_1.0) for package: libselinux-2.5-15.el7.i686
--> Processing Dependency: libsepol.so.1 for package: libselinux-2.5-15.el7.i686
--> Processing Dependency: libpcre.so.1 for package: libselinux-2.5-15.el7.i686
---> Package openssl.x86_64 1:1.0.2k-25.el7_9 will be updated
---> Package openssl.x86_64 1:1.0.2k-26.el7_9 will be an update
---> Package openssl-devel.x86_64 1:1.0.2k-25.el7_9 will be updated
---> Package zlib.x86_64 0:1.2.7-20.el7_9 will be updated
---> Package zlib.i686 0:1.2.7-21.el7_9 will be installed
---> Package zlib.x86_64 0:1.2.7-21.el7_9 will be an update
--> Running transaction check
---> Package glibc-common.x86_64 0:2.17-260.el7 will be updated
---> Package glibc-common.x86_64 0:2.17-326.el7_9 will be an update
---> Package glibc-devel.x86_64 0:2.17-260.el7 will be updated
---> Package glibc-devel.x86_64 0:2.17-326.el7_9 will be an update
---> Package glibc-headers.x86_64 0:2.17-260.el7 will be updated
---> Package glibc-headers.x86_64 0:2.17-326.el7_9 will be an update
---> Package krb5-workstation.x86_64 0:1.15.1-54.el7_9 will be updated
---> Package krb5-workstation.x86_64 0:1.15.1-55.el7_9 will be an update
---> Package libsepol.i686 0:2.5-10.el7 will be installed
---> Package libverto.i686 0:0.2.5-4.el7 will be installed
---> Package nss-softokn-freebl.x86_64 0:3.36.0-5.el7_5 will be updated
---> Package nss-softokn-freebl.i686 0:3.90.0-6.el7_9 will be installed
--> Processing Dependency: nss-util >= 3.90.0-1 for package: nss-softokn-freebl-3.90.0-6.el7_9.i686
--> Processing Dependency: nspr >= 4.35.0 for package: nss-softokn-freebl-3.90.0-6.el7_9.i686
---> Package nss-softokn-freebl.x86_64 0:3.90.0-6.el7_9 will be an update
---> Package pcre.i686 0:8.32-17.el7 will be installed
--> Processing Dependency: libstdc++.so.6(GLIBCXX_3.4.9) for package: pcre-8.32-17.el7.i686
--> Processing Dependency: libstdc++.so.6(GLIBCXX_3.4) for package: pcre-8.32-17.el7.i686
--> Processing Dependency: libstdc++.so.6(CXXABI_1.3) for package: pcre-8.32-17.el7.i686
--> Processing Dependency: libstdc++.so.6 for package: pcre-8.32-17.el7.i686
--> Processing Dependency: libgcc_s.so.1(GCC_3.0) for package: pcre-8.32-17.el7.i686
--> Processing Dependency: libgcc_s.so.1 for package: pcre-8.32-17.el7.i686
--> Running transaction check
---> Package libgcc.i686 0:4.8.5-44.el7 will be installed
---> Package libstdc++.i686 0:4.8.5-44.el7 will be installed
---> Package nspr.x86_64 0:4.19.0-1.el7_5 will be updated
---> Package nspr.x86_64 0:4.35.0-1.el7_9 will be an update
---> Package nss-util.x86_64 0:3.36.0-1.el7_5 will be updated
---> Package nss-util.x86_64 0:3.90.0-1.el7_9 will be an update
--> Finished Dependency Resolution
No Presto metadata available for updates
(1/33): glibc-2.17-326.el7_9.i686.rpm                                                                                                                                                                                                                             | 4.3 MB  00:00:01
(2/33): keyutils-libs-1.5.8-3.el7.i686.rpm                                                                                                                                                                                                                        |  25 kB  00:00:00
(3/33): krb5-devel-1.15.1-55.el7_9.i686.rpm                                                                                                                                                                                                                       | 272 kB  00:00:00
(4/33): krb5-devel-1.15.1-55.el7_9.x86_64.rpm                                                                                                                                                                                                                     | 273 kB  00:00:00
(5/33): krb5-libs-1.15.1-55.el7_9.i686.rpm                                                                                                                                                                                                                        | 812 kB  00:00:00
(6/33): krb5-libs-1.15.1-55.el7_9.x86_64.rpm                                                                                                                                                                                                                      | 810 kB  00:00:01
(7/33): glibc-devel-2.17-326.el7_9.x86_64.rpm                                                                                                                                                                                                                     | 1.1 MB  00:00:03
(8/33): libcom_err-1.42.9-19.el7.i686.rpm                                                                                                                                                                                                                         |  42 kB  00:00:00
(9/33): libgcc-4.8.5-44.el7.i686.rpm                                                                                                                                                                                                                              | 111 kB  00:00:00
(10/33): krb5-workstation-1.15.1-55.el7_9.x86_64.rpm                                                                                                                                                                                                              | 821 kB  00:00:01
(11/33): glibc-headers-2.17-326.el7_9.x86_64.rpm                                                                                                                                                                                                                  | 691 kB  00:00:05
(12/33): libkadm5-1.15.1-55.el7_9.x86_64.rpm                                                                                                                                                                                                                      | 180 kB  00:00:00
(13/33): libselinux-2.5-15.el7.i686.rpm                                                                                                                                                                                                                           | 166 kB  00:00:00
(14/33): libkadm5-1.15.1-55.el7_9.i686.rpm                                                                                                                                                                                                                        | 180 kB  00:00:01
(15/33): libstdc++-4.8.5-44.el7.i686.rpm                                                                                                                                                                                                                          | 319 kB  00:00:00
(16/33): nspr-4.35.0-1.el7_9.x86_64.rpm                                                                                                                                                                                                                           | 128 kB  00:00:00
(17/33): nss-softokn-freebl-3.90.0-6.el7_9.i686.rpm                                                                                                                                                                                                               | 327 kB  00:00:00
(18/33): nss-softokn-freebl-3.90.0-6.el7_9.x86_64.rpm                                                                                                                                                                                                             | 321 kB  00:00:00
(19/33): libverto-0.2.5-4.el7.i686.rpm                                                                                                                                                                                                                            |  16 kB  00:00:01
(20/33): nss-util-3.90.0-1.el7_9.x86_64.rpm                                                                                                                                                                                                                       |  80 kB  00:00:00
(21/33): libsepol-2.5-10.el7.i686.rpm                                                                                                                                                                                                                             | 294 kB  00:00:02
(22/33): openssl-1.0.2k-26.el7_9.x86_64.rpm                                                                                                                                                                                                                       | 494 kB  00:00:00
(23/33): openssl-libs-1.0.2k-26.el7_9.i686.rpm                                                                                                                                                                                                                    | 998 kB  00:00:01
(24/33): openssl-devel-1.0.2k-26.el7_9.i686.rpm                                                                                                                                                                                                                   | 1.5 MB  00:00:02
(25/33): openssl-libs-1.0.2k-26.el7_9.x86_64.rpm                                                                                                                                                                                                                  | 1.2 MB  00:00:02
(26/33): zlib-1.2.7-21.el7_9.i686.rpm                                                                                                                                                                                                                             |  91 kB  00:00:00
(27/33): zlib-1.2.7-21.el7_9.x86_64.rpm                                                                                                                                                                                                                           |  90 kB  00:00:00
(28/33): zlib-devel-1.2.7-21.el7_9.i686.rpm                                                                                                                                                                                                                       |  50 kB  00:00:00
(29/33): zlib-devel-1.2.7-21.el7_9.x86_64.rpm                                                                                                                                                                                                                     |  50 kB  00:00:00
(30/33): openssl-devel-1.0.2k-26.el7_9.x86_64.rpm                                                                                                                                                                                                                 | 1.5 MB  00:00:05
(31/33): glibc-common-2.17-326.el7_9.x86_64.rpm                                                                                                                                                                                                                   |  12 MB  00:00:18
(32/33): pcre-8.32-17.el7.i686.rpm                                                                                                                                                                                                                                | 420 kB  00:00:13
(33/33): glibc-2.17-326.el7_9.x86_64.rpm                                                                                                                                                                                                                          | 3.6 MB  00:01:21
[root@k8s-node-77 openssl]# ll
total 33548
-rw-r--r-- 1 root root  4466232 May 19  2022 glibc-2.17-326.el7_9.i686.rpm
-rw-r--r-- 1 root root  3817244 May 19  2022 glibc-2.17-326.el7_9.x86_64.rpm
-rw-r--r-- 1 root root 12059896 May 19  2022 glibc-common-2.17-326.el7_9.x86_64.rpm
-rw-r--r-- 1 root root  1128524 May 19  2022 glibc-devel-2.17-326.el7_9.x86_64.rpm
-rw-r--r-- 1 root root   707512 May 19  2022 glibc-headers-2.17-326.el7_9.x86_64.rpm
-rw-r--r-- 1 root root    25852 Jul  4  2014 keyutils-libs-1.5.8-3.el7.i686.rpm
-rw-r--r-- 1 root root   279040 Dec  1  2022 krb5-devel-1.15.1-55.el7_9.i686.rpm
-rw-r--r-- 1 root root   279860 Dec  1  2022 krb5-devel-1.15.1-55.el7_9.x86_64.rpm
-rw-r--r-- 1 root root   831008 Dec  1  2022 krb5-libs-1.15.1-55.el7_9.i686.rpm
-rw-r--r-- 1 root root   829408 Dec  1  2022 krb5-libs-1.15.1-55.el7_9.x86_64.rpm
-rw-r--r-- 1 root root   840496 Dec  1  2022 krb5-workstation-1.15.1-55.el7_9.x86_64.rpm
-rw-r--r-- 1 root root    43132 Oct 15  2020 libcom_err-1.42.9-19.el7.i686.rpm
-rw-r--r-- 1 root root   113236 Oct 15  2020 libgcc-4.8.5-44.el7.i686.rpm
-rw-r--r-- 1 root root   184364 Dec  1  2022 libkadm5-1.15.1-55.el7_9.i686.rpm
-rw-r--r-- 1 root root   183908 Dec  1  2022 libkadm5-1.15.1-55.el7_9.x86_64.rpm
-rw-r--r-- 1 root root   169856 Apr  4  2020 libselinux-2.5-15.el7.i686.rpm
-rw-r--r-- 1 root root   301460 Nov 12  2018 libsepol-2.5-10.el7.i686.rpm
-rw-r--r-- 1 root root   326568 Oct 15  2020 libstdc++-4.8.5-44.el7.i686.rpm
-rw-r--r-- 1 root root    16728 Jul  4  2014 libverto-0.2.5-4.el7.i686.rpm
-rw-r--r-- 1 root root   131028 Oct 19 22:04 nspr-4.35.0-1.el7_9.x86_64.rpm
-rw-r--r-- 1 root root   335260 Oct 19 22:04 nss-softokn-freebl-3.90.0-6.el7_9.i686.rpm
-rw-r--r-- 1 root root   328772 Oct 19 22:04 nss-softokn-freebl-3.90.0-6.el7_9.x86_64.rpm
-rw-r--r-- 1 root root    82096 Oct 19 22:05 nss-util-3.90.0-1.el7_9.x86_64.rpm
-rw-r--r-- 1 root root   506040 Mar 21  2023 openssl-1.0.2k-26.el7_9.x86_64.rpm
-rw-r--r-- 1 root root  1582172 Mar 21  2023 openssl-devel-1.0.2k-26.el7_9.i686.rpm
-rw-r--r-- 1 root root  1582400 Mar 21  2023 openssl-devel-1.0.2k-26.el7_9.x86_64.rpm
-rw-r--r-- 1 root root  1021796 Mar 21  2023 openssl-libs-1.0.2k-26.el7_9.i686.rpm
-rw-r--r-- 1 root root  1256452 Mar 21  2023 openssl-libs-1.0.2k-26.el7_9.x86_64.rpm
-rw-r--r-- 1 root root   430428 Aug 11  2017 pcre-8.32-17.el7.i686.rpm
-rw-r--r-- 1 root root    93224 Mar  8  2023 zlib-1.2.7-21.el7_9.i686.rpm
-rw-r--r-- 1 root root    92260 Mar  8  2023 zlib-1.2.7-21.el7_9.x86_64.rpm
-rw-r--r-- 1 root root    51524 Mar  8  2023 zlib-devel-1.2.7-21.el7_9.i686.rpm
-rw-r--r-- 1 root root    51488 Mar  8  2023 zlib-devel-1.2.7-21.el7_9.x86_64.rpm
[root@k8s-node-77 openssl]# du -sch *
4.3M	glibc-2.17-326.el7_9.i686.rpm
3.7M	glibc-2.17-326.el7_9.x86_64.rpm
12M	glibc-common-2.17-326.el7_9.x86_64.rpm
1.1M	glibc-devel-2.17-326.el7_9.x86_64.rpm
696K	glibc-headers-2.17-326.el7_9.x86_64.rpm
32K	keyutils-libs-1.5.8-3.el7.i686.rpm
280K	krb5-devel-1.15.1-55.el7_9.i686.rpm
280K	krb5-devel-1.15.1-55.el7_9.x86_64.rpm
816K	krb5-libs-1.15.1-55.el7_9.i686.rpm
816K	krb5-libs-1.15.1-55.el7_9.x86_64.rpm
828K	krb5-workstation-1.15.1-55.el7_9.x86_64.rpm
48K	libcom_err-1.42.9-19.el7.i686.rpm
116K	libgcc-4.8.5-44.el7.i686.rpm
188K	libkadm5-1.15.1-55.el7_9.i686.rpm
184K	libkadm5-1.15.1-55.el7_9.x86_64.rpm
172K	libselinux-2.5-15.el7.i686.rpm
300K	libsepol-2.5-10.el7.i686.rpm
324K	libstdc++-4.8.5-44.el7.i686.rpm
24K	libverto-0.2.5-4.el7.i686.rpm
132K	nspr-4.35.0-1.el7_9.x86_64.rpm
332K	nss-softokn-freebl-3.90.0-6.el7_9.i686.rpm
328K	nss-softokn-freebl-3.90.0-6.el7_9.x86_64.rpm
88K	nss-util-3.90.0-1.el7_9.x86_64.rpm
500K	openssl-1.0.2k-26.el7_9.x86_64.rpm
1.6M	openssl-devel-1.0.2k-26.el7_9.i686.rpm
1.6M	openssl-devel-1.0.2k-26.el7_9.x86_64.rpm
1004K	openssl-libs-1.0.2k-26.el7_9.i686.rpm
1.3M	openssl-libs-1.0.2k-26.el7_9.x86_64.rpm
428K	pcre-8.32-17.el7.i686.rpm
96K	zlib-1.2.7-21.el7_9.i686.rpm
96K	zlib-1.2.7-21.el7_9.x86_64.rpm
56K	zlib-devel-1.2.7-21.el7_9.i686.rpm
56K	zlib-devel-1.2.7-21.el7_9.x86_64.rpm
33M	total
[root@k8s-node-77 openssl]#


```



```sql
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/yourname/nccl/build/lib # 添加环境变量；也可以配置环境变量.bashrc;

export C_INCLUDE_PATH=/home/yourname/nccl/build/include (设置 C 头文件路径)

export CPLUS_INCLUDE_PATH=/home/yourname/nccl/build/include (设置C++头文件路径)
```

