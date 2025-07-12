---
title: rpl_semi_sync_master_wait_point
description: rpl_semi_sync_master_wait_point
date: 2025-06-27
weight: 20000
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;

$ \rightleftharpoons $

{{< alert >}}

{{< /alert >}}


## 主从复制

```viz-dot
digraph "主从复制" {
        rankdir = TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
        ];

        "主从复制" -> "异步复制";
        "主从复制" -> "同步复制";
        "主从复制" -> "半同步复制";
        "主从复制" -> "增强半同步复制";
}
```

#### 异步复制

主库将更新写入Binlog日志文件后，不需要等待数据更新是否已经复制到从库中，就可以继续处理更多的请求。Master将事件写入binlog，但并不知道Slave是否或何时已经接收且已处理。

在异步复制的机制的情况下，如果Master宕机，事务在Master上已提交，但很可能这些事务没有传到任何的Slave上。假设有Master->Salve故障转移的机制，此时Slave也可能会丢失事务。MySQL复制默认是异步复制，异步复制提供了最佳性能。


#### 同步复制

主库将更新写入Binlog日志文件后，需要等待数据更新已经复制到从库中，并且已经在从库执行成功，然后才能返回继续处理其它的请求。同步复制提供了最佳安全性，保证数据安全，数据不会丢失，但对性能有一定的影响。


#### 标准半同步复制

主库提交更新写入二进制日志文件后，等待数据更新写入了从服务器中继日志中，然后才能再继续处理其它请求。该功能确保至少有1个从库接收完主库传递过来的binlog内容已经写入到自己的relay log里面了，才会通知主库上面的等待线程，该操作完毕。
半同步复制，是最佳安全性与最佳性能之间的一个折中。
MySQL 5.5版本之后引入了半同步复制功能，主从服务器必须安装半同步复制插件，才能开启该复制功能。如果等待超时，超过rpl_semi_sync_master_timeout参数设置时间（默认值为10000，表示10秒），则关闭半同步复制，并自动转换为异步复制模式。当master dump线程发送完一个事务的所有事件之后，如果在rpl_semi_sync_master_timeout内，收到了从库的响应，则主从又重新恢复为增强半同步复制。
ACK (Acknowledge character）即是确认字符。

#### 增强半同步复制

增强半同步是在MySQL 5.7引入，其实半同步可以看成是一个过渡功能，因为默认的配置就是增强半同步，所以，大家一般说的半同步复制其实就是增强的半同步复制，也就是无损复制。
增强半同步和半同步不同的是，等待ACK时间不同
rpl_semi_sync_master_wait_point = AFTER_SYNC（默认）
半同步的问题是因为等待ACK的点是Commit之后，此时Master已经完成数据变更，用户已经可以看到最新数据，当Binlog还未同步到Slave时，发生主从切换，那么此时从库是没有这个最新数据的，用户看到的是老数据。
增强半同步将等待ACK的点放在提交Commit之前，此时数据还未被提交，外界看不到数据变更，此时如果发送主从切换，新库依然还是老数据，不存在数据不一致的问题。



## rpl_semi_sync_master_wait_point


```viz-dot
digraph "rpl_semi_sync_master_wait_point" {
rankdir = TB;
edge [
 fontsize="12"
];
node [
shape = octagon;
fontsize = "12"
];

"rpl_semi_sync_master_wait_point" -> "after_sync";
"rpl_semi_sync_master_wait_point" -> "after_commit";
}
```


```sql
[root@127.0.0.1:8032 12:00:15((none))]$  SET GLOBAL init_connect="SET sql_safe_updates=ON";
Query OK, 0 rows affected (0.00 sec)

[root@127.0.0.1:8032 12:00:04((none))]$ show global variables like 'sql_safe_updates';
+------------------+-------+
| Variable_name    | Value |
+------------------+-------+
| sql_safe_updates | OFF   |
+------------------+-------+
1 row in set (0.22 sec)

[root@127.0.0.1:8032 12:02:49((none))]$ show global variables like 'init_connect';
+---------------+-------------------------+
| Variable_name | Value                   |
+---------------+-------------------------+
| init_connect  | SET sql_safe_updates=ON |
+---------------+-------------------------+
1 row in set (0.01 sec)


```

上面查询命令实例表示当前mysql处于安全模式关闭的状态。

`set sql_safe_updates=1; ` 安全模式打开状态

`set sql_safe_updates=0; ` 安全模式关闭状态

在update操作中：当where条件中列（column）没有索引可用且无limit限制时会拒绝更新。where条件为常量且无limit限制时会拒绝更新。

在delete操作中： 当①where条件为常量，②或where条件为空，③或where条件中 列（column）没有索引可用且无limit限制时拒绝删除。



## 半同步和增强半同步的区别



增强半同步将等待ACK的点放在提交Commit之前，此时数据还未被提交，外界看不到数据变更，此时如果发送主从切换，新库依然还是老数据，不存在数据不一致的问题。



