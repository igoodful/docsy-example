---
title: dd
description: dd
date: 2023-10-31
weight: 20000
viz: true

---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


{{< alert title="sort参数详解" color="secondary">}}

{{< /alert >}}

> 只针对公司英文名称的第二个字母进行排序，如果相同的按照员工工资进行降序排序：


#### 制作u盘启动盘

```bash

wget https://releases.ubuntu.com/focal/ubuntu-20.04.4-live-server-amd64.iso

dd if=ubuntu-20.04.4-live-server-amd64.iso of=/dev/sdb bs=4M status=progress

fdisk -l

lsblk

sync


```


