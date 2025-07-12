---
title: 循环语句
description: for
date: 2023-10-31
weight: 2000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
- [yaml](https://www.cnblogs.com/nf01/articles/15686885.html)
{{< /alert >}}


{{< alert  color="primary">}}

{{< /alert >}}


## 一、语法特点

- loop.index 当前循环迭代的次数（从 1 开始）
- loop.index0 当前循环迭代的次数（从 0 开始）
- loop.revindex 到循环结束需要迭代的次数（从 1 开始）
- loop.revindex0 到循环结束需要迭代的次数（从 0 开始）
- loop.first 如果是第一次迭代，为 True 。
- loop.last 如果是最后一次迭代，为 True 。
- loop.length 序列中的项目数。
- loop.cycle 在一串序列间期取值的辅助函数。


```jinja
{% if users %}
<ul>
{% for user in users %}
    <li>{{ user.username|e }}</li>
{% endfor %}
</ul>
{% endif %}
```

```jinja
{% if kenny.sick %}
    Kenny is sick.
{% elif kenny.dead %}
    You killed Kenny!  You bastard!!!
{% else %}
    Kenny looks okay --- so far
{% endif %}
```



```jinja
{% extends layout_template if layout_template is defined else 'master.html' %}
```

```jinja
{{ '[%s]' % page.title if page.title }}
```



