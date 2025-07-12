---
title: when
description: when
date: 2023-10-12
weight: 60000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
[ansible](https://zhangzhuo.ltd/articles/2022/05/03/1651591339922.html)
{{< /alert >}}

---

### 变量相关


- ==：等于
- !=：不等于
- in: 包含
- not in：不包含
- is not defined：判断变量没有定义
- is defined：判断变量已经定义

```yaml
- hosts: all
  vars:
    test: "10"
  tasks:
    - name: 判断test变量是否大于10
      debug:
        msg: "{{ test }}"
      when: test | int > 10   #整数数值比较需要int转义
    - name: 判断变量是为1,2,3,10中一个值
      debug:
        msg: "{{ test }}"
      when: test in ["1","2","3","10"]
    - name: 判断变量不是为1,2,3中一个值
      debug:
        msg: "{{ test }}"
      when: test not in ["1","2","3"]
    - name: 判断变量是否没定义
      debug:
        msg: "{{ test }}"
      when: test is not defined
    - name: 判断变量已经定义
      debug:
        msg: "{{ test }}"
      when: test is defined
```


### 多个条件判断

- and：与关系
- or：或关系
- ()：可组合多个条件

```yaml
- hosts: all
  tasks:
    - name: 判断操作系统版本
      debug:
        msg: "{{ ansible_distribution_major_version }}"
      when: ansible_distribution_major_version == '7'   #单个条件判断
    - name: 判断主机是否属于test组
      debug:
        msg: "这个主机属于test组"
      when: "inventory_hostname in groups['test']"   #判断一个主机是否属于一个组中
    - name: 判断主机是centos7操作系统1
      debug:
        msg: "{{ ansible_distribution }}{{ ansible_distribution_major_version }}"
      when: ansible_distribution_major_version == '7' and ansible_distribution == 'CentOS'  #多个条件与判断1
    - name: 判断主机是centos7操作系统2
      debug:
        msg: "{{ ansible_distribution }}{{ ansible_distribution_major_version }}"
      when:                             #多个条件与判断2
      - ansible_distribution_major_version == '7'
      - ansible_distribution == 'CentOS'
    - name: 判断系统版本为7或者系统为Centos
      debug:
        msg: "{{ ansible_distribution }}{{ ansible_distribution_major_version }}"
      when: ansible_distribution_major_version == '7' or ansible_distribution == 'CentOS' #多个条件或
    - name: 判断系统版本为centos7或者centos6
      debug:
        msg: "{{ ansible_distribution }}{{ ansible_distribution_major_version }}"
      when: ( ansible_distribution_major_version == '7' or ansible_distribution == 'CentOS' ) or ( ansible_distribution_major_version == '6' or ansible_distribution == 'CentOS' )  #多个条件或
```



## 比较

#### ==



#### !=



#### >



#### >=

```yaml
- name: Multiple conditions
    dnf:
      pkg: epel-release
      state: present
    when: ansible_distribution_major_version | int >= 8

```


#### <



#### <=


## 变量是否定义

#### is defined



#### is not defined


## 列表是否包含指定项

#### in



#### not in




#### and

```yaml
- name: Logical AND using List syntax
  dnf:
    pkg: htop
    state: present
  when: ansible_distribution | lower == "rocky" and ansible_distribution_major_version == "9"

```

```yaml
- name: Logical AND using List syntax
  dnf:
    pkg: htop
    state: present
  when:
    - ansible_distribution | lower == "rocky"
    - ansible_distribution_major_version == "9"

```


#### or


```yaml
- name: Multiple conditions
    dnf:
      pkg: epel-release
      state: present
    when: (ansible_distribution | lower == "centos" or ansible_distribution | lower == "rocky") and ansible_distribution_major_version | int >= 8

```



#### not







