---
title: config
description: config
date: 2023-10-24
weight: 70000


---

{{< alert >}}
- docker version > 17


{{< /alert >}}


> `cat /data/greatdts/configs/config.sh`
```bash
#!/usr/bin/env bash
################################################################
### 注意: 配置项有位置先后顺序依赖，移动后会出错
### 用###---标注的值在部署前必须确认
################################################################

####++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
#### common vars +++++++++++++++++++++++++++++++++++++++++++++++
####++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
export LC_CTYPE=en_US.UTF-8
export LANG=en_US.UTF-8
export LANGUAGE=en_US:en

###---管理台端口
export SW_WEB_PORT=80
###---端口起始值,目前用到4个连续端口
export PORT_BASE=10110
###迁移服务端口
export DTS_BACKEND_PORT=$PORT_BASE
###同步服务端口
export SYNC_REST_PORT=$[ $PORT_BASE + 1 ]
### 同步JM端口
export JOBMANAGER_RPC_PORT=$[ $PORT_BASE + 2 ]
### 内置的zk端口
export ZK_PORT=$[ $PORT_BASE + 3 ]


. $ROOT_DIR/configs/config_utils.sh

###### 覆盖 config_utils中的配置 #################

###---数据目录，保存任务信息，升级时候要妥善备份
export SYNC_VAR=${ROOT_DIR}/../var

####++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
#### cc config +++++++++++++++++++++++++++++++++++++++++++++++++
####++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

###---配置库信息-mysql-适合生产长期使用,需要在mysql中先建立databse，程序可自动初始化
#启动前先确认配置
export SW_STORAGE=mysql
export SW_DATA_SOURCE_USER=admin
export SW_DATA_SOURCE_PASSWORD=123456
export SW_JDBC_URL="jdbc:mysql://10.5.43.1:16310/dts182?rewriteBatchedStatements=true"
export SW_R2DBC_URL="r2dbc:mysql://10.5.43.1:16310/dts182"

###---配置库信息-h2-适合演示和功能验证,低版本centos容易报错
#export SW_STORAGE=h2
#### h2管理页面端口，需要时通过scripts/start-h2-web.sh启动
#export H2_WEB_PORT=8082
#export SW_DATA_SOURCE_USER=sa
#export SW_DATA_SOURCE_PASSWORD=
#export SW_STORAGE_H2_PATH=$ROOT_DIR/h2/

export WEBAPP_JAVA_OPTS="-Xms256M -Xmx1024M"

setup_cc

####++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
#### sync config +++++++++++++++++++++++++++++++++++++++++++++++
####++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

export FLINK_ENV_JAVA_OPTS=" -javaagent:${SW_AGENT_ROOT}/skywalking-agent.jar \
	--add-opens java.base/jdk.internal.misc=ALL-UNNAMED \
	-Dio.netty.tryReflectionSetAccessible=true \
	-Dorg.apache.flink.shaded.netty4.io.netty.tryReflectionSetAccessible=true"
export FLINK_ENV_JAVA_OPTS_JM=" -Dskywalking.agent.service_name=jobmanager  -Dskywalking.logging.file_name=agent-jm.log -Dskywalking.agent.logging.level=INFO -Dflink_type=jm"
export FLINK_ENV_JAVA_OPTS_TM=" -Dskywalking.agent.service_name=taskmanager -Dskywalking.logging.file_name=agent-tm.log -Dskywalking.agent.logging.level=INFO -Dflink_type=tm"

###采集服务端地址
export SW_GRPC_LOG_SERVER_HOST=localhost
export SW_GRPC_LOG_SERVER_PORT=$[ $BASE_PORT + 3]
export OAP_OPTIONS=" --add-opens java.base/jdk.internal.misc=ALL-UNNAMED -Dio.netty.tryReflectionSetAccessible=true "

###flink-conf.yaml的配置重载
### JM主机名，如果使用hostname，请确保/etc/hosts中有配置解析
export JOBMANAGER_RPC_ADDRESS=dts182
###JOBMANAGER进程内存大小,单位MB,纯数字
export JOBMANAGER_MEMORY_PROCESS_SIZE=1600

###单机集群配置#######################
###单机集群，根据WORKERS自动启动。多机集群时设置为no，手工修改 sync/conf/
export USE_LOCAL_CLUSTER=yes
###---主机内存分配给同步任务用的总内存,单位MB，纯数字，=0时为主机一半内存
export HOST_FLINK_MEM_SIZE=40960
###---本地worker启动的数量
export WORKERS=1
###---每个TM的SLOTS数,=0时，值为CPU_CORES * 3 / $WORKERS + 1
export TM_SLOTS=10

calc_sync_resource

###---flink的配置，相同值靠下的生效
export FLINK_PROPERTIES="\n
\n
###### override segement ######\n
${FLINK_PROPERTIES}\n
restart-strategy: fixed-delay\n
restart-strategy.fixed-delay.attempts: 3\n
restart-strategy.fixed-delay.delay: 10000\n
task.cancellation.timeout: 180000\n
high-availability.zookeeper.client.session-timeout: 30000\n
high-availability.zookeeper.client.tolerate-suspended-connections: true\n
"

setup_sync

####++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
#### dts config ++++++++++++++++++++++++++++++++++++++++++++++++
####++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
###是否使用docker运行dts，在centos7.9中可选no直接运行，方便调试,其他发行版可能有libc兼容问题
export DTS_USE_DOCKER=yes
export DTS_DOCKER_OPTS=" --net=host -v $DTS_ROOT:/opt/dts -v $DTS_ROOT/logs:/logs -e TZ=${HOST_TIMEZONE} -e BACKEND_PORT=$DTS_BACKEND_PORT "
```



{{<alert color="primary">}}
**注意**：

1. `export HOST_FLINK_MEM_SIZE=40960`：分配给dts的同步任务的总内存大小，不宜过大，内存一半左右即可
2. `export SW_WEB_PORT=80`：dts的web界面管理端口，一般就是80端口，访问：http://ip:80
3. `export PORT_BASE=10110`：
4. `export DTS_BACKEND_PORT=$PORT_BASE`：迁移服务端口
5. `export SYNC_REST_PORT=$[ $PORT_BASE + 1 ]`：同步服务端口
6. `export JOBMANAGER_RPC_PORT=$[ $PORT_BASE + 2 ]`：同步JM端口
7. `export ZK_PORT=$[ $PORT_BASE + 3 ]`：内置的zk端口







{{</alert>}}
