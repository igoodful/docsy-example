---
title: type
description: 基本数据类型
date: 2023-10-31
weight: 2000
viz: true

---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


{{< alert  color="primary">}}

{{< /alert >}}


## 1. 布尔

jinja2原生的布尔值应当是小写的true和false，但在ansible中也支持首字母大写形式的True和False。因此最佳实践：一律小写true或false

- true
- false


```yaml

```

## 2. 列表或字典

Jinja2模板引擎允许使用点.来访问列表或字典元素，比如mylist=["a","b","c"]列表，在Jinja2中既可以使用mylist[1]来访问第二个元素，也可以使用mylist.1来访问它。因此最佳实践：一律中括号形式









