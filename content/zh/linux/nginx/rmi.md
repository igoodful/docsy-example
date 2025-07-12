---
title: docker rmi
date: 2023-10-12
weight: 20000
description: >
  删除镜像


---

{{< alert >}}

### 优雅删除镜像:

1. 停止容器

2. 删除容器

3. 删除镜像




{{< /alert >}}




### 删除指定镜像

```sql

docker images

docker rmi  [imageID]

docker rmi -f [imageID]

```



### 删除所有镜像

> 1. 停止所有容器
>
> 2. 删除所有容器
>
> 3. 删除所有镜像




```sql

docker stop $(docker ps -aq)

docker rm -f $(docker ps -aq)

docker rmi -f $(docker images -q)

```



