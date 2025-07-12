---
title: zk
description: GreatDTS是集评估、迁移、同步和校验功能于一体的数据迁移工具，提供便捷部署、易于使用、快速高效的同构和异构数据复制服务。
date: 2023-10-24
weight: 70000


---

{{< alert >}}
- docker version > 17


{{< /alert >}}


```sql
[root@dts182 bin]# ./jps -l
49265 org.apache.flink.runtime.entrypoint.StandaloneSessionClusterEntrypoint
48532 org.apache.flink.runtime.zookeeper.FlinkZooKeeperQuorumPeer
49540 org.apache.flink.runtime.taskexecutor.TaskManagerRunner
48728 /data/greatdts/dts-cc/webapp/skywalking-webapp.jar
48665 org.apache.skywalking.oap.server.starter.OAPServerStartUp
84575 jdk.jcmd/sun.tools.jps.Jps

[root@dts182 bin]# ./jstat -gc 48532
 S0C    S1C    S0U    S1U      EC       EU        OC         OU       MC     MU    CCSC   CCSU   YGC     YGCT    FGC    FGCT    CGC    CGCT     GCT
 0.0   12288.0  0.0   12288.0 929792.0 225280.0  557056.0   21175.2   44720.0 43536.4 5376.0 5122.0     13    0.145   0      0.000   4      0.006    0.151

```