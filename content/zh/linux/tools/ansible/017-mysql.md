---
title: 17. mysql
description: mysql
date: 2023-10-12
weight: 1700


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


## 一、mysql_db


#### 1. 选项

| 选项       | 默认值 | 作用 |
| :--------- | :----- | :--- |
| name       |        |      |
| login_host |        |      |
| login_port |        |      |
| state      |        |      |
| encoding   |        |      |




```yaml
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

```



