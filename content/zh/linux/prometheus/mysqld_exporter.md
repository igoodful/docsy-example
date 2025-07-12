---
title: mysqld_exporter 9104
description: 解压即可
date: 2023-11-05
weight: 30000


---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;

{{<alert color="danger" title="注意" >}}


{{</alert>}}

## 安装


```sql
wget https://github.com/prometheus/mysqld_exporter/releases/download/v0.12.0/mysqld_exporter-0.12.0.linux-amd64.tar.gz

tar xvf mysqld_exporter-0.12.0.linux-amd64.tar.gz

mv mysqld_exporter-0.12.0.linux-amd64/* /data/
```


## 配置
- 创建监控账户：
```sql
create user monitor@'localhost'   identified with mysql_native_password by 'QWer12#$';

grant process, replication client, select on *.* to monitor@'localhost';
```

- 创建mysqld_exporter连接MySQL server的连接信息的配置文件：`cat /home/work/mysqld_exporter/my.cnf`
```yaml
[client]
user=mysqld_exporter
password=12345678
host=127.0.0.1
port=3306
```



## 配置：service

- 配置服务：`cat  /etc/systemd/system/mysqld_exporter.service`

```systemd
[root@k8s-node-70 mysqld_exporter]# cat  /usr/lib/systemd/system/mysqld_exporter.service
[Unit]
Description=mysqld_exporter
After=network.target

[Service]
Type=simple
ExecStart=/home/work/mysqld_exporter/mysqld_exporter --config.my-cnf /home/work/mysqld_exporter/my.cnf  --web.listen-address=0.0.0.0:9104 \
--collect.slave_status \
--collect.binlog_size \
--collect.info_schema.processlist \
--collect.info_schema.innodb_metrics \
--collect.engine_innodb_status \
--collect.perf_schema.file_events \
--collect.perf_schema.replication_group_member_stats
Restart=on-failure

[Install]
WantedBy=multi-user.target


```
- 管理 mysqld_exporter
```bash
systemctl daemon-reload

systemctl enable mysqld_exporter

systemctl restart mysqld_exporter

systemctl status mysqld_exporter

systemctl stop mysqld_exporter

```


## 访问：http://10.10.10.10:9104




## 配置：prometheus.yaml


```yaml
- job_name: 'mysqld_exporter'

  scheme: http

  static_configs:

  - targets: ['192.168.40.200:9104']

    labels:

      app: mysqld_exporter

      role: mysqld_exporter

```




## 指标详解



























