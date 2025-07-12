---
title: replicasets
date: 2023-10-24
weight: 30000
description: >
  在实际操作的时候，我们反而不会去直接使用replicasets，而是会使用更上层的控制器


---

{{< alert >}}

kubectl describe rs nginx-deploy-85ff79dd56
> 关注：Controlled By:
{{< /alert >}}




##
> 指定空间：-n default
>
> 所有空间：-A

```sql
[root@master ~]# kubectl get rs
NAME                               DESIRED   CURRENT   READY   AGE
greatdbpaxos-operator-5847db7b75   1         1         1       14d
[root@master ~]# kubectl describe rs greatdbpaxos-operator-5847db7b75
Name:           greatdbpaxos-operator-5847db7b75
Namespace:      default
Selector:       app.kubernetes.io/component=operator,app.kubernetes.io/name=greatdbpaxos-operator,pod-template-hash=5847db7b75
Labels:         app.kubernetes.io/component=operator
                app.kubernetes.io/name=greatdbpaxos-operator
                pod-template-hash=5847db7b75
Annotations:    deployment.kubernetes.io/desired-replicas: 1
                deployment.kubernetes.io/max-replicas: 2
                deployment.kubernetes.io/revision: 1
Controlled By:  Deployment/greatdbpaxos-operator
Replicas:       1 current / 1 desired
Pods Status:    1 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:           app.kubernetes.io/component=operator
                    app.kubernetes.io/name=greatdbpaxos-operator
                    pod-template-hash=5847db7b75
  Service Account:  greatdbpaxos-operator
  Containers:
   operator:
    Image:      172.17.136.180/wang/greatdb-operator:latest
    Port:       <none>
    Host Port:  <none>
    Command:
      /greatdb-operator
      --port=9000
    Limits:
      cpu:     3
      memory:  4Gi
    Requests:
      cpu:        3
      memory:     4Gi
    Liveness:     http-get http://:8080/healthz delay=0s timeout=1s period=10s #success=1 #failure=3
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Events:           <none>
             ClusterIP   10.96.0.10      <none>        53/UDP,53/TCP,9153/TCP   15d


```