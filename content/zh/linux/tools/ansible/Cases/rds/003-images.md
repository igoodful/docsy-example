---
title: 03. images
description: images
date: 2023-10-12
weight: 300


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



{{< alert color="secondary" >}}

1. 解压`docker-images-arm64.tar.gz`或`docker-images-x64.tar.gz`到`/data/tmp/`

2. 导入镜像：`/data/tmp/docker-images-arm64`或`/data/tmp/docker-images-x64`
   1. `alertmanager`
   2. `greatrds_base`
   3. `keepalived`
   4. `mysql`
   5. `nginx`
   6. `prometheus`
   7. `pypiserver`
   8. `rabbitmq`
   9. `registry`

{{< /alert >}}



## 一、defaults/main.yaml

```yaml
---
docker_images_package_basename: "docker-images-{{ arch_modifier }}"
docker_images_package_suffix: ".tar.gz"
docker_images_package: "{{ docker_images_package_basename }}{{ docker_images_package_suffix }}"
docker_images_tmp_path: "{{ tmp_dir }}/{{ docker_images_package_basename }}"

```



## 二、load-images.yml





```yaml
---

# tar -xzf rds-ansible/files/install-base/arm64/docker-images-arm64.tar.gz  -C /data/tmp/
# tar -xzf rds-ansible/files/install-base/x64/docker-images-x64.tar.gz      -C /data/tmp/
# 2024-08-10T21:03:48 [root@node3 //data/tmp/docker-images-x64] $ ls
# alertmanager.tar  Dockerfile-pypiserver  greatrds_base.tar  keepalived.tar  mysql.tar  nginx.tar  prometheus.tar  pypiserver.tar  rabbitmq.tar  registry.tar
- name: transfer and extract docker images package
  unarchive:
    src: "../../files/install-base/{{ arch_modifier }}/{{ docker_images_package }}"
    dest: "{{ tmp_dir }}"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755

# 先后导入镜像：alertmanager，greatrds_base，keepalived，mysql，nginx，prometheus，pypiserver，rabbitmq，registry
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




`2024-08-10T21:14:12 [root@node3 /data] $ docker images`

输出如下：
```bash
REPOSITORY              TAG                IMAGE ID       CREATED         SIZE
greatrds_auth           6.0.1.0rc4         4fc64209222b   6 hours ago     887MB
greatrds                6.0.1.0rc4         7e32e28ac3b4   6 hours ago     1.03GB
prom/alertmanager       latest             6d5b4785e251   14 months ago   64.7MB
prom/prometheus         latest             d70139d183ad   14 months ago   234MB
nginx                   1.22.0-alpine      ccb911fdd2ca   2 years ago     23.5MB
mysql                   8.0.27             b05128b000dd   2 years ago     516MB
rabbitmq                3.9.7-management   6358b95a6e6c   2 years ago     253MB
pypiserver/pypiserver   latest             2fd43d676c81   3 years ago     52.3MB
keepalived              latest             4110c6890c70   3 years ago     108MB
greatrds_base           latest             95f5273cf072   3 years ago     743MB
registry                2                  2d4f4b5309b1   4 years ago     26.2MB

```


















