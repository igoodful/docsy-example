---
title: libncurses.so.6与libssl.so.1.1缺少
date: 2017-01-05
description: >
  mysql客户端命令不正常.


---

{{< alert >}}


{{< /alert >}}

 ### libncurses.so.6 libssl.so.1.1

```sql
TASK [check : Check Database Service status] *******************
fatal: [controller-2]: FAILED! => changed=false
  attempts: 3
  cmd:
  - mysql
  - -e
  - select 1
  delta: '0:00:00.477624'
  end: '2023-10-23 22:13:20.349611'
  msg: non-zero return code
  rc: 127
  start: '2023-10-23 22:13:19.871987'
  stderr: 'mysql: error while loading shared libraries: libncurses.so.6: cannot open shared object file: No such file or directory'
  stderr_lines: <omitted>
  stdout: ''
  stdout_lines: <omitted>
fatal: [controller-1]: FAILED! => changed=false
  attempts: 3
  cmd:
  - mysql
  - -e
  - select 1
  delta: '0:00:00.609383'
  end: '2023-10-23 22:13:20.846884'
  msg: non-zero return code
  rc: 127
  start: '2023-10-23 22:13:20.237501'
  stderr: 'mysql: error while loading shared libraries: libssl.so.1.1: cannot open shared object file: No such file or directory'
  stderr_lines: <omitted>
  stdout: ''
  stdout_lines: <omitted>

PLAY RECAP ******************************************************
controller-1               : ok=189  changed=98   unreachable=0    failed=1    skipped=17   rescued=0    ignored=2
controller-2               : ok=144  changed=71   unreachable=0    failed=1    skipped=9    rescued=0    ignored=2



```

### 解决

```sql
# "创建/bin/mysql到正常的mysql命令的软连接"

ln -s /home/work/mysql_3333/bin/mysql /bin/mysql


```








