---
title: zkEnv.sh
description: zkEnv.sh
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



### zkEnv.sh 分析

```bash
yum -y  install snapd
service snapd start

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




## zkEnv.sh

```bash
#!/usr/bin/env bash

# 若ZOOBINDIR没有定义或为空，则变量ZOOBINDIR默认位置/usr/bin；若已经定义且部位空则使用其当前值
ZOOBINDIR="${ZOOBINDIR:-/usr/bin}"

# 表示 ZOOBINDIR 的上一级目录。这个变量通常用于指向ZooKeeper的安装根目录。假设 ZOOBINDIR 的值为 /usr/bin，则 ZOOKEEPER_PREFIX 的值为 /usr
ZOOKEEPER_PREFIX="${ZOOBINDIR}/.."


ZOOCFGDIR=/data/zookeeperdata/conf
ZOO_LOG_DIR=/data/zookeeperdata/log
ZOO_LOG4J_PROP='INFO,DAYROLLINGAppender'

# 检查是否传入了至少两个参数：zkServer.sh --config /data/zookeeperdata/conf start
if [ $# -gt 1 ]; then
# 如果第一个参数是 --config，则将第二个参数的值赋给变量 confdir，并将其设置为环境变量 ZOOCFGDIR
	if [ "--config" = "$1" ]; then
		shift
                # /data/zookeeperdata/conf
		confdir=$1
                # 处理完 --config 参数后，移除已处理的参数（通过 shift 命令）
		shift
		ZOOCFGDIR=$confdir
	fi
fi

# 若变量ZOOCFGDIR没有定义，也没指定--config，则去默认位置：${ZOOKEEPER_PREFIX}/conf 》$ZOOBINDIR/../etc/zookeeper
if [ "x$ZOOCFGDIR" = "x" ]; then
	if [ -e "${ZOOKEEPER_PREFIX}/conf" ]; then
		ZOOCFGDIR="$ZOOBINDIR/../conf"
	else
		ZOOCFGDIR="$ZOOBINDIR/../etc/zookeeper"
	fi
fi

# 执行：${ZOOCFGDIR}/zookeeper-env.sh
if [ -f "${ZOOCFGDIR}/zookeeper-env.sh" ]; then
	. "${ZOOCFGDIR}/zookeeper-env.sh"
fi

# 自定义ZOOCFG 》 zoo.cfg
if [ "x$ZOOCFG" = "x" ]; then
	ZOOCFG="zoo.cfg"
fi

# 确定ZOOCFG绝对位置
ZOOCFG="$ZOOCFGDIR/$ZOOCFG"

if [ -f "$ZOOCFGDIR/java.env" ]; then
	. "$ZOOCFGDIR/java.env"
fi

# ZOO_LOG_DIR zk日志目录
if [ "x${ZOO_LOG_DIR}" = "x" ]; then
	ZOO_LOG_DIR="$ZOOKEEPER_PREFIX/logs"
fi

# 确定日志级别与日志轮转方式
if [ "x${ZOO_LOG4J_PROP}" = "x" ]; then
	ZOO_LOG4J_PROP="INFO,CONSOLE"
fi

# 找java：JAVA_HOME》$PATH》error
if [[ -n "$JAVA_HOME" ]] && [[ -x "$JAVA_HOME/bin/java" ]]; then
	JAVA="$JAVA_HOME/bin/java"
elif type -p java; then
	JAVA=java
else
	echo "Error: JAVA_HOME is not set and java could not be found in PATH." 1>&2
	exit 1
fi

# CLASSPATH新增$ZOOCFGDIR优先
CLASSPATH="$ZOOCFGDIR:$CLASSPATH"

# 新增"$ZOOBINDIR"/../zookeeper-server/src/main/resources/lib下面所有的jar包
for i in "$ZOOBINDIR"/../zookeeper-server/src/main/resources/lib/*.jar; do
	CLASSPATH="$i:$CLASSPATH"
done

# LIBPATH
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

# ZK_SERVER_HEAP
# SERVER_JVMFLAGS
ZK_SERVER_HEAP="${ZK_SERVER_HEAP:-1000}"
export SERVER_JVMFLAGS="-Xmx${ZK_SERVER_HEAP}m $SERVER_JVMFLAGS"

# ZK_CLIENT_HEAP
# CLIENT_JVMFLAGS
ZK_CLIENT_HEAP="${ZK_CLIENT_HEAP:-256}"
export CLIENT_JVMFLAGS="-Xmx${ZK_CLIENT_HEAP}m $CLIENT_JVMFLAGS"

```









