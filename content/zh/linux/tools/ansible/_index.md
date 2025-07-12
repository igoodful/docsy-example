---
title: ansible
description: Ansible默认使用非交互式Shell
date: 2023-10-12
weight: 200

---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

---



{{< alert >}}
Ansible是一个基于Python开发的配置管理和应用部署工具，现在也在自动化管理领域大放异彩
- Ansible的另一个比较鲜明的特性是它的绝大多数模块都具备幂等性(idempotence)。所谓幂等性，指的是多次操作或多次执行不影响结果，重复执行某个任务绝大多数时候不会产生任何副作用
- Ansible的作用是批量控制其它远程主机，并指挥远程主机节点做一些操作、完成一些任务
- Ansible的模块是用Python来执行的，且默认远程连接的方式是`ssh`，所以控制端和被控制端都需要有`Python`环境，并且被控制端需要启动`sshd`服务，但通常这两个条件在安装Linux系统时就已经具备了
- 所以使用Ansible的安装过程只有一个：在控制端安装`Ansible`
- 默认所有主机上都已启动sshd服务并监听在22端口上。

**参考文档**：
- [源码](https://github.com/ansible/ansible)
- [官方文档](https://docs.ansible.com/)
- [骏马金龙](https://www.junmajinlong.com/ansible/1_how_mastering_ansible/)
- [官网](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/)

{{< /alert >}}


安装大型软件或服务：

1. 确定好哪些是全局变量：因为某个任务失败了，重新执行时就需要省略安装好的服务，否则就会破坏已经安装好的服务
2. 一个服务安装不成功和成功，都能完成重复安装
3. 一个软件安装前尽量检查条件全面才安装，今早解决安装条件




```bash
# 本地测试模块
ansible localhost -m shell -a 'ls'
```



### 控制节点：control node
安装`ansible`的节点，所以在这个结构中，分为控制节点和被控制节点。Ansible是Agentless的软件，只需在控制节点安装Ansible，被控制节点一般不需额外安装任何程序，就像一个普通的命令一样，随装随用。


### 被ansible管理的节点：managed node

- 无需安装`ansible`的节点
- 只需与`control node`通信


### 主机清单文件：inventory
> 分组
> `children`定义子分组
控制节点如何知道需要控制哪些节点呢？
- /etc/ansible/ansible.cfg


### ansible执行任务：run


### module

真正执行操作的是Ansible的模块,比如copy模块用于拷贝文件到远程主机上，service模块用于管理服务的启动、停止、重启等。这有点像Shell，Shell自身提供的操作是很有限的


#### ad-hoc
一次立即执行

**格式**：`ansible [pattern] -m [module]  -a "module options"`
- `pattern` 指定主机或主机组，主机或主机组的定义在`inventory`文件中定义
- `module` 指定`ansible`的哪个模块，默认是`cmd`模块
- 指定`ansible`模块的参数

```bash
ansible web -a "/sbin/reboot"

ansible db -m ansible.builtin.copy -a "src=/etc/hosts dest=/tmp/hosts"
```

缺点：
- 一次只能执行一个任务



#### playbook
优点：
- 一次能执行多个任务
- `playbook` 文件中的任务只有前面一个任务执行成功了，后面一个任务才会执行

##### 一、编写 `playbook` 文件

`cat playbook.yaml`：
```yaml
- name: igoodful play
  hosts: web
  tasks:
    - name: ping my hosts
      ansible.builtin.ping:
    - name: print message
      ansible.builtin.debug:
        msg: Hello world
```



##### 二、执行 `playbook` 文件

```bash
ansible-playbook -i inventory.yaml playbook.yaml
```

- `-i inventory.yaml` 指定在哪些主机上执行`ansible-playbook`的任务`playbook.yaml`
- `playbook.yaml` 指定在`inventory.yaml`确定的主机上执行什么任务



##### 三、输出 `playbook` 执行结果



















