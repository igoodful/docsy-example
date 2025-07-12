---
title: 部署
description: deploy
date: 2025-05-24
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

{{< /alert >}}


## deploy

```viz-dot
digraph "deploy" {
        rankdir = TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
        ];

        "deploy" -> "set names utf8"[label="set names utf8"];
        "deploy" -> "记录用户登录信息";
}
```


## ssh互信及免密登录

```bash
ssh-keygen -t rsa
ssh-copy-id root@192.168.3.221
ssh-copy-id root@192.168.3.222
ssh-copy-id root@192.168.3.223
ssh-copy-id root@192.168.3.224
ssh-copy-id root@192.168.3.225
ssh-copy-id root@192.168.3.226

```




## tiup

- https://asktug.com/t/topic/842973


#### 在线部署`tiup`

```bash
curl --proto '=https' --tlsv1.2 -sSf https://tiup-mirrors.pingcap.com/install.sh | sh

source ~/.bash_profile

which tiup

tiup cluster

tiup update --self && tiup update cluster

```

#### 离线部署`tiup`

- https://download.pingcap.org/tidb-community-server-v6.5.11-linux-amd64.tar.gz
- https://download.pingcap.org/tidb-community-toolkit-v6.5.11-linux-amd64.tar.gz

```bash
curl --proto '=https' --tlsv1.2 -sSf https://tiup-mirrors.pingcap.com/install.sh | sh

source ~/.bash_profile

which tiup

tiup cluster

tiup update --self && tiup update cluster

```

#### 使用`tiup`制作离线镜像

- `tiup mirror clone tidb-community-server-${version}-linux-amd64 ${version} --os=linux --arch=amd64`
- `tar czvf tidb-community-server-${version}-linux-amd64.tar.gz tidb-community-server-${version}-linux-amd64`

```bash
tiup mirror clone tidb-community-server-v6.5.11-linux-amd64 v6.5.11 --os=linux --arch=amd64

tar czvf tidb-community-server-v6.5.11-linux-amd64.tar.gz tidb-community-server-v6.5.11-linux-amd64

```

###### `tiup mirror clone`

- 举例说明：

```bash
# tiup mirror clone tidb-community-server-v6.5.11-linux-amd64 v6.5.11 --os=linux --arch=amd64

Start to clone mirror, targetDir is tidb-community-server-v6.5.11-linux-amd64, source mirror is https://tiup-mirrors.pingcap.com, selectedVersions are [v6.5.11]
If this does not meet expectations, please abort this process, read `tiup mirror clone --help` and run again
Arch [amd64]
OS [linux]
PCC linux/amd64 v6.5.11 not found, using v1.0.2 instead.
alertmanager linux/amd64 v6.5.11 not found, using v0.26.0 instead.
download https://tiup-mirrors.pingcap.com/PCC-v1.0.2-linux-amd64.tar.gz 14.94 MiB / 14.94 MiB 100.00% 1.04 MiB/s
bench linux/amd64 v6.5.11 not found, using v1.12.0 instead.
download https://tiup-mirrors.pingcap.com/alertmanager-v0.26.0-linux-amd64.tar.gz 28.34 MiB / 28.34 MiB 100.00% 957.58 KiB/s
blackbox_exporter linux/amd64 v6.5.11 not found, using v0.23.0 instead.
download https://tiup-mirrors.pingcap.com/bench-v1.12.0-linux-amd64.tar.gz 33.39 MiB / 33.39 MiB 100.00% 955.72 KiB/s
download https://tiup-mirrors.pingcap.com/blackbox_exporter-v0.23.0-linux-amd64.tar.gz 10.33 MiB / 10.33 MiB 100.00% 981.81 KiB/s
download https://tiup-mirrors.pingcap.com/br-v6.5.11-linux-amd64.tar.gz 69.79 MiB / 69.79 MiB 100.00% 945.09 KiB/s
chaosd linux/amd64 v6.5.11 not found, using v1.1.1 instead.
download https://tiup-mirrors.pingcap.com/cdc-v6.5.11-linux-amd64.tar.gz 67.80 MiB / 67.80 MiB 100.00% 950.46 KiB/s
client linux/amd64 v6.5.11 not found, using v1.16.2 instead.
download https://tiup-mirrors.pingcap.com/chaosd-v1.1.1-linux-amd64.tar.gz 26.11 MiB / 26.11 MiB 100.00% 972.54 KiB/s
cloud linux/amd64 v6.5.11 not found, using v1.0.0-beta.4 instead.
download https://tiup-mirrors.pingcap.com/client-v1.16.2-linux-amd64.tar.gz 5.15 MiB / 5.15 MiB 100.00% 1.04 MiB/s
cluster linux/amd64 v6.5.11 not found, using v1.16.2 instead.
download https://tiup-mirrors.pingcap.com/cloud-v1.0.0-beta.4-linux-amd64.tar.gz 9.27 MiB / 9.27 MiB 100.00% 927.45 KiB/s
download https://tiup-mirrors.pingcap.com/cluster-v1.16.2-linux-amd64.tar.gz 8.88 MiB / 8.88 MiB 100.00% 1.02 MiB/s
dba linux/amd64 v6.5.11 not found, using v1.0.4 instead.
download https://tiup-mirrors.pingcap.com/ctl-v6.5.11-linux-amd64.tar.gz 356.48 MiB / 356.48 MiB 100.00% 940.18 KiB/s
demo linux/amd64 v6.5.11 not found, using v0.0.11 instead.
download https://tiup-mirrors.pingcap.com/dba-v1.0.4-linux-amd64.tar.gz 18.26 MiB / 18.26 MiB 100.00% 1.01 MiB/s
diag linux/amd64 v6.5.11 not found, using v1.6.0 instead.
download https://tiup-mirrors.pingcap.com/demo-v0.0.11-linux-amd64.tar.gz 2.63 MiB / 2.63 MiB 100.00% 1.05 MiB/s
dm linux/amd64 v6.5.11 not found, using v1.16.2 instead.
download https://tiup-mirrors.pingcap.com/diag-v1.6.0-linux-amd64.tar.gz 26.91 MiB / 26.91 MiB 100.00% 957.04 KiB/s
download https://tiup-mirrors.pingcap.com/dm-v1.16.2-linux-amd64.tar.gz 8.53 MiB / 8.53 MiB 100.00% 989.38 KiB/s
download https://tiup-mirrors.pingcap.com/dm-master-v6.5.11-linux-amd64.tar.gz 65.18 MiB / 65.18 MiB 100.00% 947.90 KiB/s
download https://tiup-mirrors.pingcap.com/dm-worker-v6.5.11-linux-amd64.tar.gz 62.37 MiB / 62.37 MiB 100.00% 957.11 KiB/s
download https://tiup-mirrors.pingcap.com/dmctl-v6.5.11-linux-amd64.tar.gz 58.82 MiB / 58.82 MiB 100.00% 950.07 KiB/s
download https://tiup-mirrors.pingcap.com/drainer-v6.5.11-linux-amd64.tar.gz 54.89 MiB / 54.89 MiB 100.00% 953.50 KiB/s
errdoc linux/amd64 v6.5.11 not found, using v4.0.7 instead.
download https://tiup-mirrors.pingcap.com/dumpling-v6.5.11-linux-amd64.tar.gz 41.73 MiB / 41.73 MiB 100.00% 961.37 KiB/s
download https://tiup-mirrors.pingcap.com/errdoc-v4.0.7-linux-amd64.tar.gz 10.10 MiB / 10.10 MiB 100.00% 900.47 KiB/s
influxdb linux/amd64 v6.5.11 not found, using v2.5.0 instead.
download https://tiup-mirrors.pingcap.com/grafana-v6.5.11-linux-amd64.tar.gz 50.14 MiB / 50.14 MiB 100.00% 965.92 KiB/s
insight linux/amd64 v6.5.11 not found, using v0.4.2 instead.
download https://tiup-mirrors.pingcap.com/influxdb-v2.5.0-linux-amd64.tar.gz 44.57 MiB / 44.57 MiB 100.00% 945.47 KiB/s
node_exporter linux/amd64 v6.5.11 not found, using v1.5.0 instead.
download https://tiup-mirrors.pingcap.com/insight-v0.4.2-linux-amd64.tar.gz 2.82 MiB / 2.82 MiB 100.00% 1.38 MiB/s
package linux/amd64 v6.5.11 not found, using v0.0.9 instead.
download https://tiup-mirrors.pingcap.com/node_exporter-v1.5.0-linux-amd64.tar.gz 9.71 MiB / 9.71 MiB 100.00% 972.70 KiB/s
download https://tiup-mirrors.pingcap.com/package-v0.0.9-linux-amd64.tar.gz 5.55 MiB / 5.55 MiB 100.00% 1005.39 KiB/s
download https://tiup-mirrors.pingcap.com/pd-v6.5.11-linux-amd64.tar.gz 46.85 MiB / 46.85 MiB 100.00% 944.35 KiB/s
playground linux/amd64 v6.5.11 not found, using v1.16.2 instead.
download https://tiup-mirrors.pingcap.com/pd-recover-v6.5.11-linux-amd64.tar.gz 15.01 MiB / 15.01 MiB 100.00% 1004.37 KiB/s
download https://tiup-mirrors.pingcap.com/playground-v1.16.2-linux-amd64.tar.gz 8.20 MiB / 8.20 MiB 100.00% 991.54 KiB/s
download https://tiup-mirrors.pingcap.com/prometheus-v6.5.11-linux-amd64.tar.gz 94.25 MiB / 94.25 MiB 100.00% 944.09 KiB/s
pushgateway linux/amd64 v6.5.11 not found, using v0.7.0 instead.
download https://tiup-mirrors.pingcap.com/pump-v6.5.11-linux-amd64.tar.gz 52.32 MiB / 52.32 MiB 100.00% 965.72 KiB/s
server linux/amd64 v6.5.11 not found, using v1.16.2 instead.
download https://tiup-mirrors.pingcap.com/pushgateway-v0.7.0-linux-amd64.tar.gz 6.25 MiB / 6.25 MiB 100.00% 873.79 KiB/s
spark linux/amd64 v6.5.11 not found, using v2.4.3 instead.
download https://tiup-mirrors.pingcap.com/server-v1.16.2-linux-amd64.tar.gz 3.62 MiB / 3.62 MiB 100.00% 1.73 MiB/s
sync-diff-inspector linux/amd64 v6.5.11 not found, using v9.0.0-beta.1 instead.
download https://tiup-mirrors.pingcap.com/spark-v2.4.3-any-any.tar.gz 219.33 MiB / 219.33 MiB 100.00% 946.81 KiB/s
download https://tiup-mirrors.pingcap.com/sync-diff-inspector-v9.0.0-beta.1-linux-amd64.tar.gz 59.07 MiB / 59.07 MiB 100.00% 971.54 KiB/s
download https://tiup-mirrors.pingcap.com/tidb-v6.5.11-linux-amd64.tar.gz 68.07 MiB / 68.07 MiB 100.00% 954.23 KiB/s
download https://tiup-mirrors.pingcap.com/tidb-dashboard-v6.5.11-linux-amd64.tar.gz 35.33 MiB / 35.33 MiB 100.00% 964.63 KiB/s
download https://tiup-mirrors.pingcap.com/tidb-lightning-v6.5.11-linux-amd64.tar.gz 65.59 MiB / 65.59 MiB 100.00% 943.56 KiB/s
download https://tiup-mirrors.pingcap.com/tiflash-v6.5.11-linux-amd64.tar.gz 195.91 MiB / 195.91 MiB 100.00% 950.61 KiB/s
tikv-br linux/amd64 v6.5.11 not found, using v1.1.0 instead.
download https://tiup-mirrors.pingcap.com/tikv-v6.5.11-linux-amd64.tar.gz 255.66 MiB / 255.66 MiB 100.00% 951.25 KiB/s
tikv-cdc linux/amd64 v6.5.11 not found, using v1.1.1 instead.
download https://tiup-mirrors.pingcap.com/tikv-br-v1.1.0-linux-amd64.tar.gz 18.60 MiB / 18.60 MiB 100.00% 1.02 MiB/s
tikv-importer linux/amd64 v6.5.11 not found, using v4.0.2 instead.
download https://tiup-mirrors.pingcap.com/tikv-cdc-v1.1.1-linux-amd64.tar.gz 53.83 MiB / 53.83 MiB 100.00% 954.60 KiB/s
tiproxy linux/amd64 v6.5.11 not found, using v1.3.0 instead.
download https://tiup-mirrors.pingcap.com/tikv-importer-v4.0.2-linux-amd64.tar.gz 9.85 MiB / 9.85 MiB 100.00% 1009.47 KiB/s
tispark linux/amd64 v6.5.11 not found, using v2.4.1 instead.
download https://tiup-mirrors.pingcap.com/tiproxy-v1.3.0-linux-amd64.tar.gz 23.82 MiB / 23.82 MiB 100.00% 962.94 KiB/s
tiup linux/amd64 v6.5.11 not found, using v1.16.2 instead.
download https://tiup-mirrors.pingcap.com/tispark-v2.4.1-any-any.tar.gz 22.36 MiB / 22.36 MiB 100.00% 967.72 KiB/s
download https://tiup-mirrors.pingcap.com/tiup-v1.16.2-linux-amd64.tar.gz 5.06 MiB / 5.06 MiB 100.00% 1021.10 KiB/s
download https://tiup-mirrors.pingcap.com/tiup-linux-amd64.tar.gz 5.65 MiB / 5.65 MiB 100.00% 1006.78 KiB/s

# tar czvf tidb-community-server-v6.5.11-linux-amd64.tar.gz tidb-community-server-v6.5.11-linux-amd64

```


```bash
# tiup mirror clone tiup-custom-mirror-v1.12.3 --tiup v1.12.3 --cluster v1.12.3

Start to clone mirror, targetDir is tiup-custom-mirror-v1.12.3, source mirror is https://tiup-mirrors.pingcap.com, selectedVersions are []
If this does not meet expectations, please abort this process, read `tiup mirror clone --help` and run again
Arch [amd64 arm64]
OS [linux darwin]
cluster linux/amd64 selected version is v1.12.3
cluster linux/arm64 selected version is v1.12.3
cluster darwin/amd64 selected version is v1.12.3
cluster darwin/arm64 selected version is v1.12.3
download https://tiup-mirrors.pingcap.com/cluster-v1.12.3-linux-amd64.tar.gz 8.68 MiB / 8.68 MiB 100.00% 1.14 MiB/s
download https://tiup-mirrors.pingcap.com/cluster-v1.12.3-linux-arm64.tar.gz 7.80 MiB / 7.80 MiB 100.00% 1001.33 KiB/s
download https://tiup-mirrors.pingcap.com/cluster-v1.12.3-darwin-amd64.tar.gz 9.03 MiB / 9.03 MiB 100.00% 920.18 KiB/s
tiup linux/amd64 selected version is v1.12.3
tiup linux/arm64 selected version is v1.12.3
tiup darwin/amd64 selected version is v1.12.3
tiup darwin/arm64 selected version is v1.12.3
download https://tiup-mirrors.pingcap.com/cluster-v1.12.3-darwin-arm64.tar.gz 8.55 MiB / 8.55 MiB 100.00% 1.10 MiB/s
download https://tiup-mirrors.pingcap.com/tiup-v1.12.3-linux-amd64.tar.gz 7.15 MiB / 7.15 MiB 100.00% 1.01 MiB/s
download https://tiup-mirrors.pingcap.com/tiup-v1.12.3-linux-arm64.tar.gz 6.41 MiB / 6.41 MiB 100.00% 876.77 KiB/s
download https://tiup-mirrors.pingcap.com/tiup-v1.12.3-darwin-amd64.tar.gz 7.48 MiB / 7.48 MiB 100.00% 1.15 MiB/s
download https://tiup-mirrors.pingcap.com/tiup-v1.12.3-darwin-arm64.tar.gz 7.07 MiB / 7.07 MiB 100.00% 906.66 KiB/s
download https://tiup-mirrors.pingcap.com/tiup-linux-amd64.tar.gz 5.65 MiB / 5.65 MiB 100.00% 1.15 MiB/s
download https://tiup-mirrors.pingcap.com/tiup-linux-arm64.tar.gz 5.13 MiB / 5.13 MiB 100.00% 1010.47 KiB/s
download https://tiup-mirrors.pingcap.com/tiup-darwin-amd64.tar.gz 5.75 MiB / 5.75 MiB 100.00% 989.78 KiB/s
download https://tiup-mirrors.pingcap.com/tiup-darwin-arm64.tar.gz 5.34 MiB / 5.34 MiB 100.00% 926.80 KiB/s

# cd tiup-custom-mirror-v1.12.3/
# ll
total 86348
-rw-r--r-- 1 guolicheng guolicheng  133493 2025-05-26T09:26:05 1085.cluster.json
-rw-r--r-- 1 guolicheng guolicheng  106411 2025-05-26T09:26:05 1085.tiup.json
-rw-r--r-- 1 guolicheng guolicheng    1441 2025-05-26T09:26:05 1.index.json
-rw-r--r-- 1 guolicheng guolicheng    5221 2025-05-26T09:26:05 1.root.json
-rw-rw-r-- 1 guolicheng guolicheng 9467082 2025-05-26T09:25:02 cluster-v1.12.3-darwin-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng 8968374 2025-05-26T09:25:10 cluster-v1.12.3-darwin-arm64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng 9101478 2025-05-26T09:24:43 cluster-v1.12.3-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng 8176992 2025-05-26T09:24:51 cluster-v1.12.3-linux-arm64.tar.gz
drwxrwxr-x 2 guolicheng guolicheng    4096 2025-05-26T09:24:35 keys
-rwxrwxr-x 1 guolicheng guolicheng    2530 2025-05-26T09:26:05 local_install.sh
-rw-r--r-- 1 guolicheng guolicheng    5221 2025-05-26T09:26:05 root.json
-rw-r--r-- 1 guolicheng guolicheng     736 2025-05-26T09:26:05 snapshot.json
-rw-r--r-- 1 guolicheng guolicheng     818 2025-05-26T09:26:05 timestamp.json
-rw-rw-r-- 1 guolicheng guolicheng 6024130 2025-05-26T09:25:59 tiup-darwin-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng 5602042 2025-05-26T09:26:05 tiup-darwin-arm64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng 5920146 2025-05-26T09:25:47 tiup-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng 5383471 2025-05-26T09:25:53 tiup-linux-arm64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng 7844844 2025-05-26T09:25:33 tiup-v1.12.3-darwin-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng 7411365 2025-05-26T09:25:42 tiup-v1.12.3-darwin-arm64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng 7499112 2025-05-26T09:25:18 tiup-v1.12.3-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng 6717015 2025-05-26T09:25:26 tiup-v1.12.3-linux-arm64.tar.gz

```

###### tiup mirror show

- 查看镜像地址

```bash
# tiup mirror show
https://tiup-mirrors.pingcap.com

# cat $HOME/.tiup/tiup.toml
mirror = "https://tiup-mirrors.pingcap.com"

export TIUP_MIRRORS='https://tiup-mirrors.pingcap.com'

tiup mirror set https://tiup-mirrors.pingcap.com



```


## 初始化集群拓扑文件

```bash
tiup cluster template |grep -Ev '^\s*#' > topology.yaml

tiup cluster template > topology.yaml

tiup cluster template --full > topology.yaml

tiup cluster template --multi-dc > topology.yaml


```

## 环境校验

```bash
# 校验
tiup cluster check ./topology.yaml --user tidb -p

# 修复
tiup cluster check ./topology.yaml --apply --user root

```



## 部署集群

```bash
# 部署
tiup cluster deploy tidb542 v5.4.2 ./topology.yml --user root

tiup cluster check ./topology.yaml --user root [-p] [-i /home/root/.ssh/gcp_rsa]

tiup cluster check ./topology.yaml --apply --user root [-p] [-i /home/root/.ssh/gcp_rsa]

tiup cluster deploy tidb-test v8.5.1 ./topology.yaml --user root [-p] [-i /home/root/.ssh/gcp_rsa]

```

- `tiup cluster deploy tidb542 v5.4.2 ./topology.yml --user root`



## 初始化集群

```bash
tiup cluster start kruidb-cluster

```




## 检查TiDB集群


```bash
tiup cluster display kruidb-cluster

tiup cluster edit-config kruidb-cluster


```


## 管理集群

```bash
tiup cluster list

tiup cluster display tidb-test

tiup cluster start tidb-test --init

tiup cluster start tidb-test





```

## 修改密码

```sql
set password for 'root'@'%' = 'root';

```


## `tiup mirror`

- https://blog.csdn.net/CBGCampus/article/details/125327976

#### tiup mirror clone

```bash
tiup mirror clone tiup-custom-mirror-v5.4.0 --PCC 1.0.1 --os=linux --arch=amd64


```



#### tiup mirror merge


```bash
tiup mirror merge /home/tidb/tiup-custom-mirror-v5.4.0

```




## `tiup cluster`




#### 部署集群: `tiup cluster deploy`

- `tiup cluster deploy <cluster-name> <version> <topology.yaml> [flags]`
  - 集群名称
  - 集群版本
  - 集群配置信息
  - 选项



- `-y`,`--yes`
- `-u`, `--user`
- `-p`, `--password`

```bash
# tiup cluster deploy tidb542 v5.4.2 ./topology.yml --user root

+ Detect CPU Arch Name
  - Detecting node 172.17.136.198 Arch info ... Done
  - Detecting node 172.17.135.92 Arch info ... Done
  - Detecting node 172.17.136.3 Arch info ... Done



+ Detect CPU OS Name
  - Detecting node 172.17.136.198 OS info ... Done
  - Detecting node 172.17.135.92 OS info ... Done
  - Detecting node 172.17.136.3 OS info ... Done
Please confirm your topology:
Cluster type:    tidb
Cluster name:    tidb542
Cluster version: v5.4.2
Role          Host            Ports        OS/Arch       Directories
----          ----            -----        -------       -----------
pd            172.17.136.198  2379/2380    linux/x86_64  /tidb-deploy/pd-2379,/tidb-data/pd-2379
pd            172.17.135.92   2379/2380    linux/x86_64  /tidb-deploy/pd-2379,/tidb-data/pd-2379
pd            172.17.136.3    2379/2380    linux/x86_64  /tidb-deploy/pd-2379,/tidb-data/pd-2379
tikv          172.17.136.198  20160/20180  linux/x86_64  /tidb-deploy/tikv-20160,/tidb-data/tikv-20160
tikv          172.17.135.92   20160/20180  linux/x86_64  /tidb-deploy/tikv-20160,/tidb-data/tikv-20160
tikv          172.17.136.3    20160/20180  linux/x86_64  /tidb-deploy/tikv-20160,/tidb-data/tikv-20160
tidb          172.17.136.198  4000/10080   linux/x86_64  /tidb-deploy/tidb-4000
tidb          172.17.135.92   4000/10080   linux/x86_64  /tidb-deploy/tidb-4000
tidb          172.17.136.3    4000/10080   linux/x86_64  /tidb-deploy/tidb-4000
prometheus    172.17.136.3    9090/12020   linux/x86_64  /tidb-deploy/prometheus-9090,/tidb-data/prometheus-9090
grafana       172.17.136.3    3000         linux/x86_64  /tidb-deploy/grafana-3000
alertmanager  172.17.136.3    9093/9094    linux/x86_64  /tidb-deploy/alertmanager-9093,/tidb-data/alertmanager-9093
Attention:
    1. If the topology is not what you expected, check your yaml file.
    2. Please confirm there is no port/directory conflicts in same host.
Do you want to continue? [y/N]: (default=N) y
+ Generate SSH keys ... Done
+ Download TiDB components
  - Download pd:v5.4.2 (linux/amd64) ... Done
  - Download tikv:v5.4.2 (linux/amd64) ... Done
  - Download tidb:v5.4.2 (linux/amd64) ... Done
  - Download prometheus:v5.4.2 (linux/amd64) ... Done
  - Download grafana:v5.4.2 (linux/amd64) ... Done
  - Download alertmanager: (linux/amd64) ... Done
  - Download node_exporter: (linux/amd64) ... Done
  - Download blackbox_exporter: (linux/amd64) ... Done
+ Initialize target host environments
  - Prepare 172.17.136.198:22 ... Done
  - Prepare 172.17.135.92:22 ... Done
  - Prepare 172.17.136.3:22 ... Done
+ Deploy TiDB instance
  - Copy pd -> 172.17.136.198 ... Done
  - Copy pd -> 172.17.135.92 ... Done
  - Copy pd -> 172.17.136.3 ... Done
  - Copy tikv -> 172.17.136.198 ... Done
  - Copy tikv -> 172.17.135.92 ... Done
  - Copy tikv -> 172.17.136.3 ... Done
  - Copy tidb -> 172.17.136.198 ... Done
  - Copy tidb -> 172.17.135.92 ... Done
  - Copy tidb -> 172.17.136.3 ... Done
  - Copy prometheus -> 172.17.136.3 ... Done
  - Copy grafana -> 172.17.136.3 ... Done
  - Copy alertmanager -> 172.17.136.3 ... Done
  - Deploy node_exporter -> 172.17.136.3 ... Done
  - Deploy node_exporter -> 172.17.136.198 ... Done
  - Deploy node_exporter -> 172.17.135.92 ... Done
  - Deploy blackbox_exporter -> 172.17.136.198 ... Done
  - Deploy blackbox_exporter -> 172.17.135.92 ... Done
  - Deploy blackbox_exporter -> 172.17.136.3 ... Done
+ Copy certificate to remote host
+ Init instance configs
  - Generate config pd -> 172.17.136.198:2379 ... Done
  - Generate config pd -> 172.17.135.92:2379 ... Done
  - Generate config pd -> 172.17.136.3:2379 ... Done
  - Generate config tikv -> 172.17.136.198:20160 ... Done
  - Generate config tikv -> 172.17.135.92:20160 ... Done
  - Generate config tikv -> 172.17.136.3:20160 ... Done
  - Generate config tidb -> 172.17.136.198:4000 ... Done
  - Generate config tidb -> 172.17.135.92:4000 ... Done
  - Generate config tidb -> 172.17.136.3:4000 ... Done
  - Generate config prometheus -> 172.17.136.3:9090 ... Done
  - Generate config grafana -> 172.17.136.3:3000 ... Done
  - Generate config alertmanager -> 172.17.136.3:9093 ... Done
+ Init monitor configs
  - Generate config node_exporter -> 172.17.136.198 ... Done
  - Generate config node_exporter -> 172.17.135.92 ... Done
  - Generate config node_exporter -> 172.17.136.3 ... Done
  - Generate config blackbox_exporter -> 172.17.136.198 ... Done
  - Generate config blackbox_exporter -> 172.17.135.92 ... Done
  - Generate config blackbox_exporter -> 172.17.136.3 ... Done
Enabling component pd
	Enabling instance 172.17.136.3:2379
	Enabling instance 172.17.136.198:2379
	Enabling instance 172.17.135.92:2379
	Enable instance 172.17.136.3:2379 success
	Enable instance 172.17.135.92:2379 success
	Enable instance 172.17.136.198:2379 success
Enabling component tikv
	Enabling instance 172.17.136.3:20160
	Enabling instance 172.17.136.198:20160
	Enabling instance 172.17.135.92:20160
	Enable instance 172.17.136.3:20160 success
	Enable instance 172.17.136.198:20160 success
	Enable instance 172.17.135.92:20160 success
Enabling component tidb
	Enabling instance 172.17.136.3:4000
	Enabling instance 172.17.135.92:4000
	Enabling instance 172.17.136.198:4000
	Enable instance 172.17.136.3:4000 success
	Enable instance 172.17.136.198:4000 success
	Enable instance 172.17.135.92:4000 success
Enabling component prometheus
	Enabling instance 172.17.136.3:9090
	Enable instance 172.17.136.3:9090 success
Enabling component grafana
	Enabling instance 172.17.136.3:3000
	Enable instance 172.17.136.3:3000 success
Enabling component alertmanager
	Enabling instance 172.17.136.3:9093
	Enable instance 172.17.136.3:9093 success
Enabling component node_exporter
	Enabling instance 172.17.136.3
	Enabling instance 172.17.135.92
	Enabling instance 172.17.136.198
	Enable 172.17.136.3 success
	Enable 172.17.136.198 success
	Enable 172.17.135.92 success
Enabling component blackbox_exporter
	Enabling instance 172.17.136.3
	Enabling instance 172.17.136.198
	Enabling instance 172.17.135.92
	Enable 172.17.136.3 success
	Enable 172.17.136.198 success
	Enable 172.17.135.92 success
Cluster `tidb542` deployed successfully, you can start it with command: `tiup cluster start tidb542 --init`

```

#### 启动集群: `tiup cluster start`

- `tiup cluster start tidb542 --init`
```bash
# tiup cluster start tidb542 --init

Starting cluster tidb542...
+ [ Serial ] - SSHKeySet: privateKey=/db/storage/guolicheng/.tiup/storage/cluster/clusters/tidb542/ssh/id_rsa, publicKey=/db/storage/guolicheng/.tiup/storage/cluster/clusters/tidb542/ssh/id_rsa.pub
+ [Parallel] - UserSSH: user=tidb, host=172.17.135.92
+ [Parallel] - UserSSH: user=tidb, host=172.17.136.3
+ [Parallel] - UserSSH: user=tidb, host=172.17.136.198
+ [Parallel] - UserSSH: user=tidb, host=172.17.135.92
+ [Parallel] - UserSSH: user=tidb, host=172.17.135.92
+ [Parallel] - UserSSH: user=tidb, host=172.17.136.3
+ [Parallel] - UserSSH: user=tidb, host=172.17.136.3
+ [Parallel] - UserSSH: user=tidb, host=172.17.136.3
+ [Parallel] - UserSSH: user=tidb, host=172.17.136.3
+ [Parallel] - UserSSH: user=tidb, host=172.17.136.198
+ [Parallel] - UserSSH: user=tidb, host=172.17.136.198
+ [Parallel] - UserSSH: user=tidb, host=172.17.136.3
+ [ Serial ] - StartCluster
Starting component pd
	Starting instance 172.17.136.3:2379
	Starting instance 172.17.135.92:2379
	Starting instance 172.17.136.198:2379
	Start instance 172.17.136.198:2379 success
	Start instance 172.17.135.92:2379 success
	Start instance 172.17.136.3:2379 success
Starting component tikv
	Starting instance 172.17.136.3:20160
	Starting instance 172.17.136.198:20160
	Starting instance 172.17.135.92:20160
	Start instance 172.17.136.3:20160 success
	Start instance 172.17.136.198:20160 success
	Start instance 172.17.135.92:20160 success
Starting component tidb
	Starting instance 172.17.136.3:4000
	Starting instance 172.17.136.198:4000
	Starting instance 172.17.135.92:4000
	Start instance 172.17.135.92:4000 success
	Start instance 172.17.136.198:4000 success
	Start instance 172.17.136.3:4000 success
Starting component prometheus
	Starting instance 172.17.136.3:9090
	Start instance 172.17.136.3:9090 success
Starting component grafana
	Starting instance 172.17.136.3:3000
	Start instance 172.17.136.3:3000 success
Starting component alertmanager
	Starting instance 172.17.136.3:9093
	Start instance 172.17.136.3:9093 success
Starting component node_exporter
	Starting instance 172.17.135.92
	Starting instance 172.17.136.3
	Starting instance 172.17.136.198
	Start 172.17.136.198 success
	Start 172.17.136.3 success
	Start 172.17.135.92 success
Starting component blackbox_exporter
	Starting instance 172.17.135.92
	Starting instance 172.17.136.3
	Starting instance 172.17.136.198
	Start 172.17.136.198 success
	Start 172.17.135.92 success
	Start 172.17.136.3 success
+ [ Serial ] - UpdateTopology: cluster=tidb542
Started cluster `tidb542` successfully
The root password of TiDB database has been changed.
The new password is: 'Su53-_6i9Rdbn42+^s'.
Copy and record it to somewhere safe, it is only displayed once, and will not be stored.
The generated password can NOT be get and shown again.

```

#### 集群列表: `tiup cluster list`


```bash
# tiup cluster list

Name     User  Version  Path                                                           PrivateKey
----     ----  -------  ----                                                           ----------
tidb542  tidb  v5.4.2   /db/storage/guolicheng/.tiup/storage/cluster/clusters/tidb542  /db/storage/guolicheng/.tiup/storage/cluster/clusters/tidb542/ssh/id_rsa

```


#### 集群状态: `tiup cluster display`


```bash
# tiup cluster display tidb542

Cluster type:       tidb
Cluster name:       tidb542
Cluster version:    v5.4.2
Deploy user:        tidb
SSH type:           builtin
Dashboard URL:      http://172.17.136.3:2379/dashboard
Dashboard URLs:     http://172.17.136.3:2379/dashboard
Grafana URL:        http://172.17.136.3:3000
ID                    Role          Host            Ports        OS/Arch       Status  Data Dir                      Deploy Dir
--                    ----          ----            -----        -------       ------  --------                      ----------
172.17.136.3:9093     alertmanager  172.17.136.3    9093/9094    linux/x86_64  Up      /tidb-data/alertmanager-9093  /tidb-deploy/alertmanager-9093
172.17.136.3:3000     grafana       172.17.136.3    3000         linux/x86_64  Up      -                             /tidb-deploy/grafana-3000
172.17.135.92:2379    pd            172.17.135.92   2379/2380    linux/x86_64  Up      /tidb-data/pd-2379            /tidb-deploy/pd-2379
172.17.136.198:2379   pd            172.17.136.198  2379/2380    linux/x86_64  Up|L    /tidb-data/pd-2379            /tidb-deploy/pd-2379
172.17.136.3:2379     pd            172.17.136.3    2379/2380    linux/x86_64  Up|UI   /tidb-data/pd-2379            /tidb-deploy/pd-2379
172.17.136.3:9090     prometheus    172.17.136.3    9090/12020   linux/x86_64  Up      /tidb-data/prometheus-9090    /tidb-deploy/prometheus-9090
172.17.135.92:4000    tidb          172.17.135.92   4000/10080   linux/x86_64  Up      -                             /tidb-deploy/tidb-4000
172.17.136.198:4000   tidb          172.17.136.198  4000/10080   linux/x86_64  Up      -                             /tidb-deploy/tidb-4000
172.17.136.3:4000     tidb          172.17.136.3    4000/10080   linux/x86_64  Up      -                             /tidb-deploy/tidb-4000
172.17.135.92:20160   tikv          172.17.135.92   20160/20180  linux/x86_64  Up      /tidb-data/tikv-20160         /tidb-deploy/tikv-20160
172.17.136.198:20160  tikv          172.17.136.198  20160/20180  linux/x86_64  Up      /tidb-data/tikv-20160         /tidb-deploy/tikv-20160
172.17.136.3:20160    tikv          172.17.136.3    20160/20180  linux/x86_64  Up      /tidb-data/tikv-20160         /tidb-deploy/tikv-20160
Total nodes: 12

```


#### 集群操作日志: `tiup cluster audit`


```bash
# tiup cluster audit

ID           Time                       Command
--           ----                       -------
gGWK0qcshy1  2025-05-25T10:24:14+08:00  /db/storage/guolicheng/.tiup/components/cluster/v1.16.2/tiup-cluster template
gGWK75nC4FK  2025-05-25T10:26:12+08:00  /db/storage/guolicheng/.tiup/components/cluster/v1.16.2/tiup-cluster list
gGWK88VSgRF  2025-05-25T10:26:30+08:00  /db/storage/guolicheng/.tiup/components/cluster/v1.16.2/tiup-cluster display tidb-test
gGYVHQ3Cfnj  2025-05-26T14:14:50+08:00  /db/storage/guolicheng/.tiup/components/cluster/v1.16.2/tiup-cluster template
gGYVN3k8CXq  2025-05-26T14:16:04+08:00  /db/storage/guolicheng/.tiup/components/cluster/v1.16.2/tiup-cluster template
gGYVNr9DMp8  2025-05-26T14:16:11+08:00  /db/storage/guolicheng/.tiup/components/cluster/v1.16.2/tiup-cluster template
gGYW6dq0vRb  2025-05-26T14:21:06+08:00  /db/storage/guolicheng/.tiup/components/cluster/v1.16.2/tiup-cluster check ./topology.yml --user tidb
gGYWfmdmWNV  2025-05-26T14:23:12+08:00  /db/storage/guolicheng/.tiup/components/cluster/v1.16.2/tiup-cluster check ./topology.yml --user tidb
gGYWhjTmGBh  2025-05-26T14:23:46+08:00  /db/storage/guolicheng/.tiup/components/cluster/v1.16.2/tiup-cluster check ./topology.yml --user tidb -p
gGYWkvfbTkJ  2025-05-26T14:24:24+08:00  /db/storage/guolicheng/.tiup/components/cluster/v1.16.2/tiup-cluster check ./topology.yml --apply --user root
gGYWps3dmBr  2025-05-26T14:25:16+08:00  /db/storage/guolicheng/.tiup/components/cluster/v1.16.2/tiup-cluster check ./topology.yml --apply --user root
gGYZMCRC3SV  2025-05-26T15:15:46+08:00  /db/storage/guolicheng/.tiup/components/cluster/v1.16.2/tiup-cluster check ./topology.yml --apply --user root
gGYZP32sYrs  2025-05-26T15:16:11+08:00  /db/storage/guolicheng/.tiup/components/cluster/v1.16.2/tiup-cluster check ./topology.yml --apply --user root
gGYZRKMPL6H  2025-05-26T15:16:58+08:00  /db/storage/guolicheng/.tiup/components/cluster/v1.16.2/tiup-cluster check ./topology.yaml --user tidb -p
gGYZXyR2pfQ  2025-05-26T15:18:23+08:00  /db/storage/guolicheng/.tiup/components/cluster/v1.16.2/tiup-cluster deploy tidb542 v5.4.2 ./topology.yml --user root
gGZ00Fjf1HQ  2025-05-26T15:19:18+08:00  /db/storage/guolicheng/.tiup/components/cluster/v1.16.2/tiup-cluster start tidb542 --init
gGZ0jKXyfGf  2025-05-26T15:24:01+08:00  /db/storage/guolicheng/.tiup/components/cluster/v1.16.2/tiup-cluster display tidb542
gGZ8GGQC7bD  2025-05-26T17:28:56+08:00  /db/storage/guolicheng/.tiup/components/cluster/v1.16.2/tiup-cluster list
gGZmRPZMMSc  2025-05-26T20:01:11+08:00  /db/storage/guolicheng/.tiup/components/cluster/v1.16.2/tiup-cluster audit

```




#### 停止集群: `tiup cluster stop`



#### 重启集群: `tiup cluster restart`


#### 缩容集群: `tiup cluster scale-in`



#### 扩容集群: `tiup cluster scale-out`






#### `tiup cluster clean`

- 清理数据



#### 销毁集群: `tiup cluster destroy`

```bash
# tiup cluster destroy tidb542

  ██     ██  █████  ██████  ███    ██ ██ ███    ██  ██████
  ██     ██ ██   ██ ██   ██ ████   ██ ██ ████   ██ ██
  ██  █  ██ ███████ ██████  ██ ██  ██ ██ ██ ██  ██ ██   ███
  ██ ███ ██ ██   ██ ██   ██ ██  ██ ██ ██ ██  ██ ██ ██    ██
   ███ ███  ██   ██ ██   ██ ██   ████ ██ ██   ████  ██████

This operation will destroy tidb v5.4.2 cluster tidb542 and its data.
Are you sure to continue?
(Type "Yes, I know my cluster and data will be deleted." to continue)
: root

Error: Operation aborted by user (with incorrect answer 'root') (tui.operation_aborted)
[guolicheng@48 /db/storage/guolicheng/tmp/tidb-community-server-v6.5.11-linux-amd64] $ tiup cluster destroy tidb542

  ██     ██  █████  ██████  ███    ██ ██ ███    ██  ██████
  ██     ██ ██   ██ ██   ██ ████   ██ ██ ████   ██ ██
  ██  █  ██ ███████ ██████  ██ ██  ██ ██ ██ ██  ██ ██   ███
  ██ ███ ██ ██   ██ ██   ██ ██  ██ ██ ██ ██  ██ ██ ██    ██
   ███ ███  ██   ██ ██   ██ ██   ████ ██ ██   ████  ██████

This operation will destroy tidb v5.4.2 cluster tidb542 and its data.
Are you sure to continue?
(Type "Yes, I know my cluster and data will be deleted." to continue)
: Yes, I know my cluster and data will be deleted.
Destroying cluster...
+ [ Serial ] - SSHKeySet: privateKey=/db/storage/guolicheng/.tiup/storage/cluster/clusters/tidb542/ssh/id_rsa, publicKey=/db/storage/guolicheng/.tiup/storage/cluster/clusters/tidb542/ssh/id_rsa.pub
+ [Parallel] - UserSSH: user=tidb, host=172.17.135.92
+ [Parallel] - UserSSH: user=tidb, host=172.17.135.92
+ [Parallel] - UserSSH: user=tidb, host=172.17.136.198
+ [Parallel] - UserSSH: user=tidb, host=172.17.136.3
+ [Parallel] - UserSSH: user=tidb, host=172.17.136.198
+ [Parallel] - UserSSH: user=tidb, host=172.17.136.198
+ [Parallel] - UserSSH: user=tidb, host=172.17.136.3
+ [Parallel] - UserSSH: user=tidb, host=172.17.136.3
+ [Parallel] - UserSSH: user=tidb, host=172.17.136.3
+ [Parallel] - UserSSH: user=tidb, host=172.17.136.3
+ [Parallel] - UserSSH: user=tidb, host=172.17.136.3
+ [Parallel] - UserSSH: user=tidb, host=172.17.135.92
+ [ Serial ] - StopCluster
Stopping component alertmanager
	Stopping instance 172.17.136.3
	Stop alertmanager 172.17.136.3:9093 success
Stopping component grafana
	Stopping instance 172.17.136.3
	Stop grafana 172.17.136.3:3000 success
Stopping component prometheus
	Stopping instance 172.17.136.3
	Stop prometheus 172.17.136.3:9090 success
Stopping component tidb
	Stopping instance 172.17.136.3
	Stopping instance 172.17.136.198
	Stopping instance 172.17.135.92
	Stop tidb 172.17.136.3:4000 success
	Stop tidb 172.17.136.198:4000 success
	Stop tidb 172.17.135.92:4000 success
Stopping component tikv
	Stopping instance 172.17.136.3
	Stopping instance 172.17.136.198
	Stopping instance 172.17.135.92
	Stop tikv 172.17.135.92:20160 success
	Stop tikv 172.17.136.3:20160 success
	Stop tikv 172.17.136.198:20160 success
Stopping component pd
	Stopping instance 172.17.136.3
	Stopping instance 172.17.135.92
	Stopping instance 172.17.136.198
	Stop pd 172.17.136.198:2379 success
	Stop pd 172.17.136.3:2379 success
	Stop pd 172.17.135.92:2379 success
Stopping component node_exporter
	Stopping instance 172.17.136.3
	Stopping instance 172.17.136.198
	Stopping instance 172.17.135.92
	Stop 172.17.135.92 success
	Stop 172.17.136.198 success
	Stop 172.17.136.3 success
Stopping component blackbox_exporter
	Stopping instance 172.17.136.3
	Stopping instance 172.17.136.198
	Stopping instance 172.17.135.92
	Stop 172.17.136.198 success
	Stop 172.17.135.92 success
	Stop 172.17.136.3 success
+ [ Serial ] - DestroyCluster
Destroying component alertmanager
	Destroying instance 172.17.136.3
Destroy 172.17.136.3 finished
- Destroy alertmanager paths: [/tidb-data/alertmanager-9093 /tidb-deploy/alertmanager-9093/log /tidb-deploy/alertmanager-9093 /etc/systemd/system/alertmanager-9093.service]
Destroying component grafana
	Destroying instance 172.17.136.3
Destroy 172.17.136.3 finished
- Destroy grafana paths: [/tidb-deploy/grafana-3000 /etc/systemd/system/grafana-3000.service]
Destroying component prometheus
	Destroying instance 172.17.136.3
Destroy 172.17.136.3 finished
- Destroy prometheus paths: [/tidb-deploy/prometheus-9090 /etc/systemd/system/prometheus-9090.service /tidb-data/prometheus-9090 /tidb-deploy/prometheus-9090/log]
Destroying component tidb
	Destroying instance 172.17.136.198
Destroy 172.17.136.198 finished
- Destroy tidb paths: [/tidb-deploy/tidb-4000/log /tidb-deploy/tidb-4000 /etc/systemd/system/tidb-4000.service]
	Destroying instance 172.17.135.92
Destroy 172.17.135.92 finished
- Destroy tidb paths: [/tidb-deploy/tidb-4000/log /tidb-deploy/tidb-4000 /etc/systemd/system/tidb-4000.service]
	Destroying instance 172.17.136.3
Destroy 172.17.136.3 finished
- Destroy tidb paths: [/tidb-deploy/tidb-4000/log /tidb-deploy/tidb-4000 /etc/systemd/system/tidb-4000.service]
Destroying component tikv
	Destroying instance 172.17.136.198
Destroy 172.17.136.198 finished
- Destroy tikv paths: [/etc/systemd/system/tikv-20160.service /tidb-data/tikv-20160 /tidb-deploy/tikv-20160/log /tidb-deploy/tikv-20160]
	Destroying instance 172.17.135.92
Destroy 172.17.135.92 finished
- Destroy tikv paths: [/tidb-data/tikv-20160 /tidb-deploy/tikv-20160/log /tidb-deploy/tikv-20160 /etc/systemd/system/tikv-20160.service]
	Destroying instance 172.17.136.3
Destroy 172.17.136.3 finished
- Destroy tikv paths: [/tidb-data/tikv-20160 /tidb-deploy/tikv-20160/log /tidb-deploy/tikv-20160 /etc/systemd/system/tikv-20160.service]
Destroying component pd
	Destroying instance 172.17.136.198
Destroy 172.17.136.198 finished
- Destroy pd paths: [/tidb-deploy/pd-2379 /etc/systemd/system/pd-2379.service /tidb-data/pd-2379 /tidb-deploy/pd-2379/log]
	Destroying instance 172.17.135.92
Destroy 172.17.135.92 finished
- Destroy pd paths: [/tidb-data/pd-2379 /tidb-deploy/pd-2379/log /tidb-deploy/pd-2379 /etc/systemd/system/pd-2379.service]
	Destroying instance 172.17.136.3
Destroy 172.17.136.3 finished
- Destroy pd paths: [/tidb-data/pd-2379 /tidb-deploy/pd-2379/log /tidb-deploy/pd-2379 /etc/systemd/system/pd-2379.service]
Destroying monitored 172.17.136.198
	Destroying instance 172.17.136.198
Destroy monitored on 172.17.136.198 success
Destroying monitored 172.17.135.92
	Destroying instance 172.17.135.92
Destroy monitored on 172.17.135.92 success
Destroying monitored 172.17.136.3
	Destroying instance 172.17.136.3
Destroy monitored on 172.17.136.3 success
Clean global directories 172.17.135.92
	Clean directory /tidb-deploy on instance 172.17.135.92
	Clean directory /tidb-data on instance 172.17.135.92
Clean global directories 172.17.135.92 success
Clean global directories 172.17.136.3
	Clean directory /tidb-deploy on instance 172.17.136.3
	Clean directory /tidb-data on instance 172.17.136.3
Clean global directories 172.17.136.3 success
Clean global directories 172.17.136.198
	Clean directory /tidb-deploy on instance 172.17.136.198
	Clean directory /tidb-data on instance 172.17.136.198
Clean global directories 172.17.136.198 success
Delete public key 172.17.135.92
Delete public key 172.17.135.92 success
Delete public key 172.17.136.3
Delete public key 172.17.136.3 success
Delete public key 172.17.136.198
Delete public key 172.17.136.198 success
Destroyed cluster `tidb542` successfully

```


#### 升级集群: `tiup cluster upgrade`




#### `tiup cluster import`

- 导入一个由 TiDB Ansible 部署的集群



#### `tiup cluster edit-config`

- 编辑 TiDB 集群的配置


#### `tiup cluster reload`

- 用于必要时重载集群配置


#### `tiup cluster patch`

- 使用临时的组件包替换集群上已部署的组件


#### `tiup cluster help`

- 打印 Help 信息










## v6.5.11


```bash
[guolicheng@48 /db/storage/guolicheng/tmp/tidb-community-server-v6.5.11-linux-amd64] $ ll
total 2415260
-rw-r--r-- 1 guolicheng guolicheng     34301 2025-05-25T20:41:24 1005.playground.json
-rw-r--r-- 1 guolicheng guolicheng     58319 2025-05-25T20:41:24 10589.tidb.json
-rw-r--r-- 1 guolicheng guolicheng     43534 2025-05-25T20:41:24 10833.dumpling.json
-rw-r--r-- 1 guolicheng guolicheng     42526 2025-05-25T20:41:24 1085.cluster.json
-rw-r--r-- 1 guolicheng guolicheng     29928 2025-05-25T20:41:24 1085.tiup.json
-rw-r--r-- 1 guolicheng guolicheng     59534 2025-05-25T20:41:24 10886.tidb-lightning.json
-rw-r--r-- 1 guolicheng guolicheng     43340 2025-05-25T20:41:24 10896.br.json
-rw-r--r-- 1 guolicheng guolicheng      8004 2025-05-25T20:41:24 109.cloud.json
-rw-r--r-- 1 guolicheng guolicheng      1037 2025-05-25T20:41:24 10.errdoc.json
-rw-r--r-- 1 guolicheng guolicheng      2041 2025-05-25T20:41:24 115.sync-diff-inspector.json
-rw-r--r-- 1 guolicheng guolicheng      1888 2025-05-25T20:41:24 11.PCC.json
-rw-r--r-- 1 guolicheng guolicheng      7125 2025-05-25T20:41:24 17.tispark.json
-rw-r--r-- 1 guolicheng guolicheng      6643 2025-05-25T20:41:24 18.insight.json
-rw-r--r-- 1 guolicheng guolicheng      5915 2025-05-25T20:41:24 1.index.json
-rw-r--r-- 1 guolicheng guolicheng      5221 2025-05-25T20:41:24 1.root.json
-rw-r--r-- 1 guolicheng guolicheng     14135 2025-05-25T20:41:24 2236.diag.json
-rw-r--r-- 1 guolicheng guolicheng      1104 2025-05-25T20:41:24 2.spark.json
-rw-r--r-- 1 guolicheng guolicheng     31627 2025-05-25T20:41:24 3654.dmctl.json
-rw-r--r-- 1 guolicheng guolicheng     32583 2025-05-25T20:41:24 3663.dm-worker.json
-rw-r--r-- 1 guolicheng guolicheng     32583 2025-05-25T20:41:24 3665.dm-master.json
-rw-r--r-- 1 guolicheng guolicheng      2208 2025-05-25T20:41:24 3.package.json
-rw-r--r-- 1 guolicheng guolicheng      3406 2025-05-25T20:41:24 40.demo.json
-rw-r--r-- 1 guolicheng guolicheng     18347 2025-05-25T20:41:24 431.tidb-dashboard.json
-rw-r--r-- 1 guolicheng guolicheng     55697 2025-05-25T20:41:24 4834.drainer.json
-rw-r--r-- 1 guolicheng guolicheng     54845 2025-05-25T20:41:24 4863.pump.json
-rw-r--r-- 1 guolicheng guolicheng      1016 2025-05-25T20:41:24 4.dba.json
-rw-r--r-- 1 guolicheng guolicheng      1850 2025-05-25T20:41:24 4.tikv-cdc.json
-rw-r--r-- 1 guolicheng guolicheng     58472 2025-05-25T20:41:24 5700.grafana.json
-rw-r--r-- 1 guolicheng guolicheng     59318 2025-05-25T20:41:24 5753.prometheus.json
-rw-r--r-- 1 guolicheng guolicheng      1116 2025-05-25T20:41:24 5.chaosd.json
-rw-r--r-- 1 guolicheng guolicheng       941 2025-05-25T20:41:24 5.pushgateway.json
-rw-r--r-- 1 guolicheng guolicheng     47714 2025-05-25T20:41:24 6261.tiflash.json
-rw-r--r-- 1 guolicheng guolicheng     57999 2025-05-25T20:41:24 6442.tikv.json
-rw-r--r-- 1 guolicheng guolicheng      6122 2025-05-25T20:41:24 649.tiproxy.json
-rw-r--r-- 1 guolicheng guolicheng     43007 2025-05-25T20:41:24 6682.cdc.json
-rw-r--r-- 1 guolicheng guolicheng     57960 2025-05-25T20:41:24 6716.pd-recover.json
-rw-r--r-- 1 guolicheng guolicheng     56913 2025-05-25T20:41:24 6729.pd.json
-rw-r--r-- 1 guolicheng guolicheng      2218 2025-05-25T20:41:24 6.tikv-br.json
-rw-r--r-- 1 guolicheng guolicheng     22373 2025-05-25T20:41:24 715.bench.json
-rw-r--r-- 1 guolicheng guolicheng      1344 2025-05-25T20:41:24 7.alertmanager.json
-rw-r--r-- 1 guolicheng guolicheng      1830 2025-05-25T20:41:24 7.influxdb.json
-rw-r--r-- 1 guolicheng guolicheng     16005 2025-05-25T20:41:24 82.tikv-importer.json
-rw-r--r-- 1 guolicheng guolicheng     47302 2025-05-25T20:41:24 9152.ctl.json
-rw-r--r-- 1 guolicheng guolicheng     25538 2025-05-25T20:41:24 931.client.json
-rw-r--r-- 1 guolicheng guolicheng     27128 2025-05-25T20:41:24 947.server.json
-rw-r--r-- 1 guolicheng guolicheng     26218 2025-05-25T20:41:24 991.dm.json
-rw-r--r-- 1 guolicheng guolicheng      1809 2025-05-25T20:41:24 9.blackbox_exporter.json
-rw-r--r-- 1 guolicheng guolicheng      1768 2025-05-25T20:41:24 9.node_exporter.json
-rw-rw-r-- 1 guolicheng guolicheng  29716416 2025-05-25T19:59:21 alertmanager-v0.26.0-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng  35008229 2025-05-25T19:59:57 bench-v1.12.0-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng  10831581 2025-05-25T20:00:09 blackbox_exporter-v0.23.0-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng  73175260 2025-05-25T20:01:25 br-v6.5.11-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng  71095649 2025-05-25T20:02:39 cdc-v6.5.11-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng  27379472 2025-05-25T20:03:07 chaosd-v1.1.1-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng   5400647 2025-05-25T20:03:13 client-v1.16.2-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng   9725222 2025-05-25T20:03:24 cloud-v1.0.0-beta.4-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng   9308623 2025-05-25T20:03:33 cluster-v1.16.2-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng 373797082 2025-05-25T20:10:02 ctl-v6.5.11-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng  19150816 2025-05-25T20:10:23 dba-v1.0.4-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng   2756603 2025-05-25T20:10:26 demo-v0.0.11-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng  28214478 2025-05-25T20:10:55 diag-v1.6.0-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng  61672546 2025-05-25T20:14:28 dmctl-v6.5.11-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng  68342863 2025-05-25T20:12:15 dm-master-v6.5.11-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng   8941827 2025-05-25T20:11:04 dm-v1.16.2-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng  65395602 2025-05-25T20:13:23 dm-worker-v6.5.11-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng  57560926 2025-05-25T20:15:28 drainer-v6.5.11-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng  43760572 2025-05-25T20:16:13 dumpling-v6.5.11-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng  10593859 2025-05-25T20:16:25 errdoc-v4.0.7-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng  52575491 2025-05-25T20:17:19 grafana-v6.5.11-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng  46738444 2025-05-25T20:18:08 influxdb-v2.5.0-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng   2956230 2025-05-25T20:18:11 insight-v0.4.2-linux-amd64.tar.gz
drwxrwxr-x 2 guolicheng guolicheng      4096 2025-05-26T14:28:59 keys
-rwxrwxr-x 1 guolicheng guolicheng      2530 2025-05-25T20:41:24 local_install.sh
-rw-rw-r-- 1 guolicheng guolicheng  10181529 2025-05-25T20:18:21 node_exporter-v1.5.0-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng   5819867 2025-05-25T20:18:27 package-v0.0.9-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng  15666495 2025-05-25T19:58:50 PCC-v1.0.2-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng  15734182 2025-05-25T20:19:35 pd-recover-v6.5.11-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng  49129132 2025-05-25T20:19:19 pd-v6.5.11-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng   8601987 2025-05-25T20:19:44 playground-v1.16.2-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng  98825786 2025-05-25T20:21:27 prometheus-v6.5.11-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng  54856314 2025-05-25T20:22:23 pump-v6.5.11-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng   6553694 2025-05-25T20:22:31 pushgateway-v0.7.0-linux-amd64.tar.gz
-rw-r--r-- 1 guolicheng guolicheng      5221 2025-05-25T20:41:24 root.json
-rw-rw-r-- 1 guolicheng guolicheng   3794986 2025-05-25T20:22:34 server-v1.16.2-linux-amd64.tar.gz
-rw-r--r-- 1 guolicheng guolicheng      2792 2025-05-25T20:41:24 snapshot.json
-rw-rw-r-- 1 guolicheng guolicheng 229988313 2025-05-25T20:26:32 spark-v2.4.3-any-any.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng  61940417 2025-05-25T20:27:38 sync-diff-inspector-v9.0.0-beta.1-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng  37042724 2025-05-25T20:29:30 tidb-dashboard-v6.5.11-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng  68772709 2025-05-25T20:30:42 tidb-lightning-v6.5.11-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng  71377179 2025-05-25T20:28:52 tidb-v6.5.11-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng 205428251 2025-05-25T20:34:14 tiflash-v6.5.11-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng  19504242 2025-05-25T20:39:13 tikv-br-v1.1.0-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng  56448448 2025-05-25T20:40:11 tikv-cdc-v1.1.1-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng  10330655 2025-05-25T20:40:22 tikv-importer-v4.0.2-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng 268076957 2025-05-25T20:38:53 tikv-v6.5.11-linux-amd64.tar.gz
-rw-r--r-- 1 guolicheng guolicheng       819 2025-05-25T20:41:24 timestamp.json
-rw-rw-r-- 1 guolicheng guolicheng  24978590 2025-05-25T20:40:48 tiproxy-v1.3.0-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng  23450452 2025-05-25T20:41:12 tispark-v2.4.1-any-any.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng   5920146 2025-05-25T20:41:24 tiup-linux-amd64.tar.gz
-rw-rw-r-- 1 guolicheng guolicheng   5304979 2025-05-25T20:41:18 tiup-v1.16.2-linux-amd64.tar.gz
drwxrwxr-x 2 guolicheng guolicheng      4096 2025-05-26T14:28:57 _tmp_1748240937727501452
[guolicheng@48 /db/storage/guolicheng/tmp/tidb-community-server-v6.5.11-linux-amd64] $ ./local_install.sh 

Detected shell: bash
Shell profile:  /db/storage/guolicheng/.bash_profile

✔ Installed in /db/storage/guolicheng/.tiup/bin/tiup
✔ tiup PATH is already set, skip

tiup is installed now 🎉

Next step:

  1: To make PATH change effective, restart your shell or execute:
     source /db/storage/guolicheng/.bash_profile

  2: Start a local TiDB for development:
     tiup playground
[guolicheng@48 /db/storage/guolicheng/tmp/tidb-community-server-v6.5.11-linux-amd64] $ source /db/storage/guolicheng/.bash_profile
[guolicheng@48 /db/storage/guolicheng/tmp/tidb-community-server-v6.5.11-linux-amd64] $ cd ~
[guolicheng@48 /db/storage/guolicheng] $ tiup mirror show
/db/storage/guolicheng/tmp/tidb-community-server-v6.5.11-linux-amd64
[guolicheng@48 /db/storage/guolicheng] $ cd tmp/
[guolicheng@48 /db/storage/guolicheng/tmp] $ ll
total 12924116
drwxrwxr-x  2 guolicheng guolicheng        4096 2025-04-29T10:54:11 1
-rw-rw-r--  1 guolicheng guolicheng           0 2025-04-29T10:53:26 16315_16310.cap
drwxrwxr-x 70 guolicheng guolicheng        4096 2025-04-18T19:47:53 glibc-2.28
-rw-rw-r--  1 guolicheng guolicheng    32584904 2025-04-18T19:47:26 glibc-2.28.tar.gz
drwxr-xr-x  9 guolicheng guolicheng        4096 2025-04-18T19:58:03 make-4.2.1
-rw-rw-r--  1 guolicheng guolicheng     1977576 2025-04-18T19:50:24 make-4.2.1.tar.gz
-rw-rw-r--  1 guolicheng guolicheng          19 2025-05-26T15:21:14 password.log
drwxrwxr-x  2 guolicheng guolicheng        4096 2024-09-13T17:39:11 tcp
-rw-rw-r--  1 guolicheng guolicheng 10737418240 2025-04-30T08:54:36 testfile
drwxrwxr-x  3 guolicheng guolicheng        4096 2025-05-26T15:14:28 tidb-community-server-v5.4.2-linux-amd64
drwxrwxr-x  4 guolicheng guolicheng        4096 2025-05-26T14:28:57 tidb-community-server-v6.5.11-linux-amd64
-rw-rw-r--  1 guolicheng guolicheng  2462240410 2025-05-26T09:04:32 tidb-community-server-v6.5.11-linux-amd64.tar.gz
drwxrwxr-x  3 guolicheng guolicheng        4096 2025-05-26T09:26:05 tiup-custom-mirror-v1.12.3
drwxrwxr-x  3 guolicheng guolicheng        4096 2025-05-26T09:48:57 tiup-custom-mirror-v5.4.0
-rw-rw-r--  1 guolicheng guolicheng       10888 2025-05-25T10:24:14 topology.yaml
-rw-rw-r--  1 guolicheng guolicheng         591 2025-05-26T14:23:28 topology.yml
drwx------  2 guolicheng guolicheng        4096 2025-05-27T22:42:01 vscode-goXPV1AO
drwxrwxr-x  2 guolicheng guolicheng        4096 2025-05-27T22:11:47 vscode-typescript54579
[guolicheng@48 /db/storage/guolicheng/tmp] $ cat topology.yml
global:
  user: "tidb"
  ssh_port: 22
  deploy_dir: "/tidb-deploy"
  data_dir: "/tidb-data"
  listen_host: 0.0.0.0
  arch: "amd64"

monitored:
  node_exporter_port: 9100
  blackbox_exporter_port: 9115

pd_servers:
  - host: 172.17.136.198
  - host: 172.17.135.92
  - host: 172.17.136.3

tidb_servers:
  - host: 172.17.136.198
  - host: 172.17.135.92
  - host: 172.17.136.3

tikv_servers:
  - host: 172.17.136.198
  - host: 172.17.135.92
  - host: 172.17.136.3

monitoring_servers:
  - host: 172.17.136.3

grafana_servers:
  - host: 172.17.136.3

alertmanager_servers:
  - host: 172.17.136.3
[guolicheng@48 /db/storage/guolicheng/tmp] $ tiup cluster list
Name  User  Version  Path  PrivateKey
----  ----  -------  ----  ----------
[guolicheng@48 /db/storage/guolicheng/tmp] $ tiup cluster deploy tidb6511 v6.5.11 ./topology.yml --user root



+ Detect CPU Arch Name
  - Detecting node 172.17.136.198 Arch info ... Done
  - Detecting node 172.17.135.92 Arch info ... Done
  - Detecting node 172.17.136.3 Arch info ... Done



+ Detect CPU OS Name
  - Detecting node 172.17.136.198 OS info ... Done
  - Detecting node 172.17.135.92 OS info ... Done
  - Detecting node 172.17.136.3 OS info ... Done
Please confirm your topology:
Cluster type:    tidb
Cluster name:    tidb6511
Cluster version: v6.5.11
Role          Host            Ports        OS/Arch       Directories
----          ----            -----        -------       -----------
pd            172.17.136.198  2379/2380    linux/x86_64  /tidb-deploy/pd-2379,/tidb-data/pd-2379
pd            172.17.135.92   2379/2380    linux/x86_64  /tidb-deploy/pd-2379,/tidb-data/pd-2379
pd            172.17.136.3    2379/2380    linux/x86_64  /tidb-deploy/pd-2379,/tidb-data/pd-2379
tikv          172.17.136.198  20160/20180  linux/x86_64  /tidb-deploy/tikv-20160,/tidb-data/tikv-20160
tikv          172.17.135.92   20160/20180  linux/x86_64  /tidb-deploy/tikv-20160,/tidb-data/tikv-20160
tikv          172.17.136.3    20160/20180  linux/x86_64  /tidb-deploy/tikv-20160,/tidb-data/tikv-20160
tidb          172.17.136.198  4000/10080   linux/x86_64  /tidb-deploy/tidb-4000
tidb          172.17.135.92   4000/10080   linux/x86_64  /tidb-deploy/tidb-4000
tidb          172.17.136.3    4000/10080   linux/x86_64  /tidb-deploy/tidb-4000
prometheus    172.17.136.3    9090/12020   linux/x86_64  /tidb-deploy/prometheus-9090,/tidb-data/prometheus-9090
grafana       172.17.136.3    3000         linux/x86_64  /tidb-deploy/grafana-3000
alertmanager  172.17.136.3    9093/9094    linux/x86_64  /tidb-deploy/alertmanager-9093,/tidb-data/alertmanager-9093
Attention:
    1. If the topology is not what you expected, check your yaml file.
    2. Please confirm there is no port/directory conflicts in same host.
Do you want to continue? [y/N]: (default=N) y
+ Generate SSH keys ... Done
+ Download TiDB components
  - Download pd:v6.5.11 (linux/amd64) ... Done
  - Download tikv:v6.5.11 (linux/amd64) ... Done
  - Download tidb:v6.5.11 (linux/amd64) ... Done
  - Download prometheus:v6.5.11 (linux/amd64) ... Done
  - Download grafana:v6.5.11 (linux/amd64) ... Done
  - Download alertmanager: (linux/amd64) ... Done
  - Download node_exporter: (linux/amd64) ... Done
  - Download blackbox_exporter: (linux/amd64) ... Done
+ Initialize target host environments
  - Prepare 172.17.136.198:22 ... Done
  - Prepare 172.17.135.92:22 ... Done
  - Prepare 172.17.136.3:22 ... Done
+ Deploy TiDB instance
  - Copy pd -> 172.17.136.198 ... Done
  - Copy pd -> 172.17.135.92 ... Done
  - Copy pd -> 172.17.136.3 ... Done
  - Copy tikv -> 172.17.136.198 ... Done
  - Copy tikv -> 172.17.135.92 ... Done
  - Copy tikv -> 172.17.136.3 ... Done
  - Copy tidb -> 172.17.136.198 ... Done
  - Copy tidb -> 172.17.135.92 ... Done
  - Copy tidb -> 172.17.136.3 ... Done
  - Copy prometheus -> 172.17.136.3 ... Done
  - Copy grafana -> 172.17.136.3 ... Done
  - Copy alertmanager -> 172.17.136.3 ... Done
  - Deploy node_exporter -> 172.17.136.3 ... Done
  - Deploy node_exporter -> 172.17.136.198 ... Done
  - Deploy node_exporter -> 172.17.135.92 ... Done
  - Deploy blackbox_exporter -> 172.17.136.198 ... Done
  - Deploy blackbox_exporter -> 172.17.135.92 ... Done
  - Deploy blackbox_exporter -> 172.17.136.3 ... Done
+ Copy certificate to remote host
+ Init instance configs
  - Generate config pd -> 172.17.136.198:2379 ... Done
  - Generate config pd -> 172.17.135.92:2379 ... Done
  - Generate config pd -> 172.17.136.3:2379 ... Done
  - Generate config tikv -> 172.17.136.198:20160 ... Done
  - Generate config tikv -> 172.17.135.92:20160 ... Done
  - Generate config tikv -> 172.17.136.3:20160 ... Done
  - Generate config tidb -> 172.17.136.198:4000 ... Done
  - Generate config tidb -> 172.17.135.92:4000 ... Done
  - Generate config tidb -> 172.17.136.3:4000 ... Done
  - Generate config prometheus -> 172.17.136.3:9090 ... Done
  - Generate config grafana -> 172.17.136.3:3000 ... Done
  - Generate config alertmanager -> 172.17.136.3:9093 ... Done
+ Init monitor configs
  - Generate config node_exporter -> 172.17.136.198 ... Done
  - Generate config node_exporter -> 172.17.135.92 ... Done
  - Generate config node_exporter -> 172.17.136.3 ... Done
  - Generate config blackbox_exporter -> 172.17.136.198 ... Done
  - Generate config blackbox_exporter -> 172.17.135.92 ... Done
  - Generate config blackbox_exporter -> 172.17.136.3 ... Done
Enabling component pd
	Enabling instance 172.17.136.3:2379
	Enabling instance 172.17.136.198:2379
	Enabling instance 172.17.135.92:2379
	Enable instance 172.17.136.198:2379 success
	Enable instance 172.17.136.3:2379 success
	Enable instance 172.17.135.92:2379 success
Enabling component tikv
	Enabling instance 172.17.136.3:20160
	Enabling instance 172.17.136.198:20160
	Enabling instance 172.17.135.92:20160
	Enable instance 172.17.136.198:20160 success
	Enable instance 172.17.135.92:20160 success
	Enable instance 172.17.136.3:20160 success
Enabling component tidb
	Enabling instance 172.17.136.3:4000
	Enabling instance 172.17.135.92:4000
	Enabling instance 172.17.136.198:4000
	Enable instance 172.17.136.198:4000 success
	Enable instance 172.17.135.92:4000 success
	Enable instance 172.17.136.3:4000 success
Enabling component prometheus
	Enabling instance 172.17.136.3:9090
	Enable instance 172.17.136.3:9090 success
Enabling component grafana
	Enabling instance 172.17.136.3:3000
	Enable instance 172.17.136.3:3000 success
Enabling component alertmanager
	Enabling instance 172.17.136.3:9093
	Enable instance 172.17.136.3:9093 success
Enabling component node_exporter
	Enabling instance 172.17.136.3
	Enabling instance 172.17.136.198
	Enabling instance 172.17.135.92
	Enable 172.17.136.198 success
	Enable 172.17.136.3 success
	Enable 172.17.135.92 success
Enabling component blackbox_exporter
	Enabling instance 172.17.136.3
	Enabling instance 172.17.135.92
	Enabling instance 172.17.136.198
	Enable 172.17.136.198 success
	Enable 172.17.135.92 success
	Enable 172.17.136.3 success
Cluster `tidb6511` deployed successfully, you can start it with command: `tiup cluster start tidb6511 --init`
[guolicheng@48 /db/storage/guolicheng/tmp] $ tiup cluster start tidb6511 --init
Starting cluster tidb6511...
+ [ Serial ] - SSHKeySet: privateKey=/db/storage/guolicheng/.tiup/storage/cluster/clusters/tidb6511/ssh/id_rsa, publicKey=/db/storage/guolicheng/.tiup/storage/cluster/clusters/tidb6511/ssh/id_rsa.pub
+ [Parallel] - UserSSH: user=tidb, host=172.17.135.92
+ [Parallel] - UserSSH: user=tidb, host=172.17.136.198
+ [Parallel] - UserSSH: user=tidb, host=172.17.136.198
+ [Parallel] - UserSSH: user=tidb, host=172.17.135.92
+ [Parallel] - UserSSH: user=tidb, host=172.17.136.3
+ [Parallel] - UserSSH: user=tidb, host=172.17.136.3
+ [Parallel] - UserSSH: user=tidb, host=172.17.136.198
+ [Parallel] - UserSSH: user=tidb, host=172.17.136.3
+ [Parallel] - UserSSH: user=tidb, host=172.17.135.92
+ [Parallel] - UserSSH: user=tidb, host=172.17.136.3
+ [Parallel] - UserSSH: user=tidb, host=172.17.136.3
+ [Parallel] - UserSSH: user=tidb, host=172.17.136.3
+ [ Serial ] - StartCluster
Starting component pd
	Starting instance 172.17.136.3:2379
	Starting instance 172.17.135.92:2379
	Starting instance 172.17.136.198:2379
	Start instance 172.17.136.198:2379 success
	Start instance 172.17.135.92:2379 success
	Start instance 172.17.136.3:2379 success
Starting component tikv
	Starting instance 172.17.136.3:20160
	Starting instance 172.17.136.198:20160
	Starting instance 172.17.135.92:20160
	Start instance 172.17.136.198:20160 success
	Start instance 172.17.136.3:20160 success
	Start instance 172.17.135.92:20160 success
Starting component tidb
	Starting instance 172.17.135.92:4000
	Starting instance 172.17.136.3:4000
	Starting instance 172.17.136.198:4000
	Start instance 172.17.135.92:4000 success
	Start instance 172.17.136.3:4000 success
	Start instance 172.17.136.198:4000 success
Starting component prometheus
	Starting instance 172.17.136.3:9090
	Start instance 172.17.136.3:9090 success
Starting component grafana
	Starting instance 172.17.136.3:3000
	Start instance 172.17.136.3:3000 success
Starting component alertmanager
	Starting instance 172.17.136.3:9093
	Start instance 172.17.136.3:9093 success
Starting component node_exporter
	Starting instance 172.17.136.3
	Starting instance 172.17.136.198
	Starting instance 172.17.135.92
	Start 172.17.136.198 success
	Start 172.17.135.92 success
	Start 172.17.136.3 success
Starting component blackbox_exporter
	Starting instance 172.17.136.3
	Starting instance 172.17.135.92
	Starting instance 172.17.136.198
	Start 172.17.136.198 success
	Start 172.17.135.92 success
	Start 172.17.136.3 success
+ [ Serial ] - UpdateTopology: cluster=tidb6511
Started cluster `tidb6511` successfully
The root password of TiDB database has been changed.
The new password is: 'VYi*H45820U-Sk^T7@'.
Copy and record it to somewhere safe, it is only displayed once, and will not be stored.
The generated password can NOT be get and shown again.
[guolicheng@48 /db/storage/guolicheng/tmp] $ mysql -uroot -p'VYi*H45820U-Sk^T7@' -h172.17.136.3 -P4000
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 405
Server version: 5.7.25-TiDB-v6.5.11 TiDB Server (Apache License 2.0) Community Edition, MySQL 5.7 compatible

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

[root@172.17.130.48:4000 23:03:37((none))]$ set password for 'root'@'%' = 'root';
Query OK, 0 rows affected (0.06 sec)

[root@172.17.130.48:4000 23:04:26((none))]$ 

```










