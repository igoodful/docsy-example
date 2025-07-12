---
title: 09. 系统管理
description: 系统管理
date: 2023-10-12
weight: 900


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
digraph "system" {
        rankdir=TB;
node [
        fontsize = "12";
        shape = octagon;
];
"系统管理"->"service";
"系统管理"->"systemd";
"系统管理"->"cron";
"系统管理"->"firewalld";
"系统管理"->"hostname";
"系统管理"->"timezone";
"系统管理"->"hostname";
"系统管理"->"hostname";
"系统管理"->"pam_limits";
}
```



## 一、service

首先看一下服务列表里有没有这个服务：
```bash
systemctl list-unit-files --type=service
```

如果有的话：
```bash
systemctl daemon-reload
```


即可

| 参数      | 默认值 | 说明           | 示例 |
|:----------|:-------|:---------------|:-----|
| name      |        | 服务名称       |      |
| arguments |        | 参数           |      |
| state     |        | started/absent |      |
| enabled   |        | 是否开机自启   |      |


最佳示例
- 删除原有service
- 添加新service
- systemctl daemon-reload
- enable
- started

```yaml
---
# copy mysql.service.j2

- name: rm -rf /etc/systemd/system/multi-user.target.wants/mysql_{{ mysql_port }}.service
  become: yes
  file:
    path: /etc/systemd/system/multi-user.target.wants/mysql_{{ mysql_port }}.service
    state: absent
  ignore_errors: true

- name: rm -rf /usr/lib/systemd/system/mysql_{{ mysql_port }}.service
  become: yes
  file:
    path: /usr/lib/systemd/system/mysql_{{ mysql_port }}.service
    state: absent
  ignore_errors: true

- name: Copy mysql.service.j2 template
  become: yes
  template:
    src: mysql.service.j2
    dest: "/usr/lib/systemd/system/mysql_{{ mysql_port }}.service"
    owner: "{{ ansible_user }}"
    group: "{{ ansible_user }}"
    mode: "0644"
  when: mysql_port is defined

- name: systemctl daemon-reload
  become: yes
  shell:
    cmd: systemctl daemon-reload

- name: enabled mysql_{{ mysql_port }}
  become: yes
  service:
    name: mysql_{{ mysql_port }}
    enabled: yes

- name: started mysql_{{ mysql_port }}
  become: yes
  service:
    name: mysql_{{ mysql_port }}
    state: started
```


```yaml
- name: Manage services
  service:
      arguments:
      enabled:
      name:
      pattern:
      runlevel:
      sleep:
      state:
      use:

```

#### 1. 启动
```yaml
- name: Start service httpd, if not started
  become: yes
  service:
    name: httpd
    state: started
```

#### 2. 重启
```yaml
- name: Restart service httpd, in all cases
  become: yes
  service:
    name: httpd
    state: restarted
```

#### 3. 停止
```yaml
- name: Stop service httpd, if started
  become: yes
  service:
    name: httpd
    state: stopped
```

#### 4. 重新加载
```yaml
- name: Reload service httpd, in all cases
  become: yes
  service:
    name: httpd
    state: reloaded
```

#### 5. 开机自起
```yaml
- name: Enable service httpd, and not touch the state
  service:
    name: httpd
    enabled: yes
```


#### 6. 删除开机自起
```yaml
- name: Disabled service httpd, and not touch the state
  become: yes
  service:
    name: httpd
    enabled: no
```

#### 7. docker service
```yaml
- name: set docker systemd service unit
  template:
    src: docker.service.j2
    dest: /etc/systemd/system/docker.service
  become: yes

- name: create /etc/docker/ directory
  file:
    path: /etc/docker
    state: directory
  become: yes

- name: set /etc/docker/daemon.json
  template:
    src: daemon.json.j2
    dest: /etc/docker/daemon.json
    mode: 0644
  when: docker_registry_insecure | bool
  become: yes

- name: load systemd docker service unit
  service:
    name: docker
    state: restarted
    enabled: yes
    daemon_reload: yes
  become: True

```



## 二、systemd

```yaml
- name: generate exporter conf file
  template:
    src: rds_config.ini.j2
    dest: "{{ exporter_conf_file }}"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0644

- name: generate exporter service file
  template:
    src: rds-greatrds-exporter.service.j2
    dest: "{{ exporter_service_file }}"
    mode: 0644
  become: yes

- name: start greatrds-exporter service
  systemd:
    state: restarted
    enabled: yes
    daemon_reload: yes
    name: rds-greatrds-exporter
  become: yes

```


## 三、cron

| 参数         | 默认值       | 说明                    | 示例 |
|:-------------|:-------------|:------------------------|:-----|
| name         |              | 计划名称                |      |
| job          |              | 命令或者脚本            |      |
| minute       |              |                         |      |
| hour         |              |                         |      |
| day          |              |                         |      |
| month        |              |                         |      |
| weekday      |              |                         |      |
| state        |              | present/absent          |      |
| disabled     |              | 是否禁用                |      |
| backup       |              | 是否备份                |      |
| user         |              | 用户                    |      |
| cron_file    | /etc/crontab | 绝对路径，与user一起使用 |      |
| special_time | /etc/crontab | 绝对路径，与user一起使用 |      |




```yaml
- name: Manage cron.d and crontab entries
  cron:
      backup:
      cron_file:
      day:
      disabled:
      env:
      hour:
      insertafter:
      insertbefore:
      job:
      minute:
      month:
      name:
      reboot:
      special_time:
      state:
      user:
      weekday:
```

```yaml

- name: Creates an entry like "NEW_APP_NAME=New-Ansible-APP" and insert it before USERNAME declaration
  cron:
    name: NEW_APP_NAME
    env: yes
    value: New-Ansible-APP
    insertbefore: USERNAME

- name: Removes "APP_NAME" environment variable from crontab
  cron:
    name: APP_NAME
    env: yes
    state: absent

- name: Creates a cron file under /etc/cron.d/ with root
  become: yes
  cron:
    name: sync time at every Tuesday
    weekday: "2"
    minute: "0"
    hour: "12"
    user: root
    job: "ntpdate 182.92.12.11 |tee /tmp/synctime.log"
    cron_file: ansible_sync_time


- name: Creates a cron file under /etc/cron.d/ with ansible
  become: yes
  cron:
    name: sync time at every month first day
    day: "1"
    minute: "0"
    hour: "12"
    user: ansible
    job: "sudo ntpdate 182.92.12.11 |tee /tmp/synctime.log"
    cron_file: ansible_sync_time


- name: Creates a cron file under /etc/crontab with root
  become: yes
  cron:
    name: sync time at every Tuesday
    weekday: "2"
    minute: "0"
    hour: "12"
    user: root
    job: "ntpdate 182.92.12.11 |tee /tmp/synctime.log"
    cron_file: /etc/crontab


- name: Disable a crontab task with state=present
  cron:
    name: "print the variables when reboot"
    disabled: yes
    state: present


```

#### 禁用定时任务
```yaml
- name: Disable a crontab task with state=absent
  cron:
    name: "a job for reboot"
    disabled: yes
```

#### 删除定时任务
```yaml
- name: Disable a crontab task with state=absent
  cron:
    name: "a job for reboot"
    disabled: yes
    state: absent
```

- 使用 lineinfile 模块添加crnotab 定时任务

```yaml
- name: set crontab for tan create-index.html
  lineinfile:
    path: /etc/crontab
    line: "#Ansible: tan create index.html\n*/30 * * * * root php-5.6 /data/project/store/bin/cli request_uri='/Cli/System/createTanIndex'"
```




## 四、timezone



## 五、hostname





## 六、firewalld



## 七、selinux


```viz-dot
digraph "selinux" {
        rankdir=TB;
node [
        fontsize = "12";
        shape = octagon;
];

"selinux"->"state";
"selinux"->"policy";
"selinux"->"configfile";
"state"->"disabled";
"state"->"enforcing";
"state"->"permissive";

"policy"->"targeted";




}
```





## 八、subversion

```yaml
- name: Checkout subversion repository to specified folder
  ansible.builtin.subversion:
    repo: svn+ssh://an.example.org/path/to/repo
    dest: /src/checkout

- name: Export subversion directory to folder
  ansible.builtin.subversion:
    repo: svn+ssh://an.example.org/path/to/repo
    dest: /src/export
    export: yes

- name: Get information about the repository whether or not it has already been cloned locally
  ansible.builtin.subversion:
    repo: svn+ssh://an.example.org/path/to/repo
    dest: /src/checkout
    checkout: no
    update: no
```


## 九、wait_for
> wait_for 是用来在规定时间内检测，状态是否为所期望的状态，才执行后续的操作

#### 1. 选项
| 选项            | 默认值  | 备注                                                          |
|:----------------|:--------|:--------------------------------------------------------------|
| path            |         | 等待指定文件存在，与port互斥                                   |
| port            |         | 检查端口，与path互斥                                           |
| search_regex    |         | 等待字符串出现在指定文件中                                    |
| state           | started | started/stopped/drained; absent/present                       |
| delay           | 0       | 在timeout时间内，间隔多少秒检查一次                            |
| timeout         | 300     | 等待的最大秒数                                                |
| connect_timeout |         | 在下一个事情发生前等待链接的时间，单位是秒                     |
| sleep           |         | 权限                                                          |
| host            |         | 递归                                                          |
| exclude_hosts   |         | 与state=drained一起使用。指定在活跃TCP链接要忽略的主机或IP列表 |
| msg             |         | 递归                                                          |


state
- 当检查端口的时候，started会确保端口打开；stopped会确保端口关闭；drained会检查活跃的链接。
- 当检查文件或搜索字符串的时候，present和started会确保文件或字符串存在。absent会确保文件不存在或被移除。

exclude_hosts
- 10.2.1.2,10.2.1.3 忽略来自10.2.1.2和10.2.1.3上的连接

#### 2. 示例
```yaml
# 等待8080端口已正常监听，才开始下一个任务，直到超时
- wait_for: port=8080 state=started

# 等待8000端口正常监听，每隔10s检查一次，直至等待超时
- wait_for: port=8000 delay=10

# 等待8000端口直至有连接建立
- wait_for: host=0.0.0.0 port=8000 delay=10 state=drained

# 等待8000端口有连接建立，如果连接来自10.2.1.2或者10.2.1.3，则忽略。
- wait_for: host=0.0.0.0 port=8000 state=drained exclude_hosts=10.2.1.2,10.2.1.3

# 等待/tmp/foo文件已创建
- wait_for: path=/tmp/foo

# 等待/tmp/foo文件已创建，而且该文件中需要包含completed字符串
- wait_for: path=/tmp/foo search_regex=completed

# 等待/var/lock/file.lock被删除
- wait_for: path=/var/lock/file.lock state=absent

# 等待指定的进程被销毁
- wait_for: path=/proc/3466/status state=absent

# 等待openssh启动，10s检查一次
- local_action: wait_for port=22 host="{{ ansible_ssh_host | default(inventory_hostname) }}" search_regex=OpenSSH delay=10

# 休息300s后再继续
- name: Sleep for 300 seconds and continue with play
  ansible.builtin.wait_for:
    timeout: 300
  delegate_to: localhost

# 端口
- name: Wait for port 8000 to become open on the host, don't start checking for 10 seconds
  ansible.builtin.wait_for:
    port: 8000
    delay: 10

# 端口
- name: Waits for port 8000 of any IP to close active connections, don't start checking for 10 seconds
  ansible.builtin.wait_for:
    host: 0.0.0.0
    port: 8000
    delay: 10
    state: drained

# 端口
- name: Wait for port 8000 of any IP to close active connections, ignoring connections for specified hosts
  ansible.builtin.wait_for:
    host: 0.0.0.0
    port: 8000
    state: drained
    exclude_hosts: 10.2.1.2,10.2.1.3

# 文件
- name: Wait until the file /tmp/foo is present before continuing
  ansible.builtin.wait_for:
    path: /tmp/foo

# 文件
- name: Wait until the string "completed" is in the file /tmp/foo before continuing
  ansible.builtin.wait_for:
    path: /tmp/foo
    search_regex: completed

# 文件
- name: Wait until regex pattern matches in the file /tmp/foo and print the matched group
  ansible.builtin.wait_for:
    path: /tmp/foo
    search_regex: completed (?P<task>\w+)
  register: waitfor
- ansible.builtin.debug:
    msg: Completed {{ waitfor['match_groupdict']['task'] }}

# 文件
- name: Wait until the lock file is removed
  ansible.builtin.wait_for:
    path: /var/lock/file.lock
    state: absent

# 文件
- name: Wait until the process is finished and pid was destroyed
  ansible.builtin.wait_for:
    path: /proc/3466/status
    state: absent

# 文件
- name: Output customized message when failed
  ansible.builtin.wait_for:
    path: /tmp/foo
    state: present
    msg: Timeout to find file /tmp/foo

# Do not assume the inventory_hostname is resolvable and delay 10 seconds at start
- name: Wait 300 seconds for port 22 to become open and contain "OpenSSH"
  ansible.builtin.wait_for:
    port: 22
    host: '{{ (ansible_ssh_host|default(ansible_host))|default(inventory_hostname) }}'
    search_regex: OpenSSH
    delay: 10
  connection: local

# Same as above but you normally have ansible_connection set in inventory, which overrides 'connection'
- name: Wait 300 seconds for port 22 to become open and contain "OpenSSH"
  ansible.builtin.wait_for:
    port: 22
    host: '{{ (ansible_ssh_host|default(ansible_host))|default(inventory_hostname) }}'
    search_regex: OpenSSH
    delay: 10
  vars:
    ansible_connection: local
```

## 十、iptables



## 十一、pam_limits


#### 1. 选项

| 参数       | 默认值                    | 说明              | 示例 |
|:-----------|:--------------------------|:------------------|:-----|
| dest       | /etc/security/limits.conf |                   |      |
| domain     | *                         |                   |      |
| limit_type |                           | 必填：soft/hard    |      |
| limit_item |                           | 必填：nofile/nproc |      |
| value      |                           | 1024000           |      |

limit_type
- hard
- soft
- \-

limit_item
- core
- data
- fsize
- memlock
- nofile
- rss
- stack
- cpu
- nproc
- as
- maxlogins
- maxsyslogins
- priority
- locks
- sigpending
- msgqueue
- nice
- rtprio
- chroot


#### 2. 示例

```yaml
---
- name: Set sysctl file limiits
  become: yes
  pam_limits:
     dest:  "{{ item.dest }}"
     domain: "*"
     limit_type:  "{{ item.limit_type }}"
     limit_item:  "{{ item.limit_item }}"
     value:  "{{ item.value }}"
  with_items:
     - { dest:  '/etc/security/limits.conf' ,limit_type:  'soft' ,limit_item:  'nofile' , value:  '1024000'  }
     - { dest:  '/etc/security/limits.conf' ,limit_type:  'hard' ,limit_item:  'nofile' , value:  '1024000' }
     - { dest:  '/etc/security/limits.conf' ,limit_type:  'soft' ,limit_item:  'nproc' , value:  '1024000'  }
     - { dest:  '/etc/security/limits.conf' ,limit_type:  'hard' ,limit_item:  'nproc' , value:  '1024000'  }
     - { dest:  '/etc/security/limits.conf' ,limit_type:  'soft' ,limit_item:  'sigpending' , value:  '1024000'  }
     - { dest:  '/etc/security/limits.conf' ,limit_type:  'hard' ,limit_item:  'sigpending' , value:  '1024000'  }
  tags:
     - setlimits

```


```yaml
- name: Add or modify nofile soft limit for the user joe
  community.general.pam_limits:
    domain: joe
    limit_type: soft
    limit_item: nofile
    value: 64000

- name: Add or modify fsize hard limit for the user smith. Keep or set the maximal value
  community.general.pam_limits:
    domain: smith
    limit_type: hard
    limit_item: fsize
    value: 1000000
    use_max: true

- name: Add or modify memlock, both soft and hard, limit for the user james with a comment
  community.general.pam_limits:
    domain: james
    limit_type: '-'
    limit_item: memlock
    value: unlimited
    comment: unlimited memory lock for james

- name: Add or modify hard nofile limits for wildcard domain
  community.general.pam_limits:
    domain: '*'
    limit_type: hard
    limit_item: nofile
    value: 39693561
```









