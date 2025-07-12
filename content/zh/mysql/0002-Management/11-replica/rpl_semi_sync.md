---
title: 半同步复制
description: rpl_semi_sync
date: 2023-10-25
weight: 20000


---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;


## 一、安装







## 二、参数详解

让服务器在正常工作负载下运行一段时间并监控日志。如果此步骤导致日志中出现任何警告，请调整应用程序，使其仅使用与 GTID 兼容的功能并且不会生成任何警告。

{{<alert color="danger" title="注意" >}}

- 在每台服务器上执行
- 在进行下一步之前，您必须确保错误日志中没有生成任何警告

{{</alert>}}

#### rpl_semi_sync_source_enabled

rpl_semi_sync_master_enabled

控制是否在源服务器上启用半同步复制。要启用或禁用插件，请分别将此变量设置为ON或OFF （或 1 或 0）。默认值为 OFF。

仅安装了源端半同步复制插件后，此变量才可用。


#### rpl_semi_sync_slave_enabled


#### rpl_semi_sync_master_wait_for_slave_count

源在继续之前必须为每个事务收到的副本确认数。默认情况下 rpl_semi_sync_master_wait_for_slave_count 为1，这意味着半同步复制在收到单个副本确认后继续。此变量的值越小，性能越好。

例如，如果 rpl_semi_sync_master_wait_for_slave_count 为2，则必须在 设置的超时期限之前确认收到事务， rpl_semi_sync_master_timeout 半同步复制才能继续。如果在超时期限内确认收到事务的副本较少，则源将恢复为正常复制。

笔记
这种行为还取决于 rpl_semi_sync_master_wait_no_slave

仅安装了源端半同步复制插件后，此变量才可用。

#### rpl_semi_sync_master_wait_point

此变量控制半同步复制源服务器在向提交事务的客户端返回状态之前等待副本确认事务接收的时间点。允许使用以下值：

AFTER_SYNC（默认）：源将每个事务写入其二进制日志和副本，并将二进制日志同步到磁盘。同步后，源等待副本确认收到事务。收到确认后，源将事务提交给存储引擎并将结果返回给客户端，然后客户端可以继续。

AFTER_COMMIT：源将每个事务写入其二进制日志和副本，同步二进制日志，并将事务提交给存储引擎。提交后，源等待副本确认收到事务。收到确认后，源将结果返回给客户端，然后客户端可以继续。

这些设置的复制特性有以下不同：

使用AFTER_SYNC，所有客户端都会同时看到已提交的事务：在副本确认该事务并将其提交给主服务器上的存储引擎之后。因此，所有客户端都会在主服务器上看到相同的数据。

如果发生源故障，则在源上提交的所有事务都已复制到副本（保存到其中继日志中）。源服务器意外退出并故障转移到副本不会造成损失，因为副本是最新的。但请注意，在这种情况下无法重新启动源，必须将其丢弃，因为其二进制日志可能包含未提交的事务，这些事务在二进制日志恢复后外部化时会导致与副本发生冲突。

使用AFTER_COMMIT，发出事务的客户端仅在服务器提交给存储引擎并收到副本确认后才能获得返回状态。在提交之后和副本确认之前，其他客户端可以在提交客户端之前看到已提交的事务。

如果出现问题导致副本服务器无法处理事务，则在主服务器意外退出并故障转移到副本服务器的情况下，此类客户端可能会看到与在源服务器上看到的数据相关的数据丢失。

仅安装了源端半同步复制插件后，此变量才可用。

随着 MySQL 5.7 中的添加 rpl_semi_sync_master_wait_point ，由于它会增加半同步接口版本，因此创建了版本兼容性约束：MySQL 5.7 及更高版本的服务器不能与旧版本的半同步复制插件一起使用，旧版本的服务器也不能与 MySQL 5.7 及更高版本的半同步复制插件一起使用。



#### rpl_semi_sync_source_timeout

rpl_semi_sync_master_timeout

一个以毫秒为单位的值，用于控制源在提交后等待副本确认的时间，超过该时间则超时并恢复为异步复制。默认值为 10000（10 秒）。

仅安装了源端半同步复制插件后，此变量才可用。

#### rpl_semi_sync_master_wait_point


#### rpl_semi_sync_master_wait_no_slave

控制源是否等待配置的超时时间到期 ，即使 在超时时间内 rpl_semi_sync_master_timeout 副本数量下降到少于配置的副本数量 。rpl_semi_sync_master_wait_for_slave_count

当值为（默认值 rpl_semi_sync_master_wait_no_slave） 时ON，允许副本数 rpl_semi_sync_master_wait_for_slave_count 在超时期限内降至低于。只要有足够的副本在超时期限到期之前确认事务，半同步复制就会继续。

rpl_semi_sync_master_wait_no_slave当的 值为时 ，如果 在配置的超时时间内，副本数随时 OFF下降到小于配置的数量 ，则源将恢复正常复制。 rpl_semi_sync_master_wait_for_slave_countrpl_semi_sync_master_timeout

仅安装了源端半同步复制插件后，此变量才可用。

#### rpl_semi_sync_source_trace_level

rpl_semi_sync_master_trace_level

源服务器上的半同步复制调试跟踪级别。定义了四个级别：

1 = 一般级别（例如，时间功能故障）

16 = 详细程度（更详细的信息）

32 = 网络等待级别（有关网络等待的更多信息）

64 = 功能级别（有关功能进入和退出的信息）

仅安装了源端半同步复制插件后，此变量才可用。


## 使用


#### 单数

```sql



```


#### 双数

```sql


```





