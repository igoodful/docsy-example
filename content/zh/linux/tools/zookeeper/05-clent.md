---
title: 5. 基本命令
description: cmd
date: 2025-06-19
weight: 500
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}


{{< /alert >}}


```viz-dot
digraph "cmd" {
rankdir = LR;
edge [
        fontsize="12"
        ];
node [
        shape = box;
        fontsize = "12"
];

"cmd" -> "ls";
"cmd" -> "getAllChildrenNumber";
"cmd" -> "getEphemerals";
"cmd" -> "stat";
"cmd" -> "get";
"cmd" -> "set";
"cmd" -> "create";
"cmd" -> "delete";
"cmd" -> "deleteall";
"cmd" -> "addauth";
"cmd" -> "getAcl";
"cmd" -> "setAcl";
"cmd" -> "config";
"cmd" -> "reconfig";
"cmd" -> "sync";
"cmd" -> "addWatch";
"cmd" -> "removewatches";
"cmd" -> "printwatches";
"cmd" -> "listquota";
"cmd" -> "setquota";
"cmd" -> "delquota";
"cmd" -> "redo";
"cmd" -> "connect";
"cmd" -> "quit";
"cmd" -> "close";
"cmd" -> "version";
"cmd" -> "history";

}
```

## ls


- `ls -R`: 递归列出
- `ls -s`: 显示状态
```bash
[zk: 127.0.0.1:16309(CONNECTED) 11] ls -R /
/
/dbscale
/zookeeper
/dbscale/cluster
/dbscale/configuration
/dbscale/dynamic_operation
/dbscale/keepalive
/dbscale/cluster/cluster_id
/dbscale/cluster/epoch
/dbscale/cluster/master_select
/dbscale/cluster/nodes
/dbscale/cluster/online_nodes
/dbscale/cluster/zk_monitor_172.17.134.66:16310_2
/dbscale/cluster/zk_monitor_172.17.135.147:16310_1
/dbscale/cluster/zk_monitor_172.17.137.63:16310_3
/dbscale/cluster/nodes/172.17.134.66:16310:2
/dbscale/cluster/nodes/172.17.135.147:16310:1
/dbscale/cluster/nodes/172.17.137.63:16310:3
/dbscale/cluster/online_nodes/1
/dbscale/cluster/online_nodes/2
/dbscale/cluster/online_nodes/3
/dbscale/configuration/changed_config_info
/dbscale/configuration/master_config_info
/dbscale/configuration/master_config_info_a_0
/dbscale/configuration/master_config_info_a_1
/dbscale/configuration/master_config_info_a_2
/dbscale/configuration/master_config_info_a_3
/dbscale/configuration/master_config_info_b_0
/dbscale/configuration/master_config_info_b_1
/dbscale/configuration/master_config_info_b_2
/dbscale/configuration/master_config_info_b_3
/dbscale/dynamic_operation/block_info
/dbscale/dynamic_operation/dynamic_cluster_management_info
/dbscale/dynamic_operation/dynamic_cluster_management_init_info
/dbscale/dynamic_operation/dynamic_config_info
/dbscale/dynamic_operation/dynamic_operation_info
/dbscale/dynamic_operation/dynamic_set_option_info
/dbscale/dynamic_operation/session_info
/dbscale/keepalive/keepalive_init_info
/dbscale/keepalive/keepalive_update_info
/dbscale/keepalive/mul_sync_info
/zookeeper/config
/zookeeper/quota

[zk: 127.0.0.1:16309(CONNECTED) 12] ls -s /dbscale
[cluster, configuration, dynamic_operation, keepalive]
cZxid = 0x100000002
ctime = Fri Jun 13 21:43:01 CST 2025
mZxid = 0x100000002
mtime = Fri Jun 13 21:43:01 CST 2025
pZxid = 0x100000014
cversion = 4
dataVersion = 0
aclVersion = 4
ephemeralOwner = 0x0
dataLength = 0
numChildren = 4
[zk: 127.0.0.1:16309(CONNECTED) 13] ls -w /dbscale
[cluster, configuration, dynamic_operation, keepalive]
[zk: 127.0.0.1:16309(CONNECTED) 14]

```

## stat
- `ls -s`

```bash
[zk: 127.0.0.1:16309(CONNECTED) 14] ls -s /dbscale
[cluster, configuration, dynamic_operation, keepalive]
cZxid = 0x100000002
ctime = Fri Jun 13 21:43:01 CST 2025
mZxid = 0x100000002
mtime = Fri Jun 13 21:43:01 CST 2025
pZxid = 0x100000014
cversion = 4
dataVersion = 0
aclVersion = 4
ephemeralOwner = 0x0
dataLength = 0
numChildren = 4
[zk: 127.0.0.1:16309(CONNECTED) 15] stat /dbscale
cZxid = 0x100000002
ctime = Fri Jun 13 21:43:01 CST 2025
mZxid = 0x100000002
mtime = Fri Jun 13 21:43:01 CST 2025
pZxid = 0x100000014
cversion = 4
dataVersion = 0
aclVersion = 4
ephemeralOwner = 0x0
dataLength = 0
numChildren = 4
[zk: 127.0.0.1:16309(CONNECTED) 16]

```



## create


- `auth::` 表示当前认证用户
```bash
addauth digest user:pass
create /private_node "data" auth::cdrwa

```

- `auth:dbscale` 表示认证用户为dbscale
```bash
addauth digest user:pass
create /read_only "data" auth:dbscale:cdrwa
```


## delete
- 只能删除最后一级节点

```bash
delete /app

```

## deleteall
- 递归删除节点及所有子节点

```bash
deleteall /dbscale

```





## getAcl

```bash
getAcl /dbscale

```


## setAcl

```bash
setAcl /dbscale digest:admin:...:cdrwa

```

- `-R`递归应用
```bash
setAcl -R /dbscale digest:admin:...:cdrwa

```





## config

- `config -s`: 服务端配置
```bash
[zk: 127.0.0.1:16309(CONNECTED) 7] config -s
server.157=172.17.137.63:2888:3888:participant;0.0.0.0:16309
server.158=172.17.135.147:2888:3888:participant;0.0.0.0:16309
server.159=172.17.134.66:2888:3888:participant;0.0.0.0:16309
version=500000b52
cZxid = 0x0
ctime = Thu Jan 01 08:00:00 CST 1970
mZxid = 0x500000b52
mtime = Wed Jun 18 12:30:40 CST 2025
pZxid = 0x0
cversion = 0
dataVersion = -1
aclVersion = -1
ephemeralOwner = 0x0
dataLength = 201
numChildren = 0
[zk: 127.0.0.1:16309(CONNECTED) 8] config -c
500000b52 0.0.0.0:16309,0.0.0.0:16309,0.0.0.0:16309
[zk: 127.0.0.1:16309(CONNECTED) 9] config -w
server.157=172.17.137.63:2888:3888:participant;0.0.0.0:16309
server.158=172.17.135.147:2888:3888:participant;0.0.0.0:16309
server.159=172.17.134.66:2888:3888:participant;0.0.0.0:16309
version=500000b52


```

## reconfig
```bash
# 添加节点
reconfig -add server.4=192.168.1.104:2888:3888:participant;2181

# 移除节点
reconfig -remove 3

```

- 案例

```bash
[zk: 172.17.140.189:16309(CONNECTED) 5] reconfig -remove 3
Committed new configuration:
server.165=172.17.140.189:2888:3888:participant;0.0.0.0:16309
server.149=172.17.137.159:2888:3888:participant;0.0.0.0:16309
server.150=172.17.138.136:2888:3888:participant;0.0.0.0:16309
version=3000019db
[zk: 172.17.140.189:16309(CONNECTED) 6] reconfig -remove 165
Committed new configuration:
server.149=172.17.137.159:2888:3888:participant;0.0.0.0:16309
server.150=172.17.138.136:2888:3888:participant;0.0.0.0:16309
version=300001a1e
[zk: 172.17.140.189:16309(CONNECTED) 7]
[zk: 172.17.140.189:16309(CONNECTED) 7]
[zk: 172.17.140.189:16309(CONNECTED) 7]
[zk: 172.17.140.189:16309(CONNECTED) 7] reconfig -add server.166=172.17.140.189:2888:3888:participant;0.0.0.0:16309
Committed new configuration:
server.149=172.17.137.159:2888:3888:participant;0.0.0.0:16309
server.166=172.17.140.189:2888:3888:participant;0.0.0.0:16309
server.150=172.17.138.136:2888:3888:participant;0.0.0.0:16309
version=300001ad0
[zk: 172.17.140.189:16309(CONNECTED) 8]

```


