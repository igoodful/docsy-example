---
title: 08. prometheus
description: prometheus
date: 2023-10-12
weight: 60000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



{{< alert color="secondary" >}}

只在`controller-1`执行：

1. `mkdir -p /data/greatrds/etc/monitor`
2. `scp prometheus.yml.j2 /data/greatrds/etc/monitor/prometheus.yml`
3. `mkdir -p /etc/greatrds/monitor/rules/`
4. `mkdir -p /etc/greatrds/monitor/scraps/`
5. `mkdir -p /data/greatrds/data/monitor/prometheus_data`
6. `scp alertmanager.yml.j2 /data/greatrds/etc/monitor/alertmanager.yml`
7. `scp prom_web.yml.j2 /data/greatrds/etc/monitor/prom_web.yml`
8. `scp alert_web.yml.j2 /data/greatrds/etc/monitor/alert_web.yml`
9. `docker run -d --name prometheus -p 9092:9090 -v /data/greatrds/etc/monitor/prometheus.yml:/etc/prometheus/prometheus.yml -v /data/greatrds/etc/monitor/prom_web.yml:/etc/prometheus/prom_web.yml -v /data/greatrds/data/monitor/prometheus_data:/prometheus -v /data/greatrds/etc/monitor/rules/:/etc/greatrds/monitor/rules/ -v /data/greatrds/etc/monitor/scraps:/etc/greatrds/monitor/scraps/   prom/prometheus`
   1.  `docker run -d --name alertmanager -p 9093:9093 -v /data/greatrds/etc/monitor/alertmanager.yml:/etc/alertmanager/alertmanager.yml -v /data/greatrds/etc/monitor/alert_web.yml:/etc/alertmanager/alert_web.yml prom/alertmanager`
   2.  `docker run -d --name alertmanager -p 9093:9093 -p 9094:9094 -v /data/greatrds/etc/monitor/alertmanager.yml:/etc/alertmanager/alertmanager.yml -v /data/greatrds/etc/monitor/alert_web.yml:/etc/alertmanager/alert_web.yml prom/alertmanager`


{{< /alert >}}



## 一、defaults/main.yaml

```yaml
---

logging_debug: "false"
project: "greatrds"
component: "monitor"
# monitor的配置路径
rds_dir: "{{ base_dir }}/{{ project }}"
node_config_directory: "{{ rds_dir }}/etc/{{ component }}"
work_path: "{{ rds_dir }}/data/{{ component }}"

# images
prometheus_image: "prom/prometheus:latest"
alertmanager_image: "prom/alertmanager:latest"

#prometheus config
prometheus_scrape_interval: "10s"
prometheus_evaluation_interval: "10s"
prometheus_data_path: "{{ work_path }}/prometheus_data"
rule_path_host: "{{ node_config_directory }}/rules/"
rule_path: "/etc/greatrds/monitor/rules/"
scrap_path_host: "{{ node_config_directory }}/scraps/"
scrap_path: "/etc/greatrds/monitor/scraps/"
prometheus_conf_file: "{{ node_config_directory }}/prometheus.yml"
prometheus_web_conf_file: "{{ node_config_directory }}/prom_web.yml"

#alertmanager config
alertmanager_resolve_timeout: "30s"
alertmanager_conf_file_host: "{{ node_config_directory }}/alertmanager.yml"
alertmanager_web_conf_file_host: "{{ node_config_directory }}/alert_web.yml"
alertmanager_conf_file: "/etc/greatrds/monitor/alertmanager.yml"


```



## 二、config.yml

```yaml
---

# mkdir -p /data/greatrds/etc/monitor
- name: ensure node_config_directory directory exists
  file:
    path: "{{ node_config_directory }}"
    state: directory
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: True

# scp prometheus.yml.j2 /data/greatrds/etc/monitor/prometheus.yml
- name: config prometheus
  template:
    src: "prometheus.yml.j2"
    dest: "{{ prometheus_conf_file }}"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0644
  become: True

# mkdir -p /etc/greatrds/monitor/rules/
- name: create prometheus rule dir
  file:
    dest: "{{ rule_path }}"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
    state: directory
  become: True

# mkdir -p /etc/greatrds/monitor/scraps/
- name: create prometheus scrap dir
  file:
    dest: "{{ scrap_path }}"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
    state: directory
  become: True

# mkdir -p /data/greatrds/data/monitor/prometheus_data
- name: create prometheus_data_path dir
  file:
    path: "{{ prometheus_data_path }}"
    state: directory
    owner: "65534"
    group: "65534"
    mode: 0755
  become: True

# scp alertmanager.yml.j2 /data/greatrds/etc/monitor/alertmanager.yml
- name: config alertmanager
  template:
    src: "alertmanager.yml.j2"
    dest: "{{ alertmanager_conf_file_host }}"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0644
  become: True

# scp prom_web.yml.j2 /data/greatrds/etc/monitor/prom_web.yml
- name: config prometheus web config
  template:
    src: "prom_web.yml.j2"
    dest: "{{ prometheus_web_conf_file }}"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0644
  become: True

# scp alert_web.yml.j2 /data/greatrds/etc/monitor/alert_web.yml
- name: config alertmanager web config
  template:
    src: "alert_web.yml.j2"
    dest: "{{ alertmanager_web_conf_file_host }}"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0644
  become: True


```






## 三、deploy-prometheus.yml

```yaml
---

# docker run -d --name prometheus -p 9092:9090 -v /data/greatrds/etc/monitor/prometheus.yml:/etc/prometheus/prometheus.yml -v /data/greatrds/etc/monitor/prom_web.yml:/etc/prometheus/prom_web.yml -v /data/greatrds/data/monitor/prometheus_data:/prometheus -v /data/greatrds/etc/monitor/rules/:/etc/greatrds/monitor/rules/ -v /data/greatrds/etc/monitor/scraps:/etc/greatrds/monitor/scraps/   prom/prometheus
- name: start prometheus container
  docker_container:
    name: "prometheus"
    image: "{{ prometheus_image }}"
    ports:
      - "{{ prometheus_port }}:9090"
    volumes:
      - "{{ prometheus_conf_file }}:/etc/prometheus/prometheus.yml"
      - "{{ prometheus_web_conf_file }}:/etc/prometheus/prom_web.yml"
      - "{{ prometheus_data_path }}:/prometheus"
      - "{{ rule_path_host }}:{{ rule_path }}"
      - "{{ scrap_path_host }}:{{ scrap_path }}"
    restart_policy: unless-stopped
    recreate: yes
    command: --config.file=/etc/prometheus/prometheus.yml --storage.tsdb.path=/prometheus --web.console.libraries=/usr/share/prometheus/console_libraries --web.console.templates=/usr/share/prometheus/consoles --web.enable-lifecycle --web.config.file=/etc/prometheus/prom_web.yml
    timeout: "{{ docker_container_timeout }}"
  become: yes

# 单节点时，controller-1安装
# docker run -d --name alertmanager -p 9093:9093 -v /data/greatrds/etc/monitor/alertmanager.yml:/etc/alertmanager/alertmanager.yml -v /data/greatrds/etc/monitor/alert_web.yml:/etc/alertmanager/alert_web.yml prom/alertmanager
- name: start alertmanager container
  docker_container:
    name: "alertmanager"
    image: "{{ alertmanager_image }}"
    ports:
      - "{{ alertmanager_port }}:9093"
    volumes:
      - "{{ alertmanager_conf_file_host }}:/etc/alertmanager/alertmanager.yml"
      - "{{ alertmanager_web_conf_file_host }}:/etc/alertmanager/alert_web.yml"
    restart_policy: unless-stopped
    recreate: yes
    command: --config.file=/etc/alertmanager/alertmanager.yml --web.config.file=/etc/alertmanager/alert_web.yml
    timeout: "{{ docker_container_timeout }}"
  become: yes
  when: monitor_deploy_mode == '1'

# 两个节点时，获得另一节点ip
- name: set_fact cluster_peer_host
  set_fact:
    cluster_peer_host: "{{ hostvars[groups['monitor'][1]].ansible_host if hostvars[groups['monitor'][0]].ansible_host == ansible_host else hostvars[groups['monitor'][0]].ansible_host }}"
  when: monitor_deploy_mode != '1'

# 两个节点时，
# docker run -d --name alertmanager -p 9093:9093 -p 9094:9094 -v /data/greatrds/etc/monitor/alertmanager.yml:/etc/alertmanager/alertmanager.yml -v /data/greatrds/etc/monitor/alert_web.yml:/etc/alertmanager/alert_web.yml prom/alertmanager
- name: start alertmanager container
  docker_container:
    name: "alertmanager"
    image: "{{ alertmanager_image }}"
    ports:
      - "{{ alertmanager_port }}:9093"
      - "{{ alertmanager_cluster_port }}:9094"
    volumes:
      - "{{ alertmanager_conf_file_host }}:/etc/alertmanager/alertmanager.yml"
      - "{{ alertmanager_web_conf_file_host }}:/etc/alertmanager/alert_web.yml"
    restart_policy: unless-stopped
    recreate: yes
    command: "--config.file=/etc/alertmanager/alertmanager.yml --storage.path=/alertmanager --cluster.peer={{ cluster_peer_host }}:{{ alertmanager_cluster_port }} --cluster.advertise-address={{ ansible_host }}:{{ alertmanager_cluster_port }} --web.config.file=/etc/alertmanager/alert_web.yml"
    timeout: "{{ docker_container_timeout }}"
  become: yes
  when: monitor_deploy_mode != '1'



```



## 四、prometheus.yml.j2

```yaml
global:
  scrape_interval:     {{ prometheus_scrape_interval }}
  evaluation_interval: {{ prometheus_evaluation_interval }}
alerting:
  alertmanagers:
  - static_configs:
    - targets:
{% for host in groups['monitor'] %}
      - '{{ hostvars[host].ansible_host }}:{{ alertmanager_port }}'
{% endfor %}
    basic_auth:
      username: 'admin'
      password: '123456.'

rule_files:
- {{ rule_path }}*

scrape_configs:
  - job_name: 'file_ds'
    file_sd_configs:
      - files:
        - {{ scrap_path }}*.json
        refresh_interval: 5s
    honor_labels: true
  - job_name: 'rds'
    static_configs:
{% for node in groups['exporter'] %}
      - targets: ['{{ hostvars[node].ansible_host }}:{{ exporter_port }}']
        labels:
          __metrics_path__: '/rds_metrics'
{% endfor %}
    honor_labels: true

```



## 五、prom_web.yml.j2

```yaml
basic_auth_users:
    admin: $4a$12$xjzp6ODrlObwssSxS9kC.eqS32485so4AITFoRdYdF.G0U4xfJSmm

```



## 六、alertmanager.yml.j2

```yaml
global:
  resolve_timeout: {{ alertmanager_resolve_timeout }}

route:
  group_by: ['alertname', 'instance']
  group_wait: 10s
  group_interval: 10s
  repeat_interval: 7200s
  receiver: 'api.web.hook'
receivers:
  - name: 'api.web.hook'
    webhook_configs:
      - url: 'http://{{ api_address }}:{{ api_port }}/v1/rds/monitor/alert/new'
inhibit_rules:
  - source_match:
      severity: 'critical'
    target_match:
      severity: 'major'
    equal: ['metric_name', 'instance', 'member_uuid']
  - source_match:
      severity: 'major'
    target_match:
      severity: 'minor'
    equal: ['metric_name', 'instance', 'member_uuid']
  - source_match:
      severity: 'minor'
    target_match:
      severity: 'info'
    equal: ['metric_name', 'instance', 'member_uuid']

```

- `2024-08-11T22:50:46 [root@node3 /data/tmpdir/rds_deploy/rds-ansible/files/release] $ cat  /data/greatrds/etc/monitor/alertmanager.yml `
```yaml
global:
  resolve_timeout: 30s

route:
  group_by: ['alertname', 'instance']
  group_wait: 10s
  group_interval: 10s
  repeat_interval: 7200s
  receiver: 'api.web.hook'
receivers:
  - name: 'api.web.hook'
    webhook_configs:
      - url: 'http://172.17.134.77:9010/v1/rds/monitor/alert/new'
inhibit_rules:
  - source_match:
      severity: 'critical'
    target_match:
      severity: 'major'
    equal: ['metric_name', 'instance', 'member_uuid']
  - source_match:
      severity: 'major'
    target_match:
      severity: 'minor'
    equal: ['metric_name', 'instance', 'member_uuid']
  - source_match:
      severity: 'minor'
    target_match:
      severity: 'info'
    equal: ['metric_name', 'instance', 'member_uuid']

```



## 七、alert_web.yml.j2


```yaml
basic_auth_users:
    admin: $4a$12$xjzp6ODrlObwssSxS9kC.eqS32485so4AITFoRdYdF.G0U4xfJSmm

```













