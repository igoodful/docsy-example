---
title: 关闭透明大页
date: 2023-10-12
description: 内存分配器版本问题.
weight: 60000
---

{{< alert >}}
This is a placeholder page. Replace it with your own content.
{{< /alert >}}



## 查看透明大页

```bash
# 检查透明大页是否开启
# [always] madvise never表示透明大页处于启用状态
# always madvise [never]表示透明大页处于关闭状态
[root@k8s-node-76 ~]# cat /sys/kernel/mm/transparent_hugepage/enabled
[always] madvise never


# AnonHugePages 不为0 KB 就表示开启了透明大页
[root@k8s-node-76 ~]# grep Huge  /proc/meminfo
AnonHugePages:   4198400 kB
ShmemHugePages:        0 kB
FileHugePages:         0 kB
HugePages_Total:       0
HugePages_Free:        0
HugePages_Rsvd:        0
HugePages_Surp:        0
Hugepagesize:       2048 kB
Hugetlb:               0 kB


# 如果处于启用状态，联系SA修改或者使用如下方法：
[mysql@node193 zaibei]$ cat /etc/default/grub
[mysql@node193 zaibei]$ cp /etc/default/grub /tmp/grub0802
[mysql@node193 zaibei]$ vi /etc/default/grub
## add
GRUB_CMDLINE_LINUX="transparent_hugepage=never"

```



## 关闭透明大页

```bash
# 临时关闭
echo never > /sys/kernel/mm/transparent_hugepage/enabled

echo 'never' | sudo tee /sys/kernel/mm/transparent_hugepage/defrag
echo 'never' | sudo tee /sys/kernel/mm/transparent_hugepage/enabled


## 永久关闭
[mysql@node193 zaibei]$ vim /etc/default/grub
# 修改位置
GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=centos/root rd.lvm.lv=centos/swap rhgb quiet numa=off"
# 修改后
GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=centos/root rd.lvm.lv=centos/swap rhgb quiet numa=off transparent_hugepage=never"

# 执行新的grub文件生成:grub2-mkconfig
[mysql@node193 zaibei]$ grub2-mkconfig
# 执行生效命令
[mysql@node193 zaibei]$ grub2-mkconfig -o /boot/grub2/grub.cfg
[root@gip ~]# grub2-mkconfig -o /boot/grub2/grub.cfg
[root@gip ~]# grub2-mkconfig -o /etc/grub2-efi.cfg
```
































