---
title: shell
description: shell
date: 2023-10-12
weight: 60000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}
file 模块可以帮助我们完成一些对文件的基本操作。比如，创建文件或目录、删除文件或目录、修改文件权限等

一般配合注册变量
{{< /alert >}}

{{< alert color="secondary" >}}

### 命令执行结果
- change： 表示执行命令的状态，如果命令执行了，则为 true；

- cmd： 表示的则是你当前执行的命令；

- delta： 表示命令执行所花费的时间

- start： 表示命令开始执行的时间

- end： 表示命令结束的时间

- failed： 表示命令执行的结果，如果为 false 则表示命令执行成功，true 则表示命令执行失败

- **rc**： 表示命令执行的返回码（return code），0 表示执行成功；shell命令返回的code，相当于shell里面的 $?

- stderr：命令输出的标准错误信息

- stderr_lines： 按换行符分割输出的内容，在多行输出时，显示的效果比 stderr 更加直观

- **stdout**： 命令的标准输出信息

- stdout_lines： 按换行符分割输出的内容，在多行输出时，结果更加直观



### 注意：
- 若需要中间任意一条命令出错就退出，那么尽量使用 && 的方式。



{{< /alert >}}



## 选项介绍

```bash

[root@k8s-node-77 ansible]# ansible-doc -s shell
- name: Execute shell commands on targets
  shell:
      chdir:                 # Change into this directory before running the command.
      cmd:                   # The command to run followed by optional arguments.
      creates:               # A filename, when it already exists, this step will *not* be run.
      executable:            # Change the shell used to execute the command. This expects an absolute path to the executable.
      free_form:             # The shell module takes a free form command to run, as a string. There is no actual parameter named 'free form'. See the examples on how to use this module.
      removes:               # A filename, when it does not exist, this step will *not* be run.
      stdin:                 # Set the stdin of the command directly to the specified value.
      stdin_add_newline:     # Whether to append a newline to stdin data.
      warn:                  # Whether to enable task warnings.



```

{{< alert color="secondary" >}}

最基本最简单的用法，shell 后直接跟要执行的命令即可。可以使用 && 将多条命令连接为一条命令。

{{< /alert >}}



## 举例说明
```yaml
- name: shell netstat
  shell: netstat -ntlp
```

```bash
TASK [os : shell netstat] ******************************************
changed: [node1] => changed=true
  cmd: netstat -ntlp
  delta: '0:00:00.829044'
  end: '2023-10-15 22:15:25.559031'
  rc: 0
  start: '2023-10-15 22:15:24.729987'
  stderr: ''
  stderr_lines: <omitted>
  stdout: |-
    Active Internet connections (only servers)
    Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
    tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      3729/sshd
    tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN      3730/cupsd
    tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN      3966/master
    tcp        0      0 127.0.0.1:10248         0.0.0.0:*               LISTEN      25211/kubelet
    tcp        0      0 127.0.0.1:10249         0.0.0.0:*               LISTEN      25318/kube-proxy
    tcp        0      0 127.0.0.1:9099          0.0.0.0:*               LISTEN      26030/calico-node
    tcp        0      0 0.0.0.0:36431           0.0.0.0:*               LISTEN      9066/rpc.statd
    tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN      3354/rpcbind
    tcp        0      0 0.0.0.0:179             0.0.0.0:*               LISTEN      26209/bird
    tcp        0      0 127.0.0.1:15411         0.0.0.0:*               LISTEN      23371/containerd
    tcp6       0      0 :::22                   :::*                    LISTEN      3729/sshd
    tcp6       0      0 ::1:631                 :::*                    LISTEN      3730/cupsd
    tcp6       0      0 ::1:25                  :::*                    LISTEN      3966/master
    tcp6       0      0 :::15067                :::*                    LISTEN      3736/node_exporter
    tcp6       0      0 :::31135                :::*                    LISTEN      9066/rpc.statd
    tcp6       0      0 :::33060                :::*                    LISTEN      17325/./bin/mysqld
    tcp6       0      0 :::3333                 :::*                    LISTEN      17325/./bin/mysqld
    tcp6       0      0 :::10250                :::*                    LISTEN      25211/kubelet
    tcp6       0      0 :::111                  :::*                    LISTEN      3354/rpcbind
    tcp6       0      0 :::10256                :::*                    LISTEN      25318/kube-proxy
  stdout_lines: <omitted>


```




```yaml

- name: Exec single shell command # 执行单条命令
  shell: echo "1"
- name: Exec multi commands using && # 通过&&连接执行多条命令
  shell: echo "1" && echo "2"


    # 忽略错误
- name: Exec shell and ignore errors
  shell: echo "ignore_errors" && exit 1
  ignore_errors: true
```

## 案例

#### 包含通配符
> `/bin/bash -c 'xxx'`

```yaml
# mv node_exporter
- name: mv node_exporter
  become: yes
  shell:
    cmd: "/bin/bash -c 'mv {{ install_tmpdir }}/node_exporter-{{ node_exporter_version }}.linux-*/node_exporter {{ node_exporter_base_dir }}/bin/'"
```


