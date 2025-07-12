---
title: 10. socat
description: Socat是Socket CAT的简写， 是Linux下的一个多功能的网络工具.
date: 2023-10-12
weight: 100
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

{{< /alert >}}


## 一、安装socat

### （1）源码安装
```bash
#!/bin/bash
wget http://www.dest-unreach.org/socat/download/socat-1.7.3.2.tar.gz
tar -xzvf socat-1.7.3.2.tar.gz
cd socat-1.7.3.2
./configure
make
make install

```
### （2）yum安装

```bash
yum install -y socat
```

## 二、使用




### 文件传输
- -u 表示数据传输模式为单向，从左面参数到右面参数。

1. 将文件filename.tgz从10.10.10.11传送到上10.10.10.22
```bash

socat -u open:filename.tgz tcp-listen:2000,reuseaddr # 先在10.10.10.11上执行


socat -u tcp:10.10.10.11:2000 open:filename.tgz,create # 后在10.10.10.22上执行
```

2. 将文件filename.tgz从10.10.10.11传送到10.10.10.22上
```bash

socat -u TCP-LISTEN:18080 open:filename.tgz,create # 文件接受端先开启，在10.10.10.22上执行


socat -u open:filename.tgz TCP:10.10.10.22:18080 # 文件发送端后开启，在10.10.10.11上执行
```



3. 将文件filename.tgz从10.10.10.11传送到10.10.10.22上

```bash
socat TCP4-LISTEN:12345,reuseaddr - | cat - > filename  # 先在文件接受端先开启，10.10.10.22上执行

socat -d -lf /root/socat.log -u tcp-listen:12345,reuseaddr file:/root/filename,trunc,create # 先在文件接受端先开启，10.10.10.22上执行

echo "hello" |socat - TCP4:10.10.10.22:12345,retry=100  # 再在文件发送端后开启，10.10.10.11上执行

cat filename |socat - TCP4:10.10.10.22:12345,retry=100  # 再在文件发送端后开启，10.10.10.11上执行

```


### 全备

#### 备份服务器上执行：
```bash
socat -d -lf /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240220/45509_1 -u tcp-listen:45509,reuseaddr stdout > /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240220082254.590-full/normal_0-normal_0_0-172.17.134.71-16315/backup.xbsteam &

```

#### 备份节点执行：
```bash
/bin/sh -c xtrabackup --defaults-file=/data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/my16315.conf --user=dbscale_internal --password=123456 --host=172.17.134.71 --port=16315 --backup --tmpdir=/data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/tmp  --stream=xbstream  2>/data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/tmp/UTC20240220082254.590-normal_0-normal_0_0-172.17.134.71-16315 | socat - TCP4:172.17.134.76:45509,retry=10
```



####

```bash
# 备份节点执行：
xtrabackup --defaults-file=./my16315.conf  --user=dbscale_internal --password='123456' --host=172.17.134.77 --port=16315 --backup --tmpdir=/tmp --stream=xbstream  2>xtrabackup.log | socat - TCP4:172.17.134.71:45509,retry=10

# 备份服务器上执行：
socat -d -lf /root/12345.log -u tcp-listen:45509,reuseaddr stdout > backup.xbsteam
# 或
socat -d -lf /root/12345.log -u tcp-listen:45509,reuseaddr file:./backup.xbsteam
```





**反向**

A等待连接，B连接到A后，A发送文件到B：

1. 机器A上，打开文件，然后把文件发送到65535端口，    当有连接连接到该端口时，把文件发到客户端上。
```bash
sudo socat open:svn.tar.gz tcp4-listen:65535
```

2. 机器B，连接到机器A，并把发送过来的数据保存到文件

```bash
sudo socat tcp4:192.168.1.8:65535 open:svn.tar.gz,create
```

**正向**

A等待连接，B连接到A后，B发送文件到A：

1. 机器A上
```bash
sudo socat tcp4-listen:65535 open:svn.tar.gz,create
```

2. 机器B
```bash
sudo socat open:svn.tar.gz tcp4:192.168.1.8:65535
```





## 静态编译

- 将编译好的glibc2.28中lib目录下的所有静态库放到一个单独的目录下，编译socat时添加该库到库搜索路径下即可
-

```bash
[root@centos8-25 /glc/glibc/app/glibc-2.28] # ll
total 20
drwxr-xr-x  2 root root  240 2025-04-28T12:00:55 bin
-rw-r--r--  1 root root 3214 2025-04-28T10:44:21 buildinfo
drwxr-xr-x  2 root root   36 2025-04-28T12:00:55 etc
drwxr-xr-x 21 root root 4096 2025-04-28T12:00:53 include
drwxr-xr-x  4 root root 4096 2025-04-28T12:00:55 lib
drwxr-xr-x  3 root root   21 2025-04-28T11:59:57 libexec
drwxr-xr-x  2 root root    6 2025-04-28T12:23:31 my_static_lib
drwxr-xr-x  2 root root   88 2025-04-28T12:00:55 sbin
drwxr-xr-x  5 root root   44 2025-04-28T12:00:46 share
drwxr-xr-x  3 root root   16 2025-04-28T12:00:52 var
[root@centos8-25 /glc/glibc/app/glibc-2.28] # cp lib/*.a my_static_lib/
[root@centos8-25 /glc/glibc/app/glibc-2.28] # ll my_static_lib/
total 37264
-rw-r--r-- 1 root root   109228 2025-04-28T12:24:01 libanl.a
-rw-r--r-- 1 root root    26342 2025-04-28T12:24:01 libBrokenLocale.a
-rw-r--r-- 1 root root 23755668 2025-04-28T12:24:01 libc.a
-rw-r--r-- 1 root root    84930 2025-04-28T12:24:01 libc_nonshared.a
-rw-r--r-- 1 root root   199332 2025-04-28T12:24:01 libcrypt.a
-rw-r--r-- 1 root root    69442 2025-04-28T12:24:01 libdl.a
-rw-r--r-- 1 root root     2874 2025-04-28T12:24:01 libg.a
-rw-r--r-- 1 root root  9473412 2025-04-28T12:24:01 libm-2.28.a
-rw-r--r-- 1 root root      140 2025-04-28T12:24:01 libm.a
-rw-r--r-- 1 root root     4224 2025-04-28T12:24:01 libmcheck.a
-rw-r--r-- 1 root root   703396 2025-04-28T12:24:01 libmvec.a
-rw-r--r-- 1 root root     7254 2025-04-28T12:24:01 libmvec_nonshared.a
-rw-r--r-- 1 root root  2643230 2025-04-28T12:24:01 libpthread.a
-rw-r--r-- 1 root root   587852 2025-04-28T12:24:01 libresolv.a
-rw-r--r-- 1 root root   403102 2025-04-28T12:24:01 librt.a
-rw-r--r-- 1 root root    59648 2025-04-28T12:24:01 libutil.a

```

- ansible
```yaml
# source
- name: "socat_deploy_method == 'source'"
  block:
      # ./configure
      # LDFLAGS='--static'
      - name: "cd {{ socat_source_dir }};./configure --prefix={{ socat_app_dir }} LDFLAGS='-static -L/glc/glibc/app/glibc-2.28/my_static_lib'"
        shell:
            chdir: "{{ socat_source_dir }}"
            cmd: "./configure --prefix={{ socat_app_dir }} LDFLAGS='-static -L/glc/glibc/app/glibc-2.28/my_static_lib'"
      # make
      - name: "cd {{ socat_source_dir }};make -j1"
        shell:
            chdir: "{{ socat_source_dir }}"
            cmd: "make -j1"
      # make install
      - name: "cd {{ socat_source_dir }};make install"
        shell:
            chdir: "{{ socat_source_dir }}"
            cmd: make install
  when: socat_deploy_method == "source"

```







