---
title: with_dict
description: with_dict
date: 2023-10-12
weight: 60000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

{{< /alert >}}

---

### 迭代一个字典结构
> with_dict用于迭代一个字典结构，迭代时可以使用item.key表示每个字典元素的key，item.value表示每个字典元素的value。


```yaml
---

- hosts: localhost
  gather_facts: false
  vars:
    users:
      junmajinlong_key:
        name: junmajinlong
        age: 18
      fairy_key:
        name: fairy
        age: 22
  tasks:
    - debug:
        msg: "who: {{item.key}} &&
              name: {{item.value.name}} &&
              age: {{item.value.age}}"
      with_dict: "{{users}}"
```
























