---
title: 主从延迟
date: 2023-10-31
description: >
  主从延迟


weight: 70000
---

{{< alert >}}
- 查看MySQL集群状态： dbscale show dataservers\G

- 查看MySQL选主时间：dbscale show datasource type=replication\G
{{< /alert >}}



## 查看MySQL集群状态
### dbscale show dataservers
{{< alert color="secondary">}}
mysql> dbscale show dataservers\G

- Slave normal, catching master 68073s

- dbscale enable dataserver normal_0_1;

{{< /alert >}}

```sql
mysql> dbscale show dataservers\G
*************************** 1. row ***************************
                    servername: normal_0_0
                          host: 172.17.138.54
                          port: 16315
                      username: dbscale_internal
                        status: Server normal
          master-online-status: Master_Online
                 master_backup: 1
                   remote_user: root
                   remote_port: 22
max_needed_conn/max_mysql_conn:
               master_priority: 50
*************************** 2. row ***************************
                    servername: normal_0_1
                          host: 172.17.138.51
                          port: 16315
                      username: dbscale_internal
                        status: Slave normal, catching master 68709s
          master-online-status: Not_A_Master
                 master_backup: 1
                   remote_user: root
                   remote_port: 22
max_needed_conn/max_mysql_conn:
               master_priority: 100
*************************** 3. row ***************************
                    servername: normal_0_2
                          host: 172.17.138.52
                          port: 16315
                      username: dbscale_internal
                        status: Slave normal
          master-online-status: Not_A_Master
                 master_backup: 1
                   remote_user: root
                   remote_port: 22
max_needed_conn/max_mysql_conn:
               master_priority: 100
3 rows in set (0.00 sec)


```

### dbscale show datasource type=replication
{{< alert color="secondary">}}
mysql> dbscale show dataservers\G

- MySQL集群最近选主时间：SelectMasterTime: 2023-10-30 14:38:51

{{< /alert >}}
```sql
mysql> dbscale show datasource type=replication\G
*************************** 1. row ***************************
                Name: normal_0
                Type: Replication
        SourceStatus: Working
 LoadBalanceStrategy: MASTER
        MasterSource: normal_0_normal_0_0
  MasterSourceStatus: Working
        MasterServer: normal_0_0
MasterServerHostInfo: 172.17.138.54:16315
  MasterServerStatus: Server normal
    SelectMasterTime: 2023-10-30 14:38:51
         SlaveSource: normal_0_normal_0_1
   SlaveSourceStatus: Stop
        SlaveServers: normal_0_1
 SlaveServerHostInfo: 172.17.138.51:16315
   SlaveServerStatus: server disabled
*************************** 2. row ***************************
                Name: normal_0
                Type:
        SourceStatus:
 LoadBalanceStrategy:
        MasterSource:
  MasterSourceStatus:
        MasterServer:
MasterServerHostInfo:
  MasterServerStatus:
    SelectMasterTime:
         SlaveSource: normal_0_normal_0_2
   SlaveSourceStatus: Working
        SlaveServers: normal_0_2
 SlaveServerHostInfo: 172.17.138.52:16315
   SlaveServerStatus: Slave normal
2 rows in set (0.00 sec)


```

### dbscale show datasource normal_0
```sql
mysql> dbscale show datasource normal_0 to master\G
*************************** 1. row ***************************
                Name: normal_0
                Type: Replication
        SourceStatus: Working
 LoadBalanceStrategy: MASTER
        MasterSource: normal_0_normal_0_0
  MasterSourceStatus: Working
        MasterServer: normal_0_0
MasterServerHostInfo: 172.17.138.54:16315
  MasterServerStatus: Server normal
    SelectMasterTime: 2023-10-30 14:38:51
         SlaveSource: normal_0_normal_0_1
   SlaveSourceStatus: Working
        SlaveServers: normal_0_1
 SlaveServerHostInfo: 172.17.138.51:16315
   SlaveServerStatus: Slave normal
*************************** 2. row ***************************
                Name: normal_0
                Type:
        SourceStatus:
 LoadBalanceStrategy:
        MasterSource:
  MasterSourceStatus:
        MasterServer:
MasterServerHostInfo:
  MasterServerStatus:
    SelectMasterTime:
         SlaveSource: normal_0_normal_0_2
   SlaveSourceStatus: Working
        SlaveServers: normal_0_2
 SlaveServerHostInfo: 172.17.138.52:16315
   SlaveServerStatus: Slave normal
2 rows in set (0.00 sec)

```



### dbscale request cluster info

```sql
mysql> dbscale request cluster info\G
*************************** 1. row ***************************
          master_dbscale: master
       cluster_server_id: 1
                    host: 172.17.138.54:16310
               join time: 2023-10-25 10:57:51
         ka init Version: 7
       ka update Version: 278|278
    dynamic node Version: 1
   dynamic space Version: 41
master re-scramble delay: 1
         dbscale version: GreatDBRouter-6.0.0.5987.b43cf710.with_spark.with_ssl
*************************** 2. row ***************************
          master_dbscale: slave
       cluster_server_id: 2
                    host: 172.17.138.52:16310
               join time: 2023-10-25 10:58:11
         ka init Version: 1
       ka update Version: 278|278
    dynamic node Version: 1
   dynamic space Version: 41
master re-scramble delay: 1
         dbscale version: GreatDBRouter-6.0.0.5987.b43cf710.with_spark.with_ssl
*************************** 3. row ***************************
          master_dbscale: slave
       cluster_server_id: 3
                    host: 172.17.138.51:16310
               join time: 2023-10-25 11:13:03
         ka init Version: 7
       ka update Version: 278|278
    dynamic node Version: 1
   dynamic space Version: 41
master re-scramble delay: 1
         dbscale version: GreatDBRouter-6.0.0.5987.b43cf710.with_spark.with_ssl
3 rows in set (0.01 sec)


```


### show slave status
{{< alert color="secondary">}}
mysql> show slave status\G

- Seconds_Behind_Master: 68727

- Slave_IO_Running: Yes

- Slave_SQL_Running: Yes

{{< /alert >}}
```sql
mysql> show slave status\G
*************************** 1. row ***************************
               Slave_IO_State: Waiting for source to send event
                  Master_Host: 172.17.138.54
                  Master_User: dbscale_internal
                  Master_Port: 16315
                Connect_Retry: 60
              Master_Log_File: greatdb-bin.000028
          Read_Master_Log_Pos: 673734143
               Relay_Log_File: mysql-relay.000002
                Relay_Log_Pos: 8943043
        Relay_Master_Log_File: greatdb-bin.000028
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
          Exec_Master_Log_Pos: 241839075
              Relay_Log_Space: 440838316
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: 68727
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
      Slave_SQL_Running_State: Waiting for replica workers to process their queues
           Master_Retry_Count: 86400
                  Master_Bind:
      Last_IO_Error_Timestamp:
     Last_SQL_Error_Timestamp:
               Master_SSL_Crl:
           Master_SSL_Crlpath:
           Retrieved_Gtid_Set: faed83c0-7214-11ee-a62d-00163ec5f089:59595-289698
            Executed_Gtid_Set: faed83c0-7214-11ee-a62d-00163ec5f089:1-64302,
faee4346-7214-11ee-89c0-00163ee1d983:1-122645
                Auto_Position: 1
         Replicate_Rewrite_DB:
                 Channel_Name:
           Master_TLS_Version:
       Master_public_key_path:
        Get_master_public_key: 0
            Network_Namespace:
1 row in set, 1 warning (0.00 sec)


```


## 处理 server disabled
{{< alert color="secondary">}}
- dbscale show datasource type=replication\G

- dbscale show dataservers;

- dbscale enable dataserver normal_0_1;
{{< /alert >}}
```sql
mysql> dbscale show datasource type=replication\G
*************************** 1. row ***************************
                Name: normal_0
                Type: Replication
        SourceStatus: Working
 LoadBalanceStrategy: MASTER
        MasterSource: normal_0_normal_0_0
  MasterSourceStatus: Working
        MasterServer: normal_0_0
MasterServerHostInfo: 172.17.138.54:16315
  MasterServerStatus: Server normal
    SelectMasterTime: 2023-10-30 14:38:51
         SlaveSource: normal_0_normal_0_1
   SlaveSourceStatus: Stop
        SlaveServers: normal_0_1
 SlaveServerHostInfo: 172.17.138.51:16315
   SlaveServerStatus: server disabled
*************************** 2. row ***************************
                Name: normal_0
                Type:
        SourceStatus:
 LoadBalanceStrategy:
        MasterSource:
  MasterSourceStatus:
        MasterServer:
MasterServerHostInfo:
  MasterServerStatus:
    SelectMasterTime:
         SlaveSource: normal_0_normal_0_2
   SlaveSourceStatus: Working
        SlaveServers: normal_0_2
 SlaveServerHostInfo: 172.17.138.52:16315
   SlaveServerStatus: Slave normal
2 rows in set (0.00 sec)


mysql> dbscale show dataservers;
+------------+---------------+-------+------------------+-----------------+----------------------+---------------+-------------+-------------+--------------------------------+-----------------+
| servername | host          | port  | username         | status          | master-online-status | master_backup | remote_user | remote_port | max_needed_conn/max_mysql_conn | master_priority |
+------------+---------------+-------+------------------+-----------------+----------------------+---------------+-------------+-------------+--------------------------------+-----------------+
| normal_0_0 | 172.17.138.54 | 16315 | dbscale_internal | Server normal   | Master_Online        | 1             | root        | 22          |                                | 50              |
| normal_0_1 | 172.17.138.51 | 16315 | dbscale_internal | server disabled | Master_Offline       | 1             | root        | 22          |                                | 100             |
| normal_0_2 | 172.17.138.52 | 16315 | dbscale_internal | Slave normal    | Not_A_Master         | 1             | root        | 22          |                                | 100             |
+------------+---------------+-------+------------------+-----------------+----------------------+---------------+-------------+-------------+--------------------------------+-----------------+
3 rows in set (0.00 sec)

mysql> dbscale enable dataserver normal_0_1;
Query OK, 0 rows affected (0.01 sec)


select * from information_Schema.processlist where command != ‘sleep’;
```




