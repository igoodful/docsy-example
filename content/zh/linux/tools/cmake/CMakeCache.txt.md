---
title: CMakeCache.txt
description: CMakeCache.txt
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



{{<note>}}
<!---->


```bash

```



{{</note>}}


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


