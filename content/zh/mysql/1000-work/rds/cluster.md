---
title: 每个集群
description: 每个集群
date: 2023-10-23
weight: 20000



---

{{< alert >}}


{{< /alert >}}



### 获取每个集群的一个主机名称

```sql
mysql -e "use greatrds;select name from cluster\G"

mysql -e "use greatrds;select compute_node.labels  from cluster inner join  compute_node on cluster.address=compute_node.hostname \G" |awk -F'"' '{print $(NF-1)}'

```


### 获取集群的告警
```sql
select c.name cluster_name,a.status,a.name   from greatrds.cluster c inner join greatrds.alert_rule a on c.uuid=a.instance_uuid where a.name='normal主从延迟较大' and a.status='down';
```


















