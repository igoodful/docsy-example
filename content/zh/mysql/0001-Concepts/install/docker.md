---
title: docker安装
description: docker安装
date: 2023-11-07
weight: 20000


---

{{< alert >}}

{{< /alert >}}



## 环境
{{<alert>}}

| 硬件名称           | 配置          |
|:-------------------|:--------------|
| Xtrabackup Version | 8.0.26-18     |
| MySQL Version      | 8.0.26        |
| CPU                | 4C            |
| Memory             | 8G            |
| Space              | 200G (SSD)    |
| Arch               | x64           |
| OS                 | CentOS 7.6    |
| Kernel Version     | 5.4.257-1.el7 |
| Hugepagesize       | 2048 KB       |

{{</alert>}}

## 拉取镜像并创建容器
```sql
mkdir -p /home/work/mysql_3366

mkdir -p /home/work/mysql_3366/{data,conf,log,tmp}

docker pull mysql:5.7.44

docker run -d --name my5744 \
-h my5744 -p 3366:3366 \
-v /home/work/mysql_3366/conf:/etc/mysql/conf.d \
-v /home/work/mysql_3366/data:/var/lib/mysql/ \
-e MYSQL_ROOT_PASSWORD=root \
-e TZ=Asia/Shanghai \
mysql:5.7.44

```


## 进入容器并登录
```sql
docker exec -it my5744 bash

mysql -uroot -proot  -P3366

```

输出如下：
```sql
[root@gip ~]# docker exec -it my5744 bash

bash-4.2# mysql -uroot -proot  -P3366
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.7.44 MySQL Community Server (GPL)

Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> select password('root');
+-------------------------------------------+
| password('root')                          |
+-------------------------------------------+
| *81F5E21E35407D884A6CD4A731AEBFB6AF209E1B |
+-------------------------------------------+
1 row in set, 1 warning (0.00 sec)

```
