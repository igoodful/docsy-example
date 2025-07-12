---
title: 查询存储过程
date: 2023-10-27
weight: 20000
description: >
  查询存储过程


---

{{< alert >}}
查询存储过程
{{< /alert >}}


### 获取存储过程信息


{{< alert color="secondary" >}}

select routine_schema,routine_name,routine_type,created from information_schema.routines order by routine_schema,routine_type,routine_name;

show procedure status where name like '%killsessions%'\G

show procedure status where db = 'apple'\G

use apple;show create procedure procedure_name\G

{{< /alert >}}
```sql
mysql> select routine_schema,routine_name,routine_type,created
from information_schema.routines
order by routine_schema,routine_type,routine_name;
+--------------------+-------------------------------------+--------------+---------------------+
| ROUTINE_SCHEMA     | ROUTINE_NAME                        | ROUTINE_TYPE | CREATED             |
+--------------------+-------------------------------------+--------------+---------------------+
| apple              | killsessions                        | PROCEDURE    | 2023-10-27 10:17:34 |
| oracle_sequence_db | nextval                             | PROCEDURE    | 2023-10-24 10:29:02 |
| sys                | extract_schema_from_file_name       | FUNCTION     | 2023-10-24 10:28:17 |
| sys                | extract_table_from_file_name        | FUNCTION     | 2023-10-24 10:28:17 |
| sys                | format_bytes                        | FUNCTION     | 2023-10-24 10:28:17 |
| sys                | format_path                         | FUNCTION     | 2023-10-24 10:28:17 |
| sys                | format_statement                    | FUNCTION     | 2023-10-24 10:28:17 |
| sys                | format_time                         | FUNCTION     | 2023-10-24 10:28:17 |
| sys                | list_add                            | FUNCTION     | 2023-10-24 10:28:17 |
| sys                | list_drop                           | FUNCTION     | 2023-10-24 10:28:17 |
| sys                | ps_is_account_enabled               | FUNCTION     | 2023-10-24 10:28:17 |
| sys                | ps_is_consumer_enabled              | FUNCTION     | 2023-10-24 10:28:17 |
| sys                | ps_is_instrument_default_enabled    | FUNCTION     | 2023-10-24 10:28:17 |
| sys                | ps_is_instrument_default_timed      | FUNCTION     | 2023-10-24 10:28:17 |
| sys                | ps_is_thread_instrumented           | FUNCTION     | 2023-10-24 10:28:17 |
| sys                | ps_thread_account                   | FUNCTION     | 2023-10-24 10:28:17 |
| sys                | ps_thread_id                        | FUNCTION     | 2023-10-24 10:28:17 |
| sys                | ps_thread_stack                     | FUNCTION     | 2023-10-24 10:28:17 |
| sys                | ps_thread_trx_info                  | FUNCTION     | 2023-10-24 10:28:17 |
| sys                | quote_identifier                    | FUNCTION     | 2023-10-24 10:28:17 |
| sys                | sys_get_config                      | FUNCTION     | 2023-10-24 10:28:17 |
| sys                | version_major                       | FUNCTION     | 2023-10-24 10:28:17 |
| sys                | version_minor                       | FUNCTION     | 2023-10-24 10:28:17 |
| sys                | version_patch                       | FUNCTION     | 2023-10-24 10:28:17 |
| sys                | create_synonym_db                   | PROCEDURE    | 2023-10-24 10:28:18 |
| sys                | diagnostics                         | PROCEDURE    | 2023-10-24 10:28:18 |
| sys                | execute_prepared_stmt               | PROCEDURE    | 2023-10-24 10:28:18 |
| sys                | ps_setup_disable_background_threads | PROCEDURE    | 2023-10-24 10:28:18 |
| sys                | ps_setup_disable_consumer           | PROCEDURE    | 2023-10-24 10:28:18 |
| sys                | ps_setup_disable_instrument         | PROCEDURE    | 2023-10-24 10:28:18 |
| sys                | ps_setup_disable_thread             | PROCEDURE    | 2023-10-24 10:28:18 |
| sys                | ps_setup_enable_background_threads  | PROCEDURE    | 2023-10-24 10:28:18 |
| sys                | ps_setup_enable_consumer            | PROCEDURE    | 2023-10-24 10:28:18 |
| sys                | ps_setup_enable_instrument          | PROCEDURE    | 2023-10-24 10:28:18 |
| sys                | ps_setup_enable_thread              | PROCEDURE    | 2023-10-24 10:28:18 |
| sys                | ps_setup_reload_saved               | PROCEDURE    | 2023-10-24 10:28:18 |
| sys                | ps_setup_reset_to_default           | PROCEDURE    | 2023-10-24 10:28:18 |
| sys                | ps_setup_save                       | PROCEDURE    | 2023-10-24 10:28:18 |
| sys                | ps_setup_show_disabled              | PROCEDURE    | 2023-10-24 10:28:18 |
| sys                | ps_setup_show_disabled_consumers    | PROCEDURE    | 2023-10-24 10:28:18 |
| sys                | ps_setup_show_disabled_instruments  | PROCEDURE    | 2023-10-24 10:28:18 |
| sys                | ps_setup_show_enabled               | PROCEDURE    | 2023-10-24 10:28:18 |
| sys                | ps_setup_show_enabled_consumers     | PROCEDURE    | 2023-10-24 10:28:18 |
| sys                | ps_setup_show_enabled_instruments   | PROCEDURE    | 2023-10-24 10:28:18 |
| sys                | ps_statement_avg_latency_histogram  | PROCEDURE    | 2023-10-24 10:28:18 |
| sys                | ps_trace_statement_digest           | PROCEDURE    | 2023-10-24 10:28:18 |
| sys                | ps_trace_thread                     | PROCEDURE    | 2023-10-24 10:28:18 |
| sys                | ps_truncate_all_tables              | PROCEDURE    | 2023-10-24 10:28:18 |
| sys                | statement_performance_analyzer      | PROCEDURE    | 2023-10-24 10:28:18 |
| sys                | table_exists                        | PROCEDURE    | 2023-10-24 10:28:18 |
+--------------------+-------------------------------------+--------------+---------------------+
50 rows in set (0.01 sec)


mysql> show procedure status where name like '%killsessions%'\G
*************************** 1. row ***************************
                  Db: apple
                Name: killsessions
                Type: PROCEDURE
             Definer: dbscale_internal@%
            Modified: 2023-10-27 10:17:34
             Created: 2023-10-27 10:17:34
       Security_type: DEFINER
             Comment:
character_set_client: utf8mb4
collation_connection: utf8mb4_0900_ai_ci
  Database Collation: utf8mb4_0900_ai_ci
1 row in set (0.00 sec)


mysql> show procedure status where db = 'apple'\G
*************************** 1. row ***************************
                  Db: apple
                Name: killsessions
                Type: PROCEDURE
             Definer: dbscale_internal@%
            Modified: 2023-10-27 10:17:34
             Created: 2023-10-27 10:17:34
       Security_type: DEFINER
             Comment:
character_set_client: utf8mb4
collation_connection: utf8mb4_0900_ai_ci
  Database Collation: utf8mb4_0900_ai_ci
1 row in set (0.00 sec)

mysql> use apple
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show create procedure killsessions\G
*************************** 1. row ***************************
           Procedure: killsessions
            sql_mode: ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION
    Create Procedure: CREATE DEFINER=`dbscale_internal`@`%` PROCEDURE `killsessions`()
begin
    declare done int default 0;
    declare current_id int;
    declare current_users varchar(50);
    declare current_host varchar(50);
declare current_db varchar(50);
declare current_command varchar(255);
declare execute_time int;
    declare cur cursor for select id,user,host,db,command,time from information_schema.processlist where id != connection_id() and user not in ('event_scheduler','system user') and command not in('Binlog Dump GTID');
    declare continue handler for not found set done = 1;
select * from information_schema.processlist where id != connection_id() and user not in ('event_scheduler','system user') and command not in('Binlog Dump GTID');
    open cur;
    read_loop: loop
        fetch cur into current_id, current_users, current_host,current_db,current_command,execute_time;
        if done then

            leave read_loop;
        end if;
        kill current_id;
    end loop;

    close cur;
end
character_set_client: utf8mb4
collation_connection: utf8mb4_0900_ai_ci
  Database Collation: utf8mb4_0900_ai_ci
1 row in set (0.00 sec)

```



