---
title: 禁用 numa
date: 2023-10-12
description: >
  内存分配器版本问题.


weight: 60000
---

{{< alert >}}


{{< /alert >}}

### 安装numa
```bash
[root@k8s-node-76 ~]# yum install numactl -y
```

### 查看numa

```bash
# 已经关掉了
[root@k8s-node-76 ~]# numactl --hardware
available: 1 nodes (0)
node 0 cpus: 0 1 2 3
node 0 size: 7820 MB
node 0 free: 1020 MB
node distances:
node   0
  0:  10
# 如果available: 1 nodes (0)中不是1 nodes，而是2或多个，则表示未关闭numa

```



### 关闭numa

```bash
[root@k8s-node-76 ~]# vi /etc/default/grub

GRUB_CMDLINE_LINUX="crashkernel=auto rhgb quiet numa=off"

[root@k8s-node-76 ~]#  grub2-mkconfig -o /boot/grub2/grub.cfg
[[root@k8s-node-76 ~]#  grub2-mkconfig -o /etc/grub2-efi.cfg
grub2-mkconfig -o /etc/grub2.cfg
numastat
numactl --show
numactl --hardware
shutdown -r now


```

### 重建grub 配置文件

>
> 若为MBR分区表，可用：grub2-mkconfig -o /etc/grub2.cfg
>
> 若为efi + GPT分区表，可用：grub2-mkconfig -o /etc/grub2-efi.cfg


































