---
title: 6. 四字命令
description: alert
date: 2025-06-19
weight: 600
viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}


{{< /alert >}}

### 服务端配置信息：conf
```bash
[root@k8s-node-77 ~]# echo conf | nc 172.17.134.77 16309
clientPort=16309
secureClientPort=-1
dataDir=/data/zookeeperdata/data/version-2
dataDirSize=1007297307
dataLogDir=/data/zookeeperdata/data/version-2
dataLogSize=1007297307
tickTime=6000
maxClientCnxns=60
minSessionTimeout=12000
maxSessionTimeout=120000
clientPortListenBacklog=-1
serverId=6
initLimit=5
syncLimit=2
electionAlg=3
electionPort=3888
quorumPort=2888
peerType=0
membership:
server.4=172.17.134.76:2888:3888:participant;0.0.0.0:16309
server.5=172.17.134.71:2888:3888:participant;0.0.0.0:16309
server.6=172.17.134.77:2888:3888:participant;0.0.0.0:16309


```


### 服务端的运行状态：stat
```bash
[root@k8s-node-77 ~]# echo stat |nc 172.17.134.77 16309
Zookeeper version: 3.6.2--803c7f1a12f85978cb049af5e4ef23bd8b688715, built on 09/04/2020 12:44 GMT
Clients:
 /172.17.134.77:54666[0](queued=0,recved=1,sent=0)

Latency min/avg/max: 0/4.25/12
Received: 45
Sent: 45
Connections: 1
Outstanding: 0
Zxid: 0x500040444
Mode: follower
Node count: 44


```


### 连接到服务器的客户端信息：cons
```sql
[root@k8s-node-77 ~]# echo cons | nc 172.17.134.71 16309
 /172.17.134.77:34030[0](queued=0,recved=1,sent=0)
 /172.17.134.76:29528[1](queued=0,recved=88407,sent=88409,sid=0x5004f6df2f50011,lop=SETD,est=1704459621986,to=20000,lcxid=0x658fe892,lzxid=0x500040bb0,lresp=195932043,llat=1,minlat=0,avglat=2,maxlat=473)

[root@k8s-node-77 ~]# echo cons | nc 172.17.134.76 16309
 /172.17.134.77:55118[1](queued=0,recved=53856,sent=53857,sid=0x400e51f462f0001,lop=SETD,est=1704528861258,to=20000,lcxid=0x6599de3b,lzxid=0x500040bdf,lresp=4021154556,llat=3,minlat=0,avglat=2,maxlat=513)
 /172.17.134.77:59862[0](queued=0,recved=1,sent=0)
 /172.17.134.71:34804[1](queued=0,recved=88387,sent=88392,sid=0x400ae36d45c000d,lop=SETD,est=1704459621991,to=20000,lcxid=0x659931fd,lzxid=0x500040bde,lresp=4021154014,llat=3,minlat=0,avglat=2,maxlat=514)

[root@k8s-node-77 ~]# echo cons | nc 172.17.134.77 16309
 /172.17.134.77:56134[0](queued=0,recved=1,sent=0)

```

```sql
[root@k8s-node-77 ~]# echo dump | nc 172.17.134.76 16309
SessionTracker dump:
Global Sessions(3):
0x400ae36d45c000d	20000ms
0x400e51f462f0001	20000ms
0x5004f6df2f50011	20000ms
ephemeral nodes dump:
Sessions with Ephemerals (3):
0x400e51f462f0001:
	/dbscale/cluster/zk_monitor_172.17.134.77:16310_15
	/dbscale/cluster/nodes/172.17.134.77:16310:15
0x400ae36d45c000d:
	/dbscale/cluster/nodes/172.17.134.71:16310:14
	/dbscale/cluster/zk_monitor_172.17.134.71:16310_14
0x5004f6df2f50011:
	/dbscale/cluster/master_select
	/dbscale/cluster/nodes/172.17.134.76:16310:2
	/dbscale/cluster/zk_monitor_172.17.134.76:16310_2
Connections dump:
Connections Sets (3)/(3):
0 expire at Sun Jan 07 22:14:09 CST 2024:
1 expire at Sun Jan 07 22:14:19 CST 2024:
	ip: /172.17.134.77:52436 sessionId: 0x0
2 expire at Sun Jan 07 22:14:29 CST 2024:
	ip: /172.17.134.77:55118 sessionId: 0x400e51f462f0001
	ip: /172.17.134.71:34804 sessionId: 0x400ae36d45c000d

```

```sql
[root@k8s-node-77 ~]# echo envi | nc 172.17.134.76 16309
Environment:
zookeeper.version=3.6.2--803c7f1a12f85978cb049af5e4ef23bd8b688715, built on 09/04/2020 12:44 GMT
host.name=k8s-node-76
java.version=1.8.0_392
java.vendor=Red Hat, Inc.
java.home=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.392.b08-2.el7_9.x86_64/jre
java.class.path=/data/app/zookeeper-3.6.2/bin/../zookeeper-server/target/classes:/data/app/zookeeper-3.6.2/bin/../build/classes:/data/app/zookeeper-3.6.2/bin/../zookeeper-server/target/lib/*.jar:/data/app/zookeeper-3.6.2/bin/../build/lib/*.jar:/data/app/zookeeper-3.6.2/bin/../lib/zookeeper-prometheus-metrics-3.6.2.jar:/data/app/zookeeper-3.6.2/bin/../lib/zookeeper-jute-3.6.2.jar:/data/app/zookeeper-3.6.2/bin/../lib/zookeeper-3.6.2.jar:/data/app/zookeeper-3.6.2/bin/../lib/snappy-java-1.1.7.jar:/data/app/zookeeper-3.6.2/bin/../lib/slf4j-log4j12-1.7.25.jar:/data/app/zookeeper-3.6.2/bin/../lib/slf4j-api-1.7.25.jar:/data/app/zookeeper-3.6.2/bin/../lib/simpleclient_servlet-0.6.0.jar:/data/app/zookeeper-3.6.2/bin/../lib/simpleclient_hotspot-0.6.0.jar:/data/app/zookeeper-3.6.2/bin/../lib/simpleclient_common-0.6.0.jar:/data/app/zookeeper-3.6.2/bin/../lib/simpleclient-0.6.0.jar:/data/app/zookeeper-3.6.2/bin/../lib/netty-transport-native-unix-common-4.1.50.Final.jar:/data/app/zookeeper-3.6.2/bin/../lib/netty-transport-native-epoll-4.1.50.Final.jar:/data/app/zookeeper-3.6.2/bin/../lib/netty-transport-4.1.50.Final.jar:/data/app/zookeeper-3.6.2/bin/../lib/netty-resolver-4.1.50.Final.jar:/data/app/zookeeper-3.6.2/bin/../lib/netty-handler-4.1.50.Final.jar:/data/app/zookeeper-3.6.2/bin/../lib/netty-common-4.1.50.Final.jar:/data/app/zookeeper-3.6.2/bin/../lib/netty-codec-4.1.50.Final.jar:/data/app/zookeeper-3.6.2/bin/../lib/netty-buffer-4.1.50.Final.jar:/data/app/zookeeper-3.6.2/bin/../lib/metrics-core-3.2.5.jar:/data/app/zookeeper-3.6.2/bin/../lib/log4j-1.2.17.jar:/data/app/zookeeper-3.6.2/bin/../lib/json-simple-1.1.1.jar:/data/app/zookeeper-3.6.2/bin/../lib/jline-2.14.6.jar:/data/app/zookeeper-3.6.2/bin/../lib/jetty-util-9.4.24.v20191120.jar:/data/app/zookeeper-3.6.2/bin/../lib/jetty-servlet-9.4.24.v20191120.jar:/data/app/zookeeper-3.6.2/bin/../lib/jetty-server-9.4.24.v20191120.jar:/data/app/zookeeper-3.6.2/bin/../lib/jetty-security-9.4.24.v20191120.jar:/data/app/zookeeper-3.6.2/bin/../lib/jetty-io-9.4.24.v20191120.jar:/data/app/zookeeper-3.6.2/bin/../lib/jetty-http-9.4.24.v20191120.jar:/data/app/zookeeper-3.6.2/bin/../lib/javax.servlet-api-3.1.0.jar:/data/app/zookeeper-3.6.2/bin/../lib/jackson-databind-2.10.3.jar:/data/app/zookeeper-3.6.2/bin/../lib/jackson-core-2.10.3.jar:/data/app/zookeeper-3.6.2/bin/../lib/jackson-annotations-2.10.3.jar:/data/app/zookeeper-3.6.2/bin/../lib/commons-lang-2.6.jar:/data/app/zookeeper-3.6.2/bin/../lib/commons-cli-1.2.jar:/data/app/zookeeper-3.6.2/bin/../lib/audience-annotations-0.5.0.jar:/data/app/zookeeper-3.6.2/bin/../zookeeper-*.jar:/data/app/zookeeper-3.6.2/bin/../zookeeper-server/src/main/resources/lib/*.jar:/data/zookeeperdata/conf:
java.library.path=/usr/java/packages/lib/amd64:/usr/lib64:/lib64:/lib:/usr/lib
java.io.tmpdir=/tmp
java.compiler=<NA>
os.name=Linux
os.arch=amd64
os.version=5.4.257-1.el7.elrepo.x86_64
user.name=mysql
user.home=/home/mysql
user.dir=/data/app/zookeeper-3.6.2/bin
os.memory.free=110MB
os.memory.max=889MB
os.memory.total=138MB

```

### srvr
```sql
[root@k8s-node-77 ~]# echo srvr | nc 172.17.134.71 16309
Zookeeper version: 3.6.2--803c7f1a12f85978cb049af5e4ef23bd8b688715, built on 09/04/2020 12:44 GMT
Latency min/avg/max: 0/2.044/1045
Received: 123152
Sent: 123153
Connections: 2
Outstanding: 0
Zxid: 0x500040dc2
Mode: leader
Node count: 44
Proposal sizes last/min/max: 125/48/864
[root@k8s-node-77 ~]# echo srvr | nc 172.17.134.76 16309
Zookeeper version: 3.6.2--803c7f1a12f85978cb049af5e4ef23bd8b688715, built on 09/04/2020 12:44 GMT
Latency min/avg/max: 0/2.5699/514
Received: 142591
Sent: 142596
Connections: 3
Outstanding: 0
Zxid: 0x500040dc8
Mode: follower
Node count: 44
[root@k8s-node-77 ~]# echo srvr | nc 172.17.134.77 16309
Zookeeper version: 3.6.2--803c7f1a12f85978cb049af5e4ef23bd8b688715, built on 09/04/2020 12:44 GMT
Latency min/avg/max: 0/4.25/12
Received: 50
Sent: 50
Connections: 1
Outstanding: 0
Zxid: 0x500040dcb
Mode: follower
Node count: 44


```

### 以字节为单位显示快照和日志文件的总大小：disregard
```sql
[root@k8s-node-77 ~]# echo dirs | nc 172.17.134.77 16309
datadir_size: 1007297307
logdir_size: 1007297307

```

```sql
./zkCli.sh -server 172.16.76.34:16309
addauth digest db:db
deleteall /dbscale
--- 执行以下语句创建 dbscale 节点数据：
create /dbscale ""
create /dbscale/cluster ""
create /dbscale/keepalive ""
create /dbscale/dynamic_operation ""
create /dbscale/configuration ""
create /dbscale/cluster/nodes ""
create /dbscale/cluster/online_nodes ""
create /dbscale/keepalive/keepalive_init_info ""
create /dbscale/keepalive/keepalive_update_info ""
create /dbscale/keepalive/mul_sync_info ""
create /dbscale/dynamic_operation/dynamic_operation_info ""
create /dbscale/dynamic_operation/dynamic_cluster_management_info ""
create /dbscale/dynamic_operation/dynamic_cluster_management_init_info ""
create /dbscale/dynamic_operation/dynamic_config_info ""
create /dbscale/dynamic_operation/session_info ""
create /dbscale/dynamic_operation/block_info ""
create /dbscale/configuration/master_config_info ""
create /dbscale/configuration/changed_config_info ""
```




