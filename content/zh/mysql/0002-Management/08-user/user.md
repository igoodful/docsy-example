---
title: 用户
description: user
date: 2023-10-25
weight: 20000
viz: true

---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{<note>}}
<!---->


{{</note>}}




```sql
[root@127.0.0.1:8032 23:18:21(db1)]$ select user();
+----------------+
| user()         |
+----------------+
| root@127.0.0.1 |
+----------------+
1 row in set (0.00 sec)

[root@127.0.0.1:8032 23:18:37(db1)]$ select system_user();
+----------------+
| system_user()  |
+----------------+
| root@127.0.0.1 |
+----------------+
1 row in set (0.00 sec)

[root@127.0.0.1:8032 23:19:41(db1)]$ select session_user();
+----------------+
| session_user() |
+----------------+
| root@127.0.0.1 |
+----------------+
1 row in set (0.00 sec)

[root@127.0.0.1:8032 23:19:51(db1)]$

```





## 修改密码

| 选项                                        | 功能     | 示例                                                          |
| :------------------------------------------ | :------- | :------------------------------------------------------------ |
| alter user                                  | 修改密码 | `alter user 'md'@'%' identified by 'md';`                     |
| set password for                            | 修改密码 | `set password for 'md'@'%' = 'md';`                           |
| update mysql.user set authentication_string | 修改密码 | `update mysql.user set authentication_string = xxx where yyy` |


```sql
[root@127.0.0.1:8032 19:42:07((none))]$ create user md@'%';
Query OK, 0 rows affected (0.15 sec)

[root@127.0.0.1:8032 19:42:23((none))]$ alter user 'md'@'%' identified by 'md';
Query OK, 0 rows affected (0.00 sec)

[root@127.0.0.1:8032 19:43:17((none))]$ set password for 'md'@'%' = 'md';
Query OK, 0 rows affected (0.00 sec)

[root@127.0.0.1:8032 19:43:54((none))]$ update mysql.user set authentication_string = '*6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9' where user = 'md' and host = '%';

```




## validate_password
> 密码验证组件的安装和卸载


```sql
[root@127.0.0.1:8032 23:16:11(db1)]$ install component 'file://component_validate_password';
Query OK, 0 rows affected (0.00 sec)

[root@127.0.0.1:8032 23:16:12(db1)]$ uninstall component 'file://component_validate_password';
Query OK, 0 rows affected (0.00 sec)

[root@127.0.0.1:8032 23:16:49(db1)]$

```





## 权限管理




```sql
[admin@127.0.0.1:16315 09:28:30(sbtest)]$ revoke all on *.* from  md@'%';
Query OK, 0 rows affected (0.01 sec)

[admin@127.0.0.1:16315 09:31:47(sbtest)]$



```




## 密码过期

- 立即过期
```sql
alter user fb@'%' password expire;

alter user fb@'%' identified by 'aa' password expire;

```


- 永不过期

```sql
alter user fb@'%' password expire never;

```




- 90天后过期

```sql
alter user fb@'%' password expire interval 90 day;

```


- 让此账号使用默认的密码过期全局策略

```sql
alter user fb@'%' password expire default;

```


- 设置全局过期策略 先手动更改再加入配置文件

要构建全局密码自动过期策略，请使用 default_password_lifetime 系统变量。在 5.7.11 版本之前，默认的 default_password_lifetime 值为 360(密码大约每年必须更改一次)，之后的版本默认值为 0，表示密码不会过期。此参数的单位是天，比如我们可以将此参数设置为 90 ，则表示全局密码自动过期策略是 90 天
```sql
set global default_password_lifetime = 90;

show variables like 'default_password_lifetime';

```

写入配置文件使得重启生效
```ini
[mysqld]
default_password_lifetime = 90
```

