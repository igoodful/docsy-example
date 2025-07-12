---
title: 01. systemd
description: systemd
date: 2023-11-08
weight: 100


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{<alert>}}

1. Type=simple
   - ExecStart=该服务的主进程
   - Restart=always/on-failure/on-abnormal
   - RestartSec=1
   - TimeoutStartSec，TimeoutStopSec，TimeoutSec
   - SuccessExitStatus


2. 进程以daemon启动，则Type=forking
   - PIDFile
   - KillMode
   - ExecStart=
   - Restart=always/on-failure/on-abnormal
   - RestartSec=1
   - TimeoutStartSec，TimeoutStopSec，TimeoutSec
   - SuccessExitStatus


- sudo systemctl daemon-reload
- sudo systemctl enable my_service.service
- sudo systemctl start my_service.service
- sudo systemctl status my_service.service
- sudo systemctl stop my_service.service
- sudo systemctl restart my_service.service
- sudo journalctl -u my_service.service



{{</alert>}}



---
&#128311;【】2017年上半年，城镇居民人均可支配收入18332元，增长8.1%（以下如无特别说明，均为同比名义增长）

2016年上半年，城镇居民人均可支配收入约为多少元？

【解析】$\cfrac{18332}{ 1 + 0.081 } = 17000$




## 信号

#### 1. 查看所有的信号参数
```bash
2024-06-22T21:59:02 [root@igoodful /root] $ kill -l
 1) SIGHUP	 2) SIGINT	 3) SIGQUIT	 4) SIGILL	 5) SIGTRAP
 6) SIGABRT	 7) SIGBUS	 8) SIGFPE	 9) SIGKILL	10) SIGUSR1
11) SIGSEGV	12) SIGUSR2	13) SIGPIPE	14) SIGALRM	15) SIGTERM
16) SIGSTKFLT	17) SIGCHLD	18) SIGCONT	19) SIGSTOP	20) SIGTSTP
21) SIGTTIN	22) SIGTTOU	23) SIGURG	24) SIGXCPU	25) SIGXFSZ
26) SIGVTALRM	27) SIGPROF	28) SIGWINCH	29) SIGIO	30) SIGPWR
31) SIGSYS	34) SIGRTMIN	35) SIGRTMIN+1	36) SIGRTMIN+2	37) SIGRTMIN+3
38) SIGRTMIN+4	39) SIGRTMIN+5	40) SIGRTMIN+6	41) SIGRTMIN+7	42) SIGRTMIN+8
43) SIGRTMIN+9	44) SIGRTMIN+10	45) SIGRTMIN+11	46) SIGRTMIN+12	47) SIGRTMIN+13
48) SIGRTMIN+14	49) SIGRTMIN+15	50) SIGRTMAX-14	51) SIGRTMAX-13	52) SIGRTMAX-12
53) SIGRTMAX-11	54) SIGRTMAX-10	55) SIGRTMAX-9	56) SIGRTMAX-8	57) SIGRTMAX-7
58) SIGRTMAX-6	59) SIGRTMAX-5	60) SIGRTMAX-4	61) SIGRTMAX-3	62) SIGRTMAX-2
63) SIGRTMAX-1	64) SIGRTMAX

```

| 信号名称  | 信号属猪 | 含义                                          | 含义                                         |
| :-------- | :------- | :-------------------------------------------- | :------------------------------------------- |
| SIGHUP    | 1        | 终止进程     终端线路挂断                     | /* Hangup (POSIX).  */                       |
| SIGINT    | 2        | 终止进程     中断进程 Ctrl+C                  | /* Interrupt (ANSI).  */                     |
| SIGQUIT   | 3        | 建立CORE文件终止进程，并且生成core文件 Ctrl+\ | /* Quit (POSIX).  */                         |
| SIGILL    | 4        | 建立CORE文件,非法指令                         | /* Illegal instruction (ANSI).  */           |
| SIGTRAP   | 5        | 建立CORE文件,跟踪自陷                         | /* Trace trap (POSIX).  */                   |
| SIGABRT   | 6        |                                               | /* Abort (ANSI).  */                         |
| SIGIOT    | 6        | 建立CORE文件,执行I/O自陷                      | /* IOT trap (4.2 BSD).  */                   |
| SIGBUS    | 7        | 建立CORE文件,总线错误                         | /* BUS error (4.2 BSD).  */                  |
| SIGFPE    | 8        | 建立CORE文件,浮点异常                         | /* Floating-point exception (ANSI).  */      |
| SIGKILL   | 9        | 终止进程     杀死进程                         | /* Kill, unblockable (POSIX).  */            |
| SIGUSR1   | 10       | 终止进程     用户定义信号1                    | /* User-defined signal 1 (POSIX).  */        |
| SIGSEGV   | 11       | 建立CORE文件,段非法错误                       | /* Segmentation violation (ANSI).  */        |
| SIGUSR2   | 12       | 终止进程     用户定义信号2                    | /* User-defined signal 2 (POSIX).  */        |
| SIGPIPE   | 13       | 终止进程     向一个没有读进程的管道写数据     | /* Broken pipe (POSIX).  */                  |
| SIGALARM  | 14       | 终止进程     计时器到时                       | /* Alarm clock (POSIX).  */                  |
| SIGTERM   | 15       | 终止进程     软件终止信号                     | /* Termination (ANSI).  */                   |
| SIGSTKFLT | 16       |                                               | /* Stack fault.  */                          |
| SIGCLD    | SIGCHLD  |                                               | /* Same as SIGCHLD (System V).  */           |
| SIGCHLD   | 17       | 忽略信号     当子进程停止或退出时通知父进程   | /* Child status has changed (POSIX).  */     |
| SIGCONT   | 18       | 忽略信号     继续执行一个停止的进程           | /* Continue (POSIX).  */                     |
| SIGSTOP   | 19       | 停止进程     非终端来的停止信号               | /* Stop, unblockable (POSIX).  */            |
| SIGTSTP   | 20       | 停止进程     终端来的停止信号 Ctrl+Z          | /* Keyboard stop (POSIX).  */                |
| SIGTTIN   | 21       | 停止进程     后台进程读终端                   | /* Background read from tty (POSIX).  */     |
| SIGTTOU   | 22       | 停止进程     后台进程写终端                   | /* Background write to tty (POSIX).  */      |
| SIGURG    | 23       | 忽略信号     I/O紧急信号                      | /* Urgent condition on socket (4.2 BSD).  */ |
| SIGXCPU   | 24       | 终止进程     CPU时限超时                      | /* CPU limit exceeded (4.2 BSD).  */         |
| SIGXFSZ   | 25       | 终止进程     文件长度过长                     | /* File size limit exceeded (4.2 BSD).  */   |
| SIGVTALRM | 26       | 终止进程     虚拟计时器到时                   | /* Virtual alarm clock (4.2 BSD).  */        |
| SIGPROF   | 27       | 终止进程     统计分布图用计时器到时           | /* Profiling alarm clock (4.2 BSD).  */      |
| SIGWINCH  | 28       | 忽略信号     窗口大小发生变化                 | /* Window size change (4.3 BSD, Sun).  */    |
| SIGPOLL   | SIGIO    |                                               | /* Pollable event occurred (System V).  */   |
| SIGIO     | 29       | 忽略信号     描述符上可以进行I/O              | /* I/O now possible (4.2 BSD).  */           |
| SIGPWR    | 30       |                                               | /* Power failure restart (System V).  */     |
| SIGSYS    | 31       |                                               | /* Bad system call.  */                      |
| SIGUNUSED | 31       |                                               |                                              |




只有第9种信号(SIGKILL)才可以无条件终止进程，其他信号进程都有权利忽略。 下面是常用的前15个信号：

#### 1. kill默认信号：SIGTERM
SIGTERM：这是 kill 命令的默认信号。它会请求进程优雅地终止，允许进程在退出前执行清理操作。进程可以选择忽略这个信号，但大多数进程会响应并正常退出。
用途：请求进程终止。默认的信号，进程可以捕获该信号并执行清理操作后退出。`kill -15 = kill`

#### 2. 强制终止信号：SIGKILL
> `kill -9 `
SIGKILL: SIGKILL这是一个强制终止信号，进程不能忽略它。当 SIGTERM 信号无效或需要立即终止进程时，可以使用此信号，强制终止

用途：强制终止进程。该信号不能被进程忽略或捕获，常用于强制结束无法响应的进程。

#### 3. coredump程序错误信号：SIGQUIT
> `kill -3 `= `Ctrl+\`
SIGQUIT: 这类似于SIGINT，但由于QUIT字符通常是由Ctrl+\来控制，进程在收到SIGQUIT退出时会产生core文件, 在这个意义上类似于一个程序错误信号。 退出（同 Ctrl + \）

用途：与SIGINT类似，但会产生核心转储（coredump）。常用于调试。建立CORE文件终止进程，并且生成core文件


#### 4. 中断前台进程信号：SIGINT
> `kill -2 `= `Ctrl+C`
SIGINT: 该信号与按Ctrl+C相同，在某些系统上delete + break会向进程发送相同的信号，该进程被中断并停止，但是该进程自身可以忽略此信号。

用途：当用户按下中断键（通常是Ctrl+C）时发送。用于中断前台进程。


#### 5. 段错误信号：SIGSEGV
> `kill -11 `
SIGSEGV: 当应用程序有分段冲突时，这个信号被发送到进程。

用途：当进程访问了无效的内存地址时发送，也称为段错误。

#### 父子断绝信号：SIGHUP
SIGHUP信号用于断开进程与父进程的连接，这也可以用于重新启动进程，这对于内存泄漏的守护程序很有用。终端断线

用途：当控制终端（通常是终端会话）结束或挂起时发送。常用于通知守护进程（daemon）重新读取配置文件。


#### 非法操作信号：SIGILL
> `kill -4 `
SIGILL: 当一个进程执行一个错误的、禁止的或未知的功能时，系统向该进程发送SIGILL信号，这是代表非法操作的信号。建立CORE文件,非法指令

用途：当进程执行了非法或未知的指令时发送。用于指示进程中的指令错误。

#### 程序终止信号：SIGABRT
SIGABRT: 该终止信号是中止信号,通常进程会在自身上发出此终止信号。

用途：当程序调用abort()函数时发送。用于程序自我终止并可能产生核心转储。

#### 总线错误信号：SIGBUS
> `kill -7 `

SIGBUS: 当一个进程被发送SIGBUS信号时，是因为该进程导致了一个总线错误，通常这些总线错误是由于进程试图使用假物理地址或进程的内存对齐设置不正确造成的。

用途：模拟指令异常，较少使用。建立CORE文件,总线错误

#### 浮点零除信号：SIGFPE
> `kill -8 `
SIGFPE: 被零除的进程使用SIGFPE终止。建立CORE文件,浮点异常

用途：当发生浮点错误（如除以零）时发送。用于指示浮点运算中的错误。


#### 自定义非法系统调用信号：SIGUSR2
SIGUSR2: 这表示用户定义的条件。
用途：当进程执行了非法的系统调用时发送。

#### 管道问题信号：SIGPIPE
SIGPIPE: 当进程尝试写入缺少与读取器连接的一端的管道时，此信号将发送到进程，读取器是在管道末端读取数据的过程。
用途：当进程向没有读者的管道写数据时发送。通常用于指示管道问题。

#### 定时超时信号：SIGALRM
SIGALRM: 当实时或时钟计时器到期时，将发送SIGALRM。通常用于实现定时器或超时功能。
用途：定时器到期时发送。常用于实现定时器或超时功能。

#### SIGTRAP
> `kill -11 `
SIGTRAP: 此信号用于调试目的，当某个进程执行了某个操作或满足了调试器正在等待的条件时，此信号将被发送到该进程。

用途：常用于调试器，当遇到断点（breakpoint）时暂停进程。建立CORE文件,跟踪自陷

#### SIGUSR1
SIGUSR1: 这表示用户定义的条件，用户可以通过在sigusr1.c中编程命令来设置此信号。

用途：当进程访问了无效的内存地址或发生了其他总线错误时发送。




## 一、Unit
> 启动顺序与依赖关系


| 选项                   | 功能                                           | 示例                                  |
| :--------------------- | :--------------------------------------------- | :------------------------------------ |
| Description            | 给出当前服务的简单描述                         |                                       |
| Documentation          | 给出文档位置                                   |                                       |
| Wants                  | 弱依赖关系                                     |                                       |
| Requires               | 强依赖关系                                     |                                       |
| Before                 | 启动顺序,表示需要在指定服务启动之前启动        |                                       |
| After                  | 启动顺序,表示本服务需要在指定服务启动后在启动  | After=network.target                  |
| AssertFileIsExecutable |                                                |                                       |
| AssertFileNotEmpty     |                                                |                                       |
| Conflicts              | 这里指定的 Unit 不能与当前 Unit 同时运行       |                                       |
| Condition              | 当前 Unit 运行必须满足的条件，否则不会运行     |                                       |
| Assert                 | 当前 Unit 运行必须满足的条件，否则会报启动失败 |                                       |
| StartLimitIntervalSec  | 重启速率限制，多少秒内，默认10秒               | 建议设置为0，这样服务就能一直尝试重启 |
| StartLimitBurst        | 重启速率限制，多少次重启，默认5次              |                                       |









## 二、Service
> 启动行为定义，命令或脚本必须使用绝对路径

| 选项             | 功能                               | 示例                                      |
| :--------------- | :--------------------------------- | :---------------------------------------- |
| Type             | 设置服务的启动方式                 | 前台用simple，后台/守护是forking          |
| WorkingDirectory | 设置服务运行的路径(cwd)            |                                           |
| User             | 指定服务运行的用户                 |                                           |
| Group            | 指定服务运行的用户组               |                                           |
| ProtectProc      |                                    |                                           |
| Environment      | 后面接多个不同的 shell 变量        | Environment="VAR1=word1 word2" VAR2=word3 |
| EnvironmentFile  | 指定当前服务的环境参数文件（路径） |                                           |
| Restart          | 指定服务在故障时的重启策略         |                                           |
| LimitNOFILE      |                                    |                                           |
| TasksMax         |                                    |                                           |
| TimeoutSec       | 定义服务的超时时间                 |                                           |
| SendSIGKILL      |                                    |                                           |
| ExecStartPre     | 定义启动服务前执行的命令           |                                           |
| ExecStart        | 定义启动服务时执行的命令           |                                           |
| ExecStartPost    | 定义启动服务后执行的命令           |                                           |
| ExecStop         | 定义停止服务时执行的命令           |                                           |
| ExecStopPost     | 定义停止服务后执行的命令           |                                           |
| RestartSec       | 重启等待时间，默认100毫秒          | RestartSec=5                              |
| ExecReload       | 定义重启服务时执行的命令           |                                           |
| KillMode         | 定义systemd如何停止服务            |                                           |


#### Environment

`Environment=MYSQLD_PARENT_PID=1`

PID为1的进程就是 systemd 的进程




#### RestartPreventExitStatus

- [https://blog.csdn.net/nerdsu/article/details/119912710](https://blog.csdn.net/nerdsu/article/details/119912710)

.RestartPreventExitStatus参数
查询man systemd.service发现，systemd的[Service]段落里支持一个参数，叫做RestartPreventExitStatus

该参数从字面上看，意思是当符合某些退出状态时不要进行重启。

该参数的值支持exit code和信号名2种，可写多个，以空格分隔，例如

RestartPreventExitStatus=143 137 SIGTERM SIGKILL
1.
表示，当退出情况只要符合以下4种情况中任意一种时候，则不再进行重启

exit code为143

exit code为137

信号为TERM

信号为KILL

但实际情况并没这么简单，请继续往下看


#### RestartForceExitStatus

RestartForceExitStatus=16,这时我们就可以设置只有当数据库退出状态码为16时才进行自动重启，而在其余情况下不会进行自动重启


#### ExecStart

- 必须且只能设置一个命令行。但Type=oneshot时，才可以设置任意个命令行(包括零个)， 多个命令行既可以在同一个 ExecStart= 中设置，也可以通过设置多个 ExecStart= 来达到相同的效果。

- 命令行必须以一个可执行文件(要么是绝对路径、要么是不含任何斜线的文件名)开始， 并且其后的那些参数将依次作为"argv[1] argv[2] …"传递给被执行的进程。 可选的，可以在绝对路径前面加上各种不同的前缀表示不同的含义：

- 可执行文件前的特殊前缀
  - `-` 如果在绝对路径前加上可选的 "-" 前缀，那么即使该进程以失败状态(例如非零的返回值或者出现异常)退出，也会被视为成功退出，但同时会留下错误日志。如果某个无 "-" 前缀的命令行执行失败， 那么剩余的命令行将不会被继续执行， 同时该单元将变为失败(failed)状态。
  - `+` 如果在绝对路径前加上可选的 "+" 前缀，那么进程将拥有完全的权限(超级用户的特权)，并且 User=, Group=, CapabilityBoundingSet= 选项所设置的权限限制以及 PrivateDevices=, PrivateTmp= 等文件系统名字空间的配置将被该命令行启动的进程忽略(但仍然对其他 ExecStart=, ExecStop= 有效)。
  - `!` 与 "+" 类似(进程仍然拥有超级用户的身份)，不同之处在于仅忽略 User=, Group=, SupplementaryGroups= 选项的设置，而例如名字空间之类的其他限制依然有效。注意，当与 DynamicUser= 一起使用时，将会在执行该命令之前先动态分配一对 user/group ，然后将身份凭证的切换操作留给进程自己去执行。
  - `!!` 与 "!" 极其相似，仅用于让利用 ambient capability 限制进程权限的单元兼容不支持 ambient capability 的系统(也就是不支持 AmbientCapabilities= 选项)。如果在不支持 ambient capability 的系统上使用此前缀，那么 SystemCallFilter= 与 CapabilityBoundingSet= 将被隐含的自动修改为允许进程自己丢弃 capability 与特权用户的身份(即使原来被配置为禁止这么做)，并且 AmbientCapabilities= 选项将会被忽略。此前缀在支持 ambient capability 的系统上完全没有任何效果。
  - `@` 如果在绝对路径前加上可选的 "@" 前缀，那么其后的那些参数将依次作为"argv[0] argv[1] argv[2] …"传递给被执行的进程(注意，argv[0] 是可执行文件本身)。



#### ExecStop



另外，还有一个特殊的环境变量 $MAINPID 可用于表示主进程的PID， 例如可以这样使用：

`/bin/kill -HUP $MAINPID`



#### ExecStopPost

注意，与 ExecStop= 不同，无论服务是否启动成功， 此选项中设置的命令都会在服务停止后被无条件的执行。


#### RestartSec

设置在重启服务(Restart=)前暂停多长时间。 默认值是100毫秒(100ms)。 如果未指定时间单位，那么将视为以秒为单位。 例如设为"20"等价于设为"20s"。


#### TimeoutStartSec

- 设置该服务允许的最大启动时长。 如果守护进程未能在限定的时长内发出"启动完毕"的信号，那么该服务将被视为启动失败，并会被关闭。

- 如果未指定时间单位，那么将视为以秒为单位。 例如设为"20"等价于设为"20s"。 设为 "infinity" 则表示永不超时。

- 当Type=oneshot时， 默认值为 "infinity" (永不超时)， 否则默认值等于 DefaultTimeoutStartSec= 的值

- 如果一个 Type=notify 服务发送了 "EXTEND_TIMEOUT_USEC=…" 信号， 那么允许的启动时长将会在 TimeoutStartSec= 基础上继续延长指定的时长。

- 注意，必须在 TimeoutStartSec= 用完之前发出第一个延时信号。当启动时间超出 TimeoutStartSec= 之后，该服务可以在维持始终不超时的前提下，不断重复发送 "EXTEND_TIMEOUT_USEC=…" 信号， 直到完成启动(发送 "READY=1" 信号)。




#### TimeoutStopSec

此选项有两个用途：

- 设置每个 ExecStop= 的超时时长。如果其中之一超时， 那么所有后继的 ExecStop= 都会被取消，并且该服务也会被 SIGTERM 信号强制关闭。 如果该服务没有设置 ExecStop= ，那么该服务将会立即被 SIGTERM 信号强制关闭。 (2)设置该服务自身停止的超时时长。如果超时，那么该服务将会立即被 SIGTERM 信号强制关闭(参见 systemd.kill(5) 手册中的 KillMode= 选项)。 如果未指定时间单位，那么将视为以秒为单位。 例如设为"20"等价于设为"20s"。 设为 "infinity" 则表示永不超时。 默认值等于 DefaultTimeoutStopSec= 的值(参见 systemd-system.conf(5) 手册)。

如果一个 Type=notify 服务发送了 "EXTEND_TIMEOUT_USEC=…" 信号， 那么允许的停止时长将会在 TimeoutStopSec= 基础上继续延长指定的时长。 注意，必须在 TimeoutStopSec= 用完之前发出第一个延时信号。当停止时间超出 TimeoutStopSec= 之后，该服务可以在维持始终不超时的前提下，不断重复发送 "EXTEND_TIMEOUT_USEC=…" 信号，直到完成停止。 详见 sd_notify(3) 手册。


#### TimeoutSec

一个同时设置 TimeoutStartSec= 与 TimeoutStopSec= 的快捷方式。




#### Type
启动类型，可以设置的值如下：

| 选项    | 功能                                                                    | 示例                                                     |
| :------ | :---------------------------------------------------------------------- | :------------------------------------------------------- |
| simple  | 默认值，执行ExecStart指定的命令，直接启动服务进程                       |                                                          |
| forking | ExecStart字段将以fork()方式启动，此时父进程将会退出，子进程将成为主进程 |                                                          |
| oneshot | 类似于simple,但只执行一次，Systemd 会等当前服务退出，再继续往下执行     |                                                          |
| dbus    | 当前服务通过 D-Bus 启动                                                 |                                                          |
| notify  | 类似于simple，启动结束后会发出通知信号，然后 systemd 再启动其他服务     |                                                          |
| idle    | 类似于simple，但是要等到其他任务都执行完，才会启动该服务。              | 一种使用场合是为让该服务的输出，不与其他服务的输出相混合 |


###### notify
```bash
2024-05-31T18:51:48 [root@node85 /data/app/dbscale/daemon] $ systemctl cat sshd.service
# /usr/lib/systemd/system/sshd.service
[Unit]
Description=OpenSSH server daemon
Documentation=man:sshd(8) man:sshd_config(5)
After=network.target sshd-keygen.target
Wants=sshd-keygen.target

[Service]
Type=notify
EnvironmentFile=-/etc/crypto-policies/back-ends/opensshserver.config
EnvironmentFile=-/etc/sysconfig/sshd
ExecStart=/usr/sbin/sshd -D $OPTIONS $CRYPTO_POLICY
ExecReload=/bin/kill -HUP $MAINPID
KillMode=process
Restart=on-failure
RestartSec=42s

[Install]
WantedBy=multi-user.target

```




###### 1. forking

- Type=forking，一定要给出PIDFile指令，systemd会从PIDFile指令所指定的PID文件中获取服务的主进程PID，否则很容易出问题

- 首先，PIDFile只适合在Type=forking模式下使用，其它时候没必要使用，因为其它类型的Service主进程的PID都是确定的。

- PIDFile指令的值要和服务程序的PID文件路径保持一致。

- 其次，systemd会在中间父进程退出后立即读取这个PID文件，读取成功后就认为该服务已经启动成功。但是，systemd读取PIDFile的时候，服务主进程可能还未将PID写入到PID文件中，这时systemd将出现问题。所以，对于服务程序的开发人员来说，应尽早将主进程写入到PID文件中，比如可以在中间父进程fork完之后立即写入PID文件，然后再退出，而不是在fork出来的服务主进程内部由主进程负责写入。ExecStartPost=/usr/bin/sleep 0.1

- 最后，systemd只会读PIDFile文件而不会写，也不会创建它。但是，在停止服务的时候，systemd会尝试删除PID文件。因为服务进程可能会异常终止，导致已终止的服务进程的PID文件仍然保留着，所以在使用PIDFile指令时，通常还会使用ExecStartPre指令来删除可能已经存在的PID文件。正如上面给出的nginx配置文件一样。

- 当一个命令本身会以daemon模式运行时，将不能使用simple，而应该使用Type=forking

###### 2. simple
Type=simple类型的服务只适合那些在shell下运行在前台的命令。比如ls命令、sleep命令、非daemon模式运行的nginx进程以及那些以前台调试模式运行的进程，在理论上都可以定义为simple类型的服务。至于为何有如此规则，稍后会解释的明明白白。

对于Type=simple来说，systemd系统在fork出子systemd进程后就认为服务已经启动完成了，所以systemd可以紧跟着启动排在该服务之后启动的服务

换句话说，当Type=simple时，systemd只在乎fork阶段是否成功，只要fork子进程成功，这个子进程就受systemd监管，systemd就认为该Unit已经启动

因为子进程已成功被systemd监控，无论子进程是否启动成功，在子进程退出时，systemd都会将其从监控队列中踢掉，同时杀掉所有附属进程(默认行为是如此，杀进程的方式由systemd.kill中的KillMode指令控制)。所以，查看服务的状态将是inactive(dead)。

例如，下面的配置种，睡眠1秒后，该服务的状态将变为inactive(dead)

```bash
[Unit]
Description = Test

[Service]
Type = forking
PIDFile = /run/nginx.pid
ExecStartPre = /usr/bin/rm -f /run/nginx.pid
ExecStart = /usr/sbin/nginx
ExecStartPost = /usr/bin/sleep 0.1
```

####

systemd service的命令行中必须使用绝对路径

只能编写单条命令(Type=oneshot时除外)，如果要命令续行，可在尾部使用反斜线符号\等

命令行中支持部分类似Shell的特殊符号，但不支持重定向> >> << <、管道|、后台符号&



#### KillMode

| 选项          | 功能                                               | 示例 |
| :------------ | :------------------------------------------------- | :--- |
| control-group | default,当前控制组里面的所有子进程，都会被杀掉     |      |
| process       | 只杀主进程                                         |      |
| mixed         | 主进程将收到 SIGTERM 信号，子进程收到 SIGKILL 信号 |      |
| none          | 没有进程会被杀掉，只是执行服务的 stop 命令         |      |




#### Restart
- [https://www.jinbuguo.com/systemd/systemd.service.html](https://www.jinbuguo.com/systemd/systemd.service.html)

> 服务主进程因为正常退出、异常退出、被杀死、超时的时候， 是否重新启动该服务。
>
> 服务进程是指 ExecStart=, ExecStartPre=, ExecStartPost=, ExecStop=, ExecStopPost=, ExecReload= 中设置的进程。
>
> 当进程是由于 systemd 的正常操作(例如 systemctl stop|restart)而被停止时， 该服务不会被重新启动。
>
> 所谓"超时"可以是看门狗的"keep-alive ping"超时， 也可以是 systemctl start|reload|stop 操作超时。
>
> no(默认值) 表示不会被重启。


各个选项的具体含义：

| 退出原因        | always  | on-failure | on-abnormal | on-success | on-abort | on-watchdog | no   |
| :-------------- | :------ | :--------- | :---------- | :--------- | :------- | :---------- | :--- |
| 正常退出        | &#9989; |            |             | &#9989;    |          |             |      |
| 退出码不为0     | &#9989; | &#9989;    |             |            |          |             |      |
| 进程被强制杀死  | &#9989; | &#9989;    | &#9989;     |            | &#9989;  |             |      |
| systemd操作超时 | &#9989; | &#9989;    | &#9989;     |            |          |             |      |
| 看门狗超时      | &#9989; | &#9989;    | &#9989;     |            |          | &#9989;     |      |

- 正常退出：退出码为"0"， 或者进程收到 SIGHUP, SIGINT, SIGTERM, SIGPIPE 信号之一， 并且退出码符合SuccessExitStatus=的设置。

- 异常退出：退出码不为"0"， 或者 进程被强制杀死(包括 "core dump"以及收到 SIGHUP, SIGINT, SIGTERM, SIGPIPE 之外的其他信号)， 或者进程由于 看门狗超时 或者 systemd 的操作超时 而被杀死。


(1) RestartPreventExitStatus= 中列出的退出码或信号永远不会导致该服务被重启。

(2) RestartForceExitStatus= 中列出的退出码或信号将会无条件的导致该服务被重启。

(3) 被 systemctl stop 命令或等价的操作停止的服务永远不会被重启。

注意，服务的重启频率仍然会受到由 StartLimitIntervalSec= 与 StartLimitBurst= 定义的启动频率的制约。只有在达到启动频率限制之后， 重新启动的服务才会进入失败状态。





- 对于守护进程，推荐设为 on-failure
- 对于那些允许发生错误退出的服务，可以设为 on-abnormal

##### 1. always

```systemd
[Unit]
Description = Test

[Service]
ExecStart = /usr/bin/grep md123456 /etc/profile
Restart=always
RestartSec=10

```

##### 2. on-failure

```systemd
[Unit]
Description = Test

[Service]
ExecStart = /usr/bin/grep md123456 /etc/profile
Restart=always
RestartSec=10

```


&#128311; 测试信号

`2024-06-23T14:36:03 [root@igoodful /root] $ systemctl cat  test`

```systemd
# /usr/lib/systemd/system/test.service
[Unit]
Description = Test

[Service]
ExecStart = /usr/bin/sleep 60
Restart=on-failure
RestartSec=10

```

测试结果：
- &#10060;服务进程自己主动退出，且退出码为0，则服务不会被自动重启
- &#10060;服务进程自己主动退出，且退出码为SuccessExitStatus指定的信号，则服务不会被自动重启
- &#10060;对服务进程发出信号 `SIGHUP(kill -1), SIGINT(kill -2), SIGTERM(kill -15), SIGPIPE(kill -13)`后，服务进程不再被systemd重启
- &#9989;对服务进程发起其他信号导致服务进程退出时，比如kill -9杀死进程，会自动重启
- &#9989;服务进程发生core dump时，会自动重启
- &#9989;systemd操作超时，会自动重启
- &#9989;看门狗超时，会自动重启

`2024-06-29T22:07:11 [root@node77 /root] $ cat /usr/lib/systemd/system/mysql_16315.service`
```systemd
[Unit]
Description=MySQL Server
Documentation=man:mysqld(8)
Documentation=http://dev.mysql.com/doc/refman/en/using-systemd.html
After=network.target
After=syslog.target

[Install]
WantedBy=multi-user.target

[Service]
User=mysql
Group=mysql
Type=forking
PIDFile=/data/mysqldata/16315fff-f4a3-42ca-852a-164d4f94b7e8/pid/mysqld.pid
TimeoutSec=0
PermissionsStartOnly=true
ExecStart=/data/app/mysql-8.0.26/bin/mysqld --defaults-file=/data/mysqldata/16315fff-f4a3-42ca-852a-164d4f94b7e8/my16315.conf --daemonize
Restart=on-failure
RestartPreventExitStatus=1
Environment=MYSQLD_PARENT_PID=1
PrivateTmp=false
LimitNOFILE = 1024000
LimitNPROC = 1024000

```







##### 3. on-abnormal

```systemd
[Unit]
Description = Test

[Service]
ExecStart = /usr/bin/grep md123456 /etc/profile
Restart=always
RestartSec=10

```




##### 4. on-success

{{<alert>}}

1. 服务进程自己退出，且退出码为0
2. 服务进程自己退出，且退出码为SuccessExitStatus指定的信号
3. 服务进程收到 `SIGHUP(kill -1), SIGINT(kill -2), SIGTERM(kill -15), SIGPIPE(kill -13)`信号之一

SIGPIPE(kill -13),这个信号没有模拟


{{</alert>}}

- systemctl daemon-reload
- systemctl start test
- systemctl status test


&#128311; 1. 测试正常退出码0
> 服务进程退出码为0

`2024-06-23T13:18:55 [root@igoodful /root] $ systemctl cat  test`

```systemd
# /usr/lib/systemd/system/test.service
[Unit]
Description = Test

[Service]
ExecStart = /usr/bin/sleep 5
Restart=on-success
RestartSec=10

```

执行结果是：服务进程正常执行5秒后退出，退出码为0，然后等待RestartSec=10秒后重启该服务

如下所示：

| 时间                | 状态                      | 说明                                               |
| :------------------ | :------------------------ | :------------------------------------------------- |
| 2024-06-23 13:20:38 | active (running)          | 启动时间，开始执行ExecStart的时间                  |
| 2024-06-23 13:20:43 | activating (auto-restart) | 程序正常退出时刻开始，等待RestartSec时间后自动重启 |
| 2024-06-23 13:20:53 | active (running)          | 第一次自动重启开始开始执行ExecStart的时间          |
| xxx                 | yyy                       | 重复上述过程                                       |


&#128311; 2. 测试指定正常退出码
> 服务进程退出码为指定`SuccessExitStatus=1 2 8 SIGKILL`

`2024-06-23T14:06:03 [root@igoodful /root] $ systemctl cat test`

```systemd

# /usr/lib/systemd/system/test.service
[Unit]
Description = Test

[Service]
SuccessExitStatus=1 2 8 SIGKILL

ExecStart = /usr/bin/grep dddddddd  /etc/profile
Restart=on-success
RestartSec=10


```
测试结果是：服务进程的退出码每次都为1，即服务进程的退出码不为0，但是SuccessExitStatus指定了信号`1 2 8 SIGKILL`为正常退出码，因此被systemd认为退出码1为正常退出码，于是服务进程退出后，等待RestartSec=10秒后重启该服务

&#128311; 3. 测试信号
> 服务进程收到 `SIGHUP(kill -1), SIGINT(kill -2), SIGTERM(kill -15), SIGPIPE(kill -13)`信号之一
>
> 经过测试对服务进程发送信号：`kill -1;kill-2;kill -15`，服务进程在等待RestartSec=10秒后自动重启


`2024-06-23T14:25:35 [root@igoodful /root] $ systemctl cat test`
```systemd

# /usr/lib/systemd/system/test.service
[Unit]
Description = Test

[Service]
SuccessExitStatus=1 2 8 SIGKILL

ExecStart = /usr/bin/sleep 60
Restart=on-success
RestartSec=10

```


##### 5. on-abort

```systemd
[Unit]
Description = Test

[Service]
ExecStart = /usr/bin/grep md123456 /etc/profile
Restart=always
RestartSec=10

```

##### 6. on-watchdog

```systemd
[Unit]
Description = Test

[Service]
ExecStart = /usr/bin/grep md123456 /etc/profile
Restart=always
RestartSec=10

```

##### 7. no

```systemd
[Unit]
Description = Test

[Service]
ExecStart = /usr/bin/grep md123456 /etc/profile
Restart=always
RestartSec=10

```






## 三、Install
> 服务安装定义

| 选项       | 功能                   | 示例                       |
| :--------- | :--------------------- | :------------------------- |
| WantedBy   | 表示该服务所在的Target | WantedBy=multi-user.target |
| RequiredBy |                        |                            |


## 四、命令


#### list-units
列出正在运行的 Unit

```bash
# 列出所有Unit，包括没有找到配置文件的或者启动失败的
systemctl list-units --all

# 列出所有没有运行的 Unit
systemctl list-units --all --state=inactive

# 列出所有加载失败的 Unit
systemctl list-units --failed

# 列出所有正在运行的、类型为 service 的 Unit
systemctl list-units --type=service

```


#### reload
```bash
# 重新加载一个服务的配置文件
systemctl reload apache.service
```


#### daemon-reload

```bash
# 重载所有修改过的配置文件
systemctl daemon-reload
```

#### enable
Target 的含义是服务组，如 WantedBy=multi-user.target 指的是该服务所属于 multi-user.target。当执行 systemctl enable xxx.service 命令时，xxx.service 的符号链接就会被创建在 /etc/systemd/system/multi-user.target 目录下

#### disable

#### cat
`[root@node83 /data/coredump] $ systemctl cat configuration`

```systemd
# /usr/lib/systemd/system/configuration.service
[Unit]
Description=zookeeper
After=network.target

[Service]
#Type=simple
Type=forking
#Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
User=mysql
Group=mysql

ExecStart=/data/app/zookeeper-3.6.2/bin/zkServer.sh start
ExecStop=/data/app/zookeeper-3.6.2/bin/zkServer.sh stop
PIDFile=/data/zookeeperdata/data/zookeeper_server.pid
KillMode=process

Restart=always
RestartSec=10
StartLimitInterval=10
StartLimitBurst=6
RestartPreventExitStatus=SIGTERM SIGKILL

[Install]
WantedBy=multi-user.target

```

#### status

```bash
# 显示系统状态
systemctl status

# 显示单个 Unit 的状态
systemctl status bluetooth.service

# 显示远程主机的某个 Unit 的状态
systemctl -H root@rhel7.example.com status httpd.service


# 显示某个 Unit 是否正在运行
systemctl is-active application.service

# 显示某个 Unit 是否处于启动失败状态
systemctl is-failed application.service

# 显示某个 Unit 服务是否建立了启动链接
systemctl is-enabled application.service
```


#### start


#### restart


#### stop


#### kill

```bash
# 杀死一个服务的所有子进程
systemctl kill apache.service
```



#### list-unit-files

```bash
# 列出所有配置文件
systemctl list-unit-files
```






## 五、日志管理



Systemd 统一管理所有 Unit 的启动日志。带来的好处就是，可以只用journalctl一个命令，查看所有日志（内核日志和应用日志）。日志的配置文件是/etc/systemd/journald.conf。

journalctl功能强大，用法非常多。


```bash
# 查看所有日志（默认情况下 ，只保存本次启动的日志）
journalctl

# 查看内核日志（不显示应用日志）
journalctl -k

# 查看系统本次启动的日志
journalctl -b
journalctl -b -0

# 查看上一次启动的日志（需更改设置）
journalctl -b -1

# 查看指定时间的日志
journalctl --since="2012-10-30 18:17:16"
journalctl --since "20 min ago"
journalctl --since yesterday
journalctl --since "2015-01-10" --until "2015-01-11 03:00"
journalctl --since 09:00 --until "1 hour ago"

# 显示尾部的最新10行日志
journalctl -n

# 显示尾部指定行数的日志
journalctl -n 20

# 实时滚动显示最新日志
journalctl -f

# 查看指定服务的日志
journalctl /usr/lib/systemd/systemd

# 查看指定进程的日志
journalctl _PID=1

# 查看某个路径的脚本的日志
journalctl /usr/bin/bash

# 查看指定用户的日志
journalctl _UID=33 --since today

# 查看某个 Unit 的日志
journalctl -u nginx.service
journalctl -u nginx.service --since today

# 实时滚动显示某个 Unit 的最新日志
journalctl -u nginx.service -f

# 合并显示多个 Unit 的日志
journalctl -u nginx.service -u php-fpm.service --since today

# 查看指定优先级（及其以上级别）的日志，共有8级
# 0: emerg
# 1: alert
# 2: crit
# 3: err
# 4: warning
# 5: notice
# 6: info
# 7: debug
journalctl -p err -b

# 日志默认分页输出，--no-pager 改为正常的标准输出
journalctl --no-pager

# 以 JSON 格式（单行）输出
journalctl -b -u nginx.service -o json

# 以 JSON 格式（多行）输出，可读性更好
journalctl -b -u nginx.serviceqq
 -o json-pretty

# 显示日志占据的硬盘空间
journalctl --disk-usage

# 指定日志文件占据的最大空间
journalctl --vacuum-size=1G

# 指定日志文件保存多久
journalctl --vacuum-time=1years
```






## 六、示例



#### redis
> `Type=forking`

- redis.conf
```toml
# 守护进程，修改为yes后即可后台运行
daemonize yes
bind 0.0.0.0
protected-mode no
requirepass 123456

```

- `redis.service`
```systemd
[Unit]
Description=redis-server
After=network.target
[Service]
Type=forking
ExecStart= /usr/local/bin/redis-server /usr/local/redis/redis.conf
PrivateTmp=true
[Install]
WantedBy=multi-user.target
```


#### mysql

- [https://www.percona.com/blog/how-to-use-systemd-in-linux-to-configure-and-manage-multiple-mysql-instances/](https://www.percona.com/blog/how-to-use-systemd-in-linux-to-configure-and-manage-multiple-mysql-instances/)
- [https://blog.csdn.net/n88Lpo/article/details/118382331](https://blog.csdn.net/n88Lpo/article/details/118382331)
- [https://www.jianshu.com/p/bfc98d1e6173](https://www.jianshu.com/p/bfc98d1e6173)


> `[root@k8s-node-71 ~]# cat /usr/lib/systemd/system/mysqld_3333.service`



- mysql官方

```systemd
# Copyright (c) 2017, 2022, Oracle and/or its affiliates.
#
# This program is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License, version 2.0,
# as published by the Free Software Foundation.
#
# This program is also distributed with certain software (including
# but not limited to OpenSSL) that is licensed under separate terms,
# as designated in a particular file or component or in included license
# documentation.  The authors of MySQL hereby grant you an additional
# permission to link the program and your derivative works with the
# separately licensed software that they have included with MySQL.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License, version 2.0, for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program; if not, write to the Free Software
# Foundation, Inc., 51 Franklin St, Fifth Floor, Boston, MA 02110-1301  USA

# MySQL systemd service file

[Unit]
Description=MySQL @DEB_PRODUCTNAMEC@ Server
Documentation=man:mysqld(8)
Documentation=http://dev.mysql.com/doc/refman/en/using-systemd.html
After=network.target

[Install]
WantedBy=multi-user.target

[Service]
User=mysql
Group=mysql
Type=notify
ExecStartPre=+/usr/share/mysql-@MYSQL_BASE_VERSION@/mysql-systemd-start pre @%I
ExecStart=/usr/sbin/mysqld --defaults-group-suffix=@%I
TimeoutSec=0
LimitNOFILE = 10000
Restart=on-failure
RestartPreventExitStatus=1

# Always restart when mysqld exits with exit code of 16. This special exit code
# is used by mysqld for RESTART SQL.
RestartForceExitStatus=16

# Set enviroment variable MYSQLD_PARENT_PID. This is required for restart.
Environment=MYSQLD_PARENT_PID=1
```
> MYSQLD_PARENT_PID=1，MySQL源码中会解析MYSQLD_PARENT_PID环境变量

**MYSQLD_PARENT_PID**解析：
```c++
bool is_mysqld_managed() {
#ifndef _WIN32
        char *parent_pid = getenv("MYSQLD_PARENT_PID");
        if (parent_pid == nullptr)
                return false;

        return atoi(parent_pid) == getppid();
#else
        return !(opt_debugging || opt_no_monitor);
#endif  // _WIN32
}


bool signal_restart_server() {
        if (!is_mysqld_managed()) {
                my_error(ER_RESTART_SERVER_FAILED, MYF(0), "mysqld is not managed by supervisor process");
                return true;
        }

#ifdef _WIN32
        if (!SetEvent(hEventRestart)) {
                LogErr(ERROR_LEVEL, ER_SET_EVENT_FAILED, GetLastError());
                my_error(ER_RESTART_SERVER_FAILED, MYF(0), "Internal operation failure");
                return true;
        }
#else

        if (pthread_kill(signal_thread_id.thread, SIGUSR2)) {
                DBUG_PRINT("error", ("Got error %d from pthread_kill", errno));
                my_error(ER_RESTART_SERVER_FAILED, MYF(0), "Internal operation failure");
                return true;
        }
#endif
        return false;
}









```


```systemd
# Copyright (c) 2015, 2022, Oracle and/or its affiliates.
#
# This program is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License, version 2.0,
# as published by the Free Software Foundation.
#
# This program is also distributed with certain software (including
# but not limited to OpenSSL) that is licensed under separate terms,
# as designated in a particular file or component or in included license
# documentation.  The authors of MySQL hereby grant you an additional
# permission to link the program and your derivative works with the
# separately licensed software that they have included with MySQL.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License, version 2.0, for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program; if not, write to the Free Software
# Foundation, Inc., 51 Franklin St, Fifth Floor, Boston, MA 02110-1301  USA

# MySQL systemd service file

[Unit]
Description=MySQL @DEB_PRODUCTNAMEC@ Server
Documentation=man:mysqld(8)
Documentation=http://dev.mysql.com/doc/refman/en/using-systemd.html
After=network.target

[Install]
WantedBy=multi-user.target

[Service]
User=mysql
Group=mysql
Type=notify
ExecStartPre=+/usr/share/mysql-@MYSQL_BASE_VERSION@/mysql-systemd-start pre
ExecStart=/usr/sbin/mysqld
TimeoutSec=0
LimitNOFILE = 10000
Restart=on-failure
RestartPreventExitStatus=1

# Always restart when mysqld exits with exit code of 16. This special exit code
# is used by mysqld for RESTART SQL.
RestartForceExitStatus=16

# Set enviroment variable MYSQLD_PARENT_PID. This is required for restart.
Environment=MYSQLD_PARENT_PID=1
```











- mysqld
`2024-06-29T22:07:11 [root@node77 /root] $ cat /usr/lib/systemd/system/mysql_16315.service`

`chmod 644 /usr/lib/systemd/system/mysql_16315.service`

```systemd
[Unit]
Description=MySQL Server
Documentation=http://dev.mysql.com/doc/refman/en/using-systemd.html
After=network.target
After=syslog.target

[Install]
WantedBy=multi-user.target

[Service]
User=mysql
Group=mysql
Type=forking
PIDFile=/data/mysql_3306/pid/mysqld.pid
TimeoutSec=0
PermissionsStartOnly=true
ExecStart=/data/mysql_3306/bin/mysqld --defaults-file=/data/mysql_3306/etc/my.cnf --daemonize $MYSQLD_OPTS
Restart=on-failure
RestartPreventExitStatus=1
Environment=MYSQLD_PARENT_PID=1
PrivateTmp=false
LimitNOFILE = 1024000
LimitNPROC = 1024000

```

- mysqld_safe
```systemd
[Unit]
Description=mysqld Server
After=network.target

[Install]
WantedBy=multi-user.target

[Service]
User=work
Group=work
Type=simple
Environment=
TimeoutSec=0
ExecStart=/bin/sh -c "/home/work/mysql_3333/bin/mysqld_safe --defaults-file=/home/work/mysql_3333/conf/my.cnf --user=work --datadir=/home/work/mysql_3333/data > /home/work/mysql_3333/log/out.log 2>&1"
LimitNOFILE = 1024000
LimitNPROC = 1024000
PrivateTmp=false

```

- `Type=simple`
```systemd
[Unit]
Description=MySQL Server
After=syslog.target
After=network.target

[Service]
Type=simple
PermissionsStartOnly=true
ExecStartPre=/bin/mkdir -p /var/run/mysqld
ExecStartPre=/bin/chown mysql:mysql -R /var/run/mysqld
ExecStart=/usr/sbin/mysqld --basedir=/usr --datadir=/var/lib/mysql --plugin-dir=/usr/lib/mysql/plugin --log-error=/var/log/mysql/error.log --pid-file=/var/run/mysqld/mysqld.pid --socket=/var/run/mysqld/mysqld.sock --port=3306
TimeoutSec=300
PrivateTmp=true
User=mysql
Group=mysql
WorkingDirectory=/usr

[Install]
WantedBy=multi-user.target
```


#### mysql_exporter
> 命令：`cat  /usr/lib/systemd/system/mysql_exporter.service`

输出：
```systemd
[Unit]
Description=mysql_exporter
After=network.target

[Service]
Type=simple
User=work
ExecStart=/home/work/bin/mysqld_exporter --config.my-cnf="/home/work/bin/db.cnf" --web.listen-address=":9105"
Restart=on-failure

[Install]
WantedBy=multi-user.target
```



#### nginx

{{<alert>}}

特点：
1. Type=forking
2. PIDFile=/run/nginx.pid，首先，PIDFile只适合在Type=forking模式下使用，其它时候没必要使用，因为其它类型的Service主进程的PID都是确定的。



{{</alert>}}

- `2024-06-23T09:43:37 [root@igoodful /root] $ systemctl cat nginx`

```systemd

# /usr/lib/systemd/system/nginx.service
[Unit]
Description=The nginx HTTP and reverse proxy server
After=network-online.target remote-fs.target nss-lookup.target
Wants=network-online.target

[Service]
Type=forking
PIDFile=/run/nginx.pid
# Nginx will fail to start if /run/nginx.pid already exists but has the wrong
# SELinux context. This might happen when running `nginx -t` from the cmdline.
# https://bugzilla.redhat.com/show_bug.cgi?id=1268621
ExecStartPre=/usr/bin/rm -f /run/nginx.pid
ExecStartPre=/usr/sbin/nginx -t
ExecStart=/usr/sbin/nginx
ExecReload=/usr/sbin/nginx -s reload
KillSignal=SIGQUIT
TimeoutStopSec=5
KillMode=process
PrivateTmp=true

[Install]
WantedBy=multi-user.target


```

- `2024-06-23T09:43:11 [root@igoodful /root] $ systemctl status nginx`
```bash

● nginx.service - The nginx HTTP and reverse proxy server
   Loaded: loaded (/usr/lib/systemd/system/nginx.service; enabled; vendor preset: disabled)
   Active: active (running) since Sun 2024-06-02 21:16:57 CST; 2 weeks 6 days ago
  Process: 20240 ExecStart=/usr/sbin/nginx (code=exited, status=0/SUCCESS)
  Process: 20238 ExecStartPre=/usr/sbin/nginx -t (code=exited, status=0/SUCCESS)
  Process: 20237 ExecStartPre=/usr/bin/rm -f /run/nginx.pid (code=exited, status=0/SUCCESS)
 Main PID: 20243 (nginx)
    Tasks: 2
   Memory: 67.2M
   CGroup: /system.slice/nginx.service
           ├─20243 nginx: master process /usr/sbin/nginx
           └─20245 nginx: worker process

Jun 02 21:16:57 igoodful systemd[1]: Stopped The nginx HTTP and reverse proxy server.
Jun 02 21:16:57 igoodful systemd[1]: Starting The nginx HTTP and reverse proxy server...
Jun 02 21:16:57 igoodful nginx[20238]: nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
Jun 02 21:16:57 igoodful nginx[20238]: nginx: configuration file /etc/nginx/nginx.conf test is successful
Jun 02 21:16:57 igoodful systemd[1]: Started The nginx HTTP and reverse proxy server.

```







#### minio
```systemd

[Unit]
Description=minio
Documentation=https://docs.min.io
Wants=network-online.target
After=network-online.target
AssertFileIsExecutable={{ minio_conf_dir }}/minio
AssertFileNotEmpty={{ minio_conf_dir }}/minio

[Service]
Type=notify
WorkingDirectory={{ minio_base_dir }}
User  =  {{ minio_install_user }}
Group =  {{ minio_install_group }}
ProtectProc=invisible

EnvironmentFile={{ minio_conf_dir }}/minio
ExecStartPre=/bin/bash -c "if [ -z \"${MINIO_VOLUMES}\" ]; then echo \"Variable MINIO_VOLUMES not set in /etc/default/minio\"; exit 1; fi"
ExecStart={{ minio_bin_dir }}/minio server $MINIO_OPTS $MINIO_VOLUMES

Restart=always
LimitNOFILE=1048576
TasksMax=infinity
TimeoutSec=infinity
SendSIGKILL=no

[Install]
WantedBy=multi-user.target

```











#### docker

```sql

[root@k8s-node-77 ~]# cat /etc/systemd/system/multi-user.target.wants/docker.service
[Unit]
Description=Docker Application Container Engine
Documentation=http://docs.docker.com
#After=network.target docker.socket flannel.service
#Requires=flannel.service

[Service]
Type=notify
#EnvironmentFile=-/run/flannel/docker
#WorkingDirectory=/usr/local/kubernetes/bin/
ExecStart=/usr/bin/dockerd -H tcp://localhost:4243 -H unix:///var/run/docker.sock --selinux-enabled=false --log-opt max-size=100m --log-opt max-file=3 --data-root /docker
ExecReload=/bin/kill -s HUP $MAINPID
# Having non-zero Limit*s causes performance problems due to accounting overhead
# in the kernel. We recommend using cgroups to do container-local accounting.
LimitNOFILE=infinity
LimitNPROC=infinity
LimitCORE=infinity
# Uncomment TasksMax if your systemd version supports it.
# Only systemd 226 and above support this version.
#TasksMax=infinity
TimeoutStartSec=0
# set delegate yes so that systemd does not reset the cgroups of docker containers
Delegate=yes
# kill only the docker process, not all processes in the cgroup
KillMode=process
Restart=on-failure
StartLimitBurst=3
StartLimitInterval=60s


[Install]
WantedBy=multi-user.target


```








## journalctl
- [https://www.jinbuguo.com/systemd/journalctl.html#](https://www.jinbuguo.com/systemd/journalctl.html#)


| 参数         | 含义                     | 说明                     |
| :----------- | :----------------------- | :----------------------- |
| `-a/--all`   | 完整显示所有字段内容     |                          |
| `-k/--dmesg` | 仅显示内核日志           | `journalctl -k -b -1`    |
| `-u/--unit`  | 仅显示属于特定单元的日志 | `journalctl -f -u nginx` |













```bash
journalctl -a


```



## 内核日志
显示上一次启动所产生的所有内核日志

```bash
journalctl -k -b -1

```







## 排查报错


刚刚发现两个主机的系统时间差的比较多
```bash
systemctl status adm-dbinit

journalctl -u adm-dbinit.service  --since "1 hour ago"

```











