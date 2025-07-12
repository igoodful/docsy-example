---
title: 创建卷组
description: 创建卷组
date: 2023-11-08
weight: 30000


---

{{< alert >}}

[新增一块硬盘后，将其加入系统](https://help.aliyun.com/zh/ecs/use-cases/use-lvm-to-create-a-logical-volume)

{{< /alert >}}


新增一块硬盘后，使用fdisk -l命令，我们可以看到这块新增的硬盘被标记为/dev/sdb

## 一、创建

### 1. 查看磁盘
获得新增的磁盘：/dev/sdb
```bash
df -Th

fdisk -l
```



### 2. 创建分区
新建的分区为主分区或逻辑分区均可

```bash
fdisk /dev/sdb
n

w
```

### 3. 创建：PV
创建多个物理卷

物理卷(PV)是 LVM 要使用的分区或整个磁盘。要将设备用于 LVM 逻辑卷，必须将设备初始化为物理卷。

如果您将整个磁盘作为您的物理卷使用，那么磁盘就不能有分区表。对于 DOS 磁盘分区，应该使用 fdisk 或 cfdisk 命令或对等命令将分区 id 设置为 0x8e。如果您将整个磁盘作为您的物理卷使用，那么磁盘就不能有分区表。任何现有分区表都必须被清除，这样将有效销毁该磁盘上的所有数据。您可以使用 wipefs -a <PhysicalVolume>` 命令删除现有分区表，作为 root 用户。

```bash
pvscan

pvcreate /dev/vdb1 /dev/vdb2 /dev/vdb3

pvscan

pvdisplay

pvs

# 删除物理卷
pvremove /dev/vdb3

```

### 4. 创建卷组：VG
创建卷组

```bash
vgdisplay

vgcreate vg01 /dev/sdb1 /dev/sdc1

vgdisplay

vgscan

vgs

# 在卷组中添加新的物理卷
vgextend vg01 /dev/sdd1 /dev/sde1



```

### 5. 创建逻辑卷：LV

`lvcreate -L <逻辑卷大小> -n <逻辑卷名称> <卷组名称>`


```bash
lvscan

lvcreate -L1024G -n lv01 vg01

lvscan

lvs

lvdisplay
```

### 6. 创建：文件系统

```bash
mkfs -t xfs /dev/vg01/lv01

mkfs.xfs /dev/vg01/lv01
```

### 7. 挂载

```bash
mkdir -p /home/data

mount /dev/vg01/lv01 /home/data


```

### 8. 写入：/etc/fstab
开机自动挂载



```bash
sh -c "echo `blkid /dev/vg01/lv01 | awk '{print $2}' | sed 's/\"//g'` /home/data xfs defaults 0 0 >> /etc/fstab"



```


## 二、卸载

```bash
vgscan

vgdisplay -v vg001

umount /dev/vg001/lv001

lvremove /dev/vg001/lv001

vgchange -a n vg001

vgremove vg001

vgscan

pvremove /dev/sdb1

sed -i '/lv001/d' /etc/fstab
```

{{<alert>}}


{{</alert>}}




```bash
1、lvremove /dev/gipb/datab

2、vgremove gipb

3、fdisk /dev/sdb
输入（创建新分区） ：n；
输入（主扇区）：p
输入（1）： 1 #起始扇区
输入回车：取默认值
输入回车：取默认值
输入：w 保存退出

4、parted /dev/sdb
输入：mklabel msdos
输入： Yes
输入：quit

5、mkfs.ext4 /dev/sdb
Proceed anyway？（y，n）y

按照上面的流程重新把磁盘建一下再试试。
```








