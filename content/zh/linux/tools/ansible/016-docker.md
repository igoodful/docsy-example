---
title: 16. docker
description: docker
date: 2023-10-12
weight: 1600


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


## 一、docker_container
docker_container 用于部署 docker 容器

#### 1. 选项

| 选项           | 默认值 | 作用                                                 |
| :------------- | :----- | :--------------------------------------------------- |
| name           |        | 容器的名称，必选参数                                 |
| image          |        | 用于创建容器的仓库路径和标签                         |
| restart_policy |        | 容器重启策略：no，on-failure，always，unless-stopped |
| ports          |        | 端口映射                                             |
| volumes        |        | 容器中挂载的卷列表                                   |
| recreate       | false  | 是否强制重新创建现有容器                             |
| read_only      |        | 是否把容器的根文件系统挂载为只读                     |
| command        |        | 容器启动时执行的命令                                 |
| hostname       |        | 容器的主机名                                         |
| auto_remove    | false  | 当容器的进程退出时，在守护进程端启用容器的自动删除   |
| interactive    | false  | 容器启动后，即使未连接，也要保持 stdin 打开          |
| tty            | no     |                                                      |
| network_mode   |        |                                                      |
|                |        |                                                      |








```yaml
- name: run web repo server container
  docker_container:
    name: web_repo
    image: "{{ web_repo_image }}"
    restart_policy: always
    ports:
      - "{{ web_repo_port }}:80"
    volumes:
      - "{{ web_repo_host_path }}/repo:/opt/repo"
      - "{{ web_repo_static }}:/opt/static"
      - "{{ web_repo_nginx_conf_path }}:/etc/nginx/conf.d/default.conf"
    timeout: "{{ docker_container_timeout }}"
  become: yes


- name: docker run greatrds-db-manage upgrade
  docker_container:
    name: "{{ api_data_container }}"
    image: "greatrds:{{ greatrds_version }}"
    command: greatrds-db-manage upgrade
    volumes:
      - "{{ oslo_config_path_host }}:{{ oslo_config_path_mount }}"
    interactive: yes
    tty: yes
    auto_remove: yes
    timeout: "{{ docker_container_timeout }}"
  delegate_to: "{{ groups['database'][0] }}"
  run_once: yes
  become: yes

- name: wait for greatrds-db-manage
  command: "sudo docker ps"
  register: docker_ps_result
  retries: "{{ greatrds_api_retry }}"
  delay: 10
  until: "api_data_container not in docker_ps_result.stdout"

- name: docker run greatrds-init sql
  docker_container:
    name: "{{ api_init_container }}"
    image: "greatrds:{{ greatrds_version }}"
    command: "{{ api_init_command }}"
    volumes:
      - "{{ oslo_config_path_host }}:{{ oslo_config_path_mount }}"
    interactive: yes
    tty: yes
    auto_remove: yes
    timeout: "{{ docker_container_timeout }}"
  delegate_to: "{{ groups['database'][0] }}"
  run_once: yes
  become: yes


# 删除容器
- name: remove existing greatrds-auth docker container
  docker_container:
    name: "{{ auth_container }}"
    state: absent
    timeout: "{{ docker_container_timeout }}"
  become: yes
```



## 二、docker_image
docker_image 用于编译镜像



#### 1. 选项

| 选项           | 默认值                        | 作用                                             |
| :------------- | :---------------------------- | :----------------------------------------------- |
| `name`         | 必须提供                      | `name;repository/name;registry_server:port/name` |
| `tag`          | `latest`                      |                                                  |
| `source`       |                               | `build;load;pull;local`                          |
| `load_path`    |                               | `source=load`时指定包文件路径                    |
| `timeout`      | `60`                          | 1.配置优先，2.`DOCKER_TIMEOUT`，3.用默认值       |
| `state`        | `present`                     | `present;absent`                                 |
| `force_tag`    | `false`                       | `false;true`                                     |
| `force_source` | `false`                       | `false;true`                                     |
| `force_absent` | `false`                       | `false;true`                                     |
| `docker_host`  | `unix:///var/run/docker.sock` |                                                  |
| `debug`        | `false`                       | `false;true`                                     |


```yaml
---
- name: transfer and extract docker images package
  unarchive:
    src: "../../files/install-base/{{ arch_modifier }}/{{ docker_images_package }}"
    dest: "{{ tmp_dir }}"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755

- name: load greatrds docker images
  docker_image:
    name: "{{ item.name }}"
    tag: "{{ item.tag }}"
    source: load
    load_path: "{{ docker_images_tmp_path }}/{{ item.file }}.tar"
  loop:
    - { file: alertmanager, name: prom/alertmanager, tag: latest }
    - { file: greatrds_base, name: greatrds_base, tag: latest }
    - { file: keepalived, name: keepalived, tag: latest }
    - { file: mysql, name: mysql, tag: 8.0.27 }
    - { file: nginx, name: nginx, tag: 1.22.0-alpine }
    - { file: prometheus, name: prom/prometheus, tag: latest }
    - { file: pypiserver, name: pypiserver/pypiserver, tag: latest }
    - { file: rabbitmq, name: rabbitmq, tag: 3.9.7-management }
#    - { file: redis, name: redis, tag: 4.0.14 }
    - { file: registry, name: registry, tag: 2 }
  become: yes

```


```yaml
# build.pull=no source=build force_source=yes doesn't work
- name: build greatrds docker image
  docker_image:
    name: greatrds
    tag: "{{ greatrds_version }}"
    build:
      path: "{{ api_docker_dir }}"
    nocache: yes
  become: true


```





## 三、docker_service

docker_service 模块用于部署 docker compose
















