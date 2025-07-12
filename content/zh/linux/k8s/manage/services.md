---
title: services
date: 2023-10-24
weight: 30000
description: >
  services


---

{{< alert >}}




{{< /alert >}}




##
> 指定空间：-n default
>
> 所有空间：-A

```sql
[root@master ~]# kubectl get svc -A
NAMESPACE     NAME                       TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                  AGE
default       greatdb-sample-dashboard   ClusterIP   10.103.37.22    <none>        8080/TCP                 14d
default       greatdb-sample-greatdb     ClusterIP   None            <none>        3306/TCP,33061/TCP       14d
default       greatdb-sample-read        NodePort    10.99.171.224   <none>        3306:30020/TCP           14d
default       greatdb-sample-write       NodePort    10.100.30.54    <none>        3306:30021/TCP           14d
default       kubernetes                 ClusterIP   10.96.0.1       <none>        443/TCP                  15d
kube-system   kube-dns                   ClusterIP   10.96.0.10      <none>        53/UDP,53/TCP,9153/TCP   15d


```