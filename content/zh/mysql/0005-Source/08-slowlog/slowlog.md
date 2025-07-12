---
title: slowlog
description: slowlog
date: 2023-10-30
weight: 10000


---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}
- [存储过程](http://mysql.taobao.org/monthly/2023/09/01/)

**bug：**
- [min_examined_row_limit](https://bugs.mysql.com/bug.php?id=110804)

**三个时间：**
- Query_time:语句总执行时间
- Lock_time:语句锁消耗时间
- Exec_time:语句实际执行时间（评判标准）

{{< /alert >}}

写入一条slow log的函数调用栈：
```sql
dispatch_command-->
log_slow_statement-->
log_slow_do-->
Query_logger::slow_log_write-->
Log_to_csv_event_handler::log_slow-->
handler::ha_write_row-->
ha_tina::write_row
```



### 解析
```sql
# Time: 2024-01-24T10:20:58.627594+08:00
# User@Host: root[root] @  [172.17.137.12]  Id:    10
# Query_time: 58.709935  Lock_time: 0.001690 Rows_sent: 0  Rows_examined: 99999
SET timestamp=1706062799;
update sbtest3 set pad='pad' where id < 100000;
# Time: 2024-01-24T10:22:18.717162+08:00
# User@Host: root[root] @  [172.17.137.12]  Id:     8
# Query_time: 61.220157  Lock_time: 16.520821 Rows_sent: 0  Rows_examined: 99999
SET timestamp=1706062877;
delete from  sbtest3  where id < 100000;
```
> `delete from  sbtest3  where id < 100000;`这句sql语句从服务器接受后开始执行到该sql执行完毕花的总时间为61.220157秒，其中锁等待花了16.520821秒，也就是说如果没有锁等待的话，只需花费(61.220157 - 16.520821)秒就能执行完毕
>
> 实际消耗时间(Query_time) = 实际执行时间(Query_time-Lock_time)+锁等待消耗时间(Lock_time)

### 模拟
| session1                                        | session2                                 |
|:------------------------------------------------|:-----------------------------------------|
| begin;                                          |                                          |
| update sbtest3 set pad='pad' where id < 100000; |                                          |
|                                                 | begin;                                   |
|                                                 | delete from  sbtest3  where id < 100000; |
| 等待15秒左右                                    | 等待15秒左右                             |
| commit;                                         |                                          |
|                                                 | commit;                                  |



### slow_query_log
> slow log总开关，全局变量



### log_output
> 记录日志的方式，取值范围TABLE或FILE
- 如果是TABLE，slow log记录在系统表 mysql.slow_log中
- 如果是FILE记录在文件中，文件路径由参数slow_query_log_file指定

### slow_query_log_file
> FILE方式记录slow log时保存文件的路径


### `long_query_time`
> 记录slow log的时间阈值，单位是秒，最小0， 最大 31536000 (365天)

- `long_query_time`与实际执行时间(`Query_time - Lock_time`)相比进行判断来确定是否记录到慢日志中
- 因此`Lock_time`很长但实际执行时间(`Query_time - Lock_time`)小于`long_query_time`的`SQL`是不会记录到慢日志中
- 能够记录到慢日志中的SQL都是实际执行时间(`Query_time - Lock_time`)大于`long_query_time`（除`log_queries_not_using_indexes`影响外）
- 慢日志中的实际执行时间并没有直接给出，需要计算：`Query_time - Lock_time`
- 那么`long_query_time`实际上界定的是实际执行时间，所以有些情况下虽然语句实际消耗的时间很长但是是因为锁等待时间较长而引起的，那么实际上这种语句也不会记录到慢查询。
- 评判是否是慢查询标准其实不是`Query_time`，而且`Query_time - Lock_time`的差值，这个差值与`long_query_time`进行对比。所以有时候数据库有些查询慢是锁等待造成的，而这种情况是不会记录在slow log里的。

```sql
mysql> set global long_query_time=10;
Query OK, 0 rows affected (0.00 sec)

mysql> show global variables like '%long_query_time%';
+-----------------+-----------+
| Variable_name   | Value     |
+-----------------+-----------+
| long_query_time | 10.000000 |
+-----------------+-----------+

```



### `log_queries_not_using_indexes`
> 如果语句未使用索引记录慢查询




### `log_slow_admin_statements`
> 是否记录管理语句。(如ALTER TABLE,ANALYZE TABLE, CHECK TABLE, CREATE INDEX, DROP INDEX, OPTIMIZE TABLE, and REPAIR TABLE.)
>
> 默认是OFF


### `min_examined_row_limit`
> 慢查询检查的行数阈值，记录慢日志的SQL的最小扫描行数，小于该值的SQL不会记录到慢日志
>
> 这是一个全局和session级变量：Global, Session

| 参数                                 | 值                         |
|:-------------------------------------|:---------------------------|
| Command-Line Format                  | --min-examined-row-limit=# |
| System Variable                      | min_examined_row_limit     |
| Scope                                | Global, Session            |
| Dynamic                              | Yes                        |
| SET_VAR Hint Applies                 | No                         |
| Type                                 | Integer                    |
| Default Value                        | 0                          |
| Minimum Value                        | 0                          |
| Maximum Value     (64-bit platforms) | 18446744073709551615       |
| Maximum Value (32-bit platforms)     | 4294967295                 |



### `log_throttle_queries_not_using_indexes`
> 不走索引被记录的语句条数阈值


```sql
mysql> select * from performance_schema.global_variables where variable_name in('slow_query_log','log_output','slow_query_log_file','long_query_time','log_queries_not_using_indexes','log_slow_admin_statements','min_examined_row_limit','log_throttle_queries_not_using_indexes') order by variable_name;
+----------------------------------------+------------------------------------------------------------------------------+
| VARIABLE_NAME                          | VARIABLE_VALUE                                                               |
+----------------------------------------+------------------------------------------------------------------------------+
| log_output                             | FILE                                                                         |
| log_queries_not_using_indexes          | OFF                                                                          |
| log_slow_admin_statements              | OFF                                                                          |
| log_throttle_queries_not_using_indexes | 0                                                                            |
| long_query_time                        | 1.000000                                                                     |
| min_examined_row_limit                 | 0                                                                            |
| slow_query_log                         | ON                                                                           |
| slow_query_log_file                    | /data/mysqldata/16315fff-1f3f-46e9-992b-0f597c9b634b/logfile/mysqld-slow.log |
+----------------------------------------+------------------------------------------------------------------------------+
8 rows in set (0.00 sec)


```

## 源码解读

> log.cc
```c++
void log_slow_statement(THD *thd,
                        struct System_status_var *query_start_status) {
  if (log_slow_applicable(thd)) log_slow_do(thd, query_start_status);
}

// 用来判断某个语句是否是慢日志
bool log_slow_applicable(THD *thd) {
  DBUG_TRACE;

  /*
    The following should never be true with our current code base,
    but better to keep this here so we don't accidently try to log a
    statement in a trigger or stored function
  */
  if (unlikely(thd->in_sub_stmt)) return false;  // Don't set time for sub stmt

  if (unlikely(thd->killed == THD::KILL_CONNECTION)) return false;

  /*
    Do not log administrative statements unless the appropriate option is
    set.
  */
  if (thd->enable_slow_log && opt_slow_log) {
    bool warn_no_index =
        ((thd->server_status &
          (SERVER_QUERY_NO_INDEX_USED | SERVER_QUERY_NO_GOOD_INDEX_USED)) &&
         opt_log_queries_not_using_indexes &&
         !(sql_command_flags[thd->lex->sql_command] & CF_STATUS_COMMAND));
    bool log_this_query =
        ((thd->server_status & SERVER_QUERY_WAS_SLOW) || warn_no_index) &&
        (thd->get_examined_row_count() >=
         thd->variables.min_examined_row_limit);
    bool suppress_logging = log_throttle_qni.log(thd, warn_no_index);

    if (!suppress_logging && log_this_query) return true;
  }
  return false;
}

```
> 判断某个语句是否是慢日志：`log_slow_statement` -> `log_slow_applicable`
>
> `log_slow_statement` -> `log_slow_do`
- suppress_logging：判断是否需要抑制记录。为了防止在短时间内记录过多的慢查询，以避免对性能产生影响。


**opt_slow_log：**

这是一个全局变量，即全局动态参数变量`slow_query_log`的值，该参数没有`session`级别的，只有全局的。所以，我个人认为`thd->enable_slow_log && opt_slow_log`改为`opt_slow_log`也没问题


**opt_log_queries_not_using_indexes：**

这是一个全局变量，即全局动态参数变量`log_queries_not_using_indexes`，该参数没有`session`级别的，只有全局的。

|参数	|值|
|:--	|:--|
|Command-Line Format	|--log-queries-not-using-indexes[={OFF|ON}]|
|System Variable	|log_queries_not_using_indexes|
|Scope	|Global|
|Dynamic	|Yes|
|SET_VAR Hint Applies	|No|
|Type	|Boolean|
|Default Value	|OFF|


**thd->in_sub_stmt：**
- thd->in_sub_stmt 是 MySQL 中 THD 结构体的一个成员，表示当前执行的 SQL 语句是否处于触发器或存储过程的内部语句中。in_sub_stmt 的含义是在子语句中。可能的取值有：
  - true（非零值）：表示当前 SQL 语句处于触发器或存储过程的内部语句中。在触发器或存储过程的内部语句执行时，in_sub_stmt 可能被设置为 true，以避免记录这些内部语句为慢查询。
  - false（零值）：表示当前 SQL 语句不处于触发器或存储过程的内部语句中。在正常的查询语句中，in_sub_stmt 应该是 false。

这个成员的目的是在执行触发器或存储过程时，避免记录这些内部语句为慢查询。

这样的设计是为了确保慢查询日志中只记录用户提交的顶层 SQL 语句，而不记录触发器或存储过程内部执行的子语句。

**thd->killed == THD::KILL_CONNECTION：**

- thd->killed == THD::KILL_CONNECTION 是 MySQL 中的 THD 结构体的一个成员 killed 的检查，表示当前连接是否被标记为即将关闭。具体含义是：
  - true（非零值）：表示当前连接已被标记为即将关闭。
  - false（零值）：表示当前连接未被标记为即将关闭。

这个检查的目的是在执行慢查询判断时，如果连接即将关闭，就不再记录慢查询。这样可以避免在关闭连接的过程中记录慢查询，因为关闭连接时通常已经无需再关心慢查询的记录。

这种情况通常发生在以下情形之一：
- 主动关闭连接：应用程序或用户主动关闭了连接。
- 被动关闭连接：MySQL 服务器因某些原因（例如超时或异常）决定关闭连接。

总的来说，检查 thd->killed == THD::KILL_CONNECTION 的目的是确保不在即将关闭的连接上记录慢查询，以避免在连接关闭的过程中产生额外的开销。




```c++
void log_slow_do(THD *thd, struct System_status_var *query_start_status) {
  THD_STAGE_INFO(thd, stage_logging_slow_query);
  thd->status_var.long_query_count++;

  if (thd->rewritten_query().length())
    query_logger.slow_log_write(thd, thd->rewritten_query().ptr(),
                                thd->rewritten_query().length(),
                                query_start_status);
  else
    query_logger.slow_log_write(thd, thd->query().str, thd->query().length,
                                query_start_status);
}
```
> `thd->status_var.long_query_count++;`  增加慢查询的计数器，表示执行了一次慢查询。


```c++

bool Query_logger::slow_log_write(
    THD *thd, const char *query, size_t query_length,
    struct System_status_var *query_start_status) {
  assert(thd->enable_slow_log && opt_slow_log);

  if (!(*slow_log_handler_list)) return false;

  /* do not log slow queries from replication threads */
  if (thd->slave_thread && !opt_log_slow_replica_statements) return false;

  /* fill in user_host value: the format is "%s[%s] @ %s [%s]" */
  char user_host_buff[MAX_USER_HOST_SIZE + 1];
  Security_context *sctx = thd->security_context();
  LEX_CSTRING sctx_user = sctx->user();
  LEX_CSTRING sctx_host = sctx->host();
  LEX_CSTRING sctx_ip = sctx->ip();
  size_t user_host_len =
      (strxnmov(user_host_buff, MAX_USER_HOST_SIZE, sctx->priv_user().str, "[",
                sctx_user.length ? sctx_user.str : "", "] @ ",
                sctx_host.length ? sctx_host.str : "", " [",
                sctx_ip.length ? sctx_ip.str : "", "]", NullS) -
       user_host_buff);
  ulonglong current_utime = my_micro_time();
  ulonglong query_utime, lock_utime;
  if (thd->start_utime) {
    query_utime = (current_utime - thd->start_utime);
    lock_utime = (thd->utime_after_lock - thd->start_utime);
  } else {
    query_utime = 0;
    lock_utime = 0;
  }

  bool is_command = false;
  if (!query) {
    is_command = true;
    const std::string &cn = Command_names::str_global(thd->get_command());
    query = cn.c_str();
    query_length = cn.length();
  }

  mysql_rwlock_rdlock(&LOCK_logger);

  bool error = false;
  for (Log_event_handler **current_handler = slow_log_handler_list;
       *current_handler;) {
    error |=
        (*current_handler++)
            ->log_slow(
                thd, current_utime,
                (thd->start_time.tv_sec * 1000000ULL) + thd->start_time.tv_usec,
                user_host_buff, user_host_len, query_utime, lock_utime,
                is_command, query, query_length, query_start_status);
  }

  mysql_rwlock_unlock(&LOCK_logger);

  return error;
}
```



```c++
bool Log_to_file_event_handler::log_slow(
    THD *thd, ulonglong current_utime, ulonglong query_start_utime,
    const char *user_host, size_t user_host_len, ulonglong query_utime,
    ulonglong lock_utime, bool is_command, const char *sql_text,
    size_t sql_text_len, struct System_status_var *query_start_status) {
  if (!mysql_slow_log.is_open()) return false;

  Silence_log_table_errors error_handler;
  thd->push_internal_handler(&error_handler);
  bool retval = mysql_slow_log.write_slow(thd, current_utime, query_start_utime,
                                          user_host, user_host_len, query_utime,
                                          lock_utime, is_command, sql_text,
                                          sql_text_len, query_start_status);
  thd->pop_internal_handler();
  return retval;
}
```


```c++
bool File_query_log::write_slow(THD *thd, ulonglong current_utime,
                                ulonglong query_start_utime,
                                const char *user_host, size_t,
                                ulonglong query_utime, ulonglong lock_utime,
                                bool is_command, const char *sql_text,
                                size_t sql_text_len,
                                struct System_status_var *query_start) {
  char buff[80], *end;
  char query_time_buff[22 + 7], lock_time_buff[22 + 7];
  size_t buff_len;
  end = buff;

  mysql_mutex_lock(&LOCK_log);
  assert(is_open());

  if (!(specialflag & SPECIAL_SHORT_LOG_FORMAT)) {
    char my_timestamp[iso8601_size];

    make_iso8601_timestamp(my_timestamp, current_utime,
                           iso8601_sysvar_logtimestamps);

    buff_len = snprintf(buff, sizeof buff, "# Time: %s\n", my_timestamp);

    /* Note that my_b_write() assumes it knows the length for this */
    if (my_b_write(&log_file, (uchar *)buff, buff_len)) goto err;

    buff_len = snprintf(buff, 32, "%5u", thd->thread_id());
    if (my_b_printf(&log_file, "# User@Host: %s  Id: %s\n", user_host, buff) ==
        (uint)-1)
      goto err;
  }

  /* For slow query log */
  sprintf(query_time_buff, "%.6f", ulonglong2double(query_utime) / 1000000.0);
  sprintf(lock_time_buff, "%.6f", ulonglong2double(lock_utime) / 1000000.0);

  /*
    As a general rule, if opt_log_slow_extra is set, the caller will
    have saved state at the beginning of execution, and passed in a
    pointer to that state in query_start. As there are exceptions to
    this rule however (e.g. in store routines), and for robustness,
    we test for the presence of the actual information rather than the
    the flag. If the "before" state is not available for whatever
    reason, we emit a traditional "short" line; if the information is
    available, we generate the now, "long" line (with "extra" information).
  */
  if (!query_start) {
    if (my_b_printf(&log_file,
                    "# Query_time: %s  Lock_time: %s"
                    " Rows_sent: %lu  Rows_examined: %lu\n",
                    query_time_buff, lock_time_buff,
                    (ulong)thd->get_sent_row_count(),
                    (ulong)thd->get_examined_row_count()) == (uint)-1)
      goto err; /* purecov: inspected */
  } else {
    char start_time_buff[iso8601_size];
    char end_time_buff[iso8601_size];

    if (query_start_utime) {
      make_iso8601_timestamp(start_time_buff, query_start_utime,
                             iso8601_sysvar_logtimestamps);
      make_iso8601_timestamp(end_time_buff, query_start_utime + query_utime,
                             iso8601_sysvar_logtimestamps);
    } else {
      start_time_buff[0] = '\0'; /* purecov: inspected */
      make_iso8601_timestamp(
          end_time_buff, current_utime,
          iso8601_sysvar_logtimestamps); /* purecov: inspected */
    }

    if (my_b_printf(
            &log_file,
            "# Query_time: %s  Lock_time: %s"
            " Rows_sent: %lu  Rows_examined: %lu"
            " Thread_id: %lu Errno: %lu Killed: %lu"
            " Bytes_received: %lu Bytes_sent: %lu"
            " Read_first: %lu Read_last: %lu Read_key: %lu"
            " Read_next: %lu Read_prev: %lu"
            " Read_rnd: %lu Read_rnd_next: %lu"
            " Sort_merge_passes: %lu Sort_range_count: %lu"
            " Sort_rows: %lu Sort_scan_count: %lu"
            " Created_tmp_disk_tables: %lu"
            " Created_tmp_tables: %lu"
            " Start: %s End: %s\n",
            query_time_buff, lock_time_buff, (ulong)thd->get_sent_row_count(),
            (ulong)thd->get_examined_row_count(), (ulong)thd->thread_id(),
            static_cast<ulong>(
                thd->is_error() ? thd->get_stmt_da()->mysql_errno() : 0),
            (ulong)thd->killed,
            (ulong)(thd->status_var.bytes_received -
                    query_start->bytes_received),
            (ulong)(thd->status_var.bytes_sent - query_start->bytes_sent),
            (ulong)(thd->status_var.ha_read_first_count -
                    query_start->ha_read_first_count),
            (ulong)(thd->status_var.ha_read_last_count -
                    query_start->ha_read_last_count),
            (ulong)(thd->status_var.ha_read_key_count -
                    query_start->ha_read_key_count),
            (ulong)(thd->status_var.ha_read_next_count -
                    query_start->ha_read_next_count),
            (ulong)(thd->status_var.ha_read_prev_count -
                    query_start->ha_read_prev_count),
            (ulong)(thd->status_var.ha_read_rnd_count -
                    query_start->ha_read_rnd_count),
            (ulong)(thd->status_var.ha_read_rnd_next_count -
                    query_start->ha_read_rnd_next_count),
            (ulong)(thd->status_var.filesort_merge_passes -
                    query_start->filesort_merge_passes),
            (ulong)(thd->status_var.filesort_range_count -
                    query_start->filesort_range_count),
            (ulong)(thd->status_var.filesort_rows - query_start->filesort_rows),
            (ulong)(thd->status_var.filesort_scan_count -
                    query_start->filesort_scan_count),
            (ulong)(thd->status_var.created_tmp_disk_tables -
                    query_start->created_tmp_disk_tables),
            (ulong)(thd->status_var.created_tmp_tables -
                    query_start->created_tmp_tables),
            start_time_buff, end_time_buff) == (uint)-1)
      goto err; /* purecov: inspected */
  }

  if (thd->db().str && strcmp(thd->db().str, db)) {  // Database changed
    if (my_b_printf(&log_file, "use %s;\n", thd->db().str) == (uint)-1)
      goto err;
    my_stpcpy(db, thd->db().str);
  }
  if (thd->stmt_depends_on_first_successful_insert_id_in_prev_stmt) {
    end = my_stpcpy(end, ",last_insert_id=");
    end = longlong10_to_str(
        (longlong)thd->first_successful_insert_id_in_prev_stmt_for_binlog, end,
        -10);
  }
  // Save value if we do an insert.
  if (thd->auto_inc_intervals_in_cur_stmt_for_binlog.nb_elements() > 0) {
    if (!(specialflag & SPECIAL_SHORT_LOG_FORMAT)) {
      end = my_stpcpy(end, ",insert_id=");
      end = longlong10_to_str(
          (longlong)thd->auto_inc_intervals_in_cur_stmt_for_binlog.minimum(),
          end, -10);
    }
  }

  /*
    The timestamp used to only be set when the query had checked the
    start time. Now the slow log always logs the query start time.
    This ensures logs can be used to replicate queries accurately.
  */
  end = my_stpcpy(end, ",timestamp=");
  end = longlong10_to_str(query_start_utime / 1000000, end, 10);

  if (end != buff) {
    *end++ = ';';
    *end = '\n';
    if (my_b_write(&log_file, pointer_cast<const uchar *>("SET "), 4) ||
        my_b_write(&log_file, (uchar *)buff + 1, (uint)(end - buff)))
      goto err;
  }
  if (is_command) {
    end = strxmov(buff, "# administrator command: ", NullS);
    buff_len = (ulong)(end - buff);
    DBUG_EXECUTE_IF("simulate_slow_log_write_error",
                    { DBUG_SET("+d,simulate_file_write_error"); });
    if (my_b_write(&log_file, (uchar *)buff, buff_len)) goto err;
  }
  if (my_b_write(&log_file, pointer_cast<const uchar *>(sql_text),
                 sql_text_len) ||
      my_b_write(&log_file, pointer_cast<const uchar *>(";\n"), 2) ||
      flush_io_cache(&log_file))
    goto err;

  mysql_mutex_unlock(&LOCK_log);
  return false;

err:
  check_and_print_write_error();
  mysql_mutex_unlock(&LOCK_log);
  return true;
}
```

### thd->server_status
```c++
/** The status flags are a bit-field */
enum SERVER_STATUS_flags_enum {
  /**
    Is raised when a multi-statement transaction
    has been started, either explicitly, by means
    of BEGIN or COMMIT AND CHAIN, or
    implicitly, by the first transactional
    statement, when autocommit=off.
  */
  SERVER_STATUS_IN_TRANS = 1,
  SERVER_STATUS_AUTOCOMMIT = 2,   /**< Server in auto_commit mode */
  SERVER_MORE_RESULTS_EXISTS = 8, /**< Multi query - next query exists */
  SERVER_QUERY_NO_GOOD_INDEX_USED = 16,
  SERVER_QUERY_NO_INDEX_USED = 32,
  /**
    The server was able to fulfill the clients request and opened a
    read-only non-scrollable cursor for a query. This flag comes
    in reply to COM_STMT_EXECUTE and COM_STMT_FETCH commands.
    Used by Binary Protocol Resultset to signal that COM_STMT_FETCH
    must be used to fetch the row-data.
    @todo Refify "Binary Protocol Resultset" and "COM_STMT_FETCH".
  */
  SERVER_STATUS_CURSOR_EXISTS = 64,
  /**
    This flag is sent when a read-only cursor is exhausted, in reply to
    COM_STMT_FETCH command.
  */
  SERVER_STATUS_LAST_ROW_SENT = 128,
  SERVER_STATUS_DB_DROPPED = 256, /**< A database was dropped */
  SERVER_STATUS_NO_BACKSLASH_ESCAPES = 512,
  /**
    Sent to the client if after a prepared statement reprepare
    we discovered that the new statement returns a different
    number of result set columns.
  */
  SERVER_STATUS_METADATA_CHANGED = 1024,
  SERVER_QUERY_WAS_SLOW = 2048,
  /**
    To mark ResultSet containing output parameter values.
  */
  SERVER_PS_OUT_PARAMS = 4096,

  /**
    Set at the same time as SERVER_STATUS_IN_TRANS if the started
    multi-statement transaction is a read-only transaction. Cleared
    when the transaction commits or aborts. Since this flag is sent
    to clients in OK and EOF packets, the flag indicates the
    transaction status at the end of command execution.
  */
  SERVER_STATUS_IN_TRANS_READONLY = 8192,

  /**
    This status flag, when on, implies that one of the state information has
    changed on the server because of the execution of the last statement.
  */
  SERVER_SESSION_STATE_CHANGED = (1UL << 14)
};
```






### rollback
```sql
# Time: 2024-01-24T15:54:20.340471+08:00
# User@Host: root[root] @  [172.17.137.12]  Id:     8
# Query_time: 60.809809  Lock_time: 0.003361 Rows_sent: 0  Rows_examined: 99999
SET timestamp=1706082799;
delete from  sbtest7  where id < 100000;
# Time: 2024-01-24T15:56:18.027609+08:00
# User@Host: root[root] @  [172.17.137.12]  Id:     8
# Query_time: 97.030073  Lock_time: 0.000000 Rows_sent: 0  Rows_examined: 0
SET timestamp=1706082880;
rollback;

```
- 事务回滚或提交不影响慢SQL语句的记录，rollback或commit语句都可成为慢查询的日志



