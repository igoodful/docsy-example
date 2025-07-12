---
title: backup
description: backup
date: 2023-11-05
weight: 30000


---




```python
[root@node76 python_version]# python3.9 backup.py  --config-file=backup01.conf
[TRACE 139706299742016 2024-02-21 16:04:43.619749] version: 1.0.6039.4a538e36
[TRACE 139706299742016 2024-02-21 16:04:43.619824] init_config_from_conf_file start
[TRACE 139706299742016 2024-02-21 16:04:43.619868] current backup id: UTC20240221080443.619
[DEBUG 139706299742016 2024-02-21 16:04:43.620006] use config file : backup01.conf
[DEBUG 139706299742016 2024-02-21 16:04:43.620793] get cluster config : {'host': '172.17.134.77', 'user': 'admin', 'password': '123456', 'port': 16310, 'db': 'information_schema', 'charset': 'utf8', 'autocommit': True}
[TRACE 139706299742016 2024-02-21 16:04:43.620957] init_config_from_conf_file finish
[TRACE 139706299742016 2024-02-21 16:04:43.627814] get_db_cluster_info start
[DEBUG 139706299742016 2024-02-21 16:04:43.628808] get cluster datasource with type replication : [OrderedDict([('Name', 'normal_0'), ('Type', 'Replication'), ('SourceStatus', 'Working'), ('LoadBalanceStrategy', 'MASTER'), ('MasterSource', 'normal_0_normal_0_1'), ('MasterSourceStatus', 'Working'), ('MasterServer', 'normal_0_1'), ('MasterServerHostInfo', '172.17.134.76:16315'), ('MasterServerStatus', 'Server normal'), ('SelectMasterTime', '2024-02-20 16:12:12'), ('SlaveSource', 'normal_0_normal_0_0'), ('SlaveSourceStatus', 'Working'), ('SlaveServers', 'normal_0_0'), ('SlaveServerHostInfo', '172.17.134.71:16315'), ('SlaveServerStatus', 'Slave normal'), ('SlaveExecutedGtidStatus', 'GTID good')]), OrderedDict([('Name', 'normal_0'), ('Type', ''), ('SourceStatus', ''), ('LoadBalanceStrategy', ''), ('MasterSource', ''), ('MasterSourceStatus', ''), ('MasterServer', ''), ('MasterServerHostInfo', ''), ('MasterServerStatus', ''), ('SelectMasterTime', ''), ('SlaveSource', 'normal_0_normal_0_2'), ('SlaveSourceStatus', 'Working'), ('SlaveServers', 'normal_0_2'), ('SlaveServerHostInfo', '172.17.134.77:16315'), ('SlaveServerStatus', 'Slave normal'), ('SlaveExecutedGtidStatus', 'GTID good')])]
[DEBUG 139706299742016 2024-02-21 16:04:43.628900] get datasource master-slave info : {'normal_0': {'master': 'normal_0_1', 'slaves': ['normal_0_0', 'normal_0_2']}}
[DEBUG 139706299742016 2024-02-21 16:04:43.629594] no datasource of type MGR
[DEBUG 139706299742016 2024-02-21 16:04:43.630321] get cluster dataserver list : [OrderedDict([('servername', 'normal_0_0'), ('host', '172.17.134.71'), ('port', '16315'), ('username', 'dbscale_internal'), ('status', 'Slave normal'), ('master-online-status', 'Not_A_Master'), ('master_backup', '1'), ('remote_user', 'root'), ('remote_port', '22'), ('max_needed_conn/max_mysql_conn', ''), ('master_priority', '100')]), OrderedDict([('servername', 'normal_0_1'), ('host', '172.17.134.76'), ('port', '16315'), ('username', 'dbscale_internal'), ('status', 'Server normal'), ('master-online-status', 'Master_Online'), ('master_backup', '1'), ('remote_user', 'root'), ('remote_port', '22'), ('max_needed_conn/max_mysql_conn', ''), ('master_priority', '50')]), OrderedDict([('servername', 'normal_0_2'), ('host', '172.17.134.77'), ('port', '16315'), ('username', 'dbscale_internal'), ('status', 'Slave normal'), ('master-online-status', 'Not_A_Master'), ('master_backup', '1'), ('remote_user', 'root'), ('remote_port', '22'), ('max_needed_conn/max_mysql_conn', ''), ('master_priority', '100')])]
[DEBUG 139706299742016 2024-02-21 16:04:44.027289] get current server list : ['normal_0_1', 'normal_0_0', 'normal_0_2']
[DEBUG 139706299742016 2024-02-21 16:04:44.027420] get dataserver info : {'normal_0_0': {'host': '172.17.134.71', 'port': '16315', 'status': 'Slave normal', 'belong_to_datasource': 'normal_0'}, 'normal_0_1': {'host': '172.17.134.76', 'port': '16315', 'status': 'Server normal', 'belong_to_datasource': 'normal_0'}, 'normal_0_2': {'host': '172.17.134.77', 'port': '16315', 'status': 'Slave normal', 'belong_to_datasource': 'normal_0'}}
[TRACE 139706299742016 2024-02-21 16:04:44.027485] get_db_cluster_info finish
[TRACE 139706299742016 2024-02-21 16:04:44.027557] pre_check start
[DEBUG 139706299742016 2024-02-21 16:04:44.029323] get cluster datasource with type server : [OrderedDict([('Name', 'normal_0_normal_0_0'), ('Type', 'Server'), ('Server', 'normal_0_0'), ('ServerHostInfo', '172.17.134.71:16315'), ('ServerStatus', 'Slave normal'), ('SourceStatus', 'Working'), ('Is-internal', 'YES')]), OrderedDict([('Name', 'normal_0_normal_0_1'), ('Type', 'Server'), ('Server', 'normal_0_1'), ('ServerHostInfo', '172.17.134.76:16315'), ('ServerStatus', 'Server normal'), ('SourceStatus', 'Working'), ('Is-internal', 'YES')]), OrderedDict([('Name', 'normal_0_normal_0_2'), ('Type', 'Server'), ('Server', 'normal_0_2'), ('ServerHostInfo', '172.17.134.77:16315'), ('ServerStatus', 'Slave normal'), ('SourceStatus', 'Working'), ('Is-internal', 'YES')])]
[TRACE 139706299742016 2024-02-21 16:04:44.278924] got xtrabackup version: 8.0.26 and num:8026
[TRACE 139706299742016 2024-02-21 16:04:44.664113] pre_check finish
[TRACE 139706299742016 2024-02-21 16:04:44.664226] clean_old_bad_backup start
[DEBUG 139706299742016 2024-02-21 16:04:44.773482] get old backup directories list : ['trash', 'socat_log', 'encrypted', 'UTC20240221074733.690-full', 'UTC20240221031122.943-full', 'UTC20240220160004.835-inc', 'UTC20240220093304.784-full', 'UTC20240220082254.590-full', 'UTC20240206085424.236-full', 'UTC20240206081826.526-full']
[DEBUG 139706299742016 2024-02-21 16:04:44.790293] get a bad old backup, move it to trash dir or delete it : UTC20240221074733.690-full
[DEBUG 139706299742016 2024-02-21 16:04:44.839731] get last good backup dir : UTC20240221031122.943-full
[TRACE 139706299742016 2024-02-21 16:04:44.839771] clean_old_bad_backup finish
[TRACE 139706299742016 2024-02-21 16:04:44.839783] confirm_backup_type start
[DEBUG 139706299742016 2024-02-21 16:04:44.839802] dataserver normal_0_1 skipped because backup-master=0 and the master server of datasource normal_0 is normal_0_1
[DEBUG 139706299742016 2024-02-21 16:04:44.839814] slave dataserver normal_0_2 of datasource normal_0 skipped
[DEBUG 139706299742016 2024-02-21 16:04:44.839825] ds: [normal_0] server: [normal_0_1] going to be skipped
[DEBUG 139706299742016 2024-02-21 16:04:44.839832] ds: [normal_0] server: [normal_0_0] going to be backup
[DEBUG 139706299742016 2024-02-21 16:04:44.839840] ds: [normal_0] server: [normal_0_2] going to be skipped
[DEBUG 139706299742016 2024-02-21 16:04:44.839846] this is a full backup
[TRACE 139706299742016 2024-02-21 16:04:44.839853] confirm_backup_type finish
[TRACE 139706299742016 2024-02-21 16:04:44.839861] checking password start
[TRACE 139706299742016 2024-02-21 16:04:44.895037] checking password finish
[TRACE 139706299742016 2024-02-21 16:04:44.895079] init_backup_dest_dir start
[TRACE 139706299742016 2024-02-21 16:04:44.895090] current backup folder: /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240221080443.619-full
[DEBUG 139706299742016 2024-02-21 16:04:44.996682] get current backup dest dir : @172.17.134.76::/data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240221080443.619-full
[TRACE 139706299742016 2024-02-21 16:04:44.996765] init_backup_dest_dir finish
[TRACE 139706299742016 2024-02-21 16:04:44.996800] backup_all_server start
[TRACE 139703944738560 2024-02-21 16:04:44.997157] backup_one_host(139703944738560) start
[DEBUG 139706299742016 2024-02-21 16:04:45.070115] server: normal_0_1 skipped
[DEBUG 139706299742016 2024-02-21 16:04:45.070176] server: normal_0_2 skipped
[TRACE 139706299742016 2024-02-21 16:04:45.070380] backup_all_server in-progress
[TRACE 139703944738560 2024-02-21 16:04:45.114708] run_socat_receiver_on_backup_dest retry to run socat use socat_port 45509.
[DEBUG 139703944738560 2024-02-21 16:04:45.114955] add command id 1 and command is host:,port:,user:,pswd:,cmd:socat -d -lf /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240221/45509_1 -u tcp-listen:45509,reuseaddr stdout > /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240221080443.619-full/normal_0-normal_0_0-172.17.134.71-16315/backup.xbsteam &
[TRACE 139703944738560 2024-02-21 16:04:45.115600] run_socat_receiver_on_backup_dest execute socat dest_cmd :socat -d -lf /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240221/45509_1 -u tcp-listen:45509,reuseaddr stdout > /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240221080443.619-full/normal_0-normal_0_0-172.17.134.71-16315/backup.xbsteam &.
[TRACE 139703944738560 2024-02-21 16:04:45.115644] run_socat_receiver_on_backup_dest cat socat_log_file /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240221/45509_1.
[TRACE 139703944738560 2024-02-21 16:04:45.142585] run_socat_receiver_on_backup_dest execute socat successed set_socat_port 45509.
/root/backup-recovery-tool-6039/python_version/backup.py:663: Warning: (1287, "'SHOW SLAVE STATUS' is deprecated and will be removed in a future release. Please use SHOW REPLICA STATUS instead")
  rows = cursor.fetchall()
[TRACE 139703944738560 2024-02-21 16:04:45.419214] run_socat_receiver_on_backup_dest retry to run socat use socat_port 45514.
[DEBUG 139703944738560 2024-02-21 16:04:45.419465] add command id 2 and command is host:,port:,user:,pswd:,cmd:socat -d -lf /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240221/45514_2 -u tcp-listen:45514,reuseaddr file:/data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240221080443.619-full/normal_0-normal_0_0-172.17.134.71-16315/my.conf,trunc,creat
[TRACE 139703944738560 2024-02-21 16:04:45.420151] run_socat_receiver_on_backup_dest execute socat dest_cmd :socat -d -lf /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240221/45514_2 -u tcp-listen:45514,reuseaddr file:/data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240221080443.619-full/normal_0-normal_0_0-172.17.134.71-16315/my.conf,trunc,creat.
[TRACE 139703944738560 2024-02-21 16:04:45.420690] run_socat_receiver_on_backup_dest cat socat_log_file /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240221/45514_2.
[TRACE 139703944738560 2024-02-21 16:04:45.463241] run_socat_receiver_on_backup_dest execute socat successed set_socat_port 45514.
[DEBUG 139703944738560 2024-02-21 16:04:45.505923] transfering file: /data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/my16315.conf
[DEBUG 139703944738560 2024-02-21 16:04:45.506807] delete command id 2
[DEBUG 139703944738560 2024-02-21 16:04:45.510325] add command id 3 and command is host:172.17.134.71,port:,user:,pswd:,cmd:xtrabackup --defaults-file=/data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/my16315.conf --user=dbscale_internal --password=123456 --host=172.17.134.71 --port=16315 --backup --tmpdir=/data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/tmp  --stream=xbstream  2>/data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/tmp/UTC20240221080443.619-normal_0-normal_0_0-172.17.134.71-16315 | socat - TCP4:172.17.134.76:45509,retry=10
[DEBUG 139703944738560 2024-02-21 16:04:45.510382] socat source cmd without compress for dataserver normal_0_0 : xtrabackup --defaults-file=/data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/my16315.conf --user=dbscale_internal --password=123456 --host=172.17.134.71 --port=16315 --backup --tmpdir=/data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/tmp  --stream=xbstream  2>/data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/tmp/UTC20240221080443.619-normal_0-normal_0_0-172.17.134.71-16315 | socat - TCP4:172.17.134.76:45509,retry=10
[TRACE 139703944738560 2024-02-21 16:04:45.510411] backup_one_host(139703944738560) in-progress
[DEBUG 139703944738560 2024-02-21 16:04:45.510434] server: 172.17.134.71:16315 : th_detect_ftwrl_lock get server_index is 0.
[DEBUG 139703944738560 2024-02-21 16:04:45.556534] server: 172.17.134.71:16315 : backup_one_host ftwrl_retry_times: [10]
[TRACE 139703525299968 2024-02-21 16:04:45.556758] xtrabackup_command_execute thread begin start, server: 172.17.134.71:16315
[TRACE 139703516907264 2024-02-21 16:04:45.556883] detect_ftwrl_lock thread begin start, server: 172.17.134.71:16315
[DEBUG 139703525299968 2024-02-21 16:05:00.614725] server: 172.17.134.71:16315 : execute xtrabackup: [xtrabackup --defaults-file=/data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/my16315.conf --user=dbscale_internal --password=123456 --host=172.17.134.71 --port=16315 --backup --tmpdir=/data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/tmp  --stream=xbstream  2>/data/mysqldata/16315fff-7500-4291-89ce-6c50ebca1c3c/tmp/UTC20240221080443.619-normal_0-normal_0_0-172.17.134.71-16315 | socat - TCP4:172.17.134.76:45509,retry=10] ret: [['', '']]
[TRACE 139703525299968 2024-02-21 16:05:00.614858] xtrabackup_command_execute thread end finish, server: 172.17.134.71:16315
[TRACE 139703516907264 2024-02-21 16:05:00.958957] detect_ftwrl_lock get status RESULT_SUCCESSED thread end finish, server: 172.17.134.71:16315
[TRACE 139703944738560 2024-02-21 16:05:00.959262] th_detect_ftwrl_lock returned server: 172.17.134.71:16315
[TRACE 139703944738560 2024-02-21 16:05:00.959333] backup_one_host get DETECT_FTWRL_RESULT_SUCCESSED server: 172.17.134.71:16315
[DEBUG 139703944738560 2024-02-21 16:05:00.959400] server: 172.17.134.71:16315 : backup_one_host ftwrl_retry_times: [10]
[DEBUG 139703944738560 2024-02-21 16:05:00.983806] delete command id 1
[DEBUG 139703944738560 2024-02-21 16:05:00.983905] delete command id 3
[TRACE 139703944738560 2024-02-21 16:05:01.006533] backup_one_host(139703944738560) finish
[TRACE 139706299742016 2024-02-21 16:05:01.007003] backup_all_server finish
[TRACE 139706299742016 2024-02-21 16:05:01.007214] backup_global_consistence_point start
[DEBUG 139706299742016 2024-02-21 16:05:01.031020] get cluster global consistence point : 172.17.134.71:16315	normal_0_0	greatdb-bin.000041	631	belong-to-datasource=normal_0	xb-to-lsn=21694836	xb-to-binlog-name=greatdb-bin.000041	xb-to-binlog-pos=4
[TRACE 139706299742016 2024-02-21 16:05:01.116121] backup_global_consistence_point finish
[TRACE 139706299742016 2024-02-21 16:05:01.116246] backup_binlog_files start
[DEBUG 139706299742016 2024-02-21 16:05:01.116294] dataserver normal_0_0 is not master, and backup_master = 0, salve servers's binlogs are skipped
[TRACE 139706299742016 2024-02-21 16:05:01.116328] backup_binlog_for_server start
[DEBUG 139706299742016 2024-02-21 16:05:01.122781] all binlogs for server normal_0_1 : greatdb-bin.000001 greatdb-bin.000002 greatdb-bin.000003 greatdb-bin.000004 greatdb-bin.000005 greatdb-bin.000006 greatdb-bin.000007 greatdb-bin.000008 greatdb-bin.000009 greatdb-bin.000010 greatdb-bin.000011 greatdb-bin.000012 greatdb-bin.000013 greatdb-bin.000014 greatdb-bin.000015 greatdb-bin.000016 greatdb-bin.000017 greatdb-bin.000018 greatdb-bin.000019 greatdb-bin.000020 greatdb-bin.000021 greatdb-bin.000022 greatdb-bin.000023 greatdb-bin.000024 greatdb-bin.000025 greatdb-bin.000026 greatdb-bin.000027 greatdb-bin.000028 greatdb-bin.000029 greatdb-bin.000030 greatdb-bin.000031 greatdb-bin.000032 greatdb-bin.000033 greatdb-bin.000034 greatdb-bin.000035 greatdb-bin.000036 greatdb-bin.000037 greatdb-bin.000038 greatdb-bin.000039 greatdb-bin.000040 greatdb-bin.000041
[DEBUG 139706299742016 2024-02-21 16:05:01.171439] the last binlog of last good backup for server: normal_0_1 is: greatdb-bin.000041
[DEBUG 139706299742016 2024-02-21 16:05:01.221219] server: normal_0_1 binlogs need copy: greatdb-bin.000041
[DEBUG 139706299742016 2024-02-21 16:05:01.221292] get log_bin_dir of dataserver normal_0_1 : /data/mysqldata/16315fff-d9ca-4adb-9be1-578ff4626394/dbdata/
[DEBUG 139706299742016 2024-02-21 16:05:01.253666] backup binlog file of dataserver normal_0_1 :
[TRACE 139706299742016 2024-02-21 16:05:01.314832] run_socat_receiver_on_backup_dest retry to run socat use socat_port 45514.
[DEBUG 139706299742016 2024-02-21 16:05:01.314937] add command id 4 and command is host:,port:,user:,pswd:,cmd:socat -d -lf /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240221/45514_4 -u tcp-listen:45514,reuseaddr file:/data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240221080443.619-full/normal_0-normal_0_1-172.17.134.76-16315/greatdb-bin.000041,trunc,creat
[TRACE 139706299742016 2024-02-21 16:05:01.316413] run_socat_receiver_on_backup_dest execute socat dest_cmd :socat -d -lf /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240221/45514_4 -u tcp-listen:45514,reuseaddr file:/data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240221080443.619-full/normal_0-normal_0_1-172.17.134.76-16315/greatdb-bin.000041,trunc,creat.
[TRACE 139706299742016 2024-02-21 16:05:01.316540] run_socat_receiver_on_backup_dest cat socat_log_file /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240221/45514_4.
[TRACE 139706299742016 2024-02-21 16:05:01.343064] run_socat_receiver_on_backup_dest execute socat successed set_socat_port 45514.
[DEBUG 139706299742016 2024-02-21 16:05:01.360061] transfering file: /data/mysqldata/16315fff-d9ca-4adb-9be1-578ff4626394/dbdata//greatdb-bin.000041
[DEBUG 139706299742016 2024-02-21 16:05:01.360141] delete command id 4
[TRACE 139706299742016 2024-02-21 16:05:01.360329] backup_binlog_for_server finish
[DEBUG 139706299742016 2024-02-21 16:05:01.360407] dataserver normal_0_2 is skipped, no need backup its binlog files
[TRACE 139706299742016 2024-02-21 16:05:01.360420] backup_binlog_files finish
[TRACE 139706299742016 2024-02-21 16:05:01.360723] backup_one_master_server_conf_and_basedir start
[TRACE 139706299742016 2024-02-21 16:05:01.458752] run_socat_receiver_on_backup_dest retry to run socat use socat_port 45509.
[DEBUG 139706299742016 2024-02-21 16:05:01.458824] add command id 5 and command is host:,port:,user:,pswd:,cmd:socat -d -lf /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240221/45509_5 -u tcp-listen:45509,reuseaddr stdout > /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240221080443.619-full/datanode_base/mysql-8.0.26.tar.gz &
[TRACE 139706299742016 2024-02-21 16:05:01.459560] run_socat_receiver_on_backup_dest execute socat dest_cmd :socat -d -lf /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240221/45509_5 -u tcp-listen:45509,reuseaddr stdout > /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240221080443.619-full/datanode_base/mysql-8.0.26.tar.gz &.
[TRACE 139706299742016 2024-02-21 16:05:01.459630] run_socat_receiver_on_backup_dest cat socat_log_file /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240221/45509_5.
[TRACE 139706299742016 2024-02-21 16:05:01.490977] run_socat_receiver_on_backup_dest execute socat successed set_socat_port 45509.
[DEBUG 139706299742016 2024-02-21 16:05:01.491037] socat source cmd for dataserver [normal_0_1]172.17.134.76:16315 to get datanode basedir : cd /data/app && tar -zcf - mysql-8.0.26 | socat - TCP4:172.17.134.76:45509,retry=10
[TRACE 139706299742016 2024-02-21 16:05:01.491055] backup_one_master_server_conf_and_basedir in-progress
[DEBUG 139706299742016 2024-02-21 16:05:01.491088] add command id 6 and command is host:172.17.134.76,port:,user:,pswd:,cmd:cd /data/app && tar -zcf - mysql-8.0.26 | socat - TCP4:172.17.134.76:45509,retry=10
[DEBUG 139706299742016 2024-02-21 16:05:33.881231] delete command id 6
[DEBUG 139706299742016 2024-02-21 16:05:33.881370] delete command id 5
[DEBUG 139706299742016 2024-02-21 16:05:33.881685] succeed to backup datanode basedir to file : /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240221080443.619-full/datanode_base/mysql-8.0.26.tar.gz
[TRACE 139706299742016 2024-02-21 16:05:33.881707] backup_one_master_server_conf_and_basedir finish
[TRACE 139706299742016 2024-02-21 16:05:33.881844] backup_one_dbscale_conf_and_basedir start
[TRACE 139706299742016 2024-02-21 16:05:34.021244] run_socat_receiver_on_backup_dest retry to run socat use socat_port 45514.
[DEBUG 139706299742016 2024-02-21 16:05:34.021354] add command id 7 and command is host:,port:,user:,pswd:,cmd:socat -d -lf /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240221/45514_7 -u tcp-listen:45514,reuseaddr file:/data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240221080443.619-full/calcnode_base/dbscale.conf,trunc,creat
[TRACE 139706299742016 2024-02-21 16:05:34.024206] run_socat_receiver_on_backup_dest execute socat dest_cmd :socat -d -lf /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240221/45514_7 -u tcp-listen:45514,reuseaddr file:/data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240221080443.619-full/calcnode_base/dbscale.conf,trunc,creat.
[TRACE 139706299742016 2024-02-21 16:05:34.024248] run_socat_receiver_on_backup_dest cat socat_log_file /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240221/45514_7.
[TRACE 139706299742016 2024-02-21 16:05:34.056863] run_socat_receiver_on_backup_dest execute socat successed set_socat_port 45514.
[DEBUG 139706299742016 2024-02-21 16:05:34.090525] transfering file: /tmp/dbscale.conf.UTC20240221080443.619
[DEBUG 139706299742016 2024-02-21 16:05:34.090602] delete command id 7
[DEBUG 139706299742016 2024-02-21 16:05:34.091444] get exe file of cluster 172.17.134.77:16310 : /data/app/dbscale/dbscale
[TRACE 139706299742016 2024-02-21 16:05:34.168179] run_socat_receiver_on_backup_dest retry to run socat use socat_port 45509.
[DEBUG 139706299742016 2024-02-21 16:05:34.168264] add command id 8 and command is host:,port:,user:,pswd:,cmd:socat -d -lf /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240221/45509_8 -u tcp-listen:45509,reuseaddr stdout > /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240221080443.619-full/calcnode_base/dbscale.tar.gz &
[TRACE 139706299742016 2024-02-21 16:05:34.169578] run_socat_receiver_on_backup_dest execute socat dest_cmd :socat -d -lf /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240221/45509_8 -u tcp-listen:45509,reuseaddr stdout > /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/UTC20240221080443.619-full/calcnode_base/dbscale.tar.gz &.
[TRACE 139706299742016 2024-02-21 16:05:34.169617] run_socat_receiver_on_backup_dest cat socat_log_file /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir/socat_log/20240221/45509_8.
[TRACE 139706299742016 2024-02-21 16:05:34.269878] run_socat_receiver_on_backup_dest execute socat successed set_socat_port 45509.
[DEBUG 139706299742016 2024-02-21 16:05:34.269952] socat source cmd for cluster 172.17.134.77:16310 to get calcnode basedir : echo -e '*.log
*.log_slow
*.log_audit
*.log_zk' > /tmp/exclude_file.txt && cd /data/app && tar --exclude-from=/tmp/exclude_file.txt -zcf - dbscale | socat - TCP4:172.17.134.76:45509,retry=10
[TRACE 139706299742016 2024-02-21 16:05:34.269974] backup_one_dbscale_conf_and_basedir in-progress
[DEBUG 139706299742016 2024-02-21 16:05:34.270007] add command id 8 and command is host:172.17.134.77,port:,user:,pswd:,cmd:echo -e '*.log
*.log_slow
*.log_audit
*.log_zk' > /tmp/exclude_file.txt && cd /data/app && tar --exclude-from=/tmp/exclude_file.txt -zcf - dbscale | socat - TCP4:172.17.134.76:45509,retry=10
[DEBUG 139706299742016 2024-02-21 16:06:07.607126] delete command id 9
[DEBUG 139706299742016 2024-02-21 16:06:07.607317] delete command id 8
[TRACE 139706299742016 2024-02-21 16:06:07.607353] backup_one_dbscale_conf_and_basedir finish
[TRACE 139706299742016 2024-02-21 16:06:07.607480] backup_curr_backup_config start
[TRACE 139706299742016 2024-02-21 16:06:07.667405] backup_curr_backup_config finish
[TRACE 139706299742016 2024-02-21 16:06:07.667486] encrypt_backup_dir start
[TRACE 139706299742016 2024-02-21 16:06:07.667536] encrypt_backup_dir finish
[TRACE 139706299742016 2024-02-21 16:06:07.667549] mark_backup_dest_dir_with_ok_flag start
[TRACE 139706299742016 2024-02-21 16:06:07.729893] mark_backup_dest_dir_with_ok_flag finish
cluster backup succeed


```


```yaml
[root@node76 python_version]# cat backup01.conf
[misc]
force-full-backup=1
debug=1
socat-port=45509
socat-port2=45514
compress=0
compress-threads=5
max-time-sec=45000
backup-master=0
backup-slave=1
conn-timeout = 45000
use-disaster-recovery-cluster = false
connect-method = rabbit-agent

[rabbit-agent]
rabbitmq-uri = rabbit://admin:123456@172.17.134.70:5672//

[cluster]
ip = 172.17.134.77
user = admin
port = 16310
password = 123456

[backup-store]
ip = 172.17.134.76
dir = /data/storage_path/backup_file/PHYSICAL_MODE/2ef35e42-590c-4b57-a373-d439c6696f17/backup_dir
port =
user =
password =

[server-ssh-info]
user =
password =
port =

```






















