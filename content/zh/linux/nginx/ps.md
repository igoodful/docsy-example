---
title: docker ps
date: 2023-10-12
weight: 20000
description: >
  查看Docker运行中的容器


---

{{< alert >}}

### （1）容器状态：

1. created（已创建）

2. restarting（重启中）

3. running（运行中）

4. removing（迁移中）

5. paused（暂停）

6. exited（停止）

7. dead（死亡）



### （2）状态转移图：

{{< figure src="/images/linux/docker/dockerState.jpeg" >}}



```mermaid
stateDiagram
       direction LR
       image --> created:docker create
       image --> running:docker run
       created --> running:docker start
       created --> deleted:docker rm
       running --> exited:docker stop /docker kill
       running --> running:docker restart
       running --> deleted:docker rm -f
       running --> dead
       running --> paused:docker pause
       paused --> running:docker unpause
       paused --> deleted:docker rm -f
       paused --> exited:docker stop /docker kill
       exited --> running:docker start
       exited --> deleted:docker rm

```


{{< /alert >}}


##


```sql
# "查看Docker运行中的容器"
docker ps

```



