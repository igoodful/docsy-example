---
title: wait_timeout
description: wait_timeout
date: 2023-10-25
weight: 20000
viz: true
---

{{< alert >}}
> [wait_timeout](https://opensource.actionsky.com/20220712-mysql/)
{{< /alert >}}


## wait_timeout


```viz-dot
digraph "pfs_spawn_thread" {
        rankdir = TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
        ];
        "pfs_spawn_thread" -> "handle_connection";
        "handle_connection" -> "do_command";
        "do_command" -> "my_net_set_read_timeout"[label="设置线程timeout"];
        "do_command" -> "Protocol_classic::get_command";
        "Protocol_classic::get_command" -> "Protocol_classic::read_packet";
        "Protocol_classic::read_packet" -> "my_net_read";
        "my_net_read" -> "net_read_uncompressed_packet" ;
        "net_read_uncompressed_packet" -> "net_read_packet" ;
        "net_read_packet" -> "net_read_packet_header" ;
        "net_read_packet_header" -> "net_read_raw_loop" ;


}
```




## do_command
> `my_net_set_read_timeout(net, thd->variables.net_wait_timeout);`
>
> 上面代码中的 `net_wait_timeout` 就是系统变量 `wait_timeout` 的化身



```c++
bool do_command(THD *thd) {
        bool return_value;
        int rc;
        NET *net                         = nullptr;
        enum enum_server_command command = COM_SLEEP;
        COM_DATA com_data;
        DBUG_TRACE;
        assert(thd->is_classic_protocol());

        /*
          indicator of uninitialized lex => normal flow of errors handling
          (see my_message_sql)
        */
        thd->lex->set_current_query_block(nullptr);

        /*
          XXX: this code is here only to clear possible errors of init_connect.
          Consider moving to prepare_new_connection_state() instead.
          That requires making sure the DA is cleared before non-parsing statements
          such as COM_QUIT.
        */
        thd->clear_error();  // Clear error message
        thd->get_stmt_da()->reset_diagnostics_area();

        /*
          This thread will do a blocking read from the client which
          will be interrupted when the next command is received from
          the client, the connection is closed or "net_wait_timeout"
          number of seconds has passed.
        */
        net = thd->get_protocol_classic()->get_net();
        my_net_set_read_timeout(net, thd->variables.net_wait_timeout);
        net_new_transaction(net);

        /*
          Synchronization point for testing of KILL_CONNECTION.
          This sync point can wait here, to simulate slow code execution
          between the last test of thd->killed and blocking in read().

          The goal of this test is to verify that a connection does not
          hang, if it is killed at this point of execution.
          (Bug#37780 - main.kill fails randomly)

          Note that the sync point wait itself will be terminated by a
          kill. In this case it consumes a condition broadcast, but does
          not change anything else. The consumed broadcast should not
          matter here, because the read/recv() below doesn't use it.
        */
        DEBUG_SYNC(thd, "before_do_command_net_read");

        /* For per-query performance counters with log_slow_statement */
        struct System_status_var query_start_status;
        thd->clear_copy_status_var();
        if (opt_log_slow_extra) {
                thd->copy_status_var(&query_start_status);
        }

        rc = thd->m_mem_cnt.reset();
        if (rc)
                thd->m_mem_cnt.set_thd_error_status();
        else {
                /*
                  Because of networking layer callbacks in place,
                  this call will maintain the following instrumentation:
                  - IDLE events
                  - SOCKET events
                  - STATEMENT events
                  - STAGE events
                  when reading a new network packet.
                  In particular, a new instrumented statement is started.
                  See init_net_server_extension()
                */
                thd->m_server_idle = true;
                rc                 = thd->get_protocol()->get_command(&com_data, &command);
                thd->m_server_idle = false;
        }

        if (rc) {
#ifndef NDEBUG
                char desc[VIO_DESCRIPTION_SIZE];
                vio_description(net->vio, desc);
                DBUG_PRINT("info", ("Got error %d reading command from socket %s", net->error, desc));
#endif  // NDEBUG
                /* Instrument this broken statement as "statement/com/error" */
                thd->m_statement_psi = MYSQL_REFINE_STATEMENT(thd->m_statement_psi, com_statement_info[COM_END].m_key);

                /* Check if we can continue without closing the connection */

                /* The error must be set. */
                assert(thd->is_error());
                thd->send_statement_status();

                /* Mark the statement completed. */
                MYSQL_END_STATEMENT(thd->m_statement_psi, thd->get_stmt_da());
                thd->m_statement_psi = nullptr;
                thd->m_digest        = nullptr;

                if (rc < 0) {
                        return_value = true;  // We have to close it.
                        goto out;
                }
                net->error   = NET_ERROR_UNSET;
                return_value = false;
                goto out;
        }

#ifndef NDEBUG
        char desc[VIO_DESCRIPTION_SIZE];
        vio_description(net->vio, desc);
        DBUG_PRINT("info", ("Command on %s = %d (%s)", desc, command, Command_names::str_notranslate(command).c_str()));
        expected_from_debug_flag = TDM::ANY;
        DBUG_EXECUTE_IF("tdon", { expected_from_debug_flag = TDM::ON; });
        DBUG_EXECUTE_IF("tdzero", { expected_from_debug_flag = TDM::ZERO; });
        DBUG_EXECUTE_IF("tdna", { expected_from_debug_flag = TDM::NOT_AVAILABLE; });
#endif  // NDEBUG
        DBUG_PRINT("info",
                   ("packet: '%*.s'; command: %d",
                    (int)thd->get_protocol_classic()->get_packet_length(),
                    thd->get_protocol_classic()->get_raw_packet(),
                    command));
        if (thd->get_protocol_classic()->bad_packet)
                assert(0);  // Should be caught earlier

        // Reclaim some memory
        thd->get_protocol_classic()->get_output_packet()->shrink(thd->variables.net_buffer_length);
        /* Restore read timeout value */
        my_net_set_read_timeout(net, thd->variables.net_read_timeout);

        DEBUG_SYNC(thd, "before_command_dispatch");

        return_value = dispatch_command(thd, &com_data, command);
        thd->get_protocol_classic()->get_output_packet()->shrink(thd->variables.net_buffer_length);

out:
        /* The statement instrumentation must be closed in all cases. */
        assert(thd->m_digest == nullptr);
        assert(thd->m_statement_psi == nullptr);
        return return_value;
}
```


设置最长等待时间之后，接下来就是安静的等待了，执行等待操作的方法是 vio_io_wait()
## my_net_set_read_timeout

```c++
void my_net_set_read_timeout(NET *net, uint timeout) {
        DBUG_TRACE;
        DBUG_PRINT("enter", ("timeout: %d", timeout));
        net->read_timeout = timeout;
        if (net->vio)
                vio_timeout(net->vio, 0, timeout);
}



int vio_timeout(Vio *vio, uint which, int timeout_sec) {
        int timeout_ms;
        bool old_mode;

        /*
          Vio timeouts are measured in milliseconds. Check for a possible
          overflow. In case of overflow, set to infinite.
        */
        if (timeout_sec > INT_MAX / 1000)
                timeout_ms = -1;
        else
                timeout_ms = (int)(timeout_sec * 1000);

        /* Deduce the current timeout status mode. */
        old_mode = vio->write_timeout < 0 && vio->read_timeout < 0;

        if (which)
                vio->write_timeout = timeout_ms;
        else
                vio->read_timeout = timeout_ms;

        /* VIO-specific timeout handling. Might change the blocking mode. */
        return vio->timeout ? vio->timeout(vio, which, old_mode) : 0;
}


```





## net_read_raw_loop
> 循坏

```c++
static bool net_read_raw_loop(NET *net, size_t count) {
        DBUG_TRACE;
        bool eof                    = false;
        unsigned int retry_count    = 0;
        uchar *buf                  = net->buff + net->where_b;
        bool timeout_on_full_packet = false;
        bool is_packet_timeout      = false;
#ifdef MYSQL_SERVER
        NET_SERVER *server_ext = static_cast<NET_SERVER *>(net->extension);
        if (server_ext)
                timeout_on_full_packet = server_ext->timeout_on_full_packet;
#endif

        time_t start_time = 0;
        if (timeout_on_full_packet)
                start_time = time(&start_time);
        while (count) {
                size_t recvcnt = vio_read(net->vio, buf, count);

                /* VIO_SOCKET_ERROR (-1) indicates an error. */
                if (recvcnt == VIO_SOCKET_ERROR) {
                        /* A recoverable I/O error occurred? */
                        if (net_should_retry(net, &retry_count))
                                continue;
                        else
                                break;
                }
                /* Zero indicates end of file. */
                else if (!recvcnt) {
                        eof = true;
                        break;
                }

                count -= recvcnt;
                buf += recvcnt;
#ifdef MYSQL_SERVER
                thd_increment_bytes_received(recvcnt);
#endif
                if (timeout_on_full_packet) {
                        time_t current_time = time(&current_time);
                        if (static_cast<unsigned int>(current_time - start_time) > net->read_timeout) {
                                is_packet_timeout = true;
                                break;
                        }
                }
        }

        /* On failure, propagate the error code. */
        if (count) {
                /* Interrupted by a timeout? */
                if (!eof && (vio_was_timeout(net->vio) || is_packet_timeout))
                        net->last_errno = ER_NET_READ_INTERRUPTED;
                else
                        net->last_errno = ER_NET_READ_ERROR;

#ifdef MYSQL_SERVER
                /* First packet always wait for net_wait_timeout */
                if (net->pkt_nr == 0 && (vio_was_timeout(net->vio) || is_packet_timeout)) {
                        net->last_errno = ER_CLIENT_INTERACTION_TIMEOUT;
                        /* Socket should be closed after trying to write/send error. */
                        THD *thd = current_thd;
                        if (thd) {
                                Security_context *sctx = thd->security_context();
                                std::string timeout{std::to_string(thd_get_net_wait_timeout(thd))};
                                Auth_id auth_id(sctx->priv_user(), sctx->priv_host());
                                LogErr(INFORMATION_LEVEL, ER_NET_WAIT_ERROR2, timeout.c_str(), auth_id.auth_str().c_str());
                        } else {
                                LogErr(INFORMATION_LEVEL, ER_NET_WAIT_ERROR);
                        }
                }
                net->error = NET_ERROR_SOCKET_NOT_READABLE;
                /*
                  Attempt to send error message to client although the client won't be
                  expecting messages. If later the client tries to send a command and fail
                  it will instead check if it can read an error message.
                */
                my_error(net->last_errno, MYF(0));
#else
                /* Socket should be closed. */
                net->error = NET_ERROR_SOCKET_UNUSABLE;
#endif
        }

        return count != 0;
}
```




`/root/mysql-server-8032-google-02/share/messages_to_clients.txt`
```
ER_CLIENT_INTERACTION_TIMEOUT
    eng "The client was disconnected by the server because of inactivity. See wait_timeout and interactive_timeout for configuring this behavior."
```

## 错误码位置
`/usr/include/mysql/mysqld_error.h`

```c++
#define ER_CLIENT_INTERACTION_TIMEOUT 4031
#define ER_INVALID_CAST_TO_GEOMETRY 4032
```












## ping 探活

```sql
mysqladmin -h127.0.0.1 -P8032 -uroot -proot  ping
```


## select 探活

```sql
select 1;

select version();

select @@version;

```

ping 的执行流程比 select 更短，效率更高，通过对比两者的调用栈，我们能更直观的看到这一点。






