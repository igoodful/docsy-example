---
title: 17. auth
description: auth
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
project: "greatrds_auth"
auth_dir: "{{ base_dir }}/{{ project }}" # rds auth 的配置文件路径
node_config_directory: "{{ auth_dir }}/etc"
auth_log_dir: "{{ auth_dir }}/log"
work_path: "{{ base_dir }}/{{ project }}"
auth_database_password: "{{ database_password }}"
auth_database_port: "{{ mysql_port }}"
auth_database_user: "{{ mysql_user }}"
auth_docker_dir: "{{ tmp_dir }}/docker-build/auth" # directory containing the context and Dockerfile for building greatrds image
pypyserver_packages_path: "{{ base_dir }}/pypiserver/packages/"
auth_data_container: "greatrds-auth-db-upgrade"
auth_init_container: "greatrds-auth-init"
auth_container: "greatrds-auth"
securitydb: "no"
init_command: "{{ 'greatrds-auth-init sql' if securitydb == 'no' else 'greatrds-auth-init sql -o security -r cluster4' }}"
greatrds_auth_retry: 12
```


## 二、build.yml

```yaml
---

# ls -1 /data/pypiserver/packages/ | grep greatrds_auth-.*whl | awk -F '-' '{print $2}'
# greatrds_auth-6.0.1.0rc4-py3-none-any.whl
- name: get the greatrds_auth wheel version
  shell: "ls -1 {{ pypyserver_packages_path }} | grep greatrds_auth-.*whl | awk -F '-' '{print $2}'"
  delegate_to: "{{ groups['pypiserver'][0] }}"
  register: version_result
  become: true

# version_result.stdout=6.0.1.0rc4
- name: set greatrds_auth version
  set_fact:
    greatrds_auth_version: "{{ version_result.stdout }}"

# mkdir -p /data/tmp/docker-build
- name: create docker-build directory
  file:
    path: "{{ tmp_dir }}/docker-build"
    state: directory
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: true

#  mkdir -p /data/tmp/docker-build/auth
- name: create auth_docker_dir
  file:
    path: "{{ auth_docker_dir }}"
    state: directory
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: true

# scp Dockerfile.auth.j2 /data/tmp/docker-build/auth/Dockerfile
- name: template Dockerfile for greatrds-auth
  template:
    src: Dockerfile.auth.j2
    dest: "{{ auth_docker_dir }}/Dockerfile"

# docker rm -f greatrds-auth
- name: remove existing greatrds-auth docker container
  docker_container:
    name: "{{ auth_container }}"
    state: absent
    timeout: "{{ docker_container_timeout }}"
  become: yes

# docker build -t greatrds_auth:6.0.1.0rc4
- name: build greatrds_auth docker image
  docker_image:
    name: greatrds_auth
    tag: "{{ greatrds_auth_version }}"
    build:
      path: "{{ auth_docker_dir }}"
    nocache: yes
  become: true

```




## 三、config.yml

```yaml
---

# mkdir -p /data/greatrds_auth/etc
- name: create auth config directory
  file:
    path: "{{ node_config_directory }}"
    state: directory
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: true

# cp /data/greatrds_auth/etc/greatrds_auth.conf /data/greatrds_auth/etc/greatrds_auth.conf.{{ ansible_date_time.date }}
- name: Backup {{ node_config_directory }} greatrds_auth.conf
  copy:
    src: "{{ node_config_directory }}/greatrds_auth.conf"
    dest: "{{ node_config_directory }}/greatrds_auth.conf.{{ ansible_date_time.date }}"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    remote_src: yes
    mode: 0755
  become: true
  ignore_errors: yes

# scp greatrds_auth.conf.j2 /data/greatrds_auth/etc/greatrds_auth.conf
- name: generate greatrds_auth.conf
  template:
    src: greatrds_auth.conf.j2
    dest: "{{ node_config_directory }}/greatrds_auth.conf"
    mode: "0755"

# create database auth
- name: Create rds auth database
  mysql_db:
    name: "{{ auth_database_name }}"
    login_port: "{{ auth_database_port }}"
    login_user: "{{ auth_database_user }}"
    login_password: "{{ auth_database_password }}"
    state: present
    encoding: "utf8mb4"
  delegate_to: "{{ groups['database'][0] }}"
  run_once: yes

# docker run -d --name greatrds-auth-db-upgrade -v  /data/greatrds_auth/etc:/etc/greatrds_auth greatrds_auth:6.0.1.0rc4
# greatrds-auth-db-manage upgrade
- name: docker run greatrds-auth-db-manage upgrade
  docker_container:
    name: "{{ auth_data_container }}"
    image: "greatrds_auth:{{ greatrds_auth_version }}"
    command: greatrds-auth-db-manage upgrade
    volumes:
      - "{{ node_config_directory }}/:/etc/greatrds_auth"
    interactive: yes
    tty: yes
    auto_remove: yes
    timeout: "{{ docker_container_timeout }}"
  delegate_to: "{{ groups['database'][0] }}"
  run_once: yes
  become: yes

# sudo docker ps
- name: sudo docker ps
  command: "sudo docker ps"
  register: docker_ps_result
  delegate_to: "{{ groups['database'][0] }}"
  run_once: yes

- name: sudo docker ps debug
  debug: var=docker_ps_result verbosity=0

# sudo docker ps
- name: wait for auth upgrade
  command: "sudo docker ps"
  register: docker_ps_result
  retries: "{{ greatrds_auth_retry }}"
  delay: 10
  until: "auth_data_container not in docker_ps_result.stdout"
  delegate_to: "{{ groups['database'][0] }}"
  run_once: yes

# docker run -d --name greatrds-auth-init -v  /data/greatrds_auth/etc:/etc/greatrds_auth greatrds_auth:6.0.1.0rc4
# greatrds-auth-init sql
- name: docker run greatrds-auth-init sql
  docker_container:
    name: "{{ auth_init_container }}"
    image: "greatrds_auth:{{ greatrds_auth_version }}"
    command: "{{init_command}}"
    volumes:
      - "{{ node_config_directory }}/:/etc/greatrds_auth"
    interactive: yes
    tty: yes
    auto_remove: yes
    timeout: "{{ docker_container_timeout }}"
  delegate_to: "{{ groups['database'][0] }}"
  run_once: yes
  become: yes

# sudo docker ps
- name: wait for greatrds-auth-init sql
  command: "sudo docker ps"
  register: docker_ps_result
  retries: "{{ greatrds_auth_retry }}"
  delay: 10
  until: "auth_init_container not in docker_ps_result.stdout"
  delegate_to: "{{ groups['database'][0] }}"
  run_once: yes

```




## 四、deploy.yml

```yaml
---

# docker run -d --name greatrds-auth -p 9018:9018 -v  /data/greatrds_auth/etc:/etc/greatrds_auth -v /data/greatrds_auth/log:/var/log/rds/ greatrds_auth:6.0.1.0rc4
- name: start auth container
  docker_container:
    name: "{{ auth_container }}"
    image: "greatrds_auth:{{ greatrds_auth_version }}"
    ports:
      - "{{ auth_port }}:{{ auth_port }}"
    volumes:
      - "{{ node_config_directory }}:/etc/{{ project }}"
      - "{{ auth_log_dir }}:/var/log/rds/"
    restart_policy: unless-stopped
    recreate: yes
    timeout: "{{ docker_container_timeout }}"
  become: yes


```




## 五、Dockerfile.auth.j2

```Dockerfile
# Dockerfile for GreatRDS Auth

FROM greatrds_base

RUN pip install greatrds-auth -i http://{{ pypiserver_host }}:{{ pypiserver_port }}/simple  --trusted-host {{ pypiserver_host }}

ENV GREATRDS_AUTH_LOCALEDIR  /usr/local/lib/python3.6/site-packages/greatrds_auth/locale

CMD gunicorn --paste /usr/local/etc/greatrds_auth/api-paste.ini --access-logfile /var/log/rds/auth.log --error-logfile /var/log/rds/auth.log --capture-output

```



## 六、auth-paste.ini

```yaml
[pipeline:main]
pipeline = cors healthcheck http_proxy_to_wsgi request_id authtoken clog api_v1

[app:api_v1]
paste.app_factory = greatrds_auth.api.app:app_factory

[filter:request_id]
paste.filter_factory = oslo_middleware:RequestId.factory

[filter:policy_check]
ignore_url = /v1/auth/login,/v1/auth/logout,/v1/auth/identity_info,/v1/auth/docs/.*,/v1/auth/docs
paste.filter_factory = greatrds_auth.api.middleware.permissions:PolicyCheckMiddleware.factory

[filter:authtoken]
acl_public_routes = /v1/auth/login,/v1/auth/product_information,/v1/auth/product_information/icon
paste.filter_factory = greatrds_auth.api.middleware.auth_token:AuthTokenMiddleware.factory

[filter:makeauthtoken]
acl_public_routes = /v1, /api, /v1/docs, /docs/.*, /
paste.filter_factory = greatrds.api.middleware.make_auth_token:AuthTokenMiddleware.factory

[filter:clog]
paste.filter_factory = greatrds_auth.api.middleware.clog:ClogMiddleware.factory

[filter:debug]
paste.filter_factory = oslo_middleware:Debug.factory

[filter:cors]
paste.filter_factory =  oslo_middleware.cors:filter_factory
oslo_config_project = greatrds_auth
allowed_origin = *

[filter:healthcheck]
paste.filter_factory = oslo_middleware:Healthcheck.factory
backends = disable_by_file
disable_by_file_path = /etc/greatrds_auth/healthcheck_disable
detailed = False

[filter:http_proxy_to_wsgi]
paste.filter_factory = oslo_middleware.http_proxy_to_wsgi:HTTPProxyToWSGI.factory
oslo_config_project = greatrds_auth

[server:main]
use = egg:gunicorn#main
host = 0.0.0.0
port = 9018
proc_name = greatrds_auth
workers = 8
accesslog = -
loglevel = info


```




















