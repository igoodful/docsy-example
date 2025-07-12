---
title: 19. check
description: check
date: 2023-10-12
weight: 60000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



{{< alert color="secondary" >}}


{{< /alert >}}


## 一、defaults/main.yaml

```yaml
---
# defaults file for repo

request_prefix: "{{ 'http://' if enable_https == 'no' else 'https://' }}"
rds_nginx_port: "{{ nginx_port if enable_https == 'no' else nginx_ssl_port }}"
rds_dashboard_port: "{{ nginx_port if enable_https == 'no' else nginx_ssl_port }}"
```





## 一、main.yml

```yaml
---
- include_tasks: pre_check.yml
  when: pre_check == "yes"

- include_role:
    name: api
    tasks_from: version
  when: pre_check == "no"

#- include_tasks: clear.yml
#  when: pre_check == "no"

- include_tasks: check.yml
  when: pre_check == "no"

- include_tasks: successful.yml
  when: pre_check == "no"



```

## 二、pre_check.yml

```yaml
---
- name: Check the running environment
  fail:
    msg: "CPU数量不能小于2, 当前{{ ansible_processor_vcpus }}"
  when: ansible_processor_vcpus < 4

- name: Check the running environment
  fail:
    msg: "内存不能小于4G, 当前{{ ansible_memtotal_mb }}MB"
  when: ansible_memtotal_mb < 3600

- name: Change state of selinux
  selinux:
    policy: targeted
    state: disabled
  become: true
  register: selinux_change

- name: Closed SELinux, Please restart system
  fail:
    msg: "SELinux 未处于完全关闭的状态, 请重启节点后再次安装"
  when: selinux_change.reboot_required == true

- name: Check if Docker is running
  command: systemctl status docker
  ignore_errors: yes
  changed_when: false
  register: service_docker_status

- name: Check docker install status
  fail:
    msg: |
      检测到目标主机已安装docker
      安装时可指定参数 ”ansible-playbook deploy.yml -v --skip-tags docker “ 跳过docker安装
        注意：
          1. 盲目跳过docker安装可能遇到未知问题， 请查看用户手册确认docker版本是否满足要求(使用"docker version" 查看docker版本)
          2. 也可直接安装将覆盖原有docker， 这会导致原镜像容器丢失， 请谨慎操作
  when: not service_docker_status.failed
  ignore_errors: yes

- name: Check firewalld status
  debug:
    msg: |
      在部署前，要保证控制节点的防火墙处于关闭状态
  ignore_errors: yes
  run_once: yes

- name: Check docker interface name
  debug:
    msg: |
      注意:
        1. 采取HA方式部署时，要保证控制节点IP所在的网卡名称保持一致
        2. 网卡名所在的配置在文件inventory.yml中，配置项为g_manager_interface
  run_once: yes

  when: deploy_mode != "all-in-one"

- name: Check nginx enable https
  debug:
    msg: |
        1、配置enable_https:yes, nginx启用https访问方式
        2、配置enable_https:no, nginx启用http访问方式
        3、是否开启https，在文件inventory.yml中配置enable_https
  run_once: yes

- name: Check ports that must be opened
  debug:
    msg: |
        RDS必须要开放的端口如下:
        reposerver: {{web_repo_port|int}}
        alertmanager: {{alertmanager_port}}
        database：{{mysql_port}}
        rabbitmq：{{rabbitmq_port}}
        nginx：{{nginx_port if enable_https == 'no' else nginx_ssl_port}}
        auth：{{auth_port}}
        api：{{api_port}}
        pypiserver：{{pypiserver_port}}
        registry：{{docker_registry_port}}
        exporter：{{exporter_port}}
  run_once: yes


- name: Check ports that must be opened
  debug:
    msg: |
        RDS要开放的管理端口:
        rabbitmq：{{rabbitmq_web_port}}
        prometheus：{{prometheus_port}}

  run_once: yes

```



## 三、check.yml

```yaml
---

- name: Check API Service status
  uri:
    url: "http://{{api_address}}:{{api_port}}/v1/rds/docs"
    follow_redirects: none
    method: GET
    timeout: 5
    status_code: 401
  register: _result
  until: _result.status == 401
  retries: 3
  delay: 5

- name: Check AUTH API Service status
  uri:
    url: "http://{{auth_address}}:{{auth_port}}/v1/auth/docs"
    follow_redirects: none
    method: GET
    timeout: 5
    status_code: 401
  register: _result
  until: _result.status == 401
  retries: 3
  delay: 5


- name: Check Dashboard Service status
  uri:
    url: "{{request_prefix}}{{api_address}}:{{rds_dashboard_port}}/control/#/login"
    follow_redirects: none
    validate_certs: no
    method: GET
    timeout: 5
  register: _result
  until: _result.status == 200
  retries: 3
  delay: 5


- name: Check Conductor status
  vars:
    conductor_container_name: greatrds-conductor
  command: "docker ps"
  register: _result
  retries: 3
  delay: 5
  changed_when: false
  until: conductor_container_name in _result.stdout
  become: yes
  when:
    - inventory_hostname in groups['conductor']

- name: Check Rabbitmq Service status
  uri:
    url: "http://{{api_address}}:{{rabbitmq_web_port}}"
    follow_redirects: none
    method: GET
    timeout: 5
  register: _result
  until: _result.status == 200
  retries: 3
  delay: 5
  when:
    - inventory_hostname in groups['rabbitmq']

# 检查数据库是否正常：select 1
- name: Check Database Service status
  command: mysql -e "select 1"
  args:
    chdir: "~"
  retries: 3
  delay: 5
  changed_when: false
  register: _result
  until: _result.rc == 0
  when:
    - inventory_hostname in groups['database']

# 检查repo服务是否正常
- name: Check Repo Service status
  uri:
    url: "http://{{repo_host}}:{{web_repo_port}}/gtms/"
    follow_redirects: none
    method: GET
    timeout: 5
  register: _result
  until: _result.status == 200
  retries: 3
  delay: 5
  when:
    - inventory_hostname in groups['reposerver']

- name: Check Monitor Service status
  vars:
    monitor_container_name: greatrds-monitor
  command: "docker ps"
  register: _result
  retries: 3
  delay: 5
  changed_when: false
  until: monitor_container_name in _result.stdout
  become: yes
  when:
    - inventory_hostname in groups['monitor']

- name: Check Prometheus Service status
  uri:
    url: "{{prometheus_url}}/graph"
    follow_redirects: none
    method: GET
    timeout: 5
    status_code: 401
  register: _result
  until: _result.status == 401
  retries: 3
  delay: 5
  when:
    - inventory_hostname in groups['monitor']

- name: Check AlertManager Service status
  uri:
    url: "{{alertmanager_url}}"
    follow_redirects: none
    method: GET
    timeout: 5
    status_code: 401
  register: _result
  until: _result.status == 401
  retries: 3
  delay: 5
  when:
    - inventory_hostname in groups['monitor']

- name: Check Exporter Service status
  uri:
    url: "http://{{ansible_host}}:{{exporter_port}}/rds_metrics"
    follow_redirects: none
    method: GET
    timeout: 5
  register: _result
  until: _result.status == 200
  retries: 3
  delay: 5
  when:
    - inventory_hostname in groups['exporter']

```



## 四、successful.yml

```yaml
---

- name: The installation is complete
  vars:
    exporter_nginx_port: "{{ nginx_port if enable_https == 'no' else nginx_ssl_port }}"
    request_prefix: "{{ 'http://' if enable_https == 'no' else 'https://' }}"
  debug:
    msg: |
      管理界面:
        database地址: http://{{api_address}}:{{rabbitmq_web_port}}
        rabbitmq管理地址: http://{{api_address}}:{{rabbitmq_web_port}}
          用户名: {{om_rpc_user}} 密码: {{om_rpc_password}}
        prometheus管理地址:{{prometheus_url}}
        alertmanager管理地址:{{alertmanager_url}}

      log路径:
        {{base_dir}}/greatrds/log/
        {{base_dir}}/greatrds_auth/log/

      其他日志:
        docker logs << 容器名 >>

      集群安装源信息:
        web_repo地址:http://{{repo_host}}:{{web_repo_port}}/gtms/
        web_repo挂载路径:{{repo_host}}:{{web_repo_host_path}}

      配置路径:
        配置文件路径: {{etc_dir}}
        监控配置文件:{{monitor_host}}:{{rule_path}}
        告警配置文件:{{monitor_host}}:{{scrap_path}}

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

        版本号: {{greatrds_version}}

        请通过浏览器访问控制台  {{ request_prefix }}{{api_address}}:{{ exporter_nginx_port }}/control
        默认管理员用户名密码: "admin/123456" 请登录后立即修改
  run_once: yes

```


## 五、

```yaml


```


## 一、

```yaml


```












