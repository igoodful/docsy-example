---
title: 安装
description: prometheus 9090
date: 2023-11-05
weight: 1000


---
{{< alert >}}
[官网下载地址](https://prometheus.io/download/)

- 支持多种操作系统：Linux、Windows、Mac

1. Prometheus是使用Go编写的



{{< /alert >}}

## 安装


```bash
prometheus_version="2.48.0"

curl -LO  https://github.com/prometheus/prometheus/releases/download/v${prometheus_version}/prometheus-${prometheus_version}.linux-amd64.tar.gz

tar -xzvf prometheus-${prometheus_version}.linux-amd64.tar.gz

cd prometheus-${prometheus_version}.linux-amd64



```


## 配置：prometheus.yaml


解压后的目录结构：
```bash
[root@k8s-node-70 prometheus-2.48.0-rc.2.linux-amd64]# tree
.
├── console_libraries
│   ├── menu.lib
│   └── prom.lib
├── consoles
│   ├── index.html.example
│   ├── node-cpu.html
│   ├── node-disk.html
│   ├── node.html
│   ├── node-overview.html
│   ├── prometheus.html
│   └── prometheus-overview.html
├── LICENSE
├── NOTICE
├── prometheus
├── prometheus.yml
└── promtool

2 directories, 14 files


```


默认配置文件内容：
```bash
[root@k8s-node-70 prometheus]# cat prometheus.yml
global:
  scrape_interval: 15s
  evaluation_interval: 15s
alerting:
  alertmanagers:
    - static_configs:
        - targets: ['localhost:9093']
rule_files:
scrape_configs:
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]
```

## 启动参数


| 参数                                         | 功能                                                              | 说明                                 |
|:---------------------------------------------|:------------------------------------------------------------------|:-------------------------------------|
| `--config.file`                              | 指定配置文件                                                      |                                      |
| `--storage.tsdb.path`                        | 数据存储路径                                                      | 默认值为: data                       |
| `--storage.tsdb.retention.time`              | 样本数据保存时间限制                                              | 任意1个达到了阈值，就会删除过期的数据 |
| `--storage.tsdb.retention.size`              | 样本数据保存大小限制                                              | 任意1个达到了阈值，就会删除过期的数据 |
| `--web.listen-address`                       | 指定启动端口号                                                    | 默认为: `0.0.0.0:9090`               |
| `--web.read-timeout`                         | 页面读取请求最大超时时间                                          | 默认为: 5m                           |
| `--query.max-concurrency`                    | 并发执行的最大查询数                                              | 默认为20                             |
| `--web.max-connections`                      | 同时访问Prometheus页面的最大连接数                                | 默认为: 512                          |
| `--web.enable-lifecycle`                     | 通过HTTP请求启用关闭和重新加载                                    |                                      |
| `--web.console.templates`                    | 到控制台模板目录的路径                                            | 默认为: consoles/                    |
| `--web.console.libraries`                    | 控制台库目录的路径                                                | 默认为: `console_libraries`          |
| `--alertmanager.timeout`                     | 发送警报到Alertmanager的超时时间                                  | 10s                                  |
| `--alertmanager.notification-queue-capacity` | 等待报警通知队列的大小                                            | `10000`                              |
| `--query.timeout`                            | 一个查询在终止之前可以执行的最长时间(如果超过2min，就会自动kill掉) | 默认为: 2m                           |
| ` --log.level`                               | 开启打印日志级别(debug,info,warn,error,fatal)                     | 默认为: info                         |
|                                              |                                                                   |                                      |
|                                              |                                                                   |                                      |
|                                              |                                                                   |                                      |
|                                              |                                                                   |                                      |
|                                              |                                                                   |                                      |

未设置参数 --web.enable-lifecycle时，执行curl -X POST http://localhost:9090/-/reload 会报错：

启动设置参数--web.enable-lifecycle就可以用命令 curl -X POST http://localhost:9090/-/reload 重新加载配置文件了。








## systemd
{{<note>}}
<!---->

{{</note>}}

`[root@k8s-node-70 prometheus]# cat  /usr/lib/systemd/system/prometheus.service`，输出如下：
```systemd
[Unit]
Description=Prometheus service
After=network.target

[Service]
User=root
Type=simple
ExecReload=/bin/sh -c "/bin/kill -1 `/usr/bin/pgrep prometheus`"
ExecStop=/bin/sh -c "/bin/kill -9 `/usr/bin/pgrep prometheus`"
ExecStart=/home/work/prometheus/prometheus \
--config.file=/home/work/prometheus/prometheus.yml \
--storage.tsdb.path=/home/work/prometheus/data \
--web.console.templates=/home/work/prometheus/consoles \
--web.console.libraries=/home/work/prometheus/console_libraries \
--web.enable-lifecycle

[Install]
WantedBy=multi-user.target
```

```bash
systemctl daemon-reload

systemctl enable prometheus.service

systemctl start  prometheus.service

systemctl status  prometheus

```







#### 配置案例详解
> `2024-06-24T10:10:24 [mysql@node70 /rds/greatrds/etc/monitor] $ cat prometheus.yml`

```yaml
global:
  scrape_interval:     10s
  evaluation_interval: 10s
alerting:
  alertmanagers:
  - static_configs:
    - targets:
      - '172.17.134.70:9093'
      - '172.17.134.71:9093'

rule_files:
- /etc/greatrds/monitor/rules/*

scrape_configs:
  - job_name: 'file_ds'
    file_sd_configs:
      - files:
        - /etc/greatrds/monitor/scraps/*.json
        refresh_interval: 5s
    honor_labels: true
  - job_name: 'rds'
    static_configs:
      - targets: ['172.17.134.70:9100']
        labels:
          __metrics_path__: '/rds_metrics'
      - targets: ['172.17.134.71:9100']
        labels:
          __metrics_path__: '/rds_metrics'
    honor_labels: true

```

###### 1. global
- scrape_interval：设定 Prometheus 默认的抓取间隔时间为 10 秒钟，也就是说每 10 秒钟 Prometheus 会去抓取一次配置的监控数据。
- evaluation_interval：设定 Prometheus 评估规则的间隔时间为 10 秒钟，这意味着每 10 秒钟 Prometheus 会评估一次所有的告警规则和记录规则。

###### 2. alerting
- alerting：定义了告警相关的配置。
- alertmanagers：指定了 Alertmanager 的目标地址，这里配置了两个 Alertmanager 实例，分别是 172.17.134.70:9093 和 172.17.134.71:9093。Prometheus 将告警发送到这些 Alertmanager 实例。

###### 3. rule_files
- rule_files：指定了 Prometheus 的规则文件路径。这里的规则文件路径是 /etc/greatrds/monitor/rules/*，这意味着所有位于该路径下的规则文件都会被加载。这些规则文件可以包含告警规则和记录规则。

###### 4. scrape_configs
- 配置了 Prometheus 如何从不同的目标抓取监控数据。这里定义了两个 job。
- job_name：作业名称为 file_ds。
- file_sd_configs：j基于文件服务发现配置。Prometheus 会从 /etc/greatrds/monitor/scraps/ 目录下的 JSON 文件中读取要监控的目标，并且每 5 秒钟刷新一次这些文件。
- honor_labels：设置为 true 时，表示在抓取数据时保留目标的标签，而不被配置中的标签覆盖。

- job_name：作业名称为 rds。
- static_configs：静态配置了两个监控目标，分别是 172.17.134.70:9100 和 172.17.134.71:9100。
- targets：监控目标列表，这里是两个 IP 地址和端口。
- labels：为每个目标配置了 __metrics_path__ 标签，表示要抓取的 metrics 数据路径是 /rds_metrics。
- honor_labels：设置为 true，表示保留目标的标签，而不被配置中的标签覆盖。

**总结**

这个 Prometheus 配置文件主要包括以下几个方面：

- 全局配置：定义了全局的抓取间隔和评估间隔。
- 告警配置：指定了 Alertmanager 的地址。
- 规则文件：指定了 Prometheus 规则文件的位置。
- 抓取配置：定义了两个 job，一个是通过文件服务发现监控目标（file_ds），另一个是静态配置监控目标（rds）。每个 job 都有不同的目标和抓取路径。

这样配置可以帮助 Prometheus 从不同来源高效地收集监控数据，并将告警信息发送到指定的 Alertmanager。


#### 动态


```bash
2024-06-24T10:30:27 [mysql@node70 /rds/greatrds/etc/monitor/scraps] $ ll
total 28
-rw-r--r-- 1 root root 9473 Jun 24 10:30 cluster-172.17.134.77-16310-e6076e61-v2.json
-rw-r--r-- 1 root root  277 Jun 24 10:30 node-172.17.134.70-53155a46-v2.json
-rw-r--r-- 1 root root  277 Jun 24 10:30 node-172.17.134.71-96e00279-v2.json
-rw-r--r-- 1 root root  277 Jun 24 10:30 node-172.17.134.76-60d47998-v2.json
-rw-r--r-- 1 root root  277 Jun 24 10:30 node-172.17.134.77-10afcd96-v2.json

```


###### cat cluster-172.17.134.77-16310-e6076e61-v2.yaml


```yaml
- targets:
    - '172.17.134.70:9010'
  labels:
    __param_instance_type: cluster
    __param_instance_uuid: e6076e61-c39f-4da3-82b1-2d10aaa3b073
    __metrics_path__: /v1/rds/monitor/metrics
- targets:
    - '172.17.134.71:9000'
  labels:
    __metrics_path__: /metrics
    __param_type: mysql
    __param_address: 172.17.134.71
    __param_n_address: 172.17.134.71
    __param_port: '16315'
    __param_username: admin
    __param_password: '123456'
    __param_extra_labels: >-
      {"instance":"e6076e61-c39f-4da3-82b1-2d10aaa3b073","member":"datasource","member_uuid":"16315fff-a467-44c0-8bff-de230308b123"}
- targets:
    - '172.17.134.71:9000'
  labels:
    __metrics_path__: /metrics
    __param_type: mysql_node
    __param_address: 172.17.134.71
    __param_n_address: 172.17.134.71
    __param_port: '16315'
    __param_username: admin
    __param_password: '123456'
    __param_extra_labels: >-
      {"instance":"e6076e61-c39f-4da3-82b1-2d10aaa3b073","member":"datasource","member_uuid":"16315fff-a467-44c0-8bff-de230308b123"}
    __param_extra_params: >-
      {"datadir":
      "/data/mysqldata/16315fff-a467-44c0-8bff-de230308b123/dbdata","err_log":
      "/data/mysqldata/16315fff-a467-44c0-8bff-de230308b123/logfile/mysqld.err","general_log":
      "/data/mysqldata/16315fff-a467-44c0-8bff-de230308b123/logfile/mysqld.log","slow_log":
      "/data/mysqldata/16315fff-a467-44c0-8bff-de230308b123/logfile/mysqld-slow.log","pid_file":
      "/data/mysqldata/16315fff-a467-44c0-8bff-de230308b123/pid/mysqld.pid"}
- targets:
    - '172.17.134.76:9000'
  labels:
    __metrics_path__: /metrics
    __param_type: mysql
    __param_address: 172.17.134.76
    __param_n_address: 172.17.134.76
    __param_port: '16315'
    __param_username: admin
    __param_password: '123456'
    __param_extra_labels: >-
      {"instance":"e6076e61-c39f-4da3-82b1-2d10aaa3b073","member":"datasource","member_uuid":"16315fff-c9e4-48e0-94a2-bcba9a7cccd6"}
- targets:
    - '172.17.134.76:9000'
  labels:
    __metrics_path__: /metrics
    __param_type: mysql_node
    __param_address: 172.17.134.76
    __param_n_address: 172.17.134.76
    __param_port: '16315'
    __param_username: admin
    __param_password: '123456'
    __param_extra_labels: >-
      {"instance":"e6076e61-c39f-4da3-82b1-2d10aaa3b073","member":"datasource","member_uuid":"16315fff-c9e4-48e0-94a2-bcba9a7cccd6"}
    __param_extra_params: >-
      {"datadir":
      "/data/mysqldata/16315fff-c9e4-48e0-94a2-bcba9a7cccd6/dbdata","err_log":
      "/data/mysqldata/16315fff-c9e4-48e0-94a2-bcba9a7cccd6/logfile/mysqld.err","general_log":
      "/data/mysqldata/16315fff-c9e4-48e0-94a2-bcba9a7cccd6/logfile/mysqld.log","slow_log":
      "/data/mysqldata/16315fff-c9e4-48e0-94a2-bcba9a7cccd6/logfile/mysqld-slow.log","pid_file":
      "/data/mysqldata/16315fff-c9e4-48e0-94a2-bcba9a7cccd6/pid/mysqld.pid"}
- targets:
    - '172.17.134.77:9000'
  labels:
    __metrics_path__: /metrics
    __param_type: mysql
    __param_address: 172.17.134.77
    __param_n_address: 172.17.134.77
    __param_port: '16315'
    __param_username: admin
    __param_password: '123456'
    __param_extra_labels: >-
      {"instance":"e6076e61-c39f-4da3-82b1-2d10aaa3b073","member":"datasource","member_uuid":"16315fff-f4a3-42ca-852a-164d4f94b7e8"}
- targets:
    - '172.17.134.77:9000'
  labels:
    __metrics_path__: /metrics
    __param_type: mysql_node
    __param_address: 172.17.134.77
    __param_n_address: 172.17.134.77
    __param_port: '16315'
    __param_username: admin
    __param_password: '123456'
    __param_extra_labels: >-
      {"instance":"e6076e61-c39f-4da3-82b1-2d10aaa3b073","member":"datasource","member_uuid":"16315fff-f4a3-42ca-852a-164d4f94b7e8"}
    __param_extra_params: >-
      {"datadir":
      "/data/mysqldata/16315fff-f4a3-42ca-852a-164d4f94b7e8/dbdata","err_log":
      "/data/mysqldata/16315fff-f4a3-42ca-852a-164d4f94b7e8/logfile/mysqld.err","general_log":
      "/data/mysqldata/16315fff-f4a3-42ca-852a-164d4f94b7e8/logfile/mysqld.log","slow_log":
      "/data/mysqldata/16315fff-f4a3-42ca-852a-164d4f94b7e8/logfile/mysqld-slow.log","pid_file":
      "/data/mysqldata/16315fff-f4a3-42ca-852a-164d4f94b7e8/pid/mysqld.pid"}
- targets:
    - '172.17.134.77:9000'
  labels:
    __metrics_path__: /metrics
    __param_type: zookeeper
    __param_address: 172.17.134.77
    __param_n_address: 172.17.134.77
    __param_port: '16309'
    __param_username: admin
    __param_password: '123456'
    __param_extra_labels: >-
      {"instance":"e6076e61-c39f-4da3-82b1-2d10aaa3b073","member":"configuration","member_uuid":"16309fff-9b23-4a32-a4f3-3d22b0d00bce"}
- targets:
    - '172.17.134.77:9000'
  labels:
    __metrics_path__: /metrics
    __param_type: zookeeper_node
    __param_address: 172.17.134.77
    __param_n_address: 172.17.134.77
    __param_port: '16309'
    __param_username: admin
    __param_password: '123456'
    __param_extra_labels: >-
      {"instance":"e6076e61-c39f-4da3-82b1-2d10aaa3b073","member":"configuration","member_uuid":"16309fff-9b23-4a32-a4f3-3d22b0d00bce"}
- targets:
    - '172.17.134.76:9000'
  labels:
    __metrics_path__: /metrics
    __param_type: zookeeper
    __param_address: 172.17.134.76
    __param_n_address: 172.17.134.76
    __param_port: '16309'
    __param_username: admin
    __param_password: '123456'
    __param_extra_labels: >-
      {"instance":"e6076e61-c39f-4da3-82b1-2d10aaa3b073","member":"configuration","member_uuid":"16309fff-dc43-4915-857d-c91f2fc11526"}
- targets:
    - '172.17.134.76:9000'
  labels:
    __metrics_path__: /metrics
    __param_type: zookeeper_node
    __param_address: 172.17.134.76
    __param_n_address: 172.17.134.76
    __param_port: '16309'
    __param_username: admin
    __param_password: '123456'
    __param_extra_labels: >-
      {"instance":"e6076e61-c39f-4da3-82b1-2d10aaa3b073","member":"configuration","member_uuid":"16309fff-dc43-4915-857d-c91f2fc11526"}
- targets:
    - '172.17.134.71:9000'
  labels:
    __metrics_path__: /metrics
    __param_type: zookeeper
    __param_address: 172.17.134.71
    __param_n_address: 172.17.134.71
    __param_port: '16309'
    __param_username: admin
    __param_password: '123456'
    __param_extra_labels: >-
      {"instance":"e6076e61-c39f-4da3-82b1-2d10aaa3b073","member":"configuration","member_uuid":"16309fff-afa9-4bea-9f7b-564995dd16ba"}
- targets:
    - '172.17.134.71:9000'
  labels:
    __metrics_path__: /metrics
    __param_type: zookeeper_node
    __param_address: 172.17.134.71
    __param_n_address: 172.17.134.71
    __param_port: '16309'
    __param_username: admin
    __param_password: '123456'
    __param_extra_labels: >-
      {"instance":"e6076e61-c39f-4da3-82b1-2d10aaa3b073","member":"configuration","member_uuid":"16309fff-afa9-4bea-9f7b-564995dd16ba"}
- targets:
    - '172.17.134.77:9000'
  labels:
    __metrics_path__: /metrics
    __param_type: dbscale
    __param_address: 172.17.134.77
    __param_n_address: 172.17.134.77
    __param_port: '16310'
    __param_username: admin
    __param_password: '123456'
    __param_extra_labels: >-
      {"instance":"e6076e61-c39f-4da3-82b1-2d10aaa3b073","member":"scheduler","member_uuid":"16310fff-38b8-4c58-998b-42d848738674"}
- targets:
    - '172.17.134.77:9000'
  labels:
    __metrics_path__: /metrics
    __param_type: dbscale_node
    __param_address: 172.17.134.77
    __param_n_address: 172.17.134.77
    __param_port: '16310'
    __param_username: admin
    __param_password: '123456'
    __param_extra_labels: >-
      {"instance":"e6076e61-c39f-4da3-82b1-2d10aaa3b073","member":"scheduler","member_uuid":"16310fff-38b8-4c58-998b-42d848738674"}
- targets:
    - '172.17.134.76:9000'
  labels:
    __metrics_path__: /metrics
    __param_type: dbscale
    __param_address: 172.17.134.76
    __param_n_address: 172.17.134.76
    __param_port: '16310'
    __param_username: admin
    __param_password: '123456'
    __param_extra_labels: >-
      {"instance":"e6076e61-c39f-4da3-82b1-2d10aaa3b073","member":"scheduler","member_uuid":"16310fff-fd2c-450d-87e4-070edda72f38"}
- targets:
    - '172.17.134.76:9000'
  labels:
    __metrics_path__: /metrics
    __param_type: dbscale_node
    __param_address: 172.17.134.76
    __param_n_address: 172.17.134.76
    __param_port: '16310'
    __param_username: admin
    __param_password: '123456'
    __param_extra_labels: >-
      {"instance":"e6076e61-c39f-4da3-82b1-2d10aaa3b073","member":"scheduler","member_uuid":"16310fff-fd2c-450d-87e4-070edda72f38"}
- targets:
    - '172.17.134.71:9000'
  labels:
    __metrics_path__: /metrics
    __param_type: dbscale
    __param_address: 172.17.134.71
    __param_n_address: 172.17.134.71
    __param_port: '16310'
    __param_username: admin
    __param_password: '123456'
    __param_extra_labels: >-
      {"instance":"e6076e61-c39f-4da3-82b1-2d10aaa3b073","member":"scheduler","member_uuid":"16310fff-76c8-4531-bbe7-ce1bd6a47882"}
- targets:
    - '172.17.134.71:9000'
  labels:
    __metrics_path__: /metrics
    __param_type: dbscale_node
    __param_address: 172.17.134.71
    __param_n_address: 172.17.134.71
    __param_port: '16310'
    __param_username: admin
    __param_password: '123456'
    __param_extra_labels: >-
      {"instance":"e6076e61-c39f-4da3-82b1-2d10aaa3b073","member":"scheduler","member_uuid":"16310fff-76c8-4531-bbe7-ce1bd6a47882"}

```

###### cat node-172.17.134.70-53155a46-v2.yaml

```yaml
- targets:
    - '172.17.134.70:9000'
  labels:
    __metrics_path__: /metrics
    __param_type: node
    __param_address: 172.17.134.70
    __param_extra_params: '{"data_dir": "/data"}'
    __param_extra_labels: '{"instance":"53155a46-671d-4dbb-98ec-a060b330da82"}'

```
- 实际URL：http://172.17.134.70:9000/metrics?type=node&address=172.17.134.70&extra_params={%22data_dir%22:%20%22/data%22}&extra_labels={%22instance%22:%2296e00279-07a9-4aac-b066-c44c8f9461dc%22}



###### cat node-172.17.134.71-96e00279-v2.yaml

- [https://www.bejson.com/encrypt/utf8encode/](https://www.bejson.com/encrypt/utf8encode/)

```yaml
- targets:
    - '172.17.134.71:9000'
  labels:
    __metrics_path__: /metrics
    __param_type: node
    __param_address: 172.17.134.71
    __param_extra_params: '{"data_dir": "/data"}'
    __param_extra_labels: '{"instance":"96e00279-07a9-4aac-b066-c44c8f9461dc"}'

```
- 实际URL：`http://172.17.134.71:9000/metrics?type=node&address=172.17.134.71&extra_params={%22data_dir%22:%20%22/data%22}&extra_labels={%22instance%22:%2296e00279-07a9-4aac-b066-c44c8f9461dc%22}`
- 双引号`"`的UTF-8转码为`%22`
- 空格` `的UTF-8转码为`%20`




###### cat node-172.17.134.76-60d47998-v2.yaml

```yaml
- targets:
    - '172.17.134.76:9000'
  labels:
    __metrics_path__: /metrics
    __param_type: node
    __param_address: 172.17.134.76
    __param_extra_params: '{"data_dir": "/data"}'
    __param_extra_labels: '{"instance":"60d47998-3236-425b-a631-1635aacab748"}'
```



###### cat node-172.17.134.77-10afcd96-v2.yaml

```yaml
- targets:
    - '172.17.134.77:9000'
  labels:
    __metrics_path__: /metrics
    __param_type: node
    __param_address: 172.17.134.77
    __param_extra_params: '{"data_dir": "/data"}'
    __param_extra_labels: '{"instance":"10afcd96-4a05-4954-9d3a-76861acf00ef"}'

```









