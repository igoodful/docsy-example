---
title: zookeeper
description: zookeeper
date: 2023-11-06
weight: 30000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{%pageinfo%}}

{{%/pageinfo%}}


```bash
cd /data/
tar -xzvf  apache-zookeeper-3.6.2-bin.tar.gz
mv apache-zookeeper-3.6.2-bin zookeeper_16309
cd zookeeper_16309
mkdir -p /data/zookeeper_16309/{data log conf}
```

#### zkEnv.sh
> `/data/zookeeper_16309/bin/zkEnv.sh`
```bash
# ZOOKEEPER_PREFIX的下面添加两行，不能加到文件最后哦！！
ZOOCFGDIR=/data/zookeeper_16309/conf
ZOO_LOG_DIR=/data/zookeeper_16309/log
```

### zoo.cfg
> `/data/zookeeper_16309/conf/zoo.cfg`
```bash
autopurge.purgeInterval=120
initLimit=5
syncLimit=2
autopurge.snapRetainCount=10
4lw.commands.whitelist=*
tickTime=6000
dataDir=/data/zookeeper_16309/data
reconfigEnabled=true
admin.enableServer=false
standaloneEnabled=false
dynamicConfigFile=/data/zookeeper_16309/conf/zoo.cfg.dynamic.100000000
```

### zoo.cfg.dynamic.100000000
> `/data/zookeeper_16309/conf/zoo.cfg.dynamic.100000000`
```bash
server.211=172.16.76.33:2888:3888:participant;0.0.0.0:16309
server.212=172.16.76.34:2888:3888:participant;0.0.0.0:16309
server.213=172.16.76.35:2888:3888:participant;0.0.0.0:16309
```

### myid
> `/data/zookeeper_16309/data/myid`
```bash
211
```













