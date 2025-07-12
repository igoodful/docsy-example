---
title: mysql-8.0.26
description: mysql-8.0.26
date: 2023-11-06
weight: 30000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{<note>}}
- mysql-8.0.26需要：boost_1_73_0.tar.gz
- mysql-8.0.32需要：boost_1_77_0.tar.gz
{{</note>}}

##
```bash
git clone https://github.com/mysql/mysql-server.git
cd mysql-server
git branch -r
git branch
git checkout 8.0
git pull
git checkout mysql-8.0.26
git log
```

#### WITH_SSL选项后缀不要加斜杠
> `-DWITH_SSL=/root/local/openssl-1.1.1w/`这样会报错，只能写成`-DWITH_SSL=/root/local/openssl-1.1.1w`
```bash
-DWITH_SSL=/root/local/openssl-1.1.1w
```



## mkdir

```bash
mkdir -p $HOME/local/mysql_8026/
mkdir -p $HOME/local/mysql_8026/{data,conf,tmp}
```


### cmake
需要指定ICU具体安装路径前缀，形如`-DWITH_ICU=/usr/local/`

#### debug
```bash
cmake3 ../mysql-server/ -DCMAKE_INSTALL_PREFIX=$HOME/local/mysql_8026 -DMYSQL_DATADIR=$HOME/local/mysql_8026/data -DWITH_BOOST=./boost -DDOWNLOAD_BOOST=1 -DSYSCONFDIR=$HOME/local/mysql_8026/conf -DTMPDIR=$HOME/local/mysql_8026/tmp -DMYSQLX_UNIX_ADDR=$HOME/local/mysql_8026/tmp -DWITH_ICU=bundled -DWITH_LZ4=bundled -DWITH_LZMA=bundled -DWITH_PROTOBUF=bundled -DWITH_SSL=system -DWITH_ZLIB=bundled -DWITH_ZSTD=bundled  -DENABLED_LOCAL_INFILE=1 -DENABLE_DOWNLOADS=1  -DMYSQL_TCP_PORT=8032 -DCMAKE_BUILD_TYPE=Debug  -DWITH_DEBUG=1  -DWITH_INNOBASE_STORAGE_ENGINE=1  -DWITH_FEDERATED_STORAGE_ENGINE=1 -DWITH_BLACKHOLE_STORAGE_ENGINE=1 -DWITH_MYISAM_STORAGE_ENGINE=1 -DWITH_PERFSCHEMA_STORAGE_ENGINE=1  -DENABLED_LOCAL_INFILE=1  -DDEFAULT_CHARSET=utf8mb4 -DDEFAULT_COLLATION=utf8mb4_0900_ai_ci -DFORCE_COLORED_OUTPUT=ON  --trace --trace-expand -DWITH_ICU=/usr/local/ > cmake3.out 2>cmake3
```

#### Release
```bash


```


### make
```bash
make VERBOSE=1 -j15 > make.out 2>make.verbose &
```

### make install

```bash
make install
```


## 初始化
```bash
/root/local/mysql_8026/bin/mysqld --defaults-file=/root/local/mysql_8026/conf/my.cnf --initialize

/root/local/mysql_8026/bin/mysqld_safe --defaults-file=/root/local/mysql_8026/conf/my.cnf &

```

### 查看初始化密码
```bash
grep pass /root/local/mysql_8026/log/error.log
```
输出：`2024-01-14T16:34:03.689707+08:00 6 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: kx/O5(2R0Dgg`

### 修改密码并创建用户
`/root/local/mysql_8026/bin/mysql -uroot -P8026 -S ./tmp/mysql.sock -p'kx/O5(2R0Dgg'`
```sql
alter user 'root'@'localhost' identified by 'root';

create user 'root'@'127.0.0.1' identified by 'root';
grant all on *.* to 'root'@'127.0.0.1' with grant option;

create user 'root'@'::1' identified by 'root';
grant all on *.* to 'root'@'::1' with grant option;

create user 'root'@'%' identified by 'root';
grant all on *.* to 'root'@'%' with grant option;
```

### 配置文件
```yaml
[root@dev mysql_8026]# cat conf/my.cnf
[mysqld]
plugin-load-add=mysql_clone.so
basedir = /root/local/mysql_8026
datadir = /root/local/mysql_8026/data
tmpdir = /root/local/mysql_8026/tmp
pid_file = /root/local/mysql_8026/tmp/mysqld.pid
socket = /root/local/mysql_8026/tmp/mysql.sock
log_bin = /root/local/mysql_8026/log/bin
relay_log = /root/local/mysql_8026/log/relay
log_error = /root/local/mysql_8026/log/error.log
slow_query_log_file = /root/local/mysql_8026/log/slow.log
general_log_file = /root/local/mysql_8026/log/general.log
port = 8026
mysqlx = 0
user = root
server_id = 1024
back_log = 5000
binlog_format = row
binlog_ignore_db = dbscale_tmp
binlog_transaction_dependency_tracking = WRITESET
character_set_server = utf8mb4
default_authentication_plugin = mysql_native_password
enforce_gtid_consistency = on
expire_logs_days = 15
federated = 1
gtid_mode = on
init_connect = SET NAMES utf8mb4
innodb_buffer_pool_instances = 10
innodb_buffer_pool_size = 1G
innodb_change_buffering = all
innodb_doublewrite = 1
innodb_file_per_table = 1
innodb_flush_log_at_trx_commit = 1
innodb_flush_method = O_DIRECT
innodb_io_capacity = 8000
innodb_io_capacity_max = 10000
innodb_log_buffer_size = 128M
innodb_log_files_in_group = 4
innodb_log_file_size = 1G
innodb_monitor_enable = all
innodb_print_all_deadlocks = on
innodb_read_io_threads = 4
innodb_temp_data_file_path = ibtmp1:64M:autoextend:max:81920M
innodb_thread_concurrency = 0
innodb_write_io_threads = 4
interactive_timeout = 28800
log_bin_trust_function_creators = 1
log_slave_updates = 1
log_timestamps = SYSTEM
long_query_time = 10
lower_case_table_names = 0
master_info_repository = TABLE
max_allowed_packet = 16M
max_connections = 20480
max_prepared_stmt_count = 1048576
net_read_timeout = 10000
net_write_timeout = 10000
open_files_limit = 800000
performance-schema-consumer-events_stages_current = on
performance-schema-consumer-events_stages_history = on
performance-schema-consumer-events_statements_current = on
performance-schema-consumer-events_statements_history = on
performance-schema-consumer-events_transactions_current = on
performance-schema-consumer-events_transactions_history = on
performance-schema-consumer-events_waits_current = on
performance-schema-consumer-events_waits_history = on
performance-schema-instrument = %=ON
plugin-load = rpl_semi_sync_master=semisync_master.so;rpl_semi_sync_slave=semisync_slave.so
relay_log_info_repository = TABLE
skip_external_locking = 1
skip_name_resolve = 1
skip_slave_start = 1
slave_parallel_type = LOGICAL_CLOCK
slave_parallel_workers = 16
slave_pending_jobs_size_max = 2147483648
slave_preserve_commit_order = 1
slave_rows_search_algorithms = INDEX_SCAN,HASH_SCAN
slow_query_log = on
sort_buffer_size = 2M
ssl = off
sync_binlog = 1
table_definition_cache = 5000
table_open_cache = 5000
thread_cache_size = 3000
transaction_isolation = READ-COMMITTED
transaction_write_set_extraction = XXHASH64
wait_timeout = 28800

```



{{< alert >}}
[gcc,make,cmake](https://zhuanlan.zhihu.com/p/342151242)

{{< /alert >}}


## xxx与xxx-devel的区别

### 举例说明
> openssl
- openssl-1.0.2k-26.el7_9.x86_64.rpm 执行码部分
- openssl-devel-1.0.2k-26.el7_9.x86_64.rpm 这个就是包含了头文件，头文件参考，某些库文件等跟开发相关的东西。
- 对openssl进行源码编译安装后得到的东西就是openssl-1.0.2k-26.el7_9.x86_64.rpm和openssl-devel-1.0.2k-26.el7_9.x86_64.rpm一起的内容

## 查看mysql依赖的所有动态库

```bash
2025-03-22T13:02:03 [root@x86centos7-101 /data/app/mysql-8.0.26/bin] # ls|xargs ldd > /tmp/a.log
2025-03-22T13:02:31 [root@x86centos7-101 /data/app/mysql-8.0.26/bin] # grep '\.so' /tmp/a.log  | sort -nr|uniq |awk '{print $1}' |uniq
linux-vdso.so.1
libtinfo.so.5
libstdc++.so.6
libssl.so.1.1
librt.so.1
libresolv.so.2
libpthread.so.0
libprotobuf-lite.so.3.11.4
libnuma.so.1
libncurses.so.5
libm.so.6
libgcc_s.so.1
libdl.so.2
libc.so.6
libcrypto.so.1.1
libaio.so.1
/lib64/ld-linux-x86-64.so.2
2025-03-22T13:02:32 [root@x86centos7-101 /data/app/mysql-8.0.26/bin] # 

```


在你提供的输出中，`mysql` 依赖的动态库（`.so` 文件）及其功能如下。这些库是 MySQL 8.0.26 运行时所需的核心依赖库，每个库都有其特定的功能：

---

### 1. **linux-vdso.so.1**
   - **功能**: 虚拟动态共享对象（Virtual Dynamic Shared Object），由内核提供，用于加速系统调用的执行。它是一个虚拟库，不直接对应磁盘上的文件。
   - **用途**: 提高系统调用的性能。

---

### 2. **libtinfo.so.5**
   - **功能**: 终端信息库（Terminal Information Library），是 `ncurses` 库的一部分，用于处理终端控制功能，如光标移动、屏幕清除等。
   - **用途**: MySQL 命令行工具（如 `mysql` 客户端）依赖此库来处理终端交互。

---

### 3. **libstdc++.so.6**
   - **功能**: GNU C++ 标准库（GNU Standard C++ Library），提供了 C++ 程序运行时的支持，包括 STL（标准模板库）和异常处理等。
   - **用途**: MySQL 是用 C++ 编写的，因此需要此库来支持 C++ 运行时功能。

---

### 4. **libssl.so.1.1**
   - **功能**: OpenSSL 的 SSL/TLS 库，提供了加密通信、证书管理和密钥交换等功能。
   - **用途**: MySQL 使用此库来支持 SSL/TLS 加密连接，确保数据传输的安全性。

---

### 5. **librt.so.1**
   - **功能**: 实时扩展库（Real-Time Extensions Library），提供了实时时钟和定时器功能。
   - **用途**: MySQL 使用此库来实现高精度计时和定时任务。

---

### 6. **libresolv.so.2**
   - **功能**: 解析库（Resolver Library），用于 DNS 解析和域名查询。
   - **用途**: MySQL 使用此库来解析主机名，例如在配置主从复制或连接远程数据库时。

---

### 7. **libpthread.so.0**
   - **功能**: POSIX 线程库（POSIX Threads Library），提供了多线程编程的支持。
   - **用途**: MySQL 是多线程的数据库管理系统，依赖此库来实现并发处理。

---

### 8. **libprotobuf-lite.so.3.11.4**
   - **功能**: Protocol Buffers 的轻量级运行时库（Google 的数据序列化库），用于高效的数据交换。
   - **用途**: MySQL 8.0 使用 Protocol Buffers 来实现内部通信和数据序列化。

---

### 9. **libnuma.so.1**
   - **功能**: NUMA 支持库（Non-Uniform Memory Access Library），用于优化多处理器系统中的内存访问。
   - **用途**: MySQL 在高性能服务器上运行时，可以利用 NUMA 优化内存分配和访问。

---

### 10. **libncurses.so.5**
   - **功能**: 终端控制库（Terminal Control Library），用于处理文本界面的显示和控制。
   - **用途**: MySQL 命令行工具（如 `mysql` 客户端）依赖此库来实现文本界面的交互。

---

### 11. **libm.so.6**
   - **功能**: 数学库（Math Library），提供了数学函数的实现，如三角函数、对数函数等。
   - **用途**: MySQL 在计算和数据处理中会用到这些数学函数。

---

### 12. **libgcc_s.so.1**
   - **功能**: GCC 运行时库（GCC Runtime Library），提供了 C++ 异常处理和底层运行时支持。
   - **用途**: MySQL 依赖此库来处理 C++ 异常和运行时功能。

---

### 13. **libdl.so.2**
   - **功能**: 动态加载库（Dynamic Linking Library），提供了动态加载共享库的函数，如 `dlopen` 和 `dlsym`。
   - **用途**: MySQL 使用此库在运行时动态加载插件或扩展。

---

### 14. **libc.so.6**
   - **功能**: GNU C 库（GNU C Library），提供了标准 C 函数的实现，如内存管理、字符串操作、文件 I/O 等。
   - **用途**: MySQL 的核心功能依赖此库，因为它是大多数 Linux 程序的基础库。

---

### 15. **libcrypto.so.1.1**
   - **功能**: OpenSSL 的加密库，提供了各种加密算法的实现，如 AES、RSA、SHA 等。
   - **用途**: MySQL 使用此库来实现数据加密、解密和哈希计算。

---

### 16. **libaio.so.1**
   - **功能**: 异步 I/O 库（Asynchronous I/O Library），提供了异步文件 I/O 操作的支持。
   - **用途**: MySQL 在高性能场景下使用异步 I/O 来提高磁盘操作的效率。

---

### 17. **/lib64/ld-linux-x86-64.so.2**
   - **功能**: 动态链接器（Dynamic Linker），负责在程序启动时加载和链接所需的共享库。
   - **用途**: MySQL 启动时依赖此链接器来加载所有必要的共享库。

---

### 总结
MySQL 8.0.26 依赖的这些动态库涵盖了以下核心功能：
- **加密与安全**: `libssl.so.1.1` 和 `libcrypto.so.1.1` 提供了 SSL/TLS 加密和加密算法支持。
- **多线程与并发**: `libpthread.so.0` 提供了多线程支持。
- **终端交互**: `libtinfo.so.5` 和 `libncurses.so.5` 用于命令行工具的用户界面。
- **异步 I/O**: `libaio.so.1` 提高了磁盘 I/O 性能。
- **C++ 运行时**: `libstdc++.so.6` 和 `libgcc_s.so.1` 提供了 C++ 运行时支持。
- **系统基础功能**: `libc.so.6`、`libdl.so.2`、`librt.so.1` 等提供了系统调用的基础支持。

这些库共同确保了 MySQL 的正常运行和高性能表现。





