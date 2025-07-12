---
title: Thread object has no attribute 'isAlive'
description: Thread object has no attribute 'isAlive'
date: 2023-10-12
weight: 60000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
[root@node76 python_version]# python3.9 --version
Python 3.9.0

{{< /alert >}}

### Python 3.9.0 的threading.Thread类中的 isAlive() 变成 is_alive()

否则报错：`AttributeError: 'Thread' object has no attribute 'isAlive'`


```python
def backup_one_host(server_name):
    if is_backup_need_stop():
        return
    print_trace_info("%s(%s)" % (sys._getframe().f_code.co_name, threading.get_ident()), "start")
    global dbscale_server_user
    global dbscale_server_pswd
    global dbscale_server_port

    global backup_dest_ip
    global backup_dest_dir
    global xtrabackup_bin_dir

    global backup_type
    global dataserver_info

    global connect_method
    global adm_server_port

    global socat_port
    global backup_get_execption
    global socat_log_dir
    global socat_retry_count
    global ftwrl_server_dict
    global detect_ftwrl_lock_result
    global ftwrl_dead_lock_retry
    global socat_use_lock
    global xtrabackup_version
    socat_use_lock.acquire()
    has_release_socat_use_lock = False
    try:
      host = dataserver_info[server_name]['host']
      db_port = dataserver_info[server_name]['port']
      belong_to_datasource = dataserver_info[server_name]['belong_to_datasource']

      compress_mode = ''
      if compress == True:
          compress_mode += '--compress --compress-threads='
          compress_mode += compress_threads

      inc_backup_cmd = ''
      if backup_type == 'inc':
        if xtrabackup_version > 8026:
            inc_backup_cmd = ' --incremental-lsn=%s ' % (dataserver_info[server_name]['old-xb-to-lsn'])
        else:
            inc_backup_cmd = '--incremental --incremental-lsn=%s ' % (dataserver_info[server_name]['old-xb-to-lsn'])

      dest_server_conn = get_one_dest_server_conn()
      backupfile_dir_name = belong_to_datasource + '-' + server_name + '-' + host + '-' + db_port
      (is_slave, command_id, th) = clean_before_rextrabackup(dest_server_conn, server_name, backupfile_dir_name, belong_to_datasource)
      if th == None:
          dest_server_conn.close()
          return

      db_conn_config = {
          'host': host,
          'user': dataserver_user_pswd_map[server_name]['user'],
          'password': dataserver_user_pswd_map[server_name]['password'],
          'port': int(db_port),
          'db': 'information_schema',
          'charset': "utf8",
          'autocommit': True
      }
      conn = get_one_server_conn(host, dbscale_server_user, dbscale_server_port, dbscale_server_pswd)

      (db_conn, db_cursor) = get_database_conn(db_conn_config)
      if is_slave:
          query_text = 'show slave status'
          slave_status = get_query_result_set(query_text, db_cursor)
          slave_binlog_file[server_name] = slave_status[0]['Relay_Master_Log_File']
          slave_binlog_exec_pos[server_name] = slave_status[0]['Exec_Master_Log_Pos']

      query_text = "show global variables like 'pid_file'"
      pid_file = get_query_result_set(query_text, db_cursor)[0]['Value']
      query_text = "show global variables like 'datadir'"
      datadir = get_query_result_set(query_text, db_cursor)[0]['Value']
      if not pid_file.startswith('/'):
          pid_file = datadir+'/'+pid_file

      pid_value = conn.execute('cat %s' % pid_file)[0].strip('\n')
      #print_debug_info(pid_value, "get pid value of %s:%s" % (host, db_port))
      cmd = "ps -ef | grep %s | grep -v '_safe' | grep -v 'grep'" % pid_value
      proc_info = conn.execute(cmd)[0]
      #print_debug_info(proc_info, "get process info of %s:%s" % (host, db_port))
      proc_elem = proc_info.split(' ')
      db_conf_file = ''
      for elem in proc_elem:
          if '--defaults-file=' in elem:
              db_conf_file = elem.split('=')[1]
              break
      if len(db_conf_file) == 0:
          fatal_error("fail to get config file location of db %s:%s" % (host, db_port))
      backupfile_dir_name = belong_to_datasource + '-' + server_name + '-' + host + '-' + db_port
      config = {
              'dest_user': backup_dest_user,
              'dest_pswd': backup_dest_pswd,
              'dest_port': backup_dest_port
              }
      if connect_method == 2:
          config['dest_port'] = adm_server_port
      if not transfer_file_in_conn(conn, backup_dest_ip, db_conf_file, backup_dest_dir + '/' + backupfile_dir_name, 'my.conf', **config):
          conn.close()
          dest_server_conn.close()
          return

      query_text = "show global variables like 'tmpdir'"
      tmpdir = get_query_result_set(query_text, db_cursor)[0]['Value']
      xtrabackup_log_file = tmpdir + '/' + backup_id + '-' + backupfile_dir_name #use dir name as log file name

      source_cmd = "%s --defaults-file=%s --user=%s --password=%s --host=%s --port=%s --backup --tmpdir=%s %s --stream=xbstream %s 2>%s | socat - TCP4:%s:%d,retry=%s" \
              % (xtrabackup_bin_dir, db_conf_file, dataserver_user_pswd_map[server_name]['user'], dataserver_user_pswd_map[server_name]['password'], host, db_port, tmpdir, inc_backup_cmd, compress_mode, xtrabackup_log_file, backup_dest_ip, socat_port, socat_retry_count)
      command_id2 = get_command_id()
      command_info2 = ExecCommandInfo(host, dbscale_server_user, dbscale_server_port, dbscale_server_pswd, source_cmd)
      if not add_exec_command_info(command_id2, command_info2):
          conn.close()
          dest_server_conn.close()
          del_exec_command_info(command_id)
          return

      if compress == True:
          print_debug_info(source_cmd, 'socat source cmd with compress for dataserver %s' % server_name)
      else:
          print_debug_info(source_cmd, 'socat source cmd without compress for dataserver %s' % server_name)
      print_trace_info("%s(%s)" % (sys._getframe().f_code.co_name, threading.get_ident()), "in-progress")
      global conn_timeout

      #conn.execute(source_cmd, timeout=conn_timeout)
      server_str = host + ":" + str(db_port)
      server_index = ftwrl_server_dict[server_str]
      print_debug_info("th_detect_ftwrl_lock get server_index is %d." % server_index, 'server: %s' % server_str)
      if server_index == None:
        del_exec_command_info(command_id)
        fatal_error("get error when backup db get server index")
      detect_conn = get_one_server_conn(host, dbscale_server_user, dbscale_server_port, dbscale_server_pswd)
      ftwrl_retry_times = ftwrl_dead_lock_retry
      print_debug_info("backup_one_host ftwrl_retry_times: [%d]" % ftwrl_retry_times, 'server: %s' % server_str)
      while ftwrl_retry_times > 0:
        th_conn_execute = threading.Thread(target=xtrabackup_command_execute, args=(conn, source_cmd, server_index, server_str))
        th_detect_ftwrl_lock = threading.Thread(target=detect_ftwrl_lock, args=(detect_conn, xtrabackup_log_file, db_conn, db_cursor, server_index, server_str))
        th_conn_execute.start()
        th_detect_ftwrl_lock.start()
        time.sleep(5)
        socat_use_lock.release()
        has_release_socat_use_lock = True
        th_detect_ftwrl_lock.join()
        print_trace_info("th_detect_ftwrl_lock returned", 'server: %s' % server_str)
        #th_conn_execute.join()
        xtrabackup_exec_result = detect_ftwrl_lock_result[server_index]
        if xtrabackup_exec_result == enum_detect_ftwrl_lock_result['DETECT_FTWRL_RESULT_SUCCESSED']:
            print_trace_info("backup_one_host get DETECT_FTWRL_RESULT_SUCCESSED", 'server: %s' % server_str)
            # result_index == 1 means xtrabackup successed
            th_conn_execute.join()
            th.join(timeout = 60)
            if th.is_alive():
                print_err_info("backup_one_host get socat thread is alive, join timeout.", 'server: %s' % server_str)
            break
        elif xtrabackup_exec_result == enum_detect_ftwrl_lock_result['DETECT_FTWRL_RESULT_NEED_RETRY']:
            # result_index == 1 means need to retry
            th.join(timeout = 60)
            if th.is_alive():
                print_err_info("backup_one_host get socat thread is alive, join timeout.", 'server: %s' % server_str)
            th_conn_execute.join()
            socat_use_lock.acquire()
            has_release_socat_use_lock = False
            print_trace_info("backup_one_host get DETECT_FTWRL_RESULT_NEED_RETRY", 'server: %s' % server_str)
            del_exec_command_info(command_id)
            (is_slave, command_id, th) = clean_before_rextrabackup(dest_server_conn, server_name, backupfile_dir_name, belong_to_datasource)
            ftwrl_retry_times -= 1
            detect_ftwrl_lock_result[server_index] = enum_detect_ftwrl_lock_result['DETECT_FTWRL_RESULT_UNDETECT']
            set_ftwrl_status(enum_ftwrl_status['XTRABACKUP_UNEXECUTE'], server_index)
        else:
            # find unknown error
            print_trace_info("backup_one_host get detect_ftwrl_lock_result = [%d]" % (xtrabackup_exec_result), 'server: %s' % server_str)
            try:
                fatal_content = detect_conn.execute('cat %s' % xtrabackup_log_file)[0]
                with open('%s.log' % backupfile_dir_name, 'w') as f:
                    f.write(fatal_content)
            except Exception as e:
                pass
            del_exec_command_info(command_id)
            del_exec_command_info(command_id2)
            fatal_error("get error when backup db %s:%s, log file is %s.log" % (host, db_port, backup_id + '-' + backupfile_dir_name))

      if ftwrl_retry_times == 0:
        del_exec_command_info(command_id)
        del_exec_command_info(command_id2)
        fatal_error("ftwrl_retry_times is used out, no retry more, buckup exit.")
      print_debug_info("backup_one_host ftwrl_retry_times: [%d]" % ftwrl_retry_times, 'server: %s' % server_str)
      ret = conn.execute('tail -n50 %s' % xtrabackup_log_file)
      log_info = ret[0]
      if ret[1] != '' or 'completed OK!' not in log_info:
          content = conn.execute('cat %s' % xtrabackup_log_file)[0]
          with open('%s.log' % backupfile_dir_name, 'w') as f:
              f.write(content)
          fatal_error("get error when backup db %s:%s, log file is %s.log" % (host, db_port, backup_id + '-' + backupfile_dir_name))
      conn.close()
      #th.join() #TODO what if source socat failed, we need kill related dest socat process forcely

      del_exec_command_info(command_id)
      del_exec_command_info(command_id2)

      log_lines = log_info.split('\n')
      binlog_info = []
      xb_to_lsn = ''
      need_get_gtids = False
      for line in log_lines:
          if 'MySQL binlog position: filename' in line:
              #MySQL binlog position: filename 'master-bin.000001', position '6756'
              binlog_info = line[len("MySQL binlog position: filename '"):len(line)].split("', position '")
              if binlog_info[1].endswith(','):
                  need_get_gtids = True
                  continue
          if 'The latest check point (for incremental)' in line:
              xb_to_lsn = line.split(' ')[-1].strip("'")
          if len(binlog_info) >= 2 and need_get_gtids:
              if line.endswith(','):
                  pass
              else:
                  need_get_gtids = False
              binlog_info.append(line)

      if len(binlog_info) == 0:
          fatal_error("fail to filter binlog file and position info from xtrabackup logs of server %s:%s" % (host, db_port))

      dataserver_info[server_name]['xb_to_binlog_name'] = binlog_info[0]
      slave_executed_gtids[server_name] = ''
      if binlog_info[1].find('GTID'):
          dataserver_info[server_name]['xb_to_binlog_pos'] = binlog_info[1].split("',")[0]
          if binlog_info[1].endswith(','):
              slave_executed_gtids[server_name] = binlog_info[1].split("'")[-1]
          if binlog_info[1].endswith("'"):
              slave_executed_gtids[server_name] = binlog_info[1].split(" '")[-1]
          if len(binlog_info) > 2:
              for line in binlog_info[2:len(binlog_info)]:
                  slave_executed_gtids[server_name] = slave_executed_gtids[server_name] + line
          slave_executed_gtids[server_name] = slave_executed_gtids[server_name][:-1]
      else:
          dataserver_info[server_name]['xb_to_binlog_pos'] = binlog_info[1]
      dest_server_conn.execute("cd %s && echo '%s' > %s/executed_gtids" % (backup_dest_dir, slave_executed_gtids[server_name], backupfile_dir_name))
      dest_server_conn.close()
      dataserver_info[server_name]['xb_to_lsn'] = xb_to_lsn
    except Exception as e:
        backup_get_execption = True
        print_err_info('dataserver %s get error during backup:%s' % (server_name, e), '')
        if not has_release_socat_use_lock:
            socat_use_lock.release()
        raise
    print_trace_info("%s(%s)" % (sys._getframe().f_code.co_name, threading.get_ident()), "finish")
```




