---
title: 01. 安装
description: install
date: 2023-10-12
weight: 100


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



## CoreDump

#### CoreDump简单概念
CoreDump即核心转储，是程序运行异常崩溃时，系统内核为该程序产生的内存、寄存器、运行栈等快照，并保存为一个二进制文件，可以利用该文件进行GDB调试，发现运行错误。

产生CoreDump文件
查看系统是否已经开启了该功能:
```bash
ulimit -c
0
```
上述输出结果为0说明当前系统已经关闭了该功能，所以需要打开该功能:

临时启用
```bash
ulimit -c unlimited
ulimit -c
```
unlimited
永久启用

在/etc/security/limits.conf添加一行:
```yaml
*       soft    core    unlimited
```

修改文件格式，例如：
```yaml
echo "core.%e.%p.%t" >/proc/sys/kernel/core_pattern
or
echo "/home/dan/mycore/core.%e.%p.%t" >/proc/sys/kernel/core_pattern

```
调试CoreDump文件
#include <stdio.h>
#include <unistd.h>

int main()
{
    int* p = NULL;
    printf("here");
    *p = 7;
    sleep(1000);
    return 0;
}
运行上述程序就会产生core dump 文件，如果修了core dump文件的位置就需要加上绝对地址






















