---
title: tsdb
description: Prometheus自带的tsdb时序数据库
date: 2023-11-05
weight: 30000


---
{{< alert >}}
本地存储
{{< /alert >}}

##
1. 什么是时序数据库

时序数据库全称为时间序列数据库。时间序列数据库主要用于指处理带时间标签（按照时间的顺序变化，即时间序列化）的数据，带时间标签的数据也称为时间序列数据。主要用于存储周期性的采集各种实时监控信息。

2. 特点
- 垂直写，水平读
- 数据点写入分散，且数据量巨大
- 热点数据明显
3. 存储配置

- storage.tsdb.path：数据存储目录。默认为 data/。

- storage.tsdb.retention.time：数据过期清理时间，默认为 15 天。

- storage.tsdb.wal-compression：此标志启用预写日志（WAL）的压缩。根据您的数据，您可以预期 WAL 大小将减少一半，而额外的 CPU 负载却很少。此标志在 2.11.0 中引入，默认情况下在 2.20.0 中启用。请注意，一旦启用，将 Prometheus 降级到 2.11.0 以下的版本将需要删除 WAL。




4. 目录结构




5. 储存原理（write）





6. 索引




远程存储

