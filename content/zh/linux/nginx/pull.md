---
title: docker pull
date: 2023-10-12
weight: 20000
description: >
  杀死所有正在运行的容器


---

{{< alert >}}
杀死所有正在运行的容器
{{< /alert >}}


## 查看docker的镜像源

```sql
# "杀死所有正在运行的容器"
docker kill $(docker ps -a -q)

```



