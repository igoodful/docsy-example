---
title: 表锁
description: 表锁
date: 2023-10-30
weight: 50


viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


{{<alert color="secondary">}}

本文基于 MySQL 8.0.32 源码，存储引擎为 InnoDB。

格式化：Markdown Table Prettifier

{{</alert>}}



## 一、锁模式兼容表
