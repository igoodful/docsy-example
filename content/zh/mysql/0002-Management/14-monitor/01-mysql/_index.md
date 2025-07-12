---
title: 01. mysql
description: 监控mysql
date: 2017-01-04
weight: 11000
---

{{< alert >}}


{{< /alert >}}


# 简单复制

## 一、告警级别监控

| 指标                           | 含义                     | 实现                        |
| :----------------------------- | :----------------------- | :-------------------------- |
| 当前数据库进程正在占用的io速度 | 监控进程使用的io         |                             |
| 当前数据库进程正在占用的cpu    | 监控进程使用的cpu        |                             |
| 当前数据库进程正在占用的内存   | 监控进程使用的内存       |                             |
| mysql进程是否存活              |                          |                             |
| mysql服务是否可用              |                          |                             |
| 切主次数                       |                          |                             |
| 活动会话数量                   |                          | 原则上不超过CPU核数         |
| 当前连接数                     |                          | 原则上不超过mysql最大连接数 |
| 集群最近一次备份状态           |                          |                             |
| 数据库实例运行时长             |                          |                             |
| 主从延迟时间                   |                          |                             |
| 从库io线程状态                 |                          |                             |
| 从库sql线程状态                |                          |                             |
| 数据目录使用量                 |                          |                             |
| undo log truncate日志监控      |                          |                             |
| gtid数量监控                   | 避免21亿bug              |                             |
| 超过60s的事务个数              |                          | 需要抓取对应SQL             |
| 超过180s的事务个数             |                          |                             |
| 超过600s的事务个数             |                          |                             |
| 主键是否快用完                 | 避免报主键重复错误       |                             |
| 表无主键                       | 每张表有主键             |                             |
| 表的存储引擎非innodb           | 表的存储引擎必须是innodb |                             |
| 是否存在自定义存储过程         |                          |                             |
| 是否存在自定义函数             |                          |                             |
| 是否存在自定义触发器           |                          |                             |
|                                |                          |                             |
|                                |                          |                             |
|                                |                          |                             |
|                                |                          |                             |
|                                |                          |                             |
|                                |                          |                             |
|                                |                          |                             |
|                                |                          |                             |
|                                |                          |                             |





## 二、普通监控


| 指标                     | 含义               | 实现 |
| :----------------------- | :----------------- | :--- |
| TPS                      | 监控进程使用的内存 |      |
| QPS                      |                    |      |
| 慢SQL数量增长量          |                    |      |
| 集群最近一次备份成功用时 |                    |      |
|                          |                    |      |
|                          |                    |      |
|                          |                    |      |
|                          |                    |      |
|                          |                    |      |
|                          |                    |      |
|                          |                    |      |
|                          |                    |      |






# 组复制


```sql

[root@127.0.0.1:8032 15:04:49((none))]$ SELECT * FROM performance_schema.replication_group_members;
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
| CHANNEL_NAME              | MEMBER_ID                            | MEMBER_HOST    | MEMBER_PORT | MEMBER_STATE | MEMBER_ROLE | MEMBER_VERSION | MEMBER_COMMUNICATION_STACK |
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
| group_replication_applier | 80ca4c8a-1649-11ef-ba14-00163eebf101 | 172.17.136.101 |        8032 | ONLINE       | SECONDARY   | 8.0.32         | XCom                       |
| group_replication_applier | 80ca4c8a-1649-11ef-ba14-00163eebf904 | 172.17.138.136 |        8032 | ONLINE       | PRIMARY     | 8.0.32         | XCom                       |
| group_replication_applier | 80ca4c8a-1649-11ef-ba14-00163eebf995 | 172.17.137.95  |        8032 | ONLINE       | SECONDARY   | 8.0.32         | XCom                       |
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
3 rows in set (0.00 sec)

[root@127.0.0.1:8032 15:05:02((none))]$ TABLE performance_schema.replication_group_member_stats\G
*************************** 1. row ***************************
                              CHANNEL_NAME: group_replication_applier
                                   VIEW_ID: 17307060202451613:14
                                 MEMBER_ID: 80ca4c8a-1649-11ef-ba14-00163eebf101
               COUNT_TRANSACTIONS_IN_QUEUE: 0
                COUNT_TRANSACTIONS_CHECKED: 1
                  COUNT_CONFLICTS_DETECTED: 0
        COUNT_TRANSACTIONS_ROWS_VALIDATING: 0
        TRANSACTIONS_COMMITTED_ALL_MEMBERS: 80ca4c8a-1649-11ef-ba14-00163eebf904:1-1071728,
ce9be252-2b71-11e6-b8f4-00212844f888:1-15
            LAST_CONFLICT_FREE_TRANSACTION: ce9be252-2b71-11e6-b8f4-00212844f888:15
COUNT_TRANSACTIONS_REMOTE_IN_APPLIER_QUEUE: 0
         COUNT_TRANSACTIONS_REMOTE_APPLIED: 1
         COUNT_TRANSACTIONS_LOCAL_PROPOSED: 0
         COUNT_TRANSACTIONS_LOCAL_ROLLBACK: 0
*************************** 2. row ***************************
                              CHANNEL_NAME: group_replication_applier
                                   VIEW_ID: 17307060202451613:14
                                 MEMBER_ID: 80ca4c8a-1649-11ef-ba14-00163eebf904
               COUNT_TRANSACTIONS_IN_QUEUE: 0
                COUNT_TRANSACTIONS_CHECKED: 6
                  COUNT_CONFLICTS_DETECTED: 0
        COUNT_TRANSACTIONS_ROWS_VALIDATING: 0
        TRANSACTIONS_COMMITTED_ALL_MEMBERS: 80ca4c8a-1649-11ef-ba14-00163eebf904:1-1071728,
ce9be252-2b71-11e6-b8f4-00212844f888:1-15
            LAST_CONFLICT_FREE_TRANSACTION: ce9be252-2b71-11e6-b8f4-00212844f888:15
COUNT_TRANSACTIONS_REMOTE_IN_APPLIER_QUEUE: 0
         COUNT_TRANSACTIONS_REMOTE_APPLIED: 9
         COUNT_TRANSACTIONS_LOCAL_PROPOSED: 6
         COUNT_TRANSACTIONS_LOCAL_ROLLBACK: 0
*************************** 3. row ***************************
                              CHANNEL_NAME: group_replication_applier
                                   VIEW_ID: 17307060202451613:14
                                 MEMBER_ID: 80ca4c8a-1649-11ef-ba14-00163eebf995
               COUNT_TRANSACTIONS_IN_QUEUE: 0
                COUNT_TRANSACTIONS_CHECKED: 6
                  COUNT_CONFLICTS_DETECTED: 0
        COUNT_TRANSACTIONS_ROWS_VALIDATING: 0
        TRANSACTIONS_COMMITTED_ALL_MEMBERS: 80ca4c8a-1649-11ef-ba14-00163eebf904:1-1071728,
ce9be252-2b71-11e6-b8f4-00212844f888:1-15
            LAST_CONFLICT_FREE_TRANSACTION: ce9be252-2b71-11e6-b8f4-00212844f888:15
COUNT_TRANSACTIONS_REMOTE_IN_APPLIER_QUEUE: 0
         COUNT_TRANSACTIONS_REMOTE_APPLIED: 7
         COUNT_TRANSACTIONS_LOCAL_PROPOSED: 0
         COUNT_TRANSACTIONS_LOCAL_ROLLBACK: 0
3 rows in set (0.00 sec)


```


## 一、告警级别监控

| 指标                           | 含义                                                          | 实现                                         |
| :----------------------------- | :------------------------------------------------------------ | :------------------------------------------- |
| 当前数据库进程正在占用的io速度 | 监控进程使用的io                                              |                                              |
| 当前数据库进程正在占用的cpu    | 监控进程使用的cpu                                             |                                              |
| 当前数据库进程正在占用的内存   | 监控进程使用的内存                                            |                                              |
| mysql进程是否存活              |                                                               |                                              |
| mysql服务是否可用              |                                                               |                                              |
| 切主次数                       |                                                               |                                              |
| 活动会话数量                   |                                                               | 原则上不超过CPU核数                          |
| 当前连接数                     |                                                               | 原则上不超过mysql最大连接数                  |
| 集群最近一次备份状态           |                                                               |                                              |
| 数据库实例运行时长             |                                                               |                                              |
| 节点状态                       | MEMBER_STATE为ONLINE、OFFLINE、ERROR、RECOVERING、UNREACHABLE | performance_schema.replication_group_members |
| 数据目录使用量                 |                                                               |                                              |
| undo log truncate日志监控      |                                                               |                                              |
| gtid数量监控                   | 避免21亿bug                                                   |                                              |
| 超过60s的事务个数              |                                                               | 需要抓取对应SQL                              |
| 超过180s的事务个数             |                                                               |                                              |
| 超过600s的事务个数             |                                                               |                                              |
|                                |                                                               |                                              |
|                                |                                                               |                                              |
|                                |                                                               |                                              |
|                                |                                                               |                                              |
|                                |                                                               |                                              |
|                                |                                                               |                                              |
|                                |                                                               |                                              |
|                                |                                                               |                                              |
|                                |                                                               |                                              |
|                                |                                                               |                                              |
|                                |                                                               |                                              |
|                                |                                                               |                                              |
|                                |                                                               |                                              |
|                                |                                                               |                                              |
|                                |                                                               |                                              |





## 二、普通监控


| 指标                     | 含义               | 实现 |
| :----------------------- | :----------------- | :--- |
| TPS                      | 监控进程使用的内存 |      |
| QPS                      |                    |      |
| 慢SQL数量增长量          |                    |      |
| 集群最近一次备份成功用时 |                    |      |
|                          |                    |      |
|                          |                    |      |
|                          |                    |      |
|                          |                    |      |
|                          |                    |      |
|                          |                    |      |
|                          |                    |      |
|                          |                    |      |





