---
title: tiup mirror
description: 镜像管理
date: 2025-05-27
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

#### tidb-community-server-v6.5.12-linux-amd64.tar.gz

| 内容                                            | 变更说明         |
| :---------------------------------------------- | :--------------- |
| tidb-{version}-linux-{arch}.tar.gz              |                  |
| tikv-{version}-linux-{arch}.tar.gz              |                  |
| tiflash-{version}-linux-{arch}.tar.gz           |                  |
| pd-{version}-linux-{arch}.tar.gz                |                  |
| ctl-{version}-linux-{arch}.tar.gz               |                  |
| grafana-{version}-linux-{arch}.tar.gz           |                  |
| alertmanager-{version}-linux-{arch}.tar.gz      |                  |
| blackbox_exporter-{version}-linux-{arch}.tar.gz |                  |
| prometheus-{version}-linux-{arch}.tar.gz        |                  |
| node_exporter-{version}-linux-{arch}.tar.gz     |                  |
| tiup-linux-{arch}.tar.gz                        |                  |
| tiup-{version}-linux-{arch}.tar.gz              |                  |
| local_install.sh                                |                  |
| cluster-{version}-linux-{arch}.tar.gz           |                  |
| insight-{version}-linux-{arch}.tar.gz           |                  |
| diag-{version}-linux-{arch}.tar.gz              | 从 v6.0.0 起新增 |
| influxdb-{version}-linux-{arch}.tar.gz          |                  |
| playground-{version}-linux-{arch}.tar.gz        |                  |
| tiproxy-{version}-linux-{arch}.tar.gz           | 从 v7.6.0 起新增 |

#### tidb-community-toolkit-v6.5.12-linux-amd64.tar.gz

| 内容                                            | 变更说明         |
| :---------------------------------------------- | :--------------- |
| pd-recover-{version}-linux-{arch}.tar.gz        |                  |
| etcdctl                                         | 从 v6.0.0 起新增 |
| tiup-linux-{arch}.tar.gz                        |                  |
| tiup-{version}-linux-{arch}.tar.gz              |                  |
| tidb-lightning-{version}-linux-{arch}.tar.gz    |                  |
| tidb-lightning-ctl                              |                  |
| dumpling-{version}-linux-{arch}.tar.gz          |                  |
| cdc-{version}-linux-{arch}.tar.gz               |                  |
| dm-{version}-linux-{arch}.tar.gz                |                  |
| dm-worker-{version}-linux-{arch}.tar.gz         |                  |
| dm-master-{version}-linux-{arch}.tar.gz         |                  |
| dmctl-{version}-linux-{arch}.tar.gz             |                  |
| br-{version}-linux-{arch}.tar.gz                |                  |
| package-{version}-linux-{arch}.tar.gz           |                  |
| bench-{version}-linux-{arch}.tar.gz             |                  |
| errdoc-{version}-linux-{arch}.tar.gz            |                  |
| dba-{version}-linux-{arch}.tar.gz               |                  |
| PCC-{version}-linux-{arch}.tar.gz               |                  |
| sync_diff_inspector                             |                  |
| reparo                                          |                  |
| server-{version}-linux-{arch}.tar.gz            | v6.2.0 起新增    |
| grafana-{version}-linux-{arch}.tar.gz           | v6.2.0 起新增    |
| alertmanager-{version}-linux-{arch}.tar.gz      | v6.2.0 起新增    |
| prometheus-{version}-linux-{arch}.tar.gz        | v6.2.0 起新增    |
| blackbox_exporter-{version}-linux-{arch}.tar.gz | v6.2.0 起新增    |
| node_exporter-{version}-linux-{arch}.tar.gz     | v6.2.0 起新增    |


## 图解

```viz-dot
digraph "tiup mirror" {
        rankdir = LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
        ];

        "tiup mirror" -> "clone";
        "tiup mirror" -> "show";
        "tiup mirror" -> "set";
        "tiup mirror" -> "init";
        "tiup mirror" -> "merge";
        "tiup mirror" -> "genkey";
        "tiup mirror" -> "sign";
        "tiup mirror" -> "grant";
        "tiup mirror" -> "rotate";
        "tiup mirror" -> "modify";
        "tiup mirror" -> "publish";
        "tiup mirror" -> "renew";
        "tiup mirror" -> "transfer-owner";
}
```

#### clone

- 从当前镜像中克隆出一个新的镜像,可以指定自己需要的版本，组件，平台等信息。新旧镜像的组件相同，但使用的签名密钥不同。
- `tiup mirror clone <target-dir> [global version] [flags]`

```bash
# tiup mirror clone --help

Clone a local mirror from remote mirror and download all selected components

Usage:
  tiup mirror clone <target-dir> [global version] [flags]

Examples:
  tiup mirror clone /path/to/local --arch amd64,arm64 --os linux,darwin    # Specify the architectures and OSs
  tiup mirror clone /path/to/local --os linux v6.1.0 v5.4.0              # Specify multiple versions
  tiup mirror clone /path/to/local --full                                # Build a full local mirror
  tiup mirror clone /path/to/local --tikv v4  --prefix                   # Specify the version via prefix
  tiup mirror clone /path/to/local --tidb all --pd all                   # Download all version for specific component

Flags:
  -f, --full                          Build a full mirrors repository
  -a, --arch strings                  Specify the downloading architecture (default [amd64,arm64])
  -o, --os strings                    Specify the downloading os (default [linux,darwin])
      --prefix                        Download the version with matching prefix
      --jobs uint                     Specify the number of concurrent download jobs (default 1)
  -h, --help                          help for clone
      --PCC strings                   Specify the versions for component PCC
      --alertmanager strings          Specify the versions for component alertmanager
      --bench strings                 Specify the versions for component bench
      --blackbox_exporter strings     Specify the versions for component blackbox_exporter
      --br strings                    Specify the versions for component br
      --cdc strings                   Specify the versions for component cdc
      --chaosd strings                Specify the versions for component chaosd
      --client strings                Specify the versions for component client
      --cloud strings                 Specify the versions for component cloud
      --cluster strings               Specify the versions for component cluster
      --ctl strings                   Specify the versions for component ctl
      --dba strings                   Specify the versions for component dba
      --demo strings                  Specify the versions for component demo
      --diag strings                  Specify the versions for component diag
      --dm strings                    Specify the versions for component dm
      --dm-master strings             Specify the versions for component dm-master
      --dm-worker strings             Specify the versions for component dm-worker
      --dmctl strings                 Specify the versions for component dmctl
      --drainer strings               Specify the versions for component drainer
      --dumpling strings              Specify the versions for component dumpling
      --errdoc strings                Specify the versions for component errdoc
      --grafana strings               Specify the versions for component grafana
      --influxdb strings              Specify the versions for component influxdb
      --insight strings               Specify the versions for component insight
      --node_exporter strings         Specify the versions for component node_exporter
      --package strings               Specify the versions for component package
      --pd strings                    Specify the versions for component pd
      --pd-recover strings            Specify the versions for component pd-recover
      --playground strings            Specify the versions for component playground
      --prometheus strings            Specify the versions for component prometheus
      --pump strings                  Specify the versions for component pump
      --pushgateway strings           Specify the versions for component pushgateway
      --server strings                Specify the versions for component server
      --spark strings                 Specify the versions for component spark
      --sync-diff-inspector strings   Specify the versions for component sync-diff-inspector
      --tidb strings                  Specify the versions for component tidb
      --tidb-dashboard strings        Specify the versions for component tidb-dashboard
      --tidb-lightning strings        Specify the versions for component tidb-lightning
      --tiflash strings               Specify the versions for component tiflash
      --tikv strings                  Specify the versions for component tikv
      --tikv-br strings               Specify the versions for component tikv-br
      --tikv-cdc strings              Specify the versions for component tikv-cdc
      --tikv-importer strings         Specify the versions for component tikv-importer
      --tiproxy strings               Specify the versions for component tiproxy
      --tispark strings               Specify the versions for component tispark
      --tiup strings                  Specify the versions for component tiup

```

```bash
tiup mirror clone /home/work/v6.5.11 --os linux v6.5.11 v5.4.2

tiup mirror clone /home/work/v6.5.11 --full

tiup mirror clone /home/work/v6.5.11 --tidb all --pd all

tiup mirror clone /home/work/v6.5.11 --arch amd64,arm64 --os linux v6.5.11


```


#### set

`export TIUP_MIRRORS=/home/work/tidb-community-toolkit-v6.5.11-linux-amd64`

```bash
tiup mirror set /home/work/v6.5.11

tiup mirror clone /home/work/tidb-community-toolkit-v6.5.11-linux-amd64 v6.5.11 --arch amd64 --os linux


```

#### merge

作用：制作一个包含多个版本的镜像包，可以部署和管理多个版本的集群，因为一般的镜像包只包含一个特定的版本

- https://tidb.net/blog/eeebe568

```bash
tiup mirror set /data/v6.5.11

cd  /data/v6.5.11

# 这一步很重要，需要将当前镜像中keys拷贝到~/.tiup/
cp -r keys ~/.tiup/

tiup mirror merge /data/v5.4.2

tiup list tidb


```

- 比如现在的镜像地址为`/db/storage/guolicheng/tmp/tidb-community-server-v6.5.11-linux-amd64`，

```bash
[guolicheng@48 /db/storage/guolicheng/tmp] $ ll
total 12924128
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
drwxr-xr-x  3 guolicheng guolicheng        4096 2025-02-27T19:57:20 tidb-community-server-v6.5.12-linux-amd64
drwxr-xr-x  3 guolicheng guolicheng        4096 2025-02-27T19:59:24 tidb-community-toolkit-v6.5.12-linux-amd64
drwxrwxr-x  3 guolicheng guolicheng        4096 2025-05-26T09:26:05 tiup-custom-mirror-v1.12.3
drwxrwxr-x  3 guolicheng guolicheng        4096 2025-05-26T09:48:57 tiup-custom-mirror-v5.4.0
drwxrwxr-x  2 guolicheng guolicheng        4096 2025-05-28T09:21:46 tmp
-rw-rw-r--  1 guolicheng guolicheng       10888 2025-05-25T10:24:14 topology.yaml
-rw-rw-r--  1 guolicheng guolicheng         591 2025-05-26T14:23:28 topology.yml
drwx------  2 guolicheng guolicheng        4096 2025-05-28T08:55:19 vscode-gonmP5xM
drwxrwxr-x  2 guolicheng guolicheng        4096 2025-05-27T22:11:47 vscode-typescript54579
[guolicheng@48 /db/storage/guolicheng/tmp] $ tiup mirror show
/db/storage/guolicheng/tmp/tidb-community-server-v6.5.11-linux-amd64
[guolicheng@48 /db/storage/guolicheng/tmp] $ tiup mirror merge tidb-community-server-v6.5.12-linux-amd64
Error: lstat /db/storage/guolicheng/.tiup/keys: no such file or directory
[guolicheng@48 /db/storage/guolicheng/tmp] $ tiup mirror merge tidb-community-server-v6.5.12-linux-amd64
[guolicheng@48 /db/storage/guolicheng/tmp] $ cp -r tidb-community-server-v6.5.11-linux-amd64/keys ~/.tiup/
[guolicheng@48 /db/storage/guolicheng/tmp] $ tiup mirror merge tidb-community-server-v6.5.12-linux-amd64
[guolicheng@48 /db/storage/guolicheng/tmp] $ tiup mirror merge tidb-community-toolkit-v6.5.12-linux-amd64
[guolicheng@48 /db/storage/guolicheng/tmp] $ tiup list tidb
Available versions for tidb:
Version  Installed  Release                    Platforms
-------  ---------  -------                    ---------
v6.5.11  YES        2024-09-20T03:16:44Z       linux/amd64
v6.5.12             2025-05-28T09:24:07+08:00  linux/amd64


```








