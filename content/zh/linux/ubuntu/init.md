---
title: init
description: init
date: 2025-06-05
weight: 20000
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}


{{< /alert >}}

```bash
apt install -y build-essential lrzsz wget gcc cmake make bison libprotobuf-dev protobuf-compiler libaio-dev libtirpc-dev libncurses5-dev libssl-dev pkg-config tree plantuml vim openssh-server iproute2 libudev-dev libsasl2-dev

apt install -y slapd ldap-utils

vim /etc/ssh/sshd_config

service ssh start


```



## 编译: `mysql-8.0.32`

```bash
cmake ../ -DCMAKE_BUILD_TYPE=Debug -DWITH_BUILD_ID=1 -DENABLED_LOCAL_INFILE=1 -DMYSQL_TCP_PORT=8032 -DDEFAULT_CHARSET=utf8mb4 -DDEFAULT_COLLATION=utf8mb4_bin -DWITH_BOOST=../boost -DCMAKE_INSTALL_PREFIX=/data/mysql/app -DMYSQL_DATADIR=/data/mysql/data -DSYSCONFDIR=/data/mysql/conf -DTMPDIR=/data/mysql/tmp -DMYSQL_UNIX_ADDR=/data/mysql/tmp/mysql.sock -DMYSQLX_UNIX_ADDR=/data/mysql/tmp/mysqlx.sock -DWITH_INNOBASE_STORAGE_ENGINE=1 -DWITH_FEDERATED_STORAGE_ENGINE=1 -DWITH_BLACKHOLE_STORAGE_ENGINE=1 -DWITH_MYISAM_STORAGE_ENGINE=1 -DWITH_PERFSCHEMA_STORAGE_ENGINE=1 -DWITH_ARCHIVE_STORAGE_ENGINE=1 -DWITH_ICU=bundled -DWITH_LZ4=bundled -DWITH_LIBEVENT=bundled -DWITH_ZLIB=bundled -DWITH_SSL=system -DWITH_ZSTD=bundled

nohup make doxygen &



```







