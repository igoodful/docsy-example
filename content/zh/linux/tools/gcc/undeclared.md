---
title: 类型未声明
description: xxx has not been declared
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


## 案例一

####
```bash
cd /root/8032-google/mysys && /opt/rh/gcc-toolset-11/root/usr/bin/g++ -DBOOST_NO_CXX98_FUNCTION_BASE -DHAVE_CONFIG_H -DHAVE_TLSv13 -DLZ4_DISABLE_DEPRECATE_WARNINGS -DRAPIDJSON_NO_SIZETYPEDEFINE -DRAPIDJSON_SCHEMA_USE_INTERNALREGEX=0 -DRAPIDJSON_SCHEMA_USE_STDREGEX=1 -D_FILE_OFFSET_BITS=64 -D_GNU_SOURCE -D_USE_MATH_DEFINES -D__STDC_FORMAT_MACROS -D__STDC_LIMIT_MACROS -I/root/8032-google -I/root/8032-google/include -I/root/mysql-server-8032-google -I/root/mysql-server-8032-google/include -isystem /root/mysql-server-8032-google/extra/rapidjson/include -isystem /root/mysql-server-8032-google/extra/protobuf/protobuf-3.19.4/src -isystem /root/mysql-server-8032-google/extra/lz4/lz4-1.9.4/lib -isystem /root/mysql-server-8032-google/extra/libedit/libedit-20210910-3.1/src/editline -isystem /root/mysql-server-8032-google/extra/zstd/zstd-1.5.0/lib -isystem /root/8032-google/extra/zlib/zlib-1.2.13 -isystem /root/mysql-server-8032-google/extra/zlib/zlib-1.2.13 -std=c++17 -fno-omit-frame-pointer -ftls-model=initial-exec -B/opt/rh/gcc-toolset-11/root/usr/bin  -Wall -Wextra -Wformat-security -Wvla -Wundef -Wmissing-format-attribute -Woverloaded-virtual -Wcast-qual -Wimplicit-fallthrough=5 -Wstringop-truncation -Wsuggest-override -Wmissing-include-dirs -Wextra-semi -Wlogical-op -Werror -DSAFE_MUTEX -DENABLED_DEBUG_SYNC -g -fPIC -Wshadow=local -MD -MT mysys/CMakeFiles/mysys_objlib.dir/keyring_operations_helper.cc.o -MF CMakeFiles/mysys_objlib.dir/keyring_operations_helper.cc.o.d -o CMakeFiles/mysys_objlib.dir/keyring_operations_helper.cc.o -c /root/mysql-server-8032-google/mysys/keyring_operations_helper.cc
In file included from /root/mysql-server-8032-google/include/keyring_operations_helper.h:26,
                 from /root/mysql-server-8032-google/mysys/keyring_operations_helper.cc:23:
/root/mysql-server-8032-google/include/mysql/components/services/keyring_reader_with_status.h:178:36: error: ‘size_t’ has not been declared
  178 |                                    size_t *data_size, size_t *data_type_size));
      |                                    ^~~~~~
```
## 解决

#### 查看
> `[root@dev /root/mysql-server-8032-google]# cat /root/mysql-server-8032-google/include/mysql/components/services/keyring_reader_with_status.h`
```c++
#ifndef KEYRING_READER_WITH_STATUS_INCLUDED
#define KEYRING_READER_WITH_STATUS_INCLUDED

#include <mysql/components/service.h>

DEFINE_SERVICE_HANDLE(my_h_keyring_reader_object);

BEGIN_SERVICE_DEFINITION(keyring_reader_with_status)


DECLARE_BOOL_METHOD(init, (const char *data_id, const char *auth_id,
                           my_h_keyring_reader_object *reader_object));


DECLARE_BOOL_METHOD(deinit, (my_h_keyring_reader_object reader_object));


DECLARE_BOOL_METHOD(fetch_length, (my_h_keyring_reader_object reader_object,
                                   size_t *data_size, size_t *data_type_size));

DECLARE_BOOL_METHOD(fetch,
                    (my_h_keyring_reader_object reader_object,
                     unsigned char *data_buffer, size_t data_buffer_length,
                     size_t *data_size, char *data_type,
                     size_t data_type_buffer_length, size_t *data_type_size));

END_SERVICE_DEFINITION(keyring_reader_with_status)

#endif  // !KEYRING_READER_WITH_STATUS_INCLUDED

```






## 案例二


#### 现象
```bash
cd /root/8032-google/storage/ndb && /opt/rh/gcc-toolset-11/root/usr/bin/g++ -DBOOST_NO_CXX98_FUNCTION_BASE -DHAVE_CONFIG_H -DHAVE_NDB_CONFIG_H -DHAVE_TLSv13 -DLZ4_DISABLE_DEPRECATE_WARNINGS -DMYSQL_SERVER -DRAPIDJSON_NO_SIZETYPEDEFINE -DRAPIDJSON_SCHEMA_USE_INTERNALREGEX=0 -DRAPIDJSON_SCHEMA_USE_STDREGEX=1 -D_FILE_OFFSET_BITS=64 -D_GNU_SOURCE -D_USE_MATH_DEFINES -D__STDC_FORMAT_MACROS -D__STDC_LIMIT_MACROS -I/root/8032-google -I/root/8032-google/include -I/root/mysql-server-8032-google -I/root/mysql-server-8032-google/include -I/root/mysql-server-8032-google/storage/ndb/include -I/root/mysql-server-8032-google/storage/ndb/include/util -I/root/mysql-server-8032-google/storage/ndb/include/portlib -I/root/mysql-server-8032-google/storage/ndb/include/debugger -I/root/mysql-server-8032-google/storage/ndb/include/transporter -I/root/mysql-server-8032-google/storage/ndb/include/kernel -I/root/mysql-server-8032-google/storage/ndb/include/mgmapi -I/root/mysql-server-8032-google/storage/ndb/include/mgmcommon -I/root/mysql-server-8032-google/storage/ndb/include/ndbapi -I/root/mysql-server-8032-google/storage/ndb/include/logger -I/root/8032-google/storage/ndb/include -isystem /root/mysql-server-8032-google/extra/rapidjson/include -isystem /root/mysql-server-8032-google/extra/protobuf/protobuf-3.19.4/src -isystem /root/mysql-server-8032-google/extra/lz4/lz4-1.9.4/lib -isystem /root/mysql-server-8032-google/extra/libedit/libedit-20210910-3.1/src/editline -isystem /root/mysql-server-8032-google/extra/zstd/zstd-1.5.0/lib -isystem /root/8032-google/extra/zlib/zlib-1.2.13 -isystem /root/mysql-server-8032-google/extra/zlib/zlib-1.2.13 -std=c++17 -fno-omit-frame-pointer -ftls-model=initial-exec -B/opt/rh/gcc-toolset-11/root/usr/bin  -Wall -Wextra -Wformat-security -Wvla -Wundef -Wmissing-format-attribute -Woverloaded-virtual -Wcast-qual -Wimplicit-fallthrough=5 -Wstringop-truncation -Wsuggest-override -Wmissing-include-dirs -Wextra-semi -Wlogical-op -Werror -fno-strict-aliasing -DSAFE_MUTEX -DENABLED_DEBUG_SYNC -g -DACC_SAFE_QUEUE -DAPI_TRACE -DARRAY_GUARD -DERROR_INSERT -DNDB_DEBUG -DVM_TRACE -fPIC -MD -MT storage/ndb/CMakeFiles/ndbcluster.dir/plugin/ndb_dd_sdi.cc.o -MF CMakeFiles/ndbcluster.dir/plugin/ndb_dd_sdi.cc.o.d -o CMakeFiles/ndbcluster.dir/plugin/ndb_dd_sdi.cc.o -c /root/mysql-server-8032-google/storage/ndb/plugin/ndb_dd_sdi.cc
In file included from /root/mysql-server-8032-google/extra/rapidjson/include/rapidjson/document.h:20,
                 from /root/mysql-server-8032-google/storage/ndb/plugin/ndb_dd_sdi.cc:29:
/root/mysql-server-8032-google/extra/rapidjson/include/rapidjson/reader.h:211:35: error: ‘SizeType’ has not been declared
  211 |     bool RawNumber(const Ch* str, SizeType len, bool copy) { return static_cast<Override&>(*this).String(str, len, copy); }
      |                                   ^~~~~~~~
```

#### 分析
- `/root/mysql-server-8032-google/storage/ndb/plugin/ndb_dd_sdi.cc`
  - `/root/mysql-server-8032-google/extra/rapidjson/include/rapidjson/document.h`
    - `/root/mysql-server-8032-google/extra/rapidjson/include/rapidjson/reader.h`


- `SizeType`在`/root/mysql-server/extra/rapidjson/include/rapidjson/rapidjson.h`文件中包含了`SizeType`定义

- `/root/mysql-server-8032-google/extra/rapidjson/include/rapidjson/document.h:20`为：`#include "reader.h"`
- `/root/mysql-server-8032-google/storage/ndb/plugin/ndb_dd_sdi.cc:29`位置为：`#include "my_rapidjson_size_t.h"`
- 报错位置是：`/root/mysql-server-8032-google/extra/rapidjson/include/rapidjson/reader.h:211:35`，报错的是`SizeType`没有定义
- 核对`/root/mysql-server-8032-google/extra/rapidjson/include/rapidjson/reader.h`文件中是否有引入`/root/mysql-server/extra/rapidjson/include/rapidjson/rapidjson.h`文件：确定没有直接引入




- `/root/mysql-server-8032-google/extra/rapidjson/include/rapidjson/reader.h:211:35`，报错位置具体如下：
```c++
template<typename Encoding = UTF8<>, typename Derived = void>
struct BaseReaderHandler {
    typedef typename Encoding::Ch Ch;

    typedef typename internal::SelectIf<internal::IsSame<Derived, void>, BaseReaderHandler, Derived>::Type Override;

    bool Default() { return true; }
    bool Null() { return static_cast<Override&>(*this).Default(); }
    bool Bool(bool) { return static_cast<Override&>(*this).Default(); }
    bool Int(int) { return static_cast<Override&>(*this).Default(); }
    bool Uint(unsigned) { return static_cast<Override&>(*this).Default(); }
    bool Int64(int64_t) { return static_cast<Override&>(*this).Default(); }
    bool Uint64(uint64_t) { return static_cast<Override&>(*this).Default(); }
    bool Double(double) { return static_cast<Override&>(*this).Default(); }
    /// enabled via kParseNumbersAsStringsFlag, string is not null-terminated (use length)
    bool RawNumber(const Ch* str, SizeType len, bool copy) { return static_cast<Override&>(*this).String(str, len, copy); }
    bool String(const Ch*, SizeType, bool) { return static_cast<Override&>(*this).Default(); }
    bool StartObject() { return static_cast<Override&>(*this).Default(); }
    bool Key(const Ch* str, SizeType len, bool copy) { return static_cast<Override&>(*this).String(str, len, copy); }
    bool EndObject(SizeType) { return static_cast<Override&>(*this).Default(); }
    bool StartArray() { return static_cast<Override&>(*this).Default(); }
    bool EndArray(SizeType) { return static_cast<Override&>(*this).Default(); }
};
```


- `SizeType`在`/root/mysql-server/extra/rapidjson/include/rapidjson/rapidjson.h`文件中包含了`SizeType`定义：`typedef unsigned SizeType;`
MY_RAPIDJSON_SIZE_T
```c++
#ifndef RAPIDJSON_NO_SIZETYPEDEFINE
#ifdef RAPIDJSON_DOXYGEN_RUNNING
#define RAPIDJSON_NO_SIZETYPEDEFINE
#endif
RAPIDJSON_NAMESPACE_BEGIN

typedef unsigned SizeType;
RAPIDJSON_NAMESPACE_END
#endif
```

- `/root/mysql-server-8032-google/storage/ndb/plugin/ndb_dd_sdi.cc`
```c++
#include "storage/ndb/plugin/ndb_dd_sdi.h"

// Using
#include <rapidjson/document.h>      // rapidjson::Document
#include <rapidjson/prettywriter.h>  // rapidjson::PrettyWriter
#include <rapidjson/stringbuffer.h>
#include <rapidjson/writer.h>  // rapidjson::Writer

#include "my_rapidjson_size_t.h"  // IWYU pragma: keep
#include "my_sys.h"               // my_error
#include "mysql_version.h"        // MYSQL_VERSION_ID
#include "mysqld_error.h"         // ER_IMP_INCOMPATIBLE_MYSQLD_VERSION
#include "sql/dd/impl/sdi.h"
#include "sql/dd/sdi_fwd.h"
#include "sql/dd/string_type.h"


```


- `/root/mysql-server-8032-google/include/my_rapidjson_size_t.h`
```c++

#ifndef MY_RAPIDJSON_SIZE_T
#define MY_RAPIDJSON_SIZE_T

#ifdef __cplusplus

#include <cstddef>

namespace rapidjson {
typedef ::std::size_t SizeType;
}
#endif

#endif  // MY_RAPIDJSON_SIZE_T

```

- 将`/root/mysql-server-8032-google/storage/ndb/plugin/ndb_dd_sdi.cc`中的`#include "my_rapidjson_size_t.h"`放在`#include <rapidjson/document.h>`的前面


- 执行：`make VERBOSE=1`
```bash
[ 18%] Building CXX object storage/ndb/CMakeFiles/ndbcluster.dir/plugin/ndb_dd_sdi.cc.o
cd /root/8032-google/storage/ndb && /opt/rh/gcc-toolset-11/root/usr/bin/g++ -DBOOST_NO_CXX98_FUNCTION_BASE -DHAVE_CONFIG_H -DHAVE_NDB_CONFIG_H -DHAVE_TLSv13 -DLZ4_DISABLE_DEPRECATE_WARNINGS -DMYSQL_SERVER -DRAPIDJSON_NO_SIZETYPEDEFINE -DRAPIDJSON_SCHEMA_USE_INTERNALREGEX=0 -DRAPIDJSON_SCHEMA_USE_STDREGEX=1 -D_FILE_OFFSET_BITS=64 -D_GNU_SOURCE -D_USE_MATH_DEFINES -D__STDC_FORMAT_MACROS -D__STDC_LIMIT_MACROS -I/root/8032-google -I/root/8032-google/include -I/root/mysql-server-8032-google -I/root/mysql-server-8032-google/include -I/root/mysql-server-8032-google/storage/ndb/include -I/root/mysql-server-8032-google/storage/ndb/include/util -I/root/mysql-server-8032-google/storage/ndb/include/portlib -I/root/mysql-server-8032-google/storage/ndb/include/debugger -I/root/mysql-server-8032-google/storage/ndb/include/transporter -I/root/mysql-server-8032-google/storage/ndb/include/kernel -I/root/mysql-server-8032-google/storage/ndb/include/mgmapi -I/root/mysql-server-8032-google/storage/ndb/include/mgmcommon -I/root/mysql-server-8032-google/storage/ndb/include/ndbapi -I/root/mysql-server-8032-google/storage/ndb/include/logger -I/root/8032-google/storage/ndb/include -isystem /root/mysql-server-8032-google/extra/rapidjson/include -isystem /root/mysql-server-8032-google/extra/protobuf/protobuf-3.19.4/src -isystem /root/mysql-server-8032-google/extra/lz4/lz4-1.9.4/lib -isystem /root/mysql-server-8032-google/extra/libedit/libedit-20210910-3.1/src/editline -isystem /root/mysql-server-8032-google/extra/zstd/zstd-1.5.0/lib -isystem /root/8032-google/extra/zlib/zlib-1.2.13 -isystem /root/mysql-server-8032-google/extra/zlib/zlib-1.2.13 -std=c++17 -fno-omit-frame-pointer -ftls-model=initial-exec -B/opt/rh/gcc-toolset-11/root/usr/bin  -Wall -Wextra -Wformat-security -Wvla -Wundef -Wmissing-format-attribute -Woverloaded-virtual -Wcast-qual -Wimplicit-fallthrough=5 -Wstringop-truncation -Wsuggest-override -Wmissing-include-dirs -Wextra-semi -Wlogical-op -Werror -fno-strict-aliasing -DSAFE_MUTEX -DENABLED_DEBUG_SYNC -g -DACC_SAFE_QUEUE -DAPI_TRACE -DARRAY_GUARD -DERROR_INSERT -DNDB_DEBUG -DVM_TRACE -fPIC -MD -MT storage/ndb/CMakeFiles/ndbcluster.dir/plugin/ndb_dd_sdi.cc.o -MF CMakeFiles/ndbcluster.dir/plugin/ndb_dd_sdi.cc.o.d -o CMakeFiles/ndbcluster.dir/plugin/ndb_dd_sdi.cc.o -c /root/mysql-server-8032-google/storage/ndb/plugin/ndb_dd_sdi.cc
[ 18%] Building CXX object storage/ndb/CMakeFiles/ndbcluster.dir/plugin/ndb_plugin_reference.cc.o
cd /root/8032-google/storage/ndb && /opt/rh/gcc-toolset-11/root/usr/bin/g++ -DBOOST_NO_CXX98_FUNCTION_BASE -DHAVE_CONFIG_H -DHAVE_NDB_CONFIG_H -DHAVE_TLSv13 -DLZ4_DISABLE_DEPRECATE_WARNINGS -DMYSQL_SERVER -DRAPIDJSON_NO_SIZETYPEDEFINE -DRAPIDJSON_SCHEMA_USE_INTERNALREGEX=0 -DRAPIDJSON_SCHEMA_USE_STDREGEX=1 -D_FILE_OFFSET_BITS=64 -D_GNU_SOURCE -D_USE_MATH_DEFINES -D__STDC_FORMAT_MACROS -D__STDC_LIMIT_MACROS -I/root/8032-google -I/root/8032-google/include -I/root/mysql-server-8032-google -I/root/mysql-server-8032-google/include -I/root/mysql-server-8032-google/storage/ndb/include -I/root/mysql-server-8032-google/storage/ndb/include/util -I/root/mysql-server-8032-google/storage/ndb/include/portlib -I/root/mysql-server-8032-google/storage/ndb/include/debugger -I/root/mysql-server-8032-google/storage/ndb/include/transporter -I/root/mysql-server-8032-google/storage/ndb/include/kernel -I/root/mysql-server-8032-google/storage/ndb/include/mgmapi -I/root/mysql-server-8032-google/storage/ndb/include/mgmcommon -I/root/mysql-server-8032-google/storage/ndb/include/ndbapi -I/root/mysql-server-8032-google/storage/ndb/include/logger -I/root/8032-google/storage/ndb/include -isystem /root/mysql-server-8032-google/extra/rapidjson/include -isystem /root/mysql-server-8032-google/extra/protobuf/protobuf-3.19.4/src -isystem /root/mysql-server-8032-google/extra/lz4/lz4-1.9.4/lib -isystem /root/mysql-server-8032-google/extra/libedit/libedit-20210910-3.1/src/editline -isystem /root/mysql-server-8032-google/extra/zstd/zstd-1.5.0/lib -isystem /root/8032-google/extra/zlib/zlib-1.2.13 -isystem /root/mysql-server-8032-google/extra/zlib/zlib-1.2.13 -std=c++17 -fno-omit-frame-pointer -ftls-model=initial-exec -B/opt/rh/gcc-toolset-11/root/usr/bin  -Wall -Wextra -Wformat-security -Wvla -Wundef -Wmissing-format-attribute -Woverloaded-virtual -Wcast-qual -Wimplicit-fallthrough=5 -Wstringop-truncation -Wsuggest-override -Wmissing-include-dirs -Wextra-semi -Wlogical-op -Werror -fno-strict-aliasing -DSAFE_MUTEX -DENABLED_DEBUG_SYNC -g -DACC_SAFE_QUEUE -DAPI_TRACE -DARRAY_GUARD -DERROR_INSERT -DNDB_DEBUG -DVM_TRACE -fPIC -MD -MT storage/ndb/CMakeFiles/ndbcluster.dir/plugin/ndb_plugin_reference.cc.o -MF CMakeFiles/ndbcluster.dir/plugin/ndb_plugin_reference.cc.o.d -o CMakeFiles/ndbcluster.dir/plugin/ndb_plugin_reference.cc.o -c /root/mysql-server-8032-google/storage/ndb/plugin/ndb_plugin_reference.cc

```
可以看到，解决了问题





#### 原因

根本原因是头文件包含顺序错误

通常情况下，我们不要对别人的源码的头文件`include`进行排序，否则就会导致报错，因为大多数大型项目的源程序的头文件包含是有顺序的


#### 改进

- 将`/root/mysql-server-8032-google/storage/ndb/plugin/ndb_dd_sdi.cc`中的`#include "my_rapidjson_size_t.h"`放在`#include <rapidjson/document.h>`的前面
- 禁用`clang-format`中关于头文件排序的选项：`SortIncludes: false`







