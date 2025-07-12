---
title: DaemonSet
date: 2023-10-24
weight: 30000
description: >
  DaemonSet


---

{{< alert >}}




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