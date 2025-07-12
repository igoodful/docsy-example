---
title: 关闭k8s
date: 2023-10-12
weight: 40000
description: >
  关闭k8s


---

{{< alert >}}


{{< /alert >}}




## 关闭

```sql

systemctl stop kubelet

systemctl disable  kubelet

systemctl stop  containerd

systemctl disable  containerd



```