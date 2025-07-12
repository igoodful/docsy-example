---
title: network_init
description: network_init
date: 2023-10-30
weight: 10001


---

{{< alert >}}


{{< /alert >}}


### sql/mysqld.cc
```c++
static bool network_init(void) {
  if (opt_initialize) return false;

#ifdef HAVE_SYS_UN_H
  std::string const unix_sock_name(mysqld_unix_port ? mysqld_unix_port : "");
#else
  std::string const unix_sock_name("");
#endif

  std::list<Bind_address_info> bind_addresses_info;

  if (!opt_disable_networking || unix_sock_name != "") {
    if (my_bind_addr_str != nullptr &&
        check_bind_address_has_valid_value(my_bind_addr_str,
                                           &bind_addresses_info)) {
      LogErr(ERROR_LEVEL, ER_INVALID_VALUE_OF_BIND_ADDRESSES, my_bind_addr_str);
      return true;
    }

    Bind_address_info admin_address_info;
    if (!opt_disable_networking) {
      if (my_admin_bind_addr_str != nullptr &&
          check_admin_address_has_valid_value(my_admin_bind_addr_str,
                                              &admin_address_info)) {
        LogErr(ERROR_LEVEL, ER_INVALID_ADMIN_ADDRESS, my_admin_bind_addr_str);
        return true;
      }
      /*
        Port 0 is interpreted by implementations of TCP protocol
        as a hint to find a first free port value to use and bind to it.
        On the other hand, the option mysqld_admin_port can be assigned
        the value 0 if a user specified a value that is out of allowable
        range of values. Therefore, to avoid a case when an operating
        system binds admin interface to am arbitrary selected port value,
        set it explicitly to the value MYSQL_ADMIN_PORT in case it has value 0.
      */
      if (mysqld_admin_port == 0) mysqld_admin_port = MYSQL_ADMIN_PORT;
    }
    Mysqld_socket_listener *mysqld_socket_listener = new (std::nothrow)
        Mysqld_socket_listener(bind_addresses_info, mysqld_port,
                               admin_address_info, mysqld_admin_port,
                               admin_address_info.address.empty()
                                   ? false
                                   : listen_admin_interface_in_separate_thread,
                               back_log, mysqld_port_timeout, unix_sock_name);
    if (mysqld_socket_listener == nullptr) return true;

    mysqld_socket_acceptor = new (std::nothrow)
        Connection_acceptor<Mysqld_socket_listener>(mysqld_socket_listener);
    if (mysqld_socket_acceptor == nullptr) {
      delete mysqld_socket_listener;
      mysqld_socket_listener = nullptr;
      return true;
    }

    if (mysqld_socket_acceptor->init_connection_acceptor())
      return true;  // mysqld_socket_acceptor would be freed in unireg_abort.

    if (report_port == 0) report_port = mysqld_port;

    if (!opt_disable_networking) assert(report_port != 0);
  }
#ifdef _WIN32
  // Create named pipe
  if (opt_enable_named_pipe) {
    std::string pipe_name = mysqld_unix_port ? mysqld_unix_port : "";

    named_pipe_listener = new (std::nothrow) Named_pipe_listener(&pipe_name);
    if (named_pipe_listener == NULL) return true;

    named_pipe_acceptor = new (std::nothrow)
        Connection_acceptor<Named_pipe_listener>(named_pipe_listener);
    if (named_pipe_acceptor == NULL) {
      delete named_pipe_listener;
      named_pipe_listener = NULL;
      return true;
    }

    if (named_pipe_acceptor->init_connection_acceptor())
      return true;  // named_pipe_acceptor would be freed in unireg_abort.
  }

  // Setup shared_memory acceptor
  if (opt_enable_shared_memory) {
    std::string shared_mem_base_name =
        shared_memory_base_name ? shared_memory_base_name : "";

    Shared_mem_listener *shared_mem_listener =
        new (std::nothrow) Shared_mem_listener(&shared_mem_base_name);
    if (shared_mem_listener == NULL) return true;

    shared_mem_acceptor = new (std::nothrow)
        Connection_acceptor<Shared_mem_listener>(shared_mem_listener);
    if (shared_mem_acceptor == NULL) {
      delete shared_mem_listener;
      shared_mem_listener = NULL;
      return true;
    }

    if (shared_mem_acceptor->init_connection_acceptor())
      return true;  // shared_mem_acceptor would be freed in unireg_abort.
  }
#endif  // _WIN32
  return false;
}

```