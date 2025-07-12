---
title: 05. pypiserver
description: pypiserver
date: 2023-10-12
weight: 60000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



{{< alert color="secondary" >}}

1. `which rsync`
2. `scp rds-ansible/files/pypiserver-packages/packages /data/pypiserver/`
3. `chmod -R 0755 /data/pypiserver/`
4. `docker run -d --name pypiserver -p 18080:8080 -v /data/pypiserver:/data/packages pypiserver/pypiserver`


{{< /alert >}}



## 一、defaults/main.yaml

```yaml
---

docker_registry_port: "5000"
web_repo_port: "1510"
pypiserver_port: "18080"
# defaults file for repo

pypiserver_basename: "pypiserver-packages"
pypiserver_suffix: ".tar.gz"
pypiserver_host_path: "{{ base_dir }}/pypiserver"

```


## 二、pypiserver.yml

```yaml
---
# pypiserver

# which rsync
- name: Check rsync command exists
  command: "which rsync"
  ignore_errors: yes
  register: rsync

# scp rds-ansible/files/pypiserver-packages/packages /data/pypiserver/
- name: copy pypiserver packages to host path
  copy:
    src: "{{ role_path }}/../../files/{{ pypiserver_basename }}/packages"
    dest: "{{ pypiserver_host_path }}"
#    remote_src: yes
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: yes
  when: rsync.stdout == ''

# scp rds-ansible/files/pypiserver-packages/packages /data/pypiserver/
- name: synchronize pypiserver packages to host path
  copy:
    src: "{{ role_path }}/../../files/{{ pypiserver_basename }}/packages"
    dest: "{{ pypiserver_host_path }}"
  become: yes
  when: rsync.stdout != ''

# chmod -R 0755 /data/pypiserver/
- name: set web pypiserver path permission to 755
  file:
    path: "{{ pypiserver_host_path }}"
    state: directory
    recurse: yes
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: true

# docker run -d --name pypiserver -p 18080:8080 -v /data/pypiserver:/data/packages pypiserver/pypiserver
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




















