---
title: mysql-8.0.35
description: mysql-8.0.35
date: 2023-11-06
weight: 30000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{%pageinfo%}}
- [mysql-server github](https://github.com/mysql/mysql-server/archive/refs/tags/mysql-8.0.35.tar.gz)
- [openssl 官网](http://www.openssl.org)


[guolicheng@48 test]$ md5sum mysql-server-mysql-8.0.35.tar.gz
8c2da74087303cdda2ecd44a9e0eaef7  mysql-server-mysql-8.0.35.tar.gz






{{%/pageinfo%}}
| 命令 | 软件包名称                       | MD5                              |                                                                                          |
| :--- | :------------------------------- | :------------------------------- | :--------------------------------------------------------------------------------------- |
|      | bison-3.8.2.tar.gz               | 1e541a097cda9eca675d29dd2832921f | [bison](https://ftp.gnu.org/gnu/bison/bison-3.8.2.tar.gz)                                |
|      | automake-1.16.4.tar.gz           | 2887ecb2978f03efc6fd84b8b33134f4 | [automake](https://ftp.gnu.org/gnu/automake/automake-1.16.4.tar.gz)                      |
|      | pkg-config-0.28.tar.gz           | aa3c86e67551adc3ac865160e34a2a0d | []                                                                                       |
|      | gmp-6.1.0.tar.bz2                | 86ee6e54ebfc4a90b643a65e402c4048 | [gmp](https://gmplib.org/)                                                               |
|      | mpfr-3.1.6.tar.bz2               | 320c28198def956aeacdb240b46b8969 | [mpfr](https://www.mpfr.org)                                                             |
|      | mpc-1.0.3.tar.gz                 | d6a1d5f8ddea3abd2cc3e98f58352d26 | [mpc](https://www.multiprecision.org/mpc/)                                               |
|      | isl-0.18.tar.bz2                 | 11436d6b205e516635b666090b94ab32 | [isl](https://gcc.gnu.org/pub/gcc/infrastructure/)                                       |
|      | gettext-0.22.4.tar.gz            | e541ba3603674a853cff9b28a68dd61e | [gettext](https://gnu.org/s/gettext/)                                                    |
|      | flex-2.6.3.tar.gz                | a5f65570cd9107ec8a8ec88f17b31bb1 | [flex](https://github.com/westes/flex/releases)                                          |
|      | icu-release-74-2.tar.gz          | 70e681a5d43fe813e98859d53049d150 | [libicu](https://github.com/unicode-org/icu/tags)                                        |
|      | gcc-10.4.0.tar.gz                | 854262f3582222f5dc8f093ca556e910 | [gcc](https://ftp.gnu.org/gnu/gcc/)                                                      |
|      | libtirpc-1.3.4.tar.bz2           | 375dbe7ceb2d0300d173fb40321b49b6 | [libtirpc](https://sourceforge.net/projects/libtirpc/)                                   |
|      | ncurses-6.4.tar.gz               | 5a62487b5d4ac6b132fe2bf9f8fad29b | [ncurses](https://ftp.gnu.org/gnu/ncurses/)                                              |
|      | boost_1_77_0.tar.bz2             | 91d027bceba1ab951ed2b29170ab3262 |                                                                                          |
|      | mysql-server-mysql-8.0.35.tar.gz | 8c2da74087303cdda2ecd44a9e0eaef7 | [mysql-server](https://github.com/mysql/mysql-server/archive/refs/tags/mysql-8.0.35.tar) |








{{<note>}}
<!---->
1. gcc gcc-c++ (10+)
2. cmake3
3. make （version >= 3.75）
4. ncurses ncurses-devel
5. openssl openssl-devel
6. libaio libaio-devel
7. bison (必须最新版本)
8. tirpc （libtirpc-1.3.4.tar.bz2，没有安装的话，会报xdr_free错误）
9. lz4
10. icu
11. protoc
12. protobuf
13. curl
14. rpcgen



```sql
gcc --version
g++ --version
yum --enablerepo=PowerTools install rpcgen
yum install  -y openldap compat-openldap openldap-clients openldap-servers openldap-devel
```

libtirpc

{{</note>}}



### cmake

- 指定安装根目录绝对路径：-DCMAKE_INSTALL_PREFIX=/xxx/mysql
- 指定g++绝对路径：-DCMAKE_CXX_COMPILER=/usr/bin/g++
- 指定boost源码目录：-DWITH_BOOST=xxx/boost
- 指定是否debug：-DWITH_DEBUG=1






```bash
git clone https://github.com/mysql/mysql-server.git
cd mysql-server
git branch -r
git branch
git checkout 8.0
git pull
git checkout mysql-8.0.32
git log
```







### cmake
需要指定ICU具体安装路径前缀，形如`-DWITH_ICU=/usr/local/`



#### debug
```bash
cmake3 ../mysql-server-mysql-8.0.35/ -DCMAKE_INSTALL_PREFIX=$HOME/local/mysql_3388 -DMYSQL_DATADIR=$HOME/local/mysql_3388/data -DWITH_ICU=$HOME/local  -DWITH_BOOST=./boost_1_77_0 -DSYSCONFDIR=$HOME/local/mysql_3388/etc -DWITH_INNOBASE_STORAGE_ENGINE=1  -DWITH_FEDERATED_STORAGE_ENGINE=1 -DWITH_BLACKHOLE_STORAGE_ENGINE=1 -DWITH_MYISAM_STORAGE_ENGINE=1 -DWITH_PERFSCHEMA_STORAGE_ENGINE=1  -DENABLED_LOCAL_INFILE=1  -DDEFAULT_CHARSET=utf8mb4 -DDEFAULT_COLLATION=utf8mb4_0900_ai_ci -DCMAKE_BUILD_TYPE=Debug -DFORCE_COLORED_OUTPUT=ON  --trace --trace-expand -DWITH_ICU=/usr/local/ > cmake3.out 2>cmake3
```

#### Release
```bash
cmake3 ../mysql-server-mysql-8.0.35/ -DCMAKE_INSTALL_PREFIX=$HOME/local/mysql_3306 -DMYSQL_DATADIR=$HOME/local/mysql_3306/data   -DWITH_BOOST=../mysql-server-mysql-8.0.35/boost -DSYSCONFDIR=$HOME/local/mysql/conf -DWITH_INNOBASE_STORAGE_ENGINE=1  -DWITH_FEDERATED_STORAGE_ENGINE=1 -DWITH_BLACKHOLE_STORAGE_ENGINE=1 -DWITH_MYISAM_STORAGE_ENGINE=1 -DWITH_PERFSCHEMA_STORAGE_ENGINE=1  -DENABLED_LOCAL_INFILE=1  -DDEFAULT_CHARSET=utf8mb4 -DDEFAULT_COLLATION=utf8mb4_0900_ai_ci -DCMAKE_BUILD_TYPE=Release -DFORCE_COLORED_OUTPUT=ON --trace --trace-expand > cmake3.trace.expand 2>&1

```


### make
```bash
make VERBOSE=1 -j15 > make.out 2>make.verbose &
```

### make install

```bash
make install
```

当您运行Cmake时，您可能需要向命令行添加选项。这里有些例子：

- -DBUILD_CONFIG=mysql_release：使用 Oracle 用来生成官方 MySQL 版本的二进制发行版的相同构建选项来配置源。

- -DCMAKE_INSTALL_PREFIX=dir_name：配置在特定位置安装的发行版。

- -DCPACK_MONOLITHIC_INSTALL=1：使make package生成单个安装文件而不是多个文件。

- -DWITH_DEBUG=1：构建具有调试支持的发行版。






## ubuntu

####  更新源
sudo apt-get update
####  备份原文件
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
####  编辑源列表文件
sudo vim /etc/apt/sources.list
#### 将原来的列表删除，添加如下内容（清华大学镜像源）

```bash
# start
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
```

####  更新源
sudo apt-get update
二、apt-get常用命令
```bash
apt-get使用于deb包管理式的操作系统，主要用于自动从互联网的软件仓库照中搜索、安装、升级、卸载软件或操作系统，需要root权限
sudo apt-get update 更新源
sudo apt-get install package 安装包
sudo apt-get remove package 删除包
sudo apt-cache search package 搜索软件包
sudo apt-cache show package 获取包的相关信息，如说明、大小、版本等
sudo apt-get install package --reinstall 重新安装包
sudo apt-get -f install 修复安装
sudo apt-get remove package --purge 删除包，包括配置文件等
sudo apt-get build-dep package 安装相关的编译环境
sudo apt-get upgrade 更新已安装的包
sudo apt-get dist-upgrade 升级系统
sudo apt-cache depends package 了解使用该包依赖那些包
sudo apt-cache rdepends package 查看该包被哪些包依赖
sudo apt-get source package 下载该包的源代码
sudo apt-get clean && sudo apt-get autoclean 清理无用的包
sudo apt-get check 检查是否有损坏的依赖

```





```bash
apt install build-essential cmake bison libncurses5-dev libssl-dev pkg-config






```















