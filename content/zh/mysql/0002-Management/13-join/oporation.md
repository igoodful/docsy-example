---
title: 组复制运维
description: group replica
date: 2024-11-04
weight: 20000
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{<alert color="danger" title="注意" >}}



{{</alert>}}







## 一、手动选主

单主模式中才有手动选主，多主模式都是读写节点

```sql
select group_replication_set_as_primary('member_uuid');
```
- mysql-8.0.29开始新增超时设置：`select group_replication_set_as_primary('member_uuid', 300)`





### 举例说明
```sql
[root@127.0.0.1:8032 15:59:09((none))]$ select * from performance_schema.replication_group_members;
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
| CHANNEL_NAME              | MEMBER_ID                            | MEMBER_HOST    | MEMBER_PORT | MEMBER_STATE | MEMBER_ROLE | MEMBER_VERSION | MEMBER_COMMUNICATION_STACK |
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
| group_replication_applier | 80ca4c8a-1649-11ef-ba14-00163eebf101 | 172.17.136.101 |        8032 | ONLINE       | SECONDARY   | 8.0.32         | XCom                       |
| group_replication_applier | 80ca4c8a-1649-11ef-ba14-00163eebf904 | 172.17.138.136 |        8032 | ONLINE       | PRIMARY     | 8.0.32         | XCom                       |
| group_replication_applier | 80ca4c8a-1649-11ef-ba14-00163eebf995 | 172.17.137.95  |        8032 | ONLINE       | SECONDARY   | 8.0.32         | XCom                       |
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
3 rows in set (0.02 sec)

[root@127.0.0.1:8032 16:02:08((none))]$ select group_replication_set_as_primary('80ca4c8a-1649-11ef-ba14-00163eebf995');
+--------------------------------------------------------------------------+
| group_replication_set_as_primary('80ca4c8a-1649-11ef-ba14-00163eebf995') |
+--------------------------------------------------------------------------+
| Primary server switched to: 80ca4c8a-1649-11ef-ba14-00163eebf995         |
+--------------------------------------------------------------------------+
1 row in set (0.03 sec)

[root@127.0.0.1:8032 16:02:28((none))]$ select * from performance_schema.replication_group_members;
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
| CHANNEL_NAME              | MEMBER_ID                            | MEMBER_HOST    | MEMBER_PORT | MEMBER_STATE | MEMBER_ROLE | MEMBER_VERSION | MEMBER_COMMUNICATION_STACK |
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
| group_replication_applier | 80ca4c8a-1649-11ef-ba14-00163eebf101 | 172.17.136.101 |        8032 | ONLINE       | SECONDARY   | 8.0.32         | XCom                       |
| group_replication_applier | 80ca4c8a-1649-11ef-ba14-00163eebf904 | 172.17.138.136 |        8032 | ONLINE       | SECONDARY   | 8.0.32         | XCom                       |
| group_replication_applier | 80ca4c8a-1649-11ef-ba14-00163eebf995 | 172.17.137.95  |        8032 | ONLINE       | PRIMARY     | 8.0.32         | XCom                       |
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
3 rows in set (0.00 sec)

[root@127.0.0.1:8032 16:02:31((none))]$ select group_replication_set_as_primary('80ca4c8a-1649-11ef-ba14-00163eebf904',300);
+------------------------------------------------------------------------------+
| group_replication_set_as_primary('80ca4c8a-1649-11ef-ba14-00163eebf904',300) |
+------------------------------------------------------------------------------+
| Primary server switched to: 80ca4c8a-1649-11ef-ba14-00163eebf904             |
+------------------------------------------------------------------------------+
1 row in set (0.04 sec)

[root@127.0.0.1:8032 16:04:52((none))]$ select * from performance_schema.replication_group_members;
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
| CHANNEL_NAME              | MEMBER_ID                            | MEMBER_HOST    | MEMBER_PORT | MEMBER_STATE | MEMBER_ROLE | MEMBER_VERSION | MEMBER_COMMUNICATION_STACK |
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
| group_replication_applier | 80ca4c8a-1649-11ef-ba14-00163eebf101 | 172.17.136.101 |        8032 | ONLINE       | SECONDARY   | 8.0.32         | XCom                       |
| group_replication_applier | 80ca4c8a-1649-11ef-ba14-00163eebf904 | 172.17.138.136 |        8032 | ONLINE       | PRIMARY     | 8.0.32         | XCom                       |
| group_replication_applier | 80ca4c8a-1649-11ef-ba14-00163eebf995 | 172.17.137.95  |        8032 | ONLINE       | SECONDARY   | 8.0.32         | XCom                       |
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
3 rows in set (0.00 sec)

[root@127.0.0.1:8032 16:05:59((none))]$ SELECT event_name, work_completed, work_estimated FROM performance_schema.events_stages_current WHERE event_name LIKE "%stage/group_rpl%";
+-----------------------------------------------------+----------------+----------------+
| event_name                                          | work_completed | work_estimated |
+-----------------------------------------------------+----------------+----------------+
| stage/group_rpl/Group Replication Module: Executing |           NULL |           NULL |
+-----------------------------------------------------+----------------+----------------+
1 row in set (0.00 sec)

[root@127.0.0.1:8032 16:06:11((none))]$ select event_name, work_completed, work_estimated from performance_schema.events_stages_current where event_name like "%stage/group_rpl%";
+-----------------------------------------------------+----------------+----------------+
| event_name                                          | work_completed | work_estimated |
+-----------------------------------------------------+----------------+----------------+
| stage/group_rpl/Group Replication Module: Executing |           NULL |           NULL |
+-----------------------------------------------------+----------------+----------------+
1 row in set (0.00 sec)


```



## 多主模式切换为单主模式


```sql
select * from performance_schema.replication_group_members;

select group_replication_switch_to_single_primary_mode();

select event_name, work_completed, work_estimated from performance_schema.events_stages_current where event_name like "%stage/group_rpl%";
```

### 举例说明

```sql
[root@127.0.0.1:8032 16:11:48((none))]$ select * from performance_schema.replication_group_members;
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
| CHANNEL_NAME              | MEMBER_ID                            | MEMBER_HOST    | MEMBER_PORT | MEMBER_STATE | MEMBER_ROLE | MEMBER_VERSION | MEMBER_COMMUNICATION_STACK |
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
| group_replication_applier | 80ca4c8a-1649-11ef-ba14-00163eebf101 | 172.17.136.101 |        8032 | ONLINE       | PRIMARY     | 8.0.32         | XCom                       |
| group_replication_applier | 80ca4c8a-1649-11ef-ba14-00163eebf904 | 172.17.138.136 |        8032 | ONLINE       | PRIMARY     | 8.0.32         | XCom                       |
| group_replication_applier | 80ca4c8a-1649-11ef-ba14-00163eebf995 | 172.17.137.95  |        8032 | ONLINE       | PRIMARY     | 8.0.32         | XCom                       |
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
3 rows in set (0.00 sec)

[root@127.0.0.1:8032 16:11:54((none))]$ select group_replication_switch_to_single_primary_mode();
+---------------------------------------------------+
| group_replication_switch_to_single_primary_mode() |
+---------------------------------------------------+
| Mode switched to single-primary successfully.     |
+---------------------------------------------------+
1 row in set (0.07 sec)

[root@127.0.0.1:8032 16:12:01((none))]$ select event_name, work_completed, work_estimated from performance_schema.events_stages_current where event_name like "%stage/group_rpl%";
+-----------------------------------------------------+----------------+----------------+
| event_name                                          | work_completed | work_estimated |
+-----------------------------------------------------+----------------+----------------+
| stage/group_rpl/Group Replication Module: Executing |           NULL |           NULL |
+-----------------------------------------------------+----------------+----------------+
1 row in set (0.00 sec)

[root@127.0.0.1:8032 16:12:07((none))]$ select * from performance_schema.replication_group_members;
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
| CHANNEL_NAME              | MEMBER_ID                            | MEMBER_HOST    | MEMBER_PORT | MEMBER_STATE | MEMBER_ROLE | MEMBER_VERSION | MEMBER_COMMUNICATION_STACK |
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
| group_replication_applier | 80ca4c8a-1649-11ef-ba14-00163eebf101 | 172.17.136.101 |        8032 | ONLINE       | PRIMARY     | 8.0.32         | XCom                       |
| group_replication_applier | 80ca4c8a-1649-11ef-ba14-00163eebf904 | 172.17.138.136 |        8032 | ONLINE       | SECONDARY   | 8.0.32         | XCom                       |
| group_replication_applier | 80ca4c8a-1649-11ef-ba14-00163eebf995 | 172.17.137.95  |        8032 | ONLINE       | SECONDARY   | 8.0.32         | XCom                       |
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
3 rows in set (0.00 sec)


```


## 单主模式切换为多主模式


```sql
select * from performance_schema.replication_group_members;

select group_replication_switch_to_multi_primary_mode();

select event_name, work_completed, work_estimated from performance_schema.events_stages_current where event_name like "%stage/group_rpl%";
```



### 举例说明

```sql
[root@127.0.0.1:8032 16:09:39((none))]$ select * from performance_schema.replication_group_members;
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
| CHANNEL_NAME              | MEMBER_ID                            | MEMBER_HOST    | MEMBER_PORT | MEMBER_STATE | MEMBER_ROLE | MEMBER_VERSION | MEMBER_COMMUNICATION_STACK |
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
| group_replication_applier | 80ca4c8a-1649-11ef-ba14-00163eebf101 | 172.17.136.101 |        8032 | ONLINE       | SECONDARY   | 8.0.32         | XCom                       |
| group_replication_applier | 80ca4c8a-1649-11ef-ba14-00163eebf904 | 172.17.138.136 |        8032 | ONLINE       | PRIMARY     | 8.0.32         | XCom                       |
| group_replication_applier | 80ca4c8a-1649-11ef-ba14-00163eebf995 | 172.17.137.95  |        8032 | ONLINE       | SECONDARY   | 8.0.32         | XCom                       |
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
3 rows in set (0.00 sec)

[root@127.0.0.1:8032 16:09:47((none))]$ select group_replication_switch_to_multi_primary_mode();
+--------------------------------------------------+
| group_replication_switch_to_multi_primary_mode() |
+--------------------------------------------------+
| Mode switched to multi-primary successfully.     |
+--------------------------------------------------+
1 row in set (0.15 sec)

[root@127.0.0.1:8032 16:10:46((none))]$ select event_name, work_completed, work_estimated from performance_schema.events_stages_current where event_name like "%stage/group_rpl%";
+-----------------------------------------------------+----------------+----------------+
| event_name                                          | work_completed | work_estimated |
+-----------------------------------------------------+----------------+----------------+
| stage/group_rpl/Group Replication Module: Executing |           NULL |           NULL |
+-----------------------------------------------------+----------------+----------------+
1 row in set (0.00 sec)

[root@127.0.0.1:8032 16:10:47((none))]$ select * from performance_schema.replication_group_members;
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
| CHANNEL_NAME              | MEMBER_ID                            | MEMBER_HOST    | MEMBER_PORT | MEMBER_STATE | MEMBER_ROLE | MEMBER_VERSION | MEMBER_COMMUNICATION_STACK |
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
| group_replication_applier | 80ca4c8a-1649-11ef-ba14-00163eebf101 | 172.17.136.101 |        8032 | ONLINE       | PRIMARY     | 8.0.32         | XCom                       |
| group_replication_applier | 80ca4c8a-1649-11ef-ba14-00163eebf904 | 172.17.138.136 |        8032 | ONLINE       | PRIMARY     | 8.0.32         | XCom                       |
| group_replication_applier | 80ca4c8a-1649-11ef-ba14-00163eebf995 | 172.17.137.95  |        8032 | ONLINE       | PRIMARY     | 8.0.32         | XCom                       |
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
3 rows in set (0.00 sec)


```






