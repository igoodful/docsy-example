---
title: glibc
description: glibc
date: 2023-11-06
weight: 30000
viz: true

---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{<alert>}}

1. **GCC 依赖 glibc：**
   - 当你使用 GCC 编译 C 或 C++ 代码时，编译器需要与系统上的 C 库（通常是 glibc）进行交互，因为它包含了与标准 C 库相关的函数和运行时支持。

2. **glibc 依赖 GCC：**
   - 在 glibc 的构建过程中，通常需要编译一些汇编代码和使用 GCC 提供的工具链。因此，在构建 glibc 时，你需要一个可用的 GCC 编译器。

总的来说，它们是相互依赖的，但这种依赖关系是通过编译器和 C 库之间的接口进行的，而不是直接依赖于源代码的方式。在很多情况下，系统中已经安装了默认的 GCC 和 glibc 版本，而不需要手动干预。

3. glibc有最低版本内核要求

- https://github.com/bminor/glibc/tags
- http://ftp.gnu.org/gnu/glibc
- https://sourceware.org/glibc/wiki/Testing/Builds#Building_glibc_with_intent_to_install
- 各个版本发布时间表： https://sourceware.org/glibc/wiki/Glibc%20Timeline
- https://www.cnblogs.com/caya-yuan/p/10561439.html
- 安装说明：https://sourceware.org/glibc/wiki/FAQ

**经过测试**：

若gcc-12.2.0安装后，再安装glibc-2.38是没问题的


{{</alert>}}



## 重大变更

- 在2018年发布的`glibc 2.27`中，对调用`dlopen`的静态链接程序的支持已被弃用。此举旨在简化对静态链接程序的支持，并避免用户因混合使用两种链接模型而产生的麻烦用例。用户应该选择静态或动态链接，选择最符合自身需求的模型，同时避免两者混合使用。但glibc内部继续使用dlopen来处理几个主要子系统，包括 NSS、gconv、IDN 和thread cancellation



## 运维


#### 查看glibc版本

- 在 64 位系统上，您可能需要使用 /lib64/libc.so.6
- 在 32 位系统上，您可能需要使用 /lib/libc.so.6

```bash
strings /lib64/libc.so.6 | grep '^GLIBC'



2025-04-15T14:23:58 [mysql@x86centos7-250 /root] $ /lib64/libc.so.6
GNU C Library (GNU libc) stable release version 2.17, by Roland McGrath et al.
Copyright (C) 2012 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.
There is NO warranty; not even for MERCHANTABILITY or FITNESS FOR A
PARTICULAR PURPOSE.
Compiled by GNU CC version 4.8.5 20150623 (Red Hat 4.8.5-44).
Compiled on a Linux 3.10.0 system on 2024-06-04.
Available extensions:
	The C stubs add-on version 2.1.2.
	crypt add-on version 2.1 by Michael Glad and others
	GNU Libidn by Simon Josefsson
	Native POSIX Threads Library by Ulrich Drepper et al
	BIND-8.2.3-T5B
	RT using linux kernel aio
libc ABIs: UNIQUE IFUNC
For bug reporting instructions, please see:
<http://www.gnu.org/software/libc/bugs.html>.


2025-04-15T14:21:08 [igoodfu@48 /db] $ /lib/libc.so.6
GNU C Library (GNU libc) stable release version 2.17, by Roland McGrath et al.
Copyright (C) 2012 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.
There is NO warranty; not even for MERCHANTABILITY or FITNESS FOR A
PARTICULAR PURPOSE.
Compiled by GNU CC version 4.8.5 20150623 (Red Hat 4.8.5-44).
Compiled on a Linux 3.10.0 system on 2021-02-08.
Available extensions:
	The C stubs add-on version 2.1.2.
	crypt add-on version 2.1 by Michael Glad and others
	GNU Libidn by Simon Josefsson
	Native POSIX Threads Library by Ulrich Drepper et al
	BIND-8.2.3-T5B
	RT using linux kernel aio
libc ABIs: UNIQUE IFUNC
For bug reporting instructions, please see:
<http://www.gnu.org/software/libc/bugs.html>.


2025-04-15T14:24:08 [mysql@x86centos7-250 /root] $ ls -l /lib*/libc.so.*
lrwxrwxrwx 1 root root 12 Apr 12 23:24 /lib64/libc.so.6 -> libc-2.17.so

```



## 0. 系统默认内核与glibc


| centos | kernel                | glibc | gcc   |
|:-------|:----------------------|:------|:------|
| 7      | 3.10.0-957.el7.x86_64 | 2.17  | 4.8.5 |
| 8      |                       | 2.28  |       |
|        |                       |       |       |
|        |                       |       |       |



#### glibc-2.28/INSTALL
源码中的INSTALL文件必须看，有各个依赖的版本要求

- https://www.cnblogs.com/caya-yuan/p/10561439.html

| software | version_min | version_max | 备注 |
|:---------|:------------|:------------|:-----|
| make     | 4.0         | 4.2.1       |      |
| gcc      | 4.9         | 8.1.1       |      |
| binutils | 2.25        | 2.31.1      |      |
| texinfo  | 4.7         | 6.5         |      |
| awk      | 3.1.2       | 4.2.1       |      |
| bison    | 2.7         | 3.0.4       |      |
| perl     | 5.0         | 5.28.0      |      |
| sed      | 3.02        | 4.5         |      |
| autoconf | 2.69        | 2.69        |      |
| gettext  | 0.10.36     | 0.19.8.1    |      |







## 1. 依赖

虽然没有直接的依赖包，但是依赖构建包程序

- 安装前检查gcc和glibc的发布时间，一定不能出现gcc比glibc早，即至少要保证gcc版本发布时间早于glibc版本发布
  - https://ftp.gnu.org/gnu/glibc/
  - https://ftp.gnu.org/gnu/gcc/


```viz-dot
digraph "glibc" {
node [
        fontsize = "12"
];
    rankdir="LR";
    "glibc" [ label = "glibc", shape = octagon ];
    "gcc" [ label = "gcc", shape = octagon ];
    "kernel headhers" [ label = "kernel headhers", shape = octagon ];
    "binutils" [ label = "binutils", shape = octagon ];
    "make" [ label = "make", shape = octagon ];
    "perl" [ label = "perl", shape = octagon ];
    "awk" [ label = "awk", shape = octagon ];
    "sed" [ label = "sed", shape = octagon ];
    "Python" [ label = "Python", shape = octagon ];
    "gperf" [ label = "gperf", shape = octagon ];
    "autoconf" [ label = "autoconf", shape = octagon ];
    "gettext" [ label = "gettext", shape = octagon ];
    "texinfo" [ label = "texinfo", shape = octagon ];


    "glibc" -> "gcc";
    "glibc" -> "kernel headhers";
    "glibc" -> "binutils";
    "glibc" -> "make";
    "glibc" -> "perl";
    "glibc" -> "awk";
    "glibc" -> "sed";
    "glibc" -> "Python";
    "glibc" -> "gperf";
    "glibc" -> "autoconf";
    "glibc" -> "gettext";
    "glibc" -> "texinfo";


}
```

Developers that modify glibc might need additionally:

- gperf
- autoconf
- gettext
- texinfo








## 2. 内容

基于`glibc-2.28`源码安装后的内容


```viz-dot
digraph "glibc" {
node [
        fontsize = "12"
];
    "glibc" [ label = "glibc", shape = octagon ];
    "sbin" [ label = "sbin", shape = octagon ];
    "bin" [ label = "bin", shape = octagon ];
    "lib" [ label = "lib", shape = octagon ];
    "include" [ label = "include", shape = octagon ];
    "etc" [ label = "etc", shape = octagon ];
    "libexec" [ label = "libexec", shape = octagon ];
    "share" [ label = "share", shape = octagon ];
    "var" [ label = "var", shape = octagon ];


    "glibc" -> "sbin";
    "glibc" -> "bin";
    "glibc" -> "lib";
    "glibc" -> "include";
     "glibc" -> "etc";
    "glibc" -> "libexec";
    "glibc" -> "share";
    "glibc" -> "var";

}
```




```bash
2025-03-26T14:30:07 [mysql@RDS-146 /glc/glibc/app/glibc-2.28] $ ll
total 32
drwxrwxr-x.  2 mysql mysql 4096 2025-03-26T13:06:49 bin
drwxrwxr-x.  2 mysql mysql 4096 2025-03-26T13:06:49 etc
drwxrwxr-x. 21 mysql mysql 4096 2025-03-26T13:06:47 include
drwxrwxr-x.  4 mysql mysql 4096 2025-03-26T13:06:49 lib
drwxrwxr-x.  3 mysql mysql 4096 2025-03-26T13:06:11 libexec
drwxrwxr-x.  2 mysql mysql 4096 2025-03-26T13:06:49 sbin
drwxrwxr-x.  5 mysql mysql 4096 2025-03-26T13:06:38 share
drwxrwxr-x.  3 mysql mysql 4096 2025-03-26T13:06:45 var

```






#### 2.1 sbin


```viz-dot
digraph "glibc" {
node [
        fontsize = "12"
];
    "glibc" [ label = "glibc", shape = octagon ];
    "ldconfig" [ label = "ldconfig", shape = octagon ];
    "iconvconfig" [ label = "iconvconfig", shape = octagon ];
    "nscd" [ label = "nscd", shape = octagon ];
    "sln" [ label = "sln", shape = octagon ];
    "zdump" [ label = "zdump", shape = octagon ];
    "zic" [ label = "zic", shape = octagon ];

    "glibc" -> "ldconfig";
    "glibc" -> "iconvconfig";
    "glibc" -> "nscd";
    "glibc" -> "sln";
    "glibc" -> "zdump";
    "glibc" -> "zic";
}
```


```bash
2025-03-26T14:06:58 [mysql@RDS-146 /glc/glibc/app/glibc-2.28] $ tree sbin
sbin
├── iconvconfig
├── ldconfig
├── nscd
├── sln
├── zdump
└── zic

0 directories, 6 files

```




#### 2.2 bin

```viz-dot
digraph "glibc" {
node [
        fontsize = "12"
];
    rankdir="LR";
    "glibc" [ label = "glibc", shape = octagon ];
    "catchsegv" [ label = "catchsegv", shape = octagon ];
    "gencat" [ label = "gencat", shape = octagon ];
    "getconf" [ label = "getconf", shape = octagon ];
    "getent" [ label = "getent", shape = octagon ];
    "iconv" [ label = "iconv", shape = octagon ];
    "ldd" [ label = "ldd", shape = octagon ];
    "locale" [ label = "locale", shape = octagon ];
    "localedef" [ label = "localedef", shape = octagon ];
    "makedb" [ label = "makedb", shape = octagon ];
    "mtrace" [ label = "mtrace", shape = octagon ];
    "pcprofiledump" [ label = "pcprofiledump", shape = octagon ];
    "pldd" [ label = "pldd", shape = octagon ];
    "sotruss" [ label = "sotruss", shape = octagon ];
    "sprof" [ label = "sprof", shape = octagon ];
    "tzselect" [ label = "tzselect", shape = octagon ];
    "xtrace" [ label = "xtrace", shape = octagon ];

    "glibc" -> "catchsegv";
    "glibc" -> "gencat";
    "glibc" -> "getconf";
    "glibc" -> "getent";
    "glibc" -> "iconv";
    "glibc" -> "ldd";
    "glibc" -> "locale";
    "glibc" -> "localedef";
    "glibc" -> "makedb";
    "glibc" -> "mtrace";
    "glibc" -> "pcprofiledump";
    "glibc" -> "pldd";
    "glibc" -> "sotruss";
    "glibc" -> "sprof";
    "glibc" -> "tzselect";
    "glibc" -> "xtrace";

}
```

-








```bash
2025-03-26T14:07:02 [mysql@RDS-146 /glc/glibc/app/glibc-2.28] $ tree bin
bin
├── catchsegv
├── gencat
├── getconf
├── getent
├── iconv
├── ldd
├── locale
├── localedef
├── makedb
├── mtrace
├── pcprofiledump
├── pldd
├── sotruss
├── sprof
├── tzselect
└── xtrace

0 directories, 16 files

```









#### 2.3 lib

动态库

- ld-2.28.so
- libanl-2.28.so
- libBrokenLocale-2.28.so
- libc-2.28.so
- libcrypt-2.28.so
- libc.so
- libdl-2.28.so
- libm-2.28.so
- libmemusage.so
- libm.so
- libmvec-2.28.so
- libnsl-2.28.so
- libnss_compat-2.28.so
- libnss_db-2.28.so
- libnss_dns-2.28.so
- libnss_files-2.28.so
- libnss_hesiod-2.28.so
- libpcprofile.so
- libpthread-2.28.so
- libresolv-2.28.so
- libresolv.a
- librt-2.28.so
- libSegFault.so
- libthread_db-1.0.so
- libutil-2.28.so
- `audit/sotruss-lib.so`
- `gconv/*`

静态库


- libanl.a
- libBrokenLocale.a
- libc.a
- libc_nonshared.a
- libcrypt.a
- libdl.a
- libg.a
- libm-2.28.a
- libm.a
- libmcheck.a
- libmvec.a
- libmvec_nonshared.a
- libpthread.a
- libresolv.a
- librt.a
- libutil.a






#### 2.4 include


```bash
2025-03-26T14:27:30 [mysql@RDS-146 /glc/glibc/app/glibc-2.28] $ tree include/
include/
├── aio.h
├── aliases.h
├── alloca.h
├── a.out.h
├── argp.h
├── argz.h
├── ar.h
├── arpa
│   ├── ftp.h
│   ├── inet.h
│   ├── nameser_compat.h
│   ├── nameser.h
│   ├── telnet.h
│   └── tftp.h
├── assert.h
├── bits
│   ├── a.out.h
│   ├── byteswap.h
│   ├── cmathcalls.h
│   ├── confname.h
│   ├── cpu-set.h
│   ├── dirent.h
│   ├── dlfcn.h
│   ├── elfclass.h
│   ├── endian.h
│   ├── environments.h
│   ├── epoll.h
│   ├── errno.h
│   ├── error.h
│   ├── eventfd.h
│   ├── fcntl2.h
│   ├── fcntl.h
│   ├── fcntl-linux.h
│   ├── fenv.h
│   ├── fenvinline.h
│   ├── floatn-common.h
│   ├── floatn.h
│   ├── flt-eval-method.h
│   ├── fp-fast.h
│   ├── fp-logb.h
│   ├── getopt_core.h
│   ├── getopt_ext.h
│   ├── getopt_posix.h
│   ├── hwcap.h
│   ├── indirect-return.h
│   ├── in.h
│   ├── initspin.h
│   ├── inotify.h
│   ├── ioctls.h
│   ├── ioctl-types.h
│   ├── ipc.h
│   ├── ipctypes.h
│   ├── iscanonical.h
│   ├── libc-header-start.h
│   ├── libm-simd-decl-stubs.h
│   ├── link.h
│   ├── locale.h
│   ├── local_lim.h
│   ├── long-double.h
│   ├── mathcalls.h
│   ├── mathcalls-helper-functions.h
│   ├── mathcalls-narrow.h
│   ├── mathdef.h
│   ├── math-finite.h
│   ├── mathinline.h
│   ├── math-vector.h
│   ├── mman.h
│   ├── mman-linux.h
│   ├── mman-shared.h
│   ├── monetary-ldbl.h
│   ├── mqueue2.h
│   ├── mqueue.h
│   ├── msq.h
│   ├── netdb.h
│   ├── param.h
│   ├── poll2.h
│   ├── poll.h
│   ├── posix1_lim.h
│   ├── posix2_lim.h
│   ├── posix_opt.h
│   ├── printf-ldbl.h
│   ├── pthreadtypes-arch.h
│   ├── pthreadtypes.h
│   ├── ptrace-shared.h
│   ├── resource.h
│   ├── sched.h
│   ├── select2.h
│   ├── select.h
│   ├── semaphore.h
│   ├── sem.h
│   ├── setjmp2.h
│   ├── setjmp.h
│   ├── shm.h
│   ├── sigaction.h
│   ├── sigcontext.h
│   ├── sigevent-consts.h
│   ├── siginfo-arch.h
│   ├── siginfo-consts-arch.h
│   ├── siginfo-consts.h
│   ├── signalfd.h
│   ├── signum-generic.h
│   ├── signum.h
│   ├── sigstack.h
│   ├── sigthread.h
│   ├── sockaddr.h
│   ├── socket2.h
│   ├── socket.h
│   ├── socket_type.h
│   ├── ss_flags.h
│   ├── stab.def
│   ├── statfs.h
│   ├── stat.h
│   ├── statvfs.h
│   ├── statx.h
│   ├── stdint-intn.h
│   ├── stdint-uintn.h
│   ├── stdio2.h
│   ├── stdio.h
│   ├── stdio-ldbl.h
│   ├── stdio_lim.h
│   ├── stdlib-bsearch.h
│   ├── stdlib-float.h
│   ├── stdlib.h
│   ├── stdlib-ldbl.h
│   ├── string_fortified.h
│   ├── strings_fortified.h
│   ├── stropts.h
│   ├── syscall.h
│   ├── sysctl.h
│   ├── sys_errlist.h
│   ├── syslog.h
│   ├── syslog-ldbl.h
│   ├── syslog-path.h
│   ├── sysmacros.h
│   ├── termios.h
│   ├── thread-shared-types.h
│   ├── time.h
│   ├── timerfd.h
│   ├── timex.h
│   ├── types
│   │   ├── clockid_t.h
│   │   ├── clock_t.h
│   │   ├── cookie_io_functions_t.h
│   │   ├── error_t.h
│   │   ├── __FILE.h
│   │   ├── FILE.h
│   │   ├── __fpos64_t.h
│   │   ├── __fpos_t.h
│   │   ├── __locale_t.h
│   │   ├── locale_t.h
│   │   ├── __mbstate_t.h
│   │   ├── mbstate_t.h
│   │   ├── res_state.h
│   │   ├── sig_atomic_t.h
│   │   ├── sigevent_t.h
│   │   ├── siginfo_t.h
│   │   ├── __sigset_t.h
│   │   ├── sigset_t.h
│   │   ├── __sigval_t.h
│   │   ├── sigval_t.h
│   │   ├── stack_t.h
│   │   ├── struct_FILE.h
│   │   ├── struct_iovec.h
│   │   ├── struct_itimerspec.h
│   │   ├── struct_osockaddr.h
│   │   ├── struct_rusage.h
│   │   ├── struct_sched_param.h
│   │   ├── struct_sigstack.h
│   │   ├── struct_timespec.h
│   │   ├── struct_timeval.h
│   │   ├── struct_tm.h
│   │   ├── timer_t.h
│   │   ├── time_t.h
│   │   └── wint_t.h
│   ├── types.h
│   ├── typesizes.h
│   ├── uintn-identity.h
│   ├── uio-ext.h
│   ├── uio_lim.h
│   ├── unistd.h
│   ├── utmp.h
│   ├── utmpx.h
│   ├── utsname.h
│   ├── waitflags.h
│   ├── waitstatus.h
│   ├── wchar2.h
│   ├── wchar.h
│   ├── wchar-ldbl.h
│   ├── wctype-wchar.h
│   ├── wordsize.h
│   ├── xopen_lim.h
│   └── xtitypes.h
├── byteswap.h
├── complex.h
├── cpio.h
├── crypt.h
├── ctype.h
├── dirent.h
├── dlfcn.h
├── elf.h
├── endian.h
├── envz.h
├── err.h
├── errno.h
├── error.h
├── execinfo.h
├── fcntl.h
├── features.h
├── fenv.h
├── fmtmsg.h
├── fnmatch.h
├── fpu_control.h
├── fstab.h
├── fts.h
├── ftw.h
├── gconv.h
├── getopt.h
├── glob.h
├── gnu
│   ├── libc-version.h
│   ├── lib-names-64.h
│   ├── lib-names.h
│   ├── stubs-64.h
│   └── stubs.h
├── gnu-versions.h
├── grp.h
├── gshadow.h
├── iconv.h
├── ieee754.h
├── ifaddrs.h
├── inttypes.h
├── langinfo.h
├── lastlog.h
├── libgen.h
├── libintl.h
├── limits.h
├── link.h
├── locale.h
├── malloc.h
├── math.h
├── mcheck.h
├── memory.h
├── mntent.h
├── monetary.h
├── mqueue.h
├── net
│   ├── ethernet.h
│   ├── if_arp.h
│   ├── if.h
│   ├── if_packet.h
│   ├── if_ppp.h
│   ├── if_shaper.h
│   ├── if_slip.h
│   ├── ppp-comp.h
│   ├── ppp_defs.h
│   └── route.h
├── netash
│   └── ash.h
├── netatalk
│   └── at.h
├── netax25
│   └── ax25.h
├── netdb.h
├── neteconet
│   └── ec.h
├── netinet
│   ├── ether.h
│   ├── icmp6.h
│   ├── if_ether.h
│   ├── if_fddi.h
│   ├── if_tr.h
│   ├── igmp.h
│   ├── in.h
│   ├── in_systm.h
│   ├── ip6.h
│   ├── ip.h
│   ├── ip_icmp.h
│   ├── tcp.h
│   └── udp.h
├── netipx
│   └── ipx.h
├── netiucv
│   └── iucv.h
├── netpacket
│   └── packet.h
├── netrom
│   └── netrom.h
├── netrose
│   └── rose.h
├── nfs
│   └── nfs.h
├── nl_types.h
├── nss.h
├── obstack.h
├── paths.h
├── poll.h
├── printf.h
├── proc_service.h
├── protocols
│   ├── routed.h
│   ├── rwhod.h
│   ├── talkd.h
│   └── timed.h
├── pthread.h
├── pty.h
├── pwd.h
├── re_comp.h
├── regex.h
├── regexp.h
├── resolv.h
├── rpc
│   └── netdb.h
├── sched.h
├── scsi
│   ├── scsi.h
│   ├── scsi_ioctl.h
│   └── sg.h
├── search.h
├── semaphore.h
├── setjmp.h
├── sgtty.h
├── shadow.h
├── signal.h
├── spawn.h
├── stab.h
├── stdc-predef.h
├── stdint.h
├── stdio_ext.h
├── stdio.h
├── stdlib.h
├── string.h
├── strings.h
├── stropts.h
├── sys
│   ├── acct.h
│   ├── auxv.h
│   ├── bitypes.h
│   ├── cdefs.h
│   ├── debugreg.h
│   ├── dir.h
│   ├── elf.h
│   ├── epoll.h
│   ├── errno.h
│   ├── eventfd.h
│   ├── fanotify.h
│   ├── fcntl.h
│   ├── file.h
│   ├── fsuid.h
│   ├── gmon.h
│   ├── gmon_out.h
│   ├── inotify.h
│   ├── ioctl.h
│   ├── io.h
│   ├── ipc.h
│   ├── kd.h
│   ├── klog.h
│   ├── mman.h
│   ├── mount.h
│   ├── msg.h
│   ├── mtio.h
│   ├── param.h
│   ├── pci.h
│   ├── perm.h
│   ├── personality.h
│   ├── poll.h
│   ├── prctl.h
│   ├── procfs.h
│   ├── profil.h
│   ├── ptrace.h
│   ├── queue.h
│   ├── quota.h
│   ├── random.h
│   ├── raw.h
│   ├── reboot.h
│   ├── reg.h
│   ├── resource.h
│   ├── select.h
│   ├── sem.h
│   ├── sendfile.h
│   ├── shm.h
│   ├── signalfd.h
│   ├── signal.h
│   ├── socket.h
│   ├── socketvar.h
│   ├── soundcard.h
│   ├── statfs.h
│   ├── stat.h
│   ├── statvfs.h
│   ├── stropts.h
│   ├── swap.h
│   ├── syscall.h
│   ├── sysctl.h
│   ├── sysinfo.h
│   ├── syslog.h
│   ├── sysmacros.h
│   ├── termios.h
│   ├── timeb.h
│   ├── time.h
│   ├── timerfd.h
│   ├── times.h
│   ├── timex.h
│   ├── ttychars.h
│   ├── ttydefaults.h
│   ├── types.h
│   ├── ucontext.h
│   ├── uio.h
│   ├── un.h
│   ├── unistd.h
│   ├── user.h
│   ├── utsname.h
│   ├── vfs.h
│   ├── vlimit.h
│   ├── vm86.h
│   ├── vt.h
│   ├── vtimes.h
│   ├── wait.h
│   └── xattr.h
├── syscall.h
├── sysexits.h
├── syslog.h
├── tar.h
├── termio.h
├── termios.h
├── tgmath.h
├── thread_db.h
├── threads.h
├── time.h
├── ttyent.h
├── uchar.h
├── ucontext.h
├── ulimit.h
├── unistd.h
├── utime.h
├── utmp.h
├── utmpx.h
├── values.h
├── wait.h
├── wchar.h
├── wctype.h
└── wordexp.h

20 directories, 420 files

```












#### 2.5 etc libexec var

```bash
2025-03-26T14:24:59 [mysql@RDS-146 /glc/glibc/app/glibc-2.28] $ tree etc
etc
├── ld.so.cache
└── rpc

0 directories, 2 files
2025-03-26T14:25:05 [mysql@RDS-146 /glc/glibc/app/glibc-2.28] $ tree libexec
libexec
└── getconf
    ├── POSIX_V6_LP64_OFF64
    ├── POSIX_V7_LP64_OFF64
    └── XBS5_LP64_OFF64

1 directory, 3 files
2025-03-26T14:25:20 [mysql@RDS-146 /glc/glibc/app/glibc-2.28] $ tree var
var
└── db
    └── Makefile

1 directory, 1 file
```


#### 2.6 share

```bash

2025-03-26T14:25:27 [mysql@RDS-146 /glc/glibc/app/glibc-2.28] $ tree share
share
├── i18n
│   ├── charmaps
│   │   ├── ANSI_X3.110-1983.gz
│   │   ├── ANSI_X3.4-1968.gz
│   │   ├── ARMSCII-8.gz
│   │   ├── ASMO_449.gz
│   │   ├── BIG5.gz
│   │   ├── BIG5-HKSCS.gz
│   │   ├── BRF.gz
│   │   ├── BS_4730.gz
│   │   ├── BS_VIEWDATA.gz
│   │   ├── CP10007.gz
│   │   ├── CP1125.gz
│   │   ├── CP1250.gz
│   │   ├── CP1251.gz
│   │   ├── CP1252.gz
│   │   ├── CP1253.gz
│   │   ├── CP1254.gz
│   │   ├── CP1255.gz
│   │   ├── CP1256.gz
│   │   ├── CP1257.gz
│   │   ├── CP1258.gz
│   │   ├── CP737.gz
│   │   ├── CP770.gz
│   │   ├── CP771.gz
│   │   ├── CP772.gz
│   │   ├── CP773.gz
│   │   ├── CP774.gz
│   │   ├── CP775.gz
│   │   ├── CP949.gz
│   │   ├── CSA_Z243.4-1985-1.gz
│   │   ├── CSA_Z243.4-1985-2.gz
│   │   ├── CSA_Z243.4-1985-GR.gz
│   │   ├── CSN_369103.gz
│   │   ├── CWI.gz
│   │   ├── DEC-MCS.gz
│   │   ├── DIN_66003.gz
│   │   ├── DS_2089.gz
│   │   ├── EBCDIC-AT-DE-A.gz
│   │   ├── EBCDIC-AT-DE.gz
│   │   ├── EBCDIC-CA-FR.gz
│   │   ├── EBCDIC-DK-NO-A.gz
│   │   ├── EBCDIC-DK-NO.gz
│   │   ├── EBCDIC-ES-A.gz
│   │   ├── EBCDIC-ES.gz
│   │   ├── EBCDIC-ES-S.gz
│   │   ├── EBCDIC-FI-SE-A.gz
│   │   ├── EBCDIC-FI-SE.gz
│   │   ├── EBCDIC-FR.gz
│   │   ├── EBCDIC-IS-FRISS.gz
│   │   ├── EBCDIC-IT.gz
│   │   ├── EBCDIC-PT.gz
│   │   ├── EBCDIC-UK.gz
│   │   ├── EBCDIC-US.gz
│   │   ├── ECMA-CYRILLIC.gz
│   │   ├── ES2.gz
│   │   ├── ES.gz
│   │   ├── EUC-JISX0213.gz
│   │   ├── EUC-JP.gz
│   │   ├── EUC-JP-MS.gz
│   │   ├── EUC-KR.gz
│   │   ├── EUC-TW.gz
│   │   ├── GB18030.gz
│   │   ├── GB_1988-80.gz
│   │   ├── GB2312.gz
│   │   ├── GBK.gz
│   │   ├── GEORGIAN-ACADEMY.gz
│   │   ├── GEORGIAN-PS.gz
│   │   ├── GOST_19768-74.gz
│   │   ├── GREEK7.gz
│   │   ├── GREEK7-OLD.gz
│   │   ├── GREEK-CCITT.gz
│   │   ├── HP-GREEK8.gz
│   │   ├── HP-ROMAN8.gz
│   │   ├── HP-ROMAN9.gz
│   │   ├── HP-THAI8.gz
│   │   ├── HP-TURKISH8.gz
│   │   ├── IBM037.gz
│   │   ├── IBM038.gz
│   │   ├── IBM1004.gz
│   │   ├── IBM1026.gz
│   │   ├── IBM1047.gz
│   │   ├── IBM1124.gz
│   │   ├── IBM1129.gz
│   │   ├── IBM1132.gz
│   │   ├── IBM1133.gz
│   │   ├── IBM1160.gz
│   │   ├── IBM1161.gz
│   │   ├── IBM1162.gz
│   │   ├── IBM1163.gz
│   │   ├── IBM1164.gz
│   │   ├── IBM256.gz
│   │   ├── IBM273.gz
│   │   ├── IBM274.gz
│   │   ├── IBM275.gz
│   │   ├── IBM277.gz
│   │   ├── IBM278.gz
│   │   ├── IBM280.gz
│   │   ├── IBM281.gz
│   │   ├── IBM284.gz
│   │   ├── IBM285.gz
│   │   ├── IBM290.gz
│   │   ├── IBM297.gz
│   │   ├── IBM420.gz
│   │   ├── IBM423.gz
│   │   ├── IBM424.gz
│   │   ├── IBM437.gz
│   │   ├── IBM500.gz
│   │   ├── IBM850.gz
│   │   ├── IBM851.gz
│   │   ├── IBM852.gz
│   │   ├── IBM855.gz
│   │   ├── IBM856.gz
│   │   ├── IBM857.gz
│   │   ├── IBM858.gz
│   │   ├── IBM860.gz
│   │   ├── IBM861.gz
│   │   ├── IBM862.gz
│   │   ├── IBM863.gz
│   │   ├── IBM864.gz
│   │   ├── IBM865.gz
│   │   ├── IBM866.gz
│   │   ├── IBM866NAV.gz
│   │   ├── IBM868.gz
│   │   ├── IBM869.gz
│   │   ├── IBM870.gz
│   │   ├── IBM871.gz
│   │   ├── IBM874.gz
│   │   ├── IBM875.gz
│   │   ├── IBM880.gz
│   │   ├── IBM891.gz
│   │   ├── IBM903.gz
│   │   ├── IBM904.gz
│   │   ├── IBM905.gz
│   │   ├── IBM918.gz
│   │   ├── IBM922.gz
│   │   ├── IEC_P27-1.gz
│   │   ├── INIS-8.gz
│   │   ├── INIS-CYRILLIC.gz
│   │   ├── INIS.gz
│   │   ├── INVARIANT.gz
│   │   ├── ISIRI-3342.gz
│   │   ├── ISO_10367-BOX.gz
│   │   ├── ISO_10646.gz
│   │   ├── ISO_11548-1.gz
│   │   ├── ISO_2033-1983.gz
│   │   ├── ISO_5427-EXT.gz
│   │   ├── ISO_5427.gz
│   │   ├── ISO_5428.gz
│   │   ├── ISO_646.BASIC.gz
│   │   ├── ISO_646.IRV.gz
│   │   ├── ISO_6937-2-25.gz
│   │   ├── ISO_6937-2-ADD.gz
│   │   ├── ISO_6937.gz
│   │   ├── ISO-8859-10.gz
│   │   ├── ISO-8859-11.gz
│   │   ├── ISO-8859-13.gz
│   │   ├── ISO-8859-14.gz
│   │   ├── ISO-8859-15.gz
│   │   ├── ISO-8859-16.gz
│   │   ├── ISO_8859-1,GL.gz
│   │   ├── ISO-8859-1.gz
│   │   ├── ISO-8859-2.gz
│   │   ├── ISO-8859-3.gz
│   │   ├── ISO-8859-4.gz
│   │   ├── ISO-8859-5.gz
│   │   ├── ISO-8859-6.gz
│   │   ├── ISO-8859-7.gz
│   │   ├── ISO-8859-8.gz
│   │   ├── ISO-8859-9E.gz
│   │   ├── ISO-8859-9.gz
│   │   ├── ISO_8859-SUPP.gz
│   │   ├── ISO-IR-197.gz
│   │   ├── ISO-IR-209.gz
│   │   ├── ISO-IR-90.gz
│   │   ├── IT.gz
│   │   ├── JIS_C6220-1969-JP.gz
│   │   ├── JIS_C6220-1969-RO.gz
│   │   ├── JIS_C6229-1984-A.gz
│   │   ├── JIS_C6229-1984-B-ADD.gz
│   │   ├── JIS_C6229-1984-B.gz
│   │   ├── JIS_C6229-1984-HAND-ADD.gz
│   │   ├── JIS_C6229-1984-HAND.gz
│   │   ├── JIS_C6229-1984-KANA.gz
│   │   ├── JIS_X0201.gz
│   │   ├── JOHAB.gz
│   │   ├── JUS_I.B1.002.gz
│   │   ├── JUS_I.B1.003-MAC.gz
│   │   ├── JUS_I.B1.003-SERB.gz
│   │   ├── KOI-8.gz
│   │   ├── KOI8-R.gz
│   │   ├── KOI8-RU.gz
│   │   ├── KOI8-T.gz
│   │   ├── KOI8-U.gz
│   │   ├── KSC5636.gz
│   │   ├── LATIN-GREEK-1.gz
│   │   ├── LATIN-GREEK.gz
│   │   ├── MAC-CENTRALEUROPE.gz
│   │   ├── MAC-CYRILLIC.gz
│   │   ├── MACINTOSH.gz
│   │   ├── MAC-IS.gz
│   │   ├── MAC-SAMI.gz
│   │   ├── MAC-UK.gz
│   │   ├── MIK.gz
│   │   ├── MSZ_7795.3.gz
│   │   ├── NATS-DANO-ADD.gz
│   │   ├── NATS-DANO.gz
│   │   ├── NATS-SEFI-ADD.gz
│   │   ├── NATS-SEFI.gz
│   │   ├── NC_NC00-10.gz
│   │   ├── NEXTSTEP.gz
│   │   ├── NF_Z_62-010_1973.gz
│   │   ├── NF_Z_62-010.gz
│   │   ├── NS_4551-1.gz
│   │   ├── NS_4551-2.gz
│   │   ├── PT154.gz
│   │   ├── PT2.gz
│   │   ├── PT.gz
│   │   ├── RK1048.gz
│   │   ├── SAMI.gz
│   │   ├── SAMI-WS2.gz
│   │   ├── SEN_850200_B.gz
│   │   ├── SEN_850200_C.gz
│   │   ├── SHIFT_JIS.gz
│   │   ├── SHIFT_JISX0213.gz
│   │   ├── T.101-G2.gz
│   │   ├── T.61-7BIT.gz
│   │   ├── T.61-8BIT.gz
│   │   ├── TCVN5712-1.gz
│   │   ├── TIS-620.gz
│   │   ├── TSCII.gz
│   │   ├── UTF-8.gz
│   │   ├── VIDEOTEX-SUPPL.gz
│   │   ├── VISCII.gz
│   │   └── WINDOWS-31J.gz
│   └── locales
│       ├── aa_DJ
│       ├── aa_ER
│       ├── aa_ER@saaho
│       ├── aa_ET
│       ├── af_ZA
│       ├── agr_PE
│       ├── ak_GH
│       ├── am_ET
│       ├── an_ES
│       ├── anp_IN
│       ├── ar_AE
│       ├── ar_BH
│       ├── ar_DZ
│       ├── ar_EG
│       ├── ar_IN
│       ├── ar_IQ
│       ├── ar_JO
│       ├── ar_KW
│       ├── ar_LB
│       ├── ar_LY
│       ├── ar_MA
│       ├── ar_OM
│       ├── ar_QA
│       ├── ar_SA
│       ├── ar_SD
│       ├── ar_SS
│       ├── ar_SY
│       ├── ar_TN
│       ├── ar_YE
│       ├── as_IN
│       ├── ast_ES
│       ├── ayc_PE
│       ├── az_AZ
│       ├── az_IR
│       ├── be_BY
│       ├── be_BY@latin
│       ├── bem_ZM
│       ├── ber_DZ
│       ├── ber_MA
│       ├── bg_BG
│       ├── bhb_IN
│       ├── bho_IN
│       ├── bho_NP
│       ├── bi_VU
│       ├── bn_BD
│       ├── bn_IN
│       ├── bo_CN
│       ├── bo_IN
│       ├── br_FR
│       ├── br_FR@euro
│       ├── brx_IN
│       ├── bs_BA
│       ├── byn_ER
│       ├── ca_AD
│       ├── ca_ES
│       ├── ca_ES@euro
│       ├── ca_ES@valencia
│       ├── ca_FR
│       ├── ca_IT
│       ├── ce_RU
│       ├── chr_US
│       ├── cmn_TW
│       ├── cns11643_stroke
│       ├── crh_UA
│       ├── csb_PL
│       ├── cs_CZ
│       ├── cv_RU
│       ├── cy_GB
│       ├── da_DK
│       ├── de_AT
│       ├── de_AT@euro
│       ├── de_BE
│       ├── de_BE@euro
│       ├── de_CH
│       ├── de_DE
│       ├── de_DE@euro
│       ├── de_IT
│       ├── de_LI
│       ├── de_LU
│       ├── de_LU@euro
│       ├── doi_IN
│       ├── dsb_DE
│       ├── dv_MV
│       ├── dz_BT
│       ├── el_CY
│       ├── el_GR
│       ├── el_GR@euro
│       ├── en_AG
│       ├── en_AU
│       ├── en_BW
│       ├── en_CA
│       ├── en_DK
│       ├── en_GB
│       ├── en_HK
│       ├── en_IE
│       ├── en_IE@euro
│       ├── en_IL
│       ├── en_IN
│       ├── en_NG
│       ├── en_NZ
│       ├── en_PH
│       ├── en_SC
│       ├── en_SG
│       ├── en_US
│       ├── en_ZA
│       ├── en_ZM
│       ├── en_ZW
│       ├── eo
│       ├── es_AR
│       ├── es_BO
│       ├── es_CL
│       ├── es_CO
│       ├── es_CR
│       ├── es_CU
│       ├── es_DO
│       ├── es_EC
│       ├── es_ES
│       ├── es_ES@euro
│       ├── es_GT
│       ├── es_HN
│       ├── es_MX
│       ├── es_NI
│       ├── es_PA
│       ├── es_PE
│       ├── es_PR
│       ├── es_PY
│       ├── es_SV
│       ├── es_US
│       ├── es_UY
│       ├── es_VE
│       ├── et_EE
│       ├── eu_ES
│       ├── eu_ES@euro
│       ├── fa_IR
│       ├── ff_SN
│       ├── fi_FI
│       ├── fi_FI@euro
│       ├── fil_PH
│       ├── fo_FO
│       ├── fr_BE
│       ├── fr_BE@euro
│       ├── fr_CA
│       ├── fr_CH
│       ├── fr_FR
│       ├── fr_FR@euro
│       ├── fr_LU
│       ├── fr_LU@euro
│       ├── fur_IT
│       ├── fy_DE
│       ├── fy_NL
│       ├── ga_IE
│       ├── ga_IE@euro
│       ├── gd_GB
│       ├── gez_ER
│       ├── gez_ER@abegede
│       ├── gez_ET
│       ├── gez_ET@abegede
│       ├── gl_ES
│       ├── gl_ES@euro
│       ├── gu_IN
│       ├── gv_GB
│       ├── hak_TW
│       ├── ha_NG
│       ├── he_IL
│       ├── hif_FJ
│       ├── hi_IN
│       ├── hne_IN
│       ├── hr_HR
│       ├── hsb_DE
│       ├── ht_HT
│       ├── hu_HU
│       ├── hy_AM
│       ├── i18n
│       ├── i18n_ctype
│       ├── ia_FR
│       ├── id_ID
│       ├── ig_NG
│       ├── ik_CA
│       ├── is_IS
│       ├── iso14651_t1
│       ├── iso14651_t1_common
│       ├── iso14651_t1_pinyin
│       ├── it_CH
│       ├── it_IT
│       ├── it_IT@euro
│       ├── iu_CA
│       ├── ja_JP
│       ├── kab_DZ
│       ├── ka_GE
│       ├── kk_KZ
│       ├── kl_GL
│       ├── km_KH
│       ├── kn_IN
│       ├── kok_IN
│       ├── ko_KR
│       ├── ks_IN
│       ├── ks_IN@devanagari
│       ├── ku_TR
│       ├── kw_GB
│       ├── ky_KG
│       ├── lb_LU
│       ├── lg_UG
│       ├── li_BE
│       ├── lij_IT
│       ├── li_NL
│       ├── ln_CD
│       ├── lo_LA
│       ├── lt_LT
│       ├── lv_LV
│       ├── lzh_TW
│       ├── mag_IN
│       ├── mai_IN
│       ├── mai_NP
│       ├── mfe_MU
│       ├── mg_MG
│       ├── mhr_RU
│       ├── mi_NZ
│       ├── miq_NI
│       ├── mjw_IN
│       ├── mk_MK
│       ├── ml_IN
│       ├── mni_IN
│       ├── mn_MN
│       ├── mr_IN
│       ├── ms_MY
│       ├── mt_MT
│       ├── my_MM
│       ├── nan_TW
│       ├── nan_TW@latin
│       ├── nb_NO
│       ├── nds_DE
│       ├── nds_NL
│       ├── ne_NP
│       ├── nhn_MX
│       ├── niu_NU
│       ├── niu_NZ
│       ├── nl_AW
│       ├── nl_BE
│       ├── nl_BE@euro
│       ├── nl_NL
│       ├── nl_NL@euro
│       ├── nn_NO
│       ├── nr_ZA
│       ├── nso_ZA
│       ├── oc_FR
│       ├── om_ET
│       ├── om_KE
│       ├── or_IN
│       ├── os_RU
│       ├── pa_IN
│       ├── pap_AW
│       ├── pap_CW
│       ├── pa_PK
│       ├── pl_PL
│       ├── POSIX
│       ├── ps_AF
│       ├── pt_BR
│       ├── pt_PT
│       ├── pt_PT@euro
│       ├── quz_PE
│       ├── raj_IN
│       ├── ro_RO
│       ├── ru_RU
│       ├── ru_UA
│       ├── rw_RW
│       ├── sah_RU
│       ├── sa_IN
│       ├── sat_IN
│       ├── sc_IT
│       ├── sd_IN
│       ├── sd_IN@devanagari
│       ├── se_NO
│       ├── sgs_LT
│       ├── shn_MM
│       ├── shs_CA
│       ├── sid_ET
│       ├── si_LK
│       ├── sk_SK
│       ├── sl_SI
│       ├── sm_WS
│       ├── so_DJ
│       ├── so_ET
│       ├── so_KE
│       ├── so_SO
│       ├── sq_AL
│       ├── sq_MK
│       ├── sr_ME
│       ├── sr_RS
│       ├── sr_RS@latin
│       ├── ss_ZA
│       ├── st_ZA
│       ├── sv_FI
│       ├── sv_FI@euro
│       ├── sv_SE
│       ├── sw_KE
│       ├── sw_TZ
│       ├── szl_PL
│       ├── ta_IN
│       ├── ta_LK
│       ├── tcy_IN
│       ├── te_IN
│       ├── tg_TJ
│       ├── the_NP
│       ├── th_TH
│       ├── ti_ER
│       ├── ti_ET
│       ├── tig_ER
│       ├── tk_TM
│       ├── tl_PH
│       ├── tn_ZA
│       ├── to_TO
│       ├── tpi_PG
│       ├── translit_circle
│       ├── translit_cjk_compat
│       ├── translit_cjk_variants
│       ├── translit_combining
│       ├── translit_compat
│       ├── translit_font
│       ├── translit_fraction
│       ├── translit_hangul
│       ├── translit_narrow
│       ├── translit_neutral
│       ├── translit_small
│       ├── translit_wide
│       ├── tr_CY
│       ├── tr_TR
│       ├── ts_ZA
│       ├── tt_RU
│       ├── tt_RU@iqtelif
│       ├── ug_CN
│       ├── uk_UA
│       ├── unm_US
│       ├── ur_IN
│       ├── ur_PK
│       ├── uz_UZ
│       ├── uz_UZ@cyrillic
│       ├── ve_ZA
│       ├── vi_VN
│       ├── wa_BE
│       ├── wa_BE@euro
│       ├── wae_CH
│       ├── wal_ET
│       ├── wo_SN
│       ├── xh_ZA
│       ├── yi_US
│       ├── yo_NG
│       ├── yue_HK
│       ├── yuw_PG
│       ├── zh_CN
│       ├── zh_HK
│       ├── zh_SG
│       ├── zh_TW
│       └── zu_ZA
├── info
│   ├── dir
│   ├── libc.info
│   ├── libc.info-1
│   ├── libc.info-10
│   ├── libc.info-11
│   ├── libc.info-12
│   ├── libc.info-13
│   ├── libc.info-14
│   ├── libc.info-15
│   ├── libc.info-16
│   ├── libc.info-17
│   ├── libc.info-2
│   ├── libc.info-3
│   ├── libc.info-4
│   ├── libc.info-5
│   ├── libc.info-6
│   ├── libc.info-7
│   ├── libc.info-8
│   └── libc.info-9
└── locale
    ├── be
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── bg
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── ca
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── cs
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── da
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── de
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── el
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── en_GB
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── eo
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── es
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── fi
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── fr
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── gl
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── hr
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── hu
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── ia
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── id
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── it
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── ja
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── ko
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── locale.alias
    ├── lt
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── nb
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── nl
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── pl
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── pt_BR
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── ru
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── rw
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── sk
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── sl
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── sv
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── tr
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── uk
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── vi
    │   └── LC_MESSAGES
    │       └── libc.mo
    ├── zh_CN
    │   └── LC_MESSAGES
    │       └── libc.mo
    └── zh_TW
        └── LC_MESSAGES
            └── libc.mo

75 directories, 641 files

```







## 3. 安装



#### 3.1 源码安装
```bash
wget http://ftp.gnu.org/gnu/glibc/glibc-2.38.tar.gz
tar -xzvf glibc-2.38.tar.gz
mkdir build
cd build
../configure  --prefix=/usr --disable-profile --enable-add-ons --with-headers=/usr/include --with-binutils=/usr/bin && make && make check && make install

make install DESTDIR=${HOME}/local

make localedata/install-locales


```


#### 查看glibc版本
```bsh
strings /lib64/libc.so.6 |grep GLIBC_

```

## 4. 问题


### binutils

**问题如下**：
```bash
/root/local/bin/gcc -B/usr/bin/ ../sysdeps/x86_64/multiarch/memchr-sse2.S -c    -I../include -I/root/build_glibc/string  -I/root/build_glibc  -I../sysdeps/unix/sysv/linux/x86_64/64  -I../sysdeps/unix/sysv/linux/x86_64  -I../sysdeps/unix/sysv/linux/x86/include -I../sysdeps/unix/sysv/linux/x86  -I../sysdeps/x86/nptl  -I../sysdeps/unix/sysv/linux/wordsize-64  -I../sysdeps/x86_64/nptl  -I../sysdeps/unix/sysv/linux/include -I../sysdeps/unix/sysv/linux  -I../sysdeps/nptl  -I../sysdeps/pthread  -I../sysdeps/gnu  -I../sysdeps/unix/inet  -I../sysdeps/unix/sysv  -I../sysdeps/unix/x86_64  -I../sysdeps/unix  -I../sysdeps/posix  -I../sysdeps/x86_64/64  -I../sysdeps/x86_64/fpu/multiarch  -I../sysdeps/x86_64/fpu  -I../sysdeps/x86/fpu  -I../sysdeps/x86_64/multiarch  -I../sysdeps/x86_64  -I../sysdeps/x86/include -I../sysdeps/x86  -I../sysdeps/ieee754/float128  -I../sysdeps/ieee754/ldbl-96/include -I../sysdeps/ieee754/ldbl-96  -I../sysdeps/ieee754/dbl-64  -I../sysdeps/ieee754/flt-32  -I../sysdeps/wordsize-64  -I../sysdeps/ieee754  -I../sysdeps/generic  -I.. -I../libio -I. -nostdinc -isystem /root/local/lib/gcc/x86_64-pc-linux-gnu/12.2.0/include -isystem /root/local/lib/gcc/x86_64-pc-linux-gnu/12.2.0/include-fixed -isystem /usr/include -D_LIBC_REENTRANT -include /root/build_glibc/libc-modules.h -DMODULE_NAME=libc -include ../include/libc-symbols.h  -DPIC     -DTOP_NAMESPACE=glibc -DASSEMBLER  -g -Werror=undef -Wa,--noexecstack   -o /root/build_glibc/string/memchr-sse2.o -MD -MP -MF /root/build_glibc/string/memchr-sse2.o.dt -MT /root/build_glibc/string/memchr-sse2.o
../sysdeps/x86_64/multiarch/memchr-evex-base.S: Assembler messages:
../sysdeps/x86_64/multiarch/memchr-evex-base.S:229: Error: no such instruction: `vpcmpneqb (64 * 4)(%rdi),%zmm17,%k1'
make[2]: *** [/root/build_glibc/sysd-rules:653: /root/build_glibc/string/memchr-evex512.o] Error 1
make[2]: *** Waiting for unfinished jobs....
make[2]: Leaving directory '/root/glibc-2.38/string'
make[1]: *** [Makefile:484: string/subdir_lib] Error 2
make[1]: Leaving directory '/root/glibc-2.38'
make: *** [Makefile:9: all] Error 2

```
> 安装新版本binutils到/usr路径，先升级binutils，然后使用--with-binutils=/usr/bin指定binutils工具的bin路径





```sql
简要描述
catchsegv
当程序发生段故障的时候，用来建立一个堆栈跟踪

gencat
建立消息列表

getconf
针对文件系统的指定变量显示其系统设置值

getent
从系统管理数据库获取一个条目

iconv
字符集转换

iconvconfig
建立快速加载的 iconv 模块所使用的配置文件

ldconfig
配置动态链接库的实时绑定

ldd
列出每个程序或者命令需要的共享库

lddlibc4
帮助 ldd 操作目标文件

locale
告诉编译器为内建的操作启用或禁用 POSIX locales 支持[ Tells the compiler to enable or disable the use of POSIX locales for built-in operations ]

localedef
编译 locale 标准

mtrace
读取并解释一个内存跟踪文件然后以人类可读的格式显示一个摘要

nscd
为最常用的名称服务请求提供缓存的守护进程 [ A daemon that provides a cache for the most common name service requests ]

nscd_nischeck
检查在进行 NIS+ 查找时是否需要安全模式

pcprofiledump
转储 PC profiling 产生的信息

pt_chown
一个辅助程序，帮助 grantpt 设置子虚拟终端的属主、用户组、读写权限

rpcgen
产生实现远程过程调用(RPC)协议的 C 代码

rpcinfo
对 RPC 服务器产生一个 RPC 呼叫

sln
ln 程序使用静态连接编译的版本，在 ln 不起作用的时候，sln 仍然可以建立符号链接

sprof
读取并显示共享目标的特征描述数据

tzselect
对用户提出关于当前位置的问题并输出时区信息到标准输出

xtrace
通过打印当前执行的函数跟踪程序执行情况

zdump
显示时区

zic
时区编译器

ld.so
帮助动态链接库执行的辅助程序

libBrokenLocale
帮助应用程序(如Mozilla)处理破损的 locale

libSegFault
段故障信号处理器

libanl
异步名称查询库

libbsd-compat
为了在 linux 下执行一些 BSD 程序，libbsd-compat 提供了必要的可移植性

libc
主 C 库，集成了最常用函数

libcrypt
用于的加密库

libdl
动态连接接口库

libg
g++ 运行时库

libieee
IEEE 浮点运算库

libm
数学函数库

libmcheck
包括了启动(boot)时需要的代码

libmemusage
帮助 memusage 搜集程序运行时的内存占用信息

libnsl
提供网络服务的库

libnss
名称服务切换库，包含了解释主机名、用户名、组名、别名、服务、协议等等的函数

libpcprofile
包含用于跟踪某些特定源代码的 CPU 使用时间的 profiling 函数

libpthread
POSIX 线程库

libresolv
创建、发送、解释到互联网域名服务器的数据包

librpcsvc
提供 RPC 的其他杂项服务

librt
提供了大部分的 POSIX.1b 运行时扩展接口

libthread_db
对多线程程序的调试很有用

libutil
包含了在很多不同的 Unix 程序中使用的"标准"函数
```










什么是glibc，libc，glib
glibc是linux的GUN C函数库，是linux系统中最底层的API，几乎其它任何运行时库都依赖于glibc。glibc除了封装linux操作系统所提供的系统服务，它本身也提供了许多其它必要功能服务的实现，例如：动态加载模块libdl、实时扩展接口librt。对应的动态库的名字libc.so

libc是Linux下的ANSI C函数库，被glibc包含



构建程序，只要链接的libstdc++，glibc(libc.so，libpthread，动态连接器等系列库)版本正确，就不会出问题。在低版本的os上，安装高版本的gcc，glibc，只要可以顺利编译通过，则意味这高版本库是支持低版本OS的。



查看glibc版本
```bash
[root@node70 /root] $ ldd --version
ldd (GNU libc) 2.17
Copyright (C) 2012 Free Software Foundation, Inc.

```
ldd是隶属于glibc，它的版本就是glibc的版本


通过 getconf GNU_LIBC_VERSION
pthread也是一个非常重要的库，它被包含在glibc中，可以通过

getconf GNU_LIBPTHREAD_VERSION 查看它的版本，是与glibc的版本是一致的

```bash
[root@node70 /root] $ getconf GNU_LIBPTHREAD_VERSION
NPTL 2.17

```

查找glibc库的位置
- 通过ldd

linux 上几乎所有的程序都依赖于glibc，所以可以直接通过 ldd 某个程序 | grep "libc.so"的方式查看

- 通过gcc

gcc -print-file-name=libc.so可以获得libc.so的位置，这也是该gcc依赖的libc.so的位置

```bash
[root@node70 /root] $ gcc -print-file-name=libc.so
/usr/lib/gcc/x86_64-redhat-linux/4.8.5/../../../../lib64/libc.so
```


查看glibc API的版本
```bash
[root@node70 /root] $ strings /lib64/libc.so.6 | grep GLIBC
GLIBC_2.2.5
GLIBC_2.2.6
GLIBC_2.3
GLIBC_2.3.2
GLIBC_2.3.3
GLIBC_2.3.4
GLIBC_2.4
GLIBC_2.5
GLIBC_2.6
GLIBC_2.7
GLIBC_2.8
GLIBC_2.9
GLIBC_2.10
GLIBC_2.11
GLIBC_2.12
GLIBC_2.13
GLIBC_2.14
GLIBC_2.15
GLIBC_2.16
GLIBC_2.17
GLIBC_PRIVATE

```

#### libstdc++
libstdc++是 gcc的标准C++库(libc++是clang的标准C++库)

1. 查找libstdc++.so的位置
```bash
[root@node70 /root] $ /sbin/ldconfig -p | grep stdc++
	libstdc++.so.6 (libc6,x86-64) => /lib64/libstdc++.so.6
	libstdc++.so.6 (libc6) => /lib/libstdc++.so.6


[root@node70 /root] $ locate libstdc++.so
/data/app/dbscale/libs_system/libstdc++.so.6
/data/dbinit/venv/bin/greatrds-dbinit-bin-dir/libstdc++.so.6
/opt/rh/devtoolset-8/root/usr/lib/gcc/x86_64-redhat-linux/8/libstdc++.so
/opt/rh/devtoolset-8/root/usr/lib/gcc/x86_64-redhat-linux/8/32/libstdc++.so
/rds/docker/overlay2/13c593ef1d0f30a9591bd419e8ec30b49e40c1e0ddb454b9bfeec0e7eac3955a/diff/usr/lib64/libstdc++.so.6
/rds/docker/overlay2/13c593ef1d0f30a9591bd419e8ec30b49e40c1e0ddb454b9bfeec0e7eac3955a/diff/usr/lib64/libstdc++.so.6.0.19
/rds/docker/overlay2/13c593ef1d0f30a9591bd419e8ec30b49e40c1e0ddb454b9bfeec0e7eac3955a/diff/usr/share/gdb/auto-load/usr/lib64/libstdc++.so.6.0.19-gdb.py
/rds/docker/overlay2/13c593ef1d0f30a9591bd419e8ec30b49e40c1e0ddb454b9bfeec0e7eac3955a/diff/usr/share/gdb/auto-load/usr/lib64/libstdc++.so.6.0.19-gdb.pyc
/rds/docker/overlay2/13c593ef1d0f30a9591bd419e8ec30b49e40c1e0ddb454b9bfeec0e7eac3955a/diff/usr/share/gdb/auto-load/usr/lib64/libstdc++.so.6.0.19-gdb.pyo
/rds/docker/overlay2/2c25869ff8db423abdafa11ad95f906fac7a8f53bed8aaecd198aa3b351247ee/diff/usr/lib64/libstdc++.so.6
/rds/docker/overlay2/2c25869ff8db423abdafa11ad95f906fac7a8f53bed8aaecd198aa3b351247ee/diff/usr/lib64/libstdc++.so.6.0.19
/rds/docker/overlay2/2c25869ff8db423abdafa11ad95f906fac7a8f53bed8aaecd198aa3b351247ee/diff/usr/share/gdb/auto-load/usr/lib64/libstdc++.so.6.0.19-gdb.py
/rds/docker/overlay2/2c25869ff8db423abdafa11ad95f906fac7a8f53bed8aaecd198aa3b351247ee/diff/usr/share/gdb/auto-load/usr/lib64/libstdc++.so.6.0.19-gdb.pyc
/rds/docker/overlay2/2c25869ff8db423abdafa11ad95f906fac7a8f53bed8aaecd198aa3b351247ee/diff/usr/share/gdb/auto-load/usr/lib64/libstdc++.so.6.0.19-gdb.pyo
/rds/docker/overlay2/5bfe47ffee53549bbdd0407c147bb687f5617ce426ba81474b6601ff7ea38dc2/diff/usr/lib/x86_64-linux-gnu/libstdc++.so.6
/rds/docker/overlay2/5bfe47ffee53549bbdd0407c147bb687f5617ce426ba81474b6601ff7ea38dc2/diff/usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.25
/rds/docker/overlay2/5bfe47ffee53549bbdd0407c147bb687f5617ce426ba81474b6601ff7ea38dc2/diff/usr/share/gdb/auto-load/usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.25-gdb.py
/rds/docker/overlay2/6d0ce444da68e55c0881816e3c0b1f1f97d261fa2819889bfc2e05eb846bde46/diff/usr/lib/libstdc++.so.6
/rds/docker/overlay2/6d0ce444da68e55c0881816e3c0b1f1f97d261fa2819889bfc2e05eb846bde46/diff/usr/lib/libstdc++.so.6.0.25
/rds/docker/overlay2/8c733f5ae03899526e7dc345bba8455cbe4668c6de70fb80065e22233b74ad03/diff/usr/lib/x86_64-linux-gnu/libstdc++.so.6
/rds/docker/overlay2/8c733f5ae03899526e7dc345bba8455cbe4668c6de70fb80065e22233b74ad03/diff/usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.28
/rds/docker/overlay2/8c733f5ae03899526e7dc345bba8455cbe4668c6de70fb80065e22233b74ad03/diff/usr/share/gdb/auto-load/usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.28-gdb.py
/rds/docker/overlay2/a5264976df8dd0c9d331ebddbedfed0d512b00cdde124c2c003e675b5f48fd47/diff/usr/lib/gcc/x86_64-redhat-linux/4.8.2/libstdc++.so
/rds/docker/overlay2/a5264976df8dd0c9d331ebddbedfed0d512b00cdde124c2c003e675b5f48fd47/diff/usr/lib/gcc/x86_64-redhat-linux/4.8.2/32/libstdc++.so
/rds/docker/overlay2/a5264976df8dd0c9d331ebddbedfed0d512b00cdde124c2c003e675b5f48fd47/diff/usr/lib64/libstdc++.so.6
/rds/docker/overlay2/a5264976df8dd0c9d331ebddbedfed0d512b00cdde124c2c003e675b5f48fd47/diff/usr/lib64/libstdc++.so.6.0.19
/rds/docker/overlay2/a5264976df8dd0c9d331ebddbedfed0d512b00cdde124c2c003e675b5f48fd47/diff/usr/share/gdb/auto-load/usr/lib64/libstdc++.so.6.0.19-gdb.py
/rds/docker/overlay2/a5264976df8dd0c9d331ebddbedfed0d512b00cdde124c2c003e675b5f48fd47/diff/usr/share/gdb/auto-load/usr/lib64/libstdc++.so.6.0.19-gdb.pyc
/rds/docker/overlay2/a5264976df8dd0c9d331ebddbedfed0d512b00cdde124c2c003e675b5f48fd47/diff/usr/share/gdb/auto-load/usr/lib64/libstdc++.so.6.0.19-gdb.pyo
/root/greatrdsapi/usr/lib64/libstdc++.so.6
/root/greatrdsapi/usr/lib64/libstdc++.so.6.0.19
/root/greatrdsapi/usr/share/gdb/auto-load/usr/lib64/libstdc++.so.6.0.19-gdb.py
/root/greatrdsapi/usr/share/gdb/auto-load/usr/lib64/libstdc++.so.6.0.19-gdb.pyc
/root/greatrdsapi/usr/share/gdb/auto-load/usr/lib64/libstdc++.so.6.0.19-gdb.pyo
/root/tmp/greatrds-api/usr/lib64/libstdc++.so.6
/root/tmp/greatrds-api/usr/lib64/libstdc++.so.6.0.19
/root/tmp/greatrds-api/usr/share/gdb/auto-load/usr/lib64/libstdc++.so.6.0.19-gdb.py
/root/tmp/greatrds-api/usr/share/gdb/auto-load/usr/lib64/libstdc++.so.6.0.19-gdb.pyc
/root/tmp/greatrds-api/usr/share/gdb/auto-load/usr/lib64/libstdc++.so.6.0.19-gdb.pyo
/usr/lib/libstdc++.so.6
/usr/lib/libstdc++.so.6.0.19
/usr/lib/gcc/x86_64-redhat-linux/4.8.2/libstdc++.so
/usr/lib/gcc/x86_64-redhat-linux/4.8.2/32/libstdc++.so
/usr/lib64/libstdc++.so.6
/usr/lib64/libstdc++.so.6.0.19
/usr/share/gdb/auto-load/usr/lib/libstdc++.so.6.0.19-gdb.py
/usr/share/gdb/auto-load/usr/lib/libstdc++.so.6.0.19-gdb.pyc
/usr/share/gdb/auto-load/usr/lib/libstdc++.so.6.0.19-gdb.pyo
/usr/share/gdb/auto-load/usr/lib64/libstdc++.so.6.0.19-gdb.py
/usr/share/gdb/auto-load/usr/lib64/libstdc++.so.6.0.19-gdb.pyc
/usr/share/gdb/auto-load/usr/lib64/libstdc++.so.6.0.19-gdb.pyo
```
2. 查看libstdc++的版本
libstdc++是被包含在gcc中的，对应为gcc的版本
```bash
[root@node70 /root] $ gcc --version
gcc (GCC) 4.8.5 20150623 (Red Hat 4.8.5-44)
Copyright (C) 2015 Free Software Foundation, Inc.

```


3. 查看系统libstdc++API的版本

```bash
[root@node70 /root] $ strings /usr/lib/libstdc++.so.6 | grep LIBCXX
GLIBCXX_3.4
GLIBCXX_3.4.1
GLIBCXX_3.4.2
GLIBCXX_3.4.3
GLIBCXX_3.4.4
GLIBCXX_3.4.5
GLIBCXX_3.4.6
GLIBCXX_3.4.7
GLIBCXX_3.4.8
GLIBCXX_3.4.9
GLIBCXX_3.4.10
GLIBCXX_3.4.11
GLIBCXX_3.4.12
GLIBCXX_3.4.13
GLIBCXX_3.4.14
GLIBCXX_3.4.15
GLIBCXX_3.4.16
GLIBCXX_3.4.17
GLIBCXX_3.4.18
GLIBCXX_3.4.19
GLIBCXX_DEBUG_MESSAGE_LENGTH

```
libstdc++，glibc的关系
libstdc++与gcc是捆绑在一起的，也就是说安装gcc的时候会把libstdc++装上。 那为什么glibc和gcc没有捆绑在一起呢？因为程序可以不依赖libstdc++，但是必须依赖glibc

4. 确定程序需要的glibc/libstdc++的版本
```sql
readelf -s qt_cef_poc | grep -oP "GLIBC_[\d\.]*" | sort | uniq
```

```bash
../glibc-2.28/configure --prefix=$HOME/local --disable-profile --enable-add-ons --with-headers=/usr/include --with-binutils=/usr/bin --disable-sanity-checks --disable-werror

pkg-config ncurses --libs --cflags

```

### 查看glibc支持哪些版本
```bash
[root@k8s-node-70 local]# strings /lib64/libc.so.6  |grep '^GLIBC_'
GLIBC_2.2.5
GLIBC_2.2.6
GLIBC_2.3
GLIBC_2.3.2
GLIBC_2.3.3
GLIBC_2.3.4
GLIBC_2.4
GLIBC_2.5
GLIBC_2.6
GLIBC_2.7
GLIBC_2.8
GLIBC_2.9
GLIBC_2.10
GLIBC_2.11
GLIBC_2.12
GLIBC_2.13
GLIBC_2.14
GLIBC_2.15
GLIBC_2.16
GLIBC_2.17
GLIBC_PRIVATE
GLIBC_2.8
GLIBC_2.5
GLIBC_2.9
GLIBC_2.7
GLIBC_2.6
GLIBC_2.11
GLIBC_2.16
GLIBC_2.10
GLIBC_2.17
GLIBC_2.13
GLIBC_2.2.6

```





#### you must configure in a separate build directory

```bash
[@localhost glibc-2.34]$ ./configure
checking build system type... x86_64-pc-linux-gnu
checking host system type... x86_64-pc-linux-gnu
checking for gcc... gcc
checking for suffix of object files... o
checking whether we are using the GNU C compiler... yes
checking whether gcc accepts -g... yes
checking for readelf... readelf
checking for g++... g++
checking whether we are using the GNU C++ compiler... yes
checking whether g++ accepts -g... yes
checking whether g++ can link programs... no
configure: error: you must configure in a separate build directory
```

### 安装 GLIBC 的条件及详细步骤

GNU C 库（GLIBC）是 Linux 系统的核心组件，负责提供标准 C 库函数的实现和系统调用的接口。在安装或升级 GLIBC 时，需严格满足以下条件：

---

#### **1. 系统及工具依赖**
| 依赖项           | 最低版本要求              | 检查命令            | 备注                                                    |
|------------------|------------------------|---------------------|-------------------------------------------------------|
| **GCC 编译器**   | 视 GLIBC 版本而定（见下表） | `gcc --version`     | 不同 GLIBC 版本对 GCC 有特定要求。                       |
| **GNU Make**     | 3.81 或更高               | `make --version`    | 构建工具，需支持并行编译（`-j` 参数）。                     |
| **Bison**        | 2.7 或更高                | `bison --version`   | 语法分析器生成工具，用于解析 GLIBC 的语法文件。           |
| **Gawk**         | 4.0 或更高                | `gawk --version`    | 文本处理工具，用于生成配置文件和脚本。                    |
| **GNU Binutils** | 2.25 或更高               | `ld --version`      | 包含 `ld`（链接器）、`as`（汇编器）等工具，需与 GCC 版本兼容。 |
| **Python**       | 3.4 或更高（部分新版本）    | `python3 --version` | 某些配置脚本或测试工具依赖 Python。                      |
| **Glibc 头文件** | 需匹配系统内核版本        | `uname -r`          | 确保头文件与当前内核兼容，避免符号冲突。                  |

---

#### **2. GLIBC 版本与 GCC 版本对应关系**
| GLIBC 版本     | 所需 GCC 最低版本 | 支持的内核版本    | 典型发行版适配             |
|----------------|-------------------|----------------|----------------------------|
| **GLIBC 2.35** | GCC 6.2           | Linux 5.15 或更高 | Ubuntu 22.04, Fedora 36    |
| **GLIBC 2.33** | GCC 6.2           | Linux 5.10 或更高 | Debian 11, CentOS Stream 9 |
| **GLIBC 2.31** | GCC 6.2           | Linux 5.4 或更高  | Ubuntu 20.04, RHEL 8       |
| **GLIBC 2.28** | GCC 4.9           | Linux 4.15 或更高 | Ubuntu 18.04, Debian 10    |
| **GLIBC 2.17** | GCC 4.6           | Linux 3.10 或更高 | CentOS 7, RHEL 7           |

---

#### **3. 安装前准备工作**
1. **检查当前 GLIBC 版本**：
   ```bash
   ldd --version  # 输出第一行为 GLIBC 版本
   ```

2. **安装编译工具链**（以 Ubuntu/Debian 为例）：
   ```bash
   sudo apt-get update
   sudo apt-get install build-essential bison gawk python3 git
   ```

3. **下载 GLIBC 源码**：
   ```bash
   wget https://ftp.gnu.org/gnu/glibc/glibc-2.35.tar.gz
   tar -xzf glibc-2.35.tar.gz
   cd glibc-2.35
   ```

---

#### **4. 配置与编译选项**
1. **创建独立构建目录**（避免污染源码）：
   ```bash
   mkdir build && cd build
   ```

2. **配置编译参数**：
   ```bash
   ../configure \
     --prefix=/usr/local/glibc-2.35 \  # 指定安装路径（避免覆盖系统默认）
     --enable-obsolete-rpc \            # 启用旧版 RPC 支持（如需要）
     --disable-werror                   # 忽略警告错误（避免因警告中断编译）
   ```

3. **编译并安装**：
   ```bash
   make -j$(nproc)        # 并行编译（nproc 为 CPU 核心数）
   sudo make install      # 安装到指定路径
   ```

---

#### **5. 验证安装**
1. **检查新版本 GLIBC**：
   ```bash
   /usr/local/glibc-2.35/lib/ld-linux-x86-64.so.2 --version
   ```

2. **临时使用新 GLIBC**（避免系统冲突）：
   ```bash
   export LD_LIBRARY_PATH=/usr/local/glibc-2.35/lib:$LD_LIBRARY_PATH
   ```

---

#### **6. 常见问题与解决**
| 错误类型                     | 原因分析                  | 解决方案                                                                    |
|--------------------------|-----------------------|-------------------------------------------------------------------------|
| **GCC 版本过低**             | 编译器不支持新语法特性    | 升级 GCC 或使用旧版 GLIBC。                                                  |
| **缺失依赖工具**             | Bison/Gawk 未安装或版本低 | 通过包管理器安装最新版本（如 `apt-get install bison gawk`）。                  |
| **符号冲突（kernel headers）** | 内核头文件不兼容          | 安装与当前内核匹配的头文件（如 `apt-get install linux-headers-$(uname -r)`）。 |
| **动态链接器错误**           | 路径未正确配置            | 通过 `LD_LIBRARY_PATH` 指定新 GLIBC 库路径，或更新 `/etc/ld.so.conf`。        |

---

#### **7. 注意事项**
- **系统关键性**：GLIBC 是系统核心组件，直接替换系统默认版本可能导致系统崩溃。建议在测试环境或容器中操作。
- **兼容性测试**：升级后需全面测试应用程序的兼容性（如使用 `ldd` 检查依赖）。
- **回滚方案**：备份原 GLIBC 库文件，并准备 Live USB 应急恢复工具。

通过以上步骤，可安全地安装或升级 GLIBC 以满足特定需求。


在 Linux 系统中，**内核版本**和 **glibc 版本**之间没有严格的“一一对应”关系，但存在一定的依赖性和兼容性要求。以下是常见版本之间的**最低内核要求**和典型发行版中的匹配示例：

---

### **一、glibc 版本与最低内核要求**
以下是部分 glibc 版本对内核的最低支持要求（数据来源于 glibc 官方文档和发行版实践）：

| glibc 版本     | 最低内核要求  | 说明                                             |
|----------------|-------------|------------------------------------------------|
| **glibc 2.38** | 内核 ≥ 3.2    | 支持新功能（如动态链接器优化），需兼容内核系统调用。 |
| **glibc 2.35** | 内核 ≥ 3.2    | 新增 `futex2` 等系统调用封装，需内核支持。         |
| **glibc 2.34** | 内核 ≥ 3.2    | 移除了 `libpthread` 独立库，合并到 glibc 主库。    |
| **glibc 2.31** | 内核 ≥ 3.2    | 支持 `clone3` 系统调用（需内核 ≥ 5.3）。            |
| **glibc 2.28** | 内核 ≥ 3.2    | 引入线程本地存储（TLS）优化，需内核兼容。            |
| **glibc 2.17** | 内核 ≥ 2.6.32 | 旧版兼容性基线（如 CentOS 7）。                     |

> **注**：
> - 最低内核要求通常是 **长期支持（LTS）内核**的某个版本，确保基础系统调用可用。
> - 部分 glibc 功能（如新系统调用封装）可能需要更高内核版本，需参考具体特性文档。

---

### **二、发行版中的典型匹配关系**
主流 Linux 发行版会协调内核与 glibc 的版本，以下为常见组合示例：

| 发行版           | 默认内核版本 | glibc 版本 | 备注                         |
|------------------|--------------|------------|----------------------------|
| **Ubuntu 22.04** | 5.15 (LTS)   | glibc 2.35 | 长期支持，适配新硬件和功能。   |
| **Debian 12**    | 6.1.x        | glibc 2.36 | 稳定版，内核与库版本较新。     |
| **CentOS 7**     | 3.10         | glibc 2.17 | 旧版但长期支持，兼容企业环境。 |
| **RHEL 9**       | 5.14         | glibc 2.34 | 企业级稳定组合。              |
| **Fedora 38**    | 6.2.x        | glibc 2.37 | 前沿版本，适合开发者测试。     |

---

### **三、如何检查当前版本**
1. **查看内核版本**：
   ```bash
   uname -r  # 输出示例：5.15.0-78-generic
   ```

2. **查看 glibc 版本**：
   ```bash
   ldd --version | head -n1  # 输出示例：ldd (Ubuntu GLIBC 2.35-0ubuntu3.1) 2.35
   ```

---

### **四、兼容性规则**
1. **向前兼容性**：
   - **新版内核 + 旧版 glibc**：通常安全，但程序可能无法使用内核新功能。
   - **旧版内核 + 新版 glibc**：需确保 glibc 未依赖新版内核的系统调用，否则程序可能崩溃（如 `ENOSYS` 错误）。

2. **向后兼容性**：
   - 旧版程序（依赖旧 glibc）通常可以在新版内核上运行，但需通过容器或 chroot 隔离环境。

---

### **五、常见问题与解决**
#### **1. 程序报错 `FATAL: kernel too old`**
- **原因**：程序依赖的 glibc 封装了新版内核的系统调用（如 `openat2`），但当前内核过旧。
- **解决**：升级内核或使用适配旧内核的程序版本。

#### **2. 升级 glibc 后系统崩溃**
- **原因**：手动升级 glibc 导致动态链接器（`ld.so`）不兼容。
- **解决**：通过发行版包管理器回滚，或使用 Live CD 修复。

---

### **六、建议实践**
1. **生产环境**：
   - 使用发行版的官方仓库版本，避免手动升级 glibc。
   - 例如：CentOS/RHEL 用户应通过 `yum update` 升级，而非自行编译。

2. **开发环境**：
   - 若需使用新内核特性（如 eBPF、io_uring），选择较新的发行版（如 Ubuntu 22.04+ 或 Fedora）。

3. **特殊需求**：
   - 若需在旧内核上运行新版 glibc，需验证所有依赖的系统调用是否被旧内核支持（参考 `syscalls` 手册）。

---

### **七、总结**
- **内核与 glibc 的匹配由发行版维护**，用户无需手动处理。
- **最低内核要求**：glibc 版本决定内核下限，但新版内核可兼容旧 glibc。
- **关键原则**：优先通过发行版升级系统，避免手动混用版本。

如需精确版本对应，请查阅：
- [glibc 官方文档](https://www.gnu.org/software/libc/)
- 发行版的 Release Notes（如 [Ubuntu](https://wiki.ubuntu.com/Releases)、[CentOS](https://wiki.centos.org/Manuals/ReleaseNotes)）。


以下是关于 **glibc 版本与最低内核要求** 的文档和关键信息总结，结合了官方资料和发行版实践：

---

### **一、glibc 官方文档中的最低内核要求**
1. **glibc 版本与内核兼容性**
   glibc 的每个版本在发布时均会声明其支持的最低内核版本，这一信息通常记录在 **Release Notes** 或 **官方 Wiki** 中。例如：
   - **glibc 2.34** 要求内核 ≥ 3.2，因为移除了独立的 `libpthread` 库并依赖内核的线程支持。
   - **glibc 2.31** 新增了对 `clone3` 系统调用的封装，需内核 ≥ 5.3 才能完全支持此功能。
   - **glibc 2.17**（如 CentOS 7 默认版本）最低支持内核 2.6.32，但发行版实际搭配的内核可能更高（如 CentOS 7 使用 3.10）。

2. **关键文档来源**
   - **[glibc 官方 Wiki](https://sourceware.org/glibc/wiki/)**：提供版本历史、功能变更和内核依赖说明。
   - **[glibc Release Notes](https://sourceware.org/glibc/wiki/Release/)**：每个版本的发布说明会明确标注系统要求。
   - **[Linux API Changes](http://man7.org/tlpi/api_changes/)**：记录内核系统调用与 glibc 的适配关系。

---

### **二、编译 glibc 时指定的最低内核版本**
在手动编译 glibc 时，可通过 `--enable-kernel` 参数指定支持的最低内核版本。例如：
```bash
./configure --enable-kernel=2.6.32  # 设置 glibc 支持的最低内核为 2.6.32
```
- **作用**：此参数会限制 glibc 仅使用该内核版本及之前引入的系统调用，确保兼容性。若实际运行的内核低于此版本，可能导致程序崩溃。
- **建议**：通常设置为长期支持（LTS）内核版本以平衡功能与稳定性。

---

### **三、发行版中的 glibc 与内核匹配示例**
主流 Linux 发行版会协调 glibc 和内核版本，以下为典型组合：
| 发行版           | glibc 版本 | 默认内核版本 | 说明                 |
|------------------|------------|--------------|--------------------|
| **CentOS 7**     | 2.17       | 3.10         | 企业级长期支持组合   |
| **Ubuntu 22.04** | 2.35       | 5.15         | 适配新硬件和系统调用 |
| **RHEL 9**       | 2.34       | 5.14         | 兼容企业环境需求     |
| **Debian 12**    | 2.36       | 6.1          | 稳定版搭配较新内核   |

---

### **四、如何验证与调试兼容性**
1. **查看当前版本**
   - **内核版本**：`uname -r`
   - **glibc 版本**：`ldd --version` 或执行 `/lib/libc.so.6`。

2. **常见错误与解决**
   - **`FATAL: kernel too old`**：程序依赖新版 glibc 封装的内核特性，需升级内核或降低 glibc 版本。
   - **系统崩溃**：手动升级 glibc 可能导致动态链接器不兼容，建议通过发行版包管理升级。

---

### **五、总结与建议**
- **官方文档优先**：查阅 glibc 的 Release Notes 和 Wiki 获取最低内核要求。
- **发行版协调**：生产环境应依赖发行版维护的版本组合（如通过 `yum`/`apt` 升级）。
- **开发环境**：若需使用新内核特性（如 `io_uring`），选择较新的发行版或手动指定 `--enable-kernel`。

如需完整版本历史或特定版本的详细要求，可参考 [glibc 官方时间线](https://sourceware.org/glibc/wiki/Glibc%20Timeline) 或发行版 Release Notes。




## 问题记录


#### 1. error: argument 1 of type ‘struct __jmp_buf_tag *’ declared as a pointer [-Werror=array-parameter=]

```bash
TASK [glibc : cd /glc/glibc/source/glibc-2.28/build_20250415T132648;make -j4] ***********************************************************************************************************************************************************************************************************
fatal: [node1]: FAILED! => changed=true
  cmd: make -j4
  delta: '0:00:05.433013'
  end: '2025-04-15 13:29:04.464858'
  msg: non-zero return code
  rc: 2
  start: '2025-04-15 13:28:59.031845'
  stderr: |-
    In file included from ../include/pthread.h:1,
                     from ../nptl/../nptl_db/thread_db.h:25,
                     from ../nptl/descr.h:32,
                     from ../sysdeps/x86_64/nptl/tls.h:130,
                     from ../include/link.h:51,
                     from <stdin>:1:
    ../sysdeps/nptl/pthread.h:744:47: error: argument 1 of type ‘struct __jmp_buf_tag *’ declared as a pointer [-Werror=array-parameter=]
      744 | extern int __sigsetjmp (struct __jmp_buf_tag *__env, int __savemask) __THROWNL;
          |                         ~~~~~~~~~~~~~~~~~~~~~~^~~~~
    In file included from ../include/setjmp.h:2,
                     from ../nptl/descr.h:24,
                     from ../sysdeps/x86_64/nptl/tls.h:130,
                     from ../include/link.h:51,
                     from <stdin>:1:
    ../setjmp/setjmp.h:54:46: note: previously declared as an array ‘struct __jmp_buf_tag[1]’
       54 | extern int __sigsetjmp (struct __jmp_buf_tag __env[1], int __savemask) __THROWNL;
          |                         ~~~~~~~~~~~~~~~~~~~~~^~~~~~~~
    In file included from ../include/pthread.h:1,
                     from ../nptl/../nptl_db/thread_db.h:25,
                     from ../nptl/descr.h:32,
                     from ../sysdeps/x86_64/nptl/tls.h:130,
                     from ../include/link.h:51,
                     from ../include/dlfcn.h:4,
                     from ../sysdeps/generic/ldsodefs.h:32,
                     from ../sysdeps/x86/ldsodefs.h:64,
                     from ../sysdeps/gnu/ldsodefs.h:46,
                     from ../sysdeps/unix/sysv/linux/ldsodefs.h:25,
                     from <stdin>:2:
    ../sysdeps/nptl/pthread.h:744:47: error: argument 1 of type ‘struct __jmp_buf_tag *’ declared as a pointer [-Werror=array-parameter=]
      744 | extern int __sigsetjmp (struct __jmp_buf_tag *__env, int __savemask) __THROWNL;
          |                         ~~~~~~~~~~~~~~~~~~~~~~^~~~~
    In file included from ../include/setjmp.h:2,
                     from ../nptl/descr.h:24,
                     from ../sysdeps/x86_64/nptl/tls.h:130,
                     from ../include/link.h:51,
                     from ../include/dlfcn.h:4,
                     from ../sysdeps/generic/ldsodefs.h:32,
                     from ../sysdeps/x86/ldsodefs.h:64,
                     from ../sysdeps/gnu/ldsodefs.h:46,
                     from ../sysdeps/unix/sysv/linux/ldsodefs.h:25,
                     from <stdin>:2:
    ../setjmp/setjmp.h:54:46: note: previously declared as an array ‘struct __jmp_buf_tag[1]’
       54 | extern int __sigsetjmp (struct __jmp_buf_tag __env[1], int __savemask) __THROWNL;
          |                         ~~~~~~~~~~~~~~~~~~~~~^~~~~~~~
    In file included from ../include/pthread.h:1,
                     from ../nptl/../nptl_db/thread_db.h:25,
                     from ../nptl/descr.h:32,
                     from ../sysdeps/x86_64/nptl/tls.h:130,
                     from ../sysdeps/unix/sysv/linux/x86_64/sysdep.h:24,
                     from <stdin>:2:
    ../sysdeps/nptl/pthread.h:744:47: error: argument 1 of type ‘struct __jmp_buf_tag *’ declared as a pointer [-Werror=array-parameter=]
      744 | extern int __sigsetjmp (struct __jmp_buf_tag *__env, int __savemask) __THROWNL;
          |                         ~~~~~~~~~~~~~~~~~~~~~~^~~~~
    In file included from ../include/setjmp.h:2,
                     from ../nptl/descr.h:24,
                     from ../sysdeps/x86_64/nptl/tls.h:130,
                     from ../sysdeps/unix/sysv/linux/x86_64/sysdep.h:24,
                     from <stdin>:2:
    ../setjmp/setjmp.h:54:46: note: previously declared as an array ‘struct __jmp_buf_tag[1]’
       54 | extern int __sigsetjmp (struct __jmp_buf_tag __env[1], int __savemask) __THROWNL;
          |                         ~~~~~~~~~~~~~~~~~~~~~^~~~~~~~
    In file included from ../include/pthread.h:1,
                     from ../nptl/../nptl_db/thread_db.h:25,
                     from ../nptl/descr.h:32,
                     from ../sysdeps/x86_64/nptl/tls.h:130,
                     from ../sysdeps/unix/sysv/linux/x86_64/sysdep.h:24,
                     from <stdin>:1:
    ../sysdeps/nptl/pthread.h:744:47: error: argument 1 of type ‘struct __jmp_buf_tag *’ declared as a pointer [-Werror=array-parameter=]
      744 | extern int __sigsetjmp (struct __jmp_buf_tag *__env, int __savemask) __THROWNL;
          |                         ~~~~~~~~~~~~~~~~~~~~~~^~~~~
    In file included from ../include/setjmp.h:2,
                     from ../nptl/descr.h:24,
                     from ../sysdeps/x86_64/nptl/tls.h:130,
                     from ../sysdeps/unix/sysv/linux/x86_64/sysdep.h:24,
                     from <stdin>:1:
    ../setjmp/setjmp.h:54:46: note: previously declared as an array ‘struct __jmp_buf_tag[1]’
       54 | extern int __sigsetjmp (struct __jmp_buf_tag __env[1], int __savemask) __THROWNL;
          |                         ~~~~~~~~~~~~~~~~~~~~~^~~~~~~~
    cc1: all warnings being treated as errors
    cc1: all warnings being treated as errors
    make[2]: *** [../Makerules:287: /glc/glibc/source/glibc-2.28/build_20250415T132648/tlsdesc.h] Error 1
    make[2]: *** Waiting for unfinished jobs....
    make[2]: *** [../Makerules:287: /glc/glibc/source/glibc-2.28/build_20250415T132648/rtld-offsets.h] Error 1
    cc1: all warnings being treated as errors
    make[2]: *** [../Makerules:287: /glc/glibc/source/glibc-2.28/build_20250415T132648/tcb-offsets.h] Error 1
    cc1: all warnings being treated as errors
    make[2]: *** [../Makerules:287: /glc/glibc/source/glibc-2.28/build_20250415T132648/link-defines.h] Error 1
    make[1]: *** [Makefile:258: csu/subdir_lib] Error 2
    make: *** [Makefile:9: all] Error 2
  stderr_lines: <omitted>

```


#### error: use a compatible toolchain or configure with --disable-mathvec (this results in incomplete ABI).


```bash
fatal: [node2]: FAILED! => changed=true
  cmd: ../configure --prefix=/glc/glibc/app/glibc-2.40 --enable-shared --enable-static
  delta: '0:00:11.930891'
  end: '2025-04-15 14:25:41.346980'
  msg: non-zero return code
  rc: 1
  start: '2025-04-15 14:25:29.416089'
  stderr: |-
    configure: WARNING: mathvec is enabled but linker does not support variant PCS.
    configure: error: use a compatible toolchain or configure with --disable-mathvec (this results in incomplete ABI).
  stderr_lines: <omitted>
  stdout: |-
    checking build system type... aarch64-unknown-linux-gnu
    checking host system type... aarch64-unknown-linux-gnu
    checking for gcc... gcc
    checking for suffix of object files... o
    checking whether the compiler supports GNU C... yes
    checking whether gcc accepts -g... yes
    checking for gcc option to enable C11 features... none needed
    checking for g++... g++
    checking whether the compiler supports GNU C++... yes
    checking whether g++ accepts -g... yes
    checking for g++ option to enable C++11 features... none needed
    checking whether g++ can link programs... yes
    checking for sysdeps preconfigure fragments... aarch64 alpha arc arm csky hppa i386 loongarch m68k microblaze checking for grep that handles long lines and -e... /usr/bin/grep
    checking for egrep... /usr/bin/grep -E
    mips nios2 or1k powerpc riscv s390 sh checking for grep that handles long lines and -e... (cached) /usr/bin/grep
    checking for egrep... (cached) /usr/bin/grep -E
    sparc x86_64
    checking for a BSD-compatible install... /usr/bin/install -c
    checking whether ln -s works... yes
    checking for ld... ld
    checking version of ld... 2.32, ok
    checking for gnumake... no
    checking for gmake... gmake
    checking version of gmake... 4.2.1, ok
    checking for gnumsgfmt... no
    checking for gmsgfmt... no
    checking for msgfmt... msgfmt
    checking version of msgfmt... 0.20.1, ok
    checking for makeinfo... makeinfo
    checking version of makeinfo... 6.6, ok
    checking for sed... sed
    checking version of sed... 4.8, ok
    checking for gawk... gawk
    checking version of gawk... 5.0.1, ok
    checking for bison... bison
    checking version of bison... 3.5, ok
    checking if gcc is sufficient to build libc... yes
    checking for python3... python3
    checking version of python3... 3.7.4, ok
    checking for use of fpu sysdeps directories... yes
    checking for -fstack-protector... yes
    checking for -fstack-protector-strong... yes
    checking for -fstack-protector-all... yes
    checking for assembler and linker STT_GNU_IFUNC support... yes
    checking for gcc attribute ifunc support... yes
    checking whether the linker supports textrels along with ifunc... yes
    checking for GNU attribute retain support... no
    checking if compiler warns about alias for function with incompatible types... yes
    checking sysdep dirs... sysdeps/unix/sysv/linux/aarch64 sysdeps/aarch64/nptl sysdeps/unix/sysv/linux/wordsize-64 sysdeps/unix/sysv/linux sysdeps/nptl sysdeps/pthread sysdeps/gnu sysdeps/unix/inet sysdeps/unix/sysv sysdeps/unix sysdeps/posix sysdeps/aarch64/fpu sysdeps/aarch64/multiarch sysdeps/aarch64 sysdeps/wordsize-64 sysdeps/ieee754/ldbl-128 sysdeps/ieee754/dbl-64 sysdeps/ieee754/flt-32 sysdeps/ieee754 sysdeps/generic
    checking LD_LIBRARY_PATH variable... ok
    checking for bash... /usr/bin/bash
    checking for perl... /usr/bin/perl
    checking for install-info... /usr/sbin/install-info
    checking for .set assembler directive... yes
    checking linker support for protected data symbol... yes
    checking linker support for INSERT in linker script... yes
    checking for broken __attribute__((alias()))... no
    checking whether to put _rtld_local into .sdata section... no
    checking for libunwind-support in compiler... no
    checking whether --noexecstack is desirable for .S files... yes
    checking for linker that supports -z execstack... yes
    checking for linker that supports -z start-stop-gc... no
    checking for linker that supports --depaudit... yes
    checking for linker that supports -z pack-relative-relocs... no
    checking for linker that supports --no-dynamic-linker... yes
    checking for -static-pie... yes
    checking for -fpie... yes
    checking for GLOB_DAT reloc... yes
    checking for tls descriptor support... desc
    checking if -Wno-ignored-attributes is required for aliases...
    checking whether cc puts quotes around section names... no
    checking for __builtin_memset... no
    checking for redirection of built-in functions... yes
    checking for compiler option to disable generation of FMA instructions... -ffp-contract=off
    checking for compiler option that -fsignaling-nans... -fsignaling-nans
    checking if gcc accepts -fno-tree-loop-distribute-patterns with __attribute__ ((__optimize__))... yes
    checking for libgd... no
    checking for is_selinux_enabled in -lselinux... yes
    checking for audit_log_user_avc_message in -laudit... no
    checking for cap_init in -lcap... no
    checking for maximum supported _FORTIFY_SOURCE level... 2
    checking whether the assembler requires one version per symbol... yes
    checking for __builtin_trap with no external dependencies... yes
    checking whether the C++ compiler supports thread_local... yes
    running configure fragment for sysdeps/unix/sysv/linux/aarch64
    running configure fragment for sysdeps/unix/sysv/linux
    checking installed Linux kernel header files... 3.2.0 or later
    checking for kernel header at least 3.7.0... ok
    checking for symlinks in /glc/glibc/app/glibc-2.40/include... ok
    running configure fragment for sysdeps/gnu
    running configure fragment for sysdeps/aarch64
    checking for egrep -e... /usr/bin/grep -E
    checking for big endian... no
    checking for BTI support... no
    checking if pac-ret is enabled... no
    checking for variant PCS support... no
    checking for SVE support in assembler... yes
    checking for availability of SVE ACLE... yes
  stdout_lines: <omitted>

```

- 原因：AArch64 已添加对矢量数学库 libmvec 的支持。它需要 GCC 版本 10.1.0 及以上。它可以通过`--disable-mathvec`禁用，但由于它会更改 ABI，因此不受支持。符号名称遵循 AArch64 矢量 ABI，它们在 math.h 中声明，此时必须手动调用。不确定是否有办法检查主机 GCC 版本并根据需要添加`--disable-mathvec`。我使用额外的 GLIBC 配置标志选项解决了这个问题。
- 参考：https://github.com/crosstool-ng/crosstool-ng/issues/2064



#### no such instruction: `vpcmpneqb (64 * 4)(%rdi),%zmm17,%k1'



```bash
2025-04-16T10:26:45 [root@x86centos7-250 /root] $ make install
LC_ALL=C; export LC_ALL; \
make -r PARALLELMFLAGS="" -C .. objdir=`pwd` install
make[1]: Entering directory '/glc/glibc/source/glibc-2.40'
make  subdir=csu -C csu ..=../ subdir_lib
make[2]: Entering directory '/glc/glibc/source/glibc-2.40/csu'
make[2]: Leaving directory '/glc/glibc/source/glibc-2.40/csu'
make  subdir=iconv -C iconv ..=../ subdir_lib
make[2]: Entering directory '/glc/glibc/source/glibc-2.40/iconv'
make[2]: Leaving directory '/glc/glibc/source/glibc-2.40/iconv'
make  subdir=locale -C locale ..=../ subdir_lib
make[2]: Entering directory '/glc/glibc/source/glibc-2.40/locale'
make[2]: Leaving directory '/glc/glibc/source/glibc-2.40/locale'
make  subdir=localedata -C localedata ..=../ subdir_lib
make[2]: Entering directory '/glc/glibc/source/glibc-2.40/localedata'
make[2]: Nothing to be done for 'subdir_lib'.
make[2]: Leaving directory '/glc/glibc/source/glibc-2.40/localedata'
make  subdir=iconvdata -C iconvdata ..=../ subdir_lib
make[2]: Entering directory '/glc/glibc/source/glibc-2.40/iconvdata'
make[2]: Nothing to be done for 'subdir_lib'.
make[2]: Leaving directory '/glc/glibc/source/glibc-2.40/iconvdata'
make  subdir=assert -C assert ..=../ subdir_lib
make[2]: Entering directory '/glc/glibc/source/glibc-2.40/assert'
make[2]: Leaving directory '/glc/glibc/source/glibc-2.40/assert'
make  subdir=ctype -C ctype ..=../ subdir_lib
make[2]: Entering directory '/glc/glibc/source/glibc-2.40/ctype'
make[2]: Leaving directory '/glc/glibc/source/glibc-2.40/ctype'
make  subdir=intl -C intl ..=../ subdir_lib
make[2]: Entering directory '/glc/glibc/source/glibc-2.40/intl'
make[2]: Leaving directory '/glc/glibc/source/glibc-2.40/intl'
make  subdir=catgets -C catgets ..=../ subdir_lib
make[2]: Entering directory '/glc/glibc/source/glibc-2.40/catgets'
make[2]: Leaving directory '/glc/glibc/source/glibc-2.40/catgets'
make  subdir=math -C math ..=../ subdir_lib
make[2]: Entering directory '/glc/glibc/source/glibc-2.40/math'
make[2]: Leaving directory '/glc/glibc/source/glibc-2.40/math'
make  subdir=setjmp -C setjmp ..=../ subdir_lib
make[2]: Entering directory '/glc/glibc/source/glibc-2.40/setjmp'
make[2]: Leaving directory '/glc/glibc/source/glibc-2.40/setjmp'
make  subdir=signal -C signal ..=../ subdir_lib
make[2]: Entering directory '/glc/glibc/source/glibc-2.40/signal'
make[2]: Leaving directory '/glc/glibc/source/glibc-2.40/signal'
make  subdir=stdlib -C stdlib ..=../ subdir_lib
make[2]: Entering directory '/glc/glibc/source/glibc-2.40/stdlib'
make[2]: Leaving directory '/glc/glibc/source/glibc-2.40/stdlib'
make  subdir=stdio-common -C stdio-common ..=../ subdir_lib
make[2]: Entering directory '/glc/glibc/source/glibc-2.40/stdio-common'
make[2]: Leaving directory '/glc/glibc/source/glibc-2.40/stdio-common'
make  subdir=libio -C libio ..=../ subdir_lib
make[2]: Entering directory '/glc/glibc/source/glibc-2.40/libio'
make[2]: Leaving directory '/glc/glibc/source/glibc-2.40/libio'
make  subdir=dlfcn -C dlfcn ..=../ subdir_lib
make[2]: Entering directory '/glc/glibc/source/glibc-2.40/dlfcn'
make[2]: Leaving directory '/glc/glibc/source/glibc-2.40/dlfcn'
make  subdir=nptl -C nptl ..=../ subdir_lib
make[2]: Entering directory '/glc/glibc/source/glibc-2.40/nptl'
make[2]: Leaving directory '/glc/glibc/source/glibc-2.40/nptl'
make  subdir=rt -C rt ..=../ subdir_lib
make[2]: Entering directory '/glc/glibc/source/glibc-2.40/rt'
make[2]: Leaving directory '/glc/glibc/source/glibc-2.40/rt'
make  subdir=malloc -C malloc ..=../ subdir_lib
make[2]: Entering directory '/glc/glibc/source/glibc-2.40/malloc'
make[2]: Leaving directory '/glc/glibc/source/glibc-2.40/malloc'
make  subdir=string -C string ..=../ subdir_lib
make[2]: Entering directory '/glc/glibc/source/glibc-2.40/string'
gcc -B/usr/bin/ ../sysdeps/x86_64/multiarch/memchr-evex512.S -c    -I../include -I/glc/glibc/source/glibc-2.40/build_20250416T100752/string  -I/glc/glibc/source/glibc-2.40/build_20250416T100752  -I../sysdeps/unix/sysv/linux/x86_64/64  -I../sysdeps/unix/sysv/linux/x86_64/include -I../sysdeps/unix/sysv/linux/x86_64  -I../sysdeps/unix/sysv/linux/x86/include -I../sysdeps/unix/sysv/linux/x86  -I../sysdeps/x86/nptl  -I../sysdeps/unix/sysv/linux/wordsize-64  -I../sysdeps/x86_64/nptl  -I../sysdeps/unix/sysv/linux/include -I../sysdeps/unix/sysv/linux  -I../sysdeps/nptl  -I../sysdeps/pthread  -I../sysdeps/gnu  -I../sysdeps/unix/inet  -I../sysdeps/unix/sysv  -I../sysdeps/unix/x86_64  -I../sysdeps/unix  -I../sysdeps/posix  -I../sysdeps/x86_64/64  -I../sysdeps/x86_64/fpu/multiarch  -I../sysdeps/x86_64/fpu  -I../sysdeps/x86/fpu  -I../sysdeps/x86_64/multiarch  -I../sysdeps/x86_64  -I../sysdeps/x86/include -I../sysdeps/x86  -I../sysdeps/ieee754/float128  -I../sysdeps/ieee754/ldbl-96/include -I../sysdeps/ieee754/ldbl-96  -I../sysdeps/ieee754/dbl-64  -I../sysdeps/ieee754/flt-32  -I../sysdeps/wordsize-64  -I../sysdeps/ieee754  -I../sysdeps/generic  -I.. -I../libio -I. -nostdinc -isystem /glc/gcc/app/gcc-11.4.0/lib/gcc/x86_64-pc-linux-gnu/11.4.0/include -isystem /glc/gcc/app/gcc-11.4.0/lib/gcc/x86_64-pc-linux-gnu/11.4.0/include-fixed -isystem /usr/include  -D_LIBC_REENTRANT -include /glc/glibc/source/glibc-2.40/build_20250416T100752/libc-modules.h -DMODULE_NAME=libc -include ../include/libc-symbols.h  -DPIC     -DTOP_NAMESPACE=glibc -DASSEMBLER  -g -Werror=undef -Wa,--noexecstack      -o /glc/glibc/source/glibc-2.40/build_20250416T100752/string/memchr-evex512.o -MD -MP -MF /glc/glibc/source/glibc-2.40/build_20250416T100752/string/memchr-evex512.o.dt -MT /glc/glibc/source/glibc-2.40/build_20250416T100752/string/memchr-evex512.o
../sysdeps/x86_64/multiarch/memchr-evex-base.S: Assembler messages:
../sysdeps/x86_64/multiarch/memchr-evex-base.S:229: Error: no such instruction: `vpcmpneqb (64 * 4)(%rdi),%zmm17,%k1'
make[2]: *** [/glc/glibc/source/glibc-2.40/build_20250416T100752/sysd-rules:653: /glc/glibc/source/glibc-2.40/build_20250416T100752/string/memchr-evex512.o] Error 1
make[2]: Leaving directory '/glc/glibc/source/glibc-2.40/string'
make[1]: *** [Makefile:484: string/subdir_lib] Error 2
make[1]: Leaving directory '/glc/glibc/source/glibc-2.40'
make: *** [Makefile:12: install] Error 2
2025-04-16T10:27:17 [root@x86centos7-250 /root] $ pwd
/glc/glibc/source/glibc-2.40/build_20250416T100752

```



#### ld-linux-x86-64.so.2 补救措施

```bash
2025-04-18T12:25:14 [igoodfu@48 /db/storage/guolicheng/local/lib64] $ ll
ls: relocation error: /db/storage/guolicheng/local/lib64/libc.so.6: symbol _dl_starting_up, version GLIBC_PRIVATE not defined in file ld-linux-x86-64.so.2 with link time reference

```


```bash
LD_PRELOAD=/lib64/libc-2.17.so rm -rf /lib64/libc.so.6
LD_PRELOAD=/lib64/libc-2.17.so ln -s /lib64/libc-2.17.so /lib64/libc.so.6
```

```bash
sln /usr/lib64/libc-2.17.so /lib64/libc.so.6
```




#### These critical programs are missing or too old: make
```bash
configure:5265: found /usr/local/bin/python3
configure:5276: result: python3
configure:5300: error:
*** These critical programs are missing or too old: make
*** Check the INSTALL file for required versions.

```
- 原因：make版本过低
- 解决：升级make




