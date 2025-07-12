---
title: 03. inventory
description: inventory
date: 2023-10-12
weight: 300


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
在没有对Ansible做任何配置的时候，Ansible只能通过localhost来控制本机
**`inventory`的文件位置**：
- `/etc/ansible/ansible.cfg`配置文件默认情况下：`inventory= /etc/ansible/hosts`

**`inventory`有两种格式**
- 一种是`yaml`格式
- 一种是`ini`格式
- 建议使用 YAML 格式定义 Inventory 清单， YAML 格式可以很好的避免变量混淆的情况。 YAML 插件可以保证变量的一致性和正确性。

**分组**：
- all inventory的主机所有主机都归于all的默认分组里面
- 一个主机要么位于某个你定义的组中
- 要么属于Ungrouped的状态，也就是无家可归的状态

### ansible使用的inventory
1. /etc/ansible/ansible.cfg（`ansible.cfg`中配置的`inventory`位置优先级最低，默认值为：`inventory= /etc/ansible/hosts`，`/etc/ansible/hosts`可以是文件或目录，若是目录则加载目录里面的所有文件）
2. 环境变量：ANSIBLE_INVENTORY（优先级次之，会覆盖/etc/ansible/ansible.cfg）
3. ansible/ansible-playbook -i /data/inventory.yaml（优先级最高）



{{< /alert >}}

---

```yaml
all:
  hosts:
    192.168.1.1:
  children:
    web:
      hosts:
        192.168.1.1:
        192.168.2.1:
    db:
      hosts:
        192.168.3.1:
        192.168.3.2:
```


```ini
[db]
192.168.1.1
192.168.2.1
[app]
192.168.3.1
192.168.3.2
[dev:children]
app
db
```

参数说明：

- \-i：指定主机清单
- \-e：指定变量或者变量文件

- ansible_ssh_host     #用于指定被管理的主机的真实IP
- ansible_ssh_port     #用于指定连接到被管理主机的ssh端口号，默认是22
- ansible_ssh_user     #ssh连接时默认使用的用户名
- ansible_ssh_pass     #ssh连接时的密码
- ansible_sudo_pass     #使用sudo连接用户时的密码
- ansible_sudo_exec     #如果sudo命令不在默认路径，需要指定sudo命令路径
- ansible_ssh_private_key_file     #秘钥文件路径，秘钥文件如果不想使用ssh-agent管理时可以使用此选项
- ansible_shell_type     #目标系统的shell的类型，默认sh
- ansible_connection     #SSH 连接的类型： local , ssh , paramiko，在 ansible 1.2 之前默认是 paramiko ，后来智能选择，优先使用基于 ControlPersist 的 ssh （支持的前提）
- ansible_python_interpreter     #用来指定python解释器的路径，默认为/usr/bin/python 同样可以指定ruby 、perl 的路径
- ansible_*_interpreter     #其他解释器路径，用法与ansible_python_interpreter类似，这里"*"可以是ruby或才perl等其他语言

### 所有行为控制系统变量
| 配置项                       | 默认值          | 可能值             | 说明                                                                      |
|:-----------------------------|:----------------|:-------------------|:--------------------------------------------------------------------------|
| ansible_connection           | smart           | smart,ssh,paramiko | 连接受控主机的方式                                                        |
| ansible_host                 |                 |                    | 要连接的主机名，如果与您要给它提供的别名不同                               |
| ansible_port                 | 22              |                    | 连接对方的端口号                                                          |
| ansible_user                 |                 |                    | 连接远程主机的用户。不指定时，将使用执行ansible或ansible-playbook命令的用户 |
| ansible_password             |                 |                    | 连接时的用户的ssh密码，仅在未使用密钥对验证的情况下有效                    |
| ansible_ssh_private_key_file |                 |                    | 指定密钥认证ssh连接时的私钥文件                                           |
| ansible_ssh_pipelining       |                 |                    |                                                                           |
| ansible_ssh_executable       |                 |                    |                                                                           |
| ansible_ssh_common_args      |                 |                    | 提供给ssh,sftp,scp命令的额外参数,当为一台主机或组配置 ProxyCommand 时有用 |
| ansible_sftp_extra_args      |                 |                    | 此设置始终附加到默认sftp命令行                                            |
| ansible_scp_extra_args       |                 |                    | 此设置始终附加到默认scp命令行                                             |
| ansible_ssh_extra_args       |                 |                    | 此设置始终附加到默认ssh命令行                                             |
| ansible_shell_type           | sh              | sh,csh,fish        | 指定远程主机使用的 Shell                                                  |
| ansible_shell_executable     | /bin/sh         |                    | 设置远程主机使用何种 shell                                                |
| ansible_python_interpreter   | /usr/bin/python |                    | 用来指定python解释器的路径                                                |
| ansible_*_interpreter        |                 |                    | 其他解释器路径，这里"*"可以是ruby或才perl等其他语言                        |
| ansible_become               |                 |                    | 允许进行权限额外提升                                                      |
| ansible_become_method        |                 |                    | 指定提升权限的方式，列如可使用sudo/runas等方式                             |
| ansible_become_user          |                 |                    | 提升为哪个用户的权限，默认提升为root                                       |
| ansible_become_password      |                 |                    | 提升为指定用户权限时的密码                                                |
| ansible_become_exe           |                 |                    |                                                                           |
| ansible_become_flags         |                 |                    |                                                                           |
| ansible_sudo                 | root            |                    | 指定sudo连接用户                                                          |
| ansible_sudo_pass            |                 |                    | 使用sudo连接用户时的密码                                                  |
| ansible_sudo_exec            |                 |                    | 如果sudo命令不在默认路径，需要指定sudo命令路径                             |
|                              |                 |                    |                                                                           |
|                              |                 |                    |                                                                           |
|                              |                 |                    |                                                                           |
|                              |                 |                    |                                                                           |
|                              |                 |                    |                                                                           |











- `ansible_connection`：
  - local：部署应用到管理机自身
  - docker：该连接器使用本地Docker客户端将 Playbook 直接部署到 Docker 容器中。 此连接器处理以下参数
    - ansible_host：要连接的 Docker 容器名字
    - ansible_user：在容器内操作的用户名，用户必须存在于容器内
    - ansible_docker_extra_args: "--tlsverify --tlscacert=/path/to/ca.pem --tlscert=/path/to/client-cert.pem --tlskey=/path/to/client-key.pem -H=tcp://myserver.net:4243"







### 默认的文件路径为 /etc/ansible/hosts
> 写在机器名称下面一级即可
```ini
[test]
192.168.1.1 ansible_ssh_user=root ansible_ssh_pass='root'
192.168.1.2 ansible_ssh_user=mysql ansible_ssh_pass='mysql'
192.168.1.3 ansible_ssh_user=redis ansible_ssh_port=22 ansible_ssh_pass='redis'
[web]
192.168.1.1 https_port=8443 max_threads=8
```

```ini
[local]
localhost ansible_connection=local

[test]
192.168.34.3    ansible_connection=ssh          ansible_user=root       ansible_ssh_pass=123456

[all]
localhost       ansible_connection=local
192.168.34.3    ansible_connection=ssh          ansible_user=root       ansible_ssh_pass=123456
192.168.34.4    ansible_connection=ssh          ansible_user=root       ansible_ssh_pass=123456
```


## 变量
- 你可以直接在 Inventory 清单中定义的 host 或 group 变量。刚开始的时候，你可以直接添加 host 或 group 到 Inventory 文件中。
- 当你越加越多的时候，你可能会考虑将变量和 host group 分离成独立的文件。

**变量合并：**默认情况下，在运行之前变量会合并/展开到特定目标主机
- all group
- parent group
- child group
- host
- 默认，Ansible 按字母顺序合并处于相同父/子级别的组，后加载的组将覆盖先前的组。 a_group 将与 b_group 组合并，并且匹配的 b_group 组覆盖 a_group 。
- 您可以通过设置组变量 `ansible_group_priority` 来更改此行为，以更改同一级别的组的合并顺序（在解决父/子顺序之后）。 数字越大，合并的时间越晚，优先级越高。 如果未设置，则此变量默认为1。

```yaml
a_group:
    testvar: a
    ansible_group_priority: 10
b_group:
    testvar: b
```
> `ansible_group_priority` 只在 Inventory 中有效，在 `group_vars/` 无效， 因为该变量用于加载 `group_vars` 来设置变量变量优先级。



### 主机组内变量
> 组变量是一次将变量同时应用于多个主机的便捷方法
>
> 但是，在执行之前，Ansible始终将变量（包括 Inventory 清单变量）展平到主机级别
>
> 如果该主机是多个组的成员，则 Ansible 将从所有这些组中读取变量值
>
> 如果同一主机在不同的组中被赋予不同的变量值，则 Ansible 会根据内部规则来选择要使用的值。
>
> 与主机组名称同级别的`vars`关键字



如果您需要存储列表或哈希数据，或者更喜欢将主机和组特定变量与清单文件分开, 请参考 编排主机和组变量 子组有几个要注意的属性：

- 子组成员默认属于父组成员
- 子组的变量比父组的变量优先级高，即值会覆盖父组的变量。
- 组可以有多个父组或孩子，但不能循环关系。
- 主机也可以隶属于多个组中，但是只有 一个 主机实例，可以合并多个组中的数据。







通过`[组名:vars]`的方式定义组内变量
```ini
[test]
10.10.10.11
10.10.10.12
10.10.10.13
[test:vars]
https_port=8443
max_threads=8
```

> vars变量主要用在ansible-playbook中，不支持ad-hoc。
>
> 为一个组指定变量
```yaml
[db]
10.10.10.11
10.10.10.12
10.10.10.13
[atlanta:vars]
name=igoodful
age=1024
```




在 Playbook 中的示例 INI 文件的写法：
```ini
[atlanta]
host1 http_port=80 maxRequestsPerChild=808
host2 http_port=303 maxRequestsPerChild=909
```


YAML:
```yaml
atlanta:
  host1:
    http_port: 80
    maxRequestsPerChild: 808
  host2:
    http_port: 303
    maxRequestsPerChild: 909
```
比如给 host 添加非标准 SSH 端口，把端口直接添加到主机名后，心冒号分隔即可：`badwolf.example.com:5309`

connection 连接远程主机的方式:
```ini
[targets]

localhost              ansible_connection=local
other1.example.com     ansible_connection=ssh        ansible_user=myuser
other2.example.com     ansible_connection=ssh        ansible_user=myotheruser
```

### 在 Inventory 中定义别名：

In INI 中定义:
```ini
controller ansible_port=5555 ansible_host=192.0.2.50
```

在 YAML 中定义:
```yaml
  hosts:
    controller:
      ansible_port: 5555
      ansible_host: 192.0.2.50
```
在如上的示例中， 执行 `Ansible `对`controller`主机执行命令时，会连接 `192.0.2.50` 的 `5555` 端口。 这种方式仅适用于通过静态 IP 的主机，或者通过隧道连接的主机。



### 主机组变量

```yaml
all:
  hosts:
    192.168.1.1:
  children:
    web:
      hosts:
        192.168.1.1:
        192.168.2.1:
    db:
      hosts:
        192.168.3.1:
        192.168.3.2:
```




###
`/etc/ansible/host_vars/hostname.yaml`

`/etc/ansible/group_vars/groupname.yaml`


### ini格式

```ini
10.10.10.11
[web]
10.10.10.12
10.10.10.13
[db]
10.10.10.14
10.10.10.15
```




### yaml格式

```yaml
all:
  hosts:
    10.10.10.11:
  children:
    web:
      hosts:
        10.10.10.12:
        10.10.10.13:
    db:
      hosts:
        10.10.10.14:
        10.10.10.15:
        10.10.10.12:
```

**分组名称**：
- all（默认分组名称）
- web（all分组下面的一个分组，在`children`下一级）
- db（all分组下面的一个分组，在`children`下一级）

**主机列表**：
> 分组下面的主机列表关键字
- hosts（每个具体的机器都在`hosts`的下一级）

**子分组列表**：

- children（在分组的下一级，与改分组的hosts同级）

```yaml
all:
  hosts:
    10.10.10.11:
  children:
    web:
      hosts:
        10.10.10.12:
        10.10.10.13:
    db:
      hosts:
        10.10.10.14:
        10.10.10.15:
      children:
      web:
```






1. 一台机器可以分到多个分组里面

```yaml
all:
  hosts:
  children:
    web:
      hosts:
        10.10.10.11:
        10.10.10.12:
    db:
      hosts:
        10.10.10.11:
        10.10.10.13:
        10.10.10.14:
```





指定Server的连接参数，其中包括连接方法，用户等。
```yaml
[db]
10.10.10.11     ansible_connection=local
10.10.10.12     ansible_connection=ssh        ansible_user=mysql
10.10.10.13     ansible_connection=ssh        ansible_user=root
[web]
host1 http_port=80 maxRequestsPerChild=808
host2 http_port=303 maxRequestsPerChild=909
```



### 远程主机连接参数

- 远程主机登陆端口：`ansible_ssh_port`
- 远程主机登陆用户名：`ansible_ssh_user`
- 远程主机登陆用户名的密码：`ansible_ssh_pass`
- 跳过第一次连接检测询问是否登陆的提示：`host_key_checking`
- 指定管理主机群列表文件：`ansible_ssh_private_key_file=/etc/ansible/hosts`


### group_vars
> 首先需要说明的通过这种方式定义的文件名称需要与hosts中定义的主机和主机组的名称一致







### host_vars
> 首先需要说明的通过这种方式定义的文件名称需要与hosts中定义的主机和主机组的名称一致












