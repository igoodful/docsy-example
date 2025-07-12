---
title: MySQL实例监控
description: 监控每台MySQL实例
date: 2023-10-06
weight: 60000



---

{{< alert >}}
MySQL监控
{{< /alert >}}

### MySQL监控

{{< alert title="配置信息">}}

| 硬件名称                 | 配置      |
|:-------------------------|:----------|
| Engine                   | InnoDB    |
| character_set_client     | UTF8MB4   |
| character_set_connection | UTF8MB4   |
| character_set_database   | UTF8MB4   |
| character_set_database   | UTF8MB4   |
| character_set_database   | UTF8MB4   |
| character_set_database   | UTF8MB4   |
| OS                       | Kylin-v10 |
| CPU                      | 64C       |
| Memory                   | 512G      |
| Space                    | 14T       |
| MySQL Version            | 8.0.26    |

{{< /alert >}}


### show slave status

{{< alert tile="主从延迟" color="secondary">}}

| 监控指标        | 数据                  | 采集方式          | 阈值      |
|:------------|:----------------------|:------------------|:--------|
| 主从延迟        | Seconds_Behind_Master | show slave status | 单位为秒  |
| 从库io线程状态  | Slave_IO_Running      | show slave status | Yes为正常 |
| 从库sql线程状态 | Slave_SQL_Running     | show slave status | Yes为正常 |
| 主从延迟        | show slave status     | show slave status | 单位为秒  |

{{< /alert >}}

```sql
mysql> show slave status\G
*************************** 1. row ***************************
               Slave_IO_State: Waiting for source to send event
                  Master_Host: 172.17.138.54
                  Master_User: dbscale_internal
                  Master_Port: 16315
                Connect_Retry: 60
              Master_Log_File: greatdb-bin.000029
          Read_Master_Log_Pos: 909
               Relay_Log_File: mysql-relay.000003
                Relay_Log_Pos: 464
        Relay_Master_Log_File: greatdb-bin.000029
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 909
              Relay_Log_Space: 1269
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: 0
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 44049858
                  Master_UUID: faed83c0-7214-11ee-a62d-00163ec5f089
             Master_Info_File: mysql.slave_master_info
                    SQL_Delay: 0
          SQL_Remaining_Delay: NULL
      Slave_SQL_Running_State: Replica has read all relay log; waiting for more updates
           Master_Retry_Count: 86400
                  Master_Bind:
      Last_IO_Error_Timestamp:
     Last_SQL_Error_Timestamp:
               Master_SSL_Crl:
           Master_SSL_Crlpath:
           Retrieved_Gtid_Set: faed83c0-7214-11ee-a62d-00163ec5f089:304677
            Executed_Gtid_Set: faed83c0-7214-11ee-a62d-00163ec5f089:1-304677,
faee4346-7214-11ee-89c0-00163ee1d983:1-122646
                Auto_Position: 1
         Replicate_Rewrite_DB:
                 Channel_Name:
           Master_TLS_Version:
       Master_public_key_path:
        Get_master_public_key: 0
            Network_Namespace:
1 row in set, 1 warning (0.01 sec)


```


### show slave status
{{< alert tile="主从延迟" color="secondary">}}

| 监控指标   | 数据            | 采集方式                         | 阈值      |
|:-------|:----------------|:---------------------------------|:----------|
| 活跃连接数 | Threads_running | performance_schema.global_status | cpu_count |
| 在线连接数   | Threads_connected | performance_schema.global_status |            | 102400 |
| 实例运行时长 | Uptime            | performance_schema.global_status | 均正常     |
| 入口流量     | Bytes_received    | performance_schema.global_status | 单位为字节 |
| 出口流量     | Bytes_sent        | performance_schema.global_status | 单位为字节 |
| QPS          | Questions         | performance_schema.global_status | 单位为字节 |
| TPS          | Bytes_sent        | performance_schema.global_status | 单位为字节 |
| 慢查询个数   | Slow_queries      | performance_schema.global_status | 单位为字节 |
{{< /alert >}}

```sql
mysql> select * from  performance_schema.global_status where variable_name='Threads_running';
+-----------------+----------------+
| VARIABLE_NAME   | VARIABLE_VALUE |
+-----------------+----------------+
| Threads_running | 2              |
+-----------------+----------------+
1 row in set (0.00 sec)

mysql> select variable_value from  performance_schema.global_status where variable_name='Threads_connected';
+----------------+
| variable_value |
+----------------+
| 32             |
+----------------+
1 row in set (0.00 sec)

mysql> select variable_value from  performance_schema.global_status where variable_name='Uptime';
+----------------+
| variable_value |
+----------------+
| 89428          |
+----------------+
1 row in set (0.00 sec)

mysql> select * from  performance_schema.global_status where variable_name like 'Bytes_%';
+----------------+----------------+
| VARIABLE_NAME  | VARIABLE_VALUE |
+----------------+----------------+
| Bytes_received | 726756003      |
| Bytes_sent     | 5921161908     |
+----------------+----------------+
2 rows in set (0.00 sec)

mysql> select * from  performance_schema.global_status where variable_name like 'Questions%';
+---------------+----------------+
| VARIABLE_NAME | VARIABLE_VALUE |
+---------------+----------------+
| Questions     | 3376198        |
+---------------+----------------+
1 row in set (0.00 sec)

mysql> select * from  performance_schema.global_status where variable_name like 'Slow_queries';
+---------------+----------------+
| VARIABLE_NAME | VARIABLE_VALUE |
+---------------+----------------+
| Slow_queries  | 0              |
+---------------+----------------+
1 row in set (0.00 sec)

```













