---
title: profile
description: profile
date: 2023-10-24
weight: 20000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

- centos7 的地址 mirrorlist.centos.org 变更为 vault.centos.org

{{< /alert >}}


## /etc/profile

```bash
PS1="\u@\h:\w\\$"
PS1="\e[1;33m\u@\h\e[m:\e[1;35m\w\e[m\\$ "
TMOUT=60000
HISTTIMEFORMAT="%F %T `whoami` "
export HISTSIZE=1024
export HISTFILESIZE=102400
```

### 命令提示符：PS1
[PS1](https://www.ohyee.cc/post/note_bash_terminal)
- \h    主机名（包含.的情况下取第一个字段）
- \H	完整主机名（包含.的情况下和\h有所区别）
- \w	当前目录（显示为全路径），$HOME目录显示为~
- \W	当前目录（显示为basename的信息），$HOME目录显示为~
- $	超级用户（UID为0）显示为#，其余显示为$
- \t    [\t] 以 [hh:mm:ss] 格式显示当前时间,[12:42:55]
- \!    命令的历史编号
- \e    更改提示的前景色: `\e[x;ym  \e[m` 比如 `\e[0;34m  \e[m`
- \e    更改提示的背景颜色：`\e[xm \e[m` 比如 `\e[47m \e[m`
- \e    背景与前景的结合 `export PS1="\e[0;34m\e[47m\u@\h \w> \e[m"`
- `$kernel_version`       来自 `$kernel_version` 变量的 `uname -r` 命令的输出


在`PS1`中设置字符颜色的格式为：`\[\e[F;Bm\]........\[\e[0m\]`，其中`F`为字体颜色，编号为`30-37`，`B`为背景颜色，编号为`40-47`,`\[\e[0m\]`作为颜色设定的结束。

- `\[\e[font;fg;bgm\]` 对后面的设置颜色


| F  | B  | 备注   |
|:---|:---|:-------|
| 30 | 40 | 黑色   |
|    |    |        |
| 31 | 41 | 红色   |
|    |    |        |
| 32 | 42 | 绿色   |
|    |    |        |
| 33 | 43 | 黄色   |
|    |    |        |
| 34 | 44 | 蓝色   |
|    |    |        |
| 35 | 45 | 紫红色 |
|    |    |        |
| 36 | 46 | 青蓝色 |
|    |    |        |
| 37 | 47 | 白色   |


`PS1='\[\033[01;35m\][\[\033[01;32m\]\u\[\033[01;32m\]$(echo $STY | cut -d . -f2)\[\033[01;35m\] \[\033[01;35m\]\[\033[01;35m\]\h\[\033[01;35m\] \[\033[38;5;39m\]$(pwd)\[\033[31m\]] \[\033[38;5;221m\]WORK \[\033[1;37m\]${PIPESTATUS[0]}\[\033[01;36m\] \n# \[\033[00m\]'
`

- `\[\033[01;35m\][`  设置`[`颜色
- `\[\033[01;32m\]\u` 设置`\u`颜色，即用户名颜色
- `\[\033[01;32m\]$(echo $STY | cut -d . -f2)`
- `\[\033[01;35m\] \[\033[01;35m\]\[\033[01;35m\]\h`
- `\[\033[01;35m\] \[\033[38;5;39m\]$(pwd)`
- `\[\033[31m\]] \[\033[38;5;221m\]WORK `
- `\[\033[1;37m\]${PIPESTATUS[0]}`
- `\[\033[01;36m\] \n# `
- `\[\033[00m\]`

# 声明全局变量
export PS1

PS1='\[\033[01;37m\][\[\033[01;35m\]\u@\h \[\033[38;5;39m\]$(pwd)\[\033[01;37m\]] \[\033[38;5;221m\]WORK \[\033[1;37m\]$()\[\033[01;36m\] \n$ \[\033[00m\]'

# 声明全局变量
export PS1









### 设置历史命令时间的格式：`HISTTIMEFORMAT`
> HISTTIMEFORMAT="%F %T \`whoami\` "
> whoami 完了后面要有空格不然会连住和命令
```bash
root@dev:/data/app/mysql-8.0.32#history |tail  -n 10
 1711  2024-03-01 12:23:03 root source  /etc/profile
 1712  2024-03-01 12:23:06 root history |head -n 10
 1713  2024-03-01 12:23:08 root vim /etc/profile
 1714  2024-03-01 12:23:23 root source  /etc/profile
 1715  2024-03-01 12:23:26 root history |head -n 10
 1716  2024-03-01 12:23:31 root vim /etc/profile
 1717  2024-03-01 12:23:56 root history |head -n 10
 1718  2024-03-01 12:24:11 root history |tail  -n 10
 1719  2024-03-01 12:24:20 root \su - mysql
 1720  2024-03-01 12:24:37 root history |tail  -n 10


```

###  `HISTSIZE`
> 默认1000条
> 定义了 `history` 命令输出`.bash_history`文件中的最后`HISTSIZE`行


### `HISTFILESIZE`
> `HISTFILESIZE` 定义了在 `.bash_history` 中保存命令的记录总数，可以理解为`.bash_history`文件中最多只有`HISTFILESIZE`行




## 最终方案一：`/etc/profile`

>` /etc/profile`
```bash
TMOUT=60000
HISTTIMEFORMAT="%F %T `whoami` "
export HISTSIZE=1024
export HISTFILESIZE=102400
PS_FROM=$(who am i |awk '{print $5}' |awk -F'(' '{print $2}' |awk -F')' '{print $1}')
PS_TTY=$(who am i |awk '{print $2}' |awk -F'/' '{print $2}')
PS1='\[\033[01;37m\][\[\033[01;35m\]\u@\h \[\033[01;34m\]$(pwd)\[\033[01;37m\]] \[\033[38;5;221m\]TTY \[\033[1;37m\]${PS_TTY}\[\033[01;36m\] \[\033[38;5;221m\]FROM \[\033[1;37m\]${PS_FROM}\[\033[01;36m\] \n\$ \[\033[00m\]'


```

```sql
[root@dev /root/tmp/slow] TTY 0 FROM 172.17.120.253
# ll
total 52
-rw-r--r-- 1 root root   987 Feb  5 10:48 pthread.cc
-rw-r--r-- 1 root root 47616 Jan 22 17:47 基于Ansible部署TIicBase.doc
[root@dev /root/tmp/slow] TTY 0 FROM 172.17.120.253
#


```



## 最终方案二：`/etc/profile`

>` /etc/profile`
```bash
TMOUT=60000
HISTTIMEFORMAT="%F %T `whoami` "
export HISTSIZE=1024
export HISTFILESIZE=102400
PS_FROM=$(who am i |awk '{print $5}' |awk -F'(' '{print $2}' |awk -F')' '{print $1}')
PS_TTY=$(who am i |awk '{print $2}' |awk -F'/' '{print $2}')
PS1='\[\033[01;37m\][\[\033[01;35m\]\u@\h \[\033[01;34m\]$(pwd)\[\033[01;37m\]]\[\033[01;36m\]\$ \[\033[00m\]'
```

案例：
```bash

[root@node71 /root]# ll
total 16
-rw-r--r--. 1 root root 1058 Sep 19  2022 README.txt
drwxr-xr-x  7 root root 4096 Mar 11 10:18 rpmbuild
drwxr-xr-x  3 root root 4096 Mar 11 10:24 test
drwxr-xr-x  2 root root 4096 Mar 11 10:22 tree
[root@node71 /root]#

```

## 最终方案三：`/etc/profile`


```bash
TMOUT=60000
HISTTIMEFORMAT="%F %T `whoami` "
export HISTSIZE=1024
export HISTFILESIZE=102400
PS_FROM=$(who am i |awk '{print $5}' |awk -F'(' '{print $2}' |awk -F')' '{print $1}')
PS_TTY=$(who am i |awk '{print $2}' |awk -F'/' '{print $2}')
PS1='\[\033[01;37m\][\[\033[01;35m\]\u@\h \[\033[01;34m\]$(pwd)\[\033[01;37m\]] \[\033[01;37m\]\D{%Y-%m-%d}T\t \[\033[01;36m\]\$ \[\033[00m\]'
PS1='\[\033[01;37m\]\D{%Y-%m-%d}T\t \[\033[01;37m\][\[\033[01;35m\]\u@\h \[\033[01;34m\]$(pwd)\[\033[01;37m\]] \[\033[01;36m\]\$ \[\033[00m\]'
```




## 最终方案四：`/etc/profile`
> 2024-10-30

```bash
export LANG="en_US.UTF-8"
PS1='\[\033[01;37m\]\D{%Y-%m-%d}T\t \[\033[01;37m\][\[\e[1;33m\]\u@\h \[\033[01;35m\]$(pwd)\[\033[01;37m\]] \[\033[01;36m\]\$ \[\033[00m\]'
export MYSQL_PS1="[\U:\p \R:\m:\s(\d)]$ "
alias mylogin='mysql -uadmin -p'123456' -h127.0.0.1 -P3306'
alias mylogin_local='mysql -uadmin -p'123456' -h127.0.0.1 -P3307'


```


## 2025-03-06

- 新增`alias ll="ls -l --time-style '+%Y-%m-%dT%H:%M:%S'"`让时间显示更人性化
- 或者`alias ll=ls -l --time-style=full-iso`

```bash
export PATH=/root/local/mysql_8032/bin:$PATH
export LANG="en_US.UTF-8"
alias ll="ls -l --time-style '+%Y-%m-%dT%H:%M:%S'"
alias mylogin='mysql -uroot -p'root' -h127.0.0.1 -P3306'
export MYSQL_PS1="[\U:\p \R:\m:\s(\d)]$ "
PS1='\[\033[01;37m\]\D{%Y-%m-%d}T\t \[\033[01;37m\][\[\e[1;33m\]\u@\h \[\033[01;35m\]$(pwd)\[\033[01;37m\]] \[\033[01;36m\]\$ \[\033[00m\]'
source /opt/rh/gcc-toolset-11/enable

```





















