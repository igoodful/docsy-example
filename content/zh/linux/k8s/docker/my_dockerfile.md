---
title: Dockerfile
date: 2023-10-12
weight: 20000
description: 删除容器


---

{{< alert >}}



{{< /alert >}}


## build

```bash
docker build -t your-image-name .
```


```dockerfile
FROM centos
LABEL org.opencontainers.image.authors="igoodful"
ENV BASEDIR /home/work
ENV MYSQL_VERSION 8.0.26
VOLUME ["/home/work","/home/work"]
USER root
WORKDIR $BASEDIR
RUN yum clean all \
&& yum clean all \
&& yum -y install wget \
&& yum -y install vim \
&& yum -y install systemd \
&& yum -y install net-tools \
&& yum -y install passwd
&& yum -y install liaio
&& yum -y install git
&& yum -y install cmake3
&& yum -y install ncurses-devel
&& yum -y install perl-Data-Dumper
&& yum -y install openssl-devel
&& yum -y install
&& yum -y install



RUN useradd work

FROM python:3.10

```


```bash
docker build -t centos:1.0.0 .

cmake3 ../ -DCMAKE_INSTALL_PREFIX=/usr/local/mysql -DMYSQL_DATADIR=/data/mysql -DSYSCONFDIR=/etc -DMYSQL_USER=mysql -DWITH_MYISAM_STORAGE_ENGINE=1 -DWITH_INNOBASE_STORAGE_ENGINE=1 -DMYSQL_UNIX_ADDR=/tmp/mysql.sock -DMYSQL_TCP_PORT=3306 -DEXTRA_CHARSETS=all -DDEFAULT_CHARSET=utf8mb4 -DDEFAULT_COLLATION=utf8mb4_general_ci -DWITH_DEBUG=0 -DMYSQL_MAINTAINER_MODE=0 -DWITH_SYSTEMD=1 -DDOWNLOAD_BOOST=1 -DWITH_BOOST=../boost

Tips：编译安装会比较耗时，大概1-2个小时
cmake3命令所使用的参数说明：

-DCMAKE_INSTALL_PREFIX=/usr/local/mysql：MySQL安装的根目录
-DMYSQL_DATADIR=/data/mysql：数据文件所存放的目录
-DSYSCONFDIR=/etc ：MySQL配置文件所在目录
-DMYSQL_USER=mysql：MySQL服务的用户名
-DWITH_MYISAM_STORAGE_ENGINE=1：安装MyISAM引擎
-DWITH_INNOBASE_STORAGE_ENGINE=1：安装InnoDB引擎
-DWITH_ARCHIVE_STORAGE_ENGINE=1：安装Archive引擎
-DWITH_MEMORY_STORAGE_ENGINE=1：安装Memory引擎
-DWITH_FEDERATED_STORAGE_ENGINE=1：安装Federated引擎
-DWITH_PARTITION_STORAGE_ENGINE=1：安装Partition引擎
-DWITH_READLINE=1：MySQL的readline library
-DMYSQL_UNIX_ADDR=/tmp/mysql.sock：sock文件的路径
-DMYSQL_TCP_PORT=3306 ：MySQL的监听端口
-DENABLED_LOCAL_INFILE=1：启用加载本地数据
-DENABLE_DOWNLOADS=1：编译时允许自主下载相关文件
-DEXTRA_CHARSETS=all ：使MySQL支持所有的扩展字符
-DDEFAULT_CHARSET=utf8mb4：设置默认字符集为utf8mb4
-DDEFAULT_COLLATION=utf8mb4_general_ci：设置默认字符校对
-DWITH_DEBUG=0：禁用调试模式
-DMYSQL_MAINTAINER_MODE=0：是否启用mysql维护器特定的开发环境
-DDOWNLOAD_BOOST=1：允许在线更新boost库
-DWITH_BOOST=../boost：指定boost安装路径

cmake .  -DBUILD_CONFIG=mysql_release \
-DCMAKE_BUILD_TYPE=RelWithDebInfo\
-DENABLED_LOCAL_INFILE=ON\
-DFORCE_INSOURCE_BUILD=1\
-DDEFAULT_CHARSET=utf8mb4\
-DDEFAULT_COLLATION=utf8mb4_0900_ai_ci\
-DWITH_BOOST=./\
-DDOWNLOAD_BOOST=1\
-DMYSQL_TCP_PORT=3310\
-DCMAKE_INSTALL_PREFIX=/home/work/mysql_3310\
-DMYSQL_DATADIR=/home/work/mysql_3310/data\
-DSYSCONFDIR=/home/work/mysql_3310/conf\
-DWITH_INNODB_MEMCACHED=ON\
-DWITH_INNOBASE_STORAGE_ENGINE=1\
-DWITH_FEDERATED_STORAGE_ENGINE=1\
-DWITH_BLACKHOLE_STORAGE_ENGINE=1\
-DWITH_ARCHIVE_STORAGE_ENGINE=1\
-DWITHOUT_EXAMPLE_STORAGE_ENGINE=1\
-DWITH_PERFSCHEMA_STORAGE_ENGINE=1  > glc.log

mysqld --initialize --user=mysql --basedir=/usr/local/mysql/ --datadir=/data/mysql

将MySQL生成的启动文件复制到/usr/lib/systemd/system/目录下：

cp /usr/local/src/mysql-8.0.18/builder/scripts/mysqld.service /usr/lib/systemd/system/

chown 775 /usr/lib/systemd/system/mysqld.service
```





### /etc/yum.repos.d/CentOS-Base.repo
```sql
# CentOS-Base.repo
#
# The mirror system uses the connecting IP address of the client and the
# update status of each mirror to pick mirrors that are updated to and
# geographically close to the client.  You should use this for CentOS updates
# unless you are manually picking other mirrors.
#
# If the mirrorlist= does not work for you, as a fall back you can try the
# remarked out baseurl= line instead.
#
#

[base]
name=CentOS-$releasever-stream - Base - mirrors.aliyun.com
#failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos/$releasever-stream/BaseOS/$basearch/os/
        http://mirrors.aliyuncs.com/centos/$releasever-stream/BaseOS/$basearch/os/
        http://mirrors.aliyun.com/centos/$releasever-stream/BaseOS/$basearch/os/
gpgcheck=1
gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-Official

#additional packages that may be useful
[extras]
name=CentOS-$releasever-stream - Extras - mirrors.aliyun.com
#failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos/$releasever-stream/extras/$basearch/os/
        http://mirrors.aliyuncs.com/centos/$releasever-stream/extras/$basearch/os/
        http://mirrors.aliyun.com/centos/$releasever-stream/extras/$basearch/os/
gpgcheck=1
gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-Official

#additional packages that extend functionality of existing packages
[centosplus]
name=CentOS-$releasever-stream - Plus - mirrors.aliyun.com
#failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos/$releasever-stream/centosplus/$basearch/os/
        http://mirrors.aliyuncs.com/centos/$releasever-stream/centosplus/$basearch/os/
        http://mirrors.aliyun.com/centos/$releasever-stream/centosplus/$basearch/os/
gpgcheck=1
enabled=0
gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-Official

[PowerTools]
name=CentOS-$releasever-stream - PowerTools - mirrors.aliyun.com
#failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos/$releasever-stream/PowerTools/$basearch/os/
        http://mirrors.aliyuncs.com/centos/$releasever-stream/PowerTools/$basearch/os/
        http://mirrors.aliyun.com/centos/$releasever-stream/PowerTools/$basearch/os/
gpgcheck=1
enabled=0
gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-Official


[AppStream]
name=CentOS-$releasever-stream - AppStream - mirrors.aliyun.com
#failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos/$releasever-stream/AppStream/$basearch/os/
        http://mirrors.aliyuncs.com/centos/$releasever-stream/AppStream/$basearch/os/
        http://mirrors.aliyun.com/centos/$releasever-stream/AppStream/$basearch/os/
gpgcheck=1
gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-Official


```




















