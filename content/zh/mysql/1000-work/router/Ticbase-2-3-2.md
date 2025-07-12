---
title: 升级 Ticbase 2.3.2
description: MySQL-8.0.32 router-6056
date: 2023-10-24
weight: 70000


---

{{< alert >}}

### x86
| 名称                                                                 | md5sum                           |
|:---------------------------------------------------------------------|:---------------------------------|
| mysql-8.0.32-linux-glibc2.17-x86_64.tar.gz                           | 14cbd30cc261c40ec6d414f7515bb37c |
| GreatDBRouter-6.0.0.6056-RC-4-924f4d6b-Linux-glibc2.17-x86_64.tar.gz | 5356a82aec76619234e5bc3bfd30cfa3 |
| GreatDBRouter-6.0.0.6056-RC-4-924f4d6b-Linux-glibc2.28-x86_64.tar.gz | a0c995e5534d689b1529f019413ed870 |
| percona-xtrabackup.tar.gz                                            | 0fd3478928cfe0f17e63d910c2c08f59 |
| backup-recovery-tool.tar.gz                                          | 0dfba546bde381a0376371725699af12 |

### arm
| 名称                                                                  | md5sum                            |
|:----------------------------------------------------------------------|:----------------------------------|
| mysql-8.0.32-linux-glibc2.17-aarch64.tar.gz                           | m5538ac53ee979667dbf636d53023cc38 |
| GreatDBRouter-6.0.0.6056-RC-4-924f4d6b-Linux-glibc2.28-aarch64.tar.gz | ef46b213dc6eaf45f819f68a94441d75  |
| percona-xtrabackup.tar.gz                                             | 88c31856422ef28c10cf1dec6d78202a  |
| backup-recovery-tool.tar.gz                                           | 1058361d085043d3f6a1a80d738f660c  |




{{< /alert >}}

 ## 确定环境信息

```sql
[root@node1 ~]# ldd --version
ldd (GNU libc) 2.17
---

[xxx@48 cluster4]$ ldd --version
ldd (GNU libc) 2.18
---

[root@node1 ~]# lscpu
Architecture:          x86_64
---
```
## 复制软件包到指定位置
{{<note>}}
<!---->
- cluster4/   目录下面存放：backup-recovery-tool.tar.gz 和 GreatDBRouter
- mysql8.0/   目录下面存放：percona-xtrabackup.tar.gz 和 mysql-8.0.32

{{</note>}}
```sql
/data/web_repo/data/repo/x64/static/stable/cluster4

/data/web_repo/data/repo/x64/static/stable/mysql8.0/
```


```sql
/data/web_repo/data/repo/arm64/static/stable/cluster4/

/data/web_repo/data/repo/arm64/static/stable/mysql8.0/

```


## 一、检查数据节点
> INSTANT列、存储过程、函数

```sql
select * from information_schema.innodb_tables where instant_cols > 0;
```
{{<note>}}

若存在一个表之前添加过INSTANT ADD列，则在升级到高于8.0.29的版本后，对这些表执行DML语句可能会导致服务器意外关闭，该问题在8.2中修复。
{{</note>}}

```sql
select routine_schema, routine_name from information_schema.routines where routine_schema  not in ('sys') and routine_type='procedure';
```


```sql
select routine_schema, routine_name from information_schema.routines where routine_schema  not in ('sys') and routine_type='function';
```

{{<note>}}

在Item_ref::check_cols函数执行后可能会导致数据库crash，该问题在8.0.35中修复。
{{</note>}}



## 二、备份数据
> 拷贝数据文件或使用xtrabackup备份工具对数据库进行备份

```sql
cp -R /data/mysqldata /data/mysqldata.old
```

> 登录RDS选择该集群的备份任务并立即执行


## 三、上传安装包：
> backup-recovery-tool、percona-xtrabackup、mysql-8.0.32、GreatDBRouter-6.0.0.6056-RC-4
> 执行如下命令，确认当前环境的glibc版本、CPU架构并选择合适的安装介质
```sql
ldd --version|grep libc  # 确认libc版本

lscpu|grep -i architecture # 确认cpu架构
```

安装包的md5列表


## 四、创建集群模板和参数模板
> 基础模板：点击下拉框根据架构选择JKMySQL for MySQL 8.0+版本；
> 端口范围取值时不取结束端口,例如设置起始端口16315结束端口16316,端口取值范围只有16315一个值



> 如果之前没有手动修改过集群参数，则ticbase2.3.2参数模板相对于ticbase2.3.0的新增如下变动：

1. router参数变动(升级若不开启透传，则无需更改)：开启透传模式transparent-mode=1；
2. 数据节点参数变动：限制事务大小max_binlog_cache_size=1073741824；

```sql
dbscale change startup config 'transparent-mode'='1';

dbscale backend server execute "set persist max_binlog_cache_size=1073741824;";
```


## 五、升级数据节点

> 登录RDS的WEB页面 => 数据库服务 => 集群管理 => 选择集群 => 基本信息 => 版本管理 => 小版本升级。

1. 更改服务：mysql_16315.service

```sql
ln -s /data/app/mysql-8.0.32* /data/app/mysql

sudo sed -i.bak 's|/data/app/mysql-8.0.26|/data/app/mysql|' /usr/lib/systemd/system/mysql_16315.service
```

2. 更改客户端：bin
```sql
sudo mv /usr/bin/mysql /usr/bin/mysql_8026

echo 'export PATH=$PATH:/data/app/mysql/bin' >> ~/.bashrc
```



## 六、升级router节点
> 登录RDS的WEB页面 => 数据库服务 => 集群管理 => 选择集群 => 基本信息 => 版本管理 => 小版本升级



## 七、升级备份服务器的备份恢复工具：
> backup-recovery-tool

```sql
wget -N -nv http://管控IP:1510/gtms/repo/x64/static/stable/cluster4/backup-recovery-tool.tar.gz -P /tmp

wget -N -nv http://管控IP:1510/gtms/repo/arm64/static/stable/cluster4/backup-	recovery-tool.tar.gz -P /tmp

cd /backup/storage_path/backup_file/bin/

mv backup{，-5820} recovery{，-5820}

tar -zxvf /tmp/backup-recovery-tool.tar.gz

cp /tmp/backup-recovery-tool/binary_version/{backup,recovery}  /backup/storage	_path/backup_file/bin/

/backup/storage_path/backup_file/bin/backup --version

/backup/storage_path/backup_file/bin/recovery --version

```

## 八、升级数据节点的备份工具
> xtrabackup
> 为了适配mysql8.0.32的备份，需要升级到xtrabackup8.0.32，升级方式有如下两种方式

```sql
wget -N -nv http://管控:1510/gtms/repo/x64/static/stable/mysql8.0/percona-xtrabackup.tar.gz -P /tmp

wget -N -nv http://192.168.1.111:1510/gtms/repo/arm64/static/stable/mysql8.0/percona-xtrabackup.tar.gz -P /tmp

sudo mv /usr/bin/xtrabackup /usr/bin/xtrabackup8026

tar -zxvf percona-xtrabackup.tar.gz

cp /tmp/percona-xtrabackup-8.0.32-26-Linux-x86_64.glibc2.17-minimal/bin/xtrabac	kup /usr/bin/

/usr/bin/xtrabackup --version

```
> xtrabackup8.0.32-26和ARM kylin-v10操作系统有系统库的兼容问题，需要升级/usr/lib64/libstdc++.so.6文件。

```sql
mv /usr/lib64/libstdc++.so.6{.,bak}

cp /tmp/percona-xtrabackup/lib/private/libstdc++.so.6.0.28 /usr/lib64/

ln -f /usr/lib64/libstdc++.so.6.0.28 /usr/lib64/libstdc++.so.6

cp /tmp/percona-xtrabackup/lib/private/libstdc++.so.6.0.28 /data/dbinit/venv/bin/greatrds-dbinit-bin-dir

cd /data/dbinit/venv/bin/greatrds-dbinit-bin-dir

ln -f libstdc++.so.6.0.28 libstdc++.so.6


```

## 九、修改RDS conductor容器代码
> RDS为了适配6038backup_tool需要修改代码中的默认配置文件，增加参数store-bad-backup=0

```sql
docker exec -it greatrds-conductor bash

cd /usr/local/lib/python3.6/site-packages/greatrds/conductor/resources/baremetal/backupserver

sed -i '/backup_config_context =/a\store-bad-backup=0' __init__.py

docker restart greatrds-conductor
```






