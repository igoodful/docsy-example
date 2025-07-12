---
title: MyISAM和InnoDB有什么区别？
description: MyISAM InnoDB
date: 2024-11-14
weight: 100
---

{{< alert >}}


{{< /alert >}}




MySQL 5.5 之前，MyISAM 引擎是 MySQL 的默认存储引擎

MySQL 5.5 版本之后，InnoDB 是 MySQL 的默认存储引擎





## InnoDB支持行锁

InnoDB 支持行级别的锁粒度，MyISAM 不支持，只支持表级别的锁粒度。


## InnoDB支持事务

InnoDB 提供事务支持，实现了 SQL 标准定义了四个隔离级别。

## InnoDB支持外键





## InnoDB支持崩溃后安全恢复




## InnoDB支持支持MVCC




## 索引实现不同

虽然 MyISAM 引擎和 InnoDB 引擎都是使用 B+Tree 作为索引结构，但是两者的实现方式不太一样

InnoDB 引擎中，其数据文件本身就是索引文件。相比 MyISAM，索引文件和数据文件是分离的，其表数据文件本身就是按 B+Tree 组织的一个索引结构，树的叶节点 data 域保存了完整的数据记录。



