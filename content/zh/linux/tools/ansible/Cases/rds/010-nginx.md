---
title: 10. nginx
description: nginx
date: 2023-10-12
weight: 60000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



{{< alert color="secondary" >}}

1. `mkdir -p /data/nginx/etc/conf.d`
2. `mkdir -p /data/greatrds/web`
3. `find rds-ansible/files/release -name "*.tar.gz"`
4. `tar -xzf das-cfd2d2c8.tar.gz rds-cfd2d2c8.tar.gz -C /data/greatrds/web/`
   1. `scp http_default.conf.j2 /data/nginx/etc/conf.d/default.conf`
   2. `scp https_default.conf.j2 /data/nginx/etc/conf.d/default.conf`
      1. `mkdir -p /data/nginx/etc/ssl`
      2. `scp greatrds.key /data/nginx/etc/ssl/`
      3. `scp greatrds.crt /data/nginx/etc/ssl/`
5.  `scp config.js.j2 /data/greatrds/web/db/config.js`
6.  `scp config.js.j2 /data/greatrds/web/dist/config.js`
7.  `docker run -d --name nginx -p 18080:8080 -v /data/nginx/etc/conf.d/default.conf:/etc/nginx/conf.d/default.conf -v /data/nginx/log:/var/log/nginx -v /data/greatrds/web:/usr/share/nginx/web -v /data/nginx/etc/ssl:etc/nginx/ssl nginx:1.22.0-alpine`


{{< /alert >}}



## 一、defaults/main.yaml

```yaml
---
# defaults file for nginx

nginx_dir: "{{ base_dir }}/nginx"
nginx_conf_dir: "{{ nginx_dir }}/etc"
nginx_ssl_dir_host: "{{ nginx_conf_dir }}/ssl"
nginx_ssl_dir_mount: "/etc/nginx/ssl"
# SSL private key file name
nginx_ssl_key: "greatrds.key"
# SSL certificate file name
nginx_ssl_crt: "greatrds.crt"
nginx_log_dir: "{{ nginx_dir }}/log"
nginx_image: "nginx:1.22.0-alpine"
web_host_path: "{{ base_dir }}/greatrds/web"
# container mount path of rds-dashboard static files
web_mount_path: "/usr/share/nginx/web"
enable_https: "yes"
package_path_name: "release"
securitydb: "no"
# cluster name oem
rds_description: "金科MySQL"
rds_cluster_description:  "分布式数据库5.0"
rds_ms_description:  "主从集群"
rds_alive_description:  "alive集群"


```


## 二、deploy.yml

```yaml
---

# mkdir -p /data/nginx/etc/conf.d
- name: create nginx conf path
  file:
    path: "{{ nginx_conf_dir }}/conf.d"
    state: directory
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: true

# mkdir -p /data/greatrds/web
- name: create folder for static files
  file:
    path: "{{ web_host_path }}"
    state: directory
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: true

# CAUTION: the root directory in db.zip should be db/ instead of dist/, which is the npm default
# ZIP format is not support at the moment

# find rds-ansible/files/release -name "*.tar.gz"
- name: find new static file
  find:
    paths:
      - "{{ role_path }}/../../files/{{ package_path_name }}/web/"
    patterns: "*.tar.gz"
  delegate_to: localhost
  register: new_static

# tar -xzf das-cfd2d2c8.tar.gz rds-cfd2d2c8.tar.gz -C /data/greatrds/web/
- name: transfer and extract web static files
  unarchive:
    src: "{{ item.path }}"
    dest: "{{ web_host_path }}"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  loop: "{{ new_static.files }}"
  become: true

# enable_https=no
# scp http_default.conf.j2 /data/nginx/etc/conf.d/default.conf
- name: copy http_default.conf template
  template:
    src: http_default.conf.j2
    dest: "{{ nginx_conf_dir }}/conf.d/default.conf"
  when: enable_https != "yes"

# enable_https=yes
# scp https_default.conf.j2 /data/nginx/etc/conf.d/default.conf
- block:
  - name: copy https_default.conf template
    template:
      src: https_default.conf.j2
      dest: "{{ nginx_conf_dir }}/conf.d/default.conf"
      owner: "{{ rds_user }}"
      group: "{{ rds_group }}"
      mode: 0755

# mkdir -p /data/nginx/etc/ssl
  - name: create nginx ssl directory
    file:
      path: "{{ nginx_ssl_dir_host }}"
      state: directory
      owner: "{{ rds_user }}"
      group: "{{ rds_group }}"
      mode: 0755
    become: true

# scp greatrds.key /data/nginx/etc/ssl/
  - name: copy ssl private key
    copy:
      src: "{{ nginx_ssl_key }}"
      dest: "{{ nginx_ssl_dir_host }}"
      owner: "{{ rds_user }}"
      group: "{{ rds_group }}"
      mode: 0755
    become: true

# scp greatrds.crt /data/nginx/etc/ssl/
  - name: copy ssl certificate
    copy:
      src: "{{ nginx_ssl_crt }}"
      dest: "{{ nginx_ssl_dir_host }}"
      owner: "{{ rds_user }}"
      group: "{{ rds_group }}"
      mode: 0755
    become: true
  when: enable_https == "yes"

# scp config.js.j2 /data/greatrds/web/db/config.js
- name: set api config for db frontend
  template:
    src: config.js.j2
    dest: "{{ web_host_path }}/db/config.js"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: true

# scp config.js.j2 /data/greatrds/web/dist/config.js
- name: set api config for dist frontend
  template:
    src: config.js.j2
    dest: "{{ web_host_path }}/dist/config.js"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: true


# docker run -d --name nginx -p 18080:8080 -v /data/nginx/etc/conf.d/default.conf:/etc/nginx/conf.d/default.conf -v /data/nginx/log:/var/log/nginx -v /data/greatrds/web:/usr/share/nginx/web -v /data/nginx/etc/ssl:etc/nginx/ssl nginx:1.22.0-alpine
- name: start nginx container
  docker_container:
    name: nginx
    image: "{{ nginx_image }}"
    network_mode: host
    privileged: yes
    volumes:
      - "{{ nginx_conf_dir }}/conf.d/default.conf:/etc/nginx/conf.d/default.conf"
      - "{{ nginx_log_dir }}:/var/log/nginx"
      - "{{ web_host_path }}:{{ web_mount_path }}"
      - "{{ nginx_ssl_dir_host }}:{{ nginx_ssl_dir_mount }}"
    restart_policy: unless-stopped
    recreate: yes
    timeout: "{{ docker_container_timeout }}"
  become: yes


```





## 三、config.js.j2

```js
{% if enable_https == "no" %}
window.IS_HTTP = true
// httpConfig 对应 pr打包 构建请求后端服务地址与端口
window.API_CONFIG = {
  baseIP: '{{ api_address }}',
  basePort: '{{ nginx_port }}'
}
{% else %}
// httpConfig 对应 pr打包 构建请求后端服务地址与端口
window.API_CONFIG = {
  baseIP: '{{ api_address }}',
  basePort: '{{ nginx_ssl_port }}'
}
{% endif %}

// 对应数据库版本描述
{% if securitydb == "yes" %}
window.RDS_DESCRIPTION = '万里安全数据库软件'
{% else %}
window.RDS_DESCRIPTION = '{{ rds_description }}'
{% endif %}
window.RDS_DESCRIPTION_TITLE = '是一款基于MySQL的关系型数据库集群产品，提供灵活便捷高可用的数据库服务。'
window.RDS_DESCRIPTION_DETAIL = '是一款基于MySQL的关系型数据库集群产品，提供灵活便捷高可用的数据库服务。'

window.RDS_CLUSTER_DESCRIPTION = '{{ rds_cluster_description }}'
window.RDS_CLUSTER_DESCRIPTION_TITLE = '是一款国产自主可控的分布式关系型数据库集群软件，当数据量、负载增大时数据库集群可以分层按需进行无限制的动态扩展,以满足业务发展需要'
window.RDS_CLUSTER_DESCRIPTION_DETAIL = '是一款国产自主可控的分布式关系型数据库集群软件。采用 shared-nothing 的原生分布式架构;基于经典的 paxos 协议实现数据库系统的稳定可靠无单点;基于自动数据 sharding 与分布式并行计算技术实现数据库系统高性能;基于计算存储分离与数据在线重分布技术,当数据量、负载增大时,数据库集群可以分层按需进行无限制的动态扩展,以满足业务发展需要。'

window.RDS_MS_DESCRIPTION = '{{ rds_ms_description }}'
window.RDS_MS_DESCRIPTION_TITLE = '基于keepAlived实现的一主一备的经典高可用架构。管理控制台为其提供全面的监控信息，简单易用，灵活管理，可视又可控'
window.RDS_MS_DESCRIPTION_DETAIL = '基于keepAlived实现的一主一备的经典高可用架构。管理控制台为其提供全面的监控信息，简单易用，灵活管理，可视又可控。'

window.RDS_ALIVE_DESCRIPTION = '{{ rds_alive_description }}'
window.RDS_ALIVE_DESCRIPTION_TITLE = '基于xenon实现的一主多从的高可用架构。管理控制台为其提供全面的监控信息，简单易用，灵活管理，可视又可控'
window.RDS_ALIVE_DESCRIPTION_DETAIL = ''

```



## 四、http_default.conf.j2

```nginx
upstream auth_api {
{% for node in groups['controller'] %}
  server {{ hostvars[node].ansible_host }}:{{ auth_port }};
{% endfor %}
}

upstream rds_api {
{% for node in groups['controller'] %}
  server {{ hostvars[node].ansible_host }}:{{ api_port }};
{% endfor %}
}

upstream rds_repo_api {
  server {{ repo_host }}:{{ api_port }};
}

upstream rds_repo_static {
  server {{ repo_host }}:{{ web_repo_port }};
}

server {
  listen {{ nginx_port }};
  # listen [::]:{{ nginx_port }};
  client_max_body_size 20M;
  server_name _;
  server_tokens off;
  add_header X-Frame-Options SAMEORIGIN;

  location  /das {
    alias {{ web_mount_path }}/db;
    try_files $uri $uri/ /index.html;
    index index.html;
  }

  location /control {
    alias {{ web_mount_path }}/dist;
    try_files $uri $uri/ /index.html;
    index index.html;
  }

  location  /v1/auth/ {
    proxy_pass http://auth_api/v1/auth/;
    proxy_redirect off;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
  location  /v1/rds/ {
    proxy_pass http://rds_api/v1/rds/;
    proxy_redirect off;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }

  location  /v1/rds/cluster_template/upload_pre_cehck {
    proxy_pass http://rds_repo_api/v1/rds/cluster_template/upload_pre_cehck;
    proxy_redirect off;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }

  location  /v1/rds/cluster_template/upload {
    proxy_pass http://rds_repo_api/v1/rds/cluster_template/upload;
    proxy_redirect off;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }

  location  /static/ {
    proxy_pass http://rds_repo_static/gtms/static/;
    proxy_redirect off;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }

  location  /v1/rds/cluster_template/upload_package {
    proxy_pass http://rds_repo_api/v1/rds/cluster_template/upload_package;
    proxy_redirect off;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }

  location  /v1/rds/cluster_template/delete_installation_package {
    proxy_pass http://rds_repo_api/v1/rds/cluster_template/delete_installation_package;
    proxy_redirect off;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }

  location  /v1/rds/cluster_template/package_pre_cehck {
    proxy_pass http://rds_repo_api/v1/rds/cluster_template/package_pre_cehck;
    proxy_redirect off;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }

  location  /v1/rds/cluster_template/package_type {
    proxy_pass http://rds_repo_api/v1/rds/cluster_template/package_type;
    proxy_redirect off;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
}


```



## 五、https_default.conf.j2


```nginx
upstream auth_api {
{% for node in groups['controller'] %}
  server {{ hostvars[node].ansible_host }}:{{ auth_port }};
{% endfor %}
}

upstream rds_api {
{% for node in groups['controller'] %}
  server {{ hostvars[node].ansible_host }}:{{ api_port }};
{% endfor %}
}

upstream rds_repo_api {
  server {{ repo_host }}:{{ api_port }};
}

upstream rds_repo_static {
  server {{ repo_host }}:{{ web_repo_port }};
}

server {
  listen {{ nginx_ssl_port }} default_server ssl;
  # listen [::]:{{ nginx_ssl_port }} default_server ssl;
  client_max_body_size 20M;
  server_name _;
  server_tokens off;
  add_header X-Frame-Options SAMEORIGIN;

  ssl_certificate {{ nginx_ssl_dir_mount }}/{{ nginx_ssl_crt }};
  ssl_certificate_key {{ nginx_ssl_dir_mount }}/{{ nginx_ssl_key }};

  location  /das {
    alias {{ web_mount_path }}/db;
    try_files $uri $uri/ /index.html;
    index index.html;
  }

  location /control {
    alias {{ web_mount_path }}/dist;
    try_files $uri $uri/ /index.html;
    index index.html;
  }

  location  /v1/auth/ {
    proxy_pass http://auth_api/v1/auth/;
    proxy_redirect off;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
  location  /v1/rds/ {
    proxy_pass http://rds_api/v1/rds/;
    proxy_redirect off;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }

  location  /v1/rds/cluster_template/upload_pre_cehck {
    proxy_pass http://rds_repo_api/v1/rds/cluster_template/upload_pre_cehck;
    proxy_redirect off;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }

  location  /v1/rds/cluster_template/upload {
    proxy_pass http://rds_repo_api/v1/rds/cluster_template/upload;
    proxy_redirect off;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }

  location  /static/ {
    proxy_pass http://rds_repo_static/gtms/static/;
    proxy_redirect off;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
}


```










