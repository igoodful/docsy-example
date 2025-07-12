---
title: 11. 基础
description: shell、command、script、debug、setup
date: 2023-10-12
weight: 1100


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}
- [官网](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/)
{{< /alert >}}

{{< alert color="primary" >}}

ansible的输出的颜色提示说明:

黄色：对远程节点进行相应修改

绿色：对远程节点不进行相应修改，或者只是对远程节点信息进行查看

红色：操作命令有异常

紫色：表示对命令执行发出警告信息会给一下建议

{{< /alert >}}


```viz-dot
digraph "基础" {
        rankdir=LR;
node [
        fontsize = "12";
        shape = octagon;
];
"基础"->"command";
"基础"->"shell";
"基础"->"script";
"基础"->"ping";
"基础"->"pause";
"基础"->"debug";
"基础"->"fail";
"基础"->"setup";
}
```



## 一、command

**注意**：
1. 不能使用管道: |
2. 不能使用重定向: > >> < <<
3. 不能使用后台运行符号：&
4. 不等幂
5. 若需要执行多个命令，尽量将多个命令用&&连接起来
6. 是默认模块，可配置

#### 1. 选项


| 选项              | 默认值 | 作用                                |
|:------------------|:-------|:----------------------------------|
| chdir             |        | 在运行命令之前更改到此目录          |
| cmd               |        | 要运行的命令                        |
| creates           |        | 当指定的文件不存在时，就执行对应命令 |
| removes           |        | 当指定的文件存在时，就执行对应命令   |
| argv              |        | 属组                                |
| stdin             |        | 指定命令的stdin                     |
| stdin_add_newline | yes    | 是否将换行符附加到标准输入数据      |
| strip_empty_ends  | yes    | 是否去掉stdout/stderr结果集的空行   |






#### 2. 示例
```yaml
- name: run all
  become: yes
  ansible.builtin.command:
    cmd: cat /etc/motd
    args:
      chdir: /data/mysql/
    argv:
      - -v
      - --debug
  register: output


- name: Return motd to registered var
  ansible.builtin.command: cat /etc/motd
  register: mymotd

# free-form (string) arguments, all arguments on one line
- name: Run command if /path/to/database does not exist (without 'args')
  ansible.builtin.command: /usr/bin/make_database.sh db_user db_name creates=/path/to/database

# free-form (string) arguments, some arguments on separate lines with the 'args' keyword
# 'args' is a task keyword, passed at the same level as the module
- name: Run command if /path/to/database does not exist (with 'args' keyword)
  ansible.builtin.command: /usr/bin/make_database.sh db_user db_name
  args:
    creates: /path/to/database

# 'cmd' is module parameter
- name: Run command if /path/to/database does not exist (with 'cmd' parameter)
  ansible.builtin.command:
    cmd: /usr/bin/make_database.sh db_user db_name
    creates: /path/to/database

- name: Change the working directory to somedir/ and run the command as db_owner if /path/to/database does not exist
  ansible.builtin.command: /usr/bin/make_database.sh db_user db_name
  become: yes
  become_user: db_owner
  args:
    chdir: somedir/
    creates: /path/to/database

# argv (list) arguments, each argument on a separate line, 'args' keyword not necessary
# 'argv' is a parameter, indented one level from the module
- name: Use 'argv' to send a command as a list - leave 'command' empty
  ansible.builtin.command:
    argv:
      - /usr/bin/make_database.sh
      - Username with whitespace
      - dbname with whitespace
    creates: /path/to/database

- name: Run command using argv with mixed argument formats
  ansible.builtin.command:
    argv:
      - /path/to/binary
      - -v
      - --debug
      - --longopt
      - value for longopt
      - --other-longopt=value for other longopt
      - positional

- name: Safely use templated variable to run command. Always use the quote filter to avoid injection issues
  ansible.builtin.command: cat {{ myfile|quote }}
  register: myoutput
```

#### 3. 输出

| 选项         | 默认值 | 作用                           |
|:-------------|:-------|:-----------------------------|
| cmd          |        | 任务执行的命令                 |
| rc           |        | 命令返回码，0表示成功           |
| stdout       |        | 命令标准输出                   |
| stdout_lines |        | 命令标准输出按行分割           |
| stderr       |        | 命令标准错误                   |
| stderr_lines |        | 命令标准错误按行拆分           |
| start        |        | 命令执行开始时间               |
| end          |        | 命令执行结束时间               |
| delta        |        | 命令执行增量时间               |
| msg          |        | 是否将换行符附加到标准输入数据 |




## 二、shell

#### 1. 选项

| 选项              | 默认值 | 作用                                |
|:------------------|:-------|:----------------------------------|
| chdir             |        | 在运行命令之前更改到此目录          |
| cmd               |        | 要运行的命令                        |
| creates           |        | 当指定的文件不存在时，就执行对应命令 |
| removes           |        | 当指定的文件存在时，就执行对应命令   |
| stdin             |        | 指定命令的stdin                     |
| stdin_add_newline | yes    | 是否将换行符附加到标准输入数据      |


1. 能使用管道
2. 能使用重定向
3. 不等幂
4. 若需要执行多个命令，尽量将多个命令用&&连接起来

#### 2. 示例
```yaml

- name: Exec single shell command # 执行单条命令
  ansible.builtin.shell: echo "1"
- name: Exec multi commands using && # 通过&&连接执行多条命令
  ansible.builtin.shell: echo "1" && echo "2"

# 忽略错误
- name: Exec shell and ignore errors
  ansible.builtin.shell: echo "ignore_errors" && exit 1
  ignore_errors: true
```



> `/bin/bash -c 'xxx'`

```yaml
# mv node_exporter
- name: mv node_exporter
  become: yes
  ansible.builtin.shell:
    cmd: "/bin/bash -c 'mv {{ install_tmpdir }}/node_exporter-{{ node_exporter_version }}.linux-*/node_exporter {{ node_exporter_base_dir }}/bin/'"
```

```yaml
- name: Execute the command in remote shell; stdout goes to the specified file on the remote
  ansible.builtin.shell: somescript.sh >> somelog.txt

- name: Change the working directory to somedir/ before executing the command
  ansible.builtin.shell: somescript.sh >> somelog.txt
  args:
    chdir: somedir/

# You can also use the 'args' form to provide the options.
- name: This command will change the working directory to somedir/ and will only run when somedir/somelog.txt doesn't exist
  ansible.builtin.shell: somescript.sh >> somelog.txt
  args:
    chdir: somedir/
    creates: somelog.txt

# You can also use the 'cmd' parameter instead of free form format.
- name: This command will change the working directory to somedir/
  ansible.builtin.shell:
    cmd: ls -l | grep log
    chdir: somedir/

- name: Run a command that uses non-posix shell-isms (in this example /bin/sh doesn't handle redirection and wildcards together but bash does)
  ansible.builtin.shell: cat < /tmp/*txt
  args:
    executable: /bin/bash

- name: Run a command using a templated variable (always use quote filter to avoid injection)
  ansible.builtin.shell: cat {{ myfile|quote }}

# You can use shell to run other executables to perform actions inline
- name: Run expect to wait for a successful PXE boot via out-of-band CIMC
  ansible.builtin.shell: |
    set timeout 300
    spawn ssh admin@{{ cimc_host }}

    expect "password:"
    send "{{ cimc_password }}\n"

    expect "\n{{ cimc_name }}"
    send "connect host\n"

    expect "pxeboot.n12"
    send "\n"

    exit 0
  args:
    executable: /usr/bin/expect
  delegate_to: localhost
```




```yaml
- name: shell
  shell:
    cmd: ls /home
```
执行结果
```yaml
changed: [node70] => changed=true
  cmd: ls /home
  delta: '0:00:00.397641'
  end: '2024-05-03 14:51:29.629336'
  rc: 0
  start: '2024-05-03 14:51:29.231695'
  stderr: ''
  stderr_lines: <omitted>
  stdout: |-
    centos
    db
    igoodful
    mysql
  stdout_lines: <omitted>

```



## 三、script

1. 此模块不具有幂等性

#### 1. 选项

| 选项    | 默认值 | 作用                                |
|:--------|:-------|:----------------------------------|
| chdir   |        | 在运行命令之前更改到此目录          |
| cmd     |        | 要运行的命令                        |
| creates |        | 当指定的文件不存在时，就执行对应命令 |
| removes |        | 当指定的文件存在时，就执行对应命令   |




#### 2. 示例

```yaml
- name: Run a script with arguments (free form)
  ansible.builtin.script: /some/local/script.sh --some-argument 1234

- name: Run a script with arguments (using 'cmd' parameter)
  ansible.builtin.script:
    cmd: /some/local/script.sh --some-argument 1234

- name: Run a script only if file.txt does not exist on the remote node
  ansible.builtin.script: /some/local/create_file.sh --some-argument 1234
  args:
    creates: /the/created/file.txt

- name: Run a script only if file.txt exists on the remote node
  ansible.builtin.script: /some/local/remove_file.sh --some-argument 1234
  args:
    removes: /the/removed/file.txt

- name: Run a script using an executable in a non-system path
  ansible.builtin.script: /some/local/script
  args:
    executable: /some/remote/executable

- name: Run a script using an executable in a system path
  ansible.builtin.script: /some/local/script.py
  args:
    executable: python3

- name: Run a Powershell script on a Windows host
  script: subdirectories/under/path/with/your/playbook/script.ps1
```

## 四、ping

#### 示例
```yaml
- name: Example from an Ansible Playbook
  ansible.builtin.ping:

- name: Induce an exception to see what happens
  ansible.builtin.ping:
    data: crash
```










## 五、pause

#### 1. 选项
| 选项    | 默认值 | 作用                       |
|:--------|:-------|:-------------------------|
| echo    |        | 在运行命令之前更改到此目录 |
| prompt  |        | 打印一串信息提示用户操作   |
| minutes |        | 暂停多少分钟               |
| seconds |        | 暂停多少秒                 |

#### 2. 示例
```yaml
- name: Pause for 5 minutes to build app cache
  ansible.builtin.pause:
    minutes: 5

- name: Pause until you can verify updates to an application were successful
  ansible.builtin.pause:

- name: A helpful reminder of what to look out for post-update
  ansible.builtin.pause:
    prompt: "Make sure org.foo.FooOverload exception is not present"

- name: Pause to get some sensitive input
  ansible.builtin.pause:
    prompt: "Enter a secret"
    echo: no
```

## 六、debug

```viz-dot
digraph "debug" {
        rankdir=TB;
node [
        fontsize = "12";
        shape = octagon;
];
"debug"->"msg";
"debug"->"var";
"debug"->"verbosity";

}
```

#### 1. 选项
| 选项      | 默认值      | 作用                                                |
|:----------|:------------|:--------------------------------------------------|
| msg       | hello world | 打印的自定义消息。此处变量要用双大括号               |
| var       |             | 指定要打印的变量名,与msg互斥，此处变量不能用双大括号 |
| verbosity | 0           | debug的调试级别，默认0是全部显示                     |


#### 2. 示例
```bash
# msg
ansible localhost -m debug -a 'msg="hello world"'

# msg
ansible localhost -e 'str=world' -m debug -a 'msg="hello {{str}}"'

# var
ansible localhost -e 'str="hello world"' -m debug -a 'var=str'

# var
ansible localhost -e 'str="hello world"' -m debug -a 'var1,var2,var3'
```


## 七、fail

#### 1. 选项
```viz-dot
digraph "fail" {
        rankdir=LR;
node [
        fontsize = "12";
        shape = octagon;
];
"fail"->"msg";

}
```



#### 2. 示例

```yaml
- name: "Example using fail and when together"
  ansible.builtin.fail:
    msg: "The system may not be provisioned according to the CMDB status."
  when: cmdb_status != "to-be-staged"
```




## 八、setup


```viz-dot
digraph "setup" {
        rankdir=LR;
node [
        fontsize = "12";
];

    "ansible_facts" [ label = "ansible_facts", shape = octagon ];

    "ansible_env" [ label = "ansible_env", shape = octagon ];
    "ansible_hostname" [ label = "ansible_hostname", shape = octagon ];
    "ansible_nodename" [ label = "ansible_nodename", shape = octagon ];
    "ansible_default_ipv4" [ label = "ansible_default_ipv4", shape = octagon ];
    "ansible_default_ipv6" [ label = "ansible_default_ipv6", shape = octagon ];
    "ansible_all_ipv4_addresses" [ label = "ansible_all_ipv4_addresses", shape = octagon ];
    "ansible_all_ipv6_addresses" [ label = "ansible_all_ipv6_addresses", shape = septagon ];

    "ansible_pkg_mgr" [ label = "ansible_pkg_mgr", shape = octagon ];
    "ansible_python" [ label = "ansible_python", shape = octagon ];
    "ansible_python_version" [ label = "ansible_python_version", shape = octagon ];
    "ansible_selinux" [ label = "ansible_selinux", shape = octagon ];

    "ansible_architecture" [ label = "ansible_architecture", shape = septagon ];
    "ansible_os_family" [ label = "ansible_os_family", shape = octagon ];
    "ansible_system" [ label = "ansible_system", shape = octagon ];
    "ansible_distribution" [ label = "ansible_distribution", shape = septagon ];
    "ansible_distribution_version" [ label = "ansible_distribution_version", shape = septagon ];
    "ansible_distribution_major_version" [ label = "ansible_distribution_major_version", shape = septagon ];

    "ansible_kernel" [ label = "ansible_kernel", shape = octagon ];
    "ansible_machine" [ label = "ansible_machine", shape = octagon ];
    "ansible_machine_id" [ label = "ansible_machine_id", shape = octagon ];
    "ansible_processor" [ label = "ansible_processor", shape = octagon ];
    "ansible_processor_cores" [ label = "ansible_processor_cores", shape = octagon ];
    "ansible_processor_count" [ label = "ansible_processor_count", shape = octagon ];
    "ansible_processor_vcpus" [ label = "ansible_processor_vcpus", shape = octagon ];
    "ansible_processor_threads_per_core" [ label = "ansible_processor_threads_per_core", shape = octagon ];
    "ansible_memory_mb" [ label = "ansible_memory_mb", shape = octagon ];
    "ansible_memfree_mb" [ label = "ansible_memfree_mb", shape = octagon ];
    "ansible_memtotal_mb" [ label = "ansible_memtotal_mb", shape = octagon ];
    "ansible_mounts" [ label = "ansible_mounts", shape = octagon ];
    "ansible_devices" [ label = "ansible_devices", shape = septagon ];


    "ansible_facts" -> "ansible_env" ;
    "ansible_facts" -> "ansible_hostname" ;
    "ansible_facts" -> "ansible_nodename" ;
    "ansible_facts" -> "ansible_default_ipv4" ;
    "ansible_facts" -> "ansible_default_ipv6" ;
    "ansible_facts" -> "ansible_all_ipv4_addresses" ;
    "ansible_facts" -> "ansible_all_ipv6_addresses" ;
    "ansible_facts" -> "ansible_pkg_mgr" ;
    "ansible_facts" -> "ansible_python" ;
    "ansible_facts" -> "ansible_python_version" ;
    "ansible_facts" -> "ansible_selinux" ;
    "ansible_facts" -> "ansible_architecture" ;
    "ansible_facts" -> "ansible_os_family" ;
    "ansible_facts" -> "ansible_system" ;
    "ansible_facts" -> "ansible_distribution" ;
    "ansible_facts" -> "ansible_distribution_version" ;
    "ansible_facts" -> "ansible_distribution_major_version" ;
    "ansible_facts" -> "ansible_kernel" ;
    "ansible_facts" -> "ansible_machine" ;
    "ansible_facts" -> "ansible_machine_id" ;
    "ansible_facts" -> "ansible_processor" ;
    "ansible_facts" -> "ansible_processor_cores" ;
    "ansible_facts" -> "ansible_processor_count" ;
    "ansible_facts" -> "ansible_processor_vcpus" ;
    "ansible_facts" -> "ansible_processor_threads_per_core" ;
    "ansible_facts" -> "ansible_memory_mb" ;
    "ansible_facts" -> "ansible_memfree_mb" ;
    "ansible_facts" -> "ansible_memtotal_mb" ;
    "ansible_facts" -> "ansible_mounts" ;
    "ansible_facts" -> "ansible_devices" ;
}
```

#### 1. 系统架构
> ansible_architecture

```bash
ansible localhost -m setup -a 'filter=ansible_architecture'
```

```
localhost | SUCCESS => {
    "ansible_facts": {
        "ansible_architecture": "x86_64"
    },
    "changed": false
}

```

示例：
`ansible all -m setup -a 'filter=ansible_architecture'`
```json
node76 | SUCCESS => {
    "ansible_facts": {
        "ansible_architecture": "x86_64"
    },
    "changed": false
}
node77 | SUCCESS => {
    "ansible_facts": {
        "ansible_architecture": "aarch64"
    },
    "changed": false
}

```

#### 2. 系统版本
- ansible_distribution
- ansible_distribution_version

```yaml
- hosts: all
  tasks:
    - name: Get distribution info
      ansible.builtin.debug:
        msg: The system is {{ ansible_distribution }} {{ ansible_distribution_version }}
```


#### 系统内存

```yaml
- hosts: all
  tasks:
    - name: Get memory info
      ansible.builtin.debug:
        msg:
          - The total memory is {{ ansible_memory_mb['real']['total'] }} MB.
          - The free memory is {{ ansible_memfree_mb }} MB.
```




> 主机名

`ansible all -m debug -a "msg={{ ansible_play_hosts }}"`
```json
node1 | SUCCESS => {
    "msg": [
        "node1",
        "node2",
        "node3"
    ]
}
node3 | SUCCESS => {
    "msg": [
        "node1",
        "node2",
        "node3"
    ]
}
node2 | SUCCESS => {
    "msg": [
        "node1",
        "node2",
        "node3"
    ]
}

```

`ansible all -m debug -a "msg={{ groups['all'] }}"`
```json

node1 | SUCCESS => {
    "msg": [
        "node1",
        "node2",
        "node3"
    ]
}
node2 | SUCCESS => {
    "msg": [
        "node1",
        "node2",
        "node3"
    ]
}
node3 | SUCCESS => {
    "msg": [
        "node1",
        "node2",
        "node3"
    ]
}

```


#### 主机ip列表

`ansible all -m debug -a "msg={{ ansible_play_hosts | map('extract', hostvars, 'ansible_host') | sort }}"`
```json

node1 | SUCCESS => {
    "msg": [
        "172.17.134.71",
        "172.17.134.76",
        "172.17.134.77"
    ]
}
node2 | SUCCESS => {
    "msg": [
        "172.17.134.71",
        "172.17.134.76",
        "172.17.134.77"
    ]
}
node3 | SUCCESS => {
    "msg": [
        "172.17.134.71",
        "172.17.134.76",
        "172.17.134.77"
    ]
}

```

#### hostvars

```yaml
hostvars['node1']['ansible_host']

hostvars[groups['all'][0]]['ansible_host']
```


```bash
ansible all -m setup

# 主机的节点名
ansible all -m setup -a "filter=ansible_nodename"

# 主机的主机名
ansible all -m setup -a "filter=ansible_hostname"

# 主机所属的域名
ansible all -m setup -a "filter=ansible_domain"

# 主机的总内存量
ansible all -m setup -a "filter=ansible_memtotal_mb"

# 主机的物理内存量
ansible all -m setup -a "filter=ansible_memory_mb"

# 主机当前空闲的内存量
ansible all -m setup -a "filter=ansible_memfree_mb"

# 主机操作系统的家族 // 例如 RedHat、Debian 等
ansible all -m setup -a "filter=ansible_os_family"

# 主机操作系统的主版本号
ansible all -m setup -a "filter=ansible_distribution_major_version"

# 主机操作系统的完整版本号
ansible all -m setup -a "filter=ansible_distribution_version"

# 主机的虚拟 CPU 数量
ansible all -m setup -a "filter=ansible_processor_vcpus"

# 主机的所有 IPv4 地址列表
ansible all -m setup -a "filter=ansible_all_ipv4_addresses"

# 主机的架构类型
ansible all -m setup -a "filter=ansible_architecture"

# 主机已运行的时间
ansible all -m setup -a "filter=ansible_uptime_seconds"

# 以 ansible_processor 开头的所有 facts
ansible all -m setup -a "filter=ansible_processor*"

# 主机的环境变量
ansible all -m setup -a 'filter=ansible_env'
```


#### groups hostvars

>/etc/hosts
```yaml
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

{% for host in groups['all'] %}
{{ hostvars[host]['ansible_host'] }}     {{ hostvars[host]['inventory_hostname'] }}
{% endfor %}
```






#### 1. aarch64
`[root@node83 ~]# ansible localhost -m setup`
```json

localhost | SUCCESS => {
    "ansible_facts": {
        "ansible_all_ipv4_addresses": [
            "172.17.135.83"
        ],
        "ansible_all_ipv6_addresses": [
            "fe80::4a81:2a9d:a9fb:1eab"
        ],
        "ansible_apparmor": {
            "status": "disabled"
        },
        "ansible_architecture": "aarch64",
        "ansible_bios_date": "02/06/2015",
        "ansible_bios_version": "0.0.0",
        "ansible_cmdline": {
            "BOOT_IMAGE": "/vmlinuz-4.19.90-17.ky10.aarch64",
            "crashkernel": "1024M,high",
            "ro": true,
            "root": "UUID=efa2aed7-bc27-4396-8716-c8669483ebfe",
            "smmu.bypassdev": "0x1000:0x15",
            "video": "VGA-1:640x480-32@60me"
        },
        "ansible_date_time": {
            "date": "2024-04-03",
            "day": "03",
            "epoch": "1712107455",
            "hour": "09",
            "iso8601": "2024-04-03T01:24:15Z",
            "iso8601_basic": "20240403T092415842505",
            "iso8601_basic_short": "20240403T092415",
            "iso8601_micro": "2024-04-03T01:24:15.842719Z",
            "minute": "24",
            "month": "04",
            "second": "15",
            "time": "09:24:15",
            "tz": "CST",
            "tz_offset": "+0800",
            "weekday": "Wednesday",
            "weekday_number": "3",
            "weeknumber": "14",
            "year": "2024"
        },
        "ansible_default_ipv4": {
            "address": "172.17.135.83",
            "alias": "enp1s0",
            "broadcast": "172.17.143.255",
            "gateway": "172.17.130.1",
            "interface": "enp1s0",
            "macaddress": "00:16:3e:0b:be:f4",
            "mtu": 1500,
            "netmask": "255.255.240.0",
            "network": "172.17.128.0",
            "type": "ether"
        },
        "ansible_default_ipv6": {},
        "ansible_device_links": {
            "ids": {
                "sda": [
                    "scsi-0QEMU_QEMU_HARDDISK_drive-scsi0-0-0-0"
                ],
                "sda1": [
                    "scsi-0QEMU_QEMU_HARDDISK_drive-scsi0-0-0-0-part1"
                ],
                "sda2": [
                    "scsi-0QEMU_QEMU_HARDDISK_drive-scsi0-0-0-0-part2"
                ],
                "sda3": [
                    "scsi-0QEMU_QEMU_HARDDISK_drive-scsi0-0-0-0-part3"
                ],
                "sda4": [
                    "scsi-0QEMU_QEMU_HARDDISK_drive-scsi0-0-0-0-part4"
                ]
            },
            "labels": {},
            "masters": {},
            "uuids": {
                "sda1": [
                    "EE8E-3D87"
                ],
                "sda2": [
                    "dd39bf27-b3f9-476a-9756-d9b871c0cca0"
                ],
                "sda3": [
                    "18e97c10-722f-4612-99e4-edf060648f4a"
                ],
                "sda4": [
                    "efa2aed7-bc27-4396-8716-c8669483ebfe"
                ]
            }
        },
        "ansible_devices": {
            "sda": {
                "holders": [],
                "host": "SCSI storage controller: Virtio: Virtio SCSI (rev 01)",
                "links": {
                    "ids": [
                        "scsi-0QEMU_QEMU_HARDDISK_drive-scsi0-0-0-0"
                    ],
                    "labels": [],
                    "masters": [],
                    "uuids": []
                },
                "model": "QEMU HARDDISK",
                "partitions": {
                    "sda1": {
                        "holders": [],
                        "links": {
                            "ids": [
                                "scsi-0QEMU_QEMU_HARDDISK_drive-scsi0-0-0-0-part1"
                            ],
                            "labels": [],
                            "masters": [],
                            "uuids": [
                                "EE8E-3D87"
                            ]
                        },
                        "sectors": "409600",
                        "sectorsize": 512,
                        "size": "200.00 MB",
                        "start": "2048",
                        "uuid": "EE8E-3D87"
                    },
                    "sda2": {
                        "holders": [],
                        "links": {
                            "ids": [
                                "scsi-0QEMU_QEMU_HARDDISK_drive-scsi0-0-0-0-part2"
                            ],
                            "labels": [],
                            "masters": [],
                            "uuids": [
                                "dd39bf27-b3f9-476a-9756-d9b871c0cca0"
                            ]
                        },
                        "sectors": "2097152",
                        "sectorsize": 512,
                        "size": "1.00 GB",
                        "start": "411648",
                        "uuid": "dd39bf27-b3f9-476a-9756-d9b871c0cca0"
                    },
                    "sda3": {
                        "holders": [],
                        "links": {
                            "ids": [
                                "scsi-0QEMU_QEMU_HARDDISK_drive-scsi0-0-0-0-part3"
                            ],
                            "labels": [],
                            "masters": [],
                            "uuids": [
                                "18e97c10-722f-4612-99e4-edf060648f4a"
                            ]
                        },
                        "sectors": "2097152",
                        "sectorsize": 512,
                        "size": "1.00 GB",
                        "start": "2508800",
                        "uuid": "18e97c10-722f-4612-99e4-edf060648f4a"
                    },
                    "sda4": {
                        "holders": [],
                        "links": {
                            "ids": [
                                "scsi-0QEMU_QEMU_HARDDISK_drive-scsi0-0-0-0-part4"
                            ],
                            "labels": [],
                            "masters": [],
                            "uuids": [
                                "efa2aed7-bc27-4396-8716-c8669483ebfe"
                            ]
                        },
                        "sectors": "414824415",
                        "sectorsize": 512,
                        "size": "197.80 GB",
                        "start": "4605952",
                        "uuid": "efa2aed7-bc27-4396-8716-c8669483ebfe"
                    }
                },
                "removable": "0",
                "rotational": "1",
                "sas_address": null,
                "sas_device_handle": null,
                "scheduler_mode": "bfq",
                "sectors": "419430400",
                "sectorsize": "512",
                "size": "200.00 GB",
                "support_discard": "4096",
                "vendor": "QEMU",
                "virtual": 1
            }
        },
        "ansible_distribution": "Kylin Linux Advanced Server",
        "ansible_distribution_file_parsed": true,
        "ansible_distribution_file_path": "/etc/os-release",
        "ansible_distribution_file_variety": "NA",
        "ansible_distribution_major_version": "V10",
        "ansible_distribution_release": "Tercel",
        "ansible_distribution_version": "V10",
        "ansible_dns": {
            "nameservers": [
                "172.17.141.251",
                "223.5.5.5",
                "114.114.114.114"
            ]
        },
        "ansible_domain": "",
        "ansible_effective_group_id": 0,
        "ansible_effective_user_id": 0,
        "ansible_enp1s0": {
            "active": true,
            "device": "enp1s0",
            "features": {
                "esp_hw_offload": "off [fixed]",
                "esp_tx_csum_hw_offload": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "on [fixed]",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "off [fixed]",
                "netns_local": "off [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off [fixed]",
                "rx_checksumming": "on [fixed]",
                "rx_fcs": "off [fixed]",
                "rx_gro_hw": "off [fixed]",
                "rx_udp_tunnel_port_offload": "off [fixed]",
                "rx_vlan_filter": "on [fixed]",
                "rx_vlan_offload": "off [fixed]",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "off [fixed]",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tls_hw_record": "off [fixed]",
                "tls_hw_rx_offload": "off [fixed]",
                "tls_hw_tx_offload": "off [fixed]",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "off [fixed]",
                "tx_checksumming": "on",
                "tx_esp_segmentation": "off [fixed]",
                "tx_fcoe_segmentation": "off [fixed]",
                "tx_gre_csum_segmentation": "off [fixed]",
                "tx_gre_segmentation": "off [fixed]",
                "tx_gso_partial": "off [fixed]",
                "tx_gso_robust": "on [fixed]",
                "tx_ipxip4_segmentation": "off [fixed]",
                "tx_ipxip6_segmentation": "off [fixed]",
                "tx_lockless": "off [fixed]",
                "tx_nocache_copy": "off",
                "tx_scatter_gather": "on",
                "tx_scatter_gather_fraglist": "off [fixed]",
                "tx_sctp_segmentation": "off [fixed]",
                "tx_tcp6_segmentation": "on",
                "tx_tcp_ecn_segmentation": "on",
                "tx_tcp_mangleid_segmentation": "off",
                "tx_tcp_segmentation": "on",
                "tx_udp_segmentation": "off [fixed]",
                "tx_udp_tnl_csum_segmentation": "off [fixed]",
                "tx_udp_tnl_segmentation": "off [fixed]",
                "tx_vlan_offload": "off [fixed]",
                "tx_vlan_stag_hw_insert": "off [fixed]",
                "vlan_challenged": "off [fixed]"
            },
            "hw_timestamp_filters": [],
            "ipv4": {
                "address": "172.17.135.83",
                "broadcast": "172.17.143.255",
                "netmask": "255.255.240.0",
                "network": "172.17.128.0"
            },
            "ipv6": [
                {
                    "address": "fe80::4a81:2a9d:a9fb:1eab",
                    "prefix": "64",
                    "scope": "link"
                }
            ],
            "macaddress": "00:16:3e:0b:be:f4",
            "module": "virtio_net",
            "mtu": 1500,
            "pciid": "virtio0",
            "promisc": false,
            "speed": -1,
            "timestamping": [
                "tx_software",
                "rx_software",
                "software"
            ],
            "type": "ether"
        },
        "ansible_env": {
            "CLASSPATH": ".:/usr/lib/jdk1.8.0_221/lib:/usr/lib/jdk1.8.0_221/jre/lib",
            "DBUS_SESSION_BUS_ADDRESS": "unix:path=/run/user/0/bus",
            "GO111MODULE": "on",
            "GOPATH": "/root/local/mygo",
            "GOPROXY": "https://proxy.golang.org",
            "HISTCONTROL": "",
            "HISTSIZE": "1000",
            "HISTTIMEFORMAT": "",
            "HOME": "/root",
            "HOSTNAME": "node83",
            "JAVA_HOME": "/usr/lib/jdk1.8.0_221",
            "JRE_HOME": "/usr/lib/jdk1.8.0_221/jre",
            "LANG": "en_US.UTF-8",
            "LOGNAME": "root",
            "LS_COLORS": "rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=00:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:*.lz4=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.dz=01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;31:*.zst=01;31:*.tzst=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.alz=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.cab=01;31:*.wim=01;31:*.swm=01;31:*.dwm=01;31:*.esd=01;31:*.jpg=01;35:*.jpeg=01;35:*.mjpg=01;35:*.mjpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=00;36:*.au=00;36:*.flac=00;36:*.m4a=00;36:*.mid=00;36:*.midi=00;36:*.mka=00;36:*.mp3=00;36:*.mpc=00;36:*.ogg=00;36:*.ra=00;36:*.wav=00;36:*.oga=00;36:*.opus=00;36:*.spx=00;36:*.xspf=00;36:",
            "MAIL": "/var/spool/mail/root",
            "PATH": "/usr/lib/jdk1.8.0_221/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin:/root/local/go/bin:/data/app/mysql/bin:/root/bin",
            "PROMPT_COMMAND": "kylin_history",
            "PWD": "/root",
            "SHELL": "/bin/bash",
            "SHLVL": "2",
            "SSH_ASKPASS": "/usr/libexec/openssh/gnome-ssh-askpass",
            "SSH_CLIENT": "172.17.120.253 50900 22",
            "SSH_CONNECTION": "172.17.120.253 50900 172.17.135.83 22",
            "SSH_TTY": "/dev/pts/0",
            "TERM": "xterm",
            "USER": "root",
            "XDG_RUNTIME_DIR": "/run/user/0",
            "XDG_SESSION_CLASS": "user",
            "XDG_SESSION_ID": "41",
            "XDG_SESSION_TYPE": "tty",
            "_": "/usr/bin/python3"
        },
        "ansible_fibre_channel_wwn": [],
        "ansible_fips": false,
        "ansible_form_factor": "Other",
        "ansible_fqdn": "node83",
        "ansible_hostname": "node83",
        "ansible_hostnqn": "",
        "ansible_interfaces": [
            "enp1s0",
            "lo"
        ],
        "ansible_is_chroot": false,
        "ansible_iscsi_iqn": "",
        "ansible_kernel": "4.19.90-17.ky10.aarch64",
        "ansible_lo": {
            "active": true,
            "device": "lo",
            "features": {
                "esp_hw_offload": "off [fixed]",
                "esp_tx_csum_hw_offload": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "on [fixed]",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "on [fixed]",
                "netns_local": "on [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off [fixed]",
                "rx_checksumming": "on [fixed]",
                "rx_fcs": "off [fixed]",
                "rx_gro_hw": "off [fixed]",
                "rx_udp_tunnel_port_offload": "off [fixed]",
                "rx_vlan_filter": "off [fixed]",
                "rx_vlan_offload": "off [fixed]",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "off [fixed]",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tls_hw_record": "off [fixed]",
                "tls_hw_rx_offload": "off [fixed]",
                "tls_hw_tx_offload": "off [fixed]",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on [fixed]",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "on [fixed]",
                "tx_checksumming": "on",
                "tx_esp_segmentation": "off [fixed]",
                "tx_fcoe_segmentation": "off [fixed]",
                "tx_gre_csum_segmentation": "off [fixed]",
                "tx_gre_segmentation": "off [fixed]",
                "tx_gso_partial": "off [fixed]",
                "tx_gso_robust": "off [fixed]",
                "tx_ipxip4_segmentation": "off [fixed]",
                "tx_ipxip6_segmentation": "off [fixed]",
                "tx_lockless": "on [fixed]",
                "tx_nocache_copy": "off [fixed]",
                "tx_scatter_gather": "on [fixed]",
                "tx_scatter_gather_fraglist": "on [fixed]",
                "tx_sctp_segmentation": "on",
                "tx_tcp6_segmentation": "on",
                "tx_tcp_ecn_segmentation": "on",
                "tx_tcp_mangleid_segmentation": "on",
                "tx_tcp_segmentation": "on",
                "tx_udp_segmentation": "off [fixed]",
                "tx_udp_tnl_csum_segmentation": "off [fixed]",
                "tx_udp_tnl_segmentation": "off [fixed]",
                "tx_vlan_offload": "off [fixed]",
                "tx_vlan_stag_hw_insert": "off [fixed]",
                "vlan_challenged": "on [fixed]"
            },
            "hw_timestamp_filters": [],
            "ipv4": {
                "address": "127.0.0.1",
                "broadcast": "host",
                "netmask": "255.0.0.0",
                "network": "127.0.0.0"
            },
            "ipv6": [
                {
                    "address": "::1",
                    "prefix": "128",
                    "scope": "host"
                }
            ],
            "mtu": 65536,
            "promisc": false,
            "timestamping": [
                "tx_software",
                "rx_software",
                "software"
            ],
            "type": "loopback"
        },
        "ansible_local": {},
        "ansible_lsb": {},
        "ansible_lvm": {
            "lvs": {},
            "pvs": {},
            "vgs": {}
        },
        "ansible_machine": "aarch64",
        "ansible_machine_id": "7c85570834054067942c92b414dc54bc",
        "ansible_memfree_mb": 3875,
        "ansible_memory_mb": {
            "nocache": {
                "free": 5970,
                "used": 1611
            },
            "real": {
                "free": 3875,
                "total": 7581,
                "used": 3706
            },
            "swap": {
                "cached": 0,
                "free": 1023,
                "total": 1023,
                "used": 0
            }
        },
        "ansible_memtotal_mb": 7581,
        "ansible_mounts": [
            {
                "block_available": 47673319,
                "block_size": 4096,
                "block_total": 51850491,
                "block_used": 4177172,
                "device": "/dev/sda4",
                "fstype": "xfs",
                "inode_available": 103547177,
                "inode_total": 103706048,
                "inode_used": 158871,
                "mount": "/",
                "options": "rw,relatime,attr2,inode64,noquota",
                "size_available": 195269914624,
                "size_total": 212379611136,
                "uuid": "efa2aed7-bc27-4396-8716-c8669483ebfe"
            },
            {
                "block_available": 209174,
                "block_size": 4096,
                "block_total": 259584,
                "block_used": 50410,
                "device": "/dev/sda2",
                "fstype": "xfs",
                "inode_available": 524222,
                "inode_total": 524288,
                "inode_used": 66,
                "mount": "/boot",
                "options": "rw,relatime,attr2,inode64,noquota",
                "size_available": 856776704,
                "size_total": 1063256064,
                "uuid": "dd39bf27-b3f9-476a-9756-d9b871c0cca0"
            },
            {
                "block_available": 49684,
                "block_size": 4096,
                "block_total": 51145,
                "block_used": 1461,
                "device": "/dev/sda1",
                "fstype": "vfat",
                "inode_available": 0,
                "inode_total": 0,
                "inode_used": 0,
                "mount": "/boot/efi",
                "options": "rw,relatime,fmask=0077,dmask=0077,codepage=437,iocharset=ascii,shortname=winnt,errors=remount-ro",
                "size_available": 203505664,
                "size_total": 209489920,
                "uuid": "EE8E-3D87"
            }
        ],
        "ansible_nodename": "node83",
        "ansible_os_family": "Kylin Linux Advanced Server",
        "ansible_pkg_mgr": "dnf",
        "ansible_proc_cmdline": {
            "BOOT_IMAGE": "/vmlinuz-4.19.90-17.ky10.aarch64",
            "crashkernel": [
                "auto",
                "1024M,high"
            ],
            "ro": true,
            "root": "UUID=efa2aed7-bc27-4396-8716-c8669483ebfe",
            "smmu.bypassdev": [
                "0x1000:0x17",
                "0x1000:0x15"
            ],
            "video": [
                "efifb:off",
                "VGA-1:640x480-32@60me"
            ]
        },
        "ansible_processor": [
            "0",
            "ARMv8 CPU",
            "1",
            "ARMv8 CPU",
            "2",
            "ARMv8 CPU",
            "3",
            "ARMv8 CPU"
        ],
        "ansible_processor_cores": 1,
        "ansible_processor_count": 4,
        "ansible_processor_threads_per_core": 1,
        "ansible_processor_vcpus": 4,
        "ansible_product_name": "KVM Virtual Machine",
        "ansible_product_serial": "NA",
        "ansible_product_uuid": "134e3e9d-e5d2-44c0-be8a-b4982cb8f595",
        "ansible_product_version": "virt-rhel7.6.0",
        "ansible_python": {
            "executable": "/usr/bin/python3",
            "has_sslcontext": true,
            "type": "cpython",
            "version": {
                "major": 3,
                "micro": 4,
                "minor": 7,
                "releaselevel": "final",
                "serial": 0
            },
            "version_info": [
                3,
                7,
                4,
                "final",
                0
            ]
        },
        "ansible_python_version": "3.7.4",
        "ansible_real_group_id": 0,
        "ansible_real_user_id": 0,
        "ansible_selinux": {
            "status": "disabled"
        },
        "ansible_selinux_python_present": true,
        "ansible_service_mgr": "systemd",
        "ansible_ssh_host_key_ecdsa_public": "AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBKqMNne9oilG1wtFbz5RnMHRtQ8K0BvB/1EUI3H4PN5QV2V2PtLDu7vbZkzag8br4AEPNNng70QGeu31pIReLGc=",
        "ansible_ssh_host_key_ed25519_public": "AAAAC3NzaC1lZDI1NTE5AAAAIIEH+Ddwtb80uZ7ZE3k5X7b0MySTeXfOvw78khwCu+Aa",
        "ansible_ssh_host_key_rsa_public": "AAAAB3NzaC1yc2EAAAADAQABAAABAQCpWBfajUrvism91XhtEwcIChtzSQ4ZPfFtQS5pZgF/IdyKadOorqLrWB3D8eSJJLQkLKykk6E9DrqTymcGPwpD6uOTVzA+gluO8xoawGtoickwyuspieaXUH2RO0WjumIi5CBMnD62XHJCZ0TojtHLaM7iF3ErbbZbXSBoyGmXhUmQJw2K5q0cpkHFhz4khIUuLAqeKKBtGYMksVrVJ14ParAQBnpo0L56Q3Cm1HgHiffeR6xyjxlk30Z+V797FVoR0m2awk+QPljoQEltDMoS4Udh15LNTabp3tDMf5ZWNjxpSnj5Bl/nr8yiU1cbXl0LNGMSlgR+PJ3mEO9qVjNr",
        "ansible_swapfree_mb": 1023,
        "ansible_swaptotal_mb": 1023,
        "ansible_system": "Linux",
        "ansible_system_capabilities": [
            "cap_chown",
            "cap_dac_override",
            "cap_dac_read_search",
            "cap_fowner",
            "cap_fsetid",
            "cap_kill",
            "cap_setgid",
            "cap_setuid",
            "cap_setpcap",
            "cap_linux_immutable",
            "cap_net_bind_service",
            "cap_net_broadcast",
            "cap_net_admin",
            "cap_net_raw",
            "cap_ipc_lock",
            "cap_ipc_owner",
            "cap_sys_module",
            "cap_sys_rawio",
            "cap_sys_chroot",
            "cap_sys_ptrace",
            "cap_sys_pacct",
            "cap_sys_admin",
            "cap_sys_boot",
            "cap_sys_nice",
            "cap_sys_resource",
            "cap_sys_time",
            "cap_sys_tty_config",
            "cap_mknod",
            "cap_lease",
            "cap_audit_write",
            "cap_audit_control",
            "cap_setfcap",
            "cap_mac_override",
            "cap_mac_admin",
            "cap_syslog",
            "cap_wake_alarm",
            "cap_block_suspend",
            "cap_audit_read+ep"
        ],
        "ansible_system_capabilities_enforced": "True",
        "ansible_system_vendor": "QEMU",
        "ansible_uptime_seconds": 665402,
        "ansible_user_dir": "/root",
        "ansible_user_gecos": "root",
        "ansible_user_gid": 0,
        "ansible_user_id": "root",
        "ansible_user_shell": "/bin/bash",
        "ansible_user_uid": 0,
        "ansible_userspace_bits": "64",
        "ansible_virtualization_role": "guest",
        "ansible_virtualization_type": "kvm",
        "gather_subset": [
            "all"
        ],
        "module_setup": true
    },
    "changed": false
}

```


#### 2. x86_64
`2024-04-03T09:24:06 [root@node70 /root] # ansible localhost -m setup`
```json

localhost | SUCCESS => {
    "ansible_facts": {
        "ansible_all_ipv4_addresses": [
            "172.17.134.70",
            "172.18.0.1",
            "192.168.122.1"
        ],
        "ansible_all_ipv6_addresses": [
            "fe80::6c6f:b0ff:fe2e:db5d",
            "fe80::8002:5fff:fe45:e177",
            "fe80::e423:34ff:feb4:6dd8",
            "fe80::216:3eff:feeb:f9aa",
            "fe80::42:f1ff:fee2:92b8",
            "fe80::dc50:e3ff:fe14:4a8d",
            "fe80::d469:b8ff:fe08:a738",
            "fe80::188d:d6ff:fe97:4a28",
            "fe80::a016:3cff:fec6:fbf4",
            "fe80::2438:17ff:fe26:ec41",
            "fe80::981d:59ff:fed2:88b0"
        ],
        "ansible_apparmor": {
            "status": "disabled"
        },
        "ansible_architecture": "x86_64",
        "ansible_bios_date": "01/01/2011",
        "ansible_bios_vendor": "Seabios",
        "ansible_bios_version": "0.5.1",
        "ansible_board_asset_tag": "NA",
        "ansible_board_name": "NA",
        "ansible_board_serial": "NA",
        "ansible_board_vendor": "NA",
        "ansible_board_version": "NA",
        "ansible_chassis_asset_tag": "NA",
        "ansible_chassis_serial": "NA",
        "ansible_chassis_vendor": "Bochs",
        "ansible_chassis_version": "NA",
        "ansible_cmdline": {
            "BOOT_IMAGE": "/vmlinuz-3.10.0-957.el7.x86_64",
            "LANG": "en_US.UTF-8",
            "crashkernel": "auto",
            "quiet": true,
            "rhgb": true,
            "ro": true,
            "root": "UUID=2b6b24db-7f5c-4e69-a4c6-f0ab2fff56b1"
        },
        "ansible_date_time": {
            "date": "2024-04-03",
            "day": "03",
            "epoch": "1712107575",
            "hour": "09",
            "iso8601": "2024-04-03T01:26:15Z",
            "iso8601_basic": "20240403T092615198083",
            "iso8601_basic_short": "20240403T092615",
            "iso8601_micro": "2024-04-03T01:26:15.198083Z",
            "minute": "26",
            "month": "04",
            "second": "15",
            "time": "09:26:15",
            "tz": "CST",
            "tz_offset": "+0800",
            "weekday": "Wednesday",
            "weekday_number": "3",
            "weeknumber": "14",
            "year": "2024"
        },
        "ansible_default_ipv4": {
            "address": "172.17.134.70",
            "alias": "eth0",
            "broadcast": "172.17.143.255",
            "gateway": "172.17.130.1",
            "interface": "eth0",
            "macaddress": "00:16:3e:eb:f9:aa",
            "mtu": 1500,
            "netmask": "255.255.240.0",
            "network": "172.17.128.0",
            "type": "ether"
        },
        "ansible_default_ipv6": {},
        "ansible_device_links": {
            "ids": {
                "sda": [
                    "scsi-0QEMU_QEMU_HARDDISK_drive-scsi0-0-0-0"
                ],
                "sda1": [
                    "scsi-0QEMU_QEMU_HARDDISK_drive-scsi0-0-0-0-part1"
                ],
                "sda2": [
                    "scsi-0QEMU_QEMU_HARDDISK_drive-scsi0-0-0-0-part2"
                ],
                "sr0": [
                    "ata-QEMU_DVD-ROM_QM00001"
                ],
                "sr1": [
                    "ata-QEMU_DVD-ROM_QM00002"
                ]
            },
            "labels": {
                "sr1": [
                    "cidata"
                ]
            },
            "masters": {},
            "uuids": {
                "sda1": [
                    "b4a1030d-3f54-4598-b2fc-627c781f4105"
                ],
                "sda2": [
                    "2b6b24db-7f5c-4e69-a4c6-f0ab2fff56b1"
                ],
                "sr1": [
                    "2023-10-09-12-01-19-00"
                ]
            }
        },
        "ansible_devices": {
            "sda": {
                "holders": [],
                "host": "SCSI storage controller: Red Hat, Inc. Virtio SCSI",
                "links": {
                    "ids": [
                        "scsi-0QEMU_QEMU_HARDDISK_drive-scsi0-0-0-0"
                    ],
                    "labels": [],
                    "masters": [],
                    "uuids": []
                },
                "model": "QEMU HARDDISK",
                "partitions": {
                    "sda1": {
                        "holders": [],
                        "links": {
                            "ids": [
                                "scsi-0QEMU_QEMU_HARDDISK_drive-scsi0-0-0-0-part1"
                            ],
                            "labels": [],
                            "masters": [],
                            "uuids": [
                                "b4a1030d-3f54-4598-b2fc-627c781f4105"
                            ]
                        },
                        "sectors": "614400",
                        "sectorsize": 512,
                        "size": "300.00 MB",
                        "start": "2048",
                        "uuid": "b4a1030d-3f54-4598-b2fc-627c781f4105"
                    },
                    "sda2": {
                        "holders": [],
                        "links": {
                            "ids": [
                                "scsi-0QEMU_QEMU_HARDDISK_drive-scsi0-0-0-0-part2"
                            ],
                            "labels": [],
                            "masters": [],
                            "uuids": [
                                "2b6b24db-7f5c-4e69-a4c6-f0ab2fff56b1"
                            ]
                        },
                        "sectors": "418813919",
                        "sectorsize": 512,
                        "size": "199.71 GB",
                        "start": "616448",
                        "uuid": "2b6b24db-7f5c-4e69-a4c6-f0ab2fff56b1"
                    }
                },
                "removable": "0",
                "rotational": "1",
                "sas_address": null,
                "sas_device_handle": null,
                "scheduler_mode": "deadline",
                "sectors": "419430400",
                "sectorsize": "512",
                "size": "200.00 GB",
                "support_discard": "4096",
                "vendor": "QEMU",
                "virtual": 1
            },
            "sr0": {
                "holders": [],
                "host": "IDE interface: Intel Corporation 82371SB PIIX3 IDE [Natoma/Triton II]",
                "links": {
                    "ids": [
                        "ata-QEMU_DVD-ROM_QM00001"
                    ],
                    "labels": [],
                    "masters": [],
                    "uuids": []
                },
                "model": "QEMU DVD-ROM",
                "partitions": {},
                "removable": "1",
                "rotational": "1",
                "sas_address": null,
                "sas_device_handle": null,
                "scheduler_mode": "deadline",
                "sectors": "2097151",
                "sectorsize": "512",
                "size": "1024.00 MB",
                "support_discard": "0",
                "vendor": "QEMU",
                "virtual": 1
            },
            "sr1": {
                "holders": [],
                "host": "IDE interface: Intel Corporation 82371SB PIIX3 IDE [Natoma/Triton II]",
                "links": {
                    "ids": [
                        "ata-QEMU_DVD-ROM_QM00002"
                    ],
                    "labels": [
                        "cidata"
                    ],
                    "masters": [],
                    "uuids": [
                        "2023-10-09-12-01-19-00"
                    ]
                },
                "model": "QEMU DVD-ROM",
                "partitions": {},
                "removable": "1",
                "rotational": "1",
                "sas_address": null,
                "sas_device_handle": null,
                "scheduler_mode": "deadline",
                "sectors": "724",
                "sectorsize": "2048",
                "size": "362.00 KB",
                "support_discard": "0",
                "vendor": "QEMU",
                "virtual": 1
            }
        },
        "ansible_distribution": "CentOS",
        "ansible_distribution_file_parsed": true,
        "ansible_distribution_file_path": "/etc/redhat-release",
        "ansible_distribution_file_variety": "RedHat",
        "ansible_distribution_major_version": "7",
        "ansible_distribution_release": "Core",
        "ansible_distribution_version": "7.9",
        "ansible_dns": {
            "nameservers": [
                "172.17.141.251",
                "223.5.5.5",
                "114.114.114.114"
            ]
        },
        "ansible_docker0": {
            "active": true,
            "device": "docker0",
            "features": {
                "busy_poll": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "on",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "off [fixed]",
                "netns_local": "on [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off [fixed]",
                "rx_checksumming": "off [fixed]",
                "rx_fcs": "off [fixed]",
                "rx_gro_hw": "off [fixed]",
                "rx_udp_tunnel_port_offload": "off [fixed]",
                "rx_vlan_filter": "off [fixed]",
                "rx_vlan_offload": "off [fixed]",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "off [fixed]",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "off [fixed]",
                "tx_checksumming": "on",
                "tx_fcoe_segmentation": "off [requested on]",
                "tx_gre_csum_segmentation": "on",
                "tx_gre_segmentation": "on",
                "tx_gso_partial": "on",
                "tx_gso_robust": "off [requested on]",
                "tx_ipip_segmentation": "on",
                "tx_lockless": "on [fixed]",
                "tx_nocache_copy": "off",
                "tx_scatter_gather": "on",
                "tx_scatter_gather_fraglist": "on",
                "tx_sctp_segmentation": "on",
                "tx_sit_segmentation": "on",
                "tx_tcp6_segmentation": "on",
                "tx_tcp_ecn_segmentation": "on",
                "tx_tcp_mangleid_segmentation": "on",
                "tx_tcp_segmentation": "on",
                "tx_udp_tnl_csum_segmentation": "on",
                "tx_udp_tnl_segmentation": "on",
                "tx_vlan_offload": "on",
                "tx_vlan_stag_hw_insert": "on",
                "udp_fragmentation_offload": "on",
                "vlan_challenged": "off [fixed]"
            },
            "hw_timestamp_filters": [],
            "id": "8000.0242f1e292b8",
            "interfaces": [
                "veth96a836d",
                "veth48128c2",
                "veth1a8d2df",
                "veth609055f",
                "veth1dbc5ab",
                "veth59eff92",
                "veth63ec05e",
                "veth11c8836",
                "veth847cfc7"
            ],
            "ipv4": {
                "address": "172.18.0.1",
                "broadcast": "172.18.255.255",
                "netmask": "255.255.0.0",
                "network": "172.18.0.0"
            },
            "ipv6": [
                {
                    "address": "fe80::42:f1ff:fee2:92b8",
                    "prefix": "64",
                    "scope": "link"
                }
            ],
            "macaddress": "02:42:f1:e2:92:b8",
            "mtu": 1500,
            "promisc": false,
            "stp": false,
            "timestamping": [
                "rx_software",
                "software"
            ],
            "type": "bridge"
        },
        "ansible_domain": "",
        "ansible_effective_group_id": 0,
        "ansible_effective_user_id": 0,
        "ansible_env": {
            "ACE_ROOT": "/root/local/ACE_wrappers",
            "DISPLAY": "localhost:10.0",
            "HISTCONTROL": "ignoredups",
            "HISTFILESIZE": "102400",
            "HISTSIZE": "1024",
            "HOME": "/root",
            "HOSTNAME": "node70",
            "LANG": "C",
            "LC_ALL": "C",
            "LC_NUMERIC": "C",
            "LD_LIBARY_PATH": "/root/local/ACE_wrappers/lib:",
            "LD_LIBRARY_PATH": "/root/local/bison/lib",
            "LESSOPEN": "||/usr/bin/lesspipe.sh %s",
            "LLVM_HOME": "/opt/rh/llvm-toolset-7.0/root/usr",
            "LOGNAME": "root",
            "LS_COLORS": "rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=01;05;37;41:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:*.lz4=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.Z=01;31:*.dz=01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.alz=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.cab=01;31:*.jpg=01;35:*.jpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.axv=01;35:*.anx=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=01;36:*.au=01;36:*.flac=01;36:*.mid=01;36:*.midi=01;36:*.mka=01;36:*.mp3=01;36:*.mpc=01;36:*.ogg=01;36:*.ra=01;36:*.wav=01;36:*.axa=01;36:*.oga=01;36:*.spx=01;36:*.xspf=01;36:",
            "MAIL": "/var/spool/mail/root",
            "PATH": "/root/local/bison/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/opt/rh/llvm-toolset-7.0/root/usr/bin:/root/local/ACE_wrappers:/root/bin",
            "PWD": "/root",
            "SELINUX_LEVEL_REQUESTED": "",
            "SELINUX_ROLE_REQUESTED": "",
            "SELINUX_USE_CURRENT_RANGE": "",
            "SHELL": "/bin/bash",
            "SHLVL": "3",
            "SSH_CLIENT": "172.17.120.253 50892 22",
            "SSH_CONNECTION": "172.17.120.253 50892 172.17.134.70 22",
            "SSH_TTY": "/dev/pts/0",
            "TERM": "xterm",
            "USER": "root",
            "XDG_DATA_DIRS": "/root/.local/share/flatpak/exports/share:/var/lib/flatpak/exports/share:/usr/local/share:/usr/share",
            "XDG_RUNTIME_DIR": "/run/user/0",
            "XDG_SESSION_ID": "22646",
            "_": "/usr/bin/python"
        },
        "ansible_eth0": {
            "active": true,
            "device": "eth0",
            "features": {
                "busy_poll": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "on [fixed]",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "off [fixed]",
                "netns_local": "off [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off [fixed]",
                "rx_checksumming": "on [fixed]",
                "rx_fcs": "off [fixed]",
                "rx_gro_hw": "off [fixed]",
                "rx_udp_tunnel_port_offload": "off [fixed]",
                "rx_vlan_filter": "on [fixed]",
                "rx_vlan_offload": "off [fixed]",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "off [fixed]",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "off [fixed]",
                "tx_checksumming": "on",
                "tx_fcoe_segmentation": "off [fixed]",
                "tx_gre_csum_segmentation": "off [fixed]",
                "tx_gre_segmentation": "off [fixed]",
                "tx_gso_partial": "off [fixed]",
                "tx_gso_robust": "off [fixed]",
                "tx_ipip_segmentation": "off [fixed]",
                "tx_lockless": "off [fixed]",
                "tx_nocache_copy": "off",
                "tx_scatter_gather": "on",
                "tx_scatter_gather_fraglist": "off [fixed]",
                "tx_sctp_segmentation": "off [fixed]",
                "tx_sit_segmentation": "off [fixed]",
                "tx_tcp6_segmentation": "on",
                "tx_tcp_ecn_segmentation": "on",
                "tx_tcp_mangleid_segmentation": "off",
                "tx_tcp_segmentation": "on",
                "tx_udp_tnl_csum_segmentation": "off [fixed]",
                "tx_udp_tnl_segmentation": "off [fixed]",
                "tx_vlan_offload": "off [fixed]",
                "tx_vlan_stag_hw_insert": "off [fixed]",
                "udp_fragmentation_offload": "on",
                "vlan_challenged": "off [fixed]"
            },
            "hw_timestamp_filters": [],
            "ipv4": {
                "address": "172.17.134.70",
                "broadcast": "172.17.143.255",
                "netmask": "255.255.240.0",
                "network": "172.17.128.0"
            },
            "ipv6": [
                {
                    "address": "fe80::216:3eff:feeb:f9aa",
                    "prefix": "64",
                    "scope": "link"
                }
            ],
            "macaddress": "00:16:3e:eb:f9:aa",
            "module": "virtio_net",
            "mtu": 1500,
            "pciid": "virtio0",
            "promisc": false,
            "timestamping": [
                "rx_software",
                "software"
            ],
            "type": "ether"
        },
        "ansible_fibre_channel_wwn": [],
        "ansible_fips": false,
        "ansible_form_factor": "Other",
        "ansible_fqdn": "node70",
        "ansible_hostname": "node70",
        "ansible_hostnqn": "",
        "ansible_interfaces": [
            "veth59eff92",
            "docker0",
            "veth96a836d",
            "veth63ec05e",
            "lo",
            "veth847cfc7",
            "veth1a8d2df",
            "veth48128c2",
            "veth609055f",
            "veth1dbc5ab",
            "veth11c8836",
            "eth0",
            "virbr0-nic",
            "virbr0"
        ],
        "ansible_is_chroot": false,
        "ansible_iscsi_iqn": "iqn.1994-05.com.redhat:beef1c944ea1",
        "ansible_kernel": "3.10.0-957.el7.x86_64",
        "ansible_kernel_version": "#1 SMP Thu Nov 8 23:39:32 UTC 2018",
        "ansible_lo": {
            "active": true,
            "device": "lo",
            "features": {
                "busy_poll": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "on [fixed]",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "on [fixed]",
                "netns_local": "on [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off [fixed]",
                "rx_checksumming": "on [fixed]",
                "rx_fcs": "off [fixed]",
                "rx_gro_hw": "off [fixed]",
                "rx_udp_tunnel_port_offload": "off [fixed]",
                "rx_vlan_filter": "off [fixed]",
                "rx_vlan_offload": "off [fixed]",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "off [fixed]",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on [fixed]",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "on [fixed]",
                "tx_checksumming": "on",
                "tx_fcoe_segmentation": "off [fixed]",
                "tx_gre_csum_segmentation": "off [fixed]",
                "tx_gre_segmentation": "off [fixed]",
                "tx_gso_partial": "off [fixed]",
                "tx_gso_robust": "off [fixed]",
                "tx_ipip_segmentation": "off [fixed]",
                "tx_lockless": "on [fixed]",
                "tx_nocache_copy": "off [fixed]",
                "tx_scatter_gather": "on [fixed]",
                "tx_scatter_gather_fraglist": "on [fixed]",
                "tx_sctp_segmentation": "on",
                "tx_sit_segmentation": "off [fixed]",
                "tx_tcp6_segmentation": "on",
                "tx_tcp_ecn_segmentation": "on",
                "tx_tcp_mangleid_segmentation": "on",
                "tx_tcp_segmentation": "on",
                "tx_udp_tnl_csum_segmentation": "off [fixed]",
                "tx_udp_tnl_segmentation": "off [fixed]",
                "tx_vlan_offload": "off [fixed]",
                "tx_vlan_stag_hw_insert": "off [fixed]",
                "udp_fragmentation_offload": "on",
                "vlan_challenged": "on [fixed]"
            },
            "hw_timestamp_filters": [],
            "ipv4": {
                "address": "127.0.0.1",
                "broadcast": "",
                "netmask": "255.0.0.0",
                "network": "127.0.0.0"
            },
            "ipv6": [
                {
                    "address": "::1",
                    "prefix": "128",
                    "scope": "host"
                }
            ],
            "mtu": 65536,
            "promisc": false,
            "timestamping": [
                "rx_software",
                "software"
            ],
            "type": "loopback"
        },
        "ansible_local": {},
        "ansible_lsb": {},
        "ansible_lvm": {
            "lvs": {},
            "pvs": {},
            "vgs": {}
        },
        "ansible_machine": "x86_64",
        "ansible_machine_id": "ebbb68ebb9fa496d97a89e45ebf87933",
        "ansible_memfree_mb": 1816,
        "ansible_memory_mb": {
            "nocache": {
                "free": 4239,
                "used": 3582
            },
            "real": {
                "free": 1816,
                "total": 7821,
                "used": 6005
            },
            "swap": {
                "cached": 0,
                "free": 0,
                "total": 0,
                "used": 0
            }
        },
        "ansible_memtotal_mb": 7821,
        "ansible_mounts": [
            {
                "block_available": 35379,
                "block_size": 4096,
                "block_total": 75945,
                "block_used": 40566,
                "device": "/dev/sda1",
                "fstype": "xfs",
                "inode_available": 153215,
                "inode_total": 153600,
                "inode_used": 385,
                "mount": "/boot",
                "options": "rw,seclabel,relatime,attr2,inode64,noquota",
                "size_available": 144912384,
                "size_total": 311070720,
                "uuid": "b4a1030d-3f54-4598-b2fc-627c781f4105"
            },
            {
                "block_available": 37286872,
                "block_size": 4096,
                "block_total": 51498807,
                "block_used": 14211935,
                "device": "/dev/sda2",
                "fstype": "ext4",
                "inode_available": 12563562,
                "inode_total": 13065248,
                "inode_used": 501686,
                "mount": "/",
                "options": "rw,seclabel,relatime,data=ordered",
                "size_available": 152727027712,
                "size_total": 210939113472,
                "uuid": "2b6b24db-7f5c-4e69-a4c6-f0ab2fff56b1"
            }
        ],
        "ansible_nodename": "node70",
        "ansible_os_family": "RedHat",
        "ansible_pkg_mgr": "yum",
        "ansible_proc_cmdline": {
            "BOOT_IMAGE": "/vmlinuz-3.10.0-957.el7.x86_64",
            "LANG": "en_US.UTF-8",
            "crashkernel": "auto",
            "quiet": true,
            "rhgb": true,
            "ro": true,
            "root": "UUID=2b6b24db-7f5c-4e69-a4c6-f0ab2fff56b1"
        },
        "ansible_processor": [
            "0",
            "GenuineIntel",
            "Intel Core Processor (Haswell)",
            "1",
            "GenuineIntel",
            "Intel Core Processor (Haswell)",
            "2",
            "GenuineIntel",
            "Intel Core Processor (Haswell)",
            "3",
            "GenuineIntel",
            "Intel Core Processor (Haswell)"
        ],
        "ansible_processor_cores": 2,
        "ansible_processor_count": 1,
        "ansible_processor_nproc": 4,
        "ansible_processor_threads_per_core": 2,
        "ansible_processor_vcpus": 4,
        "ansible_product_name": "Standard PC (i440FX + PIIX, 1996)",
        "ansible_product_serial": "NA",
        "ansible_product_uuid": "95C8753F-BAE3-47A2-89F1-DBA0EF255D74",
        "ansible_product_version": "pc-i440fx-2.0",
        "ansible_python": {
            "executable": "/usr/bin/python",
            "has_sslcontext": true,
            "type": "CPython",
            "version": {
                "major": 2,
                "micro": 5,
                "minor": 7,
                "releaselevel": "final",
                "serial": 0
            },
            "version_info": [
                2,
                7,
                5,
                "final",
                0
            ]
        },
        "ansible_python_version": "2.7.5",
        "ansible_real_group_id": 0,
        "ansible_real_user_id": 0,
        "ansible_selinux": {
            "config_mode": "enforcing",
            "mode": "enforcing",
            "policyvers": 31,
            "status": "enabled",
            "type": "targeted"
        },
        "ansible_selinux_python_present": true,
        "ansible_service_mgr": "systemd",
        "ansible_ssh_host_key_ecdsa_public": "AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBEIGpUTzYRY8NM7smZTUQXPO0d2dHgwPgc26s8OxJD9oztXtZAFndSE0DS2/apuw5kBAgSlDm6u+uQ8EocZb01A=",
        "ansible_ssh_host_key_ecdsa_public_keytype": "ecdsa-sha2-nistp256",
        "ansible_ssh_host_key_ed25519_public": "AAAAC3NzaC1lZDI1NTE5AAAAIAuVOpE0WP5+fobd1YiY3U/RZCuvHcsB8S/gnp+Z61se",
        "ansible_ssh_host_key_ed25519_public_keytype": "ssh-ed25519",
        "ansible_ssh_host_key_rsa_public": "AAAAB3NzaC1yc2EAAAADAQABAAABAQDTxYlGhVIyZlp/aRZGNzoYfP4JG58dftqE2KyQ1oPk/kbY4YsBzbXu0tFGFyafqAWY14tt/LM/LXJuNmSGzUf9wcSypkaiwtoxJOAyLoNqy2srXBdvKW5aUwmPLoqmsXgXyANFbfsynlNyQHViv96t0YIJmbi6KFZIvGoTB3SuMXNbmk2v7eVp7ghdlY1RSRWrepOUbgerG6ht8cQ/cr/LyBVIAnF7piyMtRTmmiEy7EtCfZiMt+0xEXB2Nbowvhm0zpTXdH1mRPIvEafrXNca6FboPmZrrORqxPEdRMJ2GBUtmIXFBSDNS5ftFNUP4p7sQcGnLOioftAGoKG1SFph",
        "ansible_ssh_host_key_rsa_public_keytype": "ssh-rsa",
        "ansible_swapfree_mb": 0,
        "ansible_swaptotal_mb": 0,
        "ansible_system": "Linux",
        "ansible_system_capabilities": [
            "cap_chown",
            "cap_dac_override",
            "cap_dac_read_search",
            "cap_fowner",
            "cap_fsetid",
            "cap_kill",
            "cap_setgid",
            "cap_setuid",
            "cap_setpcap",
            "cap_linux_immutable",
            "cap_net_bind_service",
            "cap_net_broadcast",
            "cap_net_admin",
            "cap_net_raw",
            "cap_ipc_lock",
            "cap_ipc_owner",
            "cap_sys_module",
            "cap_sys_rawio",
            "cap_sys_chroot",
            "cap_sys_ptrace",
            "cap_sys_pacct",
            "cap_sys_admin",
            "cap_sys_boot",
            "cap_sys_nice",
            "cap_sys_resource",
            "cap_sys_time",
            "cap_sys_tty_config",
            "cap_mknod",
            "cap_lease",
            "cap_audit_write",
            "cap_audit_control",
            "cap_setfcap",
            "cap_mac_override",
            "cap_mac_admin",
            "cap_syslog",
            "35",
            "36+ep"
        ],
        "ansible_system_capabilities_enforced": "True",
        "ansible_system_vendor": "QEMU",
        "ansible_uptime_seconds": 6646569,
        "ansible_user_dir": "/root",
        "ansible_user_gecos": "root",
        "ansible_user_gid": 0,
        "ansible_user_id": "root",
        "ansible_user_shell": "/bin/bash",
        "ansible_user_uid": 0,
        "ansible_userspace_architecture": "x86_64",
        "ansible_userspace_bits": "64",
        "ansible_veth11c8836": {
            "active": true,
            "device": "veth11c8836",
            "features": {
                "busy_poll": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "on",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "off [fixed]",
                "netns_local": "off [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off [fixed]",
                "rx_checksumming": "on",
                "rx_fcs": "off [fixed]",
                "rx_gro_hw": "off [fixed]",
                "rx_udp_tunnel_port_offload": "off [fixed]",
                "rx_vlan_filter": "off [fixed]",
                "rx_vlan_offload": "on",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "on",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "on",
                "tx_checksumming": "on",
                "tx_fcoe_segmentation": "off [fixed]",
                "tx_gre_csum_segmentation": "on",
                "tx_gre_segmentation": "on",
                "tx_gso_partial": "off [fixed]",
                "tx_gso_robust": "off [fixed]",
                "tx_ipip_segmentation": "on",
                "tx_lockless": "on [fixed]",
                "tx_nocache_copy": "off",
                "tx_scatter_gather": "on",
                "tx_scatter_gather_fraglist": "on",
                "tx_sctp_segmentation": "on",
                "tx_sit_segmentation": "on",
                "tx_tcp6_segmentation": "on",
                "tx_tcp_ecn_segmentation": "on",
                "tx_tcp_mangleid_segmentation": "on",
                "tx_tcp_segmentation": "on",
                "tx_udp_tnl_csum_segmentation": "on",
                "tx_udp_tnl_segmentation": "on",
                "tx_vlan_offload": "on",
                "tx_vlan_stag_hw_insert": "on",
                "udp_fragmentation_offload": "on",
                "vlan_challenged": "off [fixed]"
            },
            "hw_timestamp_filters": [],
            "ipv6": [
                {
                    "address": "fe80::d469:b8ff:fe08:a738",
                    "prefix": "64",
                    "scope": "link"
                }
            ],
            "macaddress": "d6:69:b8:08:a7:38",
            "mtu": 1500,
            "promisc": true,
            "speed": 10000,
            "timestamping": [
                "rx_software",
                "software"
            ],
            "type": "ether"
        },
        "ansible_veth1a8d2df": {
            "active": true,
            "device": "veth1a8d2df",
            "features": {
                "busy_poll": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "on",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "off [fixed]",
                "netns_local": "off [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off [fixed]",
                "rx_checksumming": "on",
                "rx_fcs": "off [fixed]",
                "rx_gro_hw": "off [fixed]",
                "rx_udp_tunnel_port_offload": "off [fixed]",
                "rx_vlan_filter": "off [fixed]",
                "rx_vlan_offload": "on",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "on",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "on",
                "tx_checksumming": "on",
                "tx_fcoe_segmentation": "off [fixed]",
                "tx_gre_csum_segmentation": "on",
                "tx_gre_segmentation": "on",
                "tx_gso_partial": "off [fixed]",
                "tx_gso_robust": "off [fixed]",
                "tx_ipip_segmentation": "on",
                "tx_lockless": "on [fixed]",
                "tx_nocache_copy": "off",
                "tx_scatter_gather": "on",
                "tx_scatter_gather_fraglist": "on",
                "tx_sctp_segmentation": "on",
                "tx_sit_segmentation": "on",
                "tx_tcp6_segmentation": "on",
                "tx_tcp_ecn_segmentation": "on",
                "tx_tcp_mangleid_segmentation": "on",
                "tx_tcp_segmentation": "on",
                "tx_udp_tnl_csum_segmentation": "on",
                "tx_udp_tnl_segmentation": "on",
                "tx_vlan_offload": "on",
                "tx_vlan_stag_hw_insert": "on",
                "udp_fragmentation_offload": "on",
                "vlan_challenged": "off [fixed]"
            },
            "hw_timestamp_filters": [],
            "ipv6": [
                {
                    "address": "fe80::8002:5fff:fe45:e177",
                    "prefix": "64",
                    "scope": "link"
                }
            ],
            "macaddress": "82:02:5f:45:e1:77",
            "mtu": 1500,
            "promisc": true,
            "speed": 10000,
            "timestamping": [
                "rx_software",
                "software"
            ],
            "type": "ether"
        },
        "ansible_veth1dbc5ab": {
            "active": true,
            "device": "veth1dbc5ab",
            "features": {
                "busy_poll": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "on",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "off [fixed]",
                "netns_local": "off [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off [fixed]",
                "rx_checksumming": "on",
                "rx_fcs": "off [fixed]",
                "rx_gro_hw": "off [fixed]",
                "rx_udp_tunnel_port_offload": "off [fixed]",
                "rx_vlan_filter": "off [fixed]",
                "rx_vlan_offload": "on",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "on",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "on",
                "tx_checksumming": "on",
                "tx_fcoe_segmentation": "off [fixed]",
                "tx_gre_csum_segmentation": "on",
                "tx_gre_segmentation": "on",
                "tx_gso_partial": "off [fixed]",
                "tx_gso_robust": "off [fixed]",
                "tx_ipip_segmentation": "on",
                "tx_lockless": "on [fixed]",
                "tx_nocache_copy": "off",
                "tx_scatter_gather": "on",
                "tx_scatter_gather_fraglist": "on",
                "tx_sctp_segmentation": "on",
                "tx_sit_segmentation": "on",
                "tx_tcp6_segmentation": "on",
                "tx_tcp_ecn_segmentation": "on",
                "tx_tcp_mangleid_segmentation": "on",
                "tx_tcp_segmentation": "on",
                "tx_udp_tnl_csum_segmentation": "on",
                "tx_udp_tnl_segmentation": "on",
                "tx_vlan_offload": "on",
                "tx_vlan_stag_hw_insert": "on",
                "udp_fragmentation_offload": "on",
                "vlan_challenged": "off [fixed]"
            },
            "hw_timestamp_filters": [],
            "ipv6": [
                {
                    "address": "fe80::2438:17ff:fe26:ec41",
                    "prefix": "64",
                    "scope": "link"
                }
            ],
            "macaddress": "26:38:17:26:ec:41",
            "mtu": 1500,
            "promisc": true,
            "speed": 10000,
            "timestamping": [
                "rx_software",
                "software"
            ],
            "type": "ether"
        },
        "ansible_veth48128c2": {
            "active": true,
            "device": "veth48128c2",
            "features": {
                "busy_poll": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "on",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "off [fixed]",
                "netns_local": "off [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off [fixed]",
                "rx_checksumming": "on",
                "rx_fcs": "off [fixed]",
                "rx_gro_hw": "off [fixed]",
                "rx_udp_tunnel_port_offload": "off [fixed]",
                "rx_vlan_filter": "off [fixed]",
                "rx_vlan_offload": "on",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "on",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "on",
                "tx_checksumming": "on",
                "tx_fcoe_segmentation": "off [fixed]",
                "tx_gre_csum_segmentation": "on",
                "tx_gre_segmentation": "on",
                "tx_gso_partial": "off [fixed]",
                "tx_gso_robust": "off [fixed]",
                "tx_ipip_segmentation": "on",
                "tx_lockless": "on [fixed]",
                "tx_nocache_copy": "off",
                "tx_scatter_gather": "on",
                "tx_scatter_gather_fraglist": "on",
                "tx_sctp_segmentation": "on",
                "tx_sit_segmentation": "on",
                "tx_tcp6_segmentation": "on",
                "tx_tcp_ecn_segmentation": "on",
                "tx_tcp_mangleid_segmentation": "on",
                "tx_tcp_segmentation": "on",
                "tx_udp_tnl_csum_segmentation": "on",
                "tx_udp_tnl_segmentation": "on",
                "tx_vlan_offload": "on",
                "tx_vlan_stag_hw_insert": "on",
                "udp_fragmentation_offload": "on",
                "vlan_challenged": "off [fixed]"
            },
            "hw_timestamp_filters": [],
            "ipv6": [
                {
                    "address": "fe80::6c6f:b0ff:fe2e:db5d",
                    "prefix": "64",
                    "scope": "link"
                }
            ],
            "macaddress": "6e:6f:b0:2e:db:5d",
            "mtu": 1500,
            "promisc": true,
            "speed": 10000,
            "timestamping": [
                "rx_software",
                "software"
            ],
            "type": "ether"
        },
        "ansible_veth59eff92": {
            "active": true,
            "device": "veth59eff92",
            "features": {
                "busy_poll": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "on",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "off [fixed]",
                "netns_local": "off [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off [fixed]",
                "rx_checksumming": "on",
                "rx_fcs": "off [fixed]",
                "rx_gro_hw": "off [fixed]",
                "rx_udp_tunnel_port_offload": "off [fixed]",
                "rx_vlan_filter": "off [fixed]",
                "rx_vlan_offload": "on",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "on",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "on",
                "tx_checksumming": "on",
                "tx_fcoe_segmentation": "off [fixed]",
                "tx_gre_csum_segmentation": "on",
                "tx_gre_segmentation": "on",
                "tx_gso_partial": "off [fixed]",
                "tx_gso_robust": "off [fixed]",
                "tx_ipip_segmentation": "on",
                "tx_lockless": "on [fixed]",
                "tx_nocache_copy": "off",
                "tx_scatter_gather": "on",
                "tx_scatter_gather_fraglist": "on",
                "tx_sctp_segmentation": "on",
                "tx_sit_segmentation": "on",
                "tx_tcp6_segmentation": "on",
                "tx_tcp_ecn_segmentation": "on",
                "tx_tcp_mangleid_segmentation": "on",
                "tx_tcp_segmentation": "on",
                "tx_udp_tnl_csum_segmentation": "on",
                "tx_udp_tnl_segmentation": "on",
                "tx_vlan_offload": "on",
                "tx_vlan_stag_hw_insert": "on",
                "udp_fragmentation_offload": "on",
                "vlan_challenged": "off [fixed]"
            },
            "hw_timestamp_filters": [],
            "ipv6": [
                {
                    "address": "fe80::a016:3cff:fec6:fbf4",
                    "prefix": "64",
                    "scope": "link"
                }
            ],
            "macaddress": "a2:16:3c:c6:fb:f4",
            "mtu": 1500,
            "promisc": true,
            "speed": 10000,
            "timestamping": [
                "rx_software",
                "software"
            ],
            "type": "ether"
        },
        "ansible_veth609055f": {
            "active": true,
            "device": "veth609055f",
            "features": {
                "busy_poll": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "on",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "off [fixed]",
                "netns_local": "off [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off [fixed]",
                "rx_checksumming": "on",
                "rx_fcs": "off [fixed]",
                "rx_gro_hw": "off [fixed]",
                "rx_udp_tunnel_port_offload": "off [fixed]",
                "rx_vlan_filter": "off [fixed]",
                "rx_vlan_offload": "on",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "on",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "on",
                "tx_checksumming": "on",
                "tx_fcoe_segmentation": "off [fixed]",
                "tx_gre_csum_segmentation": "on",
                "tx_gre_segmentation": "on",
                "tx_gso_partial": "off [fixed]",
                "tx_gso_robust": "off [fixed]",
                "tx_ipip_segmentation": "on",
                "tx_lockless": "on [fixed]",
                "tx_nocache_copy": "off",
                "tx_scatter_gather": "on",
                "tx_scatter_gather_fraglist": "on",
                "tx_sctp_segmentation": "on",
                "tx_sit_segmentation": "on",
                "tx_tcp6_segmentation": "on",
                "tx_tcp_ecn_segmentation": "on",
                "tx_tcp_mangleid_segmentation": "on",
                "tx_tcp_segmentation": "on",
                "tx_udp_tnl_csum_segmentation": "on",
                "tx_udp_tnl_segmentation": "on",
                "tx_vlan_offload": "on",
                "tx_vlan_stag_hw_insert": "on",
                "udp_fragmentation_offload": "on",
                "vlan_challenged": "off [fixed]"
            },
            "hw_timestamp_filters": [],
            "ipv6": [
                {
                    "address": "fe80::981d:59ff:fed2:88b0",
                    "prefix": "64",
                    "scope": "link"
                }
            ],
            "macaddress": "9a:1d:59:d2:88:b0",
            "mtu": 1500,
            "promisc": true,
            "speed": 10000,
            "timestamping": [
                "rx_software",
                "software"
            ],
            "type": "ether"
        },
        "ansible_veth63ec05e": {
            "active": true,
            "device": "veth63ec05e",
            "features": {
                "busy_poll": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "on",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "off [fixed]",
                "netns_local": "off [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off [fixed]",
                "rx_checksumming": "on",
                "rx_fcs": "off [fixed]",
                "rx_gro_hw": "off [fixed]",
                "rx_udp_tunnel_port_offload": "off [fixed]",
                "rx_vlan_filter": "off [fixed]",
                "rx_vlan_offload": "on",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "on",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "on",
                "tx_checksumming": "on",
                "tx_fcoe_segmentation": "off [fixed]",
                "tx_gre_csum_segmentation": "on",
                "tx_gre_segmentation": "on",
                "tx_gso_partial": "off [fixed]",
                "tx_gso_robust": "off [fixed]",
                "tx_ipip_segmentation": "on",
                "tx_lockless": "on [fixed]",
                "tx_nocache_copy": "off",
                "tx_scatter_gather": "on",
                "tx_scatter_gather_fraglist": "on",
                "tx_sctp_segmentation": "on",
                "tx_sit_segmentation": "on",
                "tx_tcp6_segmentation": "on",
                "tx_tcp_ecn_segmentation": "on",
                "tx_tcp_mangleid_segmentation": "on",
                "tx_tcp_segmentation": "on",
                "tx_udp_tnl_csum_segmentation": "on",
                "tx_udp_tnl_segmentation": "on",
                "tx_vlan_offload": "on",
                "tx_vlan_stag_hw_insert": "on",
                "udp_fragmentation_offload": "on",
                "vlan_challenged": "off [fixed]"
            },
            "hw_timestamp_filters": [],
            "ipv6": [
                {
                    "address": "fe80::188d:d6ff:fe97:4a28",
                    "prefix": "64",
                    "scope": "link"
                }
            ],
            "macaddress": "1a:8d:d6:97:4a:28",
            "mtu": 1500,
            "promisc": true,
            "speed": 10000,
            "timestamping": [
                "rx_software",
                "software"
            ],
            "type": "ether"
        },
        "ansible_veth847cfc7": {
            "active": true,
            "device": "veth847cfc7",
            "features": {
                "busy_poll": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "on",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "off [fixed]",
                "netns_local": "off [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off [fixed]",
                "rx_checksumming": "on",
                "rx_fcs": "off [fixed]",
                "rx_gro_hw": "off [fixed]",
                "rx_udp_tunnel_port_offload": "off [fixed]",
                "rx_vlan_filter": "off [fixed]",
                "rx_vlan_offload": "on",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "on",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "on",
                "tx_checksumming": "on",
                "tx_fcoe_segmentation": "off [fixed]",
                "tx_gre_csum_segmentation": "on",
                "tx_gre_segmentation": "on",
                "tx_gso_partial": "off [fixed]",
                "tx_gso_robust": "off [fixed]",
                "tx_ipip_segmentation": "on",
                "tx_lockless": "on [fixed]",
                "tx_nocache_copy": "off",
                "tx_scatter_gather": "on",
                "tx_scatter_gather_fraglist": "on",
                "tx_sctp_segmentation": "on",
                "tx_sit_segmentation": "on",
                "tx_tcp6_segmentation": "on",
                "tx_tcp_ecn_segmentation": "on",
                "tx_tcp_mangleid_segmentation": "on",
                "tx_tcp_segmentation": "on",
                "tx_udp_tnl_csum_segmentation": "on",
                "tx_udp_tnl_segmentation": "on",
                "tx_vlan_offload": "on",
                "tx_vlan_stag_hw_insert": "on",
                "udp_fragmentation_offload": "on",
                "vlan_challenged": "off [fixed]"
            },
            "hw_timestamp_filters": [],
            "ipv6": [
                {
                    "address": "fe80::dc50:e3ff:fe14:4a8d",
                    "prefix": "64",
                    "scope": "link"
                }
            ],
            "macaddress": "de:50:e3:14:4a:8d",
            "mtu": 1500,
            "promisc": true,
            "speed": 10000,
            "timestamping": [
                "rx_software",
                "software"
            ],
            "type": "ether"
        },
        "ansible_veth96a836d": {
            "active": true,
            "device": "veth96a836d",
            "features": {
                "busy_poll": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "on",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "off [fixed]",
                "netns_local": "off [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off [fixed]",
                "rx_checksumming": "on",
                "rx_fcs": "off [fixed]",
                "rx_gro_hw": "off [fixed]",
                "rx_udp_tunnel_port_offload": "off [fixed]",
                "rx_vlan_filter": "off [fixed]",
                "rx_vlan_offload": "on",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "on",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "on",
                "tx_checksumming": "on",
                "tx_fcoe_segmentation": "off [fixed]",
                "tx_gre_csum_segmentation": "on",
                "tx_gre_segmentation": "on",
                "tx_gso_partial": "off [fixed]",
                "tx_gso_robust": "off [fixed]",
                "tx_ipip_segmentation": "on",
                "tx_lockless": "on [fixed]",
                "tx_nocache_copy": "off",
                "tx_scatter_gather": "on",
                "tx_scatter_gather_fraglist": "on",
                "tx_sctp_segmentation": "on",
                "tx_sit_segmentation": "on",
                "tx_tcp6_segmentation": "on",
                "tx_tcp_ecn_segmentation": "on",
                "tx_tcp_mangleid_segmentation": "on",
                "tx_tcp_segmentation": "on",
                "tx_udp_tnl_csum_segmentation": "on",
                "tx_udp_tnl_segmentation": "on",
                "tx_vlan_offload": "on",
                "tx_vlan_stag_hw_insert": "on",
                "udp_fragmentation_offload": "on",
                "vlan_challenged": "off [fixed]"
            },
            "hw_timestamp_filters": [],
            "ipv6": [
                {
                    "address": "fe80::e423:34ff:feb4:6dd8",
                    "prefix": "64",
                    "scope": "link"
                }
            ],
            "macaddress": "e6:23:34:b4:6d:d8",
            "mtu": 1500,
            "promisc": true,
            "speed": 10000,
            "timestamping": [
                "rx_software",
                "software"
            ],
            "type": "ether"
        },
        "ansible_virbr0": {
            "active": false,
            "device": "virbr0",
            "features": {
                "busy_poll": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "off [requested on]",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "off [fixed]",
                "netns_local": "on [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off [fixed]",
                "rx_checksumming": "off [fixed]",
                "rx_fcs": "off [fixed]",
                "rx_gro_hw": "off [fixed]",
                "rx_udp_tunnel_port_offload": "off [fixed]",
                "rx_vlan_filter": "off [fixed]",
                "rx_vlan_offload": "off [fixed]",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "off [fixed]",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "off [fixed]",
                "tx_checksumming": "on",
                "tx_fcoe_segmentation": "off [requested on]",
                "tx_gre_csum_segmentation": "on",
                "tx_gre_segmentation": "on",
                "tx_gso_partial": "on",
                "tx_gso_robust": "off [requested on]",
                "tx_ipip_segmentation": "on",
                "tx_lockless": "on [fixed]",
                "tx_nocache_copy": "off",
                "tx_scatter_gather": "on",
                "tx_scatter_gather_fraglist": "on",
                "tx_sctp_segmentation": "off [requested on]",
                "tx_sit_segmentation": "on",
                "tx_tcp6_segmentation": "on",
                "tx_tcp_ecn_segmentation": "on",
                "tx_tcp_mangleid_segmentation": "on",
                "tx_tcp_segmentation": "on",
                "tx_udp_tnl_csum_segmentation": "on",
                "tx_udp_tnl_segmentation": "on",
                "tx_vlan_offload": "on",
                "tx_vlan_stag_hw_insert": "on",
                "udp_fragmentation_offload": "off [requested on]",
                "vlan_challenged": "off [fixed]"
            },
            "hw_timestamp_filters": [],
            "id": "8000.52540096cfdd",
            "interfaces": [
                "virbr0-nic"
            ],
            "ipv4": {
                "address": "192.168.122.1",
                "broadcast": "192.168.122.255",
                "netmask": "255.255.255.0",
                "network": "192.168.122.0"
            },
            "macaddress": "52:54:00:96:cf:dd",
            "mtu": 1500,
            "promisc": false,
            "stp": true,
            "timestamping": [
                "rx_software",
                "software"
            ],
            "type": "bridge"
        },
        "ansible_virbr0_nic": {
            "active": false,
            "device": "virbr0-nic",
            "features": {
                "busy_poll": "off [fixed]",
                "fcoe_mtu": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "off [fixed]",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "off [fixed]",
                "netns_local": "off [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off [fixed]",
                "rx_checksumming": "off [fixed]",
                "rx_fcs": "off [fixed]",
                "rx_gro_hw": "off [fixed]",
                "rx_udp_tunnel_port_offload": "off [fixed]",
                "rx_vlan_filter": "off [fixed]",
                "rx_vlan_offload": "off [fixed]",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "off [fixed]",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "off",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "off [requested on]",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "off [fixed]",
                "tx_checksumming": "off",
                "tx_fcoe_segmentation": "off [fixed]",
                "tx_gre_csum_segmentation": "off [fixed]",
                "tx_gre_segmentation": "off [fixed]",
                "tx_gso_partial": "off [fixed]",
                "tx_gso_robust": "off [fixed]",
                "tx_ipip_segmentation": "off [fixed]",
                "tx_lockless": "on [fixed]",
                "tx_nocache_copy": "off",
                "tx_scatter_gather": "on",
                "tx_scatter_gather_fraglist": "on",
                "tx_sctp_segmentation": "off [fixed]",
                "tx_sit_segmentation": "off [fixed]",
                "tx_tcp6_segmentation": "off [requested on]",
                "tx_tcp_ecn_segmentation": "off [requested on]",
                "tx_tcp_mangleid_segmentation": "off",
                "tx_tcp_segmentation": "off [requested on]",
                "tx_udp_tnl_csum_segmentation": "off [fixed]",
                "tx_udp_tnl_segmentation": "off [fixed]",
                "tx_vlan_offload": "on",
                "tx_vlan_stag_hw_insert": "on",
                "udp_fragmentation_offload": "off [requested on]",
                "vlan_challenged": "off [fixed]"
            },
            "hw_timestamp_filters": [],
            "macaddress": "52:54:00:96:cf:dd",
            "mtu": 1500,
            "promisc": true,
            "timestamping": [
                "rx_software",
                "software"
            ],
            "type": "ether"
        },
        "ansible_virtualization_role": "guest",
        "ansible_virtualization_type": "kvm",
        "gather_subset": [
            "all"
        ],
        "module_setup": true
    },
    "changed": false
}

```













