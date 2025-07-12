---
title: tiup cluster
description: 集群管理
date: 2025-05-28
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

## 离线包内容

- https://cn.pingcap.com/product-community/
- https://docs.pingcap.com/zh/tidb/stable/binary-package/#tidb-%E7%A6%BB%E7%BA%BF%E5%8C%85
- https://download.pingcap.org/tidb-community-server-v6.5.12-linux-amd64.tar.gz
- https://download.pingcap.org/tidb-community-toolkit-v6.5.12-linux-amd64.tar.gz





```bash
# tiup cluster list
Name      User  Version  Path                                                            PrivateKey
----      ----  -------  ----                                                            ----------
tidb6511  tidb  v6.5.11  /db/storage/guolicheng/.tiup/storage/cluster/clusters/tidb6511  /db/storage/guolicheng/.tiup/storage/cluster/clusters/tidb6511/ssh/id_rsa

# tiup cluster display tidb6511
Cluster type:       tidb
Cluster name:       tidb6511
Cluster version:    v6.5.11
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





