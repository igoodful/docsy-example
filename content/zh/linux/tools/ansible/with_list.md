---
title: with_list
description: with_list
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

### 简单列表循环: `with_list`
> with_list用于迭代简单列表
```yaml
---
- hosts: localhost
  gather_facts: false
  tasks:
    - file:
        name: "/tmp/{{item}}"
        state: touch
      with_list:
        - "filename1"
        - "filename2"
```


等价loop
```yaml
- file:
    name: "/tmp/{{item}}"
    state: touch
  loop:
    - "filename1"
    - "filename2"
```


















