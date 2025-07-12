---
title: 简介
description: TiDB
date: 2025-05-26
weight: 100
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

Backup & Restore (BR) 是一个对 TiDB 进行分布式备份和恢复的工具，可以高效地对大数据量的 TiDB 集群进行数据备份和恢复。

{{< /alert >}}

## 源码

- https://github.com/pingcap/tidb.git
- https://github.com/tikv/tikv.git
- https://github.com/tikv/pd.git


```bash
go install golang.org/x/tools/gopls@latest




```








## 图解

| 组件              | 默认端口 | 说明                                              |
|:------------------|:---------|:------------------------------------------------|
| TiDB              | 4000     | 应用及 DBA 工具访问通信端口                       |
| TiDB              | 10080    | TiDB 状态信息上报通信端口                         |
| TiKV              | 20160    | TiKV 通信端口                                     |
| TiKV              | 20180    | TiKV 状态信息上报通信端口                         |
| PD                | 2379     | 提供 TiDB 和 PD 通信端口                          |
| PD                | 2380     | PD 集群节点间通信端口                             |
| TiFlash           | 9000     | TiFlash TCP 服务端口                              |
| TiFlash           | 3930     | TiFlash RAFT 服务和 Coprocessor 服务端口          |
| TiFlash           | 20170    | TiFlash Proxy 服务端口                            |
| TiFlash           | 20292    | Prometheus 拉取 TiFlash Proxy metrics 端口        |
| TiFlash           | 8234     | Prometheus 拉取 TiFlash metrics 端口              |
| CDC               | 8300     | CDC 通信接口                                      |
| Monitoring        | 9090     | Prometheus 服务通信端口                           |
| Monitoring        | 12020    | NgMonitoring 服务通信端口                         |
| Node_exporter     | 9100     | TiDB 集群每个节点的系统信息上报通信端口           |
| Blackbox_exporter | 9115     | Blackbox_exporter 通信端口，用于 TiDB 集群端口监控 |
| Grafana           | 3000     | Web 监控服务对外服务和客户端(浏览器)访问端口      |
| Alertmanager      | 9093     | 告警 web 服务端口                                 |
| Alertmanager      | 9094     | 告警通信端口                                      |

```viz-dot
digraph "tidb" {
        rankdir = TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
        ];

        "tidb" -> "tidb server"[label="4000"];
        "tidb server" -> "pd";
        "pd" -> "storage cluster";
        "storage cluster" -> "tikv";
        "storage cluster" -> "tiflash";
}
```


## tidb server

- 无状态，不存储数据
- 横向扩展，以解决大量会话导致内存不足。会话数过高就扩容
- 供业务连接
- 解析SQL
- 优化SQL
- 生成执行计划
- 利用lvs将流量分给多个tidb server


```viz-dot
digraph "tidb server" {
        rankdir = TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
        ];

        "tidb server" -> "业务连接";
        "tidb server" -> "SQL解析、编译、执行计划";
        "tidb server" -> "关系型数据与KV的相互转化";
        "tidb server" -> "SQL语句的执行";
        "tidb server" -> "online ddl";
        "tidb server" -> "垃圾回收gc";
}
```



```viz-dot
digraph "tidb server" {
        rankdir = TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
        ];

        "tidb server" -> "protocol Layer";
        "protocol Layer" -> "Parse"[label="lex,yacc"];
        "Parse" -> "Compile"[label="AST语法树，验证》逻辑优化》物理优化》执行计划"];
        "Compile" -> "Excutor";
        "Excutor" -> "DistSQL";
        "Excutor" -> "KV";
        "Excutor" -> "Transaction";
        "Transaction" -> "KV";
        "tidb server" -> "pd client";
        "KV" -> "tikv client";
        "DistSQL" -> "tikv client";

        "tidb server" -> "GC";
        "tidb server" -> "online ddl";
        "online ddl" -> "schema load";
        "online ddl" -> "worker";
        "online ddl" -> "start job";
        "tidb server" -> "cache table";
        "tidb server" -> "memBuffer";
        "memBuffer" -> "元数据";
        "memBuffer" -> "统计信息";
}
```











## pd

大脑：placement driver


```viz-dot
digraph "pd" {
rankdir = TB;
edge [
fontsize="12"
];
node [
shape = octagon;
fontsize = "12"
];

"pd" -> "存储tikv的元数据";
"pd" -> "全局ID";
"pd" -> "全局事务ID";
"pd" -> "全局时间戳TSO";
"pd" -> "收集集群信息进行调度";
"pd" -> "提供tidb dashboard服务";
        
}
```

- region与tikv的关系
- TSO: 事务开始和结束时间，SQL的开始时间



## OLTP: tikv

```viz-dot
digraph "tikv" {
        rankdir = TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
        ];

        "tikv" -> "数据持久化";
        "tikv" -> "副本强一致性和高可用性";
        "tikv" -> "MVCC";
        "tikv" -> "支持分布式事务";
        "tikv" -> "算子下推 coprocessor";
}
```

- 行存储
- 一个`region`大约`96M~144M`
- 每个`region`至少3个副本构成raft，默认是3副本。
- 横向扩容与缩容
- mvcc
- 分布式事务
- 高可用。
- 两阶段提交的锁信息被持久化到tikv


```viz-dot
digraph "tikv node" {
        rankdir = TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
        ];
"tikv node" -> "Transaction";
"Transaction" -> "MVCC";
"MVCC" -> "raft"[label="一个region至少有3个node副本"];
"raft" -> "rocksdb raft";
"raft" -> "rocksdb kv";
        
}
```



## OLAP: tiflash

- 列存储
- 大表统计与分析






