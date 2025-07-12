---
title: 1. 安装
date: 2024-09-12
weight: 10
description: mysql-connector-python
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}

1. 跨平台的。它可以运行在Windows、Mac和各种Linux/Unix系统上。

{{< /alert >}}


### 安装

python3 --version


pip3 install aiomysql

#### 升级pip
python -m pip install -U pip setuptools


### whl 安装

1. unzip 解压安装
```bash
[root@dev site-packages]# unzip kazoo-2.7.0-py2.py3-none-any.whl
Archive:  kazoo-2.7.0-py2.py3-none-any.whl
  inflating: kazoo/__init__.py
  inflating: kazoo/client.py
  inflating: kazoo/exceptions.py
  inflating: kazoo/hosts.py
  inflating: kazoo/interfaces.py
  inflating: kazoo/loggingsupport.py
  inflating: kazoo/python2atexit.py
  inflating: kazoo/retry.py
  inflating: kazoo/security.py
  inflating: kazoo/version.py
  inflating: kazoo/handlers/__init__.py
  inflating: kazoo/handlers/eventlet.py
  inflating: kazoo/handlers/gevent.py
  inflating: kazoo/handlers/threading.py
  inflating: kazoo/handlers/utils.py
  inflating: kazoo/protocol/__init__.py
  inflating: kazoo/protocol/connection.py
  inflating: kazoo/protocol/paths.py
  inflating: kazoo/protocol/serialization.py
  inflating: kazoo/protocol/states.py
  inflating: kazoo/recipe/__init__.py
  inflating: kazoo/recipe/barrier.py
  inflating: kazoo/recipe/cache.py
  inflating: kazoo/recipe/counter.py
  inflating: kazoo/recipe/election.py
  inflating: kazoo/recipe/lease.py
  inflating: kazoo/recipe/lock.py
  inflating: kazoo/recipe/partitioner.py
  inflating: kazoo/recipe/party.py
  inflating: kazoo/recipe/queue.py
  inflating: kazoo/recipe/watchers.py
  inflating: kazoo/testing/__init__.py
  inflating: kazoo/testing/common.py
  inflating: kazoo/testing/harness.py
  inflating: kazoo/tests/__init__.py
  inflating: kazoo/tests/test_barrier.py
  inflating: kazoo/tests/test_build.py
  inflating: kazoo/tests/test_cache.py
  inflating: kazoo/tests/test_client.py
  inflating: kazoo/tests/test_connection.py
  inflating: kazoo/tests/test_counter.py
  inflating: kazoo/tests/test_election.py
  inflating: kazoo/tests/test_eventlet_handler.py
  inflating: kazoo/tests/test_exceptions.py
  inflating: kazoo/tests/test_gevent_handler.py
  inflating: kazoo/tests/test_hosts.py
  inflating: kazoo/tests/test_interrupt.py
  inflating: kazoo/tests/test_lease.py
  inflating: kazoo/tests/test_lock.py
  inflating: kazoo/tests/test_partitioner.py
  inflating: kazoo/tests/test_party.py
  inflating: kazoo/tests/test_paths.py
  inflating: kazoo/tests/test_queue.py
  inflating: kazoo/tests/test_retry.py
  inflating: kazoo/tests/test_sasl.py
  inflating: kazoo/tests/test_security.py
  inflating: kazoo/tests/test_threading_handler.py
  inflating: kazoo/tests/test_utils.py
  inflating: kazoo/tests/test_watchers.py
  inflating: kazoo/tests/util.py
  inflating: kazoo-2.7.0.dist-info/LICENSE
  inflating: kazoo-2.7.0.dist-info/METADATA
  inflating: kazoo-2.7.0.dist-info/WHEEL
  inflating: kazoo-2.7.0.dist-info/top_level.txt
  inflating: kazoo-2.7.0.dist-info/RECORD
[root@dev site-packages]# ll
total 164
-rw-r--r--.  1 root root    126 Apr  1  2021 easy_install.py
-rw-r--r--.  2 root root    315 Apr  1  2021 easy_install.pyc
-rw-r--r--.  2 root root    315 Apr  1  2021 easy_install.pyo
drwxr-xr-x.  7 root root    276 Jan 11 12:11 kazoo
drwxr-xr-x.  2 root root     85 Jan 11 12:11 kazoo-2.7.0.dist-info
-rw-r--r--.  1 root root 141271 Jan 11 12:09 kazoo-2.7.0-py2.py3-none-any.whl
drwxr-xr-x. 10 root root   4096 Nov 20 18:02 pip
drwxr-xr-x.  2 root root    111 Nov 20 18:02 pip-9.0.3.dist-info
drwxr-xr-x.  4 root root    159 Feb  7  2023 pkg_resources
drwxr-xr-x.  5 root root   4096 Feb  7  2023 setuptools
drwxr-xr-x.  2 root root    174 Feb  7  2023 setuptools-39.0.1.dist-info
[root@dev site-packages]# cd kazoo
[root@dev kazoo]# ll
total 116
-rw-rw-r--. 1 root root 60984 Mar 13  2020 client.py
-rw-rw-r--. 1 root root  4344 Mar 13  2020 exceptions.py
drwxr-xr-x. 2 root root    97 Jan 11 12:11 handlers
-rw-rw-r--. 1 root root   949 Mar 13  2020 hosts.py
-rw-rw-r--. 1 root root    41 Mar 13  2020 __init__.py
-rw-rw-r--. 1 root root  6363 Mar 13  2020 interfaces.py
-rw-rw-r--. 1 root root    49 Mar 13  2020 loggingsupport.py
drwxr-xr-x. 2 root root   103 Jan 11 12:11 protocol
-rw-rw-r--. 1 root root  1801 Mar 13  2020 python2atexit.py
drwxr-xr-x. 2 root root   200 Jan 11 12:11 recipe
-rw-rw-r--. 1 root root  5701 Mar 13  2020 retry.py
-rw-rw-r--. 1 root root  4694 Mar 13  2020 security.py
drwxr-xr-x. 2 root root    60 Jan 11 12:11 testing
drwxr-xr-x. 2 root root  4096 Jan 11 12:11 tests
-rw-rw-r--. 1 root root    22 Mar 13  2020 version.py

```

2. pip 安装
```bash
pip install kazoo-2.7.0-py2.py3-none-any.whl
```




## 源码编译安装python12




yum install llvm llvm-devel

yum install epel-release


https://valgrind.org/downloads/?src=www.discoversdk.com

### 必须添加参数：--enable-shared






```bash
./configure --enable-universalsdk  --enable-shared --enable-profiling --enable-pystats --enable-optimizations --enable-loadable-sqlite-extensions --enable-ipv6  --with-system-expat --with-system-libmpdec --with-doc-strings --with-pymalloc --with-freelists --with-c-locale-coercion --with-valgrind  --with-ensurepip=upgrade --with-openssl=/usr --with-hash-algorithm=siphash13

make -j8


make install

```




# 安装新版gcc
# 使用CentOS7自带的gcc-4.8，如果添加--enable-optimizations则会编译失败
yum install centos-release-scl
yum install devtoolset-9-gcc devtoolset-9-gcc-c++ devtoolset-9-binutils
scl enable devtoolset-9 bash
# 安装构建工具链
yum install make -y

# 安装新版openssl，要求1.1.1以上。
# 此处安装3.2.1版本，https://www.openssl.org/source/openssl-3.2.1.tar.gz
./config --prefix=/opt/openssl-3.2.1 --openssldir=/opt/openssl-3.2.1
make -j4 # 开启并行编译，4线程，可根据自己的机器配置修改
make install
echo '/opt/openssl-3.2.1/lib64' > /etc/ld.so.conf.d/openssl-3.2.1.conf
ldconfig -v

# 安装新版libmpdec，要求2.5.0以上。
# 此处安装4.0.0版本，https://www.bytereef.org/software/mpdecimal/releases/mpdecimal-4.0.0.tar.gz
./configure --prefix=/opt/libmpdec-4.0.0
make -j4
make install
echo '/opt/libmpdec-4.0.0/lib' > /etc/ld.so.conf.d/libmpdec-4.0.0.conf
ldconfig -v

# 安装其它依赖
yum install -y epel-release
yum install -y gcc make openssl-devel zlib-devel bzip2-devel readline-devel sqlite-devel libffi-devel \
    expat-devel libmpdec-devel mpdecimal-devel uuid-devel xz-devel tk-devel editline-devel

# 开始编译Python3.12
# 手动指定openssl和libmpdec的路径
export CFLAGS="-I/opt/libmpdec-4.0.0/include -I/opt/openssl-3.2.1/include" 
export LDFLAGS="-L/opt/libmpdec-4.0.0/lib -L/opt/openssl-3.2.1/lib64"
export PKG_CONFIG_PATH="/opt/openssl-3.2.1/lib64/pkgconfig:/opt/libmpdec-4.0.0/lib/pkgconfig:/usr/lib64/pkgconfig:$PKG_CONFIG_PATH"
./configure \
    --enable-loadable-sqlite-extensions \
    --with-pkg-config=yes \
    --prefix=/opt/python-3.12.2 \
    --with-ensurepip=upgrade \
    --enable-optimizations \
    --with-lto=full \
    --with-computed-gotos \
    --enable-profiling \
    --with-strict-overflow \
    --enable-shared \
    --with-system-expat \
    --with-system-libmpdec \
    --with-openssl=/opt/openssl-3.2.1 \
    --with-openssl-rpath=auto
make -j4
make install
echo '/opt/python-3.12.2/lib' > /etc/ld.so.conf.d/python-3.12.2.conf
ldconfig -v

# 测试Python3.12，输出版本号，至此编译安装完成
/opt/python-3.12.2/bin/python3.12 --version








