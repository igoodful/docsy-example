---
title: 离线安装
description: 离线安装
date: 2023-11-01
weight: 30000


---

{{< alert >}}


{{< /alert >}}


{{<alert>}}
**前提**：

通常生产环境由于安全原因都无法访问互联网。此时就需要进行离线安装，主要有两种方式：源码编译、`rpm`包安装。源码编译耗费时间长且缺乏编译环境，所以一般都选择使用离线 `rpm` 包安装。

必须先下载`yum -utils`，才可以使用这两个命令。

```bash
yum -y install yum-utils

yumdownloader --resolve --destdir=/tmp/ansible  ansible
```
- \-\-resolve 下载依赖包
- \-\-destdir 指定下载目录

```bash
yum -y install yum-utils

repotrack ansible
```

**区别**：

- `yumdownloader`解决了`ansible`的依赖包，但是无法解决依赖包的依赖包；

- `repotrack`不仅解决了`ansible`的依赖包，还解决了依赖包和依赖包之间的关系；

{{</alert>}}




## 一、查看`rpm`包依赖
```bash
yum deplist ansible

yum deplist nginx


```



## 二、全量下载：`repotrack`
> 推荐

```bash

# 安装yum-utils
yum -y install yum-utils

# 下载全量依赖包
repotrack mysql-server
```
执行这条命令后，可以发现系统将`mysql-server`所有的依赖项目全部都下载下来了，如果目标服务器再缺少依赖，再从里面找包，岂不是轻轻松松。

```bash
yum list installed |grep sqlite-libs
```


## 三、部分下载：`yumdownloader`
> 与repotrack不同的是，它并不是全量下载，而是根据你机器的环境来决定的，比如你电脑已经安装了大多数依赖项目，那么下载下来的软件包数量就少，反之就更多。
>
> 只下载系统缺少的依赖包，仅会将主软件包和基于你现在的操作系统所缺少的依赖关系包一并下载。

```bash
yum -y install yum-utils

yumdownloader --downloadonly --resolve  --destdir=/tmp/ansible  ansible

yumdownloader --downloadonly --resolve --destdir=/tmp/mysql mysql-server
```


## 四、部分下载：`yum --downloadonly`
> 只下载系统缺少的依赖包，仅会将主软件包和基于你现在的操作系统所缺少的依赖关系包一并下载。与`yumdownloader`相同
```bash
yum -y install yum-download

yum -y install ansible --downloadonly --downloaddir=/tmp/ansible
```

## 五、离线安装：`rpm -Uvh`


1. 离线安装 `rpm`
```bash
rpm -Uvh --force --nodeps *.rpm
```

2. 安装当前目录所有包
```bash
yum localinstall *
```





