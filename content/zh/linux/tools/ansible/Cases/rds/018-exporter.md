---
title: 18. exporter
description: exporter
date: 2023-10-12
weight: 60000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



{{< alert color="secondary" >}}

1. `mkdir -p /data/exporter/greatrds-exporter-dir`
2. `tar -xzf rds-ansible/files/repo/x64/static/stable/rds/greatrds-exporter.tar.gz -C /data/exporter/greatrds-exporter-dir/`
3. `ln -s /data/exporter/greatrds-exporter-dir/greatrds-exporter/greatrds-exporter /data/exporter/greatrds-exporter`
4. `scrap_rabbitmq=1/0`
5. `scrap_rds_database=1/0`
6. `nginx_schema=http/https`
7. `exporter_nginx_port=17711/17712`
8. `scrap_monitor=yes/no`
9. `scp rds_config.ini.j2 /data/exporter/rds_config.ini`
10. `scp rds-greatrds-exporter.service.j2 /usr/lib/systemd/system/rds-greatrds-exporter.service`
11. `systemctl daemon-reload`
12. `systemctl enable rds-greatrds-exporter`
13. `systemctl restart rds-greatrds-exporter`


{{< /alert >}}


## 一、defaults/main.yaml

```yaml
exporter_install_path: "{{ base_dir }}/exporter"
exporter_conf_file: "{{ exporter_install_path }}/rds_config.ini"
exporter_service_file: "/usr/lib/systemd/system/rds-greatrds-exporter.service"
enable_https: "yes"

```

## 二、deploy.yaml

```yaml
---

# mkdir -p /data/exporter/greatrds-exporter-dir
- name: create exporter_install_path
  file:
    path: "{{ exporter_install_path }}/greatrds-exporter-dir"
    state: directory
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: true

# tar -xzf rds-ansible/files/repo/x64/static/stable/rds/greatrds-exporter.tar.gz -C /data/exporter/greatrds-exporter-dir/
- name: copy greatrds-exporter
  unarchive:
    src: "{{ role_path }}/../../files/repo/{{ arch_mapping[ansible_architecture] }}/static/stable/rds/greatrds-exporter.tar.gz"
    dest: "{{ exporter_install_path }}/greatrds-exporter-dir"
  become: yes

# ln -s /data/exporter/greatrds-exporter-dir/greatrds-exporter/greatrds-exporter /data/exporter/greatrds-exporter
- name: link greatrds-exporter
  file:
    src: "{{ exporter_install_path }}/greatrds-exporter-dir/greatrds-exporter/greatrds-exporter"
    path: "{{ exporter_install_path }}/greatrds-exporter"
    state: link
    force: yes
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: yes

# scrap_rabbitmq=1/0
- name: set_fact scrap_rabbitmq exporter_conf_file needed
  set_fact:
    scrap_rabbitmq: "{{ '1' if hostvars[groups['exporter'][0]].ansible_host == ansible_host else '0' }}"

# scrap_rds_database=1/0
- name: set_fact scrap_rds_database exporter_conf_file needed
  set_fact:
    scrap_rds_database: "{{ '1' if hostvars[groups['exporter'][0]].ansible_host == ansible_host else '0' }}"

# nginx_schema=http/https
- name: set_fact nginx_schema exporter_conf_file needed
  set_fact:
    nginx_schema: "{{ 'http' if enable_https == 'no' else 'https' }}"

# exporter_nginx_port=17711/17712
- name: set_fact exporter_nginx_port exporter_conf_file needed
  set_fact:
    exporter_nginx_port: "{{ nginx_port if enable_https == 'no' else nginx_ssl_port }}"

# scrap_monitor=yes/no
- name: set_fact scrap_monitor exporter_conf_file needed
  set_fact:
    scrap_monitor: "{{ 'yes' if hostvars[groups['exporter'][0]].ansible_host == ansible_host else 'no' }}"

# scp rds_config.ini.j2 /data/exporter/rds_config.ini
- name: generate exporter conf file
  template:
    src: rds_config.ini.j2
    dest: "{{ exporter_conf_file }}"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0644

# scp rds-greatrds-exporter.service.j2 /usr/lib/systemd/system/rds-greatrds-exporter.service
- name: generate exporter service file
  template:
    src: rds-greatrds-exporter.service.j2
    dest: "{{ exporter_service_file }}"
    mode: 0644
  become: yes

# systemctl daemon-reload
# systemctl enable rds-greatrds-exporter
# systemctl restart rds-greatrds-exporter
- name: start greatrds-exporter service
  systemd:
    state: restarted
    enabled: yes
    daemon_reload: yes
    name: rds-greatrds-exporter
  become: yes

```






## 三、rds_config.ini.j2


```ini
[DEFAULT]
address={{ ansible_host }}

[mysql]
port={{ api_database_port }}
username={{ api_database_user }}
password={{ api_database_password }}

[rabbitmq]
# 配置vip
hostname={{ api_address }}
manage_port={{ om_rpc_admin_port }}
username={{ om_rpc_user }}
password={{ om_rpc_password }}
scrap={{ scrap_rabbitmq }}

[service]
api_port={{ api_port }}
auth_port={{ auth_port }}
nginx_port={{ exporter_nginx_port }}
nginx_schema={{ nginx_schema }}
rabbitmq_port={{ om_rpc_port }}
# 是否采集rds数据库中服务
scrap_rds_database={{ scrap_rds_database }}
service_down_time=180

monitor_node_ip={{ ansible_host }}
prometheus_port={{ prometheus_port }}
alertmanager_port={{ alertmanager_port }}

{% if scrap_monitor %}
repo_addr={{ repo_host }}
repo_port={{ web_repo_port }}

pypiserver_addr={{ repo_host }}
pypiserver_port={{ pypiserver_port }}

registry_addr={{ repo_host }}
registry_port={{ docker_registry_port }}
{% endif %}


```


## 四、rds-greatrds-exporter.service.j2


```ini
[Unit]
Description=RDS greatrds-exporter

[Service]
User={{ rds_user }}
Group={{ rds_group }}
Type=simple
ExecStart={{ exporter_install_path }}/greatrds-exporter --bind 0.0.0.0 --port {{ exporter_port }} -r {{ exporter_conf_file }}
Restart=on-failure
TimeoutStopSec=15

[Install]
WantedBy=multi-user.target


```

查看`rds-greatrds-exporter.service`:

`2024-08-11T21:19:01 [root@node3 /data/tmpdir/rds_deploy/rds-ansible/files/release] $ systemctl cat rds-greatrds-exporter.service`
```ini
# /usr/lib/systemd/system/rds-greatrds-exporter.service
[Unit]
Description=RDS greatrds-exporter

[Service]
User=mysql
Group=mysql
Type=simple
ExecStart=/data/exporter/greatrds-exporter --bind 0.0.0.0 --port 9100 -r /data/exporter/rds_config.ini
Restart=on-failure
TimeoutStopSec=15

[Install]
WantedBy=multi-user.target


```









