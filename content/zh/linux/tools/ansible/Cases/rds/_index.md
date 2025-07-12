---
title: rds
description:  rds


weight: 100000
---


## all.yaml
```yaml
---
rds_user: "{{ ansible_user }}"  # rds 管理用户
rds_group: "{{ ansible_user }}"  # rds 管理用户属组
ansible_become_password: "{{ ansible_password }}" # （可选）sudo 密码，和登录用户的密码（ansible_password）相同 。

#### 单节点 or 双节点 ####
controller_count: "{{ groups['controller'] | length }}"
deploy_mode: "{{ 'all-in-one' if controller_count == '1' else 'dual' }}"
monitor_deploy_mode: "{{ groups['monitor'] | length }}"
########################

### passwords ###
database_password: 123456
rabbitmq_password: 123456
redis_password: 123456
#################

######################################
# RDS container ports
######################################
enable_https: "yes"
docker_registry_port: "5000"
web_repo_port: "1510"
pypiserver_port: "18080"
nginx_port: "17711" # GreatRDS web plain HTTP port
nginx_ssl_port: "17712" # GreatRDS web frontend HTTPS port
rabbitmq_port: "5672" # RabbitMQ AMQP port
rabbitmq_web_port: "15672" # RabbitMQ web management port
mysql_port: "13336"
redis_port: "6389"
api_port: "9010"  # greatrds-api port
auth_port: "9018" # greatrds-auth port
prometheus_port: "9092"
alertmanager_port: "9093"
alertmanager_cluster_port: "9094"
######################################

change_hosts: no  # 是否允许 Ansible 更改系统的 hostname, /etc/hosts, /etc/resolv.conf 配置
lvm_vg: rds_container_vg # LVM 卷组名称

# 是否开启debug
logging_debug: "false"
# 是否开启syslog
syslog_flag: "False"
# syslog 服务器地址
syslog_addr: "172.16.70.61"

#########################
# RDS 基本配置
#########################
# keepalived vip netmask
g_vip_netmask: "24"

# 源代码路径
app_dir: "{{ base_dir }}/app"
# 临时路径
tmp_dir: "{{ base_dir }}/tmp"

##################################################
# 源配置源
##################################################
# reposerver 主机地址
repo_host: "{{ hostvars[groups['reposerver'][0]].ansible_host }}"
pypiserver_host: "{{ hostvars[groups['pypiserver'][0]].ansible_host }}"

repo_base_dir: "{% if repo_dir is defined %}{{repo_dir}}{% else %}{{ base_dir }}{% endif %}"
web_repo_host_path: "{{ repo_base_dir }}/web_repo/data"
web_repo_static: "{{ base_dir }}/static"
web_repo_static_mount: "/opt/static"
# pip 源
g_pip_server: "http://{{ repo_host }}:{{ pypiserver_port }}/simple"
g_trusted_host: "{{ repo_host }}"

##################################################

###################
# RabbitMQ
###################
# oslo.messaging rpc transport valid options are [ rabbit, amqp ]
om_rpc_transport: "rabbit"
om_rpc_user: "admin"
om_rpc_password: "{{ rabbitmq_password }}"
om_rpc_port: "{{ rabbitmq_port }}"
om_rpc_admin_port: 15672
om_rpc_group: "rabbitmq"
om_rpc_vhost: "/"

rpc_transport_url: "{{ om_rpc_transport }}://{% for host in groups[om_rpc_group] %}{{ om_rpc_user }}:{{ om_rpc_password }}@{{ hostvars[host].ansible_host }}:{{ om_rpc_port }}{% if not loop.last %},{% endif %}{% endfor %}/{{ om_rpc_vhost }}"
####################

###################
# MySQL on controllers
###################
mysql_user: "root" # Warning: DO NOT MODIFY THIS
###################

###################
# database
###################
api_database_user: "{{ mysql_user }}"
api_database_password: "{{ database_password }}"
api_database_port: "{{ mysql_port }}"
api_database_name: "greatrds"
auth_database_name: "auth"
###################

###################
# monitor config
###################
monitor_host: "{{ hostvars[groups['monitor'][0]].ansible_host if monitor_deploy_mode == '1' else g_vip_address }}"
prometheus_url: "http://{{ monitor_host }}:{{ prometheus_port }}"
alertmanager_url: "http://{{ monitor_host }}:{{ alertmanager_port }}"
rule_path: "/etc/greatrds/monitor/rules/"
scrap_path: "/etc/greatrds/monitor/scraps/"
alertmanager_conf_file: "/etc/greatrds/etc/monitor/alertmanager.yml"
###################

####################
# Docker registry server
####################
docker_registry_host: "{{ hostvars[groups['reposerver'][0]].ansible_host }}"
docker_registry_insecure: "{{ 'yes' if docker_registry else 'no' }}"
docker_runtime_directory: ""
docker_registry_domain: "docker.greatrds.com"
docker_registry: "{{ docker_registry_domain }}:{{ docker_registry_port }}"  # we use /etc/hosts to map docker_registry_domain to docker_registry_host

# Retention settings for Docker logs
docker_dir: "{{ base_dir }}/docker"
docker_log_max_file: "5"
docker_log_max_size: "50m"
# Ansible Docker API timeout
docker_container_timeout: 300

####################

# config shared by api, conductor, monitor
etc_dir: "{{ base_dir }}/greatrds/etc"
oslo_config_path_host: "{{ etc_dir }}/greatrds.conf"
oslo_config_path_mount: "/etc/greatrds/greatrds.conf"

############################################################
#      Not often need to modify the following items        #
############################################################

g_dns_servers:
  - 114.114.114.114
  - 8.8.8.8

# 子服务运行时workers, 一般不需要修改
service_workers: 1

# addresses based on number of controllers
api_database_address: "{{ hostvars[groups['controller'][0]].ansible_host if deploy_mode == 'all-in-one' else g_vip_address }}"
auth_database_address: "{{ hostvars[groups['controller'][0]].ansible_host if deploy_mode == 'all-in-one' else g_vip_address }}"
api_address: "{{ hostvars[groups['controller'][0]].ansible_host if deploy_mode == 'all-in-one' else g_vip_address }}"
auth_address: "{{ hostvars[groups['controller'][0]].ansible_host if deploy_mode == 'all-in-one' else g_vip_address }}"

# arch name mapping from ansible_architecture to filename
arch_mapping:
  x86_64: x64
  aarch64: arm64
arch_modifier: "{{ arch_mapping[ansible_architecture] }}"

exporter_port: 9100

# rds docker retry time
greatrds_api_retry: 12
greatrds_auth_retry: 12
mysql_start_retry: 6
rabbitmq_start_retry: 6

longzhou_http: "False"
longzhou_url: ""
longzhou_ak: ""
longzhou_sk: ""
longzhou_api_key: ""
longzhou_app_key: ""


```

