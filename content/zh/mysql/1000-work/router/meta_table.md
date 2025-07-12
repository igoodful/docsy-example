---
title: rds元数据库
description: meta_table
date: 2023-10-24
weight: 70000


---

{{< alert >}}



{{< /alert >}}

 ### 版本


```sql
mysql> show create table greatrds.cluster\G
*************************** 1. row ***************************
       Table: cluster
Create Table: CREATE TABLE `cluster` (
  `created_at` datetime DEFAULT NULL,
  `updated_at` datetime DEFAULT NULL,
  `id` int NOT NULL AUTO_INCREMENT,
  `project_id` varchar(255) DEFAULT NULL,
  `user_id` varchar(255) DEFAULT NULL,
  `uuid` varchar(36) DEFAULT NULL,
  `name` varchar(255) DEFAULT NULL,
  `cluster_template_id` varchar(36) DEFAULT NULL,
  `configuration_id` varchar(36) DEFAULT NULL,
  `keypair` varchar(255) DEFAULT NULL,
  `labels` text,
  `address` varchar(255) DEFAULT NULL,
  `port` int DEFAULT NULL,
  `cluster_type` varchar(255) DEFAULT NULL,
  `status` varchar(128) DEFAULT NULL,
  `status_reason` text,
  `task_status` varchar(128) DEFAULT NULL,
  `task_status_reason` text,
  `create_timeout` int DEFAULT NULL,
  `username` varchar(255) DEFAULT NULL,
  `password` varchar(255) DEFAULT NULL,
  `description` varchar(255) DEFAULT NULL,
  `management` tinyint(1) DEFAULT NULL,
  `server_type` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `uniq_bay0uuid` (`uuid`),
  UNIQUE KEY `uniq_cluster0project_id0cluster_type0name` (`name`,`project_id`,`cluster_type`),
  CONSTRAINT `cluster_chk_1` CHECK ((`management` in (0,1)))
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)

mysql> show create table greatrds.scheduler_task\G
*************************** 1. row ***************************
       Table: scheduler_task
Create Table: CREATE TABLE `scheduler_task` (
  `created_at` datetime DEFAULT NULL,
  `updated_at` datetime DEFAULT NULL,
  `id` int NOT NULL AUTO_INCREMENT,
  `uuid` varchar(36) NOT NULL,
  `scheduler_name` varchar(128) DEFAULT NULL,
  `repeat` smallint NOT NULL,
  `period` varchar(128) DEFAULT NULL,
  `scheduler_time` varchar(128) NOT NULL,
  `status` smallint NOT NULL,
  `extend` varchar(256) DEFAULT NULL,
  `level` enum('用户','系统') NOT NULL,
  `notification_lists` varchar(256) DEFAULT NULL,
  `before_exec_time` int DEFAULT NULL,
  `after_exec_time` int DEFAULT NULL,
  `user_uuid` varchar(36) NOT NULL,
  `project_id` varchar(36) NOT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `uniq_scheduler0uuid` (`uuid`)
) ENGINE=InnoDB AUTO_INCREMENT=9 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)




```


### 备份计划
```sql
mysql> show create table greatrds.backup_schedule\G
*************************** 1. row ***************************
       Table: backup_schedule
Create Table: CREATE TABLE `backup_schedule` (
  `created_at` datetime DEFAULT NULL,
  `updated_at` datetime DEFAULT NULL,
  `id` int NOT NULL AUTO_INCREMENT,
  `uuid` varchar(40) NOT NULL,
  `name` varchar(255) DEFAULT NULL,
  `db_type` varchar(255) DEFAULT NULL,
  `cluster_id` varchar(255) DEFAULT NULL,
  `backup_server_uuid` varchar(255) DEFAULT NULL,
  `backup_mode` varchar(255) DEFAULT NULL,
  `scheduler_uuid` varchar(36) DEFAULT NULL,
  `enabled` tinyint(1) NOT NULL,
  `created_by` varchar(255) DEFAULT NULL,
  `description` varchar(255) DEFAULT NULL,
  `project_id` varchar(255) DEFAULT NULL,
  `user_id` varchar(255) DEFAULT NULL,
  `cluster_type` varchar(255) DEFAULT NULL,
  `properties` text,
  PRIMARY KEY (`id`),
  UNIQUE KEY `uniq_backup_schedule0uuid` (`uuid`),
  CONSTRAINT `backup_schedule_chk_1` CHECK ((`enabled` in (0,1)))
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.00 sec)

mysql> select * from backup_schedule\G
*************************** 1. row ***************************
        created_at: 2023-12-12 13:47:55
        updated_at: 2023-12-12 14:00:55
                id: 1
              uuid: db573f7d-bfd0-4dff-b933-692e39296ace
              name: 77
           db_type: NULL
        cluster_id: 243ec456-9f9d-4719-8a3e-0fe5c7a41db8
backup_server_uuid: 32c3de7f-0394-4125-b56b-5549631051a0
       backup_mode: PHYSICAL_MODE
    scheduler_uuid: c9538a5b-97f0-4672-9659-e10e5ba18442
           enabled: 1
        created_by: admin
       description: NULL
        project_id: aba30c84-a884-40ae-b049-40e547b4e847
           user_id: 027619af-d483-4b7e-8ef7-750080069bdf
      cluster_type: cluster4
        properties: {"backup": "{\"status\": \"SUCCESS\"}"}
1 row in set (0.00 sec)
```


### 备份服务器
```sql
mysql> show create table greatrds.backup_server\G
*************************** 1. row ***************************
       Table: backup_server
Create Table: CREATE TABLE `backup_server` (
  `created_at` datetime DEFAULT NULL,
  `updated_at` datetime DEFAULT NULL,
  `id` int NOT NULL AUTO_INCREMENT,
  `uuid` varchar(40) NOT NULL,
  `name` varchar(255) DEFAULT NULL,
  `address` varchar(64) DEFAULT NULL,
  `master_address` varchar(64) DEFAULT NULL,
  `node_type` varchar(255) DEFAULT NULL,
  `running_status` varchar(128) DEFAULT NULL,
  `storage_path` varchar(255) DEFAULT NULL,
  `monitor_data` text,
  `description` varchar(255) DEFAULT NULL,
  `project_id` varchar(255) DEFAULT NULL,
  `user_id` varchar(255) DEFAULT NULL,
  `cluster_type` varchar(255) DEFAULT NULL,
  `properties` text,
  PRIMARY KEY (`id`),
  UNIQUE KEY `uniq_backup_server0uuid` (`uuid`)
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
1 row in set (0.01 sec)


mysql> select * from backup_server\G
*************************** 1. row ***************************
    created_at: 2023-12-12 13:45:54
    updated_at: 2023-12-13 08:41:56
            id: 1
          uuid: 32c3de7f-0394-4125-b56b-5549631051a0
          name: 77
       address: 172.17.134.77
master_address: NULL
     node_type: LEADER
running_status: RUNNING
  storage_path: /data/storage_path/backup_file
  monitor_data: {"nodes": [{"path": "/data/storage_path/backup_file", "disk_total": 201109, "disk_free": 71374, "disk_used": 121342, "disk_percent": 63.0}]}
   description: NULL
    project_id: aba30c84-a884-40ae-b049-40e547b4e847
       user_id: 027619af-d483-4b7e-8ef7-750080069bdf
  cluster_type: cluster4
    properties: {}
1 row in set (0.01 sec)

```








