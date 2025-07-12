---
title: 过滤器
description: filter
date: 2023-10-31
weight: 2000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


{{< alert  color="primary">}}

筛选器是一个表达式，所以写在 {{}} 内部。

{{< /alert >}}


## 一、语法特点

inja2的筛选器使用方式非常简单，直接使用一根竖线|，在模板解析时，Jinja2会将竖线左边的返回值或计算结果当作隐式参数传递给竖线右边的筛选器函数。

```yaml
- debug:
    msg: "{{'HELLO WORLD' |lower()}}"

{% if result %}
{{result |replace('no', 'yes')}}
{%endif%}






```



