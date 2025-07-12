---
title: 版本信息
description: 版本信息
date: 2023-01-05
weight: 70000


---

{{< alert >}}

## 查看rds版本
- `RDS-5.1.9.0rc16-release-10-17-x64-arm64.tar.gz`
```bash
[root@node76 mysql]# docker ps
CONTAINER ID   IMAGE                       COMMAND                  CREATED             STATUS             PORTS                                                                                                                                      NAMES
5579ffca81b2   greatrds_auth:5.1.8.0rc14   "/bin/sh -c 'gunicor…"   58 minutes ago      Up 58 minutes      0.0.0.0:9018->9018/tcp                                                                                                                     greatrds-auth
914990d9fbb6   greatrds:5.1.9.0rc16        "./docker-entrypoint…"   59 minutes ago      Up 59 minutes                                                                                                                                                 greatrds-monitor
2a2cccfc80d0   greatrds:5.1.9.0rc16        "./docker-entrypoint…"   About an hour ago   Up About an hour                                                                                                                                              greatrds-conductor
a1bc69bf4832   greatrds:5.1.9.0rc16        "./docker-entrypoint…"   About an hour ago   Up About an hour   0.0.0.0:9010->9010/tcp                                                                                                                     greatrds-api
c675b70ece76   nginx:1.22.0-alpine         "/docker-entrypoint.…"   About an hour ago   Up About an hour                                                                                                                                              nginx
309da7271ba3   rabbitmq:3.9.7-management   "docker-entrypoint.s…"   About an hour ago   Up About an hour   0.0.0.0:4369->4369/tcp, 5671/tcp, 0.0.0.0:5672->5672/tcp, 15671/tcp, 0.0.0.0:15672->15672/tcp, 0.0.0.0:25672->25672/tcp, 15691-15692/tcp   rabbitmq
1c72ead95992   prom/alertmanager:latest    "/bin/alertmanager -…"   About an hour ago   Up About an hour   0.0.0.0:9093->9093/tcp                                                                                                                     alertmanager
18df9fd0564d   prom/prometheus:latest      "/bin/prometheus --c…"   About an hour ago   Up About an hour   0.0.0.0:9092->9090/tcp                                                                                                                     prometheus
8fa962d50405   mysql:8.0.27                "docker-entrypoint.s…"   About an hour ago   Up About an hour   33060/tcp, 0.0.0.0:13336->3306/tcp                                                                                                         mysql
80a3edfafa3e   pypiserver/pypiserver       "/entrypoint.sh"         About an hour ago   Up About an hour   0.0.0.0:18080->8080/tcp                                                                                                                    pypiserver
fd1629aa7bf2   nginx:1.22.0-alpine         "/docker-entrypoint.…"   About an hour ago   Up About an hour   0.0.0.0:1510->80/tcp
```
- `RDS-6.0.1-rc3-3eff6bde-x64-arm64.tar.gz`
```bash
[root@gip software]# docker ps
CONTAINER ID   IMAGE                       COMMAND                  CREATED        STATUS                 PORTS                                                                                                                                      NAMES
77e3fe75c8f2   nginx:1.22.0-alpine         "/docker-entrypoint.…"   7 weeks ago    Up 12 days                                                                                                                                                        nginx
370278fb97e7   greatrds_auth:6.0.1.0rc1    "/bin/sh -c 'gunicor…"   7 weeks ago    Up 12 days             0.0.0.0:9018->9018/tcp                                                                                                                     greatrds-auth
f49112162ce7   greatrds:6.0.1.0rc2.dev2    "./docker-entrypoint…"   7 weeks ago    Up 12 days                                                                                                                                                        greatrds-monitor
8a352ccf6908   greatrds:6.0.1.0rc2.dev2    "./docker-entrypoint…"   7 weeks ago    Up 12 days                                                                                                                                                        greatrds-conductor
510ccb40d889   greatrds:6.0.1.0rc2.dev2    "./docker-entrypoint…"   7 weeks ago    Up 12 days             0.0.0.0:9010->9010/tcp                                                                                                                     greatrds-api
2deb470c1415   rabbitmq:3.9.7-management   "docker-entrypoint.s…"   3 months ago   Up 12 days             0.0.0.0:4369->4369/tcp, 5671/tcp, 0.0.0.0:5672->5672/tcp, 15671/tcp, 0.0.0.0:15672->15672/tcp, 0.0.0.0:25672->25672/tcp, 15691-15692/tcp   rabbitmq
90975cc6ebac   prom/alertmanager:latest    "/bin/alertmanager -…"   3 months ago   Up 12 days             0.0.0.0:9093->9093/tcp                                                                                                                     alertmanager
296ff03669c1   prom/prometheus:latest      "/bin/prometheus --c…"   3 months ago   Up 12 days             0.0.0.0:9092->9090/tcp                                                                                                                     prometheus
d09c75096b49   mysql:8.0.27                "/entrypoint.sh --se…"   3 months ago   Up 12 days (healthy)   33060-33061/tcp, 0.0.0.0:13336->3306/tcp                                                                                                   mysql
168f00edaf71   pypiserver/pypiserver       "/entrypoint.sh"         3 months ago   Up 12 days             0.0.0.0:18080->8080/tcp                                                                                                                    pypiserver
b7f366b9058c   nginx:1.22.0-alpine         "/docker-entrypoint.…"   3 months ago   Up 12 days             0.0.0.0:1510->80/tcp                                                                                                                       web_repo

```






### 2.1.0.0
2022/4/7

中国建设银行建信金科SQLScale数据库-参数模板_2022-04-07-ARM最新.csv
### 2.2.0.0
2022/9/6

中国建设银行建信金科SQLScale数据库-参数变更说明-2022-09-06.xlsx
### 2.3.0.0
2023/4/6

- 关闭自适应哈希索引
### 2.3.0.0
2023/4/11

- 在0406版本基础上新增了两种规格，2c8g和4c16g。
### 2.3.0.0
2023/6/1

参数模板修改以下内容：

- restrict_create_table参数的默认值：由1改为0；
- restrict-create-table参数的是否需要重启项：由TRUE改为FALSE

### 2.3.0.0
2023/9/4

修改以下内容：

- 删除：session-init-charset=utf8mb4
- session-init-charset默认值为空"

### 2.3.2.0
2023/11/13

修改以下内容：新增
- max_binlog_cache_size参数，默认值为1073741824;
- transparent-mode参数，默认值为1"

{{< /alert >}}


### RDS-6.0.1-rc3-3eff6bde
{{<note>}}

- 新功能
  1. 告警到SMC平台信息IP更改为主机信息

- 改进性修复
  1. 修复双monitor抢主导致监控展示的数据不全。
  2. 修复一个集群只能生效一个模板问题。
  3. 修复告警规则更新接口更新异常问题
  4. 修复告警模板执行流程延迟过大问题
{{</note>}}



### RDS-6.0.1-rc1-6e8beadb
{{<note>}}
- 包更新
  1. backup备份工具版本：version: 1.0.6039.4a538e36
  2. xtrabackup备份工具版本：8.0.32-26

- 新功能
  1. 告警规则列表增加搜索框
  2. 增加告警消息聚合，合并发送一条告警
  3. 告警模板一键适配修改应用

- 改进性修复
  1. 修复拓扑因接口返回过慢超时的问题
  2. 修复定时器任务名无法修改的问题
  3. 修复admin用户因页面操作被强制设置为密码会过期
  4. RDS适配使用libjemalloc.so内存分配方式
  5. RDS支持server-id的支持随机生产，避免主集群和灾备集群时，出现server-id冲突的情况
  6. 修复备份服务器创建第十个集群备份计划报错
  7. 修复备份策略可编辑执行时间，指定和修改从库节点

X86平台的备份正常。arm平台的备份时有问题：

- 确定所有数据节点均有xtrabackup 8.0.32-26版本的备份工具：/usr/bin/xtrabackup --version
如果没有，去RDS管控平台服务器的/data/web_repo/data/repo/arm64/static/stable/mysql8.0/的目录下解压percona-xtrabackup.tar.gz文件，把/data/web_repo/data/repo/arm64/static/stable/mysql8.0/percona-xtrabackup/bin目录下的xtrabackup文件拷贝到各个数据节点的/usr/bin/目录下
- Xtrabackup8.0.32-26和kylin-v10操作系统有系统库的兼容问题，需要升级/lib64/rm libstdc++.so.6文件：去RDS管控平台服务器的
/data/web_repo/data/repo/arm64/static/stable/mysql8.0/percona-xtrabackup/lib/private目录下的libstdc++.so.6.0.28文件拷贝到各个数据节点的/usr/lib64/目录下。

```bash
cd /usr/lib64/

rm libstdc++.so.6

ln -s libstdc++.so.6.0.28 libstdc++.so.6
```
- 更新各数据节点/data/dbinit/venv/bin/greatrds-dbinit-bin-dir/libstdc++.so.6的文件：
```sql
cp /usr/lib64/libstdc++.so.6.0.28 /data/dbinit/venv/bin/greatrds-dbinit-bin-dir/libstdc++.so.6
```

{{</note>}}




### RDS-5.1.9.0rc18
{{<note>}}

- 新功能
  1. 软件库中新增GreatDBRouter-3.1.5819.8ac266ea6-rc-WithSpark-WithSSL-ky10.x86_64.tar.gz包
  2. 支持数据库连续全量备份。

- 改进性修复
  1. 修复ARM平台备份工具。
  2. RDS兼容X86平台的kylin-v10系统
  3. 修复DAS库显示不全问题。
  4. 修复当单节点的备份文件存储在RDS上在进行恢复操作时，需要从RDS把备份文件传到本地时传输超过10分钟超时问题。
{{</note>}}













