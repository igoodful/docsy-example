---
title: socat
description: socat
date: 2025-03-22
weight: 102
viz: true

---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


{{<note>}}
<!---->

- https://sourceforge.net/p/acl/
- https://github.com/acl-dev/acl/


{{</note>}}


## 1. 依赖

```viz-dot
digraph "socat" {
node [
        fontsize = "12"
];
    "socat" [ label = "socat", shape = octagon ];
    "util" [ label = "util", shape = octagon ];
    "rt" [ label = "rt", shape = octagon ];
    "readline" [ label = "readline", shape = octagon ];

    "socat" -> "util";
    "socat" -> "rt";
    "socat" -> "readline";
}
```

## 2. 内容


### 2.1 bin

```viz-dot
digraph "socat" {
node [
        fontsize = "12"
];
    "socat" [ label = "socat", shape = octagon ];
    "chacl" [ label = "chacl", shape = octagon ];
    "getfacl" [ label = "getfacl", shape = octagon ];
    "setfacl" [ label = "setfacl", shape = octagon ];
    "socat.so" [ label = "socat.so", shape = octagon ];
    "acl/socat.h" [ label = "acl/socat.h", shape = octagon ];
    "sys/acl.h" [ label = "sys/acl.h", shape = octagon ];

    "socat" -> "chacl";
    "socat" -> "getfacl";
    "socat" -> "setfacl";
    "socat" -> "socat.so";
    "socat" -> "acl/socat.h";
    "socat" -> "sys/acl.h";
}
```




## 3. 安装


#### 3.1 yum安装
```bash


```




#### 3.2 源码安装

```bash
./configure --prefix={{ socat_app_dir }} --enable-static --disable-readline


2025-03-27T23:47:10 [mysql@ky10arm-98 //glc/socat/app/socat-1.8.0.3/bin] $ ./socat1 -V
socat by Gerhard Rieger and contributors - see www.dest-unreach.org
socat version 1.8.0.3 on Mar 27 2025 23:45:11
   running on Linux version #1 SMP Sun Jun 28 14:27:40 CST 2020, release 4.19.90-17.ky10.aarch64, machine aarch64
features:
  #define WITH_HELP 1
  #define WITH_STATS 1
  #define WITH_STDIO 1
  #define WITH_FDNUM 1
  #define WITH_FILE 1
  #define WITH_CREAT 1
  #define WITH_GOPEN 1
  #define WITH_TERMIOS 1
  #define WITH_PIPE 1
  #define WITH_SOCKETPAIR 1
  #define WITH_UNIX 1
  #define WITH_ABSTRACT_UNIXSOCKET 1
  #define WITH_IP4 1
  #define WITH_IP6 1
  #define WITH_RAWIP 1
  #define WITH_GENERICSOCKET 1
  #define WITH_INTERFACE 1
  #define WITH_TCP 1
  #define WITH_UDP 1
  #define WITH_SCTP 1
  #define WITH_DCCP 1
  #define WITH_UDPLITE 1
  #define WITH_LISTEN 1
  #define WITH_POSIXMQ 1
  #define WITH_SOCKS4 1
  #define WITH_SOCKS4A 1
  #define WITH_SOCKS5 1
  #define WITH_VSOCK 1
  #define WITH_NAMESPACES 1
  #define WITH_PROXY 1
  #define WITH_SYSTEM 1
  #define WITH_SHELL 1
  #define WITH_EXEC 1
  #undef WITH_READLINE
  #define WITH_TUN 1
  #define WITH_PTY 1
  #define WITH_OPENSSL 1
  #undef WITH_FIPS
  #undef WITH_LIBWRAP
  #define WITH_SYCLS 1
  #define WITH_FILAN 1
  #define WITH_RETRY 1
  #undef WITH_DEVTESTS
  #define WITH_MSGLEVEL 0 /*debug*/
  #define WITH_DEFAULT_IPV 4

```




## 问题


#### symbol lookup error: /glc/readline/app/readline-8.0/lib/libreadline.so.8: undefined symbol: UP


```bash
2025-03-27T23:17:59 [mysql@ky10arm-98 /glc/socat/app/socat-1.8.0.3/bin] $ ll
total 460
lrwxrwxrwx 1 mysql mysql      6 Mar 27 23:13 socat -> socat1
-rwxr-xr-x 1 mysql mysql 467320 Mar 27 23:13 socat1
2025-03-27T23:18:00 [mysql@ky10arm-98 /glc/socat/app/socat-1.8.0.3/bin] $ source ~/.bashrc
2025-03-27T23:18:11 [mysql@ky10arm-98 /glc/socat/app/socat-1.8.0.3/bin] $ ./socat -V
./socat: symbol lookup error: /glc/readline/app/readline-8.0/lib/libreadline.so.8: undefined symbol: UP
2025-03-27T23:18:16 [mysql@ky10arm-98 /glc/socat/app/socat-1.8.0.3/bin] $ ldd /glc/socat/app/socat-1.8.0.3/bin/socat
	linux-vdso.so.1 (0x0000fffe8eba0000)
	libutil.so.1 => /lib64/libutil.so.1 (0x0000fffe8eb60000)
	librt.so.1 => /lib64/librt.so.1 (0x0000fffe8eb30000)
	libreadline.so.8 => /glc/readline/app/readline-8.0/lib/libreadline.so.8 (0x0000fffe8eac0000)
	libssl.so.3 => /glc/openssl/app/openssl-3.2.0/lib/libssl.so.3 (0x0000fffe8e9b0000)
	libcrypto.so.3 => /glc/openssl/app/openssl-3.2.0/lib/libcrypto.so.3 (0x0000fffe8e460000)
	libc.so.6 => /lib64/libc.so.6 (0x0000fffe8e2d0000)
	/lib/ld-linux-aarch64.so.1 (0x0000fffe8ebb0000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x0000fffe8e290000)
	libdl.so.2 => /lib64/libdl.so.2 (0x0000fffe8e260000)
2025-03-27T23:18:33 [mysql@ky10arm-98 /glc/socat/app/socat-1.8.0.3/bin] $ ldd /glc/readline/app/readline-8.0/lib/libreadline.so.8
	linux-vdso.so.1 (0x0000fffcd4720000)
	libc.so.6 => /lib64/libc.so.6 (0x0000fffcd4510000)
	/lib/ld-linux-aarch64.so.1 (0x0000fffcd4730000)
2025-03-27T23:19:23 [mysql@ky10arm-98 /glc/socat/app/socat-1.8.0.3/bin] $ ldconfig -p | grep libncurses
	libncursesw.so.6 (libc6,AArch64) => /lib64/libncursesw.so.6
	libncursesw.so.5 (libc6,AArch64) => /lib64/libncursesw.so.5
	libncurses.so.6 (libc6,AArch64) => /lib64/libncurses.so.6
	libncurses.so.5 (libc6,AArch64) => /lib64/libncurses.so.5
	libncurses++w.so.6 (libc6,AArch64) => /lib64/libncurses++w.so.6
	libncurses++w.so.5 (libc6,AArch64) => /lib64/libncurses++w.so.5
	libncurses++w.so (libc6,AArch64) => /lib64/libncurses++w.so
	libncurses++.so.6 (libc6,AArch64) => /lib64/libncurses++.so.6
	libncurses++.so.5 (libc6,AArch64) => /lib64/libncurses++.so.5
	libncurses++.so (libc6,AArch64) => /lib64/libncurses++.so
2025-03-27T23:19:45 [mysql@ky10arm-98 /glc/socat/app/socat-1.8.0.3/bin] $ nm -D /glc/readline/app/readline-8.0/lib/libreadline.so.8 | grep UP
                 U UP


```
- 解决：添加`-disable-readline`




#### 1. socat的configure脚本测试readline不通过原因

```bash
      cat confdefs.h - <<_ACEOF >conftest.$ac_ext
/* end confdefs.h.  */
 #include <stdio.h>
          #include <readline/readline.h>
          #include <readline/history.h>
int
main (void)
{
 readline(NULL);
          append_history(0, NULL);
  ;
  return 0;
}
_ACEOF
```


## 2. 确定readline动态库缺少ncures相关符合

```bash
2025-04-01T20:26:18 [mysql@ky10arm-98 /home/mysql/tmp] $ gcc main.c
/usr/bin/ld: /tmp/ccwyarf6.o: in function `main':
main.c:(.text+0xc): undefined reference to `readline'
/usr/bin/ld: main.c:(.text+0x18): undefined reference to `append_history'
collect2: error: ld returned 1 exit status
2025-04-01T20:26:26 [mysql@ky10arm-98 /home/mysql/tmp] $ cat main.c
 #include <stdio.h>
          #include <readline/readline.h>
          #include <readline/history.h>
int
main (void)
{
 readline(NULL);
          append_history(0, NULL);
  ;
  return 0;
}
2025-04-01T20:27:02 [mysql@ky10arm-98 /home/mysql/tmp] $ ll
total 4
-rw-r--r-- 1 mysql mysql 189 Apr  1 20:26 main.c
2025-04-01T20:27:54 [mysql@ky10arm-98 /home/mysql/tmp] $ gcc main.c  -o main -lreadline
/usr/bin/ld: /glc/readline/app/readline-8.2/lib/libreadline.so: undefined reference to `tputs'
/usr/bin/ld: /glc/readline/app/readline-8.2/lib/libreadline.so: undefined reference to `tgoto'
/usr/bin/ld: /glc/readline/app/readline-8.2/lib/libreadline.so: undefined reference to `tgetflag'
/usr/bin/ld: /glc/readline/app/readline-8.2/lib/libreadline.so: undefined reference to `UP'
/usr/bin/ld: /glc/readline/app/readline-8.2/lib/libreadline.so: undefined reference to `tgetent'
/usr/bin/ld: /glc/readline/app/readline-8.2/lib/libreadline.so: undefined reference to `tgetnum'
/usr/bin/ld: /glc/readline/app/readline-8.2/lib/libreadline.so: undefined reference to `PC'
/usr/bin/ld: /glc/readline/app/readline-8.2/lib/libreadline.so: undefined reference to `tgetstr'
/usr/bin/ld: /glc/readline/app/readline-8.2/lib/libreadline.so: undefined reference to `BC'
collect2: error: ld returned 1 exit status
2025-04-01T20:29:59 [mysql@ky10arm-98 /home/mysql/tmp] $ gcc main.c  -o main -lhistory -lreadline
/usr/bin/ld: /glc/readline/app/readline-8.2/lib/libreadline.so: undefined reference to `tputs'
/usr/bin/ld: /glc/readline/app/readline-8.2/lib/libreadline.so: undefined reference to `tgoto'
/usr/bin/ld: /glc/readline/app/readline-8.2/lib/libreadline.so: undefined reference to `tgetflag'
/usr/bin/ld: /glc/readline/app/readline-8.2/lib/libreadline.so: undefined reference to `UP'
/usr/bin/ld: /glc/readline/app/readline-8.2/lib/libreadline.so: undefined reference to `tgetent'
/usr/bin/ld: /glc/readline/app/readline-8.2/lib/libreadline.so: undefined reference to `tgetnum'
/usr/bin/ld: /glc/readline/app/readline-8.2/lib/libreadline.so: undefined reference to `PC'
/usr/bin/ld: /glc/readline/app/readline-8.2/lib/libreadline.so: undefined reference to `tgetstr'
/usr/bin/ld: /glc/readline/app/readline-8.2/lib/libreadline.so: undefined reference to `BC'
collect2: error: ld returned 1 exit status
2025-04-01T20:30:29 [mysql@ky10arm-98 /home/mysql/tmp] $ gcc main.c  -o main -lhistory -lreadline -ltinfo
2025-04-01T20:30:39 [mysql@ky10arm-98 /home/mysql/tmp] $ ll
total 20
-rwxr-xr-x 1 mysql mysql 71056 Apr  1 20:30 main
-rw-r--r-- 1 mysql mysql   189 Apr  1 20:26 main.c
2025-04-01T20:30:41 [mysql@ky10arm-98 /home/mysql/tmp] $ ./main

2025-04-01T20:35:01 [mysql@ky10arm-98 /home/mysql/tmp] $ ll
total 20
-rwxr-xr-x 1 mysql mysql 71056 Apr  1 20:30 main
-rw-r--r-- 1 mysql mysql   189 Apr  1 20:26 main.c
2025-04-01T20:35:02 [mysql@ky10arm-98 /home/mysql/tmp] $ ldd -r main
	linux-vdso.so.1 (0x0000ffff82ff0000)
	libhistory.so.8 => /glc/readline/app/readline-8.2/lib/libhistory.so.8 (0x0000ffff82fb0000)
	libreadline.so.8 => /glc/readline/app/readline-8.2/lib/libreadline.so.8 (0x0000ffff82f40000)
	libtinfo.so.6 => /glc/ncurses/app/ncurses-6.4/lib/libtinfo.so.6 (0x0000ffff82ee0000)
	libc.so.6 => /lib64/libc.so.6 (0x0000ffff82d50000)
	/lib/ld-linux-aarch64.so.1 (0x0000ffff83000000)
2025-04-01T20:35:05 [mysql@ky10arm-98 /home/mysql/tmp] $

```


#### 3. 确定readline安装的动态库不正常

```bash
2025-04-01T20:30:51 [mysql@ky10arm-98 /home/mysql/tmp] $ cd /glc/readline/app/readline-8.2/lib/
ll2025-04-01T20:33:31 [mysql@ky10arm-98 /glc/readline/app/readline-8.2/lib] $ ll
total 4076
-rw-r--r-- 1 mysql mysql  298388 Apr  1 19:08 libhistory.a
lrwxrwxrwx 1 mysql mysql      15 Apr  1 19:08 libhistory.so -> libhistory.so.8
lrwxrwxrwx 1 mysql mysql      17 Apr  1 19:08 libhistory.so.8 -> libhistory.so.8.2
-rwxr-xr-x 1 mysql mysql  222720 Apr  1 19:08 libhistory.so.8.2
-rw-r--r-- 1 mysql mysql 2374622 Apr  1 19:08 libreadline.a
lrwxrwxrwx 1 mysql mysql      16 Apr  1 19:08 libreadline.so -> libreadline.so.8
lrwxrwxrwx 1 mysql mysql      18 Apr  1 19:08 libreadline.so.8 -> libreadline.so.8.2
-rwxr-xr-x 1 mysql mysql 1293960 Apr  1 19:08 libreadline.so.8.2
drwxr-xr-x 2 mysql mysql      43 Apr  1 19:08 pkgconfig
2025-04-01T20:33:31 [mysql@ky10arm-98 /glc/readline/app/readline-8.2/lib] $ ldd -r libhistory.so.8.2
	linux-vdso.so.1 (0x0000fffc2f900000)
	libc.so.6 => /lib64/libc.so.6 (0x0000fffc2f730000)
	/lib/ld-linux-aarch64.so.1 (0x0000fffc2f910000)
2025-04-01T20:33:38 [mysql@ky10arm-98 /glc/readline/app/readline-8.2/lib] $ ldd -r libreadline.so.8.2
	linux-vdso.so.1 (0x0000fffe0ebd0000)
	libc.so.6 => /lib64/libc.so.6 (0x0000fffe0e9c0000)
	/lib/ld-linux-aarch64.so.1 (0x0000fffe0ebe0000)
undefined symbol: UP	(./libreadline.so.8.2)
undefined symbol: PC	(./libreadline.so.8.2)
undefined symbol: BC	(./libreadline.so.8.2)
undefined symbol: tputs	(./libreadline.so.8.2)
undefined symbol: tgoto	(./libreadline.so.8.2)
undefined symbol: tgetflag	(./libreadline.so.8.2)
undefined symbol: tgetent	(./libreadline.so.8.2)
undefined symbol: tgetnum	(./libreadline.so.8.2)
undefined symbol: tgetstr	(./libreadline.so.8.2)
2025-04-01T20:33:46 [mysql@ky10arm-98 /glc/readline/app/readline-8.2/lib] $

```


#### 重新安装readline


```bash
2025-04-01T20:39:01 [mysql@ky10arm-98 /glc/readline/source/readline-8.2/build_20250401T190806] $ sed 10q config.log
This file contains any messages produced by compilers while
running configure, to aid debugging if configure makes a mistake.

It was created by readline configure 8.2, which was
generated by GNU Autoconf 2.71.  Invocation command line was

  $ ../configure --prefix=/glc/readline/app/readline-8.2 --enable-shared --with-curses

## --------- ##
## Platform. ##
2025-04-01T20:39:16 [mysql@ky10arm-98 /glc/readline/source/readline-8.2/build_20250401T190806] $ ll
total 180
-rw-r--r-- 1 mysql mysql   7653 Apr  1 19:08 config.h
-rw-r--r-- 1 mysql mysql 107776 Apr  1 19:08 config.log
-rwxr-xr-x 1 mysql mysql  32171 Apr  1 19:08 config.status
drwxr-xr-x 2 mysql mysql     22 Apr  1 19:08 doc
drwxr-xr-x 2 mysql mysql     22 Apr  1 19:08 examples
-rw-r--r-- 1 mysql mysql    323 Apr  1 19:08 history.pc
-rw-r--r-- 1 mysql mysql  19529 Apr  1 19:08 Makefile
-rw-r--r-- 1 mysql mysql    337 Apr  1 19:08 readline.pc
drwxr-xr-x 2 mysql mysql     22 Apr  1 20:38 shlib
-rw-r--r-- 1 mysql mysql      1 Apr  1 19:08 stamp-h
2025-04-01T20:39:57 [mysql@ky10arm-98 /glc/readline/source/readline-8.2/build_20250401T190806] $ rm -rf ./*
2025-04-01T20:40:02 [mysql@ky10arm-98 /glc/readline/source/readline-8.2/build_20250401T190806] $ ll
total 0
2025-04-01T20:40:03 [mysql@ky10arm-98 /glc/readline/source/readline-8.2/build_20250401T190806] $ sh -x -v ../configure --prefix=/glc/readline/app/readline-8.2 --enable-shared --with-curses > c.log 2>&1



```


修改/glc/readline/source/readline-8.2/build_20250401T190806/shlib/Makefile中的SHLIB_LIBS为`-ltinfo`

SHLIB_LIBS原本为空
```bash
SHLIB_LIBS =  -ltinfo
```

修改好了后再make即可


make的时候观察`gcc -shared`即可，其他临时.so或.o文件不需要链接tinfo

```bash
2025-04-01T20:55:43 [mysql@ky10arm-98 /glc/readline/source/readline-8.2/build_20250401T190806/shlib] $ vim Makefile
2025-04-01T20:58:21 [mysql@ky10arm-98 /glc/readline/source/readline-8.2/build_20250401T190806/shlib] $ make
rm -f readline.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o readline.o ../../readline.c
mv readline.o readline.so
rm -f vi_mode.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o vi_mode.o ../../vi_mode.c
mv vi_mode.o vi_mode.so
rm -f funmap.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o funmap.o ../../funmap.c
mv funmap.o funmap.so
rm -f keymaps.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o keymaps.o ../../keymaps.c
mv keymaps.o keymaps.so
rm -f parens.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o parens.o ../../parens.c
mv parens.o parens.so
rm -f search.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o search.o ../../search.c
mv search.o search.so
rm -f rltty.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o rltty.o ../../rltty.c
mv rltty.o rltty.so
rm -f complete.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o complete.o ../../complete.c
mv complete.o complete.so
rm -f bind.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o bind.o ../../bind.c
mv bind.o bind.so
rm -f isearch.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o isearch.o ../../isearch.c
mv isearch.o isearch.so
rm -f display.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o display.o ../../display.c
mv display.o display.so
rm -f signals.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o signals.o ../../signals.c
mv signals.o signals.so
rm -f util.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o util.o ../../util.c
mv util.o util.so
rm -f kill.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o kill.o ../../kill.c
mv kill.o kill.so
rm -f undo.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o undo.o ../../undo.c
mv undo.o undo.so
rm -f macro.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o macro.o ../../macro.c
mv macro.o macro.so
rm -f input.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o input.o ../../input.c
mv input.o input.so
rm -f callback.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o callback.o ../../callback.c
mv callback.o callback.so
rm -f terminal.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o terminal.o ../../terminal.c
mv terminal.o terminal.so
rm -f text.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o text.o ../../text.c
mv text.o text.so
rm -f nls.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o nls.o ../../nls.c
mv nls.o nls.so
rm -f misc.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o misc.o ../../misc.c
mv misc.o misc.so
rm -f history.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o history.o ../../history.c
mv history.o history.so
rm -f histexpand.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o histexpand.o ../../histexpand.c
mv histexpand.o histexpand.so
rm -f histfile.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o histfile.o ../../histfile.c
mv histfile.o histfile.so
rm -f histsearch.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o histsearch.o ../../histsearch.c
mv histsearch.o histsearch.so
rm -f shell.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o shell.o ../../shell.c
mv shell.o shell.so
rm -f mbutil.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o mbutil.o ../../mbutil.c
mv mbutil.o mbutil.so
rm -f tilde.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -DREADLINE_LIBRARY -c -o tilde.o ../../tilde.c
mv tilde.o tilde.so
rm -f colors.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o colors.o ../../colors.c
mv colors.o colors.so
rm -f parse-colors.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o parse-colors.o ../../parse-colors.c
mv parse-colors.o parse-colors.so
rm -f xmalloc.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o xmalloc.o ../../xmalloc.c
mv xmalloc.o xmalloc.so
rm -f xfree.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o xfree.o ../../xfree.c
mv xfree.o xfree.so
rm -f compat.so
gcc -c -DHAVE_CONFIG_H   -I. -I.. -I../..  -DRL_LIBRARY_VERSION='"8.2"' -DBRACKETED_PASTE_DEFAULT=1 -g -O2 -Wno-parentheses -Wno-format-security -Wno-tautological-constant-out-of-range-compare -fPIC -o compat.o ../../compat.c
mv compat.o compat.so
rm -f libreadline.so.8.2
gcc -shared -Wl,-soname,libreadline.so.8.2 -Wl,-rpath,/glc/readline/app/readline-8.2/lib -Wl,-soname,`basename libreadline.so.8.2 .2` -o libreadline.so.8.2 readline.so vi_mode.so funmap.so keymaps.so parens.so search.so rltty.so complete.so bind.so isearch.so display.so signals.so util.so kill.so undo.so macro.so input.so callback.so terminal.so text.so nls.so misc.so history.so histexpand.so histfile.so histsearch.so shell.so mbutil.so tilde.so colors.so parse-colors.so xmalloc.so xfree.so compat.so -ltinfo
rm -f libhistory.so.8.2
gcc -shared -Wl,-soname,libhistory.so.8.2 -Wl,-rpath,/glc/readline/app/readline-8.2/lib -Wl,-soname,`basename libhistory.so.8.2 .2` -o libhistory.so.8.2 history.so histexpand.so histfile.so histsearch.so shell.so mbutil.so xmalloc.so xfree.so -ltinfo -ltinfo
2025-04-01T20:58:42 [mysql@ky10arm-98 /glc/readline/source/readline-8.2/build_20250401T190806/shlib] $ ll
total 3844
-rw-r--r-- 1 mysql mysql  218648 Apr  1 20:58 bind.so
-rw-r--r-- 1 mysql mysql   40592 Apr  1 20:58 callback.so
-rw-r--r-- 1 mysql mysql   32208 Apr  1 20:58 colors.so
-rw-r--r-- 1 mysql mysql   10496 Apr  1 20:58 compat.so
-rw-r--r-- 1 mysql mysql  184480 Apr  1 20:58 complete.so
-rw-r--r-- 1 mysql mysql  214000 Apr  1 20:58 display.so
-rw-r--r-- 1 mysql mysql   36608 Apr  1 20:58 funmap.so
-rw-r--r-- 1 mysql mysql  107192 Apr  1 20:58 histexpand.so
-rw-r--r-- 1 mysql mysql   55840 Apr  1 20:58 histfile.so
-rw-r--r-- 1 mysql mysql   39104 Apr  1 20:58 history.so
-rw-r--r-- 1 mysql mysql   20744 Apr  1 20:58 histsearch.so
-rw-r--r-- 1 mysql mysql   62352 Apr  1 20:58 input.so
-rw-r--r-- 1 mysql mysql   66672 Apr  1 20:58 isearch.so
-rw-r--r-- 1 mysql mysql   60144 Apr  1 20:58 keymaps.so
-rw-r--r-- 1 mysql mysql   79944 Apr  1 20:58 kill.so
-rwxr-xr-x 1 mysql mysql  222720 Apr  1 20:58 libhistory.so.8.2
-rwxr-xr-x 1 mysql mysql 1293960 Apr  1 20:58 libreadline.so.8.2
-rw-r--r-- 1 mysql mysql   41856 Apr  1 20:58 macro.so
-rw-r--r-- 1 mysql mysql   18901 Apr  1 20:58 Makefile
-rw-r--r-- 1 mysql mysql   49520 Apr  1 20:58 mbutil.so
-rw-r--r-- 1 mysql mysql   70448 Apr  1 20:58 misc.so
-rw-r--r-- 1 mysql mysql   51728 Apr  1 20:58 nls.so
-rw-r--r-- 1 mysql mysql   32624 Apr  1 20:58 parens.so
-rw-r--r-- 1 mysql mysql   36120 Apr  1 20:58 parse-colors.so
-rw-r--r-- 1 mysql mysql  102584 Apr  1 20:58 readline.so
-rw-r--r-- 1 mysql mysql   54088 Apr  1 20:58 rltty.so
-rw-r--r-- 1 mysql mysql   68864 Apr  1 20:58 search.so
-rw-r--r-- 1 mysql mysql   13824 Apr  1 20:58 shell.so
-rw-r--r-- 1 mysql mysql   55440 Apr  1 20:58 signals.so
-rw-r--r-- 1 mysql mysql   73272 Apr  1 20:58 terminal.so
-rw-r--r-- 1 mysql mysql  142736 Apr  1 20:58 text.so
-rw-r--r-- 1 mysql mysql   22680 Apr  1 20:58 tilde.so
-rw-r--r-- 1 mysql mysql   44272 Apr  1 20:58 undo.so
-rw-r--r-- 1 mysql mysql   47904 Apr  1 20:58 util.so
-rw-r--r-- 1 mysql mysql  199136 Apr  1 20:58 vi_mode.so
-rw-r--r-- 1 mysql mysql    3688 Apr  1 20:58 xfree.so
-rw-r--r-- 1 mysql mysql    8936 Apr  1 20:58 xmalloc.so

2025-04-01T21:01:32 [mysql@ky10arm-98 /glc/readline/source/readline-8.2/build_20250401T190806/shlib] $ ldd -r libhistory.so.8.2
	linux-vdso.so.1 (0x0000fffd9bb10000)
	libtinfo.so.6 => /glc/ncurses/app/ncurses-6.4/lib/libtinfo.so.6 (0x0000fffd9ba70000)
	libc.so.6 => /lib64/libc.so.6 (0x0000fffd9b8e0000)
	/lib/ld-linux-aarch64.so.1 (0x0000fffd9bb20000)

2025-04-01T21:04:07 [mysql@ky10arm-98 /glc/readline/source/readline-8.2/build_20250401T190806/shlib] $ ldd -r libreadline.so.8.2
	linux-vdso.so.1 (0x0000ffffb57d0000)
	libtinfo.so.6 => /glc/ncurses/app/ncurses-6.4/lib/libtinfo.so.6 (0x0000ffffb56f0000)
	libc.so.6 => /lib64/libc.so.6 (0x0000ffffb5560000)
	/lib/ld-linux-aarch64.so.1 (0x0000ffffb57e0000)


```




#### Makefile




```makefile
# source: Makefile.in
# Copyright Gerhard Rieger and contributors (see file CHANGES)
# Published under the GNU General Public License V.2, see file COPYING

# note: @...@ forms are filled in by configure script

SHELL = /bin/sh
AR = ar
RANLIB = ranlib

.SUFFIXES: .c .o

prefix = /glc/socat/app/socat-1.8.0.3
exec_prefix = ${prefix}

BINDEST = ${exec_prefix}/bin

datarootdir = ${prefix}/share
MANDEST = ${datarootdir}/man

srcdir = ..
VPATH = ..

CC = gcc
#CCOPT1=-no-pie -fstack-protector
#CCOPT=$(CCOPT1) -fcf-protection=none 	# for gdb on Ubuntu-20.04
CCOPTS = $(CCOPT)

SYSDEFS =
CPPFLAGS = -I.
#0 INCLS = -I. @V_INCL@
DEFS = -DHAVE_CONFIG_H
LIBS = -lutil  -lrt -lreadline
LDFLAGS =

INSTALL = /usr/bin/install -c

#OBJ = $(CSRC:.c=.o) $(GENSRC:.c=.o)


#0 CFLAGS = -O -D_GNU_SOURCE -Wall -Wno-parentheses $(CCOPTS) $(DEFS) $(INCLS)
CFLAGS = -O -D_GNU_SOURCE -Wall -Wno-parentheses $(CCOPTS) $(DEFS) $(CPPFLAGS)
CLIBS = $(LIBS)
#CLIBS = $(LIBS) -lm -lefence
XIOSRCS = xioinitialize.c xiohelp.c xioparam.c xiodiag.c xioopen.c xioopts.c \
	xiosignal.c xiosigchld.c xioread.c xiowrite.c \
	xiolayer.c xioshutdown.c xioclose.c xioexit.c \
	xio-process.c xio-fd.c xio-fdnum.c xio-stdio.c xio-pipe.c \
	xio-socketpair.c xio-gopen.c xio-creat.c xio-file.c xio-named.c \
	xio-socket.c xio-interface.c xio-listen.c xio-unix.c xio-vsock.c \
	xio-ip.c xio-ip4.c xio-ip6.c xio-ipapp.c xio-tcp.c \
	xio-sctp.c xio-dccp.c xio-rawip.c xio-posixmq.c \
	xio-socks.c xio-socks5.c xio-proxy.c xio-udp.c xio-udplite.c \
	xio-progcall.c xio-exec.c xio-system.c xio-shell.c \
	xio-termios.c xio-readline.c \
	xio-pty.c xio-openssl.c xio-streams.c xio-namespaces.c \
	xio-ascii.c xiolockfile.c xio-tcpwrap.c xio-fs.c xio-tun.c
XIOOBJS = $(XIOSRCS:.c=.o)
UTLSRCS = error.c dalan.c procan.c procan-cdefs.c hostan.c fdname.c sysutils.c utils.c nestlex.c vsnprintf_r.c snprinterr.c filan.c sycls.c sslcls.c
UTLOBJS = $(UTLSRCS:.c=.o)
CFILES = $(XIOSRCS) $(UTLSRCS) socat.c procan_main.c filan_main.c
OFILES = $(CFILES:.c=.o)
PROGS = socat procan filan

HFILES = sycls.h sslcls.h error.h dalan.h procan.h filan.h hostan.h sysincludes.h xio.h xioopen.h sysutils.h utils.h nestlex.h vsnprintf_r.h snprinterr.h compat.h \
	xioconfig.h mytypes.h xioopts.h xiodiag.h xiohelp.h xiosysincludes.h \
	xiomodes.h xiolayer.h xio-process.h xio-fd.h xio-fdnum.h xio-stdio.h \
	xio-named.h xio-file.h xio-creat.h xio-gopen.h xio-pipe.h \
	xio-socketpair.h xio-socket.h xio-interface.h xio-listen.h xio-unix.h xio-vsock.h \
	xio-ip.h xio-ip4.h xio-ip6.h xio-rawip.h xio-posixmq.h \
	xio-ipapp.h xio-tcp.h xio-udp.h xio-sctp.h xio-dccp.h xio-udplite.h \
	xio-socks.h xio-socks5.h xio-proxy.h xio-progcall.h xio-exec.h \
	xio-system.h xio-shell.h xio-termios.h xio-readline.h \
	xio-pty.h xio-openssl.h xio-streams.h xio-namespaces.h \
	xio-ascii.h xiolockfile.h xio-tcpwrap.h xio-fs.h xio-tun.h


DOCFILES = README README.FIPS CHANGES FILES EXAMPLES PORTING SECURITY DEVELOPMENT doc/socat.yo doc/socat.1 doc/socat.html FAQ BUGREPORTS COPYING COPYING.OpenSSL doc/dest-unreach.css doc/socat-openssltunnel.html doc/socat-multicast.html doc/socat-tun.html doc/socat-genericsocket.html
SHFILES = socat-chain.sh socat-mux.sh socat-broker.sh \
	daemon.sh mail.sh ftp.sh readline.sh \
	socat_buildscript_for_android.sh
TESTFILES = test.sh socks4echo.sh proxyecho.sh readline-test.sh \
	proxy.sh socks4a-echo.sh socks5server-echo.sh

all: progs doc

scmclean: gitclean

gitclean: distclean docclean
	rm -f Makefile.bak configure

doc: doc/socat.1 doc/socat.html

docclean:
	rm -f doc/socat.1 doc/socat.html

doc/socat.1: doc/socat.yo
	-mkdir -p $(@D); yodl2man -o $@ $+

doc/socat.html: doc/socat.yo
# care for refs in html
	-mkdir -p $(@D); cd $(@D); yodl2html -o socat.html ../$+; cd ..

progs: $(PROGS)

depend: $(CFILES) $(HFILES)
	makedepend $(SYSDEFS) $(CFILES)

socat: socat.o libxio.a
	$(CC) $(CFLAGS) $(LDFLAGS) -o $@ socat.o libxio.a $(CLIBS)

procan.o: $(srcdir)/procan.c
	$(CC) $(CFLAGS) -c -D CC="\"$(CC)\"" -o $@ $(srcdir)/procan.c

PROCAN_OBJS=procan_main.o procan.o procan-cdefs.o hostan.o error.o sycls.o sysutils.o utils.o vsnprintf_r.o snprinterr.o
procan: $(PROCAN_OBJS)
	$(CC) $(CFLAGS) $(LDFLAGS) -o $@ $(PROCAN_OBJS) $(CLIBS)

FILAN_OBJS=filan_main.o filan.o fdname.o error.o sycls.o sysutils.o utils.o vsnprintf_r.o snprinterr.o
filan: $(FILAN_OBJS)
	$(CC) $(CFLAGS) $(LDFLAGS) -o $@ $(FILAN_OBJS) $(CLIBS)

libxio.a: $(XIOOBJS) $(UTLOBJS)
	$(AR) r $@ $(XIOOBJS) $(UTLOBJS)
	$(RANLIB) $@


strip: progs
	strip $(PROGS)

install: progs $(srcdir)/doc/socat.1
	mkdir -p $(DESTDIR)$(BINDEST)
	$(INSTALL) -m 755 socat $(DESTDIR)$(BINDEST)/socat1
	ln -sf socat1 $(DESTDIR)$(BINDEST)/socat
	$(INSTALL) -m 755 socat-chain.sh  $(DESTDIR)$(BINDEST)
	$(INSTALL) -m 755 socat-mux.sh    $(DESTDIR)$(BINDEST)
	$(INSTALL) -m 755 socat-broker.sh $(DESTDIR)$(BINDEST)
	$(INSTALL) -m 755 procan $(DESTDIR)$(BINDEST)
	$(INSTALL) -m 755 filan $(DESTDIR)$(BINDEST)
	mkdir -p $(DESTDIR)$(MANDEST)/man1
	$(INSTALL) -m 644 $(srcdir)/doc/socat.1 $(DESTDIR)$(MANDEST)/man1/socat1.1
	ln -sf socat1.1 $(DESTDIR)$(MANDEST)/man1/socat.1

uninstall:
	rm -f $(DESTDIR)$(BINDEST)/socat
	rm -f $(DESTDIR)$(BINDEST)/socat1
	rm -f $(DESTDIR)$(BINDEST)/socat-chain.sh
	rm -f $(DESTDIR)$(BINDEST)/socat-mux.sh
	rm -f $(DESTDIR)$(BINDEST)/socat-broker.sh
	rm -f $(DESTDIR)$(BINDEST)/procan
	rm -f $(DESTDIR)$(BINDEST)/filan
	rm -f $(DESTDIR)$(MANDEST)/man1/socat.1
	rm -f $(DESTDIR)$(MANDEST)/man1/socat1.1

# make a GNU-zipped tar ball of the source files
dist: socat.tar.gz socat.tar.bz2

socat.tar.gz: socat.tar
	gzip -9 <socat.tar >socat.tar.gz

socat.tar.bz2: socat.tar
	bzip2 -9 <socat.tar >socat.tar.bz2

VERSION = `sed 's/"//g' VERSION`
TARDIR = socat-$(VERSION)
socat.tar: configure.in configure Makefile.in config.h.in install-sh VERSION $(CFILES) $(HFILES) $(DOCFILES) $(SHFILES) $(TESTFILES) socat.spec \
	configure.ac
	if [ ! -d $(TARDIR) ]; then mkdir $(TARDIR); fi
	tar cf - $+ |(cd $(TARDIR); tar xf -)
	tar cvf socat.tar $(TARDIR)
	rm -f $(TARDIR)/COPYING		# write protected
	rm -r $(TARDIR)

clean:
	rm -f *.o libxio.a socat procan filan \
	socat.tar socat.tar.Z socat.tar.gz socat.tar.bz2 \
	socat.out compile.log test.log

# remove all files that are generated from the original socat distribution
# note that Makefile is also removed, so you have to start with ./configure
# again
distclean: clean
	rm -f config.status config.cache config.log config.h Makefile
	rm -rf autom4te.cache

info: socat
	uname -a >socat.out
	./socat -V >>socat.out
	./socat -hh >>socat.out

# perform some tests on socat
test: progs
	./test.sh

cert:
	# prepare critical files with correct permissions to avoid race cond
	>cert.key
	>cert.pem
	chmod 600 cert.key cert.pem
	# generate a private key
	openssl genrsa -out cert.key 1024
	# generate a self signed cert
	openssl req -new -key cert.key -x509 -days 3653 -out cert.crt
	# ...enter fields
	# generate the pem file
	cat cert.key cert.crt >cert.pem
	#echo use cert.pem on requestors side, i.e. with option cert=cert.pem
	#echo use cert.crt on checkers side, i.e. with option cafile=cert.crt


```

#### make

```bash
2025-03-30T23:18:58 [mysql@ky10arm-98 /glc/socat/source/socat-1.8.0.3/build_20250330T180509] $ make
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o socat.o ../socat.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xioinitialize.o ../xioinitialize.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xiohelp.o ../xiohelp.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xioparam.o ../xioparam.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xiodiag.o ../xiodiag.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xioopen.o ../xioopen.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xioopts.o ../xioopts.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xiosignal.o ../xiosignal.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xiosigchld.o ../xiosigchld.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xioread.o ../xioread.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xiowrite.o ../xiowrite.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xiolayer.o ../xiolayer.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xioshutdown.o ../xioshutdown.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xioclose.o ../xioclose.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xioexit.o ../xioexit.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-process.o ../xio-process.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-fd.o ../xio-fd.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-fdnum.o ../xio-fdnum.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-stdio.o ../xio-stdio.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-pipe.o ../xio-pipe.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-socketpair.o ../xio-socketpair.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-gopen.o ../xio-gopen.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-creat.o ../xio-creat.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-file.o ../xio-file.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-named.o ../xio-named.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-socket.o ../xio-socket.c
../xio-socket.c: In function ‘xiocheckpeer’:
../xio-socket.c:1692:8: warning: unused variable ‘result’ [-Wunused-variable]
 1692 |    int result;
      |        ^~~~~~
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-interface.o ../xio-interface.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-listen.o ../xio-listen.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-unix.o ../xio-unix.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-vsock.o ../xio-vsock.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-ip.o ../xio-ip.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-ip4.o ../xio-ip4.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-ip6.o ../xio-ip6.c
../xio-ip6.c: In function ‘xiolog_ancillary_ip6’:
../xio-ip6.c:271:9: warning: unused variable ‘scratch2’ [-Wunused-variable]
  271 |    char scratch2[32];
      |         ^~~~~~~~
../xio-ip6.c:270:9: warning: unused variable ‘scratch1’ [-Wunused-variable]
  270 |    char scratch1[42]; /* can hold an IPv6 address in ASCII */
      |         ^~~~~~~~
At top level:
../xio-ip6.c:359:14: warning: ‘inet6addr_info’ defined but not used [-Wunused-function]
  359 | static char *inet6addr_info(const struct in6_addr *sa, char *buff, size_t blen) {
      |              ^~~~~~~~~~~~~~
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-ipapp.o ../xio-ipapp.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-tcp.o ../xio-tcp.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-sctp.o ../xio-sctp.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-dccp.o ../xio-dccp.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-rawip.o ../xio-rawip.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-posixmq.o ../xio-posixmq.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-socks.o ../xio-socks.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-socks5.o ../xio-socks5.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-proxy.o ../xio-proxy.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-udp.o ../xio-udp.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-udplite.o ../xio-udplite.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-progcall.o ../xio-progcall.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-exec.o ../xio-exec.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-system.o ../xio-system.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-shell.o ../xio-shell.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-termios.o ../xio-termios.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-readline.o ../xio-readline.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-pty.o ../xio-pty.c
../xio-pty.c: In function ‘xioopen_pty’:
../xio-pty.c:50:10: warning: unused variable ‘tn’ [-Wunused-variable]
   50 |    char *tn = NULL;
      |          ^~
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-openssl.o ../xio-openssl.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-streams.o ../xio-streams.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-namespaces.o ../xio-namespaces.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-ascii.o ../xio-ascii.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xiolockfile.o ../xiolockfile.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-tcpwrap.o ../xio-tcpwrap.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-fs.o ../xio-fs.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o xio-tun.o ../xio-tun.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o error.o ../error.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o dalan.o ../dalan.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -c -D CC="\"gcc\"" -o procan.o ../procan.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o procan-cdefs.o ../procan-cdefs.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o hostan.o ../hostan.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o fdname.o ../fdname.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o sysutils.o ../sysutils.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o utils.o ../utils.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o nestlex.o ../nestlex.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o vsnprintf_r.o ../vsnprintf_r.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o snprinterr.o ../snprinterr.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o filan.o ../filan.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o sycls.o ../sycls.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o sslcls.o ../sslcls.c
ar r libxio.a xioinitialize.o xiohelp.o xioparam.o xiodiag.o xioopen.o xioopts.o xiosignal.o xiosigchld.o xioread.o xiowrite.o xiolayer.o xioshutdown.o xioclose.o xioexit.o xio-process.o xio-fd.o xio-fdnum.o xio-stdio.o xio-pipe.o xio-socketpair.o xio-gopen.o xio-creat.o xio-file.o xio-named.o xio-socket.o xio-interface.o xio-listen.o xio-unix.o xio-vsock.o xio-ip.o xio-ip4.o xio-ip6.o xio-ipapp.o xio-tcp.o xio-sctp.o xio-dccp.o xio-rawip.o xio-posixmq.o xio-socks.o xio-socks5.o xio-proxy.o xio-udp.o xio-udplite.o xio-progcall.o xio-exec.o xio-system.o xio-shell.o xio-termios.o xio-readline.o xio-pty.o xio-openssl.o xio-streams.o xio-namespaces.o xio-ascii.o xiolockfile.o xio-tcpwrap.o xio-fs.o xio-tun.o error.o dalan.o procan.o procan-cdefs.o hostan.o fdname.o sysutils.o utils.o nestlex.o vsnprintf_r.o snprinterr.o filan.o sycls.o sslcls.o
ar: creating libxio.a
ranlib libxio.a
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.   -o socat socat.o libxio.a -lutil  -lrt -lreadline
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o procan_main.o ../procan_main.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.   -o procan procan_main.o procan.o procan-cdefs.o hostan.o error.o sycls.o sysutils.o utils.o vsnprintf_r.o snprinterr.o -lutil  -lrt -lreadline
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.  -I.   -c -o filan_main.o ../filan_main.c
gcc -O -D_GNU_SOURCE -Wall -Wno-parentheses  -DHAVE_CONFIG_H -I.   -o filan filan_main.o filan.o fdname.o error.o sycls.o sysutils.o utils.o vsnprintf_r.o snprinterr.o -lutil  -lrt -lreadline


```




## /usr/bin/install: cannot stat 'socat-chain.sh': No such file or directory


```bash
2025-04-26 20:21:52,299 p=383453 u=guolicheng n=ansible | TASK [socat : cd /glc/socat/source/socat-1.8.0.3/build_20250426T201859;make install] ******************************************************************************************************************************************
*********************************************************************
2025-04-26 20:21:52,302 p=383453 u=guolicheng n=ansible | fatal: [centos8-25]: FAILED! => changed=true
  cmd: make install
  delta: '0:00:00.017756'
  end: '2025-04-26 20:21:52.225611'
  msg: non-zero return code
  rc: 2
  start: '2025-04-26 20:21:52.207855'
  stderr: |-
    /usr/bin/install: cannot stat 'socat-chain.sh': No such file or directory
    make: *** [Makefile:135: install] Error 1
  stderr_lines: <omitted>
  stdout: |-
    mkdir -p /glc/socat/app/socat-1.8.0.3/bin
    /usr/bin/install -c -m 755 socat /glc/socat/app/socat-1.8.0.3/bin/socat1
    ln -sf socat1 /glc/socat/app/socat-1.8.0.3/bin/socat
    /usr/bin/install -c -m 755 socat-chain.sh  /glc/socat/app/socat-1.8.0.3/bin
  stdout_lines: <omitted>
2025-04-26 20:21:52,873 p=383453 u=guolicheng n=ansible | fatal: [node-227]: FAILED! => changed=true
  cmd: make install
  delta: '0:00:00.028824'
  end: '2025-04-26 20:21:52.694162'
  msg: non-zero return code
  rc: 2
  start: '2025-04-26 20:21:52.665338'
  stderr: |-
    /usr/bin/install: cannot stat 'socat-chain.sh': No such file or directory
    make: *** [Makefile:135: install] Error 1
  stderr_lines: <omitted>
  stdout: |-
    mkdir -p /glc/socat/app/socat-1.8.0.3/bin
    /usr/bin/install -c -m 755 socat /glc/socat/app/socat-1.8.0.3/bin/socat1
    ln -sf socat1 /glc/socat/app/socat-1.8.0.3/bin/socat
    /usr/bin/install -c -m 755 socat-chain.sh  /glc/socat/app/socat-1.8.0.3/bin
  stdout_lines: <omitted>
2025-04-26 20:21:52,875 p=383453 u=guolicheng n=ansible | PLAY RECAP ******************************************************************************************************
2025-04-26 20:21:52,875 p=383453 u=guolicheng n=ansible | centos8-25                 : ok=64   changed=43   unreachable=0    failed=1    skipped=15   rescued=0    ignored=0
2025-04-26 20:21:52,875 p=383453 u=guolicheng n=ansible | node-227                   : ok=64   changed=43   unreachable=0    failed=1    skipped=15   rescued=0    ignored=0
2025-04-26 20:21:52,875 p=383453 u=guolicheng n=ansible | node-250                   : ok=63   changed=42   unreachable=0    failed=1    skipped=15   rescued=0    ignored=0
```


- 问题原因：编译路径需要在源码路径下，如果在其他路径下，源码目录下面的`socat-chain.sh`不会拷贝到编译路径下面













