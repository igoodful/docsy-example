---
title: deployments
date: 2023-10-24
weight: 30000
description: >
  使用Deployment创建的Pod是无状态的，每个 Deployment 就对应集群中的一次部署


---

{{< alert >}}

1. 实现了Pod滚动更新




操作：

1. 创建：kubectl apply -f nginx-deploy.yaml


kubectl get pods -l app=nginx

kubectl describe pod nginx-deploy-85ff79dd56-txc4h
> 关注：Controlled By:


水平扩容
kubectl scale deployment nginx-deploy --replicas=4









{{< /alert >}}




## 查看所有命名空间
> 指定空间：-n default
>
> 所有空间：-all-namespaces 与 --A 等价

```yaml
# nginx-deploy.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deploy
  namespace: default
spec:
  replicas: 3 # 期望的 Pod 副本数量，默认值为1
  selector: # Label Selector，必须匹配 Pod 模板中的标签
    matchLabels:
      app: nginx
  template: # Pod 模板
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx
          ports:
            - containerPort: 80
```



```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deploy
  namespace: default
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  minReadySeconds: 5
  strategy:
    type: RollingUpdate # 指定更新策略：RollingUpdate和Recreate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:1.7.9
          ports:
            - containerPort: 80

```


后前面相比较，除了更改了镜像之外，我们还指定了更新策略：

```yaml
minReadySeconds: 5
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxSurge: 1
    maxUnavailable: 1

```

- minReadySeconds：表示 Kubernetes 在等待设置的时间后才进行升级，如果没有设置该值，Kubernetes 会假设该容器启动起来后就提供服务了，如果没有设置该值，在某些极端情况下可能会造成服务不正常运行，默认值就是 0。

- type=RollingUpdate：表示设置更新策略为滚动更新，可以设置为Recreate和RollingUpdate两个值，Recreate表示全部重新创建，默认值就是RollingUpdate。

- maxSurge：表示升级过程中最多可以比原先设置多出的 Pod 数量，例如：maxSurage=1，replicas=5，就表示 Kubernetes 会先启动一个新的 Pod，然后才删掉一个旧的 Pod，整个升级过程中最多会有5+1个 Pod。

- maxUnavaible：表示升级过程中最多有多少个 Pod 处于无法提供服务的状态，当maxSurge不为 0 时，该值也不能为 0，例如：maxUnavaible=1，则表示 Kubernetes 整个升级过程中最多会有 1 个 Pod 处于无法服务的状态。


```sql
kubectl apply -f nginx-deploy.yaml

kubectl rollout status deployment/nginx-deploy

kubectl rollout pause deployment/nginx-deploy

kubectl rollout resume deployment/nginx-deploy

kubectl rollout history deployment nginx-deploy

kubectl rollout history deployment nginx-deploy --revision=1

kubectl rollout undo deployment nginx-deploy

kubectl rollout undo deployment nginx-deploy --to-revision=1

kubectl describe deploy nginx-deploy

kubectl get pods -l app=nginx

kubectl get deployment

kubectl get rs -l app=nginx

kubectl get rs nginx-deploy-85ff79dd56 -o yaml

```



