---
title: mysqlbinlog
description: mysqlbinlog
date: 2023-10-12
weight: 20000
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}

`mysqlbinlog --base64-output=decode-rows  -vv mysql-bin.000776|less`

{{< /alert >}}


## 下载地址：https://github.com/opencontainers/runc/releases

```sql
wget https://github.com/opencontainers/runc/releases/download/v1.1.9/runc.amd64
install -m 755 runc.amd64 /usr/local/sbin/runc
```

## 人性化可视化查看sql语句
```sql
mysqlbinlog --base64-output=decode-rows  -vv binlog.000776 > 000776.sql

less 000776.sql



```





## 增量恢复

{{% alert title="Warning" color="warning" %}}

1. -d：指定特定数据库的binlog

2. \-\-start-position和\-\-stop-position

3. \-\-start-datetime和\-\-stop-datetime

4. mysqlbinlog解析时，不要带上参数\-\-base64-output=decode-rows
{{% /alert %}}

```bash
mysqlbinlog binlog.000776 | mysql -uroot -p'123456'  your_database

```



### 统计binlog中每个gtid操作的数据量：binlog_analyze.py



```python
#!/bin/python
# -*- coding: utf8 -*-
# VERSION 1.0
# DATE 2023-10-18
# DESC 分析binlog日志，生成事务耗时、更新的行数等信息。
# binlog_analyze.py

import os
import sys
import re
import time

binlog_name = sys.argv[1]
if os.path.exists(binlog_name):
        pass
else:
        print('文件 {} 不存在'.format(binlog_name))
        sys.exit(1)

with open(binlog_name, 'r') as f:
        #with open('binlog.000005.txt','r') as f:
        while True:
                line = f.readline()
                if not line:
                        break
                # 事务提交时间
                match = re.match(r"# immediate_commit_timestamp=.* \((.*)\..*", line)
                if match:
                        commit_time = match.group(1)
                        nums = 0
                        gtid = None
                        start_time = None
                        trx_type = None
                        while True:
                                line = f.readline()
                                # 记录事务GTID
                                if gtid is None:
                                        match = re.match(r"(SET @@SESSION.GTID_NEXT=) '(.*?\d)'.*;", line)
                                        if match:
                                                gtid = match.group(2)
                                                continue
                                # 记录事务开始时间
                                if start_time is None:
                                        match = re.match(r"(SET TIMESTAMP)=(\d{10}).*;", line)
                                        if match:
                                                start_time = match.group(2)
                                                start_time_obj = time.localtime(int(start_time))
                                                start_time = time.strftime("%Y-%m-%d %H:%M:%S", start_time_obj)
                                                continue
                                # 记录事务类型
                                match = re.search(r"### (INSERT|DELETE|UPDATE) (.*)", line, re.I)
                                if match:
                                        trx_type = match.group(1)
                                        sql = match.group(1, 2)
                                        # DML语句更新的行数
                                        nums = nums + 1
                                        continue
                                if trx_type is None:
                                        match = re.search(r"(CREATE|DROP|ALTER|TRUNCATE) (.*)", line, re.I)
                                        if match:
                                                trx_type = match.group(1)
                                                sql = match.group(1, 2)
                                                nums = "None"
                                                end_time = "None"
                                                print(gtid, '"' + start_time + '"', '"' + commit_time + '"', trx_type, nums,
                                                      sql)
                                                break
                                # 事务结束
                                match = re.match(r"(^COMMIT).*", line)
                                if match:
                                        print(gtid, '"' + start_time + '"', '"' + commit_time + '"', trx_type, nums, sql)
                                        break


```


#### 举例说明
```sql
mysql> desc sbtest1;
+-------+-----------+------+-----+---------+----------------+
| Field | Type      | Null | Key | Default | Extra          |
+-------+-----------+------+-----+---------+----------------+
| id    | int       | NO   | PRI | NULL    | auto_increment |
| k     | int       | NO   | MUL | 0       |                |
| c     | char(120) | NO   |     |         |                |
| pad   | char(60)  | NO   |     |         |                |
+-------+-----------+------+-----+---------+----------------+
4 rows in set (0.05 sec)

mysql> select count(*) from  sbtest1;
+----------+
| count(*) |
+----------+
|  5000000 |
+----------+
1 row in set (14.13 sec)

mysql> update sbtest1 set pad='23243333232' limit 1000000;
Query OK, 1000000 rows affected (32.85 sec)
Rows matched: 1000000  Changed: 1000000  Warnings: 0

mysql> delete from  sbtest1  limit 2000000;

Query OK, 2000000 rows affected (1 min 20.59 sec)

mysql> delete from  sbtest1  where id < 2000100;
Query OK, 99 rows affected (0.02 sec)

```


```sql
# 先解析binlog
mysqlbinlog -v -v --base64-output=decode-rows binlog.000016  > 000016.sql

# 再通过脚本脚本统计事务操作的数据量
python3.9 binlog_analyze.py  000016.sql

15b4be34-6bc4-11ee-9370-00163ef09ff4:117993 "2023-10-18 10:44:30" "2023-10-18 10:44:56" UPDATE 1000000 ('UPDATE', '`apple`.`sbtest1`')
15b4be34-6bc4-11ee-9370-00163ef09ff4:117994 "2023-10-18 10:45:48" "2023-10-18 10:47:00" DELETE 2000000 ('DELETE', 'FROM `apple`.`sbtest1`')
15b4be34-6bc4-11ee-9370-00163ef09ff4:117995 "2023-10-18 10:55:19" "2023-10-18 10:55:19" DELETE 99 ('DELETE', 'FROM `apple`.`sbtest1`')


```


## 查看binlog
```sql

mysql>  show variables like 'log_bin%';
+---------------------------------+----------------------------------------+
| Variable_name                   | Value                                  |
+---------------------------------+----------------------------------------+
| log_bin                         | ON                                     |
| log_bin_basename                | /data/dbdata/3308/logfile/binlog       |
| log_bin_index                   | /data/dbdata/3308/logfile/binlog.index |
| log_bin_trust_function_creators | ON                                     |
| log_bin_use_v1_row_events       | OFF                                    |
+---------------------------------+----------------------------------------+
5 rows in set (0.01 sec)

mysql> show master logs;
+---------------+------------+-----------+
| Log_name      | File_size  | Encrypted |
+---------------+------------+-----------+
| binlog.000001 | 1075368023 | No        |
| binlog.000002 | 1074832718 | No        |
| binlog.000003 | 1075344754 | No        |
| binlog.000004 | 1074282956 | No        |
| binlog.000005 | 1075344184 | No        |
| binlog.000006 | 1074834048 | No        |
| binlog.000007 | 1075344564 | No        |
| binlog.000008 | 1074792221 | No        |
| binlog.000009 | 1075344815 | No        |
| binlog.000010 | 1074836584 | No        |
| binlog.000011 | 1073769073 | No        |
| binlog.000012 | 1074834251 | No        |
| binlog.000013 | 1075342868 | No        |
| binlog.000014 | 1074835581 | No        |
| binlog.000015 |  377048419 | No        |
| binlog.000016 |  666947894 | No        |
+---------------+------------+-----------+
16 rows in set (0.02 sec)

mysql> show master status\G;
*************************** 1. row ***************************
             File: binlog.000016
         Position: 666947894
     Binlog_Do_DB:
 Binlog_Ignore_DB: dbscale_tmp
Executed_Gtid_Set: 15b4be34-6bc4-11ee-9370-00163ef09ff4:1-117995
1 row in set (0.00 sec)

ERROR:
No query specified


```

## 切换binlog

```sql
mysql> flush logs;

```


## 清理binlog
```sql
mysql> purge binary logs to 'binlog.000016';

mysql> purge master logs to '2023-10-10 15:00:0';

# 清空所有binlog日志

mysql> reset master;


```

## 查看binlog内容

```sql
mysql> show binlog events in 'binlog.000016' from 32446 limit 5\G;
*************************** 1. row ***************************
   Log_name: binlog.000016
        Pos: 32446
 Event_type: Update_rows
  Server_id: 1000403308
End_log_pos: 40450
       Info: table_id: 188
*************************** 2. row ***************************
   Log_name: binlog.000016
        Pos: 40450
 Event_type: Update_rows
  Server_id: 1000403308
End_log_pos: 48454
       Info: table_id: 188
*************************** 3. row ***************************
   Log_name: binlog.000016
        Pos: 48454
 Event_type: Update_rows
  Server_id: 1000403308
End_log_pos: 56458
       Info: table_id: 188
*************************** 4. row ***************************
   Log_name: binlog.000016
        Pos: 56458
 Event_type: Update_rows
  Server_id: 1000403308
End_log_pos: 64462
       Info: table_id: 188
*************************** 5. row ***************************
   Log_name: binlog.000016
        Pos: 64462
 Event_type: Update_rows
  Server_id: 1000403308
End_log_pos: 72466
       Info: table_id: 188
5 rows in set (0.00 sec)

ERROR:
No query specified

```




### 乱码案例
```sql
mysql> set names latin1;
Query OK, 0 rows affected (0.00 sec)

mysql> insert  user values ('12345','ge',110119,'中哈哈');
Query OK, 1 row affected (0.00 sec)

mysql> show create table user\G
*************************** 1. row ***************************
       Table: user
Create Table: CREATE TABLE `user` (
  `SYPT_JRNL_NO` char(25) NOT NULL,
  `MULTI_TENANCY_ID` char(5) NOT NULL,
  `id` int DEFAULT NULL,
  `name` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`SYPT_JRNL_NO`,`MULTI_TENANCY_ID`),
  KEY `idx_SYPT_JRNL_NO` (`SYPT_JRNL_NO`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb3
1 row in set (0.03 sec)

mysql> show variables like '%char%';
+--------------------------+---------------------------------------+
| Variable_name            | Value                                 |
+--------------------------+---------------------------------------+
| character_set_client     | latin1                                |
| character_set_connection | latin1                                |
| character_set_database   | utf8mb3                               |
| character_set_filesystem | binary                                |
| character_set_results    | latin1                                |
| character_set_server     | utf8mb3                               |
| character_set_system     | utf8mb3                               |
| character_sets_dir       | /home/work/mysql_3333/share/charsets/ |
+--------------------------+---------------------------------------+
8 rows in set (0.01 sec)

```

binlog解析
```sql
#231125 10:39:27 server id 1001335348  end_log_pos 560 CRC32 0x14ba7421         Xid = 13359598
COMMIT/*!*/;
# at 560
#231125 10:41:17 server id 1001335348  end_log_pos 639 CRC32 0xedf6e65f         GTID    last_committed=1        sequence_number=2       rbr_only=yes    original_committed_timestamp=1700880077579381   immediate_commit_timestamp=1700880077579381     transaction_length=320
/*!50718 SET TRANSACTION ISOLATION LEVEL READ COMMITTED*//*!*/;
# original_commit_timestamp=1700880077579381 (2023-11-25 10:41:17.579381 CST)
# immediate_commit_timestamp=1700880077579381 (2023-11-25 10:41:17.579381 CST)
/*!80001 SET @@session.original_commit_timestamp=1700880077579381*//*!*/;
/*!80014 SET @@session.original_server_version=80026*//*!*/;
/*!80014 SET @@session.immediate_server_version=80026*//*!*/;
SET @@SESSION.GTID_NEXT= 'b3a23892-7ed7-11ee-ae86-00163eebf904:611984'/*!*/;
# at 639
#231125 10:41:17 server id 1001335348  end_log_pos 715 CRC32 0x39b43346         Query   thread_id=78    exec_time=0     error_code=0
SET TIMESTAMP=1700880077/*!*/;
/*!\C latin1 *//*!*/;
SET @@session.character_set_client=8,@@session.collation_connection=8,@@session.collation_server=33/*!*/;
BEGIN
/*!*/;
# at 715
#231125 10:41:17 server id 1001335348  end_log_pos 778 CRC32 0x49f04793         Table_map: `apple`.`user` mapped to number 238
# at 778
#231125 10:41:17 server id 1001335348  end_log_pos 849 CRC32 0xd948bcfc         Write_rows: table id 238 flags: STMT_END_F
### INSERT INTO `apple`.`user`
### SET
###   @1='12345' /* STRING(75) meta=65099 nullable=0 is_null=0 */
###   @2='ge' /* STRING(15) meta=65039 nullable=0 is_null=0 */
###   @3=110119 /* INT meta=0 nullable=1 is_null=0 */
###   @4='ä¸­å“ˆå“ˆ' /* VARSTRING(765) meta=765 nullable=1 is_null=0 */
# at 849
#231125 10:41:17 server id 1001335348  end_log_pos 880 CRC32 0x3054b46e         Xid = 13359601
COMMIT/*!*/;
SET @@SESSION.GTID_NEXT= 'AUTOMATIC' /* added by mysqlbinlog */ /*!*/;
DELIMITER ;
# End of log file
/*!50003 SET COMPLETION_TYPE=@OLD_COMPLETION_TYPE*/;
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=0*/;

```

{{<alert color="primary">}}

1. `/*!\C latin1 *//*!*/;`  写入该字符集的是会话级别的character_set_client，用`show  variables like 'character_set_client';`查看

2.








{{</alert>}}


## No space left on device

不过，偶尔查看时也会遇到以下报错：mysqlbinlog: Error writing file '/tmp/tmp.wOdzVS' (Errcode: 28 - No space left on device)‍‍

报错原因：通常是因为 binlog 解析后内容过大，导致 mysqlbinlog 默认解析后的临时文件存放路径 /tmp 空间不足，导致命令执行报错。

解决方法：mysqlbinlog 解析前手动指定临时文件存放路径，保证空间足够，注意参数TMPDIR，不能用小写，如下：

```sql
export TMPDIR=/opt/mysql3306/tmp
```

### 远程读取binlog
- \-u,\-\-user=
- \-p,\-\-password
- \-h,\-\-host
- \-P,\-\-port
- \-R,\-\-read-from-remote-server
- \-v
- \-\-base64-output=decode-rows
- \-\-raw

```sql
mysqlbinlog  -uadmin -p'123456' -h172.17.134.76 -P16315 --base64-output=decode-rows -v -R   greatdb-bin.000003|less
```



## jiexi
> 8.0.32

- `2024-05-18T19:07:12 [mysql@node70 /home/mysql/mysql_16315/log] $` **mylogin_local**
```sql
[admin@127.0.0.1:16315 19:35:08((none))]$ use apple;
[admin@127.0.0.1:16315 19:35:15(apple)]$ show tables;
+-----------------+
| Tables_in_apple |
+-----------------+
| t1              |
+-----------------+
1 row in set (0.00 sec)

[admin@127.0.0.1:16315 19:35:19(apple)]$ flush logs;
Query OK, 0 rows affected (0.02 sec)

[admin@127.0.0.1:16315 19:35:35(apple)]$ show create table t1\G
*************************** 1. row ***************************
       Table: t1
Create Table: CREATE TABLE `t1` (
  `id` int NOT NULL AUTO_INCREMENT,
  `k` int NOT NULL DEFAULT '0',
  `c` char(120) NOT NULL DEFAULT '',
  `pad` char(60) NOT NULL DEFAULT '',
  `name` varchar(256) NOT NULL,
  `d` datetime NOT NULL DEFAULT '2024-04-04 00:00:00',
  PRIMARY KEY (`id`),
  UNIQUE KEY `idx_name` (`name`),
  KEY `k_1` (`k`)
) ENGINE=InnoDB AUTO_INCREMENT=8511001 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)

[admin@127.0.0.1:16315 19:36:06(apple)]$ select * from t1 limit 1;
+----+-----------+-------------------------------------------------+----------------------------------------+-------------------------------------------------------+---------------------+
| id | k         | c                                               | pad                                    | name                                                  | d                   |
+----+-----------+-------------------------------------------------+----------------------------------------+-------------------------------------------------------+---------------------+
|  1 | 583532949 | ullam et molestias quo assumenda ut temporibus. | et est voluptatibus voluptas autem ad. | et nulla sed earum est totam similique reprehenderit. | 2024-05-16 07:07:11 |
+----+-----------+-------------------------------------------------+----------------------------------------+-------------------------------------------------------+---------------------+
1 row in set (0.00 sec)

[admin@127.0.0.1:16315 19:37:49(apple)]$ begin;
Query OK, 0 rows affected (0.00 sec)

[admin@127.0.0.1:16315 19:37:56(apple)]$ delete from t1 where id in (1,2);
Query OK, 2 rows affected (0.01 sec)

[admin@127.0.0.1:16315 19:38:32(apple)]$ commit;
Query OK, 0 rows affected (0.00 sec)

[admin@127.0.0.1:16315 19:39:01(apple)]$ update t1 set c='3' where id=3;
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

[admin@127.0.0.1:16315 19:48:06(apple)]$


```

- `mysqlbinlog --base64-output=decode-rows -vv bin.000004 > 000004.sql`
```sql
# The proper term is pseudo_replica_mode, but we use this compatibility alias
# to make the statement usable on server versions 8.0.24 and older.
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=1*/;
/*!50003 SET @OLD_COMPLETION_TYPE=@@COMPLETION_TYPE,COMPLETION_TYPE=0*/;
DELIMITER /*!*/;
# at 4
#240518 19:35:35 server id 23618686  end_log_pos 126    Start: binlog v 4, server v 8.0.32 created 240518 19:35:35
# Warning: this binlog is either in use or was not closed properly.
# at 126
#240518 19:35:35 server id 23618686  end_log_pos 193    Previous-GTIDs
# 9a2221ba-14ed-11ef-96a5-00163eebf9aa:1-8513
# at 193
#240518 19:39:01 server id 23618686  end_log_pos 268    GTID    last_committed=0        sequence_number=1       rbr_only=yes    original_committed_timestamp=1716032341433718   immediate_commit_timestamp=1716032341433718     transaction_length=607
/*!50718 SET TRANSACTION ISOLATION LEVEL READ COMMITTED*//*!*/;
# original_commit_timestamp=1716032341433718 (2024-05-18 19:39:01.433718 CST)
# immediate_commit_timestamp=1716032341433718 (2024-05-18 19:39:01.433718 CST)
/*!80001 SET @@session.original_commit_timestamp=1716032341433718*//*!*/;
/*!80014 SET @@session.original_server_version=80032*//*!*/;
/*!80014 SET @@session.immediate_server_version=80032*//*!*/;
SET @@SESSION.GTID_NEXT= '9a2221ba-14ed-11ef-96a5-00163eebf9aa:8514'/*!*/;
# at 268
#240518 19:38:32 server id 23618686  end_log_pos 340    Query   thread_id=22    exec_time=0     error_code=0
SET TIMESTAMP=1716032312/*!*/;
SET @@session.pseudo_thread_id=22/*!*/;
SET @@session.foreign_key_checks=1, @@session.sql_auto_is_null=0, @@session.unique_checks=1, @@session.autocommit=1/*!*/;
SET @@session.sql_mode=1168113696/*!*/;
SET @@session.auto_increment_increment=1, @@session.auto_increment_offset=1/*!*/;
/*!\C utf8mb4 *//*!*/;
SET @@session.character_set_client=255,@@session.collation_connection=255,@@session.collation_server=255/*!*/;
SET @@session.lc_time_names=0/*!*/;
SET @@session.collation_database=DEFAULT/*!*/;
/*!80011 SET @@session.default_collation_for_utf8mb4=255*//*!*/;
BEGIN
/*!*/;
# at 340
#240518 19:38:32 server id 23618686  end_log_pos 392    Rows_query
# delete from t1 where id in (1,2)
# at 392
#240518 19:38:32 server id 23618686  end_log_pos 454    Table_map: `apple`.`t1` mapped to number 140
# has_generated_invisible_primary_key=0
# at 454
#240518 19:38:32 server id 23618686  end_log_pos 773    Delete_rows: table id 140 flags: STMT_END_F
### DELETE FROM `apple`.`t1`
### WHERE
###   @1=1 /* INT meta=0 nullable=0 is_null=0 */
###   @2=583532949 /* INT meta=0 nullable=0 is_null=0 */
###   @3='ullam et molestias quo assumenda ut temporibus.' /* STRING(480) meta=61152 nullable=0 is_null=0 */
###   @4='et est voluptatibus voluptas autem ad.' /* STRING(240) meta=65264 nullable=0 is_null=0 */
###   @5='et nulla sed earum est totam similique reprehenderit.' /* VARSTRING(1024) meta=1024 nullable=0 is_null=0 */
###   @6='2024-05-16 07:07:11' /* DATETIME(0) meta=0 nullable=0 is_null=0 */
### DELETE FROM `apple`.`t1`
### WHERE
###   @1=2 /* INT meta=0 nullable=0 is_null=0 */
###   @2=1936732900 /* INT meta=0 nullable=0 is_null=0 */
###   @3='dolor impedit qui inventore sit voluptatum similique.' /* STRING(480) meta=61152 nullable=0 is_null=0 */
###   @4='consectetur odit temporibus quisquam qui.' /* STRING(240) meta=65264 nullable=0 is_null=0 */
###   @5='dolore nisi nihil.' /* VARSTRING(1024) meta=1024 nullable=0 is_null=0 */
###   @6='2023-06-23 16:04:56' /* DATETIME(0) meta=0 nullable=0 is_null=0 */
# at 773
#240518 19:39:01 server id 23618686  end_log_pos 800    Xid = 8579
COMMIT/*!*/;
# at 800
#240518 19:48:06 server id 23618686  end_log_pos 875    GTID    last_committed=1        sequence_number=2       rbr_only=yes    original_committed_timestamp=1716032886288540   immediate_commit_timestamp=1716032886288540     transaction_length=515
/*!50718 SET TRANSACTION ISOLATION LEVEL READ COMMITTED*//*!*/;
# original_commit_timestamp=1716032886288540 (2024-05-18 19:48:06.288540 CST)
# immediate_commit_timestamp=1716032886288540 (2024-05-18 19:48:06.288540 CST)
/*!80001 SET @@session.original_commit_timestamp=1716032886288540*//*!*/;
/*!80014 SET @@session.original_server_version=80032*//*!*/;
/*!80014 SET @@session.immediate_server_version=80032*//*!*/;
SET @@SESSION.GTID_NEXT= '9a2221ba-14ed-11ef-96a5-00163eebf9aa:8515'/*!*/;
# at 875
#240518 19:48:06 server id 23618686  end_log_pos 956    Query   thread_id=22    exec_time=0     error_code=0
SET TIMESTAMP=1716032886/*!*/;
BEGIN
/*!*/;
# at 956
#240518 19:48:06 server id 23618686  end_log_pos 1006   Rows_query
# update t1 set c='3' where id=3
# at 1006
#240518 19:48:06 server id 23618686  end_log_pos 1068   Table_map: `apple`.`t1` mapped to number 140
# has_generated_invisible_primary_key=0
# at 1068
#240518 19:48:06 server id 23618686  end_log_pos 1288   Update_rows: table id 140 flags: STMT_END_F
### UPDATE `apple`.`t1`
### WHERE
###   @1=3 /* INT meta=0 nullable=0 is_null=0 */
###   @2=1541931905 /* INT meta=0 nullable=0 is_null=0 */
###   @3='rem reiciendis impedit accusantium consequatur vel tempora aut.' /* STRING(480) meta=61152 nullable=0 is_null=0 */
###   @4='non modi excepturi.' /* STRING(240) meta=65264 nullable=0 is_null=0 */
###   @5='sequi odio dolore autem.' /* VARSTRING(1024) meta=1024 nullable=0 is_null=0 */
###   @6='2023-12-27 08:08:15' /* DATETIME(0) meta=0 nullable=0 is_null=0 */
### SET
###   @1=3 /* INT meta=0 nullable=0 is_null=0 */
###   @2=1541931905 /* INT meta=0 nullable=0 is_null=0 */
###   @3='3' /* STRING(480) meta=61152 nullable=0 is_null=0 */
###   @4='non modi excepturi.' /* STRING(240) meta=65264 nullable=0 is_null=0 */
###   @5='sequi odio dolore autem.' /* VARSTRING(1024) meta=1024 nullable=0 is_null=0 */
###   @6='2023-12-27 08:08:15' /* DATETIME(0) meta=0 nullable=0 is_null=0 */
# at 1288
#240518 19:48:06 server id 23618686  end_log_pos 1315   Xid = 8581
COMMIT/*!*/;
SET @@SESSION.GTID_NEXT= 'AUTOMATIC' /* added by mysqlbinlog */ /*!*/;
DELIMITER ;
# End of log file
/*!50003 SET COMPLETION_TYPE=@OLD_COMPLETION_TYPE*/;
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=0*/;

```

- 19:37:56 begin;
- 19:38:32 delete from t1 where id in (1,2);
- 19:39:01 commit;


- 240518 19:38:32 Delete_rows
- 240518 19:39:01 Xid = 8579 事务提交的时间
- original_commit_timestamp=1716032341433718 (2024-05-18 19:39:01.433718 CST) 事务提交的时间
- immediate_commit_timestamp=1716032341433718 (2024-05-18 19:39:01.433718 CST) 事务提交的时间


每个 binlog event 前都会带有一个时间戳，这个时间戳和我们执行每个语句时的开始时间对应

#### begin event

| 节点类型 | begin event        | 备注                    |
|:------|:-------------------|:----------------------|
| 主节点   | 执行第一个修改操作 | 不是执行BEGIN时的时间戳 |
| 从节点   | 与主节点的该值相同 | 事务提交时刻            |



#### Write Event


#### Table_map Event


#### Xid Event
> 主节点执行最后一次数据修改操作的时间，而不是提交操作的时间


#### original_commit_timestamp, immediate_commit_timestamp

| 节点类型 | original_commit_timestamp | immediate_commit_timestamp |
|:------|:--------------------------|:---------------------------|
| 主节点   | 事务提交时刻              | 事务提交时刻               |
| 从节点   | 与主节点的该值相同        | 事务提交时刻               |


#### exec_time

| 节点类型 | exec_time                                                                              | 备注     |
|:------|:---------------------------------------------------------------------------------------|:-------|
| 主节点   | 执行第一个修改SQL并完成第一行修改（write/update/delete）操作 - 第一个修改SQL开始执行时间 | 主从不同 |
| 从节点   | 从节点执行第一个修改SQL并完成第一行数据修改 - 主节点第一个修改SQL开始执行时间          | 主从不同 |


**从节点**：`exec_time = A + B + C + D`

- A = 主节点 整个事务的耗时
- B = binlog 传输耗时
- C = 同步延迟/中断耗时（可能-主要）
- D = 从节点完成第一行数据修改

#### 主节点整个事务的实际耗时:
> 主节点commit的时间 - 主节点第一个修改的时间



## 误删数据恢复


不要用`start-datetime`和`stop-datetime`，因为他们精度不够，只能精确到秒级别，如果一秒内有多个事务，那么这种方式就无法区分了。



#### `stop-position`

现假设binlog中有事务`t1~t10`，需要从`t2`开始恢复到`t5`，且包含事务`t5`

- `stop-position`为binlog中事务`t5`的`Xid`事件的`end_log_pos`，这样就能保证恢复到完整事务`t5`


- `start-position`为binlog中事务`t2`的GTID事件起始或BEGIN事件起始


```sql
[guolicheng@48 /db/storage/guolicheng/mysql_8032/log] $ mysqlbinlog -vv --base64-output=decode-rows bin.000016
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=1*/;
/*!50003 SET @OLD_COMPLETION_TYPE=@@COMPLETION_TYPE,COMPLETION_TYPE=0*/;
DELIMITER /*!*/;
# at 4
#250709 15:29:35 server id 19716134  end_log_pos 126 	Start: binlog v 4, server v 8.0.32 created 250709 15:29:35
# at 126
#250709 15:29:35 server id 19716134  end_log_pos 193 	Previous-GTIDs
# 514b2c14-ab94-11ef-b3bc-e4434bfdbc88:1-1636075
# at 193
#250709 16:30:17 server id 19716134  end_log_pos 268 	GTID	last_committed=0	sequence_number=1	rbr_only=yes	original_committed_timestamp=1752049817711820	immediate_commit_timestamp=1752049817711820	transaction_length=433
/*!50718 SET TRANSACTION ISOLATION LEVEL READ COMMITTED*//*!*/;
# original_commit_timestamp=1752049817711820 (2025-07-09 16:30:17.711820 CST)
# immediate_commit_timestamp=1752049817711820 (2025-07-09 16:30:17.711820 CST)
/*!80001 SET @@session.original_commit_timestamp=1752049817711820*//*!*/;
/*!80014 SET @@session.original_server_version=80032*//*!*/;
/*!80014 SET @@session.immediate_server_version=80032*//*!*/;
SET @@SESSION.GTID_NEXT= '514b2c14-ab94-11ef-b3bc-e4434bfdbc88:1636076'/*!*/;
# at 268
#250709 16:30:17 server id 19716134  end_log_pos 340 	Query	thread_id=1052	exec_time=0	error_code=0
SET TIMESTAMP=1752049817/*!*/;
SET @@session.pseudo_thread_id=1052/*!*/;
SET @@session.foreign_key_checks=1, @@session.sql_auto_is_null=0, @@session.unique_checks=1, @@session.autocommit=1/*!*/;
SET @@session.sql_mode=1168113696/*!*/;
SET @@session.auto_increment_increment=1, @@session.auto_increment_offset=1/*!*/;
/*!\C utf8mb4 *//*!*/;
SET @@session.character_set_client=255,@@session.collation_connection=255,@@session.collation_server=255/*!*/;
SET @@session.lc_time_names=0/*!*/;
SET @@session.collation_database=DEFAULT/*!*/;
/*!80011 SET @@session.default_collation_for_utf8mb4=255*//*!*/;
BEGIN
/*!*/;
# at 340
#250709 16:30:17 server id 19716134  end_log_pos 382 	Rows_query
# delete from t1 limit 2
# at 382
#250709 16:30:17 server id 19716134  end_log_pos 438 	Table_map: `apple`.`t1` mapped to number 95
# at 438
#250709 16:30:17 server id 19716134  end_log_pos 599 	Delete_rows: table id 95 flags: STMT_END_F
### DELETE FROM `apple`.`t1`
### WHERE
###   @1=39 /* INT meta=0 nullable=0 is_null=0 */
###   @2=1559885358 /* INT meta=0 nullable=0 is_null=0 */
###   @3=657598532 /* INT meta=0 nullable=0 is_null=0 */
###   @4='123456' /* VARSTRING(240) meta=240 nullable=0 is_null=0 */
###   @5='qui quo doloremque ea autem!' /* STRING(240) meta=65264 nullable=0 is_null=0 */
### DELETE FROM `apple`.`t1`
### WHERE
###   @1=41 /* INT meta=0 nullable=0 is_null=0 */
###   @2=1580698885 /* INT meta=0 nullable=0 is_null=0 */
###   @3=681785212 /* INT meta=0 nullable=0 is_null=0 */
###   @4='123456' /* VARSTRING(240) meta=240 nullable=0 is_null=0 */
###   @5='molestias doloribus quidem velit tenetur praesentium quia eo' /* STRING(240) meta=65264 nullable=0 is_null=0 */
# at 599
#250709 16:30:17 server id 19716134  end_log_pos 626 	Xid = 6318
COMMIT/*!*/;
# at 626
#250709 16:30:17 server id 19716134  end_log_pos 701 	GTID	last_committed=1	sequence_number=2	rbr_only=yes	original_committed_timestamp=1752049817730184	immediate_commit_timestamp=1752049817730184	transaction_length=475
/*!50718 SET TRANSACTION ISOLATION LEVEL READ COMMITTED*//*!*/;
# original_commit_timestamp=1752049817730184 (2025-07-09 16:30:17.730184 CST)
# immediate_commit_timestamp=1752049817730184 (2025-07-09 16:30:17.730184 CST)
/*!80001 SET @@session.original_commit_timestamp=1752049817730184*//*!*/;
/*!80014 SET @@session.original_server_version=80032*//*!*/;
/*!80014 SET @@session.immediate_server_version=80032*//*!*/;
SET @@SESSION.GTID_NEXT= '514b2c14-ab94-11ef-b3bc-e4434bfdbc88:1636077'/*!*/;
# at 701
#250709 16:30:17 server id 19716134  end_log_pos 773 	Query	thread_id=1052	exec_time=0	error_code=0
SET TIMESTAMP=1752049817/*!*/;
BEGIN
/*!*/;
# at 773
#250709 16:30:17 server id 19716134  end_log_pos 815 	Rows_query
# delete from t1 limit 3
# at 815
#250709 16:30:17 server id 19716134  end_log_pos 871 	Table_map: `apple`.`t1` mapped to number 95
# at 871
#250709 16:30:17 server id 19716134  end_log_pos 1074 	Delete_rows: table id 95 flags: STMT_END_F
### DELETE FROM `apple`.`t1`
### WHERE
###   @1=44 /* INT meta=0 nullable=0 is_null=0 */
###   @2=1134554347 /* INT meta=0 nullable=0 is_null=0 */
###   @3=295498719 /* INT meta=0 nullable=0 is_null=0 */
###   @4='123456' /* VARSTRING(240) meta=240 nullable=0 is_null=0 */
###   @5='consequuntur distinctio consequatur a.' /* STRING(240) meta=65264 nullable=0 is_null=0 */
### DELETE FROM `apple`.`t1`
### WHERE
###   @1=45 /* INT meta=0 nullable=0 is_null=0 */
###   @2=431367440 /* INT meta=0 nullable=0 is_null=0 */
###   @3=363238924 /* INT meta=0 nullable=0 is_null=0 */
###   @4='123456' /* VARSTRING(240) meta=240 nullable=0 is_null=0 */
###   @5='ullam nisi quod expedita ducimus sint.' /* STRING(240) meta=65264 nullable=0 is_null=0 */
### DELETE FROM `apple`.`t1`
### WHERE
###   @1=47 /* INT meta=0 nullable=0 is_null=0 */
###   @2=2058649460 /* INT meta=0 nullable=0 is_null=0 */
###   @3=1044508113 /* INT meta=0 nullable=0 is_null=0 */
###   @4='123456' /* VARSTRING(240) meta=240 nullable=0 is_null=0 */
###   @5='non soluta placeat veritatis sed.' /* STRING(240) meta=65264 nullable=0 is_null=0 */
# at 1074
#250709 16:30:17 server id 19716134  end_log_pos 1101 	Xid = 6319
COMMIT/*!*/;
# at 1101
#250709 16:30:22 server id 19716134  end_log_pos 1138 	Rotate to bin.000017  pos: 4
SET @@SESSION.GTID_NEXT= 'AUTOMATIC' /* added by mysqlbinlog */ /*!*/;
DELIMITER ;
# End of log file
/*!50003 SET COMPLETION_TYPE=@OLD_COMPLETION_TYPE*/;
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=0*/;


```












