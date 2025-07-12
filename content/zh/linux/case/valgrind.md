---
title: valgrind
description: 内存泄露检测
date: 2023-11-05
weight: 30000


---
{{< note>}}
<!---->
[官网](http://www.valgrind.org)

标配了许多有用的工具：
1. Memcheck 是内存错误检测器，valgrind 的默认工具。它可以检测以下和内存相关的问题：
   - 使用未初始化的内存

   - 读取/写入已释放的内存

   - 读取/写入 malloc 块的末端

   - 内存泄漏

   - 对malloc/new/new[]与free/delete/delete[] 的不匹配使用

   - 双重释放内存

2. Cachegrind 是一个缓存和分支预测探查器。

3. Callgrind 是一个生成调用图的缓存分析器。

4. Helgrind 是线程错误检测器。

5. DRD 还是线程错误检测器。

6. Massif 是堆分析器。

7. DHAT 是另一种堆分析器。

8. SGcheck 是一种实验性工具，可以检测堆栈和全局阵列的溢出。

9.  BBV 是一个实验性 SimPoint 基本块矢量生成器。








{{< /note >}}


### 安装
```shell
tar xvf valgrind-3.11.1.tar.bz2

cd valgrind-3.11.1

./configure --prefix=/usr/local/valgrind

make && make install


```

Valgrind Memcheck 工具的用法如下：
```shell
valgrind --tool=memcheck ./a.out

```
从上面的命令可以清楚地看到，主要的命令是“ Valgrind”，而我们要使用的工具由选项“ –tool”指定。上面的“ a.out ” 表示我们要在其上运行 memcheck 的可执行文件。此外还可以使用其他的命令行选项，以满足我们的需要。运行的程序结束后，会生成这个进程的内存分析报告。



使用 valgrind 的 memcheck 工具启动 mysql：
```shell
valgrind --tool=memcheck \
--leak-check=full \
--show-reachable=yes \
--log-file=/tmp/valgrind-mysql.log \
/usr/local/mysql/bin/mysqld \
--defaults-file=/etc/my.cnf \
--user=root

```

 利用 sysbench 模拟负载；







