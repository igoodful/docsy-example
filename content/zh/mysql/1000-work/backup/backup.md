---
title: backup.py
description: backup.py
date: 2023-10-12
weight: 60000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}


{{< /alert >}}

## 备份流程

### 解析配置文件：
> init_config_from_conf_file
```python
def init_config_from_conf_file():
    print_trace_info(sys._getframe().f_code.co_name, "start") # [TRACE 140533116843840 2024-02-22 09:11:28.048869] init_config_from_conf_file start
    global backup_id
    global connect_method
    global rabbit_mq_uri
    global adm_server_port

    global cluster_config
    global dbscale_server_user
    global dbscale_server_pswd
    global dbscale_server_port
    global conn_timeout

    global backup_dest_ip
    global backup_dest_port
    global backup_dest_user
    global backup_dest_pswd
    global backup_dest_dir
    global backup_base_dir
    global xtrabackup_bin_dir
    global store_bad_backup

    global socat_port
    global socat_port2
    global using_socat
    global debug
    global backup_type
    global backup_conf_file
    global log_audit
    global compress
    global compress_threads
    global backup_master
    global backup_slave_num
    global backup_slaves
    global socat_retry_count

    global ftwrl_dead_lock_retry
    global ftwrl_dead_lock_seconds
    global use_disaster_recovery_cluster

    backup_id = datetime.utcnow().strftime('UTC%Y%m%d%H%M%S.%f')[:-3] # backup_id=' UTC20240222014345.236'   差了8小时，需要加上
    print_trace_info("current backup id: %s" % backup_id, '') # [TRACE 140533116843840 2024-02-22 09:13:36.494535] current backup id: UTC20240222011253.698

    config = configparser.ConfigParser()

    if args.config_file is not None:
        backup_conf_file = args.config_file # /root/backup-recovery-tool-6039/python_version/backup01.conf
        print_debug_info(backup_conf_file, "use config file") # [DEBUG 140533116843840 2024-02-22 09:17:53.264571] use config file : /root/backup-recovery-tool-6039/python_version/backup01.conf
    config.read(backup_conf_file)

    if 'log-audit' in config['misc'].keys() and (config['misc']['log-audit'] == '1' or config['misc']['log-audit'] == 'true'):
        log_audit = True
    else:
        log_audit = False # log_audit = False

    if config['misc']['debug'] == '0' or config['misc']['debug'].lower() == 'false':
        debug = False
    else:
        debug = True # debug = True
    if 'force-full-backup' in config['misc'].keys() and config['misc']['force-full-backup'] == '1':
        backup_type = 'full' #  backup_type = 'full'
    if 'compress' in config['misc'].keys() and (config['misc']['compress'] == '1' or config['misc']['compress'].lower() == 'true'):
        compress = True
    else:
        compress = False # compress = False
    if 'compress-threads' in config['misc'].keys():
        compress_threads = config['misc']['compress-threads'] # compress_threads=5
    if 'backup-master' in config['misc'].keys() and (config['misc']['backup-master'] == '1' or config['misc']['backup-master'].lower() == 'true'):
        backup_master = True # backup_master = False
    backup_slave_config = config['misc'].get('backup-slave') # backup_slave_config = 1
    if backup_slave_config is not None:
        slaves = backup_slave_config.split(",") # slaves = 1
        if len(slaves) == 0:
            backup_slave_num = 0
        elif len(slaves) == 1:
            first_slave = slaves[0] # first_slave=1
            if first_slave.strip().isdigit():
                backup_slave_num = int(first_slave) # backup_slave_num=1
        if backup_slave_num is None:
            for slave in slaves:
                slave = slave.strip()
                backup_slaves.add(slave)

    connect_method_str = config['misc'].get('connect-method').lower() # connect_method_str='rabbit-agent'
    if connect_method_str == 'ssh':
        connect_method = 0
    elif connect_method_str == 'rabbit-agent':
        connect_method = 1 # connect_method = 1
    elif connect_method_str == 'adm-agent':
        connect_method = 2
    else:
        fatal_error('connection method config error, plz config ssh or rabbit-agent or adm-agent')

    if connect_method == 1:
        if config.get('rabbit-agent', 'rabbitmq-uri') is None:
            fatal_error('rabbit agent config not found')
        rabbit_mq_uri = config['rabbit-agent']['rabbitmq-uri'] # rabbit_mq_uri='rabbit://admin:123456@172.17.134.70:5672//'
    elif connect_method == 2:
        if config.get('adm-agent', 'port') is None:
            fatal_error('adm agent config not found')
        adm_server_port = config['adm-agent']['port']

    if 'socat-retry-count' in config['misc'].keys():
        socat_retry_count = int(config['misc']['socat-retry-count']) # socat_retry_count=10

    global backup_exec_timeout
    backup_exec_timeout_str = config['misc'].get('max-time-sec') # backup_exec_timeout_str=45000
    if backup_exec_timeout_str is not None and backup_exec_timeout_str.isdigit():
        backup_exec_timeout = int(backup_exec_timeout_str) # backup_exec_timeout=45000

    cluster_config = {
        'host': config['cluster']['ip'],
        'user': config['cluster']['user'],
        'password': config['cluster']['password'],
        'port': int(config['cluster']['port']),
        'db': 'information_schema',
        'charset': "utf8",
        'autocommit': True
    }
    print_debug_info(cluster_config, "get cluster config") # [DEBUG 140533116843840 2024-02-22 09:29:09.800214] get cluster config : {'host': '172.17.134.77', 'user': 'admin', 'password': '123456', 'port': 16310, 'db': 'information_schema', 'charset': 'utf8', 'autocommit': True}

    dbscale_server_user = config['server-ssh-info']['user'] # dbscale_server_user=''
    dbscale_server_pswd = config['server-ssh-info']['password'] # dbscale_server_pswd=''
    dbscale_server_port = config['server-ssh-info']['port'] # dbscale_server_port=''

    backup_dest_ip = config['backup-store']['ip'] # backup_dest_ip='172.17.134.76'
    backup_dest_port = config['backup-store']['port'] # backup_dest_port=''
    backup_dest_user = config['backup-store']['user'] # backup_dest_user=''
    backup_dest_pswd = config['backup-store']['password'] # backup_dest_pswd=''
    backup_base_dir = config['backup-store']['dir'] # backup_base_dir='/data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir'
    xtrabackup_bin_dir_config = config['misc'].get('xtrabackup-bin-dir')
    if xtrabackup_bin_dir_config is not None:
        xtrabackup_bin_dir = xtrabackup_bin_dir_config

    store_bad_backup_conf = config['misc'].get('store-bad-backup') # store_bad_backup_conf=None
    if store_bad_backup_conf is not None and (store_bad_backup_conf.lower() == '0' or store_bad_backup_conf.lower() == 'false'):
        store_bad_backup = False # store_bad_backup = True

    socat_port = int(config['misc']['socat-port']) # 45509
    socat_port2 = int(config['misc']['socat-port2']) # 45514
    using_scp = config['misc'].get('using-scp') # using_scp=None
    if using_scp is not None:
        using_socat = False # using_socat=True

    ftwrl_dead_lock_retry_config = config['misc'].get('ftwrl-dead-lock-retry') # ftwrl_dead_lock_retry_config=None
    ftwrl_dead_lock_seconds_config = config['misc'].get('ftwrl-dead-lock-seconds') # ftwrl_dead_lock_seconds_config=None
    if ftwrl_dead_lock_retry_config is not None:
        ftwrl_dead_lock_retry = int(ftwrl_dead_lock_retry_config)
    if ftwrl_dead_lock_seconds_config is not None:
        ftwrl_dead_lock_seconds = int(ftwrl_dead_lock_seconds_config)

    conn_timeout_str = config['misc'].get('conn-timeout') # conn_timeout_str=45000
    if conn_timeout_str is not None and conn_timeout_str.isdigit():
        conn_timeout = int(conn_timeout_str) # conn_timeout=45000
    if 'use-disaster-recovery-cluster' in config['misc'].keys() and (config['misc']['use-disaster-recovery-cluster'] == '1' or config['misc']['use-disaster-recovery-cluster'].lower() == 'true'):
        use_disaster_recovery_cluster = True # use_disaster_recovery_cluster=False
    print_trace_info(sys._getframe().f_code.co_name, "finish") # [TRACE 140533116843840 2024-02-22 09:36:41.528327] init_config_from_conf_file finish
```


### timing_to_stop_backup
> timing_to_stop_backup
>

```python
def timing_to_stop_backup():
    global add_command_info_lock
    global command_dict
    global backup_need_stop
    global timing_thread_sleep_time
    global backup_exec_timeout
    global mark_ok_flag
    global conn_timeout
    global backup_get_execption

    while True:
        if mark_ok_flag:
            break
        time.sleep(timing_thread_sleep_time)
        current_time = int(time.time())
        reload_backup_exec_timeout()
        if current_time - backup_start_time >= backup_exec_timeout or backup_get_execption:
            backup_need_stop = True
            print_trace_info("Set backup_need_stop, stop backup",'')
            add_command_info_lock.acquire()
            for id, cmd_info in command_dict.items():
                print_debug_info("killing command_id %d, command info %s" % (id, cmd_info.to_string()),'')
                conn = cmd_info.get_conn()
                try:
                    ret = conn.execute('ps ajx | grep "%s" | grep -v "grep"' % (cmd_info.get_cmd()), timeout=conn_timeout)[0]
                    print_debug_info(ret,'')
                    if len(ret) > 0 and len(ret.split()) > 3:
                        pid_to_kill = int(ret.split()[1])
                    print_debug_info("Prepare to kill process %d on host %s" % (pid_to_kill, cmd_info.get_host()),'')
                    ret = conn.execute('kill -9 %d' % (pid_to_kill), timeout=conn_timeout)[0]
                    print_debug_info("end to kill process %d on host %s" % (pid_to_kill, cmd_info.get_host()),'')
                except Exception as e:
                    print_err_info(e.args, "fail to do kill for command info %s. due to" % (cmd_info.to_string()))
            add_command_info_lock.release()
            return
    return
```


### do_backup

```python
def do_backup():
    global cluster_conn
    global cluster_cursor
    global cluster_config
    global backup_get_execption

    timing_stop_backup_thread = threading.Thread(target=timing_to_stop_backup)
    timing_stop_backup_thread.start()
    try:
        (cluster_conn, cluster_cursor) = get_database_conn(cluster_config)
        if log_audit == True:
            get_query_result_set('DBSCALE START BACKUP', cluster_cursor)
        get_db_cluster_info()
        pre_check()
        clean_old_bad_backup()
        confirm_backup_type()
        try_decrypt_sample()
        init_backup_dest_dir()
        backup_all_server()
        backup_global_consistence_point()
        backup_binlog_files()
        backup_one_master_server_conf_and_basedir()
        backup_one_dbscale_conf_and_basedir()
        backup_curr_backup_config()
        encrypt_backup_dir()
        mark_backup_dest_dir_with_ok_flag()
        if log_audit == True:
            get_query_result_set('DBSCALE END BACKUP', cluster_cursor)
        close_database_conn(cluster_conn, cluster_cursor)
        print_backup_conclusion_msg()
    except:
        print_trace_info('do_backup get an except, set backup_get_execption True', 'do_backup finish')
        backup_get_execption = True
        raise
    return not mark_ok_flag
```


```python
def get_database_conn(conn_config):
    conn = pymysql.connect(**conn_config)
    cursor = conn.cursor(OrderedDictCursor)
    get_query_result_set('select 1', cursor)
    return (conn, cursor)
```





### 获取集群信息：
> get_db_cluster_info

**命令：**
- dbscale show datasource type=replication / dbscale show datasource type=mgr
- dbscale show dataservers

**状态：**
- SourceStatus
- MasterSourceStatus
- MasterServerStatus
- SlaveServerStatus


```sql
mysql> dbscale show datasource type=replication\G
*************************** 1. row ***************************
                   Name: normal_0
                   Type: Replication
           SourceStatus: Working
    LoadBalanceStrategy: MASTER
           MasterSource: normal_0_normal_0_1
     MasterSourceStatus: Working
           MasterServer: normal_0_1
   MasterServerHostInfo: 172.17.134.76:16315
     MasterServerStatus: Server normal
       SelectMasterTime: 2024-02-20 16:12:12
            SlaveSource: normal_0_normal_0_0
      SlaveSourceStatus: Working
           SlaveServers: normal_0_0
    SlaveServerHostInfo: 172.17.134.71:16315
      SlaveServerStatus: Slave normal
SlaveExecutedGtidStatus: GTID good
*************************** 2. row ***************************
                   Name: normal_0
                   Type:
           SourceStatus:
    LoadBalanceStrategy:
           MasterSource:
     MasterSourceStatus:
           MasterServer:
   MasterServerHostInfo:
     MasterServerStatus:
       SelectMasterTime:
            SlaveSource: normal_0_normal_0_2
      SlaveSourceStatus: Working
           SlaveServers: normal_0_2
    SlaveServerHostInfo: 172.17.134.77:16315
      SlaveServerStatus: Slave normal
SlaveExecutedGtidStatus: GTID good
2 rows in set (0.01 sec)

mysql> dbscale show dataservers;
+------------+---------------+-------+------------------+---------------+----------------------+---------------+-------------+-------------+--------------------------------+-----------------+
| servername | host          | port  | username         | status        | master-online-status | master_backup | remote_user | remote_port | max_needed_conn/max_mysql_conn | master_priority |
+------------+---------------+-------+------------------+---------------+----------------------+---------------+-------------+-------------+--------------------------------+-----------------+
| normal_0_0 | 172.17.134.71 | 16315 | dbscale_internal | Slave normal  | Not_A_Master         | 1             | root        | 22          |                                | 100             |
| normal_0_1 | 172.17.134.76 | 16315 | dbscale_internal | Server normal | Master_Online        | 1             | root        | 22          |                                | 50              |
| normal_0_2 | 172.17.134.77 | 16315 | dbscale_internal | Slave normal  | Not_A_Master         | 1             | root        | 22          |                                | 100             |
+------------+---------------+-------+------------------+---------------+----------------------+---------------+-------------+-------------+--------------------------------+-----------------+
3 rows in set (0.00 sec)


```

**源码**
```python
def get_db_cluster_info():
    print_trace_info(sys._getframe().f_code.co_name, "start") # [TRACE 140177113528128 2024-02-22 09:48:26.205228] get_db_cluster_info start
    global replication_source_list
    global mgr_source_list
    global rep_source_ms_info
    global dataserver_info
    global curr_server_list

    try:
        query_text = "dbscale show datasource type=replication"
        replication_source_list = get_query_result_set(query_text, cluster_cursor)
        print_debug_info(replication_source_list, "get cluster datasource with type replication") # [DEBUG 140177113528128 2024-02-22 09:49:06.903245] get cluster datasource with type replication : [OrderedDict([('Name', 'normal_0'), ('Type', 'Replication'), ('SourceStatus', 'Working'), ('LoadBalanceStrategy', 'MASTER'), ('MasterSource', 'normal_0_normal_0_1'), ('MasterSourceStatus', 'Working'), ('MasterServer', 'normal_0_1'), ('MasterServerHostInfo', '172.17.134.76:16315'), ('MasterServerStatus', 'Server normal'), ('SelectMasterTime', '2024-02-20 16:12:12'), ('SlaveSource', 'normal_0_normal_0_0'), ('SlaveSourceStatus', 'Working'), ('SlaveServers', 'normal_0_0'), ('SlaveServerHostInfo', '172.17.134.71:16315'), ('SlaveServerStatus', 'Slave normal'), ('SlaveExecutedGtidStatus', 'GTID good')]), OrderedDict([('Name', 'normal_0'), ('Type', ''), ('SourceStatus', ''), ('LoadBalanceStrategy', ''), ('MasterSource', ''), ('MasterSourceStatus', ''), ('MasterServer', ''), ('MasterServerHostInfo', ''), ('MasterServerStatus', ''), ('SelectMasterTime', ''), ('SlaveSource', 'normal_0_normal_0_2'), ('SlaveSourceStatus', 'Working'), ('SlaveServers', 'normal_0_2'), ('SlaveServerHostInfo', '172.17.134.77:16315'), ('SlaveServerStatus', 'Slave normal'), ('SlaveExecutedGtidStatus', 'GTID good')])]
    except Exception as e:
        if (len(e.args) == 2):
            err_code, err_msg = e.args
            if err_code == 9004 and err_msg == 'No DataSource of the type.':
                print_debug_info("no datasource of type replication", "info")
            else:
                fatal_error(str(e))
#some column in below resultset was cut to fit screen width
#| Name   |SourceStatus |MasterSource     |MasterSourceStatus |MasterServer | MasterServerStatus | SlaveSource         | SlaveSourceStatus | SlaveServers | SlaveServerStatus |
#| auth   |Working      |auth_auth_server |Working            |auth_server  | Server normal      |                     |                   |              |                   |
#| rep    |Working      |rep_master       |Working            |master       | Server normal      | rep_read_slave1     | Working           | slave1       | Slave normal      |
#| rep    |             |                 |                   |             |                    | source1             | Working           |              |                   |
#| source1|Working      |source1_server1  |Working            |server1      | Slave normal       | source1_read_slave2 | Working           | slave2       | Slave normal      |
    for row in replication_source_list:
        source_name = row['Name'] # normal_0
        if len(row['SourceStatus']) > 0 and row['SourceStatus'] != 'Working':
            fatal_error("datasource [%s] is not Working right now" % source_name)
        if len(row['MasterSourceStatus']) > 0 and row['MasterSourceStatus'] != 'Working':
            fatal_error('MasterSourceStatus of datasource [%s] is not Working right now' % source_name)
        if len(row['MasterServerStatus']) > 0 and 'normal' not in row['MasterServerStatus']:
            fatal_error('MasterServerStatus of datasource [%s] is not normal right now' % source_name)
        #if len(row['SlaveSourceStatus']) > 0 and row['SlaveSourceStatus'] != 'Working': #TODO consider catching master
        #    fatal_error('SlaveSourceStatus of datasource [%s] is not Working right now' % source_name)
        if len(row['SlaveServerStatus']) > 0 and 'normal' not in row['SlaveServerStatus']:
            print_debug_info('slave: [%s] of datasource: [%s] is not working', '')
        if source_name not in rep_source_ms_info.keys():
            rep_source_ms_info[source_name] = {} # 第一次会执行
            rep_source_ms_info[source_name]['master'] = ''
            rep_source_ms_info[source_name]['slaves'] = []
        if len(row['MasterServer']) > 0:
            rep_source_ms_info[source_name]['master'] = row['MasterServer'] # 第一次会执行
        if len(row['SlaveServers']) > 0:
            rep_source_ms_info[source_name]['slaves'].append(row['SlaveServers']) # 第一次会执行，第二次也执行

    print_debug_info(rep_source_ms_info, "get datasource master-slave info") # [DEBUG 140177113528128 2024-02-22 09:54:00.882166] get datasource master-slave info : {'normal_0': {'master': 'normal_0_1', 'slaves': ['normal_0_0', 'normal_0_2']}}

    try:
        query_text = "dbscale show datasource type=mgr"
        mgr_source_list = get_query_result_set(query_text, cluster_cursor) # mgr_source_list=None
        print_debug_info(mgr_source_list, "get cluster datasource with type mgr")
        fatal_error("not support backup of cluster with MGR datasource yet") #TODO handle MGR
    except Exception as e:
        if (len(e.args) == 2):
            err_code, err_msg = e.args
            if err_code == 9004 and err_msg == 'No DataSource of the type.':
                print_debug_info("no datasource of type MGR", "") # [DEBUG 140177113528128 2024-02-22 09:56:16.187067] no datasource of type MGR
            else:
                fatal_error(str(e))

#| servername  | host      | port  | username | status        | master_backup | remote_user | remote_port | max_needed_conn/max_mysql_conn | master_priority |
#| auth_server | 127.0.0.1 | 13000 | root     | Server normal | 1             | root        | 22          |                                | 0               |
#| master      | 127.0.0.1 | 13000 | root     | Server normal | 1             | root        | 22          |                                | 0               |
#| server1     | 127.0.0.1 | 13003 | root     | Slave normal  | 1             | root        | 22          |                                | 0               |
#| slave1      | 127.0.0.1 | 13001 | root     | Slave normal  | 1             | root        | 22          |                                | 0               |
#| slave2      | 127.0.0.1 | 13002 | root     | Slave normal  | 1             | root        | 22          |                                | 0               |
    try:
        query_text = "dbscale show dataservers"
        dataserver_list = get_query_result_set(query_text, cluster_cursor)
        print_debug_info(dataserver_list, "get cluster dataserver list") # [DEBUG 140177113528128 2024-02-22 09:56:42.203488] get cluster dataserver list : [OrderedDict([('servername', 'normal_0_0'), ('host', '172.17.134.71'), ('port', '16315'), ('username', 'dbscale_internal'), ('status', 'Slave normal'), ('master-online-status', 'Not_A_Master'), ('master_backup', '1'), ('remote_user', 'root'), ('remote_port', '22'), ('max_needed_conn/max_mysql_conn', ''), ('master_priority', '100')]), OrderedDict([('servername', 'normal_0_1'), ('host', '172.17.134.76'), ('port', '16315'), ('username', 'dbscale_internal'), ('status', 'Server normal'), ('master-online-status', 'Master_Online'), ('master_backup', '1'), ('remote_user', 'root'), ('remote_port', '22'), ('max_needed_conn/max_mysql_conn', ''), ('master_priority', '50')]), OrderedDict([('servername', 'normal_0_2'), ('host', '172.17.134.77'), ('port', '16315'), ('username', 'dbscale_internal'), ('status', 'Slave normal'), ('master-online-status', 'Not_A_Master'), ('master_backup', '1'), ('remote_user', 'root'), ('remote_port', '22'), ('max_needed_conn/max_mysql_conn', ''), ('master_priority', '100')])]
    except Exception as e:
        fatal_error(str(e))

    for row in dataserver_list:
        server_name = row['servername'] # normal_0_0,normal_0_1,normal_0_2
        dataserver_info[server_name] = {}
        dataserver_info[server_name]['host'] = row['host'] # 172.17.134.71,172.17.134.76,172.17.134.77
        dataserver_info[server_name]['port'] = row['port'] # 16315,16315,16315
        dataserver_info[server_name]['status'] = row['status'] # 'Slave normal','Server normal','Slave normal'

    for data_source in rep_source_ms_info:
        master_server_name = rep_source_ms_info[data_source]['master'] # normal_0_1
        dataserver_info[master_server_name]['belong_to_datasource'] = data_source
        curr_server_list.append(master_server_name) # normal_0_1
        for slave in rep_source_ms_info[data_source]['slaves']:
            dataserver_info[slave]['belong_to_datasource'] = data_source
            curr_server_list.append(slave)

    for server in list(dataserver_info):
        if 'belong_to_datasource' not in dataserver_info[server]:
            dataserver_info.pop(server)

    parse_mysql_server_user_password_from_dbscale_conf()
    print_debug_info(curr_server_list, 'get current server list') # [DEBUG 140177113528128 2024-02-22 10:14:05.858344] get current server list : ['normal_0_1', 'normal_0_0', 'normal_0_2']
    print_debug_info(dataserver_info, "get dataserver info") # [DEBUG 140177113528128 2024-02-22 10:14:35.498882] get dataserver info : {'normal_0_0': {'host': '172.17.134.71', 'port': '16315', 'status': 'Slave normal', 'belong_to_datasource': 'normal_0'}, 'normal_0_1': {'host': '172.17.134.76', 'port': '16315', 'status': 'Server normal', 'belong_to_datasource': 'normal_0'}, 'normal_0_2': {'host': '172.17.134.77', 'port': '16315', 'status': 'Slave normal', 'belong_to_datasource': 'normal_0'}}
    print_trace_info(sys._getframe().f_code.co_name, "finish") # [TRACE 140177113528128 2024-02-22 10:14:38.922231] get_db_cluster_info finish

```

#### parse_mysql_server_user_password_from_dbscale_conf
```python
def parse_mysql_server_user_password_from_dbscale_conf():
    global cluster_cursor
    global dbscale_server_port
    global dbscale_server_user
    global dbscale_server_pswd

    get_query_result_set("dbscale flush all config to file '/tmp/dbscale_tmp.conf'", cluster_cursor)
    conn = check_server_connectivity("dbscale_server", cluster_config['host'], dbscale_server_port, dbscale_server_user, dbscale_server_pswd)
    lines = ''
    try:
        ret = conn.execute('cat /tmp/dbscale_tmp.conf')
        lines = ret[0]
        conn.close()
    except Exception as e:
        fatal_error('cluster server user do not have enough permission to backup dbscale config file')

    pattern_for_user = '\[data-server ([a-zA-Z0-9_]+)\]\n([^\n\[\]]*\n)*user = ([a-zA-Z0-9_]+)\n'
    pattern_for_password= '\[data-server ([a-zA-Z0-9_]+)\]\n([^\n\[\]]*\n)*password = (.+)\n'
    pattern_for_encode_password= 'encode-password([\t ]*)=([\t ]*)([0-1])\n'

    prog = re.compile(pattern_for_encode_password)
    res = prog.search(lines)
    is_encode_password = False
    if res is not None:
        pos = len(res.regs) - 1
        encode_password = lines[res.regs[pos][0]:res.regs[pos][1]]
        if encode_password == "1":
            is_encode_password = True

    prog = re.compile(pattern_for_user)
    res = prog.search(lines)
    while res is not None:
        data_server_name = lines[res.regs[1][0]:res.regs[1][1]]
        user = lines[res.regs[3][0]:res.regs[3][1]]
        dataserver_user_pswd_map[data_server_name] = {}
        dataserver_user_pswd_map[data_server_name]['user'] = user
        dataserver_user_pswd_map[data_server_name]['password'] = ''
        res = prog.search(lines, res.regs[0][1])

    prog = re.compile(pattern_for_password)
    res = prog.search(lines)
    while res is not None:
        data_server_name = lines[res.regs[1][0]:res.regs[1][1]]
        password = lines[res.regs[3][0]:res.regs[3][1]]

        if is_encode_password == True:
          password = base64.b64decode(password).decode("utf-8")

        dataserver_user_pswd_map[data_server_name]['password'] = password
        res = prog.search(lines, res.regs[0][1])
```



### 检查
> pre_check
>
- dbscale show datasource type = replication
- dbscale show datasource type=server

```sql
mysql> dbscale show datasource type = replication\G
*************************** 1. row ***************************
                   Name: normal_0
                   Type: Replication
           SourceStatus: Working
    LoadBalanceStrategy: MASTER
           MasterSource: normal_0_normal_0_1
     MasterSourceStatus: Working
           MasterServer: normal_0_1
   MasterServerHostInfo: 172.17.134.76:16315
     MasterServerStatus: Server normal
       SelectMasterTime: 2024-02-20 16:12:12
            SlaveSource: normal_0_normal_0_0
      SlaveSourceStatus: Working
           SlaveServers: normal_0_0
    SlaveServerHostInfo: 172.17.134.71:16315
      SlaveServerStatus: Slave normal
SlaveExecutedGtidStatus: GTID good
*************************** 2. row ***************************
                   Name: normal_0
                   Type:
           SourceStatus:
    LoadBalanceStrategy:
           MasterSource:
     MasterSourceStatus:
           MasterServer:
   MasterServerHostInfo:
     MasterServerStatus:
       SelectMasterTime:
            SlaveSource: normal_0_normal_0_2
      SlaveSourceStatus: Working
           SlaveServers: normal_0_2
    SlaveServerHostInfo: 172.17.134.77:16315
      SlaveServerStatus: Slave normal
SlaveExecutedGtidStatus: GTID good
2 rows in set (0.01 sec)

mysql> dbscale show datasource type=server;
+---------------------+--------+------------+---------------------+---------------+--------------+-------------+
| Name                | Type   | Server     | ServerHostInfo      | ServerStatus  | SourceStatus | Is-internal |
+---------------------+--------+------------+---------------------+---------------+--------------+-------------+
| normal_0_normal_0_0 | Server | normal_0_0 | 172.17.134.71:16315 | Slave normal  | Working      | YES         |
| normal_0_normal_0_1 | Server | normal_0_1 | 172.17.134.76:16315 | Server normal | Working      | YES         |
| normal_0_normal_0_2 | Server | normal_0_2 | 172.17.134.77:16315 | Slave normal  | Working      | YES         |
+---------------------+--------+------------+---------------------+---------------+--------------+-------------+
3 rows in set (0.00 sec)

```

```python
def pre_check():
    global use_disaster_recovery_cluster
    global cluster_dr_exclude_server
    global dataserver_info
    global xtrabackup_bin_dir
    print_trace_info(sys._getframe().f_code.co_name, "start") # [TRACE 140177113528128 2024-02-22 10:16:16.701359] pre_check start

    #check_db_cluster_status
    if use_disaster_recovery_cluster ==True: # use_disaster_recovery_cluster=False
        query_text = "dbscale show datasource type = replication"
        server_datasource = get_query_result_set(query_text, cluster_cursor)
        dis_recv_cluster_exclude_server = []
        for datasource in server_datasource:
            try:
                if datasource['SlaveDBScale'] == "Yes":
                    dis_recv_cluster_exclude_server.append(datasource['SlaveSource'])
                    continue
            except Exception as e:
                fatal_error('The column SlaveDBScale not exists, plz check backup cluster is disaster recovery cluster')
        if len(dis_recv_cluster_exclude_server) == 0:
            fatal_error('The disaster recovery cluster has no backup server')
        elif len(dis_recv_cluster_exclude_server) > 1:
            fatal_error('The disaster recovery cluster backup server more than 1')
        cluster_dr_exclude_source = dis_recv_cluster_exclude_server[0]

    query_text = "dbscale show datasource type=server"
    server_datasource = get_query_result_set(query_text, cluster_cursor)
    print_debug_info(server_datasource, "get cluster datasource with type server") # [DEBUG 140177113528128 2024-02-22 10:17:15.742503] get cluster datasource with type server : [OrderedDict([('Name', 'normal_0_normal_0_0'), ('Type', 'Server'), ('Server', 'normal_0_0'), ('ServerHostInfo', '172.17.134.71:16315'), ('ServerStatus', 'Slave normal'), ('SourceStatus', 'Working'), ('Is-internal', 'YES')]), OrderedDict([('Name', 'normal_0_normal_0_1'), ('Type', 'Server'), ('Server', 'normal_0_1'), ('ServerHostInfo', '172.17.134.76:16315'), ('ServerStatus', 'Server normal'), ('SourceStatus', 'Working'), ('Is-internal', 'YES')]), OrderedDict([('Name', 'normal_0_normal_0_2'), ('Type', 'Server'), ('Server', 'normal_0_2'), ('ServerHostInfo', '172.17.134.77:16315'), ('ServerStatus', 'Slave normal'), ('SourceStatus', 'Working'), ('Is-internal', 'YES')])]
    for datasource in server_datasource: # 正常时，以下均不执行
        if datasource['Is-internal'] != "YES":
            if use_disaster_recovery_cluster ==True and cluster_dr_exclude_source == datasource['Name']:
                cluster_dr_exclude_server = datasource['Server']
                continue
            else:
                fatal_error('do not support config datasource of type server, related server datasource is [%s]' % datasource['Name'])

    # check connectivity to mysql and server
    for ms in rep_source_ms_info:
        master_server_name = rep_source_ms_info[ms]['master'] # normal_0_1
        master_server = dataserver_info[master_server_name] # 'host':'172.17.134.76' 'port':'16315' 'status':'Server normal' 'belong_to_datasource':'normal_0'
        conn = check_server_connectivity(master_server_name, master_server['host'], dbscale_server_port, dbscale_server_user, dbscale_server_pswd)
        if conn is not None:
            conn.close()
        (conn, cursor) = check_mysql_connectivity(master_server_name, master_server['host'], master_server['port'], dataserver_user_pswd_map[master_server_name]['user'], dataserver_user_pswd_map[master_server_name]['password'])
        if cursor is not None:
            check_gtid_mode(conn, cursor)
        if conn is not None:
            conn.close()
        check_server_executable_tool(xtrabackup_bin_dir, master_server_name, master_server['host'], dbscale_server_port, dbscale_server_user, dbscale_server_pswd)
        find_xtrabackup_version(xtrabackup_bin_dir, master_server_name, master_server['host'], dbscale_server_port, dbscale_server_user, dbscale_server_pswd)
        slave_names = rep_source_ms_info[ms]['slaves'] # normal_0_0 normal_0_2
        for slave_name in slave_names:
            slave = dataserver_info[slave_name]
            conn = check_server_connectivity(slave_name, slave['host'], dbscale_server_port, dbscale_server_user, dbscale_server_pswd)
            if conn is not None:
                conn.close()
            (conn, cursor) = check_mysql_connectivity(slave_name, slave['host'], slave['port'], dataserver_user_pswd_map[slave_name]['user'], dataserver_user_pswd_map[slave_name]['password'])
            if conn is not None:
                check_gtid_mode(conn, cursor)
                conn.close()
            check_server_executable_tool(xtrabackup_bin_dir, master_server_name, master_server['host'], dbscale_server_port, dbscale_server_user, dbscale_server_pswd)

    # check connectivity to backup store server
    conn = check_server_connectivity('backup_store_server', backup_dest_ip, backup_dest_port, backup_dest_user, backup_dest_pswd) # 172.17.134.76
    if conn is None:
        fatal_error("can't connect to backup store server %s %s %s %s" % backup_dest_ip, backup_dest_port, backup_dest_user, backup_dest_pswd, '')
    else:
        conn.close()
    if args.password is not None: # args.password=None
        check_server_executable_tool('pyAesCrypt', 'backup_store_server', backup_dest_ip, backup_dest_port, backup_dest_user, backup_dest_pswd)

    # check server connectivity to dbscale server
    conn = check_server_connectivity("dbscale_server", cluster_config['host'], dbscale_server_port, dbscale_server_user, dbscale_server_pswd)
    if conn is None:
        fatal_error("can't connect to dbscale server %s %s %s %s" % cluster_config['host'], dbscale_server_port, dbscale_server_user, dbscale_server_pswd)
    else:
        conn.close()

    print_trace_info(sys._getframe().f_code.co_name, "finish") # [TRACE 140177113528128 2024-02-22 11:34:35.324789] pre_check finish
```

#### check_server_connectivity
```python
def check_server_connectivity(server_name, host, port, user, pswd):
    conn = get_one_server_conn(host, user, port, pswd, server_name == 'backup_store_server')
    got_err = False
    mes = ''
    try:
        ret = conn.execute('echo 1')
        if ret[1] != '':
            mes = ret[1]
            got_err = True
    except Exception as e:
        got_err = True
        mes = e.args
    if got_err:
        print_debug_info("can't connect to server %s, port %s" % (host, port), mes)
        connect_failed_servers.add(server_name)
        return None
    return conn
```
#### check_mysql_connectivity
```python
def check_mysql_connectivity(server_name, host, port, user, pswd):
    db_conn_config = {
        'host': host,
        'user': user,
        'password': pswd,
        'port': int(port),
        'db': 'information_schema',
        'charset': "utf8",
        'autocommit': True
    }
    conn = None
    try:
        conn = pymysql.connect(**db_conn_config)
    except Exception as e:
        print_debug_info("can't connect to dataserver on %s port %s user %s pswd %s cause: %s" % (host, port, user, pswd, e.args), '')
        connect_failed_servers.add(server_name)
        return (None, None)
    return (conn, conn.cursor(OrderedDictCursor))
```
#### check_gtid_mode

```python
def check_gtid_mode(conn, cursor):
    query_text = "show variables like 'gtid_mode'"
    gtid_mode = get_query_result_set(query_text, cursor)
    if gtid_mode[0]['Value'] != 'ON':
        fatal_error('dataserver must started with gtid_mode on')
```
#### check_server_executable_tool

```python
def check_server_executable_tool(cmd, server_name, host, port, user, pswd):
    conn = get_one_server_conn(host, user, port, pswd, server_name == 'backup_store_server')
    got_err = False
    try:
        ret = conn.execute('which %s' % (cmd)) # /bin/xtrabackup
        if ret[1] != '':
            got_err = True
    except Exception as e:
        got_err = True
    conn.close()
    if got_err:
        fatal_error("%s does not exists in /usr/bin." % (cmd))
```

#### find_xtrabackup_version
```python
def find_xtrabackup_version(cmd, server_name, host, port, user, pswd):
    global xtrabackup_version
    conn = get_one_server_conn(host, user, port, pswd, server_name == 'backup_store_server')
    got_err = False
    try:
        ret = conn.execute('%s --version' % (cmd)) # 'xtrabackup: recognized server arguments: --datadir=/var/lib/mysql \nxtrabackup version 8.0.26-18 based on MySQL server 8.0.26 Linux (x86_64) (revision id: 4aecf8219ca)\n'
        if ret is not None:
            target = ret[0]
            if target == '':
                target = ret[1]
            v_s = target.find("MySQL server") + len("MySQL server ") # 118
            v_e = target.find(" Linux") # 124
            v_str = target[v_s:v_e] # 8.0.26
            v_list = v_str.split(".")
            xtrabackup_version = int(v_list[0])*1000 + int(v_list[1])*100 + int(v_list[2]) # 8026
            print_trace_info("got xtrabackup version: %s and num:%d" % (v_str, xtrabackup_version), '') # [TRACE 140177113528128 2024-02-22 11:31:10.827273] got xtrabackup version: 8.0.26 and num:8026
        else:
            got_err = True
    except Exception as e:
        print_trace_info('', str(e))
        got_err = True
    conn.close()
    if got_err:
        fatal_error("%s does not exists in /usr/bin." % (cmd))
```


### 清理旧的坏备份
> clean_old_bad_backup
>

**encrypted  socat_log  trash  UTC20240220093304.784-full  UTC20240220160004.835-inc**
- mkdir -p /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir
- mkdir -p /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/trash
- mkdir -p /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240221
- cat /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240220093304.784-full/ok
- 若不ok，则 mv /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240220093304.784-full /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240220093304.784-full/trash



```python
def clean_old_bad_backup():
    if is_backup_need_stop():
        return
    print_trace_info(sys._getframe().f_code.co_name, "start") # [TRACE 140177113528128 2024-02-22 11:35:33.437922] clean_old_bad_backup start
    global last_good_backup
    global backup_base_dir
    global socat_log_dir
    global store_bad_backup

    dest_server_conn = get_one_dest_server_conn()
    dest_server_conn.execute("mkdir -p %s" % (backup_base_dir)) # mkdir -p /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir
    dest_server_conn.execute("mkdir -p %s/trash" % (backup_base_dir)) # mkdir -p /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/trash
    # create socat log file directory
    tmp_time = time.strftime("%Y%m%d", time.localtime()) # 20240222
    socat_log_dir = backup_base_dir + '/socat_log/' + tmp_time # /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240222
    dest_server_conn.execute("mkdir -p %s" % (socat_log_dir)) # mkdir -p /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240222
    ret = dest_server_conn.execute("ls -t %s" % (backup_base_dir)) #only show directories
    old_backup_list = ret[0].split('\n')[0:-1] #remove the last empty string made by '\n'
# 00:'UTC20240222011253.698-full'
# 01:'UTC20240221160004.855-full'
# 02:'socat_log'
# 03:'UTC20240221084144.864-full'
# 04:'UTC20240221080443.619-full'
# 05:'trash'
# 06:'UTC20240221031122.943-full'
# 07:'UTC20240220160004.835-inc'
# 08:'UTC20240220093304.784-full'
# 09:'UTC20240220082254.590-full'
# 10:'UTC20240206085424.236-full'
# 11:'UTC20240206081826.526-full'
# 12:'encrypted'
    old_backup_list.sort(reverse=True)
    print_debug_info(old_backup_list, 'get old backup directories list') # [DEBUG 140177113528128 2024-02-22 11:39:46.120027] get old backup directories list : ['trash', 'socat_log', 'encrypted', 'UTC20240222011253.698-full', 'UTC20240221160004.855-full', 'UTC20240221084144.864-full', 'UTC20240221080443.619-full', 'UTC20240221031122.943-full', 'UTC20240220160004.835-inc', 'UTC20240220093304.784-full', 'UTC20240220082254.590-full', 'UTC20240206085424.236-full', 'UTC20240206081826.526-full']
    for elem in old_backup_list:
        if elem == 'trash' or elem == 'encrypted' or elem == 'socat_log':
            continue
        try:
            ret = dest_server_conn.execute('cat %s/%s/ok' % (backup_base_dir, elem)) # cat /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240222011253.698-full/ok
        except Exception as e:
            pass
        if ret[0] == 'ok\n':
            last_good_backup = elem # UTC20240222011253.698-full
            break
        print_debug_info(elem, 'get a bad old backup, move it to trash dir or delete it')
        global conn_timeout
        if store_bad_backup:
            dest_server_conn.execute('mv {0}/{1} {0}/trash'.format(backup_base_dir, elem), timeout=conn_timeout)
        else:
            print_debug_info(elem, 'store_bad_backup is false, rm bad backup')
            dest_server_conn.execute('rm -rf {0}/{1}'.format(backup_base_dir, elem), timeout=conn_timeout)
        try:
            if store_bad_backup:
                dest_server_conn.execute('mv {0}/encrypted/{1}.tar.encrypted {0}/trash'.format(backup_base_dir, elem), timeout=conn_timeout)
            else:
                dest_server_conn.execute('rm -rf {0}/encrypted/{1}.tar.encrypted'.format(backup_base_dir, elem), timeout=conn_timeout)
        except:
            pass
    dest_server_conn.close()
    if len(last_good_backup) > 0:
        print_debug_info(last_good_backup, 'get last good backup dir') # [DEBUG 140177113528128 2024-02-22 11:42:12.721490] get last good backup dir : UTC20240222011253.698-full
    else:
        print_debug_info('not find last good backup dir', '')
    print_trace_info(sys._getframe().f_code.co_name, "finish") # [TRACE 140177113528128 2024-02-22 11:42:32.799587] clean_old_bad_backup finish

```

### 确认备份类型

> confirm_backup_type



```python
def confirm_backup_type():
    if is_backup_need_stop():
        return
    print_trace_info(sys._getframe().f_code.co_name, "start") # [TRACE 140177113528128 2024-02-22 12:19:06.842370] confirm_backup_type start
    global backup_type
    global last_good_backup
    global backup_base_dir
    global backup_dest_dir
    global backup_dest_user
    global backup_dest_ip
    global backup_dest_port
    global curr_server_list
    global skipped_servers

    if len(last_good_backup) == 0 or backup_type == 'full': # last_good_backup=UTC20240222011253.698-full backup_type == 'full'
        make_full_backup("this is a full backup")
        print_trace_info(sys._getframe().f_code.co_name, "finish")
        return

    dest_server_conn = get_one_dest_server_conn()
    last_backup_pos_info = ''
    ret = dest_server_conn.execute("cat %s/%s/cluster_consistence_info" % (backup_base_dir, last_good_backup))
    last_backup_pos_info = ret[0]
    print_debug_info(last_backup_pos_info, 'get last good backup pos info')
    lines = last_backup_pos_info[:-1].split('\n')
    servers_last_good_backup_backuped = []
    for line in lines:
        info = line.split('\t')
        if info[1] in dataserver_info:
            servers_last_good_backup_backuped.append(info[1])
#['192.168.122.110:13004', 'auth_server', 'master-bin.000001', '724', 'belong-to-datasource=auth', 'xb-to-lsn=1359021', 'xb-to-binlog-name=master-bin.000001', 'xb-to-binlog-pos=724']
            dataserver_info[info[1]]['old-xb-to-lsn'] = info[5][len('xb-to-lsn='):]
    adjust_skipped_servers(servers_last_good_backup_backuped)

    if backup_master == True:
        for server in curr_server_list:
            if is_master_server(server) and server not in servers_last_good_backup_backuped:
                make_full_backup("A master-slave switchover occurs, and the current master %s was not backed up last time, run current job as a full backup" % (server))
                print_trace_info(sys._getframe().f_code.co_name, "finish")
                return

    handling_datasource = set()
    for server in curr_server_list:
        if is_skipped(server) or server in skipped_servers:
            continue
        if server not in servers_last_good_backup_backuped:
            continue
        handling_datasource.add(dataserver_info[server]['belong_to_datasource'])
    if len(handling_datasource) != len(rep_source_ms_info):
        make_full_backup("not all replication datasource covered in this backup job, run current job as a full backup")
        print_trace_info(sys._getframe().f_code.co_name, "finish")
        return

    if len(servers_last_good_backup_backuped):
        for server in curr_server_list:
            if is_skipped(server) or server in skipped_servers:
                continue
            if server not in servers_last_good_backup_backuped:
                make_full_backup("server %s not in last backup job:%s, run current job as a full backup" % (server, last_good_backup))
                print_trace_info(sys._getframe().f_code.co_name, "finish") # [TRACE 140177113528128 2024-02-22 12:30:57.414906] confirm_backup_type finish
                return

    check_if_datasource_with_no_server_to_backup()
    print_debug_info("this is an incremental backup", '')
    print_trace_info(sys._getframe().f_code.co_name, "finish")
```

#### make_full_backup
```python
def make_full_backup(debug_info):
    global skipped_servers
    global curr_server_list
    global backup_type

    backup_type = 'full'
    skipped_servers.clear()
    adjust_skipped_servers(curr_server_list) # normal_0_1 normal_0_0 normal_0_2
    print_debug_info(debug_info, '') # [DEBUG 140177113528128 2024-02-22 12:30:30.029988] this is a full backup

```

#### adjust_skipped_servers

```python
def adjust_skipped_servers(servers_last_good_backup_backuped):
    global curr_server_list # normal_0_1 normal_0_0 normal_0_2
    global skipped_servers
    global backup_master
    global backup_slave_num
    backup_server_num_of_ds = {}
    for server in curr_server_list:
        if server not in servers_last_good_backup_backuped:
            skipped_servers.add(server)
            print_debug_info('dataserver %s skipped cause it is not backed up in last backup' % (server), '')
            continue
        is_master = is_master_server(server)
        if is_master:
            if not backup_master:
                datasource_name = dataserver_info[server]['belong_to_datasource'] # normal_0
                master_server = rep_source_ms_info[datasource_name]['master'] # normal_0_1
                skipped_servers.add(server) # normal_0_1
                print_debug_info('dataserver %s skipped because backup-master=0 and the master server of datasource %s is %s' \
                        % (server, datasource_name, master_server), '') # [DEBUG 140177113528128 2024-02-22 12:24:12.938105] dataserver normal_0_1 skipped because backup-master=0 and the master server of datasource normal_0 is normal_0_1
                continue
        else:
            if backup_slave_num is None: ## use backup slave list
                if not backup_slaves.issuperset({server}):
                    skipped_servers.add(server)
            else: ## use backup slave num
                ##one slave server status abnormal backup other slave server
                if dataserver_info[server]['status'] != "Slave normal":
                   print_debug_info('dataserver %s status is not Slave normal' % (server), '')
                   skipped_servers.add(server)
                   continue
                ds_name = dataserver_info[server]['belong_to_datasource'] # normal_0
                backup_num_of_ds = backup_server_num_of_ds.get(ds_name)
                should_backup = False # should_backup = False
                if backup_num_of_ds is None:
                    if backup_slave_num > 0: # backup_slave_num=1
                        should_backup = True # should_backup = True
                        backup_server_num_of_ds[ds_name] = 1
                else:
                    if backup_num_of_ds < backup_slave_num:
                        should_backup = True
                        backup_server_num_of_ds[ds_name] += 1
                if not should_backup:
                    print_debug_info("slave dataserver %s of datasource %s skipped" % (server, ds_name), '') # [DEBUG 140177113528128 2024-02-22 12:27:01.119122] slave dataserver normal_0_2 of datasource normal_0 skipped
                    skipped_servers.add(server)

    going_to_backup_servers = {}
    going_to_be_skipped_servers = {}
    for server in curr_server_list:
        ds_name = dataserver_info[server]['belong_to_datasource'] # normal_0
        if not skipped_servers.issuperset({server}):
            if going_to_backup_servers.get(ds_name) is None:
                going_to_backup_servers[ds_name] = []
            going_to_backup_servers[ds_name].append(server)
            print_debug_info('ds: [%s] server: [%s] going to be backup' % (ds_name, server),'') # [DEBUG 140177113528128 2024-02-22 12:28:48.883237] ds: [normal_0] server: [normal_0_0] going to be backup
        else:
            if going_to_be_skipped_servers.get(ds_name) is None:
                going_to_be_skipped_servers[ds_name] = []
            going_to_be_skipped_servers[ds_name].append(server)
            print_debug_info('ds: [%s] server: [%s] going to be skipped' % (ds_name, server),'') # [DEBUG 140177113528128 2024-02-22 12:28:05.571993] ds: [normal_0] server: [normal_0_1] going to be skipped [DEBUG 140177113528128 2024-02-22 12:29:41.960152] ds: [normal_0] server: [normal_0_2] going to be skipped
```


### 加密

> try_decrypt_sample

```python
def try_decrypt_sample():
    global connect_method # connect_method=1
    if is_backup_need_stop():
        return
    print_trace_info('checking password', "start") # [TRACE 140177113528128 2024-02-22 12:32:53.351661] checking password start
    global backup_base_dir
    global last_good_backup

    if len(last_good_backup) == 0:
        return
    last_good_backup_dir = backup_base_dir + '/' + last_good_backup # /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240222011253.698-full
    dest_server_conn = get_one_dest_server_conn() # 172.17.134.76
    err = False
    try: # ret=0
        ret = dest_server_conn.execute('ls %s | grep encrypted_sample' % (last_good_backup_dir)) # ls /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240222011253.698-full | grep encrypted_sample
        if ret[1] != '':
            err = True
        if ret[0] == '' and connect_method == 2:
            err = True
    except:
        err = True # err = True

    if args.password is not None and err:
        fatal_error("last backup didn't use a password, can't use password any more")
    elif args.password is None and not err:
        fatal_error('last backup used a password, must use the same password too')
    if args.password is not None:
        try:
            global conn_timeout
            dest_server_conn.execute('cd %s && pyAesCrypt -p %s -d ./encrypted_sample -o sample' % (last_good_backup_dir, args.password), timeout=conn_timeout)
            dest_server_conn.execute('rm -f %s/sample' % (last_good_backup_dir), timeout=30)
        except Exception as e:
            fatal_error('wrong password')

    print_trace_info('checking password', "finish") # [TRACE 140177113528128 2024-02-22 12:35:33.492514] checking password finish

```

### 初始化备份目录
> init_backup_dest_dir
>
- mkdir -p /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/encrypted
- mkdir -p /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240220093304.784-full
- mkdir -p /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240220093304.784-full/datanode_base
- mkdir -p /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240220093304.784-full/calcnode_base

```python
def init_backup_dest_dir():
    if is_backup_need_stop():
        return
    print_trace_info(sys._getframe().f_code.co_name, "start") # [TRACE 140177113528128 2024-02-22 12:35:52.598239] init_backup_dest_dir start
    global backup_dest_user
    global backup_dest_ip
    global backup_dest_port
    global backup_dest_dir
    global backup_base_dir
    global backup_id
    global backup_type

    backup_dest_dir = backup_base_dir + '/' + backup_id + '-' + backup_type
    print_trace_info("current backup folder: %s" % backup_dest_dir, '') # [TRACE 140177113528128 2024-02-22 12:36:16.209456] current backup folder: /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240222014345.236-full
    dest_server_conn = get_one_dest_server_conn() # 172.17.134.76
    dest_server_conn.execute("mkdir -p %s/encrypted" % (backup_base_dir)) # mkdir -p /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/encrypted
    dest_server_conn.execute("mkdir -p %s" % (backup_dest_dir)) # mkdir -p /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240222014345.236-full
    dest_server_conn.execute("mkdir -p %s" % (backup_dest_dir + '/datanode_base')) # mkdir -p /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240222014345.236-full/datanode_base
    dest_server_conn.execute("mkdir -p %s" % (backup_dest_dir + '/calcnode_base')) # mkdir -p /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240222014345.236-full/calcnode_base
    print_debug_info(backup_dest_user + "@" + backup_dest_ip + ":" + backup_dest_port + ":" + backup_dest_dir, "get current backup dest dir") # [DEBUG 140177113528128 2024-02-22 12:38:32.649821] get current backup dest dir : @172.17.134.76::/data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240222014345.236-full
    dest_server_conn.close()
    print_trace_info(sys._getframe().f_code.co_name, "finish") # [TRACE 140177113528128 2024-02-22 12:38:52.917886] init_backup_dest_dir finish
```

### 执行xtrabackup
> backup_all_server
>

- show slave status (is slave)
- show global variables like 'pid_file'
- show global variables like 'datadir'
- cat /data/mysqldata/16315fff-d9ca-4adb-9be1-578ff4626394/pid/mysqld.pid
- ps -ef | grep 6056 | grep -v '_safe' | grep -v 'grep'
- db_conf_file=/data/mysqldata/16315fff-d9ca-4adb-9be1-578ff4626394/my16315.conf
- socat -d -lf /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240221/45514_2 -u tcp-listen:45514,reuseaddr file:/data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240221031122.943-full/normal_0-normal_0_0-172.17.134.71-16315/my.conf,trunc,creat
- cat /data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/my16315.conf | socat - TCP4:172.17.134.76:45514,retry=10
- show global variables like 'tmpdir'
- socat -d -lf /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240221/45509_1 -u tcp-listen:45509,reuseaddr stdout > /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240221031122.943-full/normal_0-normal_0_0-172.17.134.71-16315/backup.xbsteam &
- xtrabackup --defaults-file=/data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/my16315.conf --user=dbscale_internal --password=123456 --host=172.17.134.71 --port=16315 --backup --tmpdir=/data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/tmp  --stream=xbstream  2>/data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/tmp/UTC20240221031122.943-normal_0-normal_0_0-172.17.134.71-16315 | socat - TCP4:172.17.134.76:45509,retry=10
- cd /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240221031122.943-full/normal_0-normal_0_0-172.17.134.71-16315 && echo xxx > /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240221031122.943-full/normal_0-normal_0_0-172.17.134.71-16315/executed_gtids



```sql
mysql> show global variables like 'pid_file';
+---------------+---------------------------------------------------------------------+
| Variable_name | Value                                                               |
+---------------+---------------------------------------------------------------------+
| pid_file      | /data/mysqldata/16315fff-d9ca-4adb-9be1-578ff4626394/pid/mysqld.pid |
+---------------+---------------------------------------------------------------------+
1 row in set (0.02 sec)

mysql> show global variables like 'datadir';
+---------------+--------------------------------------------------------------+
| Variable_name | Value                                                        |
+---------------+--------------------------------------------------------------+
| datadir       | /data/mysqldata/16315fff-d9ca-4adb-9be1-578ff4626394/dbdata/ |
+---------------+--------------------------------------------------------------+
1 row in set (0.01 sec)

[root@node76 ~]# cat /data/mysqldata/16315fff-d9ca-4adb-9be1-578ff4626394/pid/mysqld.pid
6056

[root@node76 ~]# ps -ef | grep 6056 | grep -v '_safe' | grep -v 'grep'
mysql     6056  3448  1 Feb06 ?        05:35:35 /data/app/mysql-8.0.26/bin/mysqld --defaults-file=/data/mysqldata/16315fff-d9ca-4adb-9be1-578ff4626394/my16315.conf --basedir=/data/app/mysql-8.0.26 --datadir=/data/mysqldata/16315fff-d9ca-4adb-9be1-578ff4626394/dbdata --plugin-dir=/data/app/mysql-8.0.26/lib/plugin --log-error=/data/mysqldata/16315fff-d9ca-4adb-9be1-578ff4626394/logfile/mysqld.err --open-files-limit=1024000 --pid-file=/data/mysqldata/16315fff-d9ca-4adb-9be1-578ff4626394/pid/mysqld.pid --socket=/data/mysqldata/16315fff-d9ca-4adb-9be1-578ff4626394/socket/mysql.sock --port=16315

mysql> show global variables like 'tmpdir';
+---------------+----------------------------------------------------------+
| Variable_name | Value                                                    |
+---------------+----------------------------------------------------------+
| tmpdir        | /data/mysqldata/16315fff-d9ca-4adb-9be1-578ff4626394/tmp |
+---------------+----------------------------------------------------------+
1 row in set (0.00 sec)

```



```python
def backup_all_server():
    if is_backup_need_stop():
        return
    print_trace_info(sys._getframe().f_code.co_name, "start") # [TRACE 140177113528128 2024-02-22 12:39:14.623780] backup_all_server start
    global backup_get_execption
    global dataserver_info # normal_0_0 'host':'172.17.134.71''port':'16315''status':'Slave normal' 'belong_to_datasource':'normal_0'  normal_0_1:172.17.134.76 'status':'Server normal'
    global backup_master
    global ftwrl_server_dict
    global ftwrl_locks
    global detect_ftwrl_lock_result
    global ftwrl_status
    ths = []
    ftwrl_thread_id = 0
    for server in dataserver_info:
        server_host = dataserver_info[server]['host'] # 172.17.134.71
        server_port = dataserver_info[server]['port'] # 16315
        server_str = server_host + ":" + str(server_port) # 172.17.134.71:16315
        ftwrl_server_dict[server_str] = ftwrl_thread_id # 172.17.134.71:16315 ftwrl_thread_id=1
        ftwrl_thread_id += 1
        tmp_lock = threading.Lock()
        ftwrl_locks.append(tmp_lock)
        detect_ftwrl_lock_result.append(enum_detect_ftwrl_lock_result['DETECT_FTWRL_RESULT_UNDETECT'])
        ftwrl_status.append(enum_ftwrl_status['XTRABACKUP_UNEXECUTE'])
        if is_backup_need_stop() or backup_get_execption:
            break
        if is_skipped(server):
            print_debug_info("server: %s skipped" % server, '')
            continue
        if is_master_server(server) and not backup_master:
            make_server_backup_dir(server)
            print_debug_info("server: %s skipped" % server, '')
            continue
        #backup_one_host(server)
        th = threading.Thread(target=backup_one_host, args=(server,))
        th.start()
        ths.append(th)
    print_trace_info(sys._getframe().f_code.co_name, "in-progress")
    for th in ths:
        th.join()
    print_trace_info(sys._getframe().f_code.co_name, "finish")

```

#### backup_one_host
```python
def backup_one_host(server_name):
    if is_backup_need_stop():
        return
    print_trace_info("%s(%s)" % (sys._getframe().f_code.co_name, threading.get_ident()), "start") # [TRACE 140174436591360 2024-02-22 12:47:04.195368] backup_one_host(140174436591360) start
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
      host = dataserver_info[server_name]['host'] # 172.17.134.71
      db_port = dataserver_info[server_name]['port'] # 16315
      belong_to_datasource = dataserver_info[server_name]['belong_to_datasource'] # normal_0

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
      backupfile_dir_name = belong_to_datasource + '-' + server_name + '-' + host + '-' + db_port # normal_0-normal_0_0-172.17.134.71-16315
      (is_slave, command_id, th) = clean_before_rextrabackup(dest_server_conn, server_name, backupfile_dir_name, belong_to_datasource)
      if th == None:
          dest_server_conn.close()
          return

      db_conn_config = {
          'host': host, # 172.17.134.71
          'user': dataserver_user_pswd_map[server_name]['user'], # dbscale_internal
          'password': dataserver_user_pswd_map[server_name]['password'], #
          'port': int(db_port), # 16315
          'db': 'information_schema',
          'charset': "utf8",
          'autocommit': True
      }
      conn = get_one_server_conn(host, dbscale_server_user, dbscale_server_port, dbscale_server_pswd)

      (db_conn, db_cursor) = get_database_conn(db_conn_config)
      if is_slave:
          query_text = 'show slave status'
          slave_status = get_query_result_set(query_text, db_cursor)
          slave_binlog_file[server_name] = slave_status[0]['Relay_Master_Log_File'] # normal_0_0 'greatdb-bin.000041'
          slave_binlog_exec_pos[server_name] = slave_status[0]['Exec_Master_Log_Pos'] # 'normal_0_0':631

      query_text = "show global variables like 'pid_file'"
      pid_file = get_query_result_set(query_text, db_cursor)[0]['Value'] #  /data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/pid/mysqld.pid
      query_text = "show global variables like 'datadir'"
      datadir = get_query_result_set(query_text, db_cursor)[0]['Value'] # /data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/dbdata/
      if not pid_file.startswith('/'):
          pid_file = datadir+'/'+pid_file

      pid_value = conn.execute('cat %s' % pid_file)[0].strip('\n') # 17177
      #print_debug_info(pid_value, "get pid value of %s:%s" % (host, db_port))
      cmd = "ps -ef | grep %s | grep -v '_safe' | grep -v 'grep'" % pid_value # ps -ef | grep 17177 | grep -v '_safe' | grep -v 'grep'
      proc_info = conn.execute(cmd)[0] # mysql    17177 15793  1 Feb04 ?        07:00:39 /data/app/mysql-8.0.26/bin/mysqld --defaults-file=/data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/my16315.conf --basedir=/data/app/mysql-8.0.26 --datadir=/data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/dbdata --plugin-dir=/data/app/mysql-8.0.26/lib/plugin --log-error=/data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/logfile/mysqld.err --open-files-limit=1024000 --pid-file=/data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/pid/mysqld.pid --socket=/data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/socket/mysql.sock --port=16315\n
      #print_debug_info(proc_info, "get process info of %s:%s" % (host, db_port))
      proc_elem = proc_info.split(' ')
      db_conf_file = ''
      for elem in proc_elem:
          if '--defaults-file=' in elem: # --defaults-file=/data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/my16315.conf
              db_conf_file = elem.split('=')[1] # /data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/my16315.conf
              break
      if len(db_conf_file) == 0:
          fatal_error("fail to get config file location of db %s:%s" % (host, db_port))
      backupfile_dir_name = belong_to_datasource + '-' + server_name + '-' + host + '-' + db_port # normal_0-normal_0_0-172.17.134.71-16315
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
      tmpdir = get_query_result_set(query_text, db_cursor)[0]['Value'] # /data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/tmp
      xtrabackup_log_file = tmpdir + '/' + backup_id + '-' + backupfile_dir_name #use dir name as log file name # /data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/tmp/UTC20240222014345.236-normal_0-normal_0_0-172.17.134.71-16315

      source_cmd = "%s --defaults-file=%s --user=%s --password=%s --host=%s --port=%s --backup --tmpdir=%s %s --stream=xbstream %s 2>%s | socat - TCP4:%s:%d,retry=%s" \
              % (xtrabackup_bin_dir, db_conf_file, dataserver_user_pswd_map[server_name]['user'], dataserver_user_pswd_map[server_name]['password'], host, db_port, tmpdir, inc_backup_cmd, compress_mode, xtrabackup_log_file, backup_dest_ip, socat_port, socat_retry_count) # xtrabackup --defaults-file=/data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/my16315.conf --user=dbscale_internal --password=123456 --host=172.17.134.71 --port=16315 --backup --tmpdir=/data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/tmp  --stream=xbstream  2>/data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/tmp/UTC20240222014345.236-normal_0-normal_0_0-172.17.134.71-16315 | socat - TCP4:172.17.134.76:45509,retry=10
      command_id2 = get_command_id() # 3
      command_info2 = ExecCommandInfo(host, dbscale_server_user, dbscale_server_port, dbscale_server_pswd, source_cmd) # 172.17.134.71
      if not add_exec_command_info(command_id2, command_info2):
          conn.close()
          dest_server_conn.close()
          del_exec_command_info(command_id)
          return

      if compress == True:
          print_debug_info(source_cmd, 'socat source cmd with compress for dataserver %s' % server_name)
      else:
          print_debug_info(source_cmd, 'socat source cmd without compress for dataserver %s' % server_name) # [DEBUG 140174436591360 2024-02-22 13:20:43.464206] socat source cmd without compress for dataserver normal_0_0 : xtrabackup --defaults-file=/data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/my16315.conf --user=dbscale_internal --password=123456 --host=172.17.134.71 --port=16315 --backup --tmpdir=/data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/tmp  --stream=xbstream  2>/data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/tmp/UTC20240222014345.236-normal_0-normal_0_0-172.17.134.71-16315 | socat - TCP4:172.17.134.76:45509,retry=10
      print_trace_info("%s(%s)" % (sys._getframe().f_code.co_name, threading.get_ident()), "in-progress") # [TRACE 140174436591360 2024-02-22 13:21:02.925110] backup_one_host(140174436591360) in-progress
      global conn_timeout

      #conn.execute(source_cmd, timeout=conn_timeout)
      server_str = host + ":" + str(db_port) # 172.17.134.71:16315
      server_index = ftwrl_server_dict[server_str] # 0
      print_debug_info("th_detect_ftwrl_lock get server_index is %d." % server_index, 'server: %s' % server_str) # [DEBUG 140174436591360 2024-02-22 13:22:38.506176] server: 172.17.134.71:16315 : th_detect_ftwrl_lock get server_index is 0.
      if server_index == None:
        del_exec_command_info(command_id)
        fatal_error("get error when backup db get server index")
      detect_conn = get_one_server_conn(host, dbscale_server_user, dbscale_server_port, dbscale_server_pswd)
      ftwrl_retry_times = ftwrl_dead_lock_retry # 10
      print_debug_info("backup_one_host ftwrl_retry_times: [%d]" % ftwrl_retry_times, 'server: %s' % server_str) # [DEBUG 140174436591360 2024-02-22 13:23:15.429595] server: 172.17.134.71:16315 : backup_one_host ftwrl_retry_times: [10]
      while ftwrl_retry_times > 0:
        th_conn_execute = threading.Thread(target=xtrabackup_command_execute, args=(conn, source_cmd, server_index, server_str))
        th_detect_ftwrl_lock = threading.Thread(target=detect_ftwrl_lock, args=(detect_conn, xtrabackup_log_file, db_conn, db_cursor, server_index, server_str))
        th_conn_execute.start()
        th_detect_ftwrl_lock.start()
        time.sleep(5)
        socat_use_lock.release()
        has_release_socat_use_lock = True
        th_detect_ftwrl_lock.join()
        print_trace_info("th_detect_ftwrl_lock returned", 'server: %s' % server_str) # [TRACE 140174436591360 2024-02-22 13:23:55.962387] th_detect_ftwrl_lock returned server: 172.17.134.71:16315
        #th_conn_execute.join()
        xtrabackup_exec_result = detect_ftwrl_lock_result[server_index]
        if xtrabackup_exec_result == enum_detect_ftwrl_lock_result['DETECT_FTWRL_RESULT_SUCCESSED']:
            print_trace_info("backup_one_host get DETECT_FTWRL_RESULT_SUCCESSED", 'server: %s' % server_str) # [TRACE 140174436591360 2024-02-22 13:24:17.276120] backup_one_host get DETECT_FTWRL_RESULT_SUCCESSED server: 172.17.134.71:16315
            # result_index == 1 means xtrabackup successed
            th_conn_execute.join()
            th.join(timeout = 60)
            if th.isAlive():
                print_err_info("backup_one_host get socat thread is alive, join timeout.", 'server: %s' % server_str)
            break
        elif xtrabackup_exec_result == enum_detect_ftwrl_lock_result['DETECT_FTWRL_RESULT_NEED_RETRY']:
            # result_index == 1 means need to retry
            th.join(timeout = 60)
            if th.isAlive():
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
      print_debug_info("backup_one_host ftwrl_retry_times: [%d]" % ftwrl_retry_times, 'server: %s' % server_str) # [DEBUG 140174436591360 2024-02-22 13:24:42.281464] server: 172.17.134.71:16315 : backup_one_host ftwrl_retry_times: [10]
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
          if len(binlog_info) >= 2 and need_get_gtids: # need_get_gtids=False
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

#### clean_before_rextrabackup
```python

def clean_before_rextrabackup(dest_server_conn, server_name, backupfile_dir_name, belong_to_datasource):
    global socat_port # 45509
    global dataserver_info
    global backup_dest_dir # /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240222014345.236-full
    global socat_log_dir # /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240222
    global socat_log_file #

    cmd = "mkdir -p %s/%s" % (backup_dest_dir, backupfile_dir_name) # mkdir -p /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240222014345.236-full/normal_0-normal_0_0-172.17.134.71-16315
    dest_server_conn.execute(cmd)

    is_slave = False
    # write master slave ip:port file
    rep = rep_source_ms_info[belong_to_datasource] # normal_0: 'master':'normal_0_1'  slaves':['normal_0_0', 'normal_0_2']
    master_server_name = rep['master'] # normal_0_1
    if master_server_name == server_name: # normal_0_1 == normal_0_0
        slave_server_names = rep['slaves']
        master_server = dataserver_info[master_server_name]
        master_slave_str = master_server['host'] + ":" + master_server['port'] + "\n"
        for slave_server_name in slave_server_names:
            slave_server = dataserver_info[slave_server_name]
            master_slave_str += slave_server['host'] + ":" + slave_server['port'] + "\n"
        master_slave_str = master_slave_str[:-1]
        cmd = "echo '%s' > %s/%s/master_slave" % (master_slave_str, backup_dest_dir, backupfile_dir_name)
        dest_server_conn.execute(cmd)
    else:
        is_slave = True
        master_server = dataserver_info[master_server_name] # 'host':'172.17.134.76''port':'16315''status':'Server normal''belong_to_datasource':'normal_0'
        rep_master_backup_dir_name = belong_to_datasource + '-' + master_server_name + '-' + master_server['host'] + '-' + master_server['port'] # normal_0-normal_0_1-172.17.134.76-16315
        cmd = "echo '%s' > %s/%s/master_dir" % (rep_master_backup_dir_name, backup_dest_dir, backupfile_dir_name) # echo 'normal_0-normal_0_1-172.17.134.76-16315' > /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240222014345.236-full/normal_0-normal_0_0-172.17.134.71-16315/master_dir
        dest_server_conn.execute(cmd)

    command_id = get_command_id() # command_id=1
    socat_log_file = str(socat_port) + '_' + str(command_id) # 45509_1
    dest_cmd = "socat -d -lf %s/%s -u tcp-listen:%d,reuseaddr stdout > %s/%s/backup.xbsteam &" % (socat_log_dir, socat_log_file, socat_port, backup_dest_dir, backupfile_dir_name) # socat -d -lf /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240222/45509_1 -u tcp-listen:45509,reuseaddr stdout > /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240222014345.236-full/normal_0-normal_0_0-172.17.134.71-16315/backup.xbsteam &
    dest_socat_ret_code = [1]
    th = run_socat_receiver_on_backup_dest(dest_server_conn, dest_cmd, command_id, True,  dest_socat_ret_code)
    if th == None:
        fatal_error("clean_before_rextrabackup find execute socat command error.")
    return (is_slave, command_id, th)
```
#### run_socat_receiver_on_backup_dest
```python
def run_socat_receiver_on_backup_dest(conn, cmd, command_id, is_socat_xstream_port, ret_code):
    global socat_log_dir
    global socat_retry_count
    def runOnBackupDest(conn, cmd, ret_code): # 172.17.134.76
        global conn_timeout
        try:
            conn.log_execute_info_once()
            ret = conn.execute(cmd, timeout=conn_timeout)
        except Exception as e:
            return
        ret_code[0] = 0

    tmp_conn = get_one_dest_server_conn() # 172.17.134.76
    can_retry_times = socat_retry_count # 10
    socat_port_get = get_socat_port(is_socat_xstream_port) # 45509
    socat_port_tmp = socat_port_get # 45509
    dest_cmd = cmd # socat -d -lf /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240222/45509_1 -u tcp-listen:45509,reuseaddr stdout > /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240222014345.236-full/normal_0-normal_0_0-172.17.134.71-16315/backup.xbsteam &
    while True and can_retry_times > -1:
        print_trace_info("run_socat_receiver_on_backup_dest retry to run socat use socat_port %d." % (socat_port_get),"") # [TRACE 140174436591360 2024-02-22 12:58:34.049011] run_socat_receiver_on_backup_dest retry to run socat use socat_port 45509.
        str_rep = re.compile(str(socat_port_tmp))
        dest_cmd = str_rep.sub(str(socat_port_get), dest_cmd) # socat -d -lf /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240222/45509_1 -u tcp-listen:45509,reuseaddr stdout > /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240222014345.236-full/normal_0-normal_0_0-172.17.134.71-16315/backup.xbsteam &
        socat_log_file = str(socat_port_get) + '_' + str(command_id) # 45509_1
        socat_port_tmp = socat_port_get # 45509
        command_info = ExecCommandInfo(cmd = dest_cmd)
        if not add_exec_command_info(command_id, command_info):
            tmp_conn.close()
            print_trace_info("run_socat_receiver_on_backup_dest add_exec_command_info faild, return the None thread.","")
            return
        th = threading.Thread(target=runOnBackupDest, args=(conn, dest_cmd, ret_code))
        th.start()
        print_trace_info("run_socat_receiver_on_backup_dest execute socat dest_cmd :%s." % (dest_cmd),"") # [TRACE 140174436591360 2024-02-22 13:01:07.483206] run_socat_receiver_on_backup_dest execute socat dest_cmd :socat -d -lf /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240222/45509_1 -u tcp-listen:45509,reuseaddr stdout > /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240222014345.236-full/normal_0-normal_0_0-172.17.134.71-16315/backup.xbsteam &. 表示在备份服务器上已经执行
        i = 0
        print_trace_info("run_socat_receiver_on_backup_dest cat socat_log_file %s/%s." % (socat_log_dir, socat_log_file),"") # [TRACE 140174436591360 2024-02-22 13:01:59.393973] run_socat_receiver_on_backup_dest cat socat_log_file /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240222/45509_1.
        while True and i <= 100:
            i = i + 1
            try:
                ret = tmp_conn.execute('ps ajx | grep "socat -d -lf %s/%s -u tcp-listen:%d" | grep -v "grep"' % (socat_log_dir, socat_log_file, socat_port_get))[0] # 172.17.134.76
            except Exception as e:
                continue
            if ret != '':
                tmp_conn.close()
                set_socat_port(is_socat_xstream_port, socat_port_get)
                print_trace_info("run_socat_receiver_on_backup_dest execute socat successed set_socat_port %d." % (socat_port_get),"") # [TRACE 140174436591360 2024-02-22 13:03:09.933037] run_socat_receiver_on_backup_dest execute socat successed set_socat_port 45509.
                return th
        if i > 100:
            try:
                ret = tmp_conn.execute('cat %s/%s | grep "Address already in use" | grep -v "grep"' % (socat_log_dir, socat_log_file), timeout=conn_timeout)[0]
            except Exception as e:
                tmp_conn.close()
                fatal_error("failed to start socat but not the port is occupied, please check the log file %s/%s" % (socat_log_dir, socat_log_file))
            if ret != '':
                print_trace_info("find the socat port is occupied, change socat_port and try again", 'socat log info:')
                socat_port_get = socat_port_get + 10
                can_retry_times = can_retry_times - 1

    fatal_error("failed to start socat when retry %d times" % (socat_retry_count))
```
#### transfer_file_in_conn
> 传输 /data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/my16315.conf
```python
def transfer_file_in_conn(conn: Conn.BackupConn, dest_ip, file_name_from, file_to_dir, file_name_to, **kargs): # 172.17.134.76 /data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/my16315.conf /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240222014345.236-full/normal_0-normal_0_0-172.17.134.71-16315 my.conf
    global using_socat # using_socat=True
    global conn_timeout # using_socat
    global socat_log_dir # /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240222
    global socat_retry_count # 10

    if is_backup_need_stop():
        return False

    dest_pswd = kargs.get('dest_pswd')
    dest_port = kargs.get('dest_port')
    dest_user = kargs.get('dest_user')
    if using_socat:
        dest_conn = get_one_server_conn(dest_ip, dest_user, dest_port, dest_pswd, using_rds_taskmanager=False)
        command_id = get_command_id() # 2
        socat_port = get_socat_port(False) # 45514
        socat_log_file = str(socat_port) + '_' + str(command_id) # 45514_2
        dest_cmd = 'socat -d -lf %s/%s -u tcp-listen:%s,reuseaddr file:%s/%s,trunc,creat' % (socat_log_dir, socat_log_file, socat_port, file_to_dir, file_name_to) # socat -d -lf /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240222/45514_2 -u tcp-listen:45514,reuseaddr file:/data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240222014345.236-full/normal_0-normal_0_0-172.17.134.71-16315/my.conf,trunc,creat
        dest_socat_ret_code = [1]
        th = run_socat_receiver_on_backup_dest(dest_conn, dest_cmd, command_id, False, dest_socat_ret_code) # 172.17.134.76
        if th == None:
            dest_conn.close()
            return False

        socat_port = get_socat_port(False) # 45514
        conn.execute('cat %s | socat - TCP4:%s:%s,retry=%s' % (file_name_from, dest_ip, socat_port, socat_retry_count), timeout=conn_timeout) # cat /data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/my16315.conf | socat - TCP4:172.17.134.76:45514,retry=10
        print_debug_info('transfering file: %s' % (file_name_from), '') # [DEBUG 140174436591360 2024-02-22 13:17:13.257822] transfering file: /data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/my16315.conf
        th.join()

        del_exec_command_info(command_id)
    else:
        cmd = "scp -P%s %s %s@%s:%s/%s" % (dest_port, file_name_from, dest_user, dest_ip, file_to_dir, file_name_to)
        command_id = get_command_id()
        command_info = ExecCommandInfo(dest_ip, dest_user, dest_port, dest_pswd, cmd)
        if not add_exec_command_info(command_id, command_info):
            return False
        conn.add_watcher(r'.*password.*', dest_pswd+'\n')
        print_debug_info('%s to %s:%s/%s' % (file_name_from, dest_ip, file_to_dir, file_name_to), 'backup file')
        conn.execute(cmd)
        del_exec_command_info(command_id)
    return True
```

#### xtrabackup_command_execute
```python
def xtrabackup_command_execute(conn, source_cmd, index, server_str):
    print_trace_info('xtrabackup_command_execute thread begin', 'start, server: %s' % server_str)
    global conn_timeout
    try:
        ret = conn.execute(source_cmd, timeout=conn_timeout)
        print_debug_info('execute xtrabackup: [%s] ret: [%s]' % (source_cmd, ret), 'server: %s' % server_str)
        set_ftwrl_status(enum_ftwrl_status['XTRABACKUP_EXECUTE_COMPLETED'], index)
    except Exception as e:
        set_ftwrl_status(enum_ftwrl_status['XTRABACKUP_EXECUTE_UNKNOW_ERROR'], index)
        print_trace_info('xtrabackup_command_execute thread end and set status XTRABACKUP_EXECUTE_UNKNOW_ERROR', 'finish, server: %s' % server_str)
        print_err_info('xtrabackup_command_execute execute command %s failed: [%s]' % (source_cmd, e), 'server: %s' % server_str)
        return
    print_trace_info('xtrabackup_command_execute thread end', 'finish, server: %s' % server_str)
```


#### detect_ftwrl_lock

```python
def detect_ftwrl_lock(conn, log_file, db_conn, db_cursor, index, server_name):
    global ftwrl_dead_lock_seconds
    global backup_get_execption
    global detect_ftwrl_lock_result
    print_trace_info('detect_ftwrl_lock thread begin', 'start, server: %s' % server_name)
    log_times = 0
    has_ftwrl_dead_lock = False
    query_id = []
    check_log_file_exist = False
    while True:
        if backup_get_execption:
            print_trace_info('detect_ftwrl_lock get backup_get_execption', 'finish, server: %s' % server_name)
            detect_ftwrl_lock_result[index] = enum_detect_ftwrl_lock_result['DETECT_FTWRL_RESULT_UNDETECT']
            return
        if get_ftwrl_status(index) == enum_ftwrl_status['XTRABACKUP_EXECUTE_COMPLETED']:
            print_trace_info('detect_ftwrl_lock get status RESULT_SUCCESSED thread end', 'finish, server: %s' % server_name)
            detect_ftwrl_lock_result[index] = enum_detect_ftwrl_lock_result['DETECT_FTWRL_RESULT_SUCCESSED']
            return
        elif get_ftwrl_status(index) == enum_ftwrl_status['XTRABACKUP_EXECUTE_UNKNOW_ERROR']:
            print_trace_info('detect_ftwrl_lock get status RESULT_NEED_RETRY thread end', 'finish server: %s' % server_name)
            detect_ftwrl_lock_result[index] = enum_detect_ftwrl_lock_result['DETECT_FTWRL_RESULT_NEED_RETRY']
            return
        if has_ftwrl_dead_lock:
            processlist_info = get_query_result_set('select * from information_schema.processlist where info like "%flush tables with read lock%" and info not like "%processlist%"', db_cursor)
            for row in processlist_info:
                info = row['INFO'].lower()
                if "flush tables with read lock" in info:
                    query_id.append(str(row['ID']))
            if len(query_id) == 0:
                has_ftwrl_dead_lock = False
                time.sleep(1)
                continue

            print_trace_info('detect_ftwrl_lock execute kill: %s' % (' '.join(query_id)), 'server: %s' % server_name)
            try:
                for id in query_id:
                    db_cursor.execute('kill %s' % (id))
            except Exception as e:
                print_err_info('kill flush tables with read lock query_id: [%s] failed: [%s]' % (' '.join(query_id), e), 'server: %s' % server_name)
            detect_ftwrl_lock_result[index] = enum_detect_ftwrl_lock_result['DETECT_FTWRL_RESULT_NEED_RETRY']
            return
        if check_log_file_exist == False:
            check_ret = conn.execute("if test -e %s; then   echo '%s'; else   echo '%s'; fi" % (log_file, "yes", "no"))[0]
        if 'no' in check_ret:
            time.sleep(1)
            continue
        else:
            check_log_file_exist = True
        tail_ret = conn.execute('tail -n500 %s' % log_file)[0]
        ret = tail_ret.split('\n')
        while len(ret) > 0 and ret[-1] == "":
            ret.pop()
        if len(ret) == 0:
            time.sleep(1)
            continue
        if "log scanned up to" in ret[-1]:
            log_times += 1
        elif "completed OK" in ret[-1]:
            detect_ftwrl_lock_result[index] = enum_detect_ftwrl_lock_result['DETECT_FTWRL_RESULT_SUCCESSED']
            return
        last_log = ret[-1]
        if log_times >= ftwrl_dead_lock_seconds:
            for log in reversed(ret):
                if "Executing FLUSH TABLES WITH READ LOCK" in log and "log scanned up to" in last_log:
                    print_trace_info('detect_ftwrl_lock find has_ftwrl_dead_lock', 'server: %s' % server_name)
                    has_ftwrl_dead_lock = True
                    break
                elif "log scanned up to" in log and "log scanned up to" in last_log:
                    last_log = log
                else:
                    log_times = 0
                    break
        time.sleep(1)
    print_trace_info('detect_ftwrl_lock thread end', 'finish, server: %s' % server_name)
    return
```

### 备份全局一致性位点

> backup_global_consistence_point
>
- dbscale get global consistence point
- show binary logs
- touch  /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240220093304.784-full/cluster_consistence_info &&
- echo "xxx" >> /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240220093304.784-full/cluster_consistence_info
- echo "xxx" >> /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240220093304.784-full/consistent_point_datetime

```sql
mysql> dbscale get global consistence point;
+-------------+--------------------+----------+
| Server_name | File               | Position |
+-------------+--------------------+----------+
| normal_0_1  | greatdb-bin.000041 | 631      |
+-------------+--------------------+----------+
1 row in set (0.03 sec)

mysql> show binary logs;
+--------------------+-----------+-----------+
| Log_name           | File_size | Encrypted |
+--------------------+-----------+-----------+
| greatdb-bin.000039 |       277 | No        |
| greatdb-bin.000040 |       277 | No        |
| greatdb-bin.000041 |       631 | No        |
+--------------------+-----------+-----------+
41 rows in set (0.01 sec)

```

```python
def backup_global_consistence_point():
    if is_backup_need_stop():
        return
    print_trace_info(sys._getframe().f_code.co_name, "start") # [TRACE 140177113528128 2024-02-22 17:29:48.263359] backup_global_consistence_point start
    global backup_dest_dir # /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240222014345.236-full
    global global_consistence_info
    global backup_master
    global global_consistence_infos
    global cluster_dr_exclude_server
    global use_disaster_recovery_cluster
    rows = []
    flag = False
    try:
      query_text = "dbscale get global consistence point" # cluster_cursor=172.17.134.77
      rows = get_query_result_set(query_text, cluster_cursor) # 'Server_name':'normal_0_1''File':'greatdb-bin.000041''Position':'631'
    except Exception as e:
      flag = True
      print_debug_info('get global consistence point fail', '')
    if flag and use_disaster_recovery_cluster == True:
        for server in dataserver_info:
            if is_master_server(server):
                query_text = "show binary logs"
                rows1 = get_query_result_set(query_text, cluster_cursor)
                dic = OrderedDict()
                dic['File'] = server
                dic['Server_name'] = rows1[-1]['Log_name']
                dic['Position'] = str(rows1[-1]['File_size'])
                rows.append(dic)

    consistent_point_datetime_str = datetime.now().strftime('%Y-%m-%d %H:%M:%S') # 2024-02-22 17:33:15
    consistence_info = ""
    for row_tmp in rows:
        row = tuple(row_tmp.values())
        ## backup master's consistence point need it's xtrabackup info
        ## if we didn't backup master server, then we shouldn't backup master server's consistence_info
        if use_disaster_recovery_cluster == True and row[0] == cluster_dr_exclude_server:
            continue
        if backup_master:
            consistence_info += construct_consistence_point_info_for_server(row, True)
        servers = get_servers_of_source(dataserver_info[row[0]]['belong_to_datasource']) # 0:'normal_0_0' 1:'normal_0_1' 2:'normal_0_2'
        servers.remove(row[0])
        for server in servers: # normal_0_0 normal_0_2
            if is_skipped(server):
                continue
            row = (server, row[1], row[2])
            consistence_info += construct_consistence_point_info_for_server(row, False)
    consistence_info = consistence_info[:-1]
    print_debug_info(consistence_info, "get cluster global consistence point") # [DEBUG 140177113528128 2024-02-22 17:35:25.206848] get cluster global consistence point : 172.17.134.71:16315   normal_0_0      greatdb-bin.000041      631     belong-to-datasource=normal_0   xb-to-lsn=21694916      xb-to-binlog-name=greatdb-bin.000041    xb-to-binlog-pos=4
    dest_server_conn = get_one_dest_server_conn()
    cmd1 = "touch {0}/cluster_consistence_info && echo '{1}' >> {0}/cluster_consistence_info".format(backup_dest_dir, consistence_info) # touch /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240222014345.236-full/cluster_consistence_info && echo '172.17.134.71:16315\tnormal_0_0\tgreatdb-bin.000041\t631\tbelong-to-datasource=normal_0\txb-to-lsn=21694916\txb-to-binlog-name=greatdb-bin.000041\txb-to-binlog-pos=4' >> /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240222014345.236-full/cluster_consistence_info
    cmd2 = "echo '%s' >> %s/consistent_point_datetime" % (consistent_point_datetime_str, backup_dest_dir) # echo '2024-02-22 17:33:15' >> /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240222014345.236-full/consistent_point_datetime
    dest_server_conn.execute(cmd1)
    dest_server_conn.execute(cmd2)
    dest_server_conn.close()
    global_consistence_info = consistence_info # 172.17.134.71:16315\tnormal_0_0\tgreatdb-bin.000041\t631\tbelong-to-datasource=normal_0\txb-to-lsn=21694916\txb-to-binlog-name=greatdb-bin.000041\txb-to-binlog-pos=4
    print_trace_info(sys._getframe().f_code.co_name, "finish") # [TRACE 140177113528128 2024-02-22 17:36:40.315421] backup_global_consistence_point finish
```

### 备份binlog
> backup_binlog_files

- show binary logs
- show global variables like 'log_bin_basename'



```python
def backup_binlog_files():
    if is_backup_need_stop():
        return
    print_trace_info(sys._getframe().f_code.co_name, "start") # [TRACE 140177113528128 2024-02-22 17:37:04.038288] backup_binlog_files start
    global dbscale_server_user
    global dbscale_server_pswd
    global dbscale_server_port

    global backup_dest_ip # 172.17.134.76
    global backup_dest_port
    global backup_dest_user
    global backup_dest_pswd
    global backup_dest_dir # /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240222014345.236-full

    for server in dataserver_info: # normal_0_0 172.17.134.71 normal_0_1 172.17.134.76 normal_0_2 172.17.134.77
        if is_skipped(server):
            print_debug_info("dataserver %s is skipped, no need backup its binlog files" % (server), '') # [DEBUG 140177113528128 2024-02-22 17:47:58.660243] dataserver normal_0_2 is skipped, no need backup its binlog files
            continue
        ## when not backup_master and cur server is not master, we do not backup binlogs of salve servers
        if not backup_master and not is_master_server(server):
            print_debug_info("dataserver %s is not master, and backup_master = 0, salve servers's binlogs are skipped" % (server), '') # [DEBUG 140177113528128 2024-02-22 17:38:39.556969] dataserver normal_0_0 is not master, and backup_master = 0, salve servers's binlogs are skipped
            continue
        backup_binlog_for_server(server)
    print_trace_info(sys._getframe().f_code.co_name, "finish") # [TRACE 140177113528128 2024-02-22 17:48:33.089813] backup_binlog_files finish
```

#### backup_binlog_for_server
```python

def backup_binlog_for_server(server_name): # normal_0_1 172.17.134.76
    if is_backup_need_stop():
        return
    print_trace_info(sys._getframe().f_code.co_name, "start") # [TRACE 140177113528128 2024-02-22 17:40:06.281685] backup_binlog_for_server start
    global dbscale_server_user
    global dbscale_server_pswd
    global dbscale_server_port

    global backup_dest_ip
    global backup_dest_port
    global backup_dest_user
    global backup_dest_pswd
    global backup_dest_dir

    global connect_method
    global adm_server_port

    host = dataserver_info[server_name]['host']
    port = dataserver_info[server_name]['port']
    belong_to_datasource = dataserver_info[server_name]['belong_to_datasource']
    db_conn_config = {
            'host': host, # 172.17.134.76
            'user': dataserver_user_pswd_map[server_name]['user'], # dbscale_internal
            'password': dataserver_user_pswd_map[server_name]['password'], # 123456
            'port': int(port), # 16315
            'db': 'information_schema',
            'charset': "utf8",
            'autocommit': True
            }
    (conn, cursor) = get_database_conn(db_conn_config)
    query_text = 'show binary logs'
    binary_logs = get_query_result_set(query_text, cursor)
    query_text = "show global variables like 'log_bin_basename'"
    log_bin_basename = get_query_result_set(query_text, cursor)[0]['Value'] # /data/mysqldata/16315fff-d9ca-4adb-9be1-578ff4626394/dbdata/greatdb-bin

    if len(binary_logs) == 0:
        fatal_error('failed to list binlogs')
    binlogs = []
    for b in binary_logs:
        binlogs.append(b['Log_name'])
    binlogs.sort()
    print_debug_info('all binlogs for server %s : %s' % (server_name, ' '.join(binlogs)), '') # [DEBUG 140177113528128 2024-02-22 17:42:48.064851] all binlogs for server normal_0_1 : greatdb-bin.000001 greatdb-bin.000002 greatdb-bin.000003 greatdb-bin.000004 greatdb-bin.000005 greatdb-bin.000006 greatdb-bin.000007 greatdb-bin.000008 greatdb-bin.000009 greatdb-bin.000010 greatdb-bin.000011 greatdb-bin.000012 greatdb-bin.000013 greatdb-bin.000014 greatdb-bin.000015 greatdb-bin.000016 greatdb-bin.000017 greatdb-bin.000018 greatdb-bin.000019 greatdb-bin.000020 greatdb-bin.000021 greatdb-bin.000022 greatdb-bin.000023 greatdb-bin.000024 greatdb-bin.000025 greatdb-bin.000026 greatdb-bin.000027 greatdb-bin.000028 greatdb-bin.000029 greatdb-bin.000030 greatdb-bin.000031 greatdb-bin.000032 greatdb-bin.000033 greatdb-bin.000034 greatdb-bin.000035 greatdb-bin.000036 greatdb-bin.000037 greatdb-bin.000038 greatdb-bin.000039 greatdb-bin.000040 greatdb-bin.000041
    server_backup_dir = belong_to_datasource + '-' + server_name + '-' + host + '-' + port # normal_0-normal_0_1-172.17.134.76-16315
    last_binlog_of_last_good_backup = get_last_binlog_of_last_good_backup(server_name) # greatdb-bin.000041
    if last_binlog_of_last_good_backup is None:
        last_binlog_of_last_good_backup = binlogs[0]
    (cur_start_binlog, cur_stop_binlog) = get_dataserver_start_stop_binlog(server_name)
    ####### when backup-master ############
    # for master server
    #   if is a full backup
    #       we will copy binlog from xb-to-binlog-name to consistent-point binlog name
    #   else
    #       we will copy binlog from the last binlog of last good backup to the consistent-point binlog name of this backup
    # for slave server
    #   if full bakcup
    #       we will copy binlogs from xb-to-binlog-name
    #   else
    #       the start binlog file is the last binlog of last backup
    #   both full and inc backup, we can't determine the stop binlog file, so we copy all the binlogs exists in it's datadir
    #
    ###### when not backup-master #########
    # for master server
    #   binlogs of master server will be used when recover slave servers
    #   the start binlog file is fetch by the `SHOW SLAVE STATUS`.Relay_Master_Log_File in every slave
    #   and the start binlog file should be the smallest index of the Relay_Master_Log_Files
    #   if full backup
    #       we will backup binlogs from Relay_Master_Log_File to consistent_point binlog file
    #   else
    #       we will backup binlogs from min(Relay_Master_Log_File,last binlog of last good backup) to consistent_point binlog file
    #       min is used to prevent binlog gaps between last backup and this backup
    # for slave server (skipped)
    #   we do not backup binlogs of slave servers
    #######################################
    if backup_type == 'full': ## last binlog of last good backup is useless here
        last_binlog_of_last_good_backup = cur_start_binlog # greatdb-bin.000041
    elif not backup_master: ## inc backup and not backup master
        last_binlog_of_last_good_backup = min(last_binlog_of_last_good_backup, cur_start_binlog)
    start_index = binlogs.index(last_binlog_of_last_good_backup) # 40
    stop_index = 0
    if cur_stop_binlog == '':
        stop_index = len(binlogs)
    else:
        stop_index = binlogs.index(cur_stop_binlog) + 1 # 41
    binlogs_need_copy = binlogs[start_index:stop_index]
    dest_server_conn = get_one_dest_server_conn()
    save_backuped_binlogs_cmd = 'echo -e "%s" >> %s/backuped_binlogs' % (server_name + ':' + ' '.join(binlogs_need_copy), backup_dest_dir) # echo -e "normal_0_1:greatdb-bin.000041" >> /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240222014345.236-full/backuped_binlogs
    try:
        ret = dest_server_conn.execute(save_backuped_binlogs_cmd)
        if ret[1] != '':
            fatal_error('writing backuped binlogs failed: %s' % ret[1])
    except Exception as e:
        fatal_error("writing backuped binlogs failed: %s" % str(e.args))
    print_debug_info('server: %s binlogs need copy: %s' % (server_name, ' '.join(binlogs_need_copy)), '') # [DEBUG 140177113528128 2024-02-22 17:45:07.443087] server: normal_0_1 binlogs need copy: greatdb-bin.000041
    log_bin_dir = log_bin_basename[0:-len(binlogs_need_copy[0][0:-7])]
    print_debug_info(log_bin_dir, 'get log_bin_dir of dataserver %s' % (server_name)) # [DEBUG 140177113528128 2024-02-22 17:45:24.379046] get log_bin_dir of dataserver normal_0_1 : /data/mysqldata/16315fff-d9ca-4adb-9be1-578ff4626394/dbdata/

    source_server_conn = get_one_server_conn(host, dbscale_server_user, dbscale_server_port, dbscale_server_pswd)
    backupfile_dir_name = belong_to_datasource + '-' + server_name + '-' + host + '-' + port
    print_debug_info('', "backup binlog file of dataserver %s" % (server_name)) # normal_0-normal_0_1-172.17.134.76-16315
    for binlog_file in binlogs_need_copy:
        config = {
                'dest_user': backup_dest_user,
                'dest_pswd': backup_dest_pswd,
                'dest_port': backup_dest_port
                }
        if connect_method == 2:
            config['dest_port'] = adm_server_port #source_server_conn=172.17.134.76，backup_dest_ip=172.17.134.76 /data/mysqldata/16315fff-d9ca-4adb-9be1-578ff4626394/dbdata/greatdb-bin.000041 normal_0-normal_0_1-172.17.134.76-16315
        if not transfer_file_in_conn(source_server_conn, backup_dest_ip, log_bin_dir + '/' + binlog_file, backup_dest_dir + '/' + backupfile_dir_name, binlog_file, **config):
            source_server_conn.close()
            close_database_conn(conn, cursor)
            break
    source_server_conn.close()
    close_database_conn(conn, cursor)
    print_trace_info(sys._getframe().f_code.co_name, "finish") # [TRACE 140177113528128 2024-02-22 17:47:44.310255] backup_binlog_for_server finish
```
#### transfer_file_in_conn
```python
def transfer_file_in_conn(conn: Conn.BackupConn, dest_ip, file_name_from, file_to_dir, file_name_to, **kargs):
    global using_socat
    global conn_timeout
    global socat_log_dir
    global socat_retry_count

    if is_backup_need_stop():
        return False

    dest_pswd = kargs.get('dest_pswd')
    dest_port = kargs.get('dest_port')
    dest_user = kargs.get('dest_user')
    if using_socat:
        dest_conn = get_one_server_conn(dest_ip, dest_user, dest_port, dest_pswd, using_rds_taskmanager=False)
        command_id = get_command_id()
        socat_port = get_socat_port(False)
        socat_log_file = str(socat_port) + '_' + str(command_id)
        dest_cmd = 'socat -d -lf %s/%s -u tcp-listen:%s,reuseaddr file:%s/%s,trunc,creat' % (socat_log_dir, socat_log_file, socat_port, file_to_dir, file_name_to)
        dest_socat_ret_code = [1]
        th = run_socat_receiver_on_backup_dest(dest_conn, dest_cmd, command_id, False, dest_socat_ret_code)
        if th == None:
            dest_conn.close()
            return False

        socat_port = get_socat_port(False)
        conn.execute('cat %s | socat - TCP4:%s:%s,retry=%s' % (file_name_from, dest_ip, socat_port, socat_retry_count), timeout=conn_timeout)
        print_debug_info('transfering file: %s' % (file_name_from), '')
        th.join()

        del_exec_command_info(command_id)
    else:
        cmd = "scp -P%s %s %s@%s:%s/%s" % (dest_port, file_name_from, dest_user, dest_ip, file_to_dir, file_name_to)
        command_id = get_command_id()
        command_info = ExecCommandInfo(dest_ip, dest_user, dest_port, dest_pswd, cmd)
        if not add_exec_command_info(command_id, command_info):
            return False
        conn.add_watcher(r'.*password.*', dest_pswd+'\n')
        print_debug_info('%s to %s:%s/%s' % (file_name_from, dest_ip, file_to_dir, file_name_to), 'backup file')
        conn.execute(cmd)
        del_exec_command_info(command_id)
    return True

```



### 备份数据节点：
> backup_one_master_server_conf_and_basedir
>
- show global variables like 'basedir'
- socat -d -lf /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240221/45509_5 -u tcp-listen:45509,reuseaddr stdout > /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240221031122.943-full/datanode_base/mysql-8.0.26.tar.gz &
- cd /data/app && tar -zcf - mysql-8.0.26 | socat - TCP4:172.17.134.76:45509,retry=10




```python
def backup_one_master_server_conf_and_basedir():
    if is_backup_need_stop():
        return
    print_trace_info(sys._getframe().f_code.co_name, "start")
    #try use auth, if not found then global, if not found then
    #other random source's master to backup db basedir
    global dbscale_server_user
    global dbscale_server_pswd
    global dbscale_server_port

    global backup_dest_ip
    global backup_dest_port
    global backup_dest_user
    global backup_dest_dir

    global socat_port
    global socat_log_dir
    global socat_retry_count

    auth_source_name = ""
    global_source_name = ""
    used_source_name = ""
    for data_source in rep_source_ms_info:
        if 'auth' in data_source:
            auth_source_name = data_source
            break
        if 'global' in data_source:
            global_source_name = data_source
            break
        used_source_name = data_source
    if len(auth_source_name) > 0:
        used_source_name = auth_source_name
    elif len(global_source_name) > 0:
        used_source_name = global_source_name

    master_server_name = rep_source_ms_info[used_source_name]['master']
    host = dataserver_info[master_server_name]['host']
    port = dataserver_info[master_server_name]['port']
    belong_to_datasource = dataserver_info[master_server_name]['belong_to_datasource']
    db_conn_config = {
        'host': host,
        'user': dataserver_user_pswd_map[master_server_name]['user'],
        'password': dataserver_user_pswd_map[master_server_name]['password'],
        'port': int(port),
        'db': 'information_schema',
        'charset': "utf8",
        'autocommit': True
    }
    (conn, cursor) = get_database_conn(db_conn_config)
    source_server_conn = get_one_server_conn(host, dbscale_server_user, dbscale_server_port, dbscale_server_pswd)

    if backup_type == 'inc':
        print_debug_info('this is an incremental backup, so skip backup datanode basedir, only backup config file', '')
        print_trace_info(sys._getframe().f_code.co_name, "finish")
        return

    query_text = "show global variables like 'basedir'"
    db_basedir = get_query_result_set(query_text, cursor)[0]['Value']
    if db_basedir[-1] == '/':
        db_basedir = db_basedir[0:-1]
    db_parent_dir = db_basedir[0:db_basedir.rfind('/')]
    db_basedir = db_basedir[len(db_parent_dir) + 1:]
    if db_parent_dir == '':
        db_parent_dir='/'

    command_id = get_command_id()
    socat_log_file = str(socat_port) + '_' + str(command_id)
    dest_cmd = "socat -d -lf %s/%s -u tcp-listen:%d,reuseaddr stdout > %s/datanode_base/%s.tar.gz &" % (socat_log_dir, socat_log_file, socat_port, backup_dest_dir, db_basedir)
    dest_socat_ret_code = [1]
    dest_server_conn = get_one_dest_server_conn()

    th = run_socat_receiver_on_backup_dest(dest_server_conn, dest_cmd, command_id, True, dest_socat_ret_code)
    if th == None:
        source_server_conn.close()
        dest_server_conn.close()
        close_database_conn(conn, cursor)
        return

    cmd = "cd %s && tar -zcf - %s | socat - TCP4:%s:%d,retry=%s" % (db_parent_dir, db_basedir, backup_dest_ip, socat_port, socat_retry_count)
    print_debug_info(cmd, 'socat source cmd for dataserver [%s]%s:%s to get datanode basedir' % (master_server_name, host, port))
    print_trace_info(sys._getframe().f_code.co_name, "in-progress")
    try:
        global conn_timeout
        command_id2 = get_command_id()
        command_info2 = ExecCommandInfo(host, dbscale_server_user, dbscale_server_port, dbscale_server_pswd, cmd)
        ret = None
        if True == add_exec_command_info(command_id2, command_info2):
            source_server_conn.log_execute_info_once()
            ret = source_server_conn.execute(cmd, timeout=conn_timeout)
            del_exec_command_info(command_id2)
    except Exception as e:
        if not is_backup_need_stop():
            fatal_error(str(e))
    if ret is not None and ret[1] != '' and not is_backup_need_stop():
        fatal_error("get error when backup datanode basedir of [%s]%s:%s, %s" % (master_server_name, host, port, ret[1]))
    th.join() #TODO what if source socat failed, we need kill related dest socat process forcely
    del_exec_command_info(command_id)
    source_server_conn.close()
    dest_server_conn.close()
    close_database_conn(conn, cursor)
    print_debug_info("%s/datanode_base/%s.tar.gz" % (backup_dest_dir, db_basedir), "succeed to backup datanode basedir to file")
    print_trace_info(sys._getframe().f_code.co_name, "finish")

```

### 备份dbscale.conf和dbscale.tar.gz
> backup_one_dbscale_conf_and_basedir

- dbscale flush all config to file /tmp/dbscale.conf.UTC20240221031122.943
- dbscale show path info
- socat -d -lf /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240221/45514_7 -u tcp-listen:45514,reuseaddr file:/data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240221031122.943-full/calcnode_base/dbscale.conf,trunc,creat
- cat xxx | socat - TCP4:172.17.134.76:45514,retry=10
- socat -d -lf /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240221/45509_8 -u tcp-listen:45509,reuseaddr stdout > /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240221031122.943-full/calcnode_base/dbscale.tar.gz &
- echo -e '*.log *.log_slow *.log_audit *.log_zk' > /tmp/exclude_file.txt && cd /data/app && tar --exclude-from=/tmp/exclude_file.txt -zcf - dbscale | socat - TCP4:172.17.134.76:45509,retry=10



```sql
mysql> dbscale show path info;
+---------------------------+------------------------------+
| DBScale_basedir           | DBScale_log                  |
+---------------------------+------------------------------+
| /data/app/dbscale/dbscale | /data/dbscalelog/dbscale.log |
+---------------------------+------------------------------+
1 row in set (0.00 sec)

```

```python
def backup_one_dbscale_conf_and_basedir():
    if is_backup_need_stop():
        return
    print_trace_info(sys._getframe().f_code.co_name, "start")
    global dbscale_server_user
    global dbscale_server_pswd
    global dbscale_server_port

    global backup_dest_ip
    global backup_dest_port
    global backup_dest_user
    global backup_dest_pswd
    global backup_dest_dir

    global connect_method
    global adm_server_port

    global backup_id

    global socat_port
    global socat_log_dir
    global socat_retry_count

    cluster_backup_conf_file = "/tmp/dbscale.conf.%s" % backup_id
    query_text = "dbscale flush all config to file '%s'" % cluster_backup_conf_file
    get_query_result_set(query_text, cluster_cursor)

    config = {
        'dest_pswd': backup_dest_pswd,
        'dest_user': backup_dest_user,
        'dest_port': backup_dest_port
    }
    if connect_method == 2:
        config['dest_port'] = adm_server_port
    conn = None
    conn = get_one_server_conn(cluster_config['host'], dbscale_server_user, dbscale_server_port, dbscale_server_pswd)
    if not transfer_file_in_conn(conn, backup_dest_ip, cluster_backup_conf_file, backup_dest_dir + '/calcnode_base', 'dbscale.conf', **config):
        conn.close()
        return

    if backup_type == 'inc':
        print_debug_info('this is an incremental backup, so skip backup calcnode basedir, only backup config file', '')
        print_trace_info(sys._getframe().f_code.co_name, "finish")
        return
    query_text = "dbscale show path info"
    cluster_exe_file = get_query_result_set(query_text, cluster_cursor)[0]['DBScale_basedir']
    print_debug_info(cluster_exe_file, "get exe file of cluster %s:%s" % (cluster_config['host'], cluster_config['port']))
    cluster_exe_path = cluster_exe_file[0:-len("/dbscale")]
    cluster_parent_dir = cluster_exe_path[0:cluster_exe_path.rfind('/')]
    cluster_basedir = cluster_exe_path[len(cluster_parent_dir) + 1:]
    command_id = get_command_id()
    socat_log_file = str(socat_port) + '_' + str(command_id)
    dest_cmd = "socat -d -lf %s/%s -u tcp-listen:%d,reuseaddr stdout > %s/calcnode_base/dbscale.tar.gz &" % (socat_log_dir, socat_log_file, socat_port, backup_dest_dir)
    dest_socat_ret_code = [1]

    exclude_file = "echo -e '*.log\n*.log_slow\n*.log_audit\n*.log_zk' > /tmp/exclude_file.txt"
    dest_server_conn = get_one_dest_server_conn()
    th = run_socat_receiver_on_backup_dest(dest_server_conn, dest_cmd, command_id, True, dest_socat_ret_code)
    if th == None:
        conn.close()
        dest_server_conn.close()
        return

    cmd = "%s && cd %s && tar --exclude-from=/tmp/exclude_file.txt -zcf - %s | socat - TCP4:%s:%d,retry=%s" % (exclude_file, cluster_parent_dir, cluster_basedir, backup_dest_ip, socat_port, socat_retry_count)
    print_debug_info(cmd, 'socat source cmd for cluster %s:%s to get calcnode basedir' % (cluster_config['host'], cluster_config['port']))
    print_trace_info(sys._getframe().f_code.co_name, "in-progress")
    try:
        global conn_timeout
        command_id2 = get_command_id()
        command_info2 = ExecCommandInfo(cluster_config['host'], dbscale_server_user, dbscale_server_port, dbscale_server_pswd, cmd)
        if True == add_exec_command_info(command_id, command_info2):
            ret = conn.execute(cmd, timeout=conn_timeout)
            del_exec_command_info(command_id2)
    except Exception as e:
        if not is_backup_need_stop():
            fatal_error(str(e))
    if ret[1] != '' and not is_backup_need_stop():
        fatal_error("get error when backup calcnode basedir of %s:%s, %s" % (cluster_config['host'], cluster_config['port'], ret[1]))
    th.join() #TODO what if source socat failed, we need kill related dest socat process forcely

    del_exec_command_info(command_id)
    conn.close()
    dest_server_conn.close()
    print_trace_info(sys._getframe().f_code.co_name, "finish")
```

**transfer_file_in_conn**
```python
def transfer_file_in_conn(conn: Conn.BackupConn, dest_ip, file_name_from, file_to_dir, file_name_to, **kargs):
    global using_socat
    global conn_timeout
    global socat_log_dir
    global socat_retry_count

    if is_backup_need_stop():
        return False

    dest_pswd = kargs.get('dest_pswd')
    dest_port = kargs.get('dest_port')
    dest_user = kargs.get('dest_user')
    if using_socat:
        dest_conn = get_one_server_conn(dest_ip, dest_user, dest_port, dest_pswd, using_rds_taskmanager=False)
        command_id = get_command_id()
        socat_port = get_socat_port(False)
        socat_log_file = str(socat_port) + '_' + str(command_id)
        dest_cmd = 'socat -d -lf %s/%s -u tcp-listen:%s,reuseaddr file:%s/%s,trunc,creat' % (socat_log_dir, socat_log_file, socat_port, file_to_dir, file_name_to)
        dest_socat_ret_code = [1]
        th = run_socat_receiver_on_backup_dest(dest_conn, dest_cmd, command_id, False, dest_socat_ret_code)
        if th == None:
            dest_conn.close()
            return False

        socat_port = get_socat_port(False)
        conn.execute('cat %s | socat - TCP4:%s:%s,retry=%s' % (file_name_from, dest_ip, socat_port, socat_retry_count), timeout=conn_timeout)
        print_debug_info('transfering file: %s' % (file_name_from), '')
        th.join()

        del_exec_command_info(command_id)
    else:
        cmd = "scp -P%s %s %s@%s:%s/%s" % (dest_port, file_name_from, dest_user, dest_ip, file_to_dir, file_name_to)
        command_id = get_command_id()
        command_info = ExecCommandInfo(dest_ip, dest_user, dest_port, dest_pswd, cmd)
        if not add_exec_command_info(command_id, command_info):
            return False
        conn.add_watcher(r'.*password.*', dest_pswd+'\n')
        print_debug_info('%s to %s:%s/%s' % (file_name_from, dest_ip, file_to_dir, file_name_to), 'backup file')
        conn.execute(cmd)
        del_exec_command_info(command_id)
    return True

```



### 备份backup.conf

> backup_curr_backup_config
>
- cd /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240221031122.943-full && touch backup.conf && echo xxx > backup.conf
```python
def backup_curr_backup_config():
    if is_backup_need_stop():
        return
    print_trace_info(sys._getframe().f_code.co_name, "start")
    global backup_dest_dir
    global backup_conf_file

    backup_conf_content = ''
    with open (backup_conf_file, "r") as conf_file:
        backup_conf_content = conf_file.read()
    dest_server_conn = get_one_dest_server_conn()
    dest_server_conn.execute("cd %s && touch backup.conf && echo '%s' > backup.conf" % (backup_dest_dir, backup_conf_content))
    dest_server_conn.close()
    print_trace_info(sys._getframe().f_code.co_name, "finish")


```

### 加密
> encrypt_backup_dir


```python
def encrypt_backup_dir():
    if is_backup_need_stop():
        return
    print_trace_info(sys._getframe().f_code.co_name, "start")
    global backup_dest_dir
    global backup_id
    global backup_type
    global conn_timeout

    tar_name = backup_id + '-' + backup_type
    if args.password is None:
        print_trace_info(sys._getframe().f_code.co_name, "finish")
        return
    dest_server_conn = get_one_dest_server_conn()
    dest_server_conn.execute('cd %s && tar --create --file %s.tar --exclude=cluster_consistence_info --exclude=consistent_point_datetime --exclude=backuped_binlogs --remove-files *' % (backup_dest_dir, tar_name), timeout=conn_timeout)
    dest_server_conn.execute("cd %s && echo 'abc123' > sample" % (backup_dest_dir))
    dest_server_conn.execute('cd %s && pyAesCrypt -p %s -e ./%s.tar -o ../encrypted/%s.tar.encrypted' % (backup_dest_dir, args.password, tar_name, tar_name), timeout=conn_timeout)
    dest_server_conn.execute('cd %s && pyAesCrypt -p %s -e ./sample -o encrypted_sample' % (backup_dest_dir, args.password), timeout=conn_timeout)
    dest_server_conn.execute('cd %s && rm %s.tar sample' % (backup_dest_dir, tar_name))

    print_trace_info(sys._getframe().f_code.co_name, "finish")
```

### 标记ok
> mark_backup_dest_dir_with_ok_flag
>
- echo 'ok' > /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240221031122.943-full/ok

```python
def mark_backup_dest_dir_with_ok_flag():
    if is_backup_need_stop():
        return
    print_trace_info(sys._getframe().f_code.co_name, "start")
    global backup_dest_dir
    dest_server_conn = get_one_dest_server_conn()
    cmd = "echo 'ok' > %s/ok" % backup_dest_dir
    ret = dest_server_conn.execute(cmd)
    dest_server_conn.close()
    if ret[1] != '':
        fatal_error("fail to mark backup dest dir with ok flag: %s" % (ret[1]))
    dest_server_conn.close()
    global mark_ok_flag
    mark_ok_flag = True
    print_trace_info(sys._getframe().f_code.co_name, "finish")
```



## 备份源码
```python
#!/usr/bin/python3
# -*- coding: utf-8 -*-

tool_version = '1.0.6039.4a538e36'

from collections import OrderedDict
from datetime import datetime
from pymysql.cursors import DictCursorMixin, Cursor, SSCursor
import configparser
import pymysql
import sys
import threading
import time
import traceback
import argparse
import re
import Conn
import base64

desc = '''
sample backup config file:
    [misc]
    force-full-backup=0
    debug=1
    log-audit=0
    socat-port=9999
    socat-port2=9998
    # The socat port is occupied or socat network exception, backup tool retry times
    # no need to retry, please configure to 0
    socat-retry-count = 10
    compress=1
    compress-threads=5
    max-time-sec=1800
    backup-master=1
    # connect-method can config : ssh or rabbit-agent or adm-agent
    connect-method = ssh
    conn-timeout = 3600
    # backup slave num (slave num of every datasource) or slave server list seperated with comma
    #backup-slave = 2
    # xtrabackup send flush tables with read lock will block when long trx
    # quote timeout and retry to fix this.
    # xtrabackup execute 'flush table with read lock' may cause dead lock
    # ftwrl-dead-lock-retry is used to retry xtrabackup times, default value is 10
    # ftwrl-dead-lock-seconds is used to set the seconds to send kill after existing ftwrl lock, default value is 8
    ftwrl-dead-lock-retry = 10
    ftwrl-dead-lock-seconds = 8
    # use disaster recovery cluster
    use-disaster-recovery-cluster = false
    # use the specified xtrabackup binary dir, default is /usr/bin/xtrabackup
    xtrabackup-bin-dir = /usr/bin/xtrabackup
    # if backup failed last time, store the bad backup file to trash or not before backup start
    store-bad-backup = 1

    [rabbit-agent]
    rabbitmq-uri = rabbit://admin:admin@172.16.70.234/

    [adm-agent]
    port = 19999

    [cluster]
    ip = 172.16.70.234
    user = root
    port = 3307
    password =

    [server-ssh-info]
    user = backup
    password = 123
    port = 22

    [backup-store]
    ip = 172.16.70.234
    port = 22
    user = backup
    password = 123
    dir = /tmp/backup
'''

parser = argparse.ArgumentParser(description=desc, formatter_class=argparse.RawDescriptionHelpFormatter)
parser.add_argument('--config-file', help='specified the config file')
parser.add_argument('-p', '--password', help='password for encrypt the backup dir')
parser.add_argument('--purge-to', help="purge old backups until specified datetime formated like '2020-01-01 10:10:10'")
parser.add_argument('--clean-trash', action='store_true', help='remove all bad backups and purged backups')
parser.add_argument('-v', '--version', action='store_true', help='show version')
args = parser.parse_args()

backup_type = 'inc' #by default this is an inc backup
xtrabackup_version=8026
debug = True
log_audit = False
backup_id = ""
backup_conf_file = 'backup.conf'
compress = False
compress_threads = '1'
backup_master = False
backup_slave_num = None
backup_slaves = set()
xtrabackup_bin_dir = "xtrabackup"
store_bad_backup = True

cluster_config = None
cluster_conn = None
cluster_cursor = None

replication_source_list = None
rep_source_ms_info = {}
mgr_source_list = None
dataserver_list = None
dataserver_info = {}
dataserver_user_pswd_map = {}
global_consistence_info = None

backup_dest_ip = None
backup_dest_port = None
backup_dest_user = None
backup_dest_pswd = None
backup_dest_dir = None
backup_base_dir = None
last_good_backup = ''
socat_use_lock = threading.Lock()

connect_failed_servers = set()
skipped_servers = set()
curr_server_list = []
slave_binlog_file = {}
slave_binlog_exec_pos = {}
slave_executed_gtids = {}

mysql_server_user_map = {}
mysql_server_password_map = {}

socat_port = 0   #socat transfer xstream
socat_port2 = 0  #socat transfer file
using_socat = True
conn_timeout = 60*60 #seconds
socat_log_dir = ''
socat_retry_count = 10
# connection method, 0 ssh, 1 rabbit-agent, 2 adm-agent
connect_method = 0
# ssh
dbscale_server_user = None
dbscale_server_pswd = None
dbscale_server_port = None
#rabbit-agent
rabbit_mq_uri = ''
# adm-agent
adm_server_port = None


#timing to stop backup
backup_get_execption = False
backup_start_time = int(time.time())
backup_need_stop = False
mark_ok_flag = False
backup_exec_timeout = -1
timing_thread_sleep_time = 5 #seconds
command_id = 1
command_id_lock = threading.Lock()
add_command_info_lock = threading.Lock()
command_dict = {}

ftwrl_dead_lock_retry = 10
ftwrl_dead_lock_seconds = 8
ftwrl_server_dict = {}
ftwrl_locks = []
ftwrl_status = []
detect_ftwrl_lock_result = []

use_disaster_recovery_cluster = False
cluster_dr_exclude_server = ''

enum_ftwrl_status = {
    "XTRABACKUP_UNEXECUTE" : 0,
    "XTRABACKUP_EXECUTE_COMPLETED" : 1,
    "XTRABACKUP_EXECUTE_UNKNOW_ERROR" : 2
}

enum_detect_ftwrl_lock_result = {
    "DETECT_FTWRL_RESULT_UNDETECT" : 0,
    "DETECT_FTWRL_RESULT_SUCCESSED" : 1,
    "DETECT_FTWRL_RESULT_NEED_RETRY" : 2
}

def get_ftwrl_status(i):
    ftwrl_locks[i].acquire()
    tmp_status = ftwrl_status[i]
    ftwrl_locks[i].release()
    return tmp_status

def set_ftwrl_status(tmp_status, i):
    ftwrl_locks[i].acquire()
    ftwrl_status[i] = tmp_status
    ftwrl_locks[i].release()

class ServerGlobalConsistencePointInfo:
    def __init__(self,
            server_name,
            ds_name,
            ip_port,
            global_cp_binlog_filename,
            global_cp_binlog_pos,
            xb_to_lsn,
            xb_to_binlog_file_name,
            xb_to_binlog_pos):
        self.server_name = server_name
        self.ds_name = ds_name
        self.ip_port = ip_port
        self.global_cp_binlog_filename = global_cp_binlog_filename
        self.global_cp_binlog_pos = global_cp_binlog_pos
        self.xb_to_lsn = xb_to_lsn
        self.xb_to_binlog_file_name = xb_to_binlog_file_name
        self.xb_to_binlog_pos = xb_to_binlog_pos

global_consistence_infos = {}

def get_socat_port(is_socat_xstream_port):
    global socat_port
    global socat_port2
    if is_socat_xstream_port == True:
        return socat_port
    else:
        return socat_port2

def set_socat_port(is_socat_xstream_port, socat_listen_port):
    global socat_port
    global socat_port2
    if is_socat_xstream_port == True:
        socat_port = socat_listen_port
    else:
        socat_port2 = socat_listen_port

def is_backup_need_stop():
    global backup_need_stop
    return backup_need_stop

def get_command_id():
    global command_id
    global command_id_lock
    command_id_lock.acquire()
    res_id = command_id
    command_id += 1
    command_id_lock.release()
    return res_id

def add_exec_command_info(command_id, command_info):
    global add_command_info_lock
    global command_dict
    global backup_need_stop
    add_command_info_lock.acquire()
    if not backup_need_stop:
        print_debug_info("add command id %d and command is %s" % (command_id, command_info.to_string()), '')
        command_dict[command_id] = command_info
        add_command_info_lock.release()
        return True
    else:
        print_debug_info("backup need stop, due to timeout, no more command will handle: %s" % (command_info.to_string()), '')
        add_command_info_lock.release()
        return False

def del_exec_command_info(command_id):
    global add_command_info_lock
    global command_dict
    add_command_info_lock.acquire()
    try:
        print_debug_info("delete command id %d" % (command_id), '')
        command_dict.pop(command_id)
    except Exception as e:
        pass
    add_command_info_lock.release()

class ExecCommandInfo:
    def __init__(self, host = '', user = '', port = '', pswd = '', cmd = ''):
        self.exec_host = host
        self.exec_port = port
        self.exec_user = user
        self.exec_pswd = pswd
        self.origin_cmd = cmd
        tmp_cmd = cmd
        pos = tmp_cmd.find('>')
        if pos != -1:
            tmp_cmd = tmp_cmd[0:pos]
        pos = tmp_cmd.find('|')
        if pos != -1:
            tmp_cmd = tmp_cmd[0:pos]
        self.exec_cmd  = tmp_cmd.strip()
    def get_conn(self):
        if len(self.exec_host):
            return get_one_server_conn(self.exec_host, self.exec_user, self.exec_port, self.exec_pswd)
        else:
            return get_one_dest_server_conn()
    def get_cmd(self):
        return self.exec_cmd
    def get_host(self):
        if len(self.exec_host):
            return self.exec_host
        else:
            global backup_dest_ip
            return backup_dest_ip
    def to_string(self):
        return "host:" + self.exec_host + ",port:" + str(self.exec_port) + \
               ",user:" + self.exec_user + ",pswd:" + self.exec_pswd + \
               ",cmd:" + self.origin_cmd

def reload_backup_exec_timeout():
    global backup_conf_file

    config = configparser.ConfigParser()
    config.read(backup_conf_file)

    global backup_exec_timeout
    backup_exec_timeout_str = config['misc'].get('max-time-sec')
    if backup_exec_timeout_str is not None and backup_exec_timeout_str.isdigit():
        backup_exec_timeout = int(backup_exec_timeout_str)

def timing_to_stop_backup():
    global add_command_info_lock
    global command_dict
    global backup_need_stop
    global timing_thread_sleep_time
    global backup_exec_timeout
    global mark_ok_flag
    global conn_timeout
    global backup_get_execption

    while True:
        if mark_ok_flag:
            break
        time.sleep(timing_thread_sleep_time)
        current_time = int(time.time())
        reload_backup_exec_timeout()
        if current_time - backup_start_time >= backup_exec_timeout or backup_get_execption:
            backup_need_stop = True
            print_trace_info("Set backup_need_stop, stop backup",'')
            add_command_info_lock.acquire()
            for id, cmd_info in command_dict.items():
                print_debug_info("killing command_id %d, command info %s" % (id, cmd_info.to_string()),'')
                conn = cmd_info.get_conn()
                try:
                    ret = conn.execute('ps ajx | grep "%s" | grep -v "grep"' % (cmd_info.get_cmd()), timeout=conn_timeout)[0]
                    print_debug_info(ret,'')
                    if len(ret) > 0 and len(ret.split()) > 3:
                        pid_to_kill = int(ret.split()[1])
                    print_debug_info("Prepare to kill process %d on host %s" % (pid_to_kill, cmd_info.get_host()),'')
                    ret = conn.execute('kill -9 %d' % (pid_to_kill), timeout=conn_timeout)[0]
                    print_debug_info("end to kill process %d on host %s" % (pid_to_kill, cmd_info.get_host()),'')
                except Exception as e:
                    print_err_info(e.args, "fail to do kill for command info %s. due to" % (cmd_info.to_string()))
            add_command_info_lock.release()
            return
    return

class OrderedDictCursor(DictCursorMixin, SSCursor):
  dict_type = OrderedDict

def init_config_from_conf_file():
    print_trace_info(sys._getframe().f_code.co_name, "start")
    global backup_id
    global connect_method
    global rabbit_mq_uri
    global adm_server_port

    global cluster_config
    global dbscale_server_user
    global dbscale_server_pswd
    global dbscale_server_port
    global conn_timeout

    global backup_dest_ip
    global backup_dest_port
    global backup_dest_user
    global backup_dest_pswd
    global backup_dest_dir
    global backup_base_dir
    global xtrabackup_bin_dir
    global store_bad_backup

    global socat_port
    global socat_port2
    global using_socat
    global debug
    global backup_type
    global backup_conf_file
    global log_audit
    global compress
    global compress_threads
    global backup_master
    global backup_slave_num
    global backup_slaves
    global socat_retry_count

    global ftwrl_dead_lock_retry
    global ftwrl_dead_lock_seconds
    global use_disaster_recovery_cluster

    backup_id = datetime.utcnow().strftime('UTC%Y%m%d%H%M%S.%f')[:-3]
    print_trace_info("current backup id: %s" % backup_id, '')

    config = configparser.ConfigParser()

    if args.config_file is not None:
        backup_conf_file = args.config_file
        print_debug_info(backup_conf_file, "use config file")
    config.read(backup_conf_file)

    if 'log-audit' in config['misc'].keys() and (config['misc']['log-audit'] == '1' or config['misc']['log-audit'] == 'true'):
        log_audit = True
    else:
        log_audit = False

    if config['misc']['debug'] == '0' or config['misc']['debug'].lower() == 'false':
        debug = False
    else:
        debug = True
    if 'force-full-backup' in config['misc'].keys() and config['misc']['force-full-backup'] == '1':
        backup_type = 'full'
    if 'compress' in config['misc'].keys() and (config['misc']['compress'] == '1' or config['misc']['compress'].lower() == 'true'):
        compress = True
    else:
        compress = False
    if 'compress-threads' in config['misc'].keys():
        compress_threads = config['misc']['compress-threads']
    if 'backup-master' in config['misc'].keys() and (config['misc']['backup-master'] == '1' or config['misc']['backup-master'].lower() == 'true'):
        backup_master = True
    backup_slave_config = config['misc'].get('backup-slave')
    if backup_slave_config is not None:
        slaves = backup_slave_config.split(",")
        if len(slaves) == 0:
            backup_slave_num = 0
        elif len(slaves) == 1:
            first_slave = slaves[0]
            if first_slave.strip().isdigit():
                backup_slave_num = int(first_slave)
        if backup_slave_num is None:
            for slave in slaves:
                slave = slave.strip()
                backup_slaves.add(slave)

    connect_method_str = config['misc'].get('connect-method').lower()
    if connect_method_str == 'ssh':
        connect_method = 0
    elif connect_method_str == 'rabbit-agent':
        connect_method = 1
    elif connect_method_str == 'adm-agent':
        connect_method = 2
    else:
        fatal_error('connection method config error, plz config ssh or rabbit-agent or adm-agent')

    if connect_method == 1:
        if config.get('rabbit-agent', 'rabbitmq-uri') is None:
            fatal_error('rabbit agent config not found')
        rabbit_mq_uri = config['rabbit-agent']['rabbitmq-uri']
    elif connect_method == 2:
        if config.get('adm-agent', 'port') is None:
            fatal_error('adm agent config not found')
        adm_server_port = config['adm-agent']['port']

    if 'socat-retry-count' in config['misc'].keys():
        socat_retry_count = int(config['misc']['socat-retry-count'])

    global backup_exec_timeout
    backup_exec_timeout_str = config['misc'].get('max-time-sec')
    if backup_exec_timeout_str is not None and backup_exec_timeout_str.isdigit():
        backup_exec_timeout = int(backup_exec_timeout_str)

    cluster_config = {
        'host': config['cluster']['ip'],
        'user': config['cluster']['user'],
        'password': config['cluster']['password'],
        'port': int(config['cluster']['port']),
        'db': 'information_schema',
        'charset': "utf8",
        'autocommit': True
    }
    print_debug_info(cluster_config, "get cluster config")

    dbscale_server_user = config['server-ssh-info']['user']
    dbscale_server_pswd = config['server-ssh-info']['password']
    dbscale_server_port = config['server-ssh-info']['port']

    backup_dest_ip = config['backup-store']['ip']
    backup_dest_port = config['backup-store']['port']
    backup_dest_user = config['backup-store']['user']
    backup_dest_pswd = config['backup-store']['password']
    backup_base_dir = config['backup-store']['dir']
    xtrabackup_bin_dir_config = config['misc'].get('xtrabackup-bin-dir')
    if xtrabackup_bin_dir_config is not None:
        xtrabackup_bin_dir = xtrabackup_bin_dir_config

    store_bad_backup_conf = config['misc'].get('store-bad-backup')
    if store_bad_backup_conf is not None and (store_bad_backup_conf.lower() == '0' or store_bad_backup_conf.lower() == 'false'):
        store_bad_backup = False

    socat_port = int(config['misc']['socat-port'])
    socat_port2 = int(config['misc']['socat-port2'])
    using_scp = config['misc'].get('using-scp')
    if using_scp is not None:
        using_socat = False

    ftwrl_dead_lock_retry_config = config['misc'].get('ftwrl-dead-lock-retry')
    ftwrl_dead_lock_seconds_config = config['misc'].get('ftwrl-dead-lock-seconds')
    if ftwrl_dead_lock_retry_config is not None:
        ftwrl_dead_lock_retry = int(ftwrl_dead_lock_retry_config)
    if ftwrl_dead_lock_seconds_config is not None:
        ftwrl_dead_lock_seconds = int(ftwrl_dead_lock_seconds_config)

    conn_timeout_str = config['misc'].get('conn-timeout')
    if conn_timeout_str is not None and conn_timeout_str.isdigit():
        conn_timeout = int(conn_timeout_str)
    if 'use-disaster-recovery-cluster' in config['misc'].keys() and (config['misc']['use-disaster-recovery-cluster'] == '1' or config['misc']['use-disaster-recovery-cluster'].lower() == 'true'):
        use_disaster_recovery_cluster = True
    print_trace_info(sys._getframe().f_code.co_name, "finish")


def print_trace_info(name, info):
    thd_id = threading.get_ident();
    if len(info) > 0:
        print("[TRACE %d %s] %s %s" % (thd_id, datetime.now(), name, info), flush=True)
    else:
        print("[TRACE %d %s] %s" % (thd_id, datetime.now(), name), flush=True)

def print_err_info(info, pre_comment):
    thd_id = threading.get_ident();
    if len(pre_comment) > 0:
        print('[ERROR %s %s] %s : %s' % (thd_id, datetime.now(), pre_comment, info), flush=True)
    else:
        print('[ERROR %s %s] %s' % (thd_id, datetime.now(), info), flush=True)

def print_debug_info(info, pre_comment):
    if debug == False:
        return
    thd_id = threading.get_ident();
    if len(pre_comment) > 0:
        print('[DEBUG %d %s] %s : %s' % (thd_id, datetime.now(), pre_comment, info), flush=True)
    else:
        print('[DEBUG %d %s] %s' % (thd_id, datetime.now(), info), flush=True)


def check_server_connectivity(server_name, host, port, user, pswd):
    conn = get_one_server_conn(host, user, port, pswd, server_name == 'backup_store_server')
    got_err = False
    mes = ''
    try:
        ret = conn.execute('echo 1')
        if ret[1] != '':
            mes = ret[1]
            got_err = True
    except Exception as e:
        got_err = True
        mes = e.args
    if got_err:
        print_debug_info("can't connect to server %s, port %s" % (host, port), mes)
        connect_failed_servers.add(server_name)
        return None
    return conn

def check_server_executable_tool(cmd, server_name, host, port, user, pswd):
    conn = get_one_server_conn(host, user, port, pswd, server_name == 'backup_store_server')
    got_err = False
    try:
        ret = conn.execute('which %s' % (cmd))
        if ret[1] != '':
            got_err = True
    except Exception as e:
        got_err = True
    conn.close()
    if got_err:
        fatal_error("%s does not exists in /usr/bin." % (cmd))

def check_mysql_connectivity(server_name, host, port, user, pswd):
    db_conn_config = {
        'host': host,
        'user': user,
        'password': pswd,
        'port': int(port),
        'db': 'information_schema',
        'charset': "utf8",
        'autocommit': True
    }
    conn = None
    try:
        conn = pymysql.connect(**db_conn_config)
    except Exception as e:
        print_debug_info("can't connect to dataserver on %s port %s user %s pswd %s cause: %s" % (host, port, user, pswd, e.args), '')
        connect_failed_servers.add(server_name)
        return (None, None)
    return (conn, conn.cursor(OrderedDictCursor))

def check_gtid_mode(conn, cursor):
    query_text = "show variables like 'gtid_mode'"
    gtid_mode = get_query_result_set(query_text, cursor)
    if gtid_mode[0]['Value'] != 'ON':
        fatal_error('dataserver must started with gtid_mode on')

def get_one_dest_server_conn():
    global backup_dest_ip
    global backup_dest_port
    global backup_dest_user
    global backup_dest_pswd
    return get_one_server_conn(backup_dest_ip, backup_dest_user, backup_dest_port, backup_dest_pswd, using_rds_taskmanager=False)


def get_one_server_conn(host, user, port, pswd, using_rds_taskmanager=False):
    global connect_method

    if connect_method == 1:
        global rabbit_mq_uri
        if using_rds_taskmanager:
            conn = Conn.AgentConn(rabbit_mq_uri, host, agent_type='rds-taskmanager')
        else:
            conn = Conn.AgentConn(rabbit_mq_uri, host, agent_type='rds-dbinit')
    elif connect_method == 2:
        global adm_server_port
        conn = Conn.DBInitConn(host, user, adm_server_port)
    else:
        conn = Conn.SSHConn(host, user, port, pswd)

    try:
        ret = conn.execute('echo 1')
    except Exception as e:
        fatal_error("get one connection from server: %s error: %s" % (host, e.args))
    if ret[0] != '1\n':
        fatal_error("fail to get server connection from here to backup dest host")
    return conn

def init_backup_dest_dir():
    if is_backup_need_stop():
        return
    print_trace_info(sys._getframe().f_code.co_name, "start")
    global backup_dest_user
    global backup_dest_ip
    global backup_dest_port
    global backup_dest_dir
    global backup_base_dir
    global backup_id
    global backup_type

    backup_dest_dir = backup_base_dir + '/' + backup_id + '-' + backup_type
    print_trace_info("current backup folder: %s" % backup_dest_dir, '')
    dest_server_conn = get_one_dest_server_conn()
    dest_server_conn.execute("mkdir -p %s/encrypted" % (backup_base_dir))
    dest_server_conn.execute("mkdir -p %s" % (backup_dest_dir))
    dest_server_conn.execute("mkdir -p %s" % (backup_dest_dir + '/datanode_base'))
    dest_server_conn.execute("mkdir -p %s" % (backup_dest_dir + '/calcnode_base'))
    print_debug_info(backup_dest_user + "@" + backup_dest_ip + ":" + backup_dest_port + ":" + backup_dest_dir, "get current backup dest dir")
    dest_server_conn.close()
    print_trace_info(sys._getframe().f_code.co_name, "finish")


def get_database_conn(conn_config):
    conn = pymysql.connect(**conn_config)
    cursor = conn.cursor(OrderedDictCursor)
    get_query_result_set('select 1', cursor)
    return (conn, cursor)


def close_database_conn(conn, cursor):
    cursor.close()
    conn.close()


def get_query_result_set(query_sql, cursor):
    cursor.execute(query_sql)
    rows = cursor.fetchall()
    return rows

def fatal_error(msg):
    print("=======stack start=======", flush=True)
    traceback.print_stack()
    print("======= stack end =======", flush=True)
    print("ERROR:", msg, flush=True)

    global backup_get_execption
    backup_get_execption = True
    sys.exit(1)

def pre_check():
    global use_disaster_recovery_cluster
    global cluster_dr_exclude_server
    global dataserver_info
    global xtrabackup_bin_dir
    print_trace_info(sys._getframe().f_code.co_name, "start")

    #check_db_cluster_status
    if use_disaster_recovery_cluster ==True:
        query_text = "dbscale show datasource type = replication"
        server_datasource = get_query_result_set(query_text, cluster_cursor)
        dis_recv_cluster_exclude_server = []
        for datasource in server_datasource:
            try:
                if datasource['SlaveDBScale'] == "Yes":
                    dis_recv_cluster_exclude_server.append(datasource['SlaveSource'])
                    continue
            except Exception as e:
                fatal_error('The column SlaveDBScale not exists, plz check backup cluster is disaster recovery cluster')
        if len(dis_recv_cluster_exclude_server) == 0:
            fatal_error('The disaster recovery cluster has no backup server')
        elif len(dis_recv_cluster_exclude_server) > 1:
            fatal_error('The disaster recovery cluster backup server more than 1')
        cluster_dr_exclude_source = dis_recv_cluster_exclude_server[0]

    query_text = "dbscale show datasource type=server"
    server_datasource = get_query_result_set(query_text, cluster_cursor)
    print_debug_info(server_datasource, "get cluster datasource with type server")
    for datasource in server_datasource:
        if datasource['Is-internal'] != "YES":
            if use_disaster_recovery_cluster ==True and cluster_dr_exclude_source == datasource['Name']:
                cluster_dr_exclude_server = datasource['Server']
                continue
            else:
                fatal_error('do not support config datasource of type server, related server datasource is [%s]' % datasource['Name'])

    # check connectivity to mysql and server
    for ms in rep_source_ms_info:
        master_server_name = rep_source_ms_info[ms]['master']
        master_server = dataserver_info[master_server_name]
        conn = check_server_connectivity(master_server_name, master_server['host'], dbscale_server_port, dbscale_server_user, dbscale_server_pswd)
        if conn is not None:
            conn.close()
        (conn, cursor) = check_mysql_connectivity(master_server_name, master_server['host'], master_server['port'], dataserver_user_pswd_map[master_server_name]['user'], dataserver_user_pswd_map[master_server_name]['password'])
        if cursor is not None:
            check_gtid_mode(conn, cursor)
        if conn is not None:
            conn.close()
        check_server_executable_tool(xtrabackup_bin_dir, master_server_name, master_server['host'], dbscale_server_port, dbscale_server_user, dbscale_server_pswd)
        find_xtrabackup_version(xtrabackup_bin_dir, master_server_name, master_server['host'], dbscale_server_port, dbscale_server_user, dbscale_server_pswd)
        slave_names = rep_source_ms_info[ms]['slaves']
        for slave_name in slave_names:
            slave = dataserver_info[slave_name]
            conn = check_server_connectivity(slave_name, slave['host'], dbscale_server_port, dbscale_server_user, dbscale_server_pswd)
            if conn is not None:
                conn.close()
            (conn, cursor) = check_mysql_connectivity(slave_name, slave['host'], slave['port'], dataserver_user_pswd_map[slave_name]['user'], dataserver_user_pswd_map[slave_name]['password'])
            if conn is not None:
                check_gtid_mode(conn, cursor)
                conn.close()
            check_server_executable_tool(xtrabackup_bin_dir, master_server_name, master_server['host'], dbscale_server_port, dbscale_server_user, dbscale_server_pswd)

    # check connectivity to backup store server
    conn = check_server_connectivity('backup_store_server', backup_dest_ip, backup_dest_port, backup_dest_user, backup_dest_pswd)
    if conn is None:
        fatal_error("can't connect to backup store server %s %s %s %s" % backup_dest_ip, backup_dest_port, backup_dest_user, backup_dest_pswd, '')
    else:
        conn.close()
    if args.password is not None:
        check_server_executable_tool('pyAesCrypt', 'backup_store_server', backup_dest_ip, backup_dest_port, backup_dest_user, backup_dest_pswd)

    # check server connectivity to dbscale server
    conn = check_server_connectivity("dbscale_server", cluster_config['host'], dbscale_server_port, dbscale_server_user, dbscale_server_pswd)
    if conn is None:
        fatal_error("can't connect to dbscale server %s %s %s %s" % cluster_config['host'], dbscale_server_port, dbscale_server_user, dbscale_server_pswd)
    else:
        conn.close()

    print_trace_info(sys._getframe().f_code.co_name, "finish")

def parse_mysql_server_user_password_from_dbscale_conf():
    global cluster_cursor
    global dbscale_server_port
    global dbscale_server_user
    global dbscale_server_pswd

    get_query_result_set("dbscale flush all config to file '/tmp/dbscale_tmp.conf'", cluster_cursor)
    conn = check_server_connectivity("dbscale_server", cluster_config['host'], dbscale_server_port, dbscale_server_user, dbscale_server_pswd)
    lines = ''
    try:
        ret = conn.execute('cat /tmp/dbscale_tmp.conf')
        lines = ret[0]
        conn.close()
    except Exception as e:
        fatal_error('cluster server user do not have enough permission to backup dbscale config file')

    pattern_for_user = '\[data-server ([a-zA-Z0-9_]+)\]\n([^\n\[\]]*\n)*user = ([a-zA-Z0-9_]+)\n'
    pattern_for_password= '\[data-server ([a-zA-Z0-9_]+)\]\n([^\n\[\]]*\n)*password = (.+)\n'
    pattern_for_encode_password= 'encode-password([\t ]*)=([\t ]*)([0-1])\n'

    prog = re.compile(pattern_for_encode_password)
    res = prog.search(lines)
    is_encode_password = False
    if res is not None:
        pos = len(res.regs) - 1
        encode_password = lines[res.regs[pos][0]:res.regs[pos][1]]
        if encode_password == "1":
            is_encode_password = True

    prog = re.compile(pattern_for_user)
    res = prog.search(lines)
    while res is not None:
        data_server_name = lines[res.regs[1][0]:res.regs[1][1]]
        user = lines[res.regs[3][0]:res.regs[3][1]]
        dataserver_user_pswd_map[data_server_name] = {}
        dataserver_user_pswd_map[data_server_name]['user'] = user
        dataserver_user_pswd_map[data_server_name]['password'] = ''
        res = prog.search(lines, res.regs[0][1])

    prog = re.compile(pattern_for_password)
    res = prog.search(lines)
    while res is not None:
        data_server_name = lines[res.regs[1][0]:res.regs[1][1]]
        password = lines[res.regs[3][0]:res.regs[3][1]]

        if is_encode_password == True:
          password = base64.b64decode(password).decode("utf-8")

        dataserver_user_pswd_map[data_server_name]['password'] = password
        res = prog.search(lines, res.regs[0][1])

def get_db_cluster_info():
    print_trace_info(sys._getframe().f_code.co_name, "start")
    global replication_source_list
    global mgr_source_list
    global rep_source_ms_info
    global dataserver_info
    global curr_server_list

    try:
        query_text = "dbscale show datasource type=replication"
        replication_source_list = get_query_result_set(query_text, cluster_cursor)
        print_debug_info(replication_source_list, "get cluster datasource with type replication")
    except Exception as e:
        if (len(e.args) == 2):
            err_code, err_msg = e.args
            if err_code == 9004 and err_msg == 'No DataSource of the type.':
                print_debug_info("no datasource of type replication", "info")
            else:
                fatal_error(str(e))
#some column in below resultset was cut to fit screen width
#| Name   |SourceStatus |MasterSource     |MasterSourceStatus |MasterServer | MasterServerStatus | SlaveSource         | SlaveSourceStatus | SlaveServers | SlaveServerStatus |
#| auth   |Working      |auth_auth_server |Working            |auth_server  | Server normal      |                     |                   |              |                   |
#| rep    |Working      |rep_master       |Working            |master       | Server normal      | rep_read_slave1     | Working           | slave1       | Slave normal      |
#| rep    |             |                 |                   |             |                    | source1             | Working           |              |                   |
#| source1|Working      |source1_server1  |Working            |server1      | Slave normal       | source1_read_slave2 | Working           | slave2       | Slave normal      |
    for row in replication_source_list:
        source_name = row['Name']
        if len(row['SourceStatus']) > 0 and row['SourceStatus'] != 'Working':
            fatal_error("datasource [%s] is not Working right now" % source_name)
        if len(row['MasterSourceStatus']) > 0 and row['MasterSourceStatus'] != 'Working':
            fatal_error('MasterSourceStatus of datasource [%s] is not Working right now' % source_name)
        if len(row['MasterServerStatus']) > 0 and 'normal' not in row['MasterServerStatus']:
            fatal_error('MasterServerStatus of datasource [%s] is not normal right now' % source_name)
        #if len(row['SlaveSourceStatus']) > 0 and row['SlaveSourceStatus'] != 'Working': #TODO consider catching master
        #    fatal_error('SlaveSourceStatus of datasource [%s] is not Working right now' % source_name)
        if len(row['SlaveServerStatus']) > 0 and 'normal' not in row['SlaveServerStatus']:
            print_debug_info('slave: [%s] of datasource: [%s] is not working', '')
        if source_name not in rep_source_ms_info.keys():
            rep_source_ms_info[source_name] = {}
            rep_source_ms_info[source_name]['master'] = ''
            rep_source_ms_info[source_name]['slaves'] = []
        if len(row['MasterServer']) > 0:
            rep_source_ms_info[source_name]['master'] = row['MasterServer']
        if len(row['SlaveServers']) > 0:
            rep_source_ms_info[source_name]['slaves'].append(row['SlaveServers'])

    print_debug_info(rep_source_ms_info, "get datasource master-slave info")

    try:
        query_text = "dbscale show datasource type=mgr"
        mgr_source_list = get_query_result_set(query_text, cluster_cursor)
        print_debug_info(mgr_source_list, "get cluster datasource with type mgr")
        fatal_error("not support backup of cluster with MGR datasource yet") #TODO handle MGR
    except Exception as e:
        if (len(e.args) == 2):
            err_code, err_msg = e.args
            if err_code == 9004 and err_msg == 'No DataSource of the type.':
                print_debug_info("no datasource of type MGR", "")
            else:
                fatal_error(str(e))

#| servername  | host      | port  | username | status        | master_backup | remote_user | remote_port | max_needed_conn/max_mysql_conn | master_priority |
#| auth_server | 127.0.0.1 | 13000 | root     | Server normal | 1             | root        | 22          |                                | 0               |
#| master      | 127.0.0.1 | 13000 | root     | Server normal | 1             | root        | 22          |                                | 0               |
#| server1     | 127.0.0.1 | 13003 | root     | Slave normal  | 1             | root        | 22          |                                | 0               |
#| slave1      | 127.0.0.1 | 13001 | root     | Slave normal  | 1             | root        | 22          |                                | 0               |
#| slave2      | 127.0.0.1 | 13002 | root     | Slave normal  | 1             | root        | 22          |                                | 0               |
    try:
        query_text = "dbscale show dataservers"
        dataserver_list = get_query_result_set(query_text, cluster_cursor)
        print_debug_info(dataserver_list, "get cluster dataserver list")
    except Exception as e:
        fatal_error(str(e))

    for row in dataserver_list:
        server_name = row['servername']
        dataserver_info[server_name] = {}
        dataserver_info[server_name]['host'] = row['host']
        dataserver_info[server_name]['port'] = row['port']
        dataserver_info[server_name]['status'] = row['status']

    for data_source in rep_source_ms_info:
        master_server_name = rep_source_ms_info[data_source]['master']
        dataserver_info[master_server_name]['belong_to_datasource'] = data_source
        curr_server_list.append(master_server_name)
        for slave in rep_source_ms_info[data_source]['slaves']:
            dataserver_info[slave]['belong_to_datasource'] = data_source
            curr_server_list.append(slave)

    for server in list(dataserver_info):
        if 'belong_to_datasource' not in dataserver_info[server]:
            dataserver_info.pop(server)

    parse_mysql_server_user_password_from_dbscale_conf()
    print_debug_info(curr_server_list, 'get current server list')
    print_debug_info(dataserver_info, "get dataserver info")
    print_trace_info(sys._getframe().f_code.co_name, "finish")

def get_servers_of_source(ds_name):
    ret = []
    for server in dataserver_info:
        if dataserver_info[server]['belong_to_datasource'] == ds_name:
            ret.append(server)
    return ret

### server = (server_name, cp_binlog_filename, cp_binlog_pos)
def construct_consistence_point_info_for_server(server, is_master_server):
    consistent_point_master_server = dataserver_info[server[0]]
    ds_name = consistent_point_master_server['belong_to_datasource']
    original_master_server = rep_source_ms_info[ds_name]['master']
    server_name = server[0]
    consistence_info = ''
    if original_master_server != server[0] and is_master_server:
        print_trace_info('', 'consistent_point_master_server is not same as the master server when start backup, topo changed when backuping, should retry backup')
        fatal_error('ds: %s, consistent_point_master_server: %s, the master server when start backup: %s' % (ds_name, server_name, original_master_server))
    cur_server_info = dataserver_info[server_name]
    host = cur_server_info['host']
    port = cur_server_info['port']
    ip_port = host + ':' + port
    global_consistence_point_binlog_filename = server[1]
    global_consistence_point_binlog_pos = server[2]
    xb_to_lsn = cur_server_info['xb_to_lsn']
    if is_master_server:
        xb_to_binlog_filename = cur_server_info['xb_to_binlog_name']
        xb_to_binlog_pos = cur_server_info['xb_to_binlog_pos']
    else:
        xb_to_binlog_filename = slave_binlog_file[server_name]
        xb_to_binlog_pos = "4"

    consistence_info = ip_port \
            + '\t' + server_name \
            + '\t' + global_consistence_point_binlog_filename \
            + '\t' + global_consistence_point_binlog_pos \
            + '\tbelong-to-datasource=' + ds_name \
            + '\txb-to-lsn=' + xb_to_lsn \
            + '\txb-to-binlog-name=' + xb_to_binlog_filename \
            + '\txb-to-binlog-pos=' + xb_to_binlog_pos + '\n'
    sgcpi = ServerGlobalConsistencePointInfo(server_name, ds_name, ip_port,
            global_consistence_point_binlog_filename, global_consistence_point_binlog_pos,
            xb_to_lsn, xb_to_binlog_filename, xb_to_binlog_pos)
    global_consistence_infos[server_name] = sgcpi
    return consistence_info

def backup_global_consistence_point():
    if is_backup_need_stop():
        return
    print_trace_info(sys._getframe().f_code.co_name, "start")
    global backup_dest_dir
    global global_consistence_info
    global backup_master
    global global_consistence_infos
    global cluster_dr_exclude_server
    global use_disaster_recovery_cluster
    rows = []
    flag = False
    try:
      query_text = "dbscale get global consistence point"
      rows = get_query_result_set(query_text, cluster_cursor)
    except Exception as e:
      flag = True
      print_debug_info('get global consistence point fail', '')
    if flag and use_disaster_recovery_cluster == True:
        for server in dataserver_info:
            if is_master_server(server):
                query_text = "show binary logs"
                rows1 = get_query_result_set(query_text, cluster_cursor)
                dic = OrderedDict()
                dic['File'] = server
                dic['Server_name'] = rows1[-1]['Log_name']
                dic['Position'] = str(rows1[-1]['File_size'])
                rows.append(dic)

    consistent_point_datetime_str = datetime.now().strftime('%Y-%m-%d %H:%M:%S')
    consistence_info = ""
    for row_tmp in rows:
        row = tuple(row_tmp.values())
        ## backup master's consistence point need it's xtrabackup info
        ## if we didn't backup master server, then we shouldn't backup master server's consistence_info
        if use_disaster_recovery_cluster == True and row[0] == cluster_dr_exclude_server:
            continue
        if backup_master:
            consistence_info += construct_consistence_point_info_for_server(row, True)
        servers = get_servers_of_source(dataserver_info[row[0]]['belong_to_datasource'])
        servers.remove(row[0])
        for server in servers:
            if is_skipped(server):
                continue
            row = (server, row[1], row[2])
            consistence_info += construct_consistence_point_info_for_server(row, False)
    consistence_info = consistence_info[:-1]
    print_debug_info(consistence_info, "get cluster global consistence point")
    dest_server_conn = get_one_dest_server_conn()
    cmd1 = "touch {0}/cluster_consistence_info && echo '{1}' >> {0}/cluster_consistence_info".format(backup_dest_dir, consistence_info)
    cmd2 = "echo '%s' >> %s/consistent_point_datetime" % (consistent_point_datetime_str, backup_dest_dir)
    dest_server_conn.execute(cmd1)
    dest_server_conn.execute(cmd2)
    dest_server_conn.close()
    global_consistence_info = consistence_info
    print_trace_info(sys._getframe().f_code.co_name, "finish")


def backup_all_server():
    if is_backup_need_stop():
        return
    print_trace_info(sys._getframe().f_code.co_name, "start")
    global backup_get_execption
    global dataserver_info
    global backup_master
    global ftwrl_server_dict
    global ftwrl_locks
    global detect_ftwrl_lock_result
    global ftwrl_status
    ths = []
    ftwrl_thread_id = 0
    for server in dataserver_info:
        server_host = dataserver_info[server]['host']
        server_port = dataserver_info[server]['port']
        server_str = server_host + ":" + str(server_port)
        ftwrl_server_dict[server_str] = ftwrl_thread_id
        ftwrl_thread_id += 1
        tmp_lock = threading.Lock()
        ftwrl_locks.append(tmp_lock)
        detect_ftwrl_lock_result.append(enum_detect_ftwrl_lock_result['DETECT_FTWRL_RESULT_UNDETECT'])
        ftwrl_status.append(enum_ftwrl_status['XTRABACKUP_UNEXECUTE'])
        if is_backup_need_stop() or backup_get_execption:
            break
        if is_skipped(server):
            print_debug_info("server: %s skipped" % server, '')
            continue
        if is_master_server(server) and not backup_master:
            make_server_backup_dir(server)
            print_debug_info("server: %s skipped" % server, '')
            continue
        #backup_one_host(server)
        th = threading.Thread(target=backup_one_host, args=(server,))
        th.start()
        ths.append(th)
    print_trace_info(sys._getframe().f_code.co_name, "in-progress")
    for th in ths:
        th.join()
    print_trace_info(sys._getframe().f_code.co_name, "finish")


def transfer_file_in_conn(conn: Conn.BackupConn, dest_ip, file_name_from, file_to_dir, file_name_to, **kargs):
    global using_socat
    global conn_timeout
    global socat_log_dir
    global socat_retry_count

    if is_backup_need_stop():
        return False

    dest_pswd = kargs.get('dest_pswd')
    dest_port = kargs.get('dest_port')
    dest_user = kargs.get('dest_user')
    if using_socat:
        dest_conn = get_one_server_conn(dest_ip, dest_user, dest_port, dest_pswd, using_rds_taskmanager=False)
        command_id = get_command_id()
        socat_port = get_socat_port(False)
        socat_log_file = str(socat_port) + '_' + str(command_id)
        dest_cmd = 'socat -d -lf %s/%s -u tcp-listen:%s,reuseaddr file:%s/%s,trunc,creat' % (socat_log_dir, socat_log_file, socat_port, file_to_dir, file_name_to)
        dest_socat_ret_code = [1]
        th = run_socat_receiver_on_backup_dest(dest_conn, dest_cmd, command_id, False, dest_socat_ret_code)
        if th == None:
            dest_conn.close()
            return False

        socat_port = get_socat_port(False)
        conn.execute('cat %s | socat - TCP4:%s:%s,retry=%s' % (file_name_from, dest_ip, socat_port, socat_retry_count), timeout=conn_timeout)
        print_debug_info('transfering file: %s' % (file_name_from), '')
        th.join()

        del_exec_command_info(command_id)
    else:
        cmd = "scp -P%s %s %s@%s:%s/%s" % (dest_port, file_name_from, dest_user, dest_ip, file_to_dir, file_name_to)
        command_id = get_command_id()
        command_info = ExecCommandInfo(dest_ip, dest_user, dest_port, dest_pswd, cmd)
        if not add_exec_command_info(command_id, command_info):
            return False
        conn.add_watcher(r'.*password.*', dest_pswd+'\n')
        print_debug_info('%s to %s:%s/%s' % (file_name_from, dest_ip, file_to_dir, file_name_to), 'backup file')
        conn.execute(cmd)
        del_exec_command_info(command_id)
    return True

def make_server_backup_dir(server_name):
    server_info = dataserver_info[server_name]
    host = server_info['host']
    port = server_info['port']
    ds = server_info['belong_to_datasource']
    server_backup_dir_name = ds + '-' + server_name + '-' + host + '-' + port
    cmd = 'mkdir -p %s/%s' % (backup_dest_dir, server_backup_dir_name)
    conn = get_one_dest_server_conn()
    conn.execute(cmd)
    conn.close()

# every 1s read the log, if find 'log scanned up to' more than ftwrl_dead_lock_seconds times, find and send kill query
def detect_ftwrl_lock(conn, log_file, db_conn, db_cursor, index, server_name):
    global ftwrl_dead_lock_seconds
    global backup_get_execption
    global detect_ftwrl_lock_result
    print_trace_info('detect_ftwrl_lock thread begin', 'start, server: %s' % server_name)
    log_times = 0
    has_ftwrl_dead_lock = False
    query_id = []
    check_log_file_exist = False
    while True:
        if backup_get_execption:
            print_trace_info('detect_ftwrl_lock get backup_get_execption', 'finish, server: %s' % server_name)
            detect_ftwrl_lock_result[index] = enum_detect_ftwrl_lock_result['DETECT_FTWRL_RESULT_UNDETECT']
            return
        if get_ftwrl_status(index) == enum_ftwrl_status['XTRABACKUP_EXECUTE_COMPLETED']:
            print_trace_info('detect_ftwrl_lock get status RESULT_SUCCESSED thread end', 'finish, server: %s' % server_name)
            detect_ftwrl_lock_result[index] = enum_detect_ftwrl_lock_result['DETECT_FTWRL_RESULT_SUCCESSED']
            return
        elif get_ftwrl_status(index) == enum_ftwrl_status['XTRABACKUP_EXECUTE_UNKNOW_ERROR']:
            print_trace_info('detect_ftwrl_lock get status RESULT_NEED_RETRY thread end', 'finish server: %s' % server_name)
            detect_ftwrl_lock_result[index] = enum_detect_ftwrl_lock_result['DETECT_FTWRL_RESULT_NEED_RETRY']
            return
        if has_ftwrl_dead_lock:
            processlist_info = get_query_result_set('select * from information_schema.processlist where info like "%flush tables with read lock%" and info not like "%processlist%"', db_cursor)
            for row in processlist_info:
                info = row['INFO'].lower()
                if "flush tables with read lock" in info:
                    query_id.append(str(row['ID']))
            if len(query_id) == 0:
                has_ftwrl_dead_lock = False
                time.sleep(1)
                continue

            print_trace_info('detect_ftwrl_lock execute kill: %s' % (' '.join(query_id)), 'server: %s' % server_name)
            try:
                for id in query_id:
                    db_cursor.execute('kill %s' % (id))
            except Exception as e:
                print_err_info('kill flush tables with read lock query_id: [%s] failed: [%s]' % (' '.join(query_id), e), 'server: %s' % server_name)
            detect_ftwrl_lock_result[index] = enum_detect_ftwrl_lock_result['DETECT_FTWRL_RESULT_NEED_RETRY']
            return
        if check_log_file_exist == False:
            check_ret = conn.execute("if test -e %s; then   echo '%s'; else   echo '%s'; fi" % (log_file, "yes", "no"))[0]
        if 'no' in check_ret:
            time.sleep(1)
            continue
        else:
            check_log_file_exist = True
        tail_ret = conn.execute('tail -n500 %s' % log_file)[0]
        ret = tail_ret.split('\n')
        while len(ret) > 0 and ret[-1] == "":
            ret.pop()
        if len(ret) == 0:
            time.sleep(1)
            continue
        if "log scanned up to" in ret[-1]:
            log_times += 1
        elif "completed OK" in ret[-1]:
            detect_ftwrl_lock_result[index] = enum_detect_ftwrl_lock_result['DETECT_FTWRL_RESULT_SUCCESSED']
            return
        last_log = ret[-1]
        if log_times >= ftwrl_dead_lock_seconds:
            for log in reversed(ret):
                if "Executing FLUSH TABLES WITH READ LOCK" in log and "log scanned up to" in last_log:
                    print_trace_info('detect_ftwrl_lock find has_ftwrl_dead_lock', 'server: %s' % server_name)
                    has_ftwrl_dead_lock = True
                    break
                elif "log scanned up to" in log and "log scanned up to" in last_log:
                    last_log = log
                else:
                    log_times = 0
                    break
        time.sleep(1)
    print_trace_info('detect_ftwrl_lock thread end', 'finish, server: %s' % server_name)
    return

def xtrabackup_command_execute(conn, source_cmd, index, server_str):
    print_trace_info('xtrabackup_command_execute thread begin', 'start, server: %s' % server_str)
    global conn_timeout
    try:
        ret = conn.execute(source_cmd, timeout=conn_timeout)
        print_debug_info('execute xtrabackup: [%s] ret: [%s]' % (source_cmd, ret), 'server: %s' % server_str)
        set_ftwrl_status(enum_ftwrl_status['XTRABACKUP_EXECUTE_COMPLETED'], index)
    except Exception as e:
        set_ftwrl_status(enum_ftwrl_status['XTRABACKUP_EXECUTE_UNKNOW_ERROR'], index)
        print_trace_info('xtrabackup_command_execute thread end and set status XTRABACKUP_EXECUTE_UNKNOW_ERROR', 'finish, server: %s' % server_str)
        print_err_info('xtrabackup_command_execute execute command %s failed: [%s]' % (source_cmd, e), 'server: %s' % server_str)
        return
    print_trace_info('xtrabackup_command_execute thread end', 'finish, server: %s' % server_str)

def clean_before_rextrabackup(dest_server_conn, server_name, backupfile_dir_name, belong_to_datasource):
    global socat_port
    global dataserver_info
    global backup_dest_dir
    global socat_log_dir
    global socat_log_file

    cmd = "mkdir -p %s/%s" % (backup_dest_dir, backupfile_dir_name)
    dest_server_conn.execute(cmd)

    is_slave = False
    # write master slave ip:port file
    rep = rep_source_ms_info[belong_to_datasource]
    master_server_name = rep['master']
    if master_server_name == server_name:
        slave_server_names = rep['slaves']
        master_server = dataserver_info[master_server_name]
        master_slave_str = master_server['host'] + ":" + master_server['port'] + "\n"
        for slave_server_name in slave_server_names:
            slave_server = dataserver_info[slave_server_name]
            master_slave_str += slave_server['host'] + ":" + slave_server['port'] + "\n"
        master_slave_str = master_slave_str[:-1]
        cmd = "echo '%s' > %s/%s/master_slave" % (master_slave_str, backup_dest_dir, backupfile_dir_name)
        dest_server_conn.execute(cmd)
    else:
        is_slave = True
        master_server = dataserver_info[master_server_name]
        rep_master_backup_dir_name = belong_to_datasource + '-' + master_server_name + '-' + master_server['host'] + '-' + master_server['port']
        cmd = "echo '%s' > %s/%s/master_dir" % (rep_master_backup_dir_name, backup_dest_dir, backupfile_dir_name)
        dest_server_conn.execute(cmd)

    command_id = get_command_id()
    socat_log_file = str(socat_port) + '_' + str(command_id)
    dest_cmd = "socat -d -lf %s/%s -u tcp-listen:%d,reuseaddr stdout > %s/%s/backup.xbsteam &" % (socat_log_dir, socat_log_file, socat_port, backup_dest_dir, backupfile_dir_name)
    dest_socat_ret_code = [1]
    th = run_socat_receiver_on_backup_dest(dest_server_conn, dest_cmd, command_id, True,  dest_socat_ret_code)
    if th == None:
        fatal_error("clean_before_rextrabackup find execute socat command error.")
    return (is_slave, command_id, th)

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
            if th.isAlive():
                print_err_info("backup_one_host get socat thread is alive, join timeout.", 'server: %s' % server_str)
            break
        elif xtrabackup_exec_result == enum_detect_ftwrl_lock_result['DETECT_FTWRL_RESULT_NEED_RETRY']:
            # result_index == 1 means need to retry
            th.join(timeout = 60)
            if th.isAlive():
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


def run_socat_receiver_on_backup_dest(conn, cmd, command_id, is_socat_xstream_port, ret_code):
    global socat_log_dir
    global socat_retry_count
    def runOnBackupDest(conn, cmd, ret_code):
        global conn_timeout
        try:
            conn.log_execute_info_once()
            ret = conn.execute(cmd, timeout=conn_timeout)
        except Exception as e:
            return
        ret_code[0] = 0

    tmp_conn = get_one_dest_server_conn()
    can_retry_times = socat_retry_count
    socat_port_get = get_socat_port(is_socat_xstream_port)
    socat_port_tmp = socat_port_get
    dest_cmd = cmd
    while True and can_retry_times > -1:
        print_trace_info("run_socat_receiver_on_backup_dest retry to run socat use socat_port %d." % (socat_port_get),"")
        str_rep = re.compile(str(socat_port_tmp))
        dest_cmd = str_rep.sub(str(socat_port_get), dest_cmd)
        socat_log_file = str(socat_port_get) + '_' + str(command_id)
        socat_port_tmp = socat_port_get
        command_info = ExecCommandInfo(cmd = dest_cmd)
        if not add_exec_command_info(command_id, command_info):
            tmp_conn.close()
            print_trace_info("run_socat_receiver_on_backup_dest add_exec_command_info faild, return the None thread.","")
            return
        th = threading.Thread(target=runOnBackupDest, args=(conn, dest_cmd, ret_code))
        th.start()
        print_trace_info("run_socat_receiver_on_backup_dest execute socat dest_cmd :%s." % (dest_cmd),"")
        i = 0
        print_trace_info("run_socat_receiver_on_backup_dest cat socat_log_file %s/%s." % (socat_log_dir, socat_log_file),"")
        while True and i <= 100:
            i = i + 1
            try:
                ret = tmp_conn.execute('ps ajx | grep "socat -d -lf %s/%s -u tcp-listen:%d" | grep -v "grep"' % (socat_log_dir, socat_log_file, socat_port_get))[0]
            except Exception as e:
                continue
            if ret != '':
                tmp_conn.close()
                set_socat_port(is_socat_xstream_port, socat_port_get)
                print_trace_info("run_socat_receiver_on_backup_dest execute socat successed set_socat_port %d." % (socat_port_get),"")
                return th
        if i > 100:
            try:
                ret = tmp_conn.execute('cat %s/%s | grep "Address already in use" | grep -v "grep"' % (socat_log_dir, socat_log_file), timeout=conn_timeout)[0]
            except Exception as e:
                tmp_conn.close()
                fatal_error("failed to start socat but not the port is occupied, please check the log file %s/%s" % (socat_log_dir, socat_log_file))
            if ret != '':
                print_trace_info("find the socat port is occupied, change socat_port and try again", 'socat log info:')
                socat_port_get = socat_port_get + 10
                can_retry_times = can_retry_times - 1

    fatal_error("failed to start socat when retry %d times" % (socat_retry_count))

def get_last_binlog_of_last_good_backup(server_name):
    if is_backup_need_stop():
        return
    global backup_base_dir

    if len(last_good_backup) == 0:
        return ''
    else:
        last_good_backup_dir = backup_base_dir + '/' + last_good_backup
        try:
            ret = get_one_dest_server_conn().execute('cat %s/backuped_binlogs' % (last_good_backup_dir))
            lines = ret[0].split('\n')[:-1]
            for line in lines:
                server_and_binlogs = line.split(':')
                server = server_and_binlogs[0]
                if server == server_name:
                    binlogs = server_and_binlogs[1]
                    binlogs = binlogs.split(' ')
                    binlogs.sort()
                    print_debug_info("the last binlog of last good backup for server: %s is: %s"% (server_name, binlogs[-1]), '')
                    return binlogs[-1]
        except:
            return ''


def backup_binlog_for_server(server_name):
    if is_backup_need_stop():
        return
    print_trace_info(sys._getframe().f_code.co_name, "start")
    global dbscale_server_user
    global dbscale_server_pswd
    global dbscale_server_port

    global backup_dest_ip
    global backup_dest_port
    global backup_dest_user
    global backup_dest_pswd
    global backup_dest_dir

    global connect_method
    global adm_server_port

    host = dataserver_info[server_name]['host']
    port = dataserver_info[server_name]['port']
    belong_to_datasource = dataserver_info[server_name]['belong_to_datasource']
    db_conn_config = {
            'host': host,
            'user': dataserver_user_pswd_map[server_name]['user'],
            'password': dataserver_user_pswd_map[server_name]['password'],
            'port': int(port),
            'db': 'information_schema',
            'charset': "utf8",
            'autocommit': True
            }
    (conn, cursor) = get_database_conn(db_conn_config)
    query_text = 'show binary logs'
    binary_logs = get_query_result_set(query_text, cursor)
    query_text = "show global variables like 'log_bin_basename'"
    log_bin_basename = get_query_result_set(query_text, cursor)[0]['Value']

    if len(binary_logs) == 0:
        fatal_error('failed to list binlogs')
    binlogs = []
    for b in binary_logs:
        binlogs.append(b['Log_name'])
    binlogs.sort()
    print_debug_info('all binlogs for server %s : %s' % (server_name, ' '.join(binlogs)), '')
    server_backup_dir = belong_to_datasource + '-' + server_name + '-' + host + '-' + port
    last_binlog_of_last_good_backup = get_last_binlog_of_last_good_backup(server_name)
    if last_binlog_of_last_good_backup is None:
        last_binlog_of_last_good_backup = binlogs[0]
    (cur_start_binlog, cur_stop_binlog) = get_dataserver_start_stop_binlog(server_name) # greatdb-bin.000043  greatdb-bin.000044
    ####### when backup-master ############
    # for master server
    #   if is a full backup
    #       we will copy binlog from xb-to-binlog-name to consistent-point binlog name
    #   else
    #       we will copy binlog from the last binlog of last good backup to the consistent-point binlog name of this backup
    # for slave server
    #   if full bakcup
    #       we will copy binlogs from xb-to-binlog-name
    #   else
    #       the start binlog file is the last binlog of last backup
    #   both full and inc backup, we can't determine the stop binlog file, so we copy all the binlogs exists in it's datadir
    #
    ###### when not backup-master #########
    # for master server
    #   binlogs of master server will be used when recover slave servers
    #   the start binlog file is fetch by the `SHOW SLAVE STATUS`.Relay_Master_Log_File in every slave
    #   and the start binlog file should be the smallest index of the Relay_Master_Log_Files
    #   if full backup
    #       we will backup binlogs from Relay_Master_Log_File to consistent_point binlog file
    #   else
    #       we will backup binlogs from min(Relay_Master_Log_File,last binlog of last good backup) to consistent_point binlog file
    #       min is used to prevent binlog gaps between last backup and this backup
    # for slave server (skipped)
    #   we do not backup binlogs of slave servers
    #######################################
    if backup_type == 'full': ## last binlog of last good backup is useless here
        last_binlog_of_last_good_backup = cur_start_binlog
    elif not backup_master: ## inc backup and not backup master
        last_binlog_of_last_good_backup = min(last_binlog_of_last_good_backup, cur_start_binlog)
    start_index = binlogs.index(last_binlog_of_last_good_backup)
    stop_index = 0
    if cur_stop_binlog == '':
        stop_index = len(binlogs)
    else:
        stop_index = binlogs.index(cur_stop_binlog) + 1
    binlogs_need_copy = binlogs[start_index:stop_index]
    dest_server_conn = get_one_dest_server_conn()
    save_backuped_binlogs_cmd = 'echo -e "%s" >> %s/backuped_binlogs' % (server_name + ':' + ' '.join(binlogs_need_copy), backup_dest_dir)
    try:
        ret = dest_server_conn.execute(save_backuped_binlogs_cmd)
        if ret[1] != '':
            fatal_error('writing backuped binlogs failed: %s' % ret[1])
    except Exception as e:
        fatal_error("writing backuped binlogs failed: %s" % str(e.args))
    print_debug_info('server: %s binlogs need copy: %s' % (server_name, ' '.join(binlogs_need_copy)), '')
    log_bin_dir = log_bin_basename[0:-len(binlogs_need_copy[0][0:-7])]
    print_debug_info(log_bin_dir, 'get log_bin_dir of dataserver %s' % (server_name))

    source_server_conn = get_one_server_conn(host, dbscale_server_user, dbscale_server_port, dbscale_server_pswd)
    backupfile_dir_name = belong_to_datasource + '-' + server_name + '-' + host + '-' + port
    print_debug_info('', "backup binlog file of dataserver %s" % (server_name))
    for binlog_file in binlogs_need_copy:
        config = {
                'dest_user': backup_dest_user,
                'dest_pswd': backup_dest_pswd,
                'dest_port': backup_dest_port
                }
        if connect_method == 2:
            config['dest_port'] = adm_server_port
        if not transfer_file_in_conn(source_server_conn, backup_dest_ip, log_bin_dir + '/' + binlog_file, backup_dest_dir + '/' + backupfile_dir_name, binlog_file, **config):
            source_server_conn.close()
            close_database_conn(conn, cursor)
            break
    source_server_conn.close()
    close_database_conn(conn, cursor)
    print_trace_info(sys._getframe().f_code.co_name, "finish")

def backup_binlog_files():
    if is_backup_need_stop():
        return
    print_trace_info(sys._getframe().f_code.co_name, "start")
    global dbscale_server_user
    global dbscale_server_pswd
    global dbscale_server_port

    global backup_dest_ip
    global backup_dest_port
    global backup_dest_user
    global backup_dest_pswd
    global backup_dest_dir

    for server in dataserver_info:
        if is_skipped(server):
            print_debug_info("dataserver %s is skipped, no need backup its binlog files" % (server), '')
            continue
        ## when not backup_master and cur server is not master, we do not backup binlogs of salve servers
        if not backup_master and not is_master_server(server):
            print_debug_info("dataserver %s is not master, and backup_master = 0, salve servers's binlogs are skipped" % (server), '')
            continue
        backup_binlog_for_server(server)
    print_trace_info(sys._getframe().f_code.co_name, "finish")

def find_start_stop_binlog_for_master_server_that_not_backup(master_name): # normal_0_1
    ds_name = dataserver_info[master_name]['belong_to_datasource'] # normal_0
    slaves = get_servers_of_source(ds_name) # normal_0_0 normal_0_2
    slaves.remove(master_name)
    start_binlogs = [] # greatdb-bin.000043
    stop_binlogs = [] # greatdb-bin.000044
    for slave in slaves:
        if not is_skipped(slave):
            start_binlogs.append(slave_binlog_file[slave])
            stop_binlogs.append(global_consistence_infos[slave].global_cp_binlog_filename)
    start_binlogs.sort()
    stop_binlogs.sort()
    if len(start_binlogs) == 0:
        return ('', '')
    return (start_binlogs[0], stop_binlogs[-1])

def get_dataserver_start_stop_binlog(server_name): # normal_0_1
    if is_backup_need_stop():
        return
    global global_consistence_info # 172.17.134.71:16315\tnormal_0_0\tgreatdb-bin.000044\t426751689\tbelong-to-datasource=normal_0\txb-to-lsn=2041207873\txb-to-binlog-name=greatdb-bin.000043\txb-to-binlog-pos=4
    ds = dataserver_info[server_name]['belong_to_datasource'] # normal_0
    master_server_name = rep_source_ms_info[ds]['master'] # normal_0_1
    if master_server_name == server_name:
        ## TODO use global_consistence_infos
        servers_info = global_consistence_info.split('\n') # '172.17.134.71:16315\tnormal_0_0\tgreatdb-bin.000044\t426751689\tbelong-to-datasource=normal_0\txb-to-lsn=2041207873\txb-to-binlog-name=greatdb-bin.000043\txb-to-binlog-pos=4'
        for line in servers_info:
            info = line.split('\t') # 0:'172.17.134.71:16315' 1:'normal_0_0' 2:'greatdb-bin.000044' 3:'426751689' 4:'belong-to-datasource=normal_0' 5:'xb-to-lsn=2041207873' 6:'xb-to-binlog-name=greatdb-bin.000043' 7:'xb-to-binlog-pos=4'
            if server_name == info[1]:
                return (info[6].split('=')[-1], info[2])
        ## if we got here, means that this master server is not choosed to do backup
        ## but we also need to backup it's binlogs, cause it's needed by it's slaves
        ## for every slave server, the binlogs needed are
        ## from slave server's Relay_Master_Log_File to consistence_point_binlog_filename
        return find_start_stop_binlog_for_master_server_that_not_backup(server_name)
    else:
        start_binlog = dataserver_info[server_name]['xb_to_binlog_name']
        return (start_binlog, '')

def get_dataserver_end_binlog_name(server_name):
    if is_backup_need_stop():
        return
    global global_consistence_info
    lines = global_consistence_info.split('\n')
    for line in lines:
        info = line.split('\t')
#['192.168.122.110:13004', 'auth_server', 'master-bin.000001', '724', 'belong-to-datasource=auth', 'xb-to-lsn=1359021', 'xb-to-binlog-name=master-bin.000001', 'xb-to-binlog-pos=724']
        if server_name == info[1]:
            return info[2]
    fatal_error("fail to get binlog name from global_consistence_info")


def backup_one_master_server_conf_and_basedir():
    if is_backup_need_stop():
        return
    print_trace_info(sys._getframe().f_code.co_name, "start")
    #try use auth, if not found then global, if not found then
    #other random source's master to backup db basedir
    global dbscale_server_user
    global dbscale_server_pswd
    global dbscale_server_port

    global backup_dest_ip
    global backup_dest_port
    global backup_dest_user
    global backup_dest_dir

    global socat_port
    global socat_log_dir
    global socat_retry_count

    auth_source_name = ""
    global_source_name = ""
    used_source_name = ""
    for data_source in rep_source_ms_info:
        if 'auth' in data_source:
            auth_source_name = data_source
            break
        if 'global' in data_source:
            global_source_name = data_source
            break
        used_source_name = data_source
    if len(auth_source_name) > 0:
        used_source_name = auth_source_name
    elif len(global_source_name) > 0:
        used_source_name = global_source_name

    master_server_name = rep_source_ms_info[used_source_name]['master']
    host = dataserver_info[master_server_name]['host']
    port = dataserver_info[master_server_name]['port']
    belong_to_datasource = dataserver_info[master_server_name]['belong_to_datasource']
    db_conn_config = {
        'host': host,
        'user': dataserver_user_pswd_map[master_server_name]['user'],
        'password': dataserver_user_pswd_map[master_server_name]['password'],
        'port': int(port),
        'db': 'information_schema',
        'charset': "utf8",
        'autocommit': True
    }
    (conn, cursor) = get_database_conn(db_conn_config)
    source_server_conn = get_one_server_conn(host, dbscale_server_user, dbscale_server_port, dbscale_server_pswd)

    if backup_type == 'inc':
        print_debug_info('this is an incremental backup, so skip backup datanode basedir, only backup config file', '')
        print_trace_info(sys._getframe().f_code.co_name, "finish")
        return

    query_text = "show global variables like 'basedir'"
    db_basedir = get_query_result_set(query_text, cursor)[0]['Value']
    if db_basedir[-1] == '/':
        db_basedir = db_basedir[0:-1]
    db_parent_dir = db_basedir[0:db_basedir.rfind('/')]
    db_basedir = db_basedir[len(db_parent_dir) + 1:]
    if db_parent_dir == '':
        db_parent_dir='/'

    command_id = get_command_id()
    socat_log_file = str(socat_port) + '_' + str(command_id)
    dest_cmd = "socat -d -lf %s/%s -u tcp-listen:%d,reuseaddr stdout > %s/datanode_base/%s.tar.gz &" % (socat_log_dir, socat_log_file, socat_port, backup_dest_dir, db_basedir)
    dest_socat_ret_code = [1]
    dest_server_conn = get_one_dest_server_conn()

    th = run_socat_receiver_on_backup_dest(dest_server_conn, dest_cmd, command_id, True, dest_socat_ret_code)
    if th == None:
        source_server_conn.close()
        dest_server_conn.close()
        close_database_conn(conn, cursor)
        return

    cmd = "cd %s && tar -zcf - %s | socat - TCP4:%s:%d,retry=%s" % (db_parent_dir, db_basedir, backup_dest_ip, socat_port, socat_retry_count)
    print_debug_info(cmd, 'socat source cmd for dataserver [%s]%s:%s to get datanode basedir' % (master_server_name, host, port))
    print_trace_info(sys._getframe().f_code.co_name, "in-progress")
    try:
        global conn_timeout
        command_id2 = get_command_id()
        command_info2 = ExecCommandInfo(host, dbscale_server_user, dbscale_server_port, dbscale_server_pswd, cmd)
        ret = None
        if True == add_exec_command_info(command_id2, command_info2):
            source_server_conn.log_execute_info_once()
            ret = source_server_conn.execute(cmd, timeout=conn_timeout)
            del_exec_command_info(command_id2)
    except Exception as e:
        if not is_backup_need_stop():
            fatal_error(str(e))
    if ret is not None and ret[1] != '' and not is_backup_need_stop():
        fatal_error("get error when backup datanode basedir of [%s]%s:%s, %s" % (master_server_name, host, port, ret[1]))
    th.join() #TODO what if source socat failed, we need kill related dest socat process forcely
    del_exec_command_info(command_id)
    source_server_conn.close()
    dest_server_conn.close()
    close_database_conn(conn, cursor)
    print_debug_info("%s/datanode_base/%s.tar.gz" % (backup_dest_dir, db_basedir), "succeed to backup datanode basedir to file")
    print_trace_info(sys._getframe().f_code.co_name, "finish")


def backup_one_dbscale_conf_and_basedir():
    if is_backup_need_stop():
        return
    print_trace_info(sys._getframe().f_code.co_name, "start")
    global dbscale_server_user
    global dbscale_server_pswd
    global dbscale_server_port

    global backup_dest_ip
    global backup_dest_port
    global backup_dest_user
    global backup_dest_pswd
    global backup_dest_dir

    global connect_method
    global adm_server_port

    global backup_id

    global socat_port
    global socat_log_dir
    global socat_retry_count

    cluster_backup_conf_file = "/tmp/dbscale.conf.%s" % backup_id
    query_text = "dbscale flush all config to file '%s'" % cluster_backup_conf_file
    get_query_result_set(query_text, cluster_cursor)

    config = {
        'dest_pswd': backup_dest_pswd,
        'dest_user': backup_dest_user,
        'dest_port': backup_dest_port
    }
    if connect_method == 2:
        config['dest_port'] = adm_server_port
    conn = None
    conn = get_one_server_conn(cluster_config['host'], dbscale_server_user, dbscale_server_port, dbscale_server_pswd)
    if not transfer_file_in_conn(conn, backup_dest_ip, cluster_backup_conf_file, backup_dest_dir + '/calcnode_base', 'dbscale.conf', **config):
        conn.close()
        return

    if backup_type == 'inc':
        print_debug_info('this is an incremental backup, so skip backup calcnode basedir, only backup config file', '')
        print_trace_info(sys._getframe().f_code.co_name, "finish")
        return
    query_text = "dbscale show path info"
    cluster_exe_file = get_query_result_set(query_text, cluster_cursor)[0]['DBScale_basedir']
    print_debug_info(cluster_exe_file, "get exe file of cluster %s:%s" % (cluster_config['host'], cluster_config['port']))
    cluster_exe_path = cluster_exe_file[0:-len("/dbscale")]
    cluster_parent_dir = cluster_exe_path[0:cluster_exe_path.rfind('/')]
    cluster_basedir = cluster_exe_path[len(cluster_parent_dir) + 1:]
    command_id = get_command_id()
    socat_log_file = str(socat_port) + '_' + str(command_id)
    dest_cmd = "socat -d -lf %s/%s -u tcp-listen:%d,reuseaddr stdout > %s/calcnode_base/dbscale.tar.gz &" % (socat_log_dir, socat_log_file, socat_port, backup_dest_dir)
    dest_socat_ret_code = [1]

    exclude_file = "echo -e '*.log\n*.log_slow\n*.log_audit\n*.log_zk' > /tmp/exclude_file.txt"
    dest_server_conn = get_one_dest_server_conn()
    th = run_socat_receiver_on_backup_dest(dest_server_conn, dest_cmd, command_id, True, dest_socat_ret_code)
    if th == None:
        conn.close()
        dest_server_conn.close()
        return

    cmd = "%s && cd %s && tar --exclude-from=/tmp/exclude_file.txt -zcf - %s | socat - TCP4:%s:%d,retry=%s" % (exclude_file, cluster_parent_dir, cluster_basedir, backup_dest_ip, socat_port, socat_retry_count)
    print_debug_info(cmd, 'socat source cmd for cluster %s:%s to get calcnode basedir' % (cluster_config['host'], cluster_config['port']))
    print_trace_info(sys._getframe().f_code.co_name, "in-progress")
    try:
        global conn_timeout
        command_id2 = get_command_id()
        command_info2 = ExecCommandInfo(cluster_config['host'], dbscale_server_user, dbscale_server_port, dbscale_server_pswd, cmd)
        if True == add_exec_command_info(command_id, command_info2):
            ret = conn.execute(cmd, timeout=conn_timeout)
            del_exec_command_info(command_id2)
    except Exception as e:
        if not is_backup_need_stop():
            fatal_error(str(e))
    if ret[1] != '' and not is_backup_need_stop():
        fatal_error("get error when backup calcnode basedir of %s:%s, %s" % (cluster_config['host'], cluster_config['port'], ret[1]))
    th.join() #TODO what if source socat failed, we need kill related dest socat process forcely

    del_exec_command_info(command_id)
    conn.close()
    dest_server_conn.close()
    print_trace_info(sys._getframe().f_code.co_name, "finish")


def mark_backup_dest_dir_with_ok_flag():
    if is_backup_need_stop():
        return
    print_trace_info(sys._getframe().f_code.co_name, "start")
    global backup_dest_dir
    dest_server_conn = get_one_dest_server_conn()
    cmd = "echo 'ok' > %s/ok" % backup_dest_dir
    ret = dest_server_conn.execute(cmd)
    dest_server_conn.close()
    if ret[1] != '':
        fatal_error("fail to mark backup dest dir with ok flag: %s" % (ret[1]))
    dest_server_conn.close()
    global mark_ok_flag
    mark_ok_flag = True
    print_trace_info(sys._getframe().f_code.co_name, "finish")


def clean_old_bad_backup():
    if is_backup_need_stop():
        return
    print_trace_info(sys._getframe().f_code.co_name, "start")
    global last_good_backup
    global backup_base_dir
    global socat_log_dir
    global store_bad_backup

    dest_server_conn = get_one_dest_server_conn()
    dest_server_conn.execute("mkdir -p %s" % (backup_base_dir))
    dest_server_conn.execute("mkdir -p %s/trash" % (backup_base_dir))
    # create socat log file directory
    tmp_time = time.strftime("%Y%m%d", time.localtime())
    socat_log_dir = backup_base_dir + '/socat_log/' + tmp_time
    dest_server_conn.execute("mkdir -p %s" % (socat_log_dir))
    ret = dest_server_conn.execute("ls -t %s" % (backup_base_dir)) #only show directories
    old_backup_list = ret[0].split('\n')[0:-1] #remove the last empty string made by '\n'
    old_backup_list.sort(reverse=True)
    print_debug_info(old_backup_list, 'get old backup directories list')
    for elem in old_backup_list:
        if elem == 'trash' or elem == 'encrypted' or elem == 'socat_log':
            continue
        try:
            ret = dest_server_conn.execute('cat %s/%s/ok' % (backup_base_dir, elem))
        except Exception as e:
            pass
        if ret[0] == 'ok\n':
            last_good_backup = elem
            break
        print_debug_info(elem, 'get a bad old backup, move it to trash dir or delete it')
        global conn_timeout
        if store_bad_backup:
            dest_server_conn.execute('mv {0}/{1} {0}/trash'.format(backup_base_dir, elem), timeout=conn_timeout)
        else:
            print_debug_info(elem, 'store_bad_backup is false, rm bad backup')
            dest_server_conn.execute('rm -rf {0}/{1}'.format(backup_base_dir, elem), timeout=conn_timeout)
        try:
            if store_bad_backup:
                dest_server_conn.execute('mv {0}/encrypted/{1}.tar.encrypted {0}/trash'.format(backup_base_dir, elem), timeout=conn_timeout)
            else:
                dest_server_conn.execute('rm -rf {0}/encrypted/{1}.tar.encrypted'.format(backup_base_dir, elem), timeout=conn_timeout)
        except:
            pass
    dest_server_conn.close()
    if len(last_good_backup) > 0:
        print_debug_info(last_good_backup, 'get last good backup dir')
    else:
        print_debug_info('not find last good backup dir', '')
    print_trace_info(sys._getframe().f_code.co_name, "finish")

def is_master_server(server_name):
    ds = dataserver_info[server_name]['belong_to_datasource']
    master_server = rep_source_ms_info[ds]['master']
    if master_server == server_name:
        return True
    else:
        return False

def is_skipped(server_name):
    if len(connect_failed_servers) > 0 and connect_failed_servers.issuperset({server_name}):
        return True
    if is_master_server(server_name):
        return False
    if len(skipped_servers) > 0 and skipped_servers.issuperset({server_name}):
        return True
    return False


def check_if_datasource_with_no_server_to_backup():
    if is_backup_need_stop():
        return
    for ds_name in rep_source_ms_info:
        master_server_name = rep_source_ms_info[ds_name]['master']
        if not is_skipped(master_server_name):
            continue
        else:
            fatal_error("master server %s is skipped, so can't backup datasource %s" % (master_server_name, ds_name))
        has_slave_can_backup = False
        for slave in rep_source_ms_info[ds_name]['slaves']:
            if not is_skipped(slave):
                has_slave_can_backup = True
                break
        if not has_slave_can_backup:
            fatal_error('datasource %s have no server to backup, all skipped' % ds_name)

def adjust_skipped_servers(servers_last_good_backup_backuped):
    global curr_server_list
    global skipped_servers
    global backup_master
    global backup_slave_num
    backup_server_num_of_ds = {}
    for server in curr_server_list:
        if server not in servers_last_good_backup_backuped:
            skipped_servers.add(server)
            print_debug_info('dataserver %s skipped cause it is not backed up in last backup' % (server), '')
            continue
        is_master = is_master_server(server)
        if is_master:
            if not backup_master:
                datasource_name = dataserver_info[server]['belong_to_datasource']
                master_server = rep_source_ms_info[datasource_name]['master']
                skipped_servers.add(server)
                print_debug_info('dataserver %s skipped because backup-master=0 and the master server of datasource %s is %s' \
                        % (server, datasource_name, master_server), '')
                continue
        else:
            if backup_slave_num is None: ## use backup slave list
                if not backup_slaves.issuperset({server}):
                    skipped_servers.add(server)
            else: ## use backup slave num
                ##one slave server status abnormal backup other slave server
                if dataserver_info[server]['status'] != "Slave normal":
                   print_debug_info('dataserver %s status is not Slave normal' % (server), '')
                   skipped_servers.add(server)
                   continue
                ds_name = dataserver_info[server]['belong_to_datasource']
                backup_num_of_ds = backup_server_num_of_ds.get(ds_name)
                should_backup = False
                if backup_num_of_ds is None:
                    if backup_slave_num > 0:
                        should_backup = True
                        backup_server_num_of_ds[ds_name] = 1
                else:
                    if backup_num_of_ds < backup_slave_num:
                        should_backup = True
                        backup_server_num_of_ds[ds_name] += 1
                if not should_backup:
                    print_debug_info("slave dataserver %s of datasource %s skipped" % (server, ds_name), '')
                    skipped_servers.add(server)

    going_to_backup_servers = {}
    going_to_be_skipped_servers = {}
    for server in curr_server_list:
        ds_name = dataserver_info[server]['belong_to_datasource']
        if not skipped_servers.issuperset({server}):
            if going_to_backup_servers.get(ds_name) is None:
                going_to_backup_servers[ds_name] = []
            going_to_backup_servers[ds_name].append(server)
            print_debug_info('ds: [%s] server: [%s] going to be backup' % (ds_name, server),'')
        else:
            if going_to_be_skipped_servers.get(ds_name) is None:
                going_to_be_skipped_servers[ds_name] = []
            going_to_be_skipped_servers[ds_name].append(server)
            print_debug_info('ds: [%s] server: [%s] going to be skipped' % (ds_name, server),'')

def make_full_backup(debug_info):
    global skipped_servers
    global curr_server_list
    global backup_type

    backup_type = 'full'
    skipped_servers.clear()
    adjust_skipped_servers(curr_server_list)
    print_debug_info(debug_info, '')

def confirm_backup_type():
    if is_backup_need_stop():
        return
    print_trace_info(sys._getframe().f_code.co_name, "start")
    global backup_type
    global last_good_backup
    global backup_base_dir
    global backup_dest_dir
    global backup_dest_user
    global backup_dest_ip
    global backup_dest_port
    global curr_server_list
    global skipped_servers

    if len(last_good_backup) == 0 or backup_type == 'full':
        make_full_backup("this is a full backup")
        print_trace_info(sys._getframe().f_code.co_name, "finish")
        return

    dest_server_conn = get_one_dest_server_conn()
    last_backup_pos_info = ''
    ret = dest_server_conn.execute("cat %s/%s/cluster_consistence_info" % (backup_base_dir, last_good_backup))
    last_backup_pos_info = ret[0]
    print_debug_info(last_backup_pos_info, 'get last good backup pos info')
    lines = last_backup_pos_info[:-1].split('\n')
    servers_last_good_backup_backuped = []
    for line in lines:
        info = line.split('\t')
        if info[1] in dataserver_info:
            servers_last_good_backup_backuped.append(info[1])
#['192.168.122.110:13004', 'auth_server', 'master-bin.000001', '724', 'belong-to-datasource=auth', 'xb-to-lsn=1359021', 'xb-to-binlog-name=master-bin.000001', 'xb-to-binlog-pos=724']
            dataserver_info[info[1]]['old-xb-to-lsn'] = info[5][len('xb-to-lsn='):]
    adjust_skipped_servers(servers_last_good_backup_backuped)

    if backup_master == True:
        for server in curr_server_list:
            if is_master_server(server) and server not in servers_last_good_backup_backuped:
                make_full_backup("A master-slave switchover occurs, and the current master %s was not backed up last time, run current job as a full backup" % (server))
                print_trace_info(sys._getframe().f_code.co_name, "finish")
                return

    handling_datasource = set()
    for server in curr_server_list:
        if is_skipped(server) or server in skipped_servers:
            continue
        if server not in servers_last_good_backup_backuped:
            continue
        handling_datasource.add(dataserver_info[server]['belong_to_datasource'])
    if len(handling_datasource) != len(rep_source_ms_info):
        make_full_backup("not all replication datasource covered in this backup job, run current job as a full backup")
        print_trace_info(sys._getframe().f_code.co_name, "finish")
        return

    if len(servers_last_good_backup_backuped):
        for server in curr_server_list:
            if is_skipped(server) or server in skipped_servers:
                continue
            if server not in servers_last_good_backup_backuped:
                make_full_backup("server %s not in last backup job:%s, run current job as a full backup" % (server, last_good_backup))
                print_trace_info(sys._getframe().f_code.co_name, "finish")
                return

    check_if_datasource_with_no_server_to_backup()
    print_debug_info("this is an incremental backup", '')
    print_trace_info(sys._getframe().f_code.co_name, "finish")

def backup_curr_backup_config():
    if is_backup_need_stop():
        return
    print_trace_info(sys._getframe().f_code.co_name, "start")
    global backup_dest_dir
    global backup_conf_file

    backup_conf_content = ''
    with open (backup_conf_file, "r") as conf_file:
        backup_conf_content = conf_file.read()
    dest_server_conn = get_one_dest_server_conn()
    dest_server_conn.execute("cd %s && touch backup.conf && echo '%s' > backup.conf" % (backup_dest_dir, backup_conf_content))
    dest_server_conn.close()
    print_trace_info(sys._getframe().f_code.co_name, "finish")


def try_decrypt_sample():
    global connect_method
    if is_backup_need_stop():
        return
    print_trace_info('checking password', "start")
    global backup_base_dir
    global last_good_backup

    if len(last_good_backup) == 0:
        return
    last_good_backup_dir = backup_base_dir + '/' + last_good_backup
    dest_server_conn = get_one_dest_server_conn()
    err = False
    try:
        ret = dest_server_conn.execute('ls %s | grep encrypted_sample' % (last_good_backup_dir))
        if ret[1] != '':
            err = True
        if ret[0] == '' and connect_method == 2:
            err = True
    except:
        err = True

    if args.password is not None and err:
        fatal_error("last backup didn't use a password, can't use password any more")
    elif args.password is None and not err:
        fatal_error('last backup used a password, must use the same password too')
    if args.password is not None:
        try:
            global conn_timeout
            dest_server_conn.execute('cd %s && pyAesCrypt -p %s -d ./encrypted_sample -o sample' % (last_good_backup_dir, args.password), timeout=conn_timeout)
            dest_server_conn.execute('rm -f %s/sample' % (last_good_backup_dir), timeout=30)
        except Exception as e:
            fatal_error('wrong password')

    print_trace_info('checking password', "finish")

def encrypt_backup_dir():
    if is_backup_need_stop():
        return
    print_trace_info(sys._getframe().f_code.co_name, "start")
    global backup_dest_dir
    global backup_id
    global backup_type
    global conn_timeout

    tar_name = backup_id + '-' + backup_type
    if args.password is None:
        print_trace_info(sys._getframe().f_code.co_name, "finish")
        return
    dest_server_conn = get_one_dest_server_conn()
    dest_server_conn.execute('cd %s && tar --create --file %s.tar --exclude=cluster_consistence_info --exclude=consistent_point_datetime --exclude=backuped_binlogs --remove-files *' % (backup_dest_dir, tar_name), timeout=conn_timeout)
    dest_server_conn.execute("cd %s && echo 'abc123' > sample" % (backup_dest_dir))
    dest_server_conn.execute('cd %s && pyAesCrypt -p %s -e ./%s.tar -o ../encrypted/%s.tar.encrypted' % (backup_dest_dir, args.password, tar_name, tar_name), timeout=conn_timeout)
    dest_server_conn.execute('cd %s && pyAesCrypt -p %s -e ./sample -o encrypted_sample' % (backup_dest_dir, args.password), timeout=conn_timeout)
    dest_server_conn.execute('cd %s && rm %s.tar sample' % (backup_dest_dir, tar_name))

    print_trace_info(sys._getframe().f_code.co_name, "finish")

def print_backup_conclusion_msg():
    global mark_ok_flag
    if mark_ok_flag:
        print("cluster backup succeed", flush=True)
    elif is_backup_need_stop():
        print("cluster backup failed because reach max-time-sec or get exception.", flush=True)
    else:
        print("cluster backup failed.", flush=True)

def do_backup():
    global cluster_conn
    global cluster_cursor
    global cluster_config
    global backup_get_execption

    timing_stop_backup_thread = threading.Thread(target=timing_to_stop_backup)
    timing_stop_backup_thread.start()
    try:
        (cluster_conn, cluster_cursor) = get_database_conn(cluster_config)
        if log_audit == True:
            get_query_result_set('DBSCALE START BACKUP', cluster_cursor)
        get_db_cluster_info()
        pre_check()
        clean_old_bad_backup()
        confirm_backup_type()
        try_decrypt_sample()
        init_backup_dest_dir()
        backup_all_server()
        backup_global_consistence_point()
        backup_binlog_files()
        backup_one_master_server_conf_and_basedir()
        backup_one_dbscale_conf_and_basedir()
        backup_curr_backup_config()
        encrypt_backup_dir()
        mark_backup_dest_dir_with_ok_flag()
        if log_audit == True:
            get_query_result_set('DBSCALE END BACKUP', cluster_cursor)
        close_database_conn(cluster_conn, cluster_cursor)
        print_backup_conclusion_msg()
    except:
        print_trace_info('do_backup get an except, set backup_get_execption True', 'do_backup finish')
        backup_get_execption = True
        raise
    return not mark_ok_flag

def move_backup_to_trash(conn, backup_id_name):
    global backup_base_dir

    print_trace_info('move backup: %s to trash'.format(backup_id_name), '')
    try:
        conn.execute('cd {0} && mv {1} {0}/trash'.format(backup_base_dir, backup_id_name), timeout=30)
        conn.execute('cd {0} && mv encrypted/{1}.tar.encrypted {0}/trash'.format(backup_base_dir, backup_id_name), timeout=30)
    except:
        pass

def purge_backups_to(dt_str):
    print_trace_info(sys._getframe().f_code.co_name, "start")
    purge_to_datetime = ''
    try:
        purge_to_datetime = datetime.strptime(dt_str, '%Y-%m-%d %H:%M:%S')
    except:
        fatal_error("purge to datetime format error %s" % (dt_str))

    global backup_base_dir
    dest_server_conn = get_one_dest_server_conn()
    folders = dest_server_conn.execute('ls %s' % (backup_base_dir))[0]
    backup_dirs = folders.split('\n')[:-1]
    backup_dirs.sort()
    for dir in backup_dirs:
        dt = ''
        try:
            dt = datetime.strptime(dir.split('-')[0], "UTC%Y%m%d%H%M%S.%f")
        except:
            continue
        if dt <= purge_to_datetime:
            move_backup_to_trash(dest_server_conn, dir)
        else:
            break
    dest_server_conn.close()
    print_trace_info(sys._getframe().f_code.co_name, "finish")
    return 0

def clean_trash():
    print_trace_info(sys._getframe().f_code.co_name, "start")
    global backup_base_dir

    conn = get_one_dest_server_conn()
    conn.execute('rm -rf %s/trash/*' % (backup_base_dir))
    print_trace_info(sys._getframe().f_code.co_name, "finish")
    return 0
def find_xtrabackup_version(cmd, server_name, host, port, user, pswd):
    global xtrabackup_version
    conn = get_one_server_conn(host, user, port, pswd, server_name == 'backup_store_server')
    got_err = False
    try:
        ret = conn.execute('%s --version' % (cmd))
        if ret is not None:
            target = ret[0]
            if target == '':
                target = ret[1]
            v_s = target.find("MySQL server") + len("MySQL server ")
            v_e = target.find(" Linux")
            v_str = target[v_s:v_e]
            v_list = v_str.split(".")
            xtrabackup_version = int(v_list[0])*1000 + int(v_list[1])*100 + int(v_list[2])
            print_trace_info("got xtrabackup version: %s and num:%d" % (v_str, xtrabackup_version), '')
        else:
            got_err = True
    except Exception as e:
        print_trace_info('', str(e))
        got_err = True
    conn.close()
    if got_err:
        fatal_error("%s does not exists in /usr/bin." % (cmd))


if __name__ == '__main__':
    if args.version:
        print("version: %s" % tool_version)
        sys.exit(0)

    print_trace_info("version: %s" % tool_version, '')
    init_config_from_conf_file()
    ret = 0
    if args.purge_to is not None:
        ret = purge_backups_to(args.purge_to)
    elif args.clean_trash:
        ret = clean_trash()
    else:
        ret = do_backup()
    sys.exit(ret)


```




















