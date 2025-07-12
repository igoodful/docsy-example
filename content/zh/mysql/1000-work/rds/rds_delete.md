---
title: 删除RDS
description: 删除RDS
date: 2017-01-05
weight: 20


---

{{< alert >}}

1. 免费开源，易于学习

2. 适用MySQL

3. 轻量级数据库客户端软件，拥有图形化界面

{{< /alert >}}



## 关闭所有容器、删除所有容器和镜像
```bash
docker ps -aq |xargs docker stop

docker ps -qa | xargs docker rm

docker image ls -aq |xargs docker image rm

```

## 删除所有相关目录

```bash
rm -rf /data/*
```





