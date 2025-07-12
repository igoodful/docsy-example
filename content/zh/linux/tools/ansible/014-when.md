---
title: 14. when
description: when
date: 2023-10-12
weight: 1400


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



| 符号                | 作用对象                               | 功能           | 示例                                                  |
|:--------------------|:---------------------------------------|:---------------|:------------------------------------------------------|
| `==`                | 字符串和数字                           | 是否相等       | `ansible_os_family == "RedHat"`                       |
| `!=`                |                                        | 是否不等       |                                                       |
| `<`                 |                                        |                |                                                       |
| `<=`                |                                        |                | `max_memory <= 512`                                   |
| `>`                 |                                        |                |                                                       |
| `>=`                |                                        |                |                                                       |
| `version`           |                                        |                |                                                       |
| `and`               |                                        |                |                                                       |
| `or`                |                                        |                |                                                       |
| `not`               |                                        |                |                                                       |
| `()`                |                                        |                |                                                       |
| `is exists`         |                                        | 路径是否存在   |                                                       |
| `is not exists`     |                                        | 路径是否不存在 |                                                       |
| `is defined`        |                                        |                |                                                       |
| `is undefined`      |                                        |                |                                                       |
| `is none`           |                                        |                |                                                       |
| `success/succeeded` | 判断执行结果                           |                |                                                       |
| `failure/failed`    | 判断执行结果                           |                |                                                       |
| `change/changed`    | 判断执行结果                           |                |                                                       |
| `skip/skipped`      | 判断执行结果                           |                |                                                       |
| `is file`           | 路径是否为一个文件                     |                |                                                       |
| `is directory`      | 路径是否为一个目录                     |                |                                                       |
| `is link`           | 是否为一个软链接                       |                |                                                       |
| `is mount`          | 是否为一个挂载点                       |                |                                                       |
| `is lower`          | 是否都是小写                           |                |                                                       |
| `is upper`          | 是否都是大写                           |                |                                                       |
| `in`                | 判断一个字符串是否存在于另一个字符串中 |                |                                                       |
| `is even`           | 是否为偶数                             |                |                                                       |
| `is odd`            | 是否为奇数                             |                |                                                       |
| version             |                                        |                | `ansible_distribution_version is version("7.3","gt")` |
|                     |                                        |                |                                                       |
|                     |                                        |                |                                                       |
|                     |                                        |                |                                                       |
|                     |                                        |                |                                                       |
|                     |                                        |                |                                                       |
|                     |                                        |                |                                                       |
|                     |                                        |                |                                                       |






version("版本号","比较操作符")中使用的比较运算符说明：

- 大于：>, gt
- 大于等于：>=, ge
- 小于：<, lt
- 小于等于：<=, le
- 等于：=, ==, eq
- 不等于：!=, <>, ne








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



## local_action


在大多数情况下，我们使用Ansible来管理和配置来自单一控制节点的远程主机。因此，在创建playbook时，我们的理解是，它们应在我们在playbook的主机块中指定的远程机器上执行。

然而，在有些情况下，你可能需要在本地机器上而不是在远程主机上执行一个特定的动作。在这种情况下，像local_action这样的功能就很有用了。

另一方面，Local_action将只在本地机器上执行设定的任务。它类似于将 delegate_to 的值设置为 localhost 或 127.0.0.1


## delegate_to


当你需要设置一个任务在特定的主机上执行时，delegate_to很有用。你可以通过指定主机名或IP地址将delegate_to模块指向目标主机

```yaml
- name: take out of load balancer pool
  command: /usr/bin/take_out_of_pool {{ inventory_hostname }}
  delegate_to: 127.0.0.1

# 上面的task会在跑ansible的机器上执行, “delegate_to: 127.0.0.1” 可以用local_action来代替
- name: take out of load balancer pool
  local_action: command /usr/bin/take_out_of_pool {{ inventory_hostname }}

```

- 只能任务级别



## delegate_facts
默认情况下, 委托任务的facts是inventory_hostname中主机的facts, 而不是被委托机器的facts. 在ansible 2.0 中, 设置delegate_facts为true可以让任务去收集被委托机器的facts.

```yaml
- hosts: app_servers
  tasks:
    - name: gather facts from db servers
      setup:
      delegate_to: "{{item}}"
      delegate_facts: True
      with_items: "{{groups['dbservers'}}"

```

## run_once
确保指定任务只执行一次，且只能在某一台机器上执行

通过run_once: true来指定该task只能在某一台机器（具体哪一台，可自定义）上执行一次. 可以和delegate_to 结合使用

```yaml
# 通过在任务上配置run_once来实现当前任务在play中只执行一次
# 下面的例子，将只会在第一个执行主机上执行，其余的主机则不执行。
- command: ifconfig
  run_once: true


- command: /opt/application/upgrade_db.py
  run_once: true
  delegate_to: web01.example.org

- name: run the task locally, only once
  command: /opt/my-custom-command
  run_once: true
  delegate_to: app.a1-61-105.dev.unp

- command: /opt/scripts/backup.sh
  run_once: true
  delegate_to: 192.168.1.111   #可以结合delegate_to（任务委派功能），表示只在192.168.1.111这台主机上面执行。如果不加这句话，第一个执行的主机会负责执行此条task。


```
指定在"web01.example.org"上执行这

如果没有delegate_to, 那么这个task会在第一台机器上执行



## failed_when


```yaml
#
- name: try docker with rds_user
  command: "sudo docker ps"
  register: docker_msg
  become: yes
  become_user: "{{ rds_user }}"
  failed_when: no

```







