---
title: 条件语句
description: if
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



```yaml
{% if users %}
<ul>
{% for user in users %}
    <li>{{ user.username|e }}</li>
{% endfor %}
</ul>
{% endif %}
```

```yaml
{% if kenny.sick %}
    Kenny is sick.
{% elif kenny.dead %}
    You killed Kenny!  You bastard!!!
{% else %}
    Kenny looks okay --- so far
{% endif %}
```



```yaml
{% extends layout_template if layout_template is defined else 'master.html' %}
```

```yaml
{{ '[%s]' % page.title if page.title }}
```



