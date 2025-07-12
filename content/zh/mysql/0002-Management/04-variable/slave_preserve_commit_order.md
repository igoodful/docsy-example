---
title: slave_preserve_commit_order
description: slave_preserve_commit_order
date: 2023-10-25
weight: 20000


---

{{< alert >}}

{{< /alert >}}


## slave_preserve_commit_order=on导致主从延迟很大
- [slave_preserve_commit_order](https://bugs.mysql.com/bug.php?id=103636)
- [slave_preserve_commit_order](https://mp.weixin.qq.com/s/D50vn6n27eNv0_ZiDtN-kw)


客户收到短信告警MySQL主从复制出现延迟
登录从库查询processlist，发现大量会话处于waiting for preceding transaction to commit
使用show slave status\G查看复制状态，sql线程处于Waiting for replica workers to process their queues
查询数据库锁及锁等待信息，会话申请的锁都是granted状态，没有锁等待信息。
用户已知该问题是bug所致，准备重启slave进程。
bug特征：
slave 协调线程：Waiting for slave workers to process their queues
所有slave worker线程：Waiting for perceding transaction to commit
从库gtid_executed: 在2147483647左右



















