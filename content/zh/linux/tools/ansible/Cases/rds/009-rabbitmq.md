---
title: 09. rabbitmq
description: rabbitmq
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
# defaults file for rabbitmq
rabbitmq_container: "rabbitmq"
rabbitmq_image: "rabbitmq:3.9.7-management"
rabbitmq_erlang_cookie: "{{ rabbitmq_cluster_cookie }}"
rabbitmq_dir: "{{ base_dir }}/rabbitmq"
rabbitmq_etc_dir: "{{ base_dir }}/rabbitmq/etc"
rabbitmq_data_dir: "{{ rabbitmq_dir }}/data"
rabbitmq_log_dir: "{{ rabbitmq_dir }}/log"
# there is no "master" in the realm of RabbitMQ, this variable refers to the first rabbitmq node
rabbitmq_cluster_master: "{{ groups['rabbitmq'][0] }}"
rabbitmq_cluster_cookie: 123456
rabbitmq_start_retry: 6

```


## deploy.yml

```yaml
---

# mkdir -p /data/rabbitmq/{data,etc,log}
- name: create rabbitmq data directory
  file:
    path: "{{ item }}"
    state: "directory"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0777
  become: true
  with_items:
    - "{{ rabbitmq_data_dir }}"
    - "{{ rabbitmq_etc_dir }}"
    - "{{ rabbitmq_log_dir }}"

# scp erlang_cookie.j2 /data/rabbitmq/data/.erlang.cookie
- name: updating rabbitmq erlang cookie
  template:
    src: erlang_cookie.j2
    dest: "{{ rabbitmq_data_dir }}/.erlang.cookie"
    mode: 0400

# scp rabbitmq.conf.j2 /data/rabbitmq/etc/rabbitmq.conf
- name: create rabbitmq.conf
  template:
    src: rabbitmq.conf.j2
    dest: "{{ rabbitmq_etc_dir }}/rabbitmq.conf"
    mode: 0666

# scp enabled_plugins.j2 /data/rabbitmq/etc/enabled_plugins
- name: create rabbitmq enabled_plugins
  template:
    src: enabled_plugins.j2
    dest: "{{ rabbitmq_etc_dir }}/enabled_plugins"
    mode: 0666

# RabbitMQ communicates via its peer's hostname
# peering by IP address is not supported by RabbitMQ
# etc_hosts param of docker_container module is dict of host-to-IP mappings, opposite to /etc/hosts

#
- name: set etc_hosts
  set_fact:
    etc_hosts: "{{ etc_hosts | default({}) | combine({ item: hostvars[item].ansible_host }) }}"
  loop: "{{ groups['rabbitmq'] }}"

#
- name: start rabbitmq container
  docker_container:
    name: "{{ rabbitmq_container }}"
    image: "{{ rabbitmq_image }}"
    hostname: "{{ inventory_hostname }}"
    volumes:
      - "{{ rabbitmq_data_dir }}:/var/lib/rabbitmq"
      - "{{ rabbitmq_log_dir }}:/var/log/rabbitmq/"
      - "{{ rabbitmq_etc_dir }}:/etc/rabbitmq"
    # port mapping is not supported in host network mode
    ports:
      - "{{ rabbitmq_port }}:5672"  # AMQP 0-9-1 port
      - "{{ rabbitmq_web_port }}:15672" # web management port
      - "4369:4369" # epmd port
      - "25672:25672"  # inter-node and CLI tools communication port
    etc_hosts: "{{ etc_hosts }}"
    restart_policy: unless-stopped
    recreate: yes
    timeout: "{{ docker_container_timeout }}"
  become: yes

#
- name: wait 30 seconds for RabbitMQ to startup
  command: "sudo docker exec {{ rabbitmq_container }} rabbitmqctl await_startup"
  register: command_result
  retries: "{{ rabbitmq_start_retry }}"
  delay: 5
  until: "'Error' not in command_result.stderr"


```


## join_cluster.yml

```yaml
---
- name: stop rabbitmq app
  command: "sudo docker exec {{ rabbitmq_container }} rabbitmqctl stop_app"
  register: command_result
  failed_when: "'Error' in command_result.stderr"

- name: add this node to cluster
  command: "sudo docker exec {{ rabbitmq_container }} rabbitmqctl join_cluster rabbit@{{ rabbitmq_cluster_master }}"
  register: command_result
  failed_when: "'Error' in command_result.stderr"

- name: start rabbitmq app
  command: "sudo docker exec {{ rabbitmq_container }} rabbitmqctl start_app"
  register: command_result
  failed_when: "'Error' in command_result.stderr"


```

## enable_ha_queues.yml

```yaml
---
- name: check if ha-mode is already enabled
  shell: sudo docker exec {{ rabbitmq_container }} rabbitmqctl list_policies
  changed_when: false
  register: list_policies
  failed_when: "'Error' in list_policies.stderr"

- name: set ha-mode to exactly two nodes for all queues for backup
  shell: sudo docker exec {{ rabbitmq_container }} rabbitmqctl set_policy ha-all ".*" '{"ha-mode":"all","ha-sync-mode":"automatic"}'
  when: list_policies.stdout.find("ha-all") == -1
  register: command_result
  failed_when: "'Error' in command_result.stderr"


```




## main.yml


```yaml
---

- include_tasks: deploy.yml

- block:
  - include_tasks: join_cluster.yml
    when: inventory_hostname != rabbitmq_cluster_master

  - include_tasks: enable_ha_queues.yml
    when: inventory_hostname == rabbitmq_cluster_master

  when: deploy_mode == "dual"


```



## rabbitmq.conf.j2

```yaml
default_vhost = /
default_user = {{ om_rpc_user }}
default_pass = {{ rabbitmq_password }}


loopback_users.guest = false
listeners.tcp.default = 5672
management.tcp.port = 15672

```


## erlang_cookie.j2

```yaml
{{ rabbitmq_erlang_cookie }}


```




## enabled_plugins.j2

```yaml
[rabbitmq_management,rabbitmq_prometheus].


```








