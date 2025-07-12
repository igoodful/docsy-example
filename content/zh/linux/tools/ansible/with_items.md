---
title: with_items
description: with_items
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

### 标准循环: `with_items`
> 有时候列表中会嵌套列表，而with_list不能，可完全替换with_list
```yaml
#循环一个值
- hosts: all
  become: yes
  tasks:
    - name: 创建用户
      user: name={{ item }}
      with_items:
      - name1
      - name2
#循环数组变量，与上面示例效果一致
- hosts: all
  become: yes
  vars:
    user_name:
    - name1
    - name1
  tasks:
    - name: 创建用户
      user: name={{ item }}
      with_items: "{{ user_name }}"
#循环多个值
#测试连接
- hosts: all
  become: yes
  tasks:
    - name: 创建用户
      user: name={{ item.name }} state=present groups={{ item.groups }}
      with_items:
      - { name: 'testuser1', groups: 'wheel' }
      - { name: 'testuser1', groups: 'wheel' }
```


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






















