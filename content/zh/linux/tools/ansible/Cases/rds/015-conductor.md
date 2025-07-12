---
title: 15. conductor
description: conductor
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
component: "conductor"
rds_dir: "{{ base_dir }}/{{ project }}"
# rds auth 的配置文件路径
node_config_directory: "{{ rds_dir }}/etc/{{ component }}"
rds_log_dir: "{{ rds_dir }}/log"
work_path: "{{ base_dir }}/{{ project }}"
ssh_key_dir: "{{ node_config_directory }}/ssh"

backup_tool_data_dir_host: "{{ work_path }}/app/instancedata"
backup_tool_data_dir: "/app/instancedata"


```

## 二、main.yaml

```yaml
---
- include_tasks: config.yml

- include_role:
    name: api
    tasks_from: version

- include_tasks: deploy.yml
```


## 三、config.yml

```yaml
---

# mkdir -p /data/greatrds/etc/conductor
- name: create conductor config directory
  file:
    path: "{{ node_config_directory }}"
    state: directory
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: true

# mkdir -p /data/greatrds/etc/conductor/ssh
- name: ensure ssh_key_dir directory exists
  file:
    path: "{{ ssh_key_dir }}"
    state: directory
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0700
  become: True

# scp id_rsa /data/greatrds/etc/conductor/ssh/id_rsa
- name: copy ssh private key
  copy:
    src: "id_rsa"
    dest: "{{ ssh_key_dir }}/id_rsa"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: "0600"
  become: true

# scp id_rsa.pub /data/greatrds/etc/conductor/ssh/id_rsa.pub
- name: copy ssh public key
  copy:
    src: "id_rsa.pub"
    dest: "{{ ssh_key_dir }}/id_rsa.pub"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: "0644"
  become: true


```


## 四、deploy.yml

```yaml
---

# docker run -d --name greatrds-conductor -v /data/greatrds/etc/greatrds.conf:/etc/greatrds/greatrds.conf -v /data/greatrds/log:/var/log/rds -v /data/greatrds/etc/conductor/ssh:/root/.ssh -v /data/greatrds/app/instancedata:/app/instancedata -v /data/static:/opt/static greatrds:6.0.1.0rc4
# run_conductor
- name: start conductor container
  docker_container:
    name: greatrds-conductor
    image: "greatrds:{{ greatrds_version }}"
    network_mode: host
    volumes:
      - "{{ oslo_config_path_host }}:{{ oslo_config_path_mount }}"
      - "{{ rds_log_dir }}:/var/log/rds"
      - "{{ ssh_key_dir }}:/root/.ssh"
      - "{{ backup_tool_data_dir_host }}:{{ backup_tool_data_dir }}"
      - "{{ web_repo_static }}:{{ web_repo_static_mount }}"
    restart_policy: unless-stopped
    recreate: yes
    command: "run_conductor"
    interactive: yes
    tty: yes
    timeout: "{{ docker_container_timeout }}"
  become: yes

```


## 五、upgrade.yml

```yaml
---

- include_role:
    name: api
    tasks_from: upload

- include_role:
    name: api
    tasks_from: build

- include_tasks: deploy.yml


```



## 六、id_rsa

#### id_rsa
```yaml
-----BEGIN OPENSSH PRIVATE KEY-----
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
-----END OPENSSH PRIVATE KEY-----


```


#### id_rsa.pub


```yaml
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCuU5E7nSR6DDHDq4Y8FRuATr8s6pdXlcRW5Yz+J04Kb3YfCddyfkwzwxIY3f6gRBYPh+Lhs2gkX5tc+hM/jto+w4VTS+++y3kKKK2gUQvMB4SqbBRS2YmAA/cd7NUpMb2co1WIzlPvQ8JmzOb43QQrCwHcXnueDg4L5eJow7PNFpyLKToFzOOhyXdtRUm82xolFP4nKNydougMdKEI785Pau0aEIxh0efSA5bnytvrws4+kA7q9alUk00+1hQNHzHamHyvdsWWRmdLLbWMjY9GEBkMYYQCFUCtCXGdyMTSmK1q+Bi+OMWVWtkq3an/UOnOa+k/w0K3mz8iSKuLNBRwkGI92wji/plfAUKj30u63+kuVEO0vEomCjmqje9kzbuc0py89Uolf0Y3iqO7V+mwIQlAEjlN+jwf0qBBqLIFp0/iDdpiWpb31yRProkORbyIy1pYqELkaGHaGZutpwx1W7C8T6T4/py9q8mUZ5g3oFLFgfviKvbsgV+GJ2858X0= sqyu@optiplex-5070


```


















