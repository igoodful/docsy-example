---
title: 04. repo
description: repo
date: 2023-10-12
weight: 60000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



{{< alert color="secondary" >}}

1. `mkdir -p /data/web_repo/data`
2. `which rsync`
3. `scp -r rds-ansible/files/repo /data/web_repo/data/`
4. `mkdir -p /data/static`
5. `scp default.conf /data/web_repo/`
6. `chmod -R 755 /data/web_repo/data`
7. `docker run -d --name web_repo -p 1510:80 -v /data/web_repo/data/repo:/opt/repo -v /data/static:/opt/static -v /data/web_repo/default.conf:/etc/nginx/conf.d/default.conf nginx:1.22.0-alpine`


{{< /alert >}}



## 一、defaults/main.yaml

```yaml
---

web_repo_port: "1510"
repo_base_dir: "{% if repo_dir is defined %}{{repo_dir}}{% else %}{{ base_dir }}{% endif %}"
web_repo_host_path: "{{ repo_base_dir }}/web_repo/data"
web_repo_static: "{{ base_dir }}/static"
web_repo_static_mount: "/opt/static"
# defaults file for repo

web_repo_package_basename: "repo"
web_repo_nginx_conf_path: "{{ repo_base_dir }}/web_repo/default.conf"
web_repo_image: "nginx:1.22.0-alpine"
package_install_dir: "yum_repo/centos/7/os/x86_64/package_install"

```


## 二、web-repo.yml

```yaml
---

# mkdir -p /data/web_repo/data
- name: create web repo data directory
  file:
    path: "{{ web_repo_host_path }}"
    state: directory
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: yes

# which rsync
- name: Check rsync command exists
  command: "which rsync"
  ignore_errors: yes
  register: rsync

- debug: msg="复制集群依赖中.....过程较长请耐心等待, 若中断,  请重新执行安装"

# scp -r rds-ansible/files/repo /data/web_repo/data/
- name: copy repo server package
  copy:
    src: "{{ role_path }}/../../files/{{ web_repo_package_basename }}"
    dest: "{{ web_repo_host_path }}"
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: yes
  when: rsync.stdout == ''

# scp -r rds-ansible/files/repo /data/web_repo/data/
- name: synchronize repo server package
  synchronize:
    src: "{{ role_path }}/../../files/{{ web_repo_package_basename }}"
    dest: "{{ web_repo_host_path }}"
  become: yes
  when: rsync.stdout != ''

# mkdir -p /data/static
- name: create api static data directory
  file:
    path: "{{ web_repo_static }}"
    state: directory
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0755
  become: yes

# scp default.conf /data/web_repo/
- name: create web repo nginx config
  copy:
    src: default.conf
    dest: "{{ web_repo_nginx_conf_path }}"

# chmod -R 755 /data/web_repo/data
- name: set web repo path permission to 755
  file:
    path: "{{ web_repo_host_path }}"
    state: directory
    recurse: yes
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
    mode: 0777
  become: true

# docker run -d --name web_repo -p 1510:80 -v /data/web_repo/data/repo:/opt/repo -v /data/static:/opt/static -v /data/web_repo/default.conf:/etc/nginx/conf.d/default.conf nginx:1.22.0-alpine
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





## 三、/etc/nginx/conf.d/default.conf
> web_repo容器内部


```nginx
server {
    listen       80;
    server_name  localhost;
    server_tokens off;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }

    location /gtms/ {
        alias /opt/;
        autoindex on;
    }
}


```

## 四、/etc/nginx/nginx.conf
> web_repo容器内部

```nginx
user  nginx;
worker_processes  auto;

error_log  /var/log/nginx/error.log notice;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}

```

- nginx的访问日志和错误日志重定向到标准输出和标准错误输出：
  - `/var/log/nginx/access.log`
  - `/var/log/nginx/error.log`

```bash
/var/log/nginx # ls -l
total 0
lrwxrwxrwx    1 root     root            11 Jul 19  2022 access.log -> /dev/stdout
lrwxrwxrwx    1 root     root            11 Jul 19  2022 error.log -> /dev/stderr

```





## 使用


- [http://172.17.134.77:1510/gtms/](http://172.17.134.77:1510/gtms/)


```bash
docker logs web_repo

docker logs -tf web_repo

docker logs --tail 10 web_repo

docker logs --tail 10 -tf  web_repo

docker logs  -tf --since="2024-07-22T15:00:00" web_repo

```


nginx指定文件路径有两种方式root和alias，指令的使用方法和作用域：

[root]

[alias]


root与alias主要区别在于nginx如何解释location后面的uri，这会使两者分别以不同的方式将请求映射到服务器文件上。
- root的处理结果是：root路径＋location路径
- alias的处理结果是：使用alias路径替换location路径
- alias是一个目录别名的定义，root则是最上层目录的定义。
- 还有一个重要的区别是alias后面必须要用“/”结束，否则会找不到文件的。。。而root则可有可无~~

root实例：
```nginx
location ^~ /t/ {
     root /www/root/html/;
}
```

如果一个请求的URI是/t/a.html时，web服务器将会返回服务器上的/www/root/html/t/a.html的文件。

alias实例：
```nginx
location ^~ /t/ {
alias /www/root/html/new_t/;
}
```

如果一个请求的URI是/t/a.html时，web服务器将会返回服务器上的/www/root/html/new_t/a.html的文件。注意这里是new_t，因为alias会把location后面配置的路径丢弃掉，把当前匹配到的目录指向到指定的目录。

注意：
1. 使用alias时，目录名后面一定要加"/"。
2. alias在使用正则匹配时，必须捕捉要匹配的内容并在指定的内容处使用。
3. alias只能位于location块中。（root可以不放在location中）


