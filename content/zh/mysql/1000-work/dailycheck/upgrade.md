---
title: 升级
description: upgrade
date: 2023-10-12
weight: 60000


---

{{< alert >}}


{{< /alert >}}



###
```sql
[mysql@k8s-node-70 adm_rc3]$ ./adm_install.sh upgrade
选择升级方式:
1. 读取已有ADM安装脚本中的配置策略
2. 读取当前ADM安装脚本中的配置策略
请输入你的选择:2
读取脚本中新的ADM平台安装策略升级
Upgrade Start...
Backup ADM container...
adm-cdbc
stop adm-cdbc container succeess on 172.17.139.57
rename 'adm-cdbc' container to 'adm-cdbc-19802' succeess on 172.17.139.57
adm-cdbc
stop adm-cdbc container succeess on 172.17.134.70
rename 'adm-cdbc' container to 'adm-cdbc-11285' succeess on 172.17.134.70
Checking GreatADM-CDBC-6.0.2-RC-3-7e4b4f16-x86_64.tar.xz and decompress
Decompress GreatADM-CDBC-6.0.2-RC-3-7e4b4f16-x86_64.tar.xz
GreatADM-CDBC-6.0.2-RC-3-7e4b4f16/
GreatADM-CDBC-6.0.2-RC-3-7e4b4f16/GreatADM-CDBC-6.0.2-RC-3-7e4b4f16.tar
GreatADM-CDBC-6.0.2-RC-3-7e4b4f16/docker-20.10.8.tgz
GreatADM-CDBC-6.0.2-RC-3-7e4b4f16/sshpass
Check GreatADM-CDBC-6.0.2-RC-3-7e4b4f16/ok is successful
Chdir to GreatADM-CDBC-6.0.2-RC-3-7e4b4f16
Checking Install Images Integrity
OK! ADM image repo tag is c12a249b445e
Checking ADM_IP_ARRAY
Will install on servers: 172.17.134.70
Checking sshpass
sshpass check successful
Checking the Firewall Status
Firewall is not active.
Firewall is disabled.
Firewall is not active.
Firewall is disabled.
Checking the Installation Environment
check docker successful on 172.17.134.70
check docker successful on 172.17.139.57
Install Start...
Now need create sub dirs under '/data/cdbc' dir on 172.17.134.70
load ADM image... ./GreatADM-CDBC-6.0.2-RC-3-7e4b4f16.tar start on 172.17.139.57, Please wait 5-10 minutes
load ADM image... ./GreatADM-CDBC-6.0.2-RC-3-7e4b4f16.tar start on 172.17.134.70, Please wait 5-10 minutes
Loaded image: docker.greatrds.com:5000/greatadm-cdbc:6.0.2-RC-3-7e4b4f16
c12a249b445e
load ADM image ./GreatADM-CDBC-6.0.2-RC-3-7e4b4f16.tar succeess on 172.17.139.57
Loaded image: docker.greatrds.com:5000/greatadm-cdbc:6.0.2-RC-3-7e4b4f16
c12a249b445e
load ADM image ./GreatADM-CDBC-6.0.2-RC-3-7e4b4f16.tar succeess on 172.17.134.70
Connecting to the METADB 172.17.130.48:3311 ...
The connection ADM METADB:adm is successful on 172.17.130.48:3311
The connection DAS METADB:adm is successful on 172.17.130.48:3311
Run ADM migrations...
INFO  [alembic.runtime.migration] Context impl MySQLImpl.
INFO  [alembic.runtime.migration] Will assume non-transactional DDL.
APPENV: AppEnvTypes.PROD
SQLALCHEMY_DATABASE_URI: mysql+aiomysql://admin:123456@172.17.130.48:3311/adm?charset=utf8mb4
...
Migrations ADM success on 172.17.134.70
Run DAS migrations...
INFO  [alembic.runtime.migration] Context impl MySQLImpl.
INFO  [alembic.runtime.migration] Will assume non-transactional DDL.
APPENV: AppEnvTypes.PROD
SQLALCHEMY_DATABASE_URI: mysql+aiomysql://admin:123456@172.17.130.48:3311/das?charset=utf8mb4
...
Migrations DAS success on 172.17.134.70
Initialize metadata
2023-12-07 14:24:39.155 | INFO     | __main__:main:84 - Initial data start
2023-12-07 14:24:40.506 | INFO     | __main__:main:91 - SQL initialization is complete! 49 error: 0;
Initialize success on 172.17.134.70
docker run -d --read-only --name adm-cdbc -p 80:8080 --cpu-period 100000 --cpu-quota 1600000 --memory=20G --env-file /data/cdbc/greatadm.conf -e ADM_ADDRESS=172.17.134.70 -e DAS_ADDRESS=172.17.134.70 -v /data/cdbc/:/data/ --restart on-failure:5 c12a249b445e
docker run -d --read-only --name adm-cdbc -p 80:8080 --cpu-period 100000 --cpu-quota 1600000 --memory=20G --env-file /data/cdbc/greatadm.conf -e ADM_ADDRESS=172.17.139.57 -e DAS_ADDRESS=172.17.139.57 -v /data/cdbc/:/data/ --restart on-failure:5 c12a249b445e
47cc28978f492f782a51d781f0b2c4a590797755ecb74561e7badb125815ad32
Start adm-cdbc container with image c12a249b445e succeess on 172.17.134.70
waiting... for ADM web console ready, max wait time is 5 min
0fbaeb5d4cb73691bce8db995c7a150697a3888d7ce29b0ad954fee87741a0c5
Start adm-cdbc container with image c12a249b445e succeess on 172.17.139.57
waiting... for ADM web console ready, max wait time is 5 min
Start successful on 172.17.134.70
Start successful on 172.17.139.57
Cant found db packages to sync
2023-12-07 14:25:58.833 | INFO     | __main__:main:49 - Trusteeship node start
sys:1: SAWarning: relationship 'ClusterGdbc.maintenances' will copy column cluster_gdbc.id to column association.cluster_id, which conflicts with relationship(s): 'MaintenanceAlert.clusters' (copies cluster_gdbc.id to association.cluster_id). If this is not the intention, consider if these relationships should be linked with back_populates, or if viewonly=True should be applied to one or more if they are read-only. For the less common case that foreign key constraints are partially overlapping, the orm.foreign() annotation can be used to isolate the columns that should be written towards.   To silence this warning, add the parameter 'overlaps="clusters"' to the 'ClusterGdbc.maintenances' relationship. (Background on this error at: https://sqlalche.me/e/14/qzyx)
sys:1: SAWarning: relationship 'ClusterGdbc.maintenances' will copy column maintenance_alert.id to column association.maintenance_id, which conflicts with relationship(s): 'MaintenanceAlert.clusters' (copies maintenance_alert.id to association.maintenance_id). If this is not the intention, consider if these relationships should be linked with back_populates, or if viewonly=True should be applied to one or more if they are read-only. For the less common case that foreign key constraints are partially overlapping, the orm.foreign() annotation can be used to isolate the columns that should be written towards.   To silence this warning, add the parameter 'overlaps="clusters"' to the 'ClusterGdbc.maintenances' relationship. (Background on this error at: https://sqlalche.me/e/14/qzyx)
2023-12-07 14:25:59.009 | INFO     | __main__:main:56 - trusteeship node success
trusteeship node success on 172.17.134.70 adm-cdbc
2023-12-07 14:26:09.291 | INFO     | __main__:main:49 - Trusteeship node start
sys:1: SAWarning: relationship 'ClusterGdbc.maintenances' will copy column cluster_gdbc.id to column association.cluster_id, which conflicts with relationship(s): 'MaintenanceAlert.clusters' (copies cluster_gdbc.id to association.cluster_id). If this is not the intention, consider if these relationships should be linked with back_populates, or if viewonly=True should be applied to one or more if they are read-only. For the less common case that foreign key constraints are partially overlapping, the orm.foreign() annotation can be used to isolate the columns that should be written towards.   To silence this warning, add the parameter 'overlaps="clusters"' to the 'ClusterGdbc.maintenances' relationship. (Background on this error at: https://sqlalche.me/e/14/qzyx)
sys:1: SAWarning: relationship 'ClusterGdbc.maintenances' will copy column maintenance_alert.id to column association.maintenance_id, which conflicts with relationship(s): 'MaintenanceAlert.clusters' (copies maintenance_alert.id to association.maintenance_id). If this is not the intention, consider if these relationships should be linked with back_populates, or if viewonly=True should be applied to one or more if they are read-only. For the less common case that foreign key constraints are partially overlapping, the orm.foreign() annotation can be used to isolate the columns that should be written towards.   To silence this warning, add the parameter 'overlaps="clusters"' to the 'ClusterGdbc.maintenances' relationship. (Background on this error at: https://sqlalche.me/e/14/qzyx)
2023-12-07 14:26:09.456 | INFO     | __main__:main:56 - trusteeship node success
trusteeship node success on 172.17.139.57 adm-cdbc
2023-12-07 14:26:13.752 | INFO     | __main__:main:85 - Initial grafana data start
2023-12-07 14:26:13.899 | INFO     | __main__:main:92 - SQL initialization is complete!
Initialize grafana_data succeess on 172.17.134.70 adm-cdbc
2023-12-07 14:26:18.329 | INFO     | __main__:main:85 - Initial grafana data start
2023-12-07 14:26:18.437 | INFO     | __main__:main:92 - SQL initialization is complete!
Initialize grafana_data succeess on 172.17.139.57 adm-cdbc
Please use the the following url ( http://172.17.134.70:80 ) to access adm console  through a browser:
The default username and password refer to the configuration of FIRST_SUPERUSER and FIRST_SUPERUSER_PASSWORD in adm.conf when you installed
done

```


### 中交升级
> 2024-01-08 17:00:00

#### 一、上传对应的安装包

| md5                              | package                                       |
|:---------------------------------|:----------------------------------------------|
| 2527bac90cc29dbc11eb1a868b7eb9a5 | GreatADM-CDBC-6.0.2-RC-3-7e4b4f16-aarch64.zip |
| 6ead65bf6f4924ce8a21d4462dca1cf5 | GreatADM-CDBC-6.0.2-RC-3-7e4b4f16-x86_64.zip  |

```bash
[greatdb@adm10 adm_new]$ uname -a
Linux adm10 4.19.90-52.15.v2207.ky10.aarch64 #1 SMP Thu Nov 24 15:59:41 CST 2022 aarch64 aarch64 aarch64 GNU/Linux

[greatdb@adm10 adm_new]$ ps aux|grep mysqld
greatdb    81691  3.0  7.8 32061504 2523648 pts/0 Sl  16:43   1:22 /usr/local/mysql/bin/mysqld --basedir=/usr/local/mysql --datadir=/data/mysql/data --plugin-dir=/usr/local/mysql/lib/plugin --user=greatdb --log-error=/data/mysql/logs/error.log --open-files-limit=65536 --pid-file=/data/mysql/var/mysql.pid --socket=/data/mysql/var/mysql.sock --port=3306
greatdb   123491  0.0  0.0 214080  1600 pts/0    S+   17:28   0:00 grep mysqld

```

#### 二、备份元数据库
> 先停止两个元数据库，然后物理备份完成，最后开启两个元数据库

#### 三、注意元数据库的信息：my.cnf
```bash
[greatdb@adm10 adm_new]$ cat /etc/my.cnf
[client]
#default login user
user=root
#default password
password=123
socket=/data/mysql/var/mysql.sock

[mysqld]
#########genarel config##########
#the user runnging mysqld process
user=greatdb
#base directory
basedir=/usr/local/mysql
#data directory
datadir=/data/mysql/data
#temp directory
tmpdir=/data/mysql/tmp
#db server character set
character_set_server=utf8mb4
#default engine
default_storage_engine=innodb
#server id
server_id=1
#service port
port=3306
#max connections
max_connections=1000
max_allowed_packet=16777216
#skip resolve name
skip_name_resolve=on
#pid file's directory
pid-file=/data/mysql/var/mysql.pid
#ignore table name case
lower_case_table_names=1
log_bin_trust_function_creators=1
secure_file_priv=''
socket=/data/mysql/var/mysql.sock
skip_grant_tables=0
thread_cache_size=1024
table_open_cache=32768
#time_zone = '+8:00'
default-time-zone=+8:00

######log part####
#swith on genarel log
general_log=off
#name and directory of genarel log
general_log_file=/data/mysql/logs/general.log
#swith on binnary log and set the file
log-bin=/data/mysql/logs/mysql-bin
#index of bannary log
log_bin_index=/data/mysql/logs/binlog.index
#binlog file size
max_binlog_size=1G
#swith bannary log
slow_query_log=off
#name and directory of slow query log
slow_query_log_file=/data/mysql/logs/slow.log
#the unit of slow log ，second
long_query_time=6
#record query not use index into slow log
log_queries_not_using_indexes=1
#error log setting
log_error=/data/mysql/logs/error.log
#auto clear binnary log , day
expire_logs_days=14
log_timestamps=SYSTEM
#relay log info storage in table
relay_log_info_repository=TABLE
#relay log file name
relay_log=/data/mysql/logs/relay-log
#write binlog when slave apply relay-log
log_slave_updates=off
#binlog format
binlog_format=ROW


######innodb setting ######
#innodb memory size，byte
innodb_buffer_pool_size=24567M
#innodb instance number,if buffer pool size less than 1024M,auto set to 1
innodb_buffer_pool_instances=16
#the chunk size for InnoDB buffer pool resizing operations, (innodb_buffer_pool_size / innodb_buffer_pool_chunk_size) should not exceed 1000.
innodb_buffer_pool_chunk_size=128M
#dump cache from memory to disk when server shutdown
innodb_buffer_pool_dump_at_shutdown=off
#record page cache immediate
innodb_buffer_pool_dump_now=off
#import cache from disk to memory when server startup
innodb_buffer_pool_load_at_startup=off
#immediate cache buffer_pool
innodb_buffer_pool_load_now=off
#change buffer size,unit percentage of innodb_buffer_pool_size
innodb_change_buffer_max_size=25
#log buffer 8M to 32M
innodb_log_buffer_size=8M
#the size of redo log
innodb_log_file_size=1024M
#the action of write log to disk:0--flush per second;1--flush on tranction commit(default); 2--0 and 1
innodb_flush_log_at_trx_commit=1
#thread of flush dirty pages,if larger than pool instances,automatically set to the same value as pool instances
innodb_page_cleaners=4
#enable innodb monitor
innodb_monitor_enable=all
#innodb can open files
innodb_open_files=32263
table_open_cache=32263
open_files_limit=65536
innodb_data_file_path=ibdata1:12M:autoextend
innodb_undo_tablespaces=3

######MyIsam setting######
key_buffer_size=64M
read_buffer_size=256K
read_rnd_buffer_size=256K
sort_buffer_size=256K
join_buffer_size=256K


####replication setting#########
log-bin-trust-function-creators=1
#enable gtid mode
gtid_mode=ON
#enforce gtid consistency
enforce_gtid_consistency=ON
#master info storage in table
master_info_repository=TABLE
#semi_sync
#rpl_semi_sync_master_enabled=on
#rpl_semi_sync_master_timeout=1000
#rpl_semi_sync_slave_enabled=on
#multi thread
slave_parallel_type=logical_clock
slave_parallel_workers=4
#keep the order on slave same as primary
slave_preserve_commit_order=on

```





#### 四、解压
```bash
unzip GreatADM-CDBC-6.0.2-RC-3-7e4b4f16-aarch64.zip
```


#### 五、修改配置：adm_install.sh
```bash
[greatdb@adm10 adm_new]$ ll
总用量 2082980
-rwxr-xr-x 1 greatdb greatdb      28289  1月  8 16:14 adm_install.sh
drwxr-xr-x 2 greatdb greatdb       4096 11月 30 10:08 GreatADM-CDBC-6.0.2-RC-3-7e4b4f16
-rw-r--r-- 1 greatdb greatdb 1066433872 11月 30 10:40 GreatADM-CDBC-6.0.2-RC-3-7e4b4f16-aarch64.tar.xz
-rw-r--r-- 1 greatdb greatdb 1066493765 12月  7 14:05 GreatADM-CDBC-6.0.2-RC-3-7e4b4f16-aarch64.zip
[greatdb@adm10 adm_new]$ pwd
/home/greatdb/adm_new
[greatdb@adm10 adm_new]$ ll ../
总用量 3556000
-rwxr-xr-x 1 greatdb greatdb      28289 10月 24 09:54 adm_install.sh
drwxr-xr-x 3 greatdb greatdb       4096  1月  8 17:06 adm_new
drwxr-xr-x 2 greatdb greatdb       4096 10月  8 13:50 GreatADM-CDBC-6.0.2-RC-1-e2910fe5
-rw-r--r-- 1 greatdb greatdb 1067512848  6月 30  2023 GreatADM-CDBC-6.0.2-RC-1-e2910fe5-aarch64.tar.xz
-rw-r--r-- 1 greatdb greatdb 1067574111 10月  8 13:48 GreatADM-CDBC-6.0.2-RC-1-e2910fe5-aarch64.zip
-rw-r--r-- 1 greatdb greatdb  665939140 10月  8 14:07 GreatDBPackage-5.1.9-GA-1-Linux-glibc2.28-aarch64.tar.xz
-rw-r--r-- 1 greatdb greatdb  840257072 10月  8 14:07 GreatDBPackage-5.1.9-RC-4-2e243b28-Linux-glibc2.28-x86_64.tar.xz
[greatdb@adm10 adm_new]$ diff adm_install.sh ../adm_install.sh
8c8
< ADM_PACKAGE_VERSION=GreatADM-CDBC-6.0.2-RC-3-7e4b4f16
---
> ADM_PACKAGE_VERSION=GreatADM-CDBC-6.0.2-RC-1-e2910fe5
79c79
< ADM_IMAGE=./GreatADM-CDBC-6.0.2-RC-3-7e4b4f16.tar
---
> ADM_IMAGE=./GreatADM-CDBC-6.0.2-RC-1-e2910fe5.tar

```




#### 六、执行升级：`./adm_install.sh upgrade`
```bash
[greatdb@adm10 adm_new]$ ./adm_install.sh upgrade
选择升级方式:
1. 读取已有ADM安装脚本中的配置策略
2. 读取当前ADM安装脚本中的配置策略
请输入你的选择:2
读取脚本中新的ADM平台安装策略升级
Upgrade Start...
Backup ADM container...

Authorized users only. All activities may be monitored and reported.

Authorized users only. All activities may be monitored and reported.
adm-cdbc
stop adm-cdbc container succeess on 10.5.43.11

Authorized users only. All activities may be monitored and reported.
adm-cdbc
stop adm-cdbc container succeess on 10.5.43.10

Authorized users only. All activities may be monitored and reported.
rename 'adm-cdbc' container to 'adm-cdbc-18122' succeess on 10.5.43.11
rename 'adm-cdbc' container to 'adm-cdbc-19083' succeess on 10.5.43.10
Checking GreatADM-CDBC-6.0.2-RC-3-7e4b4f16-aarch64.tar.xz and decompress
Decompress GreatADM-CDBC-6.0.2-RC-3-7e4b4f16-aarch64.tar.xz
GreatADM-CDBC-6.0.2-RC-3-7e4b4f16/
GreatADM-CDBC-6.0.2-RC-3-7e4b4f16/GreatADM-CDBC-6.0.2-RC-3-7e4b4f16.tar
GreatADM-CDBC-6.0.2-RC-3-7e4b4f16/docker-20.10.8.tgz
GreatADM-CDBC-6.0.2-RC-3-7e4b4f16/sshpass
./adm_install.sh:行259: touch：未找到命令
Check GreatADM-CDBC-6.0.2-RC-3-7e4b4f16/ok is successful
Chdir to GreatADM-CDBC-6.0.2-RC-3-7e4b4f16
Checking Install Images Integrity
OK! ADM image repo tag is afdd53d21d10
Checking ADM_IP_ARRAY
Will install on servers: 10.5.43.10
Checking sshpass
sshpass check successful
Checking the Firewall Status

Authorized users only. All activities may be monitored and reported.
Firewall is not active.

Authorized users only. All activities may be monitored and reported.
Firewall is disabled.

Authorized users only. All activities may be monitored and reported.
Firewall is not active.

Authorized users only. All activities may be monitored and reported.
Firewall is disabled.
Checking the Installation Environment

Authorized users only. All activities may be monitored and reported.
check docker successful on 10.5.43.10

Authorized users only. All activities may be monitored and reported.

Authorized users only. All activities may be monitored and reported.
check docker successful on 10.5.43.11

Authorized users only. All activities may be monitored and reported.
Install Start...
Now need create sub dirs under '/data/greatadm/cdbc' dir on 10.5.43.10

Authorized users only. All activities may be monitored and reported.

Authorized users only. All activities may be monitored and reported.

Authorized users only. All activities may be monitored and reported.

Authorized users only. All activities may be monitored and reported.

Authorized users only. All activities may be monitored and reported.

Authorized users only. All activities may be monitored and reported.
load ADM image... ./GreatADM-CDBC-6.0.2-RC-3-7e4b4f16.tar start on 10.5.43.11, Please wait 5-10 minutes
load ADM image... ./GreatADM-CDBC-6.0.2-RC-3-7e4b4f16.tar start on 10.5.43.10, Please wait 5-10 minutes

Authorized users only. All activities may be monitored and reported.

Authorized users only. All activities may be monitored and reported.

Authorized users only. All activities may be monitored and reported.

Authorized users only. All activities may be monitored and reported.
Loaded image: docker.greatrds.com:5000/greatadm-cdbc:6.0.2-RC-3-7e4b4f16
afdd53d21d10
load ADM image ./GreatADM-CDBC-6.0.2-RC-3-7e4b4f16.tar succeess on 10.5.43.11
Loaded image: docker.greatrds.com:5000/greatadm-cdbc:6.0.2-RC-3-7e4b4f16
afdd53d21d10
load ADM image ./GreatADM-CDBC-6.0.2-RC-3-7e4b4f16.tar succeess on 10.5.43.10
Connecting to the METADB 10.5.43.10:3306 ...

Authorized users only. All activities may be monitored and reported.
The connection ADM METADB:adm is successful on 10.5.43.10:3306

Authorized users only. All activities may be monitored and reported.
The connection DAS METADB:adm is successful on 10.5.43.10:3306
Run ADM migrations...

Authorized users only. All activities may be monitored and reported.
INFO  [alembic.runtime.migration] Context impl MySQLImpl.
INFO  [alembic.runtime.migration] Will assume non-transactional DDL.
APPENV: AppEnvTypes.PROD
SQLALCHEMY_DATABASE_URI: mysql+aiomysql://root:7SaZE!9zL4@10.5.43.10:3306/adm?charset=utf8mb4
...
Migrations ADM success on 10.5.43.10
Run DAS migrations...

Authorized users only. All activities may be monitored and reported.
INFO  [alembic.runtime.migration] Context impl MySQLImpl.
INFO  [alembic.runtime.migration] Will assume non-transactional DDL.
APPENV: AppEnvTypes.PROD
SQLALCHEMY_DATABASE_URI: mysql+aiomysql://root:7SaZE!9zL4@10.5.43.10:3306/das?charset=utf8mb4
...
Migrations DAS success on 10.5.43.10
Initialize metadata

Authorized users only. All activities may be monitored and reported.
2024-01-08 17:11:52.233 | INFO     | __main__:main:84 - Initial data start
2024-01-08 17:11:53.400 | INFO     | __main__:main:91 - SQL initialization is complete! 49 error: 0;
Initialize success on 10.5.43.10
docker run -d --read-only --name adm-cdbc -p 80:8080 --cpu-period 100000 --cpu-quota 400000 --memory=20G --env-file /data/greatadm/cdbc/greatadm.conf -e ADM_ADDRESS=10.5.43.10 -e DAS_ADDRESS=10.5.43.10 -v /data/greatadm/cdbc/:/data/ --restart on-failure:5 afdd53d21d10
docker run -d --read-only --name adm-cdbc -p 80:8080 --cpu-period 100000 --cpu-quota 400000 --memory=20G --env-file /data/greatadm/cdbc/greatadm.conf -e ADM_ADDRESS=10.5.43.11 -e DAS_ADDRESS=10.5.43.11 -v /data/greatadm/cdbc/:/data/ --restart on-failure:5 afdd53d21d10

Authorized users only. All activities may be monitored and reported.

Authorized users only. All activities may be monitored and reported.
736fd476f3764edf528afbfe0bdfe1fa6f18e40605a934467b137fae09429bb3
Start adm-cdbc container with image afdd53d21d10 succeess on 10.5.43.11
waiting... for ADM web console ready, max wait time is 5 min

Authorized users only. All activities may be monitored and reported.
da4ae1133f840d5893d430a9bd9355007025b335f15c4660e5667a23f21b48d2
Start adm-cdbc container with image afdd53d21d10 succeess on 10.5.43.10
waiting... for ADM web console ready, max wait time is 5 min

Authorized users only. All activities may be monitored and reported.
Start successful on 10.5.43.11
Start successful on 10.5.43.10
Cant found db packages to sync

Authorized users only. All activities may be monitored and reported.
2024-01-08 17:13:34.593 | INFO     | __main__:main:49 - Trusteeship node start
sys:1: SAWarning: relationship 'ClusterGdbc.maintenances' will copy column cluster_gdbc.id to column association.cluster_id, which conflicts with relationship(s): 'MaintenanceAlert.clusters' (copies cluster_gdbc.id to association.cluster_id). If this is not the intention, consider if these relationships should be linked with back_populates, or if viewonly=True should be applied to one or more if they are read-only. For the less common case that foreign key constraints are partially overlapping, the orm.foreign() annotation can be used to isolate the columns that should be written towards.   To silence this warning, add the parameter 'overlaps="clusters"' to the 'ClusterGdbc.maintenances' relationship. (Background on this error at: https://sqlalche.me/e/14/qzyx)
sys:1: SAWarning: relationship 'ClusterGdbc.maintenances' will copy column maintenance_alert.id to column association.maintenance_id, which conflicts with relationship(s): 'MaintenanceAlert.clusters' (copies maintenance_alert.id to association.maintenance_id). If this is not the intention, consider if these relationships should be linked with back_populates, or if viewonly=True should be applied to one or more if they are read-only. For the less common case that foreign key constraints are partially overlapping, the orm.foreign() annotation can be used to isolate the columns that should be written towards.   To silence this warning, add the parameter 'overlaps="clusters"' to the 'ClusterGdbc.maintenances' relationship. (Background on this error at: https://sqlalche.me/e/14/qzyx)
2024-01-08 17:13:34.786 | INFO     | __main__:main:56 - trusteeship node success
trusteeship node success on 10.5.43.10 adm-cdbc

Authorized users only. All activities may be monitored and reported.
2024-01-08 17:14:05.759 | INFO     | __main__:main:49 - Trusteeship node start
sys:1: SAWarning: relationship 'ClusterGdbc.maintenances' will copy column cluster_gdbc.id to column association.cluster_id, which conflicts with relationship(s): 'MaintenanceAlert.clusters' (copies cluster_gdbc.id to association.cluster_id). If this is not the intention, consider if these relationships should be linked with back_populates, or if viewonly=True should be applied to one or more if they are read-only. For the less common case that foreign key constraints are partially overlapping, the orm.foreign() annotation can be used to isolate the columns that should be written towards.   To silence this warning, add the parameter 'overlaps="clusters"' to the 'ClusterGdbc.maintenances' relationship. (Background on this error at: https://sqlalche.me/e/14/qzyx)
sys:1: SAWarning: relationship 'ClusterGdbc.maintenances' will copy column maintenance_alert.id to column association.maintenance_id, which conflicts with relationship(s): 'MaintenanceAlert.clusters' (copies maintenance_alert.id to association.maintenance_id). If this is not the intention, consider if these relationships should be linked with back_populates, or if viewonly=True should be applied to one or more if they are read-only. For the less common case that foreign key constraints are partially overlapping, the orm.foreign() annotation can be used to isolate the columns that should be written towards.   To silence this warning, add the parameter 'overlaps="clusters"' to the 'ClusterGdbc.maintenances' relationship. (Background on this error at: https://sqlalche.me/e/14/qzyx)
2024-01-08 17:14:05.946 | INFO     | __main__:main:56 - trusteeship node success
trusteeship node success on 10.5.43.11 adm-cdbc

Authorized users only. All activities may be monitored and reported.
2024-01-08 17:14:34.135 | INFO     | __main__:main:85 - Initial grafana data start
2024-01-08 17:14:34.207 | INFO     | __main__:main:92 - SQL initialization is complete!
Initialize grafana_data succeess on 10.5.43.10 adm-cdbc

Authorized users only. All activities may be monitored and reported.
2024-01-08 17:15:01.916 | INFO     | __main__:main:85 - Initial grafana data start
2024-01-08 17:15:01.981 | INFO     | __main__:main:92 - SQL initialization is complete!
Initialize grafana_data succeess on 10.5.43.11 adm-cdbc
Please use the the following url ( http://10.5.43.10:80 ) to access adm console  through a browser:
The default username and password refer to the configuration of FIRST_SUPERUSER and FIRST_SUPERUSER_PASSWORD in adm.conf when you installed
done
[greatdb@adm10 adm_new]$

```
> 本次升级遇到：./adm_install.sh:行259: touch：未找到命令
>
> 然后赶紧安装了touch命令，touch命令不存在，也能成功
>
> 最后升级成功








