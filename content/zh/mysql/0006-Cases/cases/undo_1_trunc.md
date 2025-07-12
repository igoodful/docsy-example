---
title: undo_1_trunc.log
description: undo_1_trunc.log,super_read_only=ON
date: 2024-09-12
weight: 60000
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< note >}}
在`InnoDB`存储引擎中，`undo`在完成事务回滚和`MVCC`之后，就可以`purge`掉了，`purge`后文件大小虽然不会变，但可以被重新分配使用

-[ xtrabackup undo log truncate](https://mp.weixin.qq.com/s/V3q4An7Mk0U5K4mmcWMONA)
{{< /note >}}


## 一、环境信息

{{< alert >}}

| 名称          | 配置       |
| :------------ | :--------- |
| Arch          | x64        |
| OS            | CentOS 7.9 |
| CPU           | 8C         |
| Memory        | 16G        |
| Space         | 200G       |
| MySQL Version | 8.0.26     |

{{< /alert >}}



## 二、故障发生
夜间在项目现场兢兢业业进行项目运维的你，正在为某个数据库集群进行全量数据备份，却在备份的准备阶段，系统就退出了，根本没法完成备份工作。经过多次备份尝试仍然行不通，对现场情况和原因不太清楚。



## 三、故障排查

通过管控报错定位到备份任务日志，看到备份失败的原因均是因为`undo log` 清理导致`undo log` 的`space_id`变化，从而导致`xtrabackup`备份失败。

查看备份节点数据目录，发现在其目录下均存在`undo_1_trunc.log`文件。两个集群从库情况基本一致，也都存在`undo_1_trunc.log`文件。

`undo_1_trunc.log`文件是`undo`表空间`truncate` 时创建、用于记录`truncate`操作的临时日志文件，`truncate`操作完成后自动删除，但`undo_1_trunc.log`文件一直存在，说明`truncate`操作一直在进行中，且文件日期比较早，说明`truncate`操作夯住的时间比较长。

查阅官方bug信息 ，发现有类似的从库undo表空间长时间处于清理状态，该问题是由参数 `super_read_only=ON` 触发的。

- [bug](https://bugs.mysql.com/bug.php?id=104573)

## 四、故障处理

为了减少触发`bug`引起的备份失败，需要调整两个参数：`innodb_max_undo_log_size`和`innodb_purge_rseg_truncate_frequency`


#### 1. `innodb_max_undo_log_size`

这个参数主要用于定义undo表空间的阈值大小。如果undo表空间超过此阈值，则在innodb_undo_log_truncate=ON时，将其标记为truncation。标记为truncation的undo表空间将被置为inactive，不会被新的事务所使用，现有事务允许完成。purge线程清空、释放那些不再需要的回滚段，当undo表空间的所有回滚段释放后，undo表空间会执行一个truncate 操作，undo表空间截断为初始化大小值（16MB）。truncate完成后，undo表空间重新激活，可以分配新的事物。

采取措施：将`innodb_max_undo_log_size`由`2147483648（2G）`调整为8G，减少标记为truncation的可能性。在`innodb_max_undo_log_size`设置较大情况下，在`undo log` 大小达到阈值前被 purge 掉，这样空间就能重用，很难达到阈值，就不会触发 `undo log truncate`，也就不会触发数据备份失败这个 bug了

#### 2. innodb_purge_rseg_truncate_frequency


这个参数是根据调用`purge`的次数，定义释放回退段的频率。在回滚段被释放之前，`undo`表空间不能被`truncate`。通常情况下，清除系统每调用（默认）`128`次`purge`，就释放一次回滚段。加大该值会减低`purge`线程释放回滚段的频率

采取措施：将`innodb_purge_rseg_truncate_frequency`由`16`调整为`128`（默认值），降低释放回滚段的频率，从而降低truncate的频率






