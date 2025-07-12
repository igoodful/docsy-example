---
title: show engine innodb status
description: show engine innodb status
date: 2023-10-25
weight: 20000


---

{{< alert >}}

{{< /alert >}}



```sql
set global innodb_thread_concurrency = 1;
set global innodb_concurrency_tickets = 10;
show engine innodb status\G
```

sysbench开启8个并发
```bash
sysbench
```


```sql
--------------
ROW OPERATIONS
--------------
1 queries inside InnoDB, 7 queries in queue
0 read views open inside InnoDB
Process ID=324005, Main thread ID=140689036084992 , state=sleeping
Number of rows inserted 9458802, updated 0, deleted 500000, read 3504895
74147.62 inserts/s, 0.00 updates/s, 0.00 deletes/s, 0.00 reads/s
Number of system rows inserted 1339, updated 576, deleted 1128, read 27586
10.33 inserts/s, 0.00 updates/s, 0.00 deletes/s, 0.00 reads/s
----------------------------
END OF INNODB MONITOR OUTPUT
============================

1 row in set (0.00 sec)

[root@127.0.0.1:8032 15:27:11((none))]$ show engine innodb status\G


```

- `1 queries inside InnoDB, 7 queries in queue`


```sql
[root@127.0.0.1:8032 15:32:13((none))]$ select * from information_schema.innodb_trx\G
*************************** 1. row ***************************
                    trx_id: 30855
                 trx_state: RUNNING
               trx_started: 2024-05-20 15:32:14
     trx_requested_lock_id: NULL
          trx_wait_started: NULL
                trx_weight: 2465
       trx_mysql_thread_id: 1300
                 trx_query: INSERT INTO sbtest4(k, c, pad) VALUES(3545436, '38757021321-68930864971-50843658148-75747589521-99467082114-28991037768-40560814508-43742704973-12899040237-44960838638', '04571954132-95207893059-06862271222-06643588316-52760796957'),(602743, '13854922690-34746400883-23611663736-38938295115-54892047414-09429489471-23676701970-78965380750-51745120121-56108475955', '41592499628-99669020375-52539937002-14107727996-55842554896'),(458040, '86860709931-29589079174-11650175677-52889401729-95421363663-13087568310-13508570259-18475222842-10575968432-80284460210', '43064746017-99722529641-29028756344-34083514413-20055977857'),(3577439, '05618806195-99337557624-84068015478-28152814843-46300854961-06125422730-25050881315-21947052778-27063851682-92637464246', '24196346939-11730034608-71490534352-11317884397-83310186047'),(3239027, '83985748862-29392213641-48482173278-53467896142-87429239888-71594970155-52489926024-84422896949-61588971682-09544627680', '19424970722-30933709131-00725713920-80796751216-50100071729'),(4343741,
       trx_operation_state: inserting
         trx_tables_in_use: 1
         trx_tables_locked: 1
          trx_lock_structs: 1
     trx_lock_memory_bytes: 1128
           trx_rows_locked: 0
         trx_rows_modified: 2464
   trx_concurrency_tickets: 10
       trx_isolation_level: READ COMMITTED
         trx_unique_checks: 1
    trx_foreign_key_checks: 1
trx_last_foreign_key_error: NULL
 trx_adaptive_hash_latched: 0
 trx_adaptive_hash_timeout: 0
          trx_is_read_only: 0
trx_autocommit_non_locking: 0
       trx_schedule_weight: NULL
*************************** 2. row ***************************
                    trx_id: 30847
                 trx_state: RUNNING
               trx_started: 2024-05-20 15:32:14
     trx_requested_lock_id: NULL
          trx_wait_started: NULL
                trx_weight: 1310
       trx_mysql_thread_id: 1299
                 trx_query: INSERT INTO sbtest5(k, c, pad) VALUES(4868306, '06676266710-95288158214-48329994653-17651660426-48748789384-55799020827-73688091441-45356061796-37324457798-45169165686', '16829727742-33471803042-60256192102-10042546700-73995089227'),(4749647, '54402442139-92437044191-70471553495-26765149505-84517034136-10159076292-21189483054-77379669110-27646464634-26190570501', '55841411358-45626699699-09037242782-84728658535-02995355095'),(2697131, '80805151111-26242729987-30763901089-00815417765-61135011554-92160815442-46021397709-65226245029-82069689246-18535994152', '35493515041-09143016701-07291197509-94656123565-59008549925'),(2337518, '47083785954-47804158428-02977188050-37263993867-93157679486-99574145983-06955286663-88025628932-33838868639-98598463829', '08670542718-20844099208-95078513586-35660050915-35270987337'),(3628826, '47807480908-29041288915-32579270587-54258626270-34098344995-83871885958-79710972008-74742232791-17170121616-30998139609', '91579240418-39279725811-86699830294-18335251633-53232234274'),(163106
       trx_operation_state: sleeping before entering InnoDB
         trx_tables_in_use: 1
         trx_tables_locked: 1
          trx_lock_structs: 1
     trx_lock_memory_bytes: 1128
           trx_rows_locked: 0
         trx_rows_modified: 1309
   trx_concurrency_tickets: 0
       trx_isolation_level: READ COMMITTED
         trx_unique_checks: 1
    trx_foreign_key_checks: 1
trx_last_foreign_key_error: NULL
 trx_adaptive_hash_latched: 0
 trx_adaptive_hash_timeout: 0
          trx_is_read_only: 0
trx_autocommit_non_locking: 0
       trx_schedule_weight: NULL
2 rows in set (0.00 sec)

```












