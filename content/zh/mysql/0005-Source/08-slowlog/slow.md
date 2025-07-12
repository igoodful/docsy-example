---
title: slow
description: slow
date: 2023-10-30
weight: 10000


---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}


{{< /alert >}}


### 问题：什么情况下会打印该信息？
```sql
/root/local/mysql_8026/bin/mysqld, Version: 8.0.26-debug (Source distribution). started with:
Tcp port: 8026  Unix socket: /root/local/mysql_8026/tmp/mysql.sock
Time                 Id Command    Argument
```

- 实例启动
- 执行`flush logs`
-


### sql/log.cc
```c++
bool File_query_log::open() {
  File file = -1;
  my_off_t pos = 0;
  char buff[FN_REFLEN];
  MY_STAT f_stat;
  DBUG_TRACE;

  assert(name != nullptr);

  if (is_open()) return false;

  write_error = false;

  /* File is regular writable file */
  if (my_stat(log_file_name, &f_stat, MYF(0)) && !MY_S_ISREG(f_stat.st_mode))
    goto err;

  db[0] = 0;

  /* First, open the file to make sure it exists. */
  if ((file = mysql_file_open(m_log_file_key, log_file_name,
                              O_CREAT | O_WRONLY | O_APPEND, MYF(MY_WME))) < 0)
    goto err;

#ifdef _WIN32
  char real_log_file_name[FN_REFLEN];
#else
  /* File name must have room for PATH_MAX. Checked against F_REFLEN later. */
  char real_log_file_name[PATH_MAX];
#endif  // _Win32

  /* Reopen and get real path. */
  if ((file = mysql_file_real_name_reopen(file,
#ifdef HAVE_PSI_FILE_INTERFACE
                                          m_log_file_key,
#endif
                                          O_CREAT | O_WRONLY | O_APPEND,
                                          log_file_name, real_log_file_name)) <
      0)
    goto err;

  if (!is_valid_log_name(real_log_file_name, strlen(real_log_file_name))) {
    LogErr(ERROR_LEVEL, ER_INVALID_ERROR_LOG_NAME, real_log_file_name);
    goto err;
  }

  if ((pos = mysql_file_tell(file, MYF(MY_WME))) == MY_FILEPOS_ERROR) {
    if (my_errno() == ESPIPE)
      pos = 0;
    else
      goto err;
  }

  if (init_io_cache(&log_file, file, IO_SIZE, WRITE_CACHE, pos, false,
                    MYF(MY_WME | MY_NABP)))
    goto err;

  {
    char *end;
    size_t len =
        snprintf(buff, sizeof(buff),
                 "%s, Version: %s (%s). "
#if defined(_WIN32)
                 "started with:\nTCP Port: %d, Named Pipe: %s\n",
                 my_progname, server_version, MYSQL_COMPILATION_COMMENT_SERVER,
                 mysqld_port, mysqld_unix_port
#else
                 "started with:\nTcp port: %d  Unix socket: %s\n",
                 my_progname, server_version, MYSQL_COMPILATION_COMMENT_SERVER,
                 mysqld_port, mysqld_unix_port
#endif
        );
    end =
        my_stpncpy(buff + len, "Time                 Id Command    Argument\n",
                   sizeof(buff) - len);
    if (my_b_write(&log_file, (uchar *)buff, (uint)(end - buff)) ||
        flush_io_cache(&log_file))
      goto err;
  }

  log_open = true;
  return false;

err:
  char log_open_file_error_message[96] = "";
  if (strcmp(opt_slow_logname, name) == 0) {
    strcpy(log_open_file_error_message,
           error_message_for_error_log(ER_LOG_SLOW_CANNOT_OPEN));
  } else if (strcmp(opt_general_logname, name) == 0) {
    strcpy(log_open_file_error_message,
           error_message_for_error_log(ER_LOG_GENERAL_CANNOT_OPEN));
  }

  char errbuf[MYSYS_STRERROR_SIZE];
  my_strerror(errbuf, sizeof(errbuf), errno);
  LogEvent()
      .type(LOG_TYPE_ERROR)
      .prio(ERROR_LEVEL)
      .errcode(ER_LOG_FILE_CANNOT_OPEN)
      .os_errno(errno)
      .os_errmsg(errbuf)
      .lookup(ER_LOG_FILE_CANNOT_OPEN, name, errno, errbuf,
              log_open_file_error_message);
  if (file >= 0) mysql_file_close(file, MYF(0));
  end_io_cache(&log_file);

  log_open = false;
  return true;
}

```
- Query_logger::reopen_log_file
- Query_logger::activate_log_handler
-




