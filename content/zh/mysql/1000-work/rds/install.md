---
title: 安装 RDS
description: 规范部署
date: 2023-10-23
weight: 10



---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
### 场景描述：
切记不要将对RDS安装目录和docker安装目录整体进行chown和chmod
{{< /alert >}}


### 创建用户：mysql
> 更改用户密码:
>
> echo "123456" | passwd \-\-stdin mysql
>
> echo "mysql:123456" | chpasswd



```sql

useradd mysql

echo "QWer12#$" | passwd --stdin mysql

sed

echo "mysql   ALL=(ALL)       NOPASSWD: ALL" >> /etc/sudoers

```

### 关闭 selinux
```sql
getenforce

setenforce 0

vim /etc/selinux/config

```

### 关闭 firewalld
```sql
systemctl stop firewalld

systemctl disable firewalld

```

### sshpass socat ntp
```sql
yum -y install chrony pciutils jq sshpass socat

yum -y install bind-utils net-tools ntpdate

systemctl enable chronyd.service

systemctl start chronyd.service

sed -i -e '/^server/s/^/#/' -e '1a server ntp.aliyun.com iburst'   /etc/chrony.conf

systemctl restart chronyd.service

```

### ntpd

```sql
systemctl start ntpd.service;

systemctl enable ntpd.service;

systemctl status ntpd.service;

```





### 确保mysql命令能正常运行

```sql
mysql --help

```


### 删除包：requests

> 若不删除，可能会报错：ERROR: Cannot uninstall 'requests'
>
>安装RDS后，会安装requests和requests-2.25.1.dist-info，如下所示
>
>[root@k8s-node-71 site-packages]# ls |grep requests
>
>requests
>
>requests-2.25.1.dist-info

```sql

[mysql@k8s-node-70 site-packages]$ cd /usr/lib/python2.7/site-packages

[mysql@k8s-node-70 site-packages]$ ls |grep requests
requests
requests-2.6.0-py2.7.egg-info

[mysql@k8s-node-70 site-packages]$ sudo rm -rf requests  requests-2.6.0-py2.7.egg-info


```









### 安装成功

```sql

TASK [check : The installation is complete] ********************
ok: [controller-1] =>
  msg: |-
    管理界面:
      database地址: http://172.17.134.70:15672
      rabbitmq管理地址: http://172.17.134.70:15672
        用户名: admin 密码: 123456
      prometheus管理地址:http://172.17.134.70:9092
      alertmanager管理地址:http://172.17.134.70:9093

    log路径:
      /data/greatrds/log/
      /data/greatrds_auth/log/

    其他日志:
      docker logs << 容器名 >>

    集群安装源信息:
      web_repo地址:http://172.17.134.70:1510/gtms/
      web_repo挂载路径:172.17.134.70:/data/web_repo/data

    配置路径:
      配置文件路径: /data/greatrds/etc
      监控配置文件:172.17.134.70:/etc/greatrds/monitor/rules/
      告警配置文件:172.17.134.70:/etc/greatrds/monitor/scraps/

    平台数据库初始化命令:
      重新创建rds数据库表:
        "docker exec -it greatrds-api greatrds-db-manage upgrade"
      重新创建auth数据库表:
        "docker exec -it greatrds-auth greatrds-auth-db-manage upgrade "
      重新初始化模板请执行， 使用-r 参数初始化指定数据库模板. -h 查看帮助:
        docker exec -it greatrds-api greatrds-init sql
      重新初始化权限请执行， 使用-r 或-o 参数初始化指定数据库权限. -h 查看帮助:
        docker exec -it greatrds-auth greatrds-auth-init sql

    安装已完成!
      使用"docker ps -a"查看服务状态

      版本号: 5.1.9.0rc16

      请通过浏览器访问控制台  https://172.17.134.70:17712/control
      默认管理员用户名密码: "admin/123456" 请登录后立即修改

PLAY RECAP *********************************************
controller-1               : ok=197  changed=98   unreachable=0    failed=0    skipped=17   rescued=0    ignored=2
controller-2               : ok=147  changed=71   unreachable=0    failed=0    skipped=13   rescued=0    ignored=2



[root@node1 ~]# docker ps
CONTAINER ID   IMAGE                       COMMAND                  CREATED        STATUS        PORTS                                                                                                                                      NAMES
aa0ea7860c4b   greatrds_auth:5.1.8.0rc14   "/bin/sh -c 'gunicor…"   31 hours ago   Up 31 hours   0.0.0.0:9018->9018/tcp                                                                                                                     greatrds-auth
c4f59b857a91   greatrds:5.1.9.0rc16        "./docker-entrypoint…"   31 hours ago   Up 31 hours                                                                                                                                              greatrds-monitor
2722b0a7d23a   greatrds:5.1.9.0rc16        "./docker-entrypoint…"   31 hours ago   Up 31 hours                                                                                                                                              greatrds-conductor
8292b7afdfed   greatrds:5.1.9.0rc16        "./docker-entrypoint…"   31 hours ago   Up 31 hours   0.0.0.0:9010->9010/tcp                                                                                                                     greatrds-api
eb23d7122e8d   nginx:1.22.0-alpine         "/docker-entrypoint.…"   31 hours ago   Up 31 hours                                                                                                                                              nginx
8dca39ca09a4   rabbitmq:3.9.7-management   "docker-entrypoint.s…"   31 hours ago   Up 31 hours   0.0.0.0:4369->4369/tcp, 5671/tcp, 0.0.0.0:5672->5672/tcp, 15671/tcp, 0.0.0.0:15672->15672/tcp, 0.0.0.0:25672->25672/tcp, 15691-15692/tcp   rabbitmq
c96039f219d5   prom/alertmanager:latest    "/bin/alertmanager -…"   31 hours ago   Up 31 hours   0.0.0.0:9093->9093/tcp                                                                                                                     alertmanager
71a968a3931d   prom/prometheus:latest      "/bin/prometheus --c…"   31 hours ago   Up 31 hours   0.0.0.0:9092->9090/tcp                                                                                                                     prometheus
414aa974f65e   mysql:8.0.27                "docker-entrypoint.s…"   31 hours ago   Up 31 hours   33060/tcp, 0.0.0.0:13336->3306/tcp                                                                                                         mysql
8132531faab1   pypiserver/pypiserver       "/entrypoint.sh"         31 hours ago   Up 31 hours   0.0.0.0:18080->8080/tcp                                                                                                                    pypiserver
d986b0df75ed   nginx:1.22.0-alpine         "/docker-entrypoint.…"   31 hours ago   Up 31 hours   0.0.0.0:1510->80/tcp
```




### deploy.yaml
> os
>
> docker
>
> images
>
> repo
>
> pypiserver
>
> registry
>
> mysql
>
> prometheus
>
> rabbitmq
>
> nginx
>
> - [x] redis
>
> - [x] keepalived
>
> upload
>
> api
>
> conductor
>
> monitor
>
> auth
>
> exporter
>
> check

```yaml
---
- import_playbook: facts.yml

- name: Apply role os
  hosts: all
  gather_facts: false
  roles:
    - {
        role: os,
        tags: os,
      }

- name: Apply role docker
  hosts: all
  gather_facts: false
  roles:
    - {
        role: docker,
        tags: docker,
      }


- name: Apply role load images
  hosts: all
  gather_facts: false
  roles:
    - {
        role: images,
        tags: images,
      }

- name: Apply role repo
  hosts: reposerver
  gather_facts: false
  roles:
    - {
        role: repo,
        tags: repo,
      }

- name: Apply role pypiserver
  hosts: pypiserver
  gather_facts: false
  roles:
    - {
        role: pypiserver,
        tags: pypiserver,
      }

- name: Apply role registry
  hosts: registry
  gather_facts: false
  roles:
    - {
        role: registry,
        tags: registry,
      }

- name: Apply role mysql
  hosts: database
  gather_facts: false
  roles:
    - {
        role: mysql,
        tags: mysql,
      }

- name: Apply role prometheus
  hosts: monitor
  gather_facts: false
  roles:
    - {
        role: prometheus,
        tags: prometheus,
      }

- name: Apply role rabbitmq
  hosts: rabbitmq
  gather_facts: false
  roles:
    - {
        role: rabbitmq,
        tags: rabbitmq,
      }

- name: Apply role nginx
  hosts: nginx
  gather_facts: false
  roles:
    - {
        role: nginx,
        tags: nginx,
      }

#- name: Apply role redis
#  hosts: redis
#  gather_facts: false
#  roles:
#    - {
#        role: redis,
#        tags: redis,
#      }

#- name: Apply keepalived
#  hosts: keepalived
#  gather_facts: false
#  roles:
#    - {
#        role: keepalived,
#        tags: keepalived,
#        when: deploy_mode != "all-in-one"
#      }

- name: Upload wheel package
  hosts: pypiserver
  gather_facts: false
  roles:
    - {
        role: upload,
        tags: upload,
      }


- name: Apply role api
  hosts: api
  gather_facts: false
  roles:
    - {
        role: api,
        tags: api,
      }

- name: Apply role conductor
  hosts: conductor
  gather_facts: false
  roles:
    - {
        role: conductor,
        tags: conductor,
      }

- name: Apply role monitor
  hosts: monitor
  gather_facts: false
  roles:
    - {
        role: monitor,
        tags: monitor,
      }

- name: Apply role auth
  hosts: auth
  gather_facts: false
  roles:
    - {
        role: auth,
        tags: auth,
      }

- name: Apply role exporter
  hosts: exporter
  gather_facts: false
  roles:
    - {
        role: exporter,
        tags: exporter,
      }

- name: Checking the Installation Result And Cleaning the battlefield
  hosts: all
  gather_facts: false
  vars:
    pre_check: "no"
  roles:
    - {
        role: check,
        tags: check,
      }


```


### inventory.yml
```yaml
all:
  # 主机名称和 IP 地址是需要修改的
  hosts:
    controller-1:
      ansible_host: 172.17.134.70
    controller-2:
      ansible_host: 172.17.134.71

  # 部署前请先阅读使用手册，确保了解各变量的作用
  vars:
    ansible_user: mysql  # SSH 登录的用户名, 可以是拥有sudo权限的普通用户
    ansible_password: QWer12#$  # Ansible SSH登录密码
    ansible_port: 22 # Ansible 登录端口
    g_manager_interface: "eth0" # 机器网卡名称
    g_vip_address: "172.17.134.70"  # （可选） Web 服务虚拟 IP 地址。双控制节点部署需要配置此变量，单控制节点部署无需配置。配置此变量时请格外注意，错误的配置会导致安装的失败。
    base_dir: "/data"  # GreatRDS 安装的根目录，请确保该目录所在分区有充足的空间
    ansible_python_interpreter: /usr/bin/python

  children:
    # GreatRDS 控制节点（web 服务）
    # controller 组可以有1~2个主机
    # 生产环境需要2个主机，高可用部署，需配置上面的 g_vip_address 变量
    controller:
      hosts:
        controller-1:
        controller-2:

    # 以下配置几个组都只能有一个主机，一般选取 controller 中的一个主机
    reposerver:
      hosts:
        # 此节点必须为安装包所在节点
        controller-1:

    # 以下配置组默认即可
    monitor:
      hosts:
        controller-1:

    keepalived:
      children:
        controller:

    database:
      children:
        controller:

    rabbitmq:
      children:
        controller:

    nginx:
      children:
        controller:

    auth:
      children:
        controller:

    api:
      children:
        controller:

    conductor:
      children:
        controller:

    exporter:
      children:
        controller:

    pypiserver:
      children:
        reposerver:

    registry:
      children:
        reposerver:
```

### upgrade.yml
```yaml
---
- import_playbook: facts.yml

- name: Upload new_wheel package
  hosts: pypiserver
  gather_facts: false
  roles:
    - {
        role: upload,
        tags: upload,
      }

- name: Apply role api
  hosts: api
  gather_facts: false
  roles:
    - {
        role: api,
        tags: api,
      }

- name: Apply role conductor
  hosts: conductor
  gather_facts: false
  roles:
    - {
        role: conductor,
        tags: conductor,
      }

- name: Apply role monitor
  hosts: monitor
  gather_facts: false
  roles:
    - {
        role: monitor,
        tags: monitor,
      }

- name: Apply role auth
  hosts: auth
  gather_facts: false
  roles:
    - {
        role: auth,
        tags: auth,
      }

- name: Apply role nginx
  hosts: nginx
  gather_facts: false
  roles:
    - {
        role: nginx,
        tags: nginx,
      }

- name: Apply role exporter
  hosts: exporter
  gather_facts: false
  roles:
    - {
        role: exporter,
        tags: exporter,
      }

- name: Checking the Update Result And Cleaning the battlefield
  hosts: all
  gather_facts: false
  vars:
    pre_check: "no"
  roles:
    - {
        role: check,
        tags: check,
      }

```


### ansible.cfg

```ini
[defaults]
# customize these base on your local taste.
inventory = inventory.yml
remote_tmp = $HOME/.ansible/tmp
local_tmp = $HOME/.ansible/tmp
log_path = ansible.log
# strategy = free
forks = 6

transport = ssh
host_key_checking = False
deprecation_warnings = False
timeout = 100
stdout_callback = yaml

gathering = smart
fact_caching = jsonfile
fact_caching_connection = fact_files
DISPLAY_SKIPPED_HOSTS = False
retry_files_enabled = False

ask_pass = no
ask_sudo_pass = no
```

##














