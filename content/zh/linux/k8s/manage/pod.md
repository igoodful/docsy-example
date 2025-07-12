---
title: pod
date: 2023-10-24
weight: 10000
description: pod

---

{{< alert >}}

#### 根据Pod里容器数量分类：

1. 单容器模型。由于Pod是Kubernetes可识别的最小对象，Kubernetes管理调度Pod而不是直接管理容器，即使只有一个容器也需要封装到Pod里。

2. 多容器模型。在这个模型中，Pod可以容纳多个紧密关联的容器以共享Pod里的资源。这些容器作为单一的，凝聚在一起的服务单元工作。


#### Pod状态：

1. Pending（挂起）：Pod 已经被 Kubernetes API 接受，但它的容器镜像还没有被拉取，或者 Pod 所需的节点资源（CPU、内存等）还没有满足。在这个状态中，Pod 是不可调度的。

2. Running（运行）：Pod 已经调度到了节点上并且所有容器都已经创建，至少有一个容器仍在运行中或者在启动过程中。

3. Succeeded（成功）：Pod 中的所有容器都已经正常终止，并且不会再重启。

4. Failed（失败）：Pod 中至少有一个容器已经以非零状态退出。

5. Unknown（未知）：无法获取 Pod 的状态。这种状态通常是由于与 Pod 相关的 API 调用失败或者 Pod 控制器处于错误状态导致的。


#### 创建pod方法：

1. 直接声明创建pod

2. controller声明创建pod。比如，deployments、replicationcontrollers、daemonsets或者replicasets

3. 静态pod，这种用的比较少，一般是把pod的声明文件放在对应的kubernetes/manifest目录下，通常用来创建apiserver，controller-manager，scheduler这类k8s管理组件的pod

#### 创建pod流程

1. Pod 先存储在 etcd 中。

2. 调度程序会分配一个节点，再将节点写入 etcd。

3. 向 kubelet 通知有个新 Pod。

4. kubelet 将创建容器的任务给CRI。

5. kubelet 将容器附加到 CNI。

6. kubelet 将容器中的卷委派给 CSI。

7. CNI 分配 IP 地址。

8. Kubelet 将 IP 地址通知给控制平面。

9. IP 地址存储在 etcd 中。

如果我们的 Pod 属于 某个Service：

1. Kubelet 等待 Readiness 探针成功。

2. 对所有相关的 Endpoint 对象更改进行通知。

3. Endpoint 将新 endpoint（IP 地址 + 端口）添加到列表中。

4. Kube-proxy 被通知 Endpoint 更改，然后 Kube-proxy 会更新每个节点上的 iptables 规则。

5. Ingress 控制器被通知 Endpoint 变化，然后控制器会将流量路由到新的 IP 地址。

6. CoreDNS 被通知 Endpoint 更改。如果服务的类型为 Headless，DNS 会进行更新。

7. 云提供商被通知 Endpoint 更改。如果 Service 是 type: LoadBalancer，新的 endpoint 配置会是负载均衡池的一部分。

8. 集群中安装的所有服务网格也会被通知 Endpoint 更改。

9. 订阅 Endpoint 更改的其他运营商也会收到通知。



{{< figure src="/images/linux/k8s/podCreate.jpg" >}}

{{< /alert >}}

### 环境信息
```sql
[root@master ~]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
172.17.137.35  master
172.17.137.36  node1
172.17.137.37  node2
172.17.139.95   k8n1
172.17.140.79   k8n2
172.17.136.180 harbor

```



kubectl get pods   # 或者 kubectl get pods





### 查看pod名称与状态
> kubectl get pod  -n default
>
> kubectl get pods -n default
>
> kubectl get po   -n default
>
```sql
[root@master ~]# kubectl get pod  -n default
NAME                                     READY   STATUS    RESTARTS        AGE
greatdb-sample-dashboard                 1/1     Running   0               14d
greatdb-sample-greatdb-0                 3/3     Running   4 (47h ago)     14d
greatdb-sample-greatdb-1                 3/3     Running   4 (2d10h ago)   14d
greatdb-sample-greatdb-2                 3/3     Running   6 (18h ago)     14d
greatdbpaxos-operator-5847db7b75-8vhzn   1/1     Running   0               14d

[root@master ~]# kubectl get pod -A
NAMESPACE            NAME                                       READY   STATUS    RESTARTS        AGE
default              greatdb-sample-dashboard                   1/1     Running   0               14d
default              greatdb-sample-greatdb-0                   3/3     Running   4 (47h ago)     14d
default              greatdb-sample-greatdb-1                   3/3     Running   4 (2d10h ago)   14d
default              greatdb-sample-greatdb-2                   3/3     Running   6 (18h ago)     14d
default              greatdbpaxos-operator-5847db7b75-8vhzn     1/1     Running   0               14d
kube-system          calico-kube-controllers-5d4b78db86-jrswv   1/1     Running   1 (14d ago)     14d
kube-system          calico-node-9v7d9                          1/1     Running   1 (14d ago)     14d
kube-system          calico-node-dtq6s                          1/1     Running   1 (14d ago)     14d
kube-system          calico-node-ks2j2                          1/1     Running   1               14d
kube-system          calico-node-l7rjg                          1/1     Running   0               14d
kube-system          calico-node-wxszk                          1/1     Running   2 (14d ago)     14d
kube-system          coredns-7f6cbbb7b8-5fxks                   1/1     Running   1 (14d ago)     15d
kube-system          coredns-7f6cbbb7b8-nhxt9                   1/1     Running   1 (14d ago)     15d
kube-system          etcd-master                                1/1     Running   2 (14d ago)     15d
kube-system          kube-apiserver-master                      1/1     Running   2 (14d ago)     15d
kube-system          kube-controller-manager-master             1/1     Running   2 (14d ago)     14d
kube-system          kube-proxy-6g5cf                           1/1     Running   2 (14d ago)     15d
kube-system          kube-proxy-897c4                           1/1     Running   0               14d
kube-system          kube-proxy-8cn8p                           1/1     Running   1 (14d ago)     15d
kube-system          kube-proxy-dq7pf                           1/1     Running   1               14d
kube-system          kube-proxy-nqndm                           1/1     Running   2 (14d ago)     15d
kube-system          kube-scheduler-master                      1/1     Running   2 (14d ago)     14d
local-path-storage   local-path-provisioner-6464fcbd8b-vwz4b    1/1     Running   0               14d
```


### 查看pod运行在哪个节点上
> 查看pod所在的节点ip与主机名称
>
> 指定空间： -n default
>
> 所有空间： -A
>
> 指定pod名称：pod pod_name

```sql
[root@master ~]# kubectl get pod  -o wide -n default
NAME                                     READY   STATUS    RESTARTS        AGE   IP               NODE    NOMINATED NODE   READINESS GATES
greatdb-sample-dashboard                 1/1     Running   0               14d   10.144.113.4     k8n1    <none>           <none>
greatdb-sample-greatdb-0                 3/3     Running   4 (47h ago)     14d   10.144.104.17    node2   <none>           <none>
greatdb-sample-greatdb-1                 3/3     Running   4 (2d10h ago)   14d   10.144.166.146   node1   <none>           <none>
greatdb-sample-greatdb-2                 3/3     Running   6 (17h ago)     14d   10.144.35.137    k8n2    <none>           <none>
greatdbpaxos-operator-5847db7b75-8vhzn   1/1     Running   0               14d   10.144.166.140   node1   <none>           <none>

```


### 查看pod的描述信息

```sql
[root@master ~]# kubectl describe pod greatdb-sample-greatdb-0
Name:         greatdb-sample-greatdb-0
Namespace:    default
Priority:     0
Node:         node2/172.17.137.37
Start Time:   Tue, 10 Oct 2023 15:02:49 +0800
Labels:       app.kubernetes.io/component=GreatDB
              app.kubernetes.io/instance=greatdb-sample
              app.kubernetes.io/managed-by=greatdb-operator
              app.kubernetes.io/name=GreatDBPaxos
              app.kubernetes.io/pod-name=greatdb-sample-greatdb-0
              app.kubernetes.io/ready=true
              app.kubernetes.io/role=PRIMARY
Annotations:  cni.projectcalico.org/containerID: 655b98480d1d4b444210114fc102d6aa6f08afb67c9fffc509e6eb0cb1c8da7c
              cni.projectcalico.org/podIP: 10.144.104.17/32
              cni.projectcalico.org/podIPs: 10.144.104.17/32
Status:       Running
IP:           10.144.104.17
IPs:
  IP:           10.144.104.17
Controlled By:  GreatDBPaxos/greatdb-sample
Containers:
  greatdb:
    Container ID:  docker://e4eb3aa796a7e2cd410ea4c13d9a566d7aaffdce4cc8b658426e86301181987d
    Image:         172.17.136.180/wang/mysql-v8.0.26:latest
    Image ID:      docker-pullable://172.17.136.180/wang/mysql-v8.0.26@sha256:0b19eeb8fcb0b40585fcf14098107fb0f5b530bdb9725cba279ec63de2222f33
    Port:          <none>
    Host Port:     <none>
    Command:
      start-greatdb.sh
    State:          Running
      Started:      Sun, 22 Oct 2023 20:51:10 +0800
    Last State:     Terminated
      Reason:       OOMKilled
      Exit Code:    137
      Started:      Thu, 19 Oct 2023 04:08:30 +0800
      Finished:     Sun, 22 Oct 2023 20:51:09 +0800
    Ready:          True
    Restart Count:  4
    Limits:
      cpu:     2
      memory:  2Gi
    Requests:
      cpu:      2
      memory:   2Gi
    Liveness:   http-get http://:8001/health delay=30s timeout=3s period=10s #success=1 #failure=3
    Readiness:  exec [ReadinessProbe] delay=30s timeout=2s period=5s #success=1 #failure=3
    Startup:    tcp-socket :3306 delay=0s timeout=2s period=30s #success=1 #failure=20
    Environment Variables from:
      greatdb-sample-secret  Secret  Optional: false
    Environment:
      PODNAME:            greatdb-sample-greatdb-0 (v1:metadata.name)
      NAMESPACE:          default (v1:metadata.namespace)
      SERVICE_NAME:       greatdb-sample-greatdb
      CLUSTERDOMAIN:      cluster.local
      FQDN:               $(PODNAME).$(SERVICE_NAME).$(NAMESPACE).svc.$(CLUSTERDOMAIN)
      SERVERPORT:         3306
      GROUPLOCALADDRESS:  $(PODNAME).$(SERVICE_NAME).$(NAMESPACE).svc.$(CLUSTERDOMAIN):33061
    Mounts:
      /etc/greatdb/ from config (ro)
      /greatdb/mysql/ from data (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-ttxs6 (ro)
  greatdb-agent:
    Container ID:  docker://84efc806a97862fbbd274b99bcb7117ad1b5afa6749569482d12003ac177186e
    Image:         172.17.136.180/wang/mysql-v8.0.26:latest
    Image ID:      docker-pullable://172.17.136.180/wang/mysql-v8.0.26@sha256:0b19eeb8fcb0b40585fcf14098107fb0f5b530bdb9725cba279ec63de2222f33
    Port:          <none>
    Host Port:     <none>
    Command:
      greatdb-agent
      --mode=server
    State:          Running
      Started:      Tue, 10 Oct 2023 15:02:50 +0800
    Ready:          True
    Restart Count:  0
    Limits:
      cpu:     2
      memory:  2Gi
    Requests:
      cpu:      1
      memory:   1Gi
    Liveness:   tcp-socket :19999 delay=15s timeout=1s period=10s #success=1 #failure=3
    Readiness:  http-get http://:19999/health delay=30s timeout=1s period=10s #success=1 #failure=3
    Environment Variables from:
      greatdb-sample-secret  Secret  Optional: false
    Environment:
      HOSTNAME:      greatdb-sample-greatdb-0 (v1:metadata.name)
      NAMESPACE:     default (v1:metadata.namespace)
      SERVICE_NAME:  greatdb-sample-greatdb
      ClusterName:   greatdb-sample
      BackupHost:    $(HOSTNAME).$(SERVICE_NAME).$(NAMESPACE).svc.cluster.local
      BackupPort:    3306
    Mounts:
      /backup/ from backup (rw)
      /greatdb/mysql/ from data (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-ttxs6 (ro)
  promtail:
    Container ID:  docker://daf8b58afbec838633cb089b93f33510d774d2845c6722c538a1e0c594a269a4
    Image:         172.17.136.180/wang/promtail-v2.7.4:latest
    Image ID:      docker-pullable://172.17.136.180/wang/promtail-v2.7.4@sha256:1caa3d52d4b5e04cb2a8d616dcaa46bd70328a254954e58c03dd4ff0da1ef014
    Port:          <none>
    Host Port:     <none>
    Command:
      /usr/bin/promtail
      -config.expand-env=true
      -config.file=/etc/promtail/promtail.yaml
    State:          Running
      Started:      Tue, 10 Oct 2023 15:02:51 +0800
    Ready:          True
    Restart Count:  0
    Limits:
      cpu:     300m
      memory:  300Mi
    Requests:
      cpu:      300m
      memory:   300Mi
    Liveness:   tcp-socket :3001 delay=10s timeout=1s period=10s #success=1 #failure=3
    Readiness:  tcp-socket :3001 delay=10s timeout=1s period=10s #success=1 #failure=3
    Environment Variables from:
      greatdb-sample-secret  Secret  Optional: false
    Environment:
      PODNAME:                 greatdb-sample-greatdb-0 (v1:metadata.name)
      NAMESPACE:               default (v1:metadata.namespace)
      SERVICE_NAME:            greatdb-sample-greatdb
      CLUSTERDOMAIN:           cluster.local
      FQDN:                    $(PODNAME).$(SERVICE_NAME).$(NAMESPACE).svc.$(CLUSTERDOMAIN)
      SERVERPORT:              3306
      GROUPLOCALADDRESS:       $(PODNAME).$(SERVICE_NAME).$(NAMESPACE).svc.$(CLUSTERDOMAIN):33061
      INSTANCE_INTERIOR_PORT:  3306
      LOKI_CLIENT:             http://greatdb-sample-dashboard:8080/log-monitor/loki/api/v1/push
    Mounts:
      /greatdb/mysql/ from data (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-ttxs6 (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  config:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      greatdb-sample-greatdb
    Optional:  false
  data:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  greatdb-sample-greatdb-0
    ReadOnly:   false
  backup:
    Type:      NFS (an NFS mount that lasts the lifetime of a pod)
    Server:    172.17.136.180
    Path:      /data/nfs/
    ReadOnly:  false
  kube-api-access-ttxs6:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   Burstable
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:                      <none>

```








### 查看指定pod信息
> kubectl get pod xxx
>
> kubectl get pod xxx -o wide
>
> kubectl get pod xxx -o yaml
>
> kubectl get pod xxx -o json
>

#### （1）查看pod
```sql
[root@master ~]# kubectl get pod greatdb-sample-greatdb-0
NAME                       READY   STATUS    RESTARTS     AGE
greatdb-sample-greatdb-0   3/3     Running   4 (2d ago)   14d

```


#### （2）wide格式查看pod
```sql
[root@master ~]# kubectl get pod greatdb-sample-greatdb-0 -o wide
NAME                       READY   STATUS    RESTARTS     AGE   IP              NODE    NOMINATED NODE   READINESS GATES
greatdb-sample-greatdb-0   3/3     Running   4 (2d ago)   14d   10.144.104.17   node2   <none>           <none>

```

#### （3）yaml格式查看pod
```sql
[root@master ~]# kubectl get pod greatdb-sample-greatdb-0 -o yaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
    cni.projectcalico.org/containerID: 655b98480d1d4b444210114fc102d6aa6f08afb67c9fffc509e6eb0cb1c8da7c
    cni.projectcalico.org/podIP: 10.144.104.17/32
    cni.projectcalico.org/podIPs: 10.144.104.17/32
  creationTimestamp: "2023-10-10T07:02:46Z"
  finalizers:
  - greatdb.com/resources-protection
  labels:
    app.kubernetes.io/component: GreatDB
    app.kubernetes.io/instance: greatdb-sample
    app.kubernetes.io/managed-by: greatdb-operator
    app.kubernetes.io/name: GreatDBPaxos
    app.kubernetes.io/pod-name: greatdb-sample-greatdb-0
    app.kubernetes.io/ready: "true"
    app.kubernetes.io/role: PRIMARY
  name: greatdb-sample-greatdb-0
  namespace: default
  ownerReferences:
  - apiVersion: greatdb.com/v1alpha1
    blockOwnerDeletion: true
    controller: true
    kind: GreatDBPaxos
    name: greatdb-sample
    uid: 5c515db7-bee5-40f9-97f9-119b4796f584
  resourceVersion: "2030183"
  uid: 981f0a72-c560-4d3e-9b75-fb979a9e6f74
spec:
  affinity:
    podAntiAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - podAffinityTerm:
          labelSelector:
            matchLabels:
              app.kubernetes.io/instance: greatdb-sample
              app.kubernetes.io/managed-by: greatdb-operator
              app.kubernetes.io/name: GreatDBPaxos
          topologyKey: kubernetes.io/hostname
        weight: 1
  containers:
  - command:
    - start-greatdb.sh
    env:
    - name: PODNAME
      valueFrom:
        fieldRef:
          apiVersion: v1
          fieldPath: metadata.name
    - name: NAMESPACE
      valueFrom:
        fieldRef:
          apiVersion: v1
          fieldPath: metadata.namespace
    - name: SERVICE_NAME
      value: greatdb-sample-greatdb
    - name: CLUSTERDOMAIN
      value: cluster.local
    - name: FQDN
      value: $(PODNAME).$(SERVICE_NAME).$(NAMESPACE).svc.$(CLUSTERDOMAIN)
    - name: SERVERPORT
      value: "3306"
    - name: GROUPLOCALADDRESS
      value: $(PODNAME).$(SERVICE_NAME).$(NAMESPACE).svc.$(CLUSTERDOMAIN):33061
    envFrom:
    - secretRef:
        name: greatdb-sample-secret
    image: 172.17.136.180/wang/mysql-v8.0.26:latest
    imagePullPolicy: IfNotPresent
    livenessProbe:
      failureThreshold: 3
      httpGet:
        path: /health
        port: 8001
        scheme: HTTP
      initialDelaySeconds: 30
      periodSeconds: 10
      successThreshold: 1
      timeoutSeconds: 3
    name: greatdb
    readinessProbe:
      exec:
        command:
        - ReadinessProbe
      failureThreshold: 3
      initialDelaySeconds: 30
      periodSeconds: 5
      successThreshold: 1
      timeoutSeconds: 2
    resources:
      limits:
        cpu: "2"
        memory: 2Gi
      requests:
        cpu: "2"
        memory: 2Gi
    startupProbe:
      failureThreshold: 20
      periodSeconds: 30
      successThreshold: 1
      tcpSocket:
        port: 3306
      timeoutSeconds: 2
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /etc/greatdb/
      name: config
      readOnly: true
    - mountPath: /greatdb/mysql/
      name: data
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-ttxs6
      readOnly: true
  - command:
    - greatdb-agent
    - --mode=server
    env:
    - name: HOSTNAME
      valueFrom:
        fieldRef:
          apiVersion: v1
          fieldPath: metadata.name
    - name: NAMESPACE
      valueFrom:
        fieldRef:
          apiVersion: v1
          fieldPath: metadata.namespace
    - name: SERVICE_NAME
      value: greatdb-sample-greatdb
    - name: ClusterName
      value: greatdb-sample
    - name: BackupHost
      value: $(HOSTNAME).$(SERVICE_NAME).$(NAMESPACE).svc.cluster.local
    - name: BackupPort
      value: "3306"
    envFrom:
    - secretRef:
        name: greatdb-sample-secret
    image: 172.17.136.180/wang/mysql-v8.0.26:latest
    imagePullPolicy: IfNotPresent
    livenessProbe:
      failureThreshold: 3
      initialDelaySeconds: 15
      periodSeconds: 10
      successThreshold: 1
      tcpSocket:
        port: 19999
      timeoutSeconds: 1
    name: greatdb-agent
    readinessProbe:
      failureThreshold: 3
      httpGet:
        path: /health
        port: 19999
        scheme: HTTP
      initialDelaySeconds: 30
      periodSeconds: 10
      successThreshold: 1
      timeoutSeconds: 1
    resources:
      limits:
        cpu: "2"
        memory: 2Gi
      requests:
        cpu: "1"
        memory: 1Gi
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /greatdb/mysql/
      name: data
    - mountPath: /backup/
      name: backup
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-ttxs6
      readOnly: true
  - command:
    - /usr/bin/promtail
    - -config.expand-env=true
    - -config.file=/etc/promtail/promtail.yaml
    env:
    - name: PODNAME
      valueFrom:
        fieldRef:
          apiVersion: v1
          fieldPath: metadata.name
    - name: NAMESPACE
      valueFrom:
        fieldRef:
          apiVersion: v1
          fieldPath: metadata.namespace
    - name: SERVICE_NAME
      value: greatdb-sample-greatdb
    - name: CLUSTERDOMAIN
      value: cluster.local
    - name: FQDN
      value: $(PODNAME).$(SERVICE_NAME).$(NAMESPACE).svc.$(CLUSTERDOMAIN)
    - name: SERVERPORT
      value: "3306"
    - name: GROUPLOCALADDRESS
      value: $(PODNAME).$(SERVICE_NAME).$(NAMESPACE).svc.$(CLUSTERDOMAIN):33061
    - name: INSTANCE_INTERIOR_PORT
      value: "3306"
    - name: LOKI_CLIENT
      value: http://greatdb-sample-dashboard:8080/log-monitor/loki/api/v1/push
    envFrom:
    - secretRef:
        name: greatdb-sample-secret
    image: 172.17.136.180/wang/promtail-v2.7.4:latest
    imagePullPolicy: IfNotPresent
    livenessProbe:
      failureThreshold: 3
      initialDelaySeconds: 10
      periodSeconds: 10
      successThreshold: 1
      tcpSocket:
        port: 3001
      timeoutSeconds: 1
    name: promtail
    readinessProbe:
      failureThreshold: 3
      initialDelaySeconds: 10
      periodSeconds: 10
      successThreshold: 1
      tcpSocket:
        port: 3001
      timeoutSeconds: 1
    resources:
      limits:
        cpu: 300m
        memory: 300Mi
      requests:
        cpu: 300m
        memory: 300Mi
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /greatdb/mysql/
      name: data
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-ttxs6
      readOnly: true
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
  hostname: greatdb-sample-greatdb-0
  nodeName: node2
  preemptionPolicy: PreemptLowerPriority
  priority: 0
  restartPolicy: Always
  schedulerName: default-scheduler
  securityContext:
    fsGroup: 1000
    runAsUser: 1000
  serviceAccount: default
  serviceAccountName: default
  subdomain: greatdb-sample-greatdb
  terminationGracePeriodSeconds: 30
  tolerations:
  - effect: NoExecute
    key: node.kubernetes.io/not-ready
    operator: Exists
    tolerationSeconds: 300
  - effect: NoExecute
    key: node.kubernetes.io/unreachable
    operator: Exists
    tolerationSeconds: 300
  volumes:
  - configMap:
      defaultMode: 420
      name: greatdb-sample-greatdb
    name: config
  - name: data
    persistentVolumeClaim:
      claimName: greatdb-sample-greatdb-0
  - name: backup
    nfs:
      path: /data/nfs/
      server: 172.17.136.180
  - name: kube-api-access-ttxs6
    projected:
      defaultMode: 420
      sources:
      - serviceAccountToken:
          expirationSeconds: 3607
          path: token
      - configMap:
          items:
          - key: ca.crt
            path: ca.crt
          name: kube-root-ca.crt
      - downwardAPI:
          items:
          - fieldRef:
              apiVersion: v1
              fieldPath: metadata.namespace
            path: namespace
status:
  conditions:
  - lastProbeTime: null
    lastTransitionTime: "2023-10-10T07:02:49Z"
    status: "True"
    type: Initialized
  - lastProbeTime: null
    lastTransitionTime: "2023-10-22T12:51:44Z"
    status: "True"
    type: Ready
  - lastProbeTime: null
    lastTransitionTime: "2023-10-22T12:51:44Z"
    status: "True"
    type: ContainersReady
  - lastProbeTime: null
    lastTransitionTime: "2023-10-10T07:02:49Z"
    status: "True"
    type: PodScheduled
  containerStatuses:
  - containerID: docker://e4eb3aa796a7e2cd410ea4c13d9a566d7aaffdce4cc8b658426e86301181987d
    image: 172.17.136.180/wang/mysql-v8.0.26:latest
    imageID: docker-pullable://172.17.136.180/wang/mysql-v8.0.26@sha256:0b19eeb8fcb0b40585fcf14098107fb0f5b530bdb9725cba279ec63de2222f33
    lastState:
      terminated:
        containerID: docker://787e4996311cda2bdd29a78f8cbb378799f8a28888e7acc9c5501625711e6730
        exitCode: 137
        finishedAt: "2023-10-22T12:51:09Z"
        reason: OOMKilled
        startedAt: "2023-10-18T20:08:30Z"
    name: greatdb
    ready: true
    restartCount: 4
    started: true
    state:
      running:
        startedAt: "2023-10-22T12:51:10Z"
  - containerID: docker://84efc806a97862fbbd274b99bcb7117ad1b5afa6749569482d12003ac177186e
    image: 172.17.136.180/wang/mysql-v8.0.26:latest
    imageID: docker-pullable://172.17.136.180/wang/mysql-v8.0.26@sha256:0b19eeb8fcb0b40585fcf14098107fb0f5b530bdb9725cba279ec63de2222f33
    lastState: {}
    name: greatdb-agent
    ready: true
    restartCount: 0
    started: true
    state:
      running:
        startedAt: "2023-10-10T07:02:50Z"
  - containerID: docker://daf8b58afbec838633cb089b93f33510d774d2845c6722c538a1e0c594a269a4
    image: 172.17.136.180/wang/promtail-v2.7.4:latest
    imageID: docker-pullable://172.17.136.180/wang/promtail-v2.7.4@sha256:1caa3d52d4b5e04cb2a8d616dcaa46bd70328a254954e58c03dd4ff0da1ef014
    lastState: {}
    name: promtail
    ready: true
    restartCount: 0
    started: true
    state:
      running:
        startedAt: "2023-10-10T07:02:51Z"
  hostIP: 172.17.137.37
  phase: Running
  podIP: 10.144.104.17
  podIPs:
  - ip: 10.144.104.17
  qosClass: Burstable
  startTime: "2023-10-10T07:02:49Z"
```

#### （4）json格式查看pod
```sql

[root@master ~]# kubectl get pod greatdb-sample-greatdb-0 -o json
{
    "apiVersion": "v1",
    "kind": "Pod",
    "metadata": {
        "annotations": {
            "cni.projectcalico.org/containerID": "655b98480d1d4b444210114fc102d6aa6f08afb67c9fffc509e6eb0cb1c8da7c",
            "cni.projectcalico.org/podIP": "10.144.104.17/32",
            "cni.projectcalico.org/podIPs": "10.144.104.17/32"
        },
        "creationTimestamp": "2023-10-10T07:02:46Z",
        "finalizers": [
            "greatdb.com/resources-protection"
        ],
        "labels": {
            "app.kubernetes.io/component": "GreatDB",
            "app.kubernetes.io/instance": "greatdb-sample",
            "app.kubernetes.io/managed-by": "greatdb-operator",
            "app.kubernetes.io/name": "GreatDBPaxos",
            "app.kubernetes.io/pod-name": "greatdb-sample-greatdb-0",
            "app.kubernetes.io/ready": "true",
            "app.kubernetes.io/role": "PRIMARY"
        },
        "name": "greatdb-sample-greatdb-0",
        "namespace": "default",
        "ownerReferences": [
            {
                "apiVersion": "greatdb.com/v1alpha1",
                "blockOwnerDeletion": true,
                "controller": true,
                "kind": "GreatDBPaxos",
                "name": "greatdb-sample",
                "uid": "5c515db7-bee5-40f9-97f9-119b4796f584"
            }
        ],
        "resourceVersion": "2030183",
        "uid": "981f0a72-c560-4d3e-9b75-fb979a9e6f74"
    },
    "spec": {
        "affinity": {
            "podAntiAffinity": {
                "preferredDuringSchedulingIgnoredDuringExecution": [
                    {
                        "podAffinityTerm": {
                            "labelSelector": {
                                "matchLabels": {
                                    "app.kubernetes.io/instance": "greatdb-sample",
                                    "app.kubernetes.io/managed-by": "greatdb-operator",
                                    "app.kubernetes.io/name": "GreatDBPaxos"
                                }
                            },
                            "topologyKey": "kubernetes.io/hostname"
                        },
                        "weight": 1
                    }
                ]
            }
        },
        "containers": [
            {
                "command": [
                    "start-greatdb.sh"
                ],
                "env": [
                    {
                        "name": "PODNAME",
                        "valueFrom": {
                            "fieldRef": {
                                "apiVersion": "v1",
                                "fieldPath": "metadata.name"
                            }
                        }
                    },
                    {
                        "name": "NAMESPACE",
                        "valueFrom": {
                            "fieldRef": {
                                "apiVersion": "v1",
                                "fieldPath": "metadata.namespace"
                            }
                        }
                    },
                    {
                        "name": "SERVICE_NAME",
                        "value": "greatdb-sample-greatdb"
                    },
                    {
                        "name": "CLUSTERDOMAIN",
                        "value": "cluster.local"
                    },
                    {
                        "name": "FQDN",
                        "value": "$(PODNAME).$(SERVICE_NAME).$(NAMESPACE).svc.$(CLUSTERDOMAIN)"
                    },
                    {
                        "name": "SERVERPORT",
                        "value": "3306"
                    },
                    {
                        "name": "GROUPLOCALADDRESS",
                        "value": "$(PODNAME).$(SERVICE_NAME).$(NAMESPACE).svc.$(CLUSTERDOMAIN):33061"
                    }
                ],
                "envFrom": [
                    {
                        "secretRef": {
                            "name": "greatdb-sample-secret"
                        }
                    }
                ],
                "image": "172.17.136.180/wang/mysql-v8.0.26:latest",
                "imagePullPolicy": "IfNotPresent",
                "livenessProbe": {
                    "failureThreshold": 3,
                    "httpGet": {
                        "path": "/health",
                        "port": 8001,
                        "scheme": "HTTP"
                    },
                    "initialDelaySeconds": 30,
                    "periodSeconds": 10,
                    "successThreshold": 1,
                    "timeoutSeconds": 3
                },
                "name": "greatdb",
                "readinessProbe": {
                    "exec": {
                        "command": [
                            "ReadinessProbe"
                        ]
                    },
                    "failureThreshold": 3,
                    "initialDelaySeconds": 30,
                    "periodSeconds": 5,
                    "successThreshold": 1,
                    "timeoutSeconds": 2
                },
                "resources": {
                    "limits": {
                        "cpu": "2",
                        "memory": "2Gi"
                    },
                    "requests": {
                        "cpu": "2",
                        "memory": "2Gi"
                    }
                },
                "startupProbe": {
                    "failureThreshold": 20,
                    "periodSeconds": 30,
                    "successThreshold": 1,
                    "tcpSocket": {
                        "port": 3306
                    },
                    "timeoutSeconds": 2
                },
                "terminationMessagePath": "/dev/termination-log",
                "terminationMessagePolicy": "File",
                "volumeMounts": [
                    {
                        "mountPath": "/etc/greatdb/",
                        "name": "config",
                        "readOnly": true
                    },
                    {
                        "mountPath": "/greatdb/mysql/",
                        "name": "data"
                    },
                    {
                        "mountPath": "/var/run/secrets/kubernetes.io/serviceaccount",
                        "name": "kube-api-access-ttxs6",
                        "readOnly": true
                    }
                ]
            },
            {
                "command": [
                    "greatdb-agent",
                    "--mode=server"
                ],
                "env": [
                    {
                        "name": "HOSTNAME",
                        "valueFrom": {
                            "fieldRef": {
                                "apiVersion": "v1",
                                "fieldPath": "metadata.name"
                            }
                        }
                    },
                    {
                        "name": "NAMESPACE",
                        "valueFrom": {
                            "fieldRef": {
                                "apiVersion": "v1",
                                "fieldPath": "metadata.namespace"
                            }
                        }
                    },
                    {
                        "name": "SERVICE_NAME",
                        "value": "greatdb-sample-greatdb"
                    },
                    {
                        "name": "ClusterName",
                        "value": "greatdb-sample"
                    },
                    {
                        "name": "BackupHost",
                        "value": "$(HOSTNAME).$(SERVICE_NAME).$(NAMESPACE).svc.cluster.local"
                    },
                    {
                        "name": "BackupPort",
                        "value": "3306"
                    }
                ],
                "envFrom": [
                    {
                        "secretRef": {
                            "name": "greatdb-sample-secret"
                        }
                    }
                ],
                "image": "172.17.136.180/wang/mysql-v8.0.26:latest",
                "imagePullPolicy": "IfNotPresent",
                "livenessProbe": {
                    "failureThreshold": 3,
                    "initialDelaySeconds": 15,
                    "periodSeconds": 10,
                    "successThreshold": 1,
                    "tcpSocket": {
                        "port": 19999
                    },
                    "timeoutSeconds": 1
                },
                "name": "greatdb-agent",
                "readinessProbe": {
                    "failureThreshold": 3,
                    "httpGet": {
                        "path": "/health",
                        "port": 19999,
                        "scheme": "HTTP"
                    },
                    "initialDelaySeconds": 30,
                    "periodSeconds": 10,
                    "successThreshold": 1,
                    "timeoutSeconds": 1
                },
                "resources": {
                    "limits": {
                        "cpu": "2",
                        "memory": "2Gi"
                    },
                    "requests": {
                        "cpu": "1",
                        "memory": "1Gi"
                    }
                },
                "terminationMessagePath": "/dev/termination-log",
                "terminationMessagePolicy": "File",
                "volumeMounts": [
                    {
                        "mountPath": "/greatdb/mysql/",
                        "name": "data"
                    },
                    {
                        "mountPath": "/backup/",
                        "name": "backup"
                    },
                    {
                        "mountPath": "/var/run/secrets/kubernetes.io/serviceaccount",
                        "name": "kube-api-access-ttxs6",
                        "readOnly": true
                    }
                ]
            },
            {
                "command": [
                    "/usr/bin/promtail",
                    "-config.expand-env=true",
                    "-config.file=/etc/promtail/promtail.yaml"
                ],
                "env": [
                    {
                        "name": "PODNAME",
                        "valueFrom": {
                            "fieldRef": {
                                "apiVersion": "v1",
                                "fieldPath": "metadata.name"
                            }
                        }
                    },
                    {
                        "name": "NAMESPACE",
                        "valueFrom": {
                            "fieldRef": {
                                "apiVersion": "v1",
                                "fieldPath": "metadata.namespace"
                            }
                        }
                    },
                    {
                        "name": "SERVICE_NAME",
                        "value": "greatdb-sample-greatdb"
                    },
                    {
                        "name": "CLUSTERDOMAIN",
                        "value": "cluster.local"
                    },
                    {
                        "name": "FQDN",
                        "value": "$(PODNAME).$(SERVICE_NAME).$(NAMESPACE).svc.$(CLUSTERDOMAIN)"
                    },
                    {
                        "name": "SERVERPORT",
                        "value": "3306"
                    },
                    {
                        "name": "GROUPLOCALADDRESS",
                        "value": "$(PODNAME).$(SERVICE_NAME).$(NAMESPACE).svc.$(CLUSTERDOMAIN):33061"
                    },
                    {
                        "name": "INSTANCE_INTERIOR_PORT",
                        "value": "3306"
                    },
                    {
                        "name": "LOKI_CLIENT",
                        "value": "http://greatdb-sample-dashboard:8080/log-monitor/loki/api/v1/push"
                    }
                ],
                "envFrom": [
                    {
                        "secretRef": {
                            "name": "greatdb-sample-secret"
                        }
                    }
                ],
                "image": "172.17.136.180/wang/promtail-v2.7.4:latest",
                "imagePullPolicy": "IfNotPresent",
                "livenessProbe": {
                    "failureThreshold": 3,
                    "initialDelaySeconds": 10,
                    "periodSeconds": 10,
                    "successThreshold": 1,
                    "tcpSocket": {
                        "port": 3001
                    },
                    "timeoutSeconds": 1
                },
                "name": "promtail",
                "readinessProbe": {
                    "failureThreshold": 3,
                    "initialDelaySeconds": 10,
                    "periodSeconds": 10,
                    "successThreshold": 1,
                    "tcpSocket": {
                        "port": 3001
                    },
                    "timeoutSeconds": 1
                },
                "resources": {
                    "limits": {
                        "cpu": "300m",
                        "memory": "300Mi"
                    },
                    "requests": {
                        "cpu": "300m",
                        "memory": "300Mi"
                    }
                },
                "terminationMessagePath": "/dev/termination-log",
                "terminationMessagePolicy": "File",
                "volumeMounts": [
                    {
                        "mountPath": "/greatdb/mysql/",
                        "name": "data"
                    },
                    {
                        "mountPath": "/var/run/secrets/kubernetes.io/serviceaccount",
                        "name": "kube-api-access-ttxs6",
                        "readOnly": true
                    }
                ]
            }
        ],
        "dnsPolicy": "ClusterFirst",
        "enableServiceLinks": true,
        "hostname": "greatdb-sample-greatdb-0",
        "nodeName": "node2",
        "preemptionPolicy": "PreemptLowerPriority",
        "priority": 0,
        "restartPolicy": "Always",
        "schedulerName": "default-scheduler",
        "securityContext": {
            "fsGroup": 1000,
            "runAsUser": 1000
        },
        "serviceAccount": "default",
        "serviceAccountName": "default",
        "subdomain": "greatdb-sample-greatdb",
        "terminationGracePeriodSeconds": 30,
        "tolerations": [
            {
                "effect": "NoExecute",
                "key": "node.kubernetes.io/not-ready",
                "operator": "Exists",
                "tolerationSeconds": 300
            },
            {
                "effect": "NoExecute",
                "key": "node.kubernetes.io/unreachable",
                "operator": "Exists",
                "tolerationSeconds": 300
            }
        ],
        "volumes": [
            {
                "configMap": {
                    "defaultMode": 420,
                    "name": "greatdb-sample-greatdb"
                },
                "name": "config"
            },
            {
                "name": "data",
                "persistentVolumeClaim": {
                    "claimName": "greatdb-sample-greatdb-0"
                }
            },
            {
                "name": "backup",
                "nfs": {
                    "path": "/data/nfs/",
                    "server": "172.17.136.180"
                }
            },
            {
                "name": "kube-api-access-ttxs6",
                "projected": {
                    "defaultMode": 420,
                    "sources": [
                        {
                            "serviceAccountToken": {
                                "expirationSeconds": 3607,
                                "path": "token"
                            }
                        },
                        {
                            "configMap": {
                                "items": [
                                    {
                                        "key": "ca.crt",
                                        "path": "ca.crt"
                                    }
                                ],
                                "name": "kube-root-ca.crt"
                            }
                        },
                        {
                            "downwardAPI": {
                                "items": [
                                    {
                                        "fieldRef": {
                                            "apiVersion": "v1",
                                            "fieldPath": "metadata.namespace"
                                        },
                                        "path": "namespace"
                                    }
                                ]
                            }
                        }
                    ]
                }
            }
        ]
    },
    "status": {
        "conditions": [
            {
                "lastProbeTime": null,
                "lastTransitionTime": "2023-10-10T07:02:49Z",
                "status": "True",
                "type": "Initialized"
            },
            {
                "lastProbeTime": null,
                "lastTransitionTime": "2023-10-22T12:51:44Z",
                "status": "True",
                "type": "Ready"
            },
            {
                "lastProbeTime": null,
                "lastTransitionTime": "2023-10-22T12:51:44Z",
                "status": "True",
                "type": "ContainersReady"
            },
            {
                "lastProbeTime": null,
                "lastTransitionTime": "2023-10-10T07:02:49Z",
                "status": "True",
                "type": "PodScheduled"
            }
        ],
        "containerStatuses": [
            {
                "containerID": "docker://e4eb3aa796a7e2cd410ea4c13d9a566d7aaffdce4cc8b658426e86301181987d",
                "image": "172.17.136.180/wang/mysql-v8.0.26:latest",
                "imageID": "docker-pullable://172.17.136.180/wang/mysql-v8.0.26@sha256:0b19eeb8fcb0b40585fcf14098107fb0f5b530bdb9725cba279ec63de2222f33",
                "lastState": {
                    "terminated": {
                        "containerID": "docker://787e4996311cda2bdd29a78f8cbb378799f8a28888e7acc9c5501625711e6730",
                        "exitCode": 137,
                        "finishedAt": "2023-10-22T12:51:09Z",
                        "reason": "OOMKilled",
                        "startedAt": "2023-10-18T20:08:30Z"
                    }
                },
                "name": "greatdb",
                "ready": true,
                "restartCount": 4,
                "started": true,
                "state": {
                    "running": {
                        "startedAt": "2023-10-22T12:51:10Z"
                    }
                }
            },
            {
                "containerID": "docker://84efc806a97862fbbd274b99bcb7117ad1b5afa6749569482d12003ac177186e",
                "image": "172.17.136.180/wang/mysql-v8.0.26:latest",
                "imageID": "docker-pullable://172.17.136.180/wang/mysql-v8.0.26@sha256:0b19eeb8fcb0b40585fcf14098107fb0f5b530bdb9725cba279ec63de2222f33",
                "lastState": {},
                "name": "greatdb-agent",
                "ready": true,
                "restartCount": 0,
                "started": true,
                "state": {
                    "running": {
                        "startedAt": "2023-10-10T07:02:50Z"
                    }
                }
            },
            {
                "containerID": "docker://daf8b58afbec838633cb089b93f33510d774d2845c6722c538a1e0c594a269a4",
                "image": "172.17.136.180/wang/promtail-v2.7.4:latest",
                "imageID": "docker-pullable://172.17.136.180/wang/promtail-v2.7.4@sha256:1caa3d52d4b5e04cb2a8d616dcaa46bd70328a254954e58c03dd4ff0da1ef014",
                "lastState": {},
                "name": "promtail",
                "ready": true,
                "restartCount": 0,
                "started": true,
                "state": {
                    "running": {
                        "startedAt": "2023-10-10T07:02:51Z"
                    }
                }
            }
        ],
        "hostIP": "172.17.137.37",
        "phase": "Running",
        "podIP": "10.144.104.17",
        "podIPs": [
            {
                "ip": "10.144.104.17"
            }
        ],
        "qosClass": "Burstable",
        "startTime": "2023-10-10T07:02:49Z"
    }
}
[root@master ~]#


```


### 查看pod环境变量
> kubectl exec podName env

```sql
[root@master ~]# kubectl exec greatdb-sample-greatdb-0 env
kubectl exec [POD] [COMMAND] is DEPRECATED and will be removed in a future version. Use kubectl exec [POD] -- [COMMAND] instead.
Defaulted container "greatdb" out of: greatdb, greatdb-agent, promtail
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/opt/greatdb/bin
HOSTNAME=greatdb-sample-greatdb-0
ClusterUserPassword=c76cde07577e3ab9
PODNAME=greatdb-sample-greatdb-0
NAMESPACE=default
FQDN=greatdb-sample-greatdb-0.greatdb-sample-greatdb.default.svc.cluster.local
SERVERPORT=3306
GROUPLOCALADDRESS=greatdb-sample-greatdb-0.greatdb-sample-greatdb.default.svc.cluster.local:33061
ClusterUser=greatdb
ROOTPASSWORD=greatdb@root
SERVICE_NAME=greatdb-sample-greatdb
CLUSTERDOMAIN=cluster.local
GREATDB_SAMPLE_DASHBOARD_PORT_8080_TCP=tcp://10.103.37.22:8080
GREATDB_SAMPLE_DASHBOARD_PORT_8080_TCP_PORT=8080
KUBERNETES_SERVICE_PORT=443
GREATDB_SAMPLE_READ_SERVICE_PORT_CLIENT=3306
GREATDB_SAMPLE_DASHBOARD_SERVICE_HOST=10.103.37.22
GREATDB_SAMPLE_DASHBOARD_PORT=tcp://10.103.37.22:8080
GREATDB_SAMPLE_DASHBOARD_PORT_8080_TCP_ADDR=10.103.37.22
GREATDB_SAMPLE_READ_SERVICE_PORT=3306
GREATDB_SAMPLE_WRITE_PORT_3306_TCP_PORT=3306
GREATDB_SAMPLE_WRITE_PORT_3306_TCP_ADDR=10.100.30.54
GREATDB_SAMPLE_DASHBOARD_SERVICE_PORT=8080
GREATDB_SAMPLE_READ_PORT_3306_TCP_PROTO=tcp
GREATDB_SAMPLE_READ_PORT_3306_TCP_ADDR=10.99.171.224
GREATDB_SAMPLE_WRITE_SERVICE_HOST=10.100.30.54
GREATDB_SAMPLE_WRITE_SERVICE_PORT=3306
GREATDB_SAMPLE_WRITE_SERVICE_PORT_CLIENT=3306
GREATDB_SAMPLE_DASHBOARD_PORT_8080_TCP_PROTO=tcp
KUBERNETES_SERVICE_HOST=10.96.0.1
GREATDB_SAMPLE_READ_SERVICE_HOST=10.99.171.224
GREATDB_SAMPLE_WRITE_PORT_3306_TCP=tcp://10.100.30.54:3306
GREATDB_SAMPLE_WRITE_PORT_3306_TCP_PROTO=tcp
GREATDB_SAMPLE_DASHBOARD_SERVICE_PORT_CLIENT=8080
KUBERNETES_PORT=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
GREATDB_SAMPLE_READ_PORT_3306_TCP=tcp://10.99.171.224:3306
GREATDB_SAMPLE_WRITE_PORT=tcp://10.100.30.54:3306
GREATDB_SAMPLE_READ_PORT=tcp://10.99.171.224:3306
GREATDB_SAMPLE_READ_PORT_3306_TCP_PORT=3306
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_PORT=443
HOME=/home/greatdb
```



