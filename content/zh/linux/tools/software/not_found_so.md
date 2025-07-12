---
title: GLIBCXX_3.4.20 not found
description: GLIBCXX_3.4.20 not found
date: 2023-11-06
weight: 30000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{<note>}}
<!---->



{{</note>}}


## 问题

```bash
[root@igoodful ~]# hugo --version
hugo: /lib64/libstdc++.so.6: version `GLIBCXX_3.4.20' not found (required by hugo)
hugo: /lib64/libstdc++.so.6: version `CXXABI_1.3.8' not found (required by hugo)
hugo: /lib64/libstdc++.so.6: version `GLIBCXX_3.4.21' not found (required by hugo)

[root@igoodful ~]# strings /usr/lib64/libstdc++.so.6 | grep GLIBCXX
GLIBCXX_3.4
GLIBCXX_3.4.1
GLIBCXX_3.4.2
GLIBCXX_3.4.3
GLIBCXX_3.4.4
GLIBCXX_3.4.5
GLIBCXX_3.4.6
GLIBCXX_3.4.7
GLIBCXX_3.4.8
GLIBCXX_3.4.9
GLIBCXX_3.4.10
GLIBCXX_3.4.11
GLIBCXX_3.4.12
GLIBCXX_3.4.13
GLIBCXX_3.4.14
GLIBCXX_3.4.15
GLIBCXX_3.4.16
GLIBCXX_3.4.17
GLIBCXX_3.4.18
GLIBCXX_3.4.19
GLIBCXX_DEBUG_MESSAGE_LENGTH

[root@igoodful ~]# uname -a
Linux igoodful 3.10.0-1160.90.1.el7.x86_64 #1 SMP Thu May 4 15:21:22 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux

[root@igoodful ~]# cat /etc/redhat-release
CentOS Linux release 7.9.2009 (Core)

```



## 解决

```bash
[root@igoodful ~]# wget http://mirror.ghettoforge.org/distributions/gf/el/7/gf/x86_64/gf-release-7-12.gf.el7.noarch.rpm

[root@igoodful ~]# rpm -Uvh gf-release-7-12.gf.el7.noarch.rpm

[root@igoodful ~]# yum -y install gcc10-libstdc++

[root@igoodful ~]# rpm -ql gcc10-libstdc++.x86_64

[root@igoodful ~]# find / -name "libstdc++.so.6*"
/usr/lib64/libstdc++.so.6.0.19
/usr/lib64/libstdc++.so.6
/usr/share/gdb/auto-load/usr/lib64/libstdc++.so.6.0.19-gdb.py
/usr/share/gdb/auto-load/usr/lib64/libstdc++.so.6.0.19-gdb.pyc
/usr/share/gdb/auto-load/usr/lib64/libstdc++.so.6.0.19-gdb.pyo
/var/lib/docker/overlay2/fcf96e0f111bcc9a6ae2930304be1b67a1c042448862045d436f50249c9983ff/diff/usr/lib64/libstdc++.so.6
/var/lib/docker/overlay2/fcf96e0f111bcc9a6ae2930304be1b67a1c042448862045d436f50249c9983ff/diff/usr/lib64/libstdc++.so.6.0.25
/var/lib/docker/overlay2/fcf96e0f111bcc9a6ae2930304be1b67a1c042448862045d436f50249c9983ff/diff/usr/share/gdb/auto-load/usr/lib64/__pycache__/libstdc++.so.6.0.25-gdb.cpython-36.pyc
/var/lib/docker/overlay2/fcf96e0f111bcc9a6ae2930304be1b67a1c042448862045d436f50249c9983ff/diff/usr/share/gdb/auto-load/usr/lib64/__pycache__/libstdc++.so.6.0.25-gdb.cpython-36.opt-1.pyc
/var/lib/docker/overlay2/fcf96e0f111bcc9a6ae2930304be1b67a1c042448862045d436f50249c9983ff/diff/usr/share/gdb/auto-load/usr/lib64/libstdc++.so.6.0.25-gdb.py
/opt/gcc-10.2.1/usr/lib64/libstdc++.so.6
/opt/gcc-10.2.1/usr/lib64/libstdc++.so.6.0.28
/opt/gcc-10.2.1/usr/share/gdb/auto-load/opt/gcc-10.2.1/usr/lib64/__pycache__/libstdc++.so.6.0.28-gdb.cpython-36.pyc
/opt/gcc-10.2.1/usr/share/gdb/auto-load/opt/gcc-10.2.1/usr/lib64/__pycache__/libstdc++.so.6.0.28-gdb.cpython-36.opt-1.pyc
/opt/gcc-10.2.1/usr/share/gdb/auto-load/opt/gcc-10.2.1/usr/lib64/libstdc++.so.6.0.28-gdb.pyo
/opt/gcc-10.2.1/usr/share/gdb/auto-load/opt/gcc-10.2.1/usr/lib64/libstdc++.so.6.0.28-gdb.pyc
/opt/gcc-10.2.1/usr/share/gdb/auto-load/opt/gcc-10.2.1/usr/lib64/libstdc++.so.6.0.28-gdb.py


[root@igoodful ~]# cd /usr/lib64
[root@igoodful ~]# cp /opt/gcc-10.2.1/usr/lib64/libstdc++.so.6.0.28 /usr/lib64/

[root@igoodful lib64]# rm  -rf libstdc++.so.6
[root@igoodful lib64]# ln -s libstdc++.so.6.0.28 libstdc++.so.6

[root@igoodful ~]# hugo version
hugo v0.126.2-8f3d902ce51512931f5759e9279d93e346c9e649+extended linux/amd64 BuildDate=2024-05-30T15:19:22Z VendorInfo=gohugoio


```













