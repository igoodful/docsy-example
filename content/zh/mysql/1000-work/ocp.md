---
title: ocp
description: ocp
date: 2025-05-24
weight: 70000
viz: true
---

<style>
th, td ###
  border: 1px solid rgb(190, 190, 190);

</style>

&#10060;

&#9989;

$ \rightleftharpoons $

######< alert >

######< /alert >


MySQL 8.0 OCP 1Z0-908

Duration: 140 Minutes | Passing Score: 62%  Number of Questions: 85

- https://education.oracle.com/oracle-certified-professional-mysql-80-database-administrator/trackp_MYSQLDBA80OCP


### Q1.Examine this command, which executes successfully:

mysqlbackup --user=dba --password --port=3306 --with-timestamp --backup-dir=/export/backups backup-and-apply-log
Which statement is true?

- A The backup accesses the MySQL server files by using a pre-existing connection.

- B The database server is put into a read-only state for the duration of the backup.

- C An offline backup of InnoDB tables is taken.

- D The backup can be impacted when DDL operations run during the backup.

Answer: D


### Q2.You reconfigure and start a slave that was not replicating for several days.
The configuration file and CHANGE MASTER command are correct.
Examine the GTID information from both master and slave:
```sql
Master:
Gtids_executed:
aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa:1-321,
bbbbbbbb-bbbb-bbbb-bbbbbbbbbbbb:1-50,
Cccccccc-cccc-cccc-cccc-cccccccccccc:1234-1237
```

```sql
Gtids_purged:
aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa:1-100,
bbbbbbbb-bbbb-bbbb-bbbbbbbbbbbb:1-10,
cccccccc-cccc-cccc-cccc-cccccccccccc:1234-1237
```

```sql
Slave:
Gtids_executed:
aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa:1-160,
cccccccc-cccc-cccc-cccc-cccccccccccc:1234-1237
Gtids_executed:
aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa:1-70,
cccccccc-cccc-cccc-cccc-cccccccccccc:1234-1237
```

Which statement is true?

- A Replication will fail because the master does not have the required transaction with
	bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb GTIDs in its binary logs.

- B Replication will fail	because the master has already purged transactions with
	cccccccc-cccc-cccc-cccc-cccccccccc GTIDs.

- C Replication will fail because of inconsistent numbers in cccccccc-cccc-cccc-cccc-cccccccccccc GTIDs.

- D Replication will fail because the slave has purged more
	aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa transactions than the master.

- F Replication will work.

- Answer:
A

### Q3.Which two authentication plugins require the plain text client plugin for authentication to work?
- A Windows Native authentication
- B PAM authentication
- C LDAP SASL authentication
- D LDAP authentication
- E SHA256 authentication
- F MySQL Native Password
- Answer:
BD

### Q4.You want to log only the changes made to the database objects and data on the MySQL system.
Which log will do this by default?
- A error log
- B slow query log
- C general query log
- D binary log
- E audit log
- Answer:
D


### Q5.You have an installation of MySQL 8 on Oracle Linux.

Consider the outputs:
```sql
Mysql>SHOW GLOBAL VARIABLES
WHERE Varisble_name='tmpdir' OR Variable_name ='tmp_table_size';
+---------------+-----------+
| Variable_name	|Value		|
+---------------+-----------+
|tmp_table_size	| 16777216	|
|tmpdir			|   /tmp	|
+---------------+-----------+
```

```bash
Shell>cd /var/lib/mysql
Shell>ls -l|grep temp
Drwxr-x---.2 mysql mysql 4096 Dec 11 14:05 #innodb_temp
```

Which statement is true about disk temporary tables for this installation?

- A Temporary tables are created in tmpdir only if configured to use MyISAM。

- B Temporary tables are created in tmpdir only after they reach tmp_table_size.

- C Temporary tables will use the InnoDB temporary tablespace located in datadir.

- D Only internal temporary tables from the optimizer will be created in tmpdir.

- E Temporary tables will use the InnoDB temporary tablespace located in /tmp.

- Answer: C

### Q6.Which two actions will secure a MySQL server from network-based attacks?
- A Use MySQL Router to proxy connections to the MySQL server.
- B Place the MySQL instance behind a firewall.
- C Use network file system (NFS) for storing data.
- D Change the listening port to 3307.
- E Allow connections from the application server only.
- Answer:
BE

### Q7.Examine the command, which execute successfully:
shell> mysqld --initialize-insecure
Which statement is true?
- A The installation creates a temporary test environment with data in the /tmp directory.
- B The installation is created without enforcing or generating SSL certificates.
- C The root password is created in the error log in plain text.
- D The root password is not created allowing easy access from the same host.
- Answer:
D

To initialize the data directory, invoke mysqld with the --initialize or --initialize-insecure option, depending on whether you want the server to generate a random initial password for the 'root'@'localhost' account, or to create that account with no password:

Use --initialize for “secure by default” installation (that is, including generation of a random initial root passwor- D . In this case, the password is marked as expired and you must choose a new one.

With --initialize-insecure, no root password is generated. This is insecure; it is assumed that you intend to assign a password to the account in a timely fashion before putting the server into production use.


### #xxvv #Q8.Examine this command:
shell> mysqldump --no-create-info --all-databases --result-file=dump.sql
Which statement is true?
- A It will not write CREATE TABLESPACE statements.
- B It will not write CREATE LOGFILE GROUP statements.
- C It will not write CREATE TABLE statements.
- D It will not write CREATE DATABASE statements.

Answer:
C

The --no-data option tells mysqldump not to dump table data,
resulting in the dump file containing only statements to create the tables.
Conversely,相反
the --no-create-info option tells mysqldump to suppress CREATE statements from the output,
so that the dump file contains only table data.


### Q9.The mysqld instance has the connection control plugin enabled with these settings:
Connection_control_min_connection_delay=1000
Connection_control_max_connection_delay=2000
The minimum and maximum delays need to be increased to 3000 and 5000, respectively.
A command is executed:
Mysql> SET GLOBAL connection_control_min_connection_delay=3000;
What is the result?
- A The minimum connection value is changed to 2000.
- B Only the minimum connection value is increased to 3000.
- C The minimum value increases to 3000 and the maximum value increases to 4000.
- D An error is returned.
- Answer:
D

实测：
mysql> show variables like "%connection_control%";
+-------------------------------------------------+-------+
| Variable_name                                   | Value |
+-------------------------------------------------+-------+
| connection_control_failed_connections_threshold | 3     |
| connection_control_max_connection_delay         | 2000  |
| connection_control_min_connection_delay         | 1000  |
+-------------------------------------------------+-------+
3 rows in set (0.00 se- C

mysql> set global connection_control_min_connection_delay=3000;
ERROR 1231 (42000): Variable 'connection_control_min_connection_delay' cannot be set to the value of '3000'


### Q10.You must export data from a set of tables in the world_x database.
Examine this set of tables: Tables ( country, countryinfo, location)
Which two option will export data into one or more files?
- A mysql> CLONE LOCAL DATA DIRECTORY = '/var/lib/mysql/world_x/country';
mysql> CLONE LOCAL DATA DIRECTORY = '/var/lib/mysql/world_x/countryinfo';
mysql> CLONE LOCAL DATA DIRECTORY = '/var/lib/mysql/world_x/location';
- B mysql>SELECT * INTO OUTFILE '/output/contry.txt' FROM world_x.country;
mysql>SELECT * INTO OUTFILE '/output/contryinfo.txt' FROM world_x.countryinfo;
mysql>SELECT * INTO OUTFILE '/output/location.txt' FROM world_x.location;
- C shell> mysqlexport world_x country countryinfo location > mydump.sql
- D shell> mysql --batch world_x.country world_x.countryinfo world_x.location > mydump.sql
- E shell> mysqldump world_x country countryinfo location > mydump.sql
- Answer:
BE


###  Q11.Examine this command and output:

```sql
Mysql>SELECT *
FROM performance_schema.table_io_waits_summary_by_table WHERE COUNT_STAAR >0\G
********************************2.row **************************************
OBJECT_TYPE:TABLE
OBJECT_SCHEMA:test
OBJECT_NAME:demo_test

CONUNT_STAR:61567093
SUM_TIMER_WAIT:59009007572922
MIN_TIMER_WAIT:395922
AVG_TIMER_WAIT:958095
MAX_TIMER_WAIT:558852005358

COUNT_READ:38665056
SUM_TIMER_READ:20598719962188
MIN_TIMER_READ:395922
AVG_TIMER_READ:532728
MAX_TIMER_READ:558852005358

COUNT_WRITE:22902028
SUM_TIMER_WRITE:38410287610743
MIN_TIMER_WRITE:1130688
AVG_TIMER_WRITE:1677006
MAX_TIMER_WRITE:17205682920

COUNT_FETCH:38665056
SUM_TIMER_FETCH:20598719962188
MIN_TIMER_FETCH:395922
AVG_TIMER_FETCH:532728
MAX_TIMER_FETCH:558852005358

.....
COUNT_DELETE:22902028
SUM_TIMER_DELETE:38410287610743
MIN_TIMER_DELETE:1130688
AVG_TIMER_DELETE:1677006
MAX_TIMER_DELETE:17205682920
```

Which two are true?

- A The longest I/O wait was for writes.
- B I/O distribution is approximately 50/50 read/write.
- C 22902028 rows were deleted.
- D Average read times are approximately three times faster than writes.
- E The I/O average time is 532728.
- Answer:
CD


COUNT_INSERT

COUNT_DELETE

COUNT_UPDATE

都应该是行数。


### Q12.A developer accidentally dropped the InnoDB table `Customers` from the `Company` database.
There is a datadir copy from two days ago in the dbbackup directory.
Which set of steps would restore only the missing table?

- A Stop the MySQL Server process, and execute:

mysqlbackup	--datadir=/var/lib/mysql	--backup-dir=/dbbackup
--include-tables='Company\Customers'	copy-back
Start the mysqld process.

- B Stop  the  MySQL  Server  process  and  restart  it  with  the command:

mysqld	--basedir=/usr/local/mysql	-- datadir=/var/lib/mysql
Run mysqldump on this table and restore the dump file.
- C Stop  the  MySQL  Server  process  and  restart  it  with  the command:

mysqld	--basedir=/usr/local/mysql	--datadir=/dbbackup
Run mysqldump on this table and restore the dump file.
- D Stop the MySQL Server process, copy the Customers.ibd file from the dbbackup directory,

and start the mysqld process.
- Answer:
C


### Q13.There has been an accidental deletion of data in one of your MySQL databases.
You determine that all entries in the binary log file after position 1797 must be replayed.
Examine this partial command:
mysqlbinlog binlog.000008 --start-position=1798

Which operation will complete the command?
- A --write-to-remote-server must be added to the command line to update the database tables.
- B It can be piped into the MySQL Server via the command-line client.
- C You must use --stop-position=1797 to avoid the DELETE statement that caused the initial problem.
- D No changes required. It automatically updates the MySQL Server with the data.
- Answer:
B


### Q14.Table t is an InnoDB table. Examine these statements and output:
```sql
Selet count(1) from t;
+---------------+
| count(1)		|
+---------------+
|	72			|
+---------------+
Mysql>show indexes from t\G
***************************1.row*************************** Table: t
Non_unique:0
Key_name:PRIMARY
Seq_in_index: 1
Column_name: a
Collation:A
Cardinality: 72
Sub_part:NULL
Packed:NULL
Null:
Index_type:BTREE
Comment:
Index_comment:
Visible:YES
Expression:NULL
***************************2.row*************************** Table: t
Non_unique:1
Key_name:b_idx
Seq_in_index: 1
Column_name: b
Collation:A
Cardinality: 1
Sub_part:NULL
Packed:NULL
Null:YES
Index_type:BTREE Comment:
Index_comment:
Visible:NO
Expression:NULL
2 row in set (0.00 sec)
```

Which two are true?
- A ANALYZE TABLE t would update index statistics uniquely for the PRIMARY index.
- B Table t has two viable(切实可行的) indexes to be used for queries.
- C SELECT b from t would perform a table scan.
- D Index b_idx has a low number of unique values.
- E SELECT a FROM t would perform a table scan.
- Answer:
CD


### Q15.You are using the InnoDB engine and the innodb_file_per_table option is set.
You delete a significant number of rows of a large table named FACTORY.INVENTORY.
Which command will reorganize the physical storage of table data
and associated index data for the INVENTORY table,
in order to reduce storage space and improve I/O efficiency?
- A CHECK TABLE FACTORY.INVENTORY
- B ANALYZE TABLE FACTORY.INVENTORY
- C OPTIMIZE TABLE FACTORY.INVENTORY
- D mysqlcheck -u root -p FACTORY.INVENTORY
- E mysqldump -u root -p FACTORY INVENTORY

- Answer:
C


### Q16.The data in this instance transient（短暂的）; no backup or replication will be required.
It is currently under performing.
The database size is static and including indexes is 19G
Total system memory is 32G
After profiling the system,you highlight these MySQL status and global variables:
Com_rollback					|85408355 	|
Com_commit						|1234342 	|
Innodb_buffer_pool_pages_free 	|163840 	|
[mysqld]
Buffer_pool_size=20G
Innodb_flush_log_at_trx_commit=2
Disable-log-bin

The OS metrics indicate that disk is a bottleneck. Other variables retain their default values.
Which two changes will provide the most benefit to the instance?
- A Max_connections=10000
- B Innodb_log_file_size=1G
- C Sync_binlog=0
- D Innodb_doublewrite=0
- E Buffer_pool_size=24G
- F Innodb_flush_log_at_trx_commit=1

- Answer:
DE

	Innodb_buffer_pool_pages_free
	The number of free pages in the InnoDB buffer pool.单位是页面，16K

### #x Q17.You are having performance issues with MySQL instances.
Those servers are monitored with MySQL Enterprise Monitor.
Using Query Analyzer, where do you begin to look for problem queries?
- A Look for queries with big prolonged spikes in row activity/access graph in the times series graph.
- B Sort the "Exec" column and check for SQL queries with high Query Response Time index (QRTi) values.
- C Look for queries with low total latency times in the Latency section in the time series graph.
- D Sort the "Exec" column and check for SQL queries with low Query Response Time index (QRTi) values.
- Answer:
D

Query Response Time index (QRTi)
	越高越好，表示：满足查询响应时间的比率。查询响应时间缺省为100ms,可以进行调整。

### Q18.Examine the full path name of the backup image from MySQL Enterprise Backup
with the --compress option: /backup/full/mybackup/myimage.img
mysqlbackup.cnf contains this data:
```toml
[mysqlbackup]
backup-dir=/backup/full/myrestore
backup-image=/backup/full/mybackup/myimage.img
uncompress
```

You must perform a database restore to a new machine.
which command can provision the new database in datadir as /data/MEB?
- A mysqlbackup --defaults-file=mysqlbackup.cnf --datadir=/data/MEB restore-and-apply-log
- B mysqlbackup --defaults-file=mysqlbackup.cnf --datadir=/data/MEB image-to-dir-and-apply-log
- C mysqlbackup --defaults-file=mysqlbackup.cnf --datadir=/data/MEB apply-log-and-copy-back
- D mysqlbackup --defaults-file=mysqlbackup.cnf --datadir=/data/MEB copy-back-and-apply-log
- E mysqlbackup --defaults-file=mysqlbackup.cnf --datadir=/data/MEB image-to-dir
###	Answer:
D

###  Q19.All mysql server instances belonging to innodb cluster have SSL configured and enabled.
You must configure innodb cluster to use SSL for group communication.

Which two statements are ture?
- A Configuring SSL group communication also configures SSL distributed recovery.
- B SSL group communication requires the use of an additional set of parameters group_replication_recovery_*.
- C If only some innoDB cluster members are enabled for SSL group communication, and
--ssl-mode=PREFERED, communication will fail back to unencrypted connection.
- D An existing innoDB cluster must be dissolved and created form scratch to enable SSL for group communication.
- E SSL group communication can be enabled for an existing cluster,one instance at time, by setting group_replication_ssl_mode.
- F SSL group communication must be enable at cluster creation time by pecifying createCluster######membersslmode:'REQUIRED'
- Answer:
DF

<<EOF
A错
B错
	组通讯SSL和分布恢复的SSL是相互独立的。
	By default, distributed recovery connections do not use SSL,
	even if you activated SSL for group communication connections,
	and the server SSL options are not applied for distributed recovery connections.
	You must configure these connections separately.
C 错
	membersslmode=REQUIRED: Enable SSL encryption for the seed instance in the cluster.
	If it cannot be enabled, an error is raised.
	SSL encryption must be enabled or disabled for all instances in the cluster.
	Verifications are performed to ensure that this invariant holds when adding a new instance to the cluster.
E 错
F 对
https://dev.mysql.com/doc/mysql-shell/8.0/en/mysql-innodb-cluster-securing.html
只能在创建cluster时，通过membersslmode启动ssl集群。
When using dba.createCluster() to set up a cluster, if the server instance provides encryption
then it is automatically enabled on the seed instance.
Pass the memberSslMode option to the dba.createCluster() method to specify a different SSL mode.
The SSL mode of a cluster can only be set at the time of creation.
EOF

### Q20.Which two are true about differences between logical and physical upgrades of MySQL databases?
- A Post-upgrade table storage requirements after physical upgrades are usually smaller 	than that after logical upgrades.
- B Physical upgrades leave data in place,
	whereas logical upgrades require data to be restored from mysqldump-type backups taken before the upgrades.
- C Physical upgrades are much faster because they do not require restarting the mysqld process.
- D Physical upgrades are performed for current instances on bare metal deployments,
	whereas logical upgrades are used for virtual machines or containerized instances.
- E Logical upgrades are much faster because they do not require starting the mysqld process.
- F Post-upgrade table storage requirements after logical upgrades are usually smaller
	than that after physical upgrades.

- Answer:
BF


Depending on the version of MySQL to which you are upgrading,
the instructions in `In-Place Upgrade` and `Logical Upgrade` indicate
whether the server performs all upgrade tasks
or whether you must also invoke mysql_upgrade after server startup.

`Logical Upgrade`
A logical upgrade involves exporting SQL from the old MySQL instance using a backup or export utility such as mysqldump or mysqlpump,
installing the new MySQL server, and applying the SQL to your new MySQL instance.


###  Q21.You encountered an insufficient privilege error in the middle of a long transaction.
The database administrator is informed and immediately grants the required privilege:
GRANT UPDATE ON world.city TO 'user1';

How can you proceed with your transaction with the least interruption?
- A Roll back the transaction and start the transaction again in the same session.
- B Change the default database and re-execute the failed statement in your transaction.
- C Re-execute the failed statement in your transaction.
- D Close the connection, reconnect, and start the transaction again.
- Answer:
C

### #vvxxvv Q22.An existing asynchronous replication setup is running MySQL 8.
Which two steps are a part of implementing GTID replication?
- A On the slave, alter the MySQL master connection setting with:

	CHANGE MASTER TO MASTER_AUTO_POSITION = 1;
- B Execute this on the slave to enable GTID:

	RESET SLAVE; START SLAVE GTID_NEXT = AUTOMATIC;
- C Enable GTID by executing this on the master and the slave:

	SET GLOBAL GTID_ENABLED = on;
- D Restart MySQL (master and slav- E  with these options enabled:

	--gtid_mode=ON
	--log-bin
	--log-slave-updates
	--enforce-gtid-consistency
- E On the slave, alter the MySQL master connection setting with:

	ALTER channel CHANGE MASTER TO MASTER_AUTO_POSITION = 1;
- F Execute this on the slave to enable GTID:

	START SLAVE IO_THREAD WITH GTID;
- Answer:
AD


###vxxvv Q23.You are using mysqlcheck for server maintenance.
Which two statements are true?
- A The mysqlcheck --optimize --all-databases command reclaims free space from table files.
- B The mysqlcheck --analyze --all-databases command performs a series of checks to spot eventual table corruptions.
- C The mysqlcheck command can be renamed mysqlrepair so that it repairs tables by default.
- D The mysqlcheck --repair --all-databases command can repair an InnoDB corrupted table.
- E The mysqlcheck --check -all-databases command takes table write locks while performing a series of checks.
- Answer:
AC

	视频也是AC
	OPTIMIZE TABLE reorganizes the physical storage of table data and associated index data, to reduce storage space and improve I/O efficiency when accessing the table.
	OPTIMIZE TABLE works for InnoDB, MyISAM, and ARCHIVE tables.

B错
	ANALYZE TABLE generates table statistics:
	ANALYZE TABLE without either HISTOGRAM clause performs a key distribution analysis and
	stores the distribution for the named table or tables.
	For MyISAM tables, ANALYZE TABLE for key distribution analysis is equivalent to using myisamchk --analyze.
D错
	REPAIR TABLE works for MyISAM, ARCHIVE, and CSV tables.

### #vvxv Q24.Examine this statement:
mysql> DROP ROLE r_role1, r_role2;
Which two are true?
- A It fails if at least one of the roles does not exist.
- B You must revoke all privileges from r_role1 and r_role2 before dropping the roles.
- C Existing connections can continue to use the roles'' privileges until they reconnect.
- D You must revoke r_role1 and r_role2 from all users and other roles before dropping the roles.
- E It fails if you do not have the ADMIN OPTION of the roles r_role1 and r_role2.
- F It fails if any of the roles is specified in the MANDATORY_ROLES variable.
- Answer:
AF
	https://dev.mysql.com/doc/refman/8.0/en/roles.html
	Roles named in the value of mandatory_roles cannot be revoked with REVOKE
	or dropped with DROP ROLE or DROP USER.

	设置MANDATORY_ROLES中的角色，是强制授予所有用户这些角色，并不是激活该角色。
	Roles the server should treat as mandatory. In effect, these roles are automatically granted to every user,
	although setting mandatory_roles does not actually change any user accounts,
	and the granted roles are not visible in the mysql.role_edges system table.


### Q25.You have a MySQL client installed on your Linux workstation with a default installation.
You have your 'admin' login credentials to connect to a MySQL server running Microsoft Windows
 on remote host 192.0.2.1:3306 to connect to the 'world' database.
Which four options need to be specified to complete this task with a single command?
- A --port=3306
- B --protocol=UDP
- C --database=world
- D --user=admin
- E --password
- F --protocol=pipe
- G --host=192.0.2.1
- H)--socket=/tmp/mysql.sock
- I)--shared-memory-base-name=world
- Answer:
CDEG

### Q26.You want to check the values of the sort_buffer_size session variables of all existing connections.
Which performance_schema table can you query?
- A global_variables
- B session_variables
- C variables_by_thread
- D user_variables_by_thread
- Answer:
C

```sql
mysql> desc performance_schema.variables_by_thread;
+----------------+-----------------+------+-----+---------+-------+
| Field          | Type            | Null | Key | Default | Extra |
+----------------+-----------------+------+-----+---------+-------+
| THREAD_ID      | bigint unsigned | NO   | PRI | NULL    |       |
| VARIABLE_NAME  | varchar(64)     | NO   | PRI | NULL    |       |
| VARIABLE_VALUE | varchar(1024)   | YES  |     | NULL    |       |
+----------------+-----------------+------+-----+---------+-------+
3 rows in set (0.00 se- C
```

### Q27.Your my.cnf file contains these settings:
[mysqld]
log_output=FILE
slow_query_log
long_query_time=2.01
log_queries_not_using_indexes

You want to log queries that looked at a minimum of 5000 records
and either took longer than 5 seconds to run or did not use indexes.
Which contains all the settings that you need to add to or modify the slow log configuration?
- A 	log_throttle_queries_not_using_indexes=5
- B 	long_query_time=5
	log_throttle_queries_not_using_indexes=5
- C 	long_query_time=5
- D 	long_query_time=5
	log_throttle_quries_not_using_indexes=5
	min_examined_row_limit=5000
- E 	long_query_time=5
	min_examined_row_limit=5000
- F 	min_examined_row_limit=5000
- G 	log_throttle_quries_not_using_indexes=5
	min_examined_row_limit=5000
- Answer:
E

log_throttle_quries_not_using_indexes 限制每分钟写入日志的未使用索引的sql语句的数量。
min_examined_row_limit 对于查询扫描行数小于此参数的sql，将不会记录到慢查询日志中。

### Q28.Examine this command and output:
```sql
mysql>SELECT * FROM data_locks LIMIT 1\G
**************************************************************************
ENGONE: INNODB
ENDINE_LOCJK_I: 1200:146
ENGINE_TRANSACTION_ID: 1200
THREAD_ID:45
EVENT_ID:11
OBJECT_SCHEMA: mydb
OBJECT_NAME: mytable1
PARTITION_NAME: NULL
SUSPARTITION_NAME: NULL
INDEX_NAME: NULL
OBJECT_INSTANCE_BEGIN:118793337250203
LOCK_TYPE: RECORD
LOCK_MODE: X
LOCK_STATUS: GRANTED
LOCK_DATA: 1922,192
```
Which two statements are true?
- A The lock is an intentional lock.
- B The lock is a shared lock.
- C The lock is at the metadata object level.
- D The lock is an exclusive lock.
- E The lock is a row-level lock.
- F The lock is at the table object level.
- Answer:
DE

	`RECORD` for a row-level lock,
	`TABLE` for a table-level lock
	A shared (`S`) lock permits the transaction that holds the lock to read a row.
	An exclusive (`X`) lock permits the transaction that holds the lock to update or delete a row.

```sql
select * from xue3 where id<2 for update;
select * from performance_schema.data_locks \G
*************************** 1. row ***************************
               ENGINE: INNODB
       ENGINE_LOCK_ID: 139677269974232:1084:139677168379024
ENGINE_TRANSACTION_ID: 32200
            THREAD_ID: 2553
             EVENT_ID: 36
        OBJECT_SCHEMA: human
          OBJECT_NAME: xue3
       PARTITION_NAME: NULL
    SUBPARTITION_NAME: NULL
           INDEX_NAME: NULL
OBJECT_INSTANCE_BEGIN: 139677168379024
            LOCK_TYPE: TABLE
            LOCK_MODE: IX
          LOCK_STATUS: GRANTED
            LOCK_DATA: NULL
*************************** 2. row ***************************
               ENGINE: INNODB
       ENGINE_LOCK_ID: 139677269974232:23:5:2:139677168375968
ENGINE_TRANSACTION_ID: 32200
            THREAD_ID: 2553
             EVENT_ID: 36
        OBJECT_SCHEMA: human
          OBJECT_NAME: xue3
       PARTITION_NAME: NULL
    SUBPARTITION_NAME: NULL
           INDEX_NAME: PRIMARY
OBJECT_INSTANCE_BEGIN: 139677168375968
            LOCK_TYPE: RECORD
            LOCK_MODE: X
          LOCK_STATUS: GRANTED
            LOCK_DATA: 1
*************************** 3. row ***************************
               ENGINE: INNODB
       ENGINE_LOCK_ID: 139677269974232:23:5:3:139677168376312
ENGINE_TRANSACTION_ID: 32200
            THREAD_ID: 2553
             EVENT_ID: 36
        OBJECT_SCHEMA: human
          OBJECT_NAME: xue3
       PARTITION_NAME: NULL
    SUBPARTITION_NAME: NULL
           INDEX_NAME: PRIMARY
OBJECT_INSTANCE_BEGIN: 139677168376312
            LOCK_TYPE: RECORD
            LOCK_MODE: X,GAP
          LOCK_STATUS: GRANTED
            LOCK_DATA: 2
3 rows in set (0.00 se- C
```

### Q29.A clean shutdown was performed with innodb_fast_shutdown=0.
While you were manipulating files, all files were accidentally deleted from the top-level data directory.
Which two files must be restored from backup to allow the DB to restart cleanly?
- A ibdata1
- B mysql.ibd
- C ib_logfile0
- D ibtmp1
- E ib_buffer_pool
- F undo_001
- Answer:
AB


### Q30.Which two statements are true about MySQL Enterprise Backup?
- A It supports the creation of incremental backups.
- B It creates logical backups.
- C It supports restoring to a remote MySQL system.
- D It supports backing up only table structures.
- E It supports backup of a remote MySQL system.
- F It can perform hot or warm backups.
- Answer:
AF


###  Q31.What is the correct syntax for using transparent data encryption with an existing InnoDB table?
- A ALTER TABLE t1 ENCRYPTION='Y';
- B ALTER TABLE t1 WITH ENCRYPTION USING MASTER KEY;
- C ALTER TABLE t1 SET TDE = 'ON';
- D ALTER TABLE t1 ADD ENCRYPTED_TABLESPACE = 'Y';

- Answer:
A

### Q32.Examine these statements and output:
```sql
Mysql>GRANT PROXY ON accounting@locahost TO ' '@'%';
Mysql>SELECT USER(),CURRENT_USER(),@@PROXY_USER;

+-------------------+----------------------	+---------------+
|USER()				|CURRENT_USER()			|@@proxy_user	|
+------------------	+----------------------	+---------------+
|jsmith@loaclhost 	| accounting@localhost 	|''@'%' 		|
+------------------	+----------------------	+---------------+
```

Which statement is true?
- A The user failed to define a username and the connecting username defaulted to ' '@'%'.
- B The user is authorized as the rsmith@localhost user.
- C The user is authenticated as the anonymous proxy user ''@'%'.
- D The user is authorized as the accounting@localhost user.
- E The user is logged in with --user=accounting as an option.
- Answer:
D


### Q33.Which are three benefits of using mysqlbackup instead of mysqldump?
- A mysqlbackup restores data from physical backups, which are faster than logical backups.
- B mysqlbackup can back up tables with the InnoDB engine without blocking reducing wait times due to contention.
- C mysqlbackup allows logical backups with concurrency resulting in faster backups and restores.
- D mysqlbackup does not back up MySQL system tables, which shortens backup time.
- E mysqlbackup can perform partial backup of stored programs.
- F mysqlbackup integrates tape backup and has the virtual tape option.
- Answer:
ABF


### #vvxv Q34.Which two statements are true about the 'mysqld-auto.cnf' file?

- A This file is for storing MySQL Server configuration options in JSON format.
- B This file is for logging purposes only and is never processed.
- C This file is for storing MySQL server_uuid values only.
- D It is read and processed at the beginning of startup configuration.
- E It is read and processed at the end of startup configuration.
- F It is always updated with changes to system variables.

- Answer:
AE

### #### Q35.Examine this command, which executes successfully:
$mysqlbackup --user=dba	--password --port=3306 --with-timestamp
--only-know-file-types --backup-dir=/exprot/backups backup

Which statement is true?
- A Only tables stored in their own tablespaces are backed up.
- B Only non-encrypted files are backed up.
- C The backup includes only data files and their metadata.
- D Only InnoDB data and log files are backed up.
- E Only files for MySQL or its built-in storage engines are backed up.

- Answer:
E

By default, all files in the database subdirectories under the data directory of the server are included in the backup .
If the --only-known-file-types option is specified, mysqlbackup only backs up those types of files
that are data files for MySQL or its built-in storage engines,
which, besides the ibdata* files, have the following extensions:
.ARM: ARCHIVE table metadata
.ARZ: ARCHIVE table data
.CSM: CSV table metadata
.CSV: CSV table data
.ibd: InnoDB tablespace created using the file-per-table mode
.MRG: Merge storage engine references to other tables
.MYD: MyISAM data

.MYI: MyISAM indexes


### Q36.Examine this statement and output:

```sql
Myswl> SHOW GRANTS FOR jsmith;
+-----------------------------------------------------------+
|Grants for jsmith@%										|
+-----------------------------------------------------------+
|GRANT USAGE ON *.* TO jsmith’@’%’							|
|GRANT UPDATE(Nam- E 	ON ‘world’. ‘country’TO ‘jsmith ‘@’%’	|
+-----------------------------------------------------------+
2 rows in set (0.00 se- C
```

Which two SQL statement can jsmith execute?
- A UPDATE world.country SET Name='one' LIMIT 1;
- B UPDATE world.country SET Name='new' WHERE Name='old';
- C UPDATE world.country SET Name='first' ORDER BY Name LIMIT 1;
- D UPDATE world.country SET Name=CONCAT('New ', Nam- E ;
- E UPDATE world.country SET Name='all';
- Answer:
AE


###? vvxxvv Q37.You have semi-synchronous replication configured and working with one slave.
rpl_semi_sync_master_timeout has never been reached.
You find that the disk system on the master has failed and as a result,
the data on the master is completely unrecoverable.

Which two statements are true?
- A Reads from the slave can return outdated data for some time, until it applies all transactions from its relay log.
- B A small amount of committed transactions may be lost in case they were committed just before the disk failure.
- C As soon as the incident happens, application can read data from the slave and rely on it to return a full and current set of data.
- D The slave automatically identifies that the master is unreachable and performs any required actions so that applications can start using the slave as the new master.
- E Reads from the slave can return outdated data until the value of the rpl_semi_sync_master_timeout variable is reached.
- F 提交的事务不会丢失。
Answer:###
AB

	更正答案：AF

###vvvxv Q38.Which two are true about binary logs used in asynchronous replication?
- A They are pulled from the master to the slave.
- B They are pushed from the master to the slave.
- C They contain events that describe all queries run on the master.
- D They contain events that describe only administrative commands run on the master.
- E They contain events that describe database changes on the master.
###Answer：
AE
	https://dev.mysql.com/doc/refman/8.0/en/replication-implementation.html
	Each replica that connects to the source requests a copy of the binary log.
	That is, it pulls the data from the source, rather than the source pushing the data to the replica.

### #vvvxv Q39. Which three actions will secure a MySQL server from network-based attacks?
- A Allow connections from the application server only.
- B Use network file system(NFS) for storing data.
- C Construct a perimeter network to allow public traffic.
- D Use MysQL Router to proxy connections to the MySQL server.
- E Change the listening port to 3307.
- F Place the MySQL instance behind a firewall.

###	Answer:
ACF

perimeter network也叫DMZ（非军事区）网络，是一个由firewall保护起来的位于intranet 和internet之间的小网络.

###  Q40.Which three sets of item information are visible in the 'mysql' system database?
- A performance monitoring information
- B plugins
- C audit log events
- D rollback segments
- E information about table structures
- F time zone information and definitions
- G help topics
- Answer:
BFG


Data Dictionary Tables
Grant System Tables
Object Information System Tables
Log System Tables
Server-Side Help System Tables
Time Zone System Tables
Replication System Tables
Optimizer System Tables
Miscellaneous System Tables
```sql
root@localhost :mysql07:56:23>show tables;
+------------------------------------------------------+
| Tables_in_mysql                                      |
+------------------------------------------------------+
| columns_priv                                         |
| component                                            |
| db                                                   |
| default_roles                                        |
| engine_cost                                          |
| func                                                 |
| general_log                                          |
| global_grants                                        |
| gtid_executed                                        |
| help_category                                        |
| help_keyword                                         |
| help_relation                                        |
| help_topic                                           |
| innodb_index_stats                                   |
| innodb_table_stats                                   |
| password_history                                     |
| plugin                                               |
| procs_priv                                           |
| proxies_priv                                         |
| replication_asynchronous_connection_failover         |
| replication_asynchronous_connection_failover_managed |
| replication_group_configuration_version              |
| replication_group_member_actions                     |
| role_edges                                           |
| server_cost                                          |
| servers                                              |
| slave_master_info                                    |
| slave_relay_log_info                                 |
| slave_worker_info                                    |
| slow_log                                             |
| tables_priv                                          |
| time_zone                                            |
| time_zone_leap_second                                |
| time_zone_name                                       |
| time_zone_transition                                 |
| time_zone_transition_type                            |
| user                                                 |
+------------------------------------------------------+
37 rows in set (0.01 se- C
```

### Q41.Examine these entries from the general query log:
Time						Id	Command	Argument
2019-12-17T00:36:23.389450z	24	Connect	root@localhost on mydb using SSL/TLS
2019-12-17T00:36:23.389450z	24	Query	select @@version_comment limit 1
2019-12-17T00:36:23.929519z	25	Connect	root@localhost on mydb using SSL/TLS
2019-12-17T00:36:23.929846z	25	Query	select @@version_comment limit 1
2019-12-17T00:36:27.633082z	24	Query	START TRANSACTION
2019-12-17T00:36:30.321657z	24	Query	UPDATE t1 SET val =1 WHERE ID=130
2019-12-17T00:36:32.417433z	25	Query	START TRANSACTION
2019-12-17T00:36:33.617642z	25	Query	UPDATE t2 SET val =5 WHERE ID=3805
2019-12-17T00:36:36.045498z	25	Query	UPDATE t1 SET val =10 WHERE ID=130
2019-12-17T00:36:33.617642z	25	Query	UPDATE t2 SET val =5 WHERE ID=3805
2019-12-17T00:36:38.513674z	24	Query	UPDATE t2 SET val =42 WHERE ID=3805

All UPDATE statements reference existing rows.
Which describes the outcome of the sequence of statements?
- A A deadlock occurs after innodb_lock_wait_timeout seconds.
- B Connection 24 experiences a lock wait timeout.
- C Connection 25 experiences a lock wait timeout.
- D All statements execute without error.
- E A deadlock occurs immediately.
- Answer:
E


### Q42.Examine this output:
```sql
Mysql>SELET FORMAT_BYTES(@@global.innodb_buffer_pool_siz- E  AS BufferPoolSize,
@@global.innodb_buffer_pool_instances AS NumInstances,
FORMAT_BYTES(@@global.innodb_buffer_buffer_pool_chunk_siz- E  AS Chunksize;

+---------------+------------------	+-------------	+
|BufferPoolSize	|   NumInstances	| ChunkSize		|
+---------------+------------------	+-------------	+
|12.00 GiB		|	8				| 128.00 MiB	|
+---------------+------------------	+-------------	+

Mysql> SELECT * FROM sys.metrics WHERE Variable_name LIKE ‘Threads%’;

+-------------------+----------------+---------------+---------+
| Variable_name     | Variable_value | Type          | Enabled |
+-------------------+----------------+---------------+---------+
| threads_cached    | 4              | Global Status | YES     |
| threads_connected | 32             | Global Status | YES     |
| threads_created   | 112            | Global Status | YES     |
| threads_running   | 16             | Global Status | YES     |
+-------------------+----------------+---------------+---------+
4 rows in set (0.01 se- C
```

Which change should optimize the number of buffer pool instances for this workload?
- A Decrease the number of buffer pool instances to 4.
- B Increase the number of buffer pool instances to 16.
- C Increase the number of buffer pool instances to 12.
- D Decrease the number of buffer pool instances to 1.
- E Increase the number of buffer pool instances to 32.
###	Answer:
B


### Q43,Which three requirements must be enabled for group replication?
- A primary key or primary key equivalent on every table
- B semi-sync replication plugin
- C binary log ROW format
- D binary log MIXED format
- E replication filters
- F binary log checksum
- G slave updates logging
- Answer:
ACG

答案应该包括G
	https://dev.mysql.com/doc/refman/8.0/en/group-replication-requirements.html
	Replica Updates Logged.
	Set log_replica_updates=ON (from MySQL 8.0.26) or log_slave_updates=ON (before MySQL 8.0.26).


### #v## Q44.Examine this command, which executes successfully on InnoDB Cluster:
dba.dropMetadataSchema()
Which two statements are true?
- A The command drops the mysql_innodb_cluster_metadata schema and re-creates it.
- B The mysql_innodb_cluster_metadata schema is dropped from the instance where the connection was established.
- C Connections driven by MySQL Router are not affected by the command.
- D The mysql_innodb_cluster_metadata schema is dropped from all reachable members of the cluster.
- E Group Replication will be dissolved and all metadata purged.
- F Group Replication is still operational, but InnoDB Cluster must be reimported under MySQL Shell.
- Answer:
DF


### Q45.Which three commands can report all the current connections running on the MySQL server?
- A SELECT * FROM sys.metrics
- B SELECT * FROM information_schema.events
- C SELECT * FROM sys.statement_analysis
- D SELECT * FROM information_schema.processlist
- E SHOW EVENTS
- F SHOW FULL PROCESSLIST
- G SELECT * FROM performance_schema.threads
H)SELECT * FROM performance_schema.events_transactions_current
- Answer:
DFG


### Q46.Examine this statement, which executes successfully:

```sql
CREATE TABLE employess(
	emp_no int unsigned NOT NULL,
	Birth_date date NOT NULL,
	First_name varchar(14) NOT NULL,
	Last_name varvhar(16) NOT NULL,
	Hire_date date NOT ULL,
	PRIMARY KEY(emp_no)
)ENGIN=InnoDB;

Now examine this query:
SELECT emp_no,first_name,last_name,bitrh_date FRON employees
WHERE MONTH(birth_dat- E =4;
```

You must add an index that can reduce the number of rows processed by the query.
Which two statements can do this?
- A ALTER TABLE employees ADD INDEX ((MONTH(birth_dat- E ));
- B ALTER TABLE employees ADD INDEX (birth_dat- E ;
- C ALTER TABLE employees ADD COLUMN birth_month tinyint unsigned GENERATED ALWAYS AS (MONTH(birth_dat- E ) VIRTUAL NOT NULL, ADD INDEX (birth_month);
- D ALTER TABLE employees ADD INDEX (birth_month);
- E ALTER TABLE employees ADD INDEX ((CAST(birth_date->>'$.month' AS unsigne- D ));
- F ALTER TABLE employees ADD COLUMN birth_month tinyint unsigned GENERATED ALWAYS AS (birth_date->>'$month') VIRTUAL NOT NULL, ADD INDEX (birth_month);
- Answer:
AC


### Q47.Which three are types of InnoDB tablespaces?
- A schema tablespaces
- B temporary table tablespaces
- C encryption tables
- D data tablespaces
- E redo tablespaces
- F undo tablespaces
- Answer:
BDF

### Q48.On examination, your MySQL installation datadir has become recursively world read/write/executable.
What are two major concerns of running an installation with incorrect file privileges?
- A Data files could be deleted.
- B Users could overwrite configuration files.
- C SQL injections could be used to insert bad data into the database.
- D Extra startup time would be required for the MySQL server to reset the privileges.
- E MySQL binaries could be damaged, deleted, or altered.
- Answer:
AB


###x#### Q49.Which two tools are available to monitor the global status of innodb locking?
- A INFORMATION_SCHEMA.INNODB_METRICS
- B SHOW ENGINE INNODB STATUS;
- C INFORMATION_SCHEMA.STATISTICS
- D SHOW STATUS;
- E SHOW TABLE STATUS;
- F INFORMATION_SCHEMA.INNODB_TABLESTATS
	- Answer:
	AB

实测A
```sql
mysql> select name from INFORMATION_SCHEMA.INNODB_METRICS where name like '%lock%';
+---------------------------------+
| name                            |
+---------------------------------+
| lock_deadlocks                  |
| lock_deadlock_false_positives   |
| lock_deadlock_rounds            |
| lock_threads_waiting            |
| lock_timeouts                   |
| lock_rec_lock_waits             |
| lock_table_lock_waits           |
```

### #x## Q50.You want to dump all databases with names that start with "db".
Which command will achieve this?
- A mysqldump --include-tables=db.% --result-file=all_db_backup.sql
- B mysqldump --include-databases=db --result-file=all_db_backup.sql
- C mysqldump --include-databases=db% --result-file=all_db_backup.sql
- D mysqldump > all_db_backup.sql
###	Answer:
D
	没有参数：include-databases

### Q51.Examine this parameter setting:
audit_log=FORCE_PLUS_PERMANENT
What effect does this have on auditing?
- A It will force the load of the audit plugin even in case of errors at server start.
- B It causes the audit log to be created if it does not exist.
- C It prevents the audit plugin from being removed from the running server.
- D It prevents the audit log from being removed or rotated.

- Answer:
C
	For example, --audit-log=FORCE_PLUS_PERMANENT tells the server to load the plugin and prevent it from being removed while the server is running.


### Q52.You recently upgraded your MYSQL installation to MYSQL8.0
Examine this client error:
Error 2059 (HY000):authentication plugin ‘caching_sha2_password’
cannot be Loaded: /usr/local/mysql/libplugin/caching_sha2_password.so:
cannot open shared object file: No such or directory

Which option will allow this client to connect to MYSQL Server?
- A [mysqld] default_authentication_plugin=sha256_password
- B [mysqld] default_authentication_plugin=caching_sha2_password
- C ALTER USER user IDENTIFIED WITH mysql_native_password BY 'password';
- D ALTER USER user IDENTIFIED WITH caching_sha2_password
- E ALTER USER user IDENTIFIED WITH sha256_password
- F [mysqld] default_authentication_plugin=mysql_native_password

- Answer:
C

### #vvxv Q53.You made some table definition changes to a schema in your MySQL Server.
Which two statements reflect how MySQL Server handles the table definition changes?
- A MySQL keeps InnoDB metadata changes in .sdi files in datadir.
- B MySQL Server stores a copy of the serialized data in the InnoDB user tablespace.
- C MySQL writes SDI to the binary log for distributed backups.
- D MySQL implicitly executes FLUSH TABLES and stores a snapshot backup of the metadata.
- E The metadata is serialized in JSON format in Serialized Dictionary Information (SDI).

- Answer:
BE

说明：InnoDB stores SDI data within its tablespace files. NDBCLUSTER stores SDI data in the NDB dictionary.
Other storage engines store SDI data in .sdi files that are created for a given table in the table''s database directory.
SDI data is generated in a compact JSON format.


### Q54.Which two statements are `true` about MySQL Installer?
- A It provides a uniform installation wizard across multiple platforms.
- B Manual download of separate product packages is required before installing them through MySQL Installer.
- C It provides only GUI-driven, interactive installations.
- D It performs product upgrades.
- E It installs most Oracle MySQL products.
- Answer:
DE

可以用于安装：
	MySQL Servers，
		 MySQL Installer can install, configure, and upgrade a separate instance of MySQL 5.6, MySQL 5.7, and MySQL 8.0 on the same host.
	MySQL Applications
		MySQL Workbench, MySQL Shell, MySQL Router, and MySQL for Visual Studio.
	MySQL Connectors
		MySQL Connector/NET, MySQL Connector/Python, MySQL Connector/ODBC, MySQL Connector/J, and MySQL Connector/C++.
	Documentation and Samples



### Q55.Examine this snippet from the binary log file named binlog.000036:
# at 5000324
#191120 14155116 server id 1 end_log_pos 500453 crc32 0x98159515 Query thread_id=9 exec_time=2 error_code=0xid=1106
SET TIMESTAMP=1574222116/*!*/;
DROP TABLE ‘rental’/* generated by server*/
/*!*/;
The rental table was accidentally dropped, and you must recover the table.
You have restored the last backup, which corresponds to the start of the binlog.000036 binary log.
Which command will complete the recovery?
- A mysqlbinlog --stop-position=5000324 binlog.000036 | mysql
- B mysqlbinlog --stop-datetime='2019-11-20 14:55:16' binlog.000036 | mysql
- C mysqlbinlog --stop-position=5000453 binlog.000036 | mysql
- D mysqlbinlog --stop-datetime='2019-11-20 14:55:18' binlog.000036 | mysql
- Answer:
A


### #x## Q56.Examine this query and output:
```sql
Mysql> EXPLAIN ANALYZE
SELECR city.CountryCode,contry,Name AS Country_Nae,
FROM world.city
INNER JOIN world.country ON country.Code =city.CountryCode
WHERE country.Continent=’Asia’
AND city.Population >100000
ORDER BY city,Population DESC\G
***********************************1.row*****************************
 EXPLATN:
->Sort <temporary>.Poppulation DESC(acctual time =8.306..8.431 row =125 loope=1)
	->Strem result(acctual time =0.145..8.033 row =125 rows=125 loope=1)
	->Nested loop inner join (cost=241.12 rows=205) (acctual time =0.141.7.787 row =155 loope=1)
		->Filter (world.country.Continent =’Asia’)(cost=25.40 rows=34)(acctual time =0.064..0.820 row =51 loope=51)
		->index lookup on city using CountryCode(Countrycode=world.country.cod- E (acctual time =4.53..row =10 ) 1 row in set (0.0094 se- C
```

Which two statements are true?
- A The query returns exactly 125 rows.
- B It takes more than 8 milliseconds to sort the rows.
- C The country table is accessed as the first table, and then joined to the city table.
- D 35 rows from the city table are included in the result.
- E The optimizer estimates that 51 rows in the country table have continent = 'Asia'.
- Answer:
AB

	视频答案：AC

```sql
EXPLAIN ANALYZE
SELECT sbtest.id,sbtest.k,sbtest1.c
FROM sbtest
INNER JOIN sbtest1 ON sbtest.id =sbtest1.id
WHERE sbtest1.k=99
AND sbtest.pad >'100000'
ORDER BY c DESC\G

*************************** 1. row ***************************
EXPLAIN:
-> Nested loop inner join  (cost=1.40 rows=1) (actual time=1.957..1.957 rows=0 loops=1)
    -> Sort: sbtest1.c DESC  (cost=0.70 rows=2) (actual time=1.768..1.770 rows=2 loops=1)
        -> Index lookup on sbtest1 using k_1 (k=99)  (actual time=0.352..0.359 rows=2 loops=1)
    -> Filter: (sbtest.pad > '100000')  (cost=0.27 rows=0) (actual time=0.078..0.078 rows=0 loops=2)
        -> Single-row index lookup on sbtest using PRIMARY (id=sbtest1.i- D   (cost=0.27 rows=1) (actual time=0.077..0.077 rows=0 loops=2)

1 row in set (0.01 se- C
```




### Q57.Examine these InnoDB Cluster parameter settings:
Cluster.setInstanceOption(’host1:3377’,’MemberWeight’,40)

Cluster.setInstanceOption(’host2:3377’,’MemberWeight’,30)

Cluster.setInstanceOption(’host3:3377’,’MemberWeight’,40)

Cluster.setInstanceOption(’host3:3377’,’exitStateAction’,”ABORT_SERVER”)

cluster.setOption("expelTimeout",1)

Now examine the partial staus:
“topology”;###
”host1:3377”:
“address”:”host1:3377”,
“mode”:”R/O”,
[...]
“statue”:”ONLINE”,
“version”:”8.0.18”
,
“host2:3377”:###
“address”:”host2:3377”,
“mode”:”R/O”,
[...]
“statue”:”ONLINE”,
“version”:”8.0.18”
,
“host3:3377”:###
“address”:”host3:3377”,
“mode”:”R/W”,
[...]
“statue”:”ONLINE”,
“version”:”8.0.18”


A permanent network failure isolates host3.
Which two statements are true?
- A The instance deployed on host2 is elected as the new primary instance.
- B The issuing command cluster.switchToMultiPrimaryMode() will fail to enable multi-primary mode.
- C The instance deployed on host3 is expelled from the cluster and must be rejoined using cluster.addInstance('host3:3377').
- D Failure of the instance deployed on host1 provokes an outage.
- E The primary instance can be specified by using the command cluster.setPrimaryInstance(<host>:<port>).
- F The instance deployed on host3 will automatically rejoin the cluster when connectivity is re-established.

- Answer:
DE

A错
	When an instance has a higher precentage number set by memberWeight, it is more likely to be elected as primary in a single-primary cluster.
B错
	If a cluster has all of its instances running MySQL server version 8.0.15 or later, you can make changes to the topology of the cluster while the cluster is online.
C错
	To rejoin an instance to a cluster issue Cluster.rejoinInstance(instanc- E .

Dv
	host1上部署的实例出现故障会导致停机。
Ev

F错
	By default instances do not automatically rejoin the cluster.
	You can configure the autoRejoinTries option at either the cluster level
	or for an individual instance .

expelTimeout: integer value to define the time period in seconds that cluster members should
wait for a non-responding member before evicting it from the cluster.


### #vvvx## Q58.You are asked to review possible options for a new MySQL instance.
It will be a large, busy reporting data warehousing instance.
[mysqld]
innodb_data_file_path=
Which two configurations would satisfy long-term storage demands?
- A ibdata1:12M;ibdata2:12M:autoextend
- B ibdata1:12M:autoextend
- C ibdata1:12M;/tmp/ibdata2:12M:autoextend
- D ibdata1:12M;ibdata2:12M;ibdata3:12M
- E ibdata1:12M:autoextend;ibdata2:12M:autoextend
- F ibdata1:12M

- Answer:
AB

答案C:错误是由于不应该指定/tmp，不安全。可以指定绝对路径。
如：ibdata1:12M;/jkdb/mysqldata/greatseed/ibdata2:12M:autoextend


### Q59.Examine this command, which executes successfully:
shell> mysqldump --master-data=2 --single-transaction --result-file=dump.sql mydb
Which two statements are true?
- A It is a cold backup.
- B It executes flush tables with read lock.
- C It enforces consistent backups for all storage engines.
- D This option uses the READ COMMITTED transaction isolation mode.
- E The backup created is a consistent data dump.
- Answer:
BE


###vvvx  Q60.Examine this command and output:
```sql
+-----------------------------------+-----------+
| Varibe_name						|   Value   |
+-----------------------------------+-----------+
| Firewall_access_denied			|   7		|
| Firewall_access_granted			|   4		|
| Firewall_access_suspicious		|   3		|
| Firewall_access_cached_entries 	|   11		|
+-----------------------------------+-----------+
```

Which statement is true?
- A Firewall_access_denied is the number of connection attempts from prohibited hosts that are denied.
- B Firewall_access_cached_entries is the number of statements found in the query cache for users in DETECTING mode.
- C Firewall_access_granted is the number of connections granted from whitelisted hosts.
- D Firewall_access_suspicious is the number of statements logged as suspicious for users in DETECTING mode.
- Answer:
D

	防火墙profile的几种工作模式：
		off:关闭防火墙
		RECORDING:训练模式，防火墙记录执行的语句，形成allowlist。
		PROTECTING:保护模式下防火墙会根据allowlist允许或禁止语句的执行。If the mysql_firewall_trace system variable is enabled, the firewall also writes rejected statements to the error log.
		DETECTING:记录但不阻止 不在allowlist中的语句。





### #vvvx## Q61.You wish to protect your MySQL database against SQL injection attacks.
Which method would fail to do this?
- A using stored procedures for any database access
- B using PREPARED STATEMENTS
- C installing and configuring the Connection Control plugin
- D avoiding concatenation of SQL statements and user-supplied values in an application
- Answer:
C

	C:（ slow down brute force attacks）
	CONNECTION_CONTROL checks incoming connection attempts and adds a delay to server responses as necessary.




###vvvx ## Q62.Which two statements are true about MySQL server multi-source replication?
- A It relies on relay_log_recovery for resilient operations.
- B It does not attempt to detect or resolve replication conflicts.
- C It needs to be re-instanced after a crash to maintain consistency.
- D It is not compatible with auto-positioning.
- E It must use GTID replication.
- F It uses only time-based replication conflict resolution.
- Answer:
AB

Multi-source replication does not implement any conflict detection or resolution when applying transactions,
and those tasks are left to the application if required.

relay_log_recovery
	If enabled, this variable enables automatic relay log recovery immediately following server startup.
	The recovery process creates a new relay log file, initializes the SQL (applier) thread position to this new relay log,
	and initializes the I/O (receiver) thread to the applier thread position.
	Reading of the relay log from the source then continues.




### Q63.You are using an existing server with a new configuration.
MySQL Server fails to start.

Examine this snapshot of the error log:

190925 12:49:05 InooDB Initializing buffer pool size=3.0G

190925 12:49:05 InooDB Completed Initializing of buffer pool InnoDB:

Error:log file ./ib_logfile0 is of different size 5242880 bytes InnoDB than specified in the .cnf file 0 26214400 bytes 1

190925 12:49:05 [ERROR] Plugin ‘InnoDB’ init function returned error.

190925 12:49:05 [ERROR] Plugin ‘InnoDB’registation as a STORAGE PNGINR failed

190925 12:49:05 [ERROR] Plugin ‘Aborting

190925 12:49:05 [ERROR] Plugin ‘/usr/sbin/mysqld:Shutdown complete

Which action would allow the server to start?
- A Remove ib_logfile0 and ib_logfile1 files from the file system.
- B Execute mysqladmin flush-logs.
- C First run mysqld --initialize to refresh the size of ib_logfile.
- D Create a new ib_logfile0 file of size 26214400.
- Answer:
A


### ###Q64.User account baduser@hostname on your MySQL instance has been compromised.
Which two commands stop any new connections using the compromised account?
- A ALTER USER baduser@hostname ACCOUNT LOCK;
- B ALTER USER baduser@hostname PASSWORD DISABLED;
- C ALTER USER baduser@hostname MAX_USER_CONNECTIONS 0;
- D ALTER USER baduser@hostname DEFAULT ROLE NONE;
- E ALTER USER baduser@hostname IDENTIFIED WITH mysql_no_login;
- Answer:
AE

	The 'mysql_no_login' server-side authentication plugin prevents all client connections to any account that uses it.
	1.配置
	[mysqld]
	plugin-load-add=mysql_no_login.so
	或：
	INSTALL PLUGIN mysql_no_login SONAME 'mysql_no_login.so';

	2.使用
	To refer to the no-login authentication plugin in the IDENTIFIED WITH clause of a CREATE USER statement, use the name mysql_no_login.
	如：
	CREATE USER 'nologin'@'localhost'  IDENTIFIED WITH mysql_no_login;





### #vvx ## Q65.Your MySQL server is running on the Microsoft Windows platform.
Which three local connection protocols are available to you?
- A UDP
- B TCP/IP
- C X Protocol
- D named pipes
- E shared memory
- F SOCKET

- Answer:
BDE

protocol Value	Transport Protocol Used	Applicable 				Platforms
TCP				TCP/IP transport to local or remote server		All
SOCKET			Unix socket-file transport to local server		Unix and Unix-like systems
PIPE			Named-pipe transport to local server			Windows
MEMORY			Shared-memory transport to local server			Windows

### #vxxv Q66.Four nodes are configured to use circular replication.
Examine these configuration parameters for each node:
slave_parallel_type=DATABASE
slave_parallel_workers=4
slave_preserve_commit_order=0

Which statement is true?
- A Setting slave_parallel_type=DATABASE won''t work for circular replication;
	it should be set to LOGICAL_CLOCK.
- B Setting slave_preserve_commit_order to ON will improve data consistency.
- C Cross-database constraints can cause database inconsistency.
- D Each slave thread is responsible for updating a specific database.
- E Increasing slave_parallel_workers will improve high availability.
- F Setting transaction_allow_batching to ON will improve data consistency.
- Answer:
C

slave_preserve_commit_order 参数起作用的前提条件是开启多线程复制：
	slave_parallel_type=LOGICAL_CLOCK
	slave_parallel_workers>0



###  Q67.Examine this command, which executes successfully:
mysqlrouter --bootstrap user@hostname:port --directory=directory_path
Which activity is performed?
- A MySQL Router is restarted.
- B MySQL Router configures all the cluster nodes based on the information retrieved from the InnoDB cluster metadata server.
- C MySQL Router is configured based on the information in files in directory_path.
- D MySQL Router configures itself based on the information retrieved from the InnoDB cluster metadata server.
- Answer:
D

 bootstrap process has created a mysqlrouter.conf file with the required files at the directory specified,
 and the result shows you how to start this MySQL Router instance.




###? vvx Q68.Which two statements are true about using MySQL Enterprise Monitor Query Analyzer?
- A It is possible to retrieve a normalized statement, but never the exact statement that was executed.
- B The single query QRTi pie chart in the Query Analyzer view is based on the average execution of all statements.
- C It is possible to import data into the Query Analyzer from heterogeneous source, such as CSV.(不支持)
- D It is possible to configure the Query Analysis built-in advisor to get notified about slow query execution.
- E It is possible to list and analyze statements in an arbitrary graph range selection from timeseries graphs.
- Answer:
DE





### Q69.You have just installed MySQL on Oracle Linux and adjusted your /etc/my.cnf parameters to suit your installation.
Examine the output:
#systemct1 start mysqld
Job for mysqld service failed because the control process exited with error code.
See “systemct1 status mysqld.service”and “journalctl -xe”for details.
# systemct1 status mysqld.service Mysqld.service -MySQL Server
Loaded: loaded( /usr/lib/system/system/mysqld.service;enabled;vendor preset: disable- D
Active:failed (Resut :exit-cod- E  since Thu 2019-12-12 07:54:53 ACDT;33s ago Docs:man:mysqld(8)
http://dev.mysql.com/doc/refman/en/using-systemd.html
Process:2732 ExecStart=/usr/sbin/mysqld SMYSQLD_OPTS (code=exited,status=1/FAILUR- E
Process:2705 ExecStartPre=/usr/sbin/mysqld_pre_systemd(code=exited,status=0/SUCCESS)
Main PID :2732 (code=exited,status=1/FAILUR- E
Status:”Server startup in progress”
DEC 12 07:54:49 oe17 systemd[1]:Starting MySQL Server....
DEC 12 07:54:49 oe17 systemd[1]:mysqld.service:main process exited,code=exited,status=1/FAILURE
DEC 12 07:54:49 oe17 systemd[1] Failed to start MySQL Server.
DEC 12 07:54:49 oe17 systemd[1]:Unit mysqld.service entered failed state.
DEC 12 07:54:49 oe17 systemd[1]:MySQL Server failed

What statement is true about the start attempt?
- A MySQL server was not started due to a problem while executing process 2732.
- B systemd found the mysqld service disabled and failed to start it.
- C MySQL server continued to start up even though another process existed.
- D systemd waited for 30 seconds before timing out and start up failed.
- E systemd attempted to start mysqld, found another systemd mysqld process running, and shut it down.
- Answer:
A


### #v## Q70.Examine this query and its output:
Myswl>select * from sys.user_summary_by_statement_type where statement in ('select','insert','Quit');
+-------+-----------+-------+---------------+---------------+-----------+-----------+---------------+------------+----------+
|user	|stsement	|total	|totsl_latency	|max_latency	|lock_latency |row_sent	| rows_examined|Rows_affected |full_scans|
+-------+-----------+-------+---------------+---------------+-----------+-----------+---------------+------------+----------+
|app	|select		|919866	|2.41 h			|330.0ms		|1.52 m		|542614816	|	5426148160 	|	919958	|			|
|app	|insert		|923070	|1.66 h			|287.41ms		|1.65 m		|	0		|	0			|	923026 	| 0			|
|app	|Quit		|679892	|9.54 s			|170.97ms		|0ps		|	0		|	0			|	0   	|	0		|
|bob	|select		|344964	|53.61h			|328.42ms		|34.51s		| 203509545	|	203509545	|	0 		| 344847	|
|bob	|insert		|346159	|37.84m			|235.77ms		|36.94s		|	0		|	0			|	346175 	| 0			|
|bob	|Quit		|254971	|3.65 s			|69.97ms		|0ps		|	0		|	0			|	0   	|	0		|
|root	|select		|230621	|36.88m			|21.47s			|23.81s		|135639074	|	135639074	|	0 		| 230595	|
|root	|Quit		|170363	|2..24 s		|130.14ms		|0ps		|	0		|	0			|	0   	|	0		|
+-------+-----------+-------+---------------+---------------+-----------+-----------+---------------+------------+----------+
9 rows in set (0.00se- C

Which two statements are true?
- A The app user had the highest total number of rows read from storage engines.
- B User bob had the largest total time waiting for locks.
- C The root user had the largest number of modified rows for a select statement.
- D The root user had the largest single wait time.
- E User bob had a significantly higher ratio of SELECT + INSERT statements to QUIT than both app and root users.
（app:2.71；bob:2.71；root：2.71）

- Answer:
AD

- A The app user had the highest total number of rows read from storage engines.（比较 rows_examined 列）
- D The root user had the largest single wait time.(比较 max_latency)

### Q71.MySQL is installed on a Linux server with this configuration:
[mysqld]
user=mysql
datadir=/data/mysql

Which method sets the default authentication to SHA-256 hashing for authenticating user account passwords?
- A Set validate-user-plugins=caching_sha2_password in the configuration file.
- B Add default_authentication_plugin=sha256_password in the configuration file.
- C Add default_authentication_plugin=mysql_native_password in the configuration file.
- D Define CREATE USER ''@'%' IDENTIFIED WITH sha256_password in the MySQL instance.
- Answer:
B


### Q72.Examine these statements, which execute successfully:
TRUNCATE test;
BEGIN;
INSERT INTO test(id, nam- E  VALUES(1, "HELLO");
ROLLBACK;
SELECT id FROM test;

Which three storage engines would return a nonempty recordset for the test table when executing the statements?
- A BLACKHOLE
- B MEMORY
- C MyISAM
- D InnoDB
- E NDB
- F ARCHIVE

- Answer:
BCF
###! vxxvv Q73.Which statement is true about InnoDB persistent index statistics?
- A Increasing innodb_stats_persistent_sample_pages determines higher pages scanning speed, at the cost of increased memory usage.
- B Index statistics are calculated from pages buffered in the buffer pool for tables with InnoDB storage engine.
- C Updating index statistics is an I/O expensive operation.
- D Execution plans based on transient index statistics improve precision when innodb_stats_persistent_sample_pages is increased.
- E Setting innodb_stats_auto_recalc=ON causes statistics to be updated automatically when a new index is created.
- Answer:
E
	怀疑答案：C
	The `innodb_stats_auto_recalc` variable, which is enabled by default,
	controls whether statistics are calculated automatically when a table undergoes changes to more than 10% of its rows.

D 错
The number of index pages to sample when estimating cardinality and other statistics for an indexed column, such as those calculated by ANALYZE TABLE. Increasing the value improves the accuracy of index statistics, which can improve the query execution plan, at the expense of increased I/O during the execution of ANALYZE TABLE for an InnoDB table.
E 错
When an index is added to an existing table, or when a column is added or dropped, index statistics are calculated and added to the innodb_index_stats table regardless of the value of innodb_stats_auto_recalc.



### #vxv Q74.You issue this command:
SHOW SLAVE STATUS
In the output, there is a value for Seconds_behind_master.
How is this time calculated?
- A It is the time between the I/O thread receiving details of the master''s last transaction and the time it was written to the relay log on the slave.
- B It is the time between the most recent transaction applied by a SQL thread and the time it was committed on the master.
- C It is the time between the most recent transaction written to the relay logs and the time it was committed on the master.
- D It is the time between the I/O thread receiving details of the master''s last transaction and the time it was applied by the SQL thread.

- Answer:
B

 The slave is behind the master by comparing the Retrieved_GTID_Set and the Executed_GTID_Set.
 In such cases, Seconds_Behind_Master will represent the difference
 between
	the timestamp of the latest transaction processed by the SQL Thread
 and
	the timestamp of the same transaction when it was processed on the master.

 This transaction timestamp of the master is preserved through replication,
 hence the slave will be able to compute the SBM locally.

### #vvxv Q75.Your MySQL instance is capturing a huge amount of financial transactions every day in the `finance` database.
Company policy is to create a backup every day.
The main tables being updated are prefixed with transactions-.
These tables are archived into tables that are prefixed with archives- each month.
Mysqlbackup --optimistic-busy-tables=”^finance\.transactions.*” backup

Which optimization process best describes what happens with the redo logs?
- A The redo logs are backed up only if there are changes showing for the transactions tables.
- B The transaction tables are backed up first, then the archive tables and redo logs.
- C The redo logs are backed up first, then the transaction and archive tables.
- D The redo logs are not backed up at all.
- E The archive tables are backed up first, then the transaction tables and redo logs.
- Answer:
E

Optimistic backup乐观备份，备份过程在内部分成2个阶段：
1.乐观阶段：在不锁定mysql实例的情况备份不满足optimistic-busy-tables条件的表，redo,undo,系统表都不备份。
2.正常阶段：备份乐观阶段未备份的表，在不同的时间施加各种锁，备份redo,undo,系统表。




###vvxxv Q76.You are considering using file-system snapshots to back up MySQL.
Which three statements are true?

- A They take roughly twice as long as logical backups.
- B They allow direct copying of table rows with operating system copy commands.
- C They work best for transaction storage engines that can perform their own recovery when restored.
- D They backup window is almost zero from the perspective of the application.
- E They do not use additional disk space.
- F They do not back up views, stored procedures, or configuration files.
- G There is a slight performance cost while the snapshot is active.
- Answer:
CDG




### #vx Q77.Which two methods allow a DBA to reset a user''s password?
- A mysql_secure_installation utility
- B mysqladmin client program
- C GRANT statement
- D ALTER USER statement
- E SET PASSWORD statement
- Answer:
DE


### #vvvx ## Q78.Which two statements are true about the binary log encryption feature?
- A It requires a keyring plugin.
- B It can be set at run time.
- C It can be activated per session.
- D It encrypts any connecting slaves connection thread.
- E When enabled it encrypts existing binary logs.
- F It requires binary logging to be enabled.
- Answer:
AB

https://dev.mysql.com/doc/refman/8.0/en/replication-binlog-encryption.html




### Q79.Which three actions are effective in capacity planning?
- A monitoring OS resources for patterns
- B buying more RAM
- C consulting the application team about any future projects and use
- D upgrading to the latest application version
- E buying more disk
- F buying more CPU
- G adding circular replication nodes for increased DML capability
H)basing expected growth on an average of the last 3 years
- Answer:
ACH


###  Q80.Which condition is true about the use of the hash join algorithm?
- A The smallest of the tables in the join must fit in memory as set by join_buffer_size.
- B No index can be used for the join.
- C The query must access no more than two tables.
- D At least one of the tables in the join must have a hash index.

- Answer:
B
###vvvx ## Q81.The replication for master and slave MySQL Server is up and running .
The disk space occupied by the binary log files continues to grow.
Which two methods mange this issue?
- A Execute the FLUSH LOGS statement.
- B Delete all binary log files manually a=on the file system to release storage space.
- C Execute the PURGE BINARY LOGS statement.
- D On the master server disable binary logging by removing the --log-bin option
- E Set the binlog_expire_logs_seconds variable.
- Answer:
CE

	PURGE BINARY LOGS is safe to run while replicas are replicating. You need not stop them.
	If you have an active replica that currently is reading one of the log files you are trying to delete, this statement does not delete the log file that is in use or any log files later than that one, but it deletes any earlier log files.
	PURGE BINARY LOGS是复制安全的，不会删除slave需要的binlog



###vvvxxv Q82.Examine this list of MySQL data directory binary logs:
Binlog.000001
Binlog.000002
........
Binlog.000289
Binlog.000300
Binlog.000301
Binlog.index

Now examine this command, which executes successfully:
mysqldump --delete-master-logs --all-databases > /backup/db_backup.sql
Which two are true?
- A All databases are backed up to the output file.
- B All details regarding deleted logs and master metadata are captured in the output file.
- C All binary logs are backed up and then deleted.
- D All databases, excluding master metadata, are backed up to the output file.
- E All binary logs are deleted from the master.
- F All non-active binary logs are removed from the master.
- Answer:
DF


A:（不会备份 sys、information_schema、performace_schema）
B:（不会记录 deleted logs）
C:（不会备份 binlog）
E: 会生成一个新的 binlog 使用）
	--all-databases, -A
		Dump all tables in all databases. This is the same as using the --databases option
			and naming all the databases on the command line.
		 As of MySQL 8.0, the mysql.event and mysql.proc tables are not used.
		Definitions for the corresponding objects are stored in data dictionary tables,
		but those tables are not dumped. To include stored routines and events in a dump made using --all-databases, use the --routines and --events options explicitly.

	--delete-master-logs
		On a replication source server, the options delete the binary logs by
		sending a PURGE BINARY LOGS statement to the server after performing the dump operation.



### vvx# Q83.Your MySQL environment has asynchronous position based-replication with one master and one slave.
The slave instance had a disk I/O problem, so it was stopped.
You determined that the slave relay log files were corrupted and unusable, but no other files are damaged.
You restart MySQL Server.
How can replication be restored?
- A The relay logs from the master should be used to replace the corrupted relay logs.
- B The slave relay logs should be deleted; then execute START SLAVE;
- C The slave needs to be restored from backup.
- D The slave relay logs should be deleted; execute CHANGE MASTER to adjust the replication relay log file name, then issue START SLAVE;

- Answer:
D

注意：asynchronous position based-replication 基于位置的异步复制

### Q84.How can mysqld_multi be configured to allow MySQL instances to use the same port number?
- A The instances have appropriate net masks set.
- B The instances use different user accounts unique to each instance.
- C The instances use different socket names.
- D The instances listen on different IP addresses.
- Answer:
D


### Q86.What does the slave I/O thread do?
- A monitors and schedules I/O calls to the subsystem for the relay logs
- B connects to the master and requests it to send updates recorded in its binary logs
- C acquires a lock on the binary log for reading each event to be sent to the slave
- D reads the relay log and executes the events contained in them
- Answer:
B


### Q87.The `languages` table uses MyISAM and the `countries` table uses the InnoDB storage engine.
Both tables are empty.
Examine these statements:
BEGIN;
INSERT INTO languages(lan- G  VALUES (“Italian”);
INSERT INTO countries(country) VALUES(“Italy”);
ROLLBACK;

What is the content of both tables after executing these statements?
- A countries has one row, languages has none.
- B Both tables have one row.
- C Both tables are empty.
- D languages has one row, coutries has none.
- Answer:
D



### #vxv Q88.Which two MySQL Server accounts are locked by default?
- A any new ROLE accounts
- B any user created without a password
- C any internal system accounts
- D any user created with a username, but missing the host name
- E any user set as DEFINER for stored programs
- Answer:
AC


### Q89.After installing MySQL 8.0 on Oracle Linux 7, you initialize the data directory with the
mysqld --initialize command.
Which two will assist in locating the root password?
- A the root_pw variable stored in the mysql.install table
- B the root password displayed on the screen via a [Warning] message
- C the root password inserted in the error log set by the --log-error=[file_name] variable
- D the root password written to the /root/.my.cnf file
- E as root, executing the SHOW PASSWORD command by using the SHA-256 password encryption plugin
- Answer:
BC


### vvvx Q90.You have appropriate privileges and are about to shut down a running MySQL server process on Oracle Linux 7.

Which three are valid methods that will shut down the MySQL server?
- A mysqld_safe -S /tmp/mysql.sock SHUTDOWN
- B kill mysqld_safe
- C mysqladmin shutdown
- D mysql -S /tmp/mysql.sock --shutdown
- E mysqld_safe --shutdown
- F systemctl stop mysqld
- G mysql> SHUTDOWN;

- Answer:
CFG
	mysqld_safe是用于启动mysqld的，不是用户停止mysqld的。
	mysqld_safe is the recommended way to start a mysqld server on Unix.

	mysqladmin is a client for performing administrative operations.
	You can use it to check the server''s configuration and current status, to create and drop databases, and more.


### vvxxv Q91.Which three statements are true about MySQL Enterprise Firewall?
- A It is available only in MySQL Enterprise versions.
- B On Windows systems, it is controlled and managed using the Windows Internet Connection Firewall control panel.
- C It provides INFORMATION_SCHEMA tables that enable views into firewall data.
- D System tables named firewall_users and firewall_whitelist in the mysql database provide persistent storage of firewall data.
- E It shows only notifications for blocked connections, which originated outside of your network’s primary domain.
- F Firewall functionality is dependent on SHA-256 and ANSI-specific functions built to the mysql.firewall table.
- Answer:
ACD
	视频答案：ACD



### Q92.Which two statements are true about the mysql_config_editor program?
- A It provides an interface to change my.cnf files.
- B It manages the configuration of client programs.
- C It can move datadir to a new location.
- D It manages the configuration of user privileges for accessing the server.
- E It will use [client] options by default unless you provide –login-path.
- F It manages the configuration of the MySQL Firewall feature.
- G It can be used to create and edit SSL certificates and log locations.
- Answer:
BE


###vvvxxvv Q93.You are attempting to start your mysqld. Examine this log output:
2019-12-12T22:21:40:353800z 0 [System] [DCY-010116] [Server] /mysql/bin/mysqld [mysld 8.0.18-comnercial starting as process 29740
2019-12-12T22:21:40:458802z 1 [ERROR] [DCY-012592] [InnoDB]Operating system error number 2 in a file operation.
2019-12-12T22:21:40:459259z 1 [ERROR] [DCY-012593] [InnoDB] The error means the system cannot find the patj specified.
2019-12-12T22:21:40:459423z 1 [ERROR] [DCY-012594] [InnoDB]If you are installing InnoDB,remember that must create directories yourself ,InnoDB does not create them.
2019-12-12T22:21:40:459606z 1 [ERROR] [DCY-012646] [InnoDB] File ./ibdatal1 ‘open’ returned os error 71.Cannot continue operation.
2019-12-12T22:21:40:459891z 1 [ERROR] [DCY-012981] [InnoDB]Cannot continue operation.

Which two things must you check?
- A the configuration file for correct datadir setting
- B that you are using the correct version of MySQL
- C that the TLS/SSL certificates are still valid
- D for the possibility that the files are locked by another process
- E for the presence of the missing files in other locations
- F that the user attempting to connect to the database is using the correct username and password
- Answer:
AE

	`open` returned os error 71 --表示文件丢失



### Q94.Examine these two reports taken 100 seconds apart:
GLOBAL STATUS 1:									  GLOBAL STATUS 2:
Com_create_table=500005               ->              Com_create_table=500505
Com_drop_table=500003                 ->              Com_drop_table=500498
Com_flush=23                          ->              Com_flush=31
Create_tmp_disk_tables=400000         ->              Create_tmp_disk_tables=400400
Create_tmp_tables=1200000             ->              Create_tmp_tables=1201200
Max_used_connections=92               ->              Max_used_connections=92
Open_files=5000                       ->              Open_files=5000
Opened_files=5000                     ->              Opened_files=7505
Open_table_definitions=3000           ->              Open_table_definitions=3000
Open_tables=1024                      ->              Open_tables=1024
Opened_table_definitions=2369         ->              Opened_table_definitions=2873
Opened_tables=3500000                 ->              Opened_tables=3503500
Threads_connected=62                  ->              Threads_connected=67
Threads_running=58                    ->              Threads_running=64
Uptime=100000                         ->              Uptime=100000


Your MySQL system normally supports 50-75 concurrent connections.
Which configuration change will improve performance?
- A decrease table_definition_cache
- B decrease open_files_limit
- C increase table_open_cache
- D increase max_connections
- Answer:
C


###vvxxv Q95.Examine this partial output for InnoDB Cluster status:
“topology”;###
	“host1:3377”:###
	”address”;”host1:3377”,
	“mode”:”R/W”, [.	]
	“STATUS”:”ONLINE”,
	“version”;”8.0.18’
	,
	“host2:3377”:###
	”address”;”host2:3377”,
	“mode”:”R/O”, [.	]
	“STATUS”:”MISSING”,
	,
	“host3:3377”:###
	”address”;”host3:3377”,
	“mode”:”R/O”, [.	]
	“STATUS”:”ONLINE”,
	“version”;”8.0.18’


Which statement explains the state of the instance deployed on host2 (missin- G ?
- A It can rejoin the cluster by using the command dba.rebootClusterFromCompleteOutage().
- B It has been expelled from the cluster because of a transaction error.
- C It can rejoin the cluster by using the command cluster.addInstance('<user>@host3:3377').
- D It has been removed from the cluster by using the command STOP GROUP_REPLICATION;
- E It can be recovered from a donor instance on host3 by using the command cluster.rejoinInstance('<user>@host3:3377').

- Answer:
B
### Q96.Which three methods display the complete table definition of an InnoDB table?
- A PRPAIR TABLE table USE_FRM
- B SHOW CREATE TABLE
- C hexdump -v-c table.frm
- D Mysqldump --no-data schema table
- E SELECT FROM table 1\G
- F Query the Information Schema
###Answer：
BDF


###vvx Q97.A scientific data gathering application uses a MySQL instance back end for data management.
There is a high concurrency of transactions at thousands of transactions per second of volatile data.
A restore from binary logs is planned using the command:

mysqlbinlog
	--start-datetime='2019-08-01 11:00:00'
	--stop-datetime='2019-08-10 08:30:25'
	binlog.000238 binlog.000239 binlog.000240 | mysql

Which two characteristics cause the restore to be inconsistent to the original data?
- A Temporary tables cannot persist across binary logs.
- B Multiple binary logs cannot be specified on the command line.
- C The temporal values do not offer high enough precision.
- D The time span of binary logs is too long to restore.
- E Transaction rate is too high to get a consistent restore.
- Answer:
CD
	视频答案CE 选此
	怀疑答案：
	使用--start-datetime 和 --stop-datetime 不精确，建议使用 --start-position 和 --stop-position
	Only use the --start-datetime and --stop-datetime options to help you find the actual event positions of interest. Using the two options to specify the range of binary log segment to apply is not recommended: there is a higher risk of missing binary log events when using the options. Use --start-position and --stop-position instead.



###?vvxxxvv Q98.Which two can minimize security risks when creating user accounts?

- A Avoid the use of wildcards in host names.
- B Avoid the use of wildcards in usernames.
- C Require the use of mixed case usernames.
- D Do not allow accounts without passwords.
- E Require users to have the FIREWALL USER privilege defined.

- Answer:
AD


	B错：		用户名可以使用% ,_ 但应避免使用

学生手册1
Avoid possible security risks when creating accounts:
– Do not create accounts without a password.
– Do not create anonymous accounts.
	— Accounts with no username such as ''@localhost
– Where possible, avoid wildcards when you specify account host names.


###? vvxxv Q99.There are five MySQL instances configured with a working group replication.
Examine the output of the group members:
mysq1> SELECT MEMBER_ID, MEMBER_STATE FROM performance_schema.replication_group_ members;
+---------------------------------------+-------------------+
|MEMBER_ ID								|	MEMBER STATE	|
+---------------------------------------+-------------------+
|1999b9fb-4aaf-11e6-bb54-28b2bd168d0	|	UNREACHABLE		|
|199b2df7-4aaf-11e6-bb16-28b2bd168d0	|	ONLINE			|
|199bb88e-4aaf-11e6-babe-28b2bd168d07	|	ONLINE			|
|19ab72fc-4aaf-11e6-b581-28b2bd168d07	|	UNREACHABLE		|
|19b33846-4aaf-11e6-ba81-28b2bd168d071	|	UNREACHABLE		|
+---------------------------------------+-------------------+

Which two statements are true about network partitioning in the cluster?
- A The group replication will buffer the transactions on the online nodes until the unreachable nodes return online.
- B A manual intervention to force group members to be only the working two instances is required.
- C The cluster will shut down to preserve data consistency.
- D There could be both a 2 node and 3 node group replication still running, so shutting down group replication and diagnosing the issue is recommended.
- E The cluster has built-in high availability and updates group_replication_ip_whitelist to remove the unreachable nodes.

- Answer:
AD
	视频答案：BD


### Q100.Which three are types of information stored in the mysql data dictionary?
- A InnoDB buffer pool LRU management data
- B performance metrics
- C access control lists
- D server runtime configuration
- E server configuration rollback
- F view definitions
- G stored procedure definitions
	###Anser:
	CFG
###?vxxxvv Q101.which two queries are examples of successful SQL injection attacks?

A.SELECT user, phone FROM customers WHERE name='\; DROP TABLE users; --';
B.SELECT id, name FROM user WHERE id=23 oR id=32 OR 1=1;
C.SELECT id, name FROM user WHERE user.id= (SELECT members.id FROM members);
D.SELECT email, passwd FROM members
WHERE email= 'INSERT INTO members ('email', 'passwd') VALUES ('bob@example.com', 'secret');--';
E. SELECT user,passwd FROM members
WHERE user='?';INSERT INTO members ('user',"passwd') VALUES ("bob@example.com', 'secret');--';
F. SEIECT id, name FROM user WHERE id=23 oR id=32 AND 1=1;

- Answer:
BE

	视频答案：AE （感觉这个对）

###?vvvx Q102.You must run multiple instances of MySQL Server on a single host.
Which three methods are supported?
- A Use system tools to lock each instance to its own CPU.
- B Run MySQL Server docker containers.
- C Use systemd with different settings for each instance.
- D Start mysqld or mysqld safe using different option files for each instance.
- E Run mysqld with --datadir defined for each instance.
- F Use resource groups to lock different instances on separate CPUs.

- Answer:
CDE
	应为答案：BCD

### #vxxvv Q103.Which statement enables all roles granted to all users automatically?
- A SET ROLE ALL;
- B SET DEFAULT ROLE ALL TO '*'@'%';
- C SET PERSIST activate_all_roles_on_login=ON;
- D SET PERSIET mandatory_roles=ALL;

- Answer:
C

	B:执行报错：ERROR 3523 (HY000): Unknown authorization ID `*`@`%`

	activate_all_roles_on_login：在用户登录时激活所有角色。是激活操作。
		If activate_all_roles_on_login is enabled, the server activates all roles granted to each account at login time.
		This takes precedence over default roles specified with SET DEFAULT ROLE.
	mandatory_roles: 强制角色是强制授权给每个用户的角色，但不许激活才能起作用。是授权操作。
		In effect, these roles are automatically granted to every user.
		Mandatory roles, like explicitly granted roles, do not take effect until activated.

	D:mandatory_roles指定具体的角色如：'role1,role2@localhost,r3@%.example.com'

### Q104.Which two siatutions will cause the binary log to rotate?
- A FLUSH HOSTS executed
- B max_binlog_size exceeded
- C max_binlog_cache_size exceeded
- D SET sql_log_bin=1 executed
- E SET sync_binlog=l executed1
- F FLUSH LOGS executed

- Answer:
BF
### Q105.Examine this statement, which executes successfully:
CREATE TABLE world.city (
	ID int NOT NULL AUTO_INCREMENT ,
	Name char(35) NOT NULL DEFAULT ‘ ’,
	CountryCode char(3) NOT NULL DEFAULT ‘‘,
	District char (20) NOT NULL DEFAULT ‘‘,
	Population int NOT NULL DEFAULT ‘0’
	PRIMARY KEY (I- D
	KEY CountryCode (CountryCode )
) ENGINE InnoDB;

You want to improve the performance of this query:

SELECT Name FROM world.city
WHERE Population BETWEEN 1000000 AND 2000000;

Which change enables the query to succeed while accessing fewer rows?
- A ALTER TABLE world.city ADD INDEX (Nam- E  ;
- B ALTER TABLE world.city ADD SPATIAL INDEX (Nam- E ;
- C ALTER TABLE world.city ADD FULLTEXT INDEX (Nam- E ;
- D ALTER TABLE world.city ADD SPATIAL INDEX (Population);
- E ALTER TABLE world.city ADD INDEX (Population);
- F ALTER TABLE world.city ADD FULLTEXT INDEX (Population);
- Answer:
E


### Q106.Examine this configuration:
You have a corporate private network, which uses its own Certificate Authority (C- A
using an industry standard 2048-bit RSA key length.
All MySQL Server and client certificates are signed using the central corporate CA.
All clients are known, controlled,and exist only on the private LAN.
The private network uses its own private authoritative DNS.
The private network also uses other nominal enterprise services.
An end-to-end encrypted connection for a MySQL client to MySQL server has been established on this LAN.
How does the MySQL Servers''self signed certificate compare to
one that would be signed by a known public,third party trusted Certificate Authority?
- A The self-signed certificate is equally secure and equally trusted.
- B The self-signed certificate is more secure and less trusted.
- C The self-signed certificate is less secure and equally trusted.
- D The self-signed certificate is equally secure and less trusted.
- E The self-signed certificate is more secure and equally trusted.
- F The self-signed certificate is less secure and less trusted.

- Answer:
F
### Q107.Which two are valid uses for binary logs on a MySQL instance?
- A logging the duration and locks for all queries
- B replication
- C audit of all queries
- D point-in-time recovery
- E recording the order in which queries are issued

- Answer:
BD
### #vxv Q108.Which two are characteristics of snapshot based backups?

- A There is no need for InnoDB tables to perform its own recovery when restoring from the snapshot backup.
- B Snapshot backups can be used only in virtual machines.
- C Snapshot based backups greatly reduce time during which the database and applications are unavailable.
- D The frozen file system can be cloned to another virtual machine immediately into active service.
- E A separate physical copy must be made before releasing the snapshot backup
- Answer:
AC
	更正答案：CE

	参考：https://dev.mysql.com/doc/refman/8.0/en/backup-methods.html

A 错 ：需要innodb执行自己的恢复过程

If you are using a Veritas file system, you can make a backup like this:
1.From a client program, execute
	FLUSH TABLES WITH READ LOCK.
2.From another shell, execute
	mount vxfs snapshot.
	使用卷管理(创建一个lv的快照卷)：
	/sbin/lvcreate -s -n 134snapshot -L 10G /dev/vg0/lvol0
3.From the first client, execute
	UNLOCK TABLES.
4.Copy files from the snapshot.
5.Unmount the snapshot.




### Q109.Examine this output:
mysql> SHOW GLOBAL VARIABLES LIKE '%dir';
+---------------------------+---------------+
|Variable_ name				|Value			|
|basedir					|	|1 /usr	|
。。。。。。
|datadir					|	|/var/lib/mysql	|
|innodb_data_home_dir		|	|/ innodb data	|
|innodb_log_group_home_dir	|	|./	|
|innodb_temp_tablespaces_dir|	|./#innodb temp/	|
|innodb_tmpdir				|
。。。。。。
|plugin dir					|	|/usr/lib/plugin	|
。。。。。。。
|tmpdir						|	|/ tmp: /var/ tmp	|
+---------------------------+---------------+
You plan to add this parameter to the configuration:
innodb_directories='/innodb_extras'

Which statement is true?

- A It defines all innodb tablespace options relative to a starting parent directory.
- B It adds more temporary workspace in addition to the innodb_ tmpdir location.
- C It moves all innodb tablespaces to the 1 innodb_ extras directory to enable a new
innodb_data_home_dir to be defined.
- D It is not necessary because innodb_data_home_dir is already defined.
- E It allows scanning of other locations to discover more innodb tablespaces .
- Answer:
E
	Defines directories to scan at startup for tablespace files.
	This option is used when moving or restoring tablespace files to a new location
	while the server is offline.
	It is also used to specify directories of tablespace files created using an absolute path or
	that reside outside of the data directory.




### #vv## Q110.Examine this command, which executes successfully:
mysqlpump --user=root --password > full_ backup.sql
Which two databases will be excluded from this dump?
- A world
- B employee
- C information_schema
- D mysql
- E sys

- Answer:
CE
	https://dev.mysql.com/doc/refman/8.0/en/mysqlpump.html
	mysqlpump does not dump the performance_schema, ndbinfo, or sys schema by default.
	To dump any of these, name them explicitly on the command line.
	You can also name them with the --databases or --include-databases option.
	mysqlpump does not dump the INFORMATION_SCHEMA schema.
	mysqlpump does not dump InnoDB CREATE TABLESPACE statements.

### Q111.You recently upgraded your MySQL installation to MySQL 8.0 .
Examine this client error:
ERROR 2059 (HY000): Authentication plugin "caching sha2_ password"
cannot be loaded: /usr/local/mysql/1ib/plugin/caching_sha2_password.so
: cannot open shared object file: NO such file or directory

Which option will allow this client to connect to MySQL Server ?
- A [mysqld]
Default_authentication_plugin=caching sha2_password
- B AITER USER user IDENTIFIED WITH mysql_native_password BY 'password' ;
- C AITER USER user IDENTIFIED WITH caching_sha2_password BY 'password' ;
- D
[mysqld]
Default_authentication_plugin=sha256_password;
- E ALTER USER user IDENTIFIED WITH sha256_password BY 'password';
- F
[mysqld]
Default_authentication_plugin=mysql_native_password

- Answer:
B

### Q112.Which three methods are part of a 'scale up' approach to capacity planning?
- A adding additional MySQL servers to the existing host
- B adding more CPU power
- C adding a replication slave
- D adding more RAM
- E adding more storage to your disk array
- F sharding the server into a parallel server farm
- G adding a new node to InnoDB Cluster
	- Answer:
BDE
### Q113.You planned an upgrade of your MySQL Server from version 5.7 to version 8.
You created a full backup and successfully tested the upgrade process on a test server.
You then upgraded the production environment successfully.
Soon after the upgrade, the application team reported a problem
and asked you to roll back the upgrade.

Which statement is true?
- A You must downgrade the data dictionary using the mysqlfrm utility.
- B You can easily switch between using MySQL 5.7 and MySQL 8 binaries after upgrading,
 because both sets of metadata are maintained.
- C You must set--skip-networking and run mysqld --dd-downgrade to prepare for rollback.
- D You must restore to your backup created in MySQL 5.7.

- Answer:
D
### Q114.You want to install and configure MySQL on Linux server with tarball binaries in the /app/mysq1 directory,
where the bin directory is found at /app/mysql/bin and the data directory at /app/data.
Which two parameters are required to configure the MySQL instance?

- A The configuration basedir=/app/mysql is needed.
- B The configuration datadir=/app/data is needed.
- C The configuration log-bin=/app/data is needed.
- D The configuration datadir=/app/mysql/data is needed
- E The configuration innodb_log_ group home dir=/datadir is needed.
- F The configuration basedir=/app/mysql/bin is needed.
- Answer:
AB


### Q115.MySQL programs look for option files in standard locations.
Which method will show the option files and the order in which they are read?
- A mysql> SHOW GLOBAL VARIABLES;
- B shell> mysql --print-defaults
- C shell> mysqladmin --debug
- D shell> mysqld --help --verbose

- Answer:
D
###? #vvv ## Q116.You must run multiple instances of MySQL Server on a single host.
Which three methods are supported?
- A Use system tools to lock each instance to its own CPU.
- B Use resource groups to lock different instances on separate CPUs.
- C Run mysq1d with --datadir defined for each instance.
- D Run MySQL Server docker containers.
- E Start mysqld or mysq1d_safe using different option files for each instance.
- F Use systemd with different settings for each instance.

- Answer:
CEF
	更正答案：DEF


### Q117.An attempt to recover an InnoDB Cluster fails.
Examine this set of messages and responses:
host3:3377 ss1 JS > dba.rebootClusterFromCompleteOutage()
Reconfiguring the defau1t cluster from complete outage.
The instance 'host1:3377' was part of the cluster configuration.
Would you like to rejoin it to the cluster? [y/N] : y
The instance 'host2:3377' was part of the cluster configuration.
Would you like to rejoin it to the cluster? [y/N] : Y

Dba. rebootClusterFromCompleteOutage: The active session instance isn''t the most updated in comparison with the ONLINE instances of the Cluster''s metadata.
Please use the most up to date instance: 'host1:3377'. (RuntimeError)

Which statement is true?
- A The cluster is running and there is at least one ONLINE instance.
- B The instance deployed on host3 must be synchronized from a donor deployed on host1 by using the command c1uster . addInstance ( ' host1 :3377').
- C It is possible to determine the most up-to-date instance by comparing different global transaction identifier (GTI- D  sets with GTID_ SUBSET (set1, set2);
- D The  active  session  instance is  invalid  and  must  be  re-created  by  using  the  command  shell.connect    ( 'host3:3377') .
- E The instance deployed on host3 must be rebuilt with a backup from the primary instance.

- Answer:
C
### Q118.Examine this partial report:
Mysql> SHOW FULL PROCESSLIST
+-----	+---	+-------	+ -----
|   Id	| User	|   Host	|.......
+-----	+---	+-------	+ -----
|4	|	event_scheduler	|	localhost	|........
|9	|	root	|	localhost:51502	|.........
|10	|	root	|	localhost:51670	|.........

Examine this query:
SELECT SUM (m.CURRENT_NUMBER_OF_BYTES_USE- D  AS TOTAL
FROM performance_schema.memory_summary_by_thread_by_event_name m
INNER JOIN performance_schema.threads t
ON m.THREAD_ID = t.THREAD_ID
WHERE t.PROCESSLIST_ID = 10;

What information does this query provide?
- A total memory used by connection number 10
- B total memory used across all connections associated with the user on connection number 10
- C total memory used by the first 10 threads
- D total memory used by thread number 10
- E total memory used across all connections associated with the user on thread number 10
- F total memory used by the first 10 connections

- Answer:
A
### Q119.Which three settings control global buffers shared by all threads on a MySQL server?
- A tmp_table_size
- B innodb_buffer_poo1_size
- C table_open_cache
- D sort_buffer_size
- E key_buffer_size
- Answer:
BCE


### Q120.You plan to take daily full backups, which include the ndbinfo and sys (internal) databases.
Which command will back up the databases in parallel?

- A mysqldump --all-databases > full-backup-$ (date +%Y%m%- D .sql
- B mysqlpump --include-databases=% > full-backup-$ (date +%Y%m%- D .sql
- C mysqlpump --all-databases > full-backup-$ (date +%Y%m%- D .sql
- D mysqldump --single-transaction > ful1-backup-$ (date +%Y%m%- D .sql
- Answer:
B

	并行：使用mysqlpump
	包括sys：需明确指定
		The --all-databases option dumps all databases
			(with certain exceptions noted in mysqlpump Restrictions).
		--include-databases=% is similar to --all-databases,
		but selects all databases for dumping, even those that are exceptions for --all-databases.



###  Q121.Examine these statements and output:
mysql> GRANT PROXY ON accounting@localhost TO ' '@'%';

mysql> SELECT USER(), CURRENT_USER(), @@proxy_user;
+------------------------+-------------------+------------------+
|USER()					| CURRENT_USER()		| @@proxy_user	|
+------------------------+-------------------+------------------+
| rsmith@localhost 		|accounting@localhost	| ' '@'%'		|
+------------------------+-------------------+------------------+

Which statement is true?
- A The user failed to define a username and the connecting username defaulted to ''G'8'.
- B The user is authorized as the rsmith@localhost user.
- C The user is authenticated as the anonymous proxy user ' '@ '%'.
- D The user is logged in with --user=accounting as an option.
- E The user is authorized as the accounting@localhost user.

- Answer:
E

USER() 连接用户 代理用户名
CURRENT_USER() 被代理用户的用户名
proxy_user: This value is NULL if proxying is not used.
			Otherwise, it indicates the proxy user account.

###  Q122.Which two actions can obtain information about deadlocks?
- A Run the SHOW ENGINE INNODB MUTEX command from the mysql client.
- B Enable the innodb_status_output_locks global parameter.
- C Enable the innodb_print_all_deadlocks global parameter.
- D Run the SHOW ENGINE INNODB STATUS command from the mysql client.
- E Use the sys.innodb_lock_waits view.

- Answer:
CD
	https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_print_all_deadlocks
	When `innodb_print_all_deadlocks` option is enabled, information about all deadlocks in InnoDB user transactions
	is recorded in the mysqld error log.
	Otherwise, you see information about only the last deadlock, using the SHOW ENGINE INNODB STATUS command.


### Q123.Examine this statement, which executes successfully:
```sql
CREATE TABLE world.city(
	ID int NOT NULL AUTO_INCREMENT ,
	Name char(35) NOT NULL DEFAULT '' ,
	CountryCode char(35) NOT NULL DEFAULT ' ',
	District char (20) NOT NULL DEFAULT '',
	Population int NOT NULL DEFAULT '0',
	PRIMARY KEY (I- D  ,
	KEY CountryCode(CountryCod- E
)ENGINE=InnoDB;

You want to improve the performance of this query:
SELECT Name
FROM world.city
WHERE Population BETWEEN 1000000 AND 2000000;
```

Which change enables the query to succeed while accessing fewer rows?
- A ALTER TABLE world.city ADD INDEX (Nam- E  ;
- B ALTER TABLE world.city ADD SPATIAL INDEX (Nam- E  ;
- C ALTER TABLE world.city ADD FULLTEXT INDEX (Nam- E  ;
- D ALTER TABLE world.city ADD FULITEXT INDEX (Population);
- E ALTER TABLE world.city ADD SPATIAL INDEX (Population) ;
- F ALTER TABLE world.city ADD INDEX (Population) ;

- Answer:
F


### Q124.User 'fwuser'@'1ocalhost' is registered with the MySQL Enterprise Firewall
and has been granted privileges for the SAKILA database.
Examine these commands that you executed and the results:
```sql
mysql> SELECT MODE FROM INFORMATION_SCHEMA.MYSOL_FIREWALL_USERS
		WHERE USERHOST='fwuser@localhost';

+-----------+
|MODE   	|
+-----------+
|PROTECTING |
+-----------+

mysql> SELECT RULE FROM INFORMATION_SCHEMA.MYSQL_FIREWALL_WHITELIST
WHERE USRHOST * 'fwuser@localhost' ;
+-----------------------------------------------------------------------------------+
|RULE																				|
+-----------------------------------------------------------------------------------+
|SELECT ‘first_ Name’ n,’last_name FROM customer WHERE 'customer_ id' = ?			|
|SELECT 'get.customer_balance*' (? . NOW ( ) )										|
|UPDATE  'rental' SET 'return date' = NOW ( ) WHERE 'rental_ id' = ?				|
|SELECT @@'version comment' LIMIT 1													|
+-----------------------------------------------------------------------------------+
```

You then execute this command:
mysql> CALL mysql.sp_set_firewall_mode('fwuser@localhost', 'RESET');

Which two are true?
- A The fwuser@localhost account is removed from the mysql.user table.
- B The information_schema.MYSQL_FIREWALL_WHITELIST table is truncated,
- C The whitelist of the fwuser@localhost account is truncated.
- D The mysql.Firewall_users table is truncated.
- E The firewall resets all options to default values.
- F The fwuser@localhost account mode is set to DETECTING.
- G The fwuser@localhost account mode is set to OFF.

- Answer:
CG

https://dev.mysql.com/doc/refman/8.0/en/firewall-usage.html
CALL mysql.sp_set_firewall_mode(user, 'RESET');
The reset operation causes the firewall to delete all rules for the profile
and set its mode to OFF.


### vvvxx Q125.A newly deployed replication master database has a 10/90 read to write ratio.
The complete dataset is currently 28G but will never fluctuate波动 beyond +- 10%.
The database storage system consists of two locally attached
PCI-E Enterprise grade disks (mounted as /data1 and /data2)
The server is dedicated to this MySQL Instance. System memory capacity is 64G.
The my.cnf file contents are displayed here:
[Imysqld]
datadir=/data1/
innodb_buffer_pool_size=28G
innodb_log_file_size=150M

Which four changes provide the most performance improvement, without sacrificing data integrity?
- A innodb-doublewrite=off
- B innodb_log_group_home_dir=/data2/
- C innodb_log_file_size=1G
- D innodb_undo_directory=/dev/shm
- E log-bin=/data2/
- F innodb_flush_log_at_trx_commit=0
- G sync_binlog=0
H)innodb_buffer_pool_size=32G
I)disable-log-bin

- Answer:
BCEH

F:错误原因：对于复制环境要进行如下设置：
For durability and consistency in a replication setup that uses InnoDB with transactions:
If binary logging is enabled,
set sync_binlog=1.
Always set
innodb_flush_log_at_trx_commit=1.

###? vvv Q126.You have a MySQL instance with GTIDs enabled.
This instance runs more than 100 transactions per second.
You discover that some data was deleted at a particular point in time.
You decide to perform a recovery from the binary logs as they are all available.
Which two commands can restore the database to the point right before data was deleted?
- A mysqlbinlog --skip-gtids.....
- B mysqlbinlog --stop-position....
- C START SLAVE SQL_THREAD UNTIL SQL_BEFORE_GTIDS=.....
- D mysqlbinlog --stop-datetime.....
- E START SLAVE IO_THREAD UNTIL SQL_BEFORE_GTIDS=... ...

- Answer:
AC
怀疑答案：BC

	--skip-gtids:
	Do not include the GTIDs from the binary log files in the output dump file.


###vvxxxvv Q127.You need to find the number of examined rows for queries that have completed.
All relevant configurations are enabled for recording the information.
Which three sources contain the number of examined rows?
A.the Performance_Schema
B.the Information_Schema
C.the error_log
D.the general_query_log
E.the sys schema
F.the slow_query_log

- Answer:
AEF
### #vvvxv Q128.Identify two ways to significantly improve data security.
- A Configure mysqld to run as the system admin account, such as root.
- B Use a private network behind a firewall.
- C Configure mysqld to use only networked disks.
- D Configure MySQL to have only one administrative account.
- E Configure mysqld to use only local disks or attached disks and to have its own account in the host system.
- Answer:
BE




### Q129.Which characters are most commonly used in a SQL injection attack?
- A <and>
- B null (\0) and newline (\n)
- C ^ and $
- D  + and -
- E  'and"

	'
- Answer:
E


### Q130.Examine these commands, which execute successfully on the ic1 host:
mysqlsh> dba.createCluster('cluster1', ###memberWeight:35)
mysqlsh> var mycluster = dba.getCluster()
mysqlsh> mycluster.addInstance(' ic@ic2', ###memberWeight:25）
mysqlsh> mycluster.addInstance(' ic@ic3', ###memberWeight:50)

Now examine this configuration setting, which is the same on all nodes:
group_replication_consistency=BEFORE_ON_PRIMARY_FAILOVER
Which statement is true if primary node ic1 fails?

- A Node ic2 becomes the new primary and existing transactions are considered stale and rolled back.
- B Node ic3 becomes the new primary and existing transactions are considered stale and rolled back.
- C Node ic3 becomes the new primary and is ignored until any backlog of transactions is completed
- D Only two nodes remain so the election process is uncertain and must be done manually.
- E Node ic2 becomes the new primary and is ignored until any backlog of transactions is completed.

- Answer:
C
### Q131.You have upgraded the MySQL binaries from 5.7.28 to 8.0.18 by using an in-place upgrade.
Examine the message sequence generated during the first start of MySQL 8.0.18:

。。。[System]。。。/usx/sbin/mysqld (mysqld 8.0.18-commercial) starting as process 2754
。。。[System]。。。Starting upgrade of data directory. .
。。。[ERROR]。。。Table upgrade required. Please do "REPAIR TABLE 'columns_ priv'" or dump/reload to fix it!
。。。[ERROR]。。。Table upgrade required. Please do "REPAIR TABLE ‘event’" or dump/reload to fix it!
。。。[ERROR]。。。Table upgrade required. Please do "REPAIR TABLE ’proc'" or dump/reload to fix it!
。。。[ERROR]。。。Table upgrade required. Please do "REPAIR TABLE ’proxies_priv’" or dump/reload to fix it!
。。。[ERROR]。。。Table upgrade required. Please do "REPAIR TABLE ‘tables_priv’" or dump/reload to fix it!
。。。[ERROR]。。。Failed to open mysql.event Table.
。。。[ERROR]。。。Failed to open mysql.proc Table.
。。。(ERROR]。。。Failed to Populate DD tables.
。。。[ERROR]。。。Aborting
。。。[System] 。。。 /usr/sbin/mysqld: shutdown complete (mysqld 8.0.18-commercial) MySQL Enterprise Server - Commercial.
Which step or set of steps will resolve the errors?

- A Start mysqld again using the --upgrade=FORCE option.
- B Go to the <datadir>/mysql directory and execute: myisamchk --update-state columns_ priv event proc proxies_ priv tables_ priv.
- C Execute: mysqlcheck --repair mysql columns_ priv event proc proxies_priv tables_priv.
- D Remove the redo logs. Replace the MySQL binaries with the 5.7.28 binaries. Prepare the tables for upgrade. Upgrade to 8.0.18 again.
- E Execute: mysqlcheck --check-upgrade mysql columns_priv event proc proxies_priv tables_priv.

- Answer:
A
	视频答案：C
		mysqlcheck --repair = REPAIR TABLE 比较直接。

	--upgrade 选项
	This option controls whether and how the server performs an automatic upgrade at startup.
	Automatic upgrade involves two steps:
	step1:msyql schema中的数据字典; Performance_Schema ; INFORMATION_SCHEMA
	step2:mysql schema中的其他系统表; sys schema ; User schemas.

	Option Value	Server Performs Step 1?	Server Performs Step 2?
	AUTO			If necessary			If necessary
	NONE			No						No
	MINIMAL			If necessary			No
	FORCE			If necessary			Yes

	`mysqlcheck` uses the SQL statements CHECK TABLE, REPAIR TABLE, ANALYZE TABLE, and OPTIMIZE TABLE in a convenient way for the user. It determines which statements to use for the operation you want to perform, and then sends the statements to the server to be executed.
	REPAIR TABLE checks the table to see whether an upgrade is required. If so, it performs the upgrade, following the same rules as CHECK TABLE ... FOR UPGRADE.



### Q132.You plan to upgrade your MySQL 5.7 instance to version 8.
You have installed the 8 build of MySQL Shell.
Examine this command executed from the operating system shell prompt:
mysqlsh --uri root@localhost:3306 --util check-for-server-upgrade
Which statement is true?
- A It documents any problems with your 5.7 tables to make them ready to upgrade to 8.
- B It fails because the operation name must be in camelCase.
- C It fixes any problems with your 5.7 tables to make them ready to upgrade to 8.
- D It is mandatory to clear the history of prior results before executing this process a second time or later.
- E It fails because checkForServerUpgrade must be executed only within an active shell session as a method of the util object.
- F It is mandatory to run this command so that MySQL 8.0 software''s auto-upgrade process has the details it needs to operate properly.

- Answer:
A
### Q133.Database `test` contains a table named `city` that has the InnoDB storage engine.
CREATE TABLE `city` (
	`ID` int NOT NULL AUTO_ INCREMENT ,
	`Name` char(35) NOT NULL DEFAULT '',
	`Countrycodechar`(3) NOT NULI DEFAULT '',
	`District` char (20) NOT NULI DEFAULT ' ',
	`Population` int NOT NULI DEFAULT '0',
	PRIMARY KEY('ID') ,
	KEY 'CountryCode' ( 'Countrycode' )
) ENGINE= InnoDB TABLESPACE=innodb_file_per_table;
What is the content of the `test` folder in the data directory?
- A city.MYD, city.MYI, and city.sdi
- B city.ibd
- C city. ibd and city.sdi
- D city. ibd and city.frm
- E city.ibd, city. frm, and city.sdi
- Answer:
B


### #vxv Q134.Which two MySQL Shell commands are excluded from the InnoDB Cluster creation procedure?
- A cluster.addInstance()
- B dba.configureLocalInstance()
- C dba.checkInstanceConfiguration()
- D cluster.setPrimaryInstance()
- E dba.configureInstance()
- F dba.createCluster()
- G cluster.forceQuorumUsingPartitionOf()

- Answer:
DG

	D设置主节点
	G从脑裂中恢复

### #vvxv Q135.Which four connection methods can MySQL clients specify with the --protocol option
when connecting to a MySQL server?
- A IPv4
- B SOCKET
- C MEMORY
- D PIPE
- E IPv6
- F FILEO
- G TCP
H)DIRECT

- Answer:
BCDG

--protocol
Value	Transport Protocol Used	Applicable Platforms
TCP		TCP/IP				All
SOCKET	Unix socket file	Unix and Unix-like systems
PIPE	Named pipe			Windows
MEMORY	Shared memory		Windows


### #vxv Q136.Which two authentication plugins require the plaintext client plugin for authentication to work?
- A LDAP authentication
- B SHA256 authentication
- C Windows Native authentication
- D PAM authentication
- E MySQL Native Password
- F LDAP SASL authentication

- Answer:
AD
### #vxv Q137.Where is the default data directory located after installing MySQL using RPM on Oracle Linux 7?
- A /usr
- B /usr/mysql
- C /etc/my.cnf
- D /var/lib/mysql
- E /usr/bin
- Answer:
D

  1、数据库目录
  /var/lib/mysql/
  2、配置文件
  /usr/share/mysql（mysql.server命令及配置文件）
  3、相关命令
  /usr/bin(mysqladmin mysqldump等命令)
  4、启动脚本
  /etc/rc.d/init.d/（启动脚本文件mysql的目录）



### #v## Q138.You must store connection parameters for connecting a Linux-based MySQL client to a remote Windows-based MySQL server listening on port 3309.
Which four methods can be used to configure user, host, and database parameters?
- A Execute the command in a bash script.
- B Embed login information into the SSH tunnel definition.
- C Define a UNIX socket.
- D Execute mysql_config_editor to configure the user connection.
- E Configure~/.ssh/config for public key authentication.
- F Use the usermod program to store static user information.
- G Execute the mysqladmin command to confiaure the user connection.
H)Configure~/.my.cnf.
I)Configure environment variables


- Answer:
ADHI



###vvxv Q139.You plan to install MySQL Server by using the RPM download.
Which two statements are true?
- A You must manually initialize the data directory.
- B You can provide the root password interactively.
- C The MySQL RPM package installation supports deploying multiple MySQL versions on the same host.
- D MySQL uses the RPM relocatable installation target feature.
- E You can find the root password in the error log after the first start.
- F The functionality is split among several RPM package files.

- Answer:
EF
	视频答案EF

###vvxxv Q140.Examine this MySQL Shell command:
dba.rebootClusterFromCompleteOutage()

Which two statements are true? (Choose two.)
- A It reconfigures InnoDB Cluster if the cluster was stopped.
- B It performs InnoDB Cluster instances rolling restart.
- C It only starts all InnoDB Cluster instances.
- D It is not mandatory that all instances are running and reachable before running the command.
- E It stops and restarts all InnoDB Cluster instances and initializes the metadata.
- F It only stops and restarts all InnoDB Cluster instances.
- G It picks the minimum number of instances necessary to rebuild the quorum and reconfigures InnoDB Cluster.

- Answer:
AD

	If your cluster suffers from a complete outage, you can ensure it is reconfigured correctly
	using dba.rebootClusterFromCompleteOutage().
	This operation takes the instance which MySQL Shell is currently connected to and
	uses its metadata to recover the cluster.
	In the event that a cluster''s instances have completely stopped,
	the instances must be started and only then can the cluster be started.



### Q141.Which two are features of MySQL Enterprise Firewall? (Choose two.)
- A recording incoming SQL statement to facilitate the creation of a whitelist of permitted commands
- B blocking of potential threats by configuring pre-approved whitelists
- C modifying SQL statement dynamically with substitutions
- D automatic locking of user accounts who break your firewall
- E provides stateless firewall access to TCP/3306

- Answer:
AB
###!!?vvv Q142.Examine the modified output:
mysql> SHOW SLAVE STATUS\G
*********************************1. row****************************
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
seconds_Behind_Master: 1612

Seconds_Behind_Master value is steadily growing.
What are two possible causes? (Choose two.)

- A The master is most probably too busy to transmit data and the slave needs to wait for more data.
- B One or more large tables do not have primary keys.
- C This value shows only I/O latency and is not indicative of the size of the transaction queue.
- D The master is producing a large volume of events in parallel but the slave is processing them serially.
- E The parallel slave threads are experiencing lock contention.
- Answer:
CD
怀疑答案：BD

A错
	不传输到slave，slave无法增加时间间隔。
C错：	不是I/O的延时
E错：	不并行salve线程，不应该存在锁冲突。

### #vxxvv Q143.You must configure the MySQL command-line client to provide the highest level of
 trust and security when connecting to a remote MySQL Server.
Which value of --ssl-mode will do this?
- A PREFERRED
- B VERIFY_CA
- C REQUIRED
- D VERIFY_IDENTITY
- Answer:
D		=CA证书+服务器主机名

    With --ssl-mode=VERIFY_CA or --ssl-mode=VERIFY_IDENTITY,
	clients require an encrypted connection, and also perform verification
	against the server CA certificate
	and (with VERIFY_IDENTITY) against the server host name in its certificate.

### Q144.Consider this shell output and executed commands:
[root@oel7 ~]# ps aux | grep mysqld
mysql 2076 3.5 24.6 1386852 372572 ? Ssl 12:01 0:01 /usr/sbin/mysqld
[root@oel7 ~] #kill -15 2076
Which statement is true about MySQL server shutdown?
- A kill -15 should be avoided. Use other methods such as mysqladmin shutdown or systemct1 stop mysqld.
- B kill -15 and kill -9 are effectively the same forced shutdown that risk committed transactions not written to disk.
- C kill -15 carries out a normal shutdown process, such as mysqladmin shutdown.
- D mysqld_safe prohibits commands that would harm the operation of the server. An error would be returned by the kill command.

- Answer:
C
### #vxv Q145.Binary log events for the 'mydb1' schema must be copied to a different schema name 'mydb2' .
Which command will do this?
- A mysqlbinlog --rewrite-db='mydb1->mydb2' | mysql
- B mysqlbinlog --datebase=mydb1 --database=mydb2 |mysql
- C mysqlbinlog --rewrite-db=' mydb1' -- rewrite-db=' mydb2' | mysql
- D mysqlbinlog --read-from=remote-server --raw | sed's/mydb1 /mydb2/g' | mysql
- Answer:
A

如：
mysqlbinlog --rewrite-db='dbcurrent->dbold' --rewrite-db='dbtest->dbcurrent' \
    binlog.00001 > /tmp/statements.sql

### Q146.Which command enables rule-based MySQL Auditing capabilities?
- A shell> mysql < audit_log_filter_linux_install.sql
- B shell> mysqld --initialize --log-raw=audit.log
- C mysql>INSTALL PLUGIN audit_log;
- D mysql>INSTALL COMPONENT audit_log;
- Answer:
A


### #vxv Q147.Examine this SQL statement:
mysql> GRANT r_read@localhost To mark WITH ADMIN OPTION;
Which two are true? (Choose two.)
- A Mark can grant the privileges assigned to the r_read@localhost role to another user.
- B ADMIN OPTION causes the role to be activated by default.
- C Mark can grant the r_read@localhost role to another user.
- D Mark can revoke the r_read@localhost role from another role.
- E ADMIN OPTION allows Mark to drop the role.
- F Mark must connect from localhost to activate the r_read@localhost role.
- Answer:
CD

https://dev.mysql.com/doc/refman/8.0/en/grant.html
If you were granted a role with a GRANT statement that includes the WITH ADMIN OPTION clause,
you become able to grant that role to other users or roles, or revoke it from other users or roles,
as long as the role is active at such time as you subsequently grant or revoke it.
This includes the ability to use WITH ADMIN OPTION itself.


### Q148.Which four are types of information stored in the MySQL data dictionary? (Choose four.)
- A performance metrics
- B table definitions
- C access control lists
- D view definitions
- E server runtime configuration
- F server configuration rollback
- G stored procedure definitions
H)InnoDB buffer pool LRU management data


- Answer:
BCDG
###vvvx Q149.You have an InnoDB Cluster configured with three servers.
Examine this command, which executes successfully.
mysqldump -uroot -P -d mydatabase > mydatabase_backup.sq1
Due to data loss, the cluster is initialized and a restore is attempted resulting in this error:
ERROR 13176(HY000) at line 23: Cannot update GTID_PURGED with the Group Replication plugin running
Which two actions, either one of which, can fix this error
and allow a successful restore of the cluster?(Choose two.)

- A Stop all instances except the primary read/write master instance and run the restore.
- B Remove the GLOBAL.gtid_purged statement from the dump file.
- C Create the backup by using the --set-gtid-purged=OFF option.
- D Remove the group replication plugin from each instance before restoring.
- E Remove the GLOBAL.gtid_executed statement from the dump file.
- F Restore using the --set-gtid-purged=OFF option.

- Answer:
BC


### #vxv Q150.Which statement is true about MySQL Enterprise Transparent Data Encryption (TD- E ?
- A MySQL TDE uses an appropriate keyring plugin to store the keys in a centralized location.
- B Both MyISAM and InnoDB tables can be encrypted by setting the keyring_engine = ALL variable in the MySQL configuration file.
- C Lost tablespace encryption keys can be regenerated only if the master database key is known
or present in the Key Vault specification.
- D TDE can encrypt InnoDB and MyISAM tables only when the tables are stored in the SYSTEM tablespace.

- Answer:
A
https://www.mysql.com/products/enterprise/tde.html
the master encryption key is stored in a centralized key management solution .

### Q151.You wish to store the username and password for a client connection to MySQL server in a file on a local file system.
Which is the best way to encrypt the file?
- A Use the AES_ENCRYPT() MySQL function on the option file.
- B Use mysql_secure_installation to encrypt stored login credentials.
- C Use a text editor to create a new defaults file and encrypt it from Linux prompt.
- D Use mysql_config_editor to create an encrypted file.

- Answer:
D
### Q152.You are backing up raw InnoDB files by using mysqlbackup.
Which two groups of files will be backed up during a full backup? (Choose two.)
- A ibbackup files
- B *.CSM files
- C *.sdi files
- D *.ibd files
- E ib_logfile* files

- Answer:
DE
###vvvxv Q153.Examine this statement and output:
myaql> SELECT ROMI NCMBER() OVER() A5 gai,
query, exec ,count, avg_latency, lock_latency
FROM sys.statement_analyais
ORDER BY exec_count;

+---+-------------------------------------------------------------------+-----------+---------------+---------------+
|QN |query																|exec count	|avg_ latency 	|lock_ latency	|
+---+-------------------------------------------------------------------+-----------+---------------+---------------+
|1	|SELECT SUM ( 'k' ) FROM mysch ... () * INTERVAL ? SQL _TSI _HOUR	|381268		| 31.44 ms		|1.01 m		    |
|2	|SELECT 'id' ,’val’,’a’ ,'b...updated' WHERE ' created' <?			|150317		|358.34 us		|30.06 s	    |
|3	|SELECT 'emp_nof' ,'val', 'cre ... ated' + INTERVAL ?SQL_TSI_ DAY	|600		|523.32 ms		|120.24 ms	    |
|4	|SELECT 'a','b','c' FROM m...?AND?CR ’k’ BETWEEN ?AND?				|200		| 10.32 s		| 40.19 ms	    |
|5	|SELECT 'a','b' FROM 'myschem' ... @ ( "emp_no" ) WHERE 'val' =?	|1			| 21.03s		|274.00 us	    |
+---+-------------------------------------------------------------------+-----------+---------------+---------------+
You must try to reduce query execution time.
Which two queries should you focus on? (Choose two.)
- A QN= 3
- B QN= 5
- C QN= 1
- D QN=4
- E QN=2

- Answer:
CD
### ## Q154.You plan to take daily full backups, which include the ndbinfo and sys (internal) databases.
Which command will back up the databases in parallel?

- A mysqldump --single-transaction > full-backup-$ (date +%Y%m%- D  .sql
- B mysqlpump --include-databases=% > full -backup-$ (date +%Y%m$- D  .sql
- C mysqlpump --all-databases > full -backup-$ (date +%Y%m%- D  .sql
- D mysqldump --all -databases > full_ backup-$ (date +%Y%m%- D  .sql
- Answer:
B


### vvvx Q155.Which two commands will display indexes on the parts table in the `manufacturing` schema?
 (Choose two.)
- A DESCRIBE manufacturing.parts;
- B SELECT * FROM information_schema.statistics WHERE table_schema='manufacturing' AND TABLE_ NAME='parts' ;
- C SHOW INDEXES FROM manufacturing.parts;
- D SELECT * FROM information_schema.COLUMN_STATISTICS;
- E EXPLAIN SELECT INDEXES FROM manufacturing.parts ;

- Answer:
BC

	SELECT * FROM information_schema.statistics WHERE table_schema='testdb' and table_name='rental'\G

	There is no INFORMATION_SCHEMA table that corresponds exactly to mysql.indexes,
	but INFORMATION_SCHEMA.STATISTICS contains much of the same information.

### vvv## Q156.Pre-production testing has revealed that your client programs and libraries
are currently incompatible with a staging environment upgrade to MysQL 8.0.
You decide to downgrade to MySQL 5.7 to work on your code.
Which two methods will achieve this?
- A Reinstall the 5.7 binaries.Execute the 5.7''s version of mysql_upgrade with the--force option.
- B Reinstall the 5.7 binaries and reinitialize --datadir.Restore the 5.7 physical backup, which you took before upgrading to 8.0.
- C Reinstall the 5.7 binaries and reinitialize --datadir.Restore a physical backup of your 8.0 tables.
- D Reinstall the 5.7 binaries and reinitialize --datadir.
Restore a logical backup of your 8.0 non-system tables and use a sequence of CREATE USER and GRANT commands to re-create the user accounts.
- E Reinstall the 5.7 binaries. Execute mysqlcheck --repair.

- Answer:
BD
### Q157.Which two are use cases of MySQL asynchronous replication? (Choose two.)
- A You can scale writes by creating a replicated mesh.
- B It guarantees near real-time replication between a master and a slave
- C You can scale reads by adding multiple slaves.
- D MySQL Enterprise Backup will automatically back up from an available slave.
- E It allows backup to be done on the slave without impacting the master.

- Answer:
CE


###?vvxv Q158.Examine this statement and output:
mysql>EXPLAIN
		SELECT country.code,country.name,
				city.name,city.district
		from country
			inner join city on city.countryCode=country.code
		where country.population > 100000000;
**********************1.row***************************
				id	: 1
		select_type	:simple
		table		:country
		partitions	:null
		type		:ALL
		posible_keys:primary
		key			:null
		key_len		:null
		rows		:239
		filtered	:33.33
		extra		:using where
**********************2.row***************************
				id	: 1
		select_type	:simple
		table		:city
		partitions	:null
		type		:ref
		posible_keys:contryCodes
		key			:contryCodes
		key_len		:3
		ref			:world.country.code
		rows		:18
		filtered	:100.00
		extra		:null
	2 rows in set, 1 warings (0.00 se- C

Which two are true?
A.The plan contains a full table scan of the city table.
B.There is a problem with the statement reported as a warning.
C.The output suggests adding an index on the countrycode column.
D.33.33% of rows in the country table will be accessed.
E.The plan contains a full table scan of the country table.
F.It is estimated that 33.33% of rows in the country table match the WHERE clause.

- Answer:
BE
	怀疑答案：EF

B错
warning: 扩展说明
	use SHOW WARNINGS following EXPLAIN to see the extended EXPLAIN output.
rows:
	The rows column indicates the number of rows MySQL believes it must examine to execute the query.
filtered:
	The filtered column indicates an estimated percentage of table rows that are filtered by the table condition.
	Percentage of rows filtered by table condition


### Q159.Which step or set of steps can be used to rotate the error log?
Which two are true ?
- A Execute SET GLOBAL log_error = '<new error log file>'.
- B Execute SET GLOBAL max_error_count = <number of messages at point to rotate>.
- C Execute SET GLOBAL expire_logs_days=0 to enforce a log rotation.
- D Rename the error log file on disk, and then execute FLUSH ERROR LOGS.

- Answer:
D

A错
mysql> SET GLOBAL log_error ='/tmp/mysqld.err';
ERROR 1238 (HY000): Variable 'log_error' is a read only variable
B错
The maximum number of error, warning, and information messages to be stored for display by the SHOW ERRORS and SHOW WARNINGS statements.
C错误
Specifies the number of days before automatic removal of binary log files.




###vvvxxxv Q160.A valid raw backup of the shop.customers MyISAM table was taken.
You must restore the table.
You begin with these steps:
1.Confirm that secure_file_priv='/var/tmp'
2.mysql>DROP TABLE shop.customers;
3.shell>cp /backup/customers.MY* /var/lib/mysql/shop/

Which two actions are required to complete the restore? (Choose two.)
- A shell>cp /backup/customers.sdi /var/tmp
- B shell>cp /backup/customers.sdi /var/lib/mysql/shop/
- C mysql>SOURCE '/var/tmp/customers.sdi'
- D mysql> IMPORT TABLE FROM /var/tmp/customers.sdi
- E shell> cp /backup/customers.frm /var/lib/mysql/shop/
- F mysql> IMPORT TABLE FROM /var/l ib/mysql/shop/customers.Sdi
- G mysql>ALTER TABLE shop.customers IMPORT TABLESPACE
H)mysql> ALTER TABLE shop.customers DISCARD TABLES PACE
- Answer:
AD

	The IMPORT TABLE statement imports MyISAM tables based on information contained
	in .sdi (serialized dictionary information) metadata files.
	IMPORT TABLE requires the FILE privilege to read the .sdi and table content files,
	and the CREATE privilege for the table to be created.



### vvvx Q161.Examine this command, which executes successfully:
cluster.addInstance('<user>@<host>:<port>' ,###recoveryMethod: 'clone' 1)
Which three statements are true? (Choose three.)
- A The account used to perform this recovery needs the BACKUP_ADMIN privilege.
- B A target instance must exist, then it will be provisioned with data from an instance already in the cluster and joined to the cluster,
- C InnoDB tablespaces outside the datadir are able to be cloned.
- D It ls always slower than	###recoveryMethod:'incremental' .
- E A new instance is installed, initialized, and provisioned with data from an instance already in the cluster and joined to the cluster.
- F InnoDB redo logs must not rotate for the duration of the execution; otherwise, the recovery will fail.

- Answer:
ABC

	Clone: choose this option to clone the donor to the instance which you are adding to the cluster,
	deleting any transactions the instance contains. The MySQL Clone plugin is automatically installed.
	The InnoDB Cluster recovery accounts are created with the required `BACKUP_ADMIN` privilege.
	Assuming you are adding an instance which is either empty (has not processed any transactions)
	or which contains transactions you do not want to retain, select the Clone option.
	The cluster then uses MySQL Clone to completely overwrite the joining instance with a snapshot
	from an donor cluster member.


### Q162.Examine this set of messages and responses:
host3:3377 ssl JS >dba.rebootClusterFromCompleteOutage()
Reconfiguring the default cluster from completeoutage...
The instance'host1 :3377' was part of the cluster configuration.
Would you like to rejoin it to the cluster? [y/N] : y
The instance 'host2 :3377'was part of the cluster configuration.
Would you like to rejoin it to the cluster? [y/N] : y
Dba.rebootClusterFromCompleteOutage :The active session instance isn''t the most updated
in comparison with the ONLINE instances of the Cluster''s metadata.
Please use the most up to date instance: 'host1:3377'.
(RuntimeError )
Which statement is true?
- A The instance deployed on host3 must be rebuilt with a backup from the primary instance.
- B The cluster is running and there is at least one ONLINE instance.
- C The instance deployed on host3 must be synchronized from a donor deployed on hos t1 by using thecommand cluster . addInstance ( 'host1:3377' ).
- D It is possible to determine the most up-to-date instance by comparing different global transaction identifier (GTI- D  sets with GTID SUBSET (set1, set2) .
- E The active session instance is invalid and must be re-created by using the command shell.connect( 'host3:3377') .
- Answer:
D


### ?### Q163.Consider an OLTP system with a high volume of concurrent INSERTS and UPDATES.
The overall MySQL Server performance has degraded with the addition of more users performing the same tasks.

What do you recommend?
- A Decrease innodb_lock_wait_timeout.
- B Enable innodb_api_disable_rowlock.
- C Set innodb_autoinc_lock_mode to 1.
- D Disable innodb_rollback_on_timeout.

- Answer:
C
### #vvxv Q164Examine this command and output:
root@dbhost:/var/lib/mysql# ls -al

total 540
drwxrwxr-x 1	mysql	mysql	4096 Aug 22 14:07 .
drwxr-xr-x 1	root	root	4096 May 22 00:42..
-rw-r	1		mysql	mysql	56 Aug 20 13:58 auto.cnf
drwxr-xr-x 1	mysql	mysql	4096 Aug 21 10:28 accounting
-rw-r--r-- 1	mysql	mysql	1112 Aug 20 13:58 ca.Pem
-rw-r	1		mysql	mysql	172040 Aug 22 14:07 ib_buffer_pool
-rw-r	1		mysql	mysql	12582919 Aug 22 14:07 ibdata1
-rw-r	1		mysql	mysql	50331648 Aug 22 14:07 ib_logfile0
-rw-r	1		mysql	mysql	50331648 Aug 20 13:47 ib_logfile1
-rw-r	1		mysql	mysql	292292 Aug 22 14:07 ibtmp1
drwxr-x--- 1	mysql	users	4096 Aug 20 13:59 mysql
-rw-r	1		mysql	mysql	64064 Aug 22 15:18 mysql-error.log
drwxr-x--- 1	mysql	mysql	4096 Aug 20 13:59 performance_ schema
-rw-rw	1		mysql	mysql	1680 Aug 20 13:59 private_key.pem
-rw-r--r-- 1	mysql	mysql	452 Aug 20 13:59 public_key.pem
-rw-r--r-- 1	mysql	mysql	1112 Aug 20 13:58 server-cert.pem
-rw	1			mysql	mysql	1680 Aug 20 13:58 server-key.pem
drwxr-x--- 1	mysql	mysql	4096 Aug 20 13:59 sys

Which two options will improve the security of the MySQL instance? (Choose two.)
- A Remove group read/write privileges from the private_key.pem file.
- B Remove world read privileges from the server-cert.pem certificate file.
- C Change the group ownership of the mysql directory to the mysql user group.
- D Remove world read privileges from the public_key.pem file.
- E Change the parent directory owner and group to mysql.
- F Remove the world read/execute privilege from the accounting directory.
- Answer:
AF


### Q165.A MySQL server is monitored using MySQL Enterprise Monitor''s agentless installation.
Which three features are available with this installation method? (Choose three.)
- A MySQL Replication monitoring
- B network-related information and network characteristics
- C MySQL Query Analysis data
- D CPU utilization
- E security-related advisor warnings
- F operating system memory utilization
- G disk usage and disk characteristics including disk advisors warnings

- Answer:
ACE
### #vxv Q166.You have a MySQL system with 500 GB of data that needs frequent backups.
You use a mix of MylSAM and InnoDB storage engines for your data.
Examine your backup requirement:
The MySQL system being backed up can never be unavailable or locked to the client applications.
The recovery from the backup must work on any system.
Only 1 hour of data can be lost on recovery of the backup.

Which option fulfills all backup requirements?
- A Take a physical backup of the MySQL system.
- B Use the Clone Plugin to copy the data to another MySQL system.
- C Take a logical backup of the MySQL system.
- D Take your backup from a slave of the MySQL system.

- Answer:
D
### Q167.A colleague complains about slow response time on your website.
Examine this query and output:
mysql> show global status like 'Table_ lock%' ;
+------------------------------+---------+
|Variable_name					| Value		|
+------------------------------+---------+
|Table_ locks_ immediate		| 53148		|
|Table_ locks_ waited			| 17716		|
+------------------------------+---------+
2 rows in set (0.00 3e- C
What is the most likely cause for the high number of lock waits?
- A You use the MyISAM storage engine for most common tables.
- B You use the InnoDB storage engine and statements wait while data is inserted.
- C The Innodb Buffer pool is full.
- D Your table accesses wait for the operating system level flush.

- Answer:
A
### Q168.Your MySQL installation is running low on space due to binary logs.
You need to reduce your log space usage urgently.
Which two sets of actions when completed will accomplish this? (Choose two.)
- A Use SET GLOBAL binlog_expire_logs_seconds=<value> and restart the server.
- B Set binlog_expire_logs_seconds in my.cnf.
- C Set binlog_expire_logs_seconds = 0 in my.cnf and restart the server.
- D Use SET PERSIST binlog_expire_logs_seconds=<value>.
- E Use PURGE BINARY LOGS to <binlog_ name>. .
- F Use SET GLOBAL binlog_expire_logs_seconds=<value> and run the FLUSH BINARY LOGS command.

- Answer:
EF
### #vvxxv Q169.Which two storage engines provide a view of the data consistent with the storage system at any moment?
#哪两个存储引擎可随时提供与存储系统一致的数据视图？
(Choose two.)
- A MyISAM
- B NDB
- C MEMORY
- D ARCHIVE
- E InnoDB

- Answer:
BE



### #vxv Q170.Examine Joe''s account:
CREATE USER 'joe'@'%' IDENTIFIED BY '*secret*';
GRANT ALL PRIVILEGES ON *.* TO 'joe'@'%';

All existing connections for joe are killed.

Which two commands will stop joe establishing access to the MySQL instance
- A Alter USER 'joe'@'%' ACCOUNT LOCK
- B AITER USER 'joe'@'%' SET password='*invalid*'
- C REVOKE All PRIVGESON ON *.* FROM 'joe'@'%'
- D REVOKE USAGE on *.* FROM 'joe'@'%'
- E ALTER USER 'joe'@'%' IDENTIFIED BY '*invalid*' PASSWORD EXPIRE
- F AlTER USER 'joe'@'%' PASSWORD HISTORY 0

- Answer:
AE
### #vxv Q171.Examine this MySQL client command to connect to a remote database:
mysql -h remote-example.org -u root --protocol=TCP --ssl-mode=
Which two --ss1-mode values will ensure that an X.509-compliant certificate
will be used to establish the SSL/TLS connection to MySQL?
- A VERIFY_CA
- B REQUIRED
- C DISABLED
- D PREFERRED
- E VERIEY_IDENTITY

- Answer:
AE

--ssl-mode 是客户端连接参数
X.509是公钥基础设施（PKI）的标准格式。X.509证书就是基于国际电信联盟（ITU）制定的X.509标准的数字证书。
X.509证书主要用于识别互联网通信和计算机网络中的身份，保护数据传输安全。X.509证书无处不在，比如我们每天使用的网站、移动应用程序、电子文档以及连接的设备等都有它的身影。
X.509证书的结构优势在于它是由公钥和私钥组成的密钥对而构建的。
公钥和私钥能够用于加密和解密信息，验证发送者的身份和确保消息本身的安全性。
基于X.509的PKI最常见的用例是使用SSL证书让网站与用户之间实现HTTPS安全浏览。X.509协议同样也适用于应用程序安全的代码签名、数字签名和其他重要的互联网协议。

### Q172.Mary connects to a Linux MySQL Server from a client on a Windows machine.
Examine this statement and output:
mysql> SELECT USER () , CURRENT USER () ;
+-------------------+-------------------+
|USER ()			| CURRENT_USER ( ) 	|
+-------------------+-------------------+
|mary@192.0.2.101	|	mary@%			|
+-------------------+-------------------+
row in set (0.00 se- C

Which two are true?
- A Mary connected using a UNIX socket.
- B Mary connected from a client machine whose IP address is 192.0.2.101.
- C Mary connected to the database server whose IP address is 192.0.2.101.
- D Mary has the privileges of account mary@%.
- E Mary authenticated to the account mary@192.0.2.101.
- Answer:
BD


###? #v Q173.Examine this command, which executes successfully:
mysqlbackup --defaults-file=/backups/server/my.cnf --backup-dir=/backups/full copy-back
Which statement is true about the copy-back process?
- A It restores files from the backup directory to their original MySQL server locations .
- B The copy-back process makes inconsistent backups.
- C The copy-back process is used to overwrite a new backup over an existing backup.
- D It restores files from the data directory to their original MySQL server locations.
- Answer:
D
	怀疑答案：A
	copy-back:
	Restores files from a directory backup to their original locations within the MySQL server.



### Q174.You are planning to take a full MySQL instance backup .
Which two factors will help to determine the backup strategy?
- A the expected size of the backup
- B how much down time is planned
- C the OS super user rights
- D the number of user accounts for the MySQL instance
- E the location of the physical server
- Answer:
AB


### #### Q175.Which three are characteristics of logical backups?
- A They consist of exact copies of database directories and files.
- B They can be created by mysqlbackup for InnoDB tables or by file-system commands, such as cp, scp, tar, or rsync, for MyISAM tables .
- C They can be performed while the MySQL server is not running.
- D They can be run only against a running MySQL server.
- E They are machine independent and highly portable.
- F Backup and restore granularity is available at the server level, database level, or table level for any storage engine.
- G In addition to databases, backups can include any related files, such as log or configuration files.
- Answer:
DEF


### Q176.What does the slave SQL thread do?
- A reads the relay log and executes the events contained in them
- B connects to the master and asks it to send updates recorded in its binary logs
- C acquires a lock on the binary log for reading each event to be sent to the slave
- D monitors and maintains channel connections if multiple replication channels are used
- Answer:
A
### Q177.Examine this statement and output:
mysql> SHOW GRANTS FOR jsmith;
+----------------------------------------------------------+
|Grants for jsmith@%                                       |
+----------------------------------------------------------+
|GRANT USAGE ON *.* TO `jsmith`@`%`                        |
|GRANT UPDATE(NAM- E  ON `world`.`country` TO `jsmith`@`%`   |
+----------------------------------------------------------+

Which two SQL statements can jsmith execute?
A.UPDATE world.country SET Name='first' ORDER BY Name LIMIT 1;
B.UPDATE world.country SET Name='new' WHERE Name='old';
C.UPDATE world.country SET Name='one' LIMIT 1;
D.UPDATE world.country SET Name=CONCAT ('New ',Nam- E ;
E.UPDATE world.country SET Name='all';

- Answer:
CE


###? #x## Q178.Which three components comprise MySQL InnoDB Cluster to achieve database high availability?
哪三个组件构成MySQL InnoDB集群以实现数据库的高可用性?
A.MySQL Servers with Group Replication to replicate data to all members of the cluster
B.MySQL X Plugin to enable MySQL to use the X Protocol to speed up and monitor data replication
C.MySQL Semi-Sync replication plugin is used to provide cluster consistency
D.MySQL Shell to create and administer InnoDB Clusters using the built-in AdminAPI
E.MySQL Enterprise Backup to keep data consistent and always ready to be used.
F.MySQL Router to ensure that client requests are load balanced and routed to the correct servers
- Answer:
ABF
	怀疑答案：ADF
		应该是innodb cluster的组件：组复制，shell,  router



###? ##Q179.Examine these statements, which execute successfully:

CREATE ROLE r_world_rd;
GRANT SELECT ON world.* to r_world_rd;
create user join indentified by 'P@ssw0rd';
grant r_world_rd to join;

Examin these statements issue
mysql>SHOW GRANTS;
+---------------------------------------+
|GRANTS FOR john@%                      |
+---------------------------------------+
|GRANT USAGE ON *.* TO `john`@`%`       |
|GRANT `r_world_rd`@`%` to `john`@`%`   |
+---------------------------------------+
2 rows in set(0.01se- C
mysql>SELECT * FROM world.city;
ERROR 1142 (42000): SELECT command denied to user 'john'@'localhost' for table 'city'

What is the reason for the error?
A.John needs to reconnect to the database.
B.The statement was blocked by MySQL Firewall.
C.John has not activated the role.
D.The DBA needs to execute FLUSH PRIVILEGES.

- Answer:
D
怀疑答案，应该是C
https://dev.mysql.com/doc/refman/8.0/en/roles.html#roles-activating

#测试
#创建用户及角色
CREATE ROLE r_world_rd;
GRANT SELECT ON newdb.* to r_world_rd;
create user john identified  with mysql_native_password by 'P@ssw0rd';
grant r_world_rd to john;
#john登录
mysql -ujohn -p'P@ssw0rd' -h172.16.50.13 -P16315
#缺省没有角色
mysql> select current_role();
+----------------+
| current_role() |
+----------------+
| NONE           |
+----------------+
mysql> set role all;  #激活所有角色
mysql> select current_role();
+------------------+
| current_role()   |
+------------------+
| `r_world_rd`@`%` |
+------------------+
mysql> select * from newdb.sys_sequence;
+------+---------------+
| name | current_value |
+------+---------------+
| a    |             1 |
| b    |             1 |
+------+---------------+
#也可以有特权用户激活role
set default role all to john;
#或者使用系统参数激活所有用户的角色
activate_all_roles_on_login=1



###? Q180.Which three are features of MySQL Enterprise Backup?
A.the ability to use the output file, which contains SQL statements, as input to a MySQL session
B.the ability to edit the output file produced by MySQL Enterprise Backup
C.the ability to back up individual tables or individual tablespaces while your server instance is online
E.the ability to extract and restore individual rows from a backup
F.the ability to perform incremental backups
- Answer:
ACF
	CF确定正确，或许选E
	可以备份指定条件的table
	To select specific data to be backed up or restored, use the partial backup and restore options described in this section.



### Q181.Which two statements are true about InnoDB data-at-rest encryption?
A.It does not support the transportable tablespaces feature.
B.It supports only non-blob datatypes.
C.It enforces encryption from disk to memory and over network transmission.
D.It decrypts data for use in memory
E.It supports all indexes transparently.
- Answer:
DE
	静态数据加密：
	InnoDB supports data-at-rest encryption for file-per-table tablespaces, general tablespaces, the mysql system tablespace, redo logs, and undo logs.

	Can indexed columns be encrypted?
	InnoDB data-at-rest encryption supports all indexes transparently.




### Q182.Examine this statement, which executes successfully:
CREATE TABLE rental(
	rental_id int unsigned NOT NULL AUTO_INCREMENT,
	rental_date datetime NOT NULL,
	inventory_id int unsigned NOT NULL,
	customer_id int unsigned NOT NULL,
	return_date datetime DEFAULT NULL,
	staff_id int unsigned NOT NULL,
	last_update timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
	PRIMARY KEY(rental_i- D
) ENGINE=InnoDB;

Now examine this query:

SELECT rental_id, customer_id
FROM rental
WHERE rental_date BETWEEN(NOW() - INTERVAL 1 MONTH) AND NOW()
AND inventory_id = 42
AND staff_id = 1024;

You want to add one or more indexes that minimize the work done by the query.
Which statement accomplishes this?

A.ALTER TABLE rental ADD INDEX (inventory_id, staff_id, customer_id, rental_id, rental_dat- E ;
B.ALTER TABLE rental ADD INDEX (inventory_i- D  ADD INDEX (staff_i- D , ADD INDEX (rental_dat- E , ADD INDEX (customer_i- D ;
C.ALTER TABLE rental ADD INDEX (inventory_i- D , ADD INDEX (staff_i- D , ADD INDEX (rental_dat- E ;
D.ALTER TABLE rental ADD INDEX D(rental_date, inventory_id, staff_id, customer_i- D ;
E.ALTER TABLE rental ADD INDEX (inventory_id, staff_id, rental_date, customer_i- D ;
F.ALTER TABLE rental ADD INDEX (inventory_id, staff_id, rental_dat- E ;


- Answer:
D

实测DE都使用索引，使用索引和索引列的前后顺序无关。都可以使用。

mysql> show index from rental;
+--------+------------+-------------+--------------+--------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
| Table  | Non_unique | Key_name    | Seq_in_index | Column_name  | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment | Visible | Expression |
+--------+------------+-------------+--------------+--------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
| rental |          0 | PRIMARY     |            1 | rental_id    | A         |     1994168 |     NULL |   NULL |      | BTREE      |         |               | YES     | NULL       |
| rental |          1 | rental_date |            1 | rental_date  | A         |           1 |     NULL |   NULL |      | BTREE      |         |               | YES     | NULL       |
| rental |          1 | rental_date |            2 | inventory_id | A         |          41 |     NULL |   NULL |      | BTREE      |         |               | YES     | NULL       |
| rental |          1 | rental_date |            3 | staff_id     | A         |       43071 |     NULL |   NULL |      | BTREE      |         |               | YES     | NULL       |
| rental |          1 | rental_date |            4 | customer_id  | A         |     1993856 |     NULL |   NULL |      | BTREE      |         |               | YES     | NULL       |
+--------+------------+-------------+--------------+--------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
5 rows in set (0.01 se- C

mysql> explain SELECT rental_id, customer_id
    -> FROM rental
    -> WHERE rental_date BETWEEN (NOW() - INTERVAL 1 MONTH) AND NOW()
    -> AND inventory_id = 42
    -> AND staff_id = 1024;
+----+-------------+--------+------------+-------+---------------+-------------+---------+------+--------+----------+--------------------------+
| id | select_type | table  | partitions | type  | possible_keys | key         | key_len | ref  | rows   | filtered | Extra                    |
+----+-------------+--------+------------+-------+---------------+-------------+---------+------+--------+----------+--------------------------+
|  1 | SIMPLE      | rental | NULL       | range | rental_date   | rental_date | 13      | NULL | 997084 |     1.00 | Using where; Using index |
+----+-------------+--------+------------+-------+---------------+-------------+---------+------+--------+----------+--------------------------+
1 row in set, 1 warning (0.00 se- C

mysql> show index from rental;
+--------+------------+--------------+--------------+--------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
| Table  | Non_unique | Key_name     | Seq_in_index | Column_name  | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment | Visible | Expression |
+--------+------------+--------------+--------------+--------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
| rental |          0 | PRIMARY      |            1 | rental_id    | A         |     1994168 |     NULL |   NULL |      | BTREE      |         |               | YES     | NULL       |
| rental |          1 | inventory_id |            1 | inventory_id | A         |          41 |     NULL |   NULL |      | BTREE      |         |               | YES     | NULL       |
| rental |          1 | inventory_id |            2 | staff_id     | A         |       44050 |     NULL |   NULL |      | BTREE      |         |               | YES     | NULL       |
| rental |          1 | inventory_id |            3 | rental_date  | A         |       43561 |     NULL |   NULL |      | BTREE      |         |               | YES     | NULL       |
| rental |          1 | inventory_id |            4 | customer_id  | A         |     1993366 |     NULL |   NULL |      | BTREE      |         |               | YES     | NULL       |
+--------+------------+--------------+--------------+--------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
5 rows in set (0.00 se- C
mysql> explain SELECT rental_id, customer_id FROM rental WHERE rental_date BETWEEN (NOW() - INTERVAL 1 MONTH) AND NOW() AND staff_id = 1024 AND inventory_id = 42;
+----+-------------+--------+------------+-------+---------------+--------------+---------+------+------+----------+--------------------------+
| id | select_type | table  | partitions | type  | possible_keys | key          | key_len | ref  | rows | filtered | Extra                    |
+----+-------------+--------+------------+-------+---------------+--------------+---------+------+------+----------+--------------------------+
|  1 | SIMPLE      | rental | NULL       | range | inventory_id  | inventory_id | 13      | NULL |   52 |   100.00 | Using where; Using index |
+----+-------------+--------+------------+-------+---------------+--------------+---------+------+------+----------+--------------------------+
1 row in set, 1 warning (0.00 se- C




###? ###Q183.Examine this statement, which executes successfully:
mysql>create user mary@192.0.2.100 identified by 'P@SSw0rd' require none password expire;

Which two are true?
A.Mary cannot query data until she changes her password.
B.Mary requires no password to connect to the MySOL server.
C.Mary cannot connect to the MysQL server until the DBA resets her password.
D.Mary must connect from the client machine 192.0.2.100.
E.Mary must connect using the username 'mary@192.0.2.100'


- Answer:
CD
怀疑答案：应该是AD
	Mark the password expired so that the user must choose a new one at the first connection to the server


###? ###Q184. Test is a non-empty InnoDB table.
Examine these statements, which are executed in one session:
mysql>BEGIN;
mysql>SELECT * FROM test FOR UPDATE;

Which is true?
A.If ANALYZE TABLE test; is invoked from the same session, it hangs until the transaction is committed or rolled back.
B.If OPTIMIZE TABLE test; is invoked, it will create a table lock on t and force a transaction rollback.
C.If OPTIMIZE LOCAL TABLE test; is invoked from another session, it executes normally and returns the status.
D.mysqlcheck --analyze --all-databases will execute normally on all tables and return a Report

- Answer:
C

	视频答案：B
	个人认为：D   mysqlcheck --analyze是收集统计信息，可以正常结束。

实测结果：
A
mysql> ANALYZE TABLE test;
+------------+---------+----------+-----------------------------+
| Table      | Op      | Msg_type | Msg_text                    |
+------------+---------+----------+-----------------------------+
| newdb.test | analyze | status   | Table is already up to date |
+------------+---------+----------+-----------------------------+
1 row in set (0.00 se- C
B
mysql> OPTIMIZE TABLE test;
+------------+----------+----------+-----------------------------+
| Table      | Op       | Msg_type | Msg_text                    |
+------------+----------+----------+-----------------------------+
| newdb.test | optimize | status   | Table is already up to date |
+------------+----------+----------+-----------------------------+
1 row in set (0.01 se- C

C
mysql> OPTIMIZE LOCAL TABLE test;
+------------+----------+----------+-----------------------------+
| Table      | Op       | Msg_type | Msg_text                    |
+------------+----------+----------+-----------------------------+
| newdb.test | optimize | status   | Table is already up to date |
+------------+----------+----------+-----------------------------+
1 row in set (0.00 se- C

D
[root@host12 /]# /edb/app/mysql-8.0.23/bin/mysqlcheck -uadmin -p'!QAZ2wsx' -h172.16.50.12 -P16315 --analyze --all-databases
newdb.sys_sequence                                 OK
newdb.test                                         Table is already up to date
one_db.test                                        OK






### Q185.What does the binlog dump thread do?
- A It monitors and schedules the rotation/deletion of the binary logs.
- B It reads the relay log and executes the events contained in them.
- C It acquires a lock on the binary log for reading each event to be sent to the slave.
- D It connects to the master and asks it to send updates recorded in its binary logs.

- Answer:
C
###? ###Q186.You must replay the binary logs on your MysQL server.
Which command do you use?
- A  mysqlbinlog binlog.000003 binlog.000004 binlog.000005 | mysql -h 127.0.0.1
- B  mysqlbinlog -h 127.0.0.1 binloq.000003 binloq.000004 binloq.000005
- C  mysglpump -h 127.0.0.1 binlog. 000003 binlog.000004 binlog.000005
- D  cat binlog. 000003 binlog. 000004 binlog.000005 | mysql -h 127.0.0.1
- E  mysql -h 127.0.0.1 --local-infile binlog.000003 binlog.000004 binloq.000005


- Answer:
B
怀疑答案：应该是A

###?! ##Q187.Examine these commands and output
mysql>SHOW FULL PROCESSLIST;
+-------+---------------+-------------------------------+-------------------------------+
|ID		|	USER		|state							|	info						|
+-------+---------------+-------------------------------+-------------------------------+
|6		|event_scheduler|waiting on empty queue			|	null						|
|20		|root			|								|								|
|21		|root			|								|								|
|22		|root			|waiting for table metadata lock|optimize table	test.demo_test	|
|24		|root			|waiting for table metadata lock|select * from	test.demo_test	|
|25		|root			|staring 						|show full proceeelist			|
+-------+---------------+-------------------------------+-------------------------------+

mysql>SELECT object_type,object_schema,object_name,lock_type,lock_status,
owner_thread_id,owner_event_id
FROM performance_schema.metadata_locks WHERE object_schema != 'performance_schem';
+---------------+---------------+---------------+-----------------------+-----------+---------------+---------------+
|object_type	|object_schema	|object_name	|lock_type				|lock_status|owner_thread_id|owner_event_id	|
|table 			|test			|demo_test		|shared_read			|granted	|60				|7				|
|table 			|test			|demo_test		|shared_write			|granted	|60				|9				|
|schema			|test			|NULL			|intention_execusive	|granted	|62				|6				|
|table 			|test			|demo_test		|shared_no_read_write	|pending	|62				|6				|
+---------------+---------------+---------------+-----------------------+-----------+---------------+---------------+

mysql>SELECT thread_id,processlist_id,processlist_user,parent_thread_id
FROM performance_schema.threads WHERE processlist_user='root';
+-----------+---------------+-------------------+-------------------+
|thread_id	|processlist_id	|processlist_user	|parent_thread_id	|
|	60		|	20			|	root			|		null		|
|	61		|	21			|	root			|		null		|
|	62		|	22			|	root			|		1			|
|	64		|	24			|	root			|		1			|
|	65		|	25			|	root			|		null		|
+-----------+---------------+-------------------+-------------------+

Which connection ID is holding the metadata lock?
A.6
B.22
C.20
D.21
E.24
F.25

- Answer:
A
	怀疑答案：thread_id 60获取了表demo_test的shared_read,shared_write锁，答案应该是C
	视频答案：C

###? vvx## Q188.Which statement is true about displaying and retrieving data with MySQL Enterprise Monitor Query Analyzer?
A.It is possible to export statements included in a graph selection in CSV format.
B.The Query Analyzer graph view range selector can extend to cover the same hour over multiple days
C.It is possible to filter a Query Analyzer view graph by database and by table.
D.The Query Analyzer can plot a CPU utilization graph for remote hosts with a MySQL Enterprise Service Manager''s built-in Agent installation.

- Answer:
D
	可以选C
D错误

### Q189.Which three statements are true about data dictionary in MySQL Server?
A.It auto detects when a new schema is moved into datadir.
B.It facilitates fast DDL by using transactional storage.
C.It stores frequently used query plans.
D.It uses the transactional storage engine
E.It is based on SQL standards
F.It provides data to INFORMATION_SCHEMA.


- Answer:
BDF
###vvxv # Q200.Which two tasks are performed by the mysql_secure_installation program?
A.It downloads the latest MySQL software over a secure connection and installs it
B.It checks whether the hash value on downloaded software from MySQL repositories matches the official count.
C.It requires setting a password for the root account.
D.It removes anonymous accounts.
E.It checks whether all account passwords match the server''s established security level.
F.It properly sets the file permissions and file ownership for MysQL server files.

- Answer:
	CD

This program enables you to improve the security of your MySQL installation in the following ways:
	You can set a password for root accounts.
	You can remove root accounts that are accessible from outside the local host.
	You can remove anonymous-user accounts.
	You can remove the test database, and privileges that permit anyone to access databases with names that start with test_.



###!vxxxvv Q201.You must determine if your MySQL server sort_buffer_size parameter setting is appropriate for your workload.
Which two commands will show the relevant systemwide counters?
- A SHOW STATUS;
- B SELECT * FROM performance_schema.global_status;
- C SELECT * FROM performance_schema.session_status;
- D SHOW ENGINE INNODB STATUS;
- E SELECT * FROM information_schema.innodb_metrics;
- F SHOW GLOBAL STATUS;

- Answer:
	CE
###? # vv##Q202.Which two statements are true about general tablespaces?
A.A new table can be created explicitly in a general tablespace.
B.General tablespaces support temporary tables
C.A general tablespace can have multiple data files.
D.Dropping a table from a general tablespace releases the space back to the operating system.
E.An existing table can be moved into a general tablespace

###	Answer:
CE
	视频答案：AE
A也应该是正确的。
https://dev.mysql.com/doc/refman/8.0/en/general-tablespaces.html#general-tablespaces-creating
The TABLESPACE option can be used with CREATE TABLE to create tables in a general tablespaces,
file-per-table tablespace, or in the system tablespace.

The TABLESPACE option can be used with ALTER TABLE to move tables between general tablespaces,
file-per-table tablespaces, and the system tablespace.

C错： 只能有一个文件




###? ###Q203.Examine this statement, which executes successfully:
mysql>SOURCE /usr/share/mysql-8.0/audit_log_filter_linux_install.sql
You then reconnect to MySQL and execute several queries.
Examine the resulting text content of the <datadir>/audit.log file:
<?xml version="1.0" encoding="UTF-8"?>
<AUDIT>
	<ADUIT_RECORD>
		<TIMESTAMP>2019-12-12t04:36:39 UTC</TIMESTAMP>
		<RECORD_ID>1_2019-12-12t04:36:39 UTC</RECORED_ID>
		<NAME>audit</NAME>
		<SERVER_ID>1</SERVER_ID>
		<VERSION>1</VERSION>
		<START_OPTIONS>/usr/sbin/mysqld</STARTUP_OPTIONS>
		<OS_VERSION>X86_64-linux</OS_VERSION>
		<MYSQL_VERSION>8.0.18-commerical</MYSQL_VERSION>
	</AUDIT_RECORD>
</AUDIT>


Why does the file contain no visible statement events?
A.You must use the audit_log_filter_set_filter() and audit_log_filter_set_user() functions to specify what to log
B.You must add audit_1og=oN to the MySQL configuration file and restart MysQL to log statements.
C.You must use the audit_log_read() and audit_log_read_bookmark() functions to read the statement events.
D.You must wait for the audit log buffer to fill before it will flush to disk.

	- Answer:
	AB
	应该是单选题，A   和214题重复

### #vxv Q204.Which statement is true about the my.ini file on a Windows platform while MySQL server is running?

A.MySQL server does not use the my.ini option file for server configuration options.
B.The option file is read by the MySQL server service only at start up
C.Using SET PERSIST will update the my.ini file.
D.Editing the file will immediately change the running server configuration.

	- Answer:
	B

	C：错误，set persist只能更改/datadir下的mysqld-auto.cnf
	SET can also be used to persist certain global system variables to the mysqld-auto.cnf file in the data directory,
	to affect server operation for subsequent startups.
	RESET PERSIST removes persisted settings from mysqld-auto.cnf.


### Q205.You have replication configured, which consists of one master and one slave on different hosts with an asynchronous replication channel between them.
Your goal is to decrease the amount of data that is transferred between these two hosts.
It is confirmed that the slave instance does not need to have data from the example database.
Which replication filter contributes to your goal?

A.on master:--replicate-ignore-db=example
B.on slave: --binlog-ignore-db=example
C.on slave:--replicate-wild-ignore=example.%
D.on slave: --replicate-ignore-db-example
E.on master: --binlog-ignore-db=example

- Answer:
E
###vvvxv Q206.Identify three functions of MysQL Enterprise Monitor.
A.Determine the availability of monitored MySQL servers.
B.Analyze query performance.
C.Centrally manage users.
D.Centrally manage server configurations.
E.Start and stop MySQL Server.
F.Start a logical backup.
G.Start a MySQL Enterprise backup.
H.Create customized alerts and provide notification alerts.

	- Answer:
ABH
### Q207.Examine the command
shell# mysqldump --master-data=2 --all-databases > full_backup.sql
You want to make incremental backups every hour after the full backup.
Which log file would be used for incremental backups?
A.slow query log
B.error log
C.innodb redo log
D.binary log.

- Answer:
D
### #vxv Q208.Which two are requirements for multiple MySQL servers started by systemd?
A.Each must have a unique socket file and TCP/IP port.
B.Each must have unique passwords
C.Each require separate server-id numbers
D.Each must have a unique data directory.
E.Each must have a unique server-uuid configured.

- Answer:
AD
###?vvx #Q209.MySQL Enterprise Monitor Query Analyzer is confiqured to monitor an instance.
Which statement is true?
A.An agent must be installed locally on the instance to use the Query Analyzer.
B.The Query Response Time index (QRTi) is fixed to 100ms and cannot be customized.
C.The slow query log must be enabled on the monitored server to collect information for the Query Analyzer.
D.The Query Analyzer can monitor an unlimited number of normalized statements
E.Enabling the events_statements_history_long consumer allows tracking the longest running query.

- Answer:
E

	更正答案:A:正确

	Query Analyzer collects information on the SQL statements MySQL client applications send to the MySQL server
	using the Performance Schema statement digests (MySQL Server 5.6.14 and abov- E .
	Data can be gathered directly from MySQL Server without additional configuration,
	using a MySQL Enterprise Monitor Agent.

	For MySQL 5.6.14 and greater, the Agent also collects digested query data from the Performance Schema
	and populates the Query Analyzer.


B:错误：QRTi可以调整

C:错误
The MySQL Query Analyzer retrieves data from the Performance Schema.
The Performance Schema supplies the statistical information about the queries, execution times, result counts and other data to display and analyze on the Query Analyzer page.
E:错误，events_statements_history_long 开关是保存长期历史数据的数量，改参数是使用enable example query特性的前提。
		Enable Example Query: provides detailed data about the queries and their parameters.
		Enabling this parameter increases the RAM used by the monitoring agent.
		Important
		This feature requires events_statements_history_long be enabled in performance_schema.setup_consumers.

###? ###Q210.Which two methods can be used to determine whether a query uses the hash join algorithm?
A.EXPLAIN FORMAT=TREE
B.EXPLAIN FORMAT=JSON
C.EXPIAIN ANALYZE
D.EXPLAIN FORMAT=TRADIRIONAL
E.EXPIAIN without any formatting argument


- Answer:
CE

怀疑答案，应该是AC

	学生手册：Can be shown only by using EXPLAIN FORMAT=TREE and EXPLAIN ANALYZE
	E 也可以
	The FORMAT option can be used to select the output format.
	TRADITIONAL ：缺省值 presents the output in tabular format.
	JSON format displays the information in JSON format.
	In MySQL 8.0.16 and later,
	TREE ：provides tree-like output with more precise descriptions of query handling than the TRADITIONAL format;
			it is the only format which shows hash join usage
	MySQL 8.0.18 introduces EXPLAIN ANALYZE, which runs a statement and produces EXPLAIN output
		along with timing and additional, iterator-based, information about how the optimizer''s expectations matched the actual execution.
		EXPLAIN ANALYZE always uses the TREE output format.
	实际测试E也可以
	Extra=using where;Using join buffer(hash join)




### Q211.Which two statements are true about using backups of the binary log?
A.Multiple binary logs can be used to restore data.
B.They allow for point-in-time recovery of the data
C.Binary logs can always be used to unapply unwanted schema changes.
D.Binary logs are relatively small, and therefore, excellent for long-term storage and disaster recovery.
E.Multiple binary logs can be applied in parallel for faster data restoration.

- Answer:
AB



### Q212.Which statement is true about cold backups?
- A They are backups taken from snapshots of a running database.
- B They are good to use when many users are online accessing the database.
- C They are good to use if only data structures must be backed up but not log files.
- D They are backups taken from OS copy commands


- Answer:
D


### Q213.Your MYSQL server was upgraded from an earlier major version.
The `sales` database contains three tables, one of which is the transactions table, which has 4 million rows.
You are running low on disk space on the datadir partition and begin to investigate.
Examine these commands and output:

mysql>show global variable like 'innodb_file%';
+-----------------------------------+-----------+
| Variable_name						| Value		|
+-----------------------------------+-----------+
| innodb_file_per_table				| On		|
+-----------------------------------+-----------+
#ls -l |grep ib
-rw-r------ 1 mysql mysql 3287			Dec 12 07:75	ib_buffer_pool
-rw-r------ 1 mysql mysql 1258659846468	Dec 12 07:75	ibdata1
-rw-r------ 1 mysql mysql 3287			Dec 12 07:75	ib_logfile0
-rw-r------ 1 mysql mysql 3287			Dec 12 07:75	ib_logfile1
-rw-r------ 1 mysql mysql 3287			Dec 12 07:75	ibtmp1
-rw-r------ 1 mysql mysql 3287			Dec 12 07:75	mysql.ibd

#ls -l sales/
totla 544
-rw-r------ 1 mysql mysql 47550316		Dec 12 07:75	sales.ibd
-rw-r------ 1 mysql mysql 114688		Dec 12 07:75	leads.ibd

Which two statements are true?

A.Truncating the transactions table will free up the most disk space.
B.Executing SET GLOBAL innodb_row_format=COMPRESSED and then ALTER TABLE transactions will free up disk space
C.Truncating the `sales` and `leads` table will free up disk space
D.Executing ALTER TABLE transactions will enable you to free up disk space
E.The transactions table was created with innodb_file_per_table=OFF.

	- Answer:
	CE
### Q214.Examine this statement, which executes successfully:
mysql>SOURCE /usr/share/mysql-8.0/audit_log_filter_linux_install.sql
You then reconnect to MySQL and execute several queries.
Examine the resulting text content of the <datadir>/audit.log file:
<?xml version="1.0" encoding="UTF-8"?>
<AUDIT>
	<ADUIT_RECORD>
		<TIMESTAMP>2019-12-12t04:36:39 UTC</TIMESTAMP>
		<RECORD_ID>1_2019-12-12t04:36:39 UTC</RECORED_ID>
		<NAME>audit</NAME>
		<SERVER_ID>1</SERVER_ID>
		<VERSION>1</VERSION>
		<START_OPTIONS>/usr/sbin/mysqld</STARTUP_OPTIONS>
		<OS_VERSION>X86_64-linux</OS_VERSION>
		<MYSQL_VERSION>8.0.18-commerical</MYSQL_VERSION>
	</AUDIT_RECORD>
</AUDIT>

Why does the file contain no visible statement events?
A.You must wait for the audit loa buffer to fill before it will flush to disk.
B.You must read the audit log statements through the Performance Schema.
C.You must add audit_log = ON to the MySQL confiquration file and restart MySQL to log statements.
D.You must use the audit_log_filter_set_filter() and audit_log_filter_set_user() functions to specify what to loq.
E.You must use the audit_log_read() and audit_log_read_bookmark() functions to read the statement events.

- Answer:
D



##视频补充
### # 1.A user wants to connect without entering his or her username and password on the linux command pormpt.
which three locations an be used to store user''s mysql credentidals to satisfy this requirement?
- A  $HOME/.mysqlrc file
- B  /etc/my.cnf file
- C  DATADIR/mysqld-auto.cnf file
- D  $HOME/.my.cnf file
- E  $HOME/.mylogin.cnf file
- F  $MYSQL_HOME/my.cnf file
- E  $HOME/.mysql/auth/login file

answer:###
BDE



### # 2. Which two are contained in the InnoDB system tablespace (ibdata1) by default?
A doublewrite buffer
B channge buffer
C Innodb data directory
D primary indexes
E table data
F user privileges

answer:###
AB

The system tablespace is the storage area for the change buffer. It may also contain table and index data if tables are created in the system tablespace rather than file-per-table or general tablespaces.
In previous MySQL releases, the system tablespace also contained the doublewrite buffer storage area. This storage area resides in separate doublewrite files as of MySQL 8.0.20.



### # 3. Which three are characteristics of a newly created roles?
A It is stored in the mysql.role table.
B It can be dropped using the DROP ROLE statement.
C It can be protected with a password.
D It can be granted to user account.
E It can be renameed using the RENAME ROLE statement
F It is created as a locked account.
answer###
BDF

### #x 4.Examine this SQL statement:
UPDATE world.city
SET population =population * 1.1
WHERE countrycode in (select code from world.country
	where continent ='Asia')

which set of privileges will all Tom to execute this SQL statement?

- A  	GRANT UPDATE ON 'world'.'city' to tom;
	GRANT ALL PRIVILEGES ON world.country to tom
- B  	GRANT UPDATE ON world.city TO tom;
	GRANT SELECT ON world.* TO tom;
- C  	GRANT UPDATE ON world.city TO tom;
	GRANT SELECT world.country to tom;
- D 	GRANT ALL PRIVILEGES ON world.city to tom;
	GRANT SELECT('CODE') ON world.country to tome;

answer###
B



### # 5.You execute this command:
shell>mysqldump --exclude-databases=% --users
Which statement is true?
A It creates a logical backup of all metadata, but contains no table data.
B It returns an error because the mysqlpump command should have been used.
C It creates a logical backup of only the users database.
D It creates a logical backup of all MySQL usr accounts.
answer###
D

###6.Which two statements are true about the data dictionary object cache?
A The disctionary object caches use a LRU algorithm to management entries in each cache.
E tablespace_definition_cache sets the number of tablespace objects that can be stored in the dictionary object cache.


### # 7.Which utility would you use to view the queries in the slow query log sorted by average query time?
A mysqlcheck
B mysqlshow
C mysqlimport
D mysqldump
E mysqldumpslow
answer###
E

### # 8.You administer a three node,sigle primary InnoDB Cluster.
Examine cluster.status() displayed her:
"statsText":"Cluster is ONLINE and can tolerate up to ONE failure."
which two statments are true?
A If two instances are unreachable because of network failure,the cluster will reconfigure to work with a sigle instance.
B Reconfiguring the cluster as multi-primary,will increase tolerance to two failures.
C There is a quorum and transactions can be committed normanlly.
D If two instance crash,it will produce an outage.
E Restarting an arbitrary instance will always provoke primary instance failover.（重新启动任意实例总是会引发主实例故障切换。）
F Shutting down two instances with the SHUTDOWN command will produce an outage.
answer###
CD

### # 9.You have configured GTID-based asynchronous replication with one master and one slave.
A user accidentally updated some data on the slave.
To fix this,you stopped replication and successfully reverted the accidental changes.
Exemine the current GTID information:

master uudi:		aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
master gtids_executed:aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa:1-10300
master gtids_purged:aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa:1-3820

slave uuid:			bbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb
slave gtids_executed:aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa:1-10167
					bbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb:1-9
slave gtids_purged:	aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa:1-2312

You must fix GTID sets on the slave to avoid repliacting unwanted transactions in case of failover.
Which set actions would allow the slave to continue replicating without erroneous transactions?
- A RESET MASTER;
  SET GLOBAL gtid_pured=aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa:1-2312;
  SET GLOBAL gtid_executed=aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa:1-10167;
- B RESET MASTER
  SET GLOBAL gtid_pured=aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa:1-10167;

- C RESET SLAVE;
  SET GLOBAL gtid_pured=aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa:1-3820;
  SET GLOBAL gtid_executed=aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa:1-10300;
- D
  SET GLOBAL gtid_pured=aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa:1-2312,
				bbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb:1-9;
  SET GLOBAL gtid_executed=aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa:1-10167;
- E RESET SLAVE;
  SET GLOBAL gtid_purged=aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa:1-10167;

answer###
	A

	应该是B （实测）
	https://dev.mysql.com/doc/refman/8.0/en/replication-gtids-lifecycle.html
	If you need to reset the GTID execution history on a server, use the RESET MASTER statement.
	For example, you might need to do this after carrying out test queries to verify a replication setup on new GTID-enabled servers,
	or when you want to join a new server to a replication group but it contains some unwanted local transactions that are not accepted by Group Replication.




Before MySQL 8.0, you could only change the value of gtid_purged when gtid_executed (and therefore gtid_purge- D  was empty.
From MySQL 8.0, this restriction does not apply, and you can also choose whether to replace the whole GTID set in gtid_purged
with a specified GTID set, or to add a specified GTID set to the GTIDs already in gtid_purged.
For details of how to do this, see the description for gtid_purged.

### # 10.Examine these command and results:
SHOW GRANTS FOR jane;
+---------------------------------------+
|grants for jane@'%'					|
+---------------------------------------+
|GRANT USEAGE ON *.* TO jane@'%'		|
|GRANT SELECT ON `SALES`.* TO jane@'%'	|
+---------------------------------------+
Jane must create a temporary table name `totalsales` in the `sales` database.
Which statment will provide Jane with the required privileges besed on the priciple of least privilege?
A GRANT CREATE TEMPORARY TABLES,INSERT,UPDATE,DELETE,SELECT ON sale.totalsales to jane;
B GRANT CREATE TEMPORARY TABLES ON sales.* to jane;
C GRANT CREATE TEMPORARY TABLES ON sales.totalsales to jane;
D grant all on sales.* to jane;
answer###
B

To create a temporary table, you must have the CREATE TEMPORARY TABLES privilege.
After a session has created a temporary table, the server performs no further privilege checks on the table.
The creating session can perform any operation on the table, such as DROP TABLE, INSERT, UPDATE, or SELECT.





### # 11.What is the reason for SSL not being used?
...
一大堆描述
...

A It si connected via a UNIX socket.
B A current database is not selected,There is noting to encrypt.
C The root user cannnot use encryption.
D The root user must use ssl_fips_mode=ON.
answer###
A



##新题补充
###1.执行revoke role1 from mary;完成后，什么时候生效？
A mary 下次登录；
B mary 在激活role1时；
C 立即生效；
D 管理员flush priveleges之后。
answer###
	C 立即生效
	A revoked role immediately affects any user account from which it was revoked,
	such that within any current session for the account,
	its privileges are adjusted for the next statement executed.

• Changes to global privileges and passwords apply only to subsequent connections of that account.
• Changes to database-level privileges apply after the client’s next USE db_name statement.
• Changes to role, table, column and routine privileges apply immediately.






### ##怀疑答案的列表：
37 AB -->		增加了一个选项 F：不会丢失已提交的事务。 更正：AF
43 AC --> ACG
73 E  --> C
99 AD -->BD
101 BE --> AE
102 CDE--> BCD
116 CEF--> DEF
126 AC --> BC
142 CD --> BD
158 BE --> EF
173 D  --> A
178 ABF--> ADF
179 D  --> C
180 ACF--> CF确定正确，或许选E
183 CD --> AD
184 C  --> D
186 B  --> A
187 A  --> C
188 D  --> C
202 CE --> AE
203 AB --> A
209 E  --> A



###达内教育-北京万寿路中心
中国
北京
北京
海淀区
万寿路西街2号
文博大厦西楼2层
102200
考试中心信息
路程39分钟




回忆考题：
5
8
16
17
20
21
22
23
24
27
28
33
36
37
40
41
42
43
46
47
48
49
50
53
55
56
58
59
60
61
62
65
69
70
71
73
74
75
76
77
79
80
82
99
108
112
116
119
124
128
135
142
144
145
153
162
172
177
178
179
182
188
203
209
210







### ##4月2日补充
### 1.Which two statements are true about the data dictionary object cache?
A tablespace_definition_caches sets the number fo tablespace objects that cant be store in the dictionary object cache.
B if the dicionary object cache becomes full, MySQL server will be unable to create any more tables/objects.
C Character set and collation definition objects are not cached.
D The dictionary object caches use a Least Recently Used (LRU) algorithm to manage entries in each cache.
E All dictionary object caches have a hard-coded size.
answer###
AD


### 3.You are investigating performance problems in a MySQL database; all data fits in memory.
You determine that SELECT queries to one table is the main cause for poor response times.
Which two have the biggest potential for eliminating the problems?

A operating system resources
B table indexes
C column definitions
D innodb mutexes
E high concurrency
F non-transaction storage engine

answer###
BF




### 4. Users report errors when trying to connect from 192.0.2.5 and is connecting using the mysql_native_password authentication plugin.
Examine these commands and output:
mysql> show global status like '%conn%';

|Aborted_connects		|	593		|
|connections 			|	1825	|
|max_used_connections	|	100		|
|thread_connected		|	4		|

mysql>select * from performance_schema.host_cache where ip='192.0.2.5'\G
				      ip:192.0.2.5
		           hosts:client05.example.com
         host_validated :yes
count_auth_plugin_errors:267


Which statement identifies the cause of the errors?
A skip_name_resolve is enabled
B thread_cache is too small
C connections are attemped without a valid user account or password
D Network connectivity issues occurring between client and the MySQL instance.
E user account are defined using the mysql_native_password plugin for password authentication.
F max_connections is too small.

answer###
D

	An authentication plugin can report different error codes to indicate the root cause of a failure.
	Depending on the type of error, one of these columns is incremented:
	COUNT_AUTHENTICATION_ERRORS, COUNT_AUTH_PLUGIN_ERRORS, COUNT_HANDSHAKE_ERRORS.
	New return codes are an optional extension to the existing plugin API.
	Unknown or unexpected plugin errors are counted in the COUNT_AUTH_PLUGIN_ERRORS column.

	COUNT_AUTHENTICATION_ERRORS -- 表示是用户名或密码错误。
	COUNT_HANDSHAKE_ERRORS -- 使用了错误的客户端插件




### 5.Relevant MySQL Enterprise plugins are enabled on your installation. You need to prevent your users
from using a specific list of passwords.
How should you implement this on your system?
A Specify the nonusable passwords via MySQL Enterprise Audit JSON filters.
B Rgister invalid password patters by using the MySQL Firewall blacklist.
C Store values in the mysql.user_invalid_passwords table.
D Manually store the values in a file and use SHA-256 to check against the mysql.user table.
E Set validate_password.dictionary_file=pass_dict.txt and sotre the nonusable passwords in the pass_dict.txt file in your data directory.
F You cannot save a list of nonusable passwords in MySQL.

answer###
E






### ##2022.5.27补充
### 1. 分析下面的语句和响应结果：
mysql>set global join_buffer_size = 1024*1024;
query OK , 0 rows affected (0.00 se- C
下面哪两项是正确的？
A 所有连接都会使用新的join_buffer_size值；
B 在MySQL server重启后，所有连接都会使用新的join_buffer_size值。
C 仅当前连接会使用新的 join_buffer_size值。
D 只有新连接才会使用新的 join_buffer_size 值。
E 在MySQL Server重启后，所有连接都会使用原始的join_buffer_size值。

- Answer:
DE

### 2. 4节点配置为使用循环复制。
检查每个节点的下列配置参数：
slave_parallel_type=database
slave_parallel_worker=4
slave_preserve_commit_order=0
以下哪一项描述是正确的？
A 设置slave_parallel_type=database不适用于循环复制，应该设置为logical_clock
B 跨数据库约束条件可能导致数据库不一致。
C 每个从属线程负责更新特定数据库。
D 将slave_perserve_commit_order设置为ON可以提高数据一致性
E 将transaction_allow_batching设置为ON可以提高数据一致性
F 增加slave_parallel_works可以提高高可用性
- Answer:
B

### 3. 您的当前系统有账户用于主机上MySQL Server开发的用户。
开发团队要求无口令访问，但这违反了公司规定。
哪个插件允许在不使用口令的情况下进行访问，同时仍然保持安全？
A PAM验证插件
B 连接控制插件
C 套接字验证插件
D MySQL防火墙插件
E 原生LDAP验证插件
answer###
C

### 4. 您对MySQL server中的模式进行了一些表定义更改。
下列哪2条语句反映了MySQL server处理表定义更改的方式？
A 对于分布式备份，Mysql将sdi写入二进制日志。
B MySQL将InnoDB元数据更改保存在datadir中的.sdi文件中。
C MySQL隐式执行flush tables并存储元数据的快照备份。
D 元数据在SDI（Serialized dictionary information)中以json格式序列化。
E MySQL Server将串行化数据的副本存储在Innodb用户表空间中。
answer###
DE













