---
title: "A long semaphore wait"
description: semaphore
date: 2024-12-17
weight: 20000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


{{<alert color="secondary">}}


{{</alert>}}


## 开一个大事务
> 更新50万行数据或100万行数据

```sql
[dbscale_internal@172.17.135.250:16310 10:08:53(db4)]$ alter table sbtest1 add column sex int default 0;
Query OK, 0 rows affected (0.03 sec)
Records: 0  Duplicates: 0  Warnings: 0

[dbscale_internal@172.17.135.250:16310 10:09:05(db4)]$ update sbtest1 set sex=1 where id<1000000;
ERROR 9001 (HY000): Execute Node failed. DirectExecuteNode fail due to exception:Fail to receive from server
[dbscale_internal@172.17.135.250:16310 10:33:45(db4)]$ update sbtest1 set sex=1 where id<500000;
ERROR 9001 (HY000): Execute Node failed. Get exception for commit with dead server connection.
[dbscale_internal@172.17.135.250:16310 10:47:06(db4)]$ update sbtest1 set sex=1 where id<100000;
Query OK, 99999 rows affected (1.45 sec)

[dbscale_internal@172.17.135.250:16310 10:50:15(db4)]$ update sbtest1 set sex=1 where id<300000 and id>100000;
Query OK, 199999 rows affected (3.72 sec)

```


## mysqld进程重启

```sql
2024-12-10T14:38:01.613665+08:00 6822 [ERROR] [MY-010584] [Repl] Slave I/O for channel '': error connecting to master 'dbscale_internal@172.17.138.136:16315' - retry-time: 60 retries: 1 message: Can't connect to MySQL server on '172.17.138.136:16315' (111), Error_code: MY-002003
2024-12-10T14:46:31.487339+08:00 0 [ERROR] [MY-013129] [Server] A message intended for a client cannot be sent there as no client-session is attached. Therefore, we're sending the information to the error-log instead: MY-001158 - Got an error reading communication packets
2024-12-10T17:59:51.400774+08:00 0 [ERROR] [MY-013129] [Server] A message intended for a client cannot be sent there as no client-session is attached. Therefore, we're sending the information to the error-log instead: MY-001158 - Got an error reading communication packets
2024-12-17T10:10:22.506141+08:00 2415398 [ERROR] [MY-010211] [Server] Got error 3044 when reading table './performance_schema/data_locks'
2024-12-17T10:14:22.928850+08:00 0 [Warning] [MY-012985] [InnoDB] A long semaphore wait:
--Thread 139731124750080 has waited at p_s.cc line 592 for 241 seconds the semaphore:
Mutex at 0x7f15b435f698, Mutex TRX_SYS created trx0sys.cc:565, lock var 1

2024-12-17T10:14:22.929640+08:00 0 [Warning] [MY-012985] [InnoDB] A long semaphore wait:
--Thread 139731126519552 has waited at lock0lock.cc line 3213 for 241 seconds the semaphore:
S-lock on RW-latch at 0x7f15b43ac3c0 created in file sync0sharded_rw.h line 80
a writer (thread id 139731124750080) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file lock0lock.cc line 3213
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/handler/p_s.cc line 590
InnoDB: ###### Starts InnoDB Monitor for 30 secs to print diagnostic info:
InnoDB: Pending preads 0, pwrites 0

=====================================
2024-12-17 10:14:42 139726101026560 INNODB MONITOR OUTPUT
=====================================
Per second averages calculated from the last 21 seconds
-----------------
BACKGROUND THREAD
-----------------
srv_master_thread loops: 73724 srv_active, 0 srv_shutdown, 578127 srv_idle
srv_master_thread log flush and writes: 0
----------
SEMAPHORES
----------
OS WAIT ARRAY INFO: reservation count 11637
--Thread 139731125044992 has waited at trx0i_s.cc line 1074 for 245 seconds the semaphore:
X-lock on RW-latch at 0x7f15b4025c20 created in file trx0i_s.cc line 1017
a writer (thread id 139731125634816) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file trx0i_s.cc line 1054
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/trx/trx0i_s.cc line 1074
--Thread 139731124750080 has waited at p_s.cc line 592 for 261 seconds the semaphore:
Mutex at 0x7f15b435f698, Mutex TRX_SYS created trx0sys.cc:565, lock var 1

--Thread 139730588555008 has waited at trx0trx.cc line 621 for 260 seconds the semaphore:
Mutex at 0x7f15b435f698, Mutex TRX_SYS created trx0sys.cc:565, lock var 1

--Thread 139731125339904 has waited at trx0i_s.cc line 1074 for 225 seconds the semaphore:
X-lock on RW-latch at 0x7f15b4025c20 created in file trx0i_s.cc line 1017
a writer (thread id 139731125634816) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file trx0i_s.cc line 1054
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/trx/trx0i_s.cc line 1074
--Thread 139731124455168 has waited at trx0i_s.cc line 1074 for 215 seconds the semaphore:
X-lock on RW-latch at 0x7f15b4025c20 created in file trx0i_s.cc line 1017
a writer (thread id 139731125634816) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file trx0i_s.cc line 1054
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/trx/trx0i_s.cc line 1074
--Thread 139731126519552 has waited at lock0lock.cc line 3213 for 261 seconds the semaphore:
S-lock on RW-latch at 0x7f15b43ac3c0 created in file sync0sharded_rw.h line 80
a writer (thread id 139731124750080) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file lock0lock.cc line 3213
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/handler/p_s.cc line 590
--Thread 139731125634816 has waited at trx0i_s.cc line 983 for 255 seconds the semaphore:
X-lock on RW-latch at 0x7f15b43ac260 created in file sync0sharded_rw.h line 80
a writer (thread id 139731124750080) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file lock0lock.cc line 3213
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/handler/p_s.cc line 590
--Thread 139731127994112 has waited at trx0i_s.cc line 1074 for 235 seconds the semaphore:
X-lock on RW-latch at 0x7f15b4025c20 created in file trx0i_s.cc line 1017
a writer (thread id 139731125634816) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file trx0i_s.cc line 1054
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/trx/trx0i_s.cc line 1074
--Thread 139731124160256 has waited at trx0i_s.cc line 1074 for 205 seconds the semaphore:
X-lock on RW-latch at 0x7f15b4025c20 created in file trx0i_s.cc line 1017
a writer (thread id 139731125634816) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file trx0i_s.cc line 1054
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/trx/trx0i_s.cc line 1074
--Thread 139730857719552 has waited at trx0i_s.cc line 1074 for 195 seconds the semaphore:
X-lock on RW-latch at 0x7f15b4025c20 created in file trx0i_s.cc line 1017
a writer (thread id 139731125634816) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file trx0i_s.cc line 1054
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/trx/trx0i_s.cc line 1074
--Thread 139730857129728 has waited at trx0i_s.cc line 1074 for 185 seconds the semaphore:
X-lock on RW-latch at 0x7f15b4025c20 created in file trx0i_s.cc line 1017
a writer (thread id 139731125634816) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file trx0i_s.cc line 1054
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/trx/trx0i_s.cc line 1074
--Thread 139730856539904 has waited at trx0i_s.cc line 1074 for 175 seconds the semaphore:
X-lock on RW-latch at 0x7f15b4025c20 created in file trx0i_s.cc line 1017
a writer (thread id 139731125634816) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file trx0i_s.cc line 1054
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/trx/trx0i_s.cc line 1074
--Thread 139730856834816 has waited at trx0i_s.cc line 1074 for 165 seconds the semaphore:
X-lock on RW-latch at 0x7f15b4025c20 created in file trx0i_s.cc line 1017
a writer (thread id 139731125634816) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file trx0i_s.cc line 1054
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/trx/trx0i_s.cc line 1074
--Thread 139730856244992 has waited at trx0i_s.cc line 1074 for 155 seconds the semaphore:
X-lock on RW-latch at 0x7f15b4025c20 created in file trx0i_s.cc line 1017
a writer (thread id 139731125634816) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file trx0i_s.cc line 1054
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/trx/trx0i_s.cc line 1074
--Thread 139730855950080 has waited at trx0i_s.cc line 1074 for 145 seconds the semaphore:
X-lock on RW-latch at 0x7f15b4025c20 created in file trx0i_s.cc line 1017
a writer (thread id 139731125634816) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file trx0i_s.cc line 1054
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/trx/trx0i_s.cc line 1074
--Thread 139730589144832 has waited at trx0i_s.cc line 1074 for 135 seconds the semaphore:
X-lock on RW-latch at 0x7f15b4025c20 created in file trx0i_s.cc line 1017
a writer (thread id 139731125634816) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file trx0i_s.cc line 1054
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/trx/trx0i_s.cc line 1074
--Thread 139730588260096 has waited at trx0i_s.cc line 1074 for 125 seconds the semaphore:
X-lock on RW-latch at 0x7f15b4025c20 created in file trx0i_s.cc line 1017
a writer (thread id 139731125634816) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file trx0i_s.cc line 1054
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/trx/trx0i_s.cc line 1074
--Thread 139730588849920 has waited at trx0i_s.cc line 1074 for 115 seconds the semaphore:
X-lock on RW-latch at 0x7f15b4025c20 created in file trx0i_s.cc line 1017
a writer (thread id 139731125634816) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file trx0i_s.cc line 1054
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/trx/trx0i_s.cc line 1074
--Thread 139730587670272 has waited at trx0i_s.cc line 1074 for 105 seconds the semaphore:
X-lock on RW-latch at 0x7f15b4025c20 created in file trx0i_s.cc line 1017
a writer (thread id 139731125634816) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file trx0i_s.cc line 1054
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/trx/trx0i_s.cc line 1074
--Thread 139730587965184 has waited at trx0i_s.cc line 1074 for 95 seconds the semaphore:
X-lock on RW-latch at 0x7f15b4025c20 created in file trx0i_s.cc line 1017
a writer (thread id 139731125634816) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file trx0i_s.cc line 1054
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/trx/trx0i_s.cc line 1074
--Thread 139730587375360 has waited at trx0i_s.cc line 1074 for 85 seconds the semaphore:
X-lock on RW-latch at 0x7f15b4025c20 created in file trx0i_s.cc line 1017
a writer (thread id 139731125634816) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file trx0i_s.cc line 1054
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/trx/trx0i_s.cc line 1074
--Thread 139730324059904 has waited at trx0i_s.cc line 1074 for 75 seconds the semaphore:
X-lock on RW-latch at 0x7f15b4025c20 created in file trx0i_s.cc line 1017
a writer (thread id 139731125634816) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file trx0i_s.cc line 1054
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/trx/trx0i_s.cc line 1074
--Thread 139730323764992 has waited at trx0i_s.cc line 1074 for 65 seconds the semaphore:
X-lock on RW-latch at 0x7f15b4025c20 created in file trx0i_s.cc line 1017
a writer (thread id 139731125634816) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file trx0i_s.cc line 1054
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/trx/trx0i_s.cc line 1074
--Thread 139730323470080 has waited at trx0i_s.cc line 1074 for 55 seconds the semaphore:
X-lock on RW-latch at 0x7f15b4025c20 created in file trx0i_s.cc line 1017
a writer (thread id 139731125634816) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file trx0i_s.cc line 1054
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/trx/trx0i_s.cc line 1074
--Thread 139730323175168 has waited at trx0i_s.cc line 1074 for 45 seconds the semaphore:
X-lock on RW-latch at 0x7f15b4025c20 created in file trx0i_s.cc line 1017
a writer (thread id 139731125634816) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file trx0i_s.cc line 1054
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/trx/trx0i_s.cc line 1074
--Thread 139730322880256 has waited at trx0i_s.cc line 1074 for 35 seconds the semaphore:
X-lock on RW-latch at 0x7f15b4025c20 created in file trx0i_s.cc line 1017
a writer (thread id 139731125634816) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file trx0i_s.cc line 1054
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/trx/trx0i_s.cc line 1074
--Thread 139730322585344 has waited at trx0i_s.cc line 1074 for 25 seconds the semaphore:
X-lock on RW-latch at 0x7f15b4025c20 created in file trx0i_s.cc line 1017
a writer (thread id 139731125634816) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file trx0i_s.cc line 1054
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/trx/trx0i_s.cc line 1074
--Thread 139730322290432 has waited at trx0i_s.cc line 1074 for 15 seconds the semaphore:
X-lock on RW-latch at 0x7f15b4025c20 created in file trx0i_s.cc line 1017
a writer (thread id 139731125634816) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file trx0i_s.cc line 1054
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/trx/trx0i_s.cc line 1074
--Thread 139730321995520 has waited at trx0i_s.cc line 1074 for 5 seconds the semaphore:
X-lock on RW-latch at 0x7f15b4025c20 created in file trx0i_s.cc line 1017
a writer (thread id 139731125634816) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file trx0i_s.cc line 1054
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/trx/trx0i_s.cc line 1074
OS WAIT ARRAY INFO: signal count 12669
RW-shared spins 0, rounds 0, OS waits 0
RW-excl spins 0, rounds 0, OS waits 0
RW-sx spins 0, rounds 0, OS waits 0
Spin rounds per wait: 0.00 RW-shared, 0.00 RW-excl, 0.00 RW-sx
FAIL TO OBTAIN LOCK MUTEX, SKIP LOCK INFO PRINTING
--------
FILE I/O
--------
I/O thread 0 state: waiting for completed aio requests (insert buffer thread)
I/O thread 1 state: waiting for completed aio requests (log thread)
I/O thread 2 state: waiting for completed aio requests (read thread)
I/O thread 3 state: waiting for completed aio requests (read thread)
I/O thread 4 state: waiting for completed aio requests (read thread)
I/O thread 5 state: waiting for completed aio requests (read thread)
I/O thread 6 state: waiting for completed aio requests (read thread)
I/O thread 7 state: waiting for completed aio requests (read thread)
I/O thread 8 state: waiting for completed aio requests (read thread)
I/O thread 9 state: waiting for completed aio requests (read thread)
I/O thread 10 state: waiting for completed aio requests (write thread)
I/O thread 11 state: waiting for completed aio requests (write thread)
I/O thread 12 state: waiting for completed aio requests (write thread)
I/O thread 13 state: waiting for completed aio requests (write thread)
I/O thread 14 state: waiting for completed aio requests (write thread)
I/O thread 15 state: waiting for completed aio requests (write thread)
I/O thread 16 state: waiting for completed aio requests (write thread)
I/O thread 17 state: waiting for completed aio requests (write thread)
Pending normal aio reads: [0, 0, 0, 0, 0, 0, 0, 0] , aio writes: [0, 0, 0, 0, 0, 0, 0, 0] ,
 ibuf aio reads:, log i/o's:, sync i/o's:
Pending flushes (fsync) log: 0; buffer pool: 7
109151 OS file reads, 5036284 OS file writes, 2219684 OS fsyncs
0.00 reads/s, 0 avg bytes/read, 0.00 writes/s, 0.00 fsyncs/s
-------------------------------------
INSERT BUFFER AND ADAPTIVE HASH INDEX
-------------------------------------
Ibuf: size 1, free list len 54, seg size 56, 22 merges
merged operations:
 insert 50, delete mark 6724, delete 35
discarded operations:
 insert 0, delete mark 0, delete 0
Hash table size 1106407, node heap has 0 buffer(s)
Hash table size 1106407, node heap has 0 buffer(s)
Hash table size 1106407, node heap has 0 buffer(s)
Hash table size 1106407, node heap has 0 buffer(s)
Hash table size 1106407, node heap has 0 buffer(s)
Hash table size 1106407, node heap has 0 buffer(s)
Hash table size 1106407, node heap has 0 buffer(s)
Hash table size 1106407, node heap has 0 buffer(s)
0.00 hash searches/s, 0.00 non-hash searches/s
---
LOG
---
Log sequence number          27021675909
Log buffer assigned up to    27021675909
Log buffer completed up to   27021675909
Log written up to            27021675909
Log flushed up to            27021675909
Added dirty pages up to      27021675909
Pages flushed up to          27021675909
Last checkpoint at           27021675909
4248183 log i/o's done, 0.00 log i/o's/second
----------------------
BUFFER POOL AND MEMORY
----------------------
Total large memory allocated 4385275904
Dictionary memory allocated 673234
Buffer pool size   262123
Free buffers       4096
Database pages     257956
Old database pages 95141
Modified db pages  0
Pending reads      0
Pending writes: LRU 0, flush list 0, single page 0
Pages made young 331242, not young 1634969
0.00 youngs/s, 0.00 non-youngs/s
Pages read 107708, created 546887, written 609225
0.00 reads/s, 0.00 creates/s, 0.00 writes/s
No buffer pool page gets since the last printout
Pages read ahead 0.00/s, evicted without access 0.00/s, Random read ahead 0.00/s
LRU len: 257956, unzip_LRU len: 0
I/O sum[0]:cur[0], unzip sum[0]:cur[0]
----------------------
INDIVIDUAL BUFFER POOL INFO
----------------------
---BUFFER POOL 0
Buffer pool size   65532
Free buffers       1024
Database pages     64491
Old database pages 23786
Modified db pages  0
Pending reads      0
Pending writes: LRU 0, flush list 0, single page 0
Pages made young 49873, not young 444811
0.00 youngs/s, 0.00 non-youngs/s
Pages read 27048, created 88012, written 145052
0.00 reads/s, 0.00 creates/s, 0.00 writes/s
No buffer pool page gets since the last printout
Pages read ahead 0.00/s, evicted without access 0.00/s, Random read ahead 0.00/s
LRU len: 64491, unzip_LRU len: 0
I/O sum[0]:cur[0], unzip sum[0]:cur[0]
---BUFFER POOL 1
Buffer pool size   65531
Free buffers       1024
Database pages     64489
Old database pages 23785
Modified db pages  0
Pending reads      0
Pending writes: LRU 0, flush list 0, single page 0
Pages made young 62770, not young 382791
0.00 youngs/s, 0.00 non-youngs/s
Pages read 26752, created 106786, written 121572
0.00 reads/s, 0.00 creates/s, 0.00 writes/s
No buffer pool page gets since the last printout
Pages read ahead 0.00/s, evicted without access 0.00/s, Random read ahead 0.00/s
LRU len: 64489, unzip_LRU len: 0
I/O sum[0]:cur[0], unzip sum[0]:cur[0]
---BUFFER POOL 2
Buffer pool size   65530
Free buffers       1024
Database pages     64488
Old database pages 23785
Modified db pages  0
Pending reads      0
Pending writes: LRU 0, flush list 0, single page 0
Pages made young 103854, not young 392495
0.00 youngs/s, 0.00 non-youngs/s
Pages read 26868, created 167441, written 162175
0.00 reads/s, 0.00 creates/s, 0.00 writes/s
No buffer pool page gets since the last printout
Pages read ahead 0.00/s, evicted without access 0.00/s, Random read ahead 0.00/s
LRU len: 64488, unzip_LRU len: 0
I/O sum[0]:cur[0], unzip sum[0]:cur[0]
---BUFFER POOL 3
Buffer pool size   65530
Free buffers       1024
Database pages     64488
Old database pages 23785
Modified db pages  0
Pending reads      0
Pending writes: LRU 0, flush list 0, single page 0
Pages made young 114745, not young 414872
0.00 youngs/s, 0.00 non-youngs/s
Pages read 27040, created 184648, written 180426
0.00 reads/s, 0.00 creates/s, 0.00 writes/s
No buffer pool page gets since the last printout
Pages read ahead 0.00/s, evicted without access 0.00/s, Random read ahead 0.00/s
LRU len: 64488, unzip_LRU len: 0
I/O sum[0]:cur[0], unzip sum[0]:cur[0]
--------------
ROW OPERATIONS
--------------
1 queries inside InnoDB, 0 queries in queue
InnoDB: ###### Diagnostic info printed to the standard error stream
2024-12-17T10:14:53.930030+08:00 0 [Warning] [MY-012985] [InnoDB] A long semaphore wait:
--Thread 139731125044992 has waited at trx0i_s.cc line 1074 for 256 seconds the semaphore:
X-lock on RW-latch at 0x7f15b4025c20 created in file trx0i_s.cc line 1017
a writer (thread id 139731125634816) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file trx0i_s.cc line 1054
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/trx/trx0i_s.cc line 1074
2024-12-17T10:14:53.930130+08:00 0 [Warning] [MY-012985] [InnoDB] A long semaphore wait:
--Thread 139731124750080 has waited at p_s.cc line 592 for 272 seconds the semaphore:
Mutex at 0x7f15b435f698, Mutex TRX_SYS created trx0sys.cc:565, lock var 1

2024-12-17T10:14:53.930160+08:00 0 [Warning] [MY-012985] [InnoDB] A long semaphore wait:
--Thread 139730588555008 has waited at trx0trx.cc line 621 for 271 seconds the semaphore:
Mutex at 0x7f15b435f698, Mutex TRX_SYS created trx0sys.cc:565, lock var 1

2024-12-17T10:14:53.930182+08:00 0 [Warning] [MY-012985] [InnoDB] A long semaphore wait:
--Thread 139731126519552 has waited at lock0lock.cc line 3213 for 272 seconds the semaphore:
S-lock on RW-latch at 0x7f15b43ac3c0 created in file sync0sharded_rw.h line 80
a writer (thread id 139731124750080) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file lock0lock.cc line 3213
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/handler/p_s.cc line 590
2024-12-17T10:14:53.930235+08:00 0 [Warning] [MY-012985] [InnoDB] A long semaphore wait:
--Thread 139731125634816 has waited at trx0i_s.cc line 983 for 266 seconds the semaphore:
X-lock on RW-latch at 0x7f15b43ac260 created in file sync0sharded_rw.h line 80
a writer (thread id 139731124750080) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file lock0lock.cc line 3213
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/handler/p_s.cc line 590
2024-12-17T10:14:53.930263+08:00 0 [Warning] [MY-012985] [InnoDB] A long semaphore wait:
--Thread 139731127994112 has waited at trx0i_s.cc line 1074 for 246 seconds the semaphore:
X-lock on RW-latch at 0x7f15b4025c20 created in file trx0i_s.cc line 1017
a writer (thread id 139731125634816) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file trx0i_s.cc line 1054
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/trx/trx0i_s.cc line 1074
InnoDB: ###### Starts InnoDB Monitor for 30 secs to print diagnostic info:
InnoDB: Pending preads 0, pwrites 0
InnoDB: ###### Diagnostic info printed to the standard error stream
2024-12-17T10:15:24.930625+08:00 0 [Warning] [MY-012985] [InnoDB] A long semaphore wait:
--Thread 139731125044992 has waited at trx0i_s.cc line 1074 for 287 seconds the semaphore:
X-lock on RW-latch at 0x7f15b4025c20 created in file trx0i_s.cc line 1017
a writer (thread id 139731125634816) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file trx0i_s.cc line 1054
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/trx/trx0i_s.cc line 1074
2024-12-17T10:15:24.930780+08:00 0 [Warning] [MY-012985] [InnoDB] A long semaphore wait:
--Thread 139731124750080 has waited at p_s.cc line 592 for 303 seconds the semaphore:
Mutex at 0x7f15b435f698, Mutex TRX_SYS created trx0sys.cc:565, lock var 1

2024-12-17T10:15:24.930834+08:00 0 [Warning] [MY-012985] [InnoDB] A long semaphore wait:
--Thread 139730588555008 has waited at trx0trx.cc line 621 for 302 seconds the semaphore:
Mutex at 0x7f15b435f698, Mutex TRX_SYS created trx0sys.cc:565, lock var 1

2024-12-17T10:15:24.930895+08:00 0 [Warning] [MY-012985] [InnoDB] A long semaphore wait:
--Thread 139731125339904 has waited at trx0i_s.cc line 1074 for 267 seconds the semaphore:
X-lock on RW-latch at 0x7f15b4025c20 created in file trx0i_s.cc line 1017
a writer (thread id 139731125634816) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file trx0i_s.cc line 1054
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/trx/trx0i_s.cc line 1074
2024-12-17T10:15:24.930976+08:00 0 [Warning] [MY-012985] [InnoDB] A long semaphore wait:
--Thread 139731124455168 has waited at trx0i_s.cc line 1074 for 257 seconds the semaphore:
X-lock on RW-latch at 0x7f15b4025c20 created in file trx0i_s.cc line 1017
a writer (thread id 139731125634816) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file trx0i_s.cc line 1054
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/trx/trx0i_s.cc line 1074
2024-12-17T10:15:24.931027+08:00 0 [Warning] [MY-012985] [InnoDB] A long semaphore wait:
--Thread 139731126519552 has waited at lock0lock.cc line 3213 for 303 seconds the semaphore:
S-lock on RW-latch at 0x7f15b43ac3c0 created in file sync0sharded_rw.h line 80
a writer (thread id 139731124750080) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file lock0lock.cc line 3213
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/handler/p_s.cc line 590
2024-12-17T10:15:24.931079+08:00 0 [Warning] [MY-012985] [InnoDB] A long semaphore wait:
--Thread 139731125634816 has waited at trx0i_s.cc line 983 for 297 seconds the semaphore:
X-lock on RW-latch at 0x7f15b43ac260 created in file sync0sharded_rw.h line 80
a writer (thread id 139731124750080) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file lock0lock.cc line 3213
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/handler/p_s.cc line 590
2024-12-17T10:15:24.931129+08:00 0 [Warning] [MY-012985] [InnoDB] A long semaphore wait:
--Thread 139731127994112 has waited at trx0i_s.cc line 1074 for 277 seconds the semaphore:
X-lock on RW-latch at 0x7f15b4025c20 created in file trx0i_s.cc line 1017
a writer (thread id 139731125634816) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file trx0i_s.cc line 1054
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/trx/trx0i_s.cc line 1074
2024-12-17T10:15:24.931180+08:00 0 [Warning] [MY-012985] [InnoDB] A long semaphore wait:
--Thread 139731124160256 has waited at trx0i_s.cc line 1074 for 247 seconds the semaphore:
X-lock on RW-latch at 0x7f15b4025c20 created in file trx0i_s.cc line 1017
a writer (thread id 139731125634816) has reserved it in mode exclusive
number of readers 0, waiters flag 1, lock_word: 0
Last time read locked in file trx0i_s.cc line 1054
Last time write locked in file ../../../mysql-8.0.26/storage/innobase/trx/trx0i_s.cc line 1074
InnoDB: ###### Starts InnoDB Monitor for 30 secs to print diagnostic info:
InnoDB: Pending preads 0, pwrites 0
InnoDB: ###### Diagnostic info printed to the standard error stream
2024-12-17T10:33:45.715516+08:00 0 [ERROR] [MY-013129] [Server] A message intended for a client cannot be sent there as no client-session is attached. Therefore, we're sending the information to the error-log instead: MY-001158 - Got an error reading communication packets
2024-12-17T10:33:45.808100+08:00 0 [ERROR] [MY-013129] [Server] A message intended for a client cannot be sent there as no client-session is attached. Therefore, we're sending the information to the error-log instead: MY-001158 - Got an error reading communication packets
2024-12-17T10:59:14 [root@x86centos7-250 /root] $

```














