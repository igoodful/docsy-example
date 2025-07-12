---
title: recovery.py
description: recovery.py
date: 2023-10-12
weight: 60000


---

{{< alert >}}


{{< /alert >}}


```python

#!/usr/bin/python3
# -*- coding: utf-8 -*-

tool_version = '1.0.6039.4a538e36'

from datetime import datetime, timedelta
from fabric import Connection
from pymysql.cursors import DictCursorMixin, Cursor, SSCursor
import configparser
import io
import pymysql
import sys
import threading
import time
import traceback
import argparse
from invoke import Responder
import re
import Conn
import random
import base64

desc = '''
sample recovery config file
    [recover-to]
    dir = UTC20210205092650.735-inc
    #stop-datetime= 2021-01-28 12:16:00

    [backup-store]
    ip = 172.16.70.234
    port = 22
    user = backup
    password = 123
    dir = /tmp/backup

    [recovery-job-server]
    ip = 172.16.70.234
    port = 22
    user = backup
    password = 123
    work-dir = /tmp/recover_dir

    [misc]
    debug=1
    socat-port=9999
    socat-port2=9998
    # The socat port is occupied or socat network exception, backup tool retry times
    # no need to retry, please configure to 0
    socat-retry-count = 10
    # connect-method can config : ssh or rabbit-agent or adm-agent
    connect-method = ssh
    conn-timeout = 3600
    server-tmp-port = 27000
    client-name = mysql
    # use the specified xtrabackup binary dir, default is /usr/bin/xtrabackup
    xtrabackup-bin-dir = /usr/bin/xtrabackup
    # use the specified xbstream binary dir, default is /usr/bin/xbstream
    xbstream-bin-dir = /usr/bin/xbstream

    [rabbit-agent]
    rabbitmq-uri = rabbit://admin:admin@172.16.70.234/

    [adm-agent]
    port = 19999
'''
parser = argparse.ArgumentParser(description=desc, formatter_class=argparse.RawDescriptionHelpFormatter)
parser.add_argument('--config-file', help='specified the config file')
parser.add_argument('-p', '--password', help='password to decrypt the encrypted backup')
parser.add_argument('-v', '--version', action='store_true', help='show version')
args = parser.parse_args()

debug = True

socat_port = 0
socat_port2 = 0
socat_retry_count = 10
socat_log_dir = ''
recover_to_backup_dir = ""
next_backup_of_target_backup = ""
recovery_conf_file = "recovery.conf"
server_tmp_port = '27000'

recovery_job_server_ssh_ip = None
recovery_job_server_ssh_port = None
recovery_job_server_ssh_user = None
recovery_job_server_ssh_pswd = None
recovery_job_server_dir = None

backup_dest_ip = None
backup_dest_port = None
backup_dest_user = None
backup_dest_pswd = None
backup_base_dir = None
dataserver_user_pswd_map = {}

backup_dir_list = []
cluster_consistence_info = []
recovery_stop_datetime = None
is_using_encryption = False

using_socat = True
connect_method = 0
adm_server_port = None
for_testing = False
conn_timeout = 60*60 #seconds
rabbitmq_uri = ''
client_name = 'mysql'
server_version = 7
xtrabackup_bin_dir = "xtrabackup"
xbstream_bin_dir = "xbstream"

def print_trace_info(func_name, stage):
    print("[TRACE] %s %s" % (func_name, stage), flush=True)

def print_debug_info(info, pre_comment):
    if debug == False:
        return
    if len(pre_comment) > 0:
        print('[DEBUG] %s : %s' % (pre_comment, info), flush=True)
    else:
        print('[DEBUG] %s' % (info), flush=True)

def fatal_error(msg):
    print("=======stack start=======", flush=True)
    traceback.print_stack()
    print("======= stack end =======", flush=True)
    print("ERROR:", msg, flush=True)
    sys.exit(1)


def get_one_dest_server_conn():
    global backup_dest_ip
    global backup_dest_port
    global backup_dest_user
    global backup_dest_pswd
    server_conn = get_one_server_conn(backup_dest_ip, backup_dest_user, backup_dest_port, backup_dest_pswd, using_rds_taskmanager=False)
    ret = server_conn.execute('echo 1')[1]
    if ret != '':
        fatal_error("fail to get ssh connection from here to backup dest host %s" % (ret))
    return server_conn

def get_one_recovery_job_server_conn():
    global recovery_job_server_ssh_ip
    global recovery_job_server_ssh_port
    global recovery_job_server_ssh_user
    global recovery_job_server_ssh_pswd

    conn = get_one_server_conn(recovery_job_server_ssh_ip, recovery_job_server_ssh_user, recovery_job_server_ssh_port, recovery_job_server_ssh_pswd)
    ret = conn.execute('echo 1')
    if ret[1] != '':
        fatal_error("fail to get ssh connection from here to recovery job server")
    return conn

def get_consistent_point_datetime_of_backup(conn, backup_dir_name):
    global backup_base_dir
    consistent_point_datetime_str = ''
    try:
        ret = conn.execute("cat %s/%s/consistent_point_datetime" % (backup_base_dir, backup_dir_name))
        if ret[1] == '':
            consistent_point_datetime_str =  ret[0][:-1]
    except:
        pass
    return consistent_point_datetime_str

def clean_bad_backups():
    global backup_base_dir

    dest_server_conn = get_one_dest_server_conn()
    ret = dest_server_conn.execute("ls %s" % (backup_base_dir))
    old_backup_list = ret[0].split('\n')[:-1]
    old_backup_list.sort(reverse=True)
    for backup_dir in old_backup_list:
        if backup_dir == 'trash' or backup_dir == 'encrypted' or backup_dir == 'socat_log':
            continue
        should_remove_to_trash = False
        try:
            ret = dest_server_conn.execute('cat %s/%s/ok' % (backup_base_dir, backup_dir))
            if ret[0] != 'ok\n':
                should_remove_to_trash = True
        except:
            should_remove_to_trash = True
        if should_remove_to_trash:
            dest_server_conn.execute('mv -f %s/%s %s/trash' % (backup_base_dir, backup_dir, backup_base_dir), timeout=30)
            try:
                dest_server_conn.execute('mv %s/encrypted/%s.tar.encrypted %s/trash' % (backup_base_dir, backup_dir, backup_base_dir), timeout=30)
            except:
                pass
            print_debug_info('backup dir: %s is not a complete backup, removed to trash' % (backup_dir), '')
            if recover_to_backup_dir is not None and recover_to_backup_dir == backup_dir:
                fatal_error('recover target backup is not a complete backup')
            break
        else:
            break
    dest_server_conn.close()


def get_backup_dir_list_to_use():
    global backup_base_dir
    global recover_to_backup_dir
    global backup_dir_list
    global recovery_stop_datetime

    print_trace_info(sys._getframe().f_code.co_name, "start")
    dest_server_conn = get_one_dest_server_conn()
    ret = dest_server_conn.execute("ls %s" % (backup_base_dir)) #only show directories
    old_backup_list = ret[0].split('\n')[0:-1] #remove the last empty string made by '\n'
    old_backup_list.sort(reverse=True)
    print_debug_info(old_backup_list, 'get all old backup directories list')
    counting = False
    if recovery_stop_datetime is not None:
        counting = True
    for elem in old_backup_list:
        if elem == 'trash' or elem == 'encrypted' or elem == 'socat_log':
            continue
        if elem == recover_to_backup_dir:
            counting = True
        if counting == True:
            print_debug_info(elem, 'get a backup dir to use')
            backup_dir_list.insert(0, elem)
        else:
            continue
        if elem.endswith('-full'):
            counting = False
            break
    print_debug_info(backup_dir_list, 'get backup directories list to use')
    backup_datetime_list = []
    if len(backup_dir_list) == 0:
        fatal_error('there is no backup to recover')
    for backup_dir in backup_dir_list:
        consistent_point_datetime = get_consistent_point_datetime_of_backup(dest_server_conn, backup_dir)
        if consistent_point_datetime == "":
            fatal_error("folder %s doesn't contain consistent point datetime file" % (backup_dir))
        backup_datetime_list.append(datetime.strptime(consistent_point_datetime, '%Y-%m-%d %H:%M:%S'))
    if recovery_stop_datetime is not None:
        if len(backup_dir_list) == 1:
            fatal_error("can't recovery to a time point when there is only one full backup: %s" % (backup_dir_list[0]))
        stop_datetime = datetime.strptime(recovery_stop_datetime, '%Y-%m-%d %H:%M:%S')
        start = None
        end = None
        for t in backup_datetime_list:
            if t < stop_datetime:
                start = t
            elif t == stop_datetime:
                start = t
                end = t
                break
            else:
                end = t
                break
        if start is None:
            fatal_error("can't recover to the datetime: %s, the first backup is: %s, it's consistent point is: %s" \
                    % (stop_datetime, backup_dir_list[0], backup_datetime_list[0].strftime('%Y-%m-%d %H:%M:%S')))
        if end is None:
            fatal_error("can't recover to the datetime: %s, the last backup is: %s, it's consistent point is : %s" \
                    % (stop_datetime, backup_dir_list[-1], backup_datetime_list[-1].strftime('%Y-%m-%d %H:%M:%S')))
        recover_to_backup_dir = backup_dir_list[backup_datetime_list.index(start)]
        global next_backup_of_target_backup
        next_backup_of_target_backup = backup_dir_list[backup_datetime_list.index(end)]
        backup_dir_list = backup_dir_list[:backup_datetime_list.index(end)]
        print_debug_info('backup_dir_list: %s' % (' '.join(backup_dir_list)), 'after check stop datetime')

    dest_server_conn.close()
    print_trace_info(sys._getframe().f_code.co_name, "finish")


def parse_mysql_server_user_password_from_dbscale_conf():
    global backup_base_dir
    global backup_dir_list

    conn = get_one_dest_server_conn()
    lines = ''
    try:
        ret = conn.execute('cat %s/%s/calcnode_base/dbscale.conf' % (backup_base_dir, backup_dir_list[0]))
        lines = ret[0]
    except Exception as e:
        fatal_error('cluster ssh user do not have enough permission to backup dbscale config file')

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

def extract_latest_cluster_consistence_info():
    global recover_to_backup_dir
    global backup_base_dir
    global cluster_consistence_info

    dest_server_conn = get_one_dest_server_conn()
    ret = dest_server_conn.execute("cat %s/%s/cluster_consistence_info" % (backup_base_dir, recover_to_backup_dir))
    dest_server_conn.close()
    info_list_tmp = ret[0].split('\n')[0:-1]
    for info in info_list_tmp:
        line = info.split('\t')
        #for convenience, to build dir name to use as an id like: source1-server1-192.168.122.110-13003
        dir_id = line[4][len('belong-to-datasource='):] + "-" + line[1] + "-" + line[0].split(":")[0] + "-" + line[0].split(":")[1]
        line.insert(0, dir_id)
        cluster_consistence_info.append(line)
        print_debug_info(line, '')


def decrypt_sample(backup_full_dir, conn):
    global connect_method
    err = False
    try:
        ret = conn.execute('cd %s && ls | grep encrypted_sample' % (backup_full_dir))
        if ret[1] != '':
            err = True
        if ret[0] == '' and connect_method == 2:
            err = True
    except:
        err = True
    if args.password is not None and err:
        print_debug_info('no need for password', 'backup has no password')
        return False
    if args.password is None and not err:
        fatal_error('need password to recover')
    if args.password is None and err:
        return False
    try:
        ret = conn.execute('cd %s && pyAesCrypt -p %s -d ./encrypted_sample -o sample' % (backup_full_dir, args.password), timeout=30)
    except Exception as e:
        fatal_error('password or other err: %s, currently at: %s' % (e.result.stderr, backup_full_dir))
    return True

def decrypt_used_backups():
    print_trace_info(sys._getframe().f_code.co_name, "start")
    global backup_dir_list
    global backup_base_dir
    global next_backup_of_target_backup
    global is_using_encryption

    conn = get_one_dest_server_conn()

    need_decrypt_backup_list = backup_dir_list.copy()
    if recovery_stop_datetime is not None:
        need_decrypt_backup_list.append(next_backup_of_target_backup)

    for backup_dir in need_decrypt_backup_list:
        backup_full_dir = backup_base_dir + '/' + backup_dir
        if decrypt_sample(backup_full_dir, conn):
            is_using_encryption = True
            cmd = 'pyAesCrypt -p {0} -d {1}/encrypted/{2}.tar.encrypted -o {1}/{2}/{2}.tar'.format(args.password, backup_base_dir, backup_dir, backup_full_dir)
            global conn_timeout
            conn.execute(cmd, timeout=conn_timeout)
            cmd = 'cd {0}/{1} && tar -xf {1}.tar && rm -f {1}.tar'.format(backup_base_dir, backup_dir)
            conn.execute(cmd, timeout=conn_timeout)

    conn.close()
    print_trace_info(sys._getframe().f_code.co_name, "finish")

def get_one_server_conn(host, user, port, pswd, using_rds_taskmanager=False):
    global connect_method

    if connect_method == 1:
        global rabbitmq_uri
        if using_rds_taskmanager:
            conn = Conn.AgentConn(rabbitmq_uri, host, agent_type='rds-taskmanager')
        else:
            conn = Conn.AgentConn(rabbitmq_uri, host, agent_type='rds-dbinit')
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

def transfer_folder_in_conn(conn :Conn.BackupConn, dest_ip, folder_from_dir, folder_from_name, folder_to_dir, folder_to_name, **kargs):
    port = kargs.get('port')
    user = kargs.get('user')
    pswd = kargs.get('pswd')
    global conn_timeout
    global socat_retry_count
    global socat_log_dir
    def Socatexecute(conn, cmd):
        global conn_timeout
        try:
            ret = conn.execute(cmd, timeout=conn_timeout)
        except Exception as e:
            return

    if using_socat:
        #pack the folder
        socat_port = kargs.get('socat-port')
        conn.execute('cd %s && tar -cf %s/%s.tar %s' % (folder_from_dir, folder_from_dir, folder_to_name, folder_from_name), timeout=conn_timeout)
        dest_conn = get_one_server_conn(dest_ip, user, port, pswd)
        dest_conn2 = get_one_server_conn(dest_ip, user, port, pswd)
        can_retry_times = socat_retry_count
        is_success = False
        while True and can_retry_times > -1:
            socat_log_file = str(socat_port) + '_' + str(can_retry_times)
            start_cmd = 'socat -d -lf %s/%s -u tcp-listen:%s,reuseaddr file:%s/%s-new.tar,trunc,creat' % (socat_log_dir, socat_log_file, socat_port, folder_to_dir, folder_to_name)
            th = threading.Thread(target=Socatexecute, args=(dest_conn, start_cmd))
            th.start()
            i = 0
            while True and i <= 100:
                i = i + 1
                try:
                    ret = dest_conn2.execute('ps ajx | grep "socat -d -lf %s/%s -u tcp-listen:%s,reuseaddr file:" | grep -v "grep"' % (socat_log_dir, socat_log_file, socat_port))[0]
                except:
                    continue
                if ret != '':
                    is_success = True
                    dest_conn2.close()
                    print_debug_info('transfering folder: %s/%s' % (folder_from_dir, folder_from_name), '')
                    conn.execute('cat %s/%s.tar | socat - tcp4:%s:%s,retry=%s' % (folder_from_dir, folder_to_name, dest_ip, socat_port, socat_retry_count), timeout=conn_timeout)
                    th.join()
                    break
            if i > 100:
                try:
                    ret = dest_conn2.execute('cat %s/%s | grep "Address already in use" | grep -v "grep"' % (socat_log_dir, socat_log_file), timeout=conn_timeout)[0]
                except Exception as e:
                    dest_conn.close()
                    dest_conn2.close()
                    fatal_error("failed to start socat but not the port is occupied, please check the log file %s/%s" % (socat_log_dir, socat_log_file))
                if ret != '':
                    print_debug_info("find the socat port %d is occupied, change socat_port and try again" % (socat_port), 'socat log info:')
                    socat_port = socat_port + 10
                    can_retry_times = can_retry_times - 1
            if is_success == True:
                break
        if can_retry_times == -1:
            fatal_error('running socat on server: %s error using port: %s and retry max times %s' % (dest_ip, port, socat_retry_count))

        conn.execute('rm -f %s/%s.tar' % (folder_from_dir, folder_to_name))
        #unpack it on the dest server
        dest_conn.execute('cd %s && tar -xf ./%s-new.tar' % (folder_to_dir, folder_to_name), timeout=conn_timeout)
        dest_conn.execute('rm -f %s/%s-new.tar' % (folder_to_dir, folder_to_name))
        dest_conn.close()
        kargs['socat-port'] = socat_port
    else:
        cmd = "scp -r -P%s %s/%s %s@%s:%s" % (port, folder_from_dir, folder_from_name, user, dest_ip, folder_to_dir)
        print_debug_info(cmd, "command to scp backup dir to recovery job server")
        conn.add_watcher(r'.*password.*', recovery_job_server_ssh_pswd + '\n')
        conn.execute(cmd)

def put_backup_dir_to_recovery_job_server():
    global recovery_job_server_dir
    global backup_base_dir
    global backup_dir_list
    global recovery_stop_datetime
    global socat_log_dir

    print_trace_info(sys._getframe().f_code.co_name, "start")
    recovery_job_server_conn = get_one_recovery_job_server_conn()
    recovery_job_server_conn.execute("rm -rf %s && mkdir -p %s" % (recovery_job_server_dir, recovery_job_server_dir))
    print_debug_info(recovery_job_server_dir, "work dir for current job on recovery job server")
    recovery_job_server_conn.execute("mkdir -p %s/datadir" % recovery_job_server_dir)
    recovery_job_server_conn.execute("mkdir -p %s/tmp" % recovery_job_server_dir)
    recovery_job_server_conn.execute("mkdir -p %s/datanode_base" % recovery_job_server_dir)
    tmp_time = time.strftime("%Y%m%d", time.localtime())
    socat_log_dir = recovery_job_server_dir + '/socat_log/' + tmp_time
    recovery_job_server_conn.execute("mkdir -p %s" % socat_log_dir)
    recovery_job_server_conn.close()

    dest_server_conn = get_one_dest_server_conn()
    need_copy_backup_dir_list = backup_dir_list.copy()
    need_copy_backup_dir_list.sort()
    if recovery_stop_datetime is not None and need_copy_backup_dir_list[-1] != next_backup_of_target_backup:
        need_copy_backup_dir_list.append(next_backup_of_target_backup)
    config = {
            'port': recovery_job_server_ssh_port,
            'user': recovery_job_server_ssh_user,
            'pswd': recovery_job_server_ssh_pswd
            }
    global using_socat
    global socat_port2
    if using_socat:
        config['socat-port'] = socat_port2
    for one_dir in need_copy_backup_dir_list:
        one_full_folder_dir = backup_base_dir
        transfer_folder_in_conn(dest_server_conn, recovery_job_server_ssh_ip, one_full_folder_dir, one_dir, recovery_job_server_dir, one_dir, **config)
    dest_server_conn.close()
    print_trace_info(sys._getframe().f_code.co_name, "finish")


def init_config_from_conf_file():
    print_trace_info(sys._getframe().f_code.co_name, "start")

    global recovery_job_server_ssh_ip
    global recovery_job_server_ssh_port
    global recovery_job_server_ssh_user
    global recovery_job_server_ssh_pswd
    global recovery_job_server_dir

    global backup_dest_ip
    global backup_dest_port
    global backup_dest_user
    global backup_dest_pswd
    global backup_base_dir

    global debug
    global socat_port
    global socat_retry_count
    global recover_to_backup_dir
    global recovery_conf_file
    global server_tmp_port
    global recovery_stop_datetime

    global connect_method
    global rabbitmq_uri
    global adm_server_port

    global client_name
    global xtrabackup_bin_dir
    global xbstream_bin_dir

    config = configparser.ConfigParser()
    if args.config_file is not None:
        recovery_conf_file = args.config_file
        print_debug_info(recovery_conf_file, "use config file")
    config.read(recovery_conf_file)

    if config['misc']['debug'] == '0' or config['misc']['debug'].lower() == 'false':
        debug = False
    else:
        debug = True

    backup_dest_ip =   config['backup-store']['ip']
    backup_dest_port = config['backup-store']['port']
    backup_dest_user = config['backup-store']['user']
    backup_dest_pswd = config['backup-store']['password']
    backup_base_dir =  config['backup-store']['dir']

    recovery_job_server_ssh_ip =   config['recovery-job-server']['ip']
    recovery_job_server_ssh_port = config['recovery-job-server']['port']
    recovery_job_server_ssh_user = config['recovery-job-server']['user']
    recovery_job_server_ssh_pswd = config['recovery-job-server']['password']

    socat_port = int(config['misc']['socat-port'])
    global socat_port2
    if using_socat:
        socat_port2_str = config['misc'].get('socat-port2')
        if socat_port2_str is not None:
            socat_port2 = int(socat_port2_str)
        else:
            fatal_error('no socat port2 configed')
    recover_to_backup_dir = config['recover-to'].get('dir')
    server_tmp_port = config['misc']['server-tmp-port']

    if config['misc'].get('for-testing') is not None:
        global for_testing
        for_testing = True

    recovery_stop_datetime = config['recover-to'].get('stop-datetime')
    if recovery_stop_datetime is not None:
        if recover_to_backup_dir is not None:
            fatal_error('[recover-to].dir and [recover-to].stop-datetime are exclusive, should be used seperately')
        else:
            recover_to_backup_dir = recovery_stop_datetime.replace(' ', '_').replace(':', '_').replace('-', '_')
        try:
            datetime.strptime(recovery_stop_datetime, "%Y-%m-%d %H:%M:%S")
        except:
            fatal_error('parse stop datetime error: %s' % (recovery_stop_datetime))
    print_debug_info(recover_to_backup_dir, "recover to")
    recovery_job_server_dir = config['recovery-job-server']['work-dir'] + '/' + recover_to_backup_dir

    conn_timeout_str = config['misc'].get('conn-timeout')
    if conn_timeout_str is not None:
        global conn_timeout
        conn_timeout = int(conn_timeout_str)

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
        rabbitmq_uri = config['rabbit-agent']['rabbitmq-uri']
    elif connect_method == 2:
        if config.get('adm-agent', 'port') is None:
            fatal_error('adm agent config not found')
        adm_server_port = config['adm-agent']['port']

    if config['misc'].get('client-name') is not None:
        client_name = config['misc'].get('client-name')

    if 'socat-retry-count' in config['misc'].keys():
        socat_retry_count = int(config['misc']['socat-retry-count'])

    xtrabackup_bin_dir_config = config['misc'].get('xtrabackup-bin-dir')
    if xtrabackup_bin_dir_config is not None:
        xtrabackup_bin_dir = xtrabackup_bin_dir_config
    xbstream_bin_dir_config = config['misc'].get('xbstream-bin-dir')
    if xbstream_bin_dir_config is not None:
        xbstream_bin_dir = xbstream_bin_dir_config

    print_trace_info(sys._getframe().f_code.co_name, "finish")

def check_if_do_backup_for_masters(conn):
    cmd = "grep 'backup-master' %s/%s/backup.conf" % (recovery_job_server_dir, recover_to_backup_dir)
    try:
        stdout = conn.execute(cmd)[0]
        words = str(stdout).split('=')
        if len(words) < 2:
            return False
        value = words[1].strip()
        if value.isdigit() and int(value) != 0:
            return True
        elif not value.isdigit() and value.capitalize() == 'True':
            return True
    except Exception as e:
        return False
    return False

def check_is_master_server_datadir(recovery_job_server_conn, server_datadir):
    backuped_data_dir = recovery_job_server_dir + '/' + recover_to_backup_dir + '/' + server_datadir
    (stdout, stderr) = recovery_job_server_conn.execute('cd %s && ls %s | grep master_dir | wc -l' % (recovery_job_server_dir, backuped_data_dir))
    if len(str(stderr)) == 0 and str(stdout) == '1\n':
        return False
    return True

def get_master_server_datadir(conn, slave_datadir):
    cmd = 'cat %s/%s/%s/master_dir' % (recovery_job_server_dir, recover_to_backup_dir, slave_datadir)
    (stdout, stderr) = conn.execute(cmd)
    if len(str(stderr)) == 0:
        return stdout[:-1]
    else:
        raise Exception('get master server datadir error cmd: %s, std')

def check_and_kill_cmd(conn, cmd):
    global conn_timeout
    pid_to_kill = -1
    try:
        ret = conn.execute('ps ajx | grep "%s" | grep -v "grep"' % cmd, timeout=conn_timeout)[0]
        print_debug_info(ret,'')
        if len(ret) > 0 and len(ret.split()) > 3:
            pid_to_kill = int(ret.split()[1])
        if pid_to_kill == -1:
            print_debug_info("Prepare to kill process %d doesn't exist." % pid_to_kill,'')
            return
        print_debug_info("Prepare to kill process %d." % pid_to_kill,'')
        conn.execute('kill -9 %d' % pid_to_kill, timeout=conn_timeout)[0]
        print_debug_info("End to kill process %d." % pid_to_kill,'')
    except Exception as e:
        print_debug_info(e.args, "fail to do kill for command info %s. due to" % cmd)

def prepare_datadir_for_all_datasource():
    global recovery_job_server_dir
    global recovery_job_server_ssh_ip
    global recovery_stop_datetime
    global for_testing
    global conn_timeout
    global server_version
    global xtrabackup_bin_dir
    global xbstream_bin_dir

    print_trace_info(sys._getframe().f_code.co_name, "start")
    recovery_job_server_conn = get_one_recovery_job_server_conn()
    datadir_list = []
    ret = recovery_job_server_conn.execute("ls %s/*-full" % recovery_job_server_dir)
    datadir_list = ret[0].split('\n')[0:-1]
    datadir_list.remove('backup.conf')
    datadir_list.remove('calcnode_base')
    datadir_list.remove('cluster_consistence_info')
    datadir_list.remove('consistent_point_datetime')
    datadir_list.remove('datanode_base')
    datadir_list.remove('backuped_binlogs')
    datadir_list.remove('ok')
    if datadir_list.count('encrypted_sample') != 0:
        datadir_list.remove('encrypted_sample')
    if datadir_list.count('sample') != 0:
        datadir_list.remove('sample')
    do_backup_for_masters = check_if_do_backup_for_masters(recovery_job_server_conn)
    recovery_job_server_conn.close()
    index = 0
    has_recovered_master_dir = set()
    for datadir in datadir_list:
        master_dir = datadir ## master server's datadir, default to itself
        recovery_job_server_conn = get_one_recovery_job_server_conn()
        backuped_data_dir = recovery_job_server_dir + '/' + recover_to_backup_dir + '/' + datadir
        binlog_dest_dir = backuped_data_dir
        ## when we backup all servers(including slave servers), when recovery, we choose master server to do recovery
        ## when we only backup slave servers, we have no choice

        is_master_server_datadir = check_is_master_server_datadir(recovery_job_server_conn, datadir)
        if do_backup_for_masters: ## backup master, then skip slaves
            if not is_master_server_datadir:
                continue
        else: ## only backup slaves, we can't skip slaves then
            if is_master_server_datadir:
                ## at this case, the datadir is empty except the binlogs
                ## we skip this datadir
                continue
            else:
                ## slave datadir, then do recovery
                ## if the ds of this slave is recovered, skip do recovery
                master_dir = get_master_server_datadir(recovery_job_server_conn, datadir)
                if has_recovered_master_dir.issuperset({master_dir}):
                    continue
                binlog_dest_dir = recovery_job_server_dir + '/' + recover_to_backup_dir + '/' + master_dir
                has_recovered_master_dir.add(master_dir)

        # copy next_backup_of_target_backup's binlog
        if recovery_stop_datetime is not None and next_backup_of_target_backup != recover_to_backup_dir:
            next_backup_dir = recovery_job_server_dir + '/' + next_backup_of_target_backup + '/' +  master_dir
            recovery_job_server_conn.execute(
                    "cd %s && cp -a `ls %s | grep -E '.+\.[0-9]{6,6}'` -t %s"
                    % (next_backup_dir, next_backup_dir, binlog_dest_dir), timeout=conn_timeout)

        cmd = "cd %s && mkdir -p datadir/%s" % (recovery_job_server_dir, datadir)
        print_trace_info("prepare datadir datadir/%s/" % datadir, "start")
        recovery_job_server_conn.execute(cmd)
        #cmd = "cp %s/%s/my.cnf datadir/%s/my.cnf" % (recover_to_backup_dir, datadir, datadir)
        #recovery_job_server_conn.execute(cmd)
        is_handling_full_backup = True
        backup_dir_count = len(backup_dir_list)
        for backup_dir in backup_dir_list: #the first one is full backup dir
            if is_handling_full_backup:
                cmd = "cd %s && %s -x < %s/%s/backup.xbsteam -C datadir/%s/" % (recovery_job_server_dir, xbstream_bin_dir, backup_dir, datadir, datadir)
                print_debug_info(cmd, "cmd to extract full backup")
                recovery_job_server_conn.execute(cmd, timeout=conn_timeout)

                cmd = "find %s/datadir/%s/ -name *.qp |wc -l" %(recovery_job_server_dir, datadir)
                output, err = recovery_job_server_conn.execute(cmd, timeout=conn_timeout)
                output = int(output)
                if output > 0:
                    cmd = "cd %s && %s --decompress --remove-original --target-dir=datadir/%s/" % (recovery_job_server_dir, xtrabackup_bin_dir, datadir)
                    print_debug_info(cmd, "cmd to decompress full backup")
                    recovery_job_server_conn.execute(cmd, timeout=conn_timeout)

                cmd = "cd %s && %s --prepare --target-dir=datadir/%s/ 2>tmp/%s-%s.log" % (recovery_job_server_dir, xtrabackup_bin_dir, datadir, datadir, backup_dir)
                cmd = cmd + " --apply-log-only"
                print_debug_info(cmd, "cmd to prepare full backup")
                recovery_job_server_conn.execute(cmd, timeout=conn_timeout)

                is_handling_full_backup = False
            else:
                cmd = "cd %s && mkdir -p %s/%s/inc_recover_dir && %s -x < %s/%s/backup.xbsteam -C %s/%s/inc_recover_dir" % (recovery_job_server_dir, backup_dir, datadir, xbstream_bin_dir, backup_dir, datadir, backup_dir, datadir)
                print_debug_info(cmd, "cmd to extract inc backup")
                recovery_job_server_conn.execute(cmd, timeout=conn_timeout)

                cmd = "find %s/%s/%s/inc_recover_dir -name *.qp |wc -l" %(recovery_job_server_dir, backup_dir, datadir)
                output, err = recovery_job_server_conn.execute(cmd, timeout=conn_timeout)
                output = int(output)
                if output > 0:
                    cmd = "cd %s && %s --decompress --remove-original --target-dir=%s/%s/inc_recover_dir" % (recovery_job_server_dir, xtrabackup_bin_dir, backup_dir, datadir)
                    print_debug_info(cmd, "cmd to decompress inc backup")
                    recovery_job_server_conn.execute(cmd, timeout=conn_timeout)

                cmd = "cd %s && %s --prepare --target-dir=datadir/%s/ --incremental-dir=%s/%s/inc_recover_dir 2>tmp/%s-%s.log" % (recovery_job_server_dir, xtrabackup_bin_dir, datadir, backup_dir, datadir, datadir, backup_dir)
                cmd = cmd + " --apply-log-only"
                print_debug_info(cmd, "cmd to prepare inc backup")
                recovery_job_server_conn.execute(cmd, timeout=conn_timeout)


        #start datanode server to apply binlog to it
        print_trace_info("apply binlog for %s" % (datadir), "start")
        ret = recovery_job_server_conn.execute("ls %s/datanode_base" % recovery_job_server_dir)
        datanode_base_dir = recovery_job_server_dir + "/datanode_base/" + ret[0][0:-1]
        datanode_server_bin = datanode_base_dir + "/bin/" + "%sd" % client_name
        datanode_client_bin = datanode_base_dir + "/bin/" + client_name
        datanode_lc_messages_dir = datanode_base_dir + "/share/"
        datanode_datadir = recovery_job_server_dir + "/datadir/" + datadir + "/"
        datanode_socket = "/tmp/" + datadir + ".sock"
        datanode_pid_file = datanode_datadir + "/db.pid"
        datanode_log_error = datanode_datadir + "/db.err"

        datanode_port = str(int(server_tmp_port) + index) if for_testing else server_tmp_port
        datanode_server_id = str(int(server_tmp_port) + index) if for_testing else server_tmp_port
        index = index + 1

        datanode_extra_opt = " --ssl=0 --binlog-format=row --log-bin=master-bin --daemonize"

        index_start = datadir.find('-')
        server_name = datadir[index_start+1:]
        server_name = server_name[:server_name.find('-')]
        db_connect_user = dataserver_user_pswd_map[server_name]['user']
        db_connect_pswd = dataserver_user_pswd_map[server_name]['password']

        #./datanode_base/mysql-5.7.24-linux-glibc2.12-x86_64/bin/mysqld
        #--lc-messages-dir=./datanode_base/mysql-5.7.24-linux-glibc2.12-x86_64/share/
        #--datadir=/tmp/recover_dir/UTC20201122055129.933-inc/datadir/auth-auth_server-192.168.122.110-13004/
        #--socket=/tmp/recover_dir/UTC20201122055129.933-inc/datadir/auth-auth_server-192.168.122.110-13004/db.sock
        #--pid-file=/tmp/recover_dir/UTC20201122055129.933-inc/datadir/auth-auth_server-192.168.122.110-13004/db.pid
        #--port=27000
        #--server-id=27000
        #--ssl=0 --binlog-format=row --log-bin=master-bin
        tmp_mysqld_cmd = datanode_server_bin + \
                " --lc-messages-dir=" + datanode_lc_messages_dir + \
                " --datadir=" + datanode_datadir + \
                " --socket=" + datanode_socket + \
                " --pid-file=" + datanode_pid_file + \
                " --log-error=" + datanode_log_error + \
                " --port=" + datanode_port + \
                " --server-id=" + datanode_server_id + datanode_extra_opt + \
                " --gtid_mode=ON --enforce-gtid-consistency=1 --lower-case-table-names=1"
        datanode_exec_cmd = 'cd %s && ' % recovery_job_server_dir + tmp_mysqld_cmd
        if server_version == 8:
            datanode_exec_cmd = datanode_exec_cmd + " --mysqlx=0"
        print_debug_info(datanode_exec_cmd, "cmd to start datanode process")
        ret = recovery_job_server_conn.execute(datanode_exec_cmd, timeout=30)
        if ret[1] != '':
            check_and_kill_cmd(recovery_job_server_conn, tmp_mysqld_cmd)
            fatal_error("fail to start datanode process using cmd : %s" % (datanode_exec_cmd))
        datanode_login_cmd = datanode_client_bin + \
                " -u" + db_connect_user + \
                " -P" + datanode_port + \
                " -h" + recovery_job_server_ssh_ip + \
                " --connect-timeout=5 -A"
        if len(db_connect_pswd) != 0:
                datanode_login_cmd += " -p" + db_connect_pswd
        wait_datanode_server_ready(recovery_job_server_conn, datanode_login_cmd, tmp_mysqld_cmd)

        #apply binlog to consistence point
        #apply binlog to stop datetime if specified
        dt_str = ''
        for info in cluster_consistence_info:
            if info[0] == datadir:
                #reset slave all
                reset_slave_all_cmd = '''cd %s && %s -u %s -h %s -P %s -e "reset slave all"'''
                reset_slave_all_cmd = reset_slave_all_cmd \
                        % (recovery_job_server_dir, client_name, db_connect_user, recovery_job_server_ssh_ip, datanode_port)
                if len(db_connect_pswd) != 0:
                    reset_slave_all_cmd += " -p" + db_connect_pswd
                print_debug_info(reset_slave_all_cmd, 'cmd to reset slave all')
                ret = recovery_job_server_conn.execute(reset_slave_all_cmd, timeout=conn_timeout)
                if ret[1].find('error') != -1 or ret[1].find('ERROR') != -1:
                    check_and_kill_cmd(recovery_job_server_conn, tmp_mysqld_cmd)
                    fatal_error('failed to RESET SLAVE ALL using cmd: %s' % (reset_slave_all_cmd))

                # set gtid_purged
                set_gtid_purged_cmd = '''cd %s && %s -u %s -h %s -P %s -e "reset master; set @@global.gtid_purged ='`cat %s/%s/executed_gtids`'"'''
                set_gtid_purged_cmd = set_gtid_purged_cmd \
                        % (recovery_job_server_dir, client_name, db_connect_user, recovery_job_server_ssh_ip, datanode_port, backup_dir, datadir)
                if len(db_connect_pswd) != 0:
                    set_gtid_purged_cmd += " -p" + db_connect_pswd
                print_debug_info(set_gtid_purged_cmd, 'cmd to set gtid_purged')
                ret = recovery_job_server_conn.execute(set_gtid_purged_cmd, timeout=conn_timeout)
                if ret[1].find('error') != -1 or ret[1].find('ERROR') != -1:
                    check_and_kill_cmd(recovery_job_server_conn, tmp_mysqld_cmd)
                    fatal_error('failed to set gtid purged using cmd: %s' % (set_gtid_purged_cmd))

                # get need apply binlogs
                start_binlog_name = info[7].split('=')[-1]
                binlog_base_name = start_binlog_name[0:-7]
                end_binlog_name = ''
                if not recovery_stop_datetime:
                    end_binlog_name = info[3]
                all_binlogs = recovery_job_server_conn.execute('cd %s && ls %s/%s | grep %s.[0-9]*' % \
                    (recovery_job_server_dir, backup_dir, master_dir, binlog_base_name))[0].split('\n')[0:-1]
                used_binlogs = []
                all_binlogs.sort()
                print_debug_info(' '.join(all_binlogs), 'server: %s' % datadir)
                start = False
                for binlog in all_binlogs:
                    if binlog == end_binlog_name:
                        used_binlogs.append(binlog)
                        break
                    elif start or binlog == start_binlog_name:
                        used_binlogs.append(binlog)
                        start = True
                    else:
                        continue
                used_binlogs = ' '.join(used_binlogs)

                from_pos = info[8].split(',')[0].split('=')[1]
                stop = ''
                if recovery_stop_datetime:
                    if not for_testing:
                        stop = "--stop-datetime='%s'" % (recovery_stop_datetime)
                    else:
                        dt = datetime.strptime(recovery_stop_datetime, "%Y-%m-%d %H:%M:%S")
                        dt = dt + timedelta(seconds=random.randint(1,5))
                        dt_str = dt.strftime("%Y-%m-%d %H:%M:%S")
                        stop = "--stop-datetime='%s'" % (dt)
                else:
                    stop = '--stop-position=' + info[4]
                datanode_server_binlog_bin = datanode_base_dir + "/bin/" + "%sbinlog" % client_name
                apply_binlog_cmd = "cd %s/%s/%s && %s --exclude-gtids=`cat ../%s/executed_gtids` %s --start-position=%s %s | %s -u %s -h %s -P %s " \
                    % (recovery_job_server_dir, backup_dir, master_dir,
                            datanode_server_binlog_bin, datadir, used_binlogs, from_pos, stop,
                            client_name, db_connect_user, recovery_job_server_ssh_ip, datanode_port)
                if len(db_connect_pswd) != 0:
                    apply_binlog_cmd += " -p" + db_connect_pswd
                print_debug_info(apply_binlog_cmd, "cmd to apply binlogs")
                ret = recovery_job_server_conn.execute(apply_binlog_cmd, timeout=2*conn_timeout)
                print_debug_info(ret[0], "apply binlog stdout: ")
                print_debug_info(ret[1], "apply binlog stderr: ")
                if ret[1].find('error') != -1 or ret[1].find('ERROR') != -1:
                    check_and_kill_cmd(recovery_job_server_conn, tmp_mysqld_cmd)
                    fatal_error("failed to apply binlog using cmd: %s" % (apply_binlog_cmd))
                shutdown_cmd = "%s -u %s -h %s -P %s -e 'shutdown'" % (client_name, db_connect_user, recovery_job_server_ssh_ip, datanode_port)
                if len(db_connect_pswd) != 0:
                    shutdown_cmd += " -p" + db_connect_pswd
                if not for_testing:
                    ret = recovery_job_server_conn.execute(shutdown_cmd)
                    if ret[1].find('error') != -1 or ret[1].find('ERROR') != -1:
                        check_and_kill_cmd(recovery_job_server_conn, tmp_mysqld_cmd)
                        fatal_error("failed to shutdown %s: %s" % (client_name, shutdown_cmd))

                # rm the auto.cnf
                rm_auto_conf_cmd = "rm -f %s/datadir/%s/auto.cnf" % (recovery_job_server_dir, datadir)
                try:
                    recovery_job_server_conn.execute(rm_auto_conf_cmd)
                except:
                    pass
                break

        # write ds_info for dbscale-pit-recovery-tool
        if recovery_stop_datetime is not None:
            try:
                id = recovery_stop_datetime.replace(' ', '_').replace(':', '_').replace('-', '_')
                ds_name = datadir[:datadir.find('-')]
                if ds_name != 'auth':
                    binlogs = recovery_job_server_conn.execute('ls %s/%s/%s/ | grep -E ".+\.[0-9]{6,6}"' % (recovery_job_server_dir, backup_dir, master_dir))[0]
                    first_binlog = binlogs.split('\n')[0]
                    binlog_full_name = '{0}/{1}/{2}/{3}'.format(recovery_job_server_dir, backup_dir, master_dir, first_binlog)
                    recovery_job_server_conn.execute("echo -e '%s\t%s\t%s' >> /tmp/ds_info_%s" % (ds_name, dt_str, binlog_full_name, id))
                    recovery_job_server_conn.execute("echo ds_info_%s > /tmp/ds_info_file_name" % id)
            except Exception as e:
                print_debug_info('write ds_info file error: %s' % e.args, '')

        print_trace_info("apply binlog for %s" % (datadir), "finish")
        print_trace_info("prepare datadir datadir/%s/" % datadir, "finish")
        recovery_job_server_conn.close()
    print_trace_info(sys._getframe().f_code.co_name, "finish")


def wait_datanode_server_ready(conn, datanode_login_cmd, tmp_mysqld_cmd):
    print_trace_info(sys._getframe().f_code.co_name, "start")
    cmd = datanode_login_cmd + " -e 'select 1'"
    print_debug_info(cmd, "cmd to check whether datanode is ready to apply binlog")
    retry_count = 0
    while True:
        try:
            time.sleep(1)
            conn.execute('cd %s && ' % (recovery_job_server_dir) + cmd)
            break
        except Exception as e:
            print_debug_info(cmd, "wait 1 more second to retry check whether datanode server is ready to apply binlog, cmd is")
            retry_count = retry_count + 1
            if retry_count >= 60:
                check_and_kill_cmd(conn, tmp_mysqld_cmd)
                fatal_error(str(e.args))
            else:
                continue
    print_trace_info(sys._getframe().f_code.co_name, "finish")


def prepare_datanode_base():
    global recovery_job_server_dir
    global backup_dir_list
    global conn_timeout

    print_trace_info(sys._getframe().f_code.co_name, "start")
    recovery_job_server_conn = get_one_recovery_job_server_conn()
    ret = recovery_job_server_conn.execute("ls %s/%s/datanode_base/*.tar.gz" % (recovery_job_server_dir, backup_dir_list[0]))
    datanode_base_file = ret[0][0:-1]
    recovery_job_server_conn.execute("cd %s && tar -zxf %s -C datanode_base/ 2>tmp/datanode_base_extract.log" % (recovery_job_server_dir, datanode_base_file), timeout=conn_timeout)
    recovery_job_server_conn.close()
    print_trace_info(sys._getframe().f_code.co_name, "finish")

def get_datanode_server_version():
    global recovery_job_server_dir
    global backup_dir_list
    global conn_timeout
    global client_name
    global server_version

    print_trace_info(sys._getframe().f_code.co_name, "start")
    recovery_job_server_conn = get_one_recovery_job_server_conn()
    ret = recovery_job_server_conn.execute("ls %s/datanode_base/" % (recovery_job_server_dir))
    datanode_base_dir = ret[0][0:-1]
    ret = recovery_job_server_conn.execute("cd %s/datanode_base/%s/ && ./bin/%sd --version" % (recovery_job_server_dir, datanode_base_dir, client_name), timeout=conn_timeout)
    if ret[0].find(' Ver ') > 0:
        version_info = ret[0].split(' Ver ')[1]
        if version_info.startswith('8.0'):
            server_version = 8
    elif ret[0].find(' Distrib ') > 0:
        version_info = ret[0].split(' Distrib ')[1].split('-')[1]
        if version_info.find(' 8.0.'):
            server_version = 8
    else:
        server_version = 7
    print_debug_info(server_version, 'get backend server version')
    recovery_job_server_conn.close()
    print_trace_info(sys._getframe().f_code.co_name, "finish")

def prepare_calcnode_base():
    global recovery_job_server_dir
    global backup_dir_list
    global recover_to_backup_dir
    global conn_timeout
    print_trace_info(sys._getframe().f_code.co_name, "start")
    recovery_job_server_conn = get_one_recovery_job_server_conn()
    cmd = "cd %s && cp -r %s/%s/calcnode_base ." % (recovery_job_server_dir, recovery_job_server_dir, backup_dir_list[0])
    recovery_job_server_conn.execute(cmd, timeout=conn_timeout)
    if len(backup_dir_list) > 1:
        cmd = "cd %s && cp -r %s/%s/calcnode_base ." % (recovery_job_server_dir, recovery_job_server_dir, backup_dir_list[-1])
        recovery_job_server_conn.execute(cmd, timeout=conn_timeout)
    recovery_job_server_conn.close()
    print_trace_info(sys._getframe().f_code.co_name, "finish")

def check_executable_tool(cmd):
    recovery_server_conn = get_one_recovery_job_server_conn()
    err = False
    try:
        ret = recovery_server_conn.execute("which %s" %(cmd))
        if ret[1] != '':
            err = True
    except Exception as e:
        err = True
    recovery_server_conn.close()
    if err:
        fatal_error("%s does not exists in /usr/bin." %(cmd))

def pre_check():
    global xtrabackup_bin_dir
    global xbstream_bin_dir
    print_trace_info(sys._getframe().f_code.co_name, "start")

    # check xtrabackup exists
    check_executable_tool(xtrabackup_bin_dir)
    # check xbstream exists
    check_executable_tool(xbstream_bin_dir)
    # check pyAesCrypt exists
    if args.password is not None:
        check_executable_tool("pyAesCrypt")
    print_trace_info(sys._getframe().f_code.co_name, "finish")

def clean_backuped_dir():
    global backup_dir_list
    global backup_base_dir
    global next_backup_of_target_backup

    if not is_using_encryption:
        return
    need_clean_backup_dir_list = backup_dir_list.copy()
    if recovery_stop_datetime is not None:
        need_clean_backup_dir_list.append(next_backup_of_target_backup)
    conn = get_one_dest_server_conn()
    for backup_dir in need_clean_backup_dir_list:
        backup_full_dir = backup_base_dir + '/' + backup_dir
        ret = conn.execute('ls %s'% (backup_full_dir))
        file_list = ret[0].split('\n')[:-1]
        if file_list.count('ok') != 0:
            file_list.remove('ok')
        if file_list.count('cluster_consistence_info') != 0:
            file_list.remove('cluster_consistence_info')
        if file_list.count('consistent_point_datetime') != 0:
            file_list.remove('consistent_point_datetime')
        if file_list.count('encrypted_sample') != 0:
            file_list.remove('encrypted_sample')
        if file_list.count('backuped_binlogs') != 0:
            file_list.remove('backuped_binlogs')
        full_file_list = list(map(lambda s: backup_base_dir + '/' + backup_dir + '/' + s, file_list))
        conn.execute('rm -rf %s' % (' '.join(full_file_list)), timeout=30)

exit_mesage = None

if __name__ == '__main__':
    if args.version:
        print("version: %s" % tool_version)
        sys.exit(0)

    print_trace_info("version: %s" % tool_version, '')
    init_config_from_conf_file()
    pre_check()
    clean_bad_backups()
    get_backup_dir_list_to_use()
    try:
        decrypt_used_backups()
        parse_mysql_server_user_password_from_dbscale_conf()
        extract_latest_cluster_consistence_info()
        put_backup_dir_to_recovery_job_server()
    except Exception as e:
        exit_mesage = str(e)
    finally:
        clean_backuped_dir()
        if exit_mesage is not None:
            fatal_error(exit_mesage)
    prepare_datanode_base()
    get_datanode_server_version()
    prepare_calcnode_base()
    prepare_datadir_for_all_datasource()
    print("recovery succeed", flush=True)
    sys.exit(0)

```