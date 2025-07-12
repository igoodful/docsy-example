---
title: 安装
description: install
date: 2023-11-17
weight: 100


---

配置MinIO Access和Secret密钥，也就是登录账号和密码。 MINIO_ACCESS_KEY、MINIO_SECRET_KEY 变为 MINIO_ROOT_USER 、MINIO_ROOT_PASSWORD


1. 老版本使用MINIO_ACCESS_KEY，MINIO_SECRET_KEY，新版本使用MINIO_ROOT_USER，MINIO_ROOT_PASSWORD
2. 现在需要增加额外一个端口号用于web管理 --console-address :9090 ；API管理地址 --console-address :9000


## 配置

- `/usr/lib/systemd/system/minio_9011.service`
```ini
[Unit]
Description=minio
Documentation=https://docs.min.io
Wants=network-online.target
After=network-online.target

[Service]
Type=simple
WorkingDirectory=/data/minio_9011
User  =  mysql
Group =  mysql

EnvironmentFile= /data/minio_9011/conf/minio.conf

ExecStart=/data/minio_9011/bin/minio server --address :9011 --console-address :9012 --config-dir /data/minio_9011/conf  /data/minio_9011/data

Restart=always
LimitNOFILE=1024000
TasksMax=infinity
TimeoutSec=infinity
SendSIGKILL=no
SuccessExitStatus=0 143

[Install]
WantedBy=multi-user.target


```

- `cat /data/minio_9011/conf/minio.conf`
```bash

MINIO_ROOT_USER=minio
MINIO_ROOT_PASSWORD=TdtuF2q0cD4p8q8XFfmf6POrsWXVESRb
MINIO_VOLUMES=/data/minio_9011/data
MINIO_SERVER_URL=http://172.17.134.76:9011

```





