---
title: PromQL
description: PromQL
date: 2023-11-05
weight: 30000


viz: true
---
{{< alert >}}

{{< /alert >}}



## 整体
```viz-dot
digraph "运算符" {
        rankdir=LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
        ];

        "运算符" -> "算术运算符" ;
        "算术运算符" -> "*" ;
        "算术运算符" -> "/" ;
        "算术运算符" -> " % " ;
        "算术运算符" -> " ^ " ;
        "算术运算符" -> "+" ;
        "算术运算符" -> "-" ;

        "运算符" -> "关系运算符";
        "关系运算符" -> ">" ;
        "关系运算符" -> ">=" ;
        "关系运算符" -> "<" ;
        "关系运算符" -> "<=" ;
        "关系运算符" -> "==" ;
        "关系运算符" -> "!=" ;

        "运算符" -> "逻辑运算符" ;
        "逻辑运算符" -> "and" ;
        "逻辑运算符" -> "or" ;
        "逻辑运算符" -> "unless" ;
}
```

指标名称：http_requests_total = http_requests_total{} 该表达式会返回指标名称为http_requests_total的所有时间序列：



## 标签匹配

### 完全匹配模式

- 通过使用label=value可以选择那些标签满足表达式定义的时间序列；

- 反之使用label!=value则可以根据标签匹配排除时间序列；

查询所有http_requests_total时间序列中满足标签instance为localhost:9090的时间序列，则可以使用如下表达式：http_requests_total{instance="localhost:9090"}

反之使用instance!="localhost:9090"则可以排除这些时间序列：http_requests_total{instance!="localhost:9090"}


### 正则表达式
多个表达式之间使用|进行分离

- 使用label=~regx表示选择那些标签符合正则表达式定义的时间序列；

- 反之使用label!~regx进行排除；

查询多个环境下的时间序列序列可以使用如下表达式：http_requests_total{environment=~"staging|testing|development",method!="GET"}

## 范围查询
直接通过类似于PromQL表达式 http_requests_total 查询时间序列时，返回值中只会包含该时间序列中的最新的一个样本值，这样的返回结果我们称之为瞬时向量。而相应的这样的表达式称之为__瞬时向量表达式。

时间范围通过时间范围选择器 [] 进行定义。例如，通过以下表达式可以选择最近5分钟内的所有样本数据：http_request_total{}[5m]

该表达式将会返回查询到的时间序列中最近5分钟的所有样本数据：

除了使用m表示分钟以外，PromQL的时间范围选择器支持其它时间单位：

- s - 秒
- m - 分钟
- h - 小时
- d - 天
- w - 周
- y - 年

在瞬时向量表达式或者区间向量表达式中，都是以当前时间为基准：

- http_request_total{} # 瞬时向量表达式，选择当前最新的数据
- http_request_total{}[5m] # 区间向量表达式，选择以当前时间为基准，5分钟内的数据

而如果我们想查询，5分钟前的瞬时样本数据，或昨天一天的区间内的样本数据呢? 这个时候我们就可以使用位移操作，位移操作的关键字为offset。

- http_request_total{} offset 5m
- http_request_total{}[1d] offset 1d


## 聚合操作
一般来说，如果描述样本特征的标签(label)在并非唯一的情况下，通过PromQL查询数据，会返回多条满足这些特征维度的时间序列。而PromQL提供的聚合操作可以用来对这些时间序列进行处理，形成一条新的时间序列：

查询系统所有http请求的总量：sum(http_request_total)

按照mode计算主机CPU的平均使用时间：avg(node_cpu) by (mode)

按照主机查询各个主机的CPU使用率：sum(sum(irate(node_cpu{mode!='idle'}[5m]))  / sum(irate(node_cpu[5m]))) by (instance)

Prometheus还提供了下列内置的聚合操作符，这些操作符作用域瞬时向量。可以将瞬时表达式返回的样本数据进行聚合，形成一个新的时间序列。

- min (最小值)
- max (最大值)
- sum (求和)
- avg (平均值)
- count (计数)
- stddev (标准差)
- stdvar (标准差异)
- count_values (对value进行计数)
- bottomk (后n条时序)
- topk (前n条时序)
- quantile (分布统计)





## 内置函数

rate rate(node_cpu[2m])

| 函数               | 适用类型 | 作用                                               | 示例 |
|:-------------------|:---------|:---------------------------------------------------|:-----|
| group              |          |                                                    |      |
| count              | Gauge    | 计算向量中元素的数量                               |      |
| count_values       | Gauge    | 计算具有相同值的元素数量                           |      |
| sum                | Gauge    |                                                    |      |
| min                | Gauge    |                                                    |      |
| max                | Gauge    |                                                    |      |
| group              |          |                                                    |      |
| rate               | Counter  |                                                    |      |
| irate              | Counter  |                                                    |      |
| increase           | Counter  | 获取区间向量中的第一个和最后一个样本并返回其增长量 |      |
| offset             | Any      |                                                    |      |
| delta              | Gauge    |                                                    |      |
| idelta             |          |                                                    |      |
| topk               |          | 样本值中最大的 k 个元素                            |      |
| bottomk            |          | 按样本值计算的最小 k 个元素                        |      |
| quantile           |          | 计算维度上的 φ 分位数 (0 ≤ φ ≤ 1)                  |      |
| sort               |          | 排序                                               |      |
| sort_desc          |          | 倒叙                                               |      |
| ceil               |          | 向上取整                                           |      |
| floor              |          | 向下取整                                           |      |
| round              |          | 四舍五入                                           |      |
| avg_over_time      | Gauge    | 区间向量内每个指标的平均值                         |      |
| sum_over_time      | Gauge    | 区间向量内每个指标的求和                           |      |
| min_over_time      | Gauge    | 区间向量内每个指标的最小值                         |      |
| max_over_time      | Gauge    | 区间向量内每个指标的最大值                         |      |
| count_over_time    | Gauge    | 区间向量内每个指标的样本数据个数                   |      |
| quantile_over_time | Gauge    | 区间向量内每个指标的样本数据值分位数               |      |
| stddev_over_time   | Gauge    | 区间向量内每个指标的总体标准差                     |      |
| stdvar_over_time   | Gauge    | 区间向量内每个指标的总体标准方差                   |      |









rate rate(node_cpu[2m])

通过rate()函数获取HTTP请求量的增长率：rate(http_requests_total[5m])

查询当前系统中，访问量前10的HTTP地址：topk(10, http_requests_total)



## 合法的PromQL表达式

所有的PromQL表达式都必须至少包含一个指标名称(例如http_request_total)，或者一个不会匹配到空字符串的标签过滤器(例如{code="200"})。

因此以下两种方式，均为合法的表达式：

- http_request_total
- http_request_total{}
- {method="get"}


不合法：

- {job=~".*"}

同时，除了使用<metric name>{label=value}的形式以外，我们还可以使用内置的__name__标签来指定监控指标名称：

- {__name__=~"http_request_total"}
- {__name__=~"node_disk_bytes_read|node_disk_bytes_written"}







## PromQL操作符


PromQL支持的所有数学运算符如下所示：

- + (加法)
- - (减法)
- * (乘法)
- / (除法)
- % (求余)
- ^ (幂运算)


例如，我们可以通过指标node_memory_free_bytes_total获取当前主机可用的内存空间大小，其样本单位为Bytes。这是如果客户端要求使用MB作为单位响应数据，那只需要将查询到的时间序列的样本值进行单位换算即可：

node_memory_free_bytes_total / (1024 * 1024)


目前，Prometheus支持以下布尔运算符如下：

- == (相等)
- != (不相等)
- > (大于)
- < (小于)
- >= (大于等于)
- <= (小于等于)

目前，Prometheus支持以下集合运算符：

- and (并且)
- or (或者)
- unless (排除)



假设我们有以下时间序列数据:
- 在时间t1，`http_requests_total`的值为 100
- 在时间t2，`http_requests_total`的值为 120
- 在时间t3，`http_requests_total`的值为 140

现在，我们将使用rate()、irate()和increase()函数来分别计算增长速率或增长量。
1. rate()函数示例:
在时间段[t1,t3]内，`rate(http_requests_total[2m])`将计算从 t1到t3的平均每秒增长速率。
计算过程为:(140-100)/(t3-t1)=40/(t3-t1)=40/2=20每秒。

2. irate()'函数示例:
在时间段[t2,t3]内，irate(http_requests_total[1m])将计算从t2到t3 的最后两个数据点之间的瞬时增长速率，
计算过程为:(140-120)/(t3-t2)=20/(t3-t2)=20/1=20 每秒。

3. increase()函数示例:在时间段 [t1,t3]内，increase(http_requests_total[2m])将计算从t1到t3的总增长量
计算过程为:140-100=40。通过这个示例，我们可以看到在不同的时间范围内，这三个函数计算的结果可能会有所不同rate()和'irate()'主要用于监控数据的增长速率，而'increase()'则用于获取在给定时间段内的总增长量。







## 常见promsql

`avg(rate(node_cpu_seconds_total[5m])) by (job, instance, mode)`

```bash
{instance="172.17.130.48:9200", job="node_exporter", mode="idle"}
{instance="172.17.130.48:9200", job="node_exporter", mode="iowait"}
{instance="172.17.130.48:9200", job="node_exporter", mode="irq"}
{instance="172.17.130.48:9200", job="node_exporter", mode="nice"}
{instance="172.17.130.48:9200", job="node_exporter", mode="softirq"}
{instance="172.17.130.48:9200", job="node_exporter", mode="steal"}
{instance="172.17.130.48:9200", job="node_exporter", mode="system"}
{instance="172.17.130.48:9200", job="node_exporter", mode="user"}
```




# idelta(node_load1[1m])


表示在一分钟内的每两个相邻统计周期的差值，可负可正








