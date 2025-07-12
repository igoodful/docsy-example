---
title: xtrabackup
description: xtrabackup
date: 2023-11-06
weight: 30000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{%pageinfo%}}

{{%/pageinfo%}}



```bash
yum -y install cmake automake autoconf libtool bison
yum -y install openssl openssl-devel libaio libaio-devel zlib zlib-devel libcurl libcurl-devel libev libev-devel libgcrypt libgcrypt-devel ncurses ncurses-devel
yum -y install zstd vim-common procps-ng-devel

# 要安装手册页，请先安装 python3-sphinx 包
yum -y install python3-sphinx
```

```bash
mkdir build
cd build
cmake -DWITH_BOOST=PATH-TO-BOOST-LIBRARY -DDOWNLOAD_BOOST=ON -DBUILD_CONFIG=xtrabackup_release -DWITH_MAN_PAGES=OFF -B ..

make

make -j$(nproc --all)

sudo make DESTDIR=<DIR_NAME> install

```

{{<note>}}
<!---->
- -DCMAKE_INSTALL_PREFIX=path/to/install
- -DWITH_BOOST=/data/boost
- -DDOWNLOAD_BOOST=OFF
- -DBUILD_CONFIG=xtrabackup_release
- -DWITH_MAN_PAGES=OFF
- -DCMAKE_C_COMPILER=/usr/bin/gcc
- -DCMAKE_CXX_COMPILER=/usr/bin/g++
- -DFORCE_INSOURCE_BUILD=1
- -DCMAKE_BUILD_TYPE=Debug

{{</note>}}

### /root/percona-xtrabackup-release-8.0.32-26/
```bash

mkdir -p /root/build_xtrabackup_8032
cd /root/build_xtrabackup_8032

cp -r libkmip  /root/percona-xtrabackup-release-8.0.32-26/extra/
mv boost_1_77_0 ../


cmake ../percona-xtrabackup-release-8.0.32-26/ -DWITH_BOOST=./boost_1_77_0 -DDOWNLOAD_BOOST=OFF -DBUILD_CONFIG=xtrabackup_release -DWITH_MAN_PAGES=OFF -DCMAKE_BUILD_TYPE=Debug -DCMAKE_INSTALL_PREFIX=/root/local/xtrabackup_8032

make -j8

make install
```

### 备份

```bash
/root/local/xtrabackup_8032/bin/xtrabackup --user=root  --password=root --host=172.17.137.12 --port=7032 --backup --target-dir=/root/local/backup/ --slave-info

```


### version GLIBCXX_3.4.26 not found
```bash
[root@node83 bin]# ./xtrabackup --help
./xtrabackup: /lib64/libstdc++.so.6: version `GLIBCXX_3.4.26' not found (required by ./xtrabackup)

```

**解决**：

```bash
[root@node83 percona-xtrabackup]# cd lib/private/
[root@node83 private]# ll
总用量 35688
drwxr-xr-x 3 root root       38  7月  4  2023 icudt69l
-rwxr-xr-x 1 root root  2368608  7月  4  2023 libprotobuf-lite.so.3.19.4
-rwxr-xr-x 1 root root 12914000  7月  4  2023 libprotobuf.so.3.19.4
-rwxr-xr-x 1 root root 21255976  9月  1 10:08 libstdc++.so.6.0.28

[root@node83 private]# cp libstdc++.so.6.0.28 /usr/lib64/
[root@node83 private]# rm -rf /usr/lib64/libstdc++.so.6
[root@node83 private]# ln -s /usr/lib64/libstdc++.so.6.0.28 /usr/lib64/libstdc++.so.6

```











