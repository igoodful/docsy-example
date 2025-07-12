---
title: 03. 查看状态
description: 查看状态
date: 2023-10-30
weight: 10001


---

{{< alert >}}


{{< /alert >}}

```sql
dbscale show dataservers\G;
dbscale request cluster info\G;
dbscale show datasource type =replication\G;
dbscale show datasource type=server\G;

dbscale show options like 'transparent-mode';
dbscale show options like 'slave-dbscale-mode';
dbscale show options like 'enable-read-only';
dbscale show options like '%enable-slave-dbscale-server%';
dbscale set global 'slave-dbscale-mode'=0/1;
dbscale set global 'enable-read-only' = 0/1;
dbscale set global 'enable-slave-dbscale-server'=1/0;

show slave status\G;
show master status\G;
stop slave;


dbscale dynamic remove datasource slave_dbscale_source;
dbscale dynamic remove dataserver slave_dbscale_server;
dbscale dynamic add dataserver server_name=slave_dbscale_server,server_host="172.17.135.83",server_port=16310,server_user="admin",server_password="123456",dbscale_server;
dbscale dynamic add server datasource slave_dbscale_source slave_dbscale_server-1-1000-400-800 group_id =207;
dbscale dynamic add slave slave_dbscale_source to normal_0;

dbscale enable dataserver normal_0_0/normal_0_1/normal_0_2/slave_dbscale_server;
dbscale disable dataserver normal_0_0/normal_0_1/normal_0_2/slave_dbscale_server;
```






#### dataservers
```sql
mysql> dbscale show dataservers;
+----------------------+---------------+-------+------------------+---------------+----------------------+---------------+-------------+-------------+--------------------------------+-----------------+
| servername           | host          | port  | username         | status        | master-online-status | master_backup | remote_user | remote_port | max_needed_conn/max_mysql_conn | master_priority |
+----------------------+---------------+-------+------------------+---------------+----------------------+---------------+-------------+-------------+--------------------------------+-----------------+
| normal_0_0           | 172.17.135.83 | 16315 | dbscale_internal | Slave normal  | Not_A_Master         | 1             | root        | 22          |                                | 0               |
| normal_0_1           | 172.17.135.84 | 16315 | dbscale_internal | Server normal | Master_Online        | 1             | root        | 22          |                                | 0               |
| normal_0_2           | 172.17.135.85 | 16315 | dbscale_internal | Slave normal  | Not_A_Master         | 1             | root        | 22          |                                | 0               |
| slave_dbscale_server | 172.17.137.30 | 16310 | admin            | Slave normal  | Not_A_Master         | 1             |             |             |                                | 0               |
+----------------------+---------------+-------+------------------+---------------+----------------------+---------------+-------------+-------------+--------------------------------+-----------------+
4 rows in set (0.00 sec)

```

#### request cluster info
```sql
mysql> dbscale request cluster info\G
*************************** 1. row ***************************
          master_dbscale: master
       cluster_server_id: 1
                    host: 172.17.135.85:16310
               join time: 2024-03-25 10:44:50
         ka init Version: 27
       ka update Version: 84|84
    dynamic node Version: 1
   dynamic space Version: 47
master re-scramble delay: 1
         dbscale version: GreatDBRouter-6.0.0.6026.d9998cb6.with_spark.with_ssl
*************************** 2. row ***************************
          master_dbscale: slave
       cluster_server_id: 4
                    host: 172.17.135.83:16310
               join time: 2024-03-26 16:34:46
         ka init Version: 25
       ka update Version: 84|84
    dynamic node Version: 1
   dynamic space Version: 47
master re-scramble delay: 1
         dbscale version: GreatDBRouter-6.0.0.6026.d9998cb6.with_spark.with_ssl
*************************** 3. row ***************************
          master_dbscale: slave
       cluster_server_id: 3
                    host: 172.17.135.84:16310
               join time: 2024-03-25 10:45:13
         ka init Version: 1
       ka update Version: 84|84
    dynamic node Version: 1
   dynamic space Version: 47
master re-scramble delay: 1
         dbscale version: GreatDBRouter-6.0.0.6026.d9998cb6.with_spark.with_ssl
3 rows in set (0.01 sec)

```




#### datasource type=replication

```sql
mysql> dbscale show datasource type=replication\G
*************************** 1. row ***************************
                   Name: normal_0
                   Type: Replication
           SourceStatus: Working
    LoadBalanceStrategy: MASTER
           MasterSource: normal_0_normal_0_1
     MasterSourceStatus: Working
           MasterServer: normal_0_1
   MasterServerHostInfo: 172.17.135.84:16315
     MasterServerStatus: Server normal
       SelectMasterTime: 2024-03-26 16:33:47
            SlaveSource: normal_0_normal_0_0
      SlaveSourceStatus: Working
           SlaveServers: normal_0_0
    SlaveServerHostInfo: 172.17.135.83:16315
      SlaveServerStatus: Slave normal
SlaveExecutedGtidStatus: GTID good
           SlaveDBScale: No
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
    SlaveServerHostInfo: 172.17.135.85:16315
      SlaveServerStatus: Slave normal
SlaveExecutedGtidStatus: GTID good
           SlaveDBScale: No
*************************** 3. row ***************************
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
            SlaveSource: slave_dbscale_source
      SlaveSourceStatus: Working
           SlaveServers:
    SlaveServerHostInfo:
      SlaveServerStatus:
SlaveExecutedGtidStatus:
           SlaveDBScale: Yes
3 rows in set (0.01 sec)

```

#### datasource type=server
```sql
mysql> dbscale show datasource type=server;
+----------------------+--------+----------------------+---------------------+---------------+--------------+-------------+
| Name                 | Type   | Server               | ServerHostInfo      | ServerStatus  | SourceStatus | Is-internal |
+----------------------+--------+----------------------+---------------------+---------------+--------------+-------------+
| normal_0_normal_0_0  | Server | normal_0_0           | 172.17.135.83:16315 | Slave normal  | Working      | YES         |
| normal_0_normal_0_1  | Server | normal_0_1           | 172.17.135.84:16315 | Server normal | Working      | YES         |
| normal_0_normal_0_2  | Server | normal_0_2           | 172.17.135.85:16315 | Slave normal  | Working      | YES         |
| slave_dbscale_source | Server | slave_dbscale_server |                     | Slave normal  | Working      | NO          |
+----------------------+--------+----------------------+---------------------+---------------+--------------+-------------+
4 rows in set (0.01 sec)

```

