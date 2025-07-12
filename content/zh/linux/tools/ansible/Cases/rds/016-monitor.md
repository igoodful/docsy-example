---
title: 16. monitor
description: monitor
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

#alertmanager config
alertmanager_resolve_timeout: "30s"
alertmanager_conf_file_host: "{{ node_config_directory }}/alertmanager.yml"
alertmanager_conf_file: "/etc/greatrds/monitor/alertmanager.yml"

#monitor config
monitor_log_file_path: "{{ rds_dir }}/log"


```



## 二、main.yaml

```yaml
---
- include_role:
    name: api
    tasks_from: version

- include_tasks: deploy-monitor.yml

```





## 三、deploy-monitor.yml

```yaml
---

# docker run -d --name greatrds-monitor -v /data/greatrds/etc/monitor/rules:/etc/greatrds/monitor/rules/ -v /data/greatrds/etc/monitor/scraps:/etc/greatrds/monitor/scraps/ -v /data/greatrds/etc/monitor/alertmanager.yml:/etc/greatrds/monitor/alertmanager.yml -v /data/greatrds/etc:/etc/greatrds/greatrds.conf -v /data/greatrds/log/:/var/log/rds/ greatrds:6.0.1.0rc4
# run_monitor
- name: start monitor container
  docker_container:
    name: "{{ project }}-{{ component }}"
    image: "greatrds:{{ greatrds_version }}"
    volumes:
      - "{{ rule_path_host }}:{{ rule_path }}"
      - "{{ scrap_path_host }}:{{ scrap_path }}"
      - "{{ alertmanager_conf_file_host }}:{{ alertmanager_conf_file }}"
      - "{{ oslo_config_path_host }}:{{ oslo_config_path_mount }}"
      - "{{ monitor_log_file_path }}:/var/log/rds/"
    restart_policy: unless-stopped
    recreate: yes
    command: run_monitor
    timeout: "{{ docker_container_timeout }}"
  become: yes


```




## 四、config.yaml

```yaml
---

# mkdir -p /data/greatrds/log/
- name: ensure monitor log path exists
  file:
    path: "{{ monitor_log_file_path }}"
    state: directory
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0644
  become: True

```





## 五、upgrade.yaml

```yaml
---

- include_role:
    name: api
    tasks_from: upload

- include_role:
    name: api
    tasks_from: build

- include_tasks: deploy-monitor.yml


```


## 六、prometheus.yml.j2

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

rule_files:
- {{ rule_path }}*

scrape_configs:
  - job_name: 'file_ds'
    file_sd_configs:
      - files:
        - {{ scrap_path }}*.json
        refresh_interval: 5s
    honor_labels: true

```



## 七、alertmanager.yml.j2

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





