---
title: 性能优化
description: A short lead description about this content page. It can be **bold** or _italic_ and can be split over multiple paragraphs.
date: 2017-01-05
weight: 10000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{<alert>}}



{{</alert>}}

| 配置                  | 参数          | 备注         |
| :-------------------- | :------------ | :----------- |
| version               | 8.0.32        | 版本         |
| engine                | innodb        | 存储引擎     |
| transaction_isolation | read commited | 事务隔离级别 |






### 哪些索引创建后从来没有使用过？
```sql
mysql> select object_schema,
object_name,
index_name
from performance_schema.table_io_waits_summary_by_index_usage
where index_name is not null and count_star = 0 and object_schema != 'mysql'
order by object_schema, object_name;


+---------------+-------------+------------+
| object_schema | object_name | index_name |
+---------------+-------------+------------+
| apple         | sbtest1     | k_1        |
| apple         | sbtest10    | PRIMARY    |
| apple         | sbtest10    | k_10       |
| apple         | sbtest11    | PRIMARY    |
| apple         | sbtest11    | k_11       |
| apple         | sbtest12    | PRIMARY    |
| apple         | sbtest12    | k_12       |
| apple         | sbtest13    | k_13       |
| apple         | sbtest13    | PRIMARY    |
| apple         | sbtest14    | k_14       |
| apple         | sbtest14    | PRIMARY    |
| apple         | sbtest15    | k_15       |
| apple         | sbtest15    | PRIMARY    |
| apple         | sbtest16    | PRIMARY    |
| apple         | sbtest16    | k_16       |
| apple         | sbtest2     | PRIMARY    |
| apple         | sbtest2     | k_2        |
| apple         | sbtest3     | PRIMARY    |
| apple         | sbtest3     | k_3        |
| apple         | sbtest4     | PRIMARY    |
+---------------+-------------+------------+
20 rows in set (0.01 sec)

```


### 哪些SQL语句执行次数最多？
```sql

mysql> select schema_name,
  digest_text,
  count_star,
  sum_rows_sent,
  sum_rows_examined,
  first_seen,
  last_seen
from performance_schema.events_statements_summary_by_digest
where schema_name is not null and schema_name != 'information_schema'
order by count_star desc
limit 5\G;

*************************** 1. row ***************************
      schema_name: apple
      digest_text: SELECT `LAST_INSERT_ID` ( )
       count_star: 59773
    sum_rows_sent: 59773
sum_rows_examined: 59773
       first_seen: 2023-10-16 15:06:35.709255
        last_seen: 2023-10-16 15:41:26.672219
*************************** 2. row ***************************
      schema_name: apple
      digest_text: USE `apple`
       count_star: 29936
    sum_rows_sent: 0
sum_rows_examined: 0
       first_seen: 2023-10-16 15:06:35.646624
        last_seen: 2023-10-16 15:41:26.731062
*************************** 3. row ***************************
      schema_name: apple
      digest_text: BEGIN
       count_star: 29887
    sum_rows_sent: 0
sum_rows_examined: 0
       first_seen: 2023-10-16 15:06:35.709142
        last_seen: 2023-10-16 15:41:26.622873
*************************** 4. row ***************************
      schema_name: apple
      digest_text: COMMIT
       count_star: 29882
    sum_rows_sent: 0
sum_rows_examined: 0
       first_seen: 2023-10-16 15:06:35.908239
        last_seen: 2023-10-16 15:41:26.700821
*************************** 5. row ***************************
      schema_name: apple
      digest_text: INSERT INTO `sbtest13` ( `k` , `c` , `pad` ) VALUES (...) /* , ... */
       count_star: 1868
    sum_rows_sent: 0
sum_rows_examined: 0
       first_seen: 2023-10-16 15:33:38.578614
        last_seen: 2023-10-16 15:41:23.971452
5 rows in set (0.03 sec)


```

### 哪些SQL语句平均耗时最长？

```sql
mysql> select
  schema_name,
  digest_text,
  count_star,
  sys.format_time(sum_timer_wait) as sum_time,
  sys.format_time(min_timer_wait) as min_time,
  sys.format_time(avg_timer_wait) as avg_time,
  sys.format_time(max_timer_wait) as max_time,
  sys.format_time(sum_lock_time) as sum_lock_time,
  sum_rows_affected,
  sum_rows_sent,
  sum_rows_examined,
  first_seen,
  last_seen
from performance_schema.events_statements_summary_by_digest
where schema_name is not null and schema_name != 'information_schema'
order by avg_timer_wait desc,count_star desc
limit 5\G;

*************************** 1. row ***************************
      schema_name: apple
      digest_text: CREATE INDEX `k_2` ON `sbtest2` ( `k` )
       count_star: 1
         sum_time: 55.04 s
         min_time: 55.04 s
         avg_time: 55.04 s
         max_time: 55.04 s
    sum_lock_time: 19.06 ms
sum_rows_affected: 0
    sum_rows_sent: 0
sum_rows_examined: 0
       first_seen: 2023-10-16 15:15:55.935623
        last_seen: 2023-10-16 15:15:55.935623
*************************** 2. row ***************************
      schema_name: apple
      digest_text: CREATE INDEX `k_4` ON `sbtest4` ( `k` )
       count_star: 1
         sum_time: 55.03 s
         min_time: 55.03 s
         avg_time: 55.03 s
         max_time: 55.03 s
    sum_lock_time: 16.85 ms
sum_rows_affected: 0
    sum_rows_sent: 0
sum_rows_examined: 0
       first_seen: 2023-10-16 15:15:55.935639
        last_seen: 2023-10-16 15:15:55.935639
*************************** 3. row ***************************
      schema_name: apple
      digest_text: CREATE INDEX `k_3` ON `sbtest3` ( `k` )
       count_star: 1
         sum_time: 54.6 s
         min_time: 54.6 s
         avg_time: 54.6 s
         max_time: 54.6 s
    sum_lock_time: 331 us
sum_rows_affected: 0
    sum_rows_sent: 0
sum_rows_examined: 0
       first_seen: 2023-10-16 15:15:56.060005
        last_seen: 2023-10-16 15:15:56.060005
*************************** 4. row ***************************
      schema_name: apple
      digest_text: CREATE INDEX `k_6` ON `sbtest6` ( `k` )
       count_star: 1
         sum_time: 50.34 s
         min_time: 50.34 s
         avg_time: 50.34 s
         max_time: 50.34 s
    sum_lock_time: 28.54 ms
sum_rows_affected: 0
    sum_rows_sent: 0
sum_rows_examined: 0
       first_seen: 2023-10-16 15:24:47.839541
        last_seen: 2023-10-16 15:24:47.839541
*************************** 5. row ***************************
      schema_name: apple
      digest_text: CREATE INDEX `k_1` ON `sbtest1` ( `k` )
       count_star: 1
         sum_time: 50.11 s
         min_time: 50.11 s
         avg_time: 50.11 s
         max_time: 50.11 s
    sum_lock_time: 804 us
sum_rows_affected: 0
    sum_rows_sent: 0
sum_rows_examined: 0
       first_seen: 2023-10-16 15:15:57.660656
        last_seen: 2023-10-16 15:15:57.660656
5 rows in set (0.03 sec)

ERROR:
No query specified

```

### 哪些SQL扫描的行数最多？

```sql
mysql> select
  schema_name,
  digest_text,
  count_star,
  sys.format_time(avg_timer_wait) as avg_time,
  sum_rows_sent,
  sum_rows_examined,
  first_seen,
  last_seen
from performance_schema.events_statements_summary_by_digest
where schema_name is not null and schema_name != 'information_schema'
order by sum_rows_examined desc
limit 5\G;

---
*************************** 1. row ***************************
      schema_name: apple
      digest_text: SELECT `LAST_INSERT_ID` ( )
       count_star: 59773
         avg_time: 285.25 us
    sum_rows_sent: 59773
sum_rows_examined: 59773
       first_seen: 2023-10-16 15:06:35.709255
        last_seen: 2023-10-16 15:41:26.672219
*************************** 2. row ***************************
      schema_name: apple
      digest_text: SELECT `object_schema` , `object_name` , `index_name` FROM `performance_schema` . `table_io_waits_summary_by_index_usage` WHERE `index_name` IS NOT NULL AND `count_star` = ? AND `object_schema` != ? ORDER BY `object_schema` , `object_name`
       count_star: 3
         avg_time: 10.58 ms
    sum_rows_sent: 276
sum_rows_examined: 975
       first_seen: 2023-10-17 09:03:34.105659
        last_seen: 2023-10-17 09:21:22.482287
*************************** 3. row ***************************
      schema_name: apple
      digest_text: SELECT SCHEMA_NAME , `digest_text` , `count_star` , `sys` . `format_time` ( `sum_timer_wait` ) AS `sum_time` , `sys` . `format_time` ( `min_timer_wait` ) AS `min_time` , `sys` . `format_time` ( `avg_timer_wait` ) AS `avg_time` , `sys` . `format_time` ( `max_timer_wait` ) AS `max_time` , `sys` . `format_time` ( `sum_lock_time` ) AS `sum_lock_time` , `sum_rows_affected` , `sum_rows_sent` , `sum_rows_examined` , `first_seen` , `last_seen` FROM `performance_schema` . `events_statements_summary_by_digest` WHERE SCHEMA_NAME IS NOT NULL AND SCHEMA_NAME != ? ORDER BY `avg_timer_wait` DESC , `count_star` DESC LIMIT ?
       count_star: 4
         avg_time: 16.92 ms
    sum_rows_sent: 30
sum_rows_examined: 557
       first_seen: 2023-10-17 09:23:44.833430
        last_seen: 2023-10-17 09:25:10.099692
*************************** 4. row ***************************
      schema_name: apple
      digest_text: SELECT `object_schema` , `object_name` , `index_name` FROM `performance_schema` . `table_io_waits_summary_by_index_usage` WHERE `index_name` IS NOT NULL AND `count_star` = ? AND `object_schema` != ? ORDER BY `object_schema` , `object_name` LIMIT ?
       count_star: 2
         avg_time: 2.94 ms
    sum_rows_sent: 30
sum_rows_examined: 496
       first_seen: 2023-10-17 09:21:38.582372
        last_seen: 2023-10-17 09:21:50.812534
*************************** 5. row ***************************
      schema_name: apple
      digest_text: SELECT SCHEMA_NAME , `digest_text` , `count_star` , `sum_rows_sent` , `sum_rows_examined` , `first_seen` , `last_seen` FROM `performance_schema` . `events_statements_summary_by_digest` WHERE SCHEMA_NAME IS NOT NULL AND SCHEMA_NAME != ? ORDER BY `count_star` DESC LIMIT ?
       count_star: 2
         avg_time: 60.12 ms
    sum_rows_sent: 10
sum_rows_examined: 269
       first_seen: 2023-10-17 09:15:57.745582
        last_seen: 2023-10-17 09:19:34.478325
5 rows in set (0.02 sec)

ERROR:
No query specified


```

### 哪些SQL排序数最多？

```sql
mysql> select
  schema_name,
  digest_text,
  count_star,
  sum_rows_sent,
  sum_sort_rows,
  first_seen,
  last_seen
from performance_schema.events_statements_summary_by_digest
where schema_name is not null and schema_name != 'information_schema'
order by sum_sort_rows desc
limit 5\G;

*************************** 1. row ***************************
  schema_name: apple
  digest_text: SELECT `object_schema` , `object_name` , `index_name` FROM `performance_schema` . `table_io_waits_summary_by_index_usage` WHERE `index_name` IS NOT NULL AND `count_star` = ? AND `object_schema` != ? ORDER BY `object_schema` , `object_name`
   count_star: 3
sum_rows_sent: 276
sum_sort_rows: 276
   first_seen: 2023-10-17 09:03:34.105659
    last_seen: 2023-10-17 09:21:22.482287
*************************** 2. row ***************************
  schema_name: apple
  digest_text: SELECT ( `cat` . `name` COLLATE `utf8_tolower_ci` ) AS `TABLE_CATALOG` , ( `sch` . `name` COLLATE `utf8_tolower_ci` ) AS `TABLE_SCHEMA` , ( `tbl` . `name` COLLATE `utf8_tolower_ci` ) AS `TABLE_NAME` , `tbl` . `type` AS `TABLE_TYPE` , IF ( ( `tbl` . `type` = ? ) , `tbl` . `engine` , ? ) AS `ENGINE` , IF ( ( `tbl` . `type` = ? ) , ?, ... ) AS `VERSION` , `tbl` . `row_format` AS `ROW_FORMAT` , IF ( ( `tbl` . `type` = ? ) , ? , `internal_table_rows` ( `sch` . `name` , `tbl` . `name` , IF ( ( `tbl` . `partition_type` IS NULL ) , `tbl` . `engine` , ? ) , `tbl` . `se_private_id` , ( `tbl` . `hidden` != ? ) , `ts` . `se_private_data` , COALESCE ( `stat` . `table_rows` , ? ) , COALESCE ( CAST ( `stat` . `cached_time` AS UNSIGNED ) , ? ) ) ) AS `TABLE_ROWS` , IF ( ( `tbl` . `type` = ? ) , ? , `internal_avg_row_length` ( `sch` . `name` , `tbl` . `name` , IF ( ( `tbl` . `partition_type` IS NULL ) , `tbl` . `engine` , ? ) , `tbl` . `se_private_id` , ( `tbl` . `hidden` != ? ) , `ts` . `se_private_data` , COALESCE ( `stat` .
   count_star: 4
sum_rows_sent: 64
sum_sort_rows: 64
   first_seen: 2023-10-16 15:35:09.750729
    last_seen: 2023-10-17 09:02:11.844992
*************************** 3. row ***************************
  schema_name: apple
  digest_text: SELECT `object_schema` , `object_name` , `index_name` FROM `performance_schema` . `table_io_waits_summary_by_index_usage` WHERE `index_name` IS NOT NULL AND `count_star` = ? AND `object_schema` != ? ORDER BY `object_schema` , `object_name` LIMIT ?
   count_star: 2
sum_rows_sent: 30
sum_sort_rows: 30
   first_seen: 2023-10-17 09:21:38.582372
    last_seen: 2023-10-17 09:21:50.812534
*************************** 4. row ***************************
  schema_name: apple
  digest_text: SELECT SCHEMA_NAME , `digest_text` , `count_star` , `sys` . `format_time` ( `sum_timer_wait` ) AS `sum_time` , `sys` . `format_time` ( `min_timer_wait` ) AS `min_time` , `sys` . `format_time` ( `avg_timer_wait` ) AS `avg_time` , `sys` . `format_time` ( `max_timer_wait` ) AS `max_time` , `sys` . `format_time` ( `sum_lock_time` ) AS `sum_lock_time` , `sum_rows_affected` , `sum_rows_sent` , `sum_rows_examined` , `first_seen` , `last_seen` FROM `performance_schema` . `events_statements_summary_by_digest` WHERE SCHEMA_NAME IS NOT NULL AND SCHEMA_NAME != ? ORDER BY `avg_timer_wait` DESC , `count_star` DESC LIMIT ?
   count_star: 4
sum_rows_sent: 30
sum_sort_rows: 30
   first_seen: 2023-10-17 09:23:44.833430
    last_seen: 2023-10-17 09:25:10.099692
*************************** 5. row ***************************
  schema_name: apple
  digest_text: SELECT ( `cat` . `name` COLLATE `utf8_tolower_ci` ) AS `CATALOG_NAME` , ( `sch` . `name` COLLATE `utf8_tolower_ci` ) AS `SCHEMA_NAME` , `cs` . `name` AS `DEFAULT_CHARACTER_SET_NAME` , `col` . `name` AS `DEFAULT_COLLATION_NAME` , ? AS `SQL_PATH` , `sch` . `default_encryption` AS `DEFAULT_ENCRYPTION` FROM ( ( ( `mysql` . `schemata` `sch` JOIN `mysql` . `catalogs` `cat` ON ( ( `cat` . `id` = `sch` . `catalog_id` ) ) ) JOIN `mysql` . `collations` `col` ON ( ( `sch` . `default_collation_id` = `col` . `id` ) ) ) JOIN `mysql` . `character_sets` `cs` ON ( ( `col` . `character_set_id` = `cs` . `id` ) ) ) WHERE ( ? != `can_access_database` ( `sch` . `name` ) )
   count_star: 3
sum_rows_sent: 27
sum_sort_rows: 27
   first_seen: 2023-10-16 15:35:09.706493
    last_seen: 2023-10-17 09:02:08.146039
5 rows in set (0.00 sec)

ERROR:
No query specified


```

### 哪些SQL使用临时表最多？

```sql
mysql> select
  schema_name,
  digest_text,
  sum_created_tmp_disk_tables,
  sum_created_tmp_tables,
  first_seen,
  last_seen
from performance_schema.events_statements_summary_by_digest
where schema_name is not null and schema_name != 'information_schema'
order by sum_created_tmp_disk_tables desc
limit 5\G;
---------------
*************************** 1. row ***************************
                schema_name: apple
                digest_text: SELECT `trace` FROM `information_schema` . `optimizer_trace`
sum_created_tmp_disk_tables: 3
     sum_created_tmp_tables: 3
                 first_seen: 2023-10-16 15:43:46.325619
                  last_seen: 2023-10-16 15:44:28.581523
*************************** 2. row ***************************
                schema_name: apple
                digest_text: CREATE TABLE `sbtest13` ( `id` INTEGER NOT NULL AUTO_INCREMENT , `k` INTEGER DEFAULT ? NOT NULL , `c` CHARACTER (?) DEFAULT ? NOT NULL , `pad` CHARACTER (?) DEFAULT ? NOT NULL , PRIMARY KEY ( `id` ) ) ENGINE = `innodb`
sum_created_tmp_disk_tables: 0
     sum_created_tmp_tables: 0
                 first_seen: 2023-10-16 15:33:37.812234
                  last_seen: 2023-10-16 15:33:37.812234
*************************** 3. row ***************************
                schema_name: apple
                digest_text: INSERT INTO `sbtest15` ( `k` , `c` , `pad` ) VALUES (...) /* , ... */
sum_created_tmp_disk_tables: 0
     sum_created_tmp_tables: 0
                 first_seen: 2023-10-16 15:33:38.495421
                  last_seen: 2023-10-16 15:41:25.612621
*************************** 4. row ***************************
                schema_name: apple
                digest_text: CREATE TABLE `sbtest14` ( `id` INTEGER NOT NULL AUTO_INCREMENT , `k` INTEGER DEFAULT ? NOT NULL , `c` CHARACTER (?) DEFAULT ? NOT NULL , `pad` CHARACTER (?) DEFAULT ? NOT NULL , PRIMARY KEY ( `id` ) ) ENGINE = `innodb`
sum_created_tmp_disk_tables: 0
     sum_created_tmp_tables: 0
                 first_seen: 2023-10-16 15:33:37.916920
                  last_seen: 2023-10-16 15:33:37.916920
*************************** 5. row ***************************
                schema_name: apple
                digest_text: CREATE INDEX `k_12` ON `sbtest12` ( `k` )
sum_created_tmp_disk_tables: 0
     sum_created_tmp_tables: 0
                 first_seen: 2023-10-16 15:33:38.578144
                  last_seen: 2023-10-16 15:33:38.578144
5 rows in set (0.03 sec)

ERROR:
No query specified

```

### 哪些SQL返回的结果集最多？

```sql

mysql> select
  schema_name,
  digest_text,
  count_star,
  sum_rows_sent,
  first_seen,
  last_seen
from performance_schema.events_statements_summary_by_digest
where schema_name is not null and schema_name != 'information_schema'
order by sum_rows_sent desc
limit 5\G;


*************************** 1. row ***************************
  schema_name: apple
  digest_text: SELECT `LAST_INSERT_ID` ( )
   count_star: 59773
sum_rows_sent: 59773
   first_seen: 2023-10-16 15:06:35.709255
    last_seen: 2023-10-16 15:41:26.672219
*************************** 2. row ***************************
  schema_name: apple
  digest_text: SELECT `object_schema` , `object_name` , `index_name` FROM `performance_schema` . `table_io_waits_summary_by_index_usage` WHERE `index_name` IS NOT NULL AND `count_star` = ? AND `object_schema` != ? ORDER BY `object_schema` , `object_name`
   count_star: 3
sum_rows_sent: 276
   first_seen: 2023-10-17 09:03:34.105659
    last_seen: 2023-10-17 09:21:22.482287
*************************** 3. row ***************************
  schema_name: apple
  digest_text: SELECT ( `cat` . `name` COLLATE `utf8_tolower_ci` ) AS `TABLE_CATALOG` , ( `sch` . `name` COLLATE `utf8_tolower_ci` ) AS `TABLE_SCHEMA` , ( `tbl` . `name` COLLATE `utf8_tolower_ci` ) AS `TABLE_NAME` , `tbl` . `type` AS `TABLE_TYPE` , IF ( ( `tbl` . `type` = ? ) , `tbl` . `engine` , ? ) AS `ENGINE` , IF ( ( `tbl` . `type` = ? ) , ?, ... ) AS `VERSION` , `tbl` . `row_format` AS `ROW_FORMAT` , IF ( ( `tbl` . `type` = ? ) , ? , `internal_table_rows` ( `sch` . `name` , `tbl` . `name` , IF ( ( `tbl` . `partition_type` IS NULL ) , `tbl` . `engine` , ? ) , `tbl` . `se_private_id` , ( `tbl` . `hidden` != ? ) , `ts` . `se_private_data` , COALESCE ( `stat` . `table_rows` , ? ) , COALESCE ( CAST ( `stat` . `cached_time` AS UNSIGNED ) , ? ) ) ) AS `TABLE_ROWS` , IF ( ( `tbl` . `type` = ? ) , ? , `internal_avg_row_length` ( `sch` . `name` , `tbl` . `name` , IF ( ( `tbl` . `partition_type` IS NULL ) , `tbl` . `engine` , ? ) , `tbl` . `se_private_id` , ( `tbl` . `hidden` != ? ) , `ts` . `se_private_data` , COALESCE ( `stat` .
   count_star: 4
sum_rows_sent: 64
   first_seen: 2023-10-16 15:35:09.750729
    last_seen: 2023-10-17 09:02:11.844992
*************************** 4. row ***************************
  schema_name: apple
  digest_text: SELECT SCHEMA_NAME , `digest_text` , `count_star` , `sys` . `format_time` ( `sum_timer_wait` ) AS `sum_time` , `sys` . `format_time` ( `min_timer_wait` ) AS `min_time` , `sys` . `format_time` ( `avg_timer_wait` ) AS `avg_time` , `sys` . `format_time` ( `max_timer_wait` ) AS `max_time` , `sys` . `format_time` ( `sum_lock_time` ) AS `sum_lock_time` , `sum_rows_affected` , `sum_rows_sent` , `sum_rows_examined` , `first_seen` , `last_seen` FROM `performance_schema` . `events_statements_summary_by_digest` WHERE SCHEMA_NAME IS NOT NULL AND SCHEMA_NAME != ? ORDER BY `avg_timer_wait` DESC , `count_star` DESC LIMIT ?
   count_star: 4
sum_rows_sent: 30
   first_seen: 2023-10-17 09:23:44.833430
    last_seen: 2023-10-17 09:25:10.099692
*************************** 5. row ***************************
  schema_name: apple
  digest_text: SELECT `object_schema` , `object_name` , `index_name` FROM `performance_schema` . `table_io_waits_summary_by_index_usage` WHERE `index_name` IS NOT NULL AND `count_star` = ? AND `object_schema` != ? ORDER BY `object_schema` , `object_name` LIMIT ?
   count_star: 2
sum_rows_sent: 30
   first_seen: 2023-10-17 09:21:38.582372
    last_seen: 2023-10-17 09:21:50.812534
5 rows in set (0.03 sec)

```

















{{< alert >}}This is an alert.{{< /alert >}}
{{< alert title="Note" >}}This is an alert with a title.{{< /alert >}}
{{% alert title="Note" %}}This is an alert with a title and **Markdown**.{{% /alert %}}
{{< alert color="success" >}}This is a successful alert.{{< /alert >}}
{{< alert color="warning" >}}This is a warning.{{< /alert >}}
{{< alert color="warning" title="Warning" >}}This is a warning with a title.{{< /alert >}}


## Another Heading
