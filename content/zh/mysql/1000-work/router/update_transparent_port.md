---
title: 变更透传端口
description: update_transparent_port
date: 2023-10-24
weight: 70000


---

{{< alert >}}

{{< /alert >}}

{{<note>}}

一、记下该集群的备份任务信息和binlog定时任务

二、停止dbscale，清理zk、修改dbscale.conf、启动dbscale

三、删除集群、纳管集群

四、应用一键告警模板、配置备份任务

{{</note>}}




### 一、记录集群的备份信息
> 登录RDS平台，记录集群物理备份任务信息

1. 查看集群备份计划
```sql
select gc.name as '集群名',
gbs.name as '备份计划名称',
gbs.backup_mode as '备份方式',
gst.period as '备份周期',
gst.scheduler_time as '备份时间',
gbs.enabled as '任务状态',
gbas.address as '备份服务器地址'
from greatrds.cluster gc,greatrds.scheduler_task gst,greatrds.backup_schedule gbs,greatrds.backup_server gbas
where gc.uuid = gbs.cluster_id  and gbs.uuid =left(gst.scheduler_name,36) and gbs.backup_server_uuid = gbas.uuid;
```

2. 查看binlog定时任务
```sql
select gc.name as '集群名',
json_unquote(json_extract(gt.parameter, '$.storage_type')) as '存储类型',
json_unquote(json_extract(gt.parameter, '$.buckets')) as '存储路径',
json_unquote(json_extract(gt.parameter, '$.context')) as 'COS配置',gt.status as '任务状态',
gst.scheduler_name'定时器名称',
gt.task_name as '定时任务名称',
gst.period as '周期',
gst.scheduler_time as '任务时间'
from greatrds.cluster gc,greatrds.tasks gt,greatrds.scheduler_task gst
where gc.uuid = gt.resource and gt.scheduler_uuid = gst.uuid and gt.type = 'binlog_backup'\G

```

### 二、记录集群的告警模板





### 三、更改所有调度节点端口

1. 停止所有调度节点dbscale
> 登录RDS平台，选择所有调度节点并停止其服务

2. 清空zookeeper中的配置
> 使用mysql用户登录集群的每个节点服务器，执行如下操作
```sql
cd /data/app/dbscale
export LD_LIBRARY_PATH=./libs
./clean_zookeeper 127.0.0.1 16309
```

3. 确认transparent-mode参数值为1；
```sql
cd /data/app/dbscale
grep -i "transparent-mode = 1" dbscale.conf
```


4. 修改transparent-port、port参数值
```sql
cp dbscale.conf dbscale.conf20231121
sed -i 's/transparent-port =.*/transparent-port = 16310/' dbscale.conf
sed -i '/type = MySQLDriver/{N;s/port =.*/port = 23399/}' dbscale.conf
```

5. 注释config-version参数
```sql
sed -i 's/config-version/#&/' dbscale.conf
```
6. 修改后的内容如下
```sql
[main]
...
#config-version = 3
transparent-mode = 1  #开启透传模式
...

[driver mysql]
type = MySQLDriver
port = 23399 #管理端口
transparent-port = 16310 #透传端口
bind-address = 127.0.0.1
```

7. 比对文件
```sql
diff dbscale.conf dbscale.conf20231121
```


### 四、启动调度节点dbscale
> 执行如下命令，启动所有调度节点服务。
```sql
cd /data/app/dbscale
./daemon/dbscale_daemon.py
```

### 五、重新纳管集群

1. RDS移除集群
> 登录RDS界面移除集群，但保留集群数据。

2. 纳管集群
> 纳管修改端口后的集群并重新应用一键告警模板、配置备份任务。

