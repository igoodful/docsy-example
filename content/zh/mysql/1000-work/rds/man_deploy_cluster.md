---
title: 手工部署集群
date: 2023-10-23
description: man_deploy_cluster


weight: 1000
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
### 场景描述：
{{< /alert >}}


| host  | ip          | os         |
|:------|:------------|:-----------|
| node1 | 10.10.10.11 | CentOS 7.9 |
| node2 | 10.10.10.12 | CentOS 7.9 |
| node3 | 10.10.10.13 | CentOS 7.9 |


### 系统参数优化

```bash
useradd mysql
echo mysql:QWer12#$ | chpasswd
echo 'QWer12#$' | passwd --stdin mysql
echo 'mysql	ALL=(ALL)	NOPASSWD: ALL' >> /etc/sudoers

```








### 部署 MySQL

#### 拷贝router解压后的libs目录下的libjemalloc.so到/usr/lib
```bash
cp libjemalloc.so /usr/lib
sudo chmod 755 /usr/lib/libjemalloc.so
```



#### 配置 my16315.conf

> 三个实例的server-id必须不一样
```bash

```


```bash
cd /data/app/mysql-8.0.32/bin

./mysqld --defaults-file= /data/mysqldata/16315fff-f18a-4634-8c14-966e81b7bf82/my16315.conf  --initialize

grep pass /data/mysqldata/16315fff-f18a-4634-8c14-966e81b7bf82/logfile/mysqld.err

/data/app/mysql-8.0.32/bin/mysqld_safe --defaults-file=/data/mysqldata/16315fff-f18a-4634-8c14-966e81b7bf82/my16315.conf --user=mysql --datadir=/data/mysqldata/16315fff-f18a-4634-8c14-966e81b7bf82/dbdata


```

#### 修改密码：root@'localhost'
> /data/app/mysql-8.0.32/bin/mysql -S /data/mysqldata/16315fff-f18a-4634-8c14-966e81b7bf82/socket/mysql.sock -P16315 -uroot -p
```sql
alter user root@'localhost' identified by '123456';
```
#### 创建



#### 创建 mysql_16315.service
```bash
[root@k8s-node-71 ~]# cat /usr/lib/systemd/system/mysql_16315.service
[Unit]
Description=mysql Server
After=network.target

[Install]
WantedBy=multi-user.target

[Service]
User=mysql
Group=mysql

Type=simple
Environment=""
TimeoutSec=0
ExecStart=/data/app/mysql/bin/mysqld_safe --defaults-file=/data/mysqldata/16315fff-c76a-42a9-a2d8-4c3bba7a37d1/my16315.conf --user=mysql --datadir=/data/mysqldata/16315fff-c76a-42a9-a2d8-4c3bba7a37d1/dbdata
LimitNOFILE = 1024000
LimitNPROC = 1024000
PrivateTmp=false

```



### zookeeper
> `./zkCli.sh -server 127.0.0.1:16309`
```bash
addauth digest db:db
ls /
create /dbscale ""
create /dbscale/cluster ""
create /dbscale/keepalive ""
create /dbscale/dynamic_operation ""
create /dbscale/configuration ""
create /dbscale/cluster/nodes ""
create /dbscale/cluster/online_nodes ""
create /dbscale/keepalive/keepalive_init_info ""
create /dbscale/keepalive/keepalive_update_info ""
create /dbscale/keepalive/mul_sync_info ""
create /dbscale/dynamic_operation/dynamic_operation_info ""
create /dbscale/dynamic_operation/dynamic_cluster_management_info ""
create /dbscale/dynamic_operation/dynamic_cluster_management_init_info ""
create /dbscale/dynamic_operation/dynamic_config_info ""
create /dbscale/dynamic_operation/session_info ""
create /dbscale/dynamic_operation/block_info ""
create /dbscale/configuration/master_config_info ""
create /dbscale/configuration/changed_config_info ""
```








### router
> node-host-addr = 172.17.134.76  必须为本机的ip
>
> 第一次启动的时候配置文件里是明文密码，不能开启encode-password
>
> encode-password=0

```yaml
[root@k8s-node-76 dbscale]# cat dbscale.conf
[main]
driver = mysql
log-file = /data/dbscalelog/dbscale.log
pid-file = dbscale.pid
do-audit-log = NONE
admin-user = dbscale_internal
dbscale-internal-user = dbscale_internal
admin-password = IVFBWjJ3c3g=
datasource-in-one = 1
check-part-primary = 1
authenticate-source = normal_0
encode-password = 1
on-view = 1
lower-case-table-names = 1
use-partial-parse = 1
enable-acl = 1
do-auth-on-dbscale = 0
enable-xa-transaction = 0
enable-dead-lock-detect = 0
disable-parallel-modify = 1
conn-pool-num = 10
default-login-schema = information_schema
enable-session-swap = 0
enable-session-swap-during-execution = 1
support-show-warning = 1
support-tokudb = 0
supreme-admin-user = admin
max-fetchnode-ready-rows-size = 10000
max-mergenode-ready-rows-size = 40000000
dbscale-cluster-id = 13476
auto-master-failover-flashback = 1
conn-always-set-session-var = 1
monitor-interval = 2
insert-select-sql-size = 65536
enable-simplified-float-number = 1
spark-password = dbscalc=
max-dataserver-monitor = 40
monitor-net-timeout = 15
backend-sql-net-timeout = 60
mul-dbscale-forward-timeout = 100
wait-timeout = 172800
max-replication-delay = 30
max-slave-retrived-binlog-pos-delay-for-wakeup = 1000000
migrate-write-thread = 10
migrate-write-packet-size = 16777216
enable-last-insert-id = 1
backlog = 10000
node-host-addr = 172.17.134.76
multiple-mode = 1
zookeeper-password = ZGJzY2FsZQ==
zk-log-file = /data/dbscalelog/zookeeper.log
zookeeper-host = 172.17.134.76:16309,172.17.134.71:16309,172.17.134.77:16309
cluster-user = dbscale_internal
cluster-password = IVFBWjJ3c3g=
function-type-file = function_type.txt
get-connection-retry-times = 1
enable-get-rep-connection = 1
monitor-retry-count = 1
monitor-retry-count-stable = 2
enable-oracle-sequence = 1
dbscale-safe-sql-mode = 2
dbscale-acl-strict-mode = 0
max-load-ready-packets = 64
load-analysis-num = 6
max-load-analysis-wait-size = 12
max-load-packet-size = 16777216
allow-modify-server-directly = 1
connection-pool-admin-interval = 60
thread-pool-min = 10
thread-pool-max = 10001
thread-pool-low = 900
default-session-variables = character_set_database:transaction_read_only
close-load-conn = 1
dbscale-hosts = %
catch-sigsegv = 1
authenticate-with-pool-conn = 0
transparent-mode = 0
max-single-sort-rows = 100000
max-load-once-packet-num = 65535
max-cross-join-moved-rows = 50000000
dbscale-master-rescramble-delay = 1
log-level = INFO


[driver mysql]
type = MySQLDriver
port = 16310
transparent-port = 23399
bind-address = 0.0.0.0


[catalog default]
data-source = normal_0





[data-source normal_0]
group-id = 100
user = dbscale_internal
password = IVFBWjJ3c3g=
type = replication
semi-sync-on = 1
load-balance-strategy = MASTER
master = normal_0_2-1-1000-400-800
slave = normal_0_0-1-1000-400-800
slave = normal_0_1-1-1000-400-800


[data-server normal_0_0]
host = 172.17.134.77
port = 16315
user = dbscale_internal
password = IVFBWjJ3c3g=
master-priority = 100
remote-user = root
remote-port = 22

[data-server normal_0_1]
host = 172.17.134.71
port = 16315
user = dbscale_internal
password = IVFBWjJ3c3g=
master-priority = 100
remote-user = root
remote-port = 22

[data-server normal_0_2]
host = 172.17.134.76
port = 16315
user = dbscale_internal
password = IVFBWjJ3c3g=
master-priority = 50
remote-user = root
remote-port = 22

```






















































