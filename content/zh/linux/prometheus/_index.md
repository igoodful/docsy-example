---
title: prometheus
description: prometheus
date: 2023-11-05
weight: 30000


---
{{< alert >}}
1. Prometheus是使用Go编写的
2. Prometheus默认监听9090端口

{{< /alert >}}


{{<caution>}}
<!---->
[文档](https://opensource.actionsky.com/category/%e6%8a%80%e6%9c%af%e4%b8%93%e6%a0%8f/prometheus/)

[文档](https://www.prometheus.wang/quickstart/use-node-exporter.html)

[文档](https://prometheus.fuckcloudnative.io/di-san-zhang-prometheus/di-4-jie-cha-xun/basics)
{{</caution>}}

### 简单配置

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: prometheus
    static_configs:
      - targets: ["localhost:9090"]
  - job_name: node_exporter
    static_configs:
      - targets: ["localhost:9100"]

```




