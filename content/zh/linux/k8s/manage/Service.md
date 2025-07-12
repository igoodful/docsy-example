---
title: Service
date: 2023-10-24
weight: 30000
description: >
  基于角色的权限控制


---

{{< alert >}}

在继续往下学习 Service 之前，我们需要先弄明白 Kubernetes 系统中的三种 IP，因为经常有同学混乱。

1. Node IP：Node 节点的 IP 地址

2. Pod IP: Pod 的 IP 地址

3. Cluster IP: Service 的 IP 地址

首先，Node IP 是 Kubernetes 集群中节点的物理网卡 IP 地址(一般为内网)，所有属于这个网络的服务器之间都可以直接通信，所以 Kubernetes 集群外要想访问 Kubernetes 集群内部的某个节点或者服务，肯定得通过 Node IP 进行通信（这个时候一般是通过外网 IP 了）

然后 Pod IP 是每个 Pod 的 IP 地址，它是网络插件进行分配的，前面我们已经讲解过

最后 Cluster IP 是一个虚拟的 IP，仅仅作用于 Kubernetes Service 这个对象，由 Kubernetes 自己来进行管理和分配地址。


```sql
kubectl apply -f service-demo.yaml

kubectl get svc

kubectl get pods -o wide

kubectl logs -f nginx-ddc8f997b-ptb7b

```

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