---
title: coredump
description: coredump
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

### 查看当前core的大小限制
```bash
[root@node85 ~]# ulimit -a|grep core
core file size          (blocks, -c) unlimited

```
### 调整core的限制为unlimited

```bash
[root@10-186-61-162 mysql]# ulimit -c unlimited
[root@10-186-61-162 mysql]# ulimit -a|grep core
core file size          (blocks, -c) unlimited
```

### /etc/security/limits.conf
```bash
ulimit -c unlimited

echo "* soft core unlimited" >> /etc/security/limits.conf

echo "* hard core unlimited" >> /etc/security/limits.conf
```


### /etc/sysctl.conf

```bash
sysctl -w "kernel.core_pattern=/data/coredump/core-%e-%u-%p-%s-%t" >>/etc/sysctl.conf
sysctl -w "kernel.core_uses_pid=1" >>/etc/sysctl.conf
sysctl -p (查看生效参数，验证设置是否生效)
```

> `cat /etc/sysctl.conf`
```bash
kernel.core_pattern=/data/coredump/core_%e_%p_%t
kernel.core_uses_pid=1
```

```bash
[root@node85 ~]# sysctl -a |grep core_pattern
kernel.core_pattern = /data/coredump/core-%e-%u-%p-%s-%t

```

###

```bash
[root@node85 ~]# ll /data/coredump/
total 471776
-rw------- 1 mysql mysql    8257536 Feb 28 11:05 core-clean_zookeeper-1000-11973-6-1709089527
-rw------- 1 mysql mysql 1081737216 Feb 28 11:04 core-dbscale-1000-10492-6-1709089444
-rw------- 1 mysql mysql 1081737216 Feb 28 11:05 core-fetch_config_fi-1000-12435-6-1709089545

```

## 给`mysql`配置`coredump`
### my.cnf
> 正常开启coredump
```yaml
[mysqld]
loose_innodb_buffer_pool_in_core_file	= 1
core-file
```
###  my.cnf
> 减少核心文件大小，以生成不带缓冲池页面的核心文件
```yaml
[mysqld]
core-file
innodb_buffer_pool_in_core_file = OFF
```

### 在RHEL7上可以对MySQL服务单独配置CORE限制
```bash
[root@10-186-61-162 system]# cat /etc/systemd/system/mysql_16315.service |grep Limit
LimitNOFILE = 65535
LimitCORE=infinity
```

### 查看mysqld进程

```sql
[root@dev tmp]# ps aux|grep mysqld
root        3220  4.0  4.0 4320180 655292 ?      tsl  22:18   0:05 /root/local/mysql_8026/bin/mysqld --defaults-file=/root/local/mysql_8026/conf/my.cnf --debug=debug --user=root
root        3353  0.0  0.0  12140  1148 pts/6    S+   22:21   0:00 grep --color=auto mysqld

```

## 模拟`coredump`

### kill
```bash
kill -sigsegv 3220
或
kill -SEGV 3220
或
kill -11 3220
```

### 查看coredump文件
```bash
[root@dev ~]# ll
total 5926312
drwxr-xr-x.  2 root root          46 Jan 11 22:28 0
-rw-------.  1 root root        1091 Dec 13  2022 anaconda-ks.cfg
drwxr-xr-x.  2 root root           6 Jan 23 16:35 bison
drwxr-xr-x. 14  505 games       4096 Jan 23 16:36 bison-2.6
-rw-r--r--.  1 root root     3068713 Jul 19  2012 bison-2.6.tar.gz
-rw-r--r--.  1 root root   110361537 Jan 11 21:49 boost_1_77_0.tar.bz2
drwxr-xr-x. 36 root root        4096 Jan 13 10:21 build_3355
drwxr-xr-x. 35 root root        4096 Jan 15 14:28 build_8026
drwxr-xr-x. 36 root root        4096 Jan 15 23:22 build_8032
drwxr-xr-x. 27 root root        4096 Jan 18 12:42 build_xtrabackup_8033
-rw-------.  1 root root  2106871808 Jan 27 22:21 core_mysqld_3220_1706365273
drwxr-xr-x. 21 root root        4096 Jan 23 16:23 dbscale
drwxrwxr-x.  9 root root        4096 Dec  9 10:30 icu-release-74-2
-rw-r--r--.  1 root root    65888936 Jan 12 21:31 icu-release-74-2.tar.gz
drwxr-xr-x.  7 root root         111 Jan 18 11:51 local
-rw-r--r--.  1 root root        4072 Jan 12 17:14 my.conf
drwxr-xr-x. 33 root root        4096 Jan 17 14:54 mysql-server
drwxr-xr-x. 32 root root        4096 Jan 14 20:19 mysql-server-8026
drwxr-xr-x. 33 root root        4096 Jan 14 19:27 mysql-server-8032
drwxr-xr-x. 32 root root        4096 Jan 12 21:26 mysql-server-mysql-8.0.35
-rw-r--r--.  1 root root  4833582236 Jan 14 12:39 mysql-server.tar.gz
drwxr-xr-x. 34 root root        4096 Jan 11 22:11 percona-xtrabackup.bak
drwxr-xr-x. 18 root root        4096 Jan 11 21:44 procps
-rwxr-xr-x.  1 root root        4832 Jan 12 14:03 sysbench.sh
drwxr-xr-x.  4 root root          90 Jan 14 20:49 targz
drwxr-xr-x.  2 root root          23 Jan 22 19:09 test
drwxr-xr-x.  4 root root         171 Jan 11 21:51 tmp
drwxr-xr-x. 34 root root        4096 Jan 18 11:52 xtrabackup_8033

```


## gdb

### gdb coredump
> gdb查看core文件堆栈信息
```bash
gdb /root/local/mysql_8026/bin/mysqld core_mysqld_3220_1706365273

```

#### 查看所有线程：info thread

```bash
(gdb)  info thread
  Id   Target Id                        Frame
* 1    Thread 0x7ffff7ee1940 (LWP 3220) handle_fatal_signal (sig=5) at /root/mysql-server-8026/sql/signal_handler.cc:75
  2    Thread 0x7fffa4c99700 (LWP 3275) 0x00007ffff7bbe7e8 in pthread_cond_timedwait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  3    Thread 0x7fffdeffd700 (LWP 3260) 0x00007ffff358452d in syscall () from /lib64/libc.so.6
  4    Thread 0x7fffb3fff700 (LWP 3269) 0x00007ffff7bbe3fc in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  5    Thread 0x7fffb949a700 (LWP 3267) 0x00007ffff7bbe7e8 in pthread_cond_timedwait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  6    Thread 0x7fffddffb700 (LWP 3257) 0x00007ffff358452d in syscall () from /lib64/libc.so.6
  7    Thread 0x7fffde7fc700 (LWP 3259) 0x00007ffff358452d in syscall () from /lib64/libc.so.6
  8    Thread 0x7fffdcff9700 (LWP 3261) 0x00007ffff358452d in syscall () from /lib64/libc.so.6
  9    Thread 0x7fffb37fe700 (LWP 3270) 0x00007ffff7bbe3fc in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  10   Thread 0x7fffa549a700 (LWP 3274) 0x00007ffff7bbe7e8 in pthread_cond_timedwait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  11   Thread 0x7fffc5132700 (LWP 3265) 0x00007ffff358452d in syscall () from /lib64/libc.so.6
  12   Thread 0x7fffb1ffc700 (LWP 3271) 0x00007ffff7bbe7e8 in pthread_cond_timedwait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  13   Thread 0x7fffc6935700 (LWP 3262) 0x00007ffff358452d in syscall () from /lib64/libc.so.6
  14   Thread 0x7fff92134700 (LWP 3280) 0x00007ffff7bbe7e8 in pthread_cond_timedwait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  15   Thread 0x7fffb8c99700 (LWP 3268) 0x00007ffff7bbe3fc in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  16   Thread 0x7fff91933700 (LWP 3303) 0x00007ffff7bbe3fc in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  17   Thread 0x7fff6b5cf700 (LWP 3312) 0x00007ffff7bbe3fc in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  18   Thread 0x7fff695cb700 (LWP 3318) 0x00007ffff7bbe3fc in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  19   Thread 0x7fffdd7fa700 (LWP 3258) 0x00007ffff358452d in syscall () from /lib64/libc.so.6
  20   Thread 0x7fffe4575700 (LWP 3317) 0x00007ffff34c514c in sigtimedwait () from /lib64/libc.so.6
  21   Thread 0x7fffe45bd700 (LWP 3316) 0x00007ffff7bbe3fc in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  22   Thread 0x7fff6a5cd700 (LWP 3314) 0x00007ffff7bbe3fc in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  23   Thread 0x7fff69dcc700 (LWP 3315) 0x00007ffff7bbe3fc in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  24   Thread 0x7fff6adce700 (LWP 3313) 0x00007ffff7bbe3fc in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  25   Thread 0x7fff6bdd0700 (LWP 3311) 0x00007ffff7bbe7e8 in pthread_cond_timedwait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  26   Thread 0x7fffe69e2700 (LWP 3310) 0x00007ffff7bbe3fc in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  27   Thread 0x7fff84c99700 (LWP 3306) 0x00007ffff7bbe7e8 in pthread_cond_timedwait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  28   Thread 0x7fff8549a700 (LWP 3305) 0x00007ffff7bbe7e8 in pthread_cond_timedwait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  29   Thread 0x7fff90931700 (LWP 3304) 0x00007ffff7bc20b0 in nanosleep () from /lib64/libpthread.so.0
  30   Thread 0x7fff92935700 (LWP 3282) 0x00007ffff7bbe7e8 in pthread_cond_timedwait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  31   Thread 0x7fff91132700 (LWP 3281) 0x00007ffff7bbe7e8 in pthread_cond_timedwait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  32   Thread 0x7fffb0ffa700 (LWP 3273) 0x00007ffff7bbe7e8 in pthread_cond_timedwait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  33   Thread 0x7fffc4931700 (LWP 3266) 0x00007ffff358452d in syscall () from /lib64/libc.so.6
  34   Thread 0x7fffc5933700 (LWP 3264) 0x00007ffff358452d in syscall () from /lib64/libc.so.6
  35   Thread 0x7fffc6134700 (LWP 3263) 0x00007ffff358452d in syscall () from /lib64/libc.so.6
  36   Thread 0x7fffb17fb700 (LWP 3272) 0x00007ffff7bbe7e8 in pthread_cond_timedwait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
(gdb) info threads
  Id   Target Id                        Frame
  1    Thread 0x7ffff7ee1940 (LWP 3220) handle_fatal_signal (sig=5) at /root/mysql-server-8026/sql/signal_handler.cc:75
  2    Thread 0x7fffa4c99700 (LWP 3275) 0x00007ffff7bbe7e8 in pthread_cond_timedwait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  3    Thread 0x7fffdeffd700 (LWP 3260) 0x00007ffff358452d in syscall () from /lib64/libc.so.6
  4    Thread 0x7fffb3fff700 (LWP 3269) 0x00007ffff7bbe3fc in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  5    Thread 0x7fffb949a700 (LWP 3267) 0x00007ffff7bbe7e8 in pthread_cond_timedwait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  6    Thread 0x7fffddffb700 (LWP 3257) 0x00007ffff358452d in syscall () from /lib64/libc.so.6
  7    Thread 0x7fffde7fc700 (LWP 3259) 0x00007ffff358452d in syscall () from /lib64/libc.so.6
  8    Thread 0x7fffdcff9700 (LWP 3261) 0x00007ffff358452d in syscall () from /lib64/libc.so.6
  9    Thread 0x7fffb37fe700 (LWP 3270) 0x00007ffff7bbe3fc in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  10   Thread 0x7fffa549a700 (LWP 3274) 0x00007ffff7bbe7e8 in pthread_cond_timedwait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  11   Thread 0x7fffc5132700 (LWP 3265) 0x00007ffff358452d in syscall () from /lib64/libc.so.6
  12   Thread 0x7fffb1ffc700 (LWP 3271) 0x00007ffff7bbe7e8 in pthread_cond_timedwait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  13   Thread 0x7fffc6935700 (LWP 3262) 0x00007ffff358452d in syscall () from /lib64/libc.so.6
  14   Thread 0x7fff92134700 (LWP 3280) 0x00007ffff7bbe7e8 in pthread_cond_timedwait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  15   Thread 0x7fffb8c99700 (LWP 3268) 0x00007ffff7bbe3fc in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  16   Thread 0x7fff91933700 (LWP 3303) 0x00007ffff7bbe3fc in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  17   Thread 0x7fff6b5cf700 (LWP 3312) 0x00007ffff7bbe3fc in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  18   Thread 0x7fff695cb700 (LWP 3318) 0x00007ffff7bbe3fc in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  19   Thread 0x7fffdd7fa700 (LWP 3258) 0x00007ffff358452d in syscall () from /lib64/libc.so.6
* 20   Thread 0x7fffe4575700 (LWP 3317) 0x00007ffff34c514c in sigtimedwait () from /lib64/libc.so.6
  21   Thread 0x7fffe45bd700 (LWP 3316) 0x00007ffff7bbe3fc in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  22   Thread 0x7fff6a5cd700 (LWP 3314) 0x00007ffff7bbe3fc in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  23   Thread 0x7fff69dcc700 (LWP 3315) 0x00007ffff7bbe3fc in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  24   Thread 0x7fff6adce700 (LWP 3313) 0x00007ffff7bbe3fc in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  25   Thread 0x7fff6bdd0700 (LWP 3311) 0x00007ffff7bbe7e8 in pthread_cond_timedwait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  26   Thread 0x7fffe69e2700 (LWP 3310) 0x00007ffff7bbe3fc in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  27   Thread 0x7fff84c99700 (LWP 3306) 0x00007ffff7bbe7e8 in pthread_cond_timedwait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  28   Thread 0x7fff8549a700 (LWP 3305) 0x00007ffff7bbe7e8 in pthread_cond_timedwait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  29   Thread 0x7fff90931700 (LWP 3304) 0x00007ffff7bc20b0 in nanosleep () from /lib64/libpthread.so.0
  30   Thread 0x7fff92935700 (LWP 3282) 0x00007ffff7bbe7e8 in pthread_cond_timedwait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  31   Thread 0x7fff91132700 (LWP 3281) 0x00007ffff7bbe7e8 in pthread_cond_timedwait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  32   Thread 0x7fffb0ffa700 (LWP 3273) 0x00007ffff7bbe7e8 in pthread_cond_timedwait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
  33   Thread 0x7fffc4931700 (LWP 3266) 0x00007ffff358452d in syscall () from /lib64/libc.so.6
  34   Thread 0x7fffc5933700 (LWP 3264) 0x00007ffff358452d in syscall () from /lib64/libc.so.6
  35   Thread 0x7fffc6134700 (LWP 3263) 0x00007ffff358452d in syscall () from /lib64/libc.so.6
  36   Thread 0x7fffb17fb700 (LWP 3272) 0x00007ffff7bbe7e8 in pthread_cond_timedwait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0
(gdb)

```
#### 切换到指定线程：thread 线程编号
```bash
(gdb) thread 1
[Switching to thread 1 (Thread 0x7ffff7ee1940 (LWP 3220))]
#0  handle_fatal_signal (sig=5) at /root/mysql-server-8026/sql/signal_handler.cc:75
75	extern "C" void handle_fatal_signal(int sig) {
(gdb) bt
#0  handle_fatal_signal (sig=5) at /root/mysql-server-8026/sql/signal_handler.cc:75
#1  <signal handler called>
#2  0x00007ffff357ea41 in poll () from /lib64/libc.so.6
#3  0x0000000003b5e5b7 in Mysqld_socket_listener::listen_for_connection_event (this=0x7fffe35d4060) at /root/mysql-server-8026/sql/conn_handler/socket_connection.cc:1342
#4  0x00000000037cd192 in Connection_acceptor<Mysqld_socket_listener>::connection_event_loop (this=0xacb2f00) at /root/mysql-server-8026/sql/conn_handler/connection_acceptor.h:64
#5  0x00000000037c061b in mysqld_main (argc=94, argv=0x8cbbed8) at /root/mysql-server-8026/sql/mysqld.cc:7616
#6  0x00000000037ae966 in main (argc=4, argv=0x7fffffffd198) at /root/mysql-server-8026/sql/main.cc:25


```

#### 打印当前线程堆栈信息：bt/where/backtrace
```bash
(gdb) thread 1
[Switching to thread 1 (Thread 0x7ffff7ee1940 (LWP 3220))]
#0  handle_fatal_signal (sig=5) at /root/mysql-server-8026/sql/signal_handler.cc:75
75	extern "C" void handle_fatal_signal(int sig) {
(gdb) bt
#0  handle_fatal_signal (sig=5) at /root/mysql-server-8026/sql/signal_handler.cc:75
#1  <signal handler called>
#2  0x00007ffff357ea41 in poll () from /lib64/libc.so.6
#3  0x0000000003b5e5b7 in Mysqld_socket_listener::listen_for_connection_event (this=0x7fffe35d4060) at /root/mysql-server-8026/sql/conn_handler/socket_connection.cc:1342
#4  0x00000000037cd192 in Connection_acceptor<Mysqld_socket_listener>::connection_event_loop (this=0xacb2f00) at /root/mysql-server-8026/sql/conn_handler/connection_acceptor.h:64
#5  0x00000000037c061b in mysqld_main (argc=94, argv=0x8cbbed8) at /root/mysql-server-8026/sql/mysqld.cc:7616
#6  0x00000000037ae966 in main (argc=4, argv=0x7fffffffd198) at /root/mysql-server-8026/sql/main.cc:25


```



#### 调试指定栈帧：frame/f 栈帧编号
> 切换到指定函数
```bash
(gdb) bt
#0  0x00007fc02529b9d5 in pthread_kill () from /lib64/libpthread.so.0
#1  0x00000000050ae821 in my_write_core (sig=11) at /root/mysql-server-8026/mysys/stacktrace.cc:308
#2  0x0000000003b6fd80 in handle_fatal_signal (sig=11) at /root/mysql-server-8026/sql/signal_handler.cc:171
#3  <signal handler called>
#4  0x00007fc020c5aa41 in poll () from /lib64/libc.so.6
#5  0x0000000003b5e5b7 in Mysqld_socket_listener::listen_for_connection_event (this=0x7fc00fd14a80) at /root/mysql-server-8026/sql/conn_handler/socket_connection.cc:1342
#6  0x00000000037cd192 in Connection_acceptor<Mysqld_socket_listener>::connection_event_loop (this=0xae0be80) at /root/mysql-server-8026/sql/conn_handler/connection_acceptor.h:64
#7  0x00000000037c061b in mysqld_main (argc=94, argv=0x8cf8ed8) at /root/mysql-server-8026/sql/mysqld.cc:7616
#8  0x00000000037ae966 in main (argc=4, argv=0x7fffb3f163d8) at /root/mysql-server-8026/sql/main.cc:25
(gdb)  frame 8
#8  0x00000000037ae966 in main (argc=4, argv=0x7fffb3f163d8) at /root/mysql-server-8026/sql/main.cc:25
25	int main(int argc, char **argv) { return mysqld_main(argc, argv); }
(gdb)

```

#### up

```bash
(gdb) bt
#0  0x00007fc02529b9d5 in pthread_kill () from /lib64/libpthread.so.0
#1  0x00000000050ae821 in my_write_core (sig=11) at /root/mysql-server-8026/mysys/stacktrace.cc:308
#2  0x0000000003b6fd80 in handle_fatal_signal (sig=11) at /root/mysql-server-8026/sql/signal_handler.cc:171
#3  <signal handler called>
#4  0x00007fc020c5aa41 in poll () from /lib64/libc.so.6
#5  0x0000000003b5e5b7 in Mysqld_socket_listener::listen_for_connection_event (this=0x7fc00fd14a80) at /root/mysql-server-8026/sql/conn_handler/socket_connection.cc:1342
#6  0x00000000037cd192 in Connection_acceptor<Mysqld_socket_listener>::connection_event_loop (this=0xae0be80) at /root/mysql-server-8026/sql/conn_handler/connection_acceptor.h:64
#7  0x00000000037c061b in mysqld_main (argc=94, argv=0x8cf8ed8) at /root/mysql-server-8026/sql/mysqld.cc:7616
#8  0x00000000037ae966 in main (argc=4, argv=0x7fffb3f163d8) at /root/mysql-server-8026/sql/main.cc:25
(gdb) frame 7
#7  0x00000000037c061b in mysqld_main (argc=94, argv=0x8cf8ed8) at /root/mysql-server-8026/sql/mysqld.cc:7616
7616	  mysqld_socket_acceptor->connection_event_loop();
(gdb) up 1
#8  0x00000000037ae966 in main (argc=4, argv=0x7fffb3f163d8) at /root/mysql-server-8026/sql/main.cc:25
25	int main(int argc, char **argv) { return mysqld_main(argc, argv); }
(gdb) down 2
#6  0x00000000037cd192 in Connection_acceptor<Mysqld_socket_listener>::connection_event_loop (this=0xae0be80) at /root/mysql-server-8026/sql/conn_handler/connection_acceptor.h:64
64	      Channel_info *channel_info = m_listener->listen_for_connection_event();
(gdb)

```


#### down
```bash
(gdb) bt
#0  0x00007fc02529b9d5 in pthread_kill () from /lib64/libpthread.so.0
#1  0x00000000050ae821 in my_write_core (sig=11) at /root/mysql-server-8026/mysys/stacktrace.cc:308
#2  0x0000000003b6fd80 in handle_fatal_signal (sig=11) at /root/mysql-server-8026/sql/signal_handler.cc:171
#3  <signal handler called>
#4  0x00007fc020c5aa41 in poll () from /lib64/libc.so.6
#5  0x0000000003b5e5b7 in Mysqld_socket_listener::listen_for_connection_event (this=0x7fc00fd14a80) at /root/mysql-server-8026/sql/conn_handler/socket_connection.cc:1342
#6  0x00000000037cd192 in Connection_acceptor<Mysqld_socket_listener>::connection_event_loop (this=0xae0be80) at /root/mysql-server-8026/sql/conn_handler/connection_acceptor.h:64
#7  0x00000000037c061b in mysqld_main (argc=94, argv=0x8cf8ed8) at /root/mysql-server-8026/sql/mysqld.cc:7616
#8  0x00000000037ae966 in main (argc=4, argv=0x7fffb3f163d8) at /root/mysql-server-8026/sql/main.cc:25
(gdb) frame 7
#7  0x00000000037c061b in mysqld_main (argc=94, argv=0x8cf8ed8) at /root/mysql-server-8026/sql/mysqld.cc:7616
7616	  mysqld_socket_acceptor->connection_event_loop();
(gdb) up 1
#8  0x00000000037ae966 in main (argc=4, argv=0x7fffb3f163d8) at /root/mysql-server-8026/sql/main.cc:25
25	int main(int argc, char **argv) { return mysqld_main(argc, argv); }
(gdb) down 2
#6  0x00000000037cd192 in Connection_acceptor<Mysqld_socket_listener>::connection_event_loop (this=0xae0be80) at /root/mysql-server-8026/sql/conn_handler/connection_acceptor.h:64
64	      Channel_info *channel_info = m_listener->listen_for_connection_event();
(gdb)

```



#### info local

```bash
(gdb) f 5
#5  0x0000000003b5e5b7 in Mysqld_socket_listener::listen_for_connection_event (this=0x7fc00fd14a80) at /root/mysql-server-8026/sql/conn_handler/socket_connection.cc:1342
1342	  int retval = poll(&m_poll_info.m_fds[0], m_socket_vector.size(), -1);
(gdb) info local
retval = 32767
__FUNCTION__ = "listen_for_connection_event"
listen_socket = 0x50000ffff
__PRETTY_FUNCTION__ = "Channel_info* Mysqld_socket_listener::listen_for_connection_event()"
connect_sock = {fd = -1276026416, m_psi = 0x7fffb3f15a40}
network_namespace_for_listening_socket = {static npos = 18446744073709551615, _M_dataplus = {<std::allocator<char>> = {<__gnu_cxx::new_allocator<char>> = {<No data fields>}, <No data fields>}, _M_p = 0x8cef200 "\336z\361\263\377\177"},
  _M_string_length = 8589934593, {_M_local_buf = " Z\361\263\377\177\000\000#\220|\003\000\000\000", _M_allocated_capacity = 140736212326944}}
channel_info = 0x37c8ddc <connection_events_loop_aborted()+30>
(gdb)

```

#### 打印变量值
> p

```
p



```



