---
title: 灾备切换之flashback
description: 灾备切换之flashback
date: 2023-10-30
weight: 30000


viz: true
---

## 架构

```viz-dot
digraph "cluster" {
        nodesep = 0.5;
        start=2;
        ranksep = 1;
        rankdir=TB;
        compound=true;

        edge [
                fontsize="12";

        ];
        node[
                shape=octagon
                fontsize = "12"
        ];

        subgraph cluster_router {
		label = "router";
                router_master[label="M"];
                router_slave1[label="S1"];
                router_slave2[label="S2"];
	}

        subgraph cluster_zookeeper {
		label = "zookeeper";
                zookeeper_master[label="M"];
                zookeeper_slave1[label="S1"];
                zookeeper_slave2[label="S2"];
	}

        subgraph cluster_db {
		label = "db";
                db_master[label="M"];
                db_slave1[label="S1"];
                db_slave2[label="S2"];

	}

        "zookeeper_master" -> "zookeeper_slave1";
        "zookeeper_master" -> "zookeeper_slave2";

        "router_master" -> "router_slave1";
        "router_master" -> "router_slave2";

        "db_master" -> "db_slave1";
        "db_master" -> "db_slave2";

        "router_slave1" -> db_master [lhead="cluster_db" ltail="cluster_router" dir=both];
        "router_slave2" -> zookeeper_master [lhead="cluster_zookeeper" ltail="cluster_router" dir=both];

}
```

## 重要SQL


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


## start
```bash
[root@node83 ~]# ./giogii_arm -si '172.17.135.83:16310' -ti '172.17.137.30:16310'  -f start -config flashback.ini
2024-04-09T15:13:58.977024+08:00 [        dr_flashback.go: 129] [ID:1] [ INFO] verify ready
2024-04-09T15:13:58.977182+08:00 [        dr_flashback.go: 130] [ID:1] [ INFO] verify whole process consists of 7 steps
2024-04-09T15:13:58.996891+08:00 [          driver_sql.go:  63] [ID:1] [DEBUG] dbscale request cluster info
2024-04-09T15:13:58.998243+08:00 [          driver_sql.go:  63] [ID:1] [DEBUG] dbscale show dataservers
2024-04-09T15:13:59.025152+08:00 [          driver_sql.go:  63] [ID:1] [DEBUG] dbscale request cluster info
2024-04-09T15:13:59.027448+08:00 [          driver_sql.go:  63] [ID:1] [DEBUG] dbscale show dataservers
2024-04-09T15:13:59.027564+08:00 [        dr_flashback.go: 136] [ID:1] [ INFO] Step 01 parse request parameter success
2024-04-09T15:13:59.046294+08:00 [        dr_flashback.go: 156] [ID:1] [ INFO] Step 02 create 172.17.135.83 sql conn success
2024-04-09T15:13:59.049782+08:00 [        dr_flashback.go: 156] [ID:1] [ INFO] Step 02 create 172.17.135.84 sql conn success
2024-04-09T15:13:59.054440+08:00 [        dr_flashback.go: 156] [ID:1] [ INFO] Step 02 create 172.17.135.85 sql conn success
2024-04-09T15:13:59.055999+08:00 [        dr_flashback.go: 156] [ID:1] [ INFO] Step 02 create 172.17.137.33 sql conn success
2024-04-09T15:13:59.058811+08:00 [        dr_flashback.go: 166] [ID:1] [ INFO] Step 02 create 172.17.137.33 rpc conn success
2024-04-09T15:13:59.237122+08:00 [        dr_flashback.go: 173] [ID:1] [ INFO] Step 02 create 172.17.137.33 sys conn success
2024-04-09T15:13:59.257061+08:00 [        dr_flashback.go: 156] [ID:1] [ INFO] Step 02 create 172.17.137.30 sql conn success
2024-04-09T15:13:59.258173+08:00 [        dr_flashback.go: 166] [ID:1] [ INFO] Step 02 create 172.17.137.30 rpc conn success
2024-04-09T15:13:59.333520+08:00 [        dr_flashback.go: 173] [ID:1] [ INFO] Step 02 create 172.17.137.30 sys conn success
2024-04-09T15:13:59.336246+08:00 [        dr_flashback.go: 156] [ID:1] [ INFO] Step 02 create 172.17.137.31 sql conn success
2024-04-09T15:13:59.336701+08:00 [        dr_flashback.go: 166] [ID:1] [ INFO] Step 02 create 172.17.137.31 rpc conn success
2024-04-09T15:13:59.473471+08:00 [        dr_flashback.go: 173] [ID:1] [ INFO] Step 02 create 172.17.137.31 sys conn success
2024-04-09T15:13:59.481617+08:00 [          driver_sql.go:  63] [ID:1] [DEBUG] dbscale show dataservers
2024-04-09T15:13:59.481783+08:00 [        dr_flashback.go: 183] [ID:1] [ INFO] Step 03 no server down , check dataserver status success
2024-04-09T15:13:59.482635+08:00 [          driver_sql.go:  63] [ID:1] [DEBUG] dbscale show dataservers
2024-04-09T15:13:59.483577+08:00 [        dr_flashback.go: 190] [ID:1] [ INFO] Step 04 slave_dbscale_server exists , check secondary cluster exists success
2024-04-09T15:13:59.487375+08:00 [          driver_sql.go:  81] [ID:1] [DEBUG] show master status
2024-04-09T15:13:59.487499+08:00 [   protocol_node_api.go: 364] [ID:1] [DEBUG] Source ExecutedGtidSet: 82c404e1-ea51-11ee-a1b2-00163e0bbef4:1-27,82ddfeaa-ea51-11ee-88eb-00163e311b0d:1-102018,c78a42f5-ea64-11ee-86be-00163e4e3cd6:1-14826
2024-04-09T15:13:59.496328+08:00 [          driver_sql.go:  81] [ID:1] [DEBUG] show master status
2024-04-09T15:13:59.496446+08:00 [   protocol_node_api.go: 370] [ID:1] [DEBUG] Target ExecutedGtidSet: 82c404e1-ea51-11ee-a1b2-00163e0bbef4:1-27,82ddfeaa-ea51-11ee-88eb-00163e311b0d:1-102018,c78a42f5-ea64-11ee-86be-00163e4e3cd6:1-14826
2024-04-09T15:13:59.501052+08:00 [          driver_sql.go: 101] [ID:1] [DEBUG] select gtid_subtract('82c404e1-ea51-11ee-a1b2-00163e0bbef4:1-27,
82ddfeaa-ea51-11ee-88eb-00163e311b0d:1-102018,
c78a42f5-ea64-11ee-86be-00163e4e3cd6:1-14826','82c404e1-ea51-11ee-a1b2-00163e0bbef4:1-27,
82ddfeaa-ea51-11ee-88eb-00163e311b0d:1-102018,
c78a42f5-ea64-11ee-86be-00163e4e3cd6:1-14826') as Variable_name
2024-04-09T15:13:59.501180+08:00 [        dr_flashback.go: 197] [ID:1] [ INFO] Step 05 primary contains secondary gtidset , check gtid set consistency success
2024-04-09T15:14:00.079617+08:00 [          driver_sys.go: 101] [ID:1] [DEBUG] df -h /data/mysqldata/ | awk 'NR>1 {print $(NF-1)}'
2024-04-09T15:14:00.079780+08:00 [        dr_flashback.go: 204] [ID:1] [ INFO] Step 06 use ssh check master cluster storage space success
2024-04-09T15:14:00.376083+08:00 [          driver_sys.go: 101] [ID:1] [DEBUG] df -h /data/mysqldata/ | awk 'NR>1 {print $(NF-1)}'
2024-04-09T15:14:00.376290+08:00 [        dr_flashback.go: 211] [ID:1] [ INFO] Step 06 use ssh check standby cluster storage space success
2024-04-09T15:14:00.383395+08:00 [          driver_sql.go:  81] [ID:1] [DEBUG] show slave status
2024-04-09T15:14:00.383574+08:00 [        dr_flashback.go: 218] [ID:1] [ INFO] Step 07 the standby cluster have no delay ,check standby cluster seconds behind master success
2024-04-09T15:14:00.383662+08:00 [        dr_flashback.go: 220] [ID:1] [ INFO] verify over
2024-04-09T15:14:00.384296+08:00 [        dr_flashback.go: 596] [ID:1] [ INFO] start ready
2024-04-09T15:14:00.384375+08:00 [        dr_flashback.go: 597] [ID:1] [ INFO] start whole process consists of 7 steps
2024-04-09T15:14:00.408943+08:00 [          driver_sql.go:  63] [ID:1] [DEBUG] dbscale request cluster info
2024-04-09T15:14:00.410567+08:00 [          driver_sql.go:  63] [ID:1] [DEBUG] dbscale show dataservers
2024-04-09T15:14:00.442930+08:00 [          driver_sql.go:  63] [ID:1] [DEBUG] dbscale request cluster info
2024-04-09T15:14:00.444558+08:00 [          driver_sql.go:  63] [ID:1] [DEBUG] dbscale show dataservers
2024-04-09T15:14:00.444663+08:00 [        dr_flashback.go: 603] [ID:1] [ INFO] Step 01 parse request parameter success
2024-04-09T15:14:00.450011+08:00 [        dr_flashback.go: 619] [ID:1] [ INFO] Step 02 create 172.17.135.83 sql conn success
2024-04-09T15:14:00.457315+08:00 [        dr_flashback.go: 626] [ID:1] [ INFO] Step 02 create 172.17.137.30 sql conn success
2024-04-09T15:14:00.458945+08:00 [          driver_sql.go:  81] [ID:1] [DEBUG] dbscale show options like 'transparent-mode'
2024-04-09T15:14:00.460522+08:00 [          driver_sql.go:  81] [ID:1] [DEBUG] dbscale show options like 'transparent-mode'
2024-04-09T15:14:00.460623+08:00 [        dr_flashback.go: 658] [ID:1] [ INFO] Step 03 check current instance success , transpanert-mode = null
2024-04-09T15:14:00.460754+08:00 [        dr_flashback.go: 667] [ID:1] [ INFO] Step 04 donor node is 172.17.137.30  success
2024-04-09T15:14:00.462782+08:00 [          driver_sql.go:  63] [ID:1] [DEBUG] dbscale show dataservers
2024-04-09T15:14:00.462881+08:00 [        dr_flashback.go: 677] [ID:1] [ INFO] Step 05 verify B is A slave success
2024-04-09T15:14:01.464951+08:00 [          driver_sql.go:  63] [ID:1] [DEBUG] dbscale show dataservers
2024-04-09T15:14:01.465164+08:00 [        dr_flashback.go: 685] [ID:1] [ INFO] Step 06 check replication relationship success
2024-04-09T15:14:02.469375+08:00 [          driver_sql.go:  63] [ID:1] [DEBUG] dbscale show dataservers
2024-04-09T15:14:02.469573+08:00 [        dr_flashback.go: 693] [ID:1] [ INFO] Step 07 check dataserver status success
2024-04-09T15:14:03.591065+08:00 [          driver_sql.go: 119] [ID:1] [DEBUG] dbscale dynamic remove datasource slave_dbscale_source
2024-04-09T15:14:03.823962+08:00 [          driver_sql.go: 119] [ID:1] [DEBUG] dbscale dynamic remove dataserver slave_dbscale_server
2024-04-09T15:14:03.955041+08:00 [          driver_sql.go: 119] [ID:1] [DEBUG] dbscale set global 'slave-dbscale-mode'=1
2024-04-09T15:14:03.955217+08:00 [        dr_flashback.go: 701] [ID:1] [ INFO] Step 08 remove standby cluster success
2024-04-09T15:14:04.995969+08:00 [          driver_sql.go: 119] [ID:1] [DEBUG] stop slave
2024-04-09T15:14:04.996173+08:00 [        dr_flashback.go: 709] [ID:1] [ INFO] Step 09 stop replica success
2024-04-09T15:14:06.000801+08:00 [          driver_sql.go:  81] [ID:1] [DEBUG] show master status
2024-04-09T15:14:06.007859+08:00 [          driver_sql.go:  81] [ID:1] [DEBUG] show slave status
2024-04-09T15:14:07.013559+08:00 [          driver_sql.go: 101] [ID:1] [DEBUG] select gtid_subtract('82c404e1-ea51-11ee-a1b2-00163e0bbef4:1-27,82ddfeaa-ea51-11ee-88eb-00163e311b0d:1-102018,c78a42f5-ea64-11ee-86be-00163e4e3cd6:1-14826','82c404e1-ea51-11ee-a1b2-00163e0bbef4:1-27,82ddfeaa-ea51-11ee-88eb-00163e311b0d:1-102018,c78a42f5-ea64-11ee-86be-00163e4e3cd6:1-14826') as Variable_name
2024-04-09T15:14:07.013754+08:00 [        dr_flashback.go: 739] [ID:1] [ INFO] source cluster GTID:[ 82c404e1-ea51-11ee-a1b2-00163e0bbef4:1-27,82ddfeaa-ea51-11ee-88eb-00163e311b0d:1-102018,c78a42f5-ea64-11ee-86be-00163e4e3cd6:1-14826 ]
2024-04-09T15:14:07.013846+08:00 [        dr_flashback.go: 740] [ID:1] [ INFO] target cluster GTID:[ 82c404e1-ea51-11ee-a1b2-00163e0bbef4:1-27,82ddfeaa-ea51-11ee-88eb-00163e311b0d:1-102018,c78a42f5-ea64-11ee-86be-00163e4e3cd6:1-14826 ]
2024-04-09T15:14:07.013935+08:00 [        dr_flashback.go: 741] [ID:1] [ INFO] the standby cluster delay done
2024-04-09T15:14:07.014022+08:00 [        dr_flashback.go: 745] [ID:1] [ INFO] Step 10 waiting for standby cluster master node apply master cluster binlog success
2024-04-09T15:14:08.017057+08:00 [          driver_sql.go:  81] [ID:1] [DEBUG] show master status
2024-04-09T15:14:08.017255+08:00 [        dr_flashback.go: 758] [ID:1] [DEBUG] the standby cluster master node gtid set is  82c404e1-ea51-11ee-a1b2-00163e0bbef4:1-27,82ddfeaa-ea51-11ee-88eb-00163e311b0d:1-102018,c78a42f5-ea64-11ee-86be-00163e4e3cd6:1-14826
2024-04-09T15:14:08.023124+08:00 [          driver_sql.go:  81] [ID:1] [DEBUG] show master status
2024-04-09T15:14:08.023263+08:00 [        dr_flashback.go: 767] [ID:1] [DEBUG] the standby cluster slave node gtid set is  82c404e1-ea51-11ee-a1b2-00163e0bbef4:1-27,82ddfeaa-ea51-11ee-88eb-00163e311b0d:1-102018,c78a42f5-ea64-11ee-86be-00163e4e3cd6:1-14826
2024-04-09T15:14:08.026467+08:00 [          driver_sql.go:  81] [ID:1] [DEBUG] show master status
2024-04-09T15:14:08.026579+08:00 [        dr_flashback.go: 767] [ID:1] [DEBUG] the standby cluster slave node gtid set is  82c404e1-ea51-11ee-a1b2-00163e0bbef4:1-27,82ddfeaa-ea51-11ee-88eb-00163e311b0d:1-102018,c78a42f5-ea64-11ee-86be-00163e4e3cd6:1-14826
2024-04-09T15:14:08.026712+08:00 [        dr_flashback.go: 778] [ID:1] [ INFO] Step 11 waiting for standby cluster slave node apply master node binlog success
2024-04-09T15:14:09.994057+08:00 [          driver_sql.go: 119] [ID:1] [DEBUG] dbscale disable dataserver normal_0_1
2024-04-09T15:14:09.994293+08:00 [        dr_flashback.go: 785] [ID:1] [ INFO] Step 12 disable donor node success
2024-04-09T15:14:11.102346+08:00 [          driver_sql.go: 119] [ID:1] [DEBUG] dbscale set global "enable-read-only" = 0
2024-04-09T15:14:11.102576+08:00 [        dr_flashback.go: 800] [ID:1] [ INFO] Step 12 set read write success
2024-04-09T15:14:12.108267+08:00 [          driver_sql.go:  81] [ID:1] [DEBUG] show variables like 'datadir'
2024-04-09T15:14:12.134063+08:00 [          driver_sql.go:  81] [ID:1] [DEBUG] show variables like 'datadir'
2024-04-09T15:14:12.145890+08:00 [          driver_sql.go:  81] [ID:1] [DEBUG] show variables like 'datadir'
2024-04-09T15:14:12.146628+08:00 [         driver_file.go:  80] [ID:1] [DEBUG] write to gio_flashback_redo.log file successfully
2024-04-09T15:14:12.147313+08:00 [        dr_flashback.go: 842] [ID:1] [ INFO] Step 13 write redo success
2024-04-09T15:14:13.151364+08:00 [          driver_sql.go:  81] [ID:1] [DEBUG] SELECT PLUGIN_NAME,PLUGIN_STATUS FROM INFORMATION_SCHEMA.PLUGINS WHERE PLUGIN_NAME='clone'
2024-04-09T15:14:13.157552+08:00 [          driver_sql.go:  81] [ID:1] [DEBUG] SELECT PLUGIN_NAME,PLUGIN_STATUS FROM INFORMATION_SCHEMA.PLUGINS WHERE PLUGIN_NAME='clone'
2024-04-09T15:14:13.161744+08:00 [          driver_sql.go:  81] [ID:1] [DEBUG] SELECT PLUGIN_NAME,PLUGIN_STATUS FROM INFORMATION_SCHEMA.PLUGINS WHERE PLUGIN_NAME='clone'
2024-04-09T15:14:13.168513+08:00 [          driver_sql.go:  81] [ID:1] [DEBUG] show variables like 'basedir'
2024-04-09T15:14:13.178133+08:00 [          driver_sql.go:  81] [ID:1] [DEBUG] show variables like 'datadir'
2024-04-09T15:14:13.541043+08:00 [          driver_sys.go: 101] [ID:1] [DEBUG] ps -ef | grep -v grep | grep mysqld | grep clonebackup | awk '{print $2}' | xargs
2024-04-09T15:14:19.645260+08:00 [          driver_sys.go: 101] [ID:1] [DEBUG] rm -rf /data/mysqldata/clonebackup/
2024-04-09T15:14:22.814007+08:00 [          driver_sys.go: 101] [ID:1] [DEBUG] mkdir -p /data/mysqldata/clonebackup/{logfile,dbdata,tmp,socket,pid}
2024-04-09T15:14:25.922442+08:00 [          driver_sys.go: 101] [ID:1] [DEBUG] cp /data/mysqldata/16315fff-d20e-4dd9-af85-76de1a24160f/*.conf /data/mysqldata/clonebackup/new.conf
2024-04-09T15:14:29.008579+08:00 [          driver_sys.go: 101] [ID:1] [DEBUG] sed -i 's/16315fff-d20e-4dd9-af85-76de1a24160f/clonebackup/g' /data/mysqldata/clonebackup/new.conf
2024-04-09T15:14:32.407383+08:00 [          driver_sys.go: 101] [ID:1] [DEBUG] echo -e 'port = 18000
innodb_buffer_pool_size = 512MB' >> /data/mysqldata/clonebackup/new.conf
2024-04-09T15:14:57.063462+08:00 [          driver_sys.go: 101] [ID:1] [DEBUG] /data/app/mysql-8.0.26/bin/mysqld --defaults-file=/data/mysqldata/clonebackup/new.conf --initialize-insecure --user=mysql
2024-04-09T15:15:02.283534+08:00 [          driver_sys.go: 101] [ID:1] [DEBUG] nohup /data/app/mysql-8.0.26/bin/mysqld_safe --defaults-file=/data/mysqldata/clonebackup/new.conf --user=mysql --datadir=/data/mysqldata/clonebackup/dbdata > /data/mysqldata/clonebackup/logfile/out.log 2>&1 &
2024-04-09T15:16:03.083237+08:00 [          driver_sys.go: 101] [ID:1] [DEBUG] /data/app/mysql-8.0.26/bin/mysql --defaults-file=/data/mysqldata/clonebackup/new.conf -uroot -e "create user if not exists root@'%' identified with mysql_native_password by 'root';grant all on *.* to root@'%' with grant option;"
2024-04-09T15:16:06.121215+08:00 [        dr_flashback.go: 982] [ID:1] [ INFO] Step 15 init clone datadir success
2024-04-09T15:16:06.161787+08:00 [          driver_sql.go:  81] [ID:1] [DEBUG] show variables like 'basedir'
2024-04-09T15:16:06.194420+08:00 [          driver_sql.go:  81] [ID:1] [DEBUG] show variables like 'datadir'
2024-04-09T15:16:07.120250+08:00 [          driver_sys.go: 101] [ID:1] [DEBUG] ps -ef | grep -v grep | grep mysqld | grep clonebackup | awk '{print $2}' | xargs
2024-04-09T15:16:13.322241+08:00 [          driver_sys.go: 101] [ID:1] [DEBUG] rm -rf /data/mysqldata/clonebackup/
2024-04-09T15:16:16.749867+08:00 [          driver_sys.go: 101] [ID:1] [DEBUG] mkdir -p /data/mysqldata/clonebackup/{logfile,dbdata,tmp,socket,pid}
2024-04-09T15:16:20.001844+08:00 [          driver_sys.go: 101] [ID:1] [DEBUG] cp /data/mysqldata/16315fff-e6ab-4549-915b-cc9ab3510c74/*.conf /data/mysqldata/clonebackup/new.conf
2024-04-09T15:16:23.936099+08:00 [          driver_sys.go: 101] [ID:1] [DEBUG] sed -i 's/16315fff-e6ab-4549-915b-cc9ab3510c74/clonebackup/g' /data/mysqldata/clonebackup/new.conf
2024-04-09T15:16:27.052098+08:00 [          driver_sys.go: 101] [ID:1] [DEBUG] echo -e 'port = 18000
innodb_buffer_pool_size = 512MB' >> /data/mysqldata/clonebackup/new.conf
2024-04-09T15:16:53.368402+08:00 [          driver_sys.go: 101] [ID:1] [DEBUG] /data/app/mysql-8.0.26/bin/mysqld --defaults-file=/data/mysqldata/clonebackup/new.conf --initialize-insecure --user=mysql
2024-04-09T15:16:58.696351+08:00 [          driver_sys.go: 101] [ID:1] [DEBUG] nohup /data/app/mysql-8.0.26/bin/mysqld_safe --defaults-file=/data/mysqldata/clonebackup/new.conf --user=mysql --datadir=/data/mysqldata/clonebackup/dbdata > /data/mysqldata/clonebackup/logfile/out.log 2>&1 &
2024-04-09T15:17:59.467081+08:00 [          driver_sys.go: 101] [ID:1] [DEBUG] /data/app/mysql-8.0.26/bin/mysql --defaults-file=/data/mysqldata/clonebackup/new.conf -uroot -e "create user if not exists root@'%' identified with mysql_native_password by 'root';grant all on *.* to root@'%' with grant option;"
2024-04-09T15:18:02.477187+08:00 [        dr_flashback.go: 982] [ID:1] [ INFO] Step 15 init clone datadir success
2024-04-09T15:18:02.512252+08:00 [          driver_sql.go: 119] [ID:1] [DEBUG] set global super_read_only = 0
2024-04-09T15:18:02.740809+08:00 [          driver_sql.go: 152] [ID:1] [DEBUG] install plugin clone SONAME 'mysql_clone.so'
2024-04-09T15:18:02.749943+08:00 [          driver_sql.go: 119] [ID:1] [DEBUG] set global clone_autotune_concurrency = off
2024-04-09T15:18:02.756587+08:00 [          driver_sql.go: 119] [ID:1] [DEBUG] set global clone_buffer_size=33554432
2024-04-09T15:18:02.760831+08:00 [          driver_sql.go: 119] [ID:1] [DEBUG] set global clone_max_concurrency=32
2024-04-09T15:18:02.766057+08:00 [          driver_sql.go: 119] [ID:1] [DEBUG] set global clone_valid_donor_list = '172.17.137.30:16315'
2024-04-09T15:18:14.629450+08:00 [          driver_sql.go: 119] [ID:1] [DEBUG] CLONE INSTANCE FROM 'admin'@'172.17.137.30':16315 IDENTIFIED BY '123456'
2024-04-09T15:18:14.629668+08:00 [        dr_flashback.go:1043] [ID:1] [ INFO] Step 16 clone donor node success
2024-04-09T15:18:14.730490+08:00 [          driver_sql.go: 119] [ID:1] [DEBUG] set global super_read_only = 0
2024-04-09T15:18:14.952089+08:00 [          driver_sql.go: 152] [ID:1] [DEBUG] install plugin clone SONAME 'mysql_clone.so'
2024-04-09T15:18:14.958888+08:00 [          driver_sql.go: 119] [ID:1] [DEBUG] set global clone_autotune_concurrency = off
2024-04-09T15:18:14.966216+08:00 [          driver_sql.go: 119] [ID:1] [DEBUG] set global clone_buffer_size=33554432
2024-04-09T15:18:14.971538+08:00 [          driver_sql.go: 119] [ID:1] [DEBUG] set global clone_max_concurrency=32
2024-04-09T15:18:14.982058+08:00 [          driver_sql.go: 119] [ID:1] [DEBUG] set global clone_valid_donor_list = '172.17.137.30:16315'
2024-04-09T15:18:34.284159+08:00 [          driver_sql.go: 119] [ID:1] [DEBUG] CLONE INSTANCE FROM 'admin'@'172.17.137.30':16315 IDENTIFIED BY '123456'
2024-04-09T15:18:34.284859+08:00 [        dr_flashback.go:1043] [ID:1] [ INFO] Step 16 clone donor node success
2024-04-09T15:18:34.284991+08:00 [        dr_flashback.go:1047] [ID:1] [ INFO] start success

```
- `cat log/gio_flashback_redo.log`内容：
```bash
target-Not_A_Master-normal_0_1
```

## stop

```bash
[root@node83 ~]# ./giogii_arm -si '172.17.135.83:16310' -ti '172.17.137.30:16310'  -f stop -config flashback.ini
2024-04-09T15:26:23.749898+08:00 [        dr_flashback.go:1052] [ID:1] [ INFO] stop ready
2024-04-09T15:26:23.753791+08:00 [        dr_flashback.go:1053] [ID:1] [ INFO] stop whole process consists of 7 steps
2024-04-09T15:26:24.296361+08:00 [          driver_sql.go:  63] [ID:1] [DEBUG] dbscale request cluster info
2024-04-09T15:26:24.305791+08:00 [          driver_sql.go:  63] [ID:1] [DEBUG] dbscale show dataservers
2024-04-09T15:26:24.351827+08:00 [          driver_sql.go:  63] [ID:1] [DEBUG] dbscale request cluster info
2024-04-09T15:26:24.359022+08:00 [          driver_sql.go:  63] [ID:1] [DEBUG] dbscale show dataservers
2024-04-09T15:26:24.359187+08:00 [        dr_flashback.go:1059] [ID:1] [ INFO] Step 01 parse request parameter success
2024-04-09T15:26:24.371743+08:00 [        dr_flashback.go:1070] [ID:1] [ INFO] Step 02 create 172.17.135.83 sql conn success
2024-04-09T15:26:24.382536+08:00 [        dr_flashback.go:1077] [ID:1] [ INFO] Step 02 create 172.17.137.30 sql conn success
2024-04-09T15:26:24.383357+08:00 [          driver_sql.go:  81] [ID:1] [DEBUG] dbscale show options like 'transparent-mode'
2024-04-09T15:26:24.384312+08:00 [          driver_sql.go:  81] [ID:1] [DEBUG] dbscale show options like 'transparent-mode'
2024-04-09T15:26:24.588651+08:00 [          driver_sql.go: 119] [ID:1] [DEBUG] dbscale set global "enable-read-only" = 1
2024-04-09T15:26:24.644738+08:00 [          driver_sql.go:  81] [ID:1] [DEBUG] show master status
2024-04-09T15:26:24.791111+08:00 [          driver_sql.go:  81] [ID:1] [DEBUG] show master status
2024-04-09T15:26:24.904350+08:00 [          driver_sql.go:  81] [ID:1] [DEBUG] show master status
2024-04-09T15:26:24.904624+08:00 [        dr_flashback.go:1163] [ID:1] [ INFO] recovery 172.17.137.31 node ready
2024-04-09T15:26:24.904749+08:00 [        dr_flashback.go:1163] [ID:1] [ INFO] recovery 172.17.137.33 node ready
2024-04-09T15:26:24.909470+08:00 [        dr_flashback.go:1198] [ID:56] [ INFO] shutdown instance ready
2024-04-09T15:26:24.910528+08:00 [        dr_flashback.go:1198] [ID:57] [ INFO] shutdown instance ready
2024-04-09T15:26:24.921705+08:00 [          driver_sql.go:  81] [ID:56] [DEBUG] show variables like 'datadir'
2024-04-09T15:26:24.937447+08:00 [          driver_sql.go:  81] [ID:56] [DEBUG] show variables like 'basedir'
2024-04-09T15:26:24.947397+08:00 [          driver_sql.go: 137] [ID:56] [DEBUG] shutdown
2024-04-09T15:26:25.029436+08:00 [          driver_sql.go:  81] [ID:57] [DEBUG] show variables like 'datadir'
2024-04-09T15:26:25.048927+08:00 [          driver_sql.go: 137] [ID:56] [DEBUG] shutdown
2024-04-09T15:26:25.049122+08:00 [        dr_flashback.go:1234] [ID:56] [ INFO] shutdown instance success
2024-04-09T15:26:25.155224+08:00 [          driver_sql.go:  81] [ID:57] [DEBUG] show variables like 'basedir'
2024-04-09T15:26:25.163449+08:00 [          driver_sql.go: 137] [ID:57] [DEBUG] shutdown
2024-04-09T15:26:25.185697+08:00 [          driver_sql.go: 137] [ID:57] [DEBUG] shutdown
2024-04-09T15:26:25.185882+08:00 [        dr_flashback.go:1234] [ID:57] [ INFO] shutdown instance success
2024-04-09T15:26:56.040317+08:00 [          driver_sys.go: 101] [ID:56] [DEBUG] rm -rf /data/mysqldata/16315fff-d20e-4dd9-af85-76de1a24160f/dbdata_bak
2024-04-09T15:26:56.359381+08:00 [          driver_sys.go: 101] [ID:57] [DEBUG] rm -rf /data/mysqldata/16315fff-e6ab-4549-915b-cc9ab3510c74/dbdata_bak
2024-04-09T15:27:01.150018+08:00 [          driver_sys.go: 101] [ID:56] [DEBUG] mv /data/mysqldata/16315fff-d20e-4dd9-af85-76de1a24160f/dbdata /data/mysqldata/16315fff-d20e-4dd9-af85-76de1a24160f/dbdata_bak
2024-04-09T15:27:01.472834+08:00 [          driver_sys.go: 101] [ID:57] [DEBUG] mv /data/mysqldata/16315fff-e6ab-4549-915b-cc9ab3510c74/dbdata /data/mysqldata/16315fff-e6ab-4549-915b-cc9ab3510c74/dbdata_bak
2024-04-09T15:27:06.359481+08:00 [          driver_sys.go: 101] [ID:56] [DEBUG] mv /data/mysqldata/clonebackup/dbdata /data/mysqldata/16315fff-d20e-4dd9-af85-76de1a24160f/
2024-04-09T15:27:06.589964+08:00 [          driver_sys.go: 101] [ID:57] [DEBUG] mv /data/mysqldata/clonebackup/dbdata /data/mysqldata/16315fff-e6ab-4549-915b-cc9ab3510c74/
2024-04-09T15:27:11.486345+08:00 [          driver_sys.go: 101] [ID:56] [DEBUG] nohup /data/app/mysql-8.0.26/bin/mysqld_safe --defaults-file=/data/mysqldata/16315fff-d20e-4dd9-af85-76de1a24160f/my16315.conf --user=mysql --datadir=/data/mysqldata/16315fff-d20e-4dd9-af85-76de1a24160f/dbdata > /data/mysqldata/16315fff-d20e-4dd9-af85-76de1a24160f/logfile/out.log 2>&1 &
2024-04-09T15:27:11.881590+08:00 [          driver_sys.go: 101] [ID:57] [DEBUG] nohup /data/app/mysql-8.0.26/bin/mysqld_safe --defaults-file=/data/mysqldata/16315fff-e6ab-4549-915b-cc9ab3510c74/my16315.conf --user=mysql --datadir=/data/mysqldata/16315fff-e6ab-4549-915b-cc9ab3510c74/dbdata > /data/mysqldata/16315fff-e6ab-4549-915b-cc9ab3510c74/logfile/out.log 2>&1 &
2024-04-09T15:27:41.691586+08:00 [          driver_sys.go: 101] [ID:56] [DEBUG] rm -rf /data/mysqldata/clonebackup/
2024-04-09T15:27:42.025588+08:00 [          driver_sys.go: 101] [ID:57] [DEBUG] rm -rf /data/mysqldata/clonebackup/
2024-04-09T15:28:12.105445+08:00 [          driver_sql.go: 119] [ID:1] [DEBUG] dbscale enable dataserver normal_0_1
2024-04-09T15:28:12.124177+08:00 [          driver_sql.go:  81] [ID:1] [DEBUG] dbscale request next group id
2024-04-09T15:28:12.355844+08:00 [          driver_sql.go: 119] [ID:1] [DEBUG] dbscale set global 'slave-dbscale-mode'=0
2024-04-09T15:28:13.892798+08:00 [          driver_sql.go: 119] [ID:1] [DEBUG] dbscale dynamic add dataserver server_name=slave_dbscale_server,server_host="172.17.137.30",server_port=16310,server_user="admin",server_
2024-04-09T15:28:14.186362+08:00 [          driver_sql.go: 119] [ID:1] [DEBUG] dbscale dynamic add server datasource slave_dbscale_source slave_dbscale_server-1-1000-400-800 group_id = 6
2024-04-09T15:28:17.023690+08:00 [          driver_sql.go: 119] [ID:1] [DEBUG] dbscale dynamic add slave slave_dbscale_source to normal_0
2024-04-09T15:28:17.025468+08:00 [         driver_file.go: 117] [ID:1] [DEBUG] truncate file successfully
2024-04-09T15:28:17.026225+08:00 [        dr_flashback.go:1193] [ID:1] [ INFO] stop success

```


