---
title:  /proc/pid
description:  /proc/pid
date: 2025-02-19
weight: 300000
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

{{< /alert >}}


{{< alert title="/proc/pid" color="secondary">}}

{{< /alert >}}



## 一、原理



| 选项                | 作用                           | 示例     |
| :------------------ | :----------------------------- | :------- |
| `/proc/pid/cmdline` | `进程的完整命令行信息`         | ``       |
| `/proc/pid/exe`     | `实际运行程序的符号链接`       | ``       |
| `/proc/pid/cwd`     | `进程当前工作目录的符号链接`   | ``       |
| `/proc/pid/environ` | `进程环境变量列表`             | ``       |
| `/proc/pid/fd/`     | `进程打开文件的情况`           | ``       |
| `/proc/pid/mem`     | `进程持有的内存`               | `不可读` |
| `/proc/pid/stat`    | `进程所有CPU活跃的信息`        | ``       |
| `/proc/pid/statm`   | `进程所占用内存大小的统计信息` | ``       |
| `/proc/pid/status`  | `所有CPU活跃的信息`            | ``       |
| `/proc/pid/root`    | `进程根目录的符号链接`         | ``       |
| `/proc/pid/comm`    | `进程的命令名`                 | ``       |
| `/proc/pid/limits`  | `当前进程的资源限制`           | ``       |
| `/proc/pid/stack`   | `当前进程的内核调用栈信息`     | ``       |


## /proc/pid/statm

/proc/[pid]/statm显示进程所占用内存大小的统计信息，包含七个值，度量单位是page（page大小可通过getconf PAGESIZE得到）。举例如下

各个值含义：

a）进程占用的总的内存；

b）进程当前时刻占用的物理内存；

c）同其它进程共享的内存；

d）进程的代码段；

e）共享库（从2.6版本起，这个值为0）；

f）进程的堆栈；

g）dirty pages（从2.6版本起，这个值为0）。

```bash
2025-03-15T14:47:36 [root@x86centos7-95 /proc/9106] # getconf PAGESIZE
4096
2025-03-15T14:51:56 [root@x86centos7-95 /proc/9106] # cat statm
2018051 425427 4477 13361 0 1995554 0

```




## /proc/pid/environ

```bash
2025-03-15T14:44:32 [root@x86centos7-95 /proc/9106] # ps aux|grep zook
mysql      603  0.1  1.2 4644484 102164 ?      Sl   Mar13   4:22 java -Dzookeeper.log.dir=/data/zookeeperdata/log -Dzookeeper.DigestAuthenticationProvider.superDigest=su:gACzJ4L2A0F2ygTno5HQnfabuik= -Dzookeeper.log.file=zookeeper-mysql-server-x86centos7-95.log -Dzookeeper.root.logger=DEBU,DAYROLLINGAppender -XX:+HeapDumpOnOutOfMemoryError -XX:OnOutOfMemoryError=kill -9 %p -cp /data/app/zookeeper-3.6.2/bin/../zookeeper-server/target/classes:/data/app/zookeeper-3.6.2/bin/../build/classes:/data/app/zookeeper-3.6.2/bin/../zookeeper-server/target/lib/*.jar:/data/app/zookeeper-3.6.2/bin/../build/lib/*.jar:/data/app/zookeeper-3.6.2/bin/../lib/zookeeper-prometheus-metrics-3.6.2.jar:/data/app/zookeeper-3.6.2/bin/../lib/zookeeper-jute-3.6.2.jar:/data/app/zookeeper-3.6.2/bin/../lib/zookeeper-3.6.2.jar:/data/app/zookeeper-3.6.2/bin/../lib/snappy-java-1.1.7.jar:/data/app/zookeeper-3.6.2/bin/../lib/slf4j-log4j12-1.7.25.jar:/data/app/zookeeper-3.6.2/bin/../lib/slf4j-api-1.7.25.jar:/data/app/zookeeper-3.6.2/bin/../lib/simpleclient_servlet-0.6.0.jar:/data/app/zookeeper-3.6.2/bin/../lib/simpleclient_hotspot-0.6.0.jar:/data/app/zookeeper-3.6.2/bin/../lib/simpleclient_common-0.6.0.jar:/data/app/zookeeper-3.6.2/bin/../lib/simpleclient-0.6.0.jar:/data/app/zookeeper-3.6.2/bin/../lib/netty-transport-native-unix-common-4.1.50.Final.jar:/data/app/zookeeper-3.6.2/bin/../lib/netty-transport-native-epoll-4.1.50.Final.jar:/data/app/zookeeper-3.6.2/bin/../lib/netty-transport-4.1.50.Final.jar:/data/app/zookeeper-3.6.2/bin/../lib/netty-resolver-4.1.50.Final.jar:/data/app/zookeeper-3.6.2/bin/../lib/netty-handler-4.1.50.Final.jar:/data/app/zookeeper-3.6.2/bin/../lib/netty-common-4.1.50.Final.jar:/data/app/zookeeper-3.6.2/bin/../lib/netty-codec-4.1.50.Final.jar:/data/app/zookeeper-3.6.2/bin/../lib/netty-buffer-4.1.50.Final.jar:/data/app/zookeeper-3.6.2/bin/../lib/metrics-core-3.2.5.jar:/data/app/zookeeper-3.6.2/bin/../lib/log4j-1.2.17.jar:/data/app/zookeeper-3.6.2/bin/../lib/json-simple-1.1.1.jar:/data/app/zookeeper-3.6.2/bin/../lib/jline-2.14.6.jar:/data/app/zookeeper-3.6.2/bin/../lib/jetty-util-9.4.24.v20191120.jar:/data/app/zookeeper-3.6.2/bin/../lib/jetty-servlet-9.4.24.v20191120.jar:/data/app/zookeeper-3.6.2/bin/../lib/jetty-server-9.4.24.v20191120.jar:/data/app/zookeeper-3.6.2/bin/../lib/jetty-security-9.4.24.v20191120.jar:/data/app/zookeeper-3.6.2/bin/../lib/jetty-io-9.4.24.v20191120.jar:/data/app/zookeeper-3.6.2/bin/../lib/jetty-http-9.4.24.v20191120.jar:/data/app/zookeeper-3.6.2/bin/../lib/javax.servlet-api-3.1.0.jar:/data/app/zookeeper-3.6.2/bin/../lib/jackson-databind-2.10.3.jar:/data/app/zookeeper-3.6.2/bin/../lib/jackson-core-2.10.3.jar:/data/app/zookeeper-3.6.2/bin/../lib/jackson-annotations-2.10.3.jar:/data/app/zookeeper-3.6.2/bin/../lib/commons-lang-2.6.jar:/data/app/zookeeper-3.6.2/bin/../lib/commons-cli-1.2.jar:/data/app/zookeeper-3.6.2/bin/../lib/audience-annotations-0.5.0.jar:/data/app/zookeeper-3.6.2/bin/../zookeeper-*.jar:/data/app/zookeeper-3.6.2/bin/../zookeeper-server/src/main/resources/lib/*.jar:/data/zookeeperdata/conf: -Xmx1000m -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.local.only=false org.apache.zookeeper.server.quorum.QuorumPeerMain /data/zookeeperdata/conf/zoo.cfg
root     30578  0.0  0.0 112712   976 pts/0    S+   14:44   0:00 grep --color=auto zook
2025-03-15T14:44:42 [root@x86centos7-95 /proc/9106] # strings /proc/603/environ
SHELL=/bin/bash
USER=mysql
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin
PWD=/
LANG=en_US.UTF-8
SHLVL=1
HOME=/home/mysql
CLIENT_JVMFLAGS=-Xmx256m
LOGNAME=mysql
SERVER_JVMFLAGS=-Xmx1000m
_=/usr/bin/nohup


```



## /proc/pid/cmdline

```bash
2025-03-15T14:47:24 [root@x86centos7-95 /proc/9106] # ps aux|grep mysqld
mysql     7576  0.0  0.0 113316  1676 ?        S    Mar10   0:00 /bin/sh /data/app/mysql-8.0.26/bin/mysqld_safe --defaults-file=/data/mysqldata/16315fff-acfb-467d-8f61-441bfbe1b313/my16315.conf --user=mysql --datadir=/data/mysqldata/16315fff-acfb-467d-8f61-441bfbe1b313/dbdata
mysql     9106  1.7 21.2 8072204 1701708 ?     Sl   Mar10 119:29 /data/app/mysql-8.0.26/bin/mysqld --defaults-file=/data/mysqldata/16315fff-acfb-467d-8f61-441bfbe1b313/my16315.conf --basedir=/data/app/mysql-8.0.26 --datadir=/data/mysqldata/16315fff-acfb-467d-8f61-441bfbe1b313/dbdata --plugin-dir=/data/app/mysql-8.0.26/lib/plugin --log-error=/data/mysqldata/16315fff-acfb-467d-8f61-441bfbe1b313/logfile/mysqld.err --open-files-limit=1024000 --pid-file=/data/mysqldata/16315fff-acfb-467d-8f61-441bfbe1b313/pid/mysqld.pid --socket=/data/mysqldata/16315fff-acfb-467d-8f61-441bfbe1b313/socket/mysql.sock --port=16315
root     31698  0.0  0.0 112708   976 pts/0    R+   14:47   0:00 grep --color=auto mysqld
2025-03-15T14:47:33 [root@x86centos7-95 /proc/9106] # strings /proc/9106/cmdline
/data/app/mysql-8.0.26/bin/mysqld
--defaults-file=/data/mysqldata/16315fff-acfb-467d-8f61-441bfbe1b313/my16315.conf
--basedir=/data/app/mysql-8.0.26
--datadir=/data/mysqldata/16315fff-acfb-467d-8f61-441bfbe1b313/dbdata
--plugin-dir=/data/app/mysql-8.0.26/lib/plugin
--log-error=/data/mysqldata/16315fff-acfb-467d-8f61-441bfbe1b313/logfile/mysqld.err
--open-files-limit=1024000
--pid-file=/data/mysqldata/16315fff-acfb-467d-8f61-441bfbe1b313/pid/mysqld.pid
--socket=/data/mysqldata/16315fff-acfb-467d-8f61-441bfbe1b313/socket/mysql.sock
--port=16315

```






