---
title: CPU
description: CPU
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


{{<alert color="danger">}}

用一句话来总结，iowait是CPU空闲且至少有一个I / O正在进行的时间百分比。

每个 CPU 可以处于四种状态之一：
1. 用户
2. 系统
3. 空闲
4. iowait

{{</alert>}}


## 一、CPU基本信息



| CPU信息 | 命令                                    | 备注 |
| :------ | :-------------------------------------- | :--- |
| CPU型号 | `lscpu        \| grep -i 'Model name'`  |      |
| CPU核数 | `grep processor /proc/cpuinfo  \|wc -l` |      |
| CPU详情 | `lscpu`                                 |      |






## 一、CPU负载


{{<alert color="danger">}}

衡量任务多少。
- 正在执行的任务+等待执行的任务数量越多，则负载越高，等待CPU执行任务的数量越多。
- 正在执行的任务+等待执行的任务数量越少，则负载越低。

{{</alert>}}


#### uptime
```bash
2024-07-15T22:51:48 [root@node1 /root] $ uptime
 23:09:38 up 13:17,  1 user,  load average: 7.43, 7.30, 7.27


```

#### top
```bash
2024-07-15T23:09:38 [root@node1 /root] $ top
top - 23:10:23 up 13:18,  1 user,  load average: 6.95, 7.18, 7.23
Tasks: 126 total,   2 running, 124 sleeping,   0 stopped,   0 zombie
%Cpu(s): 28.3 us, 13.3 sy,  0.0 ni, 36.7 id, 16.7 wa,  0.0 hi,  5.0 si,  0.0 st
KiB Mem :  8008992 total,   138936 free,  6279184 used,  1590872 buff/cache
KiB Swap:        0 total,        0 free,        0 used.  1409464 avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
 5678 mysql     20   0 7445884   5.1g   7868 S  72.2 67.2  83:18.89 mysqld
15395 mysql     20   0 1806872 267084   3860 S  66.7  3.3  36:55.65 dbscale
 1398 root       0 -20       0      0      0 S   5.6  0.0   0:35.11 kworker/3:1H
 1441 root      20   0       0      0      0 D   5.6  0.0   2:16.39 jbd2/sda2-8
15266 root      20   0  162016   2268   1548 R   5.6  0.0   0:00.04 top
    1 root      20   0  191508   3772   1900 S   0.0  0.0   0:03.23 systemd

```








## 二、CPU利用率


{{<alert color="danger">}}

衡量CPU繁忙程度。
- CPU利用率越高，说明CPU一直忙于执行任务，无论是用户态还是内核态。
- 正在执行的任务+等待执行的任务数量越少，则负载越低。

{{</alert>}}

消耗CPU前10排序的进程
```bash
ps aux | sort -k3nr |head -n 10
```

最常用什么指标来描述系统的 CPU 性能？

CPU 使用率，而不是平均负载，CPU 上下文切换

CpuUseRate





```bash
2024-06-05T17:20:58 [root@node77 /root/test] $ cat /proc/stat | grep cpu
cpu  397245 142 363537 178412475 117729 0 15022 12384 0 0
cpu0 147651 6 77934 44445460 8404 0 10690 8487 0 0
cpu1 119901 71 117031 44573629 29205 0 1709 1482 0 0
cpu2 54959 3 71707 44754849 7176 0 1595 1150 0 0
cpu3 74733 60 96864 44638536 72941 0 1027 1264 0 0
2024-06-05T17:20:59 [root@node77 /root/test] $

```

第一行的数值表示的是CPU总的使用情况，所以我们只要用第一行的数字计算就可以了。下表解析第一行各数值的含义：

且均为累计值：$totalCpuTime  =  user  +  nice + system + idle + iowait + irq + softirq + stealstolen + guest + guest\\_nice$

| 参数        | 含义                                                     | 示例      |
| :---------- | :------------------------------------------------------- | :-------- |
| name        | 设备名                                                   | cpu       |
| user        | 处于用户态的运行时间，不包含nice值为负的进程             | 397245    |
| nice        | nice值为负的进程所占用的CPU时间                          | 142       |
| system      | 处于核心态的运行时间                                     | 363537    |
| idle        | 除IO等待时间以外的其它等待时间                           | 178412475 |
| iowait      | IO等待时间                                               | 117729    |
| irq         | 硬中断时间                                               | 0         |
| softirq     | 软中断时间                                               | 15022     |
| stealstolen | 在虚拟环境运行时花费在其他操作系统的时间                 | 12384     |
| guest       | 在Linux内核控制下的操作系统虚拟cpu花费的时间             | 0         |
| guest_nice  | 在Linux内核控制下的操作系统虚拟cpu花费在nice进程上的时间 | 0         |



- us: User time       -> %us = (User + Nice) / totalCpuTime * 100%
- sy: System time     -> %sy = (System + Hard Irq + SoftIRQ) / totalCpuTime
- ni: Nick time       -> %ni = (Nice) / totalCpuTime * 100%
- id: Idle time       -> %id = (Idle) / totalCpuTime * 100%
- wa: Waiting time    -> %wa = (Waiting) / totalCputTime * 100%
- hi: Hard Irq time   -> %hi = (Hard Irq) / totalCpuTime * 100%
- si: SoftIrq time    -> %si = (SoftRQ) / totalCpuTime * 100%
- st: Steal time      -> %st = (Steal) / totalCputTime * 100%

什么是iowait？

顾名思义，就是系统因为io导致的进程wait。再深一点讲就是：这时候系统在做io，导致没有进程在干活，cpu在执行idle进程空转，所以说iowait的产生要满足两个条件，一是进程在等io，二是等io时没有进程可运行。

```bash
iostat -c 1 2 | awk 'NF { print $4 }' | tail -n 1
```


