---
title: readline
description: readline
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
digraph "readline" {
node [
        fontsize = "12"
];

"readline" [ label = "readline", shape = octagon ];
"glibc" [ label = "glibc", shape = octagon ];
"libc.so.6" [ label = "libc.so.6", shape = octagon ];
"/lib/ld-linux-aarch64.so.1" [ label = "/lib/ld-linux-aarch64.so.1", shape = octagon ];
"readline" -> "glibc";
"glibc" -> "libc.so.6";
"glibc" -> "/lib/ld-linux-aarch64.so.1";

}
```



## 2. 内容


### 2.1 readline

```viz-dot
digraph "readline" {
node [
        fontsize = "12"
];
    "readline" [ label = "readline", shape = octagon ];

    "readline" -> "libreadline.so.6.2";

}
```

### 2.2 readline-devel

```viz-dot
digraph "readline-devel" {
node [
        fontsize = "12"
];
    rankdir="LR";
    "readline-devel" [ label = "readline-devel", shape = octagon ];
    "libhistory.so" [ label = "libhistory.so", shape = octagon ];
    "libreadline.so" [ label = "libreadline.so", shape = octagon ];
    "readline/chardefs.h" [ label = "readline/chardefs.h", shape = octagon ];
    "readline/history.h" [ label = "readline/history.h", shape = octagon ];
    "readline/keymaps.h" [ label = "readline/keymaps.h", shape = octagon ];
    "readline/readline.h" [ label = "readline/readline.h", shape = octagon ];
    "readline/rlconf.h" [ label = "readline/rlconf.h", shape = octagon ];
    "readline/rlstdc.h" [ label = "readline/rlstdc.h", shape = octagon ];
    "readline/rltypedefs.h" [ label = "readline/rltypedefs.h", shape = octagon ];
    "readline/tilde.h" [ label = "readline/tilde.h", shape = octagon ];


    "readline-devel" -> "libreadline.so";
    "readline-devel" -> "libhistory.so";
    "readline-devel" -> "readline/chardefs.h";
    "readline-devel" -> "readline/history.h";
    "readline-devel" -> "readline/keymaps.h";
    "readline-devel" -> "readline/readline.h";
    "readline-devel" -> "readline/rlconf.h";
    "readline-devel" -> "readline/rlstdc.h";
    "readline-devel" -> "readline/rltypedefs.h";
    "readline-devel" -> "readline/tilde.h";


}
```


## 3. 安装


#### 3.1 yum安装

```bash
yum -y install readline readline-devel

```

#### 3.2 readline

```bash
2025-03-23T13:56:18 [root@x86centos7-101 /data/app/mysql-8.0.26/bin] # rpm -qa|grep readline
readline-devel-6.2-11.el7.x86_64
readline-6.2-11.el7.x86_64

2025-03-23T15:25:49 [root@x86centos7-101 /data/app/mysql-8.0.26/bin] # rpm -ql readline-6.2-11.el7.x86_64
/lib64/libreadline.so.6
/lib64/libreadline.so.6.2
/usr/lib64/libhistory.so.6
/usr/lib64/libhistory.so.6.2
/usr/share/doc/readline-6.2
/usr/share/doc/readline-6.2/CHANGES
/usr/share/doc/readline-6.2/COPYING
/usr/share/doc/readline-6.2/NEWS
/usr/share/doc/readline-6.2/README
/usr/share/doc/readline-6.2/USAGE
/usr/share/info/history.info.gz
/usr/share/info/rluserman.info.gz



```

#### 3.3 readline-devel

```bash
2025-03-23T15:26:01 [root@x86centos7-101 /data/app/mysql-8.0.26/bin] # rpm -ql readline-devel-6.2-11.el7.x86_64
/usr/include/readline
/usr/include/readline/chardefs.h
/usr/include/readline/history.h
/usr/include/readline/keymaps.h
/usr/include/readline/readline.h
/usr/include/readline/rlconf.h
/usr/include/readline/rlstdc.h
/usr/include/readline/rltypedefs.h
/usr/include/readline/tilde.h
/usr/lib64/libhistory.so
/usr/lib64/libreadline.so
/usr/share/doc/readline-devel-6.2
/usr/share/doc/readline-devel-6.2/excallback.c
/usr/share/doc/readline-devel-6.2/fileman.c
/usr/share/doc/readline-devel-6.2/histexamp.c
/usr/share/doc/readline-devel-6.2/manexamp.c
/usr/share/doc/readline-devel-6.2/readlinebuf.h
/usr/share/doc/readline-devel-6.2/rl-fgets.c
/usr/share/doc/readline-devel-6.2/rl.c
/usr/share/doc/readline-devel-6.2/rlcat.c
/usr/share/doc/readline-devel-6.2/rlevent.c
/usr/share/doc/readline-devel-6.2/rlfe
/usr/share/doc/readline-devel-6.2/rlfe/ChangeLog
/usr/share/doc/readline-devel-6.2/rlfe/Makefile.in
/usr/share/doc/readline-devel-6.2/rlfe/README
/usr/share/doc/readline-devel-6.2/rlfe/config.h.in
/usr/share/doc/readline-devel-6.2/rlfe/configure.in
/usr/share/doc/readline-devel-6.2/rlfe/extern.h
/usr/share/doc/readline-devel-6.2/rlfe/os.h
/usr/share/doc/readline-devel-6.2/rlfe/pty.c
/usr/share/doc/readline-devel-6.2/rlfe/rlfe.c
/usr/share/doc/readline-devel-6.2/rlfe/screen.h
/usr/share/doc/readline-devel-6.2/rlptytest.c
/usr/share/doc/readline-devel-6.2/rltest.c
/usr/share/doc/readline-devel-6.2/rlversion.c
/usr/share/info/readline.info.gz
/usr/share/man/man3/history.3.gz
/usr/share/man/man3/readline.3.gz


2025-03-23T15:32:59 [root@x86centos7-101 /lib64] # ldd /lib64/libreadline.so.6.2
	linux-vdso.so.1 =>  (0x00007fffa1ed1000)
	libtinfo.so.5 => /lib64/libtinfo.so.5 (0x00007fc124b18000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fc12474b000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fc124f88000)

2025-03-23T15:44:01 [root@x86centos7-101 /lib64] # ldd /usr/lib64/libreadline.so
	linux-vdso.so.1 =>  (0x00007ffc211e3000)
	libtinfo.so.5 => /lib64/libtinfo.so.5 (0x00007fe99fb7c000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fe99f7af000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fe99ffec000)

2025-03-23T15:44:20 [root@x86centos7-101 /lib64] # ldd /usr/lib64/libhistory.so
	linux-vdso.so.1 =>  (0x00007fff62bcb000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fd07bb19000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fd07c0ef000)
2025-03-23T15:44:43 [root@x86centos7-101 /lib64] #

2025-03-23T15:44:43 [root@x86centos7-101 /lib64] # rpm -qf /lib64/libtinfo.so.5
ncurses-libs-5.9-14.20130511.el7_4.x86_64

```



#### 3.4 源码安装

```bash


```

## 4. 问题



#### 4.1 symbol lookup error: /glc/readline/app/readline-8.0/lib/libreadline.so.8: undefined symbol: UP

```bash
2025-03-29T11:05:15 [mysql@ky10arm-98 /glc/socat/source/socat-1.8.0.3/build_20250329T103836] $ ./socat -V
./socat: symbol lookup error: /glc/readline/app/readline-8.0/lib/libreadline.so.8: undefined symbol: UP
2025-03-29T11:05:29 [mysql@ky10arm-98 /glc/socat/source/socat-1.8.0.3/build_20250329T103836] $ ldd -r ./socat
	linux-vdso.so.1 (0x0000fffc5ee00000)
	libutil.so.1 => /lib64/libutil.so.1 (0x0000fffc5edc0000)
	librt.so.1 => /lib64/librt.so.1 (0x0000fffc5ed90000)
	libreadline.so.8 => /glc/readline/app/readline-8.0/lib/libreadline.so.8 (0x0000fffc5ed20000)
	libc.so.6 => /lib64/libc.so.6 (0x0000fffc5eb90000)
	/lib/ld-linux-aarch64.so.1 (0x0000fffc5ee10000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x0000fffc5eb50000)
undefined symbol: UP	(/glc/readline/app/readline-8.0/lib/libreadline.so.8)
undefined symbol: PC	(/glc/readline/app/readline-8.0/lib/libreadline.so.8)
undefined symbol: BC	(/glc/readline/app/readline-8.0/lib/libreadline.so.8)
undefined symbol: tputs	(/glc/readline/app/readline-8.0/lib/libreadline.so.8)
undefined symbol: tgoto	(/glc/readline/app/readline-8.0/lib/libreadline.so.8)
undefined symbol: tgetflag	(/glc/readline/app/readline-8.0/lib/libreadline.so.8)
undefined symbol: tgetent	(/glc/readline/app/readline-8.0/lib/libreadline.so.8)
undefined symbol: tgetnum	(/glc/readline/app/readline-8.0/lib/libreadline.so.8)
undefined symbol: tgetstr	(/glc/readline/app/readline-8.0/lib/libreadline.so.8)

2025-03-29T11:07:00 [mysql@ky10arm-98 /glc/socat/source/socat-1.8.0.3/build_20250329T103836] $ ldd -r /glc/readline/app/readline-8.0/lib/libreadline.so.8
	linux-vdso.so.1 (0x0000fffcbd650000)
	libc.so.6 => /lib64/libc.so.6 (0x0000fffcbd440000)
	/lib/ld-linux-aarch64.so.1 (0x0000fffcbd660000)
undefined symbol: UP	(/glc/readline/app/readline-8.0/lib/libreadline.so.8)
undefined symbol: PC	(/glc/readline/app/readline-8.0/lib/libreadline.so.8)
undefined symbol: BC	(/glc/readline/app/readline-8.0/lib/libreadline.so.8)
undefined symbol: tputs	(/glc/readline/app/readline-8.0/lib/libreadline.so.8)
undefined symbol: tgoto	(/glc/readline/app/readline-8.0/lib/libreadline.so.8)
undefined symbol: tgetflag	(/glc/readline/app/readline-8.0/lib/libreadline.so.8)
undefined symbol: tgetent	(/glc/readline/app/readline-8.0/lib/libreadline.so.8)
undefined symbol: tgetnum	(/glc/readline/app/readline-8.0/lib/libreadline.so.8)
undefined symbol: tgetstr	(/glc/readline/app/readline-8.0/lib/libreadline.so.8)
2025-03-29T11:07:10 [mysql@ky10arm-98 /glc/socat/source/socat-1.8.0.3/build_20250329T103836] $


2025-03-29T11:36:19 [mysql@ky10arm-98 /glc/ncurses/app/ncurses-6.4] $ tree bin lib
bin
├── captoinfo -> tic
├── clear
├── infocmp
├── infotocap -> tic
├── ncurses6-config
├── reset -> tset
├── tabs
├── tic
├── toe
├── tput
└── tset
lib
├── libform.a
├── libform_g.a
├── libmenu.a
├── libmenu_g.a
├── libncurses++.a
├── libncurses.a
├── libncurses++_g.a
├── libncurses_g.a
├── libpanel.a
├── libpanel_g.a
└── terminfo -> ../share/terminfo

1 directory, 21 files

```


参考：
- https://github.com/spack/spack/pull/17059
- https://github.com/spack/spack/issues/17058
- 编译ncurses时没有使用`--with-shared --with-static`，而是使用了`--enable-shared --enable-static`导致只生成了静态库





