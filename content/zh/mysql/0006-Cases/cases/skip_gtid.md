---
title: 误删数据恢复
description: 误删数据恢复
date: 2023-10-12
weight: 60000


---

{{< alert >}}

{{< /alert >}}


### 一、环境信息

{{< alert >}}

| 名称          | 配置       |
|:--------------|:-----------|
| Arch          | x64        |
| OS            | CentOS 7.9 |
| CPU           | 64C        |
| Memory        | 512G       |
| Space         | 14T        |
| MySQL Version | 8.0.25     |

{{< /alert >}}

## 跳过指定gtid
```sql
set session gtid_next= 'f9f2f2be-5604-11ee-b389-00163e3cdeea:2';

begin;

commit;

set session gtid_next = automatic;

stop slave;

reset slave all;

reset master;

set global gtid_purged='e08050aa-4095-11ee-8361-00163e48fc5c:1-17313';

change master to master_host='x',master_port=3306,master_user='y',master_password='z',master_auto_position=1;
```





## 误删数据恢复

### 基于position

```sql
stop slave;

reset slave all;

reset master;

change master to master_host='x',master_port=3306,master_user='y',master_password='z', master_log_file='mysql-bin.000241',master_log_pos=669674886;

start slave io_thread;

start slave sql_thread until master_log_file='mysql-bin.000246',master_log_pos=228466573;

show slave status\G
```



### 基于gtid

```sql
stop slave;

reset slave all;

reset master;

SET @@GLOBAL.GTID_PURGED='1751c709-9e64-11ea-855e-e4434b034ad8:1-4301373';

change master to master_host='x',master_port=3306,master_user='y',master_password='z',master_auto_position=1;

start slave io_thread;

start slave sql_thread until sql_before_gtids='1751c709-9e64-11ea-855e-e4434b034ad8:4315229';

show slave status\G
```



















