---
title: debugfs
description: 恢复误删的文件
date: 2023-10-31
weight: 20000


---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

{{< /alert >}}


### 文件名称
{{< alert title="-name" color="secondary">}}
**-name -iname**：
- d: 目录
- c: 字型装置档案
- b: 区块装置档案
- p: 具名贮列
- f: 普通文件
- l: 符号连结
- s: socket

{{< /alert >}}





## 卸载分区
发现误删除文件后，立马把误删除文件所在的分区设置成只读状态，或者卸载掉该分区,让分区没有写操作
```bash
umount /data/
```














