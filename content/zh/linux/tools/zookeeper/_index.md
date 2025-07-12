---
title: zookeeper
description: zookeeper
weight: 20000
---

{{< alert >}}
Zookeeper 使用 ZAB 协议来保证 Zookeeper 的数据一致性，ZAB 协议全称 Zookeeper Atomic Broadcast ，原子消息广播协议。

ZAB 协议的两种工作模式：

- 崩溃恢复
- 消息广播

ZAB 协议定义了三种节点状态：
- Looking：选举状态；
- Following：从节点所处的状态；
- Leading：主节点所处的状态。



{{< /alert >}}

