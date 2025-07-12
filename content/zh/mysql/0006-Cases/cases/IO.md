---
title: io高
description: io高
date: 2023-10-12
weight: 60000


---

{{< alert >}}
最近一段时间线上某实例频繁报警CPU飙高，每次都捕获到同一种 SQL
{{< /alert >}}


### 一、环境信息

{{< note >}}

| 名称          | 配置       |
|:--------------|:-----------|
| Arch          | x64        |
| OS            | CentOS 8.5 |
| CPU           | 8C         |
| Memory        | 16G        |
| Space         | 200G       |
| MySQL Version | 8.0.26     |

{{< /note >}}



```bash
dnf -y install sysstat
```



### 查看磁盘所有IO负载
```bash
[root@dev log]# iostat -m -x 2
Linux 4.18.0-348.el8.x86_64 (dev) 	01/16/2024 	_x86_64_	(8 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           1.55    0.00    0.35    0.04    1.61   96.45

Device            r/s     w/s     rMB/s     wMB/s   rrqm/s   wrqm/s  %rrqm  %wrqm r_await w_await aqu-sz rareq-sz wareq-sz  svctm  %util
sda              1.24   10.78      0.09      0.28     0.02     0.52   1.73   4.61    4.58    1.07   0.02    77.73    26.56   0.49   0.59
scd0             0.00    0.00      0.00      0.00     0.00     0.00   0.00   0.00    1.11    0.00   0.00     0.17     0.00   2.33   0.00
scd1             0.00    0.00      0.00      0.00     0.00     0.00   0.00   0.00    5.35    0.00   0.00     2.06     0.00   5.81   0.00
dm-0             1.18   10.98      0.09      0.28     0.00     0.00   0.00   0.00    4.95    1.02   0.02    81.14    26.46   0.48   0.58
dm-1             0.01    0.03      0.00      0.00     0.00     0.00   0.00   0.00    2.12    3.74   0.00     4.78     4.00   0.50   0.00
```
目前有多块物理磁盘，sda磁盘的io压力较大

### 确定io高的磁盘
```bash
[root@dev log]# iostat -d /dev/sda -m -x 1
Linux 4.18.0-348.el8.x86_64 (dev) 	01/16/2024 	_x86_64_	(8 CPU)

Device            r/s     w/s     rMB/s     wMB/s   rrqm/s   wrqm/s  %rrqm  %wrqm r_await w_await aqu-sz rareq-sz wareq-sz  svctm  %util
sda              0.00 2228.00      0.00      6.00     0.00    10.00   0.00   0.45    0.00    0.38   0.84     0.00     2.76   0.47 105.50

[root@dev log]# iostat -d /dev/dm-0 -m -x 2
Linux 4.18.0-348.el8.x86_64 (dev) 	01/16/2024 	_x86_64_	(8 CPU)

Device            r/s     w/s     rMB/s     wMB/s   rrqm/s   wrqm/s  %rrqm  %wrqm r_await w_await aqu-sz rareq-sz wareq-sz  svctm  %util
dm-0             0.00 2308.00      0.00      8.91     0.00     0.00   0.00   0.00    0.00    0.29   0.67     0.00     3.95   0.39  91.10
```




### 确定进程

```bash
[root@dev log]# pidstat -d  1
Linux 4.18.0-348.el8.x86_64 (dev) 	01/16/2024 	_x86_64_	(8 CPU)

09:31:19 PM   UID       PID   kB_rd/s   kB_wr/s kB_ccwr/s iodelay  Command
09:31:20 PM     0    162650      0.00   5584.47    955.34       0  mysqld
09:31:20 PM     0    162902      0.00   1425.24      0.00       0  perf

```
> `09:31:20 PM     0    162650      0.00   5584.47    955.34       0  mysqld`
>
> 确定进程id：162650

### 确定线程
```bash
[root@dev log]# pidstat -dt -p 162650 10
Linux 4.18.0-348.el8.x86_64 (dev) 	01/16/2024 	_x86_64_	(8 CPU)

09:33:25 PM   UID      TGID       TID   kB_rd/s   kB_wr/s kB_ccwr/s iodelay  Command
09:33:35 PM     0    162650         -      0.00   5042.80    403.60       0  mysqld
09:33:35 PM     0         -    162650      0.00      0.00      0.00       0  |__mysqld
09:33:35 PM     0         -    162752      0.00      0.00      0.00       0  |__mysqld
09:33:35 PM     0         -    162753      0.00      0.00      0.00       0  |__mysqld
09:33:35 PM     0         -    162754      0.00      0.00      0.00       0  |__mysqld
09:33:35 PM     0         -    162756      0.00      0.00      0.00       0  |__mysqld
09:33:35 PM     0         -    162757      0.00      0.00      0.00       0  |__mysqld
09:33:35 PM     0         -    162758      0.00      0.00      0.00       0  |__mysqld
09:33:35 PM     0         -    162759      0.00      0.00      0.00       1  |__mysqld
09:33:35 PM     0         -    162760      0.00      0.00      0.00       1  |__mysqld
09:33:35 PM     0         -    162761      0.00      0.00      0.00       0  |__mysqld
09:33:35 PM     0         -    162762      0.00      0.00      0.00       1  |__mysqld
09:33:35 PM     0         -    162763      0.00    160.00      0.00       1  |__mysqld
09:33:35 PM     0         -    162764      0.00    134.40      0.00       0  |__mysqld
09:33:35 PM     0         -    162765      0.00    160.00      0.00       1  |__mysqld
09:33:35 PM     0         -    162766      0.00    185.60      0.00       0  |__mysqld
09:33:35 PM     0         -    162768      0.00      0.80      0.00       0  |__mysqld
09:33:35 PM     0         -    162769      0.00      0.00      0.00       0  |__mysqld
09:33:35 PM     0         -    162770      0.00      0.00      0.00     167  |__mysqld
09:33:35 PM     0         -    162772      0.00      0.00      0.00       0  |__mysqld
09:33:35 PM     0         -    162773      0.00   2891.60      0.00       0  |__mysqld
09:33:35 PM     0         -    162779      0.00      0.00      0.00       0  |__mysqld
09:33:35 PM     0         -    162780      0.00      0.00      0.00       0  |__mysqld
09:33:35 PM     0         -    162781      0.00      0.00      0.00       0  |__mysqld
09:33:35 PM     0         -    162782      0.00      0.00      0.00       0  |__mysqld
09:33:35 PM     0         -    162784      0.00      0.00      0.00       0  |__mysqld
09:33:35 PM     0         -    162785      0.00      0.00      0.00       0  |__mysqld
09:33:35 PM     0         -    162786      0.00      0.00      0.00       0  |__mysqld
09:33:35 PM     0         -    162791      0.00      0.00      0.00       0  |__mysqld
09:33:35 PM     0         -    162792      0.00      0.00      0.00       0  |__mysqld
09:33:35 PM     0         -    162793      0.00      0.00      0.00       0  |__mysqld
09:33:35 PM     0         -    162794      0.00      0.00      0.00       0  |__mysqld
09:33:35 PM     0         -    162795      0.00      0.00      0.00       0  |__mysqld
09:33:35 PM     0         -    162796      0.00      0.00      0.00       0  |__mysqld
09:33:35 PM     0         -    162797      0.00      0.00      0.00       0  |__mysqld
09:33:35 PM     0         -    162798      0.00      0.00      0.00       0  |__mysqld
09:33:35 PM     0         -    162799      0.00      0.00      0.00       0  |__mysqld
09:33:35 PM     0         -    162800      0.00      0.00      0.00       0  |__mysqld
09:33:35 PM     0         -    162805      0.00      0.00      0.00       0  |__mysqld
09:33:35 PM     0         -    162811      0.00    199.60     49.60       0  |__mysqld
09:33:35 PM     0         -    162880      0.00     25.60      0.00       0  |__mysqld
09:33:35 PM     0         -    162881      0.00      9.60      0.00       0  |__mysqld
09:33:35 PM     0         -    162882      0.00     90.40     46.80       0  |__mysqld
09:33:35 PM     0         -    162883      0.00      6.40      0.00       0  |__mysqld
09:33:35 PM     0         -    162884      0.00    183.20      2.00      28  |__mysqld
09:33:35 PM     0         -    162885      0.00     74.80      2.00       0  |__mysqld
09:33:35 PM     0         -    162886      0.00     58.40      8.40       0  |__mysqld
09:33:35 PM     0         -    162887      0.00     25.60      0.00       0  |__mysqld
09:33:35 PM     0         -    162888      0.00     71.60     49.60       0  |__mysqld
09:33:35 PM     0         -    162890      0.00    186.40     46.80       1  |__mysqld
09:33:35 PM     0         -    162892      0.00    231.20     49.60       1  |__mysqld
09:33:35 PM     0         -    162894      0.00    177.20     49.60       0  |__mysqld
09:33:35 PM     0         -    162896      0.00     80.80     49.60       0  |__mysqld
09:33:35 PM     0         -    162898      0.00     61.60     49.60       0  |__mysqld
09:33:35 PM     0         -    162899      0.00     28.80      0.00       0  |__mysqld

```
> `09:33:35 PM     0         -    162773      0.00   2891.60      0.00       0  |__mysqld`
>
> 确定线程id： 162773




### 追踪线程

```bash
perf trace -t 162773  -o /tmp/log.strace
```

1. 查看线程内容：
```bash
[root@dev tmp]# grep write log.strace |head -n 10
     0.026 ( 0.008 ms): pwrite64(fd: 6</root/local/mysql_8026/data/ib_logfile0>, buf: 0x7fff520dcc00, count: 1024, pos: 772653056) = 1024
     0.055 ( 0.003 ms): pwrite64(fd: 6</root/local/mysql_8026/data/ib_logfile0>, buf: 0x7fffe2080000, count: 512, pos: 772654080) = 512
     1.174 ( 0.016 ms): pwrite64(fd: 6</root/local/mysql_8026/data/ib_logfile0>, buf: 0x7fff520dd000, count: 1536, pos: 772654080) = 1536
     1.222 ( 0.008 ms): pwrite64(fd: 6</root/local/mysql_8026/data/ib_logfile0>, buf: 0x7fffe2080000, count: 512, pos: 772655616) = 512
     2.332 ( 0.010 ms): pwrite64(fd: 6</root/local/mysql_8026/data/ib_logfile0>, buf: 0x7fff520dd600, count: 1536, pos: 772655616) = 1536
     2.377 ( 0.007 ms): pwrite64(fd: 6</root/local/mysql_8026/data/ib_logfile0>, buf: 0x7fffe2080000, count: 512, pos: 772657152) = 512
     3.484 ( 0.011 ms): pwrite64(fd: 6</root/local/mysql_8026/data/ib_logfile0>, buf: 0x7fff520ddc00, count: 1536, pos: 772657152) = 1536
     3.518 ( 0.006 ms): pwrite64(fd: 6</root/local/mysql_8026/data/ib_logfile0>, buf: 0x7fffe2080000, count: 512, pos: 772658688) = 512
     4.623 ( 0.014 ms): pwrite64(fd: 6</root/local/mysql_8026/data/ib_logfile0>, buf: 0x7fff520de200, count: 1536, pos: 772658688) = 1536
     4.659 ( 0.006 ms): pwrite64(fd: 6</root/local/mysql_8026/data/ib_logfile0>, buf: 0x7fffe2080000, count: 512, pos: 772660224) = 512

```
> `pwrite64(fd: 6</root/local/mysql_8026/data/ib_logfile0>`
>
> 获取文件句柄：6

2. 查看这个文件句柄在干啥

```bash
[root@dev log]# lsof -p 162650|grep 6u
mysqld  162650 root    6uW  REG              253,0 1073741824 504525461 /root/local/mysql_8026/data/ib_logfile0
mysqld  162650 root   16u  IPv6             536881        0t0       TCP localhost:ca-audit-ds->localhost:55758 (ESTABLISHED)
mysqld  162650 root   26uW  REG              253,0      81920 656469634 /root/local/mysql_8026/data/#innodb_temp/temp_1.ibt
mysqld  162650 root   36u  IPv6             537938        0t0       TCP dev:ca-audit-ds->node77:52104 (ESTABLISHED)
mysqld  162650 root   46u  IPv6             537952        0t0       TCP dev:ca-audit-ds->node77:52116 (ESTABLISHED)
mysqld  162650 root   56u  IPv6             537993        0t0       TCP dev:ca-audit-ds->node77:52134 (ESTABLISHED)
mysqld  162650 root   66uW  REG              253,0   27262976 722729010 /root/local/mysql_8026/data/apple/sbtest6.ibd
mysqld  162650 root   76uW  REG              253,0   30408704 722729015 /root/local/mysql_8026/data/apple/sbtest2.ibd
mysqld  162650 root   86u   REG              253,0     393216 235871580 /root/local/mysql_8026/tmp/#235871580 (deleted)

```
> `mysqld  162650 root    6uW  REG              253,0 1073741824 504525461 /root/local/mysql_8026/data/ib_logfile0`
>
> 确定操作文件：/root/local/mysql_8026/data/ib_logfile0



### 获取MySQL线程号

```sql
mysql> select * from performance_schema.threads where thread_os_id=162773\G
*************************** 1. row ***************************
          THREAD_ID: 22
               NAME: thread/innodb/log_writer_thread
               TYPE: BACKGROUND
     PROCESSLIST_ID: NULL
   PROCESSLIST_USER: NULL
   PROCESSLIST_HOST: NULL
     PROCESSLIST_DB: NULL
PROCESSLIST_COMMAND: NULL
   PROCESSLIST_TIME: NULL
  PROCESSLIST_STATE: NULL
   PROCESSLIST_INFO: NULL
   PARENT_THREAD_ID: NULL
               ROLE: NULL
       INSTRUMENTED: YES
            HISTORY: YES
    CONNECTION_TYPE: NULL
       THREAD_OS_ID: 162773
     RESOURCE_GROUP: SYS_default
1 row in set (0.01 sec)

```
> `select * from performance_schema.threads where thread_os_id=162773\G`
>
> 确定该线程是MySQL系统后台log写线程：`thread/innodb/log_writer_thread`


```sql
mysql> select type,name,thread_id,thread_os_id,processlist_id,processlist_command,processlist_state from performance_schema.threads order by type,name,thread_id;
+------------+---------------------------------------------+-----------+--------------+----------------+---------------------+----------------------------+
| type       | name                                        | thread_id | thread_os_id | processlist_id | processlist_command | processlist_state          |
+------------+---------------------------------------------+-----------+--------------+----------------+---------------------+----------------------------+
| BACKGROUND | thread/innodb/buf_dump_thread               |        39 |       162791 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/buf_resize_thread             |        30 |       162782 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/clone_gtid_thread             |        40 |       162792 |           NULL | NULL                | waiting for handler commit |
| BACKGROUND | thread/innodb/dict_stats_thread             |        34 |       162785 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/fts_optimize_thread           |        35 |       162786 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/io_ibuf_thread                |         3 |       162752 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/io_log_thread                 |         4 |       162753 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/io_read_thread                |         5 |       162754 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/io_read_thread                |         6 |       162756 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/io_read_thread                |         7 |       162757 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/io_read_thread                |         8 |       162758 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/io_write_thread               |         9 |       162759 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/io_write_thread               |        10 |       162760 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/io_write_thread               |        11 |       162761 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/io_write_thread               |        12 |       162762 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/log_checkpointer_thread       |        18 |       162768 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/log_flush_notifier_thread     |        19 |       162769 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/log_flusher_thread            |        20 |       162770 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/log_write_notifier_thread     |        21 |       162772 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/log_writer_thread             |        22 |       162773 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/page_flush_coordinator_thread |        13 |       162763 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/page_flush_thread             |        14 |       162764 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/page_flush_thread             |        15 |       162765 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/page_flush_thread             |        16 |       162766 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/srv_error_monitor_thread      |        28 |       162780 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/srv_lock_timeout_thread       |        27 |       162779 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/srv_master_thread             |        33 |       162784 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/srv_monitor_thread            |        29 |       162781 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/srv_purge_thread              |        41 |       162793 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/srv_purge_thread              |        44 |       162793 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/srv_worker_thread             |        42 |       162794 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/srv_worker_thread             |        43 |       162795 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/srv_worker_thread             |        45 |       162795 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/srv_worker_thread             |        46 |       162796 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/srv_worker_thread             |        47 |       162794 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/innodb/srv_worker_thread             |        48 |       162796 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/sql/main                             |         1 |       162650 |           NULL | NULL                | NULL                       |
| BACKGROUND | thread/sql/signal_handler                   |        50 |       162798 |           NULL | NULL                | NULL                       |
| FOREGROUND | thread/sql/compress_gtid_table              |        51 |       162799 |              7 | Daemon              | Suspending                 |
| FOREGROUND | thread/sql/event_scheduler                  |        49 |       162797 |              5 | Daemon              | Waiting on empty queue     |
| FOREGROUND | thread/sql/one_connection                   |        52 |       162800 |              8 | Sleep               | NULL                       |
| FOREGROUND | thread/sql/one_connection                   |        53 |       162805 |              9 | Query               | executing                  |
| FOREGROUND | thread/sql/one_connection                   |        57 |       162811 |             13 | Query               | update                     |
| FOREGROUND | thread/sql/one_connection                   |        58 |       162880 |             14 | Query               | update                     |
| FOREGROUND | thread/sql/one_connection                   |        59 |       162881 |             15 | Query               | update                     |
| FOREGROUND | thread/sql/one_connection                   |        60 |       162882 |             16 | Query               | update                     |
| FOREGROUND | thread/sql/one_connection                   |        61 |       162883 |             17 | Query               | update                     |
| FOREGROUND | thread/sql/one_connection                   |        62 |       162884 |             18 | Query               | update                     |
| FOREGROUND | thread/sql/one_connection                   |        63 |       162885 |             19 | Query               | update                     |
| FOREGROUND | thread/sql/one_connection                   |        64 |       162886 |             20 | Query               | update                     |
| FOREGROUND | thread/sql/one_connection                   |        65 |       162887 |             21 | Query               | update                     |
| FOREGROUND | thread/sql/one_connection                   |        66 |       162888 |             22 | Query               | update                     |
| FOREGROUND | thread/sql/one_connection                   |        67 |       162890 |             23 | Query               | update                     |
| FOREGROUND | thread/sql/one_connection                   |        68 |       162892 |             24 | Query               | update                     |
| FOREGROUND | thread/sql/one_connection                   |        69 |       162894 |             25 | Query               | update                     |
| FOREGROUND | thread/sql/one_connection                   |        70 |       162896 |             27 | Query               | update                     |
| FOREGROUND | thread/sql/one_connection                   |        71 |       162898 |             26 | Query               | update                     |
| FOREGROUND | thread/sql/one_connection                   |        72 |       162899 |             28 | Query               | update                     |
+------------+---------------------------------------------+-----------+--------------+----------------+---------------------+----------------------------+
58 rows in set (0.00 sec)

```

