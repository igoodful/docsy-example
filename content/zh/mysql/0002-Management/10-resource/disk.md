---
title: 磁盘IO
description: disk
date: 2023-10-25
weight: 20000


---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


| 配置                  | 参数          | 备注         |
| :-------------------- | :------------ | :----------- |
| version               | 8.0.32        | 版本         |
| engine                | innodb        | 存储引擎     |
| transaction_isolation | read commited | 事务隔离级别 |


{{<alert>}}

- `innobase_hton` 对象初始化
- 初始化`innodb`参数：`innodb_init_params`

{{</alert>}}



## 磁盘利用率
VolumeRate



## IOPS



## iostat -xm 2
```c++
2024-07-15T21:10:00 [root@node1 /root] $ iostat -xm 2
Linux 3.10.0-957.el7.x86_64 (node1) 	07/15/2024 	_x86_64_	(4 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
          32.93    0.00    7.72    9.08   23.04   27.24

Device:         rrqm/s   wrqm/s     r/s     w/s    rMB/s    wMB/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
scd1              0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sda               0.00   581.50    0.00 2304.50     0.00    81.92    72.80     0.94    0.41    0.00    0.41   0.15  35.70

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
          32.09    0.00    8.38    5.41   29.90   24.23

Device:         rrqm/s   wrqm/s     r/s     w/s    rMB/s    wMB/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
scd1              0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sda               0.00   434.50    0.50 1482.50     0.00    64.85    89.55     0.64    0.43    0.00    0.43   0.17  25.40

```

- rrqm/s:         每秒进行 merge 的读操作数目。即 delta(rmerge)/s
- wrqm/s:         每秒进行 merge 的写操作数目。即 delta(wmerge)/s
- **r/s**:        每秒完成的读 I/O 设备**次数**。即 delta(rio)/s
- **w/s**:        每秒完成的写 I/O 设备**次数**。即 delta(wio)/s
- **rkB/s**:      每秒读K字节数。
- **wkB/s**:      每秒写K字节数。
- avgrq-sz:       平均每次设备I/O操作的数据大小 (扇区)。delta(rsect+wsect)/delta(rio+wio)
- avgqu-sz:       平均I/O队列长度。即 delta(aveq)/s/1000 (因为aveq的单位为毫秒)。
- await:          平均每次设备I/O操作的等待时间 (毫秒)。即 delta(ruse+wuse)/delta(rio+wio)
- svctm:          平均每次设备I/O操作的服务时间 (毫秒)。即 delta(use)/delta(rio+wio)
- **%util**:      一秒中有百分之多少的时间用于 I/O 操作，或者说一秒中有多少时间 I/O 队列是非空的。即 delta(use)/s/1000 (因为use的单位为毫秒)



```bash
iostat -d /dev/dm-0  -mx 5
```




## pidstat -d 1 5

```bash
2024-07-15T21:10:16 [root@node1 /root] $ pidstat -d 1 5
Linux 3.10.0-957.el7.x86_64 (node1) 	07/15/2024 	_x86_64_	(4 CPU)

09:12:54 PM   UID       PID   kB_rd/s   kB_wr/s kB_ccwr/s  Command
09:12:55 PM     0      1441      0.00   1453.24      0.00  jbd2/sda2-8
09:12:55 PM  1001      3504      0.00      5.76      0.00  java
09:12:55 PM  1001      5678      0.00  61018.71      0.00  mysqld

09:12:55 PM   UID       PID   kB_rd/s   kB_wr/s kB_ccwr/s  Command
09:12:56 PM     0      1441      0.00   2608.00      0.00  jbd2/sda2-8
09:12:56 PM  1001      3504      0.00      4.00      0.00  java
09:12:56 PM  1001      5678      4.00  73924.00      0.00  mysqld
09:12:56 PM  1001     15395      0.00      4.00      0.00  dbscale

09:12:56 PM   UID       PID   kB_rd/s   kB_wr/s kB_ccwr/s  Command
09:12:57 PM     0      1441      0.00   3044.00      0.00  jbd2/sda2-8
09:12:57 PM  1001      3504      0.00      8.00      0.00  java
09:12:57 PM  1001      5678      0.00  63372.00      0.00  mysqld

09:12:57 PM   UID       PID   kB_rd/s   kB_wr/s kB_ccwr/s  Command
09:12:58 PM     0      1441      0.00   1004.00      0.00  jbd2/sda2-8
09:12:58 PM  1001      3504      0.00      4.00      0.00  java
09:12:58 PM  1001      5678   3992.00  72352.00      0.00  mysqld

09:12:58 PM   UID       PID   kB_rd/s   kB_wr/s kB_ccwr/s  Command
09:12:59 PM     0      1441      0.00     67.33      0.00  jbd2/sda2-8
09:12:59 PM  1001      3504      0.00      7.92      0.00  java
09:12:59 PM  1001      5678      0.00  76364.36      0.00  mysqld

Average:      UID       PID   kB_rd/s   kB_wr/s kB_ccwr/s  Command
Average:        0      1441      0.00   1619.26      0.00  jbd2/sda2-8
Average:     1001      3504      0.00      5.93      0.00  java
Average:     1001      5678    740.00  68813.33      0.00  mysqld
Average:     1001     15395      0.00      0.74      0.00  dbscale


```
















## dmsetup info
```bash
2024-04-16T11:03:28 [root@dev /root] # dmsetup info
Name:              cl-swap
State:             ACTIVE
Read Ahead:        8192
Tables present:    LIVE
Open count:        2
Event number:      0
Major, minor:      253, 1
Number of targets: 1
UUID: LVM-ZQGekUQY17Dth2lKrhDqaNLqY8IFRXWMWDZd2V0yjz0o0ACRCIEd9w5GAwT5aEGq

Name:              cl-root
State:             ACTIVE
Read Ahead:        8192
Tables present:    LIVE
Open count:        1
Event number:      0
Major, minor:      253, 0
Number of targets: 1
UUID: LVM-ZQGekUQY17Dth2lKrhDqaNLqY8IFRXWMasR9C3RDsAXVjpXtsbdPxEEAgUQQzIKg

```








## 磁盘性能测试

```bash
#!/bin/bash
# DATA 2022-11-21
# VERSION 1.0
# DESC USING FIO TOOL TO TEST IO

# 测试参数默认值
# 队列深度32
# 块大小4K
# 并发线程数32
# 运行时间120秒

# 定义参数
iodepth=$1
block_size=$2
numjobs=$3
runtime=$4
fio_out_file="fio_test_out_file"

if [ ! -d test ]; then
	mkdir -p /greatdb/test
	cd /greatdb/test
fi

function fio_test() {
	echo "${block_size:="4k"} 块顺序读开始"
	fio -filename=/greatdb/test/fio-${block_size:="4k"}-read -direct=1 -iodepth ${iodepth:=32} -thread -rw=read -ioengine=libaio -bs=${block_size:="4k"} -size=1G -numjobs=${numjobs:=32} -runtime=${runtime:=120} -group_reporting -name=read_${block_size:="4k"}
	echo "${block_size:="4k"} 块顺序读结束"

	echo "${block_size:="4k"} 块顺序写开始"
	fio -filename=/greatdb/test/fio-${block_size:="4k"}-write -direct=1 -iodepth ${iodepth:=32} -thread -rw=write -ioengine=libaio -bs=${block_size:="4k"} -size=1G -numjobs=${numjobs:=32} -runtime=${runtime:=120} -group_reporting -name=write_${block_size:="4k"}
	echo "${block_size:="4k"} 块顺序写结束"

	echo "${block_size:="4k"} 块随机读开始"
	fio -filename=/greatdb/test/fio-${block_size:="4k"}-randread -direct=1 -iodepth ${iodepth:=32} -thread -rw=randread -ioengine=libaio -bs=${block_size:="4k"} -size=1G -numjobs=${numjobs:=32} -runtime=${runtime:=120} -group_reporting -name=randread_${block_size:="4k"}
	echo "${block_size:="4k"} 块随机读结束"

	echo "${block_size:="4k"} 块随机写开始"
	fio -filename=/greatdb/test/fio-${block_size:="4k"}-randwrite -direct=1 -iodepth ${iodepth:=32} -thread -rw=randwrite -ioengine=libaio -bs=${block_size:="4k"} -size=1G -numjobs=${numjobs:=32} -runtime=${runtime:=120} -group_reporting -name=randwrite_${block_size:="4k"}
	echo "${block_size:="4k"} 块随写读结束"

	echo "${block_size:="4k"} 块顺序读写开始"
	fio -filename=/greatdb/test/fio-${block_size:="4k"}-rw -direct=1 -iodepth ${iodepth:=32} -thread -rw=rw -ioengine=libaio -bs=${block_size:="4k"} -size=1G -numjobs=${numjobs:=32} -runtime=${runtime:=120} -group_reporting -name=rw_${block_size:="4k"}
	echo "${block_size:="4k"} 块顺序读写结束"

	echo "${block_size:="4k"} 块随机读写开始"
	fio -filename=/greatdb/test/fio-${block_size:="4k"}-randrw -direct=1 -iodepth ${iodepth:=32} -thread -rw=randrw -ioengine=libaio -bs=${block_size:="4k"} -size=1G -numjobs=${numjobs:=32} -runtime=${runtime:=120} -group_reporting -name=randrw_${block_size:="4k"}
	echo "${block_size:="4k"} 块随机读写结束"
}

cd /greatdb/test
fio_test | tee -a ${fio_out_file}

```


## sysbench


```bash
sysbench --threads=5 --report-interval=5 --time=600 --test=fileio --file-num=10 --file-total-size=10G --file-test-mode=rndrw prepare

sysbench --threads=5 --report-interval=5 --time=600 --test=fileio --file-num=10 --file-total-size=10G --file-test-mode=rndrw run

```




### 案例

```sql
2025-01-07T15:47:07 [root@dts182 /dts/tmp/iotest] $ sysbench --threads=5 --report-interval=5 --time=600 --test=fileio --file-num=10 --file-total-size=10G --file-test-mode=rndrw prepare
WARNING: the --test option is deprecated. You can pass a script name or path on the command line without any options.
sysbench 1.0.17 (using system LuaJIT 2.0.4)

10 files, 1048576Kb each, 10240Mb total
Creating files for the test...
Extra file open flags: (none)
Creating file test_file.0
Creating file test_file.1
Creating file test_file.2
Creating file test_file.3
Creating file test_file.4
Creating file test_file.5
Creating file test_file.6
Creating file test_file.7
Creating file test_file.8
Creating file test_file.9
10737418240 bytes written in 87.14 seconds (117.51 MiB/sec).
2025-01-07T15:48:56 [root@dts182 /dts/tmp/iotest] $ sysbench --threads=5 --report-interval=5 --time=600 --test=fileio --file-num=10 --file-total-size=10G --file-test-mode=rndrw run
WARNING: the --test option is deprecated. You can pass a script name or path on the command line without any options.
sysbench 1.0.17 (using system LuaJIT 2.0.4)

Running the test with following options:
Number of threads: 5
Report intermediate results every 5 second(s)
Initializing random number generator from current time


Extra file open flags: (none)
10 files, 1GiB each
10GiB total file size
Block size 16KiB
Number of IO requests: 0
Read/Write ratio for combined random IO test: 1.50
Periodic FSYNC enabled, calling fsync() each 100 requests.
Calling fsync() at the end of test, Enabled.
Using synchronous I/O mode
Doing random r/w test
Initializing worker threads...

Threads started!

[ 5s ] reads: 37.67 MiB/s writes: 25.11 MiB/s fsyncs: 399.00/s latency (ms,95%): 0.989
[ 10s ] reads: 22.50 MiB/s writes: 15.00 MiB/s fsyncs: 240.83/s latency (ms,95%): 0.971
[ 15s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 20s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 25s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 30s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 35s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 40s ] reads: 35.25 MiB/s writes: 23.50 MiB/s fsyncs: 376.00/s latency (ms,95%): 1.063
[ 45s ] reads: 5.44 MiB/s writes: 3.62 MiB/s fsyncs: 57.40/s latency (ms,95%): 1.007
[ 50s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 55s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 60s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 65s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 70s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 75s ] reads: 10.31 MiB/s writes: 6.88 MiB/s fsyncs: 111.20/s latency (ms,95%): 1.082
[ 80s ] reads: 3.56 MiB/s writes: 2.37 MiB/s fsyncs: 37.40/s latency (ms,95%): 0.920
[ 85s ] reads: 74.25 MiB/s writes: 49.50 MiB/s fsyncs: 791.81/s latency (ms,95%): 0.937
[ 90s ] reads: 63.64 MiB/s writes: 42.42 MiB/s fsyncs: 680.19/s latency (ms,95%): 0.971
[ 95s ] reads: 78.11 MiB/s writes: 52.08 MiB/s fsyncs: 831.39/s latency (ms,95%): 0.971
[ 100s ] reads: 13.88 MiB/s writes: 9.25 MiB/s fsyncs: 148.60/s latency (ms,95%): 0.920
[ 105s ] reads: 25.13 MiB/s writes: 16.75 MiB/s fsyncs: 267.81/s latency (ms,95%): 0.989
[ 110s ] reads: 17.81 MiB/s writes: 11.87 MiB/s fsyncs: 189.79/s latency (ms,95%): 0.954
[ 115s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 120s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 125s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 130s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 135s ] reads: 19.31 MiB/s writes: 12.87 MiB/s fsyncs: 206.40/s latency (ms,95%): 1.250
[ 140s ] reads: 39.38 MiB/s writes: 26.25 MiB/s fsyncs: 420.20/s latency (ms,95%): 0.920
[ 145s ] reads: 70.12 MiB/s writes: 46.75 MiB/s fsyncs: 747.00/s latency (ms,95%): 0.920
[ 150s ] reads: 17.63 MiB/s writes: 11.75 MiB/s fsyncs: 189.20/s latency (ms,95%): 0.989
[ 155s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 160s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 165s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 170s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 175s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 180s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 185s ] reads: 21.75 MiB/s writes: 14.50 MiB/s fsyncs: 231.20/s latency (ms,95%): 0.856
[ 190s ] reads: 1.31 MiB/s writes: 0.87 MiB/s fsyncs: 14.40/s latency (ms,95%): 0.989
[ 195s ] reads: 14.25 MiB/s writes: 9.50 MiB/s fsyncs: 152.40/s latency (ms,95%): 1.007
[ 200s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 205s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 210s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 215s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 220s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 225s ] reads: 9.75 MiB/s writes: 6.50 MiB/s fsyncs: 104.40/s latency (ms,95%): 0.872
[ 230s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 235s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 240s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 245s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 250s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 255s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 260s ] reads: 80.25 MiB/s writes: 53.50 MiB/s fsyncs: 855.01/s latency (ms,95%): 0.954
[ 265s ] reads: 3.00 MiB/s writes: 2.00 MiB/s fsyncs: 32.00/s latency (ms,95%): 0.872
[ 270s ] reads: 20.63 MiB/s writes: 13.75 MiB/s fsyncs: 220.21/s latency (ms,95%): 0.937
[ 275s ] reads: 181.49 MiB/s writes: 121.00 MiB/s fsyncs: 1936.74/s latency (ms,95%): 0.937
[ 280s ] reads: 72.37 MiB/s writes: 48.25 MiB/s fsyncs: 771.78/s latency (ms,95%): 0.937
[ 285s ] reads: 30.19 MiB/s writes: 20.13 MiB/s fsyncs: 320.80/s latency (ms,95%): 0.920
[ 290s ] reads: 56.06 MiB/s writes: 37.38 MiB/s fsyncs: 598.62/s latency (ms,95%): 0.937
[ 295s ] reads: 18.19 MiB/s writes: 12.12 MiB/s fsyncs: 193.40/s latency (ms,95%): 0.989
[ 300s ] reads: 0.19 MiB/s writes: 0.13 MiB/s fsyncs: 2.20/s latency (ms,95%): 0.576
[ 305s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 310s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 315s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 320s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 325s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 330s ] reads: 55.69 MiB/s writes: 37.12 MiB/s fsyncs: 595.40/s latency (ms,95%): 1.025
[ 335s ] reads: 7.50 MiB/s writes: 5.00 MiB/s fsyncs: 79.60/s latency (ms,95%): 0.954
[ 340s ] reads: 79.56 MiB/s writes: 53.03 MiB/s fsyncs: 849.03/s latency (ms,95%): 1.007
[ 345s ] reads: 39.32 MiB/s writes: 26.22 MiB/s fsyncs: 417.99/s latency (ms,95%): 0.971
[ 350s ] reads: 64.69 MiB/s writes: 43.13 MiB/s fsyncs: 691.02/s latency (ms,95%): 0.888
[ 355s ] reads: 2.25 MiB/s writes: 1.50 MiB/s fsyncs: 24.40/s latency (ms,95%): 0.856
[ 360s ] reads: 116.44 MiB/s writes: 77.63 MiB/s fsyncs: 1241.07/s latency (ms,95%): 1.025
[ 365s ] reads: 6.93 MiB/s writes: 4.63 MiB/s fsyncs: 73.80/s latency (ms,95%): 0.920
[ 370s ] reads: 55.32 MiB/s writes: 36.87 MiB/s fsyncs: 590.20/s latency (ms,95%): 0.920
[ 375s ] reads: 25.31 MiB/s writes: 16.88 MiB/s fsyncs: 270.21/s latency (ms,95%): 0.937
[ 380s ] reads: 7.31 MiB/s writes: 4.88 MiB/s fsyncs: 77.20/s latency (ms,95%): 0.797
[ 385s ] reads: 50.06 MiB/s writes: 33.37 MiB/s fsyncs: 534.99/s latency (ms,95%): 0.954
[ 390s ] reads: 65.25 MiB/s writes: 43.50 MiB/s fsyncs: 695.59/s latency (ms,95%): 0.937
[ 395s ] reads: 54.38 MiB/s writes: 36.25 MiB/s fsyncs: 579.60/s latency (ms,95%): 0.920
[ 400s ] reads: 9.19 MiB/s writes: 6.13 MiB/s fsyncs: 97.60/s latency (ms,95%): 0.904
[ 405s ] reads: 25.69 MiB/s writes: 17.12 MiB/s fsyncs: 274.59/s latency (ms,95%): 0.954
[ 410s ] reads: 12.00 MiB/s writes: 8.00 MiB/s fsyncs: 127.60/s latency (ms,95%): 0.920
[ 415s ] reads: 3.94 MiB/s writes: 2.63 MiB/s fsyncs: 42.00/s latency (ms,95%): 0.841
[ 420s ] reads: 19.69 MiB/s writes: 13.12 MiB/s fsyncs: 209.99/s latency (ms,95%): 1.063
[ 425s ] reads: 141.76 MiB/s writes: 94.51 MiB/s fsyncs: 1512.68/s latency (ms,95%): 0.937
[ 430s ] reads: 7.50 MiB/s writes: 5.00 MiB/s fsyncs: 79.60/s latency (ms,95%): 0.826
[ 435s ] reads: 98.81 MiB/s writes: 65.88 MiB/s fsyncs: 1054.80/s latency (ms,95%): 0.920
[ 440s ] reads: 59.44 MiB/s writes: 39.62 MiB/s fsyncs: 634.18/s latency (ms,95%): 0.920
[ 445s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 450s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 455s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 460s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 465s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 470s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 475s ] reads: 51.19 MiB/s writes: 34.12 MiB/s fsyncs: 546.19/s latency (ms,95%): 0.989
[ 480s ] reads: 1.69 MiB/s writes: 1.13 MiB/s fsyncs: 17.00/s latency (ms,95%): 0.989
[ 485s ] reads: 63.37 MiB/s writes: 42.25 MiB/s fsyncs: 676.15/s latency (ms,95%): 0.920
[ 490s ] reads: 42.19 MiB/s writes: 28.13 MiB/s fsyncs: 450.83/s latency (ms,95%): 0.937
[ 495s ] reads: 3.56 MiB/s writes: 2.37 MiB/s fsyncs: 38.00/s latency (ms,95%): 0.920
[ 500s ] reads: 97.13 MiB/s writes: 64.75 MiB/s fsyncs: 1035.01/s latency (ms,95%): 0.954
[ 505s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 510s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 515s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 520s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 525s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 530s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 535s ] reads: 94.31 MiB/s writes: 62.88 MiB/s fsyncs: 1005.61/s latency (ms,95%): 0.920
[ 540s ] reads: 39.56 MiB/s writes: 26.37 MiB/s fsyncs: 421.99/s latency (ms,95%): 0.920
[ 545s ] reads: 9.38 MiB/s writes: 6.25 MiB/s fsyncs: 101.40/s latency (ms,95%): 0.954
[ 550s ] reads: 42.37 MiB/s writes: 28.25 MiB/s fsyncs: 451.18/s latency (ms,95%): 1.007
[ 555s ] reads: 79.31 MiB/s writes: 52.88 MiB/s fsyncs: 846.22/s latency (ms,95%): 0.920
[ 560s ] reads: 72.37 MiB/s writes: 48.25 MiB/s fsyncs: 771.18/s latency (ms,95%): 0.954
[ 565s ] reads: 17.81 MiB/s writes: 11.88 MiB/s fsyncs: 190.20/s latency (ms,95%): 0.841
[ 570s ] reads: 10.69 MiB/s writes: 7.12 MiB/s fsyncs: 113.80/s latency (ms,95%): 1.122
[ 575s ] reads: 18.00 MiB/s writes: 12.00 MiB/s fsyncs: 192.59/s latency (ms,95%): 1.025
[ 580s ] reads: 26.06 MiB/s writes: 17.38 MiB/s fsyncs: 277.61/s latency (ms,95%): 0.856
[ 585s ] reads: 35.19 MiB/s writes: 23.46 MiB/s fsyncs: 376.39/s latency (ms,95%): 0.888
[ 590s ] reads: 8.68 MiB/s writes: 5.80 MiB/s fsyncs: 92.60/s latency (ms,95%): 0.811
[ 595s ] reads: 0.00 MiB/s writes: 0.00 MiB/s fsyncs: 0.00/s latency (ms,95%): 0.001
[ 600s ] reads: 12.75 MiB/s writes: 8.50 MiB/s fsyncs: 135.80/s latency (ms,95%): 1.102

File operations:
    reads/s:                      1476.42
    writes/s:                     984.28
    fsyncs/s:                     246.15

Throughput:
    read, MiB/s:                  23.07
    written, MiB/s:               15.38

General statistics:
    total time:                          601.8995s
    total number of events:              1629207

Latency (ms):
         min:                                    0.00
         avg:                                    1.85
         max:                                33096.06
         95th percentile:                        0.95
         sum:                              3007865.68

Threads fairness:
    events (avg/stddev):           325841.4000/2409.28
    execution time (avg/stddev):   601.5731/0.00


```




