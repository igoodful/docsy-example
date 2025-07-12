---
title: log_error_verbosity
description: 将ddl操作日志打印输出到mysql错误日志中
date: 2023-10-25
weight: 20000


---




{{< alert >}}
需要设置如下两个参数，将ddl操作日志打印输出到mysql错误日志中：
- innodb_print_ddl_logs=on，默认为OFF
- log_error_verbosity=3，默认为2
{{< /alert >}}


## 慢日志解析

{{<note>}}
<!---->
log_error_verbosity有三个可选值：
1. 错误信息
2. 错误信息和告警信息，为默认值
3. 错误信息、告警信息和通知信息。即Note类型的信息
{{</note>}}



```sql

mysql> set global innodb_print_ddl_logs=on;
Query OK, 0 rows affected (0.09 sec)

mysql> set global log_error_verbosity=3;
Query OK, 0 rows affected (0.05 sec)


mysql> create table test1(id int);
Query OK, 0 rows affected (0.13 sec)

mysql> drop table test1;
Query OK, 0 rows affected (0.06 sec)
```
error日志：
```none
2023-11-11T12:59:13.860757Z 10 [Note] [MY-012475] [InnoDB] DDL log insert : [DDL record: DROP, id=483, thread_id=10, table_id=1121]
2023-11-11T12:59:13.860899Z 10 [Note] [MY-012473] [InnoDB] DDL log insert : [DDL record: DELETE SPACE, id=484, thread_id=10, space_id=60, old_file_path=./apple/test1.ibd]
2023-11-11T12:59:13.862963Z 10 [Note] [MY-012485] [InnoDB] DDL log post ddl : begin for thread id : 10
2023-11-11T12:59:13.863044Z 10 [Note] [MY-012479] [InnoDB] DDL log replay : [DDL record: DELETE SPACE, id=484, thread_id=10, space_id=60, old_file_path=./apple/test1.ibd]
2023-11-11T12:59:13.864215Z 10 [Note] [MY-012479] [InnoDB] DDL log replay : [DDL record: DROP, id=483, thread_id=10, table_id=1121]
2023-11-11T12:59:13.864568Z 10 [Note] [MY-012486] [InnoDB] DDL log post ddl : end for thread id : 10
```




