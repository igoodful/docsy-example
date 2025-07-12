---
title: 01. 安装
description: install
date: 2023-10-12
weight: 100


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}


- [ansible-core与python](https://docs.ansible.com/ansible/latest/reference_appendices/release_and_maintenance.html#support-life)

{{< /alert >}}


## `ansible`可执行程序
```viz-dot
digraph "ansible" {
        rankdir=TB;
node [
        fontsize = "12";
];
    "exe" [ label = "executable", shape = octagon ];
    "ansible" [ label = "ansible", shape = octagon ];
    "ansible-playbook" [ label = "ansible-playbook", shape = septagon ];
    "ansible-galaxy" [ label = "ansible-galaxy", shape = septagon ];
    "ansible-vault" [ label = "ansible-vault", shape = septagon ];
    "ansible-doc" [ label = "ansible-doc", shape = septagon ];
    "ansible-pull" [ label = "ansible-pull", shape = septagon ];
    "ansible-console" [ label = "ansible-console", shape = septagon ];

    "exe" -> "ansible" ;
    "exe" -> "ansible-playbook" ;
    "exe" -> "ansible-galaxy" ;
    "exe" -> "ansible-vault" ;
    "exe" -> "ansible-doc" ;
    "exe" -> "ansible-pull" ;
    "exe" -> "ansible-console" ;


}
```




{{< alert color="secondary" >}}
### ansible配置文件查找顺序v:

1. ANSIBLE_CONFIG 检查环境变量ANSIBLE_CONFIG指向的路径文件(export ANSIBLE_CONFIG=/etc/ansible.cfg)

2. ~/.ansible.cfg 检查当前目录下的ansible.cfg配置文

3. /etc/ansible.cfg 检查etc目录的配置文件。

### yum安装后的目录结构：

1. 配置文件目录：/etc/ansible/

2. 执行文件目录：/usr/bin/

3. Lib库依赖目录：/usr/lib/pythonX.X/site-packages/ansible/

4. Help文档目录：/usr/share/doc/ansible-X.X.X/

5. Man文档目录：/usr/share/man/man1/


{{< /alert >}}


### 配置 epel

```bash
cat >>/etc/yum.repos.d/epel.repo<<'EOF'
[epel]
name=epel repo
baseurl=https://mirrors.tuna.tsinghua.edu.cn/epel/7/$basearch
enabled=1
gpgcheck=0
EOF
```

### ansible

```bash
yum -y  install epel-release
yum deplist ansible

yum -y install ansible

pip3 install ansible==2.9.1
```
从Ansible 2.9版本开始，它支持命令的选项补全功能，它依赖于python的argcomplete插件。

### 安装argcomplete：
```bash
# CentOS/RHEL
yum -y install python-argcomplete

# 任何系统都可以使用pip工具安装argcomplete
pip3 install argcomplete
```

### sshpass
```bash
yum -y install sshpass
```


```bash
yumdownloader --resolve --destdir=/root/tmp ansible
```

### yum localinstall
它的作用类似rpm -ivh xx.rpm，用于安装本地的rpm包，但rpm命令不能解决依赖，使用yum在安装本地rpm包的时候，可以解决依赖关系（会使用上yum源）。
当不能使用网络yum源的时候，可以先将下载包下载到本地，单独进行安装。
```bash
[root@node83 ansible_install]# yum -y remove ansible

[root@node83 ansible_install]# yum install --downloadonly --downloaddir=/root/tmp/ ansible

[root@node83 ansible_install]# ls
ansible-2.8.8-1.ky10.noarch.rpm              python3-babel-2.8.0-1.ky10.noarch.rpm    python3-cryptography-2.9-1.ky10.aarch64.rpm  python3-paramiko-2.4.1-7.ky10.noarch.rpm  python3-pynacl-1.2.1-4.ky10.aarch64.rpm
libsodium-1.0.18-1.el7.aarch64.rpm           python3-bcrypt-3.1.4-7.ky10.aarch64.rpm  python3-jinja2-2.10-10.1.ky10.noarch.rpm     python3-pyasn1-0.3.7-8.ky10.noarch.rpm    python3-pyyaml-5.3.1-1.ky10.aarch64.rpm
python3-asn1crypto-0.24.0-8.ky10.noarch.rpm  python3-cffi-1.11.5-10.ky10.aarch64.rpm  python3-markupsafe-1.0-3.ky10.aarch64.rpm    python3-pycparser-2.19-1.ky10.noarch.rpm
python3-ply-3.9-9.ky10.noarch.rpm   sshpass-1.06-8.ky10.aarch64.rpm

[root@node83 ansible_install]# yum localinstall *.rpm
```



### `ansible`配置文件查找顺序

1. 环境变量：`ANSIBLE_CONFIG`
2. 当前目录：./
3. 家目录：~/
4. /etc/ansible/





生成一个`ansible`配置文件
```bash
ansible --version
```





## 1. 离线安装
[离线安装](https://www.iblog.zone/archives/yum%E4%B8%8B%E8%BD%BD%E5%85%A8%E9%87%8Frpm%E4%BE%9D%E8%B5%96%E5%8C%85%E5%8F%8A%E7%A6%BB%E7%BA%BF%E5%AE%89%E8%A3%85/)

离线安装ansible （root）
在客户现场的环境中，最多只能配置与OS版本一致的yum源，很多时候并没有外网，所以在引入额外工具的时候，需要考虑在内网环境如何方便的进行安装。

大致思路是：

- 离线环境中安装setuptools和pip
- 在在线环境通过pip将paramiko的依赖下载到一个文件夹里
- 在离线环境中，通过pip访问该文件夹来解决依赖问题，顺利安装。
- 首先，离线环境中，需要安装setuptools和pip：(root)

`unzip setuptools-39.1.0.zip`
`cd setuptools-39.1.0 && $SUDO python setup.py install `
`cd ..`
`tar xzvf pip-10.0.1.tar.gz`
`cd pip-10.0.1 && $SUDO python setup.py install `
`cd ..`
在在线环境中，下载ansible的依赖：

`mkdir -p ~/ansible_soft`
`pip download -d ~/ansible_soft ansible`
下载目录打包：

`tar czvf ansible_soft.tar.gz ansible_soft`
上传到离线环境解压，通过pip 安装：(root)

`pip install --no-index --ignore-installed  --find-links=ansible_soft ansible`
验证是否安装成功：

`ansible --version`



## yumdownloader
> 仅会将主软件包和基于你现在的操作系统所缺少的依赖关系包一并下载。
>
> `yumdownloader --resolve --destdir=/tmp ansible`
参数说明：

- \-\-destdir：指定 rpm 包下载目录（不指定时，默认为当前目录）
- \-\-resolve：下载依赖的 rpm 包。

1、准备一台能连接互联网的相同OS服务器，使用yumdownloader工具下载ansible安装包以及所有依赖包。
以 root 身份安装Yumdownloader工具：
> 默认源里没有ansible，需要安装fedora epel源
```bash
yum -y install yum-utils epel-release

mkdir /root/packages
yumdownloader --resolve --destdir /root/packages/ ansible
yumdownloader --resolve --destdir /root/packages/ createrepo

# 找一台能连接互联网并且与内网服务器系统一致的服务器，下载ansible安装包以及所有依赖包
yum -y install --downloadonly --downloaddir=/root/ansible ansible

# 打包所有下载的rpm包
cd  /root
tar -czvf ansible.tar.gz ./ansible

# 远程安装
tar -xzvf ansible.tar.gz
cd ./ansible
rpm -ivh *.rpm
ansible --version
```

### 远程安装
> 这个命令会依次安装所有需要的依赖包。
```bash
rpm -ivh libyaml-0.1.4-11.el7_0.x86_64.rpm \

python-crypto-2.6.1-1.el7.x86_64.rpm \

python2-pyasn1-0.1.9-7.el7.noarch.rpm \

python-httplib2-0.9.2-1.el7.noarch.rpm \

python-jinja2-2.7.2-4.el7.noarch.rpm \

PyYAML-3.10-11.el7.x86_64.rpm \

python-babel-0.9.6-8.el7.noarch.rpm \

python-markupsafe-0.11-10.el7.x86_64.rpm \

python-six-1.9.0-2.el7.noarch.rpm \

python2-paramiko-2.0.0-1.el7.noarch.rpm
```

`pm -ivh ansible-2.9.6-1.el7.noarch.rpm`




## downloadonly
> 与 yumdownloader 命令一样，也是仅会将主软件包和基于你现在的操作系统所缺少的依赖关系包一并下载。
```bash
yum -y install yum-download

# 下载 ansible 依赖包
yum -y install ansible --downloadonly --downloaddir=/tmp
```


下载ansible和所有依赖包


2、将上一步下载的所有rpm安装包打包，传输到企业环境的服务器上，解压到/packages文件夹里。
首先安装createrepo：
cd /packages
因为createrepo依赖 deltarpm和python-deltarpm 这两个包，需要先安装这两个包
rpm -ivh deltarpm-xxxx.x86_64.rpm
rpm -ivh python-deltarpm-xxxx.x86_64.rpm
再安装createrepo：
rpm -ivh createrepo-xxxx.noarch.rpm
然后使用createrepo生成符合要求的yum仓库，执行：
createrepo /packages

3、配置本地yum源
vi /etc/yum.repos.d/yum.repo
[test]
name=myyum
baseurl=file:///packages
gpgcheck=0
enabled=1
保存退出，然后执行：
yum clean all
yum repolist

4、使用yum安装ansible
yum install ansible
验证安装成功：
ansible --version
ansible xxx
5.ansible 设置并发和优化
配置文件vim /etc/ansible/ansible.cfg
配置文件设置为50，并发数为50
forks = 50





## CentOS Stream 8 离线安装
- [CentOS Stream 8 离线安装](https://www.cnblogs.com/yuedu/p/17034731.html)

首先在一个Online 的CentOS Stream 8 上把需要的安装包下载下来，然后再拷贝到离线的主机上进行安装。

下载工具推荐用 python pip 来下载，这样在安装的时候也用pip来安装就很简单了。这里唯一需要准备的是一个和离线环境相同的系统，可以通过docker来实现，就不展示了。


### 首先 ansible 4.2.0的安装依赖pip的版本比较高，CentOS Stream 8默认的pip版本是 pip 9.0.3 , 所以先下载pip的升级包


```bash
[root@localhost ~]# cat requirements_pip.txt
pip==21.3.1
# 下载pip==21.3.1
[root@localhost ~]# python3 -m pip download  -r requirements_pip.txt -d /root/packages/pip
# 先在Online的主机上升级pip，不然下载ansible4.2.0也会报错。
[root@localhost ~]# python3 -m pip install --no-index --find-links /root/packages/pip -r requirements_pip.txt
```

### 然后开始下载ansible4.2.0


```bash
[root@localhost ~]# cat requirements.txt
ansible==4.2.0
[root@localhost ~]# mkdir -p /root/packages/ansible
[root@localhost ~]# python3 -m pip download  -r requirements.txt -d /root/packages/ansible

root@dev:~/tmp/packages# cd /root/packages/ansible
root@dev:~/packages/ansible# ll
total 46256
-rw-r--r-- 1 root root 34966129 Mar  6 10:59 ansible-4.2.0.tar.gz
-rw-r--r-- 1 root root  7118115 Mar  6 10:59 ansible-core-2.11.12.tar.gz
-rw-r--r-- 1 root root   402952 Mar  6 10:59 cffi-1.15.1-cp36-cp36m-manylinux_2_5_x86_64.manylinux1_x86_64.whl
-rw-r--r-- 1 root root  3741816 Mar  6 10:59 cryptography-40.0.2-cp36-abi3-manylinux_2_28_x86_64.whl
-rw-r--r-- 1 root root   133630 Mar  6 10:59 Jinja2-3.0.3-py3-none-any.whl
-rw-r--r-- 1 root root    30247 Mar  6 10:59 MarkupSafe-2.0.1-cp36-cp36m-manylinux_2_5_x86_64.manylinux1_x86_64.manylinux_2_12_x86_64.manylinux2010_x86_64.whl
-rw-r--r-- 1 root root    40750 Mar  6 10:59 packaging-21.3-py3-none-any.whl
-rw-r--r-- 1 root root   118697 Mar  6 10:59 pycparser-2.21-py2.py3-none-any.whl
-rw-r--r-- 1 root root   103139 Mar  6 10:59 pyparsing-3.1.1-py3-none-any.whl
-rw-r--r-- 1 root root   677167 Mar  6 10:59 PyYAML-6.0.1-cp36-cp36m-manylinux_2_17_x86_64.manylinux2014_x86_64.whl
-rw-r--r-- 1 root root    12807 Mar  6 10:59 resolvelib-0.5.4-py2.py3-none-any.whl

root@dev:~/tmp/packages/ansible# yum deplist ansible
Last metadata expiration check: 2:27:00 ago on Wed 06 Mar 2024 08:39:17 AM CST.
package: ansible-8.3.0-1.el8.noarch
  dependency: /usr/bin/bash
   provider: bash-4.4.20-4.el8.x86_64
  dependency: /usr/bin/python3.11
   provider: python3.11-3.11.7-1.el8.x86_64
  dependency: python(abi) = 3.11
   provider: python3.11-3.11.7-1.el8.x86_64
  dependency: python3.11dist(ansible-core) >= 2.15.3
   provider: ansible-core-2.16.2-1.el8.x86_64

```


### 现在本地安装ansible4.2.0验证是否成功

```bash
python3 -m pip install --no-index --find-links /root/packages/ansible -r requirements.txt
```



## repotrack

```bash
# 安装yum-utils
$ yum -y install yum-utils

# 下载 ansible 全量依赖包
$ repotrack ansible
```

### 离线安装 rpm

`rpm -Uvh --force --nodeps *.rpm`

