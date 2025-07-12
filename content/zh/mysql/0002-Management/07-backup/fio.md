---
title: fio
description: fio
date: 2023-10-13
weight: 20000
viz: true

---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}
## 概念
- **磁盘I/O**：I/O，即input/output，磁盘的输入输出，输入指的是对磁盘写入数据，输出指的是从磁盘读出数据，磁盘I/O可以理解为读写。应用发起的一次或多次数据请求，I/O请求的数据量又称I/O大小，单位为KiB，例如4KiB、256KiB、1024KiB等；

- **磁盘IOPS**：磁盘IOPS是指一秒内磁盘进行多少次I/O读写。常见磁盘的随机读写最大IOPS为：
  - 7200rpm的磁盘 IOPS = 76 IOPS
  - 10000rpm的磁盘IOPS = 111 IOPS
  - 15000rpm的磁盘IOPS = 166 IOPS
- **磁盘吞吐量**：每秒磁盘I/O的流量，即磁盘写入加上读出的数据的大小。
- **延迟latency**
- **带宽BW**

综上，磁盘I/O、IOPS和吞吐量的关系公式为：
> 吞吐量 = IOPS * I/O大小
从上述公式可以看出，磁盘I/O越大，IOPS越高，那么磁盘那么每秒I/O的吞吐量就越高，云服务器吧认为IOPS和吞吐量的数值越高越好，实际上磁盘IOPS和吞吐量两个参数是有其最大值的。
- 当应用的I/O大小较大，例如离线分析、数据仓库等应用，建议您选择吞吐量更大的大数据型实例规格族。
- 当应用的I/O对时延较为敏感，比较随机且I/O大小相对较小，例如OLTP事务型数据库、企业级应用，如SAP等应用，建议您选择IOPS更高的ESSD云盘、SSD云盘。


虽然15000rpm的磁盘计算出的理论最大IOPS仅为166，但在实际运行环境中，实际磁盘的IOPS往往能够突破200甚至更高。这其实就是在系统调用过程中，操作系统进行了一系列的优化。


磁盘吞吐量：每秒磁盘I/O的流量，即磁盘写入加上读出的数据的大小。
{{< /alert >}}



## 磁盘性能指标



#### IOPS
- 机械硬盘平均IOPS为`100 ~ 150`
- 固态硬盘平均IOPS为`1.4M ~ 2.5M`

- 磁盘的每秒读写次数，这个是随机读写考察的重点

**定义**：每秒处理多少次I/O操作。

**示例**：IOPS=11.3k 表示每秒处理11300次读写。

机械硬盘IOPS≈200，SSD可达数万。高IOPS不一定等于高效。



#### 带宽BW
- 机械硬盘顺序带宽约为`180 MB/s`,不超过`200 MB/s`
- 固态硬盘顺序带宽约为`7 GB/s ~ 14 GB/s`


- 磁盘的吞吐量，这个是顺序读写考察的重点
**定义**：每秒传输的数据量。

**示例**：BW=43.0MiB/s 表示每秒传输43兆字节。

换算关系：BW = IOPS × 块大小（如4k块达到43MiB/s需要：43*1024/4 ≈ 11k IOPS）。


#### 延迟latency
- 机械硬盘平均延迟必须小于15毫秒
- 固态硬盘平均延迟必须小于50微妙

**定义**：

**示例**：



分层理解：

slat（提交延迟）：系统处理请求的时间。

clat（完成延迟）：设备实际处理时间。

lat（总延迟）：slat + clat。




## 安装 fio
```sql
sudo yum install libaio -y

sudo yum install libaio-devel -y

sudo yum install fio -y

fio --version

fio --help
```




**FIO参数说明**

| 参数                    | 说明                                                                     | 取值示例 |
|:------------------------|:-----------------------------------------------------------------------|:---------|
| **numjobs**             | 表示测试的并发线程数。默认值：1。                                           | 4        |
| **cpus_allowed_policy** | FIO提供了参数cpus_allowed_policy以及cpus_allowed来绑定vCPU。              | split    |
| **bs**                  | 表示I/O单元的块大小（block size）。默认值：4 KiB。                            | 2k       |
| **runtime**             | 表示测试时间，即FIO运行时长。                                              | 30s      |
| **group_reporting**     | 表示测试结果显示模式                                                     |          |
| **rw**                  | 表示测试时的读写策略。                                                    | write    |
| **ioengine**            | 表示测试时FIO选择哪种I/O引擎，通常选择libaio，更符合日常应用模式。          | libaio   |
| **size**                | 表示测试文件大小。<br> FIO会将指定的文件大小全部读/写完成，然后才停止测试。 | 1G       |
| **filename**            | 表示待测试的对象路径，路径可以是云盘设备名称或者一个文件地址。             | /dev/sda |
| **name**                | 表示测试任务名称，可以随意设定。                                           | test     |



## 参数选项


####  `rw`

`I/O`模式:

- 完全顺序读：`read`
- 完全顺序写：`write`
- 完全随机读：`randread`
- 完全随机写：`randwrite`
- 完全顺序读写：`rw`
- 完全随机读写：`randrw`


#### `rwmixrea`

`--rwmixread=70`: 表示70%读+30%写的事务负载模拟，如银行核心系统70/30。


#### bs

- 默认4k
- 测试顺序读写时可以调大。



####  `size`

指定每个job处理的文件的大小。

定义的是每个线程的I/O数据量



####  `numjobs`

指定 job 的克隆数(线程)。

使用numjobs参数测试出的效果要好，更能够增加集群客户端测试压力，使测试结果更准确。

指定同时运行的独立I/O线程数量，模拟真实业务中的多任务并发场景。

#### `runtime`

控制测试持续时间（单位：秒）

指定在多少秒后停止进程。如果未指定该参数，fio 将执行至指定的文件读写完全完成。

- 1800
- =600


#### `ioengine`


#### `iodepth`

iodepth参数设置后，集群带宽值几乎不变，反而延时随着iodepth增加而成倍增加。混合设置后也同样如此，随着iodepth的值增加，带宽值没有增加，反而延时成倍增加。所有延时增加的倍数刚好是iodepth增加的倍数。

I/O 队列深度，表示每个线程（job）同时向设备提交的未完成I/O请求数量，主要用来验证压测存储设备的并行处理能力（主要为SSD）。

- 企业级 NVMe SSD：对持续高并发&低延迟要求较高，适合深度 32-64。
- 消费级 SSD：对爆发性能要求高，偏间歇性负载，适合深度 16-32。
- 云硬盘（如 EBS）：受虚拟化层限制，适合深度 8-16。
- 机械硬盘（HDD）：受物理寻道时间限制，适合深度 4-8。


#### `direct`

- 默认为 0
- 如果设置成 1，表示不使用 io buffer。建议为1，排除缓存干扰

#### `name`

指定 job 的名字

#### `filename`

指定文件 (设备) 的名称



## 解读




#### 延迟

- usec是微秒
- msec是毫秒


- slat 提交延迟
  - min为最小值
  - max为最大值
  - avg为平均值
  - stdev为标准偏差
- clat 完成延迟
  - min为最小值
  - max为最大值
  - avg为平均值
  - stdev为标准偏差
- lat 总延迟=响应时间
  - min为最小值
  - max为最大值
  - avg为平均值
  - stdev为标准偏差
- clat percentiles：延迟百分比
  - 1.00th=[  1385] 表示延迟低于1385微秒的占1%,
  - 同理99.50th=[ 26870]表示延迟低于26870微秒的占99.5%


#### IOPS BW

`read: IOPS=1030, BW=1030MiB/s (1080MB/s)(604GiB/600020msec)`

`write: IOPS=1032, BW=1032MiB/s (1083MB/s)(605GiB/600020msec)`

- `read/write`: 统计信息所针对的I/O方向
- `IOPS`: 平均每秒I/O操作次数
- `BW`: 平均带宽速率（二进制单位，1MiB=1048576B）


#### bw

基于样本的带宽统计



#### iops

基于样本的OPS统计


#### cpu




#### IO depths


#### IO submit





#### IO complete


#### IO issued rwts


#### IO latency




#### util


磁盘利用率。 值为100％表示我们使磁盘一直处于繁忙状态，而50％的磁盘将有一半的时间处于空闲状态。


## 案例


#### 案例一

```bash
[mysql@node-250 /home/mysql] $ fio --ioengine=libaio --iodepth=10 --numjobs=2   --name=test_randwriteread --rw=randrw --bs=1M --size=6G --group_reporting --direct=1 -filename=./testfile --runtime=600 --time_based
test_randwriteread: (g=0): rw=randrw, bs=(R) 1024KiB-1024KiB, (W) 1024KiB-1024KiB, (T) 1024KiB-1024KiB, ioengine=libaio, iodepth=10
...
fio-3.7
Starting 2 processes
Jobs: 2 (f=2): [m(2)][100.0%][r=1224MiB/s,w=1224MiB/s][r=1224,w=1224 IOPS][eta 00m:00s]
test_randwriteread: (groupid=0, jobs=2): err= 0: pid=24181: Wed Apr 30 14:08:51 2025
   read: IOPS=988, BW=989MiB/s (1037MB/s)(579GiB/600006msec)
    slat (usec): min=35, max=9046, avg=73.61, stdev=90.84
    clat (nsec): min=1596, max=7857.1M, avg=1669002.85, stdev=28347841.77
     lat (usec): min=275, max=7857.2k, avg=1743.36, stdev=28353.04
    clat percentiles (usec):
     |  1.00th=[    408],  5.00th=[    519], 10.00th=[    586],
     | 20.00th=[    685], 30.00th=[    758], 40.00th=[    824],
     | 50.00th=[    889], 60.00th=[    955], 70.00th=[   1029],
     | 80.00th=[   1123], 90.00th=[   1270], 95.00th=[   1418],
     | 99.00th=[   1942], 99.50th=[   2704], 99.90th=[   9372],
     | 99.95th=[ 792724], 99.99th=[1098908]
   bw (  KiB/s): min= 2048, max=792576, per=54.04%, avg=547313.40, stdev=175906.87, samples=2220
   iops        : min=    2, max=  774, avg=534.43, stdev=171.79, samples=2220
  write: IOPS=990, BW=991MiB/s (1039MB/s)(581GiB/600006msec)
    slat (usec): min=49, max=10676, avg=128.99, stdev=205.14
    clat (usec): min=2, max=7894.7k, avg=18305.09, stdev=67778.65
     lat (usec): min=520, max=7894.8k, avg=18434.90, stdev=67785.90
    clat percentiles (usec):
     |  1.00th=[    807],  5.00th=[   1090], 10.00th=[   1369],
     | 20.00th=[   7111], 30.00th=[  10290], 40.00th=[  12125],
     | 50.00th=[  15533], 60.00th=[  18744], 70.00th=[  20841],
     | 80.00th=[  23462], 90.00th=[  28181], 95.00th=[  31327],
     | 99.00th=[  37487], 99.50th=[  40633], 99.90th=[ 893387],
     | 99.95th=[1019216], 99.99th=[1518339]
   bw (  KiB/s): min= 2048, max=718848, per=54.04%, avg=548366.58, stdev=170963.47, samples=2220
   iops        : min=    2, max=  702, avg=535.46, stdev=166.96, samples=2220
  lat (usec)   : 2=0.01%, 4=0.01%, 10=0.01%, 250=0.01%, 500=1.96%
  lat (usec)   : 750=12.66%, 1000=20.25%
  lat (msec)   : 2=23.15%, 4=1.41%, 10=4.92%, 20=18.56%, 50=16.89%
  lat (msec)   : 100=0.01%, 750=0.02%, 1000=0.14%
  cpu          : usr=4.22%, sys=7.83%, ctx=916709, majf=0, minf=74
  IO depths    : 1=0.1%, 2=0.1%, 4=0.1%, 8=100.0%, 16=0.0%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.1%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwts: total=593386,594540,0,0 short=0,0,0,0 dropped=0,0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=10

Run status group 0 (all jobs):
   READ: bw=989MiB/s (1037MB/s), 989MiB/s-989MiB/s (1037MB/s-1037MB/s), io=579GiB (622GB), run=600006-600006msec
  WRITE: bw=991MiB/s (1039MB/s), 991MiB/s-991MiB/s (1039MB/s-1039MB/s), io=581GiB (623GB), run=600006-600006msec

Disk stats (read/write):
  sda: ios=1779718/1783405, merge=0/136, ticks=2266079/19615170, in_queue=21863364, util=100.00%

```

`read: IOPS=988, BW=989MiB/s (1037MB/s)(579GiB/600006msec)`
  - `IOPS`
    - 平均每秒进行了`988`次读
  - `BW`
    - 二进制：`989MiB/s`，即每秒读取数据量为`989M`
    - 十进制：`1037MB/s`,即`989*1024*1024/1000/1000=1037`
  - 读取数据总量与总耗时：`579GiB/600006msec`,即总共读取了`579G`数据量，耗时`600006`毫秒


- `write: IOPS=990, BW=991MiB/s (1039MB/s)(581GiB/600006msec)`
  - `IOPS`
    - 平均每秒进行了`990`次写
  - `BW`
    - 二进制：`991MiB/s`，即每秒写入数据量为`991M`
    - 十进制：`1039MB/s`,即`991*1024*1024/1000/1000=1039`
  - 写入数据总量与总耗时：`581GiB/600006msec`,即总共写入了`581G`数据量，耗时`600006`毫秒

###### 读取延迟

`lat (usec): min=275, max=7857.2k, avg=1743.36, stdev=28353.04`
- 最小延迟：`275`
- 最大延迟：`7857.2k`
- 平均延迟：`1743.36`

```bash
    clat percentiles (usec):
     |  1.00th=[    408],  5.00th=[    519], 10.00th=[    586],
     | 20.00th=[    685], 30.00th=[    758], 40.00th=[    824],
     | 50.00th=[    889], 60.00th=[    955], 70.00th=[   1029],
     | 80.00th=[   1123], 90.00th=[   1270], 95.00th=[   1418],
     | 99.00th=[   1942], 99.50th=[   2704], 99.90th=[   9372],
     | 99.95th=[ 792724], 99.99th=[1098908]
```
- `90.00th=[  1270]`: `90%`的I/O延迟小于等于`1270`微妙
- `95.00th=[  1418]`: `95%`的I/O延迟小于等于`1418`微妙
- `99.00th=[  1942]`: `99%`的I/O延迟小于等于`1942`微妙

###### 写数据延迟

`lat (usec): min=520, max=7894.8k, avg=18434.90, stdev=67785.90`
- 最小延迟：`520`
- 最大延迟：`7894.8k`
- 平均延迟：`18434.90`

```bash
    clat percentiles (usec):
     |  1.00th=[    807],  5.00th=[   1090], 10.00th=[   1369],
     | 20.00th=[   7111], 30.00th=[  10290], 40.00th=[  12125],
     | 50.00th=[  15533], 60.00th=[  18744], 70.00th=[  20841],
     | 80.00th=[  23462], 90.00th=[  28181], 95.00th=[  31327],
     | 99.00th=[  37487], 99.50th=[  40633], 99.90th=[ 893387],
     | 99.95th=[1019216], 99.99th=[1518339]
```
- `90.00th=[  28181]`: `90%`的I/O延迟小于等于`28181`微妙
- `95.00th=[  31327]`: `95%`的I/O延迟小于等于`31327`微妙
- `99.00th=[  37487]`: `99%`的I/O延迟小于等于`37487`微妙










## 测试脚本
```bash
function RunFio
{
 numjobs=$1   # 实例中的测试线程数，例如示例中的10
 iodepth=$2   # 同时发出I/O数的上限，例如示例中的64
 bs=$3        # 单次I/O的块文件大小，例如示例中的4k
 rw=$4        # 测试时的读写策略，例如示例中的randwrite
 size=$5
 filename=$6  # 指定测试文件的名称，例如示例中的/dev/xxx
 nr_cpus=`cat /proc/cpuinfo |grep "processor" |wc -l`
 if [ $nr_cpus -lt $numjobs ];then
     echo "Numjobs is more than cpu cores, exit!"
     exit -1
 fi
 let nu=$numjobs+1
 cpulist=""
 for ((i=1;i<10;i++))
 do
     list=`cat /sys/block/xxx/mq/*/cpu_list | awk '{if(i<=NF) print $i;}' i="$i" | tr -d ',' | tr '\n' ','`
     if [ -z $list ];then
         break
     fi
     cpulist=${cpulist}${list}
 done
 spincpu=`echo $cpulist | cut -d ',' -f 2-${nu}`
 echo $spincpu
 fio --ioengine=libaio --runtime=30s --numjobs=${numjobs} --iodepth=${iodepth} --bs=${bs} --size=${size} --rw=${rw} --filename=${filename} --time_based=1 --direct=1 --name=test --group_reporting --cpus_allowed=$spincpu --cpus_allowed_policy=split
}
echo 2 > /sys/block/xxx/queue/rq_affinity
sleep 5
RunFio 10 128 4k randwrite 1024g /dev/xxx

```

{{<alert color="primary">}}

因测试环境而异，根据实际情况修改test100w.sh脚本。

- 请将所有xxx设置为ESSD云盘实际的设备名，例如nvme1n1。

- 根据实际情况设置RunFio 10 64 4k randwrite /dev/xxx中的10、64、4k、randwrite和/dev/xxx。

- 如果云盘上的数据丢失不影响业务，可以设置filename=[设备名，例如/dev/vdb]；否则，请设置为filename=[具体的文件路径，例如/mnt/test.image]。

`echo 2 > /sys/block/xxx/queue/rq_affinity`

| rq_affinity | 说明                                                                                                                                                                                             |
|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1           | 表示块设备收到I/O完成（I/O Completion）的事件时，这个I/O被发送回处理这个I/O下发流程的vCPU所在Group上处理。在多线程并发的情况下，I/O Completion就可能集中在某一个vCPU上执行，造成瓶颈，导致性能无法提升。 |
| 2           | 表示块设备收到I/O Completion的事件时，这个I/O会在当初下发的vCPU上执行。在多线程并发的情况下，就可以完全充分发挥各个vCPU的性能。                                                                      |




{{</alert>}}


- bw：磁盘的吞吐量，这个是顺序读写考察的重点

- iops：磁盘的每秒读写次数，这个是随机读写考察的重点



## 本地盘性能测试命令

- 测试本地盘的随机写IOPS：
```sql
fio -direct=1 -iodepth=32 -rw=randwrite -ioengine=libaio -bs=4k -numjobs=4 -time_based=1 -runtime=1000 -group_reporting -filename=/dev/xxx -name=test
```
- 测试本地盘的随机读IOPS：
```sql
fio -direct=1 -iodepth=32 -rw=randread -ioengine=libaio -bs=4k -numjobs=4 -time_based=1 -runtime=1000 -group_reporting -filename=/dev/xxx -name=test
```


- 测试本地盘的顺序写吞吐量：
```sql
fio -direct=1 -iodepth=128 -rw=write -ioengine=libaio -bs=128k -numjobs=1 -time_based=1 -runtime=1000 -group_reporting -filename=/dev/xxx -name=test
```


- 测试本地盘的顺序读吞吐量：
```sql
fio -direct=1 -iodepth=128 -rw=read -ioengine=libaio -bs=128k -numjobs=1 -time_based=1 -runtime=1000 -group_reporting -filename=/dev/xxx -name=test
```


- 测试本地盘的随机写时延：
```sql
fio -direct=1 -iodepth=1 -rw=randwrite -ioengine=libaio -bs=4k -numjobs=1 -time_based=1 -runtime=1000 -group_reporting -filename=/dev/xxx -name=test
```


- 测试本地盘的随机读时延：
```sql
fio -direct=1 -iodepth=1 -rw=randread -ioengine=libaio -bs=4k -numjobs=1 -time_based=1 -runtime=1000 -group_reporting -filename=/dev/xxx -name=test
```


- 测试本地盘的顺序写时延：
```sql
fio -direct=1 -iodepth=1 -rw=write -ioengine=libaio -bs=4k -numjobs=1 -time_based=1 -runtime=1000 -group_reporting -filename=/dev/xxx -name=test
```


- 测试本地盘的顺序读时延：
```sql
fio -direct=1 -iodepth=1 -rw=read -ioengine=libaio -bs=4k -numjobs=1 -time_based=1 -runtime=1000 -group_reporting -filename=/dev/xxx -name=test
```

## 云盘性能测试命令
{{<alert color="primary" title="说明">}}
- 本示例中，使用的设备名为/dev/xxx，请您根据实际情况替换。例如需要测试的云盘为/dev/vdb，则将以下示例命令中的/dev/xxx替换为/dev/vdb。

- 本示例命令其他参数取值仅供参考，请您根据实际情况替换。

{{</alert>}}
- 测试云盘的随机写IOPS：
```sql
fio -direct=1 -iodepth=128 -rw=randwrite -ioengine=libaio -bs=4k -size=1G -numjobs=1 -runtime=1000 -group_reporting -filename=/dev/xxx -name=test
```

- 测试云盘的随机读IOPS：
```sql
fio -direct=1 -iodepth=128 -rw=randread -ioengine=libaio -bs=4k -size=1G -numjobs=1 -runtime=1000 -group_reporting -filename=/dev/xxx -name=test
```

- 测试云盘的顺序写吞吐量：
```sql
fio -direct=1 -iodepth=64 -rw=write -ioengine=libaio -bs=1024k -size=1G -numjobs=1 -runtime=1000 -group_reporting -filename=/dev/xxx -name=test
```

- 测试云盘的顺序读吞吐量：
```sql
fio -direct=1 -iodepth=64 -rw=read -ioengine=libaio -bs=1024k -size=1G -numjobs=1 -runtime=1000 -group_reporting -filename=/dev/xxx -name=test
```

- 测试云盘的随机写时延：
```sql
fio -direct=1 -iodepth=1 -rw=randwrite -ioengine=libaio -bs=4k -size=1G -numjobs=1 -group_reporting -filename=/dev/xxx -name=test
```

- 测试云盘的随机读时延：
```sql
fio -direct=1 -iodepth=1 -rw=randread -ioengine=libaio -bs=4k -size=1G -numjobs=1 -group_reporting -filename=/dev/xxx -name=test
```












## 测试云盘的随机写IOPS：
命令：`fio --direct=1 --iodepth=128 --rw=randwrite --ioengine=libaio --bs=4k --size=1G --numjobs=1 --runtime=1000 --group_reporting --filename=/dev/sda --name=Rand_Write_Testing`

```sql
io-rand-write: (g=0): rw=randwrite, bs=(R) 16.0KiB-16.0KiB, (W) 16.0KiB-16.0KiB, (T) 16.0KiB-16.0KiB, ioengine=libaio, iodepth=4
...
fio-3.7
Starting 64 processes
Jobs: 64 (f=64): [w(64)][100.0%][r=0KiB/s,w=43.9MiB/s][r=0,w=2807 IOPS][eta 00m:00s]
io-rand-write: (groupid=0, jobs=64): err= 0: pid=10612: Sun Nov 26 19:22:11 2023
  write: IOPS=1123, BW=17.6MiB/s (18.4MB/s)(353MiB/20099msec)
    slat (usec): min=2, max=366112, avg=269.37, stdev=4832.47
    clat (usec): min=212, max=2478.3k, avg=227094.50, stdev=219556.66
     lat (usec): min=220, max=2478.3k, avg=227364.40, stdev=219931.80
    clat percentiles (msec):
     |  1.00th=[    3],  5.00th=[   17], 10.00th=[   34], 20.00th=[   66],
     | 30.00th=[   95], 40.00th=[  130], 50.00th=[  167], 60.00th=[  207],
     | 70.00th=[  259], 80.00th=[  351], 90.00th=[  498], 95.00th=[  642],
     | 99.00th=[ 1099], 99.50th=[ 1234], 99.90th=[ 1653], 99.95th=[ 1804],
     | 99.99th=[ 2232]
   bw (  KiB/s): min=   31, max= 1120, per=1.58%, avg=284.04, stdev=196.29, samples=2515
   iops        : min=    1, max=   70, avg=17.67, stdev=12.28, samples=2515
  lat (usec)   : 250=0.01%, 500=0.08%, 750=0.04%, 1000=0.07%
  lat (msec)   : 2=0.37%, 4=0.83%, 10=1.84%, 20=2.83%, 50=9.16%
  lat (msec)   : 100=16.34%, 250=36.96%, 500=21.51%, 750=6.88%, 1000=1.68%
  cpu          : usr=0.04%, sys=0.06%, ctx=22708, majf=0, minf=709
  IO depths    : 1=0.3%, 2=0.6%, 4=99.1%, 8=0.0%, 16=0.0%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwts: total=0,22583,0,0 short=0,0,0,0 dropped=0,0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=4

Run status group 0 (all jobs):
  WRITE: bw=17.6MiB/s (18.4MB/s), 17.6MiB/s-17.6MiB/s (18.4MB/s-18.4MB/s), io=353MiB (370MB), run=20099-20099msec

Disk stats (read/write):
  sda: ios=87/22495, merge=10/54, ticks=5757/5108471, in_queue=5102995, util=100.00%

```



## 顺序写
命令：`fio --filename=/dev/sda --iodepth=4 --ioengine=libaio -direct=1 --rw=write --bs=16k --size=2G -numjobs=64 --runtime=20 --group_reporting --name=io-write`

```sql
io-write: (g=0): rw=write, bs=(R) 16.0KiB-16.0KiB, (W) 16.0KiB-16.0KiB, (T) 16.0KiB-16.0KiB, ioengine=libaio, iodepth=4
...
fio-3.7
Starting 64 processes
Jobs: 64 (f=64): [W(64)][100.0%][r=0KiB/s,w=1276MiB/s][r=0,w=81.6k IOPS][eta 00m:00s]
io-write: (groupid=0, jobs=64): err= 0: pid=12272: Sun Nov 26 19:34:33 2023
  write: IOPS=77.0k, BW=1203MiB/s (1262MB/s)(23.5GiB/20007msec)
    slat (nsec): min=1913, max=26581k, avg=9166.88, stdev=41356.95
    clat (usec): min=40, max=317642, avg=3312.97, stdev=4462.58
     lat (usec): min=78, max=317649, avg=3322.27, stdev=4462.30
    clat percentiles (usec):
     |  1.00th=[  1663],  5.00th=[  2057], 10.00th=[  2147], 20.00th=[  2278],
     | 30.00th=[  2343], 40.00th=[  2442], 50.00th=[  2573], 60.00th=[  2769],
     | 70.00th=[  3949], 80.00th=[  4359], 90.00th=[  4686], 95.00th=[  5538],
     | 99.00th=[  9372], 99.50th=[ 10814], 99.90th=[ 19006], 99.95th=[ 44827],
     | 99.99th=[312476]
   bw (  KiB/s): min= 4320, max=24832, per=1.56%, avg=19236.01, stdev=2665.15, samples=2535
   iops        : min=  270, max= 1552, avg=1202.17, stdev=166.57, samples=2535
  lat (usec)   : 50=0.01%, 100=0.01%, 250=0.01%, 500=0.02%, 750=0.02%
  lat (usec)   : 1000=0.06%
  lat (msec)   : 2=3.78%, 4=66.95%, 10=28.46%, 20=0.61%, 50=0.05%
  lat (msec)   : 100=0.03%, 250=0.01%, 500=0.02%
  cpu          : usr=0.52%, sys=1.05%, ctx=476983, majf=0, minf=777
  IO depths    : 1=0.1%, 2=0.1%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwts: total=0,1540626,0,0 short=0,0,0,0 dropped=0,0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=4

Run status group 0 (all jobs):
  WRITE: bw=1203MiB/s (1262MB/s), 1203MiB/s-1203MiB/s (1262MB/s-1262MB/s), io=23.5GiB (25.2GB), run=20007-20007msec

Disk stats (read/write):
  sda: ios=0/1023086, merge=0/491910, ticks=0/2697558, in_queue=2293680, util=98.26%


```


## 随机读

命令：`fio --filename=/dev/sda --iodepth=4 --ioengine=libaio -direct=1 --rw=randread --bs=16k --size=2G -numjobs=64 --runtime=20 --group_reporting --name=io-rand-read`

```sql
io-rand-read: (g=0): rw=randread, bs=(R) 16.0KiB-16.0KiB, (W) 16.0KiB-16.0KiB, (T) 16.0KiB-16.0KiB, ioengine=libaio, iodepth=4
...
fio-3.7
Starting 64 processes
Jobs: 64 (f=64): [r(64)][100.0%][r=118MiB/s,w=0KiB/s][r=7580,w=0 IOPS][eta 00m:00s]
io-rand-read: (groupid=0, jobs=64): err= 0: pid=11147: Sun Nov 26 19:26:31 2023
   read: IOPS=7445, BW=116MiB/s (122MB/s)(2333MiB/20053msec)
    slat (usec): min=2, max=9939, avg=12.36, stdev=49.05
    clat (usec): min=94, max=516940, avg=34318.89, stdev=31278.31
     lat (usec): min=110, max=516954, avg=34331.49, stdev=31279.16
    clat percentiles (usec):
     |  1.00th=[  1319],  5.00th=[  2278], 10.00th=[  2638], 20.00th=[  4686],
     | 30.00th=[ 11600], 40.00th=[ 19530], 50.00th=[ 27395], 60.00th=[ 36963],
     | 70.00th=[ 46924], 80.00th=[ 57410], 90.00th=[ 72877], 95.00th=[ 90702],
     | 99.00th=[135267], 99.50th=[156238], 99.90th=[217056], 99.95th=[250610],
     | 99.99th=[367002]
   bw (  KiB/s): min=  576, max=12160, per=1.56%, avg=1862.96, stdev=1620.30, samples=2560
   iops        : min=   36, max=  760, avg=116.39, stdev=101.27, samples=2560
  lat (usec)   : 100=0.01%, 250=0.01%, 500=0.04%, 750=0.09%, 1000=0.18%
  lat (msec)   : 2=3.54%, 4=14.68%, 10=9.23%, 20=12.80%, 50=32.33%
  lat (msec)   : 100=23.61%, 250=3.43%, 500=0.05%, 750=0.01%
  cpu          : usr=0.10%, sys=0.22%, ctx=137963, majf=0, minf=1733
  IO depths    : 1=0.1%, 2=0.1%, 4=99.9%, 8=0.0%, 16=0.0%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwts: total=149308,0,0,0 short=0,0,0,0 dropped=0,0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=4

Run status group 0 (all jobs):
   READ: bw=116MiB/s (122MB/s), 116MiB/s-116MiB/s (122MB/s-122MB/s), io=2333MiB (2446MB), run=20053-20053msec

Disk stats (read/write):
  sda: ios=147584/3, merge=274/5, ticks=5055895/15, in_queue=4983423, util=96.92%
```

## 顺序读
命令：`fio --filename=/dev/sda --iodepth=4 --ioengine=libaio -direct=1 --rw=read --bs=16k --size=2G -numjobs=64 --runtime=20 --group_reporting --name=io-read`

```sql
io-read: (g=0): rw=read, bs=(R) 16.0KiB-16.0KiB, (W) 16.0KiB-16.0KiB, (T) 16.0KiB-16.0KiB, ioengine=libaio, iodepth=4
...
fio-3.7
Starting 64 processes
Jobs: 64 (f=64): [R(64)][100.0%][r=391MiB/s,w=0KiB/s][r=25.1k,w=0 IOPS][eta 00m:00s]
io-read: (groupid=0, jobs=64): err= 0: pid=11558: Sun Nov 26 19:29:27 2023
   read: IOPS=28.9k, BW=451MiB/s (473MB/s)(9037MiB/20017msec)
    slat (nsec): min=1607, max=7989.6k, avg=7433.54, stdev=17061.43
    clat (usec): min=28, max=179563, avg=8847.23, stdev=12775.25
     lat (usec): min=80, max=179588, avg=8854.83, stdev=12775.31
    clat percentiles (usec):
     |  1.00th=[   668],  5.00th=[   832], 10.00th=[  1074], 20.00th=[  1401],
     | 30.00th=[  1549], 40.00th=[  1729], 50.00th=[  2245], 60.00th=[  2900],
     | 70.00th=[  8029], 80.00th=[ 16909], 90.00th=[ 28181], 95.00th=[ 35390],
     | 99.00th=[ 54789], 99.50th=[ 63701], 99.90th=[ 88605], 99.95th=[ 94897],
     | 99.99th=[103285]
   bw (  KiB/s): min=  832, max=33856, per=1.56%, avg=7226.01, stdev=6381.26, samples=2558
   iops        : min=   52, max= 2116, avg=451.59, stdev=398.84, samples=2558
  lat (usec)   : 50=0.01%, 100=0.01%, 250=0.01%, 500=0.30%, 750=2.51%
  lat (usec)   : 1000=5.10%
  lat (msec)   : 2=38.31%, 4=18.58%, 10=7.33%, 20=11.28%, 50=15.12%
  lat (msec)   : 100=1.45%, 250=0.02%
  cpu          : usr=0.17%, sys=0.45%, ctx=242647, majf=0, minf=1830
  IO depths    : 1=0.1%, 2=0.1%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwts: total=578390,0,0,0 short=0,0,0,0 dropped=0,0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=4

Run status group 0 (all jobs):
   READ: bw=451MiB/s (473MB/s), 451MiB/s-451MiB/s (473MB/s-473MB/s), io=9037MiB (9476MB), run=20017-20017msec

Disk stats (read/write):
  sda: ios=347019/3, merge=216870/5, ticks=2607990/7, in_queue=2444637, util=99.16%


```




## 机械硬盘（HDD）


以下是机械硬盘（HDD）的典型性能参数及详细说明，适用于企业级和消费级场景：

---

### **一、关键性能指标**
| **指标**        | **典型值范围** | **影响因素**            | **场景说明**          |
|---------------|----------------|---------------------|-------------------|
| **随机读 IOPS** | 75-150 IOPS    | 转速、寻道时间、磁头切换  | 数据库查询、虚拟机启动 |
| **随机写 IOPS** | 50-120 IOPS    | 写入策略（直写/回写）     | 日志写入、小文件更新   |
| **顺序读带宽**  | 150-220 MB/s   | 盘片密度、转速、接口带宽  | 大文件传输、视频编辑   |
| **顺序写带宽**  | 120-180 MB/s   | 写入缓存状态、碎片化程度 | 备份操作、数据归档     |
| **平均延迟**    | 5-15 ms        | 寻道时间 + 旋转延迟     | 所有随机访问操作      |
| **最大延迟**    | 20-50 ms       | 寻道失败重试、坏道处理   | 极端情况下的性能波动  |

---

### **二、性能细节分解**
#### **1. 转速与性能关系**
| **转速 (RPM)** | **平均寻道时间** | **旋转延迟** | **理论最大 IOPS** (随机读) |
|----------------|------------------|--------------|----------------------------|
| 5400           | 9-12 ms          | 5.56 ms      | ~70 IOPS                   |
| 7200           | 7-10 ms          | 4.17 ms      | ~100 IOPS                  |
| 10000          | 5-8 ms           | 3.0 ms       | ~130 IOPS                  |
| 15000 (企业级) | 3-6 ms           | 2.0 ms       | ~180 IOPS                  |

**计算公式**：
```
理论最大 IOPS ≈ 1 / (平均寻道时间 + 平均旋转延迟)
```

---

#### **2. 接口与带宽限制**
| **接口类型** | **理论带宽** | **实际顺序带宽** | **适用场景**    |
|--------------|--------------|------------------|-------------|
| SATA III     | 6 Gbps       | 150-220 MB/s     | 消费级/近线存储 |
| SAS 12G      | 12 Gbps      | 200-250 MB/s     | 企业级存储阵列  |

---

### **三、性能对比示例**
#### **1. 典型 7200 RPM HDD 性能**
```text
随机读 IOPS：90-110
随机写 IOPS：80-100
顺序读带宽：180-200 MB/s
顺序写带宽：160-180 MB/s
平均延迟：8-12 ms
```

#### **2. 企业级 15000 RPM HDD 性能**
```text
随机读 IOPS：160-180
随机写 IOPS：140-160
顺序读带宽：210-250 MB/s
顺序写带宽：190-220 MB/s
平均延迟：4-7 ms
```

---

### **四、性能优化方向**
#### **1. 提升随机性能**
- **RAID 0/10**：通过条带化提升 IOPS（但降低可靠性）
- **增大队列深度**：利用NCQ/TCQ技术提升并发处理能力
- **分区对齐**：避免跨磁道访问（减少额外旋转延迟）

#### **2. 提升顺序带宽**
- **大块传输**：使用 1MB 以上块大小（`dd bs=1M`）
- **禁用访问时间记录**：挂载选项 `noatime`
- **预读优化**：调整 `/sys/block/sdX/queue/read_ahead_kb`

---

### **五、与SSD的对比**
| **指标**    | HDD (7200 RPM) | SATA SSD       | NVMe SSD          |
|-----------|----------------|----------------|-------------------|
| 随机读 IOPS | ~100           | 50,000-100,000 | 500,000-1,000,000 |
| 顺序读带宽  | 180 MB/s       | 550 MB/s       | 3,500-7,000 MB/s  |
| 访问延迟    | 5-15 ms        | 0.1-0.2 ms     | 0.02-0.05 ms      |

---

### **六、测试方法示例**
#### **1. FIO 随机读测试**
```bash
fio --name=randread --rw=randread --bs=4k --iodepth=32 \
    --size=10G --runtime=60 --ioengine=libaio --direct=1 \
    --group_reporting --numjobs=4
```

#### **2. DD 顺序写测试**
```bash
dd if=/dev/zero of=testfile bs=1M count=10240 oflag=direct
```

---

### **注意事项**
1. **实际性能受以下因素影响**：
   - 磁盘碎片化程度
   - 写入缓存策略（`/sys/block/sdX/queue/write_cache`）
   - 文件系统类型（EXT4/XFS/Btrfs）
   - 数据温度（冷/热数据分布）

2. **企业级 HDD 特殊优化**：
   - 振动补偿技术（减少多盘干扰）
   - TLER（限时错误恢复，避免RAID失效）
   - 大容量缓存（256MB+）



## 固态硬盘（SSD）

以下是常见固态硬盘（SSD）的性能参数总结，涵盖消费级和企业级产品，结合接口类型（如PCIe 4.0/5.0、SATA）进行分类说明：

---

### **一、消费级固态硬盘性能参数**
#### **1. PCIe 5.0 接口**
- **致态 TiPro9000**
  - **顺序读写**：14.5 GB/s（读）、13.8 GB/s（写）
  - **4K 随机读写**：2147K IOPS（读）、1827K IOPS（写）
  - **延迟**：PCMark 10 测试中平均存取时间低至 2 微秒
  - **特点**：采用长江存储晶栈®Xtacking®4.0 架构，温控优化，功耗仅 8.25W。

- **三星 9100 PRO**
  - **顺序读写**：14.8 GB/s（读）、13.4 GB/s（写）
  - **4K 随机读写**：2200K IOPS（读）、2380K IOPS（写）
  - **延迟**：3DMark 存储测试中平均延迟 27 微秒，游戏加载延迟 34 微秒。

#### **2. PCIe 4.0 接口**
- **Solidigm P44 Pro**
  - **顺序读写**：7.4 GB/s（读）、6.6 GB/s（写）
  - **4K 随机读写**：140 万 IOPS（读）、130 万 IOPS（写）
  - **延迟**：3DMark 测试中平均存取时间 46 微秒。

- **三星 990 PRO**（参考前代 PCIe 4.0）
  - **顺序读写**：7.4 GB/s（读）、6.9 GB/s（写）
  - **4K 随机读写**：120 万 IOPS（读）、110 万 IOPS（写）。

#### **3. SATA 接口**
- **OCZ Vector180**（较早期产品）
  - **顺序读写**：550 MB/s（读）、530 MB/s（写）
  - **4K 随机读写**：接近 100K IOPS
  - **延迟**：典型 SATA SSD 延迟在 50-100 微秒。

---

### **二、企业级固态硬盘性能参数**
#### **1. PCIe 5.0 接口**
- **铠侠 CD8P 系列**
  - **顺序读取**：提升 60%-80%（对比 PCIe 4.0）
  - **4K 随机读写**：2000K IOPS（读）、400K IOPS（写）
  - **延迟**：OLTP 混合负载下低于 1.8 毫秒，随机读取延迟 250 微秒。

- **三星 PM1743**
  - **顺序读写**：13,000 MB/s（读）、6,600 MB/s（写）
  - **4K 随机读写**：2500K IOPS（读）、250K IOPS（写）
  - **能效**：每瓦性能 608 MB/s，功耗降低 30%。

#### **2. PCIe 4.0 接口**
- **美光 9400 NVMe**
  - **顺序读写**：未明确，但 100% 4K 随机读写达 1.6M IOPS
  - **延迟优化**：Aerospike 数据库测试中性能提升 2.1 倍，响应时间缩短 62%。

---

### **三、关键性能影响因素**
1. **NAND 闪存技术**：
   - 如长江存储的晶栈®Xtacking®4.0 架构通过垂直通道优化带宽和密度；
   - 三星 V-NAND TLC 提供高耐久性和速度。

2. **接口与协议**：
   - PCIe 5.0 x4 理论带宽是 PCIe 4.0 的两倍（64 GB/s vs. 32 GB/s）；
   - NVMe 2.0 协议支持多队列和并行处理，提升随机性能。

3. **控制器与缓存**：
   - 独立 DRAM 缓存（如 LPDDR4X）加速数据交互；
   - 多通道架构（如 16 通道）提升并行吞吐量。

4. **延迟与 QoS**：
   - 企业级 SSD 通过优化固件降低尾延迟（如铠侠 CD8P 的 99.999% 延迟稳定性）；
   - 消费级 SSD 依赖 SLC 缓存机制平衡性能与成本（如 Solidigm P44 Pro 的 200GB SLC 缓存）。

---

### **四、对比机械硬盘（HDD）**
| **指标**         | **消费级 SSD（PCIe 4.0）** | **企业级 SSD（PCIe 5.0）** | **机械硬盘（7200 RPM）** |
|----------------|--------------------------|--------------------------|------------------------|
| **顺序带宽**     | 7 GB/s                   | 14 GB/s                  | 180 MB/s               |
| **4K 随机 IOPS** | 1.4M                     | 2.5M                     | 100-150                |
| **延迟**         | 20-50 微秒               | 10-30 微秒               | 5-15 毫秒              |

---

### **总结**
固态硬盘的性能参数因应用场景和接口类型差异显著：
- **消费级**：追求高带宽（如 PCIe 5.0 的 14 GB/s）和低延迟（微秒级），适合游戏和生产力场景。
- **企业级**：强调稳定 IOPS（如 2000K+）和 QoS，适用于 AI、数据库等高负载环境。
- **技术趋势**：PCIe 5.0 和 NVMe 2.0 推动性能突破，同时通过架构优化（如多平面操作、ECC 并行化）提升能效和可靠性。



## 常见固态硬盘性能指标

- 参考：https://zhuanlan.zhihu.com/p/165266063?share_code=PCcEixvAkzIQ&utm_psn=1901176851432445809

- M.2固态
  - PCIE5.0固态
  - PCIE4.0固态
  - PCIE3.0固态
- SATA固态


| **产品/指标**                   | **接口** | **协议**       | **顺序写**   | **顺序读**   | **缓存** | **闪存类型** |
|---------------------------------|----------|----------------|--------------|--------------|----------|--------------|
| **三星;9100 PRO;MZ-VAP2T0BW**   | `M.2`    | `NVMe PCIe5.0` | `13400 MB/s` | `14700 MB/s` | `2 GB`   | `TLC`        |
| **三星;980 PRO;MZ-V8P1T0BW**    | `M.2`    | `NVMe PCIe4.0` | `5000 MB/s`  | `7000 MB/s`  | `1 GB`   | `TLC`        |
| **致态;TiPr9000;TiPr9000 2TB**  | `M.2`    | `NVMe PCIe5.0` | `12500 MB/s` | `14000 MB/s` | `2 GB`   | `TLC`        |
| **Crucail;T500;CT2000T500SSD8** | `M.2`    | `NVMe PCIe4.0` | `7000 MB/s`  | `7400 MB/s`  | `2 GB`   | `TLC`        |
| **SK HYNIX;P41;**               | `M.2`    | `NVMe PCIe4.0` | `6500 MB/s`  | `7000 MB/s`  | `2 GB`   | `TLC`        |
| **铠侠;RC20系列;LRC20Z001TC8**  | `M.2`    | `NVMe PCIe3.0` | `1700 MB/s`  | `2100 MB/s`  | `1 GB`   | `TLC`        |
| **三星;870 EVO;MZ-77E1T0B**     | `SATA`   | `SATA`         | `530 MB/s`   | `560 MB/s`   | `1 GB`   | `TLC`        |


## 常见机械硬盘性能指标

- 参考：https://zhuanlan.zhihu.com/p/356118338

1TB~2TB不推荐机械硬盘了，现在1TB~2TB固态硬盘价格已经很低了，没有必要选择机械硬盘。

- **尺寸-长-宽-高** 单位分别为英寸-mm-mm-mm

| **产品/指标**                         | **接口** | **协议** | **顺序写** | **顺序读** | **转速**    | **缓存** | **容量** | **尺寸-长-宽-高**     |
|-----------------------------------|----------|----------|------------|------------|-------------|----------|----------|-----------------------|
| **希捷;银河系列;ST600MM009**          | `SAS`    | `SAS`    | `MB/s`     | `MB/s`     | `10000 rpm` | `128 M`  | `1.2 T`  | `2.5-100.45-70.1-15`  |
| **希捷;硬盘;ST8000NM017B**            | `SATA`   | `SATA`   | `MB/s`     | `MB/s`     | `7200 rpm`  | `256 M`  | `8 T`    | `3.5-147-101.85-26.1` |
| **西部数据;机械硬盘;WUS721208BLE6L4** | `SATA`   | `SATA`   | `MB/s`     | `MB/s`     | `7200 rpm`  | `256 M`  | `8 T`    | `3.5-147-101.6-26.1`  |




















