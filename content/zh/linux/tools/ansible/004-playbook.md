---
title: 04. ansible-playbook
description: ansible-playbook
date: 2023-10-12
weight: 400


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

{{< /alert >}}
Playbook（剧本） 是 Ansible 指令的集合，其利用 YAML 语言编写，自上而下按顺序一次执行。

在前面介绍inventory的时候，我将它类比为演员表，在这里，我继续对playbook、play和task跟拍电影中的一些过程做个对比。

playbook译为剧本，就像电影、电视剧的剧本一样，剧本中记录了电影的每一片段应该怎么拍，包括：拍之前场景布置、拍之后的清场、每一个演员说什么话做什么动作、每一个演员穿什么样的衣服，等等。

Ansible的playbook也如此，电影的每一个片段可以对应于playbook中的每一个play，每一个play都可以有多个任务(tasks)，tasks可以对应于电影片段中的每一幕。所以，playbook可以用来组织多个任务，然后让这些任务统一执行，就像shell脚本组织多个命令一样，这种组织多个事件、多个任务的行为，有一个更高大上的术语：编排。

还可以继续更细致的对应起来。比如每一个play都可以定义自己的环境，比如play级别的变量，对应于电影片段的场景布置，每一个play都需要指定要执行该play的主机，即先确定好这个电影片段中涉及的演员，每一个Play可以有pre_tasks，对应于正式开拍之前的布置，每一个play可以有post_tasks，对应于拍完之后的清场。

而我们人，既是编写playbook的编剧，也是让playbook跑起来的导演。

简单总结一下`playbook`、`play`和`task`的关系：

1. `playbook`中可以定义一个或多个`play`
2. 每个`play`中可以定义一个或多个`task`
   1. 其中还可以定义两类特殊的`task`：`pre_tasks`和`post_tasks`
   2. `pre_tasks`表示执行执行普通任务之前执行的任务列表
   3. `post_tasks`表示普通任务执行完之后执行的任务列表
3. 每个`play`都需要通过`hosts`指令指定要执行该`play`的目标主机
4. 每个`play`都可以设置一些该`play`的环境控制行为，比如定义`play`级别的变量


参数说明：

- \-i：指定主机清单
- \-e：指定变量或者变量文件

```bash
ansible-playbook -i hosts -e "age=123" -e "@vars.yml" playbook.yml
```

- 检查palybook语法：`ansible-playbook -i hosts httpd.yml --syntax-check`

- 列出要执行的主机：`ansible-playbook -i hosts httpd.yml --list-hosts`

- 列出要执行得任务：`ansible-playbook -i hosts httpd.yml --list-tasks`



playbook文件 其主要有以下四部分构成:

- Target section： 定义将要执行 playbook 的远程主机组

- Variable section： 定义 playbook 运行时需要使用的变量

- Task section： 定义将要在远程主机上执行的任务列表

- Handler section： 定义 task 执行完成以后需要调用的任务


- playbook包含很多个play
  - play
    - name
    - hosts
    - vars
    - handles
    - gather_facts: false
    - tasks
      - name
      - module
- tasks中包含各个真正执行的module，如apt，copy，file， git， svn，service，command，notify，mysql等。具体的模块参数和使用文档在这里


**playbook执行顺序**：一个playbook的内容，是按照下列表依次执行的

- Variable loading 变量加载

- Fact gatherin 是否获取fact数据

- The pre_tasks execution 执行pre_tasks任务

- Handlers notified from the pre_tasks execution 执行pre_tasks任务里的事件通知

- Roles execution 执行roles角色

- Tasks execution 执行tasks任务

- Handlers notified from roles or tasks execution 执行tasks任务里的事件通知

- The post_tasks execution 执行post_tasks任务

- Handlers notified from post_tasks execution 执行post_tasks任务里的事件通知



### 在playbook中可以通过定义vars的方式来传参，那么也就可以通过文件的方式
> 参数的优先级：命令行>vars_files>vars>host_vars>group_vars>hosts

1. 直接用`vars`定义变量
```yaml
---
- hosts: all
  gather_facts: False
  vars:
    key: playbook_var
  tasks:
  - name: display Host Variable from hostfile
    debug: msg="The {{ inventory_hostname }} Value is {{ key }}"
```

2. 通过变量文件`vars_files`引入
```yaml
---
- hosts: all
  gather_facts: False
  vars_files:
    - var.yaml
  tasks:
  - name: display Host Variable from hostfile
    debug: msg="The {{ inventory_hostname }} Value is {{ key }}"
```




### 使用register内的变量

**作用**：在playbook里面，task之间可以互相传递结果。


```yaml
---
- hosts: all
  gather_facts: False
  tasks:
    - name: register variable
      shell: hostname
      register: info
    - name: display variable
      debug: msg="The variable is {{ info }}"
```

stdout代表输出。
我们可以看出，这个输出结果其实是一个字典，我们可以直接去里面的值：


```bash
[mysql@node70 rds-ansible]$ ansible-playbook test.yaml

PLAY [all] ********************************************************************************************************************************************************************************************************************

TASK [register variable] ******************************************************************************************************************************************************************************************************
changed: [controller-1]

TASK [display variable] *******************************************************************************************************************************************************************************************************
ok: [controller-1] =>
  msg: 'The variable is {''stderr_lines'': [], u''changed'': True, u''end'': u''2024-03-04 12:38:58.782636'', ''failed'': False, u''stdout'': u''node70'', u''cmd'': u''hostname'', u''rc'': 0, u''start'': u''2024-03-04 12:38:58.445758'', u''stderr'': u'''', u''delta'': u''0:00:00.336878'', ''stdout_lines'': [u''node70'']}'

PLAY RECAP ********************************************************************************************************************************************************************************************************************
controller-1               : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```



```yaml
---
- hosts: all
  gather_facts: False
  tasks:
    - name: register variable
      shell: hostname
      register: info

    - name: display variable
      debug: msg="The variable is {{ info['stdout'] }}"
```

```bash
[mysql@node70 rds-ansible]$ ansible-playbook test.yaml


PLAY [all] ********************************************************************************************************************************************************************************************************************

TASK [register variable] ******************************************************************************************************************************************************************************************************
changed: [controller-1]

TASK [display variable] *******************************************************************************************************************************************************************************************************
ok: [controller-1] =>
  msg: The variable is node70

PLAY RECAP ********************************************************************************************************************************************************************************************************************
controller-1               : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

### 一个playbook文件可以拥有多个play

```yaml
---
- hosts: webservers
  remote_user: root

  tasks:
  - name: ensure apache is at the latest version
    yum:
      name: httpd
      state: latest
  - name: write the apache config file
    template:
      src: httpd.j2
      dest: /etc/httpd/conf/httpd.conf

- hosts: databases
  remote_user: root

  tasks:
  - name: ensure postgresql is at the latest version
    yum:
      name: postgresql
      state: latest
  - name: ensure that postgresql is started
    service:
      name: postgresql
      state: started
```




###
- 指定主机：hosts:
- 指定主机执行的任务列表：tasks:
- 关闭收集主机信息：gather_facts: no




## 指定tag

```bash
ansible-playbook deploy.yml --tags os -v
```


