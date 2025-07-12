---
title: 02. docker
description: 部署docker
date: 2023-10-12
weight: 200


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



{{< alert color="secondary" >}}

一、安装`docker`
1. 创建临时目录：`mkdir -p /data/tmp/`
2. 解压安装包：`tar -xzf rds-ansible/files/install-base/x64/docker-20.10.2-x64.tgz -C /data/tmp/`
3. 拷贝可执行文件：`cp /data/tmp/docker/* /usr/bin/`
4. 拷贝服务文件：`scp templates/docker.service.j2 /etc/systemd/system/docker.service`
5. 创建配置文件目录：`mkdir -p /etc/docker`
6. 拷贝配置文件：`scp templates/daemon.json.j2 /etc/docker/daemon.json`
7. 启动服务：`systemctl daemon-reload`,`systemctl enable docker`,`systemctl restarted docker`

二、添加rds安装用户到docker组
1. 添加组：`sudo groupadd docker`
2. 给用户添加组：`sudo usermod -a -G docker mysql`
3. 执行docker：`sudo docker ps`
4. 修改sock文件权限：`sudo chmod 666 /var/run/docker.sock`



{{< /alert >}}

## 一、defaults/main.yaml
```yaml
rds_user: "{{ ansible_user }}"  # rds 管理用户
rds_group: "{{ ansible_user }}"  # rds 管理用户属组

####################
# Docker registry server
####################
docker_registry_port: "5000"
docker_registry_host: "{{ hostvars[groups['reposerver'][0]].ansible_host }}"
docker_registry_insecure: "{{ 'yes' if docker_registry else 'no' }}"
docker_runtime_directory: ""
docker_registry_domain: "docker.greatrds.com"
docker_registry: "{{ docker_registry_domain }}:{{ docker_registry_port }}"  # we use /etc/hosts to map docker_registry_domain to docker_registry_host

# 临时路径
base_dir: "/data"
tmp_dir: "{{ base_dir }}/tmp"
docker_dir: "{{ base_dir }}/docker"
arch_mapping:
  x86_64: x64
  aarch64: arm64
arch_modifier: "{{ arch_mapping[ansible_architecture] }}"

docker_engine_package: "docker-20.10.2-{{ arch_modifier }}.tgz"
```



## 二、install-docker.yml

```yaml
---
# mkdir -p /data/tmp/
- name: create tmp_dir
  file:
    path: "{{ tmp_dir }}"
    state: directory
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: true

# rds-ansible/files/install-base/x64/docker-20.10.2-x64.tgz rds-ansible/files/install-base/arm64/docker-20.10.2-x64.tgz
# 2024-08-10T20:31:06 [root@node2 /data/tmp/docker] $ ls
# containerd  containerd-shim  containerd-shim-runc-v2  ctr  docker  dockerd  docker-init  docker-proxy  runc
# tar -xzf rds-ansible/files/install-base/x64/docker-20.10.2-x64.tgz -C /data/tmp/
- name: transfer and extract docker engine package
  unarchive:
    src: "../../files/install-base/{{ arch_modifier }}/{{ docker_engine_package }}"
    dest: "{{ tmp_dir }}"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755

# cp containerd  containerd-shim  containerd-shim-runc-v2  ctr  docker  dockerd  docker-init  docker-proxy  runc  /usr/bin/
# cp /data/tmp/docker/* /usr/bin/
- name: install docker engine binaries
  shell: "cp -rf {{ tmp_dir }}/docker/* /usr/bin/"
  failed_when: no
  become: yes

# scp templates/docker.service.j2 /etc/systemd/system/docker.service
- name: set docker systemd service unit
  template:
    src: docker.service.j2
    dest: /etc/systemd/system/docker.service
  become: yes

# mkdir -p /etc/docker
- name: create /etc/docker/ directory
  file:
    path: /etc/docker
    state: directory
  become: yes

# scp  templates/daemon.json.j2 /etc/docker/daemon.json
- name: set /etc/docker/daemon.json
  template:
    src: daemon.json.j2
    dest: /etc/docker/daemon.json
    mode: 0644
  when: docker_registry_insecure | bool
  become: yes

# systemctl daemon-reload
# systemctl enable docker
# systemctl restarted docker
- name: load systemd docker service unit
  service:
    name: docker
    state: restarted
    enabled: yes
    daemon_reload: yes
  become: True

```



## 三、docker-user.yml


- 创建组：`docker`
- 添加`mysql`到`docker`组
- 测试`mysql`用户执行：`sudo docker ps`
- 修改`/var/run/docker.sock`权限为`666`



```yaml

# sudo groupadd docker
- name: add docker group
  group:
    name: "docker"
    state: present
  become: yes

# mysql添加docker组
- name: add rds_user to docker group
  user:
    name: "{{ rds_user }}"
    groups:
      - "docker"
    append: yes
  become: yes

# sudo docker ps
- name: try docker with rds_user
  command: "sudo docker ps"
  register: docker_msg
  become: yes
  become_user: "{{ rds_user }}"
  failed_when: no

# chmod 666 /var/run/docker.sock
- name: chmod 666 /var/run/docker.sock
  command: chmod 666 /var/run/docker.sock
  when:
    - '"permission denied" in docker_msg.stderr'
  become: True


```


## 四、templates/daemon.json.j2

```json
{
    "registry-mirrors": ["http://{{ docker_registry }}"],
    "insecure-registries": ["{{ docker_registry }}"]
}


```


- `/etc/docker/daemon.json`

`2024-08-10T20:44:37 [root@node3 /data/tmpdir/rds_deploy/rds-ansible/files/install-base/x64] $ cat /etc/docker/daemon.json `
```bash
{
    "registry-mirrors": ["http://docker.greatrds.com:5000"],
    "insecure-registries": ["docker.greatrds.com:5000"]
}

```



## 五、templates/docker.service.j2
```ini
[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
After=network-online.target firewalld.service
Wants=network-online.target

[Service]
Type=notify
ExecStart=/usr/bin/dockerd --data-root {{ docker_dir }}
ExecReload=/bin/kill -s HUP $MAINPID
LimitNOFILE=infinity
LimitNPROC=infinity
LimitCORE=infinity
TimeoutStartSec=0
Delegate=yes
KillMode=process
Restart=on-failure
StartLimitBurst=3
StartLimitInterval=60s

[Install]
WantedBy=multi-user.target


```
- `ExecStart=/usr/bin/dockerd --data-root /data/docker`














