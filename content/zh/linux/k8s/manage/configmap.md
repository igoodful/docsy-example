---
title: configmap
date: 2023-10-24
weight: 30000
description: >
  configmap


---

{{< alert >}}

- ConfigMap 是用来存储一些非安全的配置信息

- Secret用来保存敏感信息，例如密码、OAuth 令牌和 ssh key 等等，将这些信息放在 Secret 中比放在 Pod 的定义中或者 Docker 镜像中要更加安全和灵活



### pod如何使用configmap

1. 设置环境变量的值

2. 在容器里设置命令行参数

3. 在数据卷里面挂载配置文件


{{< /alert >}}




## 查看所有命名空间
> 指定空间：-n default
>
> 所有空间：-all-namespaces 与 --A 等价

```sql
[root@master ~]# kubectl get ns
NAME                 STATUS   AGE
default              Active   15d
greatdb              Active   14d
kube-node-lease      Active   15d
kube-public          Active   15d
kube-system          Active   15d
local-path-storage   Active   14d
```