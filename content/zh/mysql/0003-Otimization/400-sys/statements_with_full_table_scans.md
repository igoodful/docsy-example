---
title: statements_with_full_table_scans
description: statements_with_full_table_scans
date: 2024-10-09
weight: 20000
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

{{< /alert >}}




```sql
mysql> show create table  statements_with_full_table_scans\G
*************************** 1. row ***************************
                View: statements_with_full_table_scans
         Create View: CREATE ALGORITHM=MERGE DEFINER=`mysql.sys`@`localhost` SQL SECURITY INVOKER VIEW `statements_with_full_table_scans`
         (`query`,`db`,`exec_count`,`total_latency`,`no_index_used_count`,`no_good_index_used_count`,`no_index_used_pct`,`rows_sent`,`rows_examined`,`rows_sent_avg`,`rows_examined_avg`,`first_seen`,`last_seen`,`digest`)
         AS select `sys`.`format_statement`(`performance_schema`.`events_statements_summary_by_digest`.`DIGEST_TEXT`) AS `query`,
         `performance_schema`.`events_statements_summary_by_digest`.`SCHEMA_NAME` AS `db`,
         `performance_schema`.`events_statements_summary_by_digest`.`COUNT_STAR` AS `exec_count`,
         format_pico_time(`performance_schema`.`events_statements_summary_by_digest`.`SUM_TIMER_WAIT`) AS `total_latency`,
         `performance_schema`.`events_statements_summary_by_digest`.`SUM_NO_INDEX_USED` AS `no_index_used_count`,
         `performance_schema`.`events_statements_summary_by_digest`.`SUM_NO_GOOD_INDEX_USED` AS `no_good_index_used_count`,
         round((ifnull((`performance_schema`.`events_statements_summary_by_digest`.`SUM_NO_INDEX_USED` / nullif(`performance_schema`.`events_statements_summary_by_digest`.`COUNT_STAR`,0)),0) * 100),0) AS `no_index_used_pct`,
         `performance_schema`.`events_statements_summary_by_digest`.`SUM_ROWS_SENT` AS `rows_sent`,
         `performance_schema`.`events_statements_summary_by_digest`.`SUM_ROWS_EXAMINED` AS `rows_examined`,
         round((`performance_schema`.`events_statements_summary_by_digest`.`SUM_ROWS_SENT` / `performance_schema`.`events_statements_summary_by_digest`.`COUNT_STAR`),0) AS `rows_sent_avg`,
         round((`performance_schema`.`events_statements_summary_by_digest`.`SUM_ROWS_EXAMINED` / `performance_schema`.`events_statements_summary_by_digest`.`COUNT_STAR`),0) AS `rows_examined_avg`,
         `performance_schema`.`events_statements_summary_by_digest`.`FIRST_SEEN` AS `first_seen`,
         `performance_schema`.`events_statements_summary_by_digest`.`LAST_SEEN` AS `last_seen`,
         `performance_schema`.`events_statements_summary_by_digest`.`DIGEST` AS `digest`
         from `performance_schema`.`events_statements_summary_by_digest`
         where (((`performance_schema`.`events_statements_summary_by_digest`.`SUM_NO_INDEX_USED` > 0)
         or (`performance_schema`.`events_statements_summary_by_digest`.`SUM_NO_GOOD_INDEX_USED` > 0))
         and (not((`performance_schema`.`events_statements_summary_by_digest`.`DIGEST_TEXT` like 'SHOW%'))))
         order by round((ifnull((`performance_schema`.`events_statements_summary_by_digest`.`SUM_NO_INDEX_USED` / nullif(`performance_schema`.`events_statements_summary_by_digest`.`COUNT_STAR`,0)),0) * 100),0) desc,format_pico_time(`performance_schema`.`events_statements_summary_by_digest`.`SUM_TIMER_WAIT`) desc
character_set_client: utf8mb4
collation_connection: utf8mb4_0900_ai_ci
1 row in set (0.01 sec)

```


```sql
mysql> select * from statements_with_full_table_scans limit 2\G
*************************** 1. row ***************************
                   query: SELECT TABLE_NAME , VALUE FROM `dbscale` . `DBSCALE_INC_INFO`
                      db: information_schema
              exec_count: 1
           total_latency: 99.02 ms
     no_index_used_count: 1
no_good_index_used_count: 0
       no_index_used_pct: 100
               rows_sent: 0
           rows_examined: 0
           rows_sent_avg: 0
       rows_examined_avg: 0
              first_seen: 2023-12-21 17:57:26.103264
               last_seen: 2023-12-21 17:57:26.103264
                  digest: 3b448ecf9b4c0f89c88c089289cc55763365a49cc9eaaabba7c37d9b643afb38
*************************** 2. row ***************************
                   query: SELECT `id` FROM `information_ ... ex` ( HOST , ?, ... ) IN (...)
                      db: information_schema
              exec_count: 2
           total_latency: 9.59 ms
     no_index_used_count: 2
no_good_index_used_count: 0
       no_index_used_pct: 100
               rows_sent: 13
           rows_examined: 120
           rows_sent_avg: 7
       rows_examined_avg: 60
              first_seen: 2023-12-21 17:57:26.132224
               last_seen: 2023-12-21 21:36:25.435191
                  digest: d9d2e19bf2bebcc1a506147bccbdcc32b524484dc781a96368d84e886a90ac4d
2 rows in set (0.08 sec)

mysql>

```


