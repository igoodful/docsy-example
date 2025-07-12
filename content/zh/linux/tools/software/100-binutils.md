---
title: binutils
description: binutils
date: 2025-03-22
weight: 10000
viz: true

---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


{{<note>}}
<!---->
binutils 是一组开发工具，包括连接器、汇编器和其他用于目标文件和档案的工具。

- https://sourceforge.net/p/binutils/
- https://github.com/binutils-dev/binutils/
- 要先于gcc和glibc安装好
- 安装为静态最佳：`../configure --prefix=/glc/binutils/app/binutils-2.32 LDFLAGS="--static" --disable-werror`


{{</note>}}


## 1. 依赖

```viz-dot
digraph "binutils" {
node [
        fontsize = "12"
];
    "binutils" [ label = "binutils", shape = octagon ];
    "glibc" [ label = "glibc", shape = octagon ];
    "gcc" [ label = "gcc", shape = octagon ];
    "libstdc++.so.6" [ label = "libstdc++.so.6", shape = octagon ];
    "libgcc_s.so.1" [ label = "libgcc_s.so.1", shape = octagon ];
    "libc.so.6" [ label = "libc.so.6", shape = octagon ];
    "libdl.so.2" [ label = "libdl.so.2", shape = octagon ];
    "/lib64/ld-linux-x86-64.so.2" [ label = "/lib64/ld-linux-x86-64.so.2", shape = octagon ];
    "libm.so.6" [ label = "libm.so.6", shape = octagon ];
    "libpthread.so.0" [ label = "libpthread.so.0", shape = octagon ];
    "librt.so.1" [ label = "librt.so.1", shape = octagon ];


    "binutils" -> "glibc";
    "binutils" -> "gcc";
    "gcc" -> "libstdc++.so.6";
    "gcc" -> "libgcc_s.so.1";
    "glibc" -> "libc.so.6";
    "glibc" -> "/lib64/ld-linux-x86-64.so.2";
    "glibc" -> "libdl.so.2";
    "glibc" -> "libm.so.6";
    "glibc" -> "libpthread.so.0";
    "glibc" -> "librt.so.1";
}
```


## 2. 内容


### 2.1 bin

```viz-dot
digraph "libbinutils" {
node [
        fontsize = "12"
];
    "libbinutils" [ label = "libbinutils", shape = octagon ];
    "chbinutils" [ label = "chbinutils", shape = octagon ];
    "getfbinutils" [ label = "getfbinutils", shape = octagon ];
    "setfbinutils" [ label = "setfbinutils", shape = octagon ];
    "libbinutils.so" [ label = "libbinutils.so", shape = octagon ];
    "binutils/libbinutils.h" [ label = "binutils/libbinutils.h", shape = octagon ];
    "sys/binutils.h" [ label = "sys/binutils.h", shape = octagon ];

    "libbinutils" -> "chbinutils";
    "libbinutils" -> "getfbinutils";
    "libbinutils" -> "setfbinutils";
    "libbinutils" -> "libbinutils.so";
    "libbinutils" -> "binutils/libbinutils.h";
    "libbinutils" -> "sys/binutils.h";
}
```




## 3. 安装


#### 3.1 yum安装
```bash


```




#### 3.2 源码安装

##### 3.2.1 安装步骤

```bash
wget https://sourceware.org/pub/binutils/snapshots/binutils-2.32.tar.xz
tar xjf binutils-2.32.tar.xz
cd binutils-2.32
./configure --prefix=$HOME/local

```

##### 3.2.2 文件结构

```bash
2025-04-15T13:35:57 [mysql@x86centos7-250 /root] $ tree -L 3 binutils-2.32
binutils-2.32
├── bin
│   ├── addr2line
│   ├── ar
│   ├── as
│   ├── c++filt
│   ├── elfedit
│   ├── gprof
│   ├── ld
│   ├── ld.bfd
│   ├── nm
│   ├── objcopy
│   ├── objdump
│   ├── ranlib
│   ├── readelf
│   ├── size
│   ├── strings
│   └── strip
├── include
│   ├── ansidecl.h
│   ├── bfd.h
│   ├── bfdlink.h
│   ├── bfd_stdint.h
│   ├── diagnostics.h
│   ├── dis-asm.h
│   ├── plugin-api.h
│   └── symcat.h
├── lib
│   ├── libbfd-2.32.so
│   ├── libbfd.a
│   ├── libbfd.la
│   ├── libbfd.so -> libbfd-2.32.so
│   ├── libopcodes-2.32.so
│   ├── libopcodes.a
│   ├── libopcodes.la
│   └── libopcodes.so -> libopcodes-2.32.so
├── share
│   ├── info
│   │   ├── as.info
│   │   ├── bfd.info
│   │   ├── binutils.info
│   │   ├── gprof.info
│   │   └── ld.info
│   ├── locale
│   │   ├── bg
│   │   ├── ca
│   │   ├── da
│   │   ├── de
│   │   ├── eo
│   │   ├── es
│   │   ├── fi
│   │   ├── fr
│   │   ├── ga
│   │   ├── hr
│   │   ├── hu
│   │   ├── id
│   │   ├── it
│   │   ├── ja
│   │   ├── ms
│   │   ├── nl
│   │   ├── pt
│   │   ├── pt_BR
│   │   ├── ro
│   │   ├── ru
│   │   ├── rw
│   │   ├── sk
│   │   ├── sr
│   │   ├── sv
│   │   ├── tr
│   │   ├── uk
│   │   ├── vi
│   │   ├── zh_CN
│   │   └── zh_TW
│   └── man
│       └── man1
└── x86_64-pc-linux-gnu
    ├── bin
    │   ├── ar
    │   ├── as
    │   ├── ld
    │   ├── ld.bfd
    │   ├── nm
    │   ├── objcopy
    │   ├── objdump
    │   ├── ranlib
    │   ├── readelf
    │   └── strip
    └── lib
        └── ldscripts

41 directories, 47 files


```

##### 3.2.3 依赖关系

```bash
2025-04-15T13:37:11 [mysql@x86centos7-250 /glc/binutils/app/binutils-2.32/bin] $ ls |xargs ldd -r
addr2line:
	linux-vdso.so.1 =>  (0x00007fffbdd4f000)
	libbfd-2.32.so => /glc/binutils/app/binutils-2.32/lib/libbfd-2.32.so (0x00007f7a1706b000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f7a16e67000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f7a16a99000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f7a173a7000)
ar:
	linux-vdso.so.1 =>  (0x00007ffe4cfa1000)
	libbfd-2.32.so => /glc/binutils/app/binutils-2.32/lib/libbfd-2.32.so (0x00007f802b1b0000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f802afac000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f802abde000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f802b4ec000)
as:
	linux-vdso.so.1 =>  (0x00007ffec8f78000)
	libopcodes-2.32.so => /glc/binutils/app/binutils-2.32/lib/libopcodes-2.32.so (0x00007f676e6e5000)
	libbfd-2.32.so => /glc/binutils/app/binutils-2.32/lib/libbfd-2.32.so (0x00007f676e3a9000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f676e1a5000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f676ddd7000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f676ea5b000)
c++filt:
	linux-vdso.so.1 =>  (0x00007ffcc1f81000)
	libbfd-2.32.so => /glc/binutils/app/binutils-2.32/lib/libbfd-2.32.so (0x00007fce4461d000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007fce44419000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fce4404b000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fce44959000)
elfedit:
	linux-vdso.so.1 =>  (0x00007ffec9525000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007fdc550f1000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fdc54d23000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fdc552f5000)
gprof:
	linux-vdso.so.1 =>  (0x00007fff08bc3000)
	libbfd-2.32.so => /glc/binutils/app/binutils-2.32/lib/libbfd-2.32.so (0x00007f461c7ef000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f461c4ed000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f461c2e9000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f461bf1b000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f461cb2b000)
ld:
	linux-vdso.so.1 =>  (0x00007ffdfd5b6000)
	libbfd-2.32.so => /glc/binutils/app/binutils-2.32/lib/libbfd-2.32.so (0x00007fcf7dfa6000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007fcf7dda2000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fcf7d9d4000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fcf7e2e2000)
ld.bfd:
	linux-vdso.so.1 =>  (0x00007fff5f858000)
	libbfd-2.32.so => /glc/binutils/app/binutils-2.32/lib/libbfd-2.32.so (0x00007f547f689000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f547f485000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f547f0b7000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f547f9c5000)
nm:
	linux-vdso.so.1 =>  (0x00007ffc3c190000)
	libbfd-2.32.so => /glc/binutils/app/binutils-2.32/lib/libbfd-2.32.so (0x00007fde7b027000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007fde7ae23000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fde7aa55000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fde7b363000)
objcopy:
	linux-vdso.so.1 =>  (0x00007ffe8778e000)
	libbfd-2.32.so => /glc/binutils/app/binutils-2.32/lib/libbfd-2.32.so (0x00007fa3e114c000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007fa3e0f48000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fa3e0b7a000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fa3e1488000)
objdump:
	linux-vdso.so.1 =>  (0x00007fff02b9e000)
	libopcodes-2.32.so => /glc/binutils/app/binutils-2.32/lib/libopcodes-2.32.so (0x00007f7917ae7000)
	libbfd-2.32.so => /glc/binutils/app/binutils-2.32/lib/libbfd-2.32.so (0x00007f79177ab000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f79175a7000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f79171d9000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f7917e5d000)
ranlib:
	linux-vdso.so.1 =>  (0x00007ffc34fd6000)
	libbfd-2.32.so => /glc/binutils/app/binutils-2.32/lib/libbfd-2.32.so (0x00007f64f148e000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f64f128a000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f64f0ebc000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f64f17ca000)
readelf:
	linux-vdso.so.1 =>  (0x00007ffdd71cc000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f279c10d000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f279bd3f000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f279c311000)
size:
	linux-vdso.so.1 =>  (0x00007fff118ef000)
	libbfd-2.32.so => /glc/binutils/app/binutils-2.32/lib/libbfd-2.32.so (0x00007fc9fe029000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007fc9fde25000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fc9fda57000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fc9fe365000)
strings:
	linux-vdso.so.1 =>  (0x00007ffd861a4000)
	libbfd-2.32.so => /glc/binutils/app/binutils-2.32/lib/libbfd-2.32.so (0x00007ff2dce54000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007ff2dcc50000)
	libc.so.6 => /lib64/libc.so.6 (0x00007ff2dc882000)
	/lib64/ld-linux-x86-64.so.2 (0x00007ff2dd190000)
strip:
	linux-vdso.so.1 =>  (0x00007fff53bbb000)
	libbfd-2.32.so => /glc/binutils/app/binutils-2.32/lib/libbfd-2.32.so (0x00007f796f495000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f796f291000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f796eec3000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f796f7d1000)


2025-04-15T13:38:24 [mysql@x86centos7-250 /glc/binutils/app/binutils-2.32/lib] $ ll
total 24140
-rwxr-xr-x 1 mysql mysql  5391896 Apr 15 12:18 libbfd-2.32.so
-rw-r--r-- 1 mysql mysql 14856022 Apr 15 12:18 libbfd.a
-rwxr-xr-x 1 mysql mysql     1117 Apr 15 12:18 libbfd.la
lrwxrwxrwx 1 mysql mysql       14 Apr 15 12:18 libbfd.so -> libbfd-2.32.so
-rwxr-xr-x 1 mysql mysql  1867888 Apr 15 12:18 libopcodes-2.32.so
-rw-r--r-- 1 mysql mysql  2585352 Apr 15 12:18 libopcodes.a
-rwxr-xr-x 1 mysql mysql     1076 Apr 15 12:18 libopcodes.la
lrwxrwxrwx 1 mysql mysql       18 Apr 15 12:18 libopcodes.so -> libopcodes-2.32.so
2025-04-15T13:38:25 [mysql@x86centos7-250 /glc/binutils/app/binutils-2.32/lib] $ ls |xargs ldd -r
libbfd-2.32.so:
	linux-vdso.so.1 =>  (0x00007ffc4ca2a000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007fbd8a475000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fbd8a0a7000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fbd8a9b5000)
libbfd.a:
ldd: warning: you do not have execution permission for `./libbfd.a'
	not a dynamic executable
libbfd.la:
	not a dynamic executable
libbfd.so:
	linux-vdso.so.1 =>  (0x00007ffd92a6c000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f47546d5000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f4754307000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f4754c15000)
libopcodes-2.32.so:
	linux-vdso.so.1 =>  (0x00007ffdce5ef000)
	libbfd-2.32.so => /glc/binutils/app/binutils-2.32/lib/libbfd-2.32.so (0x00007f5511501000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f5511133000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f5510f2f000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f5511bb3000)
libopcodes.a:
ldd: warning: you do not have execution permission for `./libopcodes.a'
	not a dynamic executable
libopcodes.la:
	not a dynamic executable
libopcodes.so:
	linux-vdso.so.1 =>  (0x00007fff5d7d9000)
	libbfd-2.32.so => /glc/binutils/app/binutils-2.32/lib/libbfd-2.32.so (0x00007f95eec60000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f95ee892000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007f95ee68e000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f95ef312000)



```


#### 静态编译

```bash

2025-04-15T13:58:33 [mysql@x86centos7-250 /glc/binutils/app/binutils-2.32/bin] $ ll
total 102096
-rwxr-xr-x 1 mysql mysql 6287936 Apr 15 13:55 addr2line
-rwxr-xr-x 2 mysql mysql 6447760 Apr 15 13:55 ar
-rwxr-xr-x 2 mysql mysql 8444768 Apr 15 13:55 as
-rwxr-xr-x 1 mysql mysql 6237424 Apr 15 13:55 c++filt
-rwxr-xr-x 1 mysql mysql 1102672 Apr 15 13:55 elfedit
-rwxr-xr-x 1 mysql mysql 6792864 Apr 15 13:55 gprof
-rwxr-xr-x 4 mysql mysql 9321576 Apr 15 13:55 ld
-rwxr-xr-x 4 mysql mysql 9321576 Apr 15 13:55 ld.bfd
-rwxr-xr-x 2 mysql mysql 6325304 Apr 15 13:55 nm
-rwxr-xr-x 2 mysql mysql 6953456 Apr 15 13:55 objcopy
-rwxr-xr-x 2 mysql mysql 8630096 Apr 15 13:55 objdump
-rwxr-xr-x 2 mysql mysql 6447752 Apr 15 13:55 ranlib
-rwxr-xr-x 2 mysql mysql 2697232 Apr 15 13:55 readelf
-rwxr-xr-x 1 mysql mysql 6276992 Apr 15 13:55 size
-rwxr-xr-x 1 mysql mysql 6274312 Apr 15 13:55 strings
-rwxr-xr-x 2 mysql mysql 6953456 Apr 15 13:55 strip
2025-04-15T13:58:34 [mysql@x86centos7-250 /glc/binutils/app/binutils-2.32/bin] $ ls|xargs ldd -r
addr2line:
	not a dynamic executable
ar:
	not a dynamic executable
as:
	not a dynamic executable
c++filt:
	not a dynamic executable
elfedit:
	not a dynamic executable
gprof:
	not a dynamic executable
ld:
	not a dynamic executable
ld.bfd:
	not a dynamic executable
nm:
	not a dynamic executable
objcopy:
	not a dynamic executable
objdump:
	not a dynamic executable
ranlib:
	not a dynamic executable
readelf:
	not a dynamic executable
size:
	not a dynamic executable
strings:
	not a dynamic executable
strip:
	not a dynamic executable

```





## 4. 问题


#### DEFAULT_GENERATE_ELF_STT_COMMON undeclared


```bash
     Finished prerequisites of target file 'as.o'.
    Must remake target 'as.o'.
make[4]: Entering directory '/glc/binutils/source/binutils-2.31.1/build_20250328T101658/gas'
depbase=`echo as.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
gcc -DHAVE_CONFIG_H -I. -I../../gas  -I. -I../../gas -I../bfd -I../../gas/config -I../../gas/../include -I../../gas/.. -I../../gas/../bfd -DLOCALEDIR="\"/glc/binutils/app/binutils-2.31.1/share/locale\""  -W -Wall -Wstrict-prototypes -Wmissing-prototypes -Wshadow -Wstack-usage=262144 -Wwrite-strings -I../../gas/../zlib -g -O2 -MT as.o -MD -MP -MF $depbase.Tpo -c -o as.o ../../gas/as.c &&\
mv -f $depbase.Tpo $depbase.Po
Putting child 0xaaadabd63ca0 (as.o) PID 703800 on the chain.
Live child 0xaaadabd63ca0 (as.o) PID 703800
../../gas/as.c:99:31: error: ‘DEFAULT_GENERATE_ELF_STT_COMMON’ undeclared here (not in a function); did you mean ‘BFD_USE_ELF_STT_COMMON’?
 int flag_use_elf_stt_common = DEFAULT_GENERATE_ELF_STT_COMMON;
                               ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
                               BFD_USE_ELF_STT_COMMON
../../gas/as.c:100:41: error: ‘DEFAULT_GENERATE_BUILD_NOTES’ undeclared here (not in a function); did you mean ‘DEFAULT_GENERATE_ELF_STT_COMMON’?
 bfd_boolean flag_generate_build_notes = DEFAULT_GENERATE_BUILD_NOTES;
                                         ^~~~~~~~~~~~~~~~~~~~~~~~~~~~
                                         DEFAULT_GENERATE_ELF_STT_COMMON
../../gas/as.c: In function ‘print_version_id’:
../../gas/as.c:226:14: error: ‘TARGET_ALIAS’ undeclared (first use in this function); did you mean ‘TARGET_ARCH’?
     VERSION, TARGET_ALIAS, BFD_VERSION_STRING);
              ^~~~~~~~~~~~
              TARGET_ARCH
../../gas/as.c:226:14: note: each undeclared identifier is reported only once for each function it appears in
../../gas/as.c: In function ‘parse_args’:
../../gas/as.c:684:5: error: ‘TARGET_ALIAS’ undeclared (first use in this function); did you mean ‘TARGET_ARCH’?
     TARGET_ALIAS);
     ^~~~~~~~~~~~
     TARGET_ARCH
../../gas/as.c:699:44: error: ‘TARGET_CANONICAL’ undeclared (first use in this function); did you mean ‘TARGET_ALIAS’?
    fprintf (stderr, _("canonical = %s\n"), TARGET_CANONICAL);
                                            ^~~~~~~~~~~~~~~~
                                            TARGET_ALIAS
../../gas/as.c:700:43: error: ‘TARGET_CPU’ undeclared (first use in this function); did you mean ‘TARGET_ARCH’?
    fprintf (stderr, _("cpu-type = %s\n"), TARGET_CPU);
                                           ^~~~~~~~~~
                                           TARGET_ARCH
Reaping losing child 0xaaadabd63ca0 PID 703800
make[4]: *** [Makefile:1215: as.o] Error 1
Removing child 0xaaadabd63ca0 PID 703800 from chain.
make[4]: Leaving directory '/glc/binutils/source/binutils-2.31.1/build_20250328T101658/gas'
Reaping losing child 0xaaae3ced1080 PID 703790
make[3]: *** [Makefile:1260: all-recursive] Error 1
Removing child 0xaaae3ced1080 PID 703790 from chain.
make[3]: Leaving directory '/glc/binutils/source/binutils-2.31.1/build_20250328T101658/gas'
Reaping losing child 0xaaab32897930 PID 703789
make[2]: *** [Makefile:808: all] Error 2
Removing child 0xaaab32897930 PID 703789 from chain.
make[2]: Leaving directory '/glc/binutils/source/binutils-2.31.1/build_20250328T101658/gas'
Reaping losing child 0xaaaedf70fc20 PID 703770
make[1]: *** [Makefile:4864: all-gas] Error 2
Removing child 0xaaaedf70fc20 PID 703770 from chain.
make[1]: Leaving directory '/glc/binutils/source/binutils-2.31.1/build_20250328T101658'
Reaping losing child 0xaaab76d3d800 PID 703546
make: *** [Makefile:850: all] Error 2
Removing child 0xaaab76d3d800 PID 703546 from chain.
2025-03-28T10:24:54 [mysql@ky10arm-98 /glc/binutils/source/binutils-2.31.1/build_20250328T101658] $ cd ..
2025-03-28T10:25:51 [mysql@ky10arm-98 /glc/binutils/source/binutils-2.31.1] $ find ./ -type f |xargs grep 'DEFAULT_GENERATE_ELF_STT_COMMON'
./build_20250328T101658/gas/config.log:| #define DEFAULT_GENERATE_ELF_STT_COMMON 0
./build_20250328T101658/gas/config.log:#define DEFAULT_GENERATE_ELF_STT_COMMON 0
./build_20250328T101658/gas/config.status:D["DEFAULT_GENERATE_ELF_STT_COMMON"]=" 0"
./build_20250328T101658/gas/config.h:#define DEFAULT_GENERATE_ELF_STT_COMMON 0
^C
2025-03-28T10:26:27 [mysql@ky10arm-98 /glc/binutils/source/binutils-2.31.1] $ find ../ -type f |xargs grep 'DEFAULT_GENERATE_ELF_STT_COMMON'
../binutils-2.31.1/build_20250328T101658/gas/config.log:| #define DEFAULT_GENERATE_ELF_STT_COMMON 0
../binutils-2.31.1/build_20250328T101658/gas/config.log:#define DEFAULT_GENERATE_ELF_STT_COMMON 0
../binutils-2.31.1/build_20250328T101658/gas/config.status:D["DEFAULT_GENERATE_ELF_STT_COMMON"]=" 0"
../binutils-2.31.1/build_20250328T101658/gas/config.h:#define DEFAULT_GENERATE_ELF_STT_COMMON 0
../binutils-2.31.1/gas/config.in:#undef DEFAULT_GENERATE_ELF_STT_COMMON
../binutils-2.31.1/gas/configure.ac:AC_DEFINE_UNQUOTED(DEFAULT_GENERATE_ELF_STT_COMMON,
../binutils-2.31.1/gas/configure:#define DEFAULT_GENERATE_ELF_STT_COMMON $ac_default_elf_stt_common
../binutils-2.31.1/gas/as.c:int flag_use_elf_stt_common = DEFAULT_GENERATE_ELF_STT_COMMON;
../binutils-2.31.1/gas/ChangeLog-2016:	DEFAULT_GENERATE_ELF_STT_COMMON.
```
- 原因是环境变量包含了当前目录


在`~/.bashrc`文件开头给这几个环境变量配置一个假的目录，以免后追加的环境变量包含了当前目录
```bash
C_INCLUDE_PATH=/gfdgdgfdgd
CPLUS_INCLUDE_PATH=/gfdgdgfdgd
LIBRARY_PATH=/gfdgdgfdgd
LD_LIBRARY_PATH=/gfdgdgfdgd
PKG_CONFIG_PATH=/gfdgdgfdgd
# User environment PATH
PATH="$HOME/.local/bin:$HOME/bin:$PATH"
export PATH
export JAVA_HOME=/data/app/jdk-18.0.2
export JRE_HOME="$JAVA_HOME/jre"
export CLASSPATH=".:$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH"
export PATH="$JAVA_HOME/bin:$PATH"
export PATH="/glc/readline/app/readline-8.0/bin:$PATH"
export C_INCLUDE_PATH="/glc/readline/app/readline-8.0/include:$C_INCLUDE_PATH"
export CPLUS_INCLUDE_PATH="/glc/readline/app/readline-8.0/include:$CPLUS_INCLUDE_PATH"
export LIBRARY_PATH="/glc/readline/app/readline-8.0/lib:$LIBRARY_PATH"
export LD_LIBRARY_PATH="/glc/readline/app/readline-8.0/lib:$LD_LIBRARY_PATH"
export PKG_CONFIG_PATH="/glc/readline/app/readline-8.0/lib/pkgconfig:$PKG_CONFIG_PATH"
export PATH="/glc/ncurses/app/ncurses-6.3/bin:$PATH"
export C_INCLUDE_PATH="/glc/ncurses/app/ncurses-6.3/include:$C_INCLUDE_PATH"
export CPLUS_INCLUDE_PATH="/glc/ncurses/app/ncurses-6.3/include:$CPLUS_INCLUDE_PATH"
export LIBRARY_PATH="/glc/ncurses/app/ncurses-6.3/lib:$LIBRARY_PATH"
export LD_LIBRARY_PATH="/glc/ncurses/app/ncurses-6.3/lib:$LD_LIBRARY_PATH"
export PATH="/glc/ncurses/app/ncurses-6.5/bin:$PATH"
export C_INCLUDE_PATH="/glc/ncurses/app/ncurses-6.5/include:$C_INCLUDE_PATH"
export CPLUS_INCLUDE_PATH="/glc/ncurses/app/ncurses-6.5/include:$CPLUS_INCLUDE_PATH"
export LIBRARY_PATH="/glc/ncurses/app/ncurses-6.5/lib:$LIBRARY_PATH"
export LD_LIBRARY_PATH="/glc/ncurses/app/ncurses-6.5/lib:$LD_LIBRARY_PATH"
export PATH="/glc/gmp/app/gmp-6.1.0/bin:$PATH"
export C_INCLUDE_PATH="/glc/gmp/app/gmp-6.1.0/include:$C_INCLUDE_PATH"
export CPLUS_INCLUDE_PATH="/glc/gmp/app/gmp-6.1.0/include:$CPLUS_INCLUDE_PATH"
export LIBRARY_PATH="/glc/gmp/app/gmp-6.1.0/lib:$LIBRARY_PATH"
export LD_LIBRARY_PATH="/glc/gmp/app/gmp-6.1.0/lib:$LD_LIBRARY_PATH"
export PKG_CONFIG_PATH="/glc/gmp/app/gmp-6.1.0/lib/pkgconfig:$PKG_CONFIG_PATH"
export PATH="/glc/mpfr/app/mpfr-3.1.6/bin:$PATH"
export C_INCLUDE_PATH="/glc/mpfr/app/mpfr-3.1.6/include:$C_INCLUDE_PATH"
export CPLUS_INCLUDE_PATH="/glc/mpfr/app/mpfr-3.1.6/include:$CPLUS_INCLUDE_PATH"
export LIBRARY_PATH="/glc/mpfr/app/mpfr-3.1.6/lib:$LIBRARY_PATH"
export LD_LIBRARY_PATH="/glc/mpfr/app/mpfr-3.1.6/lib:$LD_LIBRARY_PATH"
export PKG_CONFIG_PATH="/glc/mpfr/app/mpfr-3.1.6/lib/pkgconfig:$PKG_CONFIG_PATH"
```

- 参考：https://github.com/riscv-software-src/riscv-tools/issues/66


#### ../../gas/as.c:99:31: error: ‘DEFAULT_GENERATE_ELF_STT_COMMON’ undeclared here (not in a function)

```bash
2025-04-14T20:12:03 [mysql@x86centos7-250 /root] $ pwd
/glc/binutils/source/binutils-2.32/build_20250414T200431
2025-04-14T20:32:25 [mysql@x86centos7-250 /root] $ make -j1
make[1]: Entering directory '/glc/binutils/source/binutils-2.32/build_20250414T200431'
make[2]: Entering directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/libiberty'
make[3]: Entering directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/libiberty/testsuite'
make[3]: Nothing to be done for 'all'.
make[3]: Leaving directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/libiberty/testsuite'
make[2]: Leaving directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/libiberty'
make[2]: Entering directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/intl'
make[2]: Nothing to be done for 'all'.
make[2]: Leaving directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/intl'
make[2]: Entering directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/zlib'
true "AR_FLAGS=rc" "CC_FOR_BUILD=gcc" "CFLAGS=-g -O2" "CXXFLAGS=-g -O2" "CFLAGS_FOR_BUILD=-g -O2" "CFLAGS_FOR_TARGET=-g -O2" "INSTALL=/usr/bin/install -c" "INSTALL_DATA=/usr/bin/install -c -m 644" "INSTALL_PROGRAM=/usr/bin/install -c" "INSTALL_SCRIPT=/usr/bin/install -c" "LDFLAGS=-static-libstdc++ -static-libgcc " "LIBCFLAGS=-g -O2" "LIBCFLAGS_FOR_TARGET=-g -O2" "MAKE=make" "MAKEINFO=makeinfo --split-size=5000000 --split-size=5000000 " "PICFLAG=-fPIC" "PICFLAG_FOR_TARGET=" "SHELL=/bin/sh" "EXPECT=expect" "RUNTEST=runtest" "RUNTESTFLAGS=" "exec_prefix=/glc/binutils/app/binutils-2.32" "infodir=/glc/binutils/app/binutils-2.32/share/info" "libdir=/glc/binutils/app/binutils-2.32/lib" "prefix=/glc/binutils/app/binutils-2.32" "tooldir=/glc/binutils/app/binutils-2.32/x86_64-pc-linux-gnu" "AR=ar" "AS=as" "CC=gcc" "CXX=g++" "LD=ld" "LIBCFLAGS=-g -O2" "NM=nm" "PICFLAG=-fPIC" "RANLIB=ranlib" "DESTDIR=" DO=all multi-do # make
make[2]: Leaving directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/zlib'
make[2]: Entering directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/bfd'
Making info in doc
make[3]: Entering directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/bfd/doc'
make[3]: Leaving directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/bfd/doc'
Making info in po
make[3]: Entering directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/bfd/po'
make[3]: Nothing to be done for 'info'.
make[3]: Leaving directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/bfd/po'
make[3]: Entering directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/bfd'
make[3]: Nothing to be done for 'info-am'.
make[3]: Leaving directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/bfd'
make  all-recursive
make[3]: Entering directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/bfd'
Making all in doc
make[4]: Entering directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/bfd/doc'
make[4]: Leaving directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/bfd/doc'
Making all in po
make[4]: Entering directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/bfd/po'
make[4]: Nothing to be done for 'all'.
make[4]: Leaving directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/bfd/po'
make[4]: Entering directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/bfd'
make[4]: Leaving directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/bfd'
make[3]: Leaving directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/bfd'
make[2]: Leaving directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/bfd'
make[2]: Entering directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/opcodes'
make  all-recursive
make[3]: Entering directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/opcodes'
Making all in .
make[4]: Entering directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/opcodes'
make[4]: Leaving directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/opcodes'
Making all in po
make[4]: Entering directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/opcodes/po'
make[4]: Nothing to be done for 'all'.
make[4]: Leaving directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/opcodes/po'
make[3]: Leaving directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/opcodes'
make[2]: Leaving directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/opcodes'
make[2]: Entering directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/gas'
make  all-recursive
make[3]: Entering directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/gas'
Making all in doc
make[4]: Entering directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/gas/doc'
make[4]: Nothing to be done for 'all'.
make[4]: Leaving directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/gas/doc'
Making all in po
make[4]: Entering directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/gas/po'
make[4]: Nothing to be done for 'all'.
make[4]: Leaving directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/gas/po'
make[4]: Entering directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/gas'
depbase=`echo as.o | sed 's|[^/]*$|.deps/&|;s|\.o$||'`;\
gcc -DHAVE_CONFIG_H -I. -I../../gas  -I. -I../../gas -I../bfd -I../../gas/config -I../../gas/../include -I../../gas/.. -I../../gas/../bfd -DLOCALEDIR="\"/glc/binutils/app/binutils-2.32/share/locale\""  -W -Wall -Wstrict-prototypes -Wmissing-prototypes -Wshadow -Wstack-usage=262144 -Wwrite-strings -I../../gas/../zlib -g -O2 -MT as.o -MD -MP -MF $depbase.Tpo -c -o as.o ../../gas/as.c &&\
mv -f $depbase.Tpo $depbase.Po
../../gas/as.c:99:31: error: ‘DEFAULT_GENERATE_ELF_STT_COMMON’ undeclared here (not in a function)
   99 | int flag_use_elf_stt_common = DEFAULT_GENERATE_ELF_STT_COMMON;
      |                               ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
../../gas/as.c:100:41: error: ‘DEFAULT_GENERATE_BUILD_NOTES’ undeclared here (not in a function)
  100 | bfd_boolean flag_generate_build_notes = DEFAULT_GENERATE_BUILD_NOTES;
      |                                         ^~~~~~~~~~~~~~~~~~~~~~~~~~~~
../../gas/as.c: In function ‘print_version_id’:
../../gas/as.c:226:21: error: ‘TARGET_ALIAS’ undeclared (first use in this function); did you mean ‘TARGET_ARCH’?
  226 |            VERSION, TARGET_ALIAS, BFD_VERSION_STRING);
      |                     ^~~~~~~~~~~~
      |                     TARGET_ARCH
../../gas/as.c:226:21: note: each undeclared identifier is reported only once for each function it appears in
../../gas/as.c: In function ‘parse_args’:
../../gas/as.c:689:19: error: ‘TARGET_ALIAS’ undeclared (first use in this function); did you mean ‘TARGET_ARCH’?
  689 |                   TARGET_ALIAS);
      |                   ^~~~~~~~~~~~
      |                   TARGET_ARCH
../../gas/as.c:704:51: error: ‘TARGET_CANONICAL’ undeclared (first use in this function)
  704 |           fprintf (stderr, _("canonical = %s\n"), TARGET_CANONICAL);
      |                                                   ^~~~~~~~~~~~~~~~
../../gas/as.c:705:50: error: ‘TARGET_CPU’ undeclared (first use in this function)
  705 |           fprintf (stderr, _("cpu-type = %s\n"), TARGET_CPU);
      |                                                  ^~~~~~~~~~
make[4]: *** [Makefile:1224: as.o] Error 1
make[4]: Leaving directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/gas'
make[3]: *** [Makefile:1269: all-recursive] Error 1
make[3]: Leaving directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/gas'
make[2]: *** [Makefile:814: all] Error 2
make[2]: Leaving directory '/glc/binutils/source/binutils-2.32/build_20250414T200431/gas'
make[1]: *** [Makefile:4864: all-gas] Error 2
make[1]: Leaving directory '/glc/binutils/source/binutils-2.32/build_20250414T200431'
make: *** [Makefile:850: all] Error 2

```


- 原因是：环境变量`C_INCLUDE_PATH`路径中包含了当前路径`.`
- `unset C_INCLUDE_PATH`或者`/etc/bashrc`添加相关环境变量
- 相关问题链接： https://github.com/riscv-software-src/riscv-tools/issues/66

`cat  /etc/bashrc`
```bash
export C_INCLUDE_PATH=/1234567890abcdefg
export CPLUS_INCLUDE_PATH=/1234567890abcdefg
export LIBRARY_PATH=/1234567890abcdefg
export LD_LIBRARY_PATH=/1234567890abcdefg
export PKG_CONFIG_PATH=/1234567890abcdefg

```


