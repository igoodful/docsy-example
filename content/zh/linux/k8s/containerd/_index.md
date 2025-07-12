---
title: containerd
description:  containerd
weight: 10
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}

{{< /alert >}}



```viz-dot
digraph "install" {
rankdir=LR;
edge [
        fontsize="12"
];
node [
        shape = octagon;
        fontsize = "12"
];


"install" -> "containerd";
"install" -> "runc";
"install" -> "cni";

}
```


|       |   |              |
|:------|:--|:-------------|
| `CNI` |   | 容器网络接口 |
| `CRI` |   |              |
| `CDI` |   |              |
|       |   |              |
|       |   |              |


#### CDI




#### CNI

```viz-dot
digraph "CNI" {
rankdir=LR;
edge [
        fontsize="12"
];
node [
        shape = octagon;
        fontsize = "12"
];

"CNI" -> "Calico";
"CNI" -> "Flannel";
"CNI" -> "Weave Net";
"CNI" -> "Bridge";
"CNI" -> "IPVlan";
"CNI" -> "KubeOVN";
"CNI" -> "Kube-router";
"CNI" -> "Canal";
"CNI" -> "Cilium";


}
```

CNI（Container Network Interface）插件是独立的可执行文件，遵循 CNI 规范。Kubernetes 通过 kubelet 调用这些插件来创建和管理容器的网络接口。

CNI 插件的主要职责包括网络接口的创建和删除、IP 地址的分配和回收、以及相关网络资源的配置和清理。

当一个新的 Pod 被创建时，kubelet 会调用 CNI 插件的 ADD 命令。CNI 插件会为 Pod 分配一个网络接口，并设置相关的网络配置，如 IP 地址和路由。这包括配置 Pod 的网络命名空间，使其能够与其他 Pod 进行通信。

当 Pod 被删除时，kubelet 会调用 CNI 插件的 DEL 命令。CNI 插件会清理之前为该 Pod 分配的网络资源，如回收 IP 地址和删除网络接口。这一过程确保资源不会被浪费，并且系统能够持续高效地运行。






#### CRI



#### CDI
#### CDI
#### CDI













