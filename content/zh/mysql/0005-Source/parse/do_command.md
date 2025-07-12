---
title: do_command
description: do_command
date: 2023-10-30
weight: 10001


---

{{< alert >}}

主要功能是处理来自客户端的命令请求，因此它会在命令处理之前执行一些与客户端交互相关的操作，比如读取客户端发送的命令数据、设置网络超时时间等。

{{< /alert >}}




```c++
bool do_command(THD *thd) {
  bool return_value;
  int rc;
  NET *net = nullptr;
  enum enum_server_command command;
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
  rc = thd->get_protocol()->get_command(&com_data, &command);
  thd->m_server_idle = false;

  if (rc) {
#ifndef NDEBUG
    char desc[VIO_DESCRIPTION_SIZE];
    vio_description(net->vio, desc);
    DBUG_PRINT("info", ("Got error %d reading command from socket %s",
                        net->error, desc));
#endif  // NDEBUG
    /* Instrument this broken statement as "statement/com/error" */
    thd->m_statement_psi = MYSQL_REFINE_STATEMENT(
        thd->m_statement_psi, com_statement_info[COM_END].m_key);

    /* Check if we can continue without closing the connection */

    /* The error must be set. */
    assert(thd->is_error());
    thd->send_statement_status();

    /* Mark the statement completed. */
    MYSQL_END_STATEMENT(thd->m_statement_psi, thd->get_stmt_da());
    thd->m_statement_psi = nullptr;
    thd->m_digest = nullptr;

    if (rc < 0) {
      return_value = true;  // We have to close it.
      goto out;
    }
    net->error = NET_ERROR_UNSET;
    return_value = false;
    goto out;
  }

#ifndef NDEBUG
  char desc[VIO_DESCRIPTION_SIZE];
  vio_description(net->vio, desc);
  DBUG_PRINT("info", ("Command on %s = %d (%s)", desc, command,
                      Command_names::str_notranslate(command).c_str()));
#endif  // NDEBUG
  DBUG_PRINT("info", ("packet: '%*.s'; command: %d",
                      (int)thd->get_protocol_classic()->get_packet_length(),
                      thd->get_protocol_classic()->get_raw_packet(), command));
  if (thd->get_protocol_classic()->bad_packet)
    assert(0);  // Should be caught earlier

  // Reclaim some memory
  thd->get_protocol_classic()->get_output_packet()->shrink(
      thd->variables.net_buffer_length);
  /* Restore read timeout value */
  my_net_set_read_timeout(net, thd->variables.net_read_timeout);

  DEBUG_SYNC(thd, "before_command_dispatch");

  return_value = dispatch_command(thd, &com_data, command);
  thd->get_protocol_classic()->get_output_packet()->shrink(
      thd->variables.net_buffer_length);

out:
  /* The statement instrumentation must be closed in all cases. */
  assert(thd->m_digest == nullptr);
  assert(thd->m_statement_psi == nullptr);
  return return_value;
}
```













