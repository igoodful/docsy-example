---
title: perf
description: perf
date: 2023-10-26
weight: 20000


viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;

{{<alert color="danger" title="注意" >}}

- [[processlist](https://github.com/akopytov/sysbench)](https://github.com/akopytov/sysbench)
- [https://percona.community/blog/2020/02/05/finding-mysql-scaling-problems-using-perf/](https://percona.community/blog/2020/02/05/finding-mysql-scaling-problems-using-perf/)
- [https://www.percona.com/blog/profiling-software-using-perf-and-flame-graphs/](https://www.percona.com/blog/profiling-software-using-perf-and-flame-graphs/)

{{</alert>}}





## 一、安装

```bash
yum -y install epel-release

yum -y install sysbench

sysbench --version

sysbench /usr/share/sysbench/oltp_read_only.lua --help
```





| perf record   | 功能                          | 默认值   |
| :------------ | :---------------------------- | :------- |
| `-p`          | 待分析进程id                  |          |
| `-t`          | 待分析线程id                  |          |
| `-F`          | 每秒采样次数                  | `4000`   |
| `-c`          | 事件的采样间隔                |          |
| `-g`          | 捕获函数调用栈                |          |
| `-C/--cpu`    | 只采集指定CPU数据             |          |
| `-e`          | 选择性能事件(`cpu-clock`)     | `cycles` |
| `-a`          | 分析整个系统的性能            |          |
| `-o/--output` | 指定输出文件，默认为perf.data |          |
| `-A`          | 以append的方式写输出文件      |          |
| `-f`          | 以OverWrite的方式写输出文件   |          |


| perf report  | 功能                                           | 功能 |
| :----------- | :--------------------------------------------- | :--- |
| `-i/--input` | 输入的数据文件                                 |      |
| `-v`         | 显示每个符号的地址                             |      |
| `-c`         | 只显示指定cpu采样信息                          |      |
| `-source`    | 以汇编和source的形式进行显示                   |      |
| `-p`         | 用指定正则表达式过滤调用函数                   |      |
| `-M`         | 以指定汇编指令风格显示                         |      |
| `-g`         | 显示调用关系，具体等同于perf top命令中的-g     |      |
| `-U`         | 只显示已解析的符号                             |      |
| `-S`         | 只考虑指定符号                                 |      |
| `-C`         | 只显示指定comm的信息（Comm. 触发事件的进程名） |      |
| `-d`         | 只显示指定dos的符号                            |      |





## 使用


并得到调用栈中各个环节的cpu消耗
```bash
perf record -e cycles -F 100 --no-children -o mysqld.perf  -p $(pidof mysqld)  -- sleep 20;

perf record -g -e cycles -F 100 --no-children -o mysqld.perf  -p $(pidof mysqld)  -- sleep 20;

perf stat -B -e cache-references,cache-misses,cycles,instructions,branches,faults,migrations -p $(pidof mysqld) sleep 20 2>&1  | tee perf-stats.txt

perf record -a -F 99 -g -p pidof mysqld – sleep 10

timeout 60s perf record --call-graph dwarf  -F 100 -o mysqld.perf -p 26573

```


只有 dwarf 获取到了正确的调用栈
1. `--call-graph fp = -g`

2. `--call-graph lbr`

3. `--call-graph dwarf`


```bash
perf report --input  mysqld.perf

perf report --input  mysqld.perf --stdio

perf report -g --no-children --input  mysqld.perf


```





