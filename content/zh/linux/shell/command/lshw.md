---
title: lshw
description: lshw
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
[greatdb@db4 /data1] $ sudo lshw -class disk
  *-disk:0                  
       description: SCSI Disk
       product: QEMU HARDDISK
       vendor: QEMU
       physical id: 0.0.0
       bus info: scsi@0:0.0.0
       logical name: /dev/sda
       version: 2.5+
       serial: 0f6ad4cba200a6ba919f
       size: 160GiB (171GB)
       capabilities: 5400rpm gpt-1.00 partitioned partitioned:gpt
       configuration: ansiversion=5 guid=e89f0f02-8731-44ef-b269-c12b991d1e2c logicalsectorsize=512 sectorsize=512
  *-disk:1
       description: SCSI Disk
       product: QEMU HARDDISK
       vendor: QEMU
       physical id: 0.1.0
       bus info: scsi@0:0.1.0
       logical name: /dev/sdb
       version: 2.5+
       serial: f00dd722a9a89dbef975
       size: 800GiB (858GB)
       capabilities: 5400rpm gpt-1.00 partitioned partitioned:gpt
       configuration: ansiversion=5 guid=2bcf22f7-742b-4bc4-938e-32f4b3584b41 logicalsectorsize=512 sectorsize=512
  *-cdrom
       description: DVD reader
       product: QEMU CD-ROM
       vendor: QEMU
       physical id: 0.2.0
       bus info: scsi@0:0.2.0
       logical name: /dev/cdrom
       logical name: /dev/sr0
       version: 2.5+
       serial: 7adda9b0700beb248ea7
       capabilities: removable audio dvd
       configuration: ansiversion=5 status=nodisc
  *-disk:2
       description: SCSI Disk
       product: QEMU HARDDISK
       vendor: QEMU
       physical id: 0.3.0
       bus info: scsi@0:0.3.0
       logical name: /dev/sdc
       version: 2.5+
       serial: f69db328049f7cf0c622
       size: 2TiB (2199GB)
       capabilities: 5400rpm partitioned partitioned:dos
       configuration: ansiversion=5 logicalsectorsize=512 sectorsize=512 signature=2b945e77



```


