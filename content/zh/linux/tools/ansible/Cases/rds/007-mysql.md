---
title: 07. mysql
description: mysql
date: 2023-10-12
weight: 60000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



{{< alert color="secondary" >}}


1. `mysql_server_id`
2. `scp client.cnf.j2 ~/.my.cnf`
3. `mkdir -p /data/database/etc`
4. `scp my.cnf.j2 /data/database/etc/my.cnf`
5. `docker run -d --name mysql -e MYSQL_ROOT_PASSWORD=123456 --restart unless-stopped -p 13336:3306 -v /data/database/etc/my.cnf:/etc/mysql/my.cnf -v /data/database/data:/var/lib/mysql mysql:8.0.27`
6. `sudo docker exec mysql mysqladmin -p123456 ping`
7. `create user root@'%'`
8. `create user replicator@'%'`
9. `reset master`
10. `reset slave`
11. `stop slave`
12. `change master`
13. `start slave`


{{< /alert >}}



## 一、defaults/main.yaml

```yaml
---

### passwords ###
database_password: 123456
rabbitmq_password: 123456
redis_password: 123456
#################
mysql_port: "13336"
#### 单节点 or 双节点 ####
controller_count: "{{ groups['controller'] | length }}"
deploy_mode: "{{ 'all-in-one' if controller_count == '1' else 'dual' }}"
monitor_deploy_mode: "{{ groups['monitor'] | length }}"
# defaults file for mysql

mysql_image: "mysql:8.0.27"
mysql_container: "mysql"
mysql_dir: "{{ base_dir }}/database"
mysql_data_dir: "{{ mysql_dir }}/data"
mysql_etc_dir: "{{ mysql_dir }}/etc"
mysql_replica_user: "replicator"
mysql_replica_password: "123456"
mysql_password: "{{ database_password }}"
mysql_start_retry: 6


```





## 二、deploy.yml

```yaml
---

# inventory_hostname=controller-1，controller-2
# 1，2，3，4
- name: set mysql server id fact
  set_fact:
    mysql_server_id: "{{ groups['database'].index(inventory_hostname) + 1 }}"

# scp client.cnf.j2 ~/.my.cnf
- name: generate mysql client config
  template:
    src: "client.cnf.j2"
    dest: "~/.my.cnf"

# mkdir -p /data/database/etc
- name: ensure mysql_etc_dir directory exists
  file:
    path: "{{ mysql_etc_dir }}"
    state: directory
    owner: "{{ rds_user }}"
    group: "{{ rds_group }}"
  become: True

# scp my.cnf.j2 /data/database/etc/my.cnf
- name: generate mysql server config
  template:
    src: "my.cnf.j2"
    dest: "{{ mysql_etc_dir }}/my.cnf"

# docker run -d --name mysql -e MYSQL_ROOT_PASSWORD=123456 --restart unless-stopped -p 13336:3306 -v /data/database/etc/my.cnf:/etc/mysql/my.cnf -v /data/database/data:/var/lib/mysql mysql:8.0.27
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

# 根据`.my.cnf`登录mysql来创建用户`root@'%'`
- name: allow root remote acccess
  mysql_user:
    name: "root"
    host: "%"
    password: "{{ database_password }}"
    priv: "*.*:ALL"


```


## 三、replica.yml


```yaml
---

# setup MySQL container master-master replication

- name: set opposite host index in ansible inventory group
  set_fact:
    oppo_idx: "{{ 1 - groups['database'].index(inventory_hostname) }}"

# 获取令一节点ip
- name: set opposite hostname
  set_fact:
    oppo_host: "{{ hostvars[groups['database'][oppo_idx | int]].ansible_host }}"

# 创建复制用户`replicator@'%'`，权限为`REPLICATION SLAVE`
- name: create replica user on mysql master
  mysql_user:
    name: "{{ mysql_replica_user }}"
    host: "%"
    password: "{{ mysql_replica_password }}"
    priv: "*.*:REPLICATION SLAVE"

# reset master
- name: reset master
  mysql_replication:
    mode: resetmaster

# reset slave
- name: reset slave
  mysql_replication:
    mode: resetslave

# stop slave
- name: stop mysql slave
  mysql_replication:
    mode: stopslave

# change master
- name: set mysql replication master
  mysql_replication:
    mode: changemaster
    master_host: "{{ oppo_host }}"
    master_port: "{{ mysql_port }}"
    master_auto_position: yes
    master_user: "{{ mysql_replica_user }}"
    master_password: "{{ mysql_replica_password }}"

# start slave
- name: start mysql slave
  mysql_replication:
    mode: startslave


```






## 四、main.yml

```yaml
---
- include_tasks: deploy.yml

- include_tasks: replica.yml
  when: deploy_mode == "dual"

```


## 五、handlers/main.yml


```yaml
---

# wait 60 seconds for MySQL container coming alive
- name: wait mysql start
  command: "sudo docker exec {{ mysql_container }} mysqladmin -p{{ database_password }} ping"
  register: command_result
  retries: "{{ mysql_start_retry }}"
  delay: 10
  until: "'mysqld is alive' in command_result.stdout"


```



## 六、client.cnf.j2

```yaml
[client]
user=mysql
password=123456
port=13336
socket=/data/database/data/mysql.sock

```


## 七、my.cnf.j2

```ini
[mysqld]

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



