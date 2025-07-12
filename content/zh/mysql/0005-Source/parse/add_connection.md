---
title: add_connection
description: add_connection
date: 2023-10-30
weight: 10001


---

{{< alert >}}
用于添加新的客户端连接到数据库服务器。



{{< /alert >}}

### sql/conn_handler/connection_handler_one_thread.cc
```c++
bool One_thread_connection_handler::add_connection(Channel_info *channel_info) {
  if (my_thread_init()) { // 初始化线程
    connection_errors_internal++; // 增加内部连接错误计数器
    channel_info->send_error_and_close_channel(ER_OUT_OF_RESOURCES, 0, false); // 通过通道信息发送资源不足的错误并关闭通道
    Connection_handler_manager::dec_connection_count(); // 减少连接计数
    return true; // 表示连接失败
  }

  THD *thd = channel_info->create_thd(); // 创建一个新的 THD 对象，并将其分配给 thd 指针。
  if (thd == nullptr) { // 如果 thd 指针为空
    connection_errors_internal++; // 增加内部连接错误计数器。
    channel_info->send_error_and_close_channel(ER_OUT_OF_RESOURCES, 0, false); // 通过通道信息发送资源不足的错误并关闭通道。
    Connection_handler_manager::dec_connection_count(); // 减少连接计数
    return true; // 表示连接失败
  }

  thd->set_new_thread_id(); // 为 thd 对象设置一个新的线程 ID

  /*
    handle_one_connection() is normally the only way a thread would
    start and would always be on the very high end of the stack ,
    therefore, the thread stack always starts at the address of the
    first local variable of handle_one_connection, which is thd. We
    need to know the start of the stack so that we could check for
    stack overruns.
  */
  thd_set_thread_stack(thd, (char *)&thd); // 设置 thd 对象的线程栈
  thd->store_globals(); // 存储全局变量。

  mysql_thread_set_psi_id(thd->thread_id()); // 设置线程的 PSI ID。
  mysql_socket_set_thread_owner(
      thd->get_protocol_classic()->get_vio()->mysql_socket); // 设置线程的套接字所有者。

  Global_THD_manager *thd_manager = Global_THD_manager::get_instance(); // 获取全局 THD 管理器的实例。
  thd_manager->add_thd(thd); // 向 THD 管理器中添加 thd 对象。

  bool error = false; // 定义布尔变量 error，并初始化为 false
  if (thd_prepare_connection(thd)) // 如果准备连接失败
    error = true;  // Returning true causes inc_aborted_connects() to be called. 将 error 设置为 true，表示连接出现错误
  else {
    delete channel_info; // 删除通道信息
    while (thd_connection_alive(thd)) { // 当连接仍然存在时执行以下循环
      if (do_command(thd)) break; // 如果执行命令失败，则退出循环
    }
    end_connection(thd); // 结束连接
  }
  close_connection(thd, 0, false, false); // 关闭连接
  thd->release_resources(); // 释放资源
  thd_manager->remove_thd(thd); // 从 THD 管理器中移除 thd 对象
  Connection_handler_manager::dec_connection_count(); // 减少连接计数
  delete thd; // 删除 thd 对象
  return error; // 返回 error，表示连接是否出现错误
}
```
**这段代码的主要作用是在数据库服务器中添加新的客户端连接:**
- 它首先初始化线程，并尝试创建一个 THD 对象来表示客户端连接。
- 如果初始化或创建失败，它会增加连接错误计数，并发送适当的错误消息
- 然后返回 true，表示连接失败。如果成功创建了 THD 对象，它将设置线程的一些属性
- 然后将 THD 对象添加到全局 THD 管理器中。接下来，它尝试准备连接
- 如果准备失败，则设置 error 为 true，表示连接出现错误。
- 否则，它会删除通道信息，并在循环中执行客户端连接的命令处理，直到连接结束。
- 最后，它关闭连接，释放资源，并从全局 THD 管理器中移除 THD 对象。
- 最终，它返回 error，表示连接是否出现错误。

**代码详解**：
- bool One_thread_connection_handler::add_connection(Channel_info *channel_info) {：定义一个名为 add_connection 的函数，它属于 One_thread_connection_handler 类，接受一个指向 Channel_info 对象的指针参数，并返回一个布尔值。
- if (my_thread_init()) {：如果线程初始化失败，则执行以下代码块。
- connection_errors_internal++;：增加内部连接错误计数器。
- channel_info->send_error_and_close_channel(ER_OUT_OF_RESOURCES, 0, false);：通过通道信息发送资源不足的错误并关闭通道。
- Connection_handler_manager::dec_connection_count();：减少连接计数。
- return true;：返回 true，表示连接失败。
- THD *thd = channel_info->create_thd();：创建一个新的 THD 对象，并将其分配给 thd 指针。
- if (thd == nullptr) {：如果 thd 指针为空，则执行以下代码块。
- connection_errors_internal++;：增加内部连接错误计数器。
- channel_info->send_error_and_close_channel(ER_OUT_OF_RESOURCES, 0, false);：通过通道信息发送资源不足的错误并关闭通道。
- Connection_handler_manager::dec_connection_count();：减少连接计数。
- return true;：返回 true，表示连接失败。
- thd->set_new_thread_id();：为 thd 对象设置一个新的线程 ID。
- thd_set_thread_stack(thd, (char *)&thd);：设置 thd 对象的线程栈。
- thd->store_globals();：存储全局变量。
- mysql_thread_set_psi_id(thd->thread_id());：设置线程的 PSI ID。
- mysql_socket_set_thread_owner(thd->get_protocol_classic()->get_vio()->mysql_socket);：设置线程的套接字所有者。
- Global_THD_manager *thd_manager = Global_THD_manager::get_instance();：获取全局 THD 管理器的实例。
- thd_manager->add_thd(thd);：向 THD 管理器中添加 thd 对象。
- bool error = false;：定义布尔变量 error，并初始化为 false。
- if (thd_prepare_connection(thd))：如果准备连接失败，则执行以下代码块。
- error = true;：将 error 设置为 true，表示连接出现错误。
- else {：否则执行以下代码块。
- delete channel_info;：删除通道信息。
- while (thd_connection_alive(thd)) {：当连接仍然存在时执行以下循环。
- if (do_command(thd)) break;：如果执行命令失败，则退出循环。
- end_connection(thd);：结束连接。
- }：结束循环。
- close_connection(thd, 0, false, false);：关闭连接。
- thd->release_resources();：释放资源。
- thd_manager->remove_thd(thd);：从 THD 管理器中移除 thd 对象。
- Connection_handler_manager::dec_connection_count();：减少连接计数。
- delete thd;：删除 thd 对象。
- return error;：返回 error，表示连接是否出现错误。
- 这段代码的主要作用是在数据库服务器中添加新的客户端连接。它首先初始化线程，并尝试创建一个 THD 对象来表示客户端连接。如果初始化或创建失败，它会增加连接错误计数，并发送适当的错误消息，然后返回 true，表示连接失败。如果成功创建了 THD 对象，它将设置线程的一些属性，然后将 THD 对象添加到全局 THD 管理器中。接下来，它尝试准备连接，如果准备失败，则设置 error 为 true，表示连接出现错误。否则，它会删除通道信息，并在循环中执行客户端连接的命令处理，直到连接结束。最后，它关闭连接，释放资源，并从全局 THD 管理器中移除 THD 对象。最终，它返回 error，表示连接是否出现错误。
