---
title: docker info
date: 2023-10-12
weight: 100
description: 杀死所有正在运行的容器
viz: true

---

{{< alert >}}
- [docker](https://docs.docker.com/reference/cli/docker/)
{{< /alert >}}


## 查看docker的镜像源

```sql
# "杀死所有正在运行的容器"
docker kill $(docker ps -a -q)

```



