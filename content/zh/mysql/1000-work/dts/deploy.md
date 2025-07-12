---
title: GreatDTS 安装
description: GreatDTS是集评估、迁移、同步和校验功能于一体的数据迁移工具，提供便捷部署、易于使用、快速高效的同构和异构数据复制服务。
date: 2023-10-24
weight: 70000


---

{{< alert >}}
- docker version > 17


{{< /alert >}}



### docker
```sql
docker version




```

### mysql
```sql
create database cc;

```





```sql
tar xvfz GreatDTS-5.1.9-RC-4.tar.gz -C /usr/local
ln -s /usr/local/GreatDTS-5.1.9-RC-4 /usr/local/GreatDTS
cd /usr/local/GreatDTS
cp -r configs-sample configs

vi /usr/local/GreatDTS/configs/config.sh
---配置库只支持GreatDB及其他mysql兼容数据库
-- 启动前先确认配置
export SW_STORAGE=mysql
-- 配置库地址 格式 host:port/database
export SW_DATA_URL="172.17.120.101:3306/cc"
export SW_DATA_SOURCE_USER=root
export SW_DATA_SOURCE_PASSWORD=123456




./scripts/start.sh

```


###

打开浏览器，访问以下入口URL并登陆，即可进入GreatDTS服务页面。






