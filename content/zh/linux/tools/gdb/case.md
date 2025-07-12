---
title: case
description: case
date: 2023-11-17
weight: 30000


---

{{< alert >}}
**pkg-config**：生成链接参数
{{< /alert >}}


### xxxx.h: No such file or directory
> 头文件找不到
```bash

```

### undefined reference to 'xxxxx'
首先这是链接错误，不是编译错误，也就是说如果只有这个错误，说明你的程序源码本身没有问题，是你用编译器编译时参数用得不对，你没有指定链接程序要用到得库，比如你的程序里用到了一些数学函数，那么你就要在编译
参数里指定程序要链接数学库，方法是在编译命令行里加入-lm。
```bash
[root@gip tmp]# pkg-config ncurses --libs --cflags
 -lncurses
[root@gip tmp]# pkg-config openssl --libs --cflags
 -lssl -lcrypto

```

## 创建相关路径
```bash
2024-08-19T10:06:09 [root@node172.17.140.146 /root] $ ll
total 5430696
-rw------- 1 root  root        2789 Mar 10  2023 anaconda-ks.cfg
-rw------- 1 mysql mysql 5423366144 Aug 19 03:39 core-dbscale-1000-25-6-1724009960
-rw-r--r-- 1 root  root    26964849 Aug 19 10:01 core-dbscale-1000-25-6-1724009960.tar.gz
drwxr-xr-x 9 54450 54457       4096 Aug 14  2023 dbscale
-rwxr-xr-x 1 root  root   110689375 Aug 19 10:02 GreatDBRouter-6.0.0.6056-RC-4-924f4d6b-Linux-glibc2.28-aarch64.tar.gz
-rw-r--r-- 1 root  root           0 Mar 10  2023 initial-setup-ks.cfg
2024-08-19T10:06:12 [root@node172.17.140.146 /root] $ mkdir /greatdb
2024-08-19T10:06:25 [root@node172.17.140.146 /root] $ mv dbscale /greatdb/
2024-08-19T10:06:32 [root@node172.17.140.146 /root] $ gdb /greatdb/dbscale/dbscale -c core-dbscale-1000-25-6-1724009960
```




## gdb

`gdb /greatdb/dbscale/dbscale -c core-dbscale-1000-25-6-1724009960`
```bash
GNU gdb (GDB) EulerOS 8.3.1-11.ky10
Copyright (C) 2019 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "aarch64-kylin-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from /greatdb/dbscale/dbscale...
[New LWP 1584160]
[New LWP 25]
[New LWP 32]
[New LWP 37]
[New LWP 39]
[New LWP 33]
[New LWP 40]
[New LWP 46]
[New LWP 48]
[New LWP 42]
[New LWP 44]
[New LWP 62]
[New LWP 30]
[New LWP 60]
[New LWP 43]
[New LWP 64]
[New LWP 38]
[New LWP 65]
[New LWP 57]
[New LWP 53]
[New LWP 41]
[New LWP 71]
[New LWP 63]
[New LWP 50]
[New LWP 45]
[New LWP 66]
[New LWP 1577323]
[New LWP 61]
[New LWP 47]
[New LWP 77]
[New LWP 72]
[New LWP 52]
[New LWP 56]
[New LWP 34]
[New LWP 51]
[New LWP 68]
[New LWP 76]
[New LWP 69]
[New LWP 54]
[New LWP 67]
[New LWP 75]
[New LWP 93]
[New LWP 9726]
[New LWP 70]
[New LWP 73]
[New LWP 85]
[New LWP 129]
[New LWP 4487]
[New LWP 81]
[New LWP 55]
[New LWP 82]
[New LWP 128]
[New LWP 87]
[New LWP 83]
[New LWP 78]
[New LWP 95]
[New LWP 36]
[New LWP 74]
[New LWP 698437]
[New LWP 10600]
[New LWP 49]
[New LWP 58]
[New LWP 115]
[New LWP 9461]
[New LWP 4481]
[New LWP 786946]
[New LWP 787061]
[New LWP 92]
[New LWP 122]
[New LWP 132]
[New LWP 786945]
[New LWP 80]
[New LWP 103]
[New LWP 105]
[New LWP 119]
[New LWP 120]
[New LWP 109]
[New LWP 4738]
[New LWP 135]
[New LWP 131]
[New LWP 123]
[New LWP 104]
[New LWP 91]
[New LWP 111]
[New LWP 106]
[New LWP 786879]
[New LWP 786899]
[New LWP 35]
[New LWP 97]
[New LWP 787069]
[New LWP 90]
[New LWP 79]
[New LWP 786883]
[New LWP 4482]
[New LWP 94]
[New LWP 229843]
[New LWP 130]
[New LWP 787064]
[New LWP 230225]
[New LWP 86]
[New LWP 110]
[New LWP 98]
[New LWP 108]
[New LWP 9717]
[New LWP 88]
[New LWP 1582837]
[New LWP 27048]
[New LWP 787073]
[New LWP 786886]
[New LWP 786905]
[New LWP 126]
[New LWP 117]
[New LWP 230221]
[New LWP 4438]
[New LWP 4743]
[New LWP 114]
[New LWP 230226]
[New LWP 4745]
[New LWP 787060]
[New LWP 96]
[New LWP 230208]
[New LWP 84]
[New LWP 4757]
[New LWP 22372]
[New LWP 101]
[New LWP 786903]
[New LWP 4740]
[New LWP 143708]
[New LWP 787056]
[New LWP 786895]
[New LWP 786882]
[New LWP 786874]
[New LWP 787068]
[New LWP 127]
[New LWP 229933]
[New LWP 786939]
[New LWP 112]
[New LWP 787040]
[New LWP 107]
[New LWP 787067]
[New LWP 116]
[New LWP 134]
[New LWP 121]
[New LWP 73875]
[New LWP 113]
[New LWP 168016]
[New LWP 787071]
[New LWP 166582]
[New LWP 118]
[New LWP 786875]
[New LWP 1576558]
[New LWP 1576559]
[New LWP 124]
[New LWP 787046]
[New LWP 787059]
[New LWP 786870]
[New LWP 787054]
[New LWP 169045]
[New LWP 4540]
[New LWP 787072]
[New LWP 786934]
[New LWP 4759]
[New LWP 229921]
[New LWP 786863]
[New LWP 311747]
[New LWP 22373]
[New LWP 786876]
[New LWP 83974]
[New LWP 786881]
[New LWP 4480]
[New LWP 9714]
[New LWP 787052]
[New LWP 787748]
[New LWP 786490]
[New LWP 786891]
[New LWP 786869]
[New LWP 786915]
[New LWP 89]
[New LWP 787036]
[New LWP 9485]
[New LWP 168788]
[New LWP 786938]
[New LWP 25929]
[New LWP 219970]
[New LWP 787752]
[New LWP 787746]
[New LWP 141]
[New LWP 9715]
[New LWP 787045]
[New LWP 100]
[New LWP 229915]
[New LWP 230674]
[New LWP 786894]
[New LWP 786901]
[New LWP 9710]
[New LWP 787063]
[New LWP 4444]
[New LWP 786491]
[New LWP 41280]
[New LWP 786916]
[New LWP 787074]
[New LWP 786914]
[New LWP 133]
[New LWP 134036]
[New LWP 786466]
[New LWP 4794]
[New LWP 786950]
[New LWP 59]
[New LWP 9731]
[New LWP 786956]
[New LWP 230211]
[New LWP 786912]
[New LWP 28674]
[New LWP 134035]
[New LWP 4741]
[New LWP 786904]
[New LWP 786960]
[New LWP 787062]
[New LWP 230207]
[New LWP 786892]
[New LWP 786928]
[New LWP 229918]
[New LWP 786933]
[New LWP 9463]
[New LWP 229931]
[New LWP 786955]
[New LWP 92714]
[New LWP 9713]
[New LWP 786932]
[New LWP 786898]
[New LWP 786910]
[New LWP 134034]
[New LWP 47550]
[New LWP 615500]
[New LWP 787075]
[New LWP 169044]
[New LWP 229923]
[New LWP 787044]
[New LWP 9711]
[New LWP 787049]
[New LWP 4486]
[New LWP 84232]
[New LWP 786937]
[New LWP 9734]
[New LWP 229914]
[New LWP 787048]
[New LWP 9462]
[New LWP 229919]
[New LWP 787051]
[New LWP 786996]
[New LWP 786884]
[New LWP 102]
[New LWP 9465]
[New LWP 4796]
[New LWP 787032]
[New LWP 786488]
[New LWP 786936]
[New LWP 786909]
[New LWP 76327]
[New LWP 4435]
[New LWP 786949]
[New LWP 6090]
[New LWP 786873]
[New LWP 133129]
[New LWP 786897]
[New LWP 4744]
[New LWP 219971]
[New LWP 229824]
[New LWP 4756]
[New LWP 786495]
[New LWP 76568]
[New LWP 230209]
[New LWP 4539]
[New LWP 229932]
[New LWP 143]
[New LWP 6091]
[New LWP 229822]
[New LWP 786941]
[New LWP 4749]
[New LWP 229929]
[New LWP 786944]
[New LWP 786925]
[New LWP 4748]
[New LWP 786469]
[New LWP 55819]
[New LWP 787053]
[New LWP 787050]
[New LWP 1581729]
[New LWP 1577346]
[New LWP 786940]
[New LWP 9771]
[New LWP 786497]
[New LWP 787037]
[New LWP 230224]
[New LWP 786907]
[New LWP 76567]
[New LWP 786900]
[New LWP 786943]
[New LWP 4755]
[New LWP 1584159]
[New LWP 84231]
[New LWP 142]
[New LWP 786862]
[New LWP 786952]
[New LWP 786913]
[New LWP 787749]
[New LWP 786872]
[New LWP 786920]
[New LWP 229920]
[New LWP 229823]
[New LWP 786887]
[New LWP 786923]
[New LWP 136]
[New LWP 1576561]
[New LWP 9722]
[New LWP 787070]
[New LWP 23333]
[New LWP 230534]
[New LWP 1576560]
[New LWP 786467]
[New LWP 230673]
[New LWP 229925]
[New LWP 781354]
[New LWP 787751]
[New LWP 786948]
[New LWP 786947]
[New LWP 787043]
[New LWP 9712]
[New LWP 229928]
[New LWP 9719]
[New LWP 1581730]
[New LWP 230216]
[New LWP 4437]
[New LWP 9773]
[New LWP 786926]
[New LWP 31]
[New LWP 25928]
[New LWP 125]
[New LWP 9708]
[New LWP 230212]
[New LWP 787035]
[New LWP 230217]
[New LWP 167249]
[New LWP 786935]
[New LWP 786908]
[New LWP 4754]
[New LWP 230222]
[New LWP 786583]
[New LWP 786889]
[New LWP 4731]
[New LWP 9727]
[New LWP 4737]
[New LWP 9730]
[New LWP 786942]
[New LWP 50423]
[New LWP 4758]
[New LWP 229926]
[New LWP 786468]
[New LWP 787042]
[New LWP 1577324]
[New LWP 1578940]
[New LWP 4733]
[New LWP 1583627]
[New LWP 11423]
[New LWP 9709]
[New LWP 5899]
[New LWP 4751]
[New LWP 4484]
[New LWP 154558]
[New LWP 229916]
[New LWP 4445]
[New LWP 786880]
[New LWP 9721]
[New LWP 4436]
[New LWP 787039]
[New LWP 9716]
[New LWP 4440]
[New LWP 164107]
[New LWP 786487]
[New LWP 787034]
[New LWP 229930]
[New LWP 229861]
[New LWP 1581728]
[New LWP 786951]
[New LWP 786871]
[New LWP 786888]
[New LWP 786954]
[New LWP 4752]
[New LWP 230213]
[New LWP 786927]
[New LWP 787057]
[New LWP 229825]
[New LWP 9729]
[New LWP 229917]
[New LWP 1581953]
[New LWP 786489]
[New LWP 4746]
[New LWP 786918]
[New LWP 4735]
[New LWP 786896]
[New LWP 73910]
[New LWP 4441]
[New LWP 787750]
[New LWP 1577327]
[New LWP 4439]
[New LWP 9723]
[New LWP 168752]
[New LWP 9728]
[New LWP 786911]
[New LWP 786493]
[New LWP 99]
[New LWP 786492]
[New LWP 169046]
[New LWP 4742]
[New LWP 786917]
[New LWP 787033]
[New LWP 4696]
[New LWP 55818]
[New LWP 787747]
[New LWP 230210]
[New LWP 1576754]
[New LWP 4750]
[New LWP 786878]
[New LWP 4734]
[New LWP 4442]
[New LWP 83975]
[New LWP 786867]
[New LWP 1577345]
[New LWP 5985]
[New LWP 168017]
[New LWP 786843]
[New LWP 786922]
[New LWP 786893]
[New LWP 9735]
[New LWP 787031]
[New LWP 766293]
[New LWP 786890]
[New LWP 787821]
[New LWP 786864]
[New LWP 1577325]
[New LWP 219233]
[New LWP 9732]
[New LWP 230219]
[New LWP 786953]
[New LWP 787055]
[New LWP 4485]
[New LWP 1577326]
[New LWP 786865]
[New LWP 786465]
[New LWP 1576753]
[New LWP 786877]
[New LWP 219730]
[New LWP 9720]
[New LWP 1577347]
[New LWP 786961]
[New LWP 4795]
[New LWP 786885]
[New LWP 787066]
[New LWP 786958]
[New LWP 9464]
[New LWP 230220]
[New LWP 9772]
[New LWP 787041]
[New LWP 4739]
[New LWP 230104]
[New LWP 9733]
[New LWP 786959]
[New LWP 4747]
[New LWP 229821]
[New LWP 786931]
[New LWP 4736]
[New LWP 230214]
[New LWP 787058]
[New LWP 4753]
[New LWP 787065]
[New LWP 787047]
[New LWP 786929]
[New LWP 312330]
[New LWP 786861]
[New LWP 786496]
[New LWP 229922]
[New LWP 786494]
[New LWP 786919]
[New LWP 229927]
[New LWP 786906]
[New LWP 787038]
[New LWP 786902]
[New LWP 766292]
[New LWP 230103]
[New LWP 786866]
[New LWP 787076]
[New LWP 787094]
[New LWP 312932]
[New LWP 786924]
[New LWP 230218]
[New LWP 1576752]
[New LWP 229924]
[New LWP 1577028]
[New LWP 786921]
[New LWP 763553]
[New LWP 786957]
[New LWP 786868]
[New LWP 786930]
[New LWP 230215]
[New LWP 1577348]

warning: File "/usr/lib64/libthread_db-1.0.so" auto-loading has been declined by your `auto-load safe-path' set to "$debugdir:$datadir/auto-load".
To enable execution of this file add
	add-auto-load-safe-path /usr/lib64/libthread_db-1.0.so
line to your configuration file "/root/.gdbinit".
To completely disable this security protection add
	set auto-load safe-path /
line to your configuration file "/root/.gdbinit".
For more information about this security protection see the
"Auto-loading safe path" section in the GDB manual.  E.g., run from the shell:
	info "(gdb)Auto-loading safe path"

warning: Unable to find libthread_db matching inferior's thread library, thread debugging will not be available.
Missing separate debuginfo for /greatdb/dbscale/libs/libcrypto.so.1.1
Try: dnf --enablerepo='*debug*' install /usr/lib/debug/.build-id/43/e1da60780e86d252d1ba80b3737b0251f95c00.debug
Missing separate debuginfo for /greatdb/dbscale/libs/libssl.so.1.1
Try: dnf --enablerepo='*debug*' install /usr/lib/debug/.build-id/be/d95d2bc5dcf81ef3de66b37a6c0c8e11d7c6f0.debug
Missing separate debuginfo for /greatdb/dbscale/libs/libz.so.1
Try: dnf --enablerepo='*debug*' install /usr/lib/debug/.build-id/b9/b31e2f5b67f02731d73c9c7244611662004586.debug
Missing separate debuginfo for /greatdb/dbscale/libs/libodbc.so.2
Try: dnf --enablerepo='*debug*' install /usr/lib/debug/.build-id/50/e1cc31e4d42bc5297951a77e514a8ca36bc3fe.debug
Missing separate debuginfo for /greatdb/dbscale/libs/libltdl.so.7
Try: dnf --enablerepo='*debug*' install /usr/lib/debug/.build-id/5e/1b01f3c09f696a7e1872d0a47810ba091f7e25.debug

warning: File "/usr/lib64/libthread_db-1.0.so" auto-loading has been declined by your `auto-load safe-path' set to "$debugdir:$datadir/auto-load".

warning: Unable to find libthread_db matching inferior's thread library, thread debugging will not be available.
Core was generated by `./dbscale --config-file /greatdb/dbscale/dbscale.conf'.
Program terminated with signal SIGABRT, Aborted.
#0  0x0000fffed54c66e0 in raise () from /lib64/libc.so.6
[Current thread is 1 (LWP 1584160)]
Missing separate debuginfos, use: dnf debuginfo-install glibc-2.28-36.1.ky10.aarch64 libgcc-7.3.0-20190804.h30.ky10.aarch64 libstdc++-7.3.0-20190804.h30.ky10.aarch64
(gdb) bt
#0  0x0000fffed54c66e0 in raise () from /lib64/libc.so.6
#1  0x0000fffed54c7a8c in abort () from /lib64/libc.so.6
#2  0x0000fffed6deb828 in OnSIGSEGV (signum=<optimized out>, info=<optimized out>, ptr=<optimized out>) at /builds/zhijun.shen/dbscale/include/seg.h:91
#3  <signal handler called>
#4  0x0000fffed55199c0 in ?? () from /lib64/libc.so.6
#5  0x0000fffed6e7b944 in dbscale::assign_mem_for_str (st=st@entry=0xfff5c4008790, s=s@entry=0xfffed6a4bb40 "Memory error during the SQL parsing!") at /builds/zhijun.shen/dbscale/src/sql_parser.cc:533
#6  0x0000fffed6956e3c in dbscale::exit_parser_for_mem_err (st=0xfff5c4008790) at /builds/zhijun.shen/dbscale/driver/mysql//gram.y:16293
#7  yyparse (pp=pp@entry=0xfff5cefddfa8) at /builds/zhijun.shen/dbscale/driver/mysql//gram.y:9667
#8  0x0000fffed697d934 in dbscale::do_parse (str=str@entry=0x2593e4d "LOAD DATA LOCAL INFILE '/home/ap/nas/dataoutput/file/output/ap901/000000000/data/20240818/AP901_SAFEID_000000000_TMP_TF_WS_SND_SMS_SAF_S_ADD_20240818_0001.dat' REPLACE INTO TABLE TF_WS_SND_SMS_SAF_S \n"...,
    len=2001, st=st@entry=0xfff5c4008790, session_alloc=session_alloc@entry=0x25929a8) at /builds/zhijun.shen/dbscale/driver/mysql//gram.y:16315
#9  0x0000fffed6799698 in dbscale::mysql::MySQLParser::parse (this=0xfffed00074d8,
    sql=0x2593e4d "LOAD DATA LOCAL INFILE '/home/ap/nas/dataoutput/file/output/ap901/000000000/data/20240818/AP901_SAFEID_000000000_TMP_TF_WS_SND_SMS_SAF_S_ADD_20240818_0001.dat' REPLACE INTO TABLE TF_WS_SND_SMS_SAF_S \n"..., stmt_allow_dot_in_ident=<optimized out>,
    need_lock=false, alloc=<optimized out>, s=<optimized out>, session_alloc=0x25929a8, ctype=dbscale::CHARSET_TYPE_OTHER) at /builds/zhijun.shen/dbscale/driver/mysql/mysql_parser.cc:45
#10 0x0000fffed674ed10 in dbscale::mysql::MySQLHandler::handle_query_command (this=this@entry=0x2d94fc0, query_sql=0x2d94fc0 "\230/\271\326\376\377", cmd_len=<optimized out>) at /builds/zhijun.shen/dbscale/driver/mysql/mysql_handler.cc:2477
#11 0x0000fffed6752a38 in dbscale::mysql::MySQLHandler::handle_query_command (this=this@entry=0x2d94fc0, packet=packet@entry=0xfff5cefde4f8) at /builds/zhijun.shen/dbscale/driver/mysql/mysql_handler.cc:2315
#12 0x0000fffed6759594 in dbscale::mysql::MySQLHandler::do_handle_request (this=0x2d94fc0) at /builds/zhijun.shen/dbscale/driver/mysql/mysql_handler.cc:3728
#13 0x0000fffed6e4dfb0 in dbscale::Handler::handle_request (this=0x2d94fc0) at /builds/zhijun.shen/dbscale/include/handler.h:348
#14 dbscale::Handler::svc (this=0x2d94fc0) at /builds/zhijun.shen/dbscale/src/handler.cc:826
#15 0x0000fffed6517f18 in ACE_Task_Base::svc_run (args=0x2d94fe8) at /work/ACE_wrappers/ace/Task.cpp:269
#16 0x0000fffed6518bd0 in ACE_Thread_Adapter::invoke_i (this=<optimized out>) at /work/ACE_wrappers/ace/Thread_Adapter.cpp:161
#17 0x0000fffed6518b0c in ACE_Thread_Adapter::invoke (this=0x170cb80) at /work/ACE_wrappers/ace/Thread_Adapter.cpp:96
#18 0x0000fffed63a88cc in ?? () from /lib64/libpthread.so.0
#19 0x0000fffed556a1ec in ?? () from /lib64/libc.so.6

```










