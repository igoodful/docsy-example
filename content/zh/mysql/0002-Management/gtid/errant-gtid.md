---
title: errant-gtid
description: errant-gtid
date: 2017-01-05
weight: 5


---

{{< alert >}}
show engines;





{{< /alert >}}


{{< alert >}}
[errant-gtid](https://percona.community/blog/2021/11/08/the-errant-gtid-pt1/)

{{< /alert >}}


什么是 GTID？ Oracle/MySQL 将 GTID 定义为“全局事务标识符 (GTID) 是创建的唯一标识符，并与在源服务器（源）上提交的每个事务相关联。该标识符不仅对于其发起的服务器是唯一的，但在给定复制拓扑中的所有服务器中是唯一的。”

错误的事务可能会使副本升级为主数据库变得非常困难。

错误的交易是糟糕的。为什么不好？错误的事务可能仍位于副本二进制日志中，因此当它成为新的主副本时，这些事件将被发送到其他副本，从而导致数据损坏或破坏复制。

很容易防止错误交易。
read_only = ON在副本 my.cnf 中
当您需要在副本上执行工作时禁用二进制日志。set session sql_log_bin = 'off';在您处理副本之前。set session sql_log_bin = 'on';当你的工作完成时。
查找并纠正错误交易
如何纠正错误的交易？比较gtid_executed主数据库和副本数据库。识别副本上的错误事务，然后将该事务应用于主副本。

我将在下面的步骤中向您展示一种方法。

在副本运行中show variables like 'gtid_executed'
您将收到与此类似的输出：

```sql
mysql_replica> show variables like 'gtid_executed'\G
*************************** 1. row ***************************
Variable_name: gtid_executed
        Value: 858d4d54-3fe1-11ec-a7e8-080027ae8b99:1,
a6b3751e-3fd3-11ec-a4f5-080027ae8b99:1-2
1 row in set (0.00 sec)
```
记下 gtid_execulated 值。您将需要它来检查是否有错误的交易。

在初次运行时show variables like 'gtid_executed'
您将收到与此类似的输出：

```sql
mysql_primary> show variables like 'gtid_executed'\G
*************************** 1. row ***************************
Variable_name: gtid_executed
        Value: 858d4d54-3fe1-11ec-a7e8-080027ae8b99:1,
a6b3751e-3fd3-11ec-a4f5-080027ae8b99:1-2
1 row in set (0.00 sec)
```
记下 gtid_execulated 值。您将需要它来检查是否有错误的交易。

我们需要确定副本是否有任何错误的事务。我们将使用函数“gtid_subset”来比较从副本和主节点执行的 GTID 集。
```sql
mysql_replica> select gtid_subset('858d4d54-3fe1-11ec-a7e8-080027ae8b99:1,
    '> a6b3751e-3fd3-11ec-a4f5-080027ae8b99:1-2','858d4d54-3fe1-11ec-a7e8-080027ae8b99:1,
    '> a6b3751e-3fd3-11ec-a4f5-080027ae8b99:1-2') as subset;
+--------+
| subset |
+--------+
|      1 |
+--------+
1 row in set (0.00 sec)
```
Subset = 1 告诉我们没有错误的交易。

现在我们需要将错误的事务引入到副本中。让我们通过创建一个新数据库来做一些简单的事情。

mysql_replica> create database community;
Query OK, 1 row affected (0.01 sec)
让我们重复上面的步骤 1。

```sql
mysql_replica> show variables like 'gtid_executed'\G
*************************** 1. row ***************************
Variable_name: gtid_executed
        Value: 858d4d54-3fe1-11ec-a7e8-080027ae8b99:1-2,
a6b3751e-3fd3-11ec-a4f5-080027ae8b99:1-2
1 row in set (0.00 sec)
```
我们将使用副本中的新 gtid_execulated和主数据库中的原始 gtid_execulated重复步骤 3 。

```sql
mysql_replica> select gtid_subset('858d4d54-3fe1-11ec-a7e8-080027ae8b99:1-2,
    '> a6b3751e-3fd3-11ec-a4f5-080027ae8b99:1-2','858d4d54-3fe1-11ec-a7e8-080027ae8b99:1,
    '> a6b3751e-3fd3-11ec-a4f5-080027ae8b99:1-2') as subset;
+--------+
| subset |
+--------+
|      0 |
+--------+
1 row in set (0.00 sec)
```
Subset = 0 告诉我们该副本有错误的事务。

现在我们需要确定错误的交易。我们需要replica executed GTID从 中减去primary executed GTID。为此，我们将使用该gtid_subtract函数。

```sql
mysql_replica> select gtid_subtract('858d4d54-3fe1-11ec-a7e8-080027ae8b99:1-2,
    '> a6b3751e-3fd3-11ec-a4f5-080027ae8b99:1-2','858d4d54-3fe1-11ec-a7e8-080027ae8b99:1,
    '> a6b3751e-3fd3-11ec-a4f5-080027ae8b99:1-2') as errant;
+----------------------------------------+
| errant                                 |
+----------------------------------------+
| 858d4d54-3fe1-11ec-a7e8-080027ae8b99:2 |
+----------------------------------------+
1 row in set (0.00 sec)
```
现在我们从副本中得到了错误的事务858d4d54-3fe1-11ec-a7e8-080027ae8b99:2

修复问题
现在让我们转向主要的。

一旦在主服务器上，我们想要插入一个伪事务来解决副本中的错误事务。

```sql
mysql_primary> set gtid_next='858d4d54-3fe1-11ec-a7e8-080027ae8b99:2';
Query OK, 0 rows affected (0.00 sec)

mysql_primary> begin;
Query OK, 0 rows affected (0.00 sec)

mysql_primary> commit;
Query OK, 0 rows affected (0.00 sec)

mysql_primary> set gtid_next='automatic';
Query OK, 0 rows affected (0.00 sec)
```
我们可以比较从副本和主节点再次执行的 GTID。

基本的：
```sql
mysql_primary> show variables like 'gtid_executed'\G
*************************** 1. row ***************************
Variable_name: gtid_executed
        Value: 858d4d54-3fe1-11ec-a7e8-080027ae8b99:1-2,
a6b3751e-3fd3-11ec-a4f5-080027ae8b99:1-2
1 row in set (0.00 sec)
```
复制品：
```sql
mysql_replica> show variables like 'gtid_executed'\G
*************************** 1. row ***************************
Variable_name: gtid_executed
        Value: 858d4d54-3fe1-11ec-a7e8-080027ae8b99:1-2,
a6b3751e-3fd3-11ec-a4f5-080027ae8b99:1-2
1 row in set (0.00 sec)
```
请注意，两个值匹配。我们已经修复了从副本到主服务器的错误事务。

现在我们需要处理有错误事务的副本。我们需要刷新并清除二进制日志。使用以下命令查找当前的二进制文件，然后刷新并清除。记住要在副本上。
```sql
show binary logs;
FLUSH LOGS;
PURGE BINARY LOGS TO 'binlog.00000x';
```
就是这样。您已修复了错误的交易。这是一个相当简单的 GTID 错误示例。我将在第二部分中讨论更复杂的示例。











