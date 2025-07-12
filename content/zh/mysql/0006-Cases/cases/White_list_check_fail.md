---
title: white list check fail
description: Access denied due to white list check fail for client:127.0.0.1
date: 2024-09-12
weight: 60000
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< note >}}

{{< /note >}}


## 一、环境信息

{{< alert >}}

| 名称          | 配置       |
| :------------ | :--------- |
| Arch          | x64        |
| OS            | CentOS 7.9 |
| CPU           | 8C         |
| Memory        | 16G        |
| Space         | 200G       |
| MySQL Version | 8.0.26     |

{{< /alert >}}


## 如何排查？

mysql初始化完成后需要进行登录，这时候登录只有`mysql@localhost`用户能够登录

```bash
mysql -S /data/mysqldata/16315fff-4203-491a-9d54-7a7e60c1f4c8/socket/mysql.sock -uroot -p'Root123456'
mysql: [Warning] Using a password on the command line interface can be insecure.
ERROR 1045 (28000): Access denied due to white list check fail for client:127.0.0.1
```


1. 当前mysqld进程存在
2. 切换成其他操作系统用户或创建新用户进行socket登录
3. 将其他正常的mysql实例启动后用socket登录和tcp/ip登录
4. 查看家目录中与mysql相关的配置
5. 源码调试















## 复现

```bash
2025-03-06T15:11:01 [mysql@ky10arm-102 /home/mysql] $ /data/app/mysql-8.0.32/bin/mysql_config_editor set --login-path=mysql --user=admin  --host=127.0.0.1 --port=16310 --password
Enter password:
WARNING : 'mysql' path already exists and will be overwritten.
 Continue? (Press y|Y for Yes, any other key for No) : yes

2025-03-06T15:11:35 [mysql@ky10arm-102 /home/mysql] $ mysql -S /data/mysqldata/16315fff-4203-491a-9d54-7a7e60c1f4c8/socket/mysql.sock -uroot -p'Root123456'
mysql: [Warning] Using a password on the command line interface can be insecure.
ERROR 1045 (28000): Access denied due to white list check fail for client:127.0.0.1

2025-03-06T15:11:47 [mysql@ky10arm-102 /home/mysql] $ ps aux|grep dbs
mysql      71002  0.5  0.2 455232 17088 ?        S    Feb28  48:32 /usr/bin/python ./daemon/dbscale_daemon.py
mysql    3185322  0.9  1.0 17769344 84096 ?      Sl   Mar05   9:17 ./dbscale --config-file dbscale.conf
mysql    3562694  0.0  0.0 214016  1536 pts/1    S+   15:11   0:00 grep dbs

2025-03-06T15:11:58 [mysql@ky10arm-102 /home/mysql] $ /data/app/mysql-8.0.32/bin/mysql_config_editor print --all
[mysql]
user = "admin"
password = *****
host = "127.0.0.1"
port = 16310

2025-03-06T15:12:18 [mysql@ky10arm-102 /home/mysql] $
```



## 结论

不要创建login-path为mysql的登录名称，即不要这样：`mysql_config_editor set --login-path=mysql`





