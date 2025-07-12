---
title: metadata_locks
description: metadata_locks
date: 2024-10-09
weight: 20000
---



{{< alert >}}

1. 及时提交事务

2. 同时尽量避免大事务

3. 元数据锁等待超时时长：lock_wait_timeout


{{< /alert >}}
## 慢日志案例如下：

{{< alert color="secondary" >}}

{{< /alert >}}


## lock_wait_timeout
```sql
[dbscale_internal@172.17.137.95:16310 09:28:17((none))]$ show global variables like '%lock_wait_timeout%';
+--------------------------+-------+
| Variable_name            | Value |
+--------------------------+-------+
| innodb_lock_wait_timeout | 20    |
| lock_wait_timeout        | 5     |
+--------------------------+-------+
2 rows in set (0.00 sec)

[dbscale_internal@172.17.137.95:16310 09:28:31((none))]$

```


















### 查询当前阻塞的事务和锁
```sql
select
  tmp.*,
  c.event_name,
  c.sql_text,
  p.user,
  p.host,
  p.db,
  t.thread_id as backup_thread_id,
  t.name,
  t.thread_os_id as thread_os_id
from
  (
    select
      r.trx_id request_trx_id,
      r.trx_state request_trx_state,
      r.trx_mysql_thread_id request_thread_id,
      r.trx_query request_trx_query,
      b.trx_id block_trx_id,
      b.trx_state block_trx_state,
      b.trx_mysql_thread_id block_thread_id,
      b.trx_query block_trx_query
    from
      performance_schema.data_lock_waits w
      join information_schema.innodb_trx r on w.requesting_engine_transaction_id = r.trx_id
      join information_schema.innodb_trx b on w.blocking_engine_transaction_id = b.trx_id
  ) tmp,
  information_schema.processlist p,
  performance_schema.threads t,
  performance_schema.events_statements_current c
where
  tmp.block_thread_id = p.id
  and t.processlist_id = p.id
  and t.thread_id = c.thread_id;
```






### 详细版查询当前阻塞的事务和锁
```sql
select
  tmp.*,
  p.user,
  p.host,
  p.db,
  t.thread_os_id,
  ec.event_name,
  ec.sql_text,
  dl.lock_type,
  dl.lock_mode,
  dl.lock_status,
  dl.lock_data
from
  (
    select
      r.trx_id request_trx_id,
      r.trx_state request_trx_state,
      r.trx_mysql_thread_id request_connect_id,
      r.trx_query request_trx_query,
      b.trx_id block_trx_id,
      b.trx_state block_trx_state,
      b.trx_mysql_thread_id block_connect_id,
      b.trx_query block_trx_query,
	  w.blocking_engine_lock_id block_engine_lock_id
    from
      performance_schema.data_lock_waits w
      join information_schema.innodb_trx r on w.requesting_engine_transaction_id = r.trx_id
      join information_schema.innodb_trx b on w.blocking_engine_transaction_id = b.trx_id
  )
  tmp
  join information_schema.processlist p
  on tmp.block_connect_id=p.id
  join performance_schema.threads t
  on p.id=t.processlist_id
  join performance_schema.events_statements_current ec
  on t.thread_id=ec.thread_id
  join performance_schema.data_locks dl
  on tmp.block_engine_lock_id=dl.engine_lock_id;
```






### 查询阻塞的事务执行过的语句
```sql
select
  date_sub(now(), interval (select variable_value from performance_schema.global_status where variable_name='uptime') - esh.timer_start*10e-13 second) as 'start_time',
  date_sub(now(), interval (select variable_value from performance_schema.global_status where variable_name='uptime') - esh.timer_end*10e-13 second) as 'end_time',
  ps.id 'process id',
  th.thread_id,
  ps.user,
  ps.host,
  esh.event_id,
  trx.trx_started,
  esh.event_name 'event name',
  esh.sql_text 'sql',
  ps.time
from
  performance_schema.events_statements_history esh
  join performance_schema.threads th on esh.thread_id = th.thread_id
  join information_schema.processlist ps on ps.id = th.processlist_id
  left join information_schema.innodb_trx trx on trx.trx_mysql_thread_id = ps.id
where
  trx.trx_id is not null
  and ps.user != 'system_user'
  and trx.trx_mysql_thread_id=18
order by
  esh.event_id;
```



### 查看那些事务持有元数据锁
### 查看占用元数据锁的后台线程ID
```sql
SELECT
  *
FROM
  performance_schema.metadata_locks
WHERE
  object_schema NOT IN('performance_schema')
  AND object_schema IS not NULL
  AND object_name IS NOT null
  AND lock_status = 'GRANTED';
```


### 查看后台线程当前执行的sql
```sql
SELECT
  *
FROM
  performance_schema.events_statements_current
WHERE
  thread_id = owner_thread_id;
```


### 查看后台线程执行过的的sql
```sql
SELECT
  *
from
  performance_schema.events_statements_history
WHERE
  thread_id = owner_thread_id;
```


### 查看阻塞元数据获取的session id
```sql
SELECT
  thread_id,
  processlist_id,
  thread_os_id
FROM
  performance_schema.threads
where
  thread_id = owner_thread_id;
```

### 查看阻塞元数据获取的事务id
```sql
select
  *
from
  information_schema.innodb_trx isi
where
  isi.trx_mysql_thread_id = processlist_id;
```




