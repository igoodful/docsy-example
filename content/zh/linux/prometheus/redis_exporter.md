---
title: redis_exporter 9121
description: redis_exporter 9121
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


















