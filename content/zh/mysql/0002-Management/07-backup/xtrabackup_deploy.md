---
title: xtrabackup 编译安装
description: 源码编译安装
date: 2023-11-07
weight: 20000


---

{{< alert >}}

1. 最快（逻辑备份中）

2. 适用于数据量大于50G

3. 支持where

4. 有TFWRL锁

{{< /alert >}}



## 环境
{{<alert>}}
| 硬件名称       | 配置          |
|:---------------|:--------------|
| CPU            | 4C            |
| Memory         | 8G            |
| Space          | 200G (SSD)    |
| MySQL Version  | 8.0.26        |
| Arch           | x64           |
| OS             | CentOS 7.6    |
| Kernel Version | 5.4.257-1.el7 |
| Hugepagesize   | 2048 KB       |


{{</alert>}}




```sql
sudo yum install cmake openssl-devel libaio libaio-devel automake autoconf \
bison libtool ncurses-devel libgcrypt-devel libev-devel libcurl-devel zlib-devel \
bibudev-dev \
zstd vim-common procps-ng-devel

sudo yum install python3-sphinx
```


### install

```sql
cd percona-xtrabackup
mkdir build
cd build
cmake -DWITH_BOOST=PATH-TO-BOOST-LIBRARY -DDOWNLOAD_BOOST=ON -DBUILD_CONFIG=xtrabackup_release -DWITH_MAN_PAGES=OFF -B ..
make -j$(nproc --all)
make DESTDIR=<your_dir> install
```
> 默认安装位置：`/usr/local/xtrabackup`
























