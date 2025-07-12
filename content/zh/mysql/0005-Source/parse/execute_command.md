---
title: execute_command
description: execute_command
date: 2023-10-30
weight: 10001


---

{{< alert >}}

do_command 和 execute_command 都会调用 dispatch_command 函数来执行实际的命令处理，但它们在调用 dispatch_command 的上下文和功能方面存在一些不同：

- 上下文不同：
  - do_command 函数用于处理客户端发送的命令，在 MySQL 服务器接收到客户端连接后，会通过 do_command 函数处理客户端发送的命令请求。
  - execute_command 函数则是在处理服务器内部的会话执行命令时使用的。例如，在服务器内部执行一些任务时，会调用 execute_command 函数来执行特定的命令。

- 功能不同：
  - do_command 函数的主要功能是处理来自客户端的命令请求，因此它会在命令处理之前执行一些与客户端交互相关的操作，比如读取客户端发送的命令数据、设置网络超时时间等。
  - execute_command 函数用于服务器内部执行命令，因此它可能会在命令处理之前执行一些服务器内部的操作，比如备份会话状态、设置执行上下文等。

虽然它们都调用了 dispatch_command 函数来执行实际的命令处理，但由于上下文和功能的不同，它们在调用 dispatch_command 前后可能会执行不同的操作。


{{< /alert >}}



```c++
int Srv_session::execute_command(enum enum_server_command command,
                                 const union COM_DATA *data,
                                 const CHARSET_INFO *client_cs,
                                 const struct st_command_service_cbs *callbacks,
                                 enum cs_text_or_binary text_or_binary,
                                 void *callbacks_context) {
  DBUG_TRACE;

  if (!srv_session_server_is_available()) {
    if (err_protocol_ctx.handler)
      err_protocol_ctx.handler(err_protocol_ctx.handler_context,
                               ER_SESSION_WAS_KILLED,
                               ER_DEFAULT(ER_SESSION_WAS_KILLED));
    return 1;
  }

  if (thd.killed) {
    if (err_protocol_ctx.handler)
      err_protocol_ctx.handler(err_protocol_ctx.handler_context,
                               ER_SESSION_WAS_KILLED,
                               ER_DEFAULT(ER_SESSION_WAS_KILLED));
    return 1;
  }

  assert(thd.get_protocol() == &protocol_error);

  // RAII:the destructor restores the state
  Srv_session::Session_backup_and_attach backup(this, false);

  if (backup.attach_error) return 1;

  if (client_cs && thd.variables.character_set_results != client_cs &&
      thd_init_client_charset(&thd, client_cs->number))
    return 1;

  /* Switch to different callbacks */
  Protocol_callback client_proto(callbacks, text_or_binary, callbacks_context);

  thd.push_protocol(&client_proto);

  mysql_audit_release(&thd);

  /*
    The server does it for COM_QUERY in dispatch_sql_command() but not for
    COM_INIT_DB, for example
  */
  if (command != COM_QUERY) thd.reset_for_next_command();

  assert(thd.m_statement_psi == nullptr);
  thd.m_statement_psi = MYSQL_START_STATEMENT(
      &thd.m_statement_state, stmt_info_new_packet.m_key, thd.db().str,
      thd.db().length, thd.charset(), nullptr);
  int ret = dispatch_command(&thd, data, command);

  thd.pop_protocol();
  assert(thd.get_protocol() == &protocol_error);
  return ret;
}

```
execute_command 函数是一个在 Srv_session 类中定义的方法，而 dispatch_command 函数则是一个单独的函数。它们之间的关系是：

execute_command 函数是一个会话级别的方法，用于执行客户端发送的命令。在执行命令之前，execute_command 会进行一些预处理（例如备份会话状态、字符集处理、协议处理等），然后调用 dispatch_command 函数来执行实际的命令。

dispatch_command 函数是 MySQL 服务器中的一个核心函数，用于分派并执行客户端发送的命令。它接收线程处理器、命令数据和命令类型等参数，并根据命令类型执行相应的操作。dispatch_command 函数的具体实现负责解析命令、执行相应的操作，并返回执行结果。

因此，execute_command 函数是一个高层次的封装，它通过调用 dispatch_command 函数来实际执行命令，而 dispatch_command 函数则是实现命令执行逻辑的底层函数。










