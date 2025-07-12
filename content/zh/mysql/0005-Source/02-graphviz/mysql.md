---
title: mysql
description: mysql
date: 2023-11-17
weight: 300000


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{%pageinfo%}}
- [](https://huanglei.rocks/posts/add-math-and-dot-for-hugo/)
{{%/pageinfo%}}


## mysql
> `fontsize`默认值为`12`，这里设置为8



```viz-dot
digraph "mysql" {
node [
  fontsize = "12"
];
    "node333" [ label = "mysql", shape = egg ];
    "node1" [ label = "-lpthread", shape = septagon ];
    "node333" -> "node1" [ style = dotted ] // mysql -> -lpthread
    "node146" [ label = "client_base", shape = octagon ];
    "node333" -> "node146" [ style = dotted ] // mysql -> client_base
    "node1" [ label = "-lpthread", shape = septagon ];
    "node146" -> "node1"  // client_base -> -lpthread
    "node128" [ label = "mysqlclient", shape = octagon ];
    "node146" -> "node128"  // client_base -> mysqlclient
    "node1" [ label = "-lpthread", shape = septagon ];
    "node128" -> "node1"  // mysqlclient -> -lpthread
    "node4" [ label = "/usr/lib64/libcrypto.so", shape = septagon ];
    "node128" -> "node4"  // mysqlclient -> /usr/lib64/libcrypto.so
    "node129" [ label = "/usr/lib64/libresolv.so", shape = septagon ];
    "node128" -> "node129"  // mysqlclient -> /usr/lib64/libresolv.so
    "node5" [ label = "/usr/lib64/libssl.so", shape = septagon ];
    "node128" -> "node5"  // mysqlclient -> /usr/lib64/libssl.so
    "node130" [ label = "clientlib", shape = octagon ];
    "node128" -> "node130"  // mysqlclient -> clientlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node130" -> "node1"  // clientlib -> -lpthread
    "node131" [ label = "clientlib_objlib", shape = hexagon ];
    "node130" -> "node131"  // clientlib -> clientlib_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node131" -> "node1"  // clientlib_objlib -> -lpthread
    "node131" [ label = "clientlib_objlib", shape = hexagon ];
    "node128" -> "node131"  // mysqlclient -> clientlib_objlib
    "node6" [ label = "dl", shape = septagon ];
    "node128" -> "node6" [ style = dotted ] // mysqlclient -> dl
    "node7" [ label = "m", shape = septagon ];
    "node128" -> "node7" [ style = dotted ] // mysqlclient -> m
    "node3" [ label = "mysys", shape = octagon ];
    "node128" -> "node3"  // mysqlclient -> mysys
    "node1" [ label = "-lpthread", shape = septagon ];
    "node3" -> "node1"  // mysys -> -lpthread
    "node4" [ label = "/usr/lib64/libcrypto.so", shape = septagon ];
    "node3" -> "node4"  // mysys -> /usr/lib64/libcrypto.so
    "node5" [ label = "/usr/lib64/libssl.so", shape = septagon ];
    "node3" -> "node5"  // mysys -> /usr/lib64/libssl.so
    "node6" [ label = "dl", shape = septagon ];
    "node3" -> "node6"  // mysys -> dl
    "node7" [ label = "m", shape = septagon ];
    "node3" -> "node7"  // mysys -> m
    "node8" [ label = "mysys_objlib", shape = hexagon ];
    "node3" -> "node8"  // mysys -> mysys_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node8" -> "node1"  // mysys_objlib -> -lpthread
    "node9" [ label = "mytime", shape = octagon ];
    "node3" -> "node9"  // mysys -> mytime
    "node1" [ label = "-lpthread", shape = septagon ];
    "node9" -> "node1"  // mytime -> -lpthread
    "node10" [ label = "mytime_objlib", shape = hexagon ];
    "node9" -> "node10"  // mytime -> mytime_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node10" -> "node1"  // mytime_objlib -> -lpthread
    "node11" [ label = "rt", shape = septagon ];
    "node3" -> "node11"  // mysys -> rt
    "node12" [ label = "strings", shape = octagon ];
    "node3" -> "node12"  // mysys -> strings
    "node1" [ label = "-lpthread", shape = septagon ];
    "node12" -> "node1"  // strings -> -lpthread
    "node3" [ label = "mysys", shape = octagon ];
    "node12" -> "node3"  // strings -> mysys
    "node13" [ label = "strings_objlib", shape = hexagon ];
    "node12" -> "node13"  // strings -> strings_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node13" -> "node1"  // strings_objlib -> -lpthread
    "node14" [ label = "uca9dump", shape = egg ];
    "node13" -> "node14"  // strings_objlib -> uca9dump
    "node1" [ label = "-lpthread", shape = septagon ];
    "node14" -> "node1" [ style = dotted ] // uca9dump -> -lpthread
    "node15" [ label = "zlib", shape = octagon ];
    "node3" -> "node15"  // mysys -> zlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node15" -> "node1"  // zlib -> -lpthread
    "node16" [ label = "zlib_objlib", shape = hexagon ];
    "node15" -> "node16"  // zlib -> zlib_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node16" -> "node1"  // zlib_objlib -> -lpthread
    "node17" [ label = "zstd", shape = octagon ];
    "node3" -> "node17"  // mysys -> zstd
    "node1" [ label = "-lpthread", shape = septagon ];
    "node17" -> "node1"  // zstd -> -lpthread
    "node18" [ label = "zstd_objlib", shape = hexagon ];
    "node17" -> "node18"  // zstd -> zstd_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node18" -> "node1"  // zstd_objlib -> -lpthread
    "node8" [ label = "mysys_objlib", shape = hexagon ];
    "node128" -> "node8"  // mysqlclient -> mysys_objlib
    "node9" [ label = "mytime", shape = octagon ];
    "node128" -> "node9"  // mysqlclient -> mytime
    "node10" [ label = "mytime_objlib", shape = hexagon ];
    "node128" -> "node10"  // mysqlclient -> mytime_objlib
    "node11" [ label = "rt", shape = septagon ];
    "node128" -> "node11" [ style = dotted ] // mysqlclient -> rt
    "node12" [ label = "strings", shape = octagon ];
    "node128" -> "node12"  // mysqlclient -> strings
    "node13" [ label = "strings_objlib", shape = hexagon ];
    "node128" -> "node13"  // mysqlclient -> strings_objlib
    "node90" [ label = "vio", shape = octagon ];
    "node128" -> "node90"  // mysqlclient -> vio
    "node1" [ label = "-lpthread", shape = septagon ];
    "node90" -> "node1"  // vio -> -lpthread
    "node91" [ label = "vio_objlib", shape = hexagon ];
    "node90" -> "node91"  // vio -> vio_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node91" -> "node1"  // vio_objlib -> -lpthread
    "node91" [ label = "vio_objlib", shape = hexagon ];
    "node128" -> "node91"  // mysqlclient -> vio_objlib
    "node15" [ label = "zlib", shape = octagon ];
    "node128" -> "node15"  // mysqlclient -> zlib
    "node16" [ label = "zlib_objlib", shape = hexagon ];
    "node128" -> "node16"  // mysqlclient -> zlib_objlib
    "node17" [ label = "zstd", shape = octagon ];
    "node128" -> "node17"  // mysqlclient -> zstd
    "node18" [ label = "zstd_objlib", shape = hexagon ];
    "node128" -> "node18"  // mysqlclient -> zstd_objlib
    "node252" [ label = "edit", shape = octagon ];
    "node333" -> "node252" [ style = dotted ] // mysql -> edit
    "node1" [ label = "-lpthread", shape = septagon ];
    "node252" -> "node1"  // edit -> -lpthread
    "node253" [ label = "/usr/lib64/libcurses.so", shape = septagon ];
    "node252" -> "node253"  // edit -> /usr/lib64/libcurses.so
    "node128" [ label = "mysqlclient", shape = octagon ];
    "node333" -> "node128" [ style = dotted ] // mysql -> mysqlclient
}


```


## 源码

```bash
digraph "mysql" {
node [
  fontsize = "12"
];
    "node333" [ label = "mysql", shape = egg ];
    "node1" [ label = "-lpthread", shape = septagon ];
    "node333" -> "node1" [ style = dotted ] // mysql -> -lpthread
    "node146" [ label = "client_base", shape = octagon ];
    "node333" -> "node146" [ style = dotted ] // mysql -> client_base
    "node1" [ label = "-lpthread", shape = septagon ];
    "node146" -> "node1"  // client_base -> -lpthread
    "node128" [ label = "mysqlclient", shape = octagon ];
    "node146" -> "node128"  // client_base -> mysqlclient
    "node1" [ label = "-lpthread", shape = septagon ];
    "node128" -> "node1"  // mysqlclient -> -lpthread
    "node4" [ label = "/usr/lib64/libcrypto.so", shape = septagon ];
    "node128" -> "node4"  // mysqlclient -> /usr/lib64/libcrypto.so
    "node129" [ label = "/usr/lib64/libresolv.so", shape = septagon ];
    "node128" -> "node129"  // mysqlclient -> /usr/lib64/libresolv.so
    "node5" [ label = "/usr/lib64/libssl.so", shape = septagon ];
    "node128" -> "node5"  // mysqlclient -> /usr/lib64/libssl.so
    "node130" [ label = "clientlib", shape = octagon ];
    "node128" -> "node130"  // mysqlclient -> clientlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node130" -> "node1"  // clientlib -> -lpthread
    "node131" [ label = "clientlib_objlib", shape = hexagon ];
    "node130" -> "node131"  // clientlib -> clientlib_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node131" -> "node1"  // clientlib_objlib -> -lpthread
    "node131" [ label = "clientlib_objlib", shape = hexagon ];
    "node128" -> "node131"  // mysqlclient -> clientlib_objlib
    "node6" [ label = "dl", shape = septagon ];
    "node128" -> "node6" [ style = dotted ] // mysqlclient -> dl
    "node7" [ label = "m", shape = septagon ];
    "node128" -> "node7" [ style = dotted ] // mysqlclient -> m
    "node3" [ label = "mysys", shape = octagon ];
    "node128" -> "node3"  // mysqlclient -> mysys
    "node1" [ label = "-lpthread", shape = septagon ];
    "node3" -> "node1"  // mysys -> -lpthread
    "node4" [ label = "/usr/lib64/libcrypto.so", shape = septagon ];
    "node3" -> "node4"  // mysys -> /usr/lib64/libcrypto.so
    "node5" [ label = "/usr/lib64/libssl.so", shape = septagon ];
    "node3" -> "node5"  // mysys -> /usr/lib64/libssl.so
    "node6" [ label = "dl", shape = septagon ];
    "node3" -> "node6"  // mysys -> dl
    "node7" [ label = "m", shape = septagon ];
    "node3" -> "node7"  // mysys -> m
    "node8" [ label = "mysys_objlib", shape = hexagon ];
    "node3" -> "node8"  // mysys -> mysys_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node8" -> "node1"  // mysys_objlib -> -lpthread
    "node9" [ label = "mytime", shape = octagon ];
    "node3" -> "node9"  // mysys -> mytime
    "node1" [ label = "-lpthread", shape = septagon ];
    "node9" -> "node1"  // mytime -> -lpthread
    "node10" [ label = "mytime_objlib", shape = hexagon ];
    "node9" -> "node10"  // mytime -> mytime_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node10" -> "node1"  // mytime_objlib -> -lpthread
    "node11" [ label = "rt", shape = septagon ];
    "node3" -> "node11"  // mysys -> rt
    "node12" [ label = "strings", shape = octagon ];
    "node3" -> "node12"  // mysys -> strings
    "node1" [ label = "-lpthread", shape = septagon ];
    "node12" -> "node1"  // strings -> -lpthread
    "node3" [ label = "mysys", shape = octagon ];
    "node12" -> "node3"  // strings -> mysys
    "node13" [ label = "strings_objlib", shape = hexagon ];
    "node12" -> "node13"  // strings -> strings_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node13" -> "node1"  // strings_objlib -> -lpthread
    "node14" [ label = "uca9dump", shape = egg ];
    "node13" -> "node14"  // strings_objlib -> uca9dump
    "node1" [ label = "-lpthread", shape = septagon ];
    "node14" -> "node1" [ style = dotted ] // uca9dump -> -lpthread
    "node15" [ label = "zlib", shape = octagon ];
    "node3" -> "node15"  // mysys -> zlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node15" -> "node1"  // zlib -> -lpthread
    "node16" [ label = "zlib_objlib", shape = hexagon ];
    "node15" -> "node16"  // zlib -> zlib_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node16" -> "node1"  // zlib_objlib -> -lpthread
    "node17" [ label = "zstd", shape = octagon ];
    "node3" -> "node17"  // mysys -> zstd
    "node1" [ label = "-lpthread", shape = septagon ];
    "node17" -> "node1"  // zstd -> -lpthread
    "node18" [ label = "zstd_objlib", shape = hexagon ];
    "node17" -> "node18"  // zstd -> zstd_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node18" -> "node1"  // zstd_objlib -> -lpthread
    "node8" [ label = "mysys_objlib", shape = hexagon ];
    "node128" -> "node8"  // mysqlclient -> mysys_objlib
    "node9" [ label = "mytime", shape = octagon ];
    "node128" -> "node9"  // mysqlclient -> mytime
    "node10" [ label = "mytime_objlib", shape = hexagon ];
    "node128" -> "node10"  // mysqlclient -> mytime_objlib
    "node11" [ label = "rt", shape = septagon ];
    "node128" -> "node11" [ style = dotted ] // mysqlclient -> rt
    "node12" [ label = "strings", shape = octagon ];
    "node128" -> "node12"  // mysqlclient -> strings
    "node13" [ label = "strings_objlib", shape = hexagon ];
    "node128" -> "node13"  // mysqlclient -> strings_objlib
    "node90" [ label = "vio", shape = octagon ];
    "node128" -> "node90"  // mysqlclient -> vio
    "node1" [ label = "-lpthread", shape = septagon ];
    "node90" -> "node1"  // vio -> -lpthread
    "node91" [ label = "vio_objlib", shape = hexagon ];
    "node90" -> "node91"  // vio -> vio_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node91" -> "node1"  // vio_objlib -> -lpthread
    "node91" [ label = "vio_objlib", shape = hexagon ];
    "node128" -> "node91"  // mysqlclient -> vio_objlib
    "node15" [ label = "zlib", shape = octagon ];
    "node128" -> "node15"  // mysqlclient -> zlib
    "node16" [ label = "zlib_objlib", shape = hexagon ];
    "node128" -> "node16"  // mysqlclient -> zlib_objlib
    "node17" [ label = "zstd", shape = octagon ];
    "node128" -> "node17"  // mysqlclient -> zstd
    "node18" [ label = "zstd_objlib", shape = hexagon ];
    "node128" -> "node18"  // mysqlclient -> zstd_objlib
    "node252" [ label = "edit", shape = octagon ];
    "node333" -> "node252" [ style = dotted ] // mysql -> edit
    "node1" [ label = "-lpthread", shape = septagon ];
    "node252" -> "node1"  // edit -> -lpthread
    "node253" [ label = "/usr/lib64/libcurses.so", shape = septagon ];
    "node252" -> "node253"  // edit -> /usr/lib64/libcurses.so
    "node128" [ label = "mysqlclient", shape = octagon ];
    "node333" -> "node128" [ style = dotted ] // mysql -> mysqlclient
}

```


## cmake 构建

```cmake
include_directories(${CMAKE_SOURCE_DIR}/client/include)

add_library(
        client_base STATIC
        bool_option.cc
        char_array_option.cc
        disabled_option.cc
        i_option.cc
        password_option.cc
        simple_option.cc
        string_option.cc
        abstract_options_provider.cc
        composite_options_provider.cc
        debug_options.cc
        help_options.cc
        mysql_connection_options.cc
        ssl_options.cc
        abstract_program.cc
        abstract_connection_program.cc
        message_data.cc
        mysql_query_runner.cc
        show_variable_query_extractor.cc
        ${CMAKE_SOURCE_DIR}/sql-common/sql_string.cc
        ${CMAKE_SOURCE_DIR}/client/multi_factor_passwordopt-vars.cc)

target_link_libraries(client_base mysqlclient)
```




