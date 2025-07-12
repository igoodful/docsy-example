---
title: AlertManager
description: AlertManager
date: 2023-11-05
weight: 30000


---

[AlertManager官网](https://prometheus.io/download/)
- Prometheus负责产生告警。在Prometheus Server中定义告警规则以及产生告警，在Prometheus中自定义告警规则
- Alertmanager负责告警产生后的后续处理


## 一、安装

```bash
cd /data/
export VERSION=0.15.2
curl -LO https://github.com/prometheus/alertmanager/releases/download/v$VERSION/alertmanager-$VERSION.darwin-amd64.tar.gz
tar xvf alertmanager-$VERSION.darwin-amd64.tar.gz

mkdir -p alertmanager_9093/{bin,conf,logs,data,templates}
```

#### 1.service
> `cat <<EOF >/lib/systemd/system/alertmanager.service`
```yaml
[Unit]
Description=alertmanager
Documentation=https://prometheus.io/
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
User=prometheus
ExecStart=/data/alertmanager_9093/bin/alertmanager --storage.path="/data/alertmanager_9093/data/" \
--config.file=/data/alertmanager_9093/alertmanager.yml \
--web.external-url=http://192.168.1.220 # 此处可以写域名，需要做proxy。
Restart=always
RestartSec=1
# Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF

```




## 二、配置



#### 1. global

| 选项                 |                                               |
|:---------------------|:----------------------------------------------|
| `resolve_timeout`    | 持续5分钟没收到告警信息后认为问题已解决的作用 |
| `repeat_interval`    |                                               |
| `smtp_smarthost`     | 告警邮件发送者SMTP地址: `smtp.qq.com:465`     |
| `smtp_from`          | 发件者邮箱                                    |
| `smtp_auth_username` | 账号: `1234567890@qq.com`                     |
| `smtp_auth_password` | 邮箱专用授权码: `auth_pass`                   |
| `smtp_require_tls`   | `false`表示关闭tls授权,有些邮箱需要开启此配置 |




#### 2. route

| 选项              | 说明                                                                                       |
|:------------------|:-----------------------------------------------------------------------------------------|
| `group_by`        | 分组字段列表                                                                               |
| `group_wait`      | 第一组告警发送通知需要等待的时间，足够的时间为同一分组获取多个告警，然后一起触发这个告警信息 |
| `group_interval`  | 当第一个报警发送后，等待`group_interval`时间来发送新的一组报警信息                          |
| `repeat_interval` | 如果一个报警信息已经发送成功了，等待 `repeat_interval`时间来重新发送他们                    |
| `routes`          | `receiver,group_by,group_wait,match/match_re,repeat_interval,routes`对象列表               |
| `receiver`        | 指定默认的接收器                                                                           |


其中告警的匹配有两种方式可以选择：
- 第一种方式基于字符串验证，通过设置match规则判断当前告警中是否存在标签labelname并且其值等于labelvalue。
  - `service: files`:匹配出标签含有service=files的告警
- 第二种方式则基于正则表达式，通过设置match_re验证当前告警标签的值是否满足正则表达式的内容。
  - `^(foo1|foo2|baz)$`:匹配出标签含有service=foo1或service=foo2或service=baz的告警



#### 3. receivers

每个 receiver 都有需要设置一个全局唯一的名称，并且对应一个或者多个通知方式，包括email、微信、Slack、钉钉等

| 选项              | 说明                                                         |
|:------------------|:-------------------------------------------------------------|
| `name`            | 名称                                                         |
| `webhook_configs` | `url,send_resolved`列表                                      |
| `email_configs`   | `to,html,headers{subject,from,to},send_resolved`             |
| `wechat_configs`  | `corp_id,api_url,to_party,agent_id,api_secret,send_resolved` |
| `slack_configs`   | ``                                                           |



- webhook_configs，email_configs，wechat_configs可同属于一个name

#### 4. inhibit_rules
抑制是一个对象列表

| 选项           | 说明       |
|:---------------|:-----------|
| `equal`        | 列表       |
| `source_match` | `severity` |
| `target_match` | `severity` |


```yaml
inhibit_rules:
- equal:
  - metric_name
  - instance
  - member_uuid
  source_match:
    severity: critical
  target_match:
    severity: major
- equal:
  - metric_name
  - instance
  - member_uuid
  source_match:
    severity: major
  target_match:
    severity: minor
- equal:
  - metric_name
  - instance
  - member_uuid
  source_match:
    severity: minor
  target_match:
    severity: info


```


#### 5. templates





Alertmanager主要负责对Prometheus产生的告警进行统一处理，因此在Alertmanager配置中一般会包含以下几个主要部分：

- 全局配置（global）：用于定义一些全局的公共参数，如全局的SMTP配置，Slack配置等内容；
- 模板（templates）：用于定义告警通知时的模板，如HTML模板，邮件模板等；
- 告警路由（route）：根据标签匹配，确定当前告警应该如何处理；
- 接收人（receivers）：接收人是一个抽象的概念，它可以是一个邮箱也可以是微信，Slack或者Webhook等，接收人一般配合告警路由使用；
- 抑制规则（inhibit_rules）：合理设置抑制规则可以减少垃圾告警的产生
其完整配置格式如下：
```yaml
global:
  [ resolve_timeout: <duration> | default = 5m ]
  [ smtp_from: <tmpl_string> ]
  [ smtp_smarthost: <string> ]
  [ smtp_hello: <string> | default = "localhost" ]
  [ smtp_auth_username: <string> ]
  [ smtp_auth_password: <secret> ]
  [ smtp_auth_identity: <string> ]
  [ smtp_auth_secret: <secret> ]
  [ smtp_require_tls: <bool> | default = true ]
  [ slack_api_url: <secret> ]
  [ victorops_api_key: <secret> ]
  [ victorops_api_url: <string> | default = "https://alert.victorops.com/integrations/generic/20131114/alert/" ]
  [ pagerduty_url: <string> | default = "https://events.pagerduty.com/v2/enqueue" ]
  [ opsgenie_api_key: <secret> ]
  [ opsgenie_api_url: <string> | default = "https://api.opsgenie.com/" ]
  [ hipchat_api_url: <string> | default = "https://api.hipchat.com/" ]
  [ hipchat_auth_token: <secret> ]
  [ wechat_api_url: <string> | default = "https://qyapi.weixin.qq.com/cgi-bin/" ]
  [ wechat_api_secret: <secret> ]
  [ wechat_api_corp_id: <string> ]
  [ http_config: <http_config> ]

templates:
  [ - <filepath> ... ]

route: <route>

receivers:
  - <receiver> ...

inhibit_rules:
  [ - <inhibit_rule> ... ]

```
在全局配置中需要注意的是resolve_timeout，该参数定义了当Alertmanager持续多长时间未接收到告警后标记告警状态为resolved（已解决）。该参数的定义可能会影响到告警恢复通知的接收时间，读者可根据自己的实际场景进行定义，其默认值为5分钟。在接下来的部分，我们将已一些实际的例子解释Alertmanager的其它配置内容。


```yaml
global:
  resolve_timeout: 5m

route:
  group_by: ['alertname']
  group_wait: 10s
  group_interval: 10s
  repeat_interval: 12h
  receiver: 'web.hook'
receivers:
- name: 'web.hook'
  webhook_configs:
  - url: 'http://127.0.0.1:5001/'
inhibit_rules:
  - source_match:
      severity: 'critical'
    target_match:
      severity: 'warning'
    equal: ['alertname', 'dev', 'instance']

```



## 启动

Alertmanager启动后可以通过9093端口访问，http://10.10.10.10:9093




## 关联Prometheus与Alertmanager


在Prometheus的架构中被划分成两个独立的部分。Prometheus负责产生告警，而Alertmanager负责告警产生后的后续处理。因此Alertmanager部署完成后，需要在Prometheus中设置Alertmanager相关的信息。


编辑Prometheus配置文件prometheus.yml,并添加以下内容

```yaml

alerting:
  alertmanagers:
    - static_configs:
        - targets: ['localhost:9093']
```

重启Prometheus服务，成功后，可以从http://10.10.10.10:9090/config查看alerting配置是否生效。


此时，再次尝试手动拉高系统CPU使用率：`cat /dev/zero>/dev/null`

```sql
./alertmanager --config.file=alertmanager.yml --web.listen-address=":9066"

```





```bash
cat > /etc/alertmanager/alertmanager.yml <<EOF
global:
  resolve_timeout: 5m
  smtp_from: 'igoodful@qq.com'
  smtp_smarthost: 'smtp.qq.com:465'
  smtp_auth_username: 'igoodful@qq.com'
  smtp_auth_password: 'xvzzfzzhxcmvbbjb'
  smtp_require_tls: false
  smtp_hello: 'qq.com'

route:
  group_by: ['alertname']
  group_wait: 5s
  group_interval: 5s
  repeat_interval: 5m
  receiver: 'email'

receivers:
- name: 'email'
  email_configs:
  - to: 'igoodful@qq.com'
    send_resolved: true
EOF

```

使CPU升高
```sql
for i in `seq $(cat /proc/cpuinfo |grep "processor" |wc -l)`; do sha512sum /dev/zero & done
```
占用cpu
for i in `seq $(cat /proc/cpuinfo |grep "processor" |wc -l)`; do sha512sum /dev/zero & done
#查看cpu使用情况
top
#释放刚占用的cpu
kill -9 `ps -ef |grep sha512sum |awk '{print $2}'`





#占用内存，size大小自己按需调整
mkdir /tmp/memory
mount -t tmpfs -o size=1024M tmpfs /tmp/memory
dd if=/dev/zero of=/tmp/memory/block
#查看内存使用情况（该命令可以在占用前、占用后、释放后使用，作个对比）
free -m
#释放内存
rm /tmp/memory/block
umount /tmp/memory
rmdir /tmp/memory



## 配置案例

- `2024-08-11T22:50:46 [root@node3 /data/tmpdir/rds_deploy/rds-ansible/files/release] $ cat  /data/greatrds/etc/monitor/alertmanager.yml `
```yaml
global:
  resolve_timeout: 30s

route:
  group_by: ['alertname', 'instance']
  group_wait: 10s
  group_interval: 10s
  repeat_interval: 7200s
  receiver: 'api.web.hook'
receivers:
  - name: 'api.web.hook'
    webhook_configs:
      - url: 'http://172.17.134.77:9010/v1/rds/monitor/alert/new'
inhibit_rules:
  - source_match:
      severity: 'critical'
    target_match:
      severity: 'major'
    equal: ['metric_name', 'instance', 'member_uuid']
  - source_match:
      severity: 'major'
    target_match:
      severity: 'minor'
    equal: ['metric_name', 'instance', 'member_uuid']
  - source_match:
      severity: 'minor'
    target_match:
      severity: 'info'
    equal: ['metric_name', 'instance', 'member_uuid']

```


这段配置文件是用于配置 Alertmanager 的，它是 Prometheus 的组件之一，用于处理从 Prometheus 发送的警报（alerts）。该配置定义了警报的路由、接收者、以及抑制规则。以下是对这段配置的详细解析：

### 1. `global` 配置
```yaml
global:
  resolve_timeout: {{ alertmanager_resolve_timeout }}
```
- **`resolve_timeout`**: 这是一个全局配置项，用于定义警报在被解析后，Alertmanager 等待多长时间才将警报从活动状态中移除。这里的值是一个变量 `{{ alertmanager_resolve_timeout }}`，通常由外部配置或环境变量提供实际值。

### 2. `route` 路由配置
```yaml
route:
  group_by: ['alertname', 'instance']
  group_wait: 10s
  group_interval: 10s
  repeat_interval: 7200s
  receiver: 'api.web.hook'
```
- **`group_by`**: 定义了 Alertmanager 如何对警报进行分组。这里按照 `alertname`（警报名称）和 `instance`（实例）进行分组。分组后，同一组内的警报会被合并在一起发送。

- **`group_wait`**: 在接收到第一个警报后，Alertmanager 等待的时间（10秒）以便将可能的相同组内的警报一起发送。这有助于减少通知的数量。

- **`group_interval`**: 定义了在发送初始通知后，再次发送通知的时间间隔。这里设置为 10 秒。

- **`repeat_interval`**: 定义了重复发送相同警报的时间间隔。这里设置为 7200 秒（即 2 小时）。这意味着即使警报仍处于活跃状态，如果没有新警报触发，相同的通知也不会在 2 小时内重复发送。

- **`receiver`**: 指定了警报发送的目标接收者。这里指定为 `'api.web.hook'`，意味着所有符合条件的警报都会发送到该接收者。

### 3. `receivers` 接收者配置
```yaml
receivers:
  - name: 'api.web.hook'
    webhook_configs:
      - url: 'http://{{ api_address }}:{{ api_port }}/v1/rds/monitor/alert/new'
```
- **`name`**: 定义了接收者的名称，必须与 `route` 中的 `receiver` 名称匹配。

- **`webhook_configs`**: 定义了接收者的配置，这里是一个 Webhook 配置。

  - **`url`**: 这是接收警报的 Webhook 地址。这里使用了模板变量 `{{ api_address }}` 和 `{{ api_port }}`，最终会替换成实际的 API 地址和端口。这个 URL 指定了警报将发送到的外部服务的路径。

### 4. `inhibit_rules` 抑制规则配置
```yaml
inhibit_rules:
  - source_match:
      severity: 'critical'
    target_match:
      severity: 'major'
    equal: ['metric_name', 'instance', 'member_uuid']
  - source_match:
      severity: 'major'
    target_match:
      severity: 'minor'
    equal: ['metric_name', 'instance', 'member_uuid']
  - source_match:
      severity: 'minor'
    target_match:
      severity: 'info'
    equal: ['metric_name', 'instance', 'member_uuid']
```
- **抑制规则**：用于定义当存在更高严重级别的警报时，抑制发送较低严重级别的警报。

  - **`source_match`** 和 **`target_match`**: 定义了源警报和目标警报的严重性等级。例如，第一个规则表示，当存在 `severity` 为 `critical` 的警报时，会抑制发送 `severity` 为 `major` 的警报。

  - **`equal`**: 指定在进行抑制判断时，需要比较的标签字段。这些字段必须在源警报和目标警报之间完全相同，才能触发抑制规则。例如，如果 `metric_name`、`instance` 和 `member_uuid` 相同，那么符合条件的目标警报就会被抑制。两个警报必须具有一组相同的标签

### 总结
- **全局设置**定义了警报的解析超时时间。
- **路由配置**定义了警报的分组、等待时间、通知间隔和接收者。
- **接收者配置**定义了具体的 Webhook 地址，用于接收警报。
- **抑制规则**确保在存在更高严重性警报时，抑制低严重性警报的发送。

这个配置文件可以帮助 Alertmanager 更好地管理和路由警报，避免冗余的通知，同时确保重要的警报能够及时传递。




| 功能   | 说明                                            | 举例                                                                                          |
|:-----|:----------------------------------------------|:--------------------------------------------------------------------------------------------|
| 分组   | 将相似告警聚合作为单一通知发送                  | 将来自同一应用服务的不同实例的告警聚合为一组，然后以单一通知的形式发送                         |
| 去重   | 相同告警在一定时间内只会被通知一次，避免重复发送 | 如果一个服务的CPU使用率超过90%的告警在5分钟内多次触发，去重机制将确保在这5分钟内只发送一次告警 |
| 路由   | 决定告警通知目的地。将告警发送到不同的接收器     | 基于告警的严重程度，将严重告警发送到PagerDuty，而其他告警发送到Email                            |
| 通知   | 通知组件负责实际的告警通知发送，支持多种通讯渠道 | 配置模板化的邮件内容，包括告警详情和解决建议，发送给运维团队                                    |
| 抑制   | 防止告警风暴的机制                              | 当主数据库发生故障时，可配置抑制规则以避免对从数据库的告警通知，集中处理主数据库问题            |
| 接收器 | 接收来自Prometheus等告警，决定如何处理这些告警   | 配置Email接收器用于发送告警邮件，Slack接收器用于发送告警到指定的Slack频道                      |



```yaml
group_wait: 15m
group_interval: 5m
repeat_interval: 15m

```







## 案例分析


#### 1. prometheus

```yaml

global:
  scrape_interval:     10s
  evaluation_interval: 10s
alerting:
  alertmanagers:
  - static_configs:
    - targets:
      - '172.17.140.146:9093'
      - '172.17.134.71:9093'
    basic_auth:
      username: 'admin'
      password: '123456'

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
      - targets: ['172.17.140.146:9100']
        labels:
          __metrics_path__: '/rds_metrics'
      - targets: ['172.17.139.62:9100']
        labels:
          __metrics_path__: '/rds_metrics'
    honor_labels: true



```


###### （1）cluster-172.17.134.76-16310-98dee4db-v2.yaml
```yaml
- targets:
    - '172.17.140.146:9010'
  labels:
    __param_instance_type: cluster
    __param_instance_uuid: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
    __metrics_path__: /v1/rds/monitor/metrics
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
      {"instance":"98dee4db-70f6-4054-bdb2-dab8901c8cf2","member":"datasource","member_uuid":"16315fff-cf61-472e-be62-050f3bb62692"}
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
      {"instance":"98dee4db-70f6-4054-bdb2-dab8901c8cf2","member":"datasource","member_uuid":"16315fff-cf61-472e-be62-050f3bb62692"}
    __param_extra_params: >-
      {"datadir":
      "/data/mysqldata/16315fff-cf61-472e-be62-050f3bb62692/dbdata","err_log":
      "/data/mysqldata/16315fff-cf61-472e-be62-050f3bb62692/logfile/mysqld.err","general_log":
      "/data/mysqldata/16315fff-cf61-472e-be62-050f3bb62692/logfile/mysqld.log","slow_log":
      "/data/mysqldata/16315fff-cf61-472e-be62-050f3bb62692/logfile/mysqld-slow.log","pid_file":
      "/data/mysqldata/16315fff-cf61-472e-be62-050f3bb62692/pid/mysqld.pid"}
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
      {"instance":"98dee4db-70f6-4054-bdb2-dab8901c8cf2","member":"datasource","member_uuid":"16315fff-907f-4bed-8e5e-920df19d22df"}
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
      {"instance":"98dee4db-70f6-4054-bdb2-dab8901c8cf2","member":"datasource","member_uuid":"16315fff-907f-4bed-8e5e-920df19d22df"}
    __param_extra_params: >-
      {"datadir":
      "/data/mysqldata/16315fff-907f-4bed-8e5e-920df19d22df/dbdata","err_log":
      "/data/mysqldata/16315fff-907f-4bed-8e5e-920df19d22df/logfile/mysqld.err","general_log":
      "/data/mysqldata/16315fff-907f-4bed-8e5e-920df19d22df/logfile/mysqld.log","slow_log":
      "/data/mysqldata/16315fff-907f-4bed-8e5e-920df19d22df/logfile/mysqld-slow.log","pid_file":
      "/data/mysqldata/16315fff-907f-4bed-8e5e-920df19d22df/pid/mysqld.pid"}
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
      {"instance":"98dee4db-70f6-4054-bdb2-dab8901c8cf2","member":"datasource","member_uuid":"16315fff-6444-40bd-b8d5-3d57f06101d1"}
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
      {"instance":"98dee4db-70f6-4054-bdb2-dab8901c8cf2","member":"datasource","member_uuid":"16315fff-6444-40bd-b8d5-3d57f06101d1"}
    __param_extra_params: >-
      {"datadir":
      "/data/mysqldata/16315fff-6444-40bd-b8d5-3d57f06101d1/dbdata","err_log":
      "/data/mysqldata/16315fff-6444-40bd-b8d5-3d57f06101d1/logfile/mysqld.err","general_log":
      "/data/mysqldata/16315fff-6444-40bd-b8d5-3d57f06101d1/logfile/mysqld.log","slow_log":
      "/data/mysqldata/16315fff-6444-40bd-b8d5-3d57f06101d1/logfile/mysqld-slow.log","pid_file":
      "/data/mysqldata/16315fff-6444-40bd-b8d5-3d57f06101d1/pid/mysqld.pid"}
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
      {"instance":"98dee4db-70f6-4054-bdb2-dab8901c8cf2","member":"configuration","member_uuid":"16309fff-22ef-4f10-a1b5-258acca6ae76"}
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
      {"instance":"98dee4db-70f6-4054-bdb2-dab8901c8cf2","member":"configuration","member_uuid":"16309fff-22ef-4f10-a1b5-258acca6ae76"}
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
      {"instance":"98dee4db-70f6-4054-bdb2-dab8901c8cf2","member":"configuration","member_uuid":"16309fff-8c8c-4050-a212-71250a933f9e"}
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
      {"instance":"98dee4db-70f6-4054-bdb2-dab8901c8cf2","member":"configuration","member_uuid":"16309fff-8c8c-4050-a212-71250a933f9e"}
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
      {"instance":"98dee4db-70f6-4054-bdb2-dab8901c8cf2","member":"configuration","member_uuid":"16309fff-69ef-4fdc-89df-1e99faf40da0"}
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
      {"instance":"98dee4db-70f6-4054-bdb2-dab8901c8cf2","member":"configuration","member_uuid":"16309fff-69ef-4fdc-89df-1e99faf40da0"}
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
      {"instance":"98dee4db-70f6-4054-bdb2-dab8901c8cf2","member":"scheduler","member_uuid":"16310fff-ee8d-408f-95cf-f665954d9084"}
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
      {"instance":"98dee4db-70f6-4054-bdb2-dab8901c8cf2","member":"scheduler","member_uuid":"16310fff-ee8d-408f-95cf-f665954d9084"}
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
      {"instance":"98dee4db-70f6-4054-bdb2-dab8901c8cf2","member":"scheduler","member_uuid":"16310fff-0ed4-4a16-8970-3bf449ee5125"}
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
      {"instance":"98dee4db-70f6-4054-bdb2-dab8901c8cf2","member":"scheduler","member_uuid":"16310fff-0ed4-4a16-8970-3bf449ee5125"}
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
      {"instance":"98dee4db-70f6-4054-bdb2-dab8901c8cf2","member":"scheduler","member_uuid":"16310fff-2b64-4903-ab4c-eef8db39623b"}
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
      {"instance":"98dee4db-70f6-4054-bdb2-dab8901c8cf2","member":"scheduler","member_uuid":"16310fff-2b64-4903-ab4c-eef8db39623b"}

```

###### （2）node-172.17.134.71-7dfc2a2a-v2.yaml
```yaml
- targets:
    - '172.17.134.71:9000'
  labels:
    __metrics_path__: /metrics
    __param_type: node
    __param_address: 172.17.134.71
    __param_extra_params: '{"data_dir": "/data"}'
    __param_extra_labels: '{"instance":"7dfc2a2a-84e4-4558-9143-2c361ab749f5"}'

```

###### （3）node-172.17.134.76-77803a6a-v2.yaml
```yaml
- targets:
    - '172.17.134.76:9000'
  labels:
    __metrics_path__: /metrics
    __param_type: node
    __param_address: 172.17.134.76
    __param_extra_params: '{"data_dir": "/data"}'
    __param_extra_labels: '{"instance":"77803a6a-3e64-4139-9f2d-d8f1acd06a49"}'

```

###### （4）node-172.17.134.77-d1b95e02-v2.yaml
```yaml
- targets:
    - '172.17.134.77:9000'
  labels:
    __metrics_path__: /metrics
    __param_type: node
    __param_address: 172.17.134.77
    __param_extra_params: '{"data_dir": "/data"}'
    __param_extra_labels: '{"instance":"d1b95e02-eab0-4691-829d-5a636fbc9bcf"}'

```



若某个指标恰好在`2024-08-08T10:10:01`达到阈值，并持续10分钟

- `prometheus`在`2024-08-08T10:10:00`完成采集，此时没有采集并没有达到该指标的阈值，即恰好`prometheus`采集完成后，该指标实际就达到了阈值，那么在`prometheus`在`2024-08-08T10:20:00`才能采集到该指标达到了阈值
- 若`prometheus`在`2024-08-08T10:09:59`评估了该指标的规则，显然地，此时该指标还没有达到阈值，此时评估的是`2024-08-08T10:00:00`的采集数据
- 则`prometheus`在`2024-08-08T10:19:59`评估了该指标的规则，显然地，此时该指标还没有达到阈值，此时评估的是`2024-08-08T10:10:00`的采集数据
- 故`prometheus`在`2024-08-08T10:29:59`评估了该指标的规则，显然地，此时该指标达到阈值，此时评估的是`2024-08-08T10:20:00`的采集数据

故最大延迟告警时间为20秒，即当`scrape_interval=evaluation_interval`，那么告警最大延迟为2倍的`scrape_interval`值

又告警规则中for配置为10秒，故需要连续10秒触发告警，因此再加上10秒延迟，故要让第一个告警发出去，至少延迟10秒（10秒内处于pending状态），最大延迟30秒。


#### 2. alertmanager.yml

```yaml

global:
  resolve_timeout: 30s
inhibit_rules:
- equal:
  - metric_name
  - instance
  - member_uuid
  source_match:
    severity: critical
  target_match:
    severity: major
- equal:
  - metric_name
  - instance
  - member_uuid
  source_match:
    severity: major
  target_match:
    severity: minor
- equal:
  - metric_name
  - instance
  - member_uuid
  source_match:
    severity: minor
  target_match:
    severity: info
receivers:
- name: api.web.hook
  webhook_configs:
  - url: http://172.17.140.146:9010/v1/rds/monitor/alert/new
route:
  group_by:
  - alertname
  - instance
  group_interval: 10s
  group_wait: 10s
  receiver: api.web.hook
  repeat_interval: 7200s
  routes:
  - match:
      alertname: "normal\u6570\u636E\u8282\u70B9\u5F53\u524D\u884C\u9501\u7B49\u5F85\
        \u4E2A\u6570\u8F83\u591A"
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
    repeat_interval: 300s
  - match:
      alertname: "normal\u6570\u636E\u8282\u70B9\u5F53\u524D\u884C\u9501\u7B49\u5F85\
        \u4E2A\u6570\u8F83\u591A"
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
    repeat_interval: 300s
  - match:
      alertname: "normal\u4E3B\u4ECE\u5EF6\u8FDF\u8F83\u5927"
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
    repeat_interval: 300s
  - match:
      alertname: "normal\u6570\u636E\u8282\u70B9\u4ECE\u5E93IO\u7EBF\u7A0B\u72B6\u6001\
        \u5F02\u5E38"
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
    repeat_interval: 300s
  - match:
      alertname: "normal\u4ECE\u5E93SQL\u7EBF\u7A0B\u72B6\u6001\u5F02\u5E38"
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
    repeat_interval: 300s
  - match:
      alertname: "normal\u6570\u636E\u8282\u70B9\u65E0\u6CD5\u8FDE\u63A5"
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
    repeat_interval: 180s
  - match:
      alertname: "normal\u6570\u636E\u8282\u70B9\u6D3B\u52A8\u4F1A\u8BDD\u6570\u8F83\
        \u591A"
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
    repeat_interval: 300s
  - match:
      alertname: "normal\u6570\u636E\u8282\u70B9\u6162SQL\u4E2A\u6570\u8F83\u591A"
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
    repeat_interval: 300s
  - match:
      alertname: "normal\u6570\u636E\u8282\u70B9\u8868\u7F13\u5B58\u547D\u4E2D\u7387\
        \u8F83\u4F4E"
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
    repeat_interval: 300s
  - match:
      alertname: "normal\u6570\u636E\u8282\u70B9\u8D85\u8FC710s\u957F\u4E8B\u52A1\u4E2A\
        \u6570\u8F83\u591A"
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
    repeat_interval: 300s
  - match:
      alertname: "normal\u6570\u636E\u8282\u70B9\u8D85\u8FC760s\u957F\u4E8B\u52A1\u4E2A\
        \u6570\u8F83\u591A"
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
    repeat_interval: 300s
  - match:
      alertname: "normal\u6570\u636E\u8282\u70B9\u8D85\u8FC7180s\u957F\u4E8B\u52A1\
        \u4E2A\u6570\u8F83\u591A"
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
    repeat_interval: 300s
  - match:
      alertname: "normal\u6570\u636E\u8282\u70B9\u8D85\u8FC7600s\u957F\u4E8B\u52A1\
        \u4E2A\u6570\u8F83\u591A"
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
    repeat_interval: 300s
  - match:
      alertname: "\u96C6\u7FA4\u8FDE\u63A5\u6570\u8FC7\u591A"
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
    repeat_interval: 300s
  - match:
      alertname: "\u96C6\u7FA4\u8FDE\u63A5\u6570\u8FC7\u591A"
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
    repeat_interval: 300s
  - match:
      alertname: "\u96C6\u7FA4\u6D3B\u52A8\u4F1A\u8BDD\u6570\u8FC7\u591A"
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
    repeat_interval: 300s
  - match:
      alertname: "zookeeper\u72B6\u6001\u5F02\u5E38"
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
    repeat_interval: 180s
  - match:
      alertname: "\u8BA1\u7B97\u8282\u70B9Replication\u6570\u636E\u6E90\u72B6\u6001\
        \u5F02\u5E38"
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
    repeat_interval: 300s
  - match:
      alertname: "\u8BA1\u7B97\u8282\u70B9DataServer\u5B58\u5728Down\u6216\u8005Error\u72B6\
        \u6001"
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
    repeat_interval: 300s
  - match:
      alertname: "\u8BA1\u7B97\u8282\u70B9DataServer\u5B58\u5728Catching Master\u72B6\
        \u6001"
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
    repeat_interval: 300s
  - match:
      alertname: "\u8BA1\u7B97\u8282\u70B9\u6162SQL\u4E2A\u6570\u8F83\u591A"
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
    repeat_interval: 300s
  - match:
      alertname: "\u8BA1\u7B97\u8282\u70B9\u63A2\u6D4B\u5230\u6570\u636E\u8282\u70B9\
        \u53D1\u751F\u4E3B\u4ECE\u5207\u6362"
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
    repeat_interval: 300s
  - match:
      alertname: "\u8BA1\u7B97\u8282\u70B9\u63A2\u6D4B\u5230\u6570\u636E\u8282\u70B9\
        \u4E3B\u4ECE\u540C\u6B65\u72B6\u6001\u65AD\u5F00"
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
    repeat_interval: 300s
  - match:
      alertname: "\u8BA1\u7B97\u8282\u70B9\u8FDE\u63A5\u6C60\u8FBE\u5230\u6700\u5927\
        \u6C34\u4F4D"
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
    repeat_interval: 300s
  - match:
      alertname: "\u8BA1\u7B97\u8282\u70B9\u4FDD\u5B58\u914D\u7F6E\u5230Zookeeper\u5931\
        \u8D25"
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
    repeat_interval: 300s
  - match:
      alertname: "\u8BA1\u7B97\u8282\u70B9\u8FDE\u63A5\u6C60\u4F7F\u7528\u7387\u8F83\
        \u9AD8"
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
    repeat_interval: 300s
  - match:
      alertname: "\u8BA1\u7B97\u8282\u70B9\u65E0\u6CD5\u8FDE\u63A5"
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
    repeat_interval: 180s


```




#### 3. rules

`2024-08-15T13:35:28 [root@node172.17.140.146 /data/greatrds/etc/monitor/rules] $ cat 98dee4db-70f6-4054-bdb2-dab8901c8cf2 `
```yaml
groups:
- name: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
  rules:
  - alert: "normal\u6570\u636E\u8282\u70B9\u5F53\u524D\u884C\u9501\u7B49\u5F85\u4E2A\
      \u6570\u8F83\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (mysql_global_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2", member_uuid=~"16315fff-cf61-472e-be62-050f3bb62692",
      status="Innodb_row_lock_current_waits"}) >= 20.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '20.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16315fff-cf61-472e-be62-050f3bb62692
      metric_name: greatdb_partition_innodb_row_lock_current_waits
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: dbf3b6cc-9430-4bd5-baa1-689fc1bc7051
      severity: critical
      type: cluster4
  - alert: "normal\u6570\u636E\u8282\u70B9\u5F53\u524D\u884C\u9501\u7B49\u5F85\u4E2A\
      \u6570\u8F83\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (mysql_global_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2", member_uuid=~"16315fff-907f-4bed-8e5e-920df19d22df",
      status="Innodb_row_lock_current_waits"}) >= 20.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '20.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16315fff-907f-4bed-8e5e-920df19d22df
      metric_name: greatdb_partition_innodb_row_lock_current_waits
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: dbf3b6cc-9430-4bd5-baa1-689fc1bc7051
      severity: critical
      type: cluster4
  - alert: "normal\u6570\u636E\u8282\u70B9\u5F53\u524D\u884C\u9501\u7B49\u5F85\u4E2A\
      \u6570\u8F83\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (mysql_global_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2", member_uuid=~"16315fff-6444-40bd-b8d5-3d57f06101d1",
      status="Innodb_row_lock_current_waits"}) >= 20.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '20.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16315fff-6444-40bd-b8d5-3d57f06101d1
      metric_name: greatdb_partition_innodb_row_lock_current_waits
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: dbf3b6cc-9430-4bd5-baa1-689fc1bc7051
      severity: critical
      type: cluster4
  - alert: "normal\u6570\u636E\u8282\u70B9\u5F53\u524D\u884C\u9501\u7B49\u5F85\u4E2A\
      \u6570\u8F83\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (mysql_global_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2", member_uuid=~"16315fff-cf61-472e-be62-050f3bb62692",
      status="Innodb_row_lock_current_waits"}) >= 5.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '5.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16315fff-cf61-472e-be62-050f3bb62692
      metric_name: greatdb_partition_innodb_row_lock_current_waits
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 60ae326e-1707-4f9d-80f7-3a273d97d9df
      severity: major
      type: cluster4
  - alert: "normal\u6570\u636E\u8282\u70B9\u5F53\u524D\u884C\u9501\u7B49\u5F85\u4E2A\
      \u6570\u8F83\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (mysql_global_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2", member_uuid=~"16315fff-907f-4bed-8e5e-920df19d22df",
      status="Innodb_row_lock_current_waits"}) >= 5.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '5.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16315fff-907f-4bed-8e5e-920df19d22df
      metric_name: greatdb_partition_innodb_row_lock_current_waits
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 60ae326e-1707-4f9d-80f7-3a273d97d9df
      severity: major
      type: cluster4
  - alert: "normal\u6570\u636E\u8282\u70B9\u5F53\u524D\u884C\u9501\u7B49\u5F85\u4E2A\
      \u6570\u8F83\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (mysql_global_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2", member_uuid=~"16315fff-6444-40bd-b8d5-3d57f06101d1",
      status="Innodb_row_lock_current_waits"}) >= 5.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '5.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16315fff-6444-40bd-b8d5-3d57f06101d1
      metric_name: greatdb_partition_innodb_row_lock_current_waits
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 60ae326e-1707-4f9d-80f7-3a273d97d9df
      severity: major
      type: cluster4
  - alert: "normal\u4E3B\u4ECE\u5EF6\u8FDF\u8F83\u5927"
    annotations:
      t_value: '{{ $value }}'
    expr: (mysql_slave_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2", member_uuid=~"16315fff-cf61-472e-be62-050f3bb62692",
      status="seconds_behind_master"}) >= 300.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '300.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16315fff-cf61-472e-be62-050f3bb62692
      metric_name: greatdb_partition_seconds_behind_master
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 79fcad85-1fdc-4b73-ae70-bcf769968786
      severity: major
      type: cluster4
  - alert: "normal\u4E3B\u4ECE\u5EF6\u8FDF\u8F83\u5927"
    annotations:
      t_value: '{{ $value }}'
    expr: (mysql_slave_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2", member_uuid=~"16315fff-907f-4bed-8e5e-920df19d22df",
      status="seconds_behind_master"}) >= 300.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '300.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16315fff-907f-4bed-8e5e-920df19d22df
      metric_name: greatdb_partition_seconds_behind_master
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 79fcad85-1fdc-4b73-ae70-bcf769968786
      severity: major
      type: cluster4
  - alert: "normal\u4E3B\u4ECE\u5EF6\u8FDF\u8F83\u5927"
    annotations:
      t_value: '{{ $value }}'
    expr: (mysql_slave_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2", member_uuid=~"16315fff-6444-40bd-b8d5-3d57f06101d1",
      status="seconds_behind_master"}) >= 300.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '300.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16315fff-6444-40bd-b8d5-3d57f06101d1
      metric_name: greatdb_partition_seconds_behind_master
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 79fcad85-1fdc-4b73-ae70-bcf769968786
      severity: major
      type: cluster4
  - alert: "normal\u6570\u636E\u8282\u70B9\u4ECE\u5E93IO\u7EBF\u7A0B\u72B6\u6001\u5F02\
      \u5E38"
    annotations:
      t_value: '{{ $value }}'
    expr: (mysql_slave_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2", member_uuid=~"16315fff-cf61-472e-be62-050f3bb62692",
      status="Slave_IO_Running"}) == 0.0
    for: 10s
    labels:
      expr: ==
      expr_value: '0.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16315fff-cf61-472e-be62-050f3bb62692
      metric_name: greatdb_partition_slave_io_running
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 3307c000-66b7-4292-bae7-f83aad926e8d
      severity: major
      type: cluster4
  - alert: "normal\u6570\u636E\u8282\u70B9\u4ECE\u5E93IO\u7EBF\u7A0B\u72B6\u6001\u5F02\
      \u5E38"
    annotations:
      t_value: '{{ $value }}'
    expr: (mysql_slave_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2", member_uuid=~"16315fff-907f-4bed-8e5e-920df19d22df",
      status="Slave_IO_Running"}) == 0.0
    for: 10s
    labels:
      expr: ==
      expr_value: '0.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16315fff-907f-4bed-8e5e-920df19d22df
      metric_name: greatdb_partition_slave_io_running
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 3307c000-66b7-4292-bae7-f83aad926e8d
      severity: major
      type: cluster4
  - alert: "normal\u6570\u636E\u8282\u70B9\u4ECE\u5E93IO\u7EBF\u7A0B\u72B6\u6001\u5F02\
      \u5E38"
    annotations:
      t_value: '{{ $value }}'
    expr: (mysql_slave_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2", member_uuid=~"16315fff-6444-40bd-b8d5-3d57f06101d1",
      status="Slave_IO_Running"}) == 0.0
    for: 10s
    labels:
      expr: ==
      expr_value: '0.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16315fff-6444-40bd-b8d5-3d57f06101d1
      metric_name: greatdb_partition_slave_io_running
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 3307c000-66b7-4292-bae7-f83aad926e8d
      severity: major
      type: cluster4
  - alert: "normal\u4ECE\u5E93SQL\u7EBF\u7A0B\u72B6\u6001\u5F02\u5E38"
    annotations:
      t_value: '{{ $value }}'
    expr: (mysql_slave_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2", member_uuid=~"16315fff-cf61-472e-be62-050f3bb62692",
      status="Slave_SQL_Running"}) == 0.0
    for: 10s
    labels:
      expr: ==
      expr_value: '0.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16315fff-cf61-472e-be62-050f3bb62692
      metric_name: greatdb_partition_slave_sql_Running
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: e9763ebf-9e8a-43ef-a386-9554c93d0a44
      severity: major
      type: cluster4
  - alert: "normal\u4ECE\u5E93SQL\u7EBF\u7A0B\u72B6\u6001\u5F02\u5E38"
    annotations:
      t_value: '{{ $value }}'
    expr: (mysql_slave_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2", member_uuid=~"16315fff-907f-4bed-8e5e-920df19d22df",
      status="Slave_SQL_Running"}) == 0.0
    for: 10s
    labels:
      expr: ==
      expr_value: '0.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16315fff-907f-4bed-8e5e-920df19d22df
      metric_name: greatdb_partition_slave_sql_Running
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: e9763ebf-9e8a-43ef-a386-9554c93d0a44
      severity: major
      type: cluster4
  - alert: "normal\u4ECE\u5E93SQL\u7EBF\u7A0B\u72B6\u6001\u5F02\u5E38"
    annotations:
      t_value: '{{ $value }}'
    expr: (mysql_slave_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2", member_uuid=~"16315fff-6444-40bd-b8d5-3d57f06101d1",
      status="Slave_SQL_Running"}) == 0.0
    for: 10s
    labels:
      expr: ==
      expr_value: '0.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16315fff-6444-40bd-b8d5-3d57f06101d1
      metric_name: greatdb_partition_slave_sql_Running
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: e9763ebf-9e8a-43ef-a386-9554c93d0a44
      severity: major
      type: cluster4
  - alert: "normal\u6570\u636E\u8282\u70B9\u65E0\u6CD5\u8FDE\u63A5"
    annotations:
      t_value: '{{ $value }}'
    expr: (up{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2", member_uuid=~"16315fff-cf61-472e-be62-050f3bb62692"})
      == 0.0
    for: 10s
    labels:
      expr: ==
      expr_value: '0.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16315fff-cf61-472e-be62-050f3bb62692
      metric_name: greatdb_partition_up
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '180'
      rule_uuid: eeed6b35-3464-4aeb-a8f2-77227d4f2a07
      severity: critical
      type: cluster4
  - alert: "normal\u6570\u636E\u8282\u70B9\u65E0\u6CD5\u8FDE\u63A5"
    annotations:
      t_value: '{{ $value }}'
    expr: (up{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2", member_uuid=~"16315fff-907f-4bed-8e5e-920df19d22df"})
      == 0.0
    for: 10s
    labels:
      expr: ==
      expr_value: '0.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16315fff-907f-4bed-8e5e-920df19d22df
      metric_name: greatdb_partition_up
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '180'
      rule_uuid: eeed6b35-3464-4aeb-a8f2-77227d4f2a07
      severity: critical
      type: cluster4
  - alert: "normal\u6570\u636E\u8282\u70B9\u65E0\u6CD5\u8FDE\u63A5"
    annotations:
      t_value: '{{ $value }}'
    expr: (up{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2", member_uuid=~"16315fff-6444-40bd-b8d5-3d57f06101d1"})
      == 0.0
    for: 10s
    labels:
      expr: ==
      expr_value: '0.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16315fff-6444-40bd-b8d5-3d57f06101d1
      metric_name: greatdb_partition_up
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '180'
      rule_uuid: eeed6b35-3464-4aeb-a8f2-77227d4f2a07
      severity: critical
      type: cluster4
  - alert: "normal\u6570\u636E\u8282\u70B9\u6D3B\u52A8\u4F1A\u8BDD\u6570\u8F83\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (mysql_global_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2", member_uuid=~"16315fff-cf61-472e-be62-050f3bb62692",
      status="Threads_running"}) >= 10.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '10.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16315fff-cf61-472e-be62-050f3bb62692
      metric_name: greatdb_partition_threads_running
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 3d1c2c5c-1cd2-4164-82fb-69ac396e98d2
      severity: major
      type: cluster4
  - alert: "normal\u6570\u636E\u8282\u70B9\u6D3B\u52A8\u4F1A\u8BDD\u6570\u8F83\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (mysql_global_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2", member_uuid=~"16315fff-907f-4bed-8e5e-920df19d22df",
      status="Threads_running"}) >= 10.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '10.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16315fff-907f-4bed-8e5e-920df19d22df
      metric_name: greatdb_partition_threads_running
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 3d1c2c5c-1cd2-4164-82fb-69ac396e98d2
      severity: major
      type: cluster4
  - alert: "normal\u6570\u636E\u8282\u70B9\u6D3B\u52A8\u4F1A\u8BDD\u6570\u8F83\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (mysql_global_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2", member_uuid=~"16315fff-6444-40bd-b8d5-3d57f06101d1",
      status="Threads_running"}) >= 10.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '10.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16315fff-6444-40bd-b8d5-3d57f06101d1
      metric_name: greatdb_partition_threads_running
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 3d1c2c5c-1cd2-4164-82fb-69ac396e98d2
      severity: major
      type: cluster4
  - alert: "normal\u6570\u636E\u8282\u70B9\u6162SQL\u4E2A\u6570\u8F83\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (irate(mysql_global_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      member_uuid=~"16315fff-cf61-472e-be62-050f3bb62692", status="Slow_queries"}[30s]))
      >= 20.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '20.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16315fff-cf61-472e-be62-050f3bb62692
      metric_name: greatdb_partition_slow_queries
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 1544535a-e43b-425b-bd98-c46d752a5313
      severity: major
      type: cluster4
  - alert: "normal\u6570\u636E\u8282\u70B9\u6162SQL\u4E2A\u6570\u8F83\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (irate(mysql_global_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      member_uuid=~"16315fff-907f-4bed-8e5e-920df19d22df", status="Slow_queries"}[30s]))
      >= 20.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '20.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16315fff-907f-4bed-8e5e-920df19d22df
      metric_name: greatdb_partition_slow_queries
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 1544535a-e43b-425b-bd98-c46d752a5313
      severity: major
      type: cluster4
  - alert: "normal\u6570\u636E\u8282\u70B9\u6162SQL\u4E2A\u6570\u8F83\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (irate(mysql_global_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      member_uuid=~"16315fff-6444-40bd-b8d5-3d57f06101d1", status="Slow_queries"}[30s]))
      >= 20.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '20.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16315fff-6444-40bd-b8d5-3d57f06101d1
      metric_name: greatdb_partition_slow_queries
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 1544535a-e43b-425b-bd98-c46d752a5313
      severity: major
      type: cluster4
  - alert: "normal\u6570\u636E\u8282\u70B9\u8868\u7F13\u5B58\u547D\u4E2D\u7387\u8F83\
      \u4F4E"
    annotations:
      t_value: '{{ $value }}'
    expr: (sum by (instance, address, port,member_uuid) (mysql_global_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      member_uuid=~"16315fff-cf61-472e-be62-050f3bb62692", status="Table_open_cache_hits"})/sum
      by (instance, address, port,member_uuid)(mysql_global_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      member_uuid=~"16315fff-cf61-472e-be62-050f3bb62692", status=~"Table_open_cache_hits|Table_open_cache_misses"})*100)
      <= 85.0
    for: 10s
    labels:
      expr: <=
      expr_value: '85.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16315fff-cf61-472e-be62-050f3bb62692
      metric_name: greatdb_partition_table_cache_hit
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 57bcbd71-8ca2-4087-8418-2d64bf1b3acd
      severity: major
      type: cluster4
  - alert: "normal\u6570\u636E\u8282\u70B9\u8868\u7F13\u5B58\u547D\u4E2D\u7387\u8F83\
      \u4F4E"
    annotations:
      t_value: '{{ $value }}'
    expr: (sum by (instance, address, port,member_uuid) (mysql_global_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      member_uuid=~"16315fff-907f-4bed-8e5e-920df19d22df", status="Table_open_cache_hits"})/sum
      by (instance, address, port,member_uuid)(mysql_global_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      member_uuid=~"16315fff-907f-4bed-8e5e-920df19d22df", status=~"Table_open_cache_hits|Table_open_cache_misses"})*100)
      <= 85.0
    for: 10s
    labels:
      expr: <=
      expr_value: '85.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16315fff-907f-4bed-8e5e-920df19d22df
      metric_name: greatdb_partition_table_cache_hit
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 57bcbd71-8ca2-4087-8418-2d64bf1b3acd
      severity: major
      type: cluster4
  - alert: "normal\u6570\u636E\u8282\u70B9\u8868\u7F13\u5B58\u547D\u4E2D\u7387\u8F83\
      \u4F4E"
    annotations:
      t_value: '{{ $value }}'
    expr: (sum by (instance, address, port,member_uuid) (mysql_global_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      member_uuid=~"16315fff-6444-40bd-b8d5-3d57f06101d1", status="Table_open_cache_hits"})/sum
      by (instance, address, port,member_uuid)(mysql_global_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      member_uuid=~"16315fff-6444-40bd-b8d5-3d57f06101d1", status=~"Table_open_cache_hits|Table_open_cache_misses"})*100)
      <= 85.0
    for: 10s
    labels:
      expr: <=
      expr_value: '85.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16315fff-6444-40bd-b8d5-3d57f06101d1
      metric_name: greatdb_partition_table_cache_hit
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 57bcbd71-8ca2-4087-8418-2d64bf1b3acd
      severity: major
      type: cluster4
  - alert: "normal\u6570\u636E\u8282\u70B9\u8D85\u8FC710s\u957F\u4E8B\u52A1\u4E2A\u6570\
      \u8F83\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (mysql_long_transaction{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      member_uuid=~"16315fff-cf61-472e-be62-050f3bb62692", time="10"}) >= 10.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '10.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port,time
      member_uuid: 16315fff-cf61-472e-be62-050f3bb62692
      metric_name: greatdb_partition_long_transaction_10
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 3d2e714c-c5ae-4e1b-9e62-34a0ed47e9aa
      severity: major
      type: cluster4
  - alert: "normal\u6570\u636E\u8282\u70B9\u8D85\u8FC710s\u957F\u4E8B\u52A1\u4E2A\u6570\
      \u8F83\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (mysql_long_transaction{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      member_uuid=~"16315fff-907f-4bed-8e5e-920df19d22df", time="10"}) >= 10.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '10.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port,time
      member_uuid: 16315fff-907f-4bed-8e5e-920df19d22df
      metric_name: greatdb_partition_long_transaction_10
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 3d2e714c-c5ae-4e1b-9e62-34a0ed47e9aa
      severity: major
      type: cluster4
  - alert: "normal\u6570\u636E\u8282\u70B9\u8D85\u8FC710s\u957F\u4E8B\u52A1\u4E2A\u6570\
      \u8F83\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (mysql_long_transaction{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      member_uuid=~"16315fff-6444-40bd-b8d5-3d57f06101d1", time="10"}) >= 10.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '10.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port,time
      member_uuid: 16315fff-6444-40bd-b8d5-3d57f06101d1
      metric_name: greatdb_partition_long_transaction_10
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 3d2e714c-c5ae-4e1b-9e62-34a0ed47e9aa
      severity: major
      type: cluster4
  - alert: "normal\u6570\u636E\u8282\u70B9\u8D85\u8FC760s\u957F\u4E8B\u52A1\u4E2A\u6570\
      \u8F83\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (mysql_long_transaction{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      member_uuid=~"16315fff-cf61-472e-be62-050f3bb62692", time="60"}) >= 10.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '10.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port,time
      member_uuid: 16315fff-cf61-472e-be62-050f3bb62692
      metric_name: greatdb_partition_long_transaction_60
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: b0779427-39c6-4338-b696-4baf7d2a55b2
      severity: major
      type: cluster4
  - alert: "normal\u6570\u636E\u8282\u70B9\u8D85\u8FC760s\u957F\u4E8B\u52A1\u4E2A\u6570\
      \u8F83\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (mysql_long_transaction{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      member_uuid=~"16315fff-907f-4bed-8e5e-920df19d22df", time="60"}) >= 10.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '10.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port,time
      member_uuid: 16315fff-907f-4bed-8e5e-920df19d22df
      metric_name: greatdb_partition_long_transaction_60
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: b0779427-39c6-4338-b696-4baf7d2a55b2
      severity: major
      type: cluster4
  - alert: "normal\u6570\u636E\u8282\u70B9\u8D85\u8FC760s\u957F\u4E8B\u52A1\u4E2A\u6570\
      \u8F83\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (mysql_long_transaction{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      member_uuid=~"16315fff-6444-40bd-b8d5-3d57f06101d1", time="60"}) >= 10.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '10.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port,time
      member_uuid: 16315fff-6444-40bd-b8d5-3d57f06101d1
      metric_name: greatdb_partition_long_transaction_60
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: b0779427-39c6-4338-b696-4baf7d2a55b2
      severity: major
      type: cluster4
  - alert: "normal\u6570\u636E\u8282\u70B9\u8D85\u8FC7180s\u957F\u4E8B\u52A1\u4E2A\
      \u6570\u8F83\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (mysql_long_transaction{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      member_uuid=~"16315fff-cf61-472e-be62-050f3bb62692", time="180"}) >= 10.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '10.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port,time
      member_uuid: 16315fff-cf61-472e-be62-050f3bb62692
      metric_name: greatdb_partition_long_transaction_180
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 82ce2af1-37be-4543-bf7e-c632e9ca646c
      severity: major
      type: cluster4
  - alert: "normal\u6570\u636E\u8282\u70B9\u8D85\u8FC7180s\u957F\u4E8B\u52A1\u4E2A\
      \u6570\u8F83\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (mysql_long_transaction{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      member_uuid=~"16315fff-907f-4bed-8e5e-920df19d22df", time="180"}) >= 10.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '10.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port,time
      member_uuid: 16315fff-907f-4bed-8e5e-920df19d22df
      metric_name: greatdb_partition_long_transaction_180
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 82ce2af1-37be-4543-bf7e-c632e9ca646c
      severity: major
      type: cluster4
  - alert: "normal\u6570\u636E\u8282\u70B9\u8D85\u8FC7180s\u957F\u4E8B\u52A1\u4E2A\
      \u6570\u8F83\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (mysql_long_transaction{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      member_uuid=~"16315fff-6444-40bd-b8d5-3d57f06101d1", time="180"}) >= 10.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '10.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port,time
      member_uuid: 16315fff-6444-40bd-b8d5-3d57f06101d1
      metric_name: greatdb_partition_long_transaction_180
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 82ce2af1-37be-4543-bf7e-c632e9ca646c
      severity: major
      type: cluster4
  - alert: "normal\u6570\u636E\u8282\u70B9\u8D85\u8FC7600s\u957F\u4E8B\u52A1\u4E2A\
      \u6570\u8F83\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (mysql_long_transaction{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      member_uuid=~"16315fff-cf61-472e-be62-050f3bb62692", time="600"}) >= 10.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '10.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port,time
      member_uuid: 16315fff-cf61-472e-be62-050f3bb62692
      metric_name: greatdb_partition_long_transaction_600
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 9ca3641f-10b2-46de-a510-912a57514009
      severity: major
      type: cluster4
  - alert: "normal\u6570\u636E\u8282\u70B9\u8D85\u8FC7600s\u957F\u4E8B\u52A1\u4E2A\
      \u6570\u8F83\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (mysql_long_transaction{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      member_uuid=~"16315fff-907f-4bed-8e5e-920df19d22df", time="600"}) >= 10.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '10.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port,time
      member_uuid: 16315fff-907f-4bed-8e5e-920df19d22df
      metric_name: greatdb_partition_long_transaction_600
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 9ca3641f-10b2-46de-a510-912a57514009
      severity: major
      type: cluster4
  - alert: "normal\u6570\u636E\u8282\u70B9\u8D85\u8FC7600s\u957F\u4E8B\u52A1\u4E2A\
      \u6570\u8F83\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (mysql_long_transaction{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      member_uuid=~"16315fff-6444-40bd-b8d5-3d57f06101d1", time="600"}) >= 10.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '10.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port,time
      member_uuid: 16315fff-6444-40bd-b8d5-3d57f06101d1
      metric_name: greatdb_partition_long_transaction_600
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 9ca3641f-10b2-46de-a510-912a57514009
      severity: major
      type: cluster4
  - alert: "\u96C6\u7FA4\u8FDE\u63A5\u6570\u8FC7\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (dbscale_client_connection_rate{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      member_uuid=~"16310fff-ee8d-408f-95cf-f665954d9084"}) >= 85.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '85.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16310fff-ee8d-408f-95cf-f665954d9084
      metric_name: greatdb_dbscale_client_connection_rate
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 97e71724-fe73-4d5f-8b1d-e5d7523bad0e
      severity: major
      type: cluster4
  - alert: "\u96C6\u7FA4\u8FDE\u63A5\u6570\u8FC7\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (dbscale_client_connection_rate{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      member_uuid=~"16310fff-0ed4-4a16-8970-3bf449ee5125"}) >= 85.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '85.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16310fff-0ed4-4a16-8970-3bf449ee5125
      metric_name: greatdb_dbscale_client_connection_rate
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 97e71724-fe73-4d5f-8b1d-e5d7523bad0e
      severity: major
      type: cluster4
  - alert: "\u96C6\u7FA4\u8FDE\u63A5\u6570\u8FC7\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (dbscale_client_connection_rate{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      member_uuid=~"16310fff-2b64-4903-ab4c-eef8db39623b"}) >= 85.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '85.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16310fff-2b64-4903-ab4c-eef8db39623b
      metric_name: greatdb_dbscale_client_connection_rate
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 97e71724-fe73-4d5f-8b1d-e5d7523bad0e
      severity: major
      type: cluster4
  - alert: "\u96C6\u7FA4\u8FDE\u63A5\u6570\u8FC7\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (dbscale_client_connection_rate{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      member_uuid=~"16310fff-ee8d-408f-95cf-f665954d9084"}) >= 95.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '95.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16310fff-ee8d-408f-95cf-f665954d9084
      metric_name: greatdb_dbscale_client_connection_rate
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 36addb82-8e30-4c2b-962a-3cf42155066f
      severity: critical
      type: cluster4
  - alert: "\u96C6\u7FA4\u8FDE\u63A5\u6570\u8FC7\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (dbscale_client_connection_rate{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      member_uuid=~"16310fff-0ed4-4a16-8970-3bf449ee5125"}) >= 95.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '95.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16310fff-0ed4-4a16-8970-3bf449ee5125
      metric_name: greatdb_dbscale_client_connection_rate
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 36addb82-8e30-4c2b-962a-3cf42155066f
      severity: critical
      type: cluster4
  - alert: "\u96C6\u7FA4\u8FDE\u63A5\u6570\u8FC7\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (dbscale_client_connection_rate{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      member_uuid=~"16310fff-2b64-4903-ab4c-eef8db39623b"}) >= 95.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '95.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16310fff-2b64-4903-ab4c-eef8db39623b
      metric_name: greatdb_dbscale_client_connection_rate
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 36addb82-8e30-4c2b-962a-3cf42155066f
      severity: critical
      type: cluster4
  - alert: "\u96C6\u7FA4\u6D3B\u52A8\u4F1A\u8BDD\u6570\u8FC7\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (dbscale_running_user_count{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2"})
      >= 20.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '20.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      member_uuid: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      metric_name: greatdb_self_running_user_count
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 32260064-efc6-491b-bd58-e5e175b084df
      severity: major
      type: cluster4
  - alert: "zookeeper\u72B6\u6001\u5F02\u5E38"
    annotations:
      t_value: '{{ $value }}'
    expr: (up{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2", member_uuid=~"16309fff-22ef-4f10-a1b5-258acca6ae76"})
      == 0.0
    for: 10s
    labels:
      expr: ==
      expr_value: '0.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16309fff-22ef-4f10-a1b5-258acca6ae76
      metric_name: greatdb_zookeeper_up
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '180'
      rule_uuid: 3654a838-c11e-42d4-9cdd-c97b753e0a68
      severity: critical
      type: cluster4
  - alert: "zookeeper\u72B6\u6001\u5F02\u5E38"
    annotations:
      t_value: '{{ $value }}'
    expr: (up{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2", member_uuid=~"16309fff-8c8c-4050-a212-71250a933f9e"})
      == 0.0
    for: 10s
    labels:
      expr: ==
      expr_value: '0.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16309fff-8c8c-4050-a212-71250a933f9e
      metric_name: greatdb_zookeeper_up
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '180'
      rule_uuid: 3654a838-c11e-42d4-9cdd-c97b753e0a68
      severity: critical
      type: cluster4
  - alert: "zookeeper\u72B6\u6001\u5F02\u5E38"
    annotations:
      t_value: '{{ $value }}'
    expr: (up{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2", member_uuid=~"16309fff-69ef-4fdc-89df-1e99faf40da0"})
      == 0.0
    for: 10s
    labels:
      expr: ==
      expr_value: '0.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16309fff-69ef-4fdc-89df-1e99faf40da0
      metric_name: greatdb_zookeeper_up
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '180'
      rule_uuid: 3654a838-c11e-42d4-9cdd-c97b753e0a68
      severity: critical
      type: cluster4
  - alert: "\u8BA1\u7B97\u8282\u70B9Replication\u6570\u636E\u6E90\u72B6\u6001\u5F02\
      \u5E38"
    annotations:
      t_value: '{{ $value }}'
    expr: (dbscale_datasource_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      field="exception"}) > 0.0
    for: 10s
    labels:
      expr: '>'
      expr_value: '0.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      member_uuid: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      metric_name: greatdb_self_datasource_exception
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: a022b3fd-1b67-427b-9f84-8d9a1edb7e06
      severity: critical
      type: cluster4
  - alert: "\u8BA1\u7B97\u8282\u70B9DataServer\u5B58\u5728Down\u6216\u8005Error\u72B6\
      \u6001"
    annotations:
      t_value: '{{ $value }}'
    expr: (dbscale_dataserver_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      field="down_error"}) > 0.0
    for: 10s
    labels:
      expr: '>'
      expr_value: '0.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      member_uuid: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      metric_name: greatdb_self_dataserver_down_error
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 33e0c207-338b-44f5-b0b0-ebae50d28674
      severity: critical
      type: cluster4
  - alert: "\u8BA1\u7B97\u8282\u70B9DataServer\u5B58\u5728Catching Master\u72B6\u6001"
    annotations:
      t_value: '{{ $value }}'
    expr: (dbscale_dataserver_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      field="catching"}) > 0.0
    for: 10s
    labels:
      expr: '>'
      expr_value: '0.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      member_uuid: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      metric_name: greatdb_self_dataserver_catching
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 6b3a2d66-f703-40ac-ada2-8b3b42e3ae72
      severity: major
      type: cluster4
  - alert: "\u8BA1\u7B97\u8282\u70B9\u6162SQL\u4E2A\u6570\u8F83\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (irate(dbscale_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",  member_uuid=~"16310fff-ee8d-408f-95cf-f665954d9084",
      status="TimesStmtRecordAsSlowQueryByDBscale"}[30s])) >= 20.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '20.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16310fff-ee8d-408f-95cf-f665954d9084
      metric_name: greatdb_dbscale_slow_queries
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 53be621f-8105-4386-8ecb-9cea7b8005a9
      severity: major
      type: cluster4
  - alert: "\u8BA1\u7B97\u8282\u70B9\u6162SQL\u4E2A\u6570\u8F83\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (irate(dbscale_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",  member_uuid=~"16310fff-0ed4-4a16-8970-3bf449ee5125",
      status="TimesStmtRecordAsSlowQueryByDBscale"}[30s])) >= 20.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '20.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16310fff-0ed4-4a16-8970-3bf449ee5125
      metric_name: greatdb_dbscale_slow_queries
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 53be621f-8105-4386-8ecb-9cea7b8005a9
      severity: major
      type: cluster4
  - alert: "\u8BA1\u7B97\u8282\u70B9\u6162SQL\u4E2A\u6570\u8F83\u591A"
    annotations:
      t_value: '{{ $value }}'
    expr: (irate(dbscale_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",  member_uuid=~"16310fff-2b64-4903-ab4c-eef8db39623b",
      status="TimesStmtRecordAsSlowQueryByDBscale"}[30s])) >= 20.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '20.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16310fff-2b64-4903-ab4c-eef8db39623b
      metric_name: greatdb_dbscale_slow_queries
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 53be621f-8105-4386-8ecb-9cea7b8005a9
      severity: major
      type: cluster4
  - alert: "\u8BA1\u7B97\u8282\u70B9\u63A2\u6D4B\u5230\u6570\u636E\u8282\u70B9\u53D1\
      \u751F\u4E3B\u4ECE\u5207\u6362"
    annotations:
      t_value: '{{ $value }}'
    expr: (irate(dbscale_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",  member_uuid=~"16310fff-ee8d-408f-95cf-f665954d9084",
      status="TimesChangeMaster"}[30s])) > 0.0
    for: 10s
    labels:
      expr: '>'
      expr_value: '0.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16310fff-ee8d-408f-95cf-f665954d9084
      metric_name: greatdb_dbscale_change_master
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 012c6172-9c86-411b-a8c9-3cd4f7b4fc2e
      severity: major
      type: cluster4
  - alert: "\u8BA1\u7B97\u8282\u70B9\u63A2\u6D4B\u5230\u6570\u636E\u8282\u70B9\u53D1\
      \u751F\u4E3B\u4ECE\u5207\u6362"
    annotations:
      t_value: '{{ $value }}'
    expr: (irate(dbscale_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",  member_uuid=~"16310fff-0ed4-4a16-8970-3bf449ee5125",
      status="TimesChangeMaster"}[30s])) > 0.0
    for: 10s
    labels:
      expr: '>'
      expr_value: '0.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16310fff-0ed4-4a16-8970-3bf449ee5125
      metric_name: greatdb_dbscale_change_master
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 012c6172-9c86-411b-a8c9-3cd4f7b4fc2e
      severity: major
      type: cluster4
  - alert: "\u8BA1\u7B97\u8282\u70B9\u63A2\u6D4B\u5230\u6570\u636E\u8282\u70B9\u53D1\
      \u751F\u4E3B\u4ECE\u5207\u6362"
    annotations:
      t_value: '{{ $value }}'
    expr: (irate(dbscale_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",  member_uuid=~"16310fff-2b64-4903-ab4c-eef8db39623b",
      status="TimesChangeMaster"}[30s])) > 0.0
    for: 10s
    labels:
      expr: '>'
      expr_value: '0.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16310fff-2b64-4903-ab4c-eef8db39623b
      metric_name: greatdb_dbscale_change_master
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 012c6172-9c86-411b-a8c9-3cd4f7b4fc2e
      severity: major
      type: cluster4
  - alert: "\u8BA1\u7B97\u8282\u70B9\u63A2\u6D4B\u5230\u6570\u636E\u8282\u70B9\u4E3B\
      \u4ECE\u540C\u6B65\u72B6\u6001\u65AD\u5F00"
    annotations:
      t_value: '{{ $value }}'
    expr: (irate(dbscale_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",  member_uuid=~"16310fff-ee8d-408f-95cf-f665954d9084",
      status="TimesReplicationBroken"}[30s])) > 0.0
    for: 10s
    labels:
      expr: '>'
      expr_value: '0.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16310fff-ee8d-408f-95cf-f665954d9084
      metric_name: greatdb_dbscale_replication_broken
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 9b62686e-6155-4310-891f-52976b55ac2f
      severity: major
      type: cluster4
  - alert: "\u8BA1\u7B97\u8282\u70B9\u63A2\u6D4B\u5230\u6570\u636E\u8282\u70B9\u4E3B\
      \u4ECE\u540C\u6B65\u72B6\u6001\u65AD\u5F00"
    annotations:
      t_value: '{{ $value }}'
    expr: (irate(dbscale_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",  member_uuid=~"16310fff-0ed4-4a16-8970-3bf449ee5125",
      status="TimesReplicationBroken"}[30s])) > 0.0
    for: 10s
    labels:
      expr: '>'
      expr_value: '0.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16310fff-0ed4-4a16-8970-3bf449ee5125
      metric_name: greatdb_dbscale_replication_broken
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 9b62686e-6155-4310-891f-52976b55ac2f
      severity: major
      type: cluster4
  - alert: "\u8BA1\u7B97\u8282\u70B9\u63A2\u6D4B\u5230\u6570\u636E\u8282\u70B9\u4E3B\
      \u4ECE\u540C\u6B65\u72B6\u6001\u65AD\u5F00"
    annotations:
      t_value: '{{ $value }}'
    expr: (irate(dbscale_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",  member_uuid=~"16310fff-2b64-4903-ab4c-eef8db39623b",
      status="TimesReplicationBroken"}[30s])) > 0.0
    for: 10s
    labels:
      expr: '>'
      expr_value: '0.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16310fff-2b64-4903-ab4c-eef8db39623b
      metric_name: greatdb_dbscale_replication_broken
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 9b62686e-6155-4310-891f-52976b55ac2f
      severity: major
      type: cluster4
  - alert: "\u8BA1\u7B97\u8282\u70B9\u8FDE\u63A5\u6C60\u8FBE\u5230\u6700\u5927\u6C34\
      \u4F4D"
    annotations:
      t_value: '{{ $value }}'
    expr: (irate(dbscale_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",  member_uuid=~"16310fff-ee8d-408f-95cf-f665954d9084",
      status="TimesDBScalePoolReachMaxWater"}[30s])) > 0.0
    for: 10s
    labels:
      expr: '>'
      expr_value: '0.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16310fff-ee8d-408f-95cf-f665954d9084
      metric_name: greatdb_dbscale_dbScale_pool_reach_max_water
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 11de36ba-db4e-45b9-8ba5-1c89668f6a98
      severity: critical
      type: cluster4
  - alert: "\u8BA1\u7B97\u8282\u70B9\u8FDE\u63A5\u6C60\u8FBE\u5230\u6700\u5927\u6C34\
      \u4F4D"
    annotations:
      t_value: '{{ $value }}'
    expr: (irate(dbscale_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",  member_uuid=~"16310fff-0ed4-4a16-8970-3bf449ee5125",
      status="TimesDBScalePoolReachMaxWater"}[30s])) > 0.0
    for: 10s
    labels:
      expr: '>'
      expr_value: '0.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16310fff-0ed4-4a16-8970-3bf449ee5125
      metric_name: greatdb_dbscale_dbScale_pool_reach_max_water
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 11de36ba-db4e-45b9-8ba5-1c89668f6a98
      severity: critical
      type: cluster4
  - alert: "\u8BA1\u7B97\u8282\u70B9\u8FDE\u63A5\u6C60\u8FBE\u5230\u6700\u5927\u6C34\
      \u4F4D"
    annotations:
      t_value: '{{ $value }}'
    expr: (irate(dbscale_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",  member_uuid=~"16310fff-2b64-4903-ab4c-eef8db39623b",
      status="TimesDBScalePoolReachMaxWater"}[30s])) > 0.0
    for: 10s
    labels:
      expr: '>'
      expr_value: '0.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16310fff-2b64-4903-ab4c-eef8db39623b
      metric_name: greatdb_dbscale_dbScale_pool_reach_max_water
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 11de36ba-db4e-45b9-8ba5-1c89668f6a98
      severity: critical
      type: cluster4
  - alert: "\u8BA1\u7B97\u8282\u70B9\u4FDD\u5B58\u914D\u7F6E\u5230Zookeeper\u5931\u8D25"
    annotations:
      t_value: '{{ $value }}'
    expr: (irate(dbscale_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",  member_uuid=~"16310fff-ee8d-408f-95cf-f665954d9084",
      status="TimesDBScaleFailFlushConfToZoo"}[30s])) > 0.0
    for: 10s
    labels:
      expr: '>'
      expr_value: '0.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16310fff-ee8d-408f-95cf-f665954d9084
      metric_name: greatdb_dbscale_dbScale_fail_flush_conf_to_zoo
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: c45699b8-616b-433a-b959-d2feacfdae24
      severity: major
      type: cluster4
  - alert: "\u8BA1\u7B97\u8282\u70B9\u4FDD\u5B58\u914D\u7F6E\u5230Zookeeper\u5931\u8D25"
    annotations:
      t_value: '{{ $value }}'
    expr: (irate(dbscale_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",  member_uuid=~"16310fff-0ed4-4a16-8970-3bf449ee5125",
      status="TimesDBScaleFailFlushConfToZoo"}[30s])) > 0.0
    for: 10s
    labels:
      expr: '>'
      expr_value: '0.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16310fff-0ed4-4a16-8970-3bf449ee5125
      metric_name: greatdb_dbscale_dbScale_fail_flush_conf_to_zoo
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: c45699b8-616b-433a-b959-d2feacfdae24
      severity: major
      type: cluster4
  - alert: "\u8BA1\u7B97\u8282\u70B9\u4FDD\u5B58\u914D\u7F6E\u5230Zookeeper\u5931\u8D25"
    annotations:
      t_value: '{{ $value }}'
    expr: (irate(dbscale_status{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",  member_uuid=~"16310fff-2b64-4903-ab4c-eef8db39623b",
      status="TimesDBScaleFailFlushConfToZoo"}[30s])) > 0.0
    for: 10s
    labels:
      expr: '>'
      expr_value: '0.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16310fff-2b64-4903-ab4c-eef8db39623b
      metric_name: greatdb_dbscale_dbScale_fail_flush_conf_to_zoo
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: c45699b8-616b-433a-b959-d2feacfdae24
      severity: major
      type: cluster4
  - alert: "\u8BA1\u7B97\u8282\u70B9\u8FDE\u63A5\u6C60\u4F7F\u7528\u7387\u8F83\u9AD8"
    annotations:
      t_value: '{{ $value }}'
    expr: (dbscale_connection_pool{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      member_uuid=~"16310fff-ee8d-408f-95cf-f665954d9084", time="Connect_rate"}) >=
      80.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '80.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16310fff-ee8d-408f-95cf-f665954d9084
      metric_name: greatdb_dbscale_connect_pool_rate
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 009598a1-2215-43eb-a35a-67f7351f23ab
      severity: major
      type: cluster4
  - alert: "\u8BA1\u7B97\u8282\u70B9\u8FDE\u63A5\u6C60\u4F7F\u7528\u7387\u8F83\u9AD8"
    annotations:
      t_value: '{{ $value }}'
    expr: (dbscale_connection_pool{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      member_uuid=~"16310fff-0ed4-4a16-8970-3bf449ee5125", time="Connect_rate"}) >=
      80.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '80.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16310fff-0ed4-4a16-8970-3bf449ee5125
      metric_name: greatdb_dbscale_connect_pool_rate
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 009598a1-2215-43eb-a35a-67f7351f23ab
      severity: major
      type: cluster4
  - alert: "\u8BA1\u7B97\u8282\u70B9\u8FDE\u63A5\u6C60\u4F7F\u7528\u7387\u8F83\u9AD8"
    annotations:
      t_value: '{{ $value }}'
    expr: (dbscale_connection_pool{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",
      member_uuid=~"16310fff-2b64-4903-ab4c-eef8db39623b", time="Connect_rate"}) >=
      80.0
    for: 10s
    labels:
      expr: '>='
      expr_value: '80.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16310fff-2b64-4903-ab4c-eef8db39623b
      metric_name: greatdb_dbscale_connect_pool_rate
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '300'
      rule_uuid: 009598a1-2215-43eb-a35a-67f7351f23ab
      severity: major
      type: cluster4
  - alert: "\u8BA1\u7B97\u8282\u70B9\u65E0\u6CD5\u8FDE\u63A5"
    annotations:
      t_value: '{{ $value }}'
    expr: (up{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",  member_uuid=~"16310fff-ee8d-408f-95cf-f665954d9084"})
      == 0.0
    for: 10s
    labels:
      expr: ==
      expr_value: '0.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16310fff-ee8d-408f-95cf-f665954d9084
      metric_name: greatdb_dbscale_up
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '180'
      rule_uuid: 4de80b49-71b3-432f-936d-978f4dc47004
      severity: critical
      type: cluster4
  - alert: "\u8BA1\u7B97\u8282\u70B9\u65E0\u6CD5\u8FDE\u63A5"
    annotations:
      t_value: '{{ $value }}'
    expr: (up{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",  member_uuid=~"16310fff-0ed4-4a16-8970-3bf449ee5125"})
      == 0.0
    for: 10s
    labels:
      expr: ==
      expr_value: '0.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16310fff-0ed4-4a16-8970-3bf449ee5125
      metric_name: greatdb_dbscale_up
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '180'
      rule_uuid: 4de80b49-71b3-432f-936d-978f4dc47004
      severity: critical
      type: cluster4
  - alert: "\u8BA1\u7B97\u8282\u70B9\u65E0\u6CD5\u8FDE\u63A5"
    annotations:
      t_value: '{{ $value }}'
    expr: (up{instance=~"98dee4db-70f6-4054-bdb2-dab8901c8cf2",  member_uuid=~"16310fff-2b64-4903-ab4c-eef8db39623b"})
      == 0.0
    for: 10s
    labels:
      expr: ==
      expr_value: '0.0'
      for_time: '1'
      instance: 98dee4db-70f6-4054-bdb2-dab8901c8cf2
      legend_name: address,port
      member_uuid: 16310fff-2b64-4903-ab4c-eef8db39623b
      metric_name: greatdb_dbscale_up
      project_id: e463de6f-62e6-4879-a39d-688ebc968e31
      repeat_interval: '180'
      rule_uuid: 4de80b49-71b3-432f-936d-978f4dc47004
      severity: critical
      type: cluster4


```


告警规则的配置方式与记录规则相同。

示例告警规则文件如下：

```yaml
groups:
- name: example
  rules:
  - alert: HighRequestLatency
    expr: job:request_latency_seconds:mean5m{job="myjob"} > 0.5
    for: 10s
    labels:
      severity: page
    annotations:
      summary: High request latency
```

可选的for子句使 Prometheus 在第一次遇到新的表达式输出向量元素与将告警即为对此元素触发进行计数之间等待一段时间。在本例中，Prometheus 在触发告警之前，检查10秒内告警是否持续处于活动状态。处于活动状态但未触发的元素处于挂起状态。

labels子句指定一组标签添加到告警，与现有的标签冲突将会被覆盖。便签值可以模板化。

annotations子句指定一组信息标签，这些标签可用于存储更长的附加信息，例如告警描述或运行手册链接。注解值可以模板化。





## 案例分析

2025-04-02T15:05:06 [mysql@RDS-146 /data/greatrds/etc/monitor] $ cat alertmanager.yml
```yaml
global:
  resolve_timeout: 30s
inhibit_rules:
- equal:
  - metric_name
  - instance
  - member_uuid
  source_match:
    severity: critical
  target_match:
    severity: major
- equal:
  - metric_name
  - instance
  - member_uuid
  source_match:
    severity: major
  target_match:
    severity: minor
- equal:
  - metric_name
  - instance
  - member_uuid
  source_match:
    severity: minor
  target_match:
    severity: info
receivers:
- name: api.web.hook
  webhook_configs:
  - url: http://172.17.135.146:9010/v1/rds/monitor/alert/new
route:
  group_by:
  - alertname
  - instance
  group_interval: 10s
  group_wait: 10s
  receiver: api.web.hook
  repeat_interval: 7200s
  routes:
  - match:
      alertname: "node\u673A\u5668\u72B6\u6001\u5F02\u5E38"
      instance: d422c687-9030-44bf-be06-f3afe30992d2
    repeat_interval: 180s
  - match:
      alertname: "IO\u4F7F\u7528\u7387\u8FC7\u9AD8"
      instance: d422c687-9030-44bf-be06-f3afe30992d2
    repeat_interval: 300s
  - match:
      alertname: "cpu\u4F7F\u7528\u7387\u8FC7\u9AD8"
      instance: d422c687-9030-44bf-be06-f3afe30992d2
    repeat_interval: 300s
  - match:
      alertname: "\u5185\u5B58\u4F7F\u7528\u7387\u8FC7\u9AD8"
      instance: d422c687-9030-44bf-be06-f3afe30992d2
    repeat_interval: 43200s
  - match:
      alertname: "\u78C1\u76D8\u4F7F\u7528\u7387\u8FC7\u9AD8"
      instance: d422c687-9030-44bf-be06-f3afe30992d2
    repeat_interval: 300s
  - match:
      alertname: "\u8D85\u8FC710\u79D2\u4E8B\u52A1\u8D85\u8FC70\u4E2A"
      instance: d422c687-9030-44bf-be06-f3afe30992d2
    repeat_interval: 300s

```

以下是对该 Alertmanager 配置文件的详细解析：

---

### **全局配置 (global)**
```yaml
global:
  resolve_timeout: 30s  # 告警恢复后持续30秒未触发则标记为已解决
```

---

### **抑制规则 (inhibit_rules)**
配置了三级告警抑制逻辑，用于减少冗余告警通知：

```yaml
inhibit_rules:
- equal: [metric_name, instance, member_uuid]  # 必须匹配这三个标签
  source_match: {severity: critical}           # 当存在严重告警时
  target_match: {severity: major}              # 抑制所有主要告警
- equal: [metric_name, instance, member_uuid]
  source_match: {severity: major}              # 当存在主要告警时
  target_match: {severity: minor}              # 抑制所有次要告警
- equal: [metric_name, instance, member_uuid]
  source_match: {severity: minor}              # 当存在次要告警时
  target_match: {severity: info}               # 抑制所有信息类告警
```

#### **关键特性**
- **级联抑制**：通过 `critical -> major -> minor -> info` 的级联关系，确保高优先级告警优先
- **精准匹配**：要求 `metric_name`（指标名）、`instance`（实例ID）、`member_uuid`（成员UUID）完全一致才会触发抑制
- **适用场景**：适用于同一资源的多级告警（如同一台服务器的CPU、内存、磁盘告警）

---

### **接收器配置 (receivers)**
```yaml
receivers:
- name: api.web.hook
  webhook_configs:
  - url: http://172.17.135.146:9010/v1/rds/monitor/alert/new  # 告警推送地址
```

#### **特性说明**
- 所有告警统一发送至内部 API 接口 `http://172.17.135.146:9010/v1/rds/monitor/alert/new`
- 无重试机制配置，需确保接收端高可用

---

### **路由配置 (route)**
```yaml
route:
  group_by: [alertname, instance]    # 按告警名和实例ID分组
  group_interval: 10s               # 同一组告警间隔10秒后发送新通知
  group_wait: 10s                   # 等待10秒以聚合同组告警
  receiver: api.web.hook            # 默认接收器
  repeat_interval: 7200s            # 默认重复发送间隔2小时
  routes:                           # 特殊路由规则
  - match:
      alertname: "node机器状态异常"  # 中文Unicode转义
      instance: d422c687-9030-44bf-be06-f3afe30992d2
    repeat_interval: 180s           # 缩短为3分钟重复告警
  - match:
      alertname: "IO使用率过高"
      instance: d422c687-9030-44bf-be06-f3afe30992d2
    repeat_interval: 300s           # 5分钟重复
  - match:
      alertname: "cpu使用率过高"
      instance: d422c687-9030-44bf-be06-f3afe30992d2
    repeat_interval: 300s
  - match:
      alertname: "内存使用率过高"
      instance: d422c687-9030-44bf-be06-f3afe30992d2
    repeat_interval: 43200s         # 12小时重复（可能是低频敏感型告警）
  - match:
      alertname: "磁盘使用率过高"
      instance: d422c687-9030-44bf-be06-f3afe30992d2
    repeat_interval: 300s
  - match:
      alertname: "超过10秒事务超过0个"
      instance: d422c687-9030-44bf-be06-f3afe30992d2
    repeat_interval: 300s
```

#### **关键设计分析**
1. **分组策略**
   - 按 `alertname` 和 `instance` 分组，确保同一实例的相同告警被合并处理
   - 适用于多指标关联场景（如同一实例的CPU、内存、磁盘关联告警）

2. **差异化重复间隔**
   - **高频告警**（如IO、CPU、磁盘使用率）：5分钟重复，适合需要快速响应的资源瓶颈
   - **低频告警**（内存使用率）：12小时重复，可能因内存扩容周期较长
   - **紧急告警**（节点状态异常）：3分钟重复，适合基础设施级故障

3. **实例级隔离**
   所有规则仅针对实例 `d422c687-9030-44bf-be06-f3afe30992d2`，表明这是关键保护对象

---

### **配置优化建议**

| 项目                 | 问题                                  | 建议                                                         |
|--------------------|-------------------------------------|------------------------------------------------------------|
| **抑制规则标签匹配** | `member_uuid` 可能不必要，过度抑制风险 | 验证 `member_uuid` 是否必需，或简化为 `[alertname, instance]` |
| **接收器可靠性**     | 未配置请求超时和重试                  | 添加 `send_resolved: true` 和 `max_alerts: 100` 等参数       |
| **路由通用性**       | 所有特殊路由绑定到单一实例，扩展性差   | 使用正则匹配（如 `instance: ~"^prod-.*$"`）实现环境分级        |
| **Unicode 可读性**   | 配置文件中直接使用中文更易维护        | 直接写中文：`alertname: "node机器状态异常"`                   |
| **内存告警间隔**     | 12小时间隔可能过长，易遗漏处理         | 根据业务 SLA 调整为 1-4 小时                                 |

---

### **典型告警处理流程**
1. **告警触发**：Prometheus 检测到指标异常
2. **分组聚合**：Alertmanager 按 `alertname + instance` 合并10秒内同类告警
3. **抑制检查**：若存在更高级别告警（如 critical），则抑制低级别通知
4. **路由分发**：
   - 匹配特殊路由的告警按设定间隔（如 180s）重复
   - 其他告警按默认 2 小时间隔重复
5. **接收处理**：Webhook 接收告警并触发内部工单系统

---

### **调试技巧**
1. **验证配置语法**：
   ```bash
   amtool check-config alertmanager.yml
   ```
2. **模拟告警测试**：
   ```bash
   amtool alert --alertmanager.url=http://localhost:9093 add alertname=TestAlert severity=critical
   ```

通过此配置，可实现精细化告警管理和分级通知，但需根据实际运维需求调整抑制规则和路由策略。





配置 Alertmanager 使用 QQ 邮箱发送告警邮件
要在 Alertmanager 中配置 QQ 邮箱作为邮件发送渠道，你需要按照以下步骤进行配置：

1. 准备工作
首先，你需要确保你的 QQ 邮箱已开启 SMTP 服务：

登录 QQ 邮箱

进入"设置" > "账户"

找到"POP3/IMAP/SMTP/Exchange/CardDAV/CalDAV服务"部分

开启"IMAP/SMTP服务"或"POP3/SMTP服务"

按照提示获取授权码（这个授权码将替代密码用于SMTP认证）

2. Alertmanager 配置
编辑 Alertmanager 的配置文件（通常是 alertmanager.yml），添加以下 SMTP 配置：

yaml
global:
  resolve_timeout: 5m
  smtp_from: '你的QQ邮箱@qq.com'
  smtp_smarthost: 'smtp.qq.com:465'  # QQ邮箱SMTP服务器
  smtp_auth_username: '你的QQ邮箱@qq.com'
  smtp_auth_password: '你的SMTP授权码'  # 不是QQ密码，是前面获取的授权码
  smtp_require_tls: true
  smtp_hello: 'qq.com'

route:
  group_by: ['alertname']
  group_wait: 10s
  group_interval: 5m
  repeat_interval: 3h
  receiver: 'email'

receivers:
- name: 'email'
  email_configs:
  - to: '接收告警的邮箱@qq.com'  # 可以是你自己的QQ邮箱或其他邮箱
    send_resolved: true
3. 注意事项
SMTP 服务器：QQ 邮箱使用 smtp.qq.com，端口可以是 465（SSL）或 587（STARTTLS）

认证：必须使用授权码而不是QQ密码

发件人地址：smtp_from 必须与 smtp_auth_username 相同

TLS：建议启用 TLS（smtp_require_tls: true）

4. 测试配置
启动 Alertmanager 后，你可以发送测试告警来验证邮件功能是否正常工作。

5. 高级配置
如果需要更复杂的配置，可以考虑：

yaml
email_configs:
- to: '接收邮箱@example.com'
  send_resolved: true
  html: '{{ template "email.default.html" . }}'  # 使用自定义HTML模板
  headers:
    Subject: '【告警】{{ .CommonLabels.alertname }}'  # 自定义邮件主题
6. 关于接收邮件
Alertmanager 本身不接收邮件，它只负责发送告警通知。如果你想通过QQ邮箱接收告警，只需在 to 字段中配置你的QQ邮箱地址即可。

如果你需要实现邮件接收并触发某些操作，这通常需要额外的邮件接收服务或监控QQ邮箱的新邮件并通过脚本处理。


什么是 POP3/IMAP/SMTP 服务
POP3 （Post Office Protocol - Version 3）协议用于支持使用电子邮件客户端获取并删除在服务器上的电子邮件。

IMAP （Internet Message Access Protocol）协议用于支持使用电子邮件客户端交互式存取服务器上的邮件。

SMTP （Simple Mail Transfer Protocol）协议用于支持使用电子邮件客户端发送电子邮件。


IMAP 和 POP 有什么区别
POP允许电子邮件客户端下载服务器上的邮件，但是你在电子邮件客户端上的操作（如：移动邮件、标记已读等）不会反馈到服务器上的，比如：你通过电子邮件客户端收取了QQ邮箱中的3封邮件并移动到了其他文件夹，这些移动动作是不会反馈到服务器上的，也就是说，QQ邮箱服务器上的这些邮件是没有同时被移动的。需要特别注意的是，第三方客户端通过POP收取邮件时，也是有可能同步删除服务端邮件。在第三方客户端设置 POP 时，请留意是否有 保留邮件副本/备份 相关选项。如有该选项，且要保留服务器上的邮件，请勾选该选项。


在IMAP协议上，电子邮件客户端的操作都会反馈到服务器上，你对邮件进行的操作（如：移动邮件、标记已读、删除邮件等）服务器上的邮件也会做相应的动作。也就是说，IMAP 是“双向”的。同时，IMAP 可以只下载邮件的主题，只有当你真正需要的时候，才会下载邮件的所有内容。在 POP3 和 IMAP 协议上，QQ邮箱推荐你使用IMAP协议来存取服务器上的邮件。


什么是授权码
授权码是QQ邮箱用于登录第三方客户端/服务的专用密码，适用于登录以下服务：POP3/IMAP/SMTP/Exchange/CardDAV/CalDAV 服务。

温馨提醒：为了你的帐户安全，请不要告诉他人你的授权码，更改QQ帐号密码会触发授权码过期，需要重新获取新的授权码登录。


在第三方客户端/服务怎么设置
登录时，请在第三方客户端的密码输入框里面填入授权码进行验证。（不是填入QQ的密码）

IMAP/SMTP 设置方法
用户名/帐户： 你的QQ邮箱完整的地址

密码： 生成的授权码

电子邮件地址： 你的QQ邮箱的完整邮件地址

接收邮件服务器： imap.qq.com，使用SSL，端口号993

发送邮件服务器： smtp.qq.com，使用SSL，端口号465或587


POP3/SMTP 设置方法
用户名/帐户： 你的QQ邮箱完整的地址

密码： 生成的授权码

电子邮件地址： 你的QQ邮箱的完整邮件地址

接收邮件服务器： pop.qq.com，使用SSL，端口号995

发送邮件服务器： smtp.qq.com，使用SSL，端口号465或587


怎么管理我的授权码
在网页QQ邮箱端管理

在邮箱帐号与安全点击 设备管理 > 授权码管理，对授权码进行管理。


在QQ邮箱APP端管理

在QQ邮箱App点击左上角头像 > 在我的帐户列表下点击帐号>安全管理>设备管理，对授权码进行管理。