---
title: mpstat
description: >
  mpstat是Multiprocessor Statistics的缩写，是实时系统监控工具
date: 2023-10-17
weight: 4
---

{{< alert >}}
### 问题现象

{{< /alert >}}

### 查看CPU核数
```sql

[root@k8s-node-76 ~]# lscpu |grep CPU
CPU op-mode(s):        32-bit, 64-bit
CPU(s):                4
On-line CPU(s) list:   0-3
CPU family:            6
CPU MHz:               2095.076
NUMA node0 CPU(s):     0-3

```


### top

```sql
[root@k8s-node-76 ~]# top
top - 17:06:42 up 2 days,  8:14,  6 users,  load average: 12.83, 5.90, 4.06
Tasks: 191 total,   6 running, 118 sleeping,   0 stopped,   0 zombie
%Cpu(s): 22.3 us, 10.0 sy,  0.0 ni,  5.4 id, 46.5 wa,  0.0 hi,  2.2 si, 13.7 st
KiB Mem :  8142784 total,   196924 free,  4654908 used,  3290952 buff/cache
KiB Swap:        0 total,        0 free,        0 used.  2684884 avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
 2885 mysql     20   0 6029524   3.7g   3792 S  68.1 47.3 112:40.25 mysqld
 9040 mysql     20   0 1286884  64820   5928 S  38.8  0.8  42:05.12 dbscale
 1507 root       0 -20       0      0      0 I  17.0  0.0   0:10.55 kworker/0:1H-kb
 3734 root      20   0  727332  21244   6120 S   2.7  0.3  21:57.23 node_exporter
15548 mysql     20   0 1149420  19676   8020 S   2.7  0.2  52:51.86 dbinit
19236 root      20   0    9972   2924   1464 R   2.7  0.0   0:00.05 rootwrap
 4842 root      20   0 1683312  49940  25368 S   2.1  0.6  59:40.10 calico-node
 1564 root      20   0       0      0      0 D   1.6  0.0   1:21.32 jbd2/sda2-8
 4217 root      20   0 1657096  63716  22388 S   1.6  0.8  48:11.35 kubelet

```


### mpstat详解
{{< alert title="mpstat" >}}
- mpstat -P ALL  [interval]

- mpstat -P ALL  [interval]  [count]

- interval 表示采样间隔

- count 表示采样次数

#### 在internal时间段里，字段分析:

1. CPU: 每个CPU的process id

2. %usr: 用户态下，cpu的利用率

3. %nice: 具有nice优先级的用户下，CPU的使用率

4. %sys: 显示在kernel内核态执行时发生的CPU利用率百分比。这不包括维护硬件和软件中断所花费的时间。

5. %iowait: 显示系统有未完成的磁盘I/O请求时，CPU或CPU处于空闲状态的时间百分比

6. %irq: 系统服务于硬中断的时间开销的百分比

7. %soft: 系统服务于软中断开销的时间开销百分比

8. %steal: 显示虚拟CPU或CPU在虚拟机监控程序为另一个虚拟处理器提供服务时被迫等待所花费的时间百分比

9. %guest: CPU处理虚拟进程的花费的时间开销

10. %idel: CPU空闲百分比

{{< /alert >}}

```sql
# "查看多核CPU核心的当前运行状况信息， 每2秒更新一次，生成一个"

[root@k8s-node-76 bin]# mpstat  -P ALL 2 1
Linux 5.4.257-1.el7.elrepo.x86_64 (k8s-node-76) 	10/20/2023 	_x86_64_	(4 CPU)

03:27:06 PM  CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
03:27:08 PM  all   13.11    0.00    4.29    0.00    0.00    0.13    0.00    0.00    0.00   82.47
03:27:08 PM    0   11.17    0.00    4.06    0.00    0.00    0.00    0.00    0.00    0.00   84.77
03:27:08 PM    1   15.08    0.00    4.02    0.00    0.00    0.50    0.00    0.00    0.00   80.40
03:27:08 PM    2   11.11    0.00    4.04    0.00    0.00    0.00    0.00    0.00    0.00   84.85
03:27:08 PM    3   15.66    0.00    4.55    0.00    0.00    0.00    0.00    0.00    0.00   79.80

Average:     CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
Average:     all   13.11    0.00    4.29    0.00    0.00    0.13    0.00    0.00    0.00   82.47
Average:       0   11.17    0.00    4.06    0.00    0.00    0.00    0.00    0.00    0.00   84.77
Average:       1   15.08    0.00    4.02    0.00    0.00    0.50    0.00    0.00    0.00   80.40
Average:       2   11.11    0.00    4.04    0.00    0.00    0.00    0.00    0.00    0.00   84.85
Average:       3   15.66    0.00    4.55    0.00    0.00    0.00    0.00    0.00    0.00   79.80


[root@k8s-node-76 bin]# mpstat  -P ALL 2 2
Linux 5.4.257-1.el7.elrepo.x86_64 (k8s-node-76) 	10/20/2023 	_x86_64_	(4 CPU)

03:27:16 PM  CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
03:27:18 PM  all   13.75    0.00    4.29    0.00    0.00    0.13    0.00    0.00    0.00   81.84
03:27:18 PM    0   12.50    0.00    4.00    0.00    0.00    0.00    0.00    0.00    0.00   83.50
03:27:18 PM    1   14.50    0.00    6.00    0.50    0.00    0.50    0.00    0.00    0.00   78.50
03:27:18 PM    2   11.68    0.00    3.55    0.00    0.00    0.00    0.00    0.00    0.00   84.77
03:27:18 PM    3   15.66    0.00    4.04    0.00    0.00    0.51    0.00    0.00    0.00   79.80

03:27:18 PM  CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
03:27:20 PM  all   11.71    0.00    4.66    0.00    0.00    0.25    0.00    0.00    0.00   83.38
03:27:20 PM    0   11.11    0.00    3.54    0.00    0.00    0.51    0.00    0.00    0.00   84.85
03:27:20 PM    1   11.62    0.00    4.04    0.00    0.00    0.00    0.00    0.00    0.00   84.34
03:27:20 PM    2   12.63    0.00    6.06    0.00    0.00    0.00    0.00    0.00    0.00   81.31
03:27:20 PM    3   11.68    0.00    4.57    0.00    0.00    0.51    0.00    0.00    0.00   83.25

Average:     CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
Average:     all   12.73    0.00    4.47    0.00    0.00    0.19    0.00    0.00    0.00   82.61
Average:       0   11.81    0.00    3.77    0.00    0.00    0.25    0.00    0.00    0.00   84.17
Average:       1   13.07    0.00    5.03    0.25    0.00    0.25    0.00    0.00    0.00   81.41
Average:       2   12.15    0.00    4.81    0.00    0.00    0.00    0.00    0.00    0.00   83.04
Average:       3   13.67    0.00    4.30    0.00    0.00    0.51    0.00    0.00    0.00   81.52




```




### pidstat








