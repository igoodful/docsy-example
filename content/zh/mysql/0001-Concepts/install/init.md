---
title: init
description: init
date: 2023-11-07
weight: 20000


---

{{< alert >}}

{{< /alert >}}



## 环境
{{<alert>}}

{{</alert>}}



### --initialize
- 命令直接初始化
```bash
bin/mysqld --initialize --user=mysql --basedir=/data/mysql_3306 --datadir=/data/mysql_3306/data
```


- 利用配置文件来初始化
```bash
bin/mysqld --defaults-file=/data/mysql_3306/etc/my.cnf --initialize --user=mysql
```

`cat /data/mysql_3306/etc/my.cnf`:
```yaml
[mysqld]
basedir=/data/mysql_3306
datadir=/data/mysql_3306/data
```
{{<alert>}}
生成随机初始化用户'root'@'localhost'的密码，但密码将被标记为已过期，因此利用该密码登录后您必须配置一个新密码
{{</alert>}}

- 登录
```sql
mysql -u root -p

mysql -u root --skip-password
```

- 创建账号
```sql
alter user 'root'@'localhost' identified by 'root-password';

create user 'root'@'127.0.0.1' identified by 'root-password';
grant all on *.* to 'root'@'127.0.0.1' with grant option;

create user 'root'@'::1' identified by 'root-password';
grant all on *.* to 'root'@'::1' with grant option;
```





### --initialize-insecure
```bash
bin/mysqld --initialize-insecure --user=mysql --basedir=/data/mysql_3306 --datadir=/data/mysql_3306/data
```


```yaml
---
# tasks file for mysql

- name: remove MYSQL_PS1 from /etc/profile
  become: yes
  lineinfile:
    path: /etc/profile
    regexp: 'MYSQL_PS1'
    state: absent

- name: Add MYSQL_PS1 to /etc/profile
  become: yes
  lineinfile:
    path: /etc/profile
    line: 'export MYSQL_PS1="[\\U:\p \\R:\\m:\\s(\\d)]$"'
    insertafter: EOF
    state: present
```

