---
title: command
description: >
  命令模块详解.
date: 2023-10-15
weight: 40000
---

{{< alert >}}
这个模块可以直接在远程主机上执行命令，并将结果返回本主机。
{{< /alert >}}

{{< alert color="secondary" >}}

ansible的输出的颜色提示说明:

黄色：对远程节点进行相应修改

绿色：对远程节点不进行相应修改，或者只是对远程节点信息进行查看

红色：操作命令有异常

紫色：表示对命令执行发出警告信息会给一下建议

{{< /alert >}}



## 选项介绍

```bash

[root@k8s-node-77 ansible]# ansible-doc -s command
- name: Execute commands on targets
  command:
      argv:                  # Passes the command as a list rather than a string. Use `argv' to avoid quoting values that would otherwise be interpreted incorrectly (for example "user name"). Only the string or the list form can be provided, not both.  One or the other
                               must be provided.
      chdir:                 # Change into this directory before running the command.
      cmd:                   # The command to run.
      creates:               # A filename or (since 2.0) glob pattern. If it already exists, this step *won't* be run.
      free_form:             # The command module takes a free form command to run. There is no actual parameter named 'free form'.
      removes:               # A filename or (since 2.0) glob pattern. If it already exists, this step *will* be run.
      stdin:                 # Set the stdin of the command directly to the specified value.
      stdin_add_newline:     # If set to `yes', append a newline to stdin data.
      strip_empty_ends:      # Strip empty lines from the end of stdout/stderr in result.
      warn:                  # Enable or disable task warnings.




```

{{< alert color="secondary" >}}



{{< /alert >}}



```yaml

- name: create directory
  command: name='netstat -ntlp'















```









