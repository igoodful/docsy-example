---
title: 2. MySQL 运维与管理
description: Show your user how to work through some end to end examples.
date: 2017-01-04
weight: 20000
---

{{< alert >}}
2.1 数据库创建与删除

2.2 用户管理与权限控制

2.3 数据备份与恢复

2.4 性能优化与调整

2.5 安全性和数据保护

2.6 主从复制和高可用性

2.7 数据迁移与升级

2.8 监控与故障排除

{{< /alert >}}

#### /etc/profile
```bash
PS1='\[\033[01;37m\]\D{%Y-%m-%d}T\t \[\033[01;37m\][\[\033[01;35m\]\u@\h \[\033[01;34m\]/home/mysql\[\033[01;37m\]] \[\033[01;36m\]$ \[\033[00m\]'
export MYSQL_PS1="[\\U:\p \\R:\\m:\\s(\\d)]$ "
alias mylogin='mysql -uadmin -p'admin' -h127.0.0.1 -P16310'
alias mylogin_local='mysql -uadmin -p'admin' -h127.0.0.1 -P16315'
```