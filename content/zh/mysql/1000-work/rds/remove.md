---
title: 删除集群
description: RDS
date: 2023-10-23
weight: 20000


---

{{< alert >}}


{{< /alert >}}



{{<alert title="" color="secondary">}}




{{</alert>}}


```bash

systemctl stop configuration.service
systemctl disable configuration.service
rm -rf /usr/lib/systemd/system/configuration.service

systemctl stop zk-16309.service
systemctl disable zk-16309.service
rm -rf /usr/lib/systemd/system/zk-16309.service


systemctl stop mysql_16315.service
systemctl disable mysql_16315.service
rm -rf /usr/lib/systemd/system/mysql_16315.service

systemctl stop mysql.service
systemctl disable mysql.service
rm -rf /usr/lib/systemd/system/mysql.service

systemctl stop db-16315.service
systemctl disable db-16315.service
rm -rf /usr/lib/systemd/system/db-16315.service

systemctl stop dbscale.service
systemctl disable dbscale.service
rm -rf /usr/lib/systemd/system/dbscale.service

systemctl stop router.service
systemctl disable router.service
rm -rf /usr/lib/systemd/system/router.service

systemctl stop router-16310.service
systemctl disable router-16310.service
rm -rf /usr/lib/systemd/system/router-16310.service

ps aux|grep mysqld|grep -v grep |awk '{print $2}' |xargs kill -9
ps aux|grep dbscale|grep -v grep |awk '{print $2}' |xargs kill -9
ps aux|grep zoo|grep -v grep |awk '{print $2}' |xargs kill -9

systemctl stop gip_node_exporter.service
systemctl disable gip_node_exporter.service
rm -rf /etc/systemd/system/gip_node_exporter.service

systemctl stop dbinit.service
systemctl disable dbinit.service
rm -rf /usr/lib/systemd/system/dbinit.service

systemctl stop rds-greatrds-exporter.service
systemctl disable rds-greatrds-exporter.service
rm -rf /usr/lib/systemd/system/rds-greatrds-exporter.service

systemctl stop greatrds-exporter.service
systemctl disable greatrds-exporter.service
rm -rf /usr/lib/systemd/system/greatrds-exporter.service

rm -rf /data/*


```


```sql
-- 关闭router
cd /data/app/dbscale && ./daemon/dbscale_daemon_stop.py

echo "=== dbscale"

pidof dbscale

echo "==="

-- 删除zookeeper
systemctl stop configuration.service

systemctl disable configuration.service

rm -rf /usr/lib/systemd/system/configuration.service

echo "=== java"

pidof java

echo "==="

-- 删除MySQL
systemctl stop mysql_16315.service

systemctl disable mysql_16315.service

rm -rf /usr/lib/systemd/system/mysql_16315.service

echo "=== mysqld"

pidof mysqld

echo "==="

--
systemctl stop rds-greatrds-exporter.service

systemctl disable rds-greatrds-exporter.service

rm -rf /usr/lib/systemd/system/rds-greatrds-exporter.service

-- dbinit.service
systemctl stop dbinit.service

systemctl disable dbinit.service

rm -rf /usr/lib/systemd/system/dbinit.service

echo "=== greatrds-dbinit"

pidof greatrds-dbinit

echo "==="

-- node_exporter
systemctl stop gip_node_exporter.service

systemctl disable gip_node_exporter.service

rm -rf /etc/systemd/system/gip_node_exporter.service

echo "=== node_exporter"

pidof node_exporter

echo "==="

-- greatrds-exporter
systemctl stop greatrds-exporter.service

systemctl disable greatrds-exporter.service

rm -rf /usr/lib/systemd/system/greatrds-exporter.service

echo "=== greatrds-exporter"

ps aux|grep /data/dbinit/venv/bin/greatrds-exporter |grep -v grep

echo "==="

```




## 添加计算节点
> 会部署这两个服务


```sql

systemctl status dbinit.service

systemctl stop dbinit.service

systemctl disable dbinit.service



systemctl status greatrds-exporter.service

systemctl stop greatrds-exporter.service

systemctl disable greatrds-exporter.service
```
















