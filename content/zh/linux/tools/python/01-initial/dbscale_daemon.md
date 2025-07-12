---
title: dbscale_daemon.py
description: dbscale_daemon.py
date: 2023-10-26
weight: 20000


---

{{< alert >}}

{{< /alert >}}





```python
#!/usr/bin/python
import os
import sys
import time
import datetime
import atexit
import base64
import subprocess
from kazoo.client import KazooClient

login_retry_times = 100
check_io_retry_times = 3
DEFAULT_DBSCALE_PID_FILE = "dbscale.pid"
is_lvs = 0
use_recorded_pid = False
recorded_pid = ''
DEFAULT_DBSCALE_CONF_FILE = "dbscale.conf"
DEFAULT_CHECK_DBSCALE_ALIVE = 5

#no need change below params, they will be inited based on dbscale config file
log_file = ""
file_pid = DEFAULT_DBSCALE_PID_FILE
user = ''
password = ''
port = ''
host = ''
cur_dbscale_ip_port = ''
zk_hosts = ''
is_default = True
conf_file = DEFAULT_DBSCALE_CONF_FILE


def printInfo(msg):
        global log_file
        curtime = datetime.datetime.now()
        printInfo = 'timeout -s 9 5 bash -c "echo {tm} {info} >> {f}" '.format(tm=curtime, info=msg, f=log_file)
        subprocess.call(printInfo, shell=True)


def insertInfo(msg):
        global log_file
        curtime = datetime.datetime.now()
        insertInfo = 'timeout -s 9 3 bash -c "echo {tm} {info} >> {f}" '.format(tm=curtime, info=msg, f=log_file)
        ret = subprocess.call(insertInfo, shell=True)
        return ret


def fork_proc():
        try:
                pid = os.fork()
                if pid > 0:
                        sys.exit(0)
        except OSError as e:
                sys.stderr.write("fork #1 failed:%d (%s)\n" % (e.errno, e.strerror))
                sys.exit(1)

        os.setsid()
        try:
                pid = os.fork()
                if pid > 0:
                        sys.exit(0)
        except OSError as e:
                sys.stderr.write("fork #2 failed: %d (%s)\n" % (e.errno, e.strerror))
                sys.exit(1)

        sys.stdout.flush()
        sys.stderr.flush()
        si = open('/dev/null', 'r')
        so = open('/dev/null', 'a+')
        if sys.version_info.major == 2:
                se = open('/dev/null', 'a+', 0)
        else:
                se = open('/dev/null', 'a+')
        os.dup2(si.fileno(), sys.stdin.fileno())
        os.dup2(so.fileno(), sys.stdout.fileno())
        os.dup2(se.fileno(), sys.stderr.fileno())


# 0 means dbscale runs ok
def check_dbscale_login():
        global login_retry_times
        global conf_file
        global recorded_pid
        global use_recorded_pid
        login_retry_times_local = login_retry_times
        checkCMD = "timeout 5 mysql -h{h} -p'{p}' -P{P} -u{u} --connect-timeout=3 -A -e 'dbscale show version'".format(
                h=host, p=password, P=port, u=user)
        while login_retry_times_local > 0:
                ret = check_disk_io()
                if ret == 124:
                        return 124
                else:
                        pro, output = my_subprocess.getstatusoutput(checkCMD)
                        if pro == 0:
                                recorded_pid = pid_in_file(file_pid)
                                use_recorded_pid = True
                                return 0
                        # pro = 124 or 1 dbscale abnormal retry max times will kill dbscale
                        else:
                                login_retry_times_local = login_retry_times_local - 1
                                if check_dbscale_pid(conf_file) != 0:
                                        return 2
                time.sleep(3)
        force_kill_dbscale()
        recorded_pid = ''
        use_recorded_pid = False
        return 1


def pid_in_file(file_name):
        global recorded_pid
        global use_recorded_pid
        if use_recorded_pid == True:
                return recorded_pid
        if os.path.isfile(file_name) == False:
                return -1
        with open(file_name) as f:
                dbscalePID = f.readline()
                return dbscalePID


# 0 means dbscale runs ok
def check_dbscale_pid(conf_file):
        dbscalePID = pid_in_file(file_pid)
        if dbscalePID == -1:
                return -1
        pid_path = "/proc/{pid}".format(pid=dbscalePID)
        pid_path = pid_path.strip('\n')
        if os.path.isdir(pid_path) == False:
                return -1
        checkCMD = "cat {path}/comm |grep -c dbscale".format(path=pid_path)
        pro = my_subprocess.getoutput(checkCMD)
        if pro == '1':
                tmp_dbscale_path = my_subprocess.getoutput("ls -alh {path}/cwd |cut -d '>' -f2".format(path=pid_path))
                act_dbscale_path = my_subprocess.getoutput("pwd")
                tmp_dbscale_path = tmp_dbscale_path.strip()
                act_dbscale_path = act_dbscale_path.strip()
                if tmp_dbscale_path == act_dbscale_path:
                        checkCMD = "cat {path}/cmdline |grep -c {file}".format(path=pid_path, file=conf_file)
                        pro = my_subprocess.getoutput(checkCMD)
                        if pro == '1':
                                return 0
        return 1


def check_disk_io():
        global recorded_pid
        global use_recorded_pid
        global check_io_retry_times
        max_retry_times = check_io_retry_times
        checkCMD = "timeout 4 mysql -h{h} -p'{p}' -P{P} -u{u} --connect-timeout=3 -A -e 'dbscale check disk io'".format(
                h=host, p=password, P=port, u=user)
        while max_retry_times > 0:
                pro = subprocess.call(checkCMD, shell=True)
                # pro = 124 timeout command timeout, connect succeed, check disk io failed
                if pro == 124:
                        max_retry_times = max_retry_times - 1
                # pro = 1 can not login dbscale, use daemon to check disk io
                elif pro == 1:
                        ret = check_disk_io_in_daemon()
                        if ret == 1:
                                return 1
                        else:
                                return 124
                else:
                        return 0
                time.sleep(1)
        force_kill_dbscale()
        recorded_pid = ''
        use_recorded_pid = False
        return 124


def check_disk_io_in_daemon():
        global recorded_pid
        global use_recorded_pid
        global check_io_retry_times
        max_retry_times = check_io_retry_times
        checkIO = "daemon check disk io"
        while max_retry_times > 0:
                ret = insertInfo(checkIO)
                if ret == 0:
                        return 1
                else:
                        max_retry_times = max_retry_times - 1
                time.sleep(1)
        force_kill_dbscale()
        printInfo("login faild, and check disk io in daemon failed, kill dbscale")
        recorded_pid = ''
        use_recorded_pid = False
        return 124


def force_kill_dbscale():
        clean_master_dbscale_zk_nodes()
        dbscalePID = pid_in_file(file_pid)
        os.system('kill -USR2 %s' % (dbscalePID))
        time.sleep(3)
        os.system('kill -KILL %s' % (dbscalePID))
        printInfo("WARNING: find dbscale abnormal, restart it")


## clean master dbscale master_select, online_nodes, nodes
def clean_master_dbscale_zk_nodes():
        global cur_dbscale_ip_port
        global zk_hosts
        if zk_hosts == "" or cur_dbscale_ip_port == "":
                return 0
        zk_hosts_array = zk_hosts.split(",")
        zk_hosts_array_len = len(zk_hosts_array)
        connected = False
        host_index = 0
        while (False == connected):
                zk = None
                try:
                        host = zk_hosts_array[host_index]
                        zk = KazooClient(hosts=host, timeout=3.0)
                        zk.start(timeout=3)
                        zk.add_auth("digest", "db:db")
                        connected = True
                except Exception as e:
                        err_info = "get error: %s, when connect zk in clean_master_dbscale_zk_nodes" % (e)
                        printInfo(err_info)
                        if zk is not None:
                                try:
                                        zk.stop()
                                        zk.close()
                                except Exception as e2:
                                        err_info = "get error: %s, when close zk in clean_master_dbscale_zk_nodes" % (e2)
                                        printInfo(err_info)
                        host_index = host_index + 1
                        host_index = host_index % zk_hosts_array_len
                        continue
        retry = 10
        current_dbscale_is_master = True
        while retry > 0 and True == current_dbscale_is_master:
                try:
                        if zk.exists("/dbscale/cluster/master_select"):
                                data, state = zk.get("/dbscale/cluster/master_select")
                                if cur_dbscale_ip_port in data:
                                        zk.delete("/dbscale/cluster/master_select", recursive=True)
                                else:
                                        current_dbscale_is_master = False
                        else:
                                current_dbscale_is_master = False
                except:
                        pass
                retry = retry - 1
        retry = 10
        current_dbscale_is_online = True
        while retry > 0 and True == current_dbscale_is_online:
                try:
                        if zk.exists("/dbscale/cluster/online_nodes"):
                                online_nodes = zk.get_children("/dbscale/cluster/online_nodes")
                                found_current_dbscale_online = None
                                for node in online_nodes:
                                        value, state = zk.get("/dbscale/cluster/online_nodes/%s" % node)
                                        if len(value) > 0 and cur_dbscale_ip_port in value:
                                                found_current_dbscale_online = node
                                                break
                                if found_current_dbscale_online != None:
                                        zk.delete("/dbscale/cluster/online_nodes/%s" % found_current_dbscale_online,
                                                  recursive=True)

                        current_dbscale_is_online = False
                except:
                        pass
                retry = retry - 1

        retry = 10
        nodes_path = "/dbscale/cluster/nodes"
        while retry > 0:
                retry = retry - 1
                try:
                        if zk.exists(nodes_path):
                                nodes = zk.get_children(nodes_path)
                                for node in nodes:
                                        if cur_dbscale_ip_port in node:
                                                zk.delete(nodes_path + "/" + node, recursive=True)
                                                break
                except:
                        pass
        try:
                zk.stop()
        except Exception as e:
                err_info = "get error: %s, when close zk in clean_master_dbscale_zk_nodes" % (e)
                printInfo(err_info)
                return 0
        return 1


# 0 means ok
def clean_zk_node(cur_dbscale_ip_port, zk_hosts):
        global log_file
        if zk_hosts == "":
                return 0
        try:
                zk = KazooClient(hosts=zk_hosts)
                zk.start()
                zk.add_auth("digest", "db:db")
                children = zk.get_children("/dbscale/cluster/nodes")
                for child in children:
                        if cur_dbscale_ip_port in child:
                                zk.delete("/dbscale/cluster/nodes/" + child, recursive=True)
                if zk.exists("/dbscale/cluster/start_config_lock"):
                        data, state = zk.get("/dbscale/cluster/start_config_lock")
                        if cur_dbscale_ip_port == data:
                                zk.delete("/dbscale/cluster/start_config_lock", recursive=True)
                if zk.exists("/dbscale/cluster/master_select"):
                        data, state = zk.get("/dbscale/cluster/master_select")
                        if cur_dbscale_ip_port == data:
                                zk.delete("/dbscale/cluster/master_select", recursive=True)
                zk.stop()
                return 0
        except:
                printInfo("get error when clean_zk_node")
                try:
                        zk.stop()
                except:
                        pass
                return 1
        return 0


def enable_lvs_dest():
        if is_lvs == 1:
                if os.system('ip addr | grep lo:0'):
                        os.system('sh /app/dbcluster/sgrdb/lvs/lvs_dest.sh stop')
                os.system('sh /app/dbcluster/sgrdb/lvs/lvs_dest.sh start')
        return 0


def parse_param(conf_file):
        global file_pid
        global user
        global password
        global port
        global host
        global cur_dbscale_ip_port
        global zk_hosts
        global log_file

        is_get_port = False
        is_get_user = False
        is_get_password = False
        is_get_host = False
        is_encode_password = False
        node_host_addr = "127.0.0.1"
        found_multiple_mode_conf = 0
        mul_mode = 0

        with open(conf_file, 'r') as f:
                lines = f.readlines()
                for line in lines:
                        if line.startswith("multiple-mode"):
                                mul_mode = line.split("=", 1)[-1].strip()
                                found_multiple_mode_conf = 1
                        if line.startswith("node-host-addr"):
                                node_host_addr = line.split("=", 1)[-1].strip()
                        if line.startswith("zookeeper-host"):
                                zk_hosts = line.split("=", 1)[-1].strip()
                        if line.startswith("pid-file"):
                                file_pid = './' + line.split("=", 1)[-1].strip()
                        if line.startswith("encode-password"):
                                encode_password = line.split("=", 1)[-1].strip()
                                if encode_password == "1":
                                        is_encode_password = True
                        if line.startswith("log-file"):
                                main_log_file = line.split("=", 1)[-1].strip()
                                pos = main_log_file.find('/')
                                if pos == -1:
                                        log_file = "./" + main_log_file + "_daemon"
                                else:
                                        log_file = main_log_file + "_daemon"
                        if is_get_user:
                                if line.startswith("user"):
                                        user = line.split("=", 1)[-1].strip()
                                        is_get_user = False
                        if is_get_port:
                                if line.startswith("port"):
                                        port = line.split("=", 1)[-1].strip()
                                        is_get_port = False
                        if is_get_password:
                                if line.startswith("password"):
                                        password = line.split("=", 1)[-1].strip()
                                        is_get_password = False
                        if is_get_host:
                                if line.startswith("bind-address"):
                                        host = line.split("=", 1)[-1].strip()
                                        is_get_host = False
                        if line.startswith("[driver"):
                                is_get_port = True
                                is_get_host = True
                        if line.startswith("[data-source"):
                                is_get_user = True
                                is_get_password = True

        if log_file.strip() == '':
                log_file = "./dbscale.log_daemon"
        if found_multiple_mode_conf == 0 or mul_mode == '0':
                cur_dbscale_ip_port = ""
                zk_hosts = ""
        else:
                cur_dbscale_ip_port = node_host_addr + ":" + port
        if is_encode_password == True:
                password = base64.b64decode(password).decode("utf-8")


def get_process_map(conf_file):
        process_dbscale = 'ps -ef|grep \"dbscale --config-file ' + conf_file + '$\" |grep -v grep |tr  -s  " "  |cut -d " " -f2'
        process_daemon = 'ps -ef|grep "dbscale_daemon.py$" |grep -v grep |tr  -s  " "  |cut -d " " -f2'
        if not is_default:
                process_daemon = 'ps -ef|grep \"dbscale_daemon.py.*-c.*' + conf_file + '$\" |grep -v grep |tr  -s  " "  |cut -d " " -f2'
        return_code, res1 = my_subprocess.getstatusoutput(process_dbscale)
        list1 = res1.split('\n')
        return_code, res2 = my_subprocess.getstatusoutput(process_daemon)
        list2 = res2.split('\n')
        mapping = {}
        for dbscale_pid in list1:
                proc_path = "/proc/" + dbscale_pid + "/cwd"
                return_code, dbscale_path = my_subprocess.getstatusoutput('ls -alh %s |cut -d ">" -f2 ' % (proc_path))
                match = False
                for daemon_pid in list2:
                        proc_path = "/proc/" + daemon_pid + "/cwd"
                        return_code, daemon_path = my_subprocess.getstatusoutput('ls -alh %s |cut -d ">" -f2 ' % (proc_path))
                        if daemon_path == dbscale_path:
                                mapping[dbscale_pid] = daemon_pid
                                match = True
                if match == False:
                        mapping[dbscale_pid] = 'nil'
        return mapping


def create_log_dir():
        global log_file
        log_path = log_file[0:log_file.rfind('/') + 1]
        os.system('mkdir -p %s' % (log_path))


def daemon_job(conf_file):
        global log_file
        while True:
                check_res = check_dbscale_pid(conf_file)
                if check_res == 0:
                        ret = check_dbscale_login()
                        if ret == 1:
                                printInfo("ERROR: login timeout")
                        if ret == 2:
                                printInfo("ERROR: dbscale shutdown")
                        if ret == 124:
                                printInfo("ERROR: check disk io abnormal")
                else:
                        mapping = get_process_map(conf_file)
                        find = False
                        unknown_dbscale_pids = []
                        for k in mapping.keys():
                                if mapping[k] == str(os.getpid()):
                                        find = True
                                if mapping[k] == 'nil':
                                        unknown_dbscale_pids.append(k)
                        if find == False:
                                for k in unknown_dbscale_pids:
                                        os.system('kill -9 %s ' % (k))
                                        time.sleep(1)
                                printInfo("ERROR: not find dbscale process start by this daemon, restart dbscale")
                                if clean_zk_node(cur_dbscale_ip_port, zk_hosts) == 0:
                                        os.system("./dbscale-service.sh start -c " + conf_file + " >> " + log_file + " 2>&1")
                                else:
                                        printInfo("ERROR: clean zk failed ,plz check zk")
                time.sleep(DEFAULT_CHECK_DBSCALE_ALIVE)
        return


def helper():
        print("-c arg (default=dbscale.conf)           specify the configure file path")
        print("-h [ --help ]                           Display this help and exit.")
        print("The daemon log file is stored in the same directory as the dbscale log file")
        print("The daemon log file name format: dbscale log file add '_daemon', such as dbscale.log_daemon")


def main():
        if len(sys.argv) >= 2:
                if sys.argv[1] == '--help' or sys.argv[1] == '-h':
                        helper()
                        return
                if sys.argv[1] != '-c':
                        print("Unrecognized option '%s'" % (sys.argv[1]))
                        print("Please run command -h or --help to get help")
                        return

        global is_default
        global log_file
        global conf_file

        enable_lvs_dest()

        if len(sys.argv) == 3:
                conf_file = sys.argv[2]
                is_default = False

        if os.path.isfile(conf_file) == False:
                print("ERROR: no such file or directory : %s " % (conf_file))
                sys.exit(1)
        parse_param(conf_file)
        if check_dbscale_pid(conf_file) == 0:
                print("dbscale process is occupied, it can be solved in some ways:")
                print("1. Empty file %s, there may be risks." % (file_pid))
                print("2. May be bad conf file, please specify pid_file in conf file.")
                sys.exit(1)
        fork_proc()
        create_log_dir()
        printInfo("daemon process start")
        daemon_job(conf_file)


if __name__ == '__main__':
        if sys.version_info.major == 2:
                import commands as my_subprocess
        elif sys.version_info.major == 3:
                import subprocess as my_subprocess

        main()


```


