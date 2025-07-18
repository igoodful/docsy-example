---
title: zk监控
description: alert
date: 2023-11-05
weight: 30000
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}


{{< /alert >}}


### 配置：zoo.cfg
> `curl http://127.0.0.1:7000/metrics`
```yaml
metricsProvider.className=org.apache.zookeeper.metrics.prometheus.PrometheusMetricsProvider
metricsProvider.httpPort=7000
```

### 配置：prometheus.yaml
```yaml
scrape_configs:
  - job_name: test-zk
    static_configs:
    - targets: ['192.168.10.32:7000','192.168.10.33:7000','192.168.10.34:7000']
```

### 配置：prometheus的rules
> `cat rules/zk.yml`
```yaml
  groups:
  - name: zk-alert-example
    rules:
    - alert: ZooKeeper server is down
      expr:  up == 0
      for: 1m
      labels:
        severity: critical
      annotations:
        summary: "Instance {{ $labels.instance }} ZooKeeper server is down"
        description: "{{ $labels.instance }} of job {{$labels.job}} ZooKeeper server is down: [{{ $value }}]."

    - alert: create too many znodes
      expr: znode_count > 1000000
      for: 1m
      labels:
        severity: warning
      annotations:
        summary: "Instance {{ $labels.instance }} create too many znodes"
        description: "{{ $labels.instance }} of job {{$labels.job}} create too many znodes: [{{ $value }}]."

    - alert: create too many connections
      expr: num_alive_connections > 50 # suppose we use the default maxClientCnxns: 60
      for: 1m
      labels:
        severity: warning
      annotations:
        summary: "Instance {{ $labels.instance }} create too many connections"
        description: "{{ $labels.instance }} of job {{$labels.job}} create too many connections: [{{ $value }}]."

    - alert: znode total occupied memory is too big
      expr: approximate_data_size /1024 /1024 > 1 * 1024 # more than 1024 MB(1 GB)
      for: 1m
      labels:
        severity: warning
      annotations:
        summary: "Instance {{ $labels.instance }} znode total occupied memory is too big"
        description: "{{ $labels.instance }} of job {{$labels.job}} znode total occupied memory is too big: [{{ $value }}] MB."

    - alert: set too many watch
      expr: watch_count > 10000
      for: 1m
      labels:
        severity: warning
      annotations:
        summary: "Instance {{ $labels.instance }} set too many watch"
        description: "{{ $labels.instance }} of job {{$labels.job}} set too many watch: [{{ $value }}]."

    - alert: a leader election happens
      expr: increase(election_time_count[5m]) > 0
      for: 1m
      labels:
        severity: warning
      annotations:
        summary: "Instance {{ $labels.instance }} a leader election happens"
        description: "{{ $labels.instance }} of job {{$labels.job}} a leader election happens: [{{ $value }}]."

    - alert: open too many files
      expr: open_file_descriptor_count > 300
      for: 1m
      labels:
        severity: warning
      annotations:
        summary: "Instance {{ $labels.instance }} open too many files"
        description: "{{ $labels.instance }} of job {{$labels.job}} open too many files: [{{ $value }}]."

    - alert: fsync time is too long
      expr: rate(fsynctime_sum[1m]) > 100
      for: 1m
      labels:
        severity: warning
      annotations:
        summary: "Instance {{ $labels.instance }} fsync time is too long"
        description: "{{ $labels.instance }} of job {{$labels.job}} fsync time is too long: [{{ $value }}]."

    - alert: take snapshot time is too long
      expr: rate(snapshottime_sum[5m]) > 100
      for: 1m
      labels:
        severity: warning
      annotations:
        summary: "Instance {{ $labels.instance }} take snapshot time is too long"
        description: "{{ $labels.instance }} of job {{$labels.job}} take snapshot time is too long: [{{ $value }}]."

    - alert: avg latency is too high
      expr: avg_latency > 100
      for: 1m
      labels:
        severity: warning
      annotations:
        summary: "Instance {{ $labels.instance }} avg latency is too high"
        description: "{{ $labels.instance }} of job {{$labels.job}} avg latency is too high: [{{ $value }}]."

    - alert: JvmMemoryFillingUp
      expr: jvm_memory_bytes_used / jvm_memory_bytes_max{area="heap"} > 0.8
      for: 5m
      labels:
        severity: warning
      annotations:
        summary: "JVM memory filling up (instance {{ $labels.instance }})"
        description: "JVM memory is filling up (> 80%)\n labels: {{ $labels }}  value = {{ $value }}\n"
```

### 配置：Alertmanager
```bash


```















