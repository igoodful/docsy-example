---
title: join
description: join
date: 2017-01-04
weight: 13000
---

{{< alert >}}

驱动表的概念：mysql中指定了连接条件时，满足查询条件的记录行数少的表为驱动表；如未指定查询条件，则扫描行数少的为驱动表。mysql优化器就是这么粗暴以小表驱动大表的方式来决定执行顺序的。


{{< /alert >}}




## inner join
> 驱动表待定


## straight_join
> 就是在内连接中使用，而强制使用左表来当驱动表,强制改变mysql的优化器选择的执行计划

尽可能让优化器去判断，因为大部分情况下mysql优化器是比人要聪明的。使用STRAIGHT_JOIN一定要慎重，因为啊部分情况下认为指定的执行顺序并不一定会比优化引擎要靠谱。

## left join

> left join就是以左表为驱动表


## right join
> right join就是以右表为驱动表















