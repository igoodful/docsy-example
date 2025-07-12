---
title: 升级RDS
date: 2023-10-23
description: >
  RDS-5.1.9 升级至 RDS-6.0.1


weight: 2000
---

{{< alert >}}

- 原来版本：RDS-5.1.9.0rc16-release-10-17-x64_redhat8.tar.gz （md5sum：498041fbc403b9fde3d614344d36bca9）

- 目标版本：RDS-6.0.1-rc3-3eff6bde-x64-arm64.tar.gz （md5sum：d437410d97c6322e28f38bbd80aeed0c）


{{< /alert >}}




### 准备

1. 升级管控平台需要在业务低峰期执行相关操作，最好在可运维时间段执行，升级时间正常情况下30分钟左右


2. 升级前需检查管控平台各组件状态，执行如下命令确认各组件运行正常。查看11容器是否运行正常。

```sql
[root@k8s-node-70 ~]# docker ps
CONTAINER ID   IMAGE                       COMMAND                  CREATED        STATUS        PORTS                                                                                                                                      NAMES
96b282c509cc   greatrds_auth:5.1.8.0rc14   "/bin/sh -c 'gunicor…"   15 hours ago   Up 15 hours   0.0.0.0:9018->9018/tcp                                                                                                                     greatrds-auth
3d3bdfd9693d   greatrds:5.1.9.0rc16        "./docker-entrypoint…"   15 hours ago   Up 15 hours                                                                                                                                              greatrds-monitor
bf2b522f81d0   greatrds:5.1.9.0rc16        "./docker-entrypoint…"   15 hours ago   Up 15 hours                                                                                                                                              greatrds-conductor
d4dbada385d0   greatrds:5.1.9.0rc16        "./docker-entrypoint…"   15 hours ago   Up 15 hours   0.0.0.0:9010->9010/tcp                                                                                                                     greatrds-api
c1624ceb19de   nginx:1.22.0-alpine         "/docker-entrypoint.…"   15 hours ago   Up 15 hours                                                                                                                                              nginx
ae60211e0d35   rabbitmq:3.9.7-management   "docker-entrypoint.s…"   15 hours ago   Up 15 hours   0.0.0.0:4369->4369/tcp, 5671/tcp, 0.0.0.0:5672->5672/tcp, 15671/tcp, 0.0.0.0:15672->15672/tcp, 0.0.0.0:25672->25672/tcp, 15691-15692/tcp   rabbitmq
b3cd366b83bd   prom/alertmanager:latest    "/bin/alertmanager -…"   15 hours ago   Up 15 hours   0.0.0.0:9093->9093/tcp                                                                                                                     alertmanager
e940eaf55d58   prom/prometheus:latest      "/bin/prometheus --c…"   15 hours ago   Up 15 hours   0.0.0.0:9092->9090/tcp                                                                                                                     prometheus
ddc259d72977   mysql:8.0.27                "docker-entrypoint.s…"   15 hours ago   Up 15 hours   33060/tcp, 0.0.0.0:13336->3306/tcp                                                                                                         mysql
88a93afad9b2   pypiserver/pypiserver       "/entrypoint.sh"         15 hours ago   Up 15 hours   0.0.0.0:18080->8080/tcp                                                                                                                    pypiserver
6fd519d9d168   nginx:1.22.0-alpine         "/docker-entrypoint.…"   15 hours ago   Up 15 hours   0.0.0.0:1510->80/tcp                                                                                                                       web_repo
[root@k8s-node-70 ~]#


```

3. 备份管控平台元数据


```sql

shell> ls -a |grep ".my.cnf"
.my.cnf

shell> cat .my.cnf
[client]
user=root
password=drACgwoqtM
port=13336
socket=/data/database/data/mysql.sock

create user admin@'%' identified with mysql_native_password by '123456';

grant all on *.* to  admin@'%' with grant option;


mysqldump  --single-transaction \
--routines \
--flush-logs \
--flush-privileges \
--skip-add-locks \
--set-gtid-purged=OFF \
--all-databases \
> db.sql

```


### 准备安装包

> 1. 修改安装介质属主和用户
>
> 2. 校验安装包md5值
>
> 3. 解压安装包

```sql
su - mysql

sudo chown -R mysql.mysql RDS-6.0.1-rc3-3eff6bde-x64-arm64.tar.gz

md5sum RDS-6.0.1-rc3-3eff6bde-x64-arm64.tar.gz

tar -xzvf RDS-6.0.1-rc3-3eff6bde-x64-arm64.tar.gz
```


### 复制inventory.yml和deploy.yml

> 确保deploy.yml文件中keepalive注释掉
```sql
cd /home/mysql/RDS-6.0.1-rc3-3eff6bde-x64-arm64/rds-ansible

mv inventory.yml inventory.yml.bak

mv deploy.yml deploy.yml.bak

cp /home/mysql/RDS-5.1.9.0rc16-release-10-17-x64_redhat8/rds-ansible/inventory.yml ./

cp /home/mysql/RDS-5.1.9.0rc16-release-10-17-x64_redhat8/rds-ansible/deploy.yml ./

diff deploy.yml deploy.yml.bak
```



### 升级
> 1. 切换到 rds-ansible 路径
>
> 2. 升级 rds
>
> 3. 升级 repo
>
> 4. 升级 pypiserver
>
> 5. 配置龙舟告警信息：将备份文件中的龙舟SMC接口信息粘贴到 gratrds.conf 中
>
> 6. 重启容器 greatrds-api
>
> 7. 检查 greatrds 版本号
>

```sql
cd /home/mysql/RDS-6.0.1-rc3-3eff6bde-x64-arm64/rds-ansible

ansible-playbook upgrade.yml -v

ansible-playbook deploy.yml -t repo

ansible-playbook deploy.yml -t pypiserver

cat /data/greatrds/etc/gratrds.conf
[DEFAULT]
longzhou_http = true
longzhou_url = http://xxx/alert/create
longzhou_ak =  yyy
longzhou_sk =  zzz
longzhou_api_key =  www
longzhou_app_key =  kkk


sudo docker ps -a
```

#### （1）执行结果

```sql
shell> ansible-playbook upgrade.yml -v

PLAY RECAP ******************************
controller-1               : ok=111  changed=35   unreachable=0    failed=0    skipped=4    rescued=0    ignored=0
controller-2               : ok=84   changed=23   unreachable=0    failed=0    skipped=8    rescued=0    ignored=0


```

#### （2）执行结果

```sql
shell> ansible-playbook deploy.yml -t repo

PLAY RECAP *****************************************
controller-1               : ok=10   changed=4    unreachable=0    failed=0    skipped=3    rescued=0    ignored=0
controller-2               : ok=1    changed=0    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0
```

#### （3）执行结果

```sql
shell> ansible-playbook deploy.yml -t pypiserver

PLAY [Checking the Installation Result And Cleaning the battlefield] **********************************

PLAY RECAP **********************
controller-1               : ok=6    changed=3    unreachable=0    failed=0    skipped=3    rescued=0    ignored=0
controller-2               : ok=1    changed=0    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0

```

#### （4）执行结果
> 没有添加龙舟告警的配置文件内容如下：
```sql
[root@k8s-node-70 etc]# cat greatrds.conf
[DEFAULT]
debug = false
# log_dir = /var/log/greatrds
host = 0.0.0.0
transport_url = rabbit://admin:123456@172.17.134.70:5672,admin:123456@172.17.134.71:5672//
file_server = 172.17.134.70
syslog_flag = False
syslog_addr = 172.16.70.61
rootwrap_config = /data/greatrds/etc/api/rootwrap.conf
dbinit_vg_name = rds_container_vg
enable_disk_limit = true
das_database_async = false
web_repo_dir = /data/web_repo
vip_address = 172.17.134.70
longzhou_http = False
longzhou_url = http://
speed_limit = True

[api]
host = 0.0.0.0
port = 9010

auth_url = https://172.17.134.70:17712/v1/auth/validation
log_auth_url = https://172.17.134.70:17712/v1/auth/logs
project_quota_url = https://172.17.134.70:17712/v1/auth/projects/quota
users_auth_url = https://172.17.134.70:17712/v1/auth/users


[database]
connection = mysql+pymysql://root:123456@172.17.134.70:13336/greatrds

[conductor]
host = 172.17.134.70
workers = 1
repo_addr= http://172.17.134.70:1510
pypi_server = 172.17.134.70
pypi_port = 18080
container_registry_addr = 172.17.134.70
container_registry_port = 5000
container_registry_domain = docker.greatrds.com
repo_conductor = 172.17.134.70
repo_mount_path = /opt/static

[monitor]
host = 172.17.134.70
workers = 1
prometheus_url = http://172.17.134.70:9092
alertmanager_url = http://172.17.134.70:9093
rule_path = /etc/greatrds/monitor/rules/
scrap_path = /etc/greatrds/monitor/scraps/
alertmanager_conf_file = /etc/greatrds/etc/monitor/alertmanager.yml


[oslo_messaging_amqp]
trace = true

[cache]
enabled = false
backend = dogpile.cache.memory

[feature-name]
caching = True
cache_time = 7200


```


### 验证

> 1. 验证版本信息

```sql
shell> docker ps|grep greatrds
4d85c389e502   greatrds_auth:6.0.1.0rc1    "/bin/sh -c 'gunicor…"   27 minutes ago   Up 26 minutes   0.0.0.0:9018->9018/tcp                                                                                                                     greatrds-auth
df45f25d00db   greatrds:6.0.1.0rc2.dev2    "./docker-entrypoint…"   28 minutes ago   Up 28 minutes                                                                                                                                              greatrds-monitor
fd3793de23ec   greatrds:6.0.1.0rc2.dev2    "./docker-entrypoint…"   28 minutes ago   Up 28 minutes                                                                                                                                              greatrds-conductor
a48a6a29f59a   greatrds:6.0.1.0rc2.dev2    "./docker-entrypoint…"   29 minutes ago   Up 29 minutes   0.0.0.0:9010->9010/tcp                                                                                                                     greatrds-api



```

> 2. 验证龙舟告警配置

验证是否能正常接收到报警信息

> 3. 验证新功能特性




## 版本回退


```sql
[root@k8s-node-71 database]# cd data/

[root@k8s-node-71 data]# ll
total 48
drwxr-xr-x  5 mysql mysql 4096 Oct 16 09:12 adm-dbinit
drwxrwx---  2 mysql mysql 4096 Oct 23 22:45 app
drwxr-xr-x  4 mysql mysql 4096 Oct 23 23:02 database
drwx--x--x 14 root  root  4096 Oct 25 10:49 docker
drwxr-xr-x  3 mysql mysql 4096 Oct 23 23:08 exporter
drwxr-xr-x  6 mysql mysql 4096 Oct 23 23:07 greatrds
drwxr-xr-x  4 mysql mysql 4096 Oct 23 23:08 greatrds_auth
drwxr-xr-x  4 mysql mysql 4096 Oct 23 23:04 nginx
drwxrwxrwx  5 mysql mysql 4096 Oct 23 23:03 rabbitmq
drwxr-xr-x  2 root  root  4096 Oct 23 23:07 static
drwxr-xr-x  6 mysql mysql 4096 Oct 23 23:04 tmp
drwxr-xr-x  3 root  root  4096 Oct 23 23:06 web_repo

[root@k8s-node-71 data]# rm -rf database

```






