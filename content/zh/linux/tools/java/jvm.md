---
title: jvm
description: jvm
date: 2023-11-17
weight: 30000


---

{{< alert >}}


{{< /alert >}}

## JVM崩溃Log日志分析

Java的应用有时候会因为各种原因Crash，这时候会产生一个类似java_errorpid.log的错误日志。可以拿到了

这个日志，怎样分析Crash的原因呢？下面我们来详细讨论如何分析java_errorpid.log的错误日志。

一. 如何得到这个日志文件

如果有一个严重的错误引起Java进程非正常退出，我们叫Crash，这时候会产生一个日志文件。

- 缺省情况下，这个文件会产生在工作目录下
- 但是，可以在Java启动参数通过下面的设置，来改变这个文件的位置和命名规则：`java -XX:ErrorFile=/var/log/java/java_error_%p.log`




















