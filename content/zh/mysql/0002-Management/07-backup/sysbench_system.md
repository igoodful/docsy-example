---
title: sysbench system
description: sysbench system
date: 2023-10-26
weight: 20000


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

- [processlist](https://it-blog-cn.com/blogs/db/processlist.html#%E4%BA%8C%E3%80%81show-processlist-%E5%8F%82%E6%95%B0%E5%88%86%E6%9E%90)

{{< /alert >}}



## 一、CPU

## 二、磁盘

```viz-dot
digraph "磁盘性能测试" {
        rankdir=TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
                ];
        "磁盘性能测试" -> "prepare";
        "磁盘性能测试" -> "run";
        "磁盘性能测试" -> "cleanup";

}
```

#### 1. 选项

| 选项                      | 功能                               | 示例                  |
|:--------------------------|:-----------------------------------|:----------------------|
| file-num.lua              | 创建的文件数量，默认128             | run                   |
| file-block-size           | 在所有IO操作中使用的块大小，默认16k | run                   |
| file-total-size           | 要创建的文件的总大小，默认2G        | run                   |
| oltp_read_only.lua        | 只读测试                           | run                   |
| oltp_write_only.lua       | 只写测试                           | run                   |
| oltp_read_write.lua       | 读写测试                           | prepare, run, cleanup |
| oltp_update_index.lua     | 带索引的更新测试                   | run                   |
| oltp_update_non_index.lua | 不带索引的更新测试                 | run                   |
| oltp_point_select.lua     | 等值查询测试                       | run                   |
| select_random_points.lua  | 随机等值查询测试                   | run                   |
| select_random_ranges.lua  | 随机范围查询测试                   | run                   |
| oltp_common.lua           |                                    |                       |



#### 2. 示例


###### A. 随机读写性能测试

- 进入你要测试的磁盘挂载点的路径下，创建测试目录
- 线程数
- 块大小
- 文件总大小
- 测试时间
- 测试模式

```bash
# cd要到你测试的磁盘目录下
cd /data/disktest
# 线程数=32 每隔4s输出一次结果 测试时间=60s
# 文件数=10 文件总大小=10G 文件操作模式=随机读写

sysbench fileio --threads=32 --report-interval=4 --time=60 --file-num=10 --file-total-size=10G --file-test-mode=rndrw prepare

sysbench fileio --threads=32 --report-interval=4 --time=60 --file-num=10 --file-total-size=10G --file-test-mode=rndrw run

sysbench fileio --threads=32 --report-interval=4 --time=60 --file-num=10 --file-total-size=10G --file-test-mode=rndrw cleanup
```





```bash
2024-05-20T16:44:31 [root@node71 /root/tmp] $ sysbench fileio help
sysbench 1.1.0-df89d34 (using bundled LuaJIT 2.1.0-beta3)

fileio options:
  --file-num=N                  number of files to create [128]
  --file-block-size=N           block size to use in all IO operations [16384]
  --file-total-size=SIZE        total size of files to create [2G]
  --file-test-mode=STRING       test mode {seqwr, seqrewr, seqrd, rndrd, rndwr, rndrw}
  --file-io-mode=STRING         file operations mode {sync,async,mmap} [sync]
  --file-extra-flags=[LIST,...] list of additional flags to use to open files {sync,dsync,direct} []
  --file-fsync-freq=N           do fsync() after this number of requests (0 - don't use fsync()) [100]
  --file-fsync-all[=on|off]     do fsync() after each write operation [off]
  --file-fsync-end[=on|off]     do fsync() at the end of test [on]
  --file-fsync-mode=STRING      which method to use for synchronization {fsync, fdatasync} [fsync]
  --file-merged-requests=N      merge at most this number of IO requests if possible (0 - don't merge) [0]
  --file-rw-ratio=N             reads/writes ratio for combined test [1.5]

```



```bash
  --file-num=N                  # 创建的文件数量 [128]
  --file-block-size=N           # 在所有IO操作中使用的块大小 [16384]
  --file-total-size=SIZE        # 要创建的文件的总大小 [2G]
  --file-test-mode=STRING       # 测试模式 {seqwr(顺序写), seqrewr(顺序读写), seqrd(顺序读), rndrd(随机读), rndwr(随机写), rndrw(随机读写)}
  --file-io-mode=STRING         # 文件操作模式 {sync(同步),async(异步),mmap} [sync]
  --file-extra-flags=[LIST,...] # 用于打开文件的附加标志列表 {sync,dsync,direct} []
  --file-fsync-freq=N           # 执行N条请求数量后执行fsync() (0 - don't use fsync()) [100]
  --file-fsync-all[=on|off]     # 每条写指令后执行fsync() [off]
  --file-fsync-end[=on|off]     # 测试执行后执行fsync() [on]
  --file-fsync-mode=STRING      # 同步方式 {fsync, fdatasync} [fsync]
  --file-merged-requests=N      # 允许范围内，最多合并IO请求数量 (0 - don't merge) [0]
  --file-rw-ratio=N             # 组合测试读/写比率 [1.5]
```






```bash
2024-05-20T16:19:18 [root@node71 /root/tmp] $ sysbench fileio --file-num=2 --file-block-size=10240 --file-total-size=10G prepare
sysbench 1.1.0-df89d34 (using bundled LuaJIT 2.1.0-beta3)

2 files, 5242880Kb each, 10240Mb total
Creating files for the test...
Extra file open flags: (none)
Creating file test_file.0
Creating file test_file.1
10737418240 bytes written in 33.10 seconds (309.40 MiB/sec).
2024-05-20T16:19:54 [root@node71 /root/tmp] $ ll
total 10485768
-rw------- 1 root root 5368709120 May 20 16:19 test_file.0
-rw------- 1 root root 5368709120 May 20 16:19 test_file.1
2024-05-20T16:19:57 [root@node71 /root/tmp] $ sysbench fileio --file-num=2 --file-block-size=10240 --file-total-size=10G --file-test-mode=seqrewr --time=30 --events=10000 --threads=4 --file-fsync-freq=200 --file-extra-flags=direct run
sysbench 1.1.0-df89d34 (using bundled LuaJIT 2.1.0-beta3)

Running the test with following options:
Number of threads: 4
Initializing random number generator from current time


Extra file open flags: directio
2 files, 5GiB each
10GiB total file size
Block size 10KiB
Periodic FSYNC enabled, calling fsync() each 200 requests.
Calling fsync() at the end of test, Enabled.
Using synchronous I/O mode
Doing sequential rewrite test
Initializing worker threads...

Threads started!


Throughput:
         read:  IOPS=0.00 0.00 MiB/s (0.00 MB/s)
         write: IOPS=9057.41 88.45 MiB/s (92.75 MB/s)
         fsync: IOPS=96.96

Latency (ms):
         min:                                  0.06
         avg:                                  0.44
         max:                                 37.16
         95th percentile:                      1.12
         sum:                               4358.00

2024-05-20T16:20:03 [root@node71 /root/tmp] $ sysbench fileio --file-num=2 --file-block-size=10240 --file-total-size=10G --file-test-mode=rndrw --time=30 --events=10000 --threads=4 --file-fsync-freq=200 --file-extra-flags=direct run
sysbench 1.1.0-df89d34 (using bundled LuaJIT 2.1.0-beta3)

Running the test with following options:
Number of threads: 4
Initializing random number generator from current time


Extra file open flags: directio
2 files, 5GiB each
10GiB total file size
Block size 10KiB
Number of IO requests: 10000
Read/Write ratio for combined random IO test: 1.50
Periodic FSYNC enabled, calling fsync() each 200 requests.
Calling fsync() at the end of test, Enabled.
Using synchronous I/O mode
Doing random r/w test
Initializing worker threads...

Threads started!


Throughput:
         read:  IOPS=9159.58 89.45 MiB/s (93.79 MB/s)
         write: IOPS=6109.47 59.66 MiB/s (62.56 MB/s)
         fsync: IOPS=163.45

Latency (ms):
         min:                                  0.06
         avg:                                  0.26
         max:                                 10.40
         95th percentile:                      0.55
         sum:                               2570.92

2024-05-20T16:20:31 [root@node71 /root/tmp] $ sysbench fileio --file-num=2 --file-block-size=10240 --file-total-size=10G --file-test-mode=rndrd --time=30 --events=10000 --threads=4 --file-fsync-freq=200 --file-extra-flags=direct run
sysbench 1.1.0-df89d34 (using bundled LuaJIT 2.1.0-beta3)

Running the test with following options:
Number of threads: 4
Initializing random number generator from current time


Extra file open flags: directio
2 files, 5GiB each
10GiB total file size
Block size 10KiB
Number of IO requests: 10000
Read/Write ratio for combined random IO test: 1.50
Periodic FSYNC enabled, calling fsync() each 200 requests.
Calling fsync() at the end of test, Enabled.
Using synchronous I/O mode
Doing random read test
Initializing worker threads...

Threads started!


Throughput:
         read:  IOPS=30524.83 298.09 MiB/s (312.57 MB/s)
         write: IOPS=0.00 0.00 MiB/s (0.00 MB/s)
         fsync: IOPS=0.00

Latency (ms):
         min:                                  0.05
         avg:                                  0.13
         max:                                  7.62
         95th percentile:                      0.19
         sum:                               1298.16

2024-05-20T16:20:52 [root@node71 /root/tmp] $ sysbench fileio --file-num=2 --file-block-size=10240 --file-total-size=10G cleanup
sysbench 1.1.0-df89d34 (using bundled LuaJIT 2.1.0-beta3)

Removing test files...
2024-05-20T16:21:28 [root@node71 /root/tmp] $ ll
total 0
2024-05-20T16:21:30 [root@node71 /root/tmp] $

```








## 三、内存



## 四、POSIX线程


## 五、Mutex调度











