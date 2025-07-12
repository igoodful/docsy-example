---
title: 主机监控
description: 用于监控主机的一些资源使用情况
date: 2023-10-06
weight: 60000



---

{{< alert >}}
### 主机监控 psutil

1. 机器状态

2. 磁盘使用率

3. cpu使用率

4. 内存使用率

5. io利用率

6. 网卡发送流量

7. 网卡接收流量



{{< /alert >}}

### MySQL监控

{{< alert title="配置信息">}}

| 硬件名称                 | 配置      |
|:-------------------------|:----------|
| Engine                   | InnoDB    |
| character_set_client     | UTF8MB4   |
| character_set_connection | UTF8MB4   |
| character_set_database   | UTF8MB4   |
| character_set_database   | UTF8MB4   |
| character_set_database   | UTF8MB4   |
| character_set_database   | UTF8MB4   |
| OS                       | Kylin-v10 |
| CPU                      | 64C       |
| Memory                   | 512G      |
| Space                    | 14T       |
| MySQL Version            | 8.0.26    |

{{< /alert >}}


### show slave status

{{< alert tile="主从延迟" color="secondary">}}

| 监控指标        | 数据                  | 采集方式          | 阈值      |
|:------------|:----------------------|:------------------|:--------|
| 主从延迟        | Seconds_Behind_Master | show slave status | 单位为秒  |
| 从库io线程状态  | Slave_IO_Running      | show slave status | Yes为正常 |
| 从库sql线程状态 | Slave_SQL_Running     | show slave status | Yes为正常 |
| 主从延迟        | show slave status     | show slave status | 单位为秒  |

{{< /alert >}}

```sql

```











