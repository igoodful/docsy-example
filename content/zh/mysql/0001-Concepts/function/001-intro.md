---
title: 1. 前言
description: 前言
date: 2023-10-12
weight: 1
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>




## mysql.help_topic

```sql
[root@127.0.0.1:8032 10:41:17(mysql)]$ desc help_topic;
+------------------+-------------------+------+-----+---------+-------+
| Field            | Type              | Null | Key | Default | Extra |
+------------------+-------------------+------+-----+---------+-------+
| help_topic_id    | int unsigned      | NO   | PRI | NULL    |       |
| name             | char(64)          | NO   | UNI | NULL    |       |
| help_category_id | smallint unsigned | NO   |     | NULL    |       |
| description      | text              | NO   |     | NULL    |       |
| example          | text              | NO   |     | NULL    |       |
| url              | text              | NO   |     | NULL    |       |
+------------------+-------------------+------+-----+---------+-------+
6 rows in set (0.00 sec)

[root@127.0.0.1:8032 10:42:00(mysql)]$ show create table help_topic\G
*************************** 1. row ***************************
       Table: help_topic
Create Table: CREATE TABLE `help_topic` (
  `help_topic_id` int unsigned NOT NULL,
  `name` char(64) NOT NULL,
  `help_category_id` smallint unsigned NOT NULL,
  `description` text NOT NULL,
  `example` text NOT NULL,
  `url` text NOT NULL,
  PRIMARY KEY (`help_topic_id`),
  UNIQUE KEY `name` (`name`)
) /*!50100 TABLESPACE `mysql` */ ENGINE=InnoDB DEFAULT CHARSET=utf8mb3 STATS_PERSISTENT=0 ROW_FORMAT=DYNAMIC COMMENT='help topics'
1 row in set (0.00 sec)

[root@127.0.0.1:8032 10:42:04(mysql)]$ select count(*) from help_topic;
+----------+
| count(*) |
+----------+
|      703 |
+----------+
1 row in set (0.00 sec)

[root@127.0.0.1:8032 10:42:08(mysql)]$ select * from help_topic limit 2\G
*************************** 1. row ***************************
   help_topic_id: 0
            name: HELP_DATE
help_category_id: 1
     description: This help information was generated from the MySQL 8.0 Reference Manual
on: 2022-11-26

         example:
             url:
*************************** 2. row ***************************
   help_topic_id: 1
            name: HELP_VERSION
help_category_id: 1
     description: This help information was generated from the MySQL 8.0 Reference Manual
on: 2022-11-26 (revision: 74597)

This information applies to MySQL 8.0 through 8.0.31.

         example:
             url:
2 rows in set (0.00 sec)

```

{{<note>}}

- 一共有$703$个函数、关键字、运算法、引擎等
- help_topic表的help_category_id是help_category表的主键，即 help_category: help_category_id = 1: N




{{</note>}}

## mysql.help_category
> 表示类型

```sql
[root@127.0.0.1:8032 10:43:16(mysql)]$ desc help_category;
+--------------------+-------------------+------+-----+---------+-------+
| Field              | Type              | Null | Key | Default | Extra |
+--------------------+-------------------+------+-----+---------+-------+
| help_category_id   | smallint unsigned | NO   | PRI | NULL    |       |
| name               | char(64)          | NO   | UNI | NULL    |       |
| parent_category_id | smallint unsigned | YES  |     | NULL    |       |
| url                | text              | NO   |     | NULL    |       |
+--------------------+-------------------+------+-----+---------+-------+
4 rows in set (0.06 sec)

[root@127.0.0.1:8032 10:43:24(mysql)]$ show create table help_category\G
*************************** 1. row ***************************
       Table: help_category
Create Table: CREATE TABLE `help_category` (
  `help_category_id` smallint unsigned NOT NULL,
  `name` char(64) NOT NULL,
  `parent_category_id` smallint unsigned DEFAULT NULL,
  `url` text NOT NULL,
  PRIMARY KEY (`help_category_id`),
  UNIQUE KEY `name` (`name`)
) /*!50100 TABLESPACE `mysql` */ ENGINE=InnoDB DEFAULT CHARSET=utf8mb3 STATS_PERSISTENT=0 ROW_FORMAT=DYNAMIC COMMENT='help categories'
1 row in set (0.00 sec)

[root@127.0.0.1:8032 10:43:31(mysql)]$ select count(*) from help_category;
+----------+
| count(*) |
+----------+
|       53 |
+----------+
1 row in set (0.00 sec)

[root@127.0.0.1:8032 10:43:49(mysql)]$ select * from help_category;
+------------------+---------------------------------------+--------------------+-----+
| help_category_id | name                                  | parent_category_id | url |
+------------------+---------------------------------------+--------------------+-----+
|                0 | Contents                              |                  0 |     |
|                1 | Help Metadata                         |                  0 |     |
|                2 | Data Types                            |                  0 |     |
|                3 | Administration                        |                  0 |     |
|                4 | Functions                             |                  0 |     |
|                5 | Enterprise Encryption Functions       |                  4 |     |
|                6 | Language Structure                    |                  0 |     |
|                7 | Geographic Features                   |                  0 |     |
|                8 | MBR                                   |                  7 |     |
|                9 | WKT                                   |                  7 |     |
|               10 | Comparison Operators                  |                  4 |     |
|               11 | Logical Operators                     |                  4 |     |
|               12 | Flow Control Functions                |                  4 |     |
|               13 | Numeric Functions                     |                  4 |     |
|               14 | Date and Time Functions               |                  4 |     |
|               15 | String Functions                      |                  4 |     |
|               16 | Cast Functions and Operators          |                  4 |     |
|               17 | XML                                   |                  4 |     |
|               18 | Bit Functions                         |                  4 |     |
|               19 | Encryption Functions                  |                  4 |     |
|               20 | Locking Functions                     |                  4 |     |
|               21 | Information Functions                 |                  4 |     |
|               22 | Spatial Functions                     |                  4 |     |
|               23 | WKT Functions                         |                 22 |     |
|               24 | WKB Functions                         |                 22 |     |
|               25 | Geometry Constructors                 |                 22 |     |
|               26 | Geometry Property Functions           |                 22 |     |
|               27 | Point Property Functions              |                 22 |     |
|               28 | LineString Property Functions         |                 22 |     |
|               29 | Polygon Property Functions            |                 22 |     |
|               30 | GeometryCollection Property Functions |                 22 |     |
|               31 | Geometry Relation Functions           |                 22 |     |
|               32 | MBR Functions                         |                 22 |     |
|               33 | GTID                                  |                  4 |     |
|               34 | Aggregate Functions and Modifiers     |                  4 |     |
|               35 | GROUP BY Functions and Modifiers      |                  4 |     |
|               36 | Window Functions                      |                  4 |     |
|               37 | Performance Schema Functions          |                  4 |     |
|               38 | Internal Functions                    |                  4 |     |
|               39 | Miscellaneous Functions               |                  4 |     |
|               40 | Data Definition                       |                  0 |     |
|               41 | Data Manipulation                     |                  0 |     |
|               42 | Transactions                          |                  0 |     |
|               43 | Replication Statements                |                  0 |     |
|               44 | Prepared Statements                   |                  0 |     |
|               45 | Compound Statements                   |                  0 |     |
|               46 | Account Management                    |                  0 |     |
|               47 | Table Maintenance                     |                  0 |     |
|               48 | Loadable Functions                    |                  0 |     |
|               49 | Components                            |                  0 |     |
|               50 | Plugins                               |                  0 |     |
|               51 | Utility                               |                  0 |     |
|               52 | Storage Engines                       |                  0 |     |
+------------------+---------------------------------------+--------------------+-----+
53 rows in set (0.00 sec)

-- 查看每种类型有多少条详情
[root@127.0.0.1:8032 10:55:25(mysql)]$ select hc.name ,count(*) from help_category hc,help_topic ht where ht.help_category_id = hc.help_category_id group by hc.name order by count(*) desc ;
+---------------------------------------+----------+
| name                                  | count(*) |
+---------------------------------------+----------+
| Administration                        |       61 |
| String Functions                      |       60 |
| Date and Time Functions               |       60 |
| MBR Functions                         |       54 |
| Data Definition                       |       39 |
| Numeric Functions                     |       37 |
| Data Types                            |       35 |
| Internal Functions                    |       29 |
| Miscellaneous Functions               |       21 |
| Data Manipulation                     |       21 |
| Compound Statements                   |       20 |
| Comparison Operators                  |       20 |
| Aggregate Functions and Modifiers     |       19 |
| Information Functions                 |       18 |
| Account Management                    |       15 |
| GeometryCollection Property Functions |       13 |
| Replication Statements                |       13 |
| Encryption Functions                  |       12 |
| Window Functions                      |       11 |
| WKB Functions                         |       11 |
| Transactions                          |       11 |
| Geometry Relation Functions           |       11 |
| Enterprise Encryption Functions       |        9 |
| WKT Functions                         |        8 |
| Geometry Constructors                 |        8 |
| Bit Functions                         |        7 |
| Logical Operators                     |        6 |
| LineString Property Functions         |        6 |
| Geometry Property Functions           |        6 |
| Polygon Property Functions            |        5 |
| Table Maintenance                     |        5 |
| Locking Functions                     |        5 |
| Utility                               |        5 |
| Components                            |        4 |
| Prepared Statements                   |        4 |
| Point Property Functions              |        4 |
| Performance Schema Functions          |        4 |
| GTID                                  |        4 |
| Flow Control Functions                |        4 |
| Cast Functions and Operators          |        3 |
| Geographic Features                   |        3 |
| Help Metadata                         |        2 |
| XML                                   |        2 |
| Loadable Functions                    |        2 |
| Language Structure                    |        2 |
| WKT                                   |        1 |
| MBR                                   |        1 |
| Plugins                               |        1 |
| Storage Engines                       |        1 |
+---------------------------------------+----------+
49 rows in set (0.00 sec)

--- Contents，Functions，Spatial Functions，GROUP BY Functions and Modifiers没有help_topic条目
[root@127.0.0.1:8032 11:07:29(mysql)]$ select * from help_category tt where  tt.name not in( select hc.name cnt from help_category hc,help_topic ht where ht.help_category_id = hc.help_category_id group by hc.name order by count(*) desc );
+------------------+----------------------------------+--------------------+-----+
| help_category_id | name                             | parent_category_id | url |
+------------------+----------------------------------+--------------------+-----+
|                0 | Contents                         |                  0 |     |
|                4 | Functions                        |                  0 |     |
|               22 | Spatial Functions                |                  4 |     |
|               35 | GROUP BY Functions and Modifiers |                  4 |     |
+------------------+----------------------------------+--------------------+-----+
4 rows in set (0.04 sec)


```

{{<note>}}

- Contents，Functions，Spatial Functions，GROUP BY Functions and Modifiers没有help_topic条目


{{</note>}}




## mysql.help_relation





## mysql.help_keyword

































