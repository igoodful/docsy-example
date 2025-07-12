---
title: 清空zookeeper配置
description: 清理和更新配置文件
date: 2017-01-05
weight: 70000


---

{{< alert >}}

{{< /alert >}}

1. 停止所有节点的dbscale
```sql
cd /data/app/dbscale

./daemon/stopper.sh

ps -ef | grep dbscale

```



2. 清理和更新配置文件
```sql
cd /data/app/dbscale

export LD_LIBRARY_PATH=./libs

./clean_zookeeper 127.0.0.1 16309

```

3. 修改配置文件dbscale.conf
{{<warning>}}
<!---->
配置文件中有，dbscale端口16310和业务端口23399（可根据需要修改），默认是23399端口，无论transparent-mode是否开启，都存在透传端口。
- 16310：为管理端口，管理端口可以执行dbscale命令，也可以执行数据库sql语句。
- 23399：为业务端口，透传业务端口可以执行用户的sql语句，不能执行dbscale的语句。
{{</warning>}}


```sql
[main]
...
transparent-mode = 1
...

[driver mysql]
type = MySQLDriver
port = 16310
transparent-port = 23399
bind-address = 127.0.0.1

```


4. 启动dbscale

```sql
cd /data/app/dbscale

./daemon/dbscale_daemon.py
```



使用命令更改透传参数，然后重启dbscale
```sql
dbscale change startup config 'transparent-mode'='1';

```










