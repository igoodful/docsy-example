---
title: libtirpc
description: libtirpc
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

[libtirpc](https://www.linuxfromscratch.org/blfs/view/svn/basicnet/libtirpc.html)
[libtirpc download](https://sourceforge.net/projects/libtirpc/)

{{</note>}}


## 1. 依赖

```viz-dot
digraph "libtirpc" {
node [
        fontsize = "12"
];
     rankdir="LR";

    "libtirpc" [ label = "libtirpc", shape = octagon ];
    "glibc" [ label = "glibc", shape = octagon ];
    "libselinux" [ label = "libselinux", shape = octagon ];
    "krb5-libs" [ label = "krb5-libs", shape = octagon ];
    "libcom_err" [ label = "libcom_err", shape = octagon ];
    "keyutils-libs" [ label = "keyutils-libs", shape = octagon ];
    "pcre" [ label = "pcre", shape = octagon ];
    "libc.so.6" [ label = "libc.so.6", shape = octagon ];
    "libpthread.so.0" [ label = "libpthread.so.0", shape = octagon ];
    "libdl.so.2" [ label = "libdl.so.2", shape = octagon ];
    "/lib64/ld-linux-x86-64.so.2" [ label = "/lib64/ld-linux-x86-64.so.2", shape = octagon ];
    "libpcre.so.1" [ label = "libpcre.so.1", shape = octagon ];
    "libselinux.so.1" [ label = "libselinux.so.1", shape = octagon ];
    "libcom_err.so.2" [ label = "libcom_err.so.2", shape = octagon ];
    "libkeyutils.so.1" [ label = "libkeyutils.so.1", shape = octagon ];
    "libgssapi_krb5.so.2" [ label = "libgssapi_krb5.so.2", shape = octagon ];
    "libkrb5.so.3" [ label = "libkrb5.so.3", shape = octagon ];
    "libk5crypto.so.3" [ label = "libk5crypto.so.3", shape = octagon ];
    "libkrb5support.so.0" [ label = "libkrb5support.so.0", shape = octagon ];


    "libtirpc" -> "glibc";
    "libtirpc" -> "libselinux";
    "libtirpc" -> "krb5-libs";
    "libtirpc" -> "libcom_err";
    "libtirpc" -> "keyutils-libs";
    "libtirpc" -> "pcre";

    "glibc" -> "libc.so.6";
    "glibc" -> "libpthread.so.0";
    "glibc" -> "libdl.so.2";
    "glibc" -> "/lib64/ld-linux-x86-64.so.2";
    "pcre" -> "libpcre.so.1";
    "libselinux" -> "libselinux.so.1";
    "libcom_err" -> "libcom_err.so.2";
    "keyutils-libs" -> "libkeyutils.so.1";
    "krb5-libs" -> "libgssapi_krb5.so.2";
    "krb5-libs" -> "libkrb5.so.3";
    "krb5-libs" -> "libk5crypto.so.3";
    "krb5-libs" -> "libkrb5support.so.0";
}
```

```bash
2025-03-24T21:03:39 [mysql@RDS-146 /glc/libtirpc/app/libtirpc-1.3.4/lib] $ ll
total 2788
-rw-r--r-- 1 mysql mysql 1879304 2025-03-24T20:59:10 libtirpc.a
-rwxr-xr-x 1 mysql mysql     997 2025-03-24T20:59:10 libtirpc.la
lrwxrwxrwx 1 mysql mysql      17 2025-03-24T20:59:10 libtirpc.so -> libtirpc.so.3.0.0
lrwxrwxrwx 1 mysql mysql      17 2025-03-24T20:59:10 libtirpc.so.3 -> libtirpc.so.3.0.0
-rwxr-xr-x 1 mysql mysql  963016 2025-03-24T20:59:10 libtirpc.so.3.0.0
drwxrwxr-x 2 mysql mysql    4096 2025-03-24T20:59:11 pkgconfig
2025-03-24T21:03:40 [mysql@RDS-146 /glc/libtirpc/app/libtirpc-1.3.4/lib] $ ldd libtirpc.so.3.0.0
	linux-vdso.so.1 =>  (0x00007ffc241f3000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007fddead79000)
	libgssapi_krb5.so.2 => /lib64/libgssapi_krb5.so.2 (0x00007fddeab2c000)
	libkrb5.so.3 => /lib64/libkrb5.so.3 (0x00007fddea843000)
	libk5crypto.so.3 => /lib64/libk5crypto.so.3 (0x00007fddea610000)
	libcom_err.so.2 => /lib64/libcom_err.so.2 (0x00007fddea40c000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fddea03e000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fddeb1c3000)
	libkrb5support.so.0 => /lib64/libkrb5support.so.0 (0x00007fdde9e2e000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007fdde9c2a000)
	libkeyutils.so.1 => /lib64/libkeyutils.so.1 (0x00007fdde9a26000)
	libresolv.so.2 => /lib64/libresolv.so.2 (0x00007fdde980c000)
	libselinux.so.1 => /lib64/libselinux.so.1 (0x00007fdde95e5000)
	libpcre.so.1 => /lib64/libpcre.so.1 (0x00007fdde9383000)
2025-03-24T21:03:47 [mysql@RDS-146 /glc/libtirpc/app/libtirpc-1.3.4/lib] $

```



## 2. 内容


### 2.1 bin

```viz-dot
digraph "libtirpc" {
node [
        fontsize = "12"
];
    "libtirpc" [ label = "libtirpc", shape = octagon ];
    "autoconf" [ label = "autoconf", shape = octagon ];
    "autoheader" [ label = "autoheader", shape = octagon ];
    "autom4te" [ label = "autom4te", shape = octagon ];
    "autoreconf" [ label = "autoreconf", shape = octagon ];
    "autoscan" [ label = "autoscan", shape = octagon ];
    "autoupdate" [ label = "autoupdate", shape = octagon ];
    "ifnames" [ label = "ifnames", shape = octagon ];

    "libtirpc" -> "autoconf";
    "libtirpc" -> "autoheader";
    "libtirpc" -> "autom4te";
    "libtirpc" -> "autoreconf";
    "libtirpc" -> "acl/libtirpc.h";
    "libtirpc" -> "autoupdate";
    "libtirpc" -> "ifnames";
    "libtirpc" -> "autoscan";
}
```




## 3. 安装


#### 3.1 yum安装





#### 3.2 源码安装


```bash
wget https://downloads.sourceforge.net/libtirpc/libtirpc-1.3.4.tar.bz2

tar -xjvf libtirpc-1.3.4.tar.bz2 && cd libtirpc-1.3.4

./configure --prefix=$HOME/local --sysconfdir=$HOME/local --disable-static --disable-gssapi && make && make install

```



```bash
2025-03-24T21:02:30 [mysql@RDS-146 /glc/libtirpc/app/libtirpc-1.3.4] $ ll
total 16
drwxrwxr-x 2 mysql mysql 4096 2025-03-24T20:59:11 etc
drwxrwxr-x 3 mysql mysql 4096 2025-03-24T20:59:11 include
drwxrwxr-x 3 mysql mysql 4096 2025-03-24T20:59:11 lib
drwxrwxr-x 3 mysql mysql 4096 2025-03-24T20:59:11 share
2025-03-24T21:02:32 [mysql@RDS-146 /glc/libtirpc/app/libtirpc-1.3.4] $ tree lib etc include
lib
├── libtirpc.a
├── libtirpc.la
├── libtirpc.so -> libtirpc.so.3.0.0
├── libtirpc.so.3 -> libtirpc.so.3.0.0
├── libtirpc.so.3.0.0
└── pkgconfig
    └── libtirpc.pc
etc
├── bindresvport.blacklist
└── netconfig
include
└── tirpc
    ├── netconfig.h
    ├── rpc
    │   ├── auth_des.h
    │   ├── auth_gss.h
    │   ├── auth.h
    │   ├── auth_unix.h
    │   ├── clnt.h
    │   ├── clnt_soc.h
    │   ├── clnt_stat.h
    │   ├── des_crypt.h
    │   ├── des.h
    │   ├── key_prot.h
    │   ├── nettype.h
    │   ├── pmap_clnt.h
    │   ├── pmap_prot.h
    │   ├── pmap_rmt.h
    │   ├── raw.h
    │   ├── rpcb_clnt.h
    │   ├── rpcb_prot.h
    │   ├── rpcb_prot.x
    │   ├── rpc_com.h
    │   ├── rpcent.h
    │   ├── rpc.h
    │   ├── rpc_msg.h
    │   ├── rpcsec_gss.h
    │   ├── svc_auth_gss.h
    │   ├── svc_auth.h
    │   ├── svc_dg.h
    │   ├── svc.h
    │   ├── svc_mt.h
    │   ├── svc_soc.h
    │   ├── types.h
    │   └── xdr.h
    └── rpcsvc
        ├── crypt.h
        └── crypt.x

4 directories, 42 files
2025-03-24T21:02:46 [mysql@RDS-146 /glc/libtirpc/app/libtirpc-1.3.4] $


```






























### 编译：debug
```bash
cmake . -DCMAKE_INSTALL_PREFIX=/usr/local/mysql -DMYSQL_DATADIR=/usr/local/mysql/data -DDOWNLOAD_BOOST=1 -DWITH_BOOST=/usr/local/mysql-8.0.12/ -DSYSCONFDIR=/etc -DWITH_INNOBASE_STORAGE_ENGINE=1 -DWITH_PARTITION_STORAGE_ENGINE=1 -DWITH_FEDERATED_STORAGE_ENGINE=1 -DWITH_BLACKHOLE_STORAGE_ENGINE=1 -DWITH_MYISAM_STORAGE_ENGINE=1 -DWITH_EMBEDDED_SERVER=1 -DENABLE_DTRACE=0 -DENABLED_LOCAL_INFILE=1 -DDEFAULT_CHARSET=utf8mb4
```

#### 编译：debug



```bash
mysqld --initialize-insecure --user=mysql
```




### xdr_free


```sql
[ 45%] Built target lot0lot-t
[ 45%] Building CXX object unittest/gunit/components/keyring_common/CMakeFiles/merge_component_keyring_common_tests-t.dir/data-t.cc.o
[ 45%] Building CXX object unittest/gunit/libmysqlgcs/CMakeFiles/gcs_message-t.dir/interface/gcs_message-t.cc.o
[ 45%] Building CXX object unittest/gunit/innodb/lob/CMakeFiles/zlob0int-t.dir/lot0buf.cc.o
[ 45%] Building CXX object extra/protobuf/protobuf-3.19.4/cmake/CMakeFiles/libprotoc.dir/__/src/google/protobuf/compiler/csharp/csharp_message.cc.o
[ 45%] Building CXX object unittest/gunit/innodb/lob/CMakeFiles/zlob0int-t.dir/fil0fil.cc.o
[ 45%] Linking CXX executable ../../../runtime_output_directory/ddl_rewriter-t
[ 45%] Built target ddl_rewriter-t
[ 45%] Building CXX object unittest/gunit/innodb/lob/CMakeFiles/zlob0int-t.dir/fut0lst.cc.o
[ 45%] Building CXX object extra/protobuf/protobuf-3.19.4/cmake/CMakeFiles/libprotoc.dir/__/src/google/protobuf/compiler/csharp/csharp_message_field.cc.o
[ 45%] Building CXX object unittest/gunit/components/keyring_common/CMakeFiles/merge_component_keyring_common_tests-t.dir/data_file-t.cc.o
[ 45%] Building CXX object unittest/gunit/innodb/lob/CMakeFiles/zlob0int-t.dir/lot0dat.cc.o
[ 46%] Building CXX object unittest/gunit/libmysqlgcs/CMakeFiles/gcs_tagged_lock-t.dir/interface/gcs_tagged_lock-t.cc.o
[ 46%] Building CXX object unittest/gunit/libmysqlgcs/CMakeFiles/gcs_logging-t.dir/xcom/gcs_logging-t.cc.o
[ 46%] Linking CXX executable ../../../../plugin_output_directory/dynamic_loader-t
[ 46%] Linking CXX executable ../../../../runtime_output_directory/zlob0int-t
[ 46%] Built target dynamic_loader-t
[ 46%] Building CXX object extra/protobuf/protobuf-3.19.4/cmake/CMakeFiles/libprotoc.dir/__/src/google/protobuf/compiler/csharp/csharp_primitive_field.cc.o
[ 46%] Built target zlob0int-t
[ 46%] Building CXX object unittest/gunit/libmysqlgcs/CMakeFiles/gcs_logging_system-t.dir/xcom/gcs_logging_system-t.cc.o
[ 46%] Building CXX object unittest/gunit/CMakeFiles/merge_small_tests-t.dir/dphyp-t.cc.o
[ 46%] Building CXX object unittest/gunit/CMakeFiles/merge_small_tests-t.dir/dynarray-t.cc.o
[ 46%] Building CXX object unittest/gunit/libmysqlgcs/CMakeFiles/gcs_parameters-t.dir/xcom/gcs_parameters-t.cc.o
[ 46%] Building CXX object unittest/gunit/components/keyring_common/CMakeFiles/merge_component_keyring_common_tests-t.dir/iterator-t.cc.o
[ 46%] Building CXX object unittest/gunit/components/keyring_common/CMakeFiles/merge_component_keyring_common_tests-t.dir/json_data_file_operations-t.cc.o
[ 46%] Linking CXX executable ../../../runtime_output_directory/gcs_message-t
../../../plugin/group_replication/libmysqlgcs/libmysqlgcs.a(gcs_xcom_proxy.cc.o): In function `Gcs_xcom_input_queue_impl<Gcs_mpsc_queue<xcom_input_request, xcom_input_request_ptr_deleter> >::push_internal(app_data*, void (*)(void*, pax_msg*))':
/db/storage/guolicheng/targz/mysql-8.0.35/plugin/group_replication/libmysqlgcs/src/bindings/xcom/gcs_xcom_input_queue.h:232: undefined reference to `xdr_free'
/db/storage/guolicheng/targz/mysql-8.0.35/plugin/group_replication/libmysqlgcs/src/bindings/xcom/gcs_xcom_input_queue.h:223: undefined reference to `xdr_free'
../../../plugin/group_replication/libmysqlgcs/libmysqlgcs.a(xcom_base.cc.o): In function `move_leader_array':
/db/storage/guolicheng/targz/mysql-8.0.35/plugin/group_replication/libmysqlgcs/src/bindings/xcom/xcom/xcom_base.cc:3764: undefined reference to `xdr_free'
/db/storage/guolicheng/targz/mysql-8.0.35/plugin/group_replication/libmysqlgcs/src/bindings/xcom/xcom/xcom_base.cc:3764: undefined reference to `xdr_free'
../../../plugin/group_replication/libmysqlgcs/libmysqlgcs.a(xcom_base.cc.o): In function `xcom_client_send_die(connection_descriptor*)':
/db/storage/guolicheng/targz/mysql-8.0.35/plugin/group_replication/libmysqlgcs/src/bindings/xcom/xcom/xcom_base.cc:8088: undefined reference to `xdr_free'
../../../plugin/group_replication/libmysqlgcs/libmysqlgcs.a(xcom_base.cc.o):/db/storage/guolicheng/targz/mysql-8.0.35/plugin/group_replication/libmysqlgcs/src/bindings/xcom/xcom/xcom_base.cc:8593: more undefined references to `xdr_free' follow
../../../plugin/group_replication/libmysqlgcs/libmysqlgcs.a(xcom_transport.cc.o): In function `apply_xdr(void*, unsigned int, int (*)(XDR*, void*, ...), void*, xdr_op)':
/db/storage/guolicheng/targz/mysql-8.0.35/plugin/group_replication/libmysqlgcs/src/bindings/xcom/xcom/xcom_transport.cc:426: undefined reference to `xdrmem_create'
../../../plugin/group_replication/libmysqlgcs/libmysqlgcs.a(xcom_transport.cc.o): In function `deserialize_msg(pax_msg*, xcom_proto, char*, unsigned int)':
/db/storage/guolicheng/targz/mysql-8.0.35/plugin/group_replication/libmysqlgcs/src/bindings/xcom/xcom/xcom_transport.cc:548: undefined reference to `xdr_free'
../../../plugin/group_replication/libmysqlgcs/libmysqlgcs.a(xcom_transport.cc.o): In function `serialize(void*, xcom_proto, unsigned int*, int (*)(XDR*, void*, ...), char**)':
/db/storage/guolicheng/targz/mysql-8.0.35/plugin/group_replication/libmysqlgcs/src/bindings/xcom/xcom/xcom_transport.cc:475: undefined reference to `xdr_sizeof'
../../../plugin/group_replication/libmysqlgcs/libmysqlgcs.a(site_def.cc.o): In function `free_site_def_body(site_def*)':
/db/storage/guolicheng/targz/mysql-8.0.35/plugin/group_replication/libmysqlgcs/src/bindings/xcom/xcom/site_def.cc:89: undefined reference to `xdr_free'
/db/storage/guolicheng/targz/mysql-8.0.35/plugin/group_replication/libmysqlgcs/src/bindings/xcom/xcom/site_def.cc:92: undefined reference to `xdr_free'
../../../plugin/group_replication/libmysqlgcs/libmysqlgcs.a(xcom_input_request.cc.o): In function `xcom_input_request_free(xcom_input_request*)':
/db/storage/guolicheng/targz/mysql-8.0.35/plugin/group_replication/libmysqlgcs/src/bindings/xcom/xcom/xcom_input_request.cc:57: undefined reference to `xdr_free'
../../../plugin/group_replication/libmysqlgcs/libmysqlgcs.a(xcom_vp_xdr.c.o): In function `xdr_bit_set_1_0':
/db/storage/guolicheng/targz/build/plugin/group_replication/libmysqlgcs/xdr_gen/xcom_vp_xdr.c:140: undefined reference to `xdr_array'
../../../plugin/group_replication/libmysqlgcs/libmysqlgcs.a(xcom_vp_xdr.c.o): In function `xdr_bit_set_1_1':
/db/storage/guolicheng/targz/build/plugin/group_replication/libmysqlgcs/xdr_gen/xcom_vp_xdr.c:836: undefined reference to `xdr_array'
../../../plugin/group_replication/libmysqlgcs/libmysqlgcs.a(xcom_vp_xdr.c.o): In function `xdr_bit_set_1_2':
/db/storage/guolicheng/targz/build/plugin/group_replication/libmysqlgcs/xdr_gen/xcom_vp_xdr.c:1530: undefined reference to `xdr_array'
../../../plugin/group_replication/libmysqlgcs/libmysqlgcs.a(xcom_vp_xdr.c.o): In function `xdr_bit_set_1_3':
/db/storage/guolicheng/targz/build/plugin/group_replication/libmysqlgcs/xdr_gen/xcom_vp_xdr.c:2223: undefined reference to `xdr_array'
../../../plugin/group_replication/libmysqlgcs/libmysqlgcs.a(xcom_vp_xdr.c.o): In function `xdr_bit_set_1_4':
/db/storage/guolicheng/targz/build/plugin/group_replication/libmysqlgcs/xdr_gen/xcom_vp_xdr.c:2911: undefined reference to `xdr_array'
../../../plugin/group_replication/libmysqlgcs/libmysqlgcs.a(xcom_vp_xdr.c.o):/db/storage/guolicheng/targz/build/plugin/group_replication/libmysqlgcs/xdr_gen/xcom_vp_xdr.c:3602: more undefined references to `xdr_array' follow
../../../plugin/group_replication/libmysqlgcs/libmysqlgcs.a(xcom_vp_xdr.c.o): In function `xdr_leader':
/db/storage/guolicheng/targz/build/plugin/group_replication/libmysqlgcs/xdr_gen/xcom_vp_xdr.c:7169: undefined reference to `xdr_string'
../../../plugin/group_replication/libmysqlgcs/libmysqlgcs.a(xcom_vp_xdr.c.o): In function `xdr_app_data_ptr_1_0':
/db/storage/guolicheng/targz/build/plugin/group_replication/libmysqlgcs/xdr_gen/xcom_vp_xdr.c:470: undefined reference to `xdr_pointer'
../../../plugin/group_replication/libmysqlgcs/libmysqlgcs.a(xcom_vp_xdr.c.o): In function `xdr_config_ptr_1_0':
/db/storage/guolicheng/targz/build/plugin/group_replication/libmysqlgcs/xdr_gen/xcom_vp_xdr.c:552: undefined reference to `xdr_pointer'
../../../plugin/group_replication/libmysqlgcs/libmysqlgcs.a(xcom_vp_xdr.c.o): In function `xdr_app_data_ptr_1_1':
/db/storage/guolicheng/targz/build/plugin/group_replication/libmysqlgcs/xdr_gen/xcom_vp_xdr.c:1164: undefined reference to `xdr_pointer'
../../../plugin/group_replication/libmysqlgcs/libmysqlgcs.a(xcom_vp_xdr.c.o): In function `xdr_config_ptr_1_1':
/db/storage/guolicheng/targz/build/plugin/group_replication/libmysqlgcs/xdr_gen/xcom_vp_xdr.c:1246: undefined reference to `xdr_pointer'
../../../plugin/group_replication/libmysqlgcs/libmysqlgcs.a(xcom_vp_xdr.c.o): In function `xdr_app_data_ptr_1_2':
/db/storage/guolicheng/targz/build/plugin/group_replication/libmysqlgcs/xdr_gen/xcom_vp_xdr.c:1858: undefined reference to `xdr_pointer'
../../../plugin/group_replication/libmysqlgcs/libmysqlgcs.a(xcom_vp_xdr.c.o):/db/storage/guolicheng/targz/build/plugin/group_replication/libmysqlgcs/xdr_gen/xcom_vp_xdr.c:1940: more undefined references to `xdr_pointer' follow
../../../plugin/group_replication/libmysqlgcs/libmysqlgcs.a(xcom_vp_xdr.c.o): In function `xdr_leader_1_1':
xcom_vp_xdr.c:(.text.xdr_leader_1_1+0xa): undefined reference to `xdr_string'
../../../plugin/group_replication/libmysqlgcs/libmysqlgcs.a(xcom_vp_xdr.c.o): In function `xdr_leader_1_0':
xcom_vp_xdr.c:(.text.xdr_leader_1_0+0xa): undefined reference to `xdr_string'
../../../plugin/group_replication/libmysqlgcs/libmysqlgcs.a(xcom_vp_xdr.c.o): In function `xdr_leader_1_5':
xcom_vp_xdr.c:(.text.xdr_leader_1_5+0xa): undefined reference to `xdr_string'
../../../plugin/group_replication/libmysqlgcs/libmysqlgcs.a(xcom_vp_xdr.c.o): In function `xdr_leader_1_9':
xcom_vp_xdr.c:(.text.xdr_leader_1_9+0xa): undefined reference to `xdr_string'
../../../plugin/group_replication/libmysqlgcs/libmysqlgcs.a(xcom_vp_xdr.c.o): In function `xdr_leader_1_3':
xcom_vp_xdr.c:(.text.xdr_leader_1_3+0xa): undefined reference to `xdr_string'
../../../plugin/group_replication/libmysqlgcs/libmysqlgcs.a(xcom_vp_xdr.c.o):xcom_vp_xdr.c:(.text.xdr_leader_1_8+0xa): more undefined references to `xdr_string' follow







[ 48%] Building CXX object extra/protobuf/protobuf-3.19.4/cmake/CMakeFiles/libprotoc.dir/__/src/google/protobuf/compiler/objectivec/objectivec_generator.cc.o
[ 48%] Building CXX object extra/protobuf/protobuf-3.19.4/cmake/CMakeFiles/libprotoc.dir/__/src/google/protobuf/compiler/objectivec/objectivec_helpers.cc.o
[ 48%] Building CXX object unittest/gunit/CMakeFiles/merge_small_tests-t.dir/strings_strnxfrm-t.cc.o
[ 48%] Building CXX object unittest/gunit/CMakeFiles/merge_small_tests-t.dir/strings_utf8-t.cc.o
[ 48%] Building CXX object unittest/gunit/CMakeFiles/merge_small_tests-t.dir/strings_valid_check-t.cc.o
[ 48%] Building CXX object extra/protobuf/protobuf-3.19.4/cmake/CMakeFiles/libprotoc.dir/__/src/google/protobuf/compiler/objectivec/objectivec_map_field.cc.o
[ 48%] Building CXX object unittest/gunit/CMakeFiles/merge_small_tests-t.dir/strtod-t.cc.o
[ 48%] Building CXX object unittest/gunit/CMakeFiles/merge_small_tests-t.dir/strtoll-t.cc.o
[ 48%] Building CXX object unittest/gunit/CMakeFiles/merge_small_tests-t.dir/template_utils-t.cc.o
[ 48%] Building CXX object unittest/gunit/CMakeFiles/merge_small_tests-t.dir/thread_utils-t.cc.o
[ 48%] Building CXX object unittest/gunit/CMakeFiles/merge_small_tests-t.dir/timespec-t.cc.o
[ 48%] Building CXX object unittest/gunit/CMakeFiles/merge_small_tests-t.dir/unhex-t.cc.o
[ 48%] Building CXX object unittest/gunit/CMakeFiles/merge_small_tests-t.dir/val_int_compare-t.cc.o
[ 48%] Building CXX object extra/protobuf/protobuf-3.19.4/cmake/CMakeFiles/libprotoc.dir/__/src/google/protobuf/compiler/objectivec/objectivec_message.cc.o
[ 48%] Building CXX object unittest/gunit/CMakeFiles/merge_small_tests-t.dir/varlen_sort-t.cc.o
[ 48%] Building CXX object extra/protobuf/protobuf-3.19.4/cmake/CMakeFiles/libprotoc.dir/__/src/google/protobuf/compiler/objectivec/objectivec_message_field.cc.o
[ 48%] Building CXX object extra/protobuf/protobuf-3.19.4/cmake/CMakeFiles/libprotoc.dir/__/src/google/protobuf/compiler/objectivec/objectivec_oneof.cc.o
[ 48%] Building CXX object extra/protobuf/protobuf-3.19.4/cmake/CMakeFiles/libprotoc.dir/__/src/google/protobuf/compiler/objectivec/objectivec_primitive_field.cc.o
[ 48%] Building CXX object extra/protobuf/protobuf-3.19.4/cmake/CMakeFiles/libprotoc.dir/__/src/google/protobuf/compiler/php/php_generator.cc.o
[ 48%] Building CXX object extra/protobuf/protobuf-3.19.4/cmake/CMakeFiles/libprotoc.dir/__/src/google/protobuf/compiler/plugin.cc.o
[ 48%] Building CXX object extra/protobuf/protobuf-3.19.4/cmake/CMakeFiles/libprotoc.dir/__/src/google/protobuf/compiler/plugin.pb.cc.o
[ 48%] Building CXX object extra/protobuf/protobuf-3.19.4/cmake/CMakeFiles/libprotoc.dir/__/src/google/protobuf/compiler/python/python_generator.cc.o
[ 48%] Building CXX object extra/protobuf/protobuf-3.19.4/cmake/CMakeFiles/libprotoc.dir/__/src/google/protobuf/compiler/ruby/ruby_generator.cc.o
[ 48%] Building CXX object extra/protobuf/protobuf-3.19.4/cmake/CMakeFiles/libprotoc.dir/__/src/google/protobuf/compiler/subprocess.cc.o
[ 48%] Building CXX object extra/protobuf/protobuf-3.19.4/cmake/CMakeFiles/libprotoc.dir/__/src/google/protobuf/compiler/zip_writer.cc.o
[ 48%] Linking CXX executable ../../runtime_output_directory/merge_small_tests-t
[ 48%] Built target merge_small_tests-t
[ 48%] Linking CXX shared library ../../../../library_output_directory/libprotoc.so
[ 48%] Built target libprotoc
make: *** [all] Error 2
[guolicheng@48 build]$


```


