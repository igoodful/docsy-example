---
title: sysbench mysql
description: sysbench mysql
date: 2023-10-26
weight: 20000
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}



{{< /alert >}}


## /root/dbscale/Dockerfile/builder/centos7/Centos-7.repo

```ini
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
name=CentOS-$releasever - Base - mirrors.aliyun.com
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos/$releasever/os/$basearch/
        http://mirrors.aliyuncs.com/centos/$releasever/os/$basearch/
        http://mirrors.cloud.aliyuncs.com/centos/$releasever/os/$basearch/
gpgcheck=1
gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-7

#released updates
[updates]
name=CentOS-$releasever - Updates - mirrors.aliyun.com
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos/$releasever/updates/$basearch/
        http://mirrors.aliyuncs.com/centos/$releasever/updates/$basearch/
        http://mirrors.cloud.aliyuncs.com/centos/$releasever/updates/$basearch/
gpgcheck=1
gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-7

#additional packages that may be useful
[extras]
name=CentOS-$releasever - Extras - mirrors.aliyun.com
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos/$releasever/extras/$basearch/
        http://mirrors.aliyuncs.com/centos/$releasever/extras/$basearch/
        http://mirrors.cloud.aliyuncs.com/centos/$releasever/extras/$basearch/
gpgcheck=1
gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-7

#additional packages that extend functionality of existing packages
[centosplus]
name=CentOS-$releasever - Plus - mirrors.aliyun.com
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos/$releasever/centosplus/$basearch/
        http://mirrors.aliyuncs.com/centos/$releasever/centosplus/$basearch/
        http://mirrors.cloud.aliyuncs.com/centos/$releasever/centosplus/$basearch/
gpgcheck=1
enabled=0
gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-7

#contrib - packages by Centos Users
[contrib]
name=CentOS-$releasever - Contrib - mirrors.aliyun.com
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos/$releasever/contrib/$basearch/
        http://mirrors.aliyuncs.com/centos/$releasever/contrib/$basearch/
        http://mirrors.cloud.aliyuncs.com/centos/$releasever/contrib/$basearch/
gpgcheck=1
enabled=0
gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-7

```




## /root/dbscale/Dockerfile/builder/centos7/Dockerfile

```dockerfile
# Docker enviroment for DBScale dtr test

FROM centos:centos7.9.2009

MAINTAINER shuai.lou <shuai.lou@greatopensource.com>

# Set aliyun repo for speed
RUN rm /etc/yum.repos.d/CentOS-Base.repo
RUN rm /etc/yum.repos.d/*
COPY Centos-7.repo /etc/yum.repos.d/CentOS-Base.repo
RUN yum makecache && yum install -y  epel-release

# Install sshd
RUN yum -y install openssh-clients openssh-server \
 && mkdir /var/run/sshd \
 && echo "root:abc123" | chpasswd \
 && sed -i 's/#PermitRootLogin yes/PermitRootLogin yes/' /etc/ssh/sshd_config \
 && ssh-keygen -A

# Install Requirements
RUN yum install -y \
 cmake gcc gcc-c++ git make openssl-devel unixODBC-devel wget deltarpm flex \
 rpm-build vim boost-devel gmp-devel libevent-devel libjemalloc-devel autoconf \
 automake libtool ant maven clang

# Install bison
COPY bison-2.7-4.el7.x86_64.rpm /tmp/
COPY ace-6.3.3-55.1.x86_64.rpm /tmp/
COPY mpc-6.3.3-55.1.x86_64.rpm /tmp/
COPY ace-devel-6.3.3-55.1.x86_64.rpm /tmp/
COPY mysql-community-client-5.7.22-1.el7.x86_64.rpm /tmp/
COPY mysql-community-common-5.7.22-1.el7.x86_64.rpm /tmp/
COPY mysql-community-devel-5.7.22-1.el7.x86_64.rpm /tmp/
COPY mysql-community-libs-5.7.22-1.el7.x86_64.rpm /tmp/
COPY zeromq-4.0.5-1.el7.centos.x86_64.rpm /tmp/
COPY zeromq-devel-4.0.5-1.el7.centos.x86_64.rpm /tmp/

RUN yum install -y \
 /tmp/bison-2.7-4.el7.x86_64.rpm \
 /tmp/ace-6.3.3-55.1.x86_64.rpm \
 /tmp/mpc-6.3.3-55.1.x86_64.rpm \
 /tmp/ace-devel-6.3.3-55.1.x86_64.rpm \
 /tmp/mysql-community-client-5.7.22-1.el7.x86_64.rpm \
 /tmp/mysql-community-common-5.7.22-1.el7.x86_64.rpm \
 /tmp/mysql-community-devel-5.7.22-1.el7.x86_64.rpm \
 /tmp/mysql-community-libs-5.7.22-1.el7.x86_64.rpm \
 /tmp/zeromq-4.0.5-1.el7.centos.x86_64.rpm \
 /tmp/zeromq-devel-4.0.5-1.el7.centos.x86_64.rpm

# Install czmq
COPY czmq-3.0.2.tar.gz /tmp/
RUN tar -xzvf /tmp/czmq-3.0.2.tar.gz -C /tmp/ \
 && cd /tmp/czmq-3.0.2 && ./configure --quiet \
 && make -j && make install

#Install zookeeper 3.5
RUN cd /tmp && git clone http://gitlab.greatopensource.com/deps/zookeeper.git
RUN tar -xzvf /tmp/zookeeper/apache-zookeeper-3.5.8.tar.gz -C /tmp/ \
  && cd /tmp/apache-zookeeper-3.5.8 && ant compile_jute
RUN cd /tmp/apache-zookeeper-3.5.8/zookeeper-client/zookeeper-client-c && autoreconf -if \
  && ./configure --without-cppunit && make && make install
RUN echo "/usr/local/lib" >> /etc/ld.so.conf && ldconfig
RUN tar -xzvf /tmp/zookeeper/apache-zookeeper-3.5.8-bin.tar.gz -C / \
  && cd /apache-zookeeper-3.5.8-bin && cp conf/zoo_sample.cfg conf/zoo.cfg
ENV ZKDIR /apache-zookeeper-3.5.8-bin

# copy tcmalloc and mimalloc
COPY libmimalloc.so /usr/local/lib
COPY libtcmalloc.so /usr/local/lib

# Install spark-agent
COPY settings.xml /usr/share/maven/conf/
RUN git clone http://gitlab.greatopensource.com/yul/dbscale-spark-agent.git /spark-agent \
  && cd /spark-agent && cd dbscale_spark && mvn package && cp log4j.properties ./target/
RUN rm -rf ~/.m2

EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]


```

- `FROM centos:centos7.9.2009`
- `RUN rm /etc/yum.repos.d/*`
- `clang`


## docker build


```bash
cd /root/dbscale/Dockerfile/builder/centos7/
docker build -t dbscale20250605 .
docker tag dbscale20250605:latest dbscale:20250605

```

## docker run

```bash
docker run -it  -d --name dbscale20250605 -p 2244:22 --hostname centos7 --user root --workdir /root dbscale:20250605
docker exec -it containerd_id /bin/bash

```


## docker cp

```bash
docker cp dbscale containerd_id:/root/

```



```bash
cd /root/dbscale
mkdir build
cd build
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib/
touch /usr/bin/clang-format
cmake -DCMAKE_BUILD_TYPE=RelWithDebInfo -DDBSCALE_DISABLE_SPARK=YES -DDOWNLOAD_DEPS=YES -DDBSCALE_TEST_DISABLE=YES -DGCOV_DISABLE=YES -DSKIP_GTEST=ON -DHAVE_OPENSSL=YES -DDBSCALE_BUILD=YES ..
make -j4
git clone http://gitlab.greatopensource.com/dbscale/jemalloc-libs.git
cp jemalloc-libs/centos7/x86/libjemalloc.so /usr/local/lib/
cd ../extra && /bin/bash pack_dbscale.sh --build
cd ../
mkdir binaries
git_no=`git log --oneline | wc -l | tr -d '\n'`
git_rev=`git rev-parse --short=8 HEAD | tr -d '\n'`
DBSCALE_VERSION=`cat VERSION | tr -d '\n' | sed 's/ /-/g'`
mv $PRODUCT_DIR/*.tar.gz binaries/$DBSCALE_VERSION.$git_no-$git_rev-Linux-glibc2.17-x86_64.tar.gz
echo "build EverDB"
echo "EDB-Grid v1.3.2" > VERSION
cd build
cmake -DCMAKE_BUILD_TYPE=RelWithDebInfo -DDBSCALE_DISABLE_SPARK=YES -DDOWNLOAD_DEPS=YES -DDBSCALE_TEST_DISABLE=YES -DGCOV_DISABLE=YES -DSKIP_GTEST=ON -DHAVE_OPENSSL=YES -DDBSCALE_BUILD=YES ..
make -j4
cd ../extra && /bin/bash pack_dbscale.sh --build
cd ../
DBSCALE_VERSION=`cat VERSION | tr -d '\n' | sed 's/ /-/g'`
mv $PRODUCT_DIR/*.tar.gz binaries/$DBSCALE_VERSION.$git_no-$git_rev-Linux-glibc2.17-x86_64.tar.gz
cd binaries/ && md5sum * > md5sum.txt && cat md5sum.txt && cd -
ls -l binaries/

```
- 编译后的二进制文件为：`/root/dbscale/build/src/dbscale`


## 一次编译日志

- http://gitlab.greatopensource.com/dbscale/dbscale/-/jobs/457925

```bash
Running with gitlab-runner 13.1.0 (6214287e)
  on centos7-zk3.5.8 DymGwyRL
Preparing the "docker" executor
00:18
Using Docker executor with image dbscale/builder:centos7-zk3.5.8 ...
Using docker image sha256:0266b12eef6a0a7563a026170e21c596ec344311fb799624a7c9a00447944897 for dbscale/builder:centos7-zk3.5.8 ...
Preparing environment
00:09
Running on runner-dymgwyrl-project-1980-concurrent-0 via gitlab-ci-server2...
Getting source from Git repository
00:59
Fetching changes...
Initialized empty Git repository in /builds/dbscale/dbscale/.git/
Created fresh repository.
Checking out c3efe6ca as edb...
Updating/initializing submodules recursively...
Submodule 'repo' (http://gitlab.greatopensource.com/dbscale/repo.git) registered for path 'repo'
Cloning into '/builds/dbscale/dbscale/repo'...
Submodule path 'repo': checked out 'c2d6c1f4237c12fb436a8faebf438d718f38ee1a'
Entering 'repo'
Downloading artifacts
00:09
Downloading artifacts for dbscale_doc_build (457922)...
Downloading artifacts from coordinator... ok        id=457922 responseStatus=200 OK token=64_2SfN3
Executing "step_script" stage of the job script
08:08
$ export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib/
$ mkdir build
$ cd build
$ touch /usr/bin/clang-format
$ cmake -DCMAKE_BUILD_TYPE=RelWithDebInfo -DDBSCALE_DISABLE_SPARK=YES -DDOWNLOAD_DEPS=YES -DDBSCALE_TEST_DISABLE=YES -DGCOV_DISABLE=YES -DSKIP_GTEST=ON -DHAVE_OPENSSL=YES -DDBSCALE_BUILD=YES ..
-- The C compiler identification is GNU 4.8.5
-- The CXX compiler identification is GNU 4.8.5
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Build dbscale
-- Bison Version: bison (GNU Bison) 2.7
-- CMAKE_SYSTEM_INFO_FILE: Platform/Linux
-- CMAKE_SYSTEM_NAME:      Linux
-- CMAKE_SYSTEM_PROCESSOR: x86_64
-- CMAKE_SYSTEM:           Linux-3.10.0-1127.10.1.el7.x86_64
-- OS Version: centos7
-- OS Architecure:x86_64
-- [download 0% complete]
-- [download 1% complete]
-- [download 2% complete]
-- [download 3% complete]
-- [download 4% complete]
-- [download 5% complete]
-- [download 6% complete]
-- [download 7% complete]
-- [download 8% complete]
-- [download 9% complete]
-- [download 10% complete]
-- [download 11% complete]
-- [download 12% complete]
-- [download 13% complete]
-- [download 14% complete]
-- [download 15% complete]
-- [download 16% complete]
-- [download 17% complete]
-- [download 18% complete]
-- [download 19% complete]
-- [download 20% complete]
-- [download 21% complete]
-- [download 22% complete]
-- [download 23% complete]
-- [download 24% complete]
-- [download 25% complete]
-- [download 26% complete]
-- [download 27% complete]
-- [download 28% complete]
-- [download 29% complete]
-- [download 30% complete]
-- [download 31% complete]
-- [download 32% complete]
-- [download 33% complete]
-- [download 34% complete]
-- [download 35% complete]
-- [download 36% complete]
-- [download 37% complete]
-- [download 38% complete]
-- [download 39% complete]
-- [download 40% complete]
-- [download 41% complete]
-- [download 42% complete]
-- [download 43% complete]
-- [download 44% complete]
-- [download 45% complete]
-- [download 46% complete]
-- [download 47% complete]
-- [download 48% complete]
-- [download 49% complete]
-- [download 50% complete]
-- [download 51% complete]
-- [download 52% complete]
-- [download 53% complete]
-- [download 54% complete]
-- [download 55% complete]
-- [download 56% complete]
-- [download 57% complete]
-- [download 58% complete]
-- [download 59% complete]
-- [download 60% complete]
-- [download 61% complete]
-- [download 62% complete]
-- [download 63% complete]
-- [download 64% complete]
-- [download 65% complete]
-- [download 66% complete]
-- [download 67% complete]
-- [download 68% complete]
-- [download 69% complete]
-- [download 70% complete]
-- [download 71% complete]
-- [download 72% complete]
-- [download 73% complete]
-- [download 74% complete]
-- [download 75% complete]
-- [download 76% complete]
-- [download 77% complete]
-- [download 78% complete]
-- [download 79% complete]
-- [download 80% complete]
-- [download 81% complete]
-- [download 82% complete]
-- [download 83% complete]
-- [download 84% complete]
-- [download 85% complete]
-- [download 86% complete]
-- [download 87% complete]
-- [download 88% complete]
-- [download 89% complete]
-- [download 90% complete]
-- [download 91% complete]
-- [download 92% complete]
-- [download 93% complete]
-- [download 94% complete]
-- [download 95% complete]
-- [download 96% complete]
-- [download 97% complete]
-- [download 98% complete]
-- [download 99% complete]
-- [download 100% complete]
-- cd /builds/dbscale/dbscale/deps_build; tar xfz jemalloc-5.0.1-glibc.tar.gz
-- [download 1% complete]
-- [download 2% complete]
-- [download 3% complete]
-- [download 4% complete]
-- [download 5% complete]
-- [download 6% complete]
-- [download 7% complete]
-- [download 8% complete]
-- [download 9% complete]
-- [download 10% complete]
-- [download 11% complete]
-- [download 12% complete]
-- [download 13% complete]
-- [download 14% complete]
-- [download 15% complete]
-- [download 16% complete]
-- [download 17% complete]
-- [download 18% complete]
-- [download 19% complete]
-- [download 20% complete]
-- [download 21% complete]
-- [download 22% complete]
-- [download 23% complete]
-- [download 24% complete]
-- [download 25% complete]
-- [download 26% complete]
-- [download 27% complete]
-- [download 28% complete]
-- [download 29% complete]
-- [download 30% complete]
-- [download 31% complete]
-- [download 32% complete]
-- [download 33% complete]
-- [download 34% complete]
-- [download 35% complete]
-- [download 36% complete]
-- [download 37% complete]
-- [download 38% complete]
-- [download 39% complete]
-- [download 40% complete]
-- [download 41% complete]
-- [download 42% complete]
-- [download 43% complete]
-- [download 44% complete]
-- [download 45% complete]
-- [download 46% complete]
-- [download 47% complete]
-- [download 48% complete]
-- [download 49% complete]
-- [download 50% complete]
-- [download 51% complete]
-- [download 52% complete]
-- [download 53% complete]
-- [download 54% complete]
-- [download 55% complete]
-- [download 56% complete]
-- [download 57% complete]
-- [download 58% complete]
-- [download 59% complete]
-- [download 60% complete]
-- [download 61% complete]
-- [download 62% complete]
-- [download 63% complete]
-- [download 64% complete]
-- [download 65% complete]
-- [download 66% complete]
-- [download 67% complete]
-- [download 68% complete]
-- [download 69% complete]
-- [download 70% complete]
-- [download 71% complete]
-- [download 72% complete]
-- [download 73% complete]
-- [download 74% complete]
-- [download 75% complete]
-- [download 76% complete]
-- [download 77% complete]
-- [download 78% complete]
-- [download 79% complete]
-- [download 80% complete]
-- [download 81% complete]
-- [download 82% complete]
-- [download 83% complete]
-- [download 84% complete]
-- [download 85% complete]
-- [download 86% complete]
-- [download 87% complete]
-- [download 88% complete]
-- [download 89% complete]
-- [download 90% complete]
-- [download 91% complete]
-- [download 92% complete]
-- [download 93% complete]
-- [download 94% complete]
-- [download 95% complete]
-- [download 96% complete]
-- [download 97% complete]
-- [download 98% complete]
-- [download 99% complete]
-- [download 100% complete]
-- cd /builds/dbscale/dbscale/deps_build; tar xfz libevent-2.1.8-glibc.tar.gz
-- [download 0% complete]
-- [download 1% complete]
-- [download 2% complete]
-- [download 3% complete]
-- [download 4% complete]
-- [download 5% complete]
-- [download 6% complete]
-- [download 7% complete]
-- [download 8% complete]
-- [download 9% complete]
-- [download 10% complete]
-- [download 11% complete]
-- [download 12% complete]
-- [download 13% complete]
-- [download 14% complete]
-- [download 15% complete]
-- [download 16% complete]
-- [download 17% complete]
-- [download 18% complete]
-- [download 19% complete]
-- [download 20% complete]
-- [download 21% complete]
-- [download 22% complete]
-- [download 23% complete]
-- [download 24% complete]
-- [download 25% complete]
-- [download 26% complete]
-- [download 27% complete]
-- [download 28% complete]
-- [download 29% complete]
-- [download 30% complete]
-- [download 31% complete]
-- [download 32% complete]
-- [download 33% complete]
-- [download 34% complete]
-- [download 35% complete]
-- [download 36% complete]
-- [download 37% complete]
-- [download 38% complete]
-- [download 39% complete]
-- [download 40% complete]
-- [download 41% complete]
-- [download 42% complete]
-- [download 43% complete]
-- [download 44% complete]
-- [download 45% complete]
-- [download 46% complete]
-- [download 47% complete]
-- [download 48% complete]
-- [download 49% complete]
-- [download 50% complete]
-- [download 51% complete]
-- [download 52% complete]
-- [download 53% complete]
-- [download 54% complete]
-- [download 55% complete]
-- [download 56% complete]
-- [download 57% complete]
-- [download 58% complete]
-- [download 59% complete]
-- [download 60% complete]
-- [download 61% complete]
-- [download 62% complete]
-- [download 63% complete]
-- [download 64% complete]
-- [download 65% complete]
-- [download 66% complete]
-- [download 67% complete]
-- [download 68% complete]
-- [download 69% complete]
-- [download 70% complete]
-- [download 71% complete]
-- [download 72% complete]
-- [download 73% complete]
-- [download 74% complete]
-- [download 75% complete]
-- [download 76% complete]
-- [download 77% complete]
-- [download 78% complete]
-- [download 79% complete]
-- [download 80% complete]
-- [download 81% complete]
-- [download 82% complete]
-- [download 83% complete]
-- [download 84% complete]
-- [download 85% complete]
-- [download 86% complete]
-- [download 87% complete]
-- [download 88% complete]
-- [download 89% complete]
-- [download 90% complete]
-- [download 91% complete]
-- [download 92% complete]
-- [download 93% complete]
-- [download 94% complete]
-- [download 95% complete]
-- [download 96% complete]
-- [download 97% complete]
-- [download 98% complete]
-- [download 99% complete]
-- [download 100% complete]
-- cd /builds/dbscale/dbscale/deps_build; tar xfz mysql-connector-c-6.1.11-glibc.tar.gz
-- [download 1% complete]
-- [download 2% complete]
-- [download 3% complete]
-- [download 5% complete]
-- [download 6% complete]
-- [download 8% complete]
-- [download 9% complete]
-- [download 12% complete]
-- [download 13% complete]
-- [download 14% complete]
-- [download 15% complete]
-- [download 17% complete]
-- [download 19% complete]
-- [download 21% complete]
-- [download 22% complete]
-- [download 24% complete]
-- [download 25% complete]
-- [download 27% complete]
-- [download 28% complete]
-- [download 30% complete]
-- [download 31% complete]
-- [download 33% complete]
-- [download 35% complete]
-- [download 37% complete]
-- [download 40% complete]
-- [download 42% complete]
-- [download 44% complete]
-- [download 46% complete]
-- [download 48% complete]
-- [download 51% complete]
-- [download 53% complete]
-- [download 54% complete]
-- [download 57% complete]
-- [download 59% complete]
-- [download 60% complete]
-- [download 62% complete]
-- [download 64% complete]
-- [download 66% complete]
-- [download 68% complete]
-- [download 70% complete]
-- [download 72% complete]
-- [download 74% complete]
-- [download 76% complete]
-- [download 78% complete]
-- [download 80% complete]
-- [download 81% complete]
-- [download 83% complete]
-- [download 85% complete]
-- [download 87% complete]
-- [download 89% complete]
-- [download 91% complete]
-- [download 93% complete]
-- [download 95% complete]
-- [download 97% complete]
-- [download 99% complete]
-- [download 100% complete]
-- cd /builds/dbscale/dbscale/deps_build; tar xfz gmp-6.1.2-glibc.tar.gz
-- [download 0% complete]
-- [download 1% complete]
-- [download 2% complete]
-- [download 3% complete]
-- [download 4% complete]
-- [download 5% complete]
-- [download 6% complete]
-- [download 7% complete]
-- [download 8% complete]
-- [download 9% complete]
-- [download 10% complete]
-- [download 11% complete]
-- [download 12% complete]
-- [download 13% complete]
-- [download 14% complete]
-- [download 15% complete]
-- [download 16% complete]
-- [download 17% complete]
-- [download 18% complete]
-- [download 19% complete]
-- [download 20% complete]
-- [download 21% complete]
-- [download 22% complete]
-- [download 23% complete]
-- [download 24% complete]
-- [download 25% complete]
-- [download 26% complete]
-- [download 27% complete]
-- [download 28% complete]
-- [download 29% complete]
-- [download 30% complete]
-- [download 31% complete]
-- [download 32% complete]
-- [download 33% complete]
-- [download 34% complete]
-- [download 35% complete]
-- [download 36% complete]
-- [download 37% complete]
-- [download 38% complete]
-- [download 39% complete]
-- [download 40% complete]
-- [download 41% complete]
-- [download 42% complete]
-- [download 43% complete]
-- [download 44% complete]
-- [download 45% complete]
-- [download 46% complete]
-- [download 47% complete]
-- [download 48% complete]
-- [download 49% complete]
-- [download 50% complete]
-- [download 51% complete]
-- [download 52% complete]
-- [download 53% complete]
-- [download 54% complete]
-- [download 55% complete]
-- [download 56% complete]
-- [download 57% complete]
-- [download 58% complete]
-- [download 59% complete]
-- [download 60% complete]
-- [download 61% complete]
-- [download 62% complete]
-- [download 63% complete]
-- [download 64% complete]
-- [download 65% complete]
-- [download 66% complete]
-- [download 67% complete]
-- [download 68% complete]
-- [download 69% complete]
-- [download 70% complete]
-- [download 71% complete]
-- [download 72% complete]
-- [download 73% complete]
-- [download 74% complete]
-- [download 75% complete]
-- [download 76% complete]
-- [download 77% complete]
-- [download 78% complete]
-- [download 79% complete]
-- [download 80% complete]
-- [download 81% complete]
-- [download 82% complete]
-- [download 83% complete]
-- [download 84% complete]
-- [download 85% complete]
-- [download 86% complete]
-- [download 87% complete]
-- [download 88% complete]
-- [download 89% complete]
-- [download 90% complete]
-- [download 91% complete]
-- [download 92% complete]
-- [download 93% complete]
-- [download 94% complete]
-- [download 95% complete]
-- [download 96% complete]
-- [download 97% complete]
-- [download 98% complete]
-- [download 99% complete]
-- [download 100% complete]
-- cd /builds/dbscale/dbscale/deps_build; tar xfz boost-1.66.0-glibc.tar.gz
-- [download 0% complete]
-- [download 1% complete]
-- [download 2% complete]
-- [download 3% complete]
-- [download 4% complete]
-- [download 5% complete]
-- [download 6% complete]
-- [download 7% complete]
-- [download 8% complete]
-- [download 9% complete]
-- [download 10% complete]
-- [download 11% complete]
-- [download 12% complete]
-- [download 13% complete]
-- [download 14% complete]
-- [download 15% complete]
-- [download 16% complete]
-- [download 17% complete]
-- [download 18% complete]
-- [download 19% complete]
-- [download 20% complete]
-- [download 21% complete]
-- [download 22% complete]
-- [download 23% complete]
-- [download 24% complete]
-- [download 25% complete]
-- [download 26% complete]
-- [download 27% complete]
-- [download 28% complete]
-- [download 29% complete]
-- [download 30% complete]
-- [download 31% complete]
-- [download 32% complete]
-- [download 33% complete]
-- [download 34% complete]
-- [download 35% complete]
-- [download 36% complete]
-- [download 37% complete]
-- [download 38% complete]
-- [download 39% complete]
-- [download 40% complete]
-- [download 41% complete]
-- [download 42% complete]
-- [download 43% complete]
-- [download 44% complete]
-- [download 45% complete]
-- [download 46% complete]
-- [download 47% complete]
-- [download 48% complete]
-- [download 49% complete]
-- [download 50% complete]
-- [download 51% complete]
-- [download 52% complete]
-- [download 53% complete]
-- [download 54% complete]
-- [download 55% complete]
-- [download 56% complete]
-- [download 57% complete]
-- [download 58% complete]
-- [download 59% complete]
-- [download 60% complete]
-- [download 61% complete]
-- [download 62% complete]
-- [download 63% complete]
-- [download 64% complete]
-- [download 65% complete]
-- [download 66% complete]
-- [download 67% complete]
-- [download 68% complete]
-- [download 69% complete]
-- [download 70% complete]
-- [download 71% complete]
-- [download 72% complete]
-- [download 73% complete]
-- [download 74% complete]
-- [download 75% complete]
-- [download 76% complete]
-- [download 77% complete]
-- [download 78% complete]
-- [download 79% complete]
-- [download 80% complete]
-- [download 81% complete]
-- [download 82% complete]
-- [download 83% complete]
-- [download 84% complete]
-- [download 85% complete]
-- [download 86% complete]
-- [download 87% complete]
-- [download 88% complete]
-- [download 89% complete]
-- [download 90% complete]
-- [download 91% complete]
-- [download 92% complete]
-- [download 93% complete]
-- [download 94% complete]
-- [download 95% complete]
-- [download 96% complete]
-- [download 97% complete]
-- [download 98% complete]
-- [download 99% complete]
-- [download 100% complete]
-- cd /builds/dbscale/dbscale/deps_build; tar xfz zeromq-4.0.5-glibc.tar.gz
-- [download 0% complete]
-- [download 1% complete]
-- [download 2% complete]
-- [download 3% complete]
-- [download 4% complete]
-- [download 5% complete]
-- [download 6% complete]
-- [download 7% complete]
-- [download 8% complete]
-- [download 9% complete]
-- [download 10% complete]
-- [download 11% complete]
-- [download 12% complete]
-- [download 13% complete]
-- [download 14% complete]
-- [download 15% complete]
-- [download 16% complete]
-- [download 17% complete]
-- [download 18% complete]
-- [download 19% complete]
-- [download 20% complete]
-- [download 21% complete]
-- [download 22% complete]
-- [download 23% complete]
-- [download 24% complete]
-- [download 26% complete]
-- [download 27% complete]
-- [download 28% complete]
-- [download 29% complete]
-- [download 30% complete]
-- [download 31% complete]
-- [download 32% complete]
-- [download 33% complete]
-- [download 35% complete]
-- [download 36% complete]
-- [download 37% complete]
-- [download 38% complete]
-- [download 39% complete]
-- [download 40% complete]
-- [download 41% complete]
-- [download 42% complete]
-- [download 43% complete]
-- [download 45% complete]
-- [download 46% complete]
-- [download 47% complete]
-- [download 48% complete]
-- [download 49% complete]
-- [download 50% complete]
-- [download 51% complete]
-- [download 52% complete]
-- [download 53% complete]
-- [download 55% complete]
-- [download 56% complete]
-- [download 57% complete]
-- [download 58% complete]
-- [download 59% complete]
-- [download 60% complete]
-- [download 61% complete]
-- [download 62% complete]
-- [download 64% complete]
-- [download 65% complete]
-- [download 66% complete]
-- [download 67% complete]
-- [download 68% complete]
-- [download 69% complete]
-- [download 70% complete]
-- [download 71% complete]
-- [download 72% complete]
-- [download 74% complete]
-- [download 75% complete]
-- [download 76% complete]
-- [download 77% complete]
-- [download 78% complete]
-- [download 79% complete]
-- [download 80% complete]
-- [download 81% complete]
-- [download 82% complete]
-- [download 84% complete]
-- [download 85% complete]
-- [download 86% complete]
-- [download 87% complete]
-- [download 88% complete]
-- [download 89% complete]
-- [download 90% complete]
-- [download 91% complete]
-- [download 93% complete]
-- [download 94% complete]
-- [download 95% complete]
-- [download 96% complete]
-- [download 97% complete]
-- [download 98% complete]
-- [download 99% complete]
-- [download 100% complete]
-- cd /builds/dbscale/dbscale/deps_build; tar xfz czmq-3.0.2-glibc.tar.gz
-- [download 1% complete]
-- [download 3% complete]
-- [download 5% complete]
-- [download 6% complete]
-- [download 8% complete]
-- [download 10% complete]
-- [download 12% complete]
-- [download 14% complete]
-- [download 15% complete]
-- [download 17% complete]
-- [download 19% complete]
-- [download 21% complete]
-- [download 23% complete]
-- [download 24% complete]
-- [download 26% complete]
-- [download 28% complete]
-- [download 30% complete]
-- [download 32% complete]
-- [download 34% complete]
-- [download 35% complete]
-- [download 37% complete]
-- [download 39% complete]
-- [download 41% complete]
-- [download 43% complete]
-- [download 44% complete]
-- [download 46% complete]
-- [download 48% complete]
-- [download 50% complete]
-- [download 52% complete]
-- [download 53% complete]
-- [download 55% complete]
-- [download 57% complete]
-- [download 59% complete]
-- [download 61% complete]
-- [download 62% complete]
-- [download 64% complete]
-- [download 66% complete]
-- [download 68% complete]
-- [download 70% complete]
-- [download 71% complete]
-- [download 73% complete]
-- [download 75% complete]
-- [download 77% complete]
-- [download 79% complete]
-- [download 80% complete]
-- [download 82% complete]
-- [download 84% complete]
-- [download 86% complete]
-- [download 88% complete]
-- [download 90% complete]
-- [download 91% complete]
-- [download 93% complete]
-- [download 95% complete]
-- [download 97% complete]
-- [download 99% complete]
-- [download 100% complete]
-- cd /builds/dbscale/dbscale/deps_build; tar xfz zookeeper-3.5.8-glibc.tar.gz
-- [download 0% complete]
-- [download 1% complete]
-- [download 2% complete]
-- [download 3% complete]
-- [download 4% complete]
-- [download 5% complete]
-- [download 6% complete]
-- [download 7% complete]
-- [download 8% complete]
-- [download 9% complete]
-- [download 10% complete]
-- [download 11% complete]
-- [download 12% complete]
-- [download 13% complete]
-- [download 14% complete]
-- [download 15% complete]
-- [download 16% complete]
-- [download 17% complete]
-- [download 18% complete]
-- [download 19% complete]
-- [download 20% complete]
-- [download 21% complete]
-- [download 22% complete]
-- [download 23% complete]
-- [download 24% complete]
-- [download 25% complete]
-- [download 26% complete]
-- [download 27% complete]
-- [download 28% complete]
-- [download 29% complete]
-- [download 30% complete]
-- [download 31% complete]
-- [download 32% complete]
-- [download 33% complete]
-- [download 34% complete]
-- [download 35% complete]
-- [download 36% complete]
-- [download 37% complete]
-- [download 38% complete]
-- [download 39% complete]
-- [download 40% complete]
-- [download 41% complete]
-- [download 42% complete]
-- [download 43% complete]
-- [download 44% complete]
-- [download 45% complete]
-- [download 46% complete]
-- [download 47% complete]
-- [download 48% complete]
-- [download 49% complete]
-- [download 50% complete]
-- [download 51% complete]
-- [download 52% complete]
-- [download 53% complete]
-- [download 54% complete]
-- [download 55% complete]
-- [download 56% complete]
-- [download 57% complete]
-- [download 58% complete]
-- [download 59% complete]
-- [download 60% complete]
-- [download 61% complete]
-- [download 62% complete]
-- [download 63% complete]
-- [download 64% complete]
-- [download 65% complete]
-- [download 66% complete]
-- [download 67% complete]
-- [download 68% complete]
-- [download 69% complete]
-- [download 70% complete]
-- [download 71% complete]
-- [download 72% complete]
-- [download 73% complete]
-- [download 74% complete]
-- [download 75% complete]
-- [download 76% complete]
-- [download 77% complete]
-- [download 78% complete]
-- [download 79% complete]
-- [download 80% complete]
-- [download 81% complete]
-- [download 82% complete]
-- [download 83% complete]
-- [download 84% complete]
-- [download 85% complete]
-- [download 86% complete]
-- [download 87% complete]
-- [download 88% complete]
-- [download 89% complete]
-- [download 90% complete]
-- [download 91% complete]
-- [download 92% complete]
-- [download 93% complete]
-- [download 94% complete]
-- [download 95% complete]
-- [download 96% complete]
-- [download 97% complete]
-- [download 98% complete]
-- [download 99% complete]
-- [download 100% complete]
-- cd /builds/dbscale/dbscale/deps_build; tar xfz ACE-6.4.5-glibc.tar.gz
-- [download 0% complete]
-- [download 1% complete]
-- [download 2% complete]
-- [download 3% complete]
-- [download 4% complete]
-- [download 5% complete]
-- [download 6% complete]
-- [download 7% complete]
-- [download 8% complete]
-- [download 9% complete]
-- [download 10% complete]
-- [download 11% complete]
-- [download 12% complete]
-- [download 13% complete]
-- [download 14% complete]
-- [download 15% complete]
-- [download 16% complete]
-- [download 17% complete]
-- [download 18% complete]
-- [download 19% complete]
-- [download 20% complete]
-- [download 21% complete]
-- [download 22% complete]
-- [download 23% complete]
-- [download 24% complete]
-- [download 25% complete]
-- [download 26% complete]
-- [download 27% complete]
-- [download 28% complete]
-- [download 29% complete]
-- [download 30% complete]
-- [download 31% complete]
-- [download 32% complete]
-- [download 33% complete]
-- [download 34% complete]
-- [download 35% complete]
-- [download 36% complete]
-- [download 37% complete]
-- [download 38% complete]
-- [download 39% complete]
-- [download 40% complete]
-- [download 41% complete]
-- [download 42% complete]
-- [download 43% complete]
-- [download 44% complete]
-- [download 45% complete]
-- [download 46% complete]
-- [download 47% complete]
-- [download 48% complete]
-- [download 49% complete]
-- [download 50% complete]
-- [download 51% complete]
-- [download 52% complete]
-- [download 53% complete]
-- [download 54% complete]
-- [download 55% complete]
-- [download 56% complete]
-- [download 57% complete]
-- [download 58% complete]
-- [download 59% complete]
-- [download 60% complete]
-- [download 61% complete]
-- [download 62% complete]
-- [download 63% complete]
-- [download 64% complete]
-- [download 65% complete]
-- [download 66% complete]
-- [download 67% complete]
-- [download 68% complete]
-- [download 69% complete]
-- [download 70% complete]
-- [download 71% complete]
-- [download 72% complete]
-- [download 73% complete]
-- [download 74% complete]
-- [download 75% complete]
-- [download 76% complete]
-- [download 77% complete]
-- [download 78% complete]
-- [download 79% complete]
-- [download 80% complete]
-- [download 81% complete]
-- [download 82% complete]
-- [download 83% complete]
-- [download 84% complete]
-- [download 85% complete]
-- [download 86% complete]
-- [download 87% complete]
-- [download 88% complete]
-- [download 89% complete]
-- [download 90% complete]
-- [download 91% complete]
-- [download 92% complete]
-- [download 93% complete]
-- [download 94% complete]
-- [download 95% complete]
-- [download 96% complete]
-- [download 97% complete]
-- [download 98% complete]
-- [download 99% complete]
-- [download 100% complete]
-- cd /builds/dbscale/dbscale/deps_build; tar xfz openssl-1.0.2-glibc.tar.gz
-- Configuring done
-- Generating done
-- Build files have been written to: /builds/dbscale/dbscale/build
$ make -j6
Scanning dependencies of target GenServerSource
[  0%] Built target GenServerSource
[  1%] [  2%] Generating lex.yy.cc, scan.l.h
Generating gram.tab.cc, gram.tab.hh
Scanning dependencies of target get_hardid
[  3%] Building CXX object src/CMakeFiles/get_hardid.dir/get_hard.cc.o
/builds/dbscale/dbscale/driver/mysql//scan.l:649: warning, rule cannot be matched
/builds/dbscale/dbscale/driver/mysql//scan.l:1411: warning, -s option given but default rule can be matched
Scanning dependencies of target dbscale_core
Scanning dependencies of target mysql_driver
[  4%] [  5%] [  6%] [  7%] [  8%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_driver.cc.o
Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_handler.cc.o
Building CXX object src/CMakeFiles/dbscale_core.dir/driver.cc.o
Building CXX object src/CMakeFiles/dbscale_core.dir/option.cc.o
Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_connection.cc.o
Linking CXX executable get_hardid
[  8%] Built target get_hardid
[  9%] Building CXX object src/CMakeFiles/dbscale_core.dir/handler.cc.o
[ 10%] Building CXX object src/CMakeFiles/dbscale_core.dir/data_server.cc.o
[ 11%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_request.cc.o
[ 12%] Building CXX object src/CMakeFiles/dbscale_core.dir/log_tool.cc.o
[ 13%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_compare.cc.o
[ 14%] Building CXX object src/CMakeFiles/dbscale_core.dir/pid_file.cc.o
[ 15%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_password.cc.o
[ 16%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_parser.cc.o
[ 17%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_plan.cc.o
[ 18%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_session.cc.o
[ 18%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_compress.cc.o
[ 19%] Building CXX object src/CMakeFiles/dbscale_core.dir/sql_parser.cc.o
[ 20%] Building CXX object src/CMakeFiles/dbscale_core.dir/log.cc.o
[ 21%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_xa_transaction.cc.o
[ 22%] Building CXX object src/CMakeFiles/dbscale_core.dir/keepalive.cc.o
[ 22%] Building CXX object src/CMakeFiles/dbscale_core.dir/statement.cc.o
[ 23%] Building CXX object src/CMakeFiles/dbscale_core.dir/plan.cc.o
[ 24%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_binlog_worker.cc.o
[ 25%] Building CXX object src/CMakeFiles/dbscale_core.dir/data_source.cc.o
[ 26%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_binlog_event.cc.o
[ 27%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_binlog_handler.cc.o
[ 28%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_binlog_method.cc.o
[ 29%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_binlog_filter.cc.o
[ 30%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_binlog_driver.cc.o
[ 31%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_binlog_protocol.cc.o
[ 32%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_binlog_revert.cc.o
[ 33%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_source.cc.o
[ 34%] Building CXX object src/CMakeFiles/dbscale_core.dir/partition.cc.o
[ 35%] Building CXX object src/CMakeFiles/dbscale_core.dir/partition_method.cc.o
[ 36%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_migrate_util.cc.o
[ 37%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_binlog_thread.cc.o
[ 38%] Building CXX object src/CMakeFiles/dbscale_core.dir/backend.cc.o
[ 39%] Building CXX object src/CMakeFiles/dbscale_core.dir/data_space.cc.o
[ 40%] Building CXX object src/CMakeFiles/dbscale_core.dir/connection_pool.cc.o
[ 40%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_config.cc.o
[ 41%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_table_info.cc.o
[ 42%] Building CXX object src/CMakeFiles/dbscale_core.dir/packet.cc.o
[ 43%] Building CXX object src/CMakeFiles/dbscale_core.dir/expression.cc.o
[ 44%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_odbc.cc.o
[ 45%] Building CXX object src/CMakeFiles/dbscale_core.dir/license.cc.o
[ 46%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_binlog_message.cc.o
[ 47%] Building CXX object src/CMakeFiles/dbscale_core.dir/remote_execute.cc.o
[ 48%] Building CXX object src/CMakeFiles/dbscale_core.dir/xa_transaction.cc.o
[ 49%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_signal_handler.cc.o
[ 50%] Building CXX object src/CMakeFiles/dbscale_core.dir/basic.cc.o
[ 50%] Building CXX object src/CMakeFiles/dbscale_core.dir/thread_pool.cc.o
[ 51%] Building CXX object src/CMakeFiles/dbscale_core.dir/manager_thread.cc.o
[ 52%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_binlog_storage.cc.o
[ 53%] Building CXX object src/CMakeFiles/dbscale_core.dir/sub_query.cc.o
[ 54%] Building CXX object src/CMakeFiles/dbscale_core.dir/construct_sql.cc.o
[ 55%] Building CXX object src/CMakeFiles/dbscale_core.dir/cross_join_utils.cc.o
[ 56%] Building CXX object src/CMakeFiles/dbscale_core.dir/routine.cc.o
[ 57%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_binlog_flashback.cc.o
[ 58%] Building CXX object src/CMakeFiles/dbscale_core.dir/cross_node_join.cc.o
[ 59%] Building CXX object src/CMakeFiles/dbscale_core.dir/backend_thread.cc.o
[ 60%] Building CXX object src/CMakeFiles/dbscale_core.dir/execute_profile.cc.o
[ 61%] Building CXX object src/CMakeFiles/dbscale_core.dir/hash_method.cc.o
[ 62%] Building CXX object src/CMakeFiles/dbscale_core.dir/table_pool.cc.o
[ 63%] Building CXX object src/CMakeFiles/dbscale_core.dir/migrate_util.cc.o
[ 64%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_binlog_parallel.cc.o
[ 65%] Building CXX object src/CMakeFiles/dbscale_core.dir/connection.cc.o
[ 65%] Building CXX object src/CMakeFiles/dbscale_core.dir/transfer_rule.cc.o
[ 66%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/lex.yy.cc.o
[ 67%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/gram.tab.cc.o
[ 68%] Building CXX object src/CMakeFiles/dbscale_core.dir/keepalive_message.cc.o
[ 69%] Building CXX object src/CMakeFiles/dbscale_core.dir/multiple.cc.o
[ 70%] Building CXX object src/CMakeFiles/dbscale_core.dir/cross_node_join_manager.cc.o
[ 71%] Building CXX object src/CMakeFiles/dbscale_core.dir/cluster_status.cc.o
[ 72%] Building CXX object src/CMakeFiles/dbscale_core.dir/multiple_message.cc.o
[ 73%] Building CXX object src/CMakeFiles/dbscale_core.dir/zookeeper_tool.cc.o
[ 74%] [ 75%] Building CXX object src/CMakeFiles/dbscale_core.dir/sha1.cc.o
Building CXX object src/CMakeFiles/dbscale_core.dir/config.cc.o
[ 76%] Building CXX object src/CMakeFiles/dbscale_core.dir/session.cc.o
[ 77%] Building CXX object src/CMakeFiles/dbscale_core.dir/socket_base_manager.cc.o
[ 78%] Building CXX object src/CMakeFiles/dbscale_core.dir/range_method.cc.o
[ 79%] Building CXX object src/CMakeFiles/dbscale_core.dir/mul_sync_topic.cc.o
[ 80%] Building CXX object src/CMakeFiles/dbscale_core.dir/memory_allocater.cc.o
[ 80%] Building CXX object src/CMakeFiles/dbscale_core.dir/table_info.cc.o
[ 81%] Building CXX object src/CMakeFiles/dbscale_core.dir/optimize_node.cc.o
[ 82%] Building CXX object src/CMakeFiles/dbscale_core.dir/join_optimizer.cc.o
[ 83%] Building CXX object src/CMakeFiles/dbscale_core.dir/message_service.cc.o
[ 84%] Building CXX object src/CMakeFiles/dbscale_core.dir/message_zmq.cc.o
[ 85%] Building CXX object src/CMakeFiles/dbscale_core.dir/signal_handler.cc.o
[ 86%] Building CXX object src/CMakeFiles/dbscale_core.dir/async_task_manager.cc.o
[ 87%] Building CXX object src/CMakeFiles/dbscale_core.dir/sql/executor.cc.o
[ 88%] Building CXX object src/CMakeFiles/dbscale_core.dir/ssl_tool.cc.o
[ 89%] Building CXX object src/CMakeFiles/dbscale_core.dir/exception.cc.o
[ 90%] Building CXX object src/CMakeFiles/dbscale_core.dir/gtid.cc.o
[ 91%] Building CXX object src/CMakeFiles/dbscale_core.dir/monitor_point_handler.cc.o
Linking CXX shared library libmysql_driver.so
[ 91%] Built target mysql_driver
Linking CXX shared library libdbscale_core.so
[ 91%] Built target dbscale_core
Scanning dependencies of target makelic
Scanning dependencies of target derror
[ 92%] Building CXX object src/CMakeFiles/makelic.dir/makelic.cc.o
Scanning dependencies of target fetch_config_file_from_zoo
[ 93%] Scanning dependencies of target pre_check
Building CXX object src/CMakeFiles/derror.dir/derror.cc.o
Scanning dependencies of target dbscale
Scanning dependencies of target clean_zookeeper
[ 93%] Building CXX object src/CMakeFiles/fetch_config_file_from_zoo.dir/generate_config.cc.o
[ 93%] [ 94%] Building CXX object src/CMakeFiles/clean_zookeeper.dir/clean_zookeeper.cc.o
[ 95%] Building CXX object src/CMakeFiles/pre_check.dir/pre_checking.cc.o
Building CXX object src/CMakeFiles/dbscale.dir/main.cc.o
Linking CXX executable makelic
[ 95%] Built target makelic
Scanning dependencies of target print_default
[ 96%] Building CXX object src/CMakeFiles/print_default.dir/print_default.cc.o
Linking CXX executable derror
[ 96%] Built target derror
Scanning dependencies of target rebalance
[ 97%] Building CXX object src/CMakeFiles/rebalance.dir/rebalance.cc.o
Linking CXX executable print_default
[ 97%] Built target print_default
Scanning dependencies of target reload_current_config
[ 98%] Building CXX object src/CMakeFiles/reload_current_config.dir/reload_current_config.cc.o
Linking CXX executable clean_zookeeper
Linking CXX executable dbscale
[ 98%] Built target clean_zookeeper
Scanning dependencies of target dbscale_binlog_tool
[ 98%] Built target dbscale
[ 99%] Building CXX object driver/mysql/CMakeFiles/dbscale_binlog_tool.dir/mysql_binlog_tool.cc.o
Scanning dependencies of target dbscale_catchup_tool
[100%] Building CXX object driver/mysql/CMakeFiles/dbscale_catchup_tool.dir/dbscale_catchup_tool.cc.o
Linking CXX executable fetch_config_file_from_zoo
[100%] Built target fetch_config_file_from_zoo
Linking CXX executable pre_check
[100%] Built target pre_check
Linking CXX executable rebalance
[100%] Built target rebalance
Linking CXX executable reload_current_config
[100%] Built target reload_current_config
Linking CXX executable dbscale_binlog_tool
Linking CXX executable dbscale_catchup_tool
[100%] Built target dbscale_binlog_tool
[100%] Built target dbscale_catchup_tool
$ git clone http://gitlab.greatopensource.com/dbscale/jemalloc-libs.git
Cloning into 'jemalloc-libs'...
$ cp jemalloc-libs/centos7/x86/libjemalloc.so /usr/local/lib/
$ cd ../extra && /bin/bash pack_dbscale.sh --build
===copy bin files
===copy direct_lib files
===copy extra files
===copy lvs files
===copy ssl files
===copy daemon files
===copy ha files
===copy changelog files
===copy manual files
===copy ldd and system libs
===copy malloc libs
===copy libz libs
DBScale-3.0.6691.c3efe6c-glibc2.17.tar.gz
===build tarball
Pack finished at:
/root/GreatDB-Cluster-Product/DBScale-3.0.6691.c3efe6c-glibc2.17.tar.gz
$ cd ../
$ mkdir binaries
$ git_no=`git log --oneline | wc -l | tr -d '\n'`
$ git_rev=`git rev-parse --short=8 HEAD | tr -d '\n'`
$ DBSCALE_VERSION=`cat VERSION | tr -d '\n' | sed 's/ /-/g'`
$ mv $PRODUCT_DIR/*.tar.gz binaries/$DBSCALE_VERSION.$git_no-$git_rev-Linux-glibc2.17-x86_64.tar.gz
$ echo "build EverDB"
build EverDB
$ echo "EDB-Grid v1.3.2" > VERSION
$ cd build
$ cmake -DCMAKE_BUILD_TYPE=RelWithDebInfo -DDBSCALE_DISABLE_SPARK=YES -DDOWNLOAD_DEPS=YES -DDBSCALE_TEST_DISABLE=YES -DGCOV_DISABLE=YES -DSKIP_GTEST=ON -DHAVE_OPENSSL=YES -DDBSCALE_BUILD=YES ..
-- Build grid
-- Bison Version: bison (GNU Bison) 2.7
-- CMAKE_SYSTEM_INFO_FILE: Platform/Linux
-- CMAKE_SYSTEM_NAME:      Linux
-- CMAKE_SYSTEM_PROCESSOR: x86_64
-- CMAKE_SYSTEM:           Linux-3.10.0-1127.10.1.el7.x86_64
-- OS Version: centos7
-- OS Architecure:x86_64
-- jemalloc-5.0.1 already be installed!
-- libevent-2.1.8 already be installed!
-- mysql-connector-c-6.1.11 already be installed!
-- gmp-6.1.2 already be installed!
-- boost-1.66.0 already be installed!
-- zeromq-4.0.5 already be installed!
-- czmq-3.0.2 already be installed!
-- zookeeper-3.5.8 already be installed!
-- ACE-6.4.5 already be installed!
-- openssl-1.0.2 already be installed!
-- Configuring done
-- Generating done
-- Build files have been written to: /builds/dbscale/dbscale/build
$ make -j6
Scanning dependencies of target GenServerSource
[  0%] Built target GenServerSource
Scanning dependencies of target get_hardid
[  1%] Building CXX object src/CMakeFiles/get_hardid.dir/get_hard.cc.o
Scanning dependencies of target mysql_driver
[  2%] [  3%] [  4%] [  5%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_request.cc.o
Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_connection.cc.o
Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_driver.cc.o
Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_handler.cc.o
Scanning dependencies of target dbscale_core
[  6%] Building CXX object src/CMakeFiles/dbscale_core.dir/option.cc.o
Linking CXX executable get_hardid
[  6%] Built target get_hardid
[  7%] Building CXX object src/CMakeFiles/dbscale_core.dir/driver.cc.o
[  8%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_compare.cc.o
[  9%] Building CXX object src/CMakeFiles/dbscale_core.dir/handler.cc.o
[ 10%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_password.cc.o
[ 11%] Building CXX object src/CMakeFiles/dbscale_core.dir/data_server.cc.o
[ 12%] Building CXX object src/CMakeFiles/dbscale_core.dir/log_tool.cc.o
[ 13%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_parser.cc.o
[ 14%] Building CXX object src/CMakeFiles/dbscale_core.dir/pid_file.cc.o
[ 15%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_plan.cc.o
[ 16%] Building CXX object src/CMakeFiles/dbscale_core.dir/sql_parser.cc.o
[ 17%] Building CXX object src/CMakeFiles/dbscale_core.dir/log.cc.o
[ 18%] Building CXX object src/CMakeFiles/dbscale_core.dir/keepalive.cc.o
[ 18%] Building CXX object src/CMakeFiles/dbscale_core.dir/statement.cc.o
[ 19%] Building CXX object src/CMakeFiles/dbscale_core.dir/plan.cc.o
[ 20%] Building CXX object src/CMakeFiles/dbscale_core.dir/data_source.cc.o
[ 21%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_session.cc.o
[ 21%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_compress.cc.o
[ 22%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_xa_transaction.cc.o
[ 23%] Building CXX object src/CMakeFiles/dbscale_core.dir/partition.cc.o
[ 24%] Building CXX object src/CMakeFiles/dbscale_core.dir/partition_method.cc.o
[ 25%] Building CXX object src/CMakeFiles/dbscale_core.dir/backend.cc.o
[ 26%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_binlog_worker.cc.o
[ 27%] Building CXX object src/CMakeFiles/dbscale_core.dir/data_space.cc.o
[ 28%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_binlog_event.cc.o
[ 29%] Building CXX object src/CMakeFiles/dbscale_core.dir/connection_pool.cc.o
[ 30%] Building CXX object src/CMakeFiles/dbscale_core.dir/packet.cc.o
[ 31%] Building CXX object src/CMakeFiles/dbscale_core.dir/expression.cc.o
[ 32%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_binlog_handler.cc.o
[ 33%] Building CXX object src/CMakeFiles/dbscale_core.dir/license.cc.o
[ 34%] Building CXX object src/CMakeFiles/dbscale_core.dir/remote_execute.cc.o
[ 35%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_binlog_method.cc.o
[ 36%] Building CXX object src/CMakeFiles/dbscale_core.dir/xa_transaction.cc.o
[ 37%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_binlog_filter.cc.o
[ 38%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_binlog_driver.cc.o
[ 39%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_binlog_protocol.cc.o
[ 40%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_binlog_revert.cc.o
[ 41%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_source.cc.o
[ 42%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_migrate_util.cc.o
[ 43%] Building CXX object src/CMakeFiles/dbscale_core.dir/basic.cc.o
[ 43%] Building CXX object src/CMakeFiles/dbscale_core.dir/thread_pool.cc.o
[ 44%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_binlog_thread.cc.o
[ 44%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_config.cc.o
[ 45%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_table_info.cc.o
[ 46%] Building CXX object src/CMakeFiles/dbscale_core.dir/manager_thread.cc.o
[ 47%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_odbc.cc.o
[ 48%] Building CXX object src/CMakeFiles/dbscale_core.dir/sub_query.cc.o
[ 49%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_binlog_message.cc.o
[ 50%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_signal_handler.cc.o
[ 51%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_binlog_storage.cc.o
[ 52%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_binlog_flashback.cc.o
[ 53%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/mysql_binlog_parallel.cc.o
[ 54%] Building CXX object src/CMakeFiles/dbscale_core.dir/construct_sql.cc.o
[ 55%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/lex.yy.cc.o
[ 56%] Building CXX object driver/mysql/CMakeFiles/mysql_driver.dir/gram.tab.cc.o
[ 57%] Building CXX object src/CMakeFiles/dbscale_core.dir/cross_join_utils.cc.o
[ 58%] Building CXX object src/CMakeFiles/dbscale_core.dir/routine.cc.o
[ 59%] Building CXX object src/CMakeFiles/dbscale_core.dir/cross_node_join.cc.o
[ 60%] Building CXX object src/CMakeFiles/dbscale_core.dir/backend_thread.cc.o
[ 61%] Building CXX object src/CMakeFiles/dbscale_core.dir/execute_profile.cc.o
[ 62%] Building CXX object src/CMakeFiles/dbscale_core.dir/hash_method.cc.o
[ 63%] Building CXX object src/CMakeFiles/dbscale_core.dir/table_pool.cc.o
[ 64%] Building CXX object src/CMakeFiles/dbscale_core.dir/migrate_util.cc.o
[ 65%] Building CXX object src/CMakeFiles/dbscale_core.dir/connection.cc.o
[ 65%] Building CXX object src/CMakeFiles/dbscale_core.dir/transfer_rule.cc.o
[ 66%] Building CXX object src/CMakeFiles/dbscale_core.dir/keepalive_message.cc.o
[ 67%] Building CXX object src/CMakeFiles/dbscale_core.dir/multiple.cc.o
[ 68%] Building CXX object src/CMakeFiles/dbscale_core.dir/cross_node_join_manager.cc.o
[ 69%] Building CXX object src/CMakeFiles/dbscale_core.dir/cluster_status.cc.o
[ 70%] Building CXX object src/CMakeFiles/dbscale_core.dir/multiple_message.cc.o
[ 71%] Building CXX object src/CMakeFiles/dbscale_core.dir/zookeeper_tool.cc.o
[ 72%] Building CXX object src/CMakeFiles/dbscale_core.dir/sha1.cc.o
[ 73%] Building CXX object src/CMakeFiles/dbscale_core.dir/config.cc.o
[ 74%] Building CXX object src/CMakeFiles/dbscale_core.dir/session.cc.o
[ 75%] Building CXX object src/CMakeFiles/dbscale_core.dir/socket_base_manager.cc.o
[ 76%] Building CXX object src/CMakeFiles/dbscale_core.dir/range_method.cc.o
[ 77%] Building CXX object src/CMakeFiles/dbscale_core.dir/mul_sync_topic.cc.o
[ 78%] Building CXX object src/CMakeFiles/dbscale_core.dir/memory_allocater.cc.o
Linking CXX shared library libmysql_driver.so
[ 78%] Building CXX object src/CMakeFiles/dbscale_core.dir/table_info.cc.o
[ 80%] Built target mysql_driver
[ 81%] Building CXX object src/CMakeFiles/dbscale_core.dir/optimize_node.cc.o
[ 82%] Building CXX object src/CMakeFiles/dbscale_core.dir/join_optimizer.cc.o
[ 83%] Building CXX object src/CMakeFiles/dbscale_core.dir/message_service.cc.o
[ 84%] Building CXX object src/CMakeFiles/dbscale_core.dir/message_zmq.cc.o
[ 85%] Building CXX object src/CMakeFiles/dbscale_core.dir/signal_handler.cc.o
[ 86%] Building CXX object src/CMakeFiles/dbscale_core.dir/async_task_manager.cc.o
[ 87%] Building CXX object src/CMakeFiles/dbscale_core.dir/sql/executor.cc.o
[ 88%] Building CXX object src/CMakeFiles/dbscale_core.dir/ssl_tool.cc.o
[ 89%] Building CXX object src/CMakeFiles/dbscale_core.dir/exception.cc.o
[ 90%] Building CXX object src/CMakeFiles/dbscale_core.dir/gtid.cc.o
[ 91%] Building CXX object src/CMakeFiles/dbscale_core.dir/monitor_point_handler.cc.o
Linking CXX shared library libdbscale_core.so
[ 91%] Built target dbscale_core
Scanning dependencies of target makelic
Scanning dependencies of target derror
Scanning dependencies of target clean_zookeeper
[ 92%] Building CXX object src/CMakeFiles/makelic.dir/makelic.cc.o
Scanning dependencies of target dbscale
Scanning dependencies of target fetch_config_file_from_zoo
Scanning dependencies of target pre_check
[ 93%] Building CXX object src/CMakeFiles/derror.dir/derror.cc.o
[ 93%] Building CXX object src/CMakeFiles/clean_zookeeper.dir/clean_zookeeper.cc.o
[ 93%] Building CXX object src/CMakeFiles/fetch_config_file_from_zoo.dir/generate_config.cc.o
[ 94%] [ 95%] Building CXX object src/CMakeFiles/pre_check.dir/pre_checking.cc.o
Building CXX object src/CMakeFiles/dbscale.dir/main.cc.o
Linking CXX executable makelic
[ 95%] Built target makelic
Scanning dependencies of target print_default
[ 96%] Building CXX object src/CMakeFiles/print_default.dir/print_default.cc.o
Linking CXX executable derror
[ 96%] Built target derror
Scanning dependencies of target rebalance
[ 97%] Building CXX object src/CMakeFiles/rebalance.dir/rebalance.cc.o
Linking CXX executable print_default
[ 97%] Built target print_default
Scanning dependencies of target reload_current_config
[ 98%] Building CXX object src/CMakeFiles/reload_current_config.dir/reload_current_config.cc.o
Linking CXX executable clean_zookeeper
[ 98%] Built target clean_zookeeper
Scanning dependencies of target dbscale_binlog_tool
[ 99%] Building CXX object driver/mysql/CMakeFiles/dbscale_binlog_tool.dir/mysql_binlog_tool.cc.o
Linking CXX executable fetch_config_file_from_zoo
[ 99%] Built target fetch_config_file_from_zoo
Scanning dependencies of target dbscale_catchup_tool
[100%] Building CXX object driver/mysql/CMakeFiles/dbscale_catchup_tool.dir/dbscale_catchup_tool.cc.o
Linking CXX executable pre_check
[100%] Built target pre_check
Linking CXX executable dbscale
[100%] Built target dbscale
Linking CXX executable rebalance
[100%] Built target rebalance
Linking CXX executable reload_current_config
[100%] Built target reload_current_config
Linking CXX executable dbscale_binlog_tool
[100%] Built target dbscale_binlog_tool
Linking CXX executable dbscale_catchup_tool
[100%] Built target dbscale_catchup_tool
$ cd ../extra && /bin/bash pack_dbscale.sh --build
===copy bin files
===copy direct_lib files
===copy extra files
===copy lvs files
===copy ssl files
===copy daemon files
===copy ha files
===copy changelog files
===copy manual files
cp: cannot stat '/builds/dbscale/dbscale/doc/EverDB*.pdf': No such file or directory
===copy ldd and system libs
===copy malloc libs
===copy libz libs
EDB-Grid-v1.3.2.6691.c3efe6c-glibc2.17.tar.gz
===build tarball
Pack finished at:
/root/GreatDB-Cluster-Product/EDB-Grid-v1.3.2.6691.c3efe6c-glibc2.17.tar.gz
$ cd ../
$ DBSCALE_VERSION=`cat VERSION | tr -d '\n' | sed 's/ /-/g'`
$ mv $PRODUCT_DIR/*.tar.gz binaries/$DBSCALE_VERSION.$git_no-$git_rev-Linux-glibc2.17-x86_64.tar.gz
$ cd binaries/ && md5sum * > md5sum.txt && cat md5sum.txt && cd -
3973a5de2a1ac9a6a8110b53e2107b90  DBScale-3.0.6691-c3efe6ca-Linux-glibc2.17-x86_64.tar.gz
bc0f5678bf86511be67ec78c36e151c2  EDB-Grid-v1.3.2.6691-c3efe6ca-Linux-glibc2.17-x86_64.tar.gz
/builds/dbscale/dbscale
$ ls -l binaries/
total 235812
-rw-r--r-- 1 root root 121682795 Jun  3 12:19 DBScale-3.0.6691-c3efe6ca-Linux-glibc2.17-x86_64.tar.gz
-rw-r--r-- 1 root root 119777789 Jun  3 12:23 EDB-Grid-v1.3.2.6691-c3efe6ca-Linux-glibc2.17-x86_64.tar.gz
-rw-r--r-- 1 root root       184 Jun  3 12:23 md5sum.txt
Uploading artifacts for successful job
00:16
Uploading artifacts...
binaries/: found 4 matching files and directories
Uploading artifacts as "archive" to coordinator... ok  id=457925 responseStatus=201 Created token=64_2SfN3
Job succeeded

```



