---
title: 37. disk
description: disk
date: 2025-04-19
weight: 370

---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



{{<alert>}}


{{</alert>}}



## 1. iostat
- `iostat -d 2`
- `iostat -d -p sda`
- `iostat -d -z 2`


```bash
iostat -d 2

```
- `-p`：p选项来显示块设备及其所有分区的统计信息
- `-d 2`：要重复iostat命令，我们可以添加以秒为单位的间隔
- `-z`：默认情况下，iostat命令显示有关系统中存在的所有块设备和分区的信息。如果我们只想打印有关真正活动的磁盘或分区的信息，跳过指标为零的设备，我们可以添加-z选项
- `-o`: 我们可以使用-o选项仅显示实际执行 I/O 活动的进程或线程


## 2. iotop

默认情况下，iotop命令显示每个进程或线程的磁盘 I/O 视图

我们可以使用-o选项仅显示实际执行 I/O 活动的进程或线程

我们可以添加-P选项来显示没有线程的进程列表

```bash
iotop

iotop -o

iotop -oP

```


## 3. sar


```bash
sar -b 1 

sar -p -d -b 1

sar 2 5 -o /tmp/data_io > /dev/null 2>&1

sar -f /tmp/data_io


```


## 4. vmstat


指定进程占用内存监控

```bash
vmstat -d 1

vmstat -p /dev/sda2 1 

```








