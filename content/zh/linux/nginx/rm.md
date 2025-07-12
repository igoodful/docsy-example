---
title: docker rm
date: 2023-10-12
weight: 20000
description: >
  删除容器


---

{{< alert >}}

### 优雅删除容器:

1. 先停止容器

2. 再删除容器

{{< /alert >}}


##




```sql
docker images

docker rm -f [image]

# "强制删除所有镜像"
docker rmi -f $(docker images -q)

```



