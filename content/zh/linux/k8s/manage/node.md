---
title: node
date: 2023-10-24
weight: 20000
description: >
  node


---

{{< alert >}}



{{< /alert >}}
















### 查看节点和版本

```sql
[root@master ~]# kubectl get nodes
NAME     STATUS   ROLES                  AGE   VERSION
k8n1     Ready    worker                 14d   v1.22.0
k8n2     Ready    worker                 14d   v1.22.0
master   Ready    control-plane,master   15d   v1.22.0
node1    Ready    worker                 15d   v1.22.0
node2    Ready    worker                 15d   v1.22.0
```















