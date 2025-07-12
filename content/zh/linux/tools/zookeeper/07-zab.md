---
title: 7. ZAB协议
description: ZAB协议
date: 2025-06-19
weight: 700
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}

- https://xie.infoq.cn/article/70472178edd0a86fbf4a42785
- https://www.cnblogs.com/dalianpai/p/12748144.html

{{< /alert >}}



```viz-dot
digraph "zab" {
rankdir = TB;
edge [
        fontsize="12"
        ];
node [
        shape = box;
        fontsize = "12"
];

"zab" -> "myid";
"zab" -> "xid";
"zab" -> "zxid";
"zab" -> "epoch";

}
```



## Paxos


## Raft

## epoch


## ZXID

ZXID是事务编号，每次写数据都会加一， 是一个64位的数字：
- 低32位是一个简单的单调递增的计数器，由 0 开始
- 而高32位则代表Leader周期epoch的编号，每次选出新的 Leader，epoch会递增

- `0x600024f74`: `6`为任期，即`currentEpoch`的值，`00024f74`为计数器
```bash
[root@node147 /root] $ echo stat | nc 127.0.0.1 16309
Zookeeper version: 3.6.2--803c7f1a12f85978cb049af5e4ef23bd8b688715, built on 09/04/2020 12:44 GMT
Clients:
 /127.0.0.1:49388[0](queued=0,recved=1,sent=0)
 /172.17.134.66:60804[1](queued=0,recved=253127,sent=253127)
 /172.17.137.63:44196[1](queued=0,recved=455757,sent=455757)

Latency min/avg/max: 0/1.4405/12095
Received: 708916
Sent: 708915
Connections: 3
Outstanding: 0
Zxid: 0x600024f74
Mode: leader
Node count: 44
Proposal sizes last/min/max: 124/48/125

```



## myid


## 角色

```viz-dot
digraph "role" {
rankdir = TB;
edge [
        fontsize="12"
        ];
node [
        shape = box;
        fontsize = "12"
];

"role" -> "Leader";
"role" -> "Follower";
"role" -> "Observer";

}
```


## 状态

```viz-dot
digraph "FastLeaderElection" {
rankdir = TB;
edge [
        fontsize="12"
        ];
node [
        shape = box;
        fontsize = "12"
];
"FastLeaderElection" -> "LOOKING";
"FastLeaderElection" -> "LEADING";
"FastLeaderElection" -> "FOLLOWING";
"FastLeaderElection" -> "OBSERVING";
"FastLeaderElection" -> "Learner";

}
```



ZooKeeper 服务器有四种工作状态：

 LOOKING：竞选状态，寻找 Leader。当服务器处于该状态时，它会认为当前服务器没有 Leader，因此需要进入 Leader 选举状态。

 FOLLOWING：跟随者状态。表明当前服务器角色是 Follower。

 LEADING：领导者状态。表明当前服务器角色是 Leader。

 OBSERVING：观察者状态。表明当前服务器角色是 Observer。



分别处理投票。针对每一次投票，服务器都需要将其他服务器的投票和自己的投票进行对比，对比规则如下：

a. 优先比较 epoch

b. 检查 zxid，zxid 比较大的服务器优先作为 leader

c. 如果 zxid 相同，那么就比较 myid，myid 较大的服务器作为 leader 服务器




## 广播模式


客户端发起写请求

Leader将客户端请求信息转化为事务Proposal，同时为每个Proposal分配一个事务ID

Leader为每个Follower单独分配一个FIFO的队列，将需要广播的Proposal依次放入到队列中

Follower接收到Proposal后，首先将其以事务日志的方式写入到本地磁盘中，写入成功后给Leader反馈一个ACK响应

Leader接收到半数以上Follower的ACK响应后，即认为消息发送成功，可以发送Commit消息

Leader向所有Follower广播Commit消息，同时自身也会完成事务提交。Follower接收到Commit消息后也会完成事务的提交















