---
title: config.log
description: config.log
date: 2023-11-01
weight: 30000
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{<alert>}}

记录 configure 执行过程中的 详细日志

{{</alert>}}



### 1. 简介

记录 configure 执行过程中的 详细日志，包括: 调用的命令（如编译器测试）。错误和警告信息。环境变量（如 PATH、CC）。



```bash


```


## 2. 选项


```bash



```



| 序号                   | 步骤                              | 原理                                                                                                  | 备注      |
|:---------------------|:----------------------------------|:------------------------------------------------------------------------------------------------------|:----------|
| 二进制目录             | `ZOOBINDIR`                       | 若ZOOBINDIR没有定义或为空，则默认/usr/bin                                                              |           |
| 安装根目录             | `ZOOKEEPER_PREFIX`                | `${ZOOBINDIR}/..`                                                                                     |           |
| 配置目录               | `ZOOCFGDIR`                       | `--config /path/to/config`》自定义`ZOOCFGDIR`》`${ZOOKEEPER_PREFIX}/conf`》`$ZOOBINDIR/../etc/zookeeper` |           |
| 加载自定义环境变量     | `. ${ZOOCFGDIR}/zookeeper-env.sh` | `. "${ZOOCFGDIR}/zookeeper-env.sh"`                                                                   |           |
| zk配置文件             | `ZOOCFG`                          | 自定义`ZOOCFG`》`zoo.cfg`，绝对路径为：`$ZOOCFGDIR/$ZOOCFG`                                              |           |
| 加载Java环境变量       | `. "$ZOOCFGDIR/java.env"`         |                                                                                                       |           |
| 日志目录               | `ZOO_LOG_DIR`                     | 自定义`ZOO_LOG_DIR`》`$ZOOKEEPER_PREFIX/logs`                                                          |           |
| 日志级别与日志轮转方式 | `ZOO_LOG4J_PROP`                  | 自定义`ZOO_LOG4J_PROP`》`INFO,CONSOLE`                                                                 |           |
| 确定Java可执行文件路径 | `JAVA_HOME`                       | `$JAVA_HOME/bin/java`》`type -p java`》报错                                                             |           |
| 设置类路径：CLASSPATH   | `CLASSPATH`                       | 新增`$ZOOBINDIR"/../zookeeper-server/src/main/resources/lib`下面所有的jar包》新增`$ZOOCFGDIR`          |           |
| 设置类路径：LIBPATH     | `LIBPATH`                         |                                                                                                       |           |
| 设置JVM参数            | `ZK_SERVER_HEAP`                  | 自定义`ZK_SERVER_HEAP`》`1000`                                                                         | 默认1000M |
| 设置JVM参数            | `SERVER_JVMFLAGS`                 | 新增`-Xmx${ZK_SERVER_HEAP}m`                                                                          |           |
| 设置JVM参数            | `ZK_CLIENT_HEAP`                  | 自定义`ZK_SERVER_HEAP`》`256`                                                                          | 默认256M  |
| 设置JVM参数            | `CLIENT_JVMFLAGS`                 | 新增`-Xmx${ZK_CLIENT_HEAP}m`                                                                          |           |













## config.sub


```bash

```












