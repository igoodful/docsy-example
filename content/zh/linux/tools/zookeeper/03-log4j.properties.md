---
title: 3. log4j.properties
description: log4j.properties
date: 2023-11-05
weight: 300
viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
占用端口

{{< /alert >}}

| 端口 | 使用说明   |
| :--- | :--------- |
| 2181 | 访问       |
| 2888 | 内部端口， |
| 3888 | 内部端口， |

## 一、配置详解

#### 1. zoo.cfg
```bash
tickTime=6000
initLimit=5
syncLimit=2
autopurge.snapRetainCount=10
autopurge.purgeInterval=120
4lw.commands.whitelist=stat,mntr,conf
dataDir=/data/zookeeper/data
reconfigEnabled=true
admin.enableServer=false
standaloneEnabled=false
dynamicConfigFile=/data/zookeeper/conf/zoo.cfg.dynamic.100000000

```

- `tickTime=6000` 定义ZooKeeper的时间单位（单位：毫秒）。所有与时间相关的配置（如超时、心跳）均基于此值。较大的 `tickTime` 会延长超时判定时间，适用于高延迟网络或负载较重的场景，但可能影响故障检测速度。
- `initLimit=5` 表示$5 \times tickTime = 5 \times 6s = 30s$，集群启动时，允许 `Follower` 与 `Leader` 建立连接并完成初始同步的最大时间（单位为 `tickTime` 的倍数）。
- `syncLimit=2` 表示$2 \times tickTime = 2 \times 6s = 12s$，`Follower` 与 `Leader` 间心跳同步的超时时间（单位为 `tickTime` 的倍数）。若 `Follower` 在 12 秒内未与 `Leader` 通信，会被标记为失效。
- `autopurge.purgeInterval=120`
- `autopurge.snapRetainCount=10`
- `4lw.commands.whitelist=stat,mntr,conf`
- `dataDir=/data/zk/data`
- `reconfigEnabled=true`
- `dynamicConfigFile=/data/zk/conf/zoo.cfg.dynamic.100000000` 指定动态集群配置文件的路径。文件名中的 100000000 是版本号，动态配置变更时会递增。
- `standaloneEnabled=false` 禁用单机模式，强制以集群模式运行。ZooKeeper 3.5+ 引入，若设为 false，必须通过动态或静态配置文件定义集群节点。
- `admin.enableServer=false` 禁用内置的 AdminServer（默认端口 8080）。关闭后无法通过 HTTP 访问 /commands 监控端点，需通过四字命令或其他监控工具。






#### 2. zoo.cfg.dynamic.100000000
```bash
server.211=172.16.76.33:2888:3888:participant;0.0.0.0:16309
server.212=172.16.76.34:2888:3888:participant;0.0.0.0:16309
server.213=172.16.76.35:2888:3888:participant;0.0.0.0:16309
```



#### 3. log4j.properties

`cat log4j.properties`
```bash

zookeeper.root.logger=INFO, CONSOLE, DAYROLLINGAppender
log4j.rootLogger=${zookeeper.root.logger}

zookeeper.log.dir=.
zookeeper.log.file=zookeeper.log
zookeeper.log.threshold=INFO
zookeeper.log.maxfilesize=256MB
zookeeper.log.maxbackupindex=20

zookeeper.console.threshold=INFO
zookeeper.tracelog.dir=${zookeeper.log.dir}
zookeeper.tracelog.file=zookeeper_trace.log



# 按时间轮转日志，但不能限制日志数量
# DAYROLLING appender
log4j.appender.DAYROLLINGAppender=org.apache.log4j.DailyRollingFileAppender
log4j.appender.DAYROLLINGAppender.DatePattern='.'yyyy-MM-dd-HH
log4j.appender.DAYROLLINGAppender.File=${zookeeper.log.dir}/${zookeeper.log.file}
log4j.appender.DAYROLLINGAppender.Threshold=INFO
log4j.appender.DAYROLLINGAppender.layout=org.apache.log4j.PatternLayout
log4j.appender.DAYROLLINGAppender.layout.ConversionPattern=%d{ISO8601} [myid:%X{myid}] - %-5p [%t:%C{1}@%L] - %m%n


# 按照文件大小，可以限制文件个数
# Add ROLLINGFILE to rootLogger to get log file output
#
log4j.appender.ROLLINGFILE=org.apache.log4j.RollingFileAppender
log4j.appender.ROLLINGFILE.Threshold=${zookeeper.log.threshold}
log4j.appender.ROLLINGFILE.File=${zookeeper.log.dir}/${zookeeper.log.file}
log4j.appender.ROLLINGFILE.MaxFileSize=${zookeeper.log.maxfilesize}
log4j.appender.ROLLINGFILE.MaxBackupIndex=${zookeeper.log.maxbackupindex}
log4j.appender.ROLLINGFILE.layout=org.apache.log4j.PatternLayout
log4j.appender.ROLLINGFILE.layout.ConversionPattern=%d{ISO8601} [myid:%X{myid}] - %-5p [%t:%C{1}@%L] - %m%n


#
# console
# Add "console" to rootlogger above if you want to use this
#
log4j.appender.CONSOLE=org.apache.log4j.ConsoleAppender
log4j.appender.CONSOLE.Threshold=${zookeeper.console.threshold}
log4j.appender.CONSOLE.layout=org.apache.log4j.PatternLayout
log4j.appender.CONSOLE.layout.ConversionPattern=%d{ISO8601} [myid:%X{myid}] - %-5p [%t:%C{1}@%L] - %m%n

#
# Add TRACEFILE to rootLogger to get log file output
#
log4j.appender.TRACEFILE=org.apache.log4j.FileAppender
log4j.appender.TRACEFILE.Threshold=TRACE
log4j.appender.TRACEFILE.File=${zookeeper.tracelog.dir}/${zookeeper.tracelog.file}
log4j.appender.TRACEFILE.layout=org.apache.log4j.PatternLayout
log4j.appender.TRACEFILE.layout.ConversionPattern=%d{ISO8601} [myid:%X{myid}] - %-5p [%t:%C{1}@%L][%x] - %m%n

#
# zk audit logging
#
audit.logger=INFO, RFAAUDIT
zookeeper.auditlog.file=zookeeper_audit.log
zookeeper.auditlog.threshold=INFO
log4j.logger.org.apache.zookeeper.audit.Log4jAuditLogger=${audit.logger}
log4j.additivity.org.apache.zookeeper.audit.Log4jAuditLogger=false
log4j.appender.RFAAUDIT=org.apache.log4j.RollingFileAppender
log4j.appender.RFAAUDIT.File=${zookeeper.log.dir}/${zookeeper.auditlog.file}
log4j.appender.RFAAUDIT.layout=org.apache.log4j.PatternLayout
log4j.appender.RFAAUDIT.layout.ConversionPattern=%d{ISO8601} %p %c{2}: %m%n
log4j.appender.RFAAUDIT.Threshold=${zookeeper.auditlog.threshold}
log4j.appender.RFAAUDIT.MaxFileSize=10MB
log4j.appender.RFAAUDIT.MaxBackupIndex=10


```


##### `zookeeper.root.logger`

- `INFO` 每月结束时滚动。
- `DAYROLLINGAppender` 按照时间切割。
- `ROLLINGFILE` 按照文件大小切割。


##### `log4j.appender.ROLLINGFILE`

- `org.apache.log4j.DaliyRollingFileAppender` 按照时间切割。
- `org.apache.log4j.RollingFileAppender` 按照文件大小切割。



##### `log4j.appender.DAYROLLINGAppender`

- `org.apache.log4j.DailyRollingFileAppender` 实现按时间滚动日志文件。按时间间隔（如每小时、每天）生成新日志文件。旧文件保留，文件名附加时间戳（例如 zookeeper.log.2023-10-01-12）。

- ``


##### `log4j.appender.DAYROLLINGAppender.DatePattern`

滚动时间策略,最小滚动单位是分钟

- `log4j.appender.FILE.DatePattern="." yyyy-MM` 每月结束时滚动。
- `log4j.appender.FILE.DatePattern="." yyyy-MM-dd` 在每天的中午和午夜滚动。
- `log4j.appender.FILE.DatePattern="." yyyy-MM-dd-a` 每天午夜滚动。默认值。指二十四小時制的00:00（24:00）。
- `log4j.appender.FILE.DatePattern="." yyyy-MM-dd-HH` 滚动在每个小时的顶部。即每小时的59分59秒开始切割。
- `log4j.appender.FILE.DatePattern="." yyyy-MM-dd-HH-mm` 每分钟滚动一次。即59秒开始切割。
- `log4j.appender.FILE.DatePattern="." yyyy-ww` 根据区域设置，每周的第一天滚动。




##### `log4j.appender.DAYROLLINGAppender.File`

指定当前日志文件的完整路径。

- `${zookeeper.log.dir}/${zookeeper.log.file}`



##### `log4j.appender.DAYROLLINGAppender.Threshold`

日志级别过滤

- `TRACE`
- `DEBUG`
- `INFO`
- `WARN`
- `ERROR`
- `FATAL`




#### 4. myid
```bash
211
```

## 二、脚本详解


####  1. zkEnv.sh
> 设置 ZooKeeper 的环境变量和配置，它可以设置 Java 环境、内存配置、JVM 参数等环境变量
>
> `source zkEnv.sh`

```viz-dot
digraph "zlib" {
node [
        rankdir=LR;
        fontsize = "12"
];
    "node15" [ label = "zlib", shape = octagon ];
    "node1" [ label = "-lpthread", shape = septagon ];
    "node15" -> "node1"  // zlib -> -lpthread
    "node16" [ label = "zlib_objlib", shape = hexagon ];
    "node15" -> "node16"  // zlib -> zlib_objlib
    "node1" [ label = "-lpthread", shape = septagon ];
    "node16" -> "node1"  // zlib_objlib -> -lpthread
}
```


```bash
#!/usr/bin/env bash

ZOOBINDIR="${ZOOBINDIR:-/usr/bin}"
ZOOKEEPER_PREFIX="${ZOOBINDIR}/.."

#check to see if the conf dir is given as an optional argument
if [ $# -gt 1 ]; then
	if [ "--config" = "$1" ]; then
		shift
		confdir=$1
		shift
		ZOOCFGDIR=$confdir
	fi
fi

if [ "x$ZOOCFGDIR" = "x" ]; then
	if [ -e "${ZOOKEEPER_PREFIX}/conf" ]; then
		ZOOCFGDIR="$ZOOBINDIR/../conf"
	else
		ZOOCFGDIR="$ZOOBINDIR/../etc/zookeeper"
	fi
fi

if [ -f "${ZOOCFGDIR}/zookeeper-env.sh" ]; then
	. "${ZOOCFGDIR}/zookeeper-env.sh"
fi

if [ "x$ZOOCFG" = "x" ]; then
	ZOOCFG="zoo.cfg"
fi

ZOOCFG="$ZOOCFGDIR/$ZOOCFG"

if [ -f "$ZOOCFGDIR/java.env" ]; then
	. "$ZOOCFGDIR/java.env"
fi

if [ "x${ZOO_LOG_DIR}" = "x" ]; then
	ZOO_LOG_DIR="$ZOOKEEPER_PREFIX/logs"
fi

if [ "x${ZOO_LOG4J_PROP}" = "x" ]; then
	ZOO_LOG4J_PROP="INFO,CONSOLE"
fi

if [[ -n "$JAVA_HOME" ]] && [[ -x "$JAVA_HOME/bin/java" ]]; then
	JAVA="$JAVA_HOME/bin/java"
elif type -p java; then
	JAVA=java
else
	echo "Error: JAVA_HOME is not set and java could not be found in PATH." 1>&2
	exit 1
fi

#add the zoocfg dir to classpath
CLASSPATH="$ZOOCFGDIR:$CLASSPATH"

for i in "$ZOOBINDIR"/../zookeeper-server/src/main/resources/lib/*.jar; do
	CLASSPATH="$i:$CLASSPATH"
done

#make it work in the binary package
#(use array for LIBPATH to account for spaces within wildcard expansion)
if ls "${ZOOKEEPER_PREFIX}"/share/zookeeper/zookeeper-*.jar >/dev/null 2>&1; then
	LIBPATH=("${ZOOKEEPER_PREFIX}"/share/zookeeper/*.jar)
else
	#release tarball format
	for i in "$ZOOBINDIR"/../zookeeper-*.jar; do
		CLASSPATH="$i:$CLASSPATH"
	done
	LIBPATH=("${ZOOBINDIR}"/../lib/*.jar)
fi

for i in "${LIBPATH[@]}"; do
	CLASSPATH="$i:$CLASSPATH"
done

#make it work for developers
for d in "$ZOOBINDIR"/../build/lib/*.jar; do
	CLASSPATH="$d:$CLASSPATH"
done

for d in "$ZOOBINDIR"/../zookeeper-server/target/lib/*.jar; do
	CLASSPATH="$d:$CLASSPATH"
done

#make it work for developers
CLASSPATH="$ZOOBINDIR/../build/classes:$CLASSPATH"

#make it work for developers
CLASSPATH="$ZOOBINDIR/../zookeeper-server/target/classes:$CLASSPATH"

case "$(uname)" in
CYGWIN* | MINGW*) cygwin=true ;;
*) cygwin=false ;;
esac

if $cygwin; then
	CLASSPATH=$(cygpath -wp "$CLASSPATH")
fi

#echo "CLASSPATH=$CLASSPATH"

# default heap for zookeeper server
ZK_SERVER_HEAP="${ZK_SERVER_HEAP:-1000}"
export SERVER_JVMFLAGS="-Xmx${ZK_SERVER_HEAP}m $SERVER_JVMFLAGS"

# default heap for zookeeper client
ZK_CLIENT_HEAP="${ZK_CLIENT_HEAP:-256}"
export CLIENT_JVMFLAGS="-Xmx${ZK_CLIENT_HEAP}m $CLIENT_JVMFLAGS"

```



#### 2. zkServer.sh

```viz-dot
digraph "zkServer.sh" {
node [
        rankdir=LR;
        fontsize = "12";
        shape = octagon;
];
"zkServer.sh" -> "start";
"zkServer.sh" -> "stop";
"zkServer.sh" -> "restart";
"zkServer.sh" -> "status";
"zkServer.sh" -> "upgrade";
"zkServer.sh" -> "print-cmd";

}
```


#### 3. zkCli.sh
> `./zkCli.sh -server ip:port`

```viz-dot
digraph "zkCli.sh" {
node [
        rankdir=LR;
        fontsize = "12";
        shape = octagon;
];
"zkCli.sh" -> "create";
"zkCli.sh" -> "delete";
"zkCli.sh" -> "ls";
"zkCli.sh" -> "get";
"zkCli.sh" -> "set";
"zkCli.sh" -> "stat";
"zkCli.sh" -> "quit";
"zkCli.sh" -> "setAcl";
"zkCli.sh" -> "addauth digest user:password";

}
```

#### 4. zkCleanup.sh





