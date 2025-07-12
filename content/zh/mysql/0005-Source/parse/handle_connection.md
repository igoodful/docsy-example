---
title: handle_connection
description: handle_connection
date: 2023-10-30
weight: 10001


---

{{< alert >}}

线程处理函数 handle_connection，用于处理客户端与服务器之间的连接。


{{< /alert >}}


```c++
extern "C" {
static void *handle_connection(void *arg) {
  Global_THD_manager *thd_manager = Global_THD_manager::get_instance();
  Connection_handler_manager *handler_manager =
      Connection_handler_manager::get_instance();
  Channel_info *channel_info = static_cast<Channel_info *>(arg);
  bool pthread_reused MY_ATTRIBUTE((unused)) = false;
  // 如果线程初始化失败（通过 my_thread_init() 判断），则会增加连接错误计数，发送错误信息给客户端并关闭连接，然后退出线程。
  if (my_thread_init()) {
    connection_errors_internal++;
    channel_info->send_error_and_close_channel(ER_OUT_OF_RESOURCES, 0, false);
    handler_manager->inc_aborted_connects();
    Connection_handler_manager::dec_connection_count();
    delete channel_info;
    my_thread_exit(nullptr);
    return nullptr;
  }
  // 接着进入无限循环，不断处理连接，用于处理多个连接
  for (;;) {
    THD *thd = init_new_thd(channel_info); // 首先初始化一个新的 THD 对象表示当前线程，然后进行连接准备工作。用于处理连接。如果初始化失败，则增加连接错误计数，减少连接数并退出循环。
    if (thd == nullptr) {
      connection_errors_internal++;
      handler_manager->inc_aborted_connects();
      Connection_handler_manager::dec_connection_count();
      break;  // We are out of resources, no sense in continuing.
    }

#ifdef HAVE_PSI_THREAD_INTERFACE
    if (pthread_reused) {
      /*
        Reusing existing pthread:
        Create new instrumentation for the new THD job,
        and attach it to this running pthread.
      */
      PSI_thread *psi = PSI_THREAD_CALL(new_thread)(key_thread_one_connection,
                                                    thd, thd->thread_id());
      PSI_THREAD_CALL(set_thread_os_id)(psi);
      PSI_THREAD_CALL(set_thread)(psi);
    }
#endif

#ifdef HAVE_PSI_THREAD_INTERFACE
    /* Find the instrumented thread */
    PSI_thread *psi = PSI_THREAD_CALL(get_thread)();
    /* Save it within THD, so it can be inspected */
    thd->set_psi(psi);
#endif /* HAVE_PSI_THREAD_INTERFACE */
    mysql_thread_set_psi_id(thd->thread_id());
    mysql_thread_set_psi_THD(thd);
    MYSQL_SOCKET socket = thd->get_protocol_classic()->get_vio()->mysql_socket;
    mysql_socket_set_thread_owner(socket);
    thd_manager->add_thd(thd); // 将当前线程的 THD 对象添加到全局的 Global_THD_manager 中

    if (thd_prepare_connection(thd))
      handler_manager->inc_aborted_connects();
    else {
      while (thd_connection_alive(thd)) {
        if (do_command(thd)) break;
      }
      end_connection(thd); // 结束连接
    }
    close_connection(thd, 0, false, false); // 关闭连接

    thd->get_stmt_da()->reset_diagnostics_area();
    thd->release_resources(); // 释放当前线程的资源，清空诊断信息

    // Clean up errors now, before possibly waiting for a new connection.
#if OPENSSL_VERSION_NUMBER < 0x10100000L
    ERR_remove_thread_state(0);
#endif /* OPENSSL_VERSION_NUMBER < 0x10100000L */
    thd_manager->remove_thd(thd); // 从全局的 Global_THD_manager 中移除当前线程的 THD 对象
    Connection_handler_manager::dec_connection_count();

#ifdef HAVE_PSI_THREAD_INTERFACE
    /*
      Delete the instrumentation for the job that just completed.
    */
    thd->set_psi(nullptr);
    PSI_THREAD_CALL(delete_current_thread)();
#endif /* HAVE_PSI_THREAD_INTERFACE */

    delete thd; // 删除当前线程的 THD 对象

    // Server is shutting down so end the pthread.
    if (connection_events_loop_aborted()) break; // 如果服务器正在关闭，则跳出循环

    channel_info = Per_thread_connection_handler::block_until_new_connection(); // 获取新的连接通道信息
    if (channel_info == nullptr) break;
    pthread_reused = true;
    if (connection_events_loop_aborted()) {
      // Close the channel and exit as server is undergoing shutdown.
      channel_info->send_error_and_close_channel(ER_SERVER_SHUTDOWN, 0, false);
      delete channel_info;
      channel_info = nullptr;
      Connection_handler_manager::dec_connection_count();
      break;
    }
  }

  my_thread_end();
  my_thread_exit(nullptr);
  return nullptr;
}
}  // extern "C"


```

这段代码是 MySQL 8.0.26 源码中的一个线程处理函数 handle_connection，用于处理客户端与服务器之间的连接。以下是对该函数的详细解析：

函数签名：

`static void *handle_connection(void *arg)`
- arg：指向 Channel_info 结构的指针，表示一个通道信息，其中包含了客户端连接的相关信息。

- 函数实现：
  - 首先获取全局的 Global_THD_manager 和 Connection_handler_manager 实例，用于管理线程以及连接处理。
  - 将传入的参数 arg 转换为 Channel_info 类型的指针，表示当前连接的通道信息。
  - 如果线程初始化失败（通过 my_thread_init() 判断），则会增加连接错误计数，发送错误信息给客户端并关闭连接，然后退出线程。
  - 接着进入无限循环，不断处理连接：
    - 调用 init_new_thd 函数初始化一个新的 THD 对象，表示一个线程句柄，用于处理连接。如果初始化失败，则增加连接错误计数，减少连接数并退出循环。
    - 将当前线程的 THD 对象添加到全局的 Global_THD_manager 中。
    - 调用 thd_prepare_connection 函数准备连接，如果准备连接失败，则增加连接错误计数。
    - 否则，进入循环处理客户端命令：
      - 调用 do_command 函数执行客户端命令，如果命令执行完毕或者出现错误则跳出循环。
    - 调用 end_connection 函数结束连接。
    - 调用 close_connection 函数关闭连接。
    - 释放当前线程的资源，清空诊断信息。
    - 从全局的 Global_THD_manager 中移除当前线程的 THD 对象。
    - 删除当前线程的 THD 对象。
    - 如果服务器正在关闭，则跳出循环。
    - 通过 Per_thread_connection_handler::block_until_new_connection() 获取新的连接通道信息。
    - 如果获取到新的连接通道信息，则继续下一轮循环处理。
    - 否则，跳出循环，结束线程。

- 线程处理流程：
  - 线程处理函数中包含一个无限循环，用于处理多个连接。
  - 每次处理连接时，首先初始化一个新的 THD 对象表示当前线程，然后进行连接准备工作。
  - 接着循环处理客户端命令，直到连接结束或者出现错误。
  - 处理完一个连接后，释放资源，关闭连接，然后等待新的连接。
- 线程退出：
  - 当服务器正在关闭或者无法获取新的连接时，线程会退出。

该函数是 MySQL 8.0.26 中用于处理客户端连接的关键函数之一，负责管理连接的生命周期，处理客户端发送的命令，并在连接结束后释放资源。










