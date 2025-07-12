---
title: 查看系统线程栈方向
description: pmap
date: 2023-10-30
weight: 10000


---

{{< alert >}}


{{< /alert >}}


```bash
[guolicheng@48 build]$ pmap -x 315720
315720:   /db/storage/guolicheng/mysql_3333/bin/mysqld --defaults-file=/db/storage/guolicheng/mysql_3333/conf/my.cnf
Address           Kbytes     RSS   Dirty Mode  Mapping
0000000000400000   55732    5280       0 r-x-- mysqld
0000000003a6e000    1484     736     484 r---- mysqld
0000000003be1000    3620     244     152 rw--- mysqld
0000000003f6a000    5288    5016    5004 rw---   [ anon ]
000000000620c000   61408   54512   54368 rw---   [ anon ]
00007fdff8000000     132       4       4 rw---   [ anon ]
00007fdff8021000   65404       0       0 -----   [ anon ]
00007fdffc000000     132       4       4 rw---   [ anon ]
00007fdffc021000   65404       0       0 -----   [ anon ]
00007fe000000000    3892     316     316 rw---   [ anon ]
00007fe0003cd000   61644       0       0 -----   [ anon ]
00007fe004000000     132       4       4 rw---   [ anon ]
00007fe004021000   65404       0       0 -----   [ anon ]
00007fe008000000   17164    6812    6812 rw---   [ anon ]
00007fe0090c3000   48372       0       0 -----   [ anon ]
00007fe00c000000   16136    9384    9384 rw---   [ anon ]
00007fe00cfc2000   49400       0       0 -----   [ anon ]
00007fe010000000    4768    1208    1184 rw---   [ anon ]
00007fe0104a8000   60768       0       0 -----   [ anon ]
00007fe014000000   17556   10780   10776 rw---   [ anon ]
00007fe015125000   47980       0       0 -----   [ anon ]
00007fe018000000   17476   10684   10668 rw---   [ anon ]
00007fe019111000   48060       0       0 -----   [ anon ]
00007fe01c000000     132       4       4 rw---   [ anon ]
00007fe01c021000   65404       0       0 -----   [ anon ]
00007fe020000000   27476   20752   20720 rw---   [ anon ]
00007fe021ad5000   38060       0       0 -----   [ anon ]
00007fe024000000     132       4       4 rw---   [ anon ]
00007fe024021000   65404       0       0 -----   [ anon ]
00007fe028000000     184     136     136 rw---   [ anon ]
00007fe02802e000   65352       0       0 -----   [ anon ]
00007fe02c000000   53736   47164   47104 rw---   [ anon ]
00007fe02f47a000   11800       0       0 -----   [ anon ]
00007fe030000000     132      16      16 rw---   [ anon ]
00007fe030021000   65404       0       0 -----   [ anon ]
00007fe034000000     132      48      48 rw---   [ anon ]
00007fe034021000   65404       0       0 -----   [ anon ]
00007fe038000000     132       0       0 rw---   [ anon ]
00007fe038021000   65404       0       0 -----   [ anon ]
00007fe03c000000     132       0       0 rw---   [ anon ]
00007fe03c021000   65404       0       0 -----   [ anon ]
00007fe040000000     132       0       0 rw---   [ anon ]
00007fe040021000   65404       0       0 -----   [ anon ]
00007fe044000000     132       0       0 rw---   [ anon ]
00007fe044021000   65404       0       0 -----   [ anon ]
00007fe048000000     144      64      64 rw---   [ anon ]
00007fe048024000   65392       0       0 -----   [ anon ]
00007fe04c340000       4       0       0 -----   [ anon ]
00007fe04c341000    8192      24      24 rw---   [ anon ]
00007fe04cb41000       4       0       0 -----   [ anon ]
00007fe04cb42000    8192      32      32 rw---   [ anon ]
00007fe04d342000       4       0       0 -----   [ anon ]
00007fe04d343000    8192      24      24 rw---   [ anon ]
00007fe04db43000       4       0       0 -----   [ anon ]
00007fe04db44000    8192      32      32 rw---   [ anon ]
00007fe04e344000       4       0       0 -----   [ anon ]
00007fe04e345000    8192      36      36 rw---   [ anon ]
00007fe04eb45000       4       0       0 -----   [ anon ]
00007fe04eb46000    8192      44      44 rw---   [ anon ]
00007fe04f346000       4       0       0 -----   [ anon ]
00007fe04f347000    8192       8       8 rw---   [ anon ]
00007fe04fb47000       4       0       0 -----   [ anon ]
00007fe04fb48000    8192      52      52 rw---   [ anon ]
00007fe050348000       4       0       0 -----   [ anon ]
00007fe050349000    8192       8       8 rw---   [ anon ]
00007fe050b49000       4       0       0 -----   [ anon ]
00007fe050b4a000    8192      16      16 rw---   [ anon ]
00007fe05134a000       4       0       0 -----   [ anon ]
00007fe05134b000  570068  490088  490068 rw---   [ anon ]
00007fe074000000    6692    6352    6352 rw---   [ anon ]
00007fe074689000   58844       0       0 -----   [ anon ]
00007fe0785a1000       4       0       0 -----   [ anon ]
00007fe0785a2000    8192      16      16 rw---   [ anon ]
00007fe078da2000       4       0       0 -----   [ anon ]
00007fe078da3000    8192      12      12 rw---   [ anon ]
00007fe0795a3000       4       0       0 -----   [ anon ]
00007fe0795a4000    8192       8       8 rw---   [ anon ]
00007fe079da4000       4       0       0 -----   [ anon ]
00007fe079da5000    8192       8       8 rw---   [ anon ]
00007fe07a5a5000       4       0       0 -----   [ anon ]
00007fe07a5a6000  223592  173788  173756 rw---   [ anon ]
00007fe088000000    4804    4624    4624 rw---   [ anon ]
00007fe0884b1000   60732       0       0 -----   [ anon ]
00007fe08c34a000       4       0       0 -----   [ anon ]
00007fe08c34b000  347680  273268  273264 rw---   [ anon ]
00007fe0a17fb000       4       0       0 -----   [ anon ]
00007fe0a17fc000    8192      16      16 rw---   [ anon ]
00007fe0a1ffc000       4       0       0 -----   [ anon ]
00007fe0a1ffd000    8192      20      20 rw---   [ anon ]
00007fe0a27fd000       4       0       0 -----   [ anon ]
00007fe0a27fe000    8192      20      20 rw---   [ anon ]
00007fe0a2ffe000       4       0       0 -----   [ anon ]
00007fe0a2fff000    8192      20      20 rw---   [ anon ]
00007fe0a37ff000       4       0       0 -----   [ anon ]
00007fe0a3800000    8192      24      24 rw---   [ anon ]
00007fe0a4000000    3372    3272    3272 rw---   [ anon ]
00007fe0a434b000   62164       0       0 -----   [ anon ]
00007fe0a86d1000       4       0       0 -----   [ anon ]
00007fe0a86d2000    8192      12      12 rw---   [ anon ]
00007fe0a8ed2000       4       0       0 -----   [ anon ]
00007fe0a8ed3000  115892   82120   82120 rw---   [ anon ]
00007fe0b0000000    3900    3384    3384 rw---   [ anon ]
00007fe0b03cf000   61636       0       0 -----   [ anon ]
00007fe0b4000000    3400    3200    3200 rw---   [ anon ]
00007fe0b4352000   62136       0       0 -----   [ anon ]
00007fe0b8000000    6664    6348    6344 rw---   [ anon ]
00007fe0b8682000   58872       0       0 -----   [ anon ]
00007fe0bc0cb000       4       0       0 -----   [ anon ]
00007fe0bc0cc000     280      36      36 rw---   [ anon ]
00007fe0bc112000       4       0       0 -----   [ anon ]
00007fe0bc113000     280      80      80 rw---   [ anon ]
00007fe0bc159000       4       0       0 -----   [ anon ]
00007fe0bc15a000     280      56      56 rw---   [ anon ]
00007fe0bc1a0000     200       0       0 r-x-- mysql_clone.so
00007fe0bc1d2000    2048       0       0 ----- mysql_clone.so
00007fe0bc3d2000       4       4       4 r---- mysql_clone.so
00007fe0bc3d3000       4       4       4 rw--- mysql_clone.so
00007fe0bc3d4000       8       8       8 rw---   [ anon ]
00007fe0bc3d6000      28       4       0 r-x-- semisync_slave.so
00007fe0bc3dd000    2044       0       0 ----- semisync_slave.so
00007fe0bc5dc000       4       4       4 r---- semisync_slave.so
00007fe0bc5dd000       4       4       4 rw--- semisync_slave.so
00007fe0bc5de000     100      20       0 r-x-- semisync_master.so
00007fe0bc5f7000    2048       0       0 ----- semisync_master.so
00007fe0bc7f7000       4       4       4 r---- semisync_master.so
00007fe0bc7f8000       4       4       4 rw--- semisync_master.so
00007fe0bc7f9000       4       0       0 -----   [ anon ]
00007fe0bc7fa000    8192      12      12 rw---   [ anon ]
00007fe0bcffa000       4       0       0 -----   [ anon ]
00007fe0bcffb000    8192      12      12 rw---   [ anon ]
00007fe0bd7fb000       4       0       0 -----   [ anon ]
00007fe0bd7fc000    8192      20      20 rw---   [ anon ]
00007fe0bdffc000       4       0       0 -----   [ anon ]
00007fe0bdffd000    8192      12      12 rw---   [ anon ]
00007fe0be7fd000       4       0       0 -----   [ anon ]
00007fe0be7fe000    8192      12      12 rw---   [ anon ]
00007fe0beffe000       4       0       0 -----   [ anon ]
00007fe0befff000    8192      20      20 rw---   [ anon ]
00007fe0bf7ff000       4       0       0 -----   [ anon ]
00007fe0bf800000    8192      20      20 rw---   [ anon ]
00007fe0c0000000    3376    3224    3224 rw---   [ anon ]
00007fe0c034c000   62160       0       0 -----   [ anon ]
00007fe0c4000000    3404    3284    3284 rw---   [ anon ]
00007fe0c4353000   62132       0       0 -----   [ anon ]
00007fe0c8000000   62660   50112   50108 rw---   [ anon ]
00007fe0cbd31000    2876       0       0 -----   [ anon ]
00007fe0cc032000       4       0       0 rw---   [ anon ]
00007fe0cc033000       4       0       0 -----   [ anon ]
00007fe0cc034000   21204    8192    8192 rw---   [ anon ]
00007fe0cd4f9000       4       0       0 -----   [ anon ]
00007fe0cd4fa000     280      44      44 rw---   [ anon ]
00007fe0cd540000       4       0       0 -----   [ anon ]
00007fe0cd541000     280      64      64 rw---   [ anon ]
00007fe0cd587000       4       0       0 -----   [ anon ]
00007fe0cd588000     280      96      96 rw---   [ anon ]
00007fe0cd5ce000       4       0       0 -----   [ anon ]
00007fe0cd5cf000     280      88      88 rw---   [ anon ]
00007fe0cd615000       4       0       0 -----   [ anon ]
00007fe0cd616000     280      92      92 rw---   [ anon ]
00007fe0cd65c000       4       0       0 -----   [ anon ]
00007fe0cd65d000     280      44      44 rw---   [ anon ]
00007fe0cd6a3000       4       0       0 -----   [ anon ]
00007fe0cd6a4000   17688   17412   17412 rw---   [ anon ]
00007fe0ce7ea000      12       4       0 rw-s- [aio] (deleted)
00007fe0ce7ed000   11056    8784    8784 rw---   [ anon ]
00007fe0cf2b9000       4       0       0 -----   [ anon ]
00007fe0cf2ba000   16388      24      24 rw---   [ anon ]
00007fe0d02bb000      28       0       0 r-x-- component_reference_cache.so
00007fe0d02c2000    2044       0       0 ----- component_reference_cache.so
00007fe0d04c1000       4       0       0 r---- component_reference_cache.so
00007fe0d04c2000       4       0       0 rw--- component_reference_cache.so
00007fe0d04c3000      48      20       0 r-x-- libnss_files-2.18.so
00007fe0d04cf000    2044       0       0 ----- libnss_files-2.18.so
00007fe0d06ce000       4       0       0 r---- libnss_files-2.18.so
00007fe0d06cf000       4       0       0 rw--- libnss_files-2.18.so
00007fe0d06d0000  182820  119248  119244 rw---   [ anon ]
00007fe0db959000    1672     728       0 r-x-- libc-2.18.so
00007fe0dbafb000    2048       0       0 ----- libc-2.18.so
00007fe0dbcfb000      16      16      16 r---- libc-2.18.so
00007fe0dbcff000       8       8       8 rw--- libc-2.18.so
00007fe0dbd01000      16      12      12 rw---   [ anon ]
00007fe0dbd05000      84      16       0 r-x-- libgcc_s-4.8.5-20150702.so.1
00007fe0dbd1a000    2044       0       0 ----- libgcc_s-4.8.5-20150702.so.1
00007fe0dbf19000       4       4       4 r---- libgcc_s-4.8.5-20150702.so.1
00007fe0dbf1a000       4       4       4 rw--- libgcc_s-4.8.5-20150702.so.1
00007fe0dbf1b000    1028      88       0 r-x-- libm-2.18.so
00007fe0dc01c000    2044       0       0 ----- libm-2.18.so
00007fe0dc21b000       4       4       4 r---- libm-2.18.so
00007fe0dc21c000       4       4       4 rw--- libm-2.18.so
00007fe0dc21d000    1508     268       0 r-x-- libstdc++.so.6.0.25
00007fe0dc396000    2048       0       0 ----- libstdc++.so.6.0.25
00007fe0dc596000      40      40      40 r---- libstdc++.so.6.0.25
00007fe0dc5a0000       8       8       8 rw--- libstdc++.so.6.0.25
00007fe0dc5a2000      16      16      16 rw---   [ anon ]
00007fe0dc5a6000      40      16       0 r-x-- libnuma.so.1
00007fe0dc5b0000    2048       0       0 ----- libnuma.so.1
00007fe0dc7b0000       4       4       4 rw--- libnuma.so.1
00007fe0dc7b1000       4       4       0 r-x-- libaio.so.1.0.1
00007fe0dc7b2000    2044       0       0 ----- libaio.so.1.0.1
00007fe0dc9b1000       4       4       4 r---- libaio.so.1.0.1
00007fe0dc9b2000       4       4       4 rw--- libaio.so.1.0.1
00007fe0dc9b3000     508       0       0 r-x-- libssl.so.1.1
00007fe0dca32000    2044       0       0 ----- libssl.so.1.1
00007fe0dcc31000      52      52      52 rw--- libssl.so.1.1
00007fe0dcc3e000      20       4       4 rw--- libssl.so.1.1
00007fe0dcc43000    2476     132       0 r-x-- libcrypto.so.1.1
00007fe0dceae000    2044       0       0 ----- libcrypto.so.1.1
00007fe0dd0ad000     184     184     184 rw--- libcrypto.so.1.1
00007fe0dd0db000      16      12      12 rw---   [ anon ]
00007fe0dd0df000      88       4       4 rw--- libcrypto.so.1.1
00007fe0dd0f5000      28      16       0 r-x-- librt-2.18.so
00007fe0dd0fc000    2044       0       0 ----- librt-2.18.so
00007fe0dd2fb000       4       4       4 r---- librt-2.18.so
00007fe0dd2fc000       4       4       4 rw--- librt-2.18.so
00007fe0dd2fd000       8       8       0 r-x-- libdl-2.18.so
00007fe0dd2ff000    2048       0       0 ----- libdl-2.18.so
00007fe0dd4ff000       4       4       4 r---- libdl-2.18.so
00007fe0dd500000       4       4       4 rw--- libdl-2.18.so
00007fe0dd501000     568       8       0 r-x-- libprotobuf-lite.so.3.11.4
00007fe0dd58f000    2048       0       0 ----- libprotobuf-lite.so.3.11.4
00007fe0dd78f000       8       8       8 r---- libprotobuf-lite.so.3.11.4
00007fe0dd791000       4       4       4 rw--- libprotobuf-lite.so.3.11.4
00007fe0dd792000       4       4       4 rw---   [ anon ]
00007fe0dd793000     100      76       0 r-x-- libpthread-2.18.so
00007fe0dd7ac000    2044       0       0 ----- libpthread-2.18.so
00007fe0dd9ab000       4       4       4 r---- libpthread-2.18.so
00007fe0dd9ac000       4       4       4 rw--- libpthread-2.18.so
00007fe0dd9ad000      16       4       4 rw---   [ anon ]
00007fe0dd9b1000     128     108       0 r-x-- ld-2.18.so
00007fe0dd9d1000       4       0       0 rw---   [ anon ]
00007fe0dd9d2000      12       4       0 rw-s- [aio] (deleted)
00007fe0dd9d5000      12       4       0 rw-s- [aio] (deleted)
00007fe0dd9d8000      12       4       0 rw-s- [aio] (deleted)
00007fe0dd9db000      12       4       0 rw-s- [aio] (deleted)
00007fe0dd9de000      12       4       0 rw-s- [aio] (deleted)
00007fe0dd9e1000    1912     640     640 rw---   [ anon ]
00007fe0ddbbf000       4       0       0 rw-s- [aio] (deleted)
00007fe0ddbc0000      12       4       0 rw-s- [aio] (deleted)
00007fe0ddbc3000      12       4       0 rw-s- [aio] (deleted)
00007fe0ddbc6000      12       4       0 rw-s- [aio] (deleted)
00007fe0ddbc9000      12       4       0 rw-s- [aio] (deleted)
00007fe0ddbcc000       4       0       0 rw-s- [aio] (deleted)
00007fe0ddbcd000      16       8       8 rw---   [ anon ]
00007fe0ddbd1000       4       4       4 r---- ld-2.18.so
00007fe0ddbd2000       4       4       4 rw--- ld-2.18.so
00007fe0ddbd3000       4       4       4 rw---   [ anon ]
00007fff18f75000     132      52      52 rw---   [ stack ]
00007fff18fe0000       8       4       0 r-x--   [ anon ]
ffffffffff600000       4       0       0 r-x--   [ anon ]
---------------- ------- ------- -------
total kB         3870024 1433764 1426224


```

### 根据`00007fff18f75000     132      52      52 rw---   [ stack ]`，可知：
在给定的输出中，关注 `[ stack ]` 部分，它表示当前线程的栈的区域。根据这个输出：

- `[ stack ]` 是可读写的 (`rw---`)。
- 栈的增长方向通常由低地址向高地址增长。

因此，从这个输出来看，该系统的线程栈方向是从低地址向高地址增长。这是Linux系统的一种典型行为。















