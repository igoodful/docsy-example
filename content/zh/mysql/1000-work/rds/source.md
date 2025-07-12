---
title: RDS源码
description: RDS源码
date: 2023-10-23
weight: 10


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
- 安装包：`RDS-5.1.9.0rc16-release-10-17-x64-arm64.tar.gz`
- 切记不要将对RDS安装目录和docker安装目录整体进行chown和chmod
{{< /alert >}}


## 一、os

```viz-dot
digraph "os" {
        rankdir = LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = box;
                fontsize = "12"
        ];

        "os" -> "dns";
        "dns" -> "ansible_processor_vcpus<2?";
        "dns" -> "/etc/hostname"[label="hostname"];
        "dns" -> "/etc/hosts";
        "dns" -> "/etc/resolv.conf";
        "os" -> "/etc/security/limits.conf";
        "/etc/security/limits.conf" -> "nofile"[label="soft hard 1024000"];
        "/etc/security/limits.conf" -> "nproc"[label="soft hard 1024000"];
        "/etc/security/limits.conf" -> "sigpending"[label="soft hard 1024000"];

        "os" -> "firewalld";
        "os" -> "network";
        "network" -> "/etc/sysctl.conf"[label="net.ipv4.ip_forward = 1"];
        "network" -> "/etc/sysctl.d/"[label="net\.ipv4\.ip_forward\s*=\s*0"];
        "os" -> "logrotate";
        "logrotate" -> "base_dir/greatrds/log";
        "logrotate" -> "base_dir/greatrds_auth/log";
        "logrotate" -> "base_dir/nginx/log";
        "logrotate" -> "docker_dir/containers";

        "os" -> "dir";
        "dir" -> "base_dir/app"[label="0770"];
        "dir" -> "base_dir/tmp"[label="0770"];
        "os" -> "ansible-dep";
        "ansible-dep" -> "mysql-client-arm64";
        "ansible-dep" -> "mysql-client-x64";
        "ansible-dep" -> "ansible-dep-wheels-py2.tar.gz";
        "ansible-dep" -> "pip, setuptools and wheel";
        "ansible-dep" -> "docker-4.2.1-py2.py3-none-any.whl";
        "ansible-dep" -> "PyMySQL-0.9.3-py2.py3-none-any.whl";


}
```

- ansible_os_family
  - RedHat
  - Debian
  - Kylin Linux Advanced Server
  - FreeBSD
  - Suse
  - Archlinux
  - Solaris
  - Darwin


```yaml
{{ docker_dir }}/containers/*/*.log {
  rotate 7
  daily
  compress
  missingok
  delaycompress
  copytruncate
}

/data/greatrds/log/*.log {
  rotate 7
  daily
  compress
  missingok
  delaycompress
  copytruncate
}


/data/greatrds_auth/log/*.log {
  rotate 7
  daily
  compress
  missingok
  delaycompress
  copytruncate
}

/data/nginx/log/*.log {
  rotate 7
  daily
  compress
  missingok
  delaycompress
  copytruncate
}
```

## 二、docker

```viz-dot
digraph "docker" {
        rankdir = LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
        ];

        "docker" -> "tmp_dir"[label="0755"];
        "docker" -> "unarchive docker-20.10.2-{{ arm64/x64 }}.tgz";
        "docker" -> "cp -rf {{ tmp_dir }}/docker/* /usr/bin/";
        "docker" -> "/etc/systemd/system/docker.service";
        "docker" -> "/etc/docker";
        "docker" -> "/etc/docker/daemon.json";
        "docker" -> "group: docker";
        "docker" -> "add rds_user group: docker";
        "docker" -> "sudo docker ps";
        "docker" -> "chmod 666 /var/run/docker.sock";

}
```


- `tar xzf docker-20.10.2-x64.tgz`
```bash
2024-05-11T23:21:59 [root@node77 /home/mysql/RDS-5.1.9.0rc16-release-10-17-x64-arm64/rds-ansible/files/install-base/x64/docker] $ ll
total 224244
-rwxr-xr-x 1 centos centos 39602024 Dec 29  2020 containerd
-rwxr-xr-x 1 centos centos  7270400 Dec 29  2020 containerd-shim
-rwxr-xr-x 1 centos centos  9957376 Dec 29  2020 containerd-shim-runc-v2
-rwxr-xr-x 1 centos centos 21516360 Dec 29  2020 ctr
-rwxr-xr-x 1 centos centos 55151081 Dec 29  2020 docker
-rwxr-xr-x 1 centos centos 78846368 Dec 29  2020 dockerd
-rwxr-xr-x 1 centos centos   708616 Dec 29  2020 docker-init
-rwxr-xr-x 1 centos centos  2928566 Dec 29  2020 docker-proxy
-rwxr-xr-x 1 centos centos 13631136 Dec 29  2020 runc

```

- `/etc/systemd/system/docker.service`
```service
[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
After=network-online.target firewalld.service
Wants=network-online.target

[Service]
Type=notify
# the default is not to use systemd for cgroups because the delegate issues still
# exists and systemd currently does not support the cgroup feature set required
# for containers run by docker
ExecStart=/usr/bin/dockerd --data-root {{ docker_dir }}
ExecReload=/bin/kill -s HUP $MAINPID
# Having non-zero Limit*s causes performance problems due to accounting overhead
# in the kernel. We recommend using cgroups to do container-local accounting.
LimitNOFILE=infinity
LimitNPROC=infinity
LimitCORE=infinity
# Uncomment TasksMax if your systemd version supports it.
# Only systemd 226 and above support this version.
#TasksMax=infinity
TimeoutStartSec=0
# set delegate yes so that systemd does not reset the cgroups of docker containers
Delegate=yes
# kill only the docker process, not all processes in the cgroup
KillMode=process
# restart the docker process if it exits prematurely
Restart=on-failure
StartLimitBurst=3
StartLimitInterval=60s

[Install]
WantedBy=multi-user.target
```

- `/etc/docker/daemon.json`
```json
{
    "registry-mirrors": ["http://{{ docker_registry }}"],
    "insecure-registries": ["{{ docker_registry }}"]
}
```


#### 1. `install-docker.yml`
```yaml
---

- name: create tmp_dir
  file:
    path: "{{ tmp_dir }}"
    state: directory
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: true

- name: transfer and extract docker engine package
  unarchive:
    src: "../../files/install-base/{{ arch_modifier }}/{{ docker_engine_package }}"
    dest: "{{ tmp_dir }}"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755

- name: install docker engine binaries
  shell: "cp -rf {{ tmp_dir }}/docker/* /usr/bin/"
  failed_when: no
  become: yes

- name: set docker systemd service unit
  template:
    src: docker.service.j2
    dest: /etc/systemd/system/docker.service
  become: yes

- name: create /etc/docker/ directory
  file:
    path: /etc/docker
    state: directory
  become: yes

- name: set /etc/docker/daemon.json
  template:
    src: daemon.json.j2
    dest: /etc/docker/daemon.json
    mode: 0644
  when: docker_registry_insecure | bool
  become: yes

- name: load systemd docker service unit
  service:
    name: docker
    state: restarted
    enabled: yes
    daemon_reload: yes
  become: True

```

#### 2. `docker-user.yml`

```yaml

- name: add docker group
  group:
    name: "docker"
    state: present
  become: yes

- name: add rds_user to docker group
  user:
    name: "{{ rds_user }}"
    groups:
      - "docker"
    append: yes
  become: yes

- name: try docker with rds_user
  command: "sudo docker ps"
  register: docker_msg
  become: yes
  become_user: "{{ rds_user }}"
  failed_when: no

- name: chmod 666 /var/run/docker.sock
  command: chmod 666 /var/run/docker.sock
  when:
    - '"permission denied" in docker_msg.stderr'
  become: True
```

#### 3. `main.yml`
```yaml
---
- include_tasks: install-docker.yml
- include_tasks: docker-user.yml
```

## 三、images

```viz-dot
digraph "docker" {
        rankdir = LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
        ];

        "docker" -> "unarchive docker-images-{{arm64|x64}}.tar.gz/"[label="0755"];
        "docker" -> "alertmanager";
        "docker" -> "greatrds_base";
        "docker" -> "keepalived";
        "docker" -> "mysql";
        "docker" -> "nginx";
        "docker" -> "prometheus";
        "docker" -> "pypiserver";
        "docker" -> "rabbitmq";
        "docker" -> "registry";

}
```

#### 1. default

```yaml
---
docker_images_package_basename: "docker-images-{{ arch_modifier }}"
docker_images_package_suffix: ".tar.gz"
docker_images_package: "{{ docker_images_package_basename }}{{ docker_images_package_suffix }}"
docker_images_tmp_path: "{{ tmp_dir }}/{{ docker_images_package_basename }}"
```

#### 2. load-images.yml

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

#### 3. main.yml

```yaml
---
- include_tasks: load-images.yml

```






## 四、repo

```viz-dot
digraph "repo" {
        rankdir = LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = box;
                fontsize = "12"
        ];

        "repo" -> "dir {{base_dir}}/web_repo/data"[label="0755"];
        "repo" -> "copy rds-ansible/files/repo -> /data/web_repo/data/repo";
        "repo" -> "dir /data/static";
        "repo" -> "copy default.conf /data/web_repo/default.conf";
        "repo" -> "{{base_dir}}/web_repo/data"[label="0777"];
        "repo" -> "run nginx:1.22.0-alpine";
        "run nginx:1.22.0-alpine" -> "1510:80";
        "run nginx:1.22.0-alpine" -> "{{base_dir}}/web_repo/default.conf:/etc/nginx/conf.d/default.conf";
        "run nginx:1.22.0-alpine" -> "{{base_dir}}/static:/opt/static";
        "run nginx:1.22.0-alpine" -> "{{base_dir}}/web_repo/data/repo:/opt/repo";
        "repo" -> "registry";

}
```



```yaml
web_repo_static: "/data/static"
web_repo_static_mount: "/opt/static"
repo_base_dir: "{% if repo_dir is defined %}{{repo_dir}}{% else %}/data{% endif %}"
web_repo_host_path: "{{ repo_base_dir }}/web_repo/data"
```

#### 1. default

```yaml
web_repo_package_basename: "repo"
web_repo_nginx_conf_path: "{{ repo_base_dir }}/web_repo/default.conf"
web_repo_image: "nginx:1.22.0-alpine"
package_install_dir: "yum_repo/centos/7/os/x86_64/package_install"
```


#### 2. web-repo.yml
```yaml
---
# Linux repo server

- name: create web repo data directory
  file:
    path: "{{ web_repo_host_path }}"
    state: directory
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: yes

- debug: msg="复制集群依赖中.....过程较长请耐心等待, 若中断,  请重新执行安装"

- name: copy repo server package
  copy:
    src: "{{ role_path }}/../../files/{{ web_repo_package_basename }}"
    dest: "{{ web_repo_host_path }}"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: yes

- name: create api static data directory
  file:
    path: "{{ web_repo_static }}"
    state: directory
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: yes

- name: create web repo nginx config
  copy:
    src: default.conf
    dest: "{{ web_repo_nginx_conf_path }}"

- name: set web repo path permission to 755
  file:
    path: "{{ web_repo_host_path }}"
    state: directory
    recurse: yes
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0777
  become: true

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

#- name: upload package_install packages (dbscale, greatdb, sgrdb)
#  copy:
#    src: "{{ item }}"
#    dest: "{{ web_repo_host_path }}/{{ package_install_dir }}/"
#  with_fileglob: "../../files/package_install/*"

```

#### 3. main.yml

```yaml
---

- include_tasks: web-repo.yml

```







## 五、pypiserver

```viz-dot
digraph "pypiserver" {
        rankdir = LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = box;
                fontsize = "12"
        ];

        "pypiserver" -> "copy rds-ansible/files/pypiserver-packages/packages -> /data/pypiserver";
        "pypiserver" -> "run pypiserver/pypiserver";
        "run pypiserver/pypiserver" -> "18080:8080";
        "run pypiserver/pypiserver" -> "/data/pypiserver/packages:/data/packages";
}
```


#### 1. default

```yaml
---
# defaults file for repo

pypiserver_basename: "pypiserver-packages"
pypiserver_suffix: ".tar.gz"
pypiserver_host_path: "/data/pypiserver"
```

#### 2. pypiserver.yml
```yaml
---
# pypiserver

- name: copy pypiserver packages to host path
  copy:
    src: "{{ role_path }}/../../files/{{ pypiserver_basename }}/packages"
    dest: "{{ pypiserver_host_path }}"
#    remote_src: yes
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: yes

- name: run pypiserver container
  docker_container:
    name: pypiserver
    image: pypiserver/pypiserver
    restart_policy: always
    ports:
      - "{{ pypiserver_port }}:8080"
    volumes:
      - "{{ pypiserver_host_path }}/packages:/data/packages"
    timeout: "{{ docker_container_timeout }}"
  become: yes
```

#### 3. main.yaml

```yaml
---
- include_tasks: pypiserver.yml

```


## 六、registry

```viz-dot
digraph "pypiserver" {
        rankdir = LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = box;
                fontsize = "12"
        ];

        "pypiserver" -> "copy rds-ansible/files/pypiserver-packages/packages -> /data/pypiserver";
        "pypiserver" -> "run pypiserver/pypiserver";
        "run pypiserver/pypiserver" -> "18080:8080";
        "run pypiserver/pypiserver" -> "/data/pypiserver/packages:/data/packages";
}
```

#### 1. default

```yaml
---
# defaults file for repo
dbinit_images_base_dir: "dbinit-docker-images"
```

#### 2. registry.yaml
```yaml
---
# Docker registry server

- name: check dbinit arm64 images package exists
  local_action:
    module: stat
    path: "{{ role_path }}/../../files/{{ dbinit_images_base_dir }}/arm64"
  register: dbinit_arm64_package_output

- name: check dbinit x64 images package exists
  local_action:
    module: stat
    path: "{{ role_path }}/../../files/{{ dbinit_images_base_dir }}/x64"
  register: dbinit_x64_package_output

- block:
  - name: add Docker registry domain name to /etc/hosts
    lineinfile:
      path: /etc/hosts
      line: "{{ docker_registry_host }}  {{ docker_registry_domain }}"
    become: yes

  - name: run docker registry container
    docker_container:
      name: registry
      image: registry:2
      ports:
        - "{{ docker_registry_port }}:5000"
      restart_policy: always
      timeout: "{{ docker_container_timeout }}"
    become: yes
  when: dbinit_x64_package_output.stat.exists or dbinit_x64_package_output.stat.exists

- block:
  - name: load and tag dbinit x64 docker images
    docker_image:
      name: "{{ item.name }}"
      repository: "{{ docker_registry }}/{{ item.name }}"
      tag: "{{ item.tag }}"
      source: load
      load_path: "{{ role_path }}/../../files/{{ dbinit_images_base_dir }}/x64/{{ item.name }}.tar"
    loop:
      - { name: zookeeper, tag: 3.5.8 }
      - { name: greatdb-cluster-dbscale, tag: latest }
      - { name: greatdb-server, tag: latest }
      - { name: xtrabackup, tag: 2.4 }
    become: true

  - name: push dbinit x64 docker images
    docker_image:
      name: "{{ docker_registry }}/{{ item.name }}"
      tag: "{{ item.tag }}"
      push: yes
    loop:
      - { name: zookeeper, tag: 3.5.8 }
      - { name: greatdb-cluster-dbscale, tag: latest }
      - { name: greatdb-server, tag: latest }
      - { name: xtrabackup, tag: 2.4 }
    become: true

  when: dbinit_x64_package_output.stat.exists

- block:
  - name: load and tag dbinit arm64 docker images
    docker_image:
      name: "{{ item.name }}"
      repository: "{{ docker_registry }}/{{ item.name }}"
      tag: "{{ item.tag }}"
      source: load
      load_path: "{{ role_path }}/../../files/{{ dbinit_images_base_dir }}/arm64/{{ item.name }}.tar"
    loop:
      - { name: zookeeper, tag: 3.5.8 }
      - { name: greatdb-cluster-dbscale, tag: latest }
      - { name: greatdb-server, tag: latest }
      - { name: xtrabackup, tag: 2.4 }
    become: true

  - name: push dbinit arm64 docker images
    docker_image:
      name: "{{ docker_registry }}/{{ item.name }}"
      tag: "{{ item.tag }}"
      push: yes
    loop:
      - { name: zookeeper-arm, tag: 3.5.8 }
      - { name: greatdb-cluster-dbscale-arm, tag: latest }
      - { name: greatdb-server-arm, tag: latest }
    become: true

  when: dbinit_arm64_package_output.stat.exists

```

#### 3. main.yml

```yaml
---
- include_tasks: registry.yml

```



## 七、mysql

`mysql:8.0.27 -> mysql`
```viz-dot
digraph "mysql:8.0.27" {
        rankdir = LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = box;
                fontsize = "12"
        ];

        "mysql" -> "mysql_server_id";
        "mysql" -> "template client.cnf.j2 -> ~/.my.cnf";
        "mysql" -> "dir /data/database/etc";
        "mysql" -> "template my.cnf.j2 -> \n /data/database/etc/my.cnf";
        "mysql" -> "run mysql";
        "run mysql" -> "/data/database/etc/my.cnf:\n /etc/mysql/my.cnf";
        "run mysql" -> "/data/database/data:\n /var/lib/mysql";
        "run mysql" -> "13336:\n 3306";
        "run mysql" -> "MYSQL_ROOT_PASSWORD:\n drACgwoqtM";
        "mysql" -> "user: root@% \n password: drACgwoqtM";
        "mysql" -> "oppo_idx" [label="获取另一节点索引0/1"];
        "mysql" -> "oppo_host" [label="获取另一节点ip，接下来搭建双主"];
        "mysql" -> "user: replicator@%  \n password: E^@p6@pcQRPeH$T4dm \n priv: REPLICATION SLAVE";
        "mysql" -> "resetmaster";
        "mysql" -> "resetslave";
        "mysql" -> "stopslave";
        "mysql" -> "changemaster";
        "mysql" -> "startslave";

}
```

```bash
2024-05-12T19:32:30 [root@node77 /root] $ docker top mysql
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
polkitd             9789                9766                2                   May11               ?                   00:41:29            mysqld --server_id 1 --gtid_mode=ON --enforce_gtid_consistency=ON --default_authentication_plugin=mysql_native_password
2024-05-12T19:32:38 [root@node77 /root] $

```

#### 1. client.cnf.j2

```jinja
[client]
user={{ mysql_user }}
password={{ database_password }}
port={{ mysql_port }}
socket={{ mysql_data_dir }}/mysql.sock

```

#### 2. my.cnf.j2

```jinja
[mysqld]
# default-authentication-plugin=mysql_native_password
skip-host-cache
skip-name-resolve
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
secure-file-priv=NULL
user=mysql

pid-file=/var/run/mysqld/mysqld.pid
lower_case_table_names = 1
max_connections = 1000
[client]
socket=/var/lib/mysql/mysql.sock
```


#### 3. default

```yaml
---
# defaults file for mysql

mysql_image: "mysql:8.0.27"
mysql_container: "mysql"
mysql_dir: "/data/database"
mysql_data_dir: "{{ mysql_dir }}/data"
mysql_etc_dir: "{{ mysql_dir }}/etc"
mysql_replica_user: "replicator"
mysql_replica_password: "E^@p6@pcQRPeH$T4dm"
mysql_password: "{{ database_password }}"
mysql_start_retry: 6
```

#### 4. deploy.yml

```yaml
---
- name: set mysql server id fact
  set_fact:
    mysql_server_id: "{{ groups['database'].index(inventory_hostname) + 1 }}"

- name: generate mysql client config
  template:
    src: "client.cnf.j2"
    dest: "~/.my.cnf"

- name: ensure mysql_etc_dir directory exists
  file:
    path: "{{ mysql_etc_dir }}"
    state: directory
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
  become: True

- name: generate mysql server config
  template:
    src: "my.cnf.j2"
    dest: "{{ mysql_etc_dir }}/my.cnf"

- name: start mysql container
  docker_container:
    name: "{{ mysql_container }}"
    image: "{{ mysql_image }}"
    # master-master replication options
    command: "--server_id {{ mysql_server_id }} --gtid_mode=ON --enforce_gtid_consistency=ON --default_authentication_plugin=mysql_native_password"
    #command: "--server_id {{ mysql_server_id }} --gtid_mode=ON --enforce_gtid_consistency=ON --default_authentication_plugin=mysql_native_password --lower-case-table-names=1"
    volumes:
      - "{{ mysql_etc_dir }}/my.cnf:/etc/mysql/my.cnf"
      - "{{ mysql_data_dir }}:/var/lib/mysql"
    ports:
      - "{{ mysql_port }}:3306"
    env:
      MYSQL_ROOT_PASSWORD: "{{ database_password }}"
    privileged: yes
    restart_policy: unless-stopped
    recreate: yes
    timeout: "{{ docker_container_timeout }}"
  notify: wait mysql start
  become: yes

- meta: flush_handlers

- name: allow root remote acccess
  mysql_user:
    name: "root"
    host: "%"
    password: "{{ database_password }}"
    priv: "*.*:ALL"

```

#### 5. replica.yml

```yaml
---

# setup MySQL container master-master replication

- name: set opposite host index in ansible inventory group
  set_fact:
    oppo_idx: "{{ 1 - groups['database'].index(inventory_hostname) }}"

- name: set opposite hostname
  set_fact:
    oppo_host: "{{ hostvars[groups['database'][oppo_idx | int]].ansible_host }}"

- name: create replica user on mysql master
  mysql_user:
    name: "{{ mysql_replica_user }}"
    host: "%"
    password: "{{ mysql_replica_password }}"
    priv: "*.*:REPLICATION SLAVE"

- name: reset master
  mysql_replication:
    mode: resetmaster

- name: reset slave
  mysql_replication:
    mode: resetslave

- name: stop mysql slave
  mysql_replication:
    mode: stopslave

- name: set mysql replication master
  mysql_replication:
    mode: changemaster
    master_host: "{{ oppo_host }}"
    master_port: "{{ mysql_port }}"
    master_auto_position: yes
    master_user: "{{ mysql_replica_user }}"
    master_password: "{{ mysql_replica_password }}"

- name: start mysql slave
  mysql_replication:
    mode: startslave

```


#### 6. main.yml

```yaml
---
- include_tasks: deploy.yml

- include_tasks: replica.yml
  when: deploy_mode == "dual"

```

## 八、prometheus


- `prom/prometheus:latest -> prometheus`
- `prom/alertmanager:latest -> alertmanager`

```viz-dot
digraph "prometheus" {
        rankdir = LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = box;
                fontsize = "12"
        ];

        "prometheus" -> "dir: /data/greatrds/etc/monitor";
        "prometheus" -> "template: prometheus.yml.j2 \n /data/greatrds/etc/monitor/prometheus.yml";
        "prometheus" -> "dir /etc/greatrds/monitor/rules/";
        "prometheus" -> "dir /etc/greatrds/monitor/scraps/";
        "prometheus" -> "dir /data/greatrds/data/monitor/prometheus_data";
        "prometheus" -> "template: alertmanager.yml.j2 \n /data/greatrds/etc/monitor/alertmanager.yml";
        "prometheus" -> "run prometheus";
        "run prometheus" -> "/data/greatrds/etc/monitor/prometheus.yml:\n /etc/prometheus/prometheus.yml";
        "run prometheus" -> "/data/greatrds/data/monitor/prometheus_data:\n /prometheus";
        "run prometheus" -> "/etc/greatrds/monitor/rules/:\n /etc/greatrds/monitor/rules/";
        "run prometheus" -> "/etc/greatrds/monitor/scraps/:\n /etc/greatrds/monitor/scraps/";
        "run prometheus" -> "9092:9090";
        "run prometheus" -> "command:\n config.file=/etc/prometheus/prometheus.yml \n storage.tsdb.path=/prometheus \n web.console.libraries=/usr/share/prometheus/console_libraries \n web.console.templates=/usr/share/prometheus/consoles \n web.enable-lifecycle";
        "prometheus" -> "run alertmanager";
        "run alertmanager" -> "9093:9093";
        "run alertmanager" -> "9094:9094" [label="双节点"];
        "run alertmanager" -> "/data/greatrds/etc/monitor/alertmanager.yml:\n /etc/alertmanager/alertmanager.yml";
        "run alertmanager" -> "command:\n config.file=/etc/alertmanager/alertmanager.yml \n storage.path=/alertmanager \n cluster.peer={{ cluster_peer_host }}:9094"[label="双节点"];

}
```




#### 1. default
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

```

#### 2. prometheus.yml.j2

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
  - job_name: 'rds'
    static_configs:
{% for node in groups['exporter'] %}
      - targets: ['{{ hostvars[node].ansible_host }}:{{ exporter_port }}']
        labels:
          __metrics_path__: '/rds_metrics'
{% endfor %}
    honor_labels: true
```

#### 3. alertmanager.yml.j2
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

#### 4. config.yml

```yaml
---

- name: ensure node_config_directory directory exists
  file:
    path: "{{ node_config_directory }}"
    state: directory
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: True

- name: config prometheus
  template:
    src: "prometheus.yml.j2"
    dest: "{{ prometheus_conf_file }}"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0644
  become: True

- name: create prometheus rule dir
  file:
    dest: "{{ rule_path }}"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
    state: directory
  become: True

- name: create prometheus scrap dir
  file:
    dest: "{{ scrap_path }}"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
    state: directory
  become: True

- name: create prometheus_data_path dir
  file:
    path: "{{ prometheus_data_path }}"
    state: directory
    owner: "65534"
    group: "65534"
    mode: 0755
  become: True

- name: config alertmanager
  template:
    src: "alertmanager.yml.j2"
    dest: "{{ alertmanager_conf_file_host }}"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0644
  become: True
```

#### 5. deploy-prometheus.yml

```yaml
---

- name: start prometheus container
  docker_container:
    name: "prometheus"
    image: "{{ prometheus_image }}"
    ports:
      - "{{ prometheus_port }}:9090"
    volumes:
      - "{{ prometheus_conf_file }}:/etc/prometheus/prometheus.yml"
      - "{{ prometheus_data_path }}:/prometheus"
      - "{{ rule_path_host }}:{{ rule_path }}"
      - "{{ scrap_path_host }}:{{ scrap_path }}"
    restart_policy: unless-stopped
    recreate: yes
    command: --config.file=/etc/prometheus/prometheus.yml --storage.tsdb.path=/prometheus --web.console.libraries=/usr/share/prometheus/console_libraries --web.console.templates=/usr/share/prometheus/consoles --web.enable-lifecycle
    timeout: "{{ docker_container_timeout }}"
  become: yes

- name: start alertmanager container
  docker_container:
    name: "alertmanager"
    image: "{{ alertmanager_image }}"
    ports:
      - "{{ alertmanager_port }}:9093"
    volumes:
      - "{{ alertmanager_conf_file_host }}:/etc/alertmanager/alertmanager.yml"
    restart_policy: unless-stopped
    recreate: yes
    timeout: "{{ docker_container_timeout }}"
  become: yes
  when: monitor_deploy_mode == '1'

- name: set_fact cluster_peer_host
  set_fact:
    cluster_peer_host: "{{ hostvars[groups['monitor'][1]].ansible_host if hostvars[groups['monitor'][0]].ansible_host == ansible_host else hostvars[groups['monitor'][0]].ansible_host }}"
  when: monitor_deploy_mode != '1'

- name: start alertmanager container
  docker_container:
    name: "alertmanager"
    image: "{{ alertmanager_image }}"
    ports:
      - "{{ alertmanager_port }}:9093"
      - "{{ alertmanager_cluster_port }}:9094"
    volumes:
      - "{{ alertmanager_conf_file_host }}:/etc/alertmanager/alertmanager.yml"
    restart_policy: unless-stopped
    recreate: yes
    command: "--config.file=/etc/alertmanager/alertmanager.yml --storage.path=/alertmanager --cluster.peer={{ cluster_peer_host }}:{{ alertmanager_cluster_port }}"
    timeout: "{{ docker_container_timeout }}"
  become: yes
  when: monitor_deploy_mode != '1'


```

#### 6. main.yml

```yaml
---
- include_tasks: config.yml
- include_tasks: deploy-prometheus.yml

```


## 九、rabbitmq

`rabbitmq:3.9.7-management -> rabbitmq`
```viz-dot
digraph "rabbitmq" {
        rankdir = LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = box;
                fontsize = "12"
        ];

        "rabbitmq" -> "dir: /data/rabbitmq/data" [label="0777"];
        "rabbitmq" -> "dir: /data/rabbitmq/etc" [label="0777"];
        "rabbitmq" -> "dir: /data/rabbitmq/log" [label="0777"];
        "rabbitmq" -> "template: erlang_cookie.j2 \n /data/rabbitmq/data/.erlang.cookie";
        "rabbitmq" -> "template: rabbitmq.conf.j2 \n /data/rabbitmq/etc/rabbitmq.conf";
        "rabbitmq" -> "template: enabled_plugins.j2 \n /data/rabbitmq/etc/enabled_plugins";

        "rabbitmq" -> "run rabbitmq";
        "run rabbitmq" -> "/data/rabbitmq/data:\n /var/lib/rabbitmq";
        "run rabbitmq" -> "/data/rabbitmq/log:\n /var/log/rabbitmq/";
        "run rabbitmq" -> "/data/rabbitmq/etc:\n /etc/rabbitmq";
        "run rabbitmq" -> "5672:5672";
        "run rabbitmq" -> "15672:15672";
        "run rabbitmq" -> "4369:4369";
        "run rabbitmq" -> "25672:25672";
        "run rabbitmq" -> "etc_hosts:{{etc_hosts}}";
        "rabbitmq" -> "command: sudo docker exec rabbitmq rabbitmqctl await_startup";
        "rabbitmq" -> "command: sudo docker exec rabbitmq rabbitmqctl stop_app" [label="双节点"];
        "rabbitmq" -> "command: sudo docker exec rabbitmq rabbitmqctl join_cluster rabbit@{{ node0 }}" [label="双节点"];
        "rabbitmq" -> "command: sudo docker exec rabbitmq rabbitmqctl start_app" [label="双节点"];
        "rabbitmq" -> "command: sudo docker exec rabbitmq rabbitmqctl list_policies" [label="双节点"];
        "rabbitmq" -> "shell: sudo docker exec rabbitmq rabbitmqctl set_policy ha-all \".*\" '{\"ha-mode\":\"all\",\"ha-sync-mode\":\"automatic\"}'" [label="双节点"];

}
```




#### 1. default

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
rabbitmq_cluster_cookie: mrqTsFUSsqFsLkregI4fYOifeuvlter0Mp6ANELB
rabbitmq_start_retry: 6

```

#### 2. erlang_cookie.j2

```yaml
{{ rabbitmq_erlang_cookie }}
```

#### 3. rabbitmq.conf.j2
- om_rpc_user: admin
- rabbitmq_password: 123456

```yaml
default_vhost = /
default_user = {{ om_rpc_user }}
default_pass = {{ rabbitmq_password }}


loopback_users.guest = false
listeners.tcp.default = 5672
management.tcp.port = 15672
```

#### 4. enabled_plugins.j2

```yaml
[rabbitmq_management,rabbitmq_prometheus].
```


#### 5. deploy.yml

```yaml
---
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

- name: updating rabbitmq erlang cookie
  template:
    src: erlang_cookie.j2
    dest: "{{ rabbitmq_data_dir }}/.erlang.cookie"
    mode: 0400

- name: create rabbitmq.conf
  template:
    src: rabbitmq.conf.j2
    dest: "{{ rabbitmq_etc_dir }}/rabbitmq.conf"
    mode: 0666

- name: create rabbitmq enabled_plugins
  template:
    src: enabled_plugins.j2
    dest: "{{ rabbitmq_etc_dir }}/enabled_plugins"
    mode: 0666

# RabbitMQ communicates via its peer's hostname
# peering by IP address is not supported by RabbitMQ
# etc_hosts param of docker_container module is dict of host-to-IP mappings, opposite to /etc/hosts
- name: set etc_hosts
  set_fact:
    etc_hosts: "{{ etc_hosts | default({}) | combine({ item: hostvars[item].ansible_host }) }}"
  loop: "{{ groups['rabbitmq'] }}"

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

- name: wait 30 seconds for RabbitMQ to startup
  command: "sudo docker exec {{ rabbitmq_container }} rabbitmqctl await_startup"
  register: command_result
  retries: "{{ rabbitmq_start_retry }}"
  delay: 5
  until: "'Error' not in command_result.stderr"

```

#### 6. join_cluster.yml

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

#### 7. enable_ha_queues.yml

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


## 十、nginx

`nginx:1.22.0-alpine -> nginx`
```viz-dot
digraph "nginx" {
        rankdir = LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = box;
                fontsize = "12"
        ];

        "nginx" -> "dir: /data/nginx/etc" [label="0755"];
        "nginx" -> "dir: /data/greatrds/web" [label="0755"];
        "nginx" -> "unarchive: rds-ansible/files/release/web/das-a8d50526.tar.gz \n /data/greatrds/web";
        "nginx" -> "unarchive: rds-ansible/files/release/web/rds-2a131ec4.tar.gz \n /data/greatrds/web";
        "nginx" -> "template: http_default.conf.j2 \n /data/nginx/etc";
        "nginx" -> "dir: /data/nginx/etc/ssl" [label="0755"];
        "nginx" -> "copy: greatrds.key \n /data/nginx/etc/ssl";
        "nginx" -> "copy: greatrds.crt \n /data/nginx/etc/ssl" [label="https"];
        "nginx" -> "template: config.js.j2 \n /data/greatrds/web/db/config.js";
        "nginx" -> "template: config.js.j2 \n /data/greatrds/web/dist/config.js";

        "nginx" -> "run nginx";
        "run nginx" -> "/data/nginx/etc/conf.d/default.conf: \n /etc/nginx/conf.d/default.conf";
        "run nginx" -> "/data/nginx/log:\n /var/log/nginx";
        "run nginx" -> "/data/greatrds/web:\n /usr/share/nginx/web";
        "run nginx" -> "/data/nginx/etc/ssl:\n /etc/nginx/ssl";

}
```


#### 1. default

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

#### 2. http_default.conf.j2

```yaml
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

#### 3. https_default.conf.j2

```yaml
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

#### 4. config.js.j2

```yaml
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

#### 5. deploy.yml

```yaml
---

- name: create nginx conf path
  file:
    path: "{{ nginx_conf_dir }}/conf.d"
    state: directory
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: true

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

- name: find new static file
  find:
    paths:
      - "{{ role_path }}/../../files/{{ package_path_name }}/web/"
    patterns: "*.tar.gz"
  delegate_to: localhost
  register: new_static

- name: transfer and extract web static files
  unarchive:
    src: "{{ item.path }}"
    dest: "{{ web_host_path }}"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  loop: "{{ new_static.files }}"
  become: true

- name: copy http_default.conf template
  template:
    src: http_default.conf.j2
    dest: "{{ nginx_conf_dir }}/conf.d/default.conf"
  when: enable_https != "yes"

- block:
  - name: copy https_default.conf template
    template:
      src: https_default.conf.j2
      dest: "{{ nginx_conf_dir }}/conf.d/default.conf"
      owner: "{{ rds_user }}"
      group: "{{ rds_group }}"
      mode: 0755

  - name: create nginx ssl directory
    file:
      path: "{{ nginx_ssl_dir_host }}"
      state: directory
      owner: "{{ rds_user }}"
      group: "{{ rds_group }}"
      mode: 0755
    become: true

  - name: copy ssl private key
    copy:
      src: "{{ nginx_ssl_key }}"
      dest: "{{ nginx_ssl_dir_host }}"
      owner: "{{ rds_user }}"
      group: "{{ rds_group }}"
      mode: 0755
    become: true

  - name: copy ssl certificate
    copy:
      src: "{{ nginx_ssl_crt }}"
      dest: "{{ nginx_ssl_dir_host }}"
      owner: "{{ rds_user }}"
      group: "{{ rds_group }}"
      mode: 0755
    become: true
  when: enable_https == "yes"

- name: set api config for db frontend
  template:
    src: config.js.j2
    dest: "{{ web_host_path }}/db/config.js"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: true

- name: set api config for dist frontend
  template:
    src: config.js.j2
    dest: "{{ web_host_path }}/dist/config.js"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: true

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

#### 6. main.yml

```yaml
- include_tasks: deploy.yml
```




#### 8. greatrds.key

```bash
-----BEGIN RSA PRIVATE KEY-----
MIIEowIBAAKCAQEAs82XX/WpUDhWuhpS0okczWsU2BEFpRxTjgraU+aZPZdDJAAa
6ltY+oraNDXMSYMnYa3To1Fl8Pn+q+mEXjDzhDw1IZtVUn0vRxkS5D28gkI3+1Z9
XqzAegHCJ5ptP84hNfhZSZNKkyq5GRE/a/wlT1YGB1M43KY3Mno62kpXN0EbZwYU
kb5ncGzXP+2haLSeGnqIhkfuwIUIfEFbwquzIVwhl6jJrUznBRjNpiq31+T6LF6N
ZcqBSBbxYa00LSCQHYEetvBB6pkEEQa4+bAlQ2nmCTTcq8O4TCxIx55qvdaGj7Qq
xR9+wX1uNfXWm0PcuZ7IzWR2XRaEcvRLt/WW3wIDAQABAoIBAQCKcb0i2Cc8a4Tr
2yT2kzioOtFb5Q9xqLP1ZQnjHGXV9MhvydzvotKKZ6vIE7xuxhvmrE+PKzfRKs0b
VbzkQdDfevR2IgK6d0WV+wp+z8J8kbxFPL1fzqiNyRjUIaYJQRB1W/p0GoOLeZP4
Zz1sSkP4KIaP0VaxHxLVPSRfvGoXUjD7cu0C6VQDm9u0jrykU0eEagfSRwCd5j8J
aa9/w3UTKBPzWnprtw5zSj/+CYfqzIXEq7fuO0Namuky6sQWqzhlC47mP3ZGMxV9
SEPK/wzruAy30qrxoLYP54SOHRb9nyVrgrF4rx1Rr/7K6m/FqauIqdXpArYtfUp8
6IN+7LexAoGBAOgRYOSOounUGdK25KtkO1flxk1RT+KJuR+uzd/fwLmVHrBIABrf
yuIvjoem8kU5lyhoJ9pN3B4DxZ7nb1bFq2gKv+4DYXdw+bHEd7hs3CtJ+mC4BalS
HCnEgiVE2bF4ikfz90/D1E9M3hEoNc02P8/KbqSKZyjQIJu/datH3PAVAoGBAMZY
amhyRzItvbBPJf5U/qhaIsYBiydUlN8S8wHrz6/xnZAEP/RS6dWfNBMU5TKvaIgW
p5A7MYmpM1IaXBsQ1R1Gu+9MtqxVEZR4lZyvMGjB4Uc+Nsk9Pyx5NVUwT8rxHRwl
+tLkJpPjotdaGsVncZAk2Dizuz6s2loVn8MmqrQjAoGAQWKMGzl4+0rQT8dKHGQs
ODK8MZKkYF6PczbMOLSRV3iFYgOXS/sb7aiajuvhvNFiHndiKybeyvIjHwd0Hk2p
YrIMq6H2NzH0mDqTxLmfOu8oBVIz/iXtW3NLe/avI0qkI2vAlY/GZOVyeAENT5lm
V0Rg8ikwsZYaaeeC3tRrkpUCgYAdz4gerp5YwlsyNpMlSK0FrJZxYCYEAUhmxrhT
X5fVf+EIljrug8+1VbpD6+4O0iJJ4e3JVbw6Tn3yJxgU0VDWl50DeGo0OIzt3bnO
1N3XwNwVExq04Emwa39CkIJNpDOV2Xd7XuMVcy80YsyHQubknUUKMKlB4ETpKTrb
3+HkNQKBgBKeJ9Q0dUWcZQDYoe9W/K1vPMgAZN18MR6KS3mX4kAuFCjEpY/VHU8E
rpsZ8/ikFCZZzbZbQwgW43SGdSJEYNEXKNYT1vdz1mb2HX/1jYMBa2LV5G7pxwn4
jgWrjoDaGztM6CqbuDTFmoB5W4DyP+Ean9Hs8xD8oz8iDadw449H
-----END RSA PRIVATE KEY-----

```

#### 9. greatrds.crt

```bash
-----BEGIN CERTIFICATE-----
MIIDvzCCAqcCFFcER6u5KPKZG2tKArmNDxWYFsuDMA0GCSqGSIb3DQEBCwUAMIGb
MQswCQYDVQQGEwJDTjEQMA4GA1UECAwHQmVpamluZzEQMA4GA1UEBwwHQmVpamlu
ZzEQMA4GA1UECgwHR3JlYXREQjERMA8GA1UECwwIR3JlYXRSRFMxFTATBgNVBAMM
DGdyZWF0cmRzLmNvbTEsMCoGCSqGSIb3DQEJARYdc2lxaW5nLnl1QGdyZWF0b3Bl
bnNvdXJjZS5jb20wHhcNMjEwMTA2MDg1MzAzWhcNMjIwMTA2MDg1MzAzWjCBmzEL
MAkGA1UEBhMCQ04xEDAOBgNVBAgMB0JlaWppbmcxEDAOBgNVBAcMB0JlaWppbmcx
EDAOBgNVBAoMB0dyZWF0REIxETAPBgNVBAsMCEdyZWF0UkRTMRUwEwYDVQQDDAxn
cmVhdHJkcy5jb20xLDAqBgkqhkiG9w0BCQEWHXNpcWluZy55dUBncmVhdG9wZW5z
b3VyY2UuY29tMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAs82XX/Wp
UDhWuhpS0okczWsU2BEFpRxTjgraU+aZPZdDJAAa6ltY+oraNDXMSYMnYa3To1Fl
8Pn+q+mEXjDzhDw1IZtVUn0vRxkS5D28gkI3+1Z9XqzAegHCJ5ptP84hNfhZSZNK
kyq5GRE/a/wlT1YGB1M43KY3Mno62kpXN0EbZwYUkb5ncGzXP+2haLSeGnqIhkfu
wIUIfEFbwquzIVwhl6jJrUznBRjNpiq31+T6LF6NZcqBSBbxYa00LSCQHYEetvBB
6pkEEQa4+bAlQ2nmCTTcq8O4TCxIx55qvdaGj7QqxR9+wX1uNfXWm0PcuZ7IzWR2
XRaEcvRLt/WW3wIDAQABMA0GCSqGSIb3DQEBCwUAA4IBAQBQPELo1BExMOkzZtBN
C0vTKbuaaG7bGGQwgemWwrTt91SKhIy0JuZmKpoSM00SkjPoS/SBDSHBu9VPITQa
XbFJwqkbl6lK1MKkFWARxeyGnshIsfh5bisiOWmI335ay+uHjgmqNNeBqb6Of2D+
9IVcptFdqyhonfJjxw8zlVOHu18A/VH3kVOIV15tfbaCpGLRj7mSxGuybJEg7wXW
snAmeKhWl/fuKmBzfJlECcYVByhIhubmdE6LqLKM9M2tjKQiwc+yYxsm7vZLPd+h
bQyXUptJLxBI3rbWZAyH7mdUsHed7N1AbDgsEF0cK/LUvyLf/FimH/bpViqeO9jK
SVFJ
-----END CERTIFICATE-----

```

#### 10. greatrds.csr

```bash
-----BEGIN CERTIFICATE REQUEST-----
MIIC4TCCAckCAQAwgZsxCzAJBgNVBAYTAkNOMRAwDgYDVQQIDAdCZWlqaW5nMRAw
DgYDVQQHDAdCZWlqaW5nMRAwDgYDVQQKDAdHcmVhdERCMREwDwYDVQQLDAhHcmVh
dFJEUzEVMBMGA1UEAwwMZ3JlYXRyZHMuY29tMSwwKgYJKoZIhvcNAQkBFh1zaXFp
bmcueXVAZ3JlYXRvcGVuc291cmNlLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEP
ADCCAQoCggEBALPNl1/1qVA4VroaUtKJHM1rFNgRBaUcU44K2lPmmT2XQyQAGupb
WPqK2jQ1zEmDJ2Gt06NRZfD5/qvphF4w84Q8NSGbVVJ9L0cZEuQ9vIJCN/tWfV6s
wHoBwieabT/OITX4WUmTSpMquRkRP2v8JU9WBgdTONymNzJ6OtpKVzdBG2cGFJG+
Z3Bs1z/toWi0nhp6iIZH7sCFCHxBW8KrsyFcIZeoya1M5wUYzaYqt9fk+ixejWXK
gUgW8WGtNC0gkB2BHrbwQeqZBBEGuPmwJUNp5gk03KvDuEwsSMeear3Who+0KsUf
fsF9bjX11ptD3LmeyM1kdl0WhHL0S7f1lt8CAwEAAaAAMA0GCSqGSIb3DQEBCwUA
A4IBAQCw9XHXCunQqRrqTk/V9qZNDRK/OL+MhZ2zFNP4A5Q0xQyWqESDPQFuZdVk
SiBJI3cfbuZDO4vAQSnjyaoRcoYiDXlN2hAdr+BGOjWUUlOtQe/AbVurLuE5b1rV
gf89zPztPkcx6GoAlgw621eqRIiLMZ/woHw+phZs/rtn2qTss2K2+O0mzC5SIsUe
VMABsyjXofg9Zjrog8x9RrdfUIbFvNngLHltJplSdX7OxNoTssGH+jQTD2VWoIm2
vaPx8oZgTfOLu9UcfRxeETVDH8aPF4cLmfizEE7KAbpCFlSq82su8lMOtLs2MAv+
2FwDzDXp0wRcKLMdAIZ18swp9myD
-----END CERTIFICATE REQUEST-----

```






## 十一、upload

`nginx:1.22.0-alpine -> nginx`
```viz-dot
digraph "nginx" {
        rankdir = LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = box;
                fontsize = "12"
        ];

        "nginx" -> "dir: /data/nginx/etc" [label="0755"];
        "nginx" -> "dir: /data/greatrds/web" [label="0755"];
        "nginx" -> "unarchive: rds-ansible/files/release/web/das-a8d50526.tar.gz \n /data/greatrds/web";
        "nginx" -> "unarchive: rds-ansible/files/release/web/rds-2a131ec4.tar.gz \n /data/greatrds/web";
        "nginx" -> "template: http_default.conf.j2 \n /data/nginx/etc";
        "nginx" -> "dir: /data/nginx/etc/ssl" [label="0755"];
        "nginx" -> "copy: greatrds.key \n /data/nginx/etc/ssl";
        "nginx" -> "copy: greatrds.crt \n /data/nginx/etc/ssl" [label="https"];
        "nginx" -> "template: config.js.j2 \n /data/greatrds/web/db/config.js";
        "nginx" -> "template: config.js.j2 \n /data/greatrds/web/dist/config.js";

        "nginx" -> "run nginx";
        "run nginx" -> "/data/nginx/etc/conf.d/default.conf: \n /etc/nginx/conf.d/default.conf";
        "run nginx" -> "/data/nginx/log:\n /var/log/nginx";
        "run nginx" -> "/data/greatrds/web:\n /usr/share/nginx/web";
        "run nginx" -> "/data/nginx/etc/ssl:\n /etc/nginx/ssl";

}
```


#### 1. default

```yaml
---
docker_images_package_basename: "docker-images-{{ arch_modifier }}"
docker_images_package_suffix: ".tar.gz"
docker_images_package: "{{ docker_images_package_basename }}{{ docker_images_package_suffix }}"
docker_images_tmp_path: "{{ tmp_dir }}/{{ docker_images_package_basename }}"
pypiserver_host_path: "{{ base_dir }}/pypiserver"
package_path_name: "release"
```

#### 2. upload.yml

```yaml
---

- name: find existing wheels on pypiserver
  find:
    paths:
      - "{{ pypiserver_host_path }}/packages/"
    patterns: greatrds*.whl
  delegate_to: "{{ groups['pypiserver'][0] }}"
  register: old_wheels
  become: true

- name: remove old wheels
  file:
    path: "{{ item.path }}"
    state: absent
  loop: "{{ old_wheels.files }}"
  delegate_to: "{{ groups['pypiserver'][0] }}"
  become: true

- name: find new wheel
  find:
    paths:
      - "{{ role_path }}/../../files/{{ package_path_name }}/wheels/"
    patterns: "*.whl"
  delegate_to: localhost
  register: new_wheel
  become: true

- name: Get debug info
  debug: var=new_wheel verbosity=0

- name: upload new wheel
  copy:
    src: "{{ item.path }}"
    dest: "{{ pypiserver_host_path }}/packages/"
  delegate_to: "{{ groups['pypiserver'][0] }}"
  loop: "{{ new_wheel.files }}"
  become: true

- name: restart pypyserver
  command: docker restart pypiserver
  become: yes

```

#### 3. main.yml

```yaml
---
#- include_tasks: clean-env.yml
#  when: clean_mode == env
#
#- include_tasks: clean-all.yml
#  when: clean_mode == all
- include_tasks: upload.yml
```





## 十二、api

#### default

```yaml
---
logging_debug: "false"
project: "greatrds"
component: "api"
rds_dir: "{{ base_dir }}/{{ project }}"
node_config_directory: "{{ rds_dir }}/etc/{{ component }}" # rds 的配置文件路径
rds_log_dir: "{{ rds_dir }}/log"
work_path: "{{ base_dir }}/{{ project }}"
web_slow_log_database_name: "web_slow_log"
api_docker_dir: "{{ tmp_dir }}/docker-build/api" # directory containing the context and Dockerfile for building greatrds image
pypyserver_packages_path: "{{ base_dir }}/pypiserver/packages/"
api_container: "greatrds-api"
api_data_container: "greatrds-db-manage"
api_init_container: "greatrds-init"
enable_https: "yes"
securitydb: "no"
api_init_command: "{{ 'greatrds-init sql' if securitydb == 'no' else 'greatrds-init sql -r securitydb' }}"
greatrds_api_retry: 12
```


#### version.yml

```yaml
---

- name: get the greatrds wheel version
  shell: "ls -1 {{ pypyserver_packages_path }} | grep greatrds-.*whl | awk -F '-' '{print $2}'"
  delegate_to: "{{ groups['pypiserver'][0] }}"
  register: version_result
  become: true

- name: set greatrds version
  set_fact:
    greatrds_version: "{{ version_result.stdout }}"

- name: Get greatrds_version info
  debug: var=version_result verbosity=0
```
#### build.yml

```yaml
# ---

- name: create docker-build directory
  file:
    path: "{{ tmp_dir }}/docker-build"
    state: directory
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: true

- name: create api_docker_dir
  file:
    path: "{{ api_docker_dir }}"
    state: directory
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: true

- name: copy api-paste.ini for greatrds
  copy:
    src: api-paste.ini
    dest: "{{ api_docker_dir }}/api-paste.ini"

- name: copy docker-entrypoint for greatrds
  copy:
    src: docker-entrypoint.sh
    dest: "{{ api_docker_dir }}/docker-entrypoint.sh"

- name: template Dockerfile for greatrds
  template:
    src: Dockerfile.api.j2
    dest: "{{ api_docker_dir }}/Dockerfile"

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

#### config.yml

```yaml
---

- name: create greatrds api config directory
  file:
    path: "{{ node_config_directory }}"
    state: directory
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: true

- name: create etc directory
  file:
    path: "{{ etc_dir }}"
    state: directory
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: true

- name: Backup greatrds.conf
  copy:
    src: "{{ oslo_config_path_host }}"
    dest: "{{ etc_dir }}/greatrds.conf.{{ ansible_date_time.date }}"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    remote_src: yes
    mode: 0755
  become: true
  ignore_errors: yes

- name: generate greatrds.conf
  template:
    src: "greatrds.conf.j2"
    dest: "{{ oslo_config_path_host }}"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: true

- name: Create web_slow_log database
  mysql_db:
    name: "{{ web_slow_log_database_name }}"
    login_host: "127.0.0.1"
    login_port: "{{ api_database_port }}"
    state: present
    encoding: "utf8mb4"
  delegate_to: "{{ groups['database'][0] }}"
  run_once: yes

- name: Create rds api database
  mysql_db:
    name: "{{ api_database_name }}"
    login_host: "127.0.0.1"
    login_port: "{{ api_database_port }}"
    state: present
    encoding: "utf8mb4"
  delegate_to: "{{ groups['database'][0] }}"
  run_once: yes

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

- name: wait for greatrds-init sql
  command: "sudo docker ps"
  register: docker_ps_result
  retries: "{{ greatrds_api_retry }}"
  delay: 10
  until: "api_init_container not in docker_ps_result.stdout"
```

#### deploy.yml
```yaml
---

- name: remove existing greatrds container
  docker_container:
    name: "{{ api_container }}"
    state: absent
    timeout: "{{ docker_container_timeout }}"
  become: yes

- name: start greatrds api container
  docker_container:
    name: "{{ api_container }}"
    image: "greatrds:{{ greatrds_version }}"
    ports:
      - "{{ api_port }}:{{ api_port }}"
    volumes:
      - "{{ oslo_config_path_host }}:{{ oslo_config_path_mount }}"
      - "{{ rds_log_dir }}:/var/log/rds/"
      - "{{ web_repo_host_path }}:/gtms/"
    restart_policy: unless-stopped
    recreate: yes
    timeout: "{{ docker_container_timeout }}"
  become: yes

```

#### api-paste.ini

```yaml
[pipeline:main]
pipeline = cors healthcheck http_proxy_to_wsgi request_id authtoken daskey clog api_v1

[app:api_v1]
paste.app_factory = greatrds.api.app:app_factory

[filter:authtoken]
acl_public_routes = /v1/rds/monitor/alert/new, /v1/rds, /v1/rds/api, /v1/rds/docs, /v1/rds/docs/.*, /, /v1/rds/das,/v1/rds/das.*, /das, /das/.*, /v1/rds/monitor/metrics, /v1/rds/build, /v1/rds/hosts/add_host_record, /v1/rds/overview/check_info, /v1/rds/overview/version/.*
paste.filter_factory = greatrds.api.middleware.auth_token:AuthTokenMiddleware.factory

[filter:makeauthtoken]
acl_public_routes = /v1/rds, /v1/rds/api, /v1/rds/docs, /v1/rds/docs/.*, /, /v1/rds/das, /v1/rds/das/.*
paste.filter_factory = greatrds.api.middleware.make_auth_token:AuthTokenMiddleware.factory

[filter:clog]
paste.filter_factory = greatrds.api.middleware.clog:ClogMiddleware.factory

[filter:daskey]
acl_public_routes = /v1/rds/das,/v1/rds/das/*, /das, /das/.*
paste.filter_factory = greatrds.api.middleware.das_key:DasKeyMiddleware.factory

[filter:request_id]
paste.filter_factory = oslo_middleware:RequestId.factory

[filter:debug]
paste.filter_factory = oslo_middleware:Debug.factory

[filter:cors]
paste.filter_factory =  oslo_middleware.cors:filter_factory
oslo_config_project = greatrds
allowed_origin= *

[filter:healthcheck]
paste.filter_factory = oslo_middleware:Healthcheck.factory
backends = disable_by_file
disable_by_file_path = /etc/greatrds/healthcheck_disable
detailed = False

[filter:http_proxy_to_wsgi]
paste.filter_factory = oslo_middleware.http_proxy_to_wsgi:HTTPProxyToWSGI.factory
oslo_config_project = greatrds

[server:main]
use = egg:gunicorn#main
host = 0.0.0.0
port = 9010
proc_name = greatrds
workers = 8
accesslog = -
loglevel = info


```

#### docker-entrypoint.sh

```yaml
#!/bin/sh
mkdir -p /var/log/rds

if [ "X$AUTH_URL" != "X" ]; then
    sed -i "s/auth_url = .*/^auth_url = $AUTH_URL/g" /etc/greatrds/greatrds.conf
fi

if [ "$1" = 'run_api' ]; then
    echo "[RUN]: Launching API Service [gunicorn --paste /usr/local/etc/greatrds/api-paste.ini]"
    touch /var/log/rds/api.log
    gunicorn --paste /usr/local/etc/greatrds/api-paste.ini --access-logfile /var/log/rds/api.log --error-logfile /var/log/rds/api.log --capture-output

elif [ "$1" = 'run_conductor' ]; then
    echo "[RUN]: Launching conductor Service [greatrds-conductore]"
    touch /var/log/rds/conductor.log
    greatrds-conductor --log-file=/var/log/rds/conductor.log  --config-file=/etc/greatrds/greatrds.conf

elif [ "$1" = 'run_monitor' ]; then
    echo "[RUN]: Launching monitor Service [greatrds-monitor]"
    touch /var/log/rds/monitor.log
    greatrds-monitor --log-file=/var/log/rds/monitor.log  --config-file=/etc/greatrds/greatrds.conf

else
    echo "[RUN]: Builtin command not provided [run]"
    echo "[RUN]: $*"
    exec "$@"
fi

```

#### Dockerfile.api.j2

```yaml
# Dockerfile for GreatRDS API

FROM greatrds_base

RUN pip install -q greatrds -i http://{{ pypiserver_host }}:{{ pypiserver_port }}/simple  --trusted-host {{ pypiserver_host }}

ENV GREATRDS_LOCALEDIR  /usr/local/lib/python3.6/site-packages/greatrds/locale

COPY api-paste.ini /usr/local/etc/greatrds/api-paste.ini
ADD docker-entrypoint.sh .
RUN chmod +x docker-entrypoint.sh

ENTRYPOINT ["./docker-entrypoint.sh"]
CMD ["run_api"]

```


## 十三、conductor

`nginx:1.22.0-alpine -> greatrds-conductor`
```viz-dot
digraph "conductor" {
        rankdir = LR;
        edge [
                fontsize="12"
                ];
        node [
                shape = box;
                fontsize = "12"
        ];

        "conductor" -> "dir: /data/greatrds/etc/conductor" [label="0755"];
        "conductor" -> "dir: /data/greatrds/etc/conductor/ssh" [label="0700"];
        "conductor" -> "copy: id_rsa \n /data/greatrds/etc/conductor/ssh/id_rsa";
        "conductor" -> "copy: id_rsa.pub \n /data/greatrds/etc/conductor/ssh/id_rsa.pub";
        "conductor" -> "template: http_default.conf.j2 \n /data/nginx/etc";
        "conductor" -> "dir: /data/nginx/etc/ssl" [label="0755"];
        "conductor" -> "copy: greatrds.key \n /data/nginx/etc/ssl";
        "conductor" -> "copy: greatrds.crt \n /data/nginx/etc/ssl" [label="https"];
        "conductor" -> "template: config.js.j2 \n /data/greatrds/web/db/config.js";
        "conductor" -> "template: config.js.j2 \n /data/greatrds/web/dist/config.js";

        "conductor" -> "run conductor";
        "run conductor" -> "/data/nginx/etc/conf.d/default.conf: \n /etc/nginx/conf.d/default.conf";
        "run conductor" -> "/data/nginx/log:\n /var/log/nginx";
        "run conductor" -> "/data/greatrds/web:\n /usr/share/nginx/web";
        "run conductor" -> "/data/nginx/etc/ssl:\n /etc/nginx/ssl";

}
```



#### 1. default

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

#### 2. config.yml

```yaml
---

- name: create conductor config directory
  file:
    path: "{{ node_config_directory }}"
    state: directory
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: true

- name: ensure ssh_key_dir directory exists
  file:
    path: "{{ ssh_key_dir }}"
    state: directory
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0700
  become: True

- name: copy ssh private key
  copy:
    src: "id_rsa"
    dest: "{{ ssh_key_dir }}/id_rsa"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: "0600"
  become: true

- name: copy ssh public key
  copy:
    src: "id_rsa.pub"
    dest: "{{ ssh_key_dir }}/id_rsa.pub"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: "0644"
  become: true

```

#### 3. deploy.yml

```yaml
---
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

#### 4. upgrade.yml

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


#### 5. main.yml

```yaml
---
- include_tasks: config.yml

- include_role:
    name: api
    tasks_from: version

- include_tasks: deploy.yml

```

#### 6. id_rsa

```bash
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABlwAAAAdzc2gtcn
NhAAAAAwEAAQAAAYEArlORO50keg2Rw6uGPBUbgE6/LOqXV5XEVuWM/idOCm92HwnXcn5M
M8MSGN3+oEQWD4fi4bNoJF+bXPoTP47aPsOFU0vvvst5CiitoFELzAeEqmwUUtmJgAP3He
zVKTG9nKNViM5T70PCZszm+NzkKwsB3F57ng4OC+XiaMOzzRaciyk6Bczjocl3bUVJvNsa
JRT+JyjcnaLoDHShCO/OT2rtGhCMYdHn0gOW58rb68LOPpAO6vWpVJNNPtYUDR8x2ph8r3
bFlkZnSy21jI2PRhAZDGGEAhVArQlxncjE0pitavgYvjjFlVrZKt2p/1DpzmvpP8NCt5s/
IkirizQUcJBiPdsI4v6ZXwFCo99Lut/pLlRDtLxKJgo5qo3vZM27nNKcvPVKJX9GN4qju1
fpsCEJQBI5Tfo8H9KgUqiyBadP4g3aYlqW99ckT66JDkW8iMtaWKhC5Ghh2hmbracMdVuw
vE+k+P6cvavJlGeYN6BSxYH74ir27IFfhidvOfF9AAAFiJXUPDSV1Dw0AAAAB3NzaC1yc2
EAAAGBAK5TkTudJHoNkcOrhjwVG4BOvyzql1eVxFbljP4nTgpvdh8J13J+TDPDEhjd/qBE
Fg+H4uGzaCRfm1z6Ez+O2j7DhVNL777LeQooraBRC8wHhKpsFFLZiYAD9x3s1SkxvZyjVY
jOU+9DwmbM5vjc5CsLAdxee54ODgvl4mjDs80WnIspOgXM46HJd21FSbzbGiUU/ico3J2i
6Ax0oQjvzk9q7RoQjGHR59IDlufK2+vCzj6QDur1qVSTTT7WFA0fMdqYfK92xZZGZ0sttY
yNj0YQGQxhhAIVQK0JcZ3IxNKYrWr4GL44xZVa2Srdqf9Q6c5r6T/DQrebPyJIq4s0FHCQ
Yj3bCOL+mV8BQqPfS7rf6S5UQ7S8SiYKOaqN72TNu5zSnLz1SiV/RjeKo7tX6bAhCUASOU
36PB/SoFKosgWnT+IN2mJalvfXJE+uiQ5FvIjLWlioQuRoYdoZm62nDHVbsLxPpPj+nL2r
yZRnmDegUsWB++Iq9uyBX4YnbznxfQAAAAMBAAEAAAGBAJtcYLfXUZXxF/JhoE1Vpl/QTn
LSU7S8BfJHvEMZdmS4d3YsCDXCt1j+RdehqPbeFycaPKpXgL3B8y2GtbbUMk7zY5Qbvfu1
TRcvr6WL9U6NeL+FeQtpUjCj2A10b3i6GrlJm8ZD0+wN4GhLvqEWA8cUlUD3d4YiEoSqCQ
DDQS/LMhKX2F0o/8nFSkk5EgFc500bC3KM6SkX8WSAYLAdQCnpKowrKWFYeBjkGA5V7W+M
+tApBRKDg7ibWqFgmxgirovWkODghYpbFEY1rtuVlckAa3FLYarkVStFlKqV5Xcf1A+sm4
OhFt82RRt3gqTKsfjFLJxFMWryjrUzqlLXe2FZy+znoKrRZseEFS1unO6lQgrM5nbhZ2gX
q0xVPwV8Sg6osnC5bQLdY5n/4w5ybfnPFYcPmx2ykx8EksbHhBsktL+/WcYbQFnXzFWg+7
Mw4aqFsIrQ+pASn0qjTGRJDGs9YwLiugUsaPH7rzWyDj4YHUeU4GIGGQROooUHNhheRQAA
AMBY1m5Z9TriAU+14fx4EMO9Ieeus/YnzOSMtRV00dPDb/NTy4AZ12qonZ/obCRAAjnNYR
P+hN6C9BZQnUtkwhU9CrmIMZ6Zo9iMRVa8IUIcdtArYAARcxUtZ+b75aUp6ySGv3vfWqYY
AbH9eDsg5yRI963c15JzVEqq0SLpE7JRtjedBAzugt4Rkero/wYBsqQs0yW9ZVIhKUygbR
uU3YcTt1fwdOYox/v44AHYrVT2Ns8pFbyuSrJ0khpxpKzHVi4AAADBANqL/eQvD6F0LEoF
1o2C+Vpz30Prd1BmpGFgdEFSQiVh5yCuW9agj5qffy/zsmQGkfCXwMYOrk+IC9JkxCQdGC
5dlOibEWmZaLefTRm2sLsPsf5hTiCVOh/yJ5cKnPHcnTN52EM+VxxqXLKUkwcVAUHBqdlB
laxtgl4UQHA4eBYTNpY7BWaC8sV4okHd4EIk8Y4jsSJQmXFIKZ9A+aU0MiDloOyIrQ+Zeq
YSbT/vPMY3JtDmPQ+SL0Q04DbvYj/c2wAAAMEAzDONw7sUR6Gvg1vAtnKTb94TEsWbMtLn
+8oFQRn/Wzt8SycnqUc+xlzaERbiU10Aus7BrHmW/M8eyHGrwZC1yNO3HMEmWUxJk343yX
oUg3CBLDXotOqWrWGLzmhbJ6YR8AeemGw7nyV14mjmitFPmRbgMk4+OmnxUEbl9KcrDXRA
jHWy7PG9iOBiqAoh9zTXbfd3jxwagokqXF13EWNbETRPjG267lL3CBE0hqQ/y04qioZq7o
g72HYNuprBkY6HAAAAEnNxeXVAb3B0aXBsZXgtNTA3MA==
-----END OPENSSH PRIVATE KEY-----

```

#### 7. id_rsa.pub

```bash
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCuU5E7nSR6DZHDq4Y8FRuATr8s6pdXlcRW5Yz+J04Kb3YfCddyfkwzwxIY3f6gRBYPh+Lhs2gkX5tc+hM/jto+w4VTS+++y3kKKK2gUQvMB4SqbBRS2YmAA/cd7NUpMb2co1WIzlPvQ8JmzOb43OQrCwHcXnueDg4L5eJow7PNFpyLKToFzOOhyXdtRUm82xolFP4nKNydougMdKEI785Pau0aEIxh0efSA5bnytvrws4+kA7q9alUk00+1hQNHzHamHyvdsWWRmdLLbWMjY9GEBkMYYQCFUCtCXGdyMTSmK1q+Bi+OMWVWtkq3an/UOnOa+k/w0K3mz8iSKuLNBRwkGI92wji/plfAUKj30u63+kuVEO0vEomCjmqje9kzbuc0py89Uolf0Y3iqO7V+mwIQlAEjlN+jwf0qBSqLIFp0/iDdpiWpb31yRProkORbyIy1pYqELkaGHaGZutpwx1W7C8T6T4/py9q8mUZ5g3oFLFgfviKvbsgV+GJ2858X0= sqyu@optiplex-5070

```



## 十四、monitor



## 十五、auth



## 十六、exporter



## 十七、check






## 十八、全局变量

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
database_password: drACgwoqtM
rabbitmq_password: 123456
redis_password: egI4fYOife
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
app_dir: "/data/app"
# 临时路径
tmp_dir: "/data/tmp"

##################################################
# 源配置源
##################################################
# reposerver 主机地址
repo_host: "{{ hostvars[groups['reposerver'][0]].ansible_host }}"
pypiserver_host: "{{ hostvars[groups['pypiserver'][0]].ansible_host }}"

repo_base_dir: "{% if repo_dir is defined %}{{repo_dir}}{% else %}/data{% endif %}"
web_repo_host_path: "{{ repo_base_dir }}/web_repo/data"
web_repo_static: "/data/static"
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
docker_dir: "/data/docker"
docker_log_max_file: "5"
docker_log_max_size: "50m"
# Ansible Docker API timeout
docker_container_timeout: 300

####################

# config shared by api, conductor, monitor
etc_dir: "/data/greatrds/etc"
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

```






