---
title: 15. loop
description: loop
date: 2023-10-12
weight: 1500


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

{{< /alert >}}

---

## 一、loop





## 二、with_items



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


## 三、with_nested


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


## 四、with_list



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

## 五、with_dict

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



