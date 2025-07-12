---
title: node_exporter 9100
description: node_exporter默认端口9100
date: 2023-11-05
weight: 30000


---
{{< alert >}}
[node_exporter官网](https://prometheus.io/download/)
{{< /alert >}}

## 安装


```sql
curl -OL https://github.com/prometheus/node_exporter/releases/download/v1.6.1/node_exporter-1.6.1.linux-amd64.tar.gz
tar -xzf node_exporter-1.6.1.linux-amd64.tar.gz
cd node_exporter-1.6.1.linux-amd64/
mv node_exporter /usr/local/bin/
touch node.log
node_exporter --web.listen-address=":9200" --web.config.file=./node.log &
```


## 配置

```yaml
[root@k8s-node-70 prometheus-2.48.0-rc.2.linux-amd64]# cat prometheus.yml
global:
  scrape_interval: 15s
  evaluation_interval: 15s
alerting:
  alertmanagers:
    - static_configs:
        - targets:
rule_files:
scrape_configs:
  - job_name: "prometheus"
    static_configs:
      - targets: ["172.17.134.70:9090"]
  - job_name: "node"
    static_configs:
      - targets: ['172.17.134.70:9200']

```



## 配置：service
> cat /usr/lib/systemd/system/node_exporter.service
```systemd
[Service]
User=prometheus
Group=prometheus
ExecStart=/home/work/node_exporter/node_exporter --web.listen-address=":9200"

[Install]
WantedBy=multi-user.target

[Unit]
Description=node_exporter
After=network.target

```


```bash
systemctl start node_exporter

systemctl status node_exporter

systemctl enable node_exporter

```


##

```
http://172.17.134.70:9200/
```





## 源码分析



#### 内存

- [/proc/meminfo](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/6/html/deployment_guide/s2-proc-meminfo#s2-proc-meminfo)


```go
// 所有监控指标的共同命令空间的前缀
const namespace = "node"

// 所有内存相关的监控指标的共同命令空间的子前缀
const (
	memInfoSubsystem = "memory"
)
```

根据`2024-07-26T16:07:02 [root@dev /root] $ cat /proc/meminfo`得到内存监控
```bash
MemTotal:       16211044 kB
MemFree:         1009864 kB
MemAvailable:   13467760 kB
Buffers:            1608 kB
Cached:         12624128 kB
SwapCached:         9988 kB
Active:          4155384 kB
Inactive:       10465792 kB
Active(anon):     520872 kB
Inactive(anon):  1590696 kB
Active(file):    3634512 kB
Inactive(file):  8875096 kB
Unevictable:           0 kB
Mlocked:               0 kB
SwapTotal:       2097148 kB
SwapFree:        1991180 kB
Dirty:                84 kB
Writeback:             0 kB
AnonPages:       1899524 kB
Mapped:           344832 kB
Shmem:            116132 kB
KReclaimable:     285896 kB
Slab:             397124 kB
SReclaimable:     285896 kB
SUnreclaim:       111228 kB
KernelStack:        5872 kB
PageTables:        20072 kB
NFS_Unstable:          0 kB
Bounce:                0 kB
WritebackTmp:          0 kB
CommitLimit:    10202668 kB
Committed_AS:    5072116 kB
VmallocTotal:   34359738367 kB
VmallocUsed:           0 kB
VmallocChunk:          0 kB
Percpu:            22848 kB
HardwareCorrupted:     0 kB
AnonHugePages:   1304576 kB
ShmemHugePages:        0 kB
ShmemPmdMapped:        0 kB
FileHugePages:         0 kB
FilePmdMapped:         0 kB
HugePages_Total:       0
HugePages_Free:        0
HugePages_Rsvd:        0
HugePages_Surp:        0
Hugepagesize:       2048 kB
Hugetlb:               0 kB
DirectMap4k:      268268 kB
DirectMap2M:    15460352 kB
DirectMap1G:     3145728 kB

```









