---
title: with_nested
description: with_nested
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



### 嵌套循环: `with_nested`


```yaml
#创建目录并且修改权限
- hosts: all
  become: yes
  tasks:
    - name: 创建文件
      file: path=/opt/{{ item[0] }} owner={{ item[1] }}  state=directory
      with_nested:
        - [ 'test-1', 'test-2' ]
        - [ 'name1', 'name2', ]
#最后文件的所属用户为name2，在test-1与test-2目录上面都会循环2次修改权限的操作
```






















