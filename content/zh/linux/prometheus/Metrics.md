---
title: Metrics
description: 指标
date: 2023-11-05
weight: 30000


---
{{< alert >}}



{{< /alert >}}
## 样本

**样本**：在时间序列中的每一个点称为一个样本（sample），样本由以下三部分组成：
- 指标（metric）：指标名称和描述当前样本特征的 labelsets；
- 时间戳（timestamp）：一个精确到毫秒的时间戳；
- 样本值（value）： 一个 folat64 的浮点型数据表示当前样本的值。

**表示方式**：通过如下表达方式表示指定指标名称和指定标签集合的时间序列：<metric name>{<label name>=<label value>, ...}

例如，指标名称为 api_http_requests_total，标签为 method="POST" 和 handler="/messages" 的时间序列可以表示为：api_http_requests_total{method="POST", handler="/messages"}

## 指标类型

### Gauge
{{<caution>}}
<!---->
Gauge 类型代表一种样本数据可以任意变化的指标，即可增可减，侧重于反应系统的当前状态
{{</caution>}}

- 通过Gauge指标，用户可以直接查看系统的当前状态：node_memory_MemFree

- 计算CPU温度在两个小时内的差异：delta(cpu_temp_celsius{host="zeus"}[2h])

- 预测系统磁盘空间在4个小时之后的剩余情况：predict_linear(node_filesystem_free{job="node"}[1h], 4 * 3600)


指标：node_memory_MemFree_bytes
```bash
[root@k8s-node-70 prometheus]# free -h
              total        used        free      shared  buff/cache   available
Mem:           7.8G        926M        5.7G        174M        1.1G        6.4G
Swap:            0B          0B          0B

```
访问：`http://172.17.134.70:9100/metrics`
```sql
# HELP node_memory_MemFree_bytes Memory information field MemFree_bytes.
# TYPE node_memory_MemFree_bytes gauge
node_memory_MemFree_bytes 6.139314176e+09
```
1. delta，获取样本在一段时间内的变化情况
{{<caution>}}
<!---->
`delta(v range-vector)` 的参数是一个区间向量，返回一个瞬时向量。它计算一个区间向量 v 的第一个元素和最后一个元素之间的差值。
{{</caution>}}

`delta(node_memory_MemFree_bytes[1m])`

2. idelta
delta(v range-vector) 的参数是一个区间向量, 返回一个瞬时向量。它计算最新的 2 个样本值之间的差值。


3. predict_linear，对样本数据的变化趋势做出预测

基于 2 小时的样本数据，来预测主机可用磁盘空间在 4 个小时之后的剩余情况：`predict_linear(node_filesystem_free{job="node"}[2h], 4 * 3600) < 0`

### Counter
{{<caution>}}
<!---->
Counter 类型代表一种样本数据单调递增的指标，即只增不减，除非监控系统发生了重置。让用户方便的了解事件产生的速率的变化
- 用 counter 类型的指标来表示服务的请求数、已完成的任务数、错误发生的次数等。一般在定义Counter类型指标的名称时推荐使用_total作为后缀。
- 不要将 counter 类型应用于样本数据非单调递增的指标，例如：当前运行的进程数量（应该用 Gauge 类型）。
{{</caution>}}

- 过去 5 分钟内的 http 请求数的平均增长速率：rate(http_requests_total[5m])

- 返回度量指标 http_requests_total 过去 5 分钟内的 http 请求数的平均增长速率总和，维度是 job：`sum(rate(http_requests_total[5m])) by (job)`
结果：
```sql
{job="apiserver"}  0.16666666666666666
{job="kubelet"}  0.49999876544124355
```

- 查询当前系统中，访问量前10的HTTP地址：topk(10, http_requests_total)


irate(v range-vector) 函数用于计算区间向量的增长率，但是其反应出的是瞬时增长率。irate 函数是通过区间向量中最后两个两本数据来计算区间向量的增长速率，它会在单调性发生变化时(如由于采样目标重启引起的计数器复位)自动中断。这种方式可以避免在时间窗口范围内的“长尾问题”，并且体现出更好的灵敏度，通过irate函数绘制的图标能够更好的反应样本数据的瞬时变化状态。
例如，以下表达式返回区间向量中每个时间序列过去 5 分钟内最后两个样本数据的 HTTP 请求数的增长率：
irate(http_requests_total{job="api-server"}[5m])
irate 只能用于绘制快速变化的计数器，在长期趋势分析或者告警中更推荐使用 rate 函数。因为使用 irate 函数时，速率的简短变化会重置 FOR 语句，形成的图形有很多波峰，难以阅读。








### Histogram 直方图

Histogram和Summary主用用于统计和分析样本的分布情况。通过Histogram和Summary类型的监控指标，我们可以快速了解监控样本的分布情况。





### Summary















