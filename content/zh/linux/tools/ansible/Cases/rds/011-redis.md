---
title: 11. redis
description: redis
date: 2023-10-12
weight: 60000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



{{< alert color="secondary" >}}

实际不需要

{{< /alert >}}



## 一、defaults/main.yaml

```yaml
---
# defaults file for redis

redis_dir: "{{ base_dir }}/redis"
redis_data_dir: "{{ redis_dir }}/data"
redis_container_name: "redis"
redis_image: "redis:4.0.14"


```


## 二、deploy.yml

```yaml
---

- name: start redis container
  docker_container:
    name: "{{ redis_container_name }}"
    image: "{{ redis_image }}"
    command: "redis-server --requirepass {{ redis_password }}"
    privileged: true
    restart_policy: unless-stopped
    ports:
      - "{{ redis_port }}:6379"
    volumes:
      - "{{ redis_data_dir }}:/data"
    timeout: "{{ docker_container_timeout }}"
  become: yes

```






















