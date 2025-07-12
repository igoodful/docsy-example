---
title: secret
date: 2023-10-24
weight: 30000
description: >
  secret


---

{{< alert >}}

### Secret 和 ConfigMap这两种资源对象的异同点：

#### 相同点：
1. key/value的形式

2. 属于某个特定的命名空间

3. 可以导出到环境变量

4. 可以通过目录/文件形式挂载

5. 通过 volume 挂载的配置信息均可热更新
#### 不同点
1. Secret 可以被 ServerAccount 关联

2. Secret 可以存储 docker register 的鉴权信息，用在 ImagePullSecret 参数中，用于拉取私有仓库的镜像

3. Secret 支持 Base64 加密

4. Secret 分为 kubernetes.io/service-account-token、kubernetes.io/dockerconfigjson、Opaque 三种类型，而 Configmap 不区分类型


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