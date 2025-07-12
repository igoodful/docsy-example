---
title: in
description: 成员测试
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


## 1. 版本比较

Ansible提供了一个version测试函数可以用来测试版本号是否大于、小于、等于、不等于给定的版本号。



语法
```yaml
version('VERSION',CMP)
```

其中CMP可以是如下几种：
```bash
<, lt, <=, le, >, gt, >=, ge, ==, =, eq, !=, <>, ne
```


```yaml
{{ ansible_facts["distribution_version"] is version("7.5","<=") }}

```






