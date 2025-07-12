---
title: innobase
description: innobase
date: 2023-11-17
weight: 3000


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


## innobase
> `fontsize`默认值为`12`，这里设置为8
 ```viz-dot
digraph "innobase" {
node [
  fontsize = "8"
];
    "node60" [ label = "innobase", shape = octagon ];
    "node1" [ label = "-lpthread", shape = septagon ];
    "node60" -> "node1"  // innobase -> -lpthread
    "node61" [ label = "aio", shape = septagon ];
    "node60" -> "node61"  // innobase -> aio
    "node62" [ label = "lz4_lib", shape = octagon ];
    "node60" -> "node62"  // innobase -> lz4_lib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node62" -> "node1"  // lz4_lib -> -lpthread
    "node63" [ label = "numa", shape = septagon ];
    "node60" -> "node63"  // innobase -> numa
    "node64" [ label = "sql_dd", shape = octagon ];
    "node60" -> "node64"  // innobase -> sql_dd
    "node1" [ label = "-lpthread", shape = septagon ];
    "node64" -> "node1"  // sql_dd -> -lpthread
    "node65" [ label = "sql_gis", shape = octagon ];
    "node64" -> "node65"  // sql_dd -> sql_gis
    "node1" [ label = "-lpthread", shape = septagon ];
    "node65" -> "node1"  // sql_gis -> -lpthread
    "node64" [ label = "sql_dd", shape = octagon ];
    "node65" -> "node64"  // sql_gis -> sql_dd
    "node49" [ label = "sql_main", shape = octagon ];
    "node65" -> "node49"  // sql_gis -> sql_main
    "node1" [ label = "-lpthread", shape = septagon ];
    "node49" -> "node1"  // sql_main -> -lpthread
    "node4" [ label = "/usr/lib64/libcrypto.so", shape = septagon ];
    "node49" -> "node4"  // sql_main -> /usr/lib64/libcrypto.so
    "node50" [ label = "/usr/lib64/libevent_core.so", shape = septagon ];
    "node49" -> "node50"  // sql_main -> /usr/lib64/libevent_core.so
    "node51" [ label = "/usr/lib64/libevent_extra.so", shape = septagon ];
    "node49" -> "node51"  // sql_main -> /usr/lib64/libevent_extra.so
    "node52" [ label = "/usr/lib64/libevent_openssl.so", shape = septagon ];
    "node49" -> "node52"  // sql_main -> /usr/lib64/libevent_openssl.so
    "node53" [ label = "/usr/lib64/libevent_pthreads.so", shape = septagon ];
    "node49" -> "node53"  // sql_main -> /usr/lib64/libevent_pthreads.so
    "node5" [ label = "/usr/lib64/libssl.so", shape = septagon ];
    "node49" -> "node5"  // sql_main -> /usr/lib64/libssl.so
    "node40" [ label = "/usr/local/lib/libicudata.so", shape = septagon ];
    "node49" -> "node40"  // sql_main -> /usr/local/lib/libicudata.so
    "node41" [ label = "/usr/local/lib/libicui18n.so", shape = septagon ];
    "node49" -> "node41"  // sql_main -> /usr/local/lib/libicui18n.so
    "node42" [ label = "/usr/local/lib/libicuio.so", shape = septagon ];
    "node49" -> "node42"  // sql_main -> /usr/local/lib/libicuio.so
    "node43" [ label = "/usr/local/lib/libicuuc.so", shape = septagon ];
    "node49" -> "node43"  // sql_main -> /usr/local/lib/libicuuc.so
    "node54" [ label = "archive", shape = octagon ];
    "node49" -> "node54"  // sql_main -> archive
    "node1" [ label = "-lpthread", shape = septagon ];
    "node54" -> "node1"  // archive -> -lpthread
    "node15" [ label = "zlib", shape = octagon ];
    "node54" -> "node15"  // archive -> zlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node15" -> "node1"  // zlib -> -lpthread
    "node16" [ label = "zlib_objlib", shape = hexagon ];
    "node15" -> "node16"  // zlib -> zlib_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node16" -> "node1"  // zlib_objlib -> -lpthread
    "node46" [ label = "binlog", shape = octagon ];
    "node49" -> "node46"  // sql_main -> binlog
    "node1" [ label = "-lpthread", shape = septagon ];
    "node46" -> "node1"  // binlog -> -lpthread
    "node47" [ label = "binlogevents_static", shape = octagon ];
    "node46" -> "node47"  // binlog -> binlogevents_static
    "node1" [ label = "-lpthread", shape = septagon ];
    "node47" -> "node1"  // binlogevents_static -> -lpthread
    "node3" [ label = "mysys", shape = octagon ];
    "node47" -> "node3"  // binlogevents_static -> mysys
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
    "node17" [ label = "zstd", shape = octagon ];
    "node3" -> "node17"  // mysys -> zstd
    "node1" [ label = "-lpthread", shape = septagon ];
    "node17" -> "node1"  // zstd -> -lpthread
    "node18" [ label = "zstd_objlib", shape = hexagon ];
    "node17" -> "node18"  // zstd -> zstd_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node18" -> "node1"  // zstd_objlib -> -lpthread
    "node17" [ label = "zstd", shape = octagon ];
    "node47" -> "node17"  // binlogevents_static -> zstd
    "node48" [ label = "rpl", shape = octagon ];
    "node46" -> "node48"  // binlog -> rpl
    "node1" [ label = "-lpthread", shape = septagon ];
    "node48" -> "node1"  // rpl -> -lpthread
    "node49" [ label = "sql_main", shape = octagon ];
    "node48" -> "node49"  // rpl -> sql_main
    "node47" [ label = "binlogevents_static", shape = octagon ];
    "node49" -> "node47"  // sql_main -> binlogevents_static
    "node55" [ label = "blackhole", shape = octagon ];
    "node49" -> "node55"  // sql_main -> blackhole
    "node1" [ label = "-lpthread", shape = septagon ];
    "node55" -> "node1"  // blackhole -> -lpthread
    "node56" [ label = "csv", shape = octagon ];
    "node49" -> "node56"  // sql_main -> csv
    "node1" [ label = "-lpthread", shape = septagon ];
    "node56" -> "node1"  // csv -> -lpthread
    "node6" [ label = "dl", shape = septagon ];
    "node49" -> "node6"  // sql_main -> dl
    "node57" [ label = "federated", shape = octagon ];
    "node49" -> "node57"  // sql_main -> federated
    "node1" [ label = "-lpthread", shape = septagon ];
    "node57" -> "node1"  // federated -> -lpthread
    "node39" [ label = "gen_keyword_list", shape = egg ];
    "node49" -> "node39"  // sql_main -> gen_keyword_list
    "node1" [ label = "-lpthread", shape = septagon ];
    "node39" -> "node1" [ style = dotted ] // gen_keyword_list -> -lpthread
    "node40" [ label = "/usr/local/lib/libicudata.so", shape = septagon ];
    "node39" -> "node40" [ style = dotted ] // gen_keyword_list -> /usr/local/lib/libicudata.so
    "node41" [ label = "/usr/local/lib/libicui18n.so", shape = septagon ];
    "node39" -> "node41" [ style = dotted ] // gen_keyword_list -> /usr/local/lib/libicui18n.so
    "node42" [ label = "/usr/local/lib/libicuio.so", shape = septagon ];
    "node39" -> "node42" [ style = dotted ] // gen_keyword_list -> /usr/local/lib/libicuio.so
    "node43" [ label = "/usr/local/lib/libicuuc.so", shape = septagon ];
    "node39" -> "node43" [ style = dotted ] // gen_keyword_list -> /usr/local/lib/libicuuc.so
    "node6" [ label = "dl", shape = septagon ];
    "node39" -> "node6" [ style = dotted ] // gen_keyword_list -> dl
    "node37" [ label = "gen_lex_hash", shape = egg ];
    "node49" -> "node37"  // sql_main -> gen_lex_hash
    "node1" [ label = "-lpthread", shape = septagon ];
    "node37" -> "node1" [ style = dotted ] // gen_lex_hash -> -lpthread
    "node36" [ label = "gen_lex_token", shape = egg ];
    "node49" -> "node36"  // sql_main -> gen_lex_token
    "node1" [ label = "-lpthread", shape = septagon ];
    "node36" -> "node1" [ style = dotted ] // gen_lex_token -> -lpthread
    "node58" [ label = "heap", shape = octagon ];
    "node49" -> "node58"  // sql_main -> heap
    "node1" [ label = "-lpthread", shape = septagon ];
    "node58" -> "node1"  // heap -> -lpthread
    "node59" [ label = "heap_library", shape = octagon ];
    "node58" -> "node59"  // heap -> heap_library
    "node1" [ label = "-lpthread", shape = septagon ];
    "node59" -> "node1"  // heap_library -> -lpthread
    "node3" [ label = "mysys", shape = octagon ];
    "node59" -> "node3"  // heap_library -> mysys
    "node59" [ label = "heap_library", shape = octagon ];
    "node49" -> "node59"  // sql_main -> heap_library
    "node60" [ label = "innobase", shape = octagon ];
    "node49" -> "node60"  // sql_main -> innobase
    "node66" [ label = "libprotobuf-lite\n(protobuf::libprotobuf-lite)", shape = doubleoctagon ];
    "node49" -> "node66"  // sql_main -> libprotobuf-lite
    "node1" [ label = "-lpthread", shape = septagon ];
    "node66" -> "node1"  // libprotobuf-lite -> -lpthread
    "node62" [ label = "lz4_lib", shape = octagon ];
    "node49" -> "node62"  // sql_main -> lz4_lib
    "node67" [ label = "master", shape = octagon ];
    "node49" -> "node67"  // sql_main -> master
    "node1" [ label = "-lpthread", shape = septagon ];
    "node67" -> "node1"  // master -> -lpthread
    "node46" [ label = "binlog", shape = octagon ];
    "node67" -> "node46"  // master -> binlog
    "node49" [ label = "sql_main", shape = octagon ];
    "node67" -> "node49"  // master -> sql_main
    "node68" [ label = "myisam", shape = octagon ];
    "node49" -> "node68"  // sql_main -> myisam
    "node1" [ label = "-lpthread", shape = septagon ];
    "node68" -> "node1"  // myisam -> -lpthread
    "node69" [ label = "myisam_library", shape = octagon ];
    "node68" -> "node69"  // myisam -> myisam_library
    "node1" [ label = "-lpthread", shape = septagon ];
    "node69" -> "node1"  // myisam_library -> -lpthread
    "node3" [ label = "mysys", shape = octagon ];
    "node69" -> "node3"  // myisam_library -> mysys
    "node69" [ label = "myisam_library", shape = octagon ];
    "node49" -> "node69"  // sql_main -> myisam_library
    "node70" [ label = "myisammrg", shape = octagon ];
    "node49" -> "node70"  // sql_main -> myisammrg
    "node1" [ label = "-lpthread", shape = septagon ];
    "node70" -> "node1"  // myisammrg -> -lpthread
    "node71" [ label = "mysql_server_component_services", shape = octagon ];
    "node49" -> "node71"  // sql_main -> mysql_server_component_services
    "node1" [ label = "-lpthread", shape = septagon ];
    "node71" -> "node1"  // mysql_server_component_services -> -lpthread
    "node72" [ label = "mysqlx", shape = octagon ];
    "node49" -> "node72"  // sql_main -> mysqlx
    "node1" [ label = "-lpthread", shape = septagon ];
    "node72" -> "node1"  // mysqlx -> -lpthread
    "node50" [ label = "/usr/lib64/libevent_core.so", shape = septagon ];
    "node72" -> "node50"  // mysqlx -> /usr/lib64/libevent_core.so
    "node51" [ label = "/usr/lib64/libevent_extra.so", shape = septagon ];
    "node72" -> "node51"  // mysqlx -> /usr/lib64/libevent_extra.so
    "node52" [ label = "/usr/lib64/libevent_openssl.so", shape = septagon ];
    "node72" -> "node52"  // mysqlx -> /usr/lib64/libevent_openssl.so
    "node53" [ label = "/usr/lib64/libevent_pthreads.so", shape = septagon ];
    "node72" -> "node53"  // mysqlx -> /usr/lib64/libevent_pthreads.so
    "node40" [ label = "/usr/local/lib/libicudata.so", shape = septagon ];
    "node72" -> "node40"  // mysqlx -> /usr/local/lib/libicudata.so
    "node41" [ label = "/usr/local/lib/libicui18n.so", shape = septagon ];
    "node72" -> "node41"  // mysqlx -> /usr/local/lib/libicui18n.so
    "node42" [ label = "/usr/local/lib/libicuio.so", shape = septagon ];
    "node72" -> "node42"  // mysqlx -> /usr/local/lib/libicuio.so
    "node43" [ label = "/usr/local/lib/libicuuc.so", shape = septagon ];
    "node72" -> "node43"  // mysqlx -> /usr/local/lib/libicuuc.so
    "node66" [ label = "libprotobuf-lite\n(protobuf::libprotobuf-lite)", shape = doubleoctagon ];
    "node72" -> "node66"  // mysqlx -> libprotobuf-lite
    "node62" [ label = "lz4_lib", shape = octagon ];
    "node72" -> "node62"  // mysqlx -> lz4_lib
    "node73" [ label = "mysqlxmessages_lite", shape = octagon ];
    "node72" -> "node73"  // mysqlx -> mysqlxmessages_lite
    "node1" [ label = "-lpthread", shape = septagon ];
    "node73" -> "node1"  // mysqlxmessages_lite -> -lpthread
    "node74" [ label = "protoc\n(protobuf::protoc)", shape = egg ];
    "node73" -> "node74"  // mysqlxmessages_lite -> protoc
    "node1" [ label = "-lpthread", shape = septagon ];
    "node74" -> "node1" [ style = dotted ] // protoc -> -lpthread
    "node75" [ label = "libprotobuf\n(protobuf::libprotobuf)", shape = doubleoctagon ];
    "node74" -> "node75" [ style = dotted ] // protoc -> libprotobuf
    "node1" [ label = "-lpthread", shape = septagon ];
    "node75" -> "node1"  // libprotobuf -> -lpthread
    "node15" [ label = "zlib", shape = octagon ];
    "node75" -> "node15"  // libprotobuf -> zlib
    "node76" [ label = "libprotoc\n(protobuf::libprotoc)", shape = doubleoctagon ];
    "node74" -> "node76" [ style = dotted ] // protoc -> libprotoc
    "node1" [ label = "-lpthread", shape = septagon ];
    "node76" -> "node1"  // libprotoc -> -lpthread
    "node75" [ label = "libprotobuf\n(protobuf::libprotobuf)", shape = doubleoctagon ];
    "node76" -> "node75"  // libprotoc -> libprotobuf
    "node73" [ label = "mysqlxmessages_lite", shape = octagon ];
    "node49" -> "node73"  // sql_main -> mysqlxmessages_lite
    "node3" [ label = "mysys", shape = octagon ];
    "node49" -> "node3"  // sql_main -> mysys
    "node79" [ label = "ndbclient_static", shape = octagon ];
    "node49" -> "node79"  // sql_main -> ndbclient_static
    "node1" [ label = "-lpthread", shape = septagon ];
    "node79" -> "node1"  // ndbclient_static -> -lpthread
    "node4" [ label = "/usr/lib64/libcrypto.so", shape = septagon ];
    "node79" -> "node4"  // ndbclient_static -> /usr/lib64/libcrypto.so
    "node5" [ label = "/usr/lib64/libssl.so", shape = septagon ];
    "node79" -> "node5"  // ndbclient_static -> /usr/lib64/libssl.so
    "node6" [ label = "dl", shape = septagon ];
    "node79" -> "node6" [ style = dotted ] // ndbclient_static -> dl
    "node7" [ label = "m", shape = septagon ];
    "node79" -> "node7" [ style = dotted ] // ndbclient_static -> m
    "node3" [ label = "mysys", shape = octagon ];
    "node79" -> "node3"  // ndbclient_static -> mysys
    "node8" [ label = "mysys_objlib", shape = hexagon ];
    "node79" -> "node8"  // ndbclient_static -> mysys_objlib
    "node9" [ label = "mytime", shape = octagon ];
    "node79" -> "node9"  // ndbclient_static -> mytime
    "node10" [ label = "mytime_objlib", shape = hexagon ];
    "node79" -> "node10"  // ndbclient_static -> mytime_objlib
    "node80" [ label = "ndbapi", shape = octagon ];
    "node79" -> "node80"  // ndbclient_static -> ndbapi
    "node1" [ label = "-lpthread", shape = septagon ];
    "node80" -> "node1"  // ndbapi -> -lpthread
    "node81" [ label = "ndbapi_objlib", shape = hexagon ];
    "node80" -> "node81"  // ndbapi -> ndbapi_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node81" -> "node1"  // ndbapi_objlib -> -lpthread
    "node81" [ label = "ndbapi_objlib", shape = hexagon ];
    "node79" -> "node81"  // ndbclient_static -> ndbapi_objlib
    "node19" [ label = "ndbgeneral", shape = octagon ];
    "node79" -> "node19"  // ndbclient_static -> ndbgeneral
    "node1" [ label = "-lpthread", shape = septagon ];
    "node19" -> "node1"  // ndbgeneral -> -lpthread
    "node3" [ label = "mysys", shape = octagon ];
    "node19" -> "node3"  // ndbgeneral -> mysys
    "node20" [ label = "ndbgeneral_objlib", shape = hexagon ];
    "node19" -> "node20"  // ndbgeneral -> ndbgeneral_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node20" -> "node1"  // ndbgeneral_objlib -> -lpthread
    "node21" [ label = "ndbportlib", shape = octagon ];
    "node19" -> "node21"  // ndbgeneral -> ndbportlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node21" -> "node1"  // ndbportlib -> -lpthread
    "node3" [ label = "mysys", shape = octagon ];
    "node21" -> "node3"  // ndbportlib -> mysys
    "node22" [ label = "ndbportlib_objlib", shape = hexagon ];
    "node21" -> "node22"  // ndbportlib -> ndbportlib_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node22" -> "node1"  // ndbportlib_objlib -> -lpthread
    "node23" [ label = "ndbtrace", shape = octagon ];
    "node19" -> "node23"  // ndbgeneral -> ndbtrace
    "node1" [ label = "-lpthread", shape = septagon ];
    "node23" -> "node1"  // ndbtrace -> -lpthread
    "node19" [ label = "ndbgeneral", shape = octagon ];
    "node23" -> "node19"  // ndbtrace -> ndbgeneral
    "node24" [ label = "ndblogger", shape = octagon ];
    "node23" -> "node24"  // ndbtrace -> ndblogger
    "node1" [ label = "-lpthread", shape = septagon ];
    "node24" -> "node1"  // ndblogger -> -lpthread
    "node19" [ label = "ndbgeneral", shape = octagon ];
    "node24" -> "node19"  // ndblogger -> ndbgeneral
    "node25" [ label = "ndblogger_objlib", shape = hexagon ];
    "node24" -> "node25"  // ndblogger -> ndblogger_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node25" -> "node1"  // ndblogger_objlib -> -lpthread
    "node26" [ label = "ndbmgmapi", shape = octagon ];
    "node23" -> "node26"  // ndbtrace -> ndbmgmapi
    "node1" [ label = "-lpthread", shape = septagon ];
    "node26" -> "node1"  // ndbmgmapi -> -lpthread
    "node19" [ label = "ndbgeneral", shape = octagon ];
    "node26" -> "node19"  // ndbmgmapi -> ndbgeneral
    "node27" [ label = "ndbmgmapi_objlib", shape = hexagon ];
    "node26" -> "node27"  // ndbmgmapi -> ndbmgmapi_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node27" -> "node1"  // ndbmgmapi_objlib -> -lpthread
    "node28" [ label = "ndbmgmcommon", shape = octagon ];
    "node26" -> "node28"  // ndbmgmapi -> ndbmgmcommon
    "node1" [ label = "-lpthread", shape = septagon ];
    "node28" -> "node1"  // ndbmgmcommon -> -lpthread
    "node26" [ label = "ndbmgmapi", shape = octagon ];
    "node28" -> "node26"  // ndbmgmcommon -> ndbmgmapi
    "node29" [ label = "ndbmgmcommon_objlib", shape = hexagon ];
    "node28" -> "node29"  // ndbmgmcommon -> ndbmgmcommon_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node29" -> "node1"  // ndbmgmcommon_objlib -> -lpthread
    "node21" [ label = "ndbportlib", shape = octagon ];
    "node26" -> "node21"  // ndbmgmapi -> ndbportlib
    "node30" [ label = "ndbsignaldata", shape = octagon ];
    "node23" -> "node30"  // ndbtrace -> ndbsignaldata
    "node1" [ label = "-lpthread", shape = septagon ];
    "node30" -> "node1"  // ndbsignaldata -> -lpthread
    "node31" [ label = "ndbsignaldata_objlib", shape = hexagon ];
    "node30" -> "node31"  // ndbsignaldata -> ndbsignaldata_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node31" -> "node1"  // ndbsignaldata_objlib -> -lpthread
    "node32" [ label = "ndbtrace_objlib", shape = hexagon ];
    "node23" -> "node32"  // ndbtrace -> ndbtrace_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node32" -> "node1"  // ndbtrace_objlib -> -lpthread
    "node15" [ label = "zlib", shape = octagon ];
    "node19" -> "node15"  // ndbgeneral -> zlib
    "node20" [ label = "ndbgeneral_objlib", shape = hexagon ];
    "node79" -> "node20"  // ndbclient_static -> ndbgeneral_objlib
    "node24" [ label = "ndblogger", shape = octagon ];
    "node79" -> "node24"  // ndbclient_static -> ndblogger
    "node25" [ label = "ndblogger_objlib", shape = hexagon ];
    "node79" -> "node25"  // ndbclient_static -> ndblogger_objlib
    "node26" [ label = "ndbmgmapi", shape = octagon ];
    "node79" -> "node26"  // ndbclient_static -> ndbmgmapi
    "node27" [ label = "ndbmgmapi_objlib", shape = hexagon ];
    "node79" -> "node27"  // ndbclient_static -> ndbmgmapi_objlib
    "node28" [ label = "ndbmgmcommon", shape = octagon ];
    "node79" -> "node28"  // ndbclient_static -> ndbmgmcommon
    "node29" [ label = "ndbmgmcommon_objlib", shape = hexagon ];
    "node79" -> "node29"  // ndbclient_static -> ndbmgmcommon_objlib
    "node21" [ label = "ndbportlib", shape = octagon ];
    "node79" -> "node21"  // ndbclient_static -> ndbportlib
    "node22" [ label = "ndbportlib_objlib", shape = hexagon ];
    "node79" -> "node22"  // ndbclient_static -> ndbportlib_objlib
    "node30" [ label = "ndbsignaldata", shape = octagon ];
    "node79" -> "node30"  // ndbclient_static -> ndbsignaldata
    "node31" [ label = "ndbsignaldata_objlib", shape = hexagon ];
    "node79" -> "node31"  // ndbclient_static -> ndbsignaldata_objlib
    "node23" [ label = "ndbtrace", shape = octagon ];
    "node79" -> "node23"  // ndbclient_static -> ndbtrace
    "node32" [ label = "ndbtrace_objlib", shape = hexagon ];
    "node79" -> "node32"  // ndbclient_static -> ndbtrace_objlib
    "node82" [ label = "ndbtransport", shape = octagon ];
    "node79" -> "node82"  // ndbclient_static -> ndbtransport
    "node1" [ label = "-lpthread", shape = septagon ];
    "node82" -> "node1"  // ndbtransport -> -lpthread
    "node83" [ label = "ndbtransport_objlib", shape = hexagon ];
    "node82" -> "node83"  // ndbtransport -> ndbtransport_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node83" -> "node1"  // ndbtransport_objlib -> -lpthread
    "node83" [ label = "ndbtransport_objlib", shape = hexagon ];
    "node79" -> "node83"  // ndbclient_static -> ndbtransport_objlib
    "node11" [ label = "rt", shape = septagon ];
    "node79" -> "node11" [ style = dotted ] // ndbclient_static -> rt
    "node12" [ label = "strings", shape = octagon ];
    "node79" -> "node12"  // ndbclient_static -> strings
    "node13" [ label = "strings_objlib", shape = hexagon ];
    "node79" -> "node13"  // ndbclient_static -> strings_objlib
    "node15" [ label = "zlib", shape = octagon ];
    "node79" -> "node15"  // ndbclient_static -> zlib
    "node16" [ label = "zlib_objlib", shape = hexagon ];
    "node79" -> "node16"  // ndbclient_static -> zlib_objlib
    "node84" [ label = "ndbcluster", shape = octagon ];
    "node49" -> "node84"  // sql_main -> ndbcluster
    "node1" [ label = "-lpthread", shape = septagon ];
    "node84" -> "node1"  // ndbcluster -> -lpthread
    "node79" [ label = "ndbclient_static", shape = octagon ];
    "node84" -> "node79"  // ndbcluster -> ndbclient_static
    "node85" [ label = "ngram_parser", shape = octagon ];
    "node49" -> "node85"  // sql_main -> ngram_parser
    "node1" [ label = "-lpthread", shape = septagon ];
    "node85" -> "node1"  // ngram_parser -> -lpthread
    "node63" [ label = "numa", shape = septagon ];
    "node49" -> "node63"  // sql_main -> numa
    "node86" [ label = "perfschema", shape = octagon ];
    "node49" -> "node86"  // sql_main -> perfschema
    "node1" [ label = "-lpthread", shape = septagon ];
    "node86" -> "node1"  // perfschema -> -lpthread
    "node48" [ label = "rpl", shape = octagon ];
    "node49" -> "node48"  // sql_main -> rpl
    "node87" [ label = "slave", shape = octagon ];
    "node49" -> "node87"  // sql_main -> slave
    "node1" [ label = "-lpthread", shape = septagon ];
    "node87" -> "node1"  // slave -> -lpthread
    "node46" [ label = "binlog", shape = octagon ];
    "node87" -> "node46"  // slave -> binlog
    "node66" [ label = "libprotobuf-lite\n(protobuf::libprotobuf-lite)", shape = doubleoctagon ];
    "node87" -> "node66"  // slave -> libprotobuf-lite
    "node88" [ label = "rpl_protobuf_lite", shape = octagon ];
    "node87" -> "node88"  // slave -> rpl_protobuf_lite
    "node1" [ label = "-lpthread", shape = septagon ];
    "node88" -> "node1"  // rpl_protobuf_lite -> -lpthread
    "node74" [ label = "protoc\n(protobuf::protoc)", shape = egg ];
    "node88" -> "node74"  // rpl_protobuf_lite -> protoc
    "node49" [ label = "sql_main", shape = octagon ];
    "node87" -> "node49"  // slave -> sql_main
    "node64" [ label = "sql_dd", shape = octagon ];
    "node49" -> "node64"  // sql_main -> sql_dd
    "node65" [ label = "sql_gis", shape = octagon ];
    "node49" -> "node65"  // sql_main -> sql_gis
    "node12" [ label = "strings", shape = octagon ];
    "node49" -> "node12"  // sql_main -> strings
    "node89" [ label = "temptable", shape = octagon ];
    "node49" -> "node89"  // sql_main -> temptable
    "node1" [ label = "-lpthread", shape = septagon ];
    "node89" -> "node1"  // temptable -> -lpthread
    "node90" [ label = "vio", shape = octagon ];
    "node49" -> "node90"  // sql_main -> vio
    "node1" [ label = "-lpthread", shape = septagon ];
    "node90" -> "node1"  // vio -> -lpthread
    "node91" [ label = "vio_objlib", shape = hexagon ];
    "node90" -> "node91"  // vio -> vio_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node91" -> "node1"  // vio_objlib -> -lpthread
    "node15" [ label = "zlib", shape = octagon ];
    "node49" -> "node15"  // sql_main -> zlib
    "node49" [ label = "sql_main", shape = octagon ];
    "node64" -> "node49"  // sql_dd -> sql_main
    "node65" [ label = "sql_gis", shape = octagon ];
    "node60" -> "node65"  // innobase -> sql_gis
    "node15" [ label = "zlib", shape = octagon ];
    "node60" -> "node15"  // innobase -> zlib
}

```

## dot源码
```bash
digraph "innobase" {
node [
  fontsize = "12"
];
    "node60" [ label = "innobase", shape = octagon ];
    "node1" [ label = "-lpthread", shape = septagon ];
    "node60" -> "node1"  // innobase -> -lpthread
    "node61" [ label = "aio", shape = septagon ];
    "node60" -> "node61"  // innobase -> aio
    "node62" [ label = "lz4_lib", shape = octagon ];
    "node60" -> "node62"  // innobase -> lz4_lib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node62" -> "node1"  // lz4_lib -> -lpthread
    "node63" [ label = "numa", shape = septagon ];
    "node60" -> "node63"  // innobase -> numa
    "node64" [ label = "sql_dd", shape = octagon ];
    "node60" -> "node64"  // innobase -> sql_dd
    "node1" [ label = "-lpthread", shape = septagon ];
    "node64" -> "node1"  // sql_dd -> -lpthread
    "node65" [ label = "sql_gis", shape = octagon ];
    "node64" -> "node65"  // sql_dd -> sql_gis
    "node1" [ label = "-lpthread", shape = septagon ];
    "node65" -> "node1"  // sql_gis -> -lpthread
    "node64" [ label = "sql_dd", shape = octagon ];
    "node65" -> "node64"  // sql_gis -> sql_dd
    "node49" [ label = "sql_main", shape = octagon ];
    "node65" -> "node49"  // sql_gis -> sql_main
    "node1" [ label = "-lpthread", shape = septagon ];
    "node49" -> "node1"  // sql_main -> -lpthread
    "node4" [ label = "/usr/lib64/libcrypto.so", shape = septagon ];
    "node49" -> "node4"  // sql_main -> /usr/lib64/libcrypto.so
    "node50" [ label = "/usr/lib64/libevent_core.so", shape = septagon ];
    "node49" -> "node50"  // sql_main -> /usr/lib64/libevent_core.so
    "node51" [ label = "/usr/lib64/libevent_extra.so", shape = septagon ];
    "node49" -> "node51"  // sql_main -> /usr/lib64/libevent_extra.so
    "node52" [ label = "/usr/lib64/libevent_openssl.so", shape = septagon ];
    "node49" -> "node52"  // sql_main -> /usr/lib64/libevent_openssl.so
    "node53" [ label = "/usr/lib64/libevent_pthreads.so", shape = septagon ];
    "node49" -> "node53"  // sql_main -> /usr/lib64/libevent_pthreads.so
    "node5" [ label = "/usr/lib64/libssl.so", shape = septagon ];
    "node49" -> "node5"  // sql_main -> /usr/lib64/libssl.so
    "node40" [ label = "/usr/local/lib/libicudata.so", shape = septagon ];
    "node49" -> "node40"  // sql_main -> /usr/local/lib/libicudata.so
    "node41" [ label = "/usr/local/lib/libicui18n.so", shape = septagon ];
    "node49" -> "node41"  // sql_main -> /usr/local/lib/libicui18n.so
    "node42" [ label = "/usr/local/lib/libicuio.so", shape = septagon ];
    "node49" -> "node42"  // sql_main -> /usr/local/lib/libicuio.so
    "node43" [ label = "/usr/local/lib/libicuuc.so", shape = septagon ];
    "node49" -> "node43"  // sql_main -> /usr/local/lib/libicuuc.so
    "node54" [ label = "archive", shape = octagon ];
    "node49" -> "node54"  // sql_main -> archive
    "node1" [ label = "-lpthread", shape = septagon ];
    "node54" -> "node1"  // archive -> -lpthread
    "node15" [ label = "zlib", shape = octagon ];
    "node54" -> "node15"  // archive -> zlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node15" -> "node1"  // zlib -> -lpthread
    "node16" [ label = "zlib_objlib", shape = hexagon ];
    "node15" -> "node16"  // zlib -> zlib_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node16" -> "node1"  // zlib_objlib -> -lpthread
    "node46" [ label = "binlog", shape = octagon ];
    "node49" -> "node46"  // sql_main -> binlog
    "node1" [ label = "-lpthread", shape = septagon ];
    "node46" -> "node1"  // binlog -> -lpthread
    "node47" [ label = "binlogevents_static", shape = octagon ];
    "node46" -> "node47"  // binlog -> binlogevents_static
    "node1" [ label = "-lpthread", shape = septagon ];
    "node47" -> "node1"  // binlogevents_static -> -lpthread
    "node3" [ label = "mysys", shape = octagon ];
    "node47" -> "node3"  // binlogevents_static -> mysys
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
    "node17" [ label = "zstd", shape = octagon ];
    "node3" -> "node17"  // mysys -> zstd
    "node1" [ label = "-lpthread", shape = septagon ];
    "node17" -> "node1"  // zstd -> -lpthread
    "node18" [ label = "zstd_objlib", shape = hexagon ];
    "node17" -> "node18"  // zstd -> zstd_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node18" -> "node1"  // zstd_objlib -> -lpthread
    "node17" [ label = "zstd", shape = octagon ];
    "node47" -> "node17"  // binlogevents_static -> zstd
    "node48" [ label = "rpl", shape = octagon ];
    "node46" -> "node48"  // binlog -> rpl
    "node1" [ label = "-lpthread", shape = septagon ];
    "node48" -> "node1"  // rpl -> -lpthread
    "node49" [ label = "sql_main", shape = octagon ];
    "node48" -> "node49"  // rpl -> sql_main
    "node47" [ label = "binlogevents_static", shape = octagon ];
    "node49" -> "node47"  // sql_main -> binlogevents_static
    "node55" [ label = "blackhole", shape = octagon ];
    "node49" -> "node55"  // sql_main -> blackhole
    "node1" [ label = "-lpthread", shape = septagon ];
    "node55" -> "node1"  // blackhole -> -lpthread
    "node56" [ label = "csv", shape = octagon ];
    "node49" -> "node56"  // sql_main -> csv
    "node1" [ label = "-lpthread", shape = septagon ];
    "node56" -> "node1"  // csv -> -lpthread
    "node6" [ label = "dl", shape = septagon ];
    "node49" -> "node6"  // sql_main -> dl
    "node57" [ label = "federated", shape = octagon ];
    "node49" -> "node57"  // sql_main -> federated
    "node1" [ label = "-lpthread", shape = septagon ];
    "node57" -> "node1"  // federated -> -lpthread
    "node39" [ label = "gen_keyword_list", shape = egg ];
    "node49" -> "node39"  // sql_main -> gen_keyword_list
    "node1" [ label = "-lpthread", shape = septagon ];
    "node39" -> "node1" [ style = dotted ] // gen_keyword_list -> -lpthread
    "node40" [ label = "/usr/local/lib/libicudata.so", shape = septagon ];
    "node39" -> "node40" [ style = dotted ] // gen_keyword_list -> /usr/local/lib/libicudata.so
    "node41" [ label = "/usr/local/lib/libicui18n.so", shape = septagon ];
    "node39" -> "node41" [ style = dotted ] // gen_keyword_list -> /usr/local/lib/libicui18n.so
    "node42" [ label = "/usr/local/lib/libicuio.so", shape = septagon ];
    "node39" -> "node42" [ style = dotted ] // gen_keyword_list -> /usr/local/lib/libicuio.so
    "node43" [ label = "/usr/local/lib/libicuuc.so", shape = septagon ];
    "node39" -> "node43" [ style = dotted ] // gen_keyword_list -> /usr/local/lib/libicuuc.so
    "node6" [ label = "dl", shape = septagon ];
    "node39" -> "node6" [ style = dotted ] // gen_keyword_list -> dl
    "node37" [ label = "gen_lex_hash", shape = egg ];
    "node49" -> "node37"  // sql_main -> gen_lex_hash
    "node1" [ label = "-lpthread", shape = septagon ];
    "node37" -> "node1" [ style = dotted ] // gen_lex_hash -> -lpthread
    "node36" [ label = "gen_lex_token", shape = egg ];
    "node49" -> "node36"  // sql_main -> gen_lex_token
    "node1" [ label = "-lpthread", shape = septagon ];
    "node36" -> "node1" [ style = dotted ] // gen_lex_token -> -lpthread
    "node58" [ label = "heap", shape = octagon ];
    "node49" -> "node58"  // sql_main -> heap
    "node1" [ label = "-lpthread", shape = septagon ];
    "node58" -> "node1"  // heap -> -lpthread
    "node59" [ label = "heap_library", shape = octagon ];
    "node58" -> "node59"  // heap -> heap_library
    "node1" [ label = "-lpthread", shape = septagon ];
    "node59" -> "node1"  // heap_library -> -lpthread
    "node3" [ label = "mysys", shape = octagon ];
    "node59" -> "node3"  // heap_library -> mysys
    "node59" [ label = "heap_library", shape = octagon ];
    "node49" -> "node59"  // sql_main -> heap_library
    "node60" [ label = "innobase", shape = octagon ];
    "node49" -> "node60"  // sql_main -> innobase
    "node66" [ label = "libprotobuf-lite\n(protobuf::libprotobuf-lite)", shape = doubleoctagon ];
    "node49" -> "node66"  // sql_main -> libprotobuf-lite
    "node1" [ label = "-lpthread", shape = septagon ];
    "node66" -> "node1"  // libprotobuf-lite -> -lpthread
    "node62" [ label = "lz4_lib", shape = octagon ];
    "node49" -> "node62"  // sql_main -> lz4_lib
    "node67" [ label = "master", shape = octagon ];
    "node49" -> "node67"  // sql_main -> master
    "node1" [ label = "-lpthread", shape = septagon ];
    "node67" -> "node1"  // master -> -lpthread
    "node46" [ label = "binlog", shape = octagon ];
    "node67" -> "node46"  // master -> binlog
    "node49" [ label = "sql_main", shape = octagon ];
    "node67" -> "node49"  // master -> sql_main
    "node68" [ label = "myisam", shape = octagon ];
    "node49" -> "node68"  // sql_main -> myisam
    "node1" [ label = "-lpthread", shape = septagon ];
    "node68" -> "node1"  // myisam -> -lpthread
    "node69" [ label = "myisam_library", shape = octagon ];
    "node68" -> "node69"  // myisam -> myisam_library
    "node1" [ label = "-lpthread", shape = septagon ];
    "node69" -> "node1"  // myisam_library -> -lpthread
    "node3" [ label = "mysys", shape = octagon ];
    "node69" -> "node3"  // myisam_library -> mysys
    "node69" [ label = "myisam_library", shape = octagon ];
    "node49" -> "node69"  // sql_main -> myisam_library
    "node70" [ label = "myisammrg", shape = octagon ];
    "node49" -> "node70"  // sql_main -> myisammrg
    "node1" [ label = "-lpthread", shape = septagon ];
    "node70" -> "node1"  // myisammrg -> -lpthread
    "node71" [ label = "mysql_server_component_services", shape = octagon ];
    "node49" -> "node71"  // sql_main -> mysql_server_component_services
    "node1" [ label = "-lpthread", shape = septagon ];
    "node71" -> "node1"  // mysql_server_component_services -> -lpthread
    "node72" [ label = "mysqlx", shape = octagon ];
    "node49" -> "node72"  // sql_main -> mysqlx
    "node1" [ label = "-lpthread", shape = septagon ];
    "node72" -> "node1"  // mysqlx -> -lpthread
    "node50" [ label = "/usr/lib64/libevent_core.so", shape = septagon ];
    "node72" -> "node50"  // mysqlx -> /usr/lib64/libevent_core.so
    "node51" [ label = "/usr/lib64/libevent_extra.so", shape = septagon ];
    "node72" -> "node51"  // mysqlx -> /usr/lib64/libevent_extra.so
    "node52" [ label = "/usr/lib64/libevent_openssl.so", shape = septagon ];
    "node72" -> "node52"  // mysqlx -> /usr/lib64/libevent_openssl.so
    "node53" [ label = "/usr/lib64/libevent_pthreads.so", shape = septagon ];
    "node72" -> "node53"  // mysqlx -> /usr/lib64/libevent_pthreads.so
    "node40" [ label = "/usr/local/lib/libicudata.so", shape = septagon ];
    "node72" -> "node40"  // mysqlx -> /usr/local/lib/libicudata.so
    "node41" [ label = "/usr/local/lib/libicui18n.so", shape = septagon ];
    "node72" -> "node41"  // mysqlx -> /usr/local/lib/libicui18n.so
    "node42" [ label = "/usr/local/lib/libicuio.so", shape = septagon ];
    "node72" -> "node42"  // mysqlx -> /usr/local/lib/libicuio.so
    "node43" [ label = "/usr/local/lib/libicuuc.so", shape = septagon ];
    "node72" -> "node43"  // mysqlx -> /usr/local/lib/libicuuc.so
    "node66" [ label = "libprotobuf-lite\n(protobuf::libprotobuf-lite)", shape = doubleoctagon ];
    "node72" -> "node66"  // mysqlx -> libprotobuf-lite
    "node62" [ label = "lz4_lib", shape = octagon ];
    "node72" -> "node62"  // mysqlx -> lz4_lib
    "node73" [ label = "mysqlxmessages_lite", shape = octagon ];
    "node72" -> "node73"  // mysqlx -> mysqlxmessages_lite
    "node1" [ label = "-lpthread", shape = septagon ];
    "node73" -> "node1"  // mysqlxmessages_lite -> -lpthread
    "node74" [ label = "protoc\n(protobuf::protoc)", shape = egg ];
    "node73" -> "node74"  // mysqlxmessages_lite -> protoc
    "node1" [ label = "-lpthread", shape = septagon ];
    "node74" -> "node1" [ style = dotted ] // protoc -> -lpthread
    "node75" [ label = "libprotobuf\n(protobuf::libprotobuf)", shape = doubleoctagon ];
    "node74" -> "node75" [ style = dotted ] // protoc -> libprotobuf
    "node1" [ label = "-lpthread", shape = septagon ];
    "node75" -> "node1"  // libprotobuf -> -lpthread
    "node15" [ label = "zlib", shape = octagon ];
    "node75" -> "node15"  // libprotobuf -> zlib
    "node76" [ label = "libprotoc\n(protobuf::libprotoc)", shape = doubleoctagon ];
    "node74" -> "node76" [ style = dotted ] // protoc -> libprotoc
    "node1" [ label = "-lpthread", shape = septagon ];
    "node76" -> "node1"  // libprotoc -> -lpthread
    "node75" [ label = "libprotobuf\n(protobuf::libprotobuf)", shape = doubleoctagon ];
    "node76" -> "node75"  // libprotoc -> libprotobuf
    "node73" [ label = "mysqlxmessages_lite", shape = octagon ];
    "node49" -> "node73"  // sql_main -> mysqlxmessages_lite
    "node3" [ label = "mysys", shape = octagon ];
    "node49" -> "node3"  // sql_main -> mysys
    "node79" [ label = "ndbclient_static", shape = octagon ];
    "node49" -> "node79"  // sql_main -> ndbclient_static
    "node1" [ label = "-lpthread", shape = septagon ];
    "node79" -> "node1"  // ndbclient_static -> -lpthread
    "node4" [ label = "/usr/lib64/libcrypto.so", shape = septagon ];
    "node79" -> "node4"  // ndbclient_static -> /usr/lib64/libcrypto.so
    "node5" [ label = "/usr/lib64/libssl.so", shape = septagon ];
    "node79" -> "node5"  // ndbclient_static -> /usr/lib64/libssl.so
    "node6" [ label = "dl", shape = septagon ];
    "node79" -> "node6" [ style = dotted ] // ndbclient_static -> dl
    "node7" [ label = "m", shape = septagon ];
    "node79" -> "node7" [ style = dotted ] // ndbclient_static -> m
    "node3" [ label = "mysys", shape = octagon ];
    "node79" -> "node3"  // ndbclient_static -> mysys
    "node8" [ label = "mysys_objlib", shape = hexagon ];
    "node79" -> "node8"  // ndbclient_static -> mysys_objlib
    "node9" [ label = "mytime", shape = octagon ];
    "node79" -> "node9"  // ndbclient_static -> mytime
    "node10" [ label = "mytime_objlib", shape = hexagon ];
    "node79" -> "node10"  // ndbclient_static -> mytime_objlib
    "node80" [ label = "ndbapi", shape = octagon ];
    "node79" -> "node80"  // ndbclient_static -> ndbapi
    "node1" [ label = "-lpthread", shape = septagon ];
    "node80" -> "node1"  // ndbapi -> -lpthread
    "node81" [ label = "ndbapi_objlib", shape = hexagon ];
    "node80" -> "node81"  // ndbapi -> ndbapi_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node81" -> "node1"  // ndbapi_objlib -> -lpthread
    "node81" [ label = "ndbapi_objlib", shape = hexagon ];
    "node79" -> "node81"  // ndbclient_static -> ndbapi_objlib
    "node19" [ label = "ndbgeneral", shape = octagon ];
    "node79" -> "node19"  // ndbclient_static -> ndbgeneral
    "node1" [ label = "-lpthread", shape = septagon ];
    "node19" -> "node1"  // ndbgeneral -> -lpthread
    "node3" [ label = "mysys", shape = octagon ];
    "node19" -> "node3"  // ndbgeneral -> mysys
    "node20" [ label = "ndbgeneral_objlib", shape = hexagon ];
    "node19" -> "node20"  // ndbgeneral -> ndbgeneral_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node20" -> "node1"  // ndbgeneral_objlib -> -lpthread
    "node21" [ label = "ndbportlib", shape = octagon ];
    "node19" -> "node21"  // ndbgeneral -> ndbportlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node21" -> "node1"  // ndbportlib -> -lpthread
    "node3" [ label = "mysys", shape = octagon ];
    "node21" -> "node3"  // ndbportlib -> mysys
    "node22" [ label = "ndbportlib_objlib", shape = hexagon ];
    "node21" -> "node22"  // ndbportlib -> ndbportlib_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node22" -> "node1"  // ndbportlib_objlib -> -lpthread
    "node23" [ label = "ndbtrace", shape = octagon ];
    "node19" -> "node23"  // ndbgeneral -> ndbtrace
    "node1" [ label = "-lpthread", shape = septagon ];
    "node23" -> "node1"  // ndbtrace -> -lpthread
    "node19" [ label = "ndbgeneral", shape = octagon ];
    "node23" -> "node19"  // ndbtrace -> ndbgeneral
    "node24" [ label = "ndblogger", shape = octagon ];
    "node23" -> "node24"  // ndbtrace -> ndblogger
    "node1" [ label = "-lpthread", shape = septagon ];
    "node24" -> "node1"  // ndblogger -> -lpthread
    "node19" [ label = "ndbgeneral", shape = octagon ];
    "node24" -> "node19"  // ndblogger -> ndbgeneral
    "node25" [ label = "ndblogger_objlib", shape = hexagon ];
    "node24" -> "node25"  // ndblogger -> ndblogger_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node25" -> "node1"  // ndblogger_objlib -> -lpthread
    "node26" [ label = "ndbmgmapi", shape = octagon ];
    "node23" -> "node26"  // ndbtrace -> ndbmgmapi
    "node1" [ label = "-lpthread", shape = septagon ];
    "node26" -> "node1"  // ndbmgmapi -> -lpthread
    "node19" [ label = "ndbgeneral", shape = octagon ];
    "node26" -> "node19"  // ndbmgmapi -> ndbgeneral
    "node27" [ label = "ndbmgmapi_objlib", shape = hexagon ];
    "node26" -> "node27"  // ndbmgmapi -> ndbmgmapi_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node27" -> "node1"  // ndbmgmapi_objlib -> -lpthread
    "node28" [ label = "ndbmgmcommon", shape = octagon ];
    "node26" -> "node28"  // ndbmgmapi -> ndbmgmcommon
    "node1" [ label = "-lpthread", shape = septagon ];
    "node28" -> "node1"  // ndbmgmcommon -> -lpthread
    "node26" [ label = "ndbmgmapi", shape = octagon ];
    "node28" -> "node26"  // ndbmgmcommon -> ndbmgmapi
    "node29" [ label = "ndbmgmcommon_objlib", shape = hexagon ];
    "node28" -> "node29"  // ndbmgmcommon -> ndbmgmcommon_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node29" -> "node1"  // ndbmgmcommon_objlib -> -lpthread
    "node21" [ label = "ndbportlib", shape = octagon ];
    "node26" -> "node21"  // ndbmgmapi -> ndbportlib
    "node30" [ label = "ndbsignaldata", shape = octagon ];
    "node23" -> "node30"  // ndbtrace -> ndbsignaldata
    "node1" [ label = "-lpthread", shape = septagon ];
    "node30" -> "node1"  // ndbsignaldata -> -lpthread
    "node31" [ label = "ndbsignaldata_objlib", shape = hexagon ];
    "node30" -> "node31"  // ndbsignaldata -> ndbsignaldata_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node31" -> "node1"  // ndbsignaldata_objlib -> -lpthread
    "node32" [ label = "ndbtrace_objlib", shape = hexagon ];
    "node23" -> "node32"  // ndbtrace -> ndbtrace_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node32" -> "node1"  // ndbtrace_objlib -> -lpthread
    "node15" [ label = "zlib", shape = octagon ];
    "node19" -> "node15"  // ndbgeneral -> zlib
    "node20" [ label = "ndbgeneral_objlib", shape = hexagon ];
    "node79" -> "node20"  // ndbclient_static -> ndbgeneral_objlib
    "node24" [ label = "ndblogger", shape = octagon ];
    "node79" -> "node24"  // ndbclient_static -> ndblogger
    "node25" [ label = "ndblogger_objlib", shape = hexagon ];
    "node79" -> "node25"  // ndbclient_static -> ndblogger_objlib
    "node26" [ label = "ndbmgmapi", shape = octagon ];
    "node79" -> "node26"  // ndbclient_static -> ndbmgmapi
    "node27" [ label = "ndbmgmapi_objlib", shape = hexagon ];
    "node79" -> "node27"  // ndbclient_static -> ndbmgmapi_objlib
    "node28" [ label = "ndbmgmcommon", shape = octagon ];
    "node79" -> "node28"  // ndbclient_static -> ndbmgmcommon
    "node29" [ label = "ndbmgmcommon_objlib", shape = hexagon ];
    "node79" -> "node29"  // ndbclient_static -> ndbmgmcommon_objlib
    "node21" [ label = "ndbportlib", shape = octagon ];
    "node79" -> "node21"  // ndbclient_static -> ndbportlib
    "node22" [ label = "ndbportlib_objlib", shape = hexagon ];
    "node79" -> "node22"  // ndbclient_static -> ndbportlib_objlib
    "node30" [ label = "ndbsignaldata", shape = octagon ];
    "node79" -> "node30"  // ndbclient_static -> ndbsignaldata
    "node31" [ label = "ndbsignaldata_objlib", shape = hexagon ];
    "node79" -> "node31"  // ndbclient_static -> ndbsignaldata_objlib
    "node23" [ label = "ndbtrace", shape = octagon ];
    "node79" -> "node23"  // ndbclient_static -> ndbtrace
    "node32" [ label = "ndbtrace_objlib", shape = hexagon ];
    "node79" -> "node32"  // ndbclient_static -> ndbtrace_objlib
    "node82" [ label = "ndbtransport", shape = octagon ];
    "node79" -> "node82"  // ndbclient_static -> ndbtransport
    "node1" [ label = "-lpthread", shape = septagon ];
    "node82" -> "node1"  // ndbtransport -> -lpthread
    "node83" [ label = "ndbtransport_objlib", shape = hexagon ];
    "node82" -> "node83"  // ndbtransport -> ndbtransport_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node83" -> "node1"  // ndbtransport_objlib -> -lpthread
    "node83" [ label = "ndbtransport_objlib", shape = hexagon ];
    "node79" -> "node83"  // ndbclient_static -> ndbtransport_objlib
    "node11" [ label = "rt", shape = septagon ];
    "node79" -> "node11" [ style = dotted ] // ndbclient_static -> rt
    "node12" [ label = "strings", shape = octagon ];
    "node79" -> "node12"  // ndbclient_static -> strings
    "node13" [ label = "strings_objlib", shape = hexagon ];
    "node79" -> "node13"  // ndbclient_static -> strings_objlib
    "node15" [ label = "zlib", shape = octagon ];
    "node79" -> "node15"  // ndbclient_static -> zlib
    "node16" [ label = "zlib_objlib", shape = hexagon ];
    "node79" -> "node16"  // ndbclient_static -> zlib_objlib
    "node84" [ label = "ndbcluster", shape = octagon ];
    "node49" -> "node84"  // sql_main -> ndbcluster
    "node1" [ label = "-lpthread", shape = septagon ];
    "node84" -> "node1"  // ndbcluster -> -lpthread
    "node79" [ label = "ndbclient_static", shape = octagon ];
    "node84" -> "node79"  // ndbcluster -> ndbclient_static
    "node85" [ label = "ngram_parser", shape = octagon ];
    "node49" -> "node85"  // sql_main -> ngram_parser
    "node1" [ label = "-lpthread", shape = septagon ];
    "node85" -> "node1"  // ngram_parser -> -lpthread
    "node63" [ label = "numa", shape = septagon ];
    "node49" -> "node63"  // sql_main -> numa
    "node86" [ label = "perfschema", shape = octagon ];
    "node49" -> "node86"  // sql_main -> perfschema
    "node1" [ label = "-lpthread", shape = septagon ];
    "node86" -> "node1"  // perfschema -> -lpthread
    "node48" [ label = "rpl", shape = octagon ];
    "node49" -> "node48"  // sql_main -> rpl
    "node87" [ label = "slave", shape = octagon ];
    "node49" -> "node87"  // sql_main -> slave
    "node1" [ label = "-lpthread", shape = septagon ];
    "node87" -> "node1"  // slave -> -lpthread
    "node46" [ label = "binlog", shape = octagon ];
    "node87" -> "node46"  // slave -> binlog
    "node66" [ label = "libprotobuf-lite\n(protobuf::libprotobuf-lite)", shape = doubleoctagon ];
    "node87" -> "node66"  // slave -> libprotobuf-lite
    "node88" [ label = "rpl_protobuf_lite", shape = octagon ];
    "node87" -> "node88"  // slave -> rpl_protobuf_lite
    "node1" [ label = "-lpthread", shape = septagon ];
    "node88" -> "node1"  // rpl_protobuf_lite -> -lpthread
    "node74" [ label = "protoc\n(protobuf::protoc)", shape = egg ];
    "node88" -> "node74"  // rpl_protobuf_lite -> protoc
    "node49" [ label = "sql_main", shape = octagon ];
    "node87" -> "node49"  // slave -> sql_main
    "node64" [ label = "sql_dd", shape = octagon ];
    "node49" -> "node64"  // sql_main -> sql_dd
    "node65" [ label = "sql_gis", shape = octagon ];
    "node49" -> "node65"  // sql_main -> sql_gis
    "node12" [ label = "strings", shape = octagon ];
    "node49" -> "node12"  // sql_main -> strings
    "node89" [ label = "temptable", shape = octagon ];
    "node49" -> "node89"  // sql_main -> temptable
    "node1" [ label = "-lpthread", shape = septagon ];
    "node89" -> "node1"  // temptable -> -lpthread
    "node90" [ label = "vio", shape = octagon ];
    "node49" -> "node90"  // sql_main -> vio
    "node1" [ label = "-lpthread", shape = septagon ];
    "node90" -> "node1"  // vio -> -lpthread
    "node91" [ label = "vio_objlib", shape = hexagon ];
    "node90" -> "node91"  // vio -> vio_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node91" -> "node1"  // vio_objlib -> -lpthread
    "node15" [ label = "zlib", shape = octagon ];
    "node49" -> "node15"  // sql_main -> zlib
    "node49" [ label = "sql_main", shape = octagon ];
    "node64" -> "node49"  // sql_dd -> sql_main
    "node65" [ label = "sql_gis", shape = octagon ];
    "node60" -> "node65"  // innobase -> sql_gis
    "node15" [ label = "zlib", shape = octagon ];
    "node60" -> "node15"  // innobase -> zlib
}
```


