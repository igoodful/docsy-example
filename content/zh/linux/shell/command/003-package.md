---
title: 3. 包管理
description: repotrack，rpm，yum
date: 2023-10-31
weight: 30
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

{{< /alert >}}


{{< alert title="sort参数详解" color="secondary">}}

{{< /alert >}}









## 查询已安装的软件包信息：`rpm -q`

### 查看安装了的软件包：`rpm -qa`
{{<alert>}}
- `rpm -qa | wc -l`       查看centos上安装了多少个软件
- `rpm -qa | sort`        查询所有已安装的软件包，并排序
- `rpm -qa | grep nginx`  查找某软件包是否安装

{{</alert>}}

```bash
root@dev:~# rpm -qa | grep vsftpd
vsftpd-3.0.3-36.el8.x86_64

```

### 查看已安装软件包的信息：`rpm -qi`
```bash
root@dev:~#  rpm -qi vsftpd-3.0.3-36.el8.x86_64
Name        : vsftpd
Version     : 3.0.3
Release     : 36.el8
Architecture: x86_64
Install Date: Thu 29 Feb 2024 07:41:07 PM CST
Group       : System Environment/Daemons
Size        : 355805
License     : GPLv2 with exceptions
Signature   : RSA/SHA256, Tue 11 Apr 2023 05:46:10 PM CST, Key ID 05b555b38483c65d
Source RPM  : vsftpd-3.0.3-36.el8.src.rpm
Build Date  : Fri 07 Apr 2023 04:53:25 AM CST
Build Host  : x86-06.stream.rdu2.redhat.com
Relocations : (not relocatable)
Packager    : builder@centos.org
Vendor      : CentOS
URL         : https://security.appspot.com/vsftpd.html
Summary     : Very Secure Ftp Daemon
Description :
vsftpd is a Very Secure FTP daemon. It was written completely from
scratch.


```

### 查看已安装软件包的所有安装文件：`rpm -ql`

```bash
root@dev:~# rpm -ql vsftpd-3.0.3-36.el8.x86_64
/etc/logrotate.d/vsftpd
/etc/pam.d/vsftpd
/etc/vsftpd
/etc/vsftpd/ftpusers
/etc/vsftpd/user_list
/etc/vsftpd/vsftpd.conf
/etc/vsftpd/vsftpd_conf_migrate.sh
/usr/lib/.build-id
/usr/lib/.build-id/9e
/usr/lib/.build-id/9e/e728247223cf0a52c97484dc2b4f6a003dda7b
/usr/lib/systemd/system-generators/vsftpd-generator
/usr/lib/systemd/system/vsftpd.service
/usr/lib/systemd/system/vsftpd.target
/usr/lib/systemd/system/vsftpd@.service
/usr/sbin/vsftpd
/usr/share/doc/vsftpd
/usr/share/doc/vsftpd/AUDIT
/usr/share/doc/vsftpd/BENCHMARKS
/usr/share/doc/vsftpd/BUGS
/usr/share/doc/vsftpd/COPYING
/usr/share/doc/vsftpd/Changelog
/usr/share/doc/vsftpd/EXAMPLE
/usr/share/doc/vsftpd/EXAMPLE/INTERNET_SITE
/usr/share/doc/vsftpd/EXAMPLE/INTERNET_SITE/README
/usr/share/doc/vsftpd/EXAMPLE/INTERNET_SITE/vsftpd.conf
/usr/share/doc/vsftpd/EXAMPLE/INTERNET_SITE/vsftpd.xinetd
/usr/share/doc/vsftpd/EXAMPLE/INTERNET_SITE_NOINETD
/usr/share/doc/vsftpd/EXAMPLE/INTERNET_SITE_NOINETD/README
/usr/share/doc/vsftpd/EXAMPLE/INTERNET_SITE_NOINETD/vsftpd.conf
/usr/share/doc/vsftpd/EXAMPLE/PER_IP_CONFIG
/usr/share/doc/vsftpd/EXAMPLE/PER_IP_CONFIG/README
/usr/share/doc/vsftpd/EXAMPLE/PER_IP_CONFIG/hosts.allow
/usr/share/doc/vsftpd/EXAMPLE/README
/usr/share/doc/vsftpd/EXAMPLE/VIRTUAL_HOSTS
/usr/share/doc/vsftpd/EXAMPLE/VIRTUAL_HOSTS/README
/usr/share/doc/vsftpd/EXAMPLE/VIRTUAL_USERS
/usr/share/doc/vsftpd/EXAMPLE/VIRTUAL_USERS/README
/usr/share/doc/vsftpd/EXAMPLE/VIRTUAL_USERS/logins.txt
/usr/share/doc/vsftpd/EXAMPLE/VIRTUAL_USERS/vsftpd.conf
/usr/share/doc/vsftpd/EXAMPLE/VIRTUAL_USERS/vsftpd.pam
/usr/share/doc/vsftpd/EXAMPLE/VIRTUAL_USERS_2
/usr/share/doc/vsftpd/EXAMPLE/VIRTUAL_USERS_2/README
/usr/share/doc/vsftpd/FAQ
/usr/share/doc/vsftpd/INSTALL
/usr/share/doc/vsftpd/LICENSE
/usr/share/doc/vsftpd/README
/usr/share/doc/vsftpd/README.security
/usr/share/doc/vsftpd/REWARD
/usr/share/doc/vsftpd/SECURITY
/usr/share/doc/vsftpd/SECURITY/DESIGN
/usr/share/doc/vsftpd/SECURITY/IMPLEMENTATION
/usr/share/doc/vsftpd/SECURITY/OVERVIEW
/usr/share/doc/vsftpd/SECURITY/TRUST
/usr/share/doc/vsftpd/SIZE
/usr/share/doc/vsftpd/SPEED
/usr/share/doc/vsftpd/TODO
/usr/share/doc/vsftpd/TUNING
/usr/share/doc/vsftpd/vsftpd.xinetd
/usr/share/man/man5/vsftpd.conf.5.gz
/usr/share/man/man8/vsftpd.8.gz
/var/ftp
/var/ftp/pub


```

### 查询软件包配置文件：`rpm -qc`

```bash
root@dev:~# rpm -qc vsftpd-3.0.3-36.el8.x86_64
/etc/logrotate.d/vsftpd
/etc/pam.d/vsftpd
/etc/vsftpd/ftpusers
/etc/vsftpd/user_list
/etc/vsftpd/vsftpd.conf

```
### 查询软件包帮助文档：`rpm -qd`

```bash
root@dev:~# rpm -qd vsftpd-3.0.3-36.el8.x86_64
/usr/share/doc/vsftpd/AUDIT
/usr/share/doc/vsftpd/BENCHMARKS
/usr/share/doc/vsftpd/BUGS
/usr/share/doc/vsftpd/COPYING
/usr/share/doc/vsftpd/Changelog
/usr/share/doc/vsftpd/EXAMPLE/INTERNET_SITE/README
/usr/share/doc/vsftpd/EXAMPLE/INTERNET_SITE/vsftpd.conf
/usr/share/doc/vsftpd/EXAMPLE/INTERNET_SITE/vsftpd.xinetd
/usr/share/doc/vsftpd/EXAMPLE/INTERNET_SITE_NOINETD/README
/usr/share/doc/vsftpd/EXAMPLE/INTERNET_SITE_NOINETD/vsftpd.conf
/usr/share/doc/vsftpd/EXAMPLE/PER_IP_CONFIG/README
/usr/share/doc/vsftpd/EXAMPLE/PER_IP_CONFIG/hosts.allow
/usr/share/doc/vsftpd/EXAMPLE/README
/usr/share/doc/vsftpd/EXAMPLE/VIRTUAL_HOSTS/README
/usr/share/doc/vsftpd/EXAMPLE/VIRTUAL_USERS/README
/usr/share/doc/vsftpd/EXAMPLE/VIRTUAL_USERS/logins.txt
/usr/share/doc/vsftpd/EXAMPLE/VIRTUAL_USERS/vsftpd.conf
/usr/share/doc/vsftpd/EXAMPLE/VIRTUAL_USERS/vsftpd.pam
/usr/share/doc/vsftpd/EXAMPLE/VIRTUAL_USERS_2/README
/usr/share/doc/vsftpd/FAQ
/usr/share/doc/vsftpd/INSTALL
/usr/share/doc/vsftpd/LICENSE
/usr/share/doc/vsftpd/README
/usr/share/doc/vsftpd/README.security
/usr/share/doc/vsftpd/REWARD
/usr/share/doc/vsftpd/SECURITY/DESIGN
/usr/share/doc/vsftpd/SECURITY/IMPLEMENTATION
/usr/share/doc/vsftpd/SECURITY/OVERVIEW
/usr/share/doc/vsftpd/SECURITY/TRUST
/usr/share/doc/vsftpd/SIZE
/usr/share/doc/vsftpd/SPEED
/usr/share/doc/vsftpd/TODO
/usr/share/doc/vsftpd/TUNING
/usr/share/doc/vsftpd/vsftpd.xinetd
/usr/share/man/man5/vsftpd.conf.5.gz
/usr/share/man/man8/vsftpd.8.gz

```

### 查看指定文件所属的软件包：`rpm -qf`
> 只要是原软件包安装出来的文件，均可反向查询到所属安装包，比如：二进制文件、库文件、配置文件、帮助文档等目录或文件均可反向找到所属软件包
```bash
root@dev:~# rpm -qa | grep vsftpd
vsftpd-3.0.3-36.el8.x86_64
root@dev:~# rpm -qf /usr/share/doc/vsftpd/SIZE
vsftpd-3.0.3-36.el8.x86_64
root@dev:~# rpm -qf /var/ftp
filesystem-3.8-6.el8.x86_64
vsftpd-3.0.3-36.el8.x86_64
root@dev:~# rpm -qf /etc/vsftpd/vsftpd.conf
vsftpd-3.0.3-36.el8.x86_64
root@dev:~# rpm -qf /usr/lib/systemd/system/vsftpd.service
vsftpd-3.0.3-36.el8.x86_64
root@dev:~# rpm -qf /usr/sbin/vsftpd
vsftpd-3.0.3-36.el8.x86_64
root@dev:~# rpm -qf /etc/vsftpd
vsftpd-3.0.3-36.el8.x86_64


```



## 制作`rpm`包

### 安装`rpmdevtools`
```bash
yum -y install rpmbuild
yum -y install rpmdevtools
yum -y install rpm-build
```

```bash
rpmdev-setuptree
```

- 带源码的rpm包制作：rpmbuild -bs ~/rpmbuild/SPECS/{项目的spec文件}
- 二进制rpm包制作：rpmbuild -bb ~/rpmbuild/SPECS/{项目的spec文件}

查询一个rpm安装包的信息
通过rpm命令可以获取到安装包包含的安装文件和默认的安装路径
```bash
rpm -qpl myproject-5.4.2-1.el7.noarch.rpm
```

查询系统已安装软件
在一个新系统中，查询已安装的rpm软件包，可以使用如下命令行进行查询。
```bash
rpm -qa | grep {关键字}
```

```bash
rpmbuild -bs ~/rpmbuild/SPECS/{项目的spec文件}

rpmbuild -bb ~/rpmbuild/SPECS/{项目的spec文件}
```

### 撰写Spec文件





### 解压`rmp`包：`rpm2cpio`

```bash
rpm2cpio xxx.rpm | cpio -div
```





```bash

[root@dev ~]# dnf history info 12
Transaction ID : 12
Begin time     : Mon 27 Mar 2023 02:17:03 PM CST
Begin rpmdb    : 393:9217b737c3339d409e660f5bfd4f07f39fe01906
End time       : Mon 27 Mar 2023 02:17:04 PM CST (1 seconds)
End rpmdb      : 394:e9c183f94891c4b79d3a659c35ab22ef2339a4ac
User           : root <root>
Return-Code    : Success
Releasever     : 8
Command Line   : install -y gdisk
Comment        :
Packages Altered:
    Install gdisk-1.0.3-11.el8.x86_64 @base

```



### repolist

- `yum repolist all | grep mysql`

- `yum repolist enabled | grep mysql`







### search

- `yum search libaio`





### install
```bash
yum -y search libaio

yum -y install package-${version}
```




### list

{{<alert>}}

- `yum list` 列出所有可安装的软件包
- `yum list installed` 列出所有已安装的软件包
- `yum list updates` 列出所有可更新的软件包
- `yum list extras` 列出所有已安装但不在 `Yum Repository` 内的软件包



{{</alert>}}

1. `yum list`
```bash
root@dev:~# yum list
zsh.x86_64                                                                                               5.5.1-10.el8                                                                                              base
zsh-html.noarch                                                                                          5.5.1-10.el8                                                                                              AppStream
zsh-syntax-highlighting.noarch                                                                           0.7.1-1.el8                                                                                               epel
zvbi.x86_64                                                                                              0.2.35-9.el8                                                                                              epel
zvbi-devel.x86_64                                                                                        0.2.35-9.el8                                                                                              epel
zvbi-fonts.noarch                                                                                        0.2.35-9.el8                                                                                              epel
zxing-cpp.x86_64                                                                                         1.2.0-1.el8                                                                                               epel
zxing-cpp-devel.x86_64                                                                                   1.2.0-1.el8                                                                                               epel
zziplib.i686                                                                                             0.13.68-9.el8                                                                                             AppStream
zziplib.x86_64                                                                                           0.13.68-9.el8                                                                                             AppStream
zziplib-utils.x86_64                                                                                     0.13.68-9.el8                                                                                             AppStream


```

2. `yum list updates`

```bash
root@dev:~# yum list updates
Last metadata expiration check: 1:02:28 ago on Wed 06 Mar 2024 03:32:27 PM CST.
Available Upgrades
NetworkManager.x86_64                                                                                          1:1.40.16-14.el8                                                                                              base
NetworkManager-libnm.x86_64                                                                                    1:1.40.16-14.el8                                                                                              base
NetworkManager-team.x86_64                                                                                     1:1.40.16-14.el8                                                                                              base
NetworkManager-tui.x86_64                                                                                      1:1.40.16-14.el8                                                                                              base
acl.x86_64                                                                                                     2.2.53-3.el8                                                                                                  base
alsa-lib.x86_64                                                                                                1.2.10-2.el8                                                                                                  AppStream
ansible-core.x86_64
```

3. `yum list installed`
```bash
root@dev:~# yum list installed
Installed Packages
NetworkManager.x86_64                                                                                         1:1.32.10-4.el8                                                                                               @anaconda
NetworkManager-libnm.x86_64                                                                                   1:1.32.10-4.el8                                                                                               @anaconda
NetworkManager-team.x86_64                                                                                    1:1.32.10-4.el8                                                                                               @anaconda
NetworkManager-tui.x86_64                                                                                     1:1.32.10-4.el8                                                                                               @anaconda
acl.x86_64                                                                                                    2.2.53-1.el8                                                                                                  @anaconda
acpid.x86_64                                                                                                  2.0.30-2.el8                                                                                                  @appstream
alsa-lib.x86_64
```

4. `yum list extras`
```bash
root@dev:~# yum list extras
Last metadata expiration check: 1:04:28 ago on Wed 06 Mar 2024 03:32:27 PM CST.
Extra Packages
centos-linux-release.noarch                                                                                            8.5-1.2111.el8                                                                                             @anaconda
centos-linux-repos.noarch                                                                                              8-3.el8                                                                                                    @anaconda
rpcgen.x86_64                                                                                                          1.3.1-4.el8
```



```bash
root@dev:~/tmp/packages# yum list net-tools --showduplicates | sort -r
net-tools.x86_64                 2.0-0.52.20160912git.el8                  base
net-tools.x86_64                 2.0-0.52.20160912git.el8                  @base
Last metadata expiration check: 2:42:31 ago on Wed 06 Mar 2024 08:39:17 AM CST.
Installed Packages
Available Packages

root@dev:~/tmp/packages# yum --showduplicates list net-tools
Last metadata expiration check: 2:44:18 ago on Wed 06 Mar 2024 08:39:17 AM CST.
Installed Packages
net-tools.x86_64                                                                                                2.0-0.52.20160912git.el8                                                                                                @base
Available Packages
net-tools.x86_64                                                                                                2.0-0.52.20160912git.el8                                                                                                base

```


### info


1. `yum info installed`
```bash
root@dev:~/tmp/mysql-8026# yum info installed
Name         : zlib
Version      : 1.2.11
Release      : 25.el8
Architecture : x86_64
Size         : 195 k
Source       : zlib-1.2.11-25.el8.src.rpm
Repository   : @System
From repo    : base
Summary      : The compression and decompression library
URL          : http://www.zlib.net/
License      : zlib and Boost
Description  : Zlib is a general-purpose, patent-free, lossless data compression
             : library which is used by many different programs.

Name         : zlib-devel
Version      : 1.2.11
Release      : 25.el8
Architecture : x86_64
Size         : 138 k
Source       : zlib-1.2.11-25.el8.src.rpm
Repository   : @System
From repo    : base
Summary      : Header files and libraries for Zlib development
URL          : http://www.zlib.net/
License      : zlib and Boost
Description  : The zlib-devel package contains the header files and libraries needed
             : to develop programs that use the zlib compression and decompression
             : library.

```

2. `yum info extras`
```bash
root@dev:~/tmp/mysql-8026# yum info extras
Last metadata expiration check: 0:57:40 ago on Wed 06 Mar 2024 03:32:27 PM CST.
Extra Packages
Name         : centos-linux-release
Version      : 8.5
Release      : 1.2111.el8
Architecture : noarch
Size         : 26 k
Source       : centos-linux-release-8.5-1.2111.el8.src.rpm
Repository   : @System
From repo    : anaconda
Summary      : CentOS Linux release files
URL          : https://centos.org
License      : GPLv2
Description  : CentOS Linux release files.

Name         : centos-linux-repos
Version      : 8
Release      : 3.el8
Architecture : noarch
Size         : 26 k
Source       : centos-repos-8-3.el8.src.rpm
Repository   : @System
From repo    : anaconda
Summary      : CentOS Linux package repositories
URL          : https://centos.org
License      : GPLv2
Description  : This package provides the package repository files for CentOS Linux.

Name         : rpcgen
Version      : 1.3.1
Release      : 4.el8
Architecture : x86_64
Size         : 102 k
Source       : rpcsvc-proto-1.3.1-4.el8.src.rpm
Repository   : @System
From repo    : PowerTools
Summary      : RPC protocol compiler
URL          : https://github.com/thkukuk/rpcsvc-proto
License      : BSD and LGPLv2+
Description  : rpcgen is a tool that generates C code to implement an RPC protocol.
             : The input to rpcgen is a language similar to C known as RPC Language
             : (Remote Procedure Call Language).

```

3. `yum info updates`

```bash
root@dev:~/tmp/mysql-8026# yum info updates
Name         : xz-libs
Version      : 5.2.4
Release      : 4.el8
Architecture : x86_64
Size         : 94 k
Source       : xz-5.2.4-4.el8.src.rpm
Repository   : base
Summary      : Libraries for decoding LZMA compression
URL          : http://tukaani.org/xz/
License      : Public Domain
Description  : Libraries for decoding files compressed with LZMA or XZ utils.

Name         : yum
Version      : 4.7.0
Release      : 20.el8
Architecture : noarch
Size         : 209 k
Source       : dnf-4.7.0-20.el8.src.rpm
Repository   : base
Summary      : Package manager
URL          : https://github.com/rpm-software-management/dnf
License      : GPLv2+
Description  : Utility that allows users to manage packages on their systems.
             : It supports RPMs, modules and comps groups & environments.

```





### deplist

```bash
root@dev:~/tmp/packages# yum deplist net-tools
Last metadata expiration check: 2:39:26 ago on Wed 06 Mar 2024 08:39:17 AM CST.
package: net-tools-2.0-0.52.20160912git.el8.x86_64
  dependency: /bin/sh
   provider: bash-4.4.20-4.el8.x86_64
  dependency: libc.so.6(GLIBC_2.14)(64bit)
   provider: glibc-2.28-251.el8.x86_64
  dependency: libselinux.so.1()(64bit)
   provider: libselinux-2.9-8.el8.x86_64
  dependency: rtld(GNU_HASH)
   provider: glibc-2.28-251.el8.i686
   provider: glibc-2.28-251.el8.x86_64
  dependency: systemd
   provider: systemd-239-79.el8.i686
   provider: systemd-239-79.el8.x86_64

```

```bash
root@dev:~/tmp/packages# yum deplist samba-4.18.6
Last metadata expiration check: 2:48:56 ago on Wed 06 Mar 2024 08:39:17 AM CST.
package: samba-4.18.6-1.el8.x86_64
  dependency: /bin/sh
   provider: bash-4.4.20-4.el8.x86_64
  dependency: /usr/sbin/groupadd
   provider: shadow-utils-2:4.6-22.el8.x86_64
  dependency: libCHARSET3-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libCHARSET3-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libREG-FULL-samba4.so()(64bit)
   provider: samba-libs-4.19.4-3.el8.x86_64
  dependency: libREG-FULL-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-libs-4.18.6-1.el8.x86_64
  dependency: libRPC-SERVER-LOOP-samba4.so()(64bit)
   provider: samba-libs-4.19.4-3.el8.x86_64
  dependency: libRPC-SERVER-LOOP-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-libs-4.18.6-1.el8.x86_64
  dependency: libads-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libads-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libauth-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libauth-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libc.so.6(GLIBC_2.15)(64bit)
   provider: glibc-2.28-251.el8.x86_64
  dependency: libcli-cldap-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libcli-cldap-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libcli-nbt-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libcli-nbt-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libcli-smb-common-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libcli-smb-common-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libcli-spoolss-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libcli-spoolss-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libcliauth-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libcliauth-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libcmdline-contexts-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libcmdline-contexts-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libcmdline-samba4.so()(64bit)
   provider: samba-common-libs-4.19.4-3.el8.x86_64
  dependency: libcmdline-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-common-libs-4.18.6-1.el8.x86_64
  dependency: libcommon-auth-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libcommon-auth-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libcups.so.2()(64bit)
   provider: cups-libs-1:2.2.6-56.el8.x86_64
  dependency: libdbus-1.so.3()(64bit)
   provider: dbus-libs-1:1.12.8-26.el8.x86_64
  dependency: libdbus-1.so.3(LIBDBUS_1_3)(64bit)
   provider: dbus-libs-1:1.12.8-26.el8.x86_64
  dependency: libdbwrap-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libdbwrap-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libdcerpc-binding.so.0()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libdcerpc-binding.so.0(DCERPC_BINDING_0.0.1)(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libdcerpc-samba-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libdcerpc-samba-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libdcerpc-server-core.so.0()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libdcerpc-server-core.so.0(DCERPC_SERVER_CORE_0.0.1)(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libgenrand-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libgenrand-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libgnutls.so.30()(64bit)
   provider: gnutls-3.6.16-8.el8.1.x86_64
  dependency: libgnutls.so.30(GNUTLS_3_4)(64bit)
   provider: gnutls-3.6.16-8.el8.1.x86_64
  dependency: libgnutls.so.30(GNUTLS_3_6_3)(64bit)
   provider: gnutls-3.6.16-8.el8.1.x86_64
  dependency: libgse-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libgse-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libldap-2.4.so.2()(64bit)
   provider: openldap-2.4.46-18.el8.x86_64
  dependency: liblibsmb-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: liblibsmb-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libmessages-dgm-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libmessages-dgm-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libmsghdr-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libmsghdr-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libmsrpc3-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libmsrpc3-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libndr-nbt.so.0()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libndr-nbt.so.0(NDR_NBT_0.0.1)(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libndr-samba-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libndr-samba-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libndr-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libndr-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libndr-standard.so.0()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libndr-standard.so.0(NDR_STANDARD_0.0.1)(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libndr.so.3()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libndr.so.3(NDR_0.0.1)(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libndr.so.3(NDR_0.0.6)(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libndr.so.3(NDR_0.2.0)(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libndr.so.3(NDR_1.0.0)(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libndr.so.3(NDR_2.0.0)(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libnetapi = 4.18.6-1.el8
   provider: libnetapi-4.18.6-1.el8.i686
   provider: libnetapi-4.18.6-1.el8.x86_64
  dependency: libpopt.so.0()(64bit)
   provider: popt-1.18-1.el8.x86_64
  dependency: libpopt.so.0(LIBPOPT_0)(64bit)
   provider: popt-1.18-1.el8.x86_64
  dependency: libprinting-migrate-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libprinting-migrate-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libpthread.so.0()(64bit)
   provider: glibc-2.28-251.el8.x86_64
  dependency: libpthread.so.0(GLIBC_2.2.5)(64bit)
   provider: glibc-2.28-251.el8.x86_64
  dependency: libreplace-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libreplace-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libsamba-cluster-support-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libsamba-cluster-support-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libsamba-credentials.so.1()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libsamba-credentials.so.1(SAMBA_CREDENTIALS_1.0.0)(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libsamba-debug-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libsamba-debug-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libsamba-errors.so.1()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libsamba-errors.so.1(SAMBA_ERRORS_1.0.0)(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libsamba-hostconfig.so.0()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libsamba-hostconfig.so.0(SAMBA_HOSTCONFIG_0.0.1)(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libsamba-passdb.so.0()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libsamba-passdb.so.0(SAMBA_PASSDB_0.2.0)(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libsamba-security-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libsamba-security-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libsamba-sockets-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libsamba-sockets-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libsamba-util.so.0()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libsamba-util.so.0(SAMBA_UTIL_0.0.1)(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libsamba3-util-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libsamba3-util-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libsecrets3-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libsecrets3-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libsmbconf.so.0()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libsmbconf.so.0(SMBCONF_0.0.1)(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libsmbd-base-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libsmbd-base-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libsmbd-shim-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libsmbd-shim-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libsocket-blocking-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libsocket-blocking-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libsys-rw-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libsys-rw-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libtalloc.so.2()(64bit)
   provider: libtalloc-2.4.1-0.el8.x86_64
  dependency: libtalloc.so.2(TALLOC_2.0.2)(64bit)
   provider: libtalloc-2.4.1-0.el8.x86_64
  dependency: libtdb.so.1()(64bit)
   provider: libtdb-1.4.9-0.el8.x86_64
  dependency: libtdb.so.1(TDB_1.2.1)(64bit)
   provider: libtdb-1.4.9-0.el8.x86_64
  dependency: libtdb.so.1(TDB_1.2.2)(64bit)
   provider: libtdb-1.4.9-0.el8.x86_64
  dependency: libtdb.so.1(TDB_1.2.5)(64bit)
   provider: libtdb-1.4.9-0.el8.x86_64
  dependency: libtevent-util.so.0()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libtevent-util.so.0(TEVENT_UTIL_0.0.1)(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libtevent.so.0()(64bit)
   provider: libtevent-0.16.0-0.el8.x86_64
  dependency: libtevent.so.0(TEVENT_0.9.16)(64bit)
   provider: libtevent-0.16.0-0.el8.x86_64
  dependency: libtevent.so.0(TEVENT_0.9.21)(64bit)
   provider: libtevent-0.16.0-0.el8.x86_64
  dependency: libtevent.so.0(TEVENT_0.9.9)(64bit)
   provider: libtevent-0.16.0-0.el8.x86_64
  dependency: libtime-basic-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libtime-basic-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libutil-reg-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libutil-reg-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libutil-tdb-samba4.so()(64bit)
   provider: samba-client-libs-4.19.4-3.el8.x86_64
  dependency: libutil-tdb-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: libwbclient = 4.18.6-1.el8
   provider: libwbclient-4.18.6-1.el8.i686
   provider: libwbclient-4.18.6-1.el8.x86_64
  dependency: libxattr-tdb-samba4.so()(64bit)
   provider: samba-libs-4.19.4-3.el8.x86_64
  dependency: libxattr-tdb-samba4.so(SAMBA_4.18.6_SAMBA4)(64bit)
   provider: samba-libs-4.18.6-1.el8.x86_64
  dependency: pam
   provider: pam-1.3.1-32.el8.i686
   provider: pam-1.3.1-32.el8.x86_64
  dependency: rtld(GNU_HASH)
   provider: glibc-2.28-251.el8.i686
   provider: glibc-2.28-251.el8.x86_64
  dependency: samba-client-libs = 4.18.6-1.el8
   provider: samba-client-libs-4.18.6-1.el8.i686
   provider: samba-client-libs-4.18.6-1.el8.x86_64
  dependency: samba-common = 4.18.6-1.el8
   provider: samba-common-4.18.6-1.el8.noarch
  dependency: samba-common-libs = 4.18.6-1.el8
   provider: samba-common-libs-4.18.6-1.el8.i686
   provider: samba-common-libs-4.18.6-1.el8.x86_64
  dependency: samba-common-tools = 4.18.6-1.el8
   provider: samba-common-tools-4.18.6-1.el8.x86_64
  dependency: samba-dcerpc = 4.18.6-1.el8
   provider: samba-dcerpc-4.18.6-1.el8.x86_64
  dependency: samba-libs = 4.18.6-1.el8
   provider: samba-libs-4.18.6-1.el8.i686
   provider: samba-libs-4.18.6-1.el8.x86_64

```

这下就完全一页屏幕也显示不全了，可见依赖项目之多。也就是说，假设我们下载也单个samba的安装包，还必须把这些依赖解决才能正常把程序跑起来。但平常我们使用yum安装时，是无感的，其实由系统自动为我们解决了依赖，从网上下载安装了。

要解决离线状态下的软件安装问题，推荐使用以下几种方法：

`repotrack mysql-server`
```bash
root@dev:~/tmp/mysql-8026# ls
acl-2.2.53-3.el8.x86_64.rpm                                   grub2-tools-minimal-2.02-129.el8.x86_64.rpm        libuuid-2.32.1-43.el8.x86_64.rpm                               perl-podlators-4.11-1.el8.noarch.rpm
audit-libs-3.1.2-1.el8.x86_64.rpm                             grubby-8.40-48.el8.x86_64.rpm                      libverto-0.3.2-2.el8.x86_64.rpm                                perl-Pod-Perldoc-3.28-396.el8.noarch.rpm
basesystem-11-5.el8.noarch.rpm                                gzip-1.9-13.el8.x86_64.rpm                         libxcrypt-4.1.1-6.el8.x86_64.rpm                               perl-Pod-Simple-3.35-395.el8.noarch.rpm
bash-4.4.20-4.el8.x86_64.rpm                                  hardlink-1.3-6.el8.x86_64.rpm                      libxkbcommon-0.9.1-1.el8.x86_64.rpm                            perl-Pod-Usage-1.69-395.el8.noarch.rpm
brotli-1.0.6-3.el8.x86_64.rpm                                 info-6.5-7.el8_5.x86_64.rpm                        libxml2-2.9.7-18.el8.x86_64.rpm                                perl-Scalar-List-Utils-1.49-2.el8.x86_64.rpm
bzip2-libs-1.0.6-26.el8.x86_64.rpm                            json-c-0.13.1-3.el8.x86_64.rpm                     libzstd-1.4.4-1.el8.x86_64.rpm                                 perl-Socket-2.027-3.el8.x86_64.rpm
ca-certificates-2023.2.60_v7.0.306-80.0.el8.noarch.rpm        kbd-2.0.4-11.el8.x86_64.rpm                        lua-libs-5.3.4-12.el8.x86_64.rpm                               perl-Storable-3.11-3.el8.x86_64.rpm
centos-gpg-keys-8-6.el8.noarch.rpm                            kbd-legacy-2.0.4-11.el8.noarch.rpm                 lz4-libs-1.8.3-3.el8_4.x86_64.rpm                              perl-Term-ANSIColor-4.06-396.el8.noarch.rpm
centos-stream-release-8.6-1.el8.noarch.rpm                    kbd-misc-2.0.4-11.el8.noarch.rpm                   mariadb-connector-c-config-3.1.11-2.el8_3.noarch.rpm           perl-Term-Cap-1.17-395.el8.noarch.rpm
centos-stream-repos-8-6.el8.noarch.rpm                        keyutils-libs-1.5.10-9.el8.x86_64.rpm              mecab-0.996-1.module_el8.4.0+589+11e12751.9.x86_64.rpm         perl-Text-ParseWords-3.30-395.el8.noarch.rpm
checkpolicy-2.9-1.el8.x86_64.rpm                              kmod-25-20.el8.x86_64.rpm                          memstrack-0.2.5-2.el8.x86_64.rpm                               perl-Text-Tabs+Wrap-2013.0523-395.el8.noarch.rpm
chkconfig-1.19.2-1.el8.x86_64.rpm                             kmod-libs-25-20.el8.x86_64.rpm                     mpfr-3.1.6-1.el8.x86_64.rpm                                    perl-threads-2.21-2.el8.x86_64.rpm
coreutils-8.30-15.el8.x86_64.rpm                              kpartx-0.8.4-41.el8.x86_64.rpm                     mysql-8.0.26-1.module_el8.4.0+915+de215114.x86_64.rpm          perl-threads-shared-1.58-2.el8.x86_64.rpm
coreutils-common-8.30-15.el8.x86_64.rpm                       krb5-libs-1.18.2-26.el8.x86_64.rpm                 mysql-common-8.0.26-1.module_el8.4.0+915+de215114.x86_64.rpm   perl-Time-Local-1.280-1.el8.noarch.rpm
cpio-2.12-11.el8.x86_64.rpm                                   libacl-2.2.53-3.el8.x86_64.rpm                     mysql-errmsg-8.0.26-1.module_el8.4.0+915+de215114.x86_64.rpm   perl-Unicode-Normalize-1.25-396.el8.x86_64.rpm
cracklib-2.9.6-15.el8.x86_64.rpm                              libaio-0.3.112-1.el8.x86_64.rpm                    mysql-server-8.0.26-1.module_el8.4.0+915+de215114.x86_64.rpm   perl-URI-1.73-3.el8.noarch.rpm
cracklib-dicts-2.9.6-15.el8.x86_64.rpm                        libarchive-3.3.3-5.el8.x86_64.rpm                  ncurses-6.1-10.20180224.el8.x86_64.rpm                         pigz-2.4-4.el8.x86_64.rpm
crypto-policies-20230731-1.git3177e06.el8.noarch.rpm          libattr-2.4.48-3.el8.x86_64.rpm                    ncurses-base-6.1-10.20180224.el8.noarch.rpm                    platform-python-3.6.8-59.el8.x86_64.rpm
crypto-policies-scripts-20230731-1.git3177e06.el8.noarch.rpm  libblkid-2.32.1-43.el8.x86_64.rpm                  ncurses-libs-6.1-10.20180224.el8.x86_64.rpm                    platform-python-pip-9.0.3-24.el8.noarch.rpm
cryptsetup-libs-2.3.7-7.el8.x86_64.rpm                        libcap-2.48-5.el8.x86_64.rpm                       nettle-3.4.1-7.el8.x86_64.rpm                                  platform-python-setuptools-39.2.0-7.el8.noarch.rpm
curl-7.61.1-34.el8.x86_64.rpm                                 libcap-ng-0.7.11-1.el8.x86_64.rpm                  numactl-libs-2.0.16-4.el8.x86_64.rpm                           policycoreutils-2.9-24.el8.x86_64.rpm
cyrus-sasl-lib-2.1.27-6.el8_5.x86_64.rpm                      libcom_err-1.45.6-5.el8.x86_64.rpm                 openldap-2.4.46-18.el8.x86_64.rpm                              policycoreutils-python-utils-2.9-24.el8.noarch.rpm
dbus-1.12.8-26.el8.x86_64.rpm                                 libcroco-0.6.12-4.el8_2.1.x86_64.rpm               openssl-1.1.1k-12.el8.x86_64.rpm                               popt-1.18-1.el8.x86_64.rpm
dbus-common-1.12.8-26.el8.noarch.rpm                          libcurl-7.61.1-34.el8.x86_64.rpm                   openssl-libs-1.1.1k-12.el8.x86_64.rpm                          procps-ng-3.3.15-14.el8.x86_64.rpm
dbus-daemon-1.12.8-26.el8.x86_64.rpm                          libdb-5.3.28-42.el8_4.x86_64.rpm                   openssl-pkcs11-0.4.10-3.el8.x86_64.rpm                         protobuf-lite-3.5.0-15.el8.x86_64.rpm
dbus-libs-1.12.8-26.el8.x86_64.rpm                            libdb-utils-5.3.28-42.el8_4.x86_64.rpm             os-prober-1.74-9.el8.x86_64.rpm                                publicsuffix-list-dafsa-20180723-1.el8.noarch.rpm
dbus-tools-1.12.8-26.el8.x86_64.rpm                           libedit-3.1-23.20170329cvs.el8.x86_64.rpm          p11-kit-0.23.22-2.el8.x86_64.rpm                               python3-audit-3.1.2-1.el8.x86_64.rpm
device-mapper-1.02.181-9.el8.x86_64.rpm                       libevent-2.1.8-5.el8.x86_64.rpm                    p11-kit-trust-0.23.22-2.el8.x86_64.rpm                         python3-libs-3.6.8-59.el8.x86_64.rpm
device-mapper-libs-1.02.181-9.el8.x86_64.rpm                  libfdisk-2.32.1-43.el8.x86_64.rpm                  pam-1.3.1-32.el8.x86_64.rpm                                    python3-libselinux-2.9-8.el8.x86_64.rpm
diffutils-3.6-6.el8.x86_64.rpm                                libffi-3.1-24.el8.x86_64.rpm                       pcre2-10.32-3.el8.x86_64.rpm                                   python3-libsemanage-2.9-9.el8.x86_64.rpm
dracut-049-233.git20240115.el8.x86_64.rpm                     libgcc-8.5.0-21.el8.x86_64.rpm                     pcre-8.42-6.el8.x86_64.rpm                                     python3-pip-wheel-9.0.3-24.el8.noarch.rpm
elfutils-debuginfod-client-0.190-2.el8.x86_64.rpm             libgcrypt-1.8.5-7.el8.x86_64.rpm                   perl-Carp-1.42-396.el8.noarch.rpm                              python3-policycoreutils-2.9-24.el8.noarch.rpm
elfutils-default-yama-scope-0.190-2.el8.noarch.rpm            libgomp-8.5.0-21.el8.x86_64.rpm                    perl-constant-1.33-396.el8.noarch.rpm                          python3-setools-4.3.0-5.el8.x86_64.rpm
elfutils-libelf-0.190-2.el8.x86_64.rpm                        libgpg-error-1.31-1.el8.x86_64.rpm                 perl-Data-Dumper-2.167-399.el8.x86_64.rpm                      python3-setuptools-wheel-39.2.0-7.el8.noarch.rpm
elfutils-libs-0.190-2.el8.x86_64.rpm                          libidn2-2.2.0-1.el8.x86_64.rpm                     perl-Digest-1.17-395.el8.noarch.rpm                            readline-7.0-10.el8.x86_64.rpm
expat-2.2.5-11.el8.x86_64.rpm                                 libkcapi-1.4.0-2.el8.x86_64.rpm                    perl-Digest-MD5-2.55-396.el8.x86_64.rpm                        rpm-4.14.3-31.el8.x86_64.rpm
file-5.33-25.el8.x86_64.rpm                                   libkcapi-hmaccalc-1.4.0-2.el8.x86_64.rpm           perl-Encode-2.97-3.el8.x86_64.rpm                              rpm-libs-4.14.3-31.el8.x86_64.rpm
file-libs-5.33-25.el8.x86_64.rpm                              libmount-2.32.1-43.el8.x86_64.rpm                  perl-Errno-1.28-422.el8.x86_64.rpm                             sed-4.5-5.el8.x86_64.rpm
filesystem-3.8-6.el8.x86_64.rpm                               libnghttp2-1.33.0-5.el8.x86_64.rpm                 perl-Exporter-5.72-396.el8.noarch.rpm                          setup-2.12.2-11.el8.noarch.rpm
findutils-4.6.0-22.el8.x86_64.rpm                             libnsl2-1.2.0-2.20180605git4a062cf.el8.x86_64.rpm  perl-File-Path-2.15-2.el8.noarch.rpm                           shadow-utils-4.6-22.el8.x86_64.rpm
gawk-4.2.1-4.el8.x86_64.rpm                                   libpsl-0.20.2-6.el8.x86_64.rpm                     perl-File-Temp-0.230.600-1.el8.noarch.rpm                      shared-mime-info-1.9-4.el8.x86_64.rpm
gdbm-1.18-2.el8.x86_64.rpm                                    libpwquality-1.4.4-6.el8.x86_64.rpm                perl-Getopt-Long-2.50-4.el8.noarch.rpm                         sqlite-libs-3.26.0-19.el8.x86_64.rpm
gdbm-libs-1.18-2.el8.x86_64.rpm                               libseccomp-2.5.2-1.el8.x86_64.rpm                  perl-HTTP-Tiny-0.074-3.el8.noarch.rpm                          systemd-239-79.el8.x86_64.rpm
gettext-0.19.8.1-17.el8.x86_64.rpm                            libselinux-2.9-8.el8.x86_64.rpm                    perl-interpreter-5.26.3-422.el8.x86_64.rpm                     systemd-libs-239-79.el8.x86_64.rpm
gettext-libs-0.19.8.1-17.el8.x86_64.rpm                       libselinux-utils-2.9-8.el8.x86_64.rpm              perl-IO-1.38-422.el8.x86_64.rpm                                systemd-pam-239-79.el8.x86_64.rpm
glib2-2.56.4-163.el8.x86_64.rpm                               libsemanage-2.9-9.el8.x86_64.rpm                   perl-IO-Socket-IP-0.39-5.el8.noarch.rpm                        systemd-udev-239-79.el8.x86_64.rpm
glibc-2.28-251.el8.x86_64.rpm                                 libsepol-2.9-3.el8.x86_64.rpm                      perl-IO-Socket-SSL-2.066-4.module_el8+339+1ec643e0.noarch.rpm  trousers-0.3.15-2.el8.x86_64.rpm
glibc-all-langpacks-2.28-251.el8.x86_64.rpm                   libsigsegv-2.11-5.el8.x86_64.rpm                   perl-libnet-3.11-3.el8.noarch.rpm                              trousers-lib-0.3.15-2.el8.x86_64.rpm
glibc-common-2.28-251.el8.x86_64.rpm                          libsmartcols-2.32.1-43.el8.x86_64.rpm              perl-libs-5.26.3-422.el8.x86_64.rpm                            tzdata-2024a-1.el8.noarch.rpm
glibc-gconv-extra-2.28-251.el8.x86_64.rpm                     libssh-0.9.6-6.el8.x86_64.rpm                      perl-macros-5.26.3-422.el8.x86_64.rpm                          util-linux-2.32.1-43.el8.x86_64.rpm
gmp-6.1.2-12.el8.x86_64.rpm                                   libssh-config-0.9.6-6.el8.noarch.rpm               perl-MIME-Base64-3.15-396.el8.x86_64.rpm                       which-2.21-20.el8.x86_64.rpm
gnutls-3.6.16-8.el8.1.x86_64.rpm                              libstdc++-8.5.0-21.el8.x86_64.rpm                  perl-Mozilla-CA-20160104-7.module_el8+645+9d809f8c.noarch.rpm  xkeyboard-config-2.28-1.el8.noarch.rpm
grep-3.1-6.el8.x86_64.rpm                                     libtasn1-4.13-4.el8.x86_64.rpm                     perl-Net-SSLeay-1.88-2.module_el8+339+1ec643e0.x86_64.rpm      xz-5.2.4-4.el8.x86_64.rpm
groff-base-1.22.3-18.el8.x86_64.rpm                           libtirpc-1.1.4-8.el8.x86_64.rpm                    perl-parent-0.237-1.el8.noarch.rpm                             xz-libs-5.2.4-4.el8.x86_64.rpm
grub2-common-2.02-129.el8.noarch.rpm                          libunistring-0.9.9-3.el8.x86_64.rpm                perl-PathTools-3.74-1.el8.x86_64.rpm                           zlib-1.2.11-25.el8.x86_64.rpm
grub2-tools-2.02-129.el8.x86_64.rpm                           libutempter-1.1.6-14.el8.x86_64.rpm                perl-Pod-Escapes-1.07-395.el8.noarch.rpm

```









