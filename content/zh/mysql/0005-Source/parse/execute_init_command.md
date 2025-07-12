---
title: execute_init_command
description: execute_init_command
date: 2023-10-30
weight: 10001


---

{{< alert >}}

- thd：当前线程的 THD 结构体指针，表示当前执行线程。
- init_command：需要执行的初始化命令。
- var_lock：MySQL 读写锁，用于保护共享变量。


在 MySQL 8.0.26 源码中，execute_init_command、do_command 和 execute_command 是处理客户端命令的三个重要函数，它们之间存在一定的联系，主要体现在以下几个方面：

- 调用关系：

execute_init_command 和 do_command 在服务器启动过程中都可能被调用，而 execute_command 在处理客户端命令时被调用。
execute_init_command 可用于执行服务器启动时的初始化命令，而 do_command 和 execute_command 则用于处理客户端发送的命令。

- 预处理阶段：

在执行客户端命令之前，通常需要进行一些预处理工作，如字符集转换、协议切换等。
execute_command 函数用于执行特定客户端命令之前的预处理工作，而 execute_init_command 可以看作是一种特殊的初始化命令的预处理，用于执行服务器启动时的初始化命令。
实际命令执行：

execute_init_command 和 do_command 中都有调用 dispatch_command 函数的过程，用于实际执行命令。
execute_init_command 在执行初始化命令时也需要调用 dispatch_command 函数来执行实际的命令。
execute_command 在预处理完成后，最终也会调用 dispatch_command 函数来执行客户端发送的命令。
- 执行流程：

在服务器启动时，首先可能会执行 execute_init_command 函数来执行初始化命令。
当客户端连接到服务器并发送命令时，服务器会调用 do_command 函数来处理客户端命令，其中可能会涉及到对部分命令的预处理，这时会调用 execute_command 函数。
在 execute_command 函数中，会对客户端命令进行预处理，并最终调用 dispatch_command 函数来执行实际的命令。
综上所述，execute_init_command、do_command 和 execute_command 这三个函数在处理客户端命令的过程中扮演着不同的角色，但它们之间存在一定的联系，共同构成了处理客户端命令的完整流程。
{{< /alert >}}


```c++

void execute_init_command(THD *thd, LEX_STRING *init_command,
                          mysql_rwlock_t *var_lock) {
  Protocol_classic *protocol = thd->get_protocol_classic();
  Vio *save_vio;
  ulong save_client_capabilities;
  COM_DATA com_data;

  mysql_rwlock_rdlock(var_lock);
  if (!init_command->length) {
    mysql_rwlock_unlock(var_lock);
    return;
  }

  /*
    copy the value under a lock, and release the lock.
    init_command has to be executed without a lock held,
    as it may try to change itself
  */
  size_t len = init_command->length;
  char *buf = thd->strmake(init_command->str, len);
  mysql_rwlock_unlock(var_lock);

#if defined(ENABLED_PROFILING)
  thd->profiling->start_new_query();
  thd->profiling->set_query_source(buf, len);
#endif

  /*
    Clear the DA in anticipation of possible failures in anticipation
    of possible command parsing failures.
  */
  thd->get_stmt_da()->reset_diagnostics_area();

  THD_STAGE_INFO(thd, stage_execution_of_init_command);
  save_client_capabilities = protocol->get_client_capabilities();
  protocol->add_client_capability(CLIENT_MULTI_QUERIES);
  /*
    We do not prepare a COM_QUERY packet with query attributes
    since the init commands have nobody to supply query attributes.
  */
  protocol->remove_client_capability(CLIENT_QUERY_ATTRIBUTES);
  /*
    We don't need return result of execution to client side.
    To forbid this we should set thd->net.vio to 0.
  */
  save_vio = protocol->get_vio();
  protocol->set_vio(nullptr);
  if (!protocol->create_command(&com_data, COM_QUERY, (uchar *)buf, len))
    dispatch_command(thd, &com_data, COM_QUERY);
  protocol->set_client_capabilities(save_client_capabilities);
  protocol->set_vio(save_vio);

#if defined(ENABLED_PROFILING)
  thd->profiling->finish_current_query();
#endif
}

```
