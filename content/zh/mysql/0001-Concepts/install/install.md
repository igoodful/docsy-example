---
title: 源码编译安装
description: 源码编译安装
date: 2023-11-07
weight: 20000


---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



## 环境
{{<alert>}}

| 硬件名称           | 配置          |
| :----------------- | :------------ |
| Xtrabackup Version | 8.0.26-18     |
| MySQL Version      | 8.0.26        |
| CPU                | 4C            |
| Memory             | 8G            |
| Space              | 200G (SSD)    |
| Arch               | x64           |
| OS                 | CentOS 7.6    |
| Kernel Version     | 5.4.257-1.el7 |
| Hugepagesize       | 2048 KB       |

{{</alert>}}


## 安装依赖

{{<caution>}}
<!---->
1. cmake，下载地址：[http://www.cmake.org](http://www.cmake.org)
2. make 3.75+，下载地址：[http://www.gnu.org/software/make/](http://www.gnu.org/software/make/)
3. gcc 10+，下载地址：[http://www.gnu.org/software/make/](http://www.gnu.org/software/make/)
4. openssl  1.0.1+，下载地址：[http://www.openssl.org](http://www.openssl.org)，`openssl version`
5. ncurses，
6. perl，
7. Boost C++
8. bison 2.1+，下载地址：[http://www.gnu.org/software/bison/](http://www.gnu.org/software/bison/)
9. m4，下载地址：[http://www.gnu.org/software/m4/](http://www.gnu.org/software/m4/)，仅仅Solaris Express操作系统需要
10. git，linux系统一般都存在
11. tar，linux系统和mac系统一般都存在
{{</caution>}}


### glic 版本

GNU C库（glibc）是GNU系统的标准C库。它是GNU系统的主要库，GNU/Linux系统的大多数程序都使用它。它提供了分配内存、搜索目录、打开和关闭文件、读写文件、字符串处理、模式匹配、算术等基本程序。

当你在Linux上安装一些软件时，你可能会遇到以下错误。
```sql
./configure: /lib64/libc.so.6: version `GLIBC_2.14' not found (required by ./configure)
```
{{<caution>}}
<!---->
yum安装只能安装到2.17版本：`yum -y install glibc`

- glibc下载地址：[https://ftp.gnu.org/gnu/glibc/](https://ftp.gnu.org/gnu/glibc/)

- 查看glibc版本：`ldd --version` 或 `getconf GNU_LIBC_VERSION`

- 获取libc.so库的位置：`ldd $(which top) | grep "libc.so"`

- 查看libc.so有哪些版本：strings /lib64/libc.so.6 |grep ^GLIBC
{{</caution>}}

```sql
[root@k8s-node-77 ~]# getconf GNU_LIBC_VERSION
glibc 2.17

[root@k8s-node-77 ~]#  ldd --version
ldd (GNU libc) 2.17
Copyright (C) 2012 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
Written by Roland McGrath and Ulrich Drepper.

[root@k8s-node-77 ~]# ldd $(which top) | grep "libc.so"
	libc.so.6 => /lib64/libc.so.6 (0x00007f5af4091000)

[root@k8s-node-77 ~]# ll /lib64/libc.so.6
lrwxrwxrwx. 1 root root 12 Aug 19  2022 /lib64/libc.so.6 -> libc-2.17.so

[root@k8s-node-77 ~]# strings /lib64/libc.so.6 |grep ^GLIBC
GLIBC_2.2.5
GLIBC_2.2.6
GLIBC_2.3
GLIBC_2.3.2
GLIBC_2.3.3
GLIBC_2.3.4
GLIBC_2.4
GLIBC_2.5
GLIBC_2.6
GLIBC_2.7
GLIBC_2.8
GLIBC_2.9
GLIBC_2.10
GLIBC_2.11
GLIBC_2.12
GLIBC_2.13
GLIBC_2.14
GLIBC_2.15
GLIBC_2.16
GLIBC_2.17
GLIBC_PRIVATE
GLIBC_2.8
GLIBC_2.5
GLIBC_2.9
GLIBC_2.7
GLIBC_2.6
GLIBC_2.11
GLIBC_2.16
GLIBC_2.10
GLIBC_2.17
GLIBC_2.13
GLIBC_2.2.6



[wangjingyu@48 ~]$  ldd --version
ldd (GNU libc) 2.18
Copyright (C) 2013 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
Written by Roland McGrath and Ulrich Drepper.

```




安装依赖包
```sql
yum install openssl-devel libaio libaio-devel automake autoconf \
bison libtool ncurses-devel libgcrypt-devel libev-devel libcurl-devel zlib-devel \
vim-common libcurl-devel libudev-devel

```

安装编译环境

开始编译


执行make



执行make install






### 先源码安装：openssl

### 再源码安装：gcc

### 源码安装：cmake


```sql

Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : libgpg-error-devel-1.12-3.el7.x86_64                                                                                                                                                                                                                                  1/2
  Installing : libgcrypt-devel-1.5.3-14.el7.x86_64                                                                                                                                                                                                                                   2/2
  Verifying  : libgpg-error-devel-1.12-3.el7.x86_64                                                                                                                                                                                                                                  1/2
  Verifying  : libgcrypt-devel-1.5.3-14.el7.x86_64                                                                                                                                                                                                                                   2/2

Installed:
  libgcrypt-devel.x86_64 0:1.5.3-14.el7

Dependency Installed:
  libgpg-error-devel.x86_64 0:1.12-3.el7

Complete!
[root@k8s-node-76 bin]# libgcrypt-config
Usage: /usr/bin/libgcrypt-config [OPTIONS]
Options:
	[--prefix]
	[--exec-prefix]
	[--version]
        [--api-version]
	[--libs]
	[--cflags]
	[--algorithms]
        [--host]
[root@k8s-node-76 bin]# which libgcrypt-config
/usr/bin/libgcrypt-config
[root@k8s-node-76 bin]# ldd /usr/bin/libgcrypt-config
	not a dynamic executable
[root@k8s-node-76 bin]# ll /usr/bin/libgcrypt-config
-rwxr-xr-x 1 root root 3866 Aug  2  2017 /usr/bin/libgcrypt-config
[root@k8s-node-76 bin]# less /usr/bin/libgcrypt-config
[root@k8s-node-76 bin]#

```



## 安装目录


| 名称                | 功能         | 备注 |
| :------------------ | :----------- | :--- |
| mysqld              | 启动服务     |      |
| mysqld_safe         | 脚本启动服务 |      |
| mysqld_multi        | 脚本启动服务 |      |
| mysql               | 客户端       |      |
| mysqladmin          | 管理         |      |
| mysqlbinlog         | 解析binlog   |      |
| mysqldump           | 逻辑备份     |      |
| mysqldumpslow       | 慢日志分析   |      |
| mysqlslap           | 压测         |      |
| mysql_config_editor | 登录密码     |      |


#### 1. bin
```bash
2024-07-11T20:21:02 [root@dev /root/local/mysql_8032] $ tree bin
bin
├── comp_err
├── ibd2sdi
├── innochecksum
├── lz4_decompress
├── myisamchk
├── myisam_ftdump
├── myisamlog
├── myisampack
├── my_print_defaults
├── mysql
├── mysqladmin
├── mysqlbinlog
├── mysqlcheck
├── mysql_client_test
├── mysql_config
├── mysql_config_editor
├── mysqld
├── mysqld_multi
├── mysqld_safe
├── mysqldump
├── mysqldumpslow
├── mysqlimport
├── mysql_keyring_encryption_test
├── mysql_migrate_keyring
├── mysqlpump
├── mysqlrouter
├── mysqlrouter_keyring
├── mysqlrouter_passwd
├── mysqlrouter_plugin_info
├── mysql_secure_installation
├── mysqlshow
├── mysqlslap
├── mysql_ssl_rsa_setup
├── mysqltest
├── mysqltest_safe_process
├── mysql_tzinfo_to_sql
├── mysql_upgrade
├── mysqlxtest
├── perror
└── zlib_decompress
```

#### 2. include
```bash
2024-07-11T20:26:47 [root@dev /root/local/mysql_8032] $ tree include
include
├── errmsg.h
├── field_types.h
├── my_command.h
├── my_compress.h
├── my_list.h
├── mysql
│   ├── client_plugin.h
│   ├── plugin_auth_common.h
│   └── udf_registration_types.h
├── mysql_com.h
├── mysqld_error.h
├── mysql.h
├── mysql_time.h
├── mysql_version.h
├── mysqlx_ername.h
├── mysqlx_error.h
└── mysqlx_version.h

```

#### 3. lib
```bash
1 directory, 16 files
2024-07-11T20:26:53 [root@dev /root/local/mysql_8032] $ tree lib
lib
├── libmysqlclient.a
├── libmysqlclient.so -> libmysqlclient.so.21
├── libmysqlclient.so.21 -> libmysqlclient.so.21.2.32
├── libmysqlclient.so.21.2.32
├── libmysqlservices.a
├── mysqlrouter
│   ├── connection_pool.so
│   ├── destination_status.so
│   ├── http_auth_backend.so
│   ├── http_auth_realm.so
│   ├── http_server.so
│   ├── io.so
│   ├── keepalive.so
│   ├── metadata_cache.so
│   ├── private
│   │   ├── libmysqlharness.so.1
│   │   ├── libmysqlharness_stdx.so.1
│   │   ├── libmysqlharness_tls.so.1
│   │   ├── libmysqlrouter_connection_pool.so.1
│   │   ├── libmysqlrouter_destination_status.so.1
│   │   ├── libmysqlrouter_http_auth_backend.so.1
│   │   ├── libmysqlrouter_http_auth_realm.so.1
│   │   ├── libmysqlrouter_http.so.1
│   │   ├── libmysqlrouter_io_component.so.1
│   │   ├── libmysqlrouter_metadata_cache.so.1
│   │   ├── libmysqlrouter_mysqlxmessages.so.1
│   │   ├── libmysqlrouter_routing.so.1
│   │   ├── libmysqlrouter.so.1
│   │   └── libprotobuf-lite.so.3.19.4
│   ├── rest_api.so
│   ├── rest_connection_pool.so
│   ├── rest_metadata_cache.so
│   ├── rest_router.so
│   ├── rest_routing.so
│   ├── router_openssl.so
│   ├── router_protobuf.so
│   └── routing.so
├── pkgconfig
│   └── mysqlclient.pc
├── plugin
│   ├── adt_null.so
│   ├── auth.so
│   ├── auth_socket.so
│   ├── auth_test_plugin.so
│   ├── component_audit_api_message_emit.so
│   ├── component_example_component1.so
│   ├── component_example_component2.so
│   ├── component_example_component3.so
│   ├── component_keyring_file.so
│   ├── component_log_filter_dragnet.so
│   ├── component_log_sink_json.so
│   ├── component_log_sink_syseventlog.so
│   ├── component_log_sink_test.so
│   ├── component_mysqlbackup.so
│   ├── component_mysqlx_global_reset.so
│   ├── component_pfs_example_component_population.so
│   ├── component_pfs_example.so
│   ├── component_query_attributes.so
│   ├── component_reference_cache.so
│   ├── component_test_audit_api_message.so
│   ├── component_test_backup_lock_service.so
│   ├── component_test_component_deinit.so
│   ├── component_test_host_application_signal.so
│   ├── component_test_mysql_command_services.so
│   ├── component_test_mysql_current_thread_reader.so
│   ├── component_test_mysql_runtime_error.so
│   ├── component_test_mysql_system_variable_set.so
│   ├── component_test_pfs_notification.so
│   ├── component_test_pfs_resource_group.so
│   ├── component_test_sensitive_system_variables.so
│   ├── component_test_status_var_reader.so
│   ├── component_test_status_var_service_int.so
│   ├── component_test_status_var_service_reg_only.so
│   ├── component_test_status_var_service.so
│   ├── component_test_status_var_service_str.so
│   ├── component_test_status_var_service_unreg_only.so
│   ├── component_test_string_service_charset.so
│   ├── component_test_string_service_long.so
│   ├── component_test_string_service.so
│   ├── component_test_system_variable_source.so
│   ├── component_test_sys_var_service_int.so
│   ├── component_test_sys_var_service_same.so
│   ├── component_test_sys_var_service.so
│   ├── component_test_sys_var_service_str.so
│   ├── component_test_table_access.so
│   ├── component_test_udf_registration.so
│   ├── component_test_udf_services.so
│   ├── component_udf_reg_3_func.so
│   ├── component_udf_reg_avg_func.so
│   ├── component_udf_reg_int_func.so
│   ├── component_udf_reg_int_same_func.so
│   ├── component_udf_reg_only_3_func.so
│   ├── component_udf_reg_real_func.so
│   ├── component_udf_unreg_3_func.so
│   ├── component_udf_unreg_int_func.so
│   ├── component_udf_unreg_real_func.so
│   ├── component_validate_password.so
│   ├── conflicting_variables.so
│   ├── connection_control.so
│   ├── daemon_example.ini
│   ├── ddl_rewriter.so
│   ├── group_replication.so
│   ├── ha_example.so
│   ├── ha_mock.so
│   ├── keyring_file.so
│   ├── keyring_udf.so
│   ├── libdaemon_example.so
│   ├── libtest_framework.so
│   ├── libtest_services.so
│   ├── libtest_services_threaded.so
│   ├── libtest_session_attach.so
│   ├── libtest_session_detach.so
│   ├── libtest_session_info.so
│   ├── libtest_session_in_thd.so
│   ├── libtest_sql_2_sessions.so
│   ├── libtest_sql_all_col_types.so
│   ├── libtest_sql_cmds_1.so
│   ├── libtest_sql_commit.so
│   ├── libtest_sql_complex.so
│   ├── libtest_sql_errors.so
│   ├── libtest_sql_lock.so
│   ├── libtest_sql_processlist.so
│   ├── libtest_sql_replication.so
│   ├── libtest_sql_reset_connection.so
│   ├── libtest_sql_shutdown.so
│   ├── libtest_sql_sleep_is_connected.so
│   ├── libtest_sql_sqlmode.so
│   ├── libtest_sql_stmt.so
│   ├── libtest_sql_stored_procedures_functions.so
│   ├── libtest_sql_views_triggers.so
│   ├── libtest_x_sessions_deinit.so
│   ├── libtest_x_sessions_init.so
│   ├── locking_service.so
│   ├── mypluglib.so
│   ├── mysql_clone.so
│   ├── mysql_no_login.so
│   ├── pfs_example_plugin_employee.so
│   ├── qa_auth_client.so
│   ├── qa_auth_interface.so
│   ├── qa_auth_server.so
│   ├── replication_observers_example_plugin.so
│   ├── rewrite_example.so
│   ├── rewriter.so
│   ├── semisync_master.so
│   ├── semisync_replica.so
│   ├── semisync_slave.so
│   ├── semisync_source.so
│   ├── test_security_context.so
│   ├── test_services_command_services.so
│   ├── test_services_host_application_signal.so
│   ├── test_services_plugin_registry.so
│   ├── test_udf_services.so
│   ├── udf_example.so
│   ├── validate_password.so
│   └── version_token.so
└── private
    ├── libprotobuf-lite.so.3.19.4
    └── libprotobuf.so.3.19.4

5 directories, 153 files
2024-07-11T20:27:00 [root@dev /root/local/mysql_8032] $
```
















