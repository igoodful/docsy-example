---
title: expr
description: expr
date: 2025-04-01
weight: 30000
viz: true

---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


{{<alert>}}


{{</alert>}}



### expr


eval 属 Shell 内建命令，通过连接参数构造命令。

使用空格分隔每个参数，构造的命令应由 Shell 读取和执行。

eval 会对后面的命令进行两遍扫描。如果第一遍扫描后，命令是个普通命令，则执行此命令；如果命令中含有变量的间接引用，则保证间接引用的语义。也就是说，eval 命令将会首先扫描命令行进行所有的置换，然后再执行该命令。

主要是每次执行一个shell命令它会先检察一次，看到有`$`标志就会把值替换一次，然后再执行一遍。eval不会唤起起另一个shell来执行，而是在本身这个shell内多解析一次，所以替换的结果可以保留下来使用。

```bash
for i in `seq 1 $(cat /proc/cpuinfo |grep "physical id" |wc -l)`; do dd if=/dev/zero of=/dev/null & done
```

**上述程序的结束**：

```bash
fg 后按 ctrl + C (因为该命令是放在后台执行)
```

```bash
pkill -9 dd
```














