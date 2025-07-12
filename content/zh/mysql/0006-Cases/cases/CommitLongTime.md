---
title: commit耗时
date: 2023-10-12
description: >
  慢日志有出现commit耗时超过1秒的现象


weight: 60000
---

{{< alert >}}
生产环境mysql，慢日志有出现commit耗时超过1秒的现象。

高峰期（08:30-09:40），慢日志会出现大量的commit超过1秒的记录。

非高峰期，binlog轮转时，慢日志会也会出现少量的commit超过1秒的记录。

高峰期磁盘吞吐量在75MB/s左右，磁盘繁忙程度45%左右


binlog_cache_size 参数配置为1MB，存在 Binlog_cache_disk_use 使用磁盘的现象。max_binlog_size 参数配置为512MB，最大可配置大小为1G。

MySQL数据文件与日志文件在OS层的文件系统类型为ext3。MySQL官方基于Linux Filesystems 的最佳实践的建议，ext4比ext3性能更好。

MySQL官方明确8.x binlog rotate 有bug，轮转期间出现性能下降与commit变慢。推测5.7中这个问题也同样存在。
{{< /alert >}}


## 一、环境架构



{{< alert title="配置信息">}}

| 硬件名称      | 配置      |
|:--------------|:----------|
| Arch          | aarch64   |
| OS            | Kylin-v10 |
| CPU           | 64C       |
| Memory        | 512G      |
| Space         | 14T       |
| MySQL Version | 8.0.26    |

{{< /alert >}}

## 二、问题现象

N_ISAG_BB集群主库的CPU资源使用瞬间激增，持续时间为2分钟。




## 三、影响范围

该问题暂时对业务无影响。


## 四、问题分析过程

### （1）问题发生时间
问题发生时间段为2023-10-12 14:26:00至2023-10-12 14:28:00，持续2分钟。

### （2）问题处理过程

1. 在收到告警后，登录数据库后台，查看当前数据库状态。经初步确认，没有发现高耗时的SQL。与报障人员确认后，初步判断问题可能源于历史高消耗SQL，导致了 CPU 资源的激增。

2. 接着查看慢查询日志，分析发现这个时间段产生了慢SQL语句且平均执行时间在20s左右，而在平常该类型语句平均耗时3s以内，因此该时间段内的慢查询是因资源无法满足所致，如下图所示

3. 随后，通过分析该时间段内的binlog日志，发现存在高并发批次插入大量数据行的SQL语句，一次insert数据量最高达到86万条，如下图

4. 根据上述输出信息，我们可以得出以下结论：高并发批次插入大量数据，导致数据库性能下降，进而出现大量慢查询。

## 五、问题原因分析
{{< alert title="原因">}}

N_ISAG_BB系统CPU使用激增问题由高并发批次插入大量数据，导致数据库性能下降出现大量慢查询，并发线程堆积使得CPU使用率激增，innodb层的并发线程处理被慢查询阻塞。

{{< /alert >}}

## 六、解决问题
{{< alert title="建议" color="secondary">}}

commit耗时超过1秒的现象，给出如下建议：

1. 建议在线调大 max_binlog_size，由512M 调整为 1G，减少binlog轮转次数

2. 建议在线调大 binlog_cache_size，由 1M 调整为 4M，减少binlog使用磁盘次数

3. 建议数据库的OS文件系统由ext3调整为ext4或者xfs，磁盘提供更高的性能与稳定性


{{< /alert >}}



[All commits are unnecessarily blocked for more than 1ms at binlog rotate](https://bugs.mysql.com/bug.php?id=101200)

[binlog rotate optimize](https://bugs.mysql.com/bug.php?id=110889)
































