---
title: 安装
description: A short lead description about this content page. It can be **bold** or _italic_ and can be split over multiple paragraphs.
date: 2017-01-05
weight: 200
---

{{< alert >}}

1. docker官方镜像源：[Docker](https://hub.docker.com )


{{< /alert >}}


### 安装
> 安装指定版本：yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io

```sql
yum -y install yum-utils device-mapper-persistent-data lvm2

yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

yum list docker-ce --showduplicates | sort -r

yum remove docker \
docker-client \
docker-client-latest \
docker-common \
docker-latest \
docker-latest-logrotate \
docker-logrotate \
docker-engine

yum -y install docker-ce docker-ce-cli containerd.io docker-compose-plugin

systemctl start docker

systemctl enable docker

```


### 配置

>  配置文件位置：/etc/docker/daemon.json

```sql
import os

print("====")

```


### 卸载

```sql
yum -y remove docker-ce

rm -rf /var/lib/docker

```



## 添加docker用户与组

以root执行：
```bash
# 添加docker组
groupadd docker

# 添加mysql用户到docker组
gpasswd -a mysql docker

# 更新用户组
newgrp docker

# 需要重启docker才会生效
systemctl stop docker

systemctl start docker

```

以mysql用户执行：
```bash
# 检查是否生效
docker version

docker ps

groups mysql

id mysql

```

#### permission denied
> 普通用户不在docker组的报错信息
```bash
[mysql@node71 ~]$ docker ps
Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get http://%2Fvar%2Frun%2Fdocker.sock/v1.26/containers/json: dial unix /var/run/docker.sock: connect: permission denied

[mysql@node71 /home/mysql]$ docker version
Client:
 Version:         1.13.1
 API version:     1.26
 Package version:
Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get http://%2Fvar%2Frun%2Fdocker.sock/v1.26/version: dial unix /var/run/docker.sock: connect: permission denied

```





