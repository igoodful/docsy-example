---
title: 创建复制用户
description: user
date: 2023-10-25
weight: 20000


---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{<alert color="danger" title="注意" >}}



{{</alert>}}



## 配置


&#10060;


| 变量参数                         | 不需重启? | 默认值 | 建议值   | 描述                            |
| :------------------------------- | :-------- | :----- | :------- | :------------------------------ |
| `log_bin`                        | &#9989;   | `on`   | 默认值   | 开启binlog日志                  |
| `log_replica_updates`            | &#9989;   | `on`   | 默认值   | 级联拓扑需要开启，`A -> B -> C` |
| `server_id`                      | &#9989;   | `1`    | 互不相同 |                                 |
| `server_uuid`                    | &#9989;   | `on`   | 互不相同 |                                 |
| `skip_slave_start`               | &#9989;   | `off`  | `on`     |                                 |
| `sync_binlog`                    | &#9989;   | `1`    |          |                                 |
| `innodb_flush_log_at_trx_commit` | &#9989;   | `1`    |          |                                 |
| `log_bin_basename`               | &#9989;   | `1`    |          |                                 |
| `log_bin_index`                  | &#9989;   | `1`    |          |                                 |



#### 创建用户

{{<alert color="danger" title="注意" >}}

- `caching_sha2_password`


{{</alert>}}


```sql

[root@127.0.0.1:8032 15:30:48((none))]$ set sql_log_bin = 0;

[root@127.0.0.1:8032 15:32:48((none))]$ create user repl@'%' identified by 'repl';
Query OK, 0 rows affected (0.29 sec)

[root@127.0.0.1:8032 15:33:24((none))]$ grant replication slave on *.* to repl@'%';
Query OK, 0 rows affected (0.02 sec)

[root@127.0.0.1:8032 15:33:58((none))]$

```


- 复制用户密码以明文存储

```sql
[admin@127.0.0.1:16315 15:30:44((none))]$ select * from mysql.slave_master_info\G
*************************** 1. row ***************************
                Number_of_lines: 32
                Master_log_name:
                 Master_log_pos: 4
                           Host: 172.17.134.76
                      User_name: dbscale_internal
                  User_password: root123456
                           Port: 16315
                  Connect_retry: 60
                    Enabled_ssl: 0
                         Ssl_ca:
                     Ssl_capath:
                       Ssl_cert:
                     Ssl_cipher:
                        Ssl_key:
         Ssl_verify_server_cert: 0
                      Heartbeat: 30
                           Bind:
             Ignored_server_ids: 0
                           Uuid: 4d5f8402-27d3-11ef-88fa-00163ef09ff4
                    Retry_count: 86400
                        Ssl_crl:
                    Ssl_crlpath:
          Enabled_auto_position: 1
                   Channel_name:
                    Tls_version:
                Public_key_path:
                 Get_public_key: 0
              Network_namespace:
   Master_compression_algorithm: uncompressed
  Master_zstd_compression_level: 3
               Tls_ciphersuites: NULL
Source_connection_auto_failover: 0
1 row in set (0.00 sec)

[admin@127.0.0.1:16315 15:30:49((none))]$
```





#### change master

`MySQL 8.0.23`之前版本：
```sql
change master to master_host='10.10.10.10', master_port=3306, master_user='repl', master_password='repl', master_log_file='bin.000001', master_log_pos=114;

```

`MySQL 8.0.23`版本开始：
```sql
change replication source to source_host='10.10.10.10', source_port=3306, source_user='repl', source_password='repl', source_log_file='bin.000001', source_log_pos=114;

```













