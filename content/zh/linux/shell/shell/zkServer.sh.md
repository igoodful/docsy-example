---
title: zkServer.sh
description: zkServer.sh
date: 2023-11-01
weight: 30000
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{<alert>}}


{{</alert>}}



### zkEnv.sh

```bash
yum -y  install snapd
service snapd start

```

echo "Using config: $ZOOCFG" >&2
| 序号                                     | 步骤                                                                  | 原理     | 备注                                                                    |
|:-----------------------------------------|:----------------------------------------------------------------------|:---------|:------------------------------------------------------------------------|
| 脚本所在目录                             | `ZOOBIN="${BASH_SOURCE-$0}";ZOOBIN="$(dirname "${ZOOBIN}")"`          |          | `/data/app/zookeeper-3.6.2/bin`                                         |
| 脚本目录绝对路径                         | `ZOOBINDIR="$(cd "${ZOOBIN}";pwd)"`                                   |          | `/data/app/zookeeper-3.6.2/bin`                                         |
| 执行环境脚本                             | `"$ZOOBINDIR"/../libexec/zkEnv.sh`》`"$ZOOBINDIR"/zkEnv.sh`            |          | `. "$ZOOBINDIR"/zkEnv.sh`                                               |
| JMXLOCALONLY                             | 没定义`JMXLOCALONLY`则定义`JMXLOCALONLY=false`                        |          | `JMXLOCALONLY=false`                                                    |
| JMXDISABLE                               | 没定义`JMXDISABLE`或`JMXDISABLE=false`则                              |          |                                                                         |
| ZOOMAIN                                  | `ZOOMAIN`                                                             |          |                                                                         |
| JVMFLAGS                                 | `JVMFLAGS`                                                            |          |                                                                         |
| 第二个参数为配置文件路径                 | `ZOOCFG`                                                              |          | 命令行配置文件》自定义配置变量》默认配置文件                              |
| 打印配置文件                             | `echo "Using config: $ZOOCFG" >&2`                                    |          | 打印配置文件                                                            |
| 数据路径                                 | `ZOO_DATADIR`                                                         |          | `ZOO_DATADIR=/data/zookeeperdata/data`                                  |
| 事务和快照日志路径                       | `ZOO_DATALOGDIR`                                                      |          | 默认没有配置                                                            |
| 是否定义`ZOO_DATADIR_AUTOCREATE_DISABLE` | `$ZOO_DATADIR/version-2`和`$ZOO_DATALOGDIR/version-2`                 |          | 默认没有定义则会自动创建                                                |
| 定义：pid文件                             | 自定义`ZOOPIDFILE`》`$ZOO_DATADIR/zookeeper_server.pid`                |          | 自动创建`ZOO_DATADIR`或pid所在路径，默认数据目录下`zookeeper_server.pid` |
| 创建：系统日志路径                        | `mkdir -p "$ZOO_LOG_DIR"`                                             |          | 自动创建系统日志路径：`ZOO_LOG_DIR`                                      |
| 定义：系统日志文件名                      | `ZOO_LOG_FILE=zookeeper-$USER-server-$HOSTNAME.log`                   |          |                                                                         |
| 定义：daemon日志文件名                    | `_ZOO_DAEMON_OUT="$ZOO_LOG_DIR/zookeeper-$USER-server-$HOSTNAME.out"` |          | 路径与系统日志相同路径                                                  |
| `======`                                 | `======`                                                              | `======` | `======`                                                                |
| start                                    | `Starting zookeeper ... `                                             |          |                                                                         |
| ZOOPIDFILE                               | `kill -0 $(cat "$ZOOPIDFILE")`                                        |          | 根据进程pid文件查看进程是否已经正常运行                                 |
| 启动                                     | `nohup $JAVA >"$_ZOO_DAEMON_OUT" 2>&1 </dev/null &`                   |          | 启动命令                                                                |
| 写ZOOPIDFILE                             | `/bin/echo -n $! >"$ZOOPIDFILE"`                                      |          | 写入pid文件                                                             |
| 根据pid文件查看进程                      | `pid=$(cat "${ZOOPIDFILE}");sleep 1;ps -p "${pid}"`                   |          |                                                                         |
| `======`                                 | `======`                                                              | `======` | `======`                                                                |
| stop                                     | `Stopping zookeeper ... `                                             |          |                                                                         |
| 根据pid文件是否存在kill                  | `$KILL $(cat "$ZOOPIDFILE")`                                          |          |                                                                         |
| 删除pid文件                              | `rm "$ZOOPIDFILE"`                                                    |          |                                                                         |
| `======`                                 | `======`                                                              | `======` | `======`                                                                |
| restart                                  | `shift`                                                               |          |                                                                         |
| stop                                     | `"$0" stop ${@}`                                                      |          |                                                                         |
| sleep                                    | `sleep 3`                                                             |          |                                                                         |
| start                                    | `"$0" start ${@}`                                                     |          |                                                                         |
| `======`                                 | `======`                                                              | `======` | `======`                                                                |
| status                                   | ``                                                                    |          |                                                                         |
| rm                                       | ``                                                                    |          |                                                                         |
| rm                                       | ``                                                                    |          |                                                                         |
| rm                                       | ``                                                                    |          |                                                                         |
| rm                                       | ``                                                                    |          |                                                                         |

- 启动命令：`nohup "$JAVA" $ZOO_DATADIR_AUTOCREATE "-Dzookeeper.log.dir=${ZOO_LOG_DIR}" "-Dzookeeper.DigestAuthenticationProvider.superDigest=su:gACzJ4L2A0F2ygTno5HQnfabuik="  "-Dzookeeper.log.file=${ZOO_LOG_FILE}" "-Dzookeeper.root.logger=${ZOO_LOG4J_PROP}" -XX:+HeapDumpOnOutOfMemoryError -XX:OnOutOfMemoryError='kill -9 %p' -cp "$CLASSPATH" $JVMFLAGS $ZOOMAIN "$ZOOCFG" >"$_ZOO_DAEMON_OUT" 2>&1 </dev/null &`
- 状态获取命令：`"$JAVA" "-Dzookeeper.log.dir=${ZOO_LOG_DIR}" "-Dzookeeper.root.logger=${ZOO_LOG4J_PROP}" "-Dzookeeper.log.file=${ZOO_LOG_FILE}" -cp "$CLASSPATH" $CLIENT_JVMFLAGS $JVMFLAGS org.apache.zookeeper.client.FourLetterWordMain $clientPortAddress $clientPort srvr $isSSL 2>/dev/null |$GREP Mode`


## 超级用户的摘要认证信息
```bash
echo -n super_user:password | openssl dgst -binary -sha1 | openssl base64

```


## 提取dataDir

```bash
grep  "^[[:space:]]*dataDir" /data/zookeeperdata/conf/zoo.cfg | sed -e 's/.*=//'

echo -e "/data/zookeeperdata/data" | sed -e 's/^[[:space:]]*//' -e 's/[[:space:]]*$//'

```
- `grep  "^[[:space:]]*dataDir"`：匹配零个或多个空白字符（包括空格和制表符），从 zoo.cfg 文件中查找以 dataDir 开头的行（dataDir前可包含任意数量的空白符）
- `sed -e 's/.*=//'`：删除`=`及其之前的内容
- 结果：`dataDir=/data/zookeeperdata/data`  》 `/data/zookeeperdata/data`
- `echo -e "/data/zookeeperdata/data" | sed -e 's/^[[:space:]]*//' -e 's/[[:space:]]*$//'`：删除`/data/zookeeperdata/data`前后的空白符



### 最终进程

```bash
java -Dzookeeper.log.dir=/data/zookeeperdata/log -Dzookeeper.DigestAuthenticationProvider.superDigest=su:gACzJ4L2A0F2ygTno5HQnfabuik= -Dzookeeper.log.file=zookeeper-mysql-server-x86centos7-95.log -Dzookeeper.root.logger=INFO,DAYROLLINGAppender -XX:+HeapDumpOnOutOfMemoryError -XX:OnOutOfMemoryError=kill -9 %p -cp /data/app/zookeeper-3.6.2/bin/../zookeeper-server/target/classes:/data/app/zookeeper-3.6.2/bin/../build/classes:/data/app/zookeeper-3.6.2/bin/../zookeeper-server/target/lib/*.jar:/data/app/zookeeper-3.6.2/bin/../build/lib/*.jar:/data/app/zookeeper-3.6.2/bin/../lib/zookeeper-prometheus-metrics-3.6.2.jar:/data/app/zookeeper-3.6.2/bin/../lib/zookeeper-jute-3.6.2.jar:/data/app/zookeeper-3.6.2/bin/../lib/zookeeper-3.6.2.jar:/data/app/zookeeper-3.6.2/bin/../lib/snappy-java-1.1.7.jar:/data/app/zookeeper-3.6.2/bin/../lib/slf4j-log4j12-1.7.25.jar:/data/app/zookeeper-3.6.2/bin/../lib/slf4j-api-1.7.25.jar:/data/app/zookeeper-3.6.2/bin/../lib/simpleclient_servlet-0.6.0.jar:/data/app/zookeeper-3.6.2/bin/../lib/simpleclient_hotspot-0.6.0.jar:/data/app/zookeeper-3.6.2/bin/../lib/simpleclient_common-0.6.0.jar:/data/app/zookeeper-3.6.2/bin/../lib/simpleclient-0.6.0.jar:/data/app/zookeeper-3.6.2/bin/../lib/netty-transport-native-unix-common-4.1.50.Final.jar:/data/app/zookeeper-3.6.2/bin/../lib/netty-transport-native-epoll-4.1.50.Final.jar:/data/app/zookeeper-3.6.2/bin/../lib/netty-transport-4.1.50.Final.jar:/data/app/zookeeper-3.6.2/bin/../lib/netty-resolver-4.1.50.Final.jar:/data/app/zookeeper-3.6.2/bin/../lib/netty-handler-4.1.50.Final.jar:/data/app/zookeeper-3.6.2/bin/../lib/netty-common-4.1.50.Final.jar:/data/app/zookeeper-3.6.2/bin/../lib/netty-codec-4.1.50.Final.jar:/data/app/zookeeper-3.6.2/bin/../lib/netty-buffer-4.1.50.Final.jar:/data/app/zookeeper-3.6.2/bin/../lib/metrics-core-3.2.5.jar:/data/app/zookeeper-3.6.2/bin/../lib/log4j-1.2.17.jar:/data/app/zookeeper-3.6.2/bin/../lib/json-simple-1.1.1.jar:/data/app/zookeeper-3.6.2/bin/../lib/jline-2.14.6.jar:/data/app/zookeeper-3.6.2/bin/../lib/jetty-util-9.4.24.v20191120.jar:/data/app/zookeeper-3.6.2/bin/../lib/jetty-servlet-9.4.24.v20191120.jar:/data/app/zookeeper-3.6.2/bin/../lib/jetty-server-9.4.24.v20191120.jar:/data/app/zookeeper-3.6.2/bin/../lib/jetty-security-9.4.24.v20191120.jar:/data/app/zookeeper-3.6.2/bin/../lib/jetty-io-9.4.24.v20191120.jar:/data/app/zookeeper-3.6.2/bin/../lib/jetty-http-9.4.24.v20191120.jar:/data/app/zookeeper-3.6.2/bin/../lib/javax.servlet-api-3.1.0.jar:/data/app/zookeeper-3.6.2/bin/../lib/jackson-databind-2.10.3.jar:/data/app/zookeeper-3.6.2/bin/../lib/jackson-core-2.10.3.jar:/data/app/zookeeper-3.6.2/bin/../lib/jackson-annotations-2.10.3.jar:/data/app/zookeeper-3.6.2/bin/../lib/commons-lang-2.6.jar:/data/app/zookeeper-3.6.2/bin/../lib/commons-cli-1.2.jar:/data/app/zookeeper-3.6.2/bin/../lib/audience-annotations-0.5.0.jar:/data/app/zookeeper-3.6.2/bin/../zookeeper-*.jar:/data/app/zookeeper-3.6.2/bin/../zookeeper-server/src/main/resources/lib/*.jar:/data/zookeeperdata/conf: -Xmx1000m -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.local.only=false org.apache.zookeeper.server.quorum.QuorumPeerMain /data/zookeeperdata/conf/zoo.cfg

```
- `/data/zookeeperdata/conf/zoo.cfg` 使用的配置文件
- `-Dzookeeper.log.dir=/data/zookeeperdata/log` 设置ZooKeeper日志文件的存储目录
- `-Dzookeeper.DigestAuthenticationProvider.superDigest=su:gACzJ4L2A0F2ygTno5HQnfabuik=` 设置 ZooKeeper 的超级用户su的摘要认证信息
- `-Dzookeeper.log.file=zookeeper-mysql-server-x86centos7-95.log` 设置 ZooKeeper 日志文件的名称
- `-Dzookeeper.root.logger=INFO,DAYROLLINGAppender` 设置 ZooKeeper 的日志级别为 INFO，并使用 DAYROLLINGAppender 作为日志输出方式（按天滚动日志文件）
- `-Dcom.sun.management.jmxremote` 启用 JMX 远程监控
- `-Dcom.sun.management.jmxremote.local.only=false` 允许远程 JMX 连接（不仅限于本地连接）
- `-XX:+HeapDumpOnOutOfMemoryError` 在发生内存溢出（OutOfMemoryError）时生成堆转储文件（Heap Dump），用于后续分析
- `-XX:OnOutOfMemoryError=kill -9 %p` 在发生内存溢出时，强制终止当前 Java 进程（%p 表示当前进程的 PID）
- `-Xmx1000m` 设置 JVM 的最大堆内存为 1000MB
- `org.apache.zookeeper.server.quorum.QuorumPeerMain` 这是 ZooKeeper 的主类，用于启动 ZooKeeper 服务器
- `-cp`：类路径，类路径指定了 JVM 加载类和资源的路径
  - `/data/app/zookeeper-3.6.2/bin/../zookeeper-server/target/classes` 默认不存在
  - `/data/app/zookeeper-3.6.2/bin/../build/classes` 默认不存在
  - `/data/app/zookeeper-3.6.2/bin/../zookeeper-server/target/lib/*.jar` 默认不存在
  - `/data/app/zookeeper-3.6.2/bin/../build/lib/*.jar` 默认不存在
  - `/data/app/zookeeper-3.6.2/bin/../lib/*.jar` ZooKeeper 的核心库和第三方库
  - `/data/zookeeperdata/conf` ZooKeeper 的配置文件目录




- `/data/app/zookeeper-3.6.2/lib`

```bash
2025-03-13T22:13:55 [root@x86centos7-95 /data/app/zookeeper-3.6.2/lib] # ll ..
total 40
drwxr-xr-x 2 mysql mysql  4096 Mar 11 17:37 bin
drwxr-xr-x 2 mysql mysql  4096 Mar 10 18:25 conf
drwxr-xr-x 5 mysql mysql  4096 Sep  4  2020 docs
drwxr-xr-x 2 mysql mysql  4096 Mar 10 18:25 lib
-rw-r--r-- 1 mysql mysql 11358 Sep  4  2020 LICENSE.txt
-rw-r--r-- 1 mysql mysql   432 Sep  4  2020 NOTICE.txt
-rw-r--r-- 1 mysql mysql  1963 Sep  4  2020 README.md
-rw-r--r-- 1 mysql mysql  3166 Sep  4  2020 README_packaging.md

2025-03-13T22:14:04 [root@x86centos7-95 /data/app/zookeeper-3.6.2/lib] # ll |grep '.jar'
-rw-r--r-- 1 mysql mysql   20437 Mar 21  2020 audience-annotations-0.5.0.jar
-rw-r--r-- 1 mysql mysql   41123 Mar 21  2020 commons-cli-1.2.jar
-rw-r--r-- 1 mysql mysql  284220 Mar 21  2020 commons-lang-2.6.jar
-rw-r--r-- 1 mysql mysql   68079 Mar 21  2020 jackson-annotations-2.10.3.jar
-rw-r--r-- 1 mysql mysql  349108 Mar 21  2020 jackson-core-2.10.3.jar
-rw-r--r-- 1 mysql mysql 1404171 Mar 21  2020 jackson-databind-2.10.3.jar
-rw-r--r-- 1 mysql mysql   95806 Mar 21  2020 javax.servlet-api-3.1.0.jar
-rw-r--r-- 1 mysql mysql  210803 Mar 27  2020 jetty-http-9.4.24.v20191120.jar
-rw-r--r-- 1 mysql mysql  159229 Mar 27  2020 jetty-io-9.4.24.v20191120.jar
-rw-r--r-- 1 mysql mysql  117783 Mar 27  2020 jetty-security-9.4.24.v20191120.jar
-rw-r--r-- 1 mysql mysql  672677 Mar 27  2020 jetty-server-9.4.24.v20191120.jar
-rw-r--r-- 1 mysql mysql  131565 Mar 27  2020 jetty-servlet-9.4.24.v20191120.jar
-rw-r--r-- 1 mysql mysql  549438 Mar 27  2020 jetty-util-9.4.24.v20191120.jar
-rw-r--r-- 1 mysql mysql  268780 May 26  2020 jline-2.14.6.jar
-rw-r--r-- 1 mysql mysql   23931 Mar 27  2020 json-simple-1.1.1.jar
-rw-r--r-- 1 mysql mysql  489884 Mar 21  2020 log4j-1.2.17.jar
-rw-r--r-- 1 mysql mysql  136325 Mar 27  2020 metrics-core-3.2.5.jar
-rw-r--r-- 1 mysql mysql   11362 Sep  4  2020 metrics-core-3.2.5.jar_LICENSE.txt
-rw-r--r-- 1 mysql mysql  289845 May 26  2020 netty-buffer-4.1.50.Final.jar
-rw-r--r-- 1 mysql mysql  320177 May 26  2020 netty-codec-4.1.50.Final.jar
-rw-r--r-- 1 mysql mysql  626103 May 26  2020 netty-common-4.1.50.Final.jar
-rw-r--r-- 1 mysql mysql  455224 May 26  2020 netty-handler-4.1.50.Final.jar
-rw-r--r-- 1 mysql mysql   33160 May 26  2020 netty-resolver-4.1.50.Final.jar
-rw-r--r-- 1 mysql mysql  473176 May 26  2020 netty-transport-4.1.50.Final.jar
-rw-r--r-- 1 mysql mysql  124005 May 26  2020 netty-transport-native-epoll-4.1.50.Final.jar
-rw-r--r-- 1 mysql mysql   33064 May 26  2020 netty-transport-native-unix-common-4.1.50.Final.jar
-rw-r--r-- 1 mysql mysql   59164 Mar 27  2020 simpleclient-0.6.0.jar
-rw-r--r-- 1 mysql mysql    5846 Mar 27  2020 simpleclient_common-0.6.0.jar
-rw-r--r-- 1 mysql mysql   21659 Mar 27  2020 simpleclient_hotspot-0.6.0.jar
-rw-r--r-- 1 mysql mysql    7098 Mar 27  2020 simpleclient_servlet-0.6.0.jar
-rw-r--r-- 1 mysql mysql   41203 Mar 21  2020 slf4j-api-1.7.25.jar
-rw-r--r-- 1 mysql mysql   12244 Mar 21  2020 slf4j-log4j12-1.7.25.jar
-rw-r--r-- 1 mysql mysql 2019014 Mar 27  2020 snappy-java-1.1.7.jar
-rw-r--r-- 1 mysql mysql   11358 Sep  4  2020 snappy-java-1.1.7.jar_LICENSE.txt
-rw-r--r-- 1 mysql mysql 1245934 Sep  4  2020 zookeeper-3.6.2.jar
-rw-r--r-- 1 mysql mysql  250405 Sep  4  2020 zookeeper-jute-3.6.2.jar
-rw-r--r-- 1 mysql mysql   16435 Sep  4  2020 zookeeper-prometheus-metrics-3.6.2.jar


```



### /data/zookeeperdata/conf/zoo.cfg /data/zookeeperdata/conf/zoo.cfg.dynamic.100000000

```bash
2025-03-13T22:57:05 [root@x86centos7-95 /data/zookeeperdata/conf] # cat /data/zookeeperdata/conf/zoo.cfg.dynamic.100000000
server.4=172.17.137.159:2888:3888:participant;0.0.0.0:16309
server.5=172.17.138.136:2888:3888:participant;0.0.0.0:16309
server.6=172.17.140.189:2888:3888:participant;0.0.0.0:16309

2025-03-13T22:57:17 [root@x86centos7-95 /data/zookeeperdata/conf] # cat zoo.cfg
autopurge.purgeInterval=120
initLimit=5
syncLimit=2
autopurge.snapRetainCount=10
4lw.commands.whitelist=stat,mntr,conf
tickTime=6000
dataDir=/data/zookeeperdata/data
reconfigEnabled=true
admin.enableServer=false
standaloneEnabled=false
dynamicConfigFile=/data/zookeeperdata/conf/zoo.cfg.dynamic.100000000

```


## zkServer.sh


```bash
#!/usr/bin/env bash

# 该脚本所在目录
ZOOBIN="${BASH_SOURCE-$0}"
ZOOBIN="$(dirname "${ZOOBIN}")"

# 获取脚本绝对路径
ZOOBINDIR="$(
	cd "${ZOOBIN}"
	pwd
)"

# "$ZOOBINDIR"/../libexec/zkEnv.sh 》 "$ZOOBINDIR"/zkEnv.sh
if [ -e "$ZOOBIN/../libexec/zkEnv.sh" ]; then
	. "$ZOOBINDIR"/../libexec/zkEnv.sh
else
	. "$ZOOBINDIR"/zkEnv.sh
fi

# JMXLOCALONLY
if [ "x$JMXLOCALONLY" = "x" ]; then
	JMXLOCALONLY=false
fi

# JMXDISABLE
# ZOOMAIN
if [ "x$JMXDISABLE" = "x" ] || [ "$JMXDISABLE" = 'false' ]; then
	echo "ZooKeeper JMX enabled by default" >&2
	# JMXPORT
	if [ "x$JMXPORT" = "x" ]; then
		# ZOOMAIN
		ZOOMAIN="-Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.local.only=$JMXLOCALONLY org.apache.zookeeper.server.quorum.QuorumPeerMain"
	else
		if [ "x$JMXAUTH" = "x" ]; then
			JMXAUTH=false
		fi
		if [ "x$JMXSSL" = "x" ]; then
			JMXSSL=false
		fi
		if [ "x$JMXLOG4J" = "x" ]; then
			JMXLOG4J=true
		fi
		echo "ZooKeeper remote JMX Port set to $JMXPORT" >&2
		echo "ZooKeeper remote JMX authenticate set to $JMXAUTH" >&2
		echo "ZooKeeper remote JMX ssl set to $JMXSSL" >&2
		echo "ZooKeeper remote JMX log4j set to $JMXLOG4J" >&2
		ZOOMAIN="-Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.port=$JMXPORT -Dcom.sun.management.jmxremote.authenticate=$JMXAUTH -Dcom.sun.management.jmxremote.ssl=$JMXSSL -Dzookeeper.jmx.log4j.disable=$JMXLOG4J org.apache.zookeeper.server.quorum.QuorumPeerMain"
	fi
else
	echo "JMX disabled by user request" >&2
	ZOOMAIN="org.apache.zookeeper.server.quorum.QuorumPeerMain"
fi

# SERVER_JVMFLAGS
# JVMFLAGS
if [ "x$SERVER_JVMFLAGS" != "x" ]; then
	JVMFLAGS="$SERVER_JVMFLAGS $JVMFLAGS"
fi

# ZOOCFG
if [ "x$2" != "x" ]; then
	ZOOCFG="$ZOOCFGDIR/$2"
fi

# 命令行指定配置文件所在路径与配置文件路径不相等则命令行优先
if [ "x$(dirname "$ZOOCFG")" != "x$ZOOCFGDIR" ]; then
	ZOOCFG="$2"
fi

# KILL
if $cygwin; then
	ZOOCFG=$(cygpath -wp "$ZOOCFG")
	# cygwin has a "kill" in the shell itself, gets confused
	KILL=/bin/kill
else
	KILL=kill
fi

echo "Using config: $ZOOCFG" >&2

# grep
case "$OSTYPE" in
*solaris*)
	GREP=/usr/xpg4/bin/grep
	;;
*)
	GREP=grep
	;;
esac

# ZOO_DATADIR=/data/zookeeperdata/data
ZOO_DATADIR="$($GREP "^[[:space:]]*dataDir" "$ZOOCFG" | sed -e 's/.*=//')"
# 删除/data/zookeeperdata/data前后的空白符
ZOO_DATADIR="$(echo -e "${ZOO_DATADIR}" | sed -e 's/^[[:space:]]*//' -e 's/[[:space:]]*$//')"

# ZOO_DATALOGDIR
ZOO_DATALOGDIR="$($GREP "^[[:space:]]*dataLogDir" "$ZOOCFG" | sed -e 's/.*=//')"

# ZOO_DATADIR_AUTOCREATE_DISABLE
if [ -n "$ZOO_DATADIR_AUTOCREATE_DISABLE" ]; then
	if [ ! -d "$ZOO_DATADIR/version-2" ]; then
		echo "ZooKeeper data directory is missing at $ZOO_DATADIR fix the path or run initialize"
		exit 1
	fi

	if [ -n "$ZOO_DATALOGDIR" ] && [ ! -d "$ZOO_DATALOGDIR/version-2" ]; then
		echo "ZooKeeper txnlog directory is missing at $ZOO_DATALOGDIR fix the path or run initialize"
		exit 1
	fi
	ZOO_DATADIR_AUTOCREATE="-Dzookeeper.datadir.autocreate=false"
fi

# ZOOPIDFILE
if [ -z "$ZOOPIDFILE" ]; then
	if [ ! -d "$ZOO_DATADIR" ]; then
		mkdir -p "$ZOO_DATADIR"
	fi
	ZOOPIDFILE="$ZOO_DATADIR/zookeeper_server.pid"
else
	# 创建pid文件所在目录
	mkdir -p "$(dirname "$ZOOPIDFILE")"
fi

# ZOO_LOG_DIR
if [ ! -w "$ZOO_LOG_DIR" ]; then
	mkdir -p "$ZOO_LOG_DIR"
fi

# ZOO_LOG_FILE
ZOO_LOG_FILE=zookeeper-$USER-server-$HOSTNAME.log

# _ZOO_DAEMON_OUT
_ZOO_DAEMON_OUT="$ZOO_LOG_DIR/zookeeper-$USER-server-$HOSTNAME.out"

case $1 in
start)
	echo -n "Starting zookeeper ... "
	if [ -f "$ZOOPIDFILE" ]; then
		if kill -0 $(cat "$ZOOPIDFILE") >/dev/null 2>&1; then
			echo $command already running as process $(cat "$ZOOPIDFILE").
			exit 1
		fi
	fi
	nohup "$JAVA" $ZOO_DATADIR_AUTOCREATE "-Dzookeeper.log.dir=${ZOO_LOG_DIR}" "-Dzookeeper.DigestAuthenticationProvider.superDigest=su:gACzJ4L2A0F2ygTno5HQnfabuik=" \
		"-Dzookeeper.log.file=${ZOO_LOG_FILE}" "-Dzookeeper.root.logger=${ZOO_LOG4J_PROP}" \
		-XX:+HeapDumpOnOutOfMemoryError -XX:OnOutOfMemoryError='kill -9 %p' \
		-cp "$CLASSPATH" $JVMFLAGS $ZOOMAIN "$ZOOCFG" >"$_ZOO_DAEMON_OUT" 2>&1 </dev/null &
	if [ $? -eq 0 ]; then
		case "$OSTYPE" in
		*solaris*)
			/bin/echo "${!}\\c" >"$ZOOPIDFILE"
			;;
		*)
			/bin/echo -n $! >"$ZOOPIDFILE"
			;;
		esac
		if [ $? -eq 0 ]; then
			sleep 1
			pid=$(cat "${ZOOPIDFILE}")
			if ps -p "${pid}" >/dev/null 2>&1; then
				echo STARTED
			else
				echo FAILED TO START
				exit 1
			fi
		else
			echo FAILED TO WRITE PID
			exit 1
		fi
	else
		echo SERVER DID NOT START
		exit 1
	fi
	;;
start-foreground)
	ZOO_CMD=(exec "$JAVA")
	if [ "${ZOO_NOEXEC}" != "" ]; then
		ZOO_CMD=("$JAVA")
	fi
	"${ZOO_CMD[@]}" $ZOO_DATADIR_AUTOCREATE "-Dzookeeper.log.dir=${ZOO_LOG_DIR}" \
		"-Dzookeeper.log.file=${ZOO_LOG_FILE}" "-Dzookeeper.root.logger=${ZOO_LOG4J_PROP}" \
		-XX:+HeapDumpOnOutOfMemoryError -XX:OnOutOfMemoryError='kill -9 %p' \
		-cp "$CLASSPATH" $JVMFLAGS $ZOOMAIN "$ZOOCFG"
	;;
print-cmd)
	echo "\"$JAVA\" $ZOO_DATADIR_AUTOCREATE -Dzookeeper.log.dir=\"${ZOO_LOG_DIR}\" \
    -Dzookeeper.log.file=\"${ZOO_LOG_FILE}\" -Dzookeeper.root.logger=\"${ZOO_LOG4J_PROP}\" \
    -XX:+HeapDumpOnOutOfMemoryError -XX:OnOutOfMemoryError='kill -9 %p' \
    -cp \"$CLASSPATH\" $JVMFLAGS $ZOOMAIN \"$ZOOCFG\" > \"$_ZOO_DAEMON_OUT\" 2>&1 < /dev/null"
	;;
stop)
	echo -n "Stopping zookeeper ... "
	if [ ! -f "$ZOOPIDFILE" ]; then
		echo "no zookeeper to stop (could not find file $ZOOPIDFILE)"
	else
		$KILL $(cat "$ZOOPIDFILE")
		rm "$ZOOPIDFILE"
		sleep 1
		echo STOPPED
	fi
	exit 0
	;;
version)
	ZOOMAIN=org.apache.zookeeper.version.VersionInfoMain
	$JAVA -cp "$CLASSPATH" $ZOOMAIN 2>/dev/null
	;;
restart)
	shift
	"$0" stop ${@}
	sleep 3
	"$0" start ${@}
	;;
status)
	# -q is necessary on some versions of linux where nc returns too quickly, and no stat result is output
	isSSL="false"
	clientPortAddress=$($GREP "^[[:space:]]*clientPortAddress[^[:alpha:]]" "$ZOOCFG" | sed -e 's/.*=//')
	if ! [ $clientPortAddress ]; then
		clientPortAddress="localhost"
	fi
	clientPort=$($GREP "^[[:space:]]*clientPort[^[:alpha:]]" "$ZOOCFG" | sed -e 's/.*=//')
	if ! [[ "$clientPort" =~ ^[0-9]+$ ]]; then
		dataDir=$($GREP "^[[:space:]]*dataDir" "$ZOOCFG" | sed -e 's/.*=//')
		myid=$(cat "$dataDir/myid" 2>/dev/null)
		if ! [[ "$myid" =~ ^[0-9]+$ ]]; then
			echo "myid could not be determined, will not able to locate clientPort in the server configs."
		else
			clientPortAndAddress=$($GREP "^[[:space:]]*server.$myid=.*;.*" "$ZOOCFG" | sed -e 's/.*=//' | sed -e 's/.*;//')
			if [ ! "$clientPortAndAddress" ]; then
				# Client port not found in static config file. Looking in dynamic config file.
				echo "Client port not found in static config file. Looking in dynamic config file."
				# dynamicConfigFile=/data/zookeeperdata/conf/zoo.cfg.dynamic.100000000
				dynamicConfigFile=$($GREP "^[[:space:]]*dynamicConfigFile" "$ZOOCFG" | sed -e 's/.*=//')
				clientPortAndAddress=$($GREP "^[[:space:]]*server.$myid=.*;.*" "$dynamicConfigFile" | sed -e 's/.*=//' | sed -e 's/.*;//')
			fi
			if [ ! "$clientPortAndAddress" ]; then
				echo "Client port not found in the server configs"
			else
				if [[ "$clientPortAndAddress" =~ ^.*:[0-9]+ ]]; then
					if [[ "$clientPortAndAddress" =~ \[.*\]:[0-9]+ ]]; then
						# Extracts address from address:port for example extracts 127::1 from "[127::1]:2181"
						clientPortAddress=$(echo "$clientPortAndAddress" | sed -e 's|\[||' | sed -e 's|\]:.*||')
					else
						clientPortAddress=$(echo "$clientPortAndAddress" | sed -e 's/:.*//')
					fi
				fi
				# 获取端口号
				clientPort=$(echo "$clientPortAndAddress" | sed -e 's/.*://')
			fi
		fi
	fi
	if [ ! "$clientPort" ]; then
		echo "Client port not found. Looking for secureClientPort in the static config."
		secureClientPort=$($GREP "^[[:space:]]*secureClientPort[^[:alpha:]]" "$ZOOCFG" | sed -e 's/.*=//')
		if [ "$secureClientPort" ]; then
			isSSL="true"
			clientPort=$secureClientPort
		else
			echo "Unable to find either secure or unsecure client port in any configs. Terminating."
			exit 1
		fi
	fi
	# Client port found: 16309. Client address: 0.0.0.0. Client SSL: false.
	echo "Client port found: $clientPort. Client address: $clientPortAddress. Client SSL: $isSSL."
	# 获取状态
	STAT=$("$JAVA" "-Dzookeeper.log.dir=${ZOO_LOG_DIR}" "-Dzookeeper.root.logger=${ZOO_LOG4J_PROP}" "-Dzookeeper.log.file=${ZOO_LOG_FILE}" \
		-cp "$CLASSPATH" $CLIENT_JVMFLAGS $JVMFLAGS org.apache.zookeeper.client.FourLetterWordMain \
		$clientPortAddress $clientPort srvr $isSSL 2>/dev/null |
		$GREP Mode)
	# 检查状态是否为空
	if [ "x$STAT" = "x" ]; then
		if [ "$isSSL" = "true" ]; then
			# 启用了ssl
			echo " "
			echo "Note: We used secureClientPort ($secureClientPort) to establish connection, but we failed. The 'status'"
			echo "  command establishes a client connection to the server to execute diagnostic commands. Please make sure you"
			echo "  provided all the Client SSL connection related parameters in the CLIENT_JVMFLAGS environment variable! E.g.:"
			echo "  CLIENT_JVMFLAGS=\"-Dzookeeper.clientCnxnSocket=org.apache.zookeeper.ClientCnxnSocketNetty"
			echo "  -Dzookeeper.ssl.trustStore.location=/tmp/clienttrust.jks -Dzookeeper.ssl.trustStore.password=password"
			echo "  -Dzookeeper.ssl.keyStore.location=/tmp/client.jks -Dzookeeper.ssl.keyStore.password=password"
			echo "  -Dzookeeper.client.secure=true\" ./zkServer.sh status"
			echo " "
		fi
		echo "Error contacting service. It is probably not running."
		exit 1
	else
		echo $STAT
		exit 0
	fi
	;;
*)
	echo "Usage: $0 [--config <conf-dir>] {start|start-foreground|stop|version|restart|status|print-cmd}" >&2
	;;

esac

```






