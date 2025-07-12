---
title: compile
description: compile
date: 2023-10-31
weight: 2000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

{{< /alert >}}


{{< alert title="sort参数详解" color="secondary">}}

{{< /alert >}}


## 统计
```bash
[root@dev /root/mysql-server-8032-google]# find . -regex '.*.h' |awk -F'/' '{print $1 $2}' |sort -nr |uniq -c |sort -nr
   1177 .extra
    815 .sql
    720 .storage
    657 .plugin
    377 .include
    348 .router
    129 .client
    122 .unittest
     67 .components
     26 .libbinlogevents
     22 .libmysql
     14 .mysql-test
     12 .sql-common
      7 .strings
      7 .mysys
      6 .packaging
      6 .libchangestreams
      3 .scripts
      1 .vio
      1 .support-files
[root@dev /root/mysql-server-8032-google]# find . -regex '.*.cc' |awk -F'/' '{print $1 $2}' |sort -nr |uniq -c |sort -nr
    669 .sql
    565 .storage
    435 .plugin
    433 .router
    408 .unittest
    285 .extra
    118 .client
    108 .mysys
    101 .components
     39 .strings
     27 .libmysql
     18 .sql-common
     12 .vio
     10 .utilities
      3 .testclients
      3 .mysql-test
      1 .scripts
[root@dev /root/mysql-server-8032-google]#

```

经过排查，`mysql-server/include`目录下的头文件经过`clang-format`格式化后存在问题，只要不格式化该目录即可，但不是最佳解决方案



```cmake
SET(abi_check_out ${BINARY_DIR}/abi_check.out)


SET(ABI_SOURCE_DIR ${SOURCE_DIR})
SET(ABI_BINARY_DIR ${BINARY_DIR})
# If we're using WSL on Windows, we have to translate paths using wslpath
IF(WSL_EXECUTABLE)
  EXECUTE_PROCESS(
    COMMAND ${WSL_EXECUTABLE} wslpath ${ABI_SOURCE_DIR}
    OUTPUT_STRIP_TRAILING_WHITESPACE OUTPUT_VARIABLE ABI_SOURCE_DIR)
  EXECUTE_PROCESS(
    COMMAND ${WSL_EXECUTABLE} wslpath ${ABI_BINARY_DIR}
    OUTPUT_STRIP_TRAILING_WHITESPACE OUTPUT_VARIABLE ABI_BINARY_DIR)
  EXECUTE_PROCESS(
    COMMAND ${WSL_EXECUTABLE} wslpath ${abi_check_out}
    OUTPUT_STRIP_TRAILING_WHITESPACE OUTPUT_VARIABLE abi_current)
ELSE()
  SET (abi_current ${abi_check_out})
ENDIF()

FOREACH(file ${ABI_HEADERS})
  GET_FILENAME_COMPONENT(header_basename ${file} NAME)
  SET(tmpfile ${BINARY_DIR}/${header_basename}.pp.tmp)
  SET(abi_file ${file})
  IF(WSL_EXECUTABLE)
    EXECUTE_PROCESS(
      COMMAND ${WSL_EXECUTABLE} wslpath ${abi_file}
      OUTPUT_STRIP_TRAILING_WHITESPACE OUTPUT_VARIABLE abi_file)
  ENDIF()

  EXECUTE_PROCESS(
    COMMAND ${WSL_EXECUTABLE} ${COMPILER}
      -E -nostdinc -dI -DMYSQL_ABI_CHECK -I${ABI_SOURCE_DIR}/include
      -I${ABI_BINARY_DIR}/include -I${ABI_SOURCE_DIR}/include/mysql
      -I${ABI_SOURCE_DIR}/sql -I${ABI_SOURCE_DIR}/libbinlogevents/export
      ${abi_file}
      ERROR_QUIET OUTPUT_FILE ${tmpfile})
  EXECUTE_PROCESS(
    COMMAND ${WSL_EXECUTABLE} sed -e "/^# /d"
                -e "/^[ ]*$/d"
                -e "/^#pragma GCC set_debug_pwd/d"
                -e "/^#ident/d"
    RESULT_VARIABLE result OUTPUT_FILE ${abi_check_out} INPUT_FILE ${tmpfile})
  IF(NOT ${result} EQUAL 0)
    MESSAGE(FATAL_ERROR "sed returned error ${result}")
  ENDIF()
  FILE(REMOVE ${tmpfile})

  IF(WSL_EXECUTABLE)
    EXECUTE_PROCESS(
      COMMAND ${WSL_EXECUTABLE} wslpath ${file}
      OUTPUT_STRIP_TRAILING_WHITESPACE OUTPUT_VARIABLE abi_original)
  ELSE()
    SET (abi_original ${file})
  ENDIF()

  EXECUTE_PROCESS(
    COMMAND ${WSL_EXECUTABLE} diff -w ${abi_original}.pp ${abi_current}
    RESULT_VARIABLE result)
  IF(NOT ${result} EQUAL 0)
    MESSAGE(FATAL_ERROR
      "ABI check found difference between ${file}.pp and ${abi_check_out}")
  ENDIF()
  FILE(REMOVE ${abi_check_out})
ENDFOREACH()

```




## 问题记录


#### difference between abi_check.out


```bash
[  8%] Generating sql_commands_system_tables.h
[  8%] Generating sql_commands_system_users.h
[  8%] Generating sql_commands_help_data.h
[  8%] Generating sql_commands_system_tables_data_fix.h
[  8%] Generating sql_firewall_stored_procedures.h
[  8%] Built target GenBootstrapPriv
[  8%] Building CXX object storage/ndb/src/ndbapi/CMakeFiles/ndbapi_objlib.dir/Ndbinit.cpp.o
290,301c290
<         int type;
<         unsigned int interface_version;
<         const char *name;
<         const char *author;
<         const char *desc;
<         unsigned int version[3];
<         const char *license;
<         void *mysql_api;
<         int (*init)(char *, size_t, int, va_list);
<         int (*deinit)(void);
<         int (*options)(const char *option, const void *);
<         int (*get_options)(const char *option, void *);
---
>         int type; unsigned int interface_version; const char *name; const char *author; const char *desc; unsigned int version[3]; const char *license; void *mysql_api; int (*init)(char *, size_t, int, va_list); int (*deinit)(void); int (*options)(const char *option, const void *); int (*get_options)(const char *option, void *);
335,346c324
<         int type;
<         unsigned int interface_version;
<         const char *name;
<         const char *author;
<         const char *desc;
<         unsigned int version[3];
<         const char *license;
<         void *mysql_api;
<         int (*init)(char *, size_t, int, va_list);
<         int (*deinit)(void);
<         int (*options)(const char *option, const void *);
<         int (*get_options)(const char *option, void *);
---
>         int type; unsigned int interface_version; const char *name; const char *author; const char *desc; unsigned int version[3]; const char *license; void *mysql_api; int (*init)(char *, size_t, int, va_list); int (*deinit)(void); int (*options)(const char *option, const void *); int (*get_options)(const char *option, void *);
CMake Error at /root/mysql-server-8032-google/cmake/do_abi_check.cmake:121 (MESSAGE):
  ABI check found difference between
  /root/mysql-server-8032-google/include/mysql.h.pp and
  /root/8032-google/abi_check.out


make[2]: *** [CMakeFiles/abi_check.dir/build.make:70: CMakeFiles/abi_check] Error 1
make[1]: *** [CMakeFiles/Makefile2:6128: CMakeFiles/abi_check.dir/all] Error 2
make[1]: *** Waiting for unfinished jobs....

```

找出需要进行`abi_check`的所有文件：`[root@dev /root/mysql-server-8032-google]# find ./ -regex '.*.h.pp'`
```sql
./include/mysql.h.pp
./include/mysql/services.h.pp
./include/mysql/plugin_auth.h.pp
./include/mysql/plugin_ftparser.h.pp
./include/mysql/psi/psi_abi_table_v1.h.pp
./include/mysql/psi/psi_abi_cond_v1.h.pp
./include/mysql/psi/psi_abi_error_v1.h.pp
./include/mysql/psi/psi_abi_file_v1.h.pp
./include/mysql/psi/psi_abi_idle_v1.h.pp
./include/mysql/psi/psi_abi_mdl_v1.h.pp
./include/mysql/psi/psi_abi_memory_v1.h.pp
./include/mysql/psi/psi_abi_mutex_v1.h.pp
./include/mysql/psi/psi_abi_rwlock_v1.h.pp
./include/mysql/psi/psi_abi_stage_v1.h.pp
./include/mysql/psi/psi_abi_statement_v1.h.pp
./include/mysql/psi/psi_abi_system_v1.h.pp
./include/mysql/psi/psi_abi_thread_v1.h.pp
./include/mysql/psi/psi_abi_socket_v1.h.pp
./include/mysql/psi/psi_abi_transaction_v1.h.pp
./include/mysql/plugin_keyring.h.pp
./include/mysql/client_plugin.h.pp
./include/mysql/plugin_audit.h.pp

```

获取这些`.h.pp`文件中的头文件：`[root@dev /root/mysql-server-8032-google]# find ./ -regex '.*.h.pp' |xargs grep '#include' |awk -F':' '{print $2}'  |sed 's/#include //g' |sed 's/\"//g' |sort -nr |uniq`
```sql
<stdint.h>
status_var.h
plugin.h
plugin_auth_common.h
plugin_audit_message_types.h
mysql_version.h
mysql/udf_registration_types.h
mysql_time.h
mysql/service_srv_session.h
mysql/service_srv_session_bits.h
<mysql/service_plugin_registry.h>
<mysql/service_parser.h>
<mysql/service_mysql_string.h>
<mysql/service_mysql_password_policy.h>
<mysql/service_mysql_keyring.h>
<mysql/service_mysql_alloc.h>
<mysql/service_my_plugin_log.h>
<mysql/service_locking.h>
<mysql/service_command.h>
mysql/psi/psi_transaction.h
mysql/psi/psi_thread.h
mysql/psi/psi_table.h
mysql/psi/psi_system.h
mysql/psi/psi_statement.h
mysql/psi/psi_stage.h
mysql/psi/psi_socket.h
mysql/psi/psi_rwlock.h
mysql/psi/psi_mutex.h
mysql/psi/psi_memory.h
mysql/psi/psi_mdl.h
mysql/psi/psi_idle.h
mysql/psi/psi_file.h
mysql/psi/psi_error.h
mysql/psi/psi_cond.h
<mysql/plugin.h>
mysql/mysql_lex_string.h
<mysql/mysql_lex_string.h>
<mysql/components/services/registry.h>
<mysql/components/services/bits/system_variables_bits.h>
mysql/components/services/bits/psi_transaction_bits.h
mysql/components/services/bits/psi_thread_bits.h
mysql/components/services/bits/psi_table_bits.h
mysql/components/services/bits/psi_system_bits.h
mysql/components/services/bits/psi_statement_bits.h
mysql/components/services/bits/psi_stage_bits.h
mysql/components/services/bits/psi_socket_bits.h
mysql/components/services/bits/psi_rwlock_bits.h
mysql/components/services/bits/psi_mutex_bits.h
mysql/components/services/bits/psi_memory_bits.h
mysql/components/services/bits/psi_mdl_bits.h
mysql/components/services/bits/psi_idle_bits.h
mysql/components/services/bits/psi_file_bits.h
mysql/components/services/bits/psi_error_bits.h
mysql/components/services/bits/psi_cond_bits.h
<mysql/components/services/bits/psi_bits.h>
<mysql/components/services/bits/plugin_audit_connection_types.h>
<mysql/components/services/bits/my_thread_bits.h>
<mysql/components/services/bits/my_io_bits.h>
<mysql/components/service.h>
my_sqlcommand.h
mysql_com.h
mysql/com_data.h
mysql/client_plugin.h
my_sharedlib.h
my_psi_config.h
my_macros.h
my_list.h
my_inttypes.h
my_config.h
my_compress.h
my_command.h
field_types.h
errmsg.h
decimal.h


```

说明include中共有377个头文件，其中74个头文件需要进行api检测
```bash
[root@dev /root/mysql-server-8032-google]# find ./ -regex '.*.h.pp' |xargs grep '#include' |awk -F':' '{print $2}'  |sed 's/#include //g' |sed 's/\"//g' |sort -nr |uniq |wc -l
74

[root@dev /root/mysql-server-8032-google]# find ./include/ -name "*.h" |wc -l
377
```




#### difference between abi_check.out

```bash
[ 45%] Building C object CMakeFiles/server_unittest_library.dir/server_unittest_library_dummy.c.o
/opt/rh/gcc-toolset-11/root/usr/bin/gcc -DBOOST_NO_CXX98_FUNCTION_BASE -DHAVE_CONFIG_H -DHAVE_TLSv13 -DLZ4_DISABLE_DEPRECATE_WARNINGS -DRAPIDJSON_NO_SIZETYPEDEFINE -DRAPIDJSON_SCHEMA_USE_INTERNALREGEX=0 -DRAPIDJSON_SCHEMA_USE_STDREGEX=1 -D_FILE_OFFSET_BITS=64 -D_GNU_SOURCE -D_USE_MATH_DEFINES -D__STDC_FORMAT_MACROS -D__STDC_LIMIT_MACROS -Dserver_unittest_library_EXPORTS -I/root/8032-google -I/root/8032-google/include -I/root/mysql-server-8032-google -I/root/mysql-server-8032-google/include -isystem /root/mysql-server-8032-google/extra/rapidjson/include -isystem /root/mysql-server-8032-google/extra/protobuf/protobuf-3.19.4/src -isystem /root/mysql-server-8032-google/extra/lz4/lz4-1.9.4/lib -isystem /root/mysql-server-8032-google/extra/libedit/libedit-20210910-3.1/src/editline -isystem /root/mysql-server-8032-google/extra/zstd/zstd-1.5.0/lib -isystem /root/8032-google/extra/zlib/zlib-1.2.13 -isystem /root/mysql-server-8032-google/extra/zlib/zlib-1.2.13 -isystem /root/mysql-server-8032-google/plugin/x/client -isystem /root/8032-google/plugin/x/generated/protobuf_lite -isystem /root/8032-google/plugin/x/generated -fno-omit-frame-pointer -ftls-model=initial-exec -B/opt/rh/gcc-toolset-11/root/usr/bin  -Wall -Wextra -Wformat-security -Wvla -Wundef -Wmissing-format-attribute -Wwrite-strings -Wjump-misses-init -Wstringop-truncation -Wmissing-include-dirs -Werror -DSAFE_MUTEX -DENABLED_DEBUG_SYNC -g -fPIC -MD -MT CMakeFiles/server_unittest_library.dir/server_unittest_library_dummy.c.o -MF CMakeFiles/server_unittest_library.dir/server_unittest_library_dummy.c.o.d -o CMakeFiles/server_unittest_library.dir/server_unittest_library_dummy.c.o -c /root/8032-google/server_unittest_library_dummy.c
[ 45%] Linking CXX shared library library_output_directory/libserver_unittest_library.so
/usr/bin/cmake -E cmake_link_script CMakeFiles/server_unittest_library.dir/link.txt --verbose=1
/opt/rh/gcc-toolset-11/root/usr/bin/g++ -fPIC -std=c++17 -fno-omit-frame-pointer -ftls-model=initial-exec -B/opt/rh/gcc-toolset-11/root/usr/bin  -Wall -Wextra -Wformat-security -Wvla -Wundef -Wmissing-format-attribute -Woverloaded-virtual -Wcast-qual -Wimplicit-fallthrough=5 -Wstringop-truncation -Wsuggest-override -Wmissing-include-dirs -Wextra-semi -Wlogical-op -Werror -DSAFE_MUTEX -DENABLED_DEBUG_SYNC -g   -Wl,-u,builtin_perfschema_plugin -Wl,-u,mysql_service_mysql_rwlock_v1  -Wl,-rpath,'$ORIGIN/../../private' -shared -Wl,-soname,libserver_unittest_library.so -o library_output_directory/libserver_unittest_library.so CMakeFiles/server_unittest_library.dir/server_unittest_library_dummy.c.o  -Wl,-rpath,/usr/local/lib:/root/8032-google/library_output_directory -lpthread sql/libsql_main.a storage/archive/libarchive.a archive_output_directory/libzlib.a storage/blackhole/libblackhole.a storage/csv/libcsv.a storage/federated/libfederated.a storage/heap/libheap.a storage/heap/libheap_library.a storage/innobase/libinnobase.a sql/libsql_dd.a sql/libsql_gis.a archive_output_directory/libzlib.a liblz4_lib.a -lnuma storage/myisam/libmyisam.a storage/myisam/libmyisam_library.a storage/myisammrg/libmyisammrg.a storage/ndb/libndbcluster.a archive_output_directory/libndbclient_static.a storage/perfschema/libperfschema.a storage/temptable/libtemptable.a plugin/fulltext/libngram_parser.a plugin/x/libmysqlx.a /usr/lib64/libevent_core.so /usr/lib64/libevent_extra.so /usr/lib64/libevent_openssl.so /usr/lib64/libevent_pthreads.so /usr/local/lib/libicuuc.so /usr/local/lib/libicuio.so /usr/local/lib/libicudata.so /usr/local/lib/libicui18n.so liblz4_lib.a plugin/x/protocol/protobuf/libmysqlxmessages_lite.a components/libminchassis/libminchassis.a /usr/local/lib/libicuuc.so /usr/local/lib/libicuio.so /usr/local/lib/libicudata.so /usr/local/lib/libicui18n.so -lnuma sql/librpl.a sql/libmaster.a sql/libslave.a sql/libbinlog.a sql/libsql_main.a storage/innobase/libinnobase.a sql/libsql_dd.a sql/libsql_gis.a sql/librpl.a sql/libmaster.a sql/libslave.a sql/libbinlog.a sql/libsql_main.a storage/innobase/libinnobase.a sql/libsql_dd.a sql/libsql_gis.a sql/librpl.a sql/libmaster.a sql/libslave.a sql/libbinlog.a storage/archive/libarchive.a storage/blackhole/libblackhole.a storage/csv/libcsv.a storage/federated/libfederated.a storage/heap/libheap.a storage/heap/libheap_library.a storage/myisam/libmyisam.a storage/myisam/libmyisam_library.a storage/myisammrg/libmyisammrg.a storage/ndb/libndbcluster.a archive_output_directory/libndbclient_static.a storage/perfschema/libperfschema.a storage/temptable/libtemptable.a plugin/fulltext/libngram_parser.a plugin/x/libmysqlx.a /usr/lib64/libevent_core.so /usr/lib64/libevent_extra.so /usr/lib64/libevent_openssl.so /usr/lib64/libevent_pthreads.so /usr/local/lib/libicuuc.so /usr/local/lib/libicuio.so /usr/local/lib/libicudata.so /usr/local/lib/libicui18n.so plugin/x/protocol/protobuf/libmysqlxmessages_lite.a sql/server_component/libmysql_server_component_services.a archive_output_directory/libvio.a liblz4_lib.a -laio -lnuma library_output_directory/libprotobuf-lite.so.3.19.4 sql/protobuf/librpl_protobuf_lite.a libbinlogevents/lib/libbinlogevents.a archive_output_directory/libmysys.a archive_output_directory/libstrings.a archive_output_directory/libmysys.a archive_output_directory/libstrings.a archive_output_directory/libzlib.a -lm -lrt /usr/lib64/libssl.so /usr/lib64/libcrypto.so archive_output_directory/libmytime.a archive_output_directory/libzstd.a -lpthread -ldl
make[2]: Leaving directory '/root/8032-google'
[ 45%] Built target server_unittest_library
make  -f CMakeFiles/abi_check.dir/build.make CMakeFiles/abi_check.dir/depend
make[2]: Entering directory '/root/8032-google'
cd /root/8032-google && /usr/bin/cmake -E cmake_depends "Unix Makefiles" /root/mysql-server-8032-google /root/mysql-server-8032-google /root/8032-google /root/8032-google /root/8032-google/CMakeFiles/abi_check.dir/DependInfo.cmake --color=
make[2]: Leaving directory '/root/8032-google'
make  -f CMakeFiles/abi_check.dir/build.make CMakeFiles/abi_check.dir/build
make[2]: Entering directory '/root/8032-google'
/usr/bin/cmake -DCOMPILER=/opt/rh/gcc-toolset-11/root/usr/bin/gcc -DSOURCE_DIR=/root/mysql-server-8032-google -DBINARY_DIR=/root/8032-google -DWSL_EXECUTABLE= "-DABI_HEADERS=/root/mysql-server-8032-google/include/mysql/plugin_audit.h;/root/mysql-server-8032-google/include/mysql/plugin_ftparser.h;/root/mysql-server-8032-google/include/mysql.h;/root/mysql-server-8032-google/include/mysql/client_plugin.h;/root/mysql-server-8032-google/include/mysql/plugin_auth.h;/root/mysql-server-8032-google/include/mysql/plugin_keyring.h;/root/mysql-server-8032-google/include/mysql/psi/psi_abi_thread_v1.h;/root/mysql-server-8032-google/include/mysql/psi/psi_abi_mutex_v1.h;/root/mysql-server-8032-google/include/mysql/psi/psi_abi_rwlock_v1.h;/root/mysql-server-8032-google/include/mysql/psi/psi_abi_cond_v1.h;/root/mysql-server-8032-google/include/mysql/psi/psi_abi_file_v1.h;/root/mysql-server-8032-google/include/mysql/psi/psi_abi_socket_v1.h;/root/mysql-server-8032-google/include/mysql/psi/psi_abi_table_v1.h;/root/mysql-server-8032-google/include/mysql/psi/psi_abi_mdl_v1.h;/root/mysql-server-8032-google/include/mysql/psi/psi_abi_idle_v1.h;/root/mysql-server-8032-google/include/mysql/psi/psi_abi_stage_v1.h;/root/mysql-server-8032-google/include/mysql/psi/psi_abi_statement_v1.h;/root/mysql-server-8032-google/include/mysql/psi/psi_abi_transaction_v1.h;/root/mysql-server-8032-google/include/mysql/psi/psi_abi_memory_v1.h;/root/mysql-server-8032-google/include/mysql/psi/psi_abi_error_v1.h;/root/mysql-server-8032-google/include/mysql/psi/psi_abi_system_v1.h;/root/mysql-server-8032-google/include/mysql/services.h" -P /root/mysql-server-8032-google/cmake/do_abi_check.cmake
290,301c290
<         int type;
<         unsigned int interface_version;
<         const char *name;
<         const char *author;
<         const char *desc;
<         unsigned int version[3];
<         const char *license;
<         void *mysql_api;
<         int (*init)(char *, size_t, int, va_list);
<         int (*deinit)(void);
<         int (*options)(const char *option, const void *);
<         int (*get_options)(const char *option, void *);
---
>         int type; unsigned int interface_version; const char *name; const char *author; const char *desc; unsigned int version[3]; const char *license; void *mysql_api; int (*init)(char *, size_t, int, va_list); int (*deinit)(void); int (*options)(const char *option, const void *); int (*get_options)(const char *option, void *);
335,346c324
<         int type;
<         unsigned int interface_version;
<         const char *name;
<         const char *author;
<         const char *desc;
<         unsigned int version[3];
<         const char *license;
<         void *mysql_api;
<         int (*init)(char *, size_t, int, va_list);
<         int (*deinit)(void);
<         int (*options)(const char *option, const void *);
<         int (*get_options)(const char *option, void *);
---
>         int type; unsigned int interface_version; const char *name; const char *author; const char *desc; unsigned int version[3]; const char *license; void *mysql_api; int (*init)(char *, size_t, int, va_list); int (*deinit)(void); int (*options)(const char *option, const void *); int (*get_options)(const char *option, void *);
CMake Error at /root/mysql-server-8032-google/cmake/do_abi_check.cmake:121 (MESSAGE):
  ABI check found difference between
  /root/mysql-server-8032-google/include/mysql.h.pp and
  /root/8032-google/abi_check.out


make[2]: *** [CMakeFiles/abi_check.dir/build.make:70: CMakeFiles/abi_check] Error 1
make[2]: Leaving directory '/root/8032-google'
make[1]: *** [CMakeFiles/Makefile2:6128: CMakeFiles/abi_check.dir/all] Error 2
make[1]: Leaving directory '/root/8032-google'
make: *** [Makefile:166: all] Error 2

```

单独执行下报错命令：`/usr/bin/cmake`
```bash
[root@dev /root/8032-google]# /usr/bin/cmake -DCOMPILER=/opt/rh/gcc-toolset-11/root/usr/bin/gcc -DSOURCE_DIR=/root/mysql-server-8032-google -DBINARY_DIR=/root/8032-google -DWSL_EXECUTABLE= "-DABI_HEADERS=/root/mysql-server-8032-google/include/mysql/plugin_audit.h;/root/mysql-server-8032-google/include/mysql/plugin_ftparser.h;/root/mysql-server-8032-google/include/mysql.h;/root/mysql-server-8032-google/include/mysql/client_plugin.h;/root/mysql-server-8032-google/include/mysql/plugin_auth.h;/root/mysql-server-8032-google/include/mysql/plugin_keyring.h;/root/mysql-server-8032-google/include/mysql/psi/psi_abi_thread_v1.h;/root/mysql-server-8032-google/include/mysql/psi/psi_abi_mutex_v1.h;/root/mysql-server-8032-google/include/mysql/psi/psi_abi_rwlock_v1.h;/root/mysql-server-8032-google/include/mysql/psi/psi_abi_cond_v1.h;/root/mysql-server-8032-google/include/mysql/psi/psi_abi_file_v1.h;/root/mysql-server-8032-google/include/mysql/psi/psi_abi_socket_v1.h;/root/mysql-server-8032-google/include/mysql/psi/psi_abi_table_v1.h;/root/mysql-server-8032-google/include/mysql/psi/psi_abi_mdl_v1.h;/root/mysql-server-8032-google/include/mysql/psi/psi_abi_idle_v1.h;/root/mysql-server-8032-google/include/mysql/psi/psi_abi_stage_v1.h;/root/mysql-server-8032-google/include/mysql/psi/psi_abi_statement_v1.h;/root/mysql-server-8032-google/include/mysql/psi/psi_abi_transaction_v1.h;/root/mysql-server-8032-google/include/mysql/psi/psi_abi_memory_v1.h;/root/mysql-server-8032-google/include/mysql/psi/psi_abi_error_v1.h;/root/mysql-server-8032-google/include/mysql/psi/psi_abi_system_v1.h;/root/mysql-server-8032-google/include/mysql/services.h" -P /root/mysql-server-8032-google/cmake/do_abi_check.cmake
290,301c290
<         int type;
<         unsigned int interface_version;
<         const char *name;
<         const char *author;
<         const char *desc;
<         unsigned int version[3];
<         const char *license;
<         void *mysql_api;
<         int (*init)(char *, size_t, int, va_list);
<         int (*deinit)(void);
<         int (*options)(const char *option, const void *);
<         int (*get_options)(const char *option, void *);
---
>         int type; unsigned int interface_version; const char *name; const char *author; const char *desc; unsigned int version[3]; const char *license; void *mysql_api; int (*init)(char *, size_t, int, va_list); int (*deinit)(void); int (*options)(const char *option, const void *); int (*get_options)(const char *option, void *);
335,346c324
<         int type;
<         unsigned int interface_version;
<         const char *name;
<         const char *author;
<         const char *desc;
<         unsigned int version[3];
<         const char *license;
<         void *mysql_api;
<         int (*init)(char *, size_t, int, va_list);
<         int (*deinit)(void);
<         int (*options)(const char *option, const void *);
<         int (*get_options)(const char *option, void *);
---
>         int type; unsigned int interface_version; const char *name; const char *author; const char *desc; unsigned int version[3]; const char *license; void *mysql_api; int (*init)(char *, size_t, int, va_list); int (*deinit)(void); int (*options)(const char *option, const void *); int (*get_options)(const char *option, void *);
CMake Error at /root/mysql-server-8032-google/cmake/do_abi_check.cmake:121 (MESSAGE):
  ABI check found difference between
  /root/mysql-server-8032-google/include/mysql.h.pp and
  /root/8032-google/abi_check.out

```

这里cmake和cmake3相同
```bash
[root@dev /root/8032-google]# cmake --version
cmake version 3.26.5

CMake suite maintained and supported by Kitware (kitware.com/cmake).

[root@dev /root/8032-google]# cmake3 --version
cmake version 3.26.5

CMake suite maintained and supported by Kitware (kitware.com/cmake).

```

`[root@dev /root/tmp]# cat do_abi_check.cmake `
```cmake
set(abi_check_out ${BINARY_DIR}/abi_check.out)

set(ABI_SOURCE_DIR ${SOURCE_DIR})
set(ABI_BINARY_DIR ${BINARY_DIR})

# If we're using WSL on Windows, we have to translate paths using wslpath
if(WSL_EXECUTABLE)
        execute_process(
                COMMAND ${WSL_EXECUTABLE} wslpath ${ABI_SOURCE_DIR}
                OUTPUT_STRIP_TRAILING_WHITESPACE
                OUTPUT_VARIABLE ABI_SOURCE_DIR)
        execute_process(
                COMMAND ${WSL_EXECUTABLE} wslpath ${ABI_BINARY_DIR}
                OUTPUT_STRIP_TRAILING_WHITESPACE
                OUTPUT_VARIABLE ABI_BINARY_DIR)
        execute_process(
                COMMAND ${WSL_EXECUTABLE} wslpath ${abi_check_out}
                OUTPUT_STRIP_TRAILING_WHITESPACE
                OUTPUT_VARIABLE abi_current)
else()
        set(abi_current ${abi_check_out})
endif()

foreach(file ${ABI_HEADERS})
        get_filename_component(header_basename ${file} NAME)
        set(tmpfile ${BINARY_DIR}/${header_basename}.pp.tmp)
        set(abi_file ${file})
        if(WSL_EXECUTABLE)
                execute_process(
                        COMMAND ${WSL_EXECUTABLE} wslpath ${abi_file}
                        OUTPUT_STRIP_TRAILING_WHITESPACE
                        OUTPUT_VARIABLE abi_file)
        endif()

        execute_process(
                COMMAND
                        ${WSL_EXECUTABLE} ${COMPILER} -E -nostdinc -dI -DMYSQL_ABI_CHECK -I${ABI_SOURCE_DIR}/include
                        -I${ABI_BINARY_DIR}/include -I${ABI_SOURCE_DIR}/include/mysql -I${ABI_SOURCE_DIR}/sql
                        -I${ABI_SOURCE_DIR}/libbinlogevents/export ${abi_file}
                ERROR_QUIET
                OUTPUT_FILE ${tmpfile})
        execute_process(
                COMMAND ${WSL_EXECUTABLE} sed -e "/^# /d" -e "/^[	]*$/d" -e "/^#pragma GCC set_debug_pwd/d" -e "/^#ident/d"
                RESULT_VARIABLE result
                OUTPUT_FILE ${abi_check_out}
                INPUT_FILE ${tmpfile})
        if(NOT ${result} EQUAL 0)
                message(FATAL_ERROR "sed returned error ${result}")
        endif()
        file(REMOVE ${tmpfile})

        if(WSL_EXECUTABLE)
                execute_process(
                        COMMAND ${WSL_EXECUTABLE} wslpath ${file}
                        OUTPUT_STRIP_TRAILING_WHITESPACE
                        OUTPUT_VARIABLE abi_original)
        else()
                set(abi_original ${file})
        endif()

        execute_process(COMMAND ${WSL_EXECUTABLE} diff -w ${abi_original}.pp ${abi_current} RESULT_VARIABLE result)
        if(NOT ${result} EQUAL 0)
                message(FATAL_ERROR "ABI check found difference between ${file}.pp and ${abi_check_out}")
        endif()
        file(REMOVE ${abi_check_out})
endforeach()

```


