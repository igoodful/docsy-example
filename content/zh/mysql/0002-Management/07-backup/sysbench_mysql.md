---
title: sysbench mysql
description: sysbench mysql
date: 2023-10-26
weight: 20000


viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;

{{<alert color="danger" title="注意" >}}

- [[processlist](https://github.com/akopytov/sysbench)](https://github.com/akopytov/sysbench)

{{</alert>}}





## 一、安装

```bash
yum -y install epel-release

yum -y install sysbench

sysbench --version

sysbench /usr/share/sysbench/oltp_read_only.lua --help
```

```bash
curl -s https://packagecloud.io/install/repositories/akopytov/sysbench/script.rpm.sh | sudo bash

sudo yum -y install sysbench

```

#### 源码安装

```bash
 yum -y install make automake libtool pkgconfig libaio-devel openssl-devel

# 安装mysql开发库
wget https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm
yum install mysql80-community-release-el7-3.noarch.rpm

# 安装PostgreSQL开发库
yum -y install postgresql-devel

```


```bash
wget https://github.com/akopytov/sysbench/archive/refs/tags/1.0.20.tar.gz

tar -xzf 1.0.20.tar.gz

./autogen.sh
# Add --with-pgsql to build with PostgreSQL support
./configure --prefix=/root/sysbench1020

make -j4

make install

```
以上将默认构建支持 MySQL 的 sysbench。如果您在非标准位置有 MySQL 头文件和库（并且 mysql_config在 中找不到PATH），您可以使用--with-mysql-includes和--with-mysql-libs选项明确指定它们./configure。

要编译不支持 MySQL 的 sysbench，请使用--without-mysql。如果没有可用的数据库驱动程序，数据库相关脚本将无法运行，但其他基准测试将可以运行。



```bash
2024-07-03T15:42:08 [root@node77 /root] $ tar -xzf 1.0.20.tar.gz
2024-07-03T15:42:15 [root@node77 /root] $ ll
total 137800
-rw-r--r--   1 root root  1509630 Jul  3 15:42 1.0.20.tar.gz
-rw-r--r--   1 root root 65718155 Jun 19 11:20 70.sql
drwxr-x---  10 root root     4096 Jun 27 16:31 back
-rw-r--r--   1 root root        0 Jul  3 15:16 bak-db-err.log
-rw-r--r--   1 root root    26024 Apr 25  2019 mysql80-community-release-el7-3.noarch.rpm
-rw-r--r--   1 root root 73822841 Jul  3 15:16 rds-20240704.sql
-rw-r--r--.  1 root root     1058 Sep 19  2022 README.txt
drwxrwxr-x  12 root root     4096 Apr 24  2020 sysbench-1.0.20
-rwxr-xr-x   1 root root     4865 Jun 30 12:18 sysbench77.sh
2024-07-03T15:42:17 [root@node77 /root] $ cd sysbench-1.0.20/
2024-07-03T15:42:21 [root@node77 /root/sysbench-1.0.20] $ ll
total 152
-rwxrwxr-x 1 root root  3074 Apr 24  2020 autogen.sh
-rw-rw-r-- 1 root root 16755 Apr 24  2020 ChangeLog
drwxrwxr-x 2 root root  4096 Apr 24  2020 config
-rw-rw-r-- 1 root root 14744 Apr 24  2020 configure.ac
-rw-rw-r-- 1 root root 18092 Apr 24  2020 COPYING
drwxrwxr-x 3 root root  4096 Apr 24  2020 debian
drwxrwxr-x 3 root root  4096 Apr 24  2020 doc
-rwxrwxr-x 1 root root  7122 Apr 24  2020 install-sh
drwxrwxr-x 2 root root  4096 Apr 24  2020 m4
-rw-rw-r-- 1 root root  1583 Apr 24  2020 Makefile.am
-rwxrwxr-x 1 root root 10266 Apr 24  2020 missing
-rwxrwxr-x 1 root root  1988 Apr 24  2020 mkinstalldirs
-rw-rw-r-- 1 root root 16086 Apr 24  2020 README.md
-rw-rw-r-- 1 root root  2069 Apr 24  2020 README-Oracle.md
-rw-rw-r-- 1 root root   478 Apr 24  2020 README-WIN.txt
drwxrwxr-x 2 root root  4096 Apr 24  2020 rpm
drwxrwxr-x 2 root root  4096 Apr 24  2020 scripts
drwxrwxr-x 2 root root  4096 Apr 24  2020 snap
drwxrwxr-x 5 root root  4096 Apr 24  2020 src
drwxrwxr-x 4 root root  4096 Apr 24  2020 tests
drwxrwxr-x 5 root root  4096 Apr 24  2020 third_party
2024-07-03T15:42:22 [root@node77 /root/sysbench-1.0.20] $ ./autogen.sh
./autogen.sh: running `libtoolize --copy --force'
libtoolize: putting auxiliary files in AC_CONFIG_AUX_DIR, `config'.
libtoolize: copying file `config/ltmain.sh'
libtoolize: putting macros in AC_CONFIG_MACRO_DIR, `m4'.
libtoolize: copying file `m4/libtool.m4'
libtoolize: copying file `m4/ltoptions.m4'
libtoolize: copying file `m4/ltsugar.m4'
libtoolize: copying file `m4/ltversion.m4'
libtoolize: copying file `m4/lt~obsolete.m4'
./autogen.sh: running `aclocal -I m4'
./autogen.sh: running `autoheader'
./autogen.sh: running `automake -c --foreign --add-missing'
configure.ac:59: installing 'config/ar-lib'
configure.ac:45: installing 'config/compile'
configure.ac:27: installing 'config/config.guess'
configure.ac:27: installing 'config/config.sub'
configure.ac:32: installing 'config/install-sh'
configure.ac:32: installing 'config/missing'
src/Makefile.am: installing 'config/depcomp'
parallel-tests: installing 'config/test-driver'
./autogen.sh: running `autoconf'
Libtoolized with: libtoolize (GNU libtool) 2.4.2
Automade with: automake (GNU automake) 1.13.4
Configured with: autoconf (GNU Autoconf) 2.69
2024-07-03T15:42:49 [root@node77 /root/sysbench-1.0.20] $ ./configure --prefix=/root/sysbench1020
checking build system type... x86_64-unknown-linux-gnu
checking host system type... x86_64-unknown-linux-gnu
checking target system type... x86_64-unknown-linux-gnu
checking for a BSD-compatible install... /usr/bin/install -c
checking whether build environment is sane... yes
checking for a thread-safe mkdir -p... /usr/bin/mkdir -p
checking for gawk... gawk
checking whether make sets $(MAKE)... yes
checking whether make supports nested variables... yes
checking for style of include used by make... GNU
checking for gcc... gcc
checking whether the C compiler works... yes
checking for C compiler default output file name... a.out
checking for suffix of executables...
checking whether we are cross compiling... no
checking for suffix of object files... o
checking whether we are using the GNU C compiler... yes
checking whether gcc accepts -g... yes
checking for gcc option to accept ISO C89... none needed
checking dependency style of gcc... gcc3
checking how to run the C preprocessor... gcc -E
checking for grep that handles long lines and -e... /usr/bin/grep
checking for egrep... /usr/bin/grep -E
checking for ANSI C header files... yes
checking for sys/types.h... yes
checking for sys/stat.h... yes
checking for stdlib.h... yes
checking for string.h... yes
checking for memory.h... yes
checking for strings.h... yes
checking for inttypes.h... yes
checking for stdint.h... yes
checking for unistd.h... yes
checking minix/config.h usability... no
checking minix/config.h presence... no
checking for minix/config.h... no
checking whether it is safe to define __EXTENSIONS__... yes
checking for gcc... (cached) gcc
checking whether we are using the GNU C compiler... (cached) yes
checking whether gcc accepts -g... (cached) yes
checking for gcc option to accept ISO C89... (cached) none needed
checking dependency style of gcc... (cached) gcc3
checking for gcc option to accept ISO C99... -std=gnu99
checking how to run the C preprocessor... gcc -E
checking whether gcc -std=gnu99 and cc understand -c and -o together... yes
checking for a sed that does not truncate output... /usr/bin/sed
checking for C compiler vendor... gnu
checking for gcc architecture flag...
checking for x86 cpuid 0 output... d:756e6547:6c65746e:49656e69
checking for x86 cpuid 1 output... 306c1:3040800:fffa3203:1f8bfbff
checking whether C compiler accepts -march=haswell... no
checking whether C compiler accepts -march=core-avx2... yes
checking for gcc architecture flag... -march=core-avx2
checking for ar... ar
checking the archiver (ar) interface... ar
checking how to print strings... printf
checking for a sed that does not truncate output... (cached) /usr/bin/sed
checking for fgrep... /usr/bin/grep -F
checking for ld used by gcc -std=gnu99... /usr/bin/ld
checking if the linker (/usr/bin/ld) is GNU ld... yes
checking for BSD- or MS-compatible name lister (nm)... /usr/bin/nm -B
checking the name lister (/usr/bin/nm -B) interface... BSD nm
checking whether ln -s works... yes
checking the maximum length of command line arguments... 1572864
checking whether the shell understands some XSI constructs... yes
checking whether the shell understands "+="... yes
checking how to convert x86_64-unknown-linux-gnu file names to x86_64-unknown-linux-gnu format... func_convert_file_noop
checking how to convert x86_64-unknown-linux-gnu file names to toolchain format... func_convert_file_noop
checking for /usr/bin/ld option to reload object files... -r
checking for objdump... objdump
checking how to recognize dependent libraries... pass_all
checking for dlltool... no
checking how to associate runtime and link libraries... printf %s\n
checking for archiver @FILE support... @
checking for strip... strip
checking for ranlib... ranlib
checking command to parse /usr/bin/nm -B output from gcc -std=gnu99 object... ok
checking for sysroot... no
checking for mt... no
checking if : is a manifest tool... no
checking for dlfcn.h... yes
checking for objdir... .libs
checking if gcc -std=gnu99 supports -fno-rtti -fno-exceptions... no
checking for gcc -std=gnu99 option to produce PIC... -fPIC -DPIC
checking if gcc -std=gnu99 PIC flag -fPIC -DPIC works... yes
checking if gcc -std=gnu99 static flag -static works... no
checking if gcc -std=gnu99 supports -c -o file.o... yes
checking if gcc -std=gnu99 supports -c -o file.o... (cached) yes
checking whether the gcc -std=gnu99 linker (/usr/bin/ld -m elf_x86_64) supports shared libraries... yes
checking whether -lc should be explicitly linked in... no
checking dynamic linker characteristics... GNU/Linux ld.so
checking how to hardcode library paths into programs... immediate
checking whether stripping libraries is possible... yes
checking if libtool supports shared libraries... yes
checking whether to build shared libraries... yes
checking whether to build static libraries... yes
checking for pkg-config... yes
checking for C compiler vendor... (cached) gnu
checking whether to compile with MySQL support... yes
checking whether to compile with Drizzle support... no
checking whether to compile with libattachsql support... no
checking whether to compile with Oracle support... no
checking whether to compile with PostgreSQL support... no
checking for pkg-config... /usr/bin/pkg-config
checking pkg-config is at least version 0.9.0... yes
checking whether to build with system or bundled LuaJIT... bundled
checking whether to build with system or bundled Concurrency Kit... bundled
checking whether SHM_HUGETLB is declared... yes
checking whether O_SYNC is declared... yes
checking for xsltproc... xsltproc
checking whether xsltproc works... no
checking for the pthreads library -lpthreads... no
checking whether pthreads work without any flags... no
checking whether pthreads work with -Kthread... no
checking whether pthreads work with -kthread... no
checking for the pthreads library -llthread... no
checking whether pthreads work with -pthread... yes
checking for joinable pthread attribute... PTHREAD_CREATE_JOINABLE
checking if more special flags are required for pthreads... no
checking for sqrt in -lm... yes
checking for mysql_config... /usr/bin/mysql_config
checking MySQL C flags... -I/usr/include/mysql
checking MySQL linker flags... -L/usr/lib64/mysql -lmysqlclient -lpthread -lz -lm -ldl -lssl -lcrypto
checking if mysql.h defines MYSQL_OPT_SSL_MODE... no
checking for ld used by GCC... /usr/bin/ld -m elf_x86_64
checking if the linker (/usr/bin/ld -m elf_x86_64) is GNU ld... yes
checking for shared library run path origin... done
checking libaio.h usability... no
checking libaio.h presence... no
checking for libaio.h... no
checking for malloc in -lumem... no
checking for malloc in -lmtmalloc... no
checking for ANSI C header files... (cached) yes
checking errno.h usability... yes
checking errno.h presence... yes
checking for errno.h... yes
checking fcntl.h usability... yes
checking fcntl.h presence... yes
checking for fcntl.h... yes
checking math.h usability... yes
checking math.h presence... yes
checking for math.h... yes
checking pthread.h usability... yes
checking pthread.h presence... yes
checking for pthread.h... yes
checking sched.h usability... yes
checking sched.h presence... yes
checking for sched.h... yes
checking signal.h usability... yes
checking signal.h presence... yes
checking for signal.h... yes
checking for stdlib.h... (cached) yes
checking for string.h... (cached) yes
checking sys/aio.h usability... no
checking sys/aio.h presence... no
checking for sys/aio.h... no
checking sys/ipc.h usability... yes
checking sys/ipc.h presence... yes
checking for sys/ipc.h... yes
checking sys/time.h usability... yes
checking sys/time.h presence... yes
checking for sys/time.h... yes
checking sys/mman.h usability... yes
checking sys/mman.h presence... yes
checking for sys/mman.h... yes
checking sys/shm.h usability... yes
checking sys/shm.h presence... yes
checking for sys/shm.h... yes
checking thread.h usability... no
checking thread.h presence... no
checking for thread.h... no
checking for unistd.h... (cached) yes
checking limits.h usability... yes
checking limits.h presence... yes
checking for limits.h... yes
checking libgen.h usability... yes
checking libgen.h presence... yes
checking for libgen.h... yes
checking for off_t... yes
checking whether time.h and sys/time.h may both be included... yes
checking for thread local storage (TLS) class... __thread
checking for __attribute__((format))... yes
checking for __attribute__((unused))... yes
checking for special C compiler options needed for large files... no
checking for _FILE_OFFSET_BITS value needed for large files... no
checking size of size_t... 8
checking size of bool... 1
checking for stdlib.h... (cached) yes
checking for unistd.h... (cached) yes
checking for sys/param.h... yes
checking for getpagesize... yes
checking for working mmap... yes
checking whether strerror_r is declared... yes
checking for strerror_r... yes
checking whether strerror_r returns char *... yes
checking for library containing clock_gettime... none required
checking for alarm... yes
checking for clock_gettime... yes
checking for directio... no
checking for fdatasync... yes
checking for gettimeofday... yes
checking for isatty... yes
checking for memalign... yes
checking for memset... yes
checking for posix_memalign... yes
checking for pthread_yield... yes
checking for setvbuf... yes
checking for sqrt... yes
checking for strdup... yes
checking for thr_setconcurrency... no
checking for valloc... yes
checking for pthread_once... yes
checking that generated files are newer than configure... done
configure: creating ./config.status
config.status: creating Makefile
config.status: creating doc/xsl/Makefile
config.status: creating doc/xsl/catalog.xml
config.status: creating doc/Makefile
config.status: creating third_party/luajit/Makefile
config.status: creating third_party/concurrency_kit/Makefile
config.status: creating src/Makefile
config.status: creating src/drivers/Makefile
config.status: creating src/drivers/mysql/Makefile
config.status: creating src/drivers/drizzle/Makefile
config.status: creating src/drivers/oracle/Makefile
config.status: creating src/drivers/pgsql/Makefile
config.status: creating src/drivers/attachsql/Makefile
config.status: creating src/tests/Makefile
config.status: creating src/tests/cpu/Makefile
config.status: creating src/tests/fileio/Makefile
config.status: creating src/tests/memory/Makefile
config.status: creating src/tests/threads/Makefile
config.status: creating src/tests/mutex/Makefile
config.status: creating src/lua/Makefile
config.status: creating src/lua/internal/Makefile
config.status: creating tests/Makefile
config.status: creating tests/include/config.sh
config.status: creating snap/snapcraft.yaml
config.status: creating config/config.h
config.status: executing depfiles commands
config.status: executing libtool commands
===============================================================================
sysbench version   : 1.0.20
CC                 : gcc -std=gnu99
CFLAGS             : -O2 -funroll-loops -ggdb3  -march=core-avx2 -Wall -Wextra -Wpointer-arith -Wbad-function-cast -Wstrict-prototypes -Wnested-externs -Wno-format-zero-length -Wundef -Wstrict-prototypes -Wmissing-prototypes -Wmissing-declarations -Wredundant-decls -Wcast-align -Wvla   -pthread
CPPFLAGS           : -D_GNU_SOURCE   -I$(top_srcdir)/src -I$(abs_top_builddir)/third_party/luajit/inc -I$(abs_top_builddir)/third_party/concurrency_kit/include
LDFLAGS            :
LIBS               : -lm
EXTRA_LDFLAGS      :

prefix             : /root/sysbench1020
bindir             : ${prefix}/bin
libexecdir         : ${prefix}/libexec
mandir             : ${prefix}/share/man
datadir            : ${prefix}/share

MySQL support      : yes
Drizzle support    : no
AttachSQL support  : no
Oracle support     : no
PostgreSQL support : no

LuaJIT             : bundled
LUAJIT_CFLAGS      : -I$(abs_top_builddir)/third_party/luajit/inc
LUAJIT_LIBS        : $(abs_top_builddir)/third_party/luajit/lib/libluajit-5.1.a -ldl
LUAJIT_LDFLAGS     : -rdynamic

Concurrency Kit    : bundled
CK_CFLAGS          : -I$(abs_top_builddir)/third_party/concurrency_kit/include
CK_LIBS            : $(abs_top_builddir)/third_party/concurrency_kit/lib/libck.a
configure flags    :
===============================================================================
2024-07-03T15:45:00 [root@node77 /root/sysbench-1.0.20] $

2024-07-03T15:45:00 [root@node77 /root/sysbench-1.0.20] $ make -j4
Making all in doc
make[1]: Entering directory `/root/sysbench-1.0.20/doc'
Making all in xsl
make[2]: Entering directory `/root/sysbench-1.0.20/doc/xsl'
make[2]: Nothing to be done for `all'.
make[2]: Leaving directory `/root/sysbench-1.0.20/doc/xsl'
make[2]: Entering directory `/root/sysbench-1.0.20/doc'
touch manual.html
make[2]: Leaving directory `/root/sysbench-1.0.20/doc'
make[1]: Leaving directory `/root/sysbench-1.0.20/doc'
Making all in third_party/luajit
make[1]: Entering directory `/root/sysbench-1.0.20/third_party/luajit'
make -C ./luajit clean
make[2]: Entering directory `/root/sysbench-1.0.20/third_party/luajit/luajit'
make -C src clean
make[3]: Entering directory `/root/sysbench-1.0.20/third_party/luajit/luajit/src'
rm -f luajit libluajit.a libluajit.so host/minilua host/buildvm lj_vm.S lj_bcdef.h lj_ffdef.h lj_libdef.h lj_recdef.h lj_folddef.h host/buildvm_arch.h jit/vmdef.lua *.o host/*.o *.obj *.lib *.exp *.dll *.exe *.manifest *.pdb *.ilk
make[3]: Leaving directory `/root/sysbench-1.0.20/third_party/luajit/luajit/src'
make[2]: Leaving directory `/root/sysbench-1.0.20/third_party/luajit/luajit'
rm -rf tmp
mkdir tmp
tar -C . -cf - luajit | tar -xf - -C tmp/
chmod -R u+w tmp
make -C tmp/luajit \
                PREFIX=/root/sysbench-1.0.20/third_party/luajit \
                INSTALL_INC=/root/sysbench-1.0.20/third_party/luajit/inc \
                install
make[2]: Entering directory `/root/sysbench-1.0.20/third_party/luajit/tmp/luajit'
==== Building LuaJIT 2.1.0-beta2 ====
make -C src
make[3]: Entering directory `/root/sysbench-1.0.20/third_party/luajit/tmp/luajit/src'
HOSTCC    host/minilua.o
HOSTCC    host/buildvm_asm.o
HOSTCC    host/buildvm_peobj.o
HOSTCC    host/buildvm_lib.o
HOSTCC    host/buildvm_fold.o
CC        lj_gc.o
CC        lj_char.o
CC        lj_obj.o
CC        lj_buf.o
CC        lj_str.o
CC        lj_tab.o
CC        lj_func.o
CC        lj_udata.o
CC        lj_meta.o
CC        lj_debug.o
CC        lj_state.o
CC        lj_vmevent.o
CC        lj_vmmath.o
CC        lj_strscan.o
CC        lj_strfmt.o
CC        lj_strfmt_num.o
CC        lj_api.o
CC        lj_profile.o
CC        lj_lex.o
CC        lj_parse.o
CC        lj_bcread.o
CC        lj_bcwrite.o
CC        lj_load.o
CC        lj_ir.o
CC        lj_opt_mem.o
CC        lj_opt_narrow.o
CC        lj_opt_dce.o
CC        lj_opt_loop.o
CC        lj_opt_split.o
CC        lj_opt_sink.o
CC        lj_mcode.o
CC        lj_snap.o
CC        lj_asm.o
CC        lj_trace.o
CC        lj_gdbjit.o
CC        lj_ctype.o
CC        lj_cdata.o
CC        lj_cconv.o
CC        lj_ccall.o
CC        lj_ccallback.o
CC        lj_carith.o
CC        lj_clib.o
CC        lj_cparse.o
CC        lj_lib.o
CC        lj_alloc.o
CC        lib_aux.o
CC        lib_package.o
CC        lib_init.o
CC        luajit.o
HOSTLINK  host/minilua
DYNASM    host/buildvm_arch.h
HOSTCC    host/buildvm.o
HOSTLINK  host/buildvm
BUILDVM   lj_vm.S
BUILDVM   lj_ffdef.h
BUILDVM   lj_bcdef.h
BUILDVM   lj_folddef.h
BUILDVM   lj_recdef.h
BUILDVM   lj_libdef.h
BUILDVM   jit/vmdef.lua
ASM       lj_vm.o
CC        lj_err.o
CC        lj_bc.o
CC        lj_dispatch.o
CC        lj_record.o
CC        lj_crecord.o
CC        lj_ffrecord.o
CC        lib_base.o
CC        lib_math.o
CC        lib_bit.o
CC        lib_string.o
CC        lib_table.o
CC        lib_io.o
CC        lib_os.o
CC        lib_debug.o
CC        lib_jit.o
CC        lib_ffi.o
CC        lj_opt_fold.o
DYNLINK   libluajit.so
AR        libluajit.a
LINK      luajit
OK        Successfully built LuaJIT
make[3]: Leaving directory `/root/sysbench-1.0.20/third_party/luajit/tmp/luajit/src'
==== Successfully built LuaJIT 2.1.0-beta2 ====
==== Installing LuaJIT 2.1.0-beta2 to /root/sysbench-1.0.20/third_party/luajit ====
mkdir -p /root/sysbench-1.0.20/third_party/luajit/bin /root/sysbench-1.0.20/third_party/luajit/lib /root/sysbench-1.0.20/third_party/luajit/inc /root/sysbench-1.0.20/third_party/luajit/share/man/man1 /root/sysbench-1.0.20/third_party/luajit/lib/pkgconfig /root/sysbench-1.0.20/third_party/luajit/share/luajit-2.1.0-beta2/jit /root/sysbench-1.0.20/third_party/luajit/share/lua/5.1 /root/sysbench-1.0.20/third_party/luajit/lib/lua/5.1
cd src && install -m 0755 luajit /root/sysbench-1.0.20/third_party/luajit/bin/luajit-2.1.0-beta2
cd src && test -f libluajit.a && install -m 0644 libluajit.a /root/sysbench-1.0.20/third_party/luajit/lib/libluajit-5.1.a || :
rm -f /root/sysbench-1.0.20/third_party/luajit/lib/libluajit-5.1.so.2.1.0 /root/sysbench-1.0.20/third_party/luajit/lib/libluajit-5.1.so /root/sysbench-1.0.20/third_party/luajit/lib/libluajit-5.1.so.2
cd src && test -f libluajit.so && \
  install -m 0755 libluajit.so /root/sysbench-1.0.20/third_party/luajit/lib/libluajit-5.1.so.2.1.0 && \
  ldconfig -n /root/sysbench-1.0.20/third_party/luajit/lib && \
  ln -sf libluajit-5.1.so.2.1.0 /root/sysbench-1.0.20/third_party/luajit/lib/libluajit-5.1.so && \
  ln -sf libluajit-5.1.so.2.1.0 /root/sysbench-1.0.20/third_party/luajit/lib/libluajit-5.1.so.2 || :
cd etc && install -m 0644 luajit.1 /root/sysbench-1.0.20/third_party/luajit/share/man/man1
cd etc && sed -e "s|^prefix=.*|prefix=/root/sysbench-1.0.20/third_party/luajit|" -e "s|^multilib=.*|multilib=lib|" luajit.pc > luajit.pc.tmp && \
  install -m 0644 luajit.pc.tmp /root/sysbench-1.0.20/third_party/luajit/lib/pkgconfig/luajit.pc && \
  rm -f luajit.pc.tmp
cd src && install -m 0644 lua.h lualib.h lauxlib.h luaconf.h lua.hpp luajit.h /root/sysbench-1.0.20/third_party/luajit/inc
cd src/jit && install -m 0644 bc.lua bcsave.lua dump.lua p.lua v.lua zone.lua dis_x86.lua dis_x64.lua dis_arm.lua dis_arm64.lua dis_ppc.lua dis_mips.lua dis_mipsel.lua dis_mips64.lua dis_mips64el.lua vmdef.lua /root/sysbench-1.0.20/third_party/luajit/share/luajit-2.1.0-beta2/jit
==== Successfully installed LuaJIT 2.1.0-beta2 to /root/sysbench-1.0.20/third_party/luajit ====

Note: the development releases deliberately do NOT install a symlink for luajit
You can do this now by running this command (with sudo):

  ln -sf luajit-2.1.0-beta2 /root/sysbench-1.0.20/third_party/luajit/bin/luajit

make[2]: Leaving directory `/root/sysbench-1.0.20/third_party/luajit/tmp/luajit'
make[1]: Leaving directory `/root/sysbench-1.0.20/third_party/luajit'
Making all in third_party/concurrency_kit
make[1]: Entering directory `/root/sysbench-1.0.20/third_party/concurrency_kit'
rm -rf tmp
mkdir tmp
tar -C . -cf - ck | tar -xf - -C tmp/
chmod -R u+w tmp
cd tmp/ck &&                        \
CC="gcc -std=gnu99"                          \
CFLAGS="-O2 -funroll-loops -ggdb3  -march=core-avx2 -D_GNU_SOURCE "      \
LDFLAGS=""                \
  ./configure  \
  --prefix=/root/sysbench-1.0.20/third_party/concurrency_kit && \
make && \
make install
Detecting operating system.......success [linux]
Detecting machine architecture...success [x86_64]
Finding dirname command..........success [/usr/bin/dirname]
Determining build directory......success [/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck]
Finding gzip tool................success [/usr/bin/gzip]
Finding suitable compiler........success [/usr/bin/gcc]
Detecting VMA bits...............success [48]
Checking header file usability...success [stdbool.h]
Checking header file usability...success [stddef.h]
Checking header file usability...success [stdint.h]
Checking header file usability...success [string.h]
Detecting git SHA................not within a git repo []
Generating header files..........success
Generating build files...........success

           VERSION = 1.0.0
           GIT_SHA =
         BUILD_DIR = /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck
           SRC_DIR = /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck
            SYSTEM = linux
           PROFILE = x86_64
                CC = /usr/bin/gcc
          COMPILER = gcc
            CFLAGS = -D_XOPEN_SOURCE=600 -D_BSD_SOURCE -D_DEFAULT_SOURCE -std=gnu99 -pedantic -Wall -W -Wundef -Wendif-labels -Wshadow -Wpointer-arith -Wcast-align -Wcast-qual -Wwrite-strings -Wstrict-prototypes -Wmissing-prototypes -Wnested-externs -Winline -Wdisabled-optimization -fstrict-aliasing -O2 -pipe -Wno-parentheses -O2 -funroll-loops -ggdb3  -march=core-avx2 -D_GNU_SOURCE  -fPIC
    PTHREAD_CFLAGS = -pthread
                LD = /usr/bin/gcc
            LDNAME = libck.so
    LDNAME_VERSION = libck.so.1.0.0
      LDNAME_MAJOR = libck.so.0
           LDFLAGS = -Wl,-soname,libck.so.0 -m64  -shared -fPIC
              GZIP = /usr/bin/gzip -c
             CORES = 4
      POINTER_PACK = CK_MD_POINTER_PACK_DISABLE
          VMA_BITS = 48
      MEMORY_MODEL = CK_MD_TSO
               RTM = CK_MD_RTM_DISABLE
               LSE = CK_MD_LSE_DISABLE

Headers will be installed in /root/sysbench-1.0.20/third_party/concurrency_kit/include
Libraries will be installed in /root/sysbench-1.0.20/third_party/concurrency_kit/lib
Documentation will be installed in /root/sysbench-1.0.20/third_party/concurrency_kit/share/man
make[2]: Entering directory `/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck'
make -C doc all || exit
make[3]: Entering directory `/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/doc'
for target in CK_ARRAY_FOREACH ck_array_buffer ck_array_commit ck_array_deinit ck_array_init ck_array_initialized ck_array_length ck_array_put ck_array_put_unique ck_array_remove ck_array_deinit ck_brlock ck_ht_count ck_ht_destroy ck_ht_gc ck_ht_get_spmc ck_ht_grow_spmc ck_ht_hash ck_ht_hash_direct ck_ht_init ck_ht_put_spmc ck_ht_remove_spmc ck_ht_reset_spmc ck_ht_reset_size_spmc ck_ht_set_spmc ck_ht_entry_empty ck_ht_entry_key ck_ht_entry_key_direct ck_ht_entry_key_length ck_ht_entry_key_set ck_ht_entry_key_set_direct ck_ht_entry_set ck_ht_entry_set_direct ck_ht_entry_value_direct ck_ht_entry_value ck_ht_iterator_init ck_ht_next ck_ht_stat ck_bitmap_init ck_bitmap_reset ck_bitmap_set ck_bitmap_bts ck_bitmap_test ck_bitmap_base ck_bitmap_union ck_bitmap_size ck_bitmap_clear ck_bitmap_bits ck_bitmap_buffer ck_bitmap_next ck_bitmap_iterator_init ck_elide ck_epoch_barrier ck_epoch_begin ck_epoch_call ck_epoch_end ck_epoch_init ck_epoch_poll ck_epoch_recycle ck_epoch_register ck_epoch_reclaim ck_epoch_synchronize ck_epoch_unregister ck_hs_gc ck_hs_init ck_hs_destroy CK_HS_HASH ck_hs_apply ck_hs_iterator_init ck_hs_next ck_hs_get ck_hs_put ck_hs_put_unique ck_hs_set ck_hs_fas ck_hs_remove ck_hs_move ck_hs_grow ck_hs_rebuild ck_hs_count ck_hs_reset ck_hs_reset_size ck_hs_stat ck_rhs_gc ck_rhs_init ck_rhs_destroy CK_RHS_HASH ck_rhs_apply ck_rhs_iterator_init ck_rhs_next ck_rhs_get ck_rhs_put ck_rhs_put_unique ck_rhs_set ck_rhs_fas ck_rhs_remove ck_rhs_move ck_rhs_grow ck_rhs_rebuild ck_rhs_count ck_rhs_reset ck_rhs_reset_size ck_rhs_stat ck_rwcohort CK_RWCOHORT_INIT CK_RWCOHORT_INSTANCE CK_RWCOHORT_PROTOTYPE CK_RWCOHORT_READ_LOCK CK_RWCOHORT_READ_UNLOCK CK_RWCOHORT_WRITE_LOCK CK_RWCOHORT_WRITE_UNLOCK ck_cohort CK_COHORT_PROTOTYPE CK_COHORT_TRYLOCK_PROTOTYPE CK_COHORT_INSTANCE CK_COHORT_INIT CK_COHORT_LOCK CK_COHORT_UNLOCK CK_COHORT_TRYLOCK ck_pr ck_pr_fence_acquire ck_pr_fence_release ck_pr_barrier ck_pr_fas ck_pr_fence_atomic ck_pr_fence_atomic_load ck_pr_fence_atomic_store ck_pr_fence_load ck_pr_fence_load_atomic ck_pr_fence_load_store ck_pr_fence_load_depends ck_pr_fence_memory ck_pr_fence_store ck_pr_fence_store_atomic ck_pr_fence_store_load ck_pr_stall ck_pr_faa ck_pr_inc ck_pr_dec ck_pr_not ck_pr_neg ck_pr_add ck_pr_sub ck_pr_and ck_pr_xor ck_pr_or ck_pr_cas ck_pr_bts ck_pr_btc ck_pr_btr ck_pr_store ck_pr_load ck_pr_rtm ck_queue ck_ring_init ck_ring_dequeue_spmc ck_ring_enqueue_spmc ck_ring_enqueue_spmc_size ck_ring_trydequeue_spmc ck_ring_dequeue_spsc ck_ring_enqueue_spsc ck_ring_enqueue_spsc_size ck_ring_size ck_ring_capacity ck_tflock ck_rwlock ck_pflock ck_swlock ck_sequence ck_spinlock; do 			   \
	/usr/bin/gzip -c /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/doc/$target > /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/doc/$target.3.gz; \
done
make[3]: Leaving directory `/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/doc'
---[ Manual pages are ready for installation.
make -C src all || exit
make[3]: Entering directory `/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/src'
/usr/bin/gcc -D_XOPEN_SOURCE=600 -D_BSD_SOURCE -D_DEFAULT_SOURCE -std=gnu99 -pedantic -Wall -W -Wundef -Wendif-labels -Wshadow -Wpointer-arith -Wcast-align -Wcast-qual -Wwrite-strings -Wstrict-prototypes -Wmissing-prototypes -Wnested-externs -Winline -Wdisabled-optimization -fstrict-aliasing -O2 -pipe -Wno-parentheses -O2 -funroll-loops -ggdb3  -march=core-avx2 -D_GNU_SOURCE  -fPIC -I/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/include -I/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/include -m64 -D__x86_64__ -c -o /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/src/ck_barrier_centralized.o /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/src/ck_barrier_centralized.c
/usr/bin/gcc -D_XOPEN_SOURCE=600 -D_BSD_SOURCE -D_DEFAULT_SOURCE -std=gnu99 -pedantic -Wall -W -Wundef -Wendif-labels -Wshadow -Wpointer-arith -Wcast-align -Wcast-qual -Wwrite-strings -Wstrict-prototypes -Wmissing-prototypes -Wnested-externs -Winline -Wdisabled-optimization -fstrict-aliasing -O2 -pipe -Wno-parentheses -O2 -funroll-loops -ggdb3  -march=core-avx2 -D_GNU_SOURCE  -fPIC -I/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/include -I/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/include -m64 -D__x86_64__ -c -o /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/src/ck_barrier_combining.o /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/src/ck_barrier_combining.c
/usr/bin/gcc -D_XOPEN_SOURCE=600 -D_BSD_SOURCE -D_DEFAULT_SOURCE -std=gnu99 -pedantic -Wall -W -Wundef -Wendif-labels -Wshadow -Wpointer-arith -Wcast-align -Wcast-qual -Wwrite-strings -Wstrict-prototypes -Wmissing-prototypes -Wnested-externs -Winline -Wdisabled-optimization -fstrict-aliasing -O2 -pipe -Wno-parentheses -O2 -funroll-loops -ggdb3  -march=core-avx2 -D_GNU_SOURCE  -fPIC -I/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/include -I/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/include -m64 -D__x86_64__ -c -o /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/src/ck_barrier_dissemination.o /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/src/ck_barrier_dissemination.c
/usr/bin/gcc -D_XOPEN_SOURCE=600 -D_BSD_SOURCE -D_DEFAULT_SOURCE -std=gnu99 -pedantic -Wall -W -Wundef -Wendif-labels -Wshadow -Wpointer-arith -Wcast-align -Wcast-qual -Wwrite-strings -Wstrict-prototypes -Wmissing-prototypes -Wnested-externs -Winline -Wdisabled-optimization -fstrict-aliasing -O2 -pipe -Wno-parentheses -O2 -funroll-loops -ggdb3  -march=core-avx2 -D_GNU_SOURCE  -fPIC -I/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/include -I/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/include -m64 -D__x86_64__ -c -o /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/src/ck_barrier_tournament.o /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/src/ck_barrier_tournament.c
/usr/bin/gcc -D_XOPEN_SOURCE=600 -D_BSD_SOURCE -D_DEFAULT_SOURCE -std=gnu99 -pedantic -Wall -W -Wundef -Wendif-labels -Wshadow -Wpointer-arith -Wcast-align -Wcast-qual -Wwrite-strings -Wstrict-prototypes -Wmissing-prototypes -Wnested-externs -Winline -Wdisabled-optimization -fstrict-aliasing -O2 -pipe -Wno-parentheses -O2 -funroll-loops -ggdb3  -march=core-avx2 -D_GNU_SOURCE  -fPIC -I/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/include -I/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/include -m64 -D__x86_64__ -c -o /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/src/ck_barrier_mcs.o /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/src/ck_barrier_mcs.c
/usr/bin/gcc -D_XOPEN_SOURCE=600 -D_BSD_SOURCE -D_DEFAULT_SOURCE -std=gnu99 -pedantic -Wall -W -Wundef -Wendif-labels -Wshadow -Wpointer-arith -Wcast-align -Wcast-qual -Wwrite-strings -Wstrict-prototypes -Wmissing-prototypes -Wnested-externs -Winline -Wdisabled-optimization -fstrict-aliasing -O2 -pipe -Wno-parentheses -O2 -funroll-loops -ggdb3  -march=core-avx2 -D_GNU_SOURCE  -fPIC -I/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/include -I/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/include -m64 -D__x86_64__ -c -o /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/src/ck_epoch.o /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/src/ck_epoch.c
/usr/bin/gcc -D_XOPEN_SOURCE=600 -D_BSD_SOURCE -D_DEFAULT_SOURCE -std=gnu99 -pedantic -Wall -W -Wundef -Wendif-labels -Wshadow -Wpointer-arith -Wcast-align -Wcast-qual -Wwrite-strings -Wstrict-prototypes -Wmissing-prototypes -Wnested-externs -Winline -Wdisabled-optimization -fstrict-aliasing -O2 -pipe -Wno-parentheses -O2 -funroll-loops -ggdb3  -march=core-avx2 -D_GNU_SOURCE  -fPIC -I/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/include -I/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/include -m64 -D__x86_64__ -c -o /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/src/ck_ht.o /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/src/ck_ht.c
/usr/bin/gcc -D_XOPEN_SOURCE=600 -D_BSD_SOURCE -D_DEFAULT_SOURCE -std=gnu99 -pedantic -Wall -W -Wundef -Wendif-labels -Wshadow -Wpointer-arith -Wcast-align -Wcast-qual -Wwrite-strings -Wstrict-prototypes -Wmissing-prototypes -Wnested-externs -Winline -Wdisabled-optimization -fstrict-aliasing -O2 -pipe -Wno-parentheses -O2 -funroll-loops -ggdb3  -march=core-avx2 -D_GNU_SOURCE  -fPIC -I/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/include -I/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/include -m64 -D__x86_64__ -c -o /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/src/ck_hp.o /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/src/ck_hp.c
/usr/bin/gcc -D_XOPEN_SOURCE=600 -D_BSD_SOURCE -D_DEFAULT_SOURCE -std=gnu99 -pedantic -Wall -W -Wundef -Wendif-labels -Wshadow -Wpointer-arith -Wcast-align -Wcast-qual -Wwrite-strings -Wstrict-prototypes -Wmissing-prototypes -Wnested-externs -Winline -Wdisabled-optimization -fstrict-aliasing -O2 -pipe -Wno-parentheses -O2 -funroll-loops -ggdb3  -march=core-avx2 -D_GNU_SOURCE  -fPIC -I/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/include -I/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/include -m64 -D__x86_64__ -c -o /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/src/ck_hs.o /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/src/ck_hs.c
/usr/bin/gcc -D_XOPEN_SOURCE=600 -D_BSD_SOURCE -D_DEFAULT_SOURCE -std=gnu99 -pedantic -Wall -W -Wundef -Wendif-labels -Wshadow -Wpointer-arith -Wcast-align -Wcast-qual -Wwrite-strings -Wstrict-prototypes -Wmissing-prototypes -Wnested-externs -Winline -Wdisabled-optimization -fstrict-aliasing -O2 -pipe -Wno-parentheses -O2 -funroll-loops -ggdb3  -march=core-avx2 -D_GNU_SOURCE  -fPIC -I/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/include -I/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/include -m64 -D__x86_64__ -c -o /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/src/ck_rhs.o /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/src/ck_rhs.c
/usr/bin/gcc -D_XOPEN_SOURCE=600 -D_BSD_SOURCE -D_DEFAULT_SOURCE -std=gnu99 -pedantic -Wall -W -Wundef -Wendif-labels -Wshadow -Wpointer-arith -Wcast-align -Wcast-qual -Wwrite-strings -Wstrict-prototypes -Wmissing-prototypes -Wnested-externs -Winline -Wdisabled-optimization -fstrict-aliasing -O2 -pipe -Wno-parentheses -O2 -funroll-loops -ggdb3  -march=core-avx2 -D_GNU_SOURCE  -fPIC -I/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/include -I/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/include -m64 -D__x86_64__ -c -o /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/src/ck_array.o /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/src/ck_array.c
/usr/bin/gcc -Wl,-soname,libck.so.0 -m64  -shared -fPIC -m64 -o /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/src/libck.so ck_barrier_centralized.o ck_barrier_combining.o ck_barrier_dissemination.o ck_barrier_tournament.o ck_barrier_mcs.o ck_epoch.o ck_ht.o ck_hp.o ck_hs.o ck_rhs.o ck_array.o
ar rcs /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/src/libck.a ck_barrier_centralized.o ck_barrier_combining.o ck_barrier_dissemination.o ck_barrier_tournament.o ck_barrier_mcs.o ck_epoch.o ck_ht.o ck_hp.o ck_hs.o ck_rhs.o ck_array.o
make[3]: Leaving directory `/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/src'


---[ Concurrency Kit has built successfully. You may now "make install".
make[2]: Leaving directory `/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck'
make[2]: Entering directory `/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck'
make -C doc all || exit
mkdir -p //root/sysbench-1.0.20/third_party/concurrency_kit/include || exit
mkdir -p //root/sysbench-1.0.20/third_party/concurrency_kit/lib
make[3]: Entering directory `/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/doc'
for target in CK_ARRAY_FOREACH ck_array_buffer ck_array_commit ck_array_deinit ck_array_init ck_array_initialized ck_array_length ck_array_put ck_array_put_unique ck_array_remove ck_array_deinit ck_brlock ck_ht_count ck_ht_destroy ck_ht_gc ck_ht_get_spmc ck_ht_grow_spmc ck_ht_hash ck_ht_hash_direct ck_ht_init ck_ht_put_spmc ck_ht_remove_spmc ck_ht_reset_spmc ck_ht_reset_size_spmc ck_ht_set_spmc ck_ht_entry_empty ck_ht_entry_key ck_ht_entry_key_direct ck_ht_entry_key_length ck_ht_entry_key_set ck_ht_entry_key_set_direct ck_ht_entry_set ck_ht_entry_set_direct ck_ht_entry_value_direct ck_ht_entry_value ck_ht_iterator_init ck_ht_next ck_ht_stat ck_bitmap_init ck_bitmap_reset ck_bitmap_set ck_bitmap_bts ck_bitmap_test ck_bitmap_base ck_bitmap_union ck_bitmap_size ck_bitmap_clear ck_bitmap_bits ck_bitmap_buffer ck_bitmap_next ck_bitmap_iterator_init ck_elide ck_epoch_barrier ck_epoch_begin ck_epoch_call ck_epoch_end ck_epoch_init ck_epoch_poll ck_epoch_recycle ck_epoch_register ck_epoch_reclaim ck_epoch_synchronize ck_epoch_unregister ck_hs_gc ck_hs_init ck_hs_destroy CK_HS_HASH ck_hs_apply ck_hs_iterator_init ck_hs_next ck_hs_get ck_hs_put ck_hs_put_unique ck_hs_set ck_hs_fas ck_hs_remove ck_hs_move ck_hs_grow ck_hs_rebuild ck_hs_count ck_hs_reset ck_hs_reset_size ck_hs_stat ck_rhs_gc ck_rhs_init ck_rhs_destroy CK_RHS_HASH ck_rhs_apply ck_rhs_iterator_init ck_rhs_next ck_rhs_get ck_rhs_put ck_rhs_put_unique ck_rhs_set ck_rhs_fas ck_rhs_remove ck_rhs_move ck_rhs_grow ck_rhs_rebuild ck_rhs_count ck_rhs_reset ck_rhs_reset_size ck_rhs_stat ck_rwcohort CK_RWCOHORT_INIT CK_RWCOHORT_INSTANCE CK_RWCOHORT_PROTOTYPE CK_RWCOHORT_READ_LOCK CK_RWCOHORT_READ_UNLOCK CK_RWCOHORT_WRITE_LOCK CK_RWCOHORT_WRITE_UNLOCK ck_cohort CK_COHORT_PROTOTYPE CK_COHORT_TRYLOCK_PROTOTYPE CK_COHORT_INSTANCE CK_COHORT_INIT CK_COHORT_LOCK CK_COHORT_UNLOCK CK_COHORT_TRYLOCK ck_pr ck_pr_fence_acquire ck_pr_fence_release ck_pr_barrier ck_pr_fas ck_pr_fence_atomic ck_pr_fence_atomic_load ck_pr_fence_atomic_store ck_pr_fence_load ck_pr_fence_load_atomic ck_pr_fence_load_store ck_pr_fence_load_depends ck_pr_fence_memory ck_pr_fence_store ck_pr_fence_store_atomic ck_pr_fence_store_load ck_pr_stall ck_pr_faa ck_pr_inc ck_pr_dec ck_pr_not ck_pr_neg ck_pr_add ck_pr_sub ck_pr_and ck_pr_xor ck_pr_or ck_pr_cas ck_pr_bts ck_pr_btc ck_pr_btr ck_pr_store ck_pr_load ck_pr_rtm ck_queue ck_ring_init ck_ring_dequeue_spmc ck_ring_enqueue_spmc ck_ring_enqueue_spmc_size ck_ring_trydequeue_spmc ck_ring_dequeue_spsc ck_ring_enqueue_spsc ck_ring_enqueue_spsc_size ck_ring_size ck_ring_capacity ck_tflock ck_rwlock ck_pflock ck_swlock ck_sequence ck_spinlock; do 			   \
	/usr/bin/gzip -c /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/doc/$target > /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/doc/$target.3.gz; \
done
mkdir -p //root/sysbench-1.0.20/third_party/concurrency_kit/lib
cp src/libck.so //root/sysbench-1.0.20/third_party/concurrency_kit/lib/libck.so.1.0.0
cp /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/include/*.h //root/sysbench-1.0.20/third_party/concurrency_kit/include || exit
cp src/libck.a //root/sysbench-1.0.20/third_party/concurrency_kit/lib/libck.a
ln -sf libck.so.1.0.0 //root/sysbench-1.0.20/third_party/concurrency_kit/lib/libck.so
chmod 644 //root/sysbench-1.0.20/third_party/concurrency_kit/lib/libck.a
ln -sf libck.so.1.0.0 //root/sysbench-1.0.20/third_party/concurrency_kit/lib/libck.so.0
chmod 744 //root/sysbench-1.0.20/third_party/concurrency_kit/lib/libck.so.1.0.0	\
	  //root/sysbench-1.0.20/third_party/concurrency_kit/lib/libck.so		\
	  //root/sysbench-1.0.20/third_party/concurrency_kit/lib/libck.so.0
chmod 644 //root/sysbench-1.0.20/third_party/concurrency_kit/include/ck_*.h || exit
mkdir -p /root/sysbench-1.0.20/third_party/concurrency_kit/include/gcc || exit
cp -r /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/include/gcc/* //root/sysbench-1.0.20/third_party/concurrency_kit/include/gcc || exit
cp include/ck_md.h //root/sysbench-1.0.20/third_party/concurrency_kit/include/ck_md.h || exit
chmod 755 //root/sysbench-1.0.20/third_party/concurrency_kit/include/gcc
chmod 644 //root/sysbench-1.0.20/third_party/concurrency_kit/include/gcc/ck_*.h //root/sysbench-1.0.20/third_party/concurrency_kit/include/gcc/*/ck_*.h || exit
mkdir -p /root/sysbench-1.0.20/third_party/concurrency_kit/include/spinlock || exit
cp -r /root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/include/spinlock/* //root/sysbench-1.0.20/third_party/concurrency_kit/include/spinlock || exit
chmod 755 //root/sysbench-1.0.20/third_party/concurrency_kit/include/spinlock
chmod 644 //root/sysbench-1.0.20/third_party/concurrency_kit/include/spinlock/*.h || exit
make[3]: Leaving directory `/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/doc'
---[ Manual pages are ready for installation.
make -C src all || exit
make[3]: Entering directory `/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/src'
make[3]: Nothing to be done for `all'.
make[3]: Leaving directory `/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/src'


---[ Concurrency Kit has built successfully. You may now "make install".
make -C doc install
make[3]: Entering directory `/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/doc'
mkdir -p //root/sysbench-1.0.20/third_party/concurrency_kit/share/man/man3 || exit
cp *.3.gz //root/sysbench-1.0.20/third_party/concurrency_kit/share/man/man3 || exit
make[3]: Leaving directory `/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck/doc'
mkdir -p //root/sysbench-1.0.20/third_party/concurrency_kit/lib || exit
mkdir -p //root/sysbench-1.0.20/third_party/concurrency_kit/lib/pkgconfig || exit
chmod 755 //root/sysbench-1.0.20/third_party/concurrency_kit/lib/pkgconfig
cp build/ck.pc //root/sysbench-1.0.20/third_party/concurrency_kit/lib/pkgconfig/ck.pc || exit


---[ Concurrency Kit has installed successfully.
make[2]: Leaving directory `/root/sysbench-1.0.20/third_party/concurrency_kit/tmp/ck'
make[1]: Leaving directory `/root/sysbench-1.0.20/third_party/concurrency_kit'
Making all in src
make[1]: Entering directory `/root/sysbench-1.0.20/src'
Making all in drivers
make[2]: Entering directory `/root/sysbench-1.0.20/src/drivers'
Making all in mysql
make[3]: Entering directory `/root/sysbench-1.0.20/src/drivers/mysql'
gcc -std=gnu99 -DHAVE_CONFIG_H -I. -I../../../config  -I/usr/include/mysql -I../../../src -I/root/sysbench-1.0.20/third_party/luajit/inc -I/root/sysbench-1.0.20/third_party/concurrency_kit/include -D_GNU_SOURCE  -Wall -Wextra -Wpointer-arith -Wbad-function-cast -Wstrict-prototypes -Wnested-externs -Wno-format-zero-length -Wundef -Wstrict-prototypes -Wmissing-prototypes -Wmissing-declarations -Wredundant-decls -Wcast-align -Wvla   -pthread -O2 -funroll-loops -ggdb3  -march=core-avx2 -MT libsbmysql_a-drv_mysql.o -MD -MP -MF .deps/libsbmysql_a-drv_mysql.Tpo -c -o libsbmysql_a-drv_mysql.o `test -f 'drv_mysql.c' || echo './'`drv_mysql.c
mv -f .deps/libsbmysql_a-drv_mysql.Tpo .deps/libsbmysql_a-drv_mysql.Po
rm -f libsbmysql.a
ar cru libsbmysql.a libsbmysql_a-drv_mysql.o
ranlib libsbmysql.a
make[3]: Leaving directory `/root/sysbench-1.0.20/src/drivers/mysql'
make[3]: Entering directory `/root/sysbench-1.0.20/src/drivers'
make[3]: Nothing to be done for `all-am'.
make[3]: Leaving directory `/root/sysbench-1.0.20/src/drivers'
make[2]: Leaving directory `/root/sysbench-1.0.20/src/drivers'
Making all in tests
make[2]: Entering directory `/root/sysbench-1.0.20/src/tests'
Making all in cpu
make[3]: Entering directory `/root/sysbench-1.0.20/src/tests/cpu'
gcc -std=gnu99 -DHAVE_CONFIG_H -I. -I../../../config  -I../../../src -I/root/sysbench-1.0.20/third_party/luajit/inc -I/root/sysbench-1.0.20/third_party/concurrency_kit/include -D_GNU_SOURCE  -Wall -Wextra -Wpointer-arith -Wbad-function-cast -Wstrict-prototypes -Wnested-externs -Wno-format-zero-length -Wundef -Wstrict-prototypes -Wmissing-prototypes -Wmissing-declarations -Wredundant-decls -Wcast-align -Wvla   -pthread -O2 -funroll-loops -ggdb3  -march=core-avx2 -MT libsbcpu_a-sb_cpu.o -MD -MP -MF .deps/libsbcpu_a-sb_cpu.Tpo -c -o libsbcpu_a-sb_cpu.o `test -f 'sb_cpu.c' || echo './'`sb_cpu.c
mv -f .deps/libsbcpu_a-sb_cpu.Tpo .deps/libsbcpu_a-sb_cpu.Po
rm -f libsbcpu.a
ar cru libsbcpu.a libsbcpu_a-sb_cpu.o
ranlib libsbcpu.a
make[3]: Leaving directory `/root/sysbench-1.0.20/src/tests/cpu'
Making all in fileio
make[3]: Entering directory `/root/sysbench-1.0.20/src/tests/fileio'
gcc -std=gnu99 -DHAVE_CONFIG_H -I. -I../../../config  -I../../../src -I/root/sysbench-1.0.20/third_party/luajit/inc -I/root/sysbench-1.0.20/third_party/concurrency_kit/include -D_GNU_SOURCE  -Wall -Wextra -Wpointer-arith -Wbad-function-cast -Wstrict-prototypes -Wnested-externs -Wno-format-zero-length -Wundef -Wstrict-prototypes -Wmissing-prototypes -Wmissing-declarations -Wredundant-decls -Wcast-align -Wvla   -pthread -O2 -funroll-loops -ggdb3  -march=core-avx2 -MT libsbfileio_a-sb_fileio.o -MD -MP -MF .deps/libsbfileio_a-sb_fileio.Tpo -c -o libsbfileio_a-sb_fileio.o `test -f 'sb_fileio.c' || echo './'`sb_fileio.c
gcc -std=gnu99 -DHAVE_CONFIG_H -I. -I../../../config  -I../../../src -I/root/sysbench-1.0.20/third_party/luajit/inc -I/root/sysbench-1.0.20/third_party/concurrency_kit/include -D_GNU_SOURCE  -Wall -Wextra -Wpointer-arith -Wbad-function-cast -Wstrict-prototypes -Wnested-externs -Wno-format-zero-length -Wundef -Wstrict-prototypes -Wmissing-prototypes -Wmissing-declarations -Wredundant-decls -Wcast-align -Wvla   -pthread -O2 -funroll-loops -ggdb3  -march=core-avx2 -MT libsbfileio_a-crc32.o -MD -MP -MF .deps/libsbfileio_a-crc32.Tpo -c -o libsbfileio_a-crc32.o `test -f 'crc32.c' || echo './'`crc32.c
In file included from ../../../src/sb_rand.h:24:0,
                 from sb_fileio.c:64:
../../../src/xoroshiro128plus.h:34:17: warning: no previous prototype for ‘xoroshiro_rotl’ [-Wmissing-prototypes]
 inline uint64_t xoroshiro_rotl(const uint64_t x, int k) {
                 ^
../../../src/xoroshiro128plus.h:38:17: warning: no previous prototype for ‘xoroshiro_next’ [-Wmissing-prototypes]
 inline uint64_t xoroshiro_next(uint64_t s[2]) {
                 ^
In file included from sb_fileio.c:64:0:
../../../src/sb_rand.h:44:17: warning: no previous prototype for ‘sb_rand_uniform_uint64’ [-Wmissing-prototypes]
 inline uint64_t sb_rand_uniform_uint64(void)
                 ^
../../../src/sb_rand.h:50:15: warning: no previous prototype for ‘sb_rand_uniform_double’ [-Wmissing-prototypes]
 inline double sb_rand_uniform_double(void)
               ^
mv -f .deps/libsbfileio_a-crc32.Tpo .deps/libsbfileio_a-crc32.Po
mv -f .deps/libsbfileio_a-sb_fileio.Tpo .deps/libsbfileio_a-sb_fileio.Po
rm -f libsbfileio.a
ar cru libsbfileio.a libsbfileio_a-sb_fileio.o libsbfileio_a-crc32.o
ranlib libsbfileio.a
make[3]: Leaving directory `/root/sysbench-1.0.20/src/tests/fileio'
Making all in memory
make[3]: Entering directory `/root/sysbench-1.0.20/src/tests/memory'
gcc -std=gnu99 -DHAVE_CONFIG_H -I. -I../../../config  -I../../../src -I/root/sysbench-1.0.20/third_party/luajit/inc -I/root/sysbench-1.0.20/third_party/concurrency_kit/include -D_GNU_SOURCE  -Wall -Wextra -Wpointer-arith -Wbad-function-cast -Wstrict-prototypes -Wnested-externs -Wno-format-zero-length -Wundef -Wstrict-prototypes -Wmissing-prototypes -Wmissing-declarations -Wredundant-decls -Wcast-align -Wvla   -pthread -O2 -funroll-loops -ggdb3  -march=core-avx2 -MT libsbmemory_a-sb_memory.o -MD -MP -MF .deps/libsbmemory_a-sb_memory.Tpo -c -o libsbmemory_a-sb_memory.o `test -f 'sb_memory.c' || echo './'`sb_memory.c
In file included from ../../../src/sb_rand.h:24:0,
                 from sb_memory.c:27:
../../../src/xoroshiro128plus.h:34:17: warning: no previous prototype for ‘xoroshiro_rotl’ [-Wmissing-prototypes]
 inline uint64_t xoroshiro_rotl(const uint64_t x, int k) {
                 ^
../../../src/xoroshiro128plus.h:38:17: warning: no previous prototype for ‘xoroshiro_next’ [-Wmissing-prototypes]
 inline uint64_t xoroshiro_next(uint64_t s[2]) {
                 ^
In file included from sb_memory.c:27:0:
../../../src/sb_rand.h:44:17: warning: no previous prototype for ‘sb_rand_uniform_uint64’ [-Wmissing-prototypes]
 inline uint64_t sb_rand_uniform_uint64(void)
                 ^
../../../src/sb_rand.h:50:15: warning: no previous prototype for ‘sb_rand_uniform_double’ [-Wmissing-prototypes]
 inline double sb_rand_uniform_double(void)
               ^
mv -f .deps/libsbmemory_a-sb_memory.Tpo .deps/libsbmemory_a-sb_memory.Po
rm -f libsbmemory.a
ar cru libsbmemory.a libsbmemory_a-sb_memory.o
ranlib libsbmemory.a
make[3]: Leaving directory `/root/sysbench-1.0.20/src/tests/memory'
Making all in threads
make[3]: Entering directory `/root/sysbench-1.0.20/src/tests/threads'
gcc -std=gnu99 -DHAVE_CONFIG_H -I. -I../../../config  -I../../../src -I/root/sysbench-1.0.20/third_party/luajit/inc -I/root/sysbench-1.0.20/third_party/concurrency_kit/include -D_GNU_SOURCE  -Wall -Wextra -Wpointer-arith -Wbad-function-cast -Wstrict-prototypes -Wnested-externs -Wno-format-zero-length -Wundef -Wstrict-prototypes -Wmissing-prototypes -Wmissing-declarations -Wredundant-decls -Wcast-align -Wvla   -pthread -O2 -funroll-loops -ggdb3  -march=core-avx2 -MT libsbthreads_a-sb_threads.o -MD -MP -MF .deps/libsbthreads_a-sb_threads.Tpo -c -o libsbthreads_a-sb_threads.o `test -f 'sb_threads.c' || echo './'`sb_threads.c
mv -f .deps/libsbthreads_a-sb_threads.Tpo .deps/libsbthreads_a-sb_threads.Po
rm -f libsbthreads.a
ar cru libsbthreads.a libsbthreads_a-sb_threads.o
ranlib libsbthreads.a
make[3]: Leaving directory `/root/sysbench-1.0.20/src/tests/threads'
Making all in mutex
make[3]: Entering directory `/root/sysbench-1.0.20/src/tests/mutex'
gcc -std=gnu99 -DHAVE_CONFIG_H -I. -I../../../config  -I../../../src -I/root/sysbench-1.0.20/third_party/luajit/inc -I/root/sysbench-1.0.20/third_party/concurrency_kit/include -D_GNU_SOURCE  -Wall -Wextra -Wpointer-arith -Wbad-function-cast -Wstrict-prototypes -Wnested-externs -Wno-format-zero-length -Wundef -Wstrict-prototypes -Wmissing-prototypes -Wmissing-declarations -Wredundant-decls -Wcast-align -Wvla   -pthread -O2 -funroll-loops -ggdb3  -march=core-avx2 -MT libsbmutex_a-sb_mutex.o -MD -MP -MF .deps/libsbmutex_a-sb_mutex.Tpo -c -o libsbmutex_a-sb_mutex.o `test -f 'sb_mutex.c' || echo './'`sb_mutex.c
In file included from ../../../src/sb_rand.h:24:0,
                 from sb_mutex.c:33:
../../../src/xoroshiro128plus.h:34:17: warning: no previous prototype for ‘xoroshiro_rotl’ [-Wmissing-prototypes]
 inline uint64_t xoroshiro_rotl(const uint64_t x, int k) {
                 ^
../../../src/xoroshiro128plus.h:38:17: warning: no previous prototype for ‘xoroshiro_next’ [-Wmissing-prototypes]
 inline uint64_t xoroshiro_next(uint64_t s[2]) {
                 ^
In file included from sb_mutex.c:33:0:
../../../src/sb_rand.h:44:17: warning: no previous prototype for ‘sb_rand_uniform_uint64’ [-Wmissing-prototypes]
 inline uint64_t sb_rand_uniform_uint64(void)
                 ^
../../../src/sb_rand.h:50:15: warning: no previous prototype for ‘sb_rand_uniform_double’ [-Wmissing-prototypes]
 inline double sb_rand_uniform_double(void)
               ^
mv -f .deps/libsbmutex_a-sb_mutex.Tpo .deps/libsbmutex_a-sb_mutex.Po
rm -f libsbmutex.a
ar cru libsbmutex.a libsbmutex_a-sb_mutex.o
ranlib libsbmutex.a
make[3]: Leaving directory `/root/sysbench-1.0.20/src/tests/mutex'
make[3]: Entering directory `/root/sysbench-1.0.20/src/tests'
make[3]: Nothing to be done for `all-am'.
make[3]: Leaving directory `/root/sysbench-1.0.20/src/tests'
make[2]: Leaving directory `/root/sysbench-1.0.20/src/tests'
Making all in lua
make[2]: Entering directory `/root/sysbench-1.0.20/src/lua'
Making all in internal
make[3]: Entering directory `/root/sysbench-1.0.20/src/lua/internal'
Creating sysbench.lua.h from sysbench.lua
Creating sysbench.rand.lua.h from sysbench.rand.lua
Creating sysbench.sql.lua.h from sysbench.sql.lua
Creating sysbench.compat.lua.h from sysbench.compat.lua
Creating sysbench.cmdline.lua.h from sysbench.cmdline.lua
Creating sysbench.histogram.lua.h from sysbench.histogram.lua
make  all-am
make[4]: Entering directory `/root/sysbench-1.0.20/src/lua/internal'
make[4]: Nothing to be done for `all-am'.
make[4]: Leaving directory `/root/sysbench-1.0.20/src/lua/internal'
make[3]: Leaving directory `/root/sysbench-1.0.20/src/lua/internal'
make[3]: Entering directory `/root/sysbench-1.0.20/src/lua'
make[3]: Nothing to be done for `all-am'.
make[3]: Leaving directory `/root/sysbench-1.0.20/src/lua'
make[2]: Leaving directory `/root/sysbench-1.0.20/src/lua'
Making all in .
make[2]: Entering directory `/root/sysbench-1.0.20/src'
gcc -std=gnu99 -DHAVE_CONFIG_H -I. -I../config  -I../src -I/root/sysbench-1.0.20/third_party/luajit/inc -I/root/sysbench-1.0.20/third_party/concurrency_kit/include -DDATADIR=\"/root/sysbench1020/share/sysbench\" -DLIBDIR=\"/root/sysbench1020/lib/sysbench\" -D_GNU_SOURCE  -Wall -Wextra -Wpointer-arith -Wbad-function-cast -Wstrict-prototypes -Wnested-externs -Wno-format-zero-length -Wundef -Wstrict-prototypes -Wmissing-prototypes -Wmissing-declarations -Wredundant-decls -Wcast-align -Wvla   -pthread -O2 -funroll-loops -ggdb3  -march=core-avx2 -MT sysbench.o -MD -MP -MF .deps/sysbench.Tpo -c -o sysbench.o sysbench.c
gcc -std=gnu99 -DHAVE_CONFIG_H -I. -I../config  -I../src -I/root/sysbench-1.0.20/third_party/luajit/inc -I/root/sysbench-1.0.20/third_party/concurrency_kit/include -DDATADIR=\"/root/sysbench1020/share/sysbench\" -DLIBDIR=\"/root/sysbench1020/lib/sysbench\" -D_GNU_SOURCE  -Wall -Wextra -Wpointer-arith -Wbad-function-cast -Wstrict-prototypes -Wnested-externs -Wno-format-zero-length -Wundef -Wstrict-prototypes -Wmissing-prototypes -Wmissing-declarations -Wredundant-decls -Wcast-align -Wvla   -pthread -O2 -funroll-loops -ggdb3  -march=core-avx2 -MT sb_timer.o -MD -MP -MF .deps/sb_timer.Tpo -c -o sb_timer.o sb_timer.c
gcc -std=gnu99 -DHAVE_CONFIG_H -I. -I../config  -I../src -I/root/sysbench-1.0.20/third_party/luajit/inc -I/root/sysbench-1.0.20/third_party/concurrency_kit/include -DDATADIR=\"/root/sysbench1020/share/sysbench\" -DLIBDIR=\"/root/sysbench1020/lib/sysbench\" -D_GNU_SOURCE  -Wall -Wextra -Wpointer-arith -Wbad-function-cast -Wstrict-prototypes -Wnested-externs -Wno-format-zero-length -Wundef -Wstrict-prototypes -Wmissing-prototypes -Wmissing-declarations -Wredundant-decls -Wcast-align -Wvla   -pthread -O2 -funroll-loops -ggdb3  -march=core-avx2 -MT sb_options.o -MD -MP -MF .deps/sb_options.Tpo -c -o sb_options.o sb_options.c
gcc -std=gnu99 -DHAVE_CONFIG_H -I. -I../config  -I../src -I/root/sysbench-1.0.20/third_party/luajit/inc -I/root/sysbench-1.0.20/third_party/concurrency_kit/include -DDATADIR=\"/root/sysbench1020/share/sysbench\" -DLIBDIR=\"/root/sysbench1020/lib/sysbench\" -D_GNU_SOURCE  -Wall -Wextra -Wpointer-arith -Wbad-function-cast -Wstrict-prototypes -Wnested-externs -Wno-format-zero-length -Wundef -Wstrict-prototypes -Wmissing-prototypes -Wmissing-declarations -Wredundant-decls -Wcast-align -Wvla   -pthread -O2 -funroll-loops -ggdb3  -march=core-avx2 -MT sb_logger.o -MD -MP -MF .deps/sb_logger.Tpo -c -o sb_logger.o sb_logger.c
In file included from sb_rand.h:24:0,
                 from sysbench.c:73:
xoroshiro128plus.h:34:17: warning: no previous prototype for ‘xoroshiro_rotl’ [-Wmissing-prototypes]
 inline uint64_t xoroshiro_rotl(const uint64_t x, int k) {
                 ^
xoroshiro128plus.h:38:17: warning: no previous prototype for ‘xoroshiro_next’ [-Wmissing-prototypes]
 inline uint64_t xoroshiro_next(uint64_t s[2]) {
                 ^
In file included from sysbench.c:73:0:
sb_rand.h:44:17: warning: no previous prototype for ‘sb_rand_uniform_uint64’ [-Wmissing-prototypes]
 inline uint64_t sb_rand_uniform_uint64(void)
                 ^
sb_rand.h:50:15: warning: no previous prototype for ‘sb_rand_uniform_double’ [-Wmissing-prototypes]
 inline double sb_rand_uniform_double(void)
               ^
mv -f .deps/sb_timer.Tpo .deps/sb_timer.Po
gcc -std=gnu99 -DHAVE_CONFIG_H -I. -I../config  -I../src -I/root/sysbench-1.0.20/third_party/luajit/inc -I/root/sysbench-1.0.20/third_party/concurrency_kit/include -DDATADIR=\"/root/sysbench1020/share/sysbench\" -DLIBDIR=\"/root/sysbench1020/lib/sysbench\" -D_GNU_SOURCE  -Wall -Wextra -Wpointer-arith -Wbad-function-cast -Wstrict-prototypes -Wnested-externs -Wno-format-zero-length -Wundef -Wstrict-prototypes -Wmissing-prototypes -Wmissing-declarations -Wredundant-decls -Wcast-align -Wvla   -pthread -O2 -funroll-loops -ggdb3  -march=core-avx2 -MT db_driver.o -MD -MP -MF .deps/db_driver.Tpo -c -o db_driver.o db_driver.c
mv -f .deps/sb_logger.Tpo .deps/sb_logger.Po
gcc -std=gnu99 -DHAVE_CONFIG_H -I. -I../config  -I../src -I/root/sysbench-1.0.20/third_party/luajit/inc -I/root/sysbench-1.0.20/third_party/concurrency_kit/include -DDATADIR=\"/root/sysbench1020/share/sysbench\" -DLIBDIR=\"/root/sysbench1020/lib/sysbench\" -D_GNU_SOURCE  -Wall -Wextra -Wpointer-arith -Wbad-function-cast -Wstrict-prototypes -Wnested-externs -Wno-format-zero-length -Wundef -Wstrict-prototypes -Wmissing-prototypes -Wmissing-declarations -Wredundant-decls -Wcast-align -Wvla   -pthread -O2 -funroll-loops -ggdb3  -march=core-avx2 -MT sb_histogram.o -MD -MP -MF .deps/sb_histogram.Tpo -c -o sb_histogram.o sb_histogram.c
In file included from sb_rand.h:24:0,
                 from sb_histogram.c:39:
xoroshiro128plus.h:34:17: warning: no previous prototype for ‘xoroshiro_rotl’ [-Wmissing-prototypes]
 inline uint64_t xoroshiro_rotl(const uint64_t x, int k) {
                 ^
xoroshiro128plus.h:38:17: warning: no previous prototype for ‘xoroshiro_next’ [-Wmissing-prototypes]
 inline uint64_t xoroshiro_next(uint64_t s[2]) {
                 ^
In file included from sb_histogram.c:39:0:
sb_rand.h:44:17: warning: no previous prototype for ‘sb_rand_uniform_uint64’ [-Wmissing-prototypes]
 inline uint64_t sb_rand_uniform_uint64(void)
                 ^
sb_rand.h:50:15: warning: no previous prototype for ‘sb_rand_uniform_double’ [-Wmissing-prototypes]
 inline double sb_rand_uniform_double(void)
               ^
mv -f .deps/sb_options.Tpo .deps/sb_options.Po
gcc -std=gnu99 -DHAVE_CONFIG_H -I. -I../config  -I../src -I/root/sysbench-1.0.20/third_party/luajit/inc -I/root/sysbench-1.0.20/third_party/concurrency_kit/include -DDATADIR=\"/root/sysbench1020/share/sysbench\" -DLIBDIR=\"/root/sysbench1020/lib/sysbench\" -D_GNU_SOURCE  -Wall -Wextra -Wpointer-arith -Wbad-function-cast -Wstrict-prototypes -Wnested-externs -Wno-format-zero-length -Wundef -Wstrict-prototypes -Wmissing-prototypes -Wmissing-declarations -Wredundant-decls -Wcast-align -Wvla   -pthread -O2 -funroll-loops -ggdb3  -march=core-avx2 -MT sb_rand.o -MD -MP -MF .deps/sb_rand.Tpo -c -o sb_rand.o sb_rand.c
In file included from sb_rand.h:24:0,
                 from sb_rand.c:35:
xoroshiro128plus.h:34:17: warning: no previous prototype for ‘xoroshiro_rotl’ [-Wmissing-prototypes]
 inline uint64_t xoroshiro_rotl(const uint64_t x, int k) {
                 ^
xoroshiro128plus.h:38:17: warning: no previous prototype for ‘xoroshiro_next’ [-Wmissing-prototypes]
 inline uint64_t xoroshiro_next(uint64_t s[2]) {
                 ^
In file included from sb_rand.c:35:0:
sb_rand.h:44:17: warning: no previous prototype for ‘sb_rand_uniform_uint64’ [-Wmissing-prototypes]
 inline uint64_t sb_rand_uniform_uint64(void)
                 ^
sb_rand.h:50:15: warning: no previous prototype for ‘sb_rand_uniform_double’ [-Wmissing-prototypes]
 inline double sb_rand_uniform_double(void)
               ^
mv -f .deps/sb_rand.Tpo .deps/sb_rand.Po
gcc -std=gnu99 -DHAVE_CONFIG_H -I. -I../config  -I../src -I/root/sysbench-1.0.20/third_party/luajit/inc -I/root/sysbench-1.0.20/third_party/concurrency_kit/include -DDATADIR=\"/root/sysbench1020/share/sysbench\" -DLIBDIR=\"/root/sysbench1020/lib/sysbench\" -D_GNU_SOURCE  -Wall -Wextra -Wpointer-arith -Wbad-function-cast -Wstrict-prototypes -Wnested-externs -Wno-format-zero-length -Wundef -Wstrict-prototypes -Wmissing-prototypes -Wmissing-declarations -Wredundant-decls -Wcast-align -Wvla   -pthread -O2 -funroll-loops -ggdb3  -march=core-avx2 -MT sb_thread.o -MD -MP -MF .deps/sb_thread.Tpo -c -o sb_thread.o sb_thread.c
In file included from sb_rand.h:24:0,
                 from sb_thread.c:37:
xoroshiro128plus.h:34:17: warning: no previous prototype for ‘xoroshiro_rotl’ [-Wmissing-prototypes]
 inline uint64_t xoroshiro_rotl(const uint64_t x, int k) {
                 ^
xoroshiro128plus.h:38:17: warning: no previous prototype for ‘xoroshiro_next’ [-Wmissing-prototypes]
 inline uint64_t xoroshiro_next(uint64_t s[2]) {
                 ^
In file included from sb_thread.c:37:0:
sb_rand.h:44:17: warning: no previous prototype for ‘sb_rand_uniform_uint64’ [-Wmissing-prototypes]
 inline uint64_t sb_rand_uniform_uint64(void)
                 ^
sb_rand.h:50:15: warning: no previous prototype for ‘sb_rand_uniform_double’ [-Wmissing-prototypes]
 inline double sb_rand_uniform_double(void)
               ^
mv -f .deps/sb_histogram.Tpo .deps/sb_histogram.Po
gcc -std=gnu99 -DHAVE_CONFIG_H -I. -I../config  -I../src -I/root/sysbench-1.0.20/third_party/luajit/inc -I/root/sysbench-1.0.20/third_party/concurrency_kit/include -DDATADIR=\"/root/sysbench1020/share/sysbench\" -DLIBDIR=\"/root/sysbench1020/lib/sysbench\" -D_GNU_SOURCE  -Wall -Wextra -Wpointer-arith -Wbad-function-cast -Wstrict-prototypes -Wnested-externs -Wno-format-zero-length -Wundef -Wstrict-prototypes -Wmissing-prototypes -Wmissing-declarations -Wredundant-decls -Wcast-align -Wvla   -pthread -O2 -funroll-loops -ggdb3  -march=core-avx2 -MT sb_barrier.o -MD -MP -MF .deps/sb_barrier.Tpo -c -o sb_barrier.o sb_barrier.c
mv -f .deps/db_driver.Tpo .deps/db_driver.Po
gcc -std=gnu99 -DHAVE_CONFIG_H -I. -I../config  -I../src -I/root/sysbench-1.0.20/third_party/luajit/inc -I/root/sysbench-1.0.20/third_party/concurrency_kit/include -DDATADIR=\"/root/sysbench1020/share/sysbench\" -DLIBDIR=\"/root/sysbench1020/lib/sysbench\" -D_GNU_SOURCE  -Wall -Wextra -Wpointer-arith -Wbad-function-cast -Wstrict-prototypes -Wnested-externs -Wno-format-zero-length -Wundef -Wstrict-prototypes -Wmissing-prototypes -Wmissing-declarations -Wredundant-decls -Wcast-align -Wvla   -pthread -O2 -funroll-loops -ggdb3  -march=core-avx2 -MT sb_lua.o -MD -MP -MF .deps/sb_lua.Tpo -c -o sb_lua.o sb_lua.c
mv -f .deps/sysbench.Tpo .deps/sysbench.Po
gcc -std=gnu99 -DHAVE_CONFIG_H -I. -I../config  -I../src -I/root/sysbench-1.0.20/third_party/luajit/inc -I/root/sysbench-1.0.20/third_party/concurrency_kit/include -DDATADIR=\"/root/sysbench1020/share/sysbench\" -DLIBDIR=\"/root/sysbench1020/lib/sysbench\" -D_GNU_SOURCE  -Wall -Wextra -Wpointer-arith -Wbad-function-cast -Wstrict-prototypes -Wnested-externs -Wno-format-zero-length -Wundef -Wstrict-prototypes -Wmissing-prototypes -Wmissing-declarations -Wredundant-decls -Wcast-align -Wvla   -pthread -O2 -funroll-loops -ggdb3  -march=core-avx2 -MT sb_util.o -MD -MP -MF .deps/sb_util.Tpo -c -o sb_util.o sb_util.c
mv -f .deps/sb_thread.Tpo .deps/sb_thread.Po
gcc -std=gnu99 -DHAVE_CONFIG_H -I. -I../config  -I../src -I/root/sysbench-1.0.20/third_party/luajit/inc -I/root/sysbench-1.0.20/third_party/concurrency_kit/include -DDATADIR=\"/root/sysbench1020/share/sysbench\" -DLIBDIR=\"/root/sysbench1020/lib/sysbench\" -D_GNU_SOURCE  -Wall -Wextra -Wpointer-arith -Wbad-function-cast -Wstrict-prototypes -Wnested-externs -Wno-format-zero-length -Wundef -Wstrict-prototypes -Wmissing-prototypes -Wmissing-declarations -Wredundant-decls -Wcast-align -Wvla   -pthread -O2 -funroll-loops -ggdb3  -march=core-avx2 -MT sb_counter.o -MD -MP -MF .deps/sb_counter.Tpo -c -o sb_counter.o sb_counter.c
mv -f .deps/sb_barrier.Tpo .deps/sb_barrier.Po
In file included from sb_rand.h:24:0,
                 from sb_lua.c:37:
xoroshiro128plus.h:34:17: warning: no previous prototype for ‘xoroshiro_rotl’ [-Wmissing-prototypes]
 inline uint64_t xoroshiro_rotl(const uint64_t x, int k) {
                 ^
xoroshiro128plus.h:38:17: warning: no previous prototype for ‘xoroshiro_next’ [-Wmissing-prototypes]
 inline uint64_t xoroshiro_next(uint64_t s[2]) {
                 ^
In file included from sb_lua.c:37:0:
sb_rand.h:44:17: warning: no previous prototype for ‘sb_rand_uniform_uint64’ [-Wmissing-prototypes]
 inline uint64_t sb_rand_uniform_uint64(void)
                 ^
sb_rand.h:50:15: warning: no previous prototype for ‘sb_rand_uniform_double’ [-Wmissing-prototypes]
 inline double sb_rand_uniform_double(void)
               ^
mv -f .deps/sb_util.Tpo .deps/sb_util.Po
mv -f .deps/sb_counter.Tpo .deps/sb_counter.Po
mv -f .deps/sb_lua.Tpo .deps/sb_lua.Po
/bin/sh ../libtool  --tag=CC   --mode=link gcc -std=gnu99 -Wall -Wextra -Wpointer-arith -Wbad-function-cast -Wstrict-prototypes -Wnested-externs -Wno-format-zero-length -Wundef -Wstrict-prototypes -Wmissing-prototypes -Wmissing-declarations -Wredundant-decls -Wcast-align -Wvla   -pthread -O2 -funroll-loops -ggdb3  -march=core-avx2      -rdynamic  -o sysbench sysbench.o sb_timer.o sb_options.o sb_logger.o db_driver.o sb_histogram.o sb_rand.o sb_thread.o sb_barrier.o sb_lua.o sb_util.o sb_counter.o tests/fileio/libsbfileio.a tests/threads/libsbthreads.a tests/memory/libsbmemory.a tests/cpu/libsbcpu.a tests/mutex/libsbmutex.a drivers/mysql/libsbmysql.a -L/usr/lib64/mysql -lmysqlclient -lpthread -lz -lm -ldl -lssl -lcrypto     /root/sysbench-1.0.20/third_party/luajit/lib/libluajit-5.1.a -ldl /root/sysbench-1.0.20/third_party/concurrency_kit/lib/libck.a -lm
libtool: link: gcc -std=gnu99 -Wall -Wextra -Wpointer-arith -Wbad-function-cast -Wstrict-prototypes -Wnested-externs -Wno-format-zero-length -Wundef -Wstrict-prototypes -Wmissing-prototypes -Wmissing-declarations -Wredundant-decls -Wcast-align -Wvla -pthread -O2 -funroll-loops -ggdb3 -march=core-avx2 -rdynamic -o sysbench sysbench.o sb_timer.o sb_options.o sb_logger.o db_driver.o sb_histogram.o sb_rand.o sb_thread.o sb_barrier.o sb_lua.o sb_util.o sb_counter.o  tests/fileio/libsbfileio.a tests/threads/libsbthreads.a tests/memory/libsbmemory.a tests/cpu/libsbcpu.a tests/mutex/libsbmutex.a drivers/mysql/libsbmysql.a -L/usr/lib64/mysql -lmysqlclient -lpthread -lz -lssl -lcrypto /root/sysbench-1.0.20/third_party/luajit/lib/libluajit-5.1.a -ldl /root/sysbench-1.0.20/third_party/concurrency_kit/lib/libck.a -lm -pthread
make[2]: Leaving directory `/root/sysbench-1.0.20/src'
make[1]: Leaving directory `/root/sysbench-1.0.20/src'
Making all in tests
make[1]: Entering directory `/root/sysbench-1.0.20/tests'
make[1]: Nothing to be done for `all'.
make[1]: Leaving directory `/root/sysbench-1.0.20/tests'
make[1]: Entering directory `/root/sysbench-1.0.20'
make[1]: Nothing to be done for `all-am'.
make[1]: Leaving directory `/root/sysbench-1.0.20'
2024-07-03T15:47:13 [root@node77 /root/sysbench-1.0.20] $ echo $?
0
2024-07-03T15:48:35 [root@node77 /root/sysbench-1.0.20] $ make install
Making install in doc
make[1]: Entering directory `/root/sysbench-1.0.20/doc'
Making install in xsl
make[2]: Entering directory `/root/sysbench-1.0.20/doc/xsl'
make[3]: Entering directory `/root/sysbench-1.0.20/doc/xsl'
make[3]: Nothing to be done for `install-exec-am'.
make[3]: Nothing to be done for `install-data-am'.
make[3]: Leaving directory `/root/sysbench-1.0.20/doc/xsl'
make[2]: Leaving directory `/root/sysbench-1.0.20/doc/xsl'
make[2]: Entering directory `/root/sysbench-1.0.20/doc'
make[3]: Entering directory `/root/sysbench-1.0.20/doc'
make[3]: Nothing to be done for `install-exec-am'.
 /usr/bin/mkdir -p '/root/sysbench1020/share/doc/sysbench'
 /usr/bin/install -c -m 644 manual.html '/root/sysbench1020/share/doc/sysbench'
make[3]: Leaving directory `/root/sysbench-1.0.20/doc'
make[2]: Leaving directory `/root/sysbench-1.0.20/doc'
make[1]: Leaving directory `/root/sysbench-1.0.20/doc'
Making install in third_party/luajit
make[1]: Entering directory `/root/sysbench-1.0.20/third_party/luajit'
make[2]: Entering directory `/root/sysbench-1.0.20/third_party/luajit'
make[2]: Nothing to be done for `install-exec-am'.
make[2]: Nothing to be done for `install-data-am'.
make[2]: Leaving directory `/root/sysbench-1.0.20/third_party/luajit'
make[1]: Leaving directory `/root/sysbench-1.0.20/third_party/luajit'
Making install in third_party/concurrency_kit
make[1]: Entering directory `/root/sysbench-1.0.20/third_party/concurrency_kit'
make[2]: Entering directory `/root/sysbench-1.0.20/third_party/concurrency_kit'
make[2]: Nothing to be done for `install-exec-am'.
make[2]: Nothing to be done for `install-data-am'.
make[2]: Leaving directory `/root/sysbench-1.0.20/third_party/concurrency_kit'
make[1]: Leaving directory `/root/sysbench-1.0.20/third_party/concurrency_kit'
Making install in src
make[1]: Entering directory `/root/sysbench-1.0.20/src'
Making install in drivers
make[2]: Entering directory `/root/sysbench-1.0.20/src/drivers'
Making install in mysql
make[3]: Entering directory `/root/sysbench-1.0.20/src/drivers/mysql'
make[4]: Entering directory `/root/sysbench-1.0.20/src/drivers/mysql'
make[4]: Nothing to be done for `install-exec-am'.
make[4]: Nothing to be done for `install-data-am'.
make[4]: Leaving directory `/root/sysbench-1.0.20/src/drivers/mysql'
make[3]: Leaving directory `/root/sysbench-1.0.20/src/drivers/mysql'
make[3]: Entering directory `/root/sysbench-1.0.20/src/drivers'
make[4]: Entering directory `/root/sysbench-1.0.20/src/drivers'
make[4]: Nothing to be done for `install-exec-am'.
make[4]: Nothing to be done for `install-data-am'.
make[4]: Leaving directory `/root/sysbench-1.0.20/src/drivers'
make[3]: Leaving directory `/root/sysbench-1.0.20/src/drivers'
make[2]: Leaving directory `/root/sysbench-1.0.20/src/drivers'
Making install in tests
make[2]: Entering directory `/root/sysbench-1.0.20/src/tests'
Making install in cpu
make[3]: Entering directory `/root/sysbench-1.0.20/src/tests/cpu'
make[4]: Entering directory `/root/sysbench-1.0.20/src/tests/cpu'
make[4]: Nothing to be done for `install-exec-am'.
make[4]: Nothing to be done for `install-data-am'.
make[4]: Leaving directory `/root/sysbench-1.0.20/src/tests/cpu'
make[3]: Leaving directory `/root/sysbench-1.0.20/src/tests/cpu'
Making install in fileio
make[3]: Entering directory `/root/sysbench-1.0.20/src/tests/fileio'
make[4]: Entering directory `/root/sysbench-1.0.20/src/tests/fileio'
make[4]: Nothing to be done for `install-exec-am'.
make[4]: Nothing to be done for `install-data-am'.
make[4]: Leaving directory `/root/sysbench-1.0.20/src/tests/fileio'
make[3]: Leaving directory `/root/sysbench-1.0.20/src/tests/fileio'
Making install in memory
make[3]: Entering directory `/root/sysbench-1.0.20/src/tests/memory'
make[4]: Entering directory `/root/sysbench-1.0.20/src/tests/memory'
make[4]: Nothing to be done for `install-exec-am'.
make[4]: Nothing to be done for `install-data-am'.
make[4]: Leaving directory `/root/sysbench-1.0.20/src/tests/memory'
make[3]: Leaving directory `/root/sysbench-1.0.20/src/tests/memory'
Making install in threads
make[3]: Entering directory `/root/sysbench-1.0.20/src/tests/threads'
make[4]: Entering directory `/root/sysbench-1.0.20/src/tests/threads'
make[4]: Nothing to be done for `install-exec-am'.
make[4]: Nothing to be done for `install-data-am'.
make[4]: Leaving directory `/root/sysbench-1.0.20/src/tests/threads'
make[3]: Leaving directory `/root/sysbench-1.0.20/src/tests/threads'
Making install in mutex
make[3]: Entering directory `/root/sysbench-1.0.20/src/tests/mutex'
make[4]: Entering directory `/root/sysbench-1.0.20/src/tests/mutex'
make[4]: Nothing to be done for `install-exec-am'.
make[4]: Nothing to be done for `install-data-am'.
make[4]: Leaving directory `/root/sysbench-1.0.20/src/tests/mutex'
make[3]: Leaving directory `/root/sysbench-1.0.20/src/tests/mutex'
make[3]: Entering directory `/root/sysbench-1.0.20/src/tests'
make[4]: Entering directory `/root/sysbench-1.0.20/src/tests'
make[4]: Nothing to be done for `install-exec-am'.
make[4]: Nothing to be done for `install-data-am'.
make[4]: Leaving directory `/root/sysbench-1.0.20/src/tests'
make[3]: Leaving directory `/root/sysbench-1.0.20/src/tests'
make[2]: Leaving directory `/root/sysbench-1.0.20/src/tests'
Making install in lua
make[2]: Entering directory `/root/sysbench-1.0.20/src/lua'
Making install in internal
make[3]: Entering directory `/root/sysbench-1.0.20/src/lua/internal'
make  install-am
make[4]: Entering directory `/root/sysbench-1.0.20/src/lua/internal'
make[5]: Entering directory `/root/sysbench-1.0.20/src/lua/internal'
make[5]: Nothing to be done for `install-exec-am'.
make[5]: Nothing to be done for `install-data-am'.
make[5]: Leaving directory `/root/sysbench-1.0.20/src/lua/internal'
make[4]: Leaving directory `/root/sysbench-1.0.20/src/lua/internal'
make[3]: Leaving directory `/root/sysbench-1.0.20/src/lua/internal'
make[3]: Entering directory `/root/sysbench-1.0.20/src/lua'
make[4]: Entering directory `/root/sysbench-1.0.20/src/lua'
make[4]: Nothing to be done for `install-exec-am'.
 /usr/bin/mkdir -p '/root/sysbench1020/share/sysbench'
 /usr/bin/install -c -m 644 oltp_common.lua '/root/sysbench1020/share/sysbench'
 /usr/bin/mkdir -p '/root/sysbench1020/share/sysbench'
 /usr/bin/install -c bulk_insert.lua oltp_delete.lua oltp_insert.lua oltp_read_only.lua oltp_read_write.lua oltp_point_select.lua oltp_update_index.lua oltp_update_non_index.lua oltp_write_only.lua select_random_points.lua select_random_ranges.lua '/root/sysbench1020/share/sysbench'
make[4]: Leaving directory `/root/sysbench-1.0.20/src/lua'
make[3]: Leaving directory `/root/sysbench-1.0.20/src/lua'
make[2]: Leaving directory `/root/sysbench-1.0.20/src/lua'
Making install in .
make[2]: Entering directory `/root/sysbench-1.0.20/src'
make[3]: Entering directory `/root/sysbench-1.0.20/src'
 /usr/bin/mkdir -p '/root/sysbench1020/bin'
  /bin/sh ../libtool   --mode=install /usr/bin/install -c sysbench '/root/sysbench1020/bin'
libtool: install: /usr/bin/install -c sysbench /root/sysbench1020/bin/sysbench
make[3]: Nothing to be done for `install-data-am'.
make[3]: Leaving directory `/root/sysbench-1.0.20/src'
make[2]: Leaving directory `/root/sysbench-1.0.20/src'
make[1]: Leaving directory `/root/sysbench-1.0.20/src'
Making install in tests
make[1]: Entering directory `/root/sysbench-1.0.20/tests'
make[2]: Entering directory `/root/sysbench-1.0.20/tests'
make[2]: Nothing to be done for `install-exec-am'.
make	INSTALL_TO_DIR="/root/sysbench1020/share/sysbench/tests" install_test_files
make[3]: Entering directory `/root/sysbench-1.0.20/tests'
make[3]: Leaving directory `/root/sysbench-1.0.20/tests'
 /usr/bin/mkdir -p '/root/sysbench1020/share/sysbench/tests'
 /usr/bin/install -c test_run.sh '/root/sysbench1020/share/sysbench/tests'
make[2]: Leaving directory `/root/sysbench-1.0.20/tests'
make[1]: Leaving directory `/root/sysbench-1.0.20/tests'
make[1]: Entering directory `/root/sysbench-1.0.20'
make[2]: Entering directory `/root/sysbench-1.0.20'
make[2]: Nothing to be done for `install-exec-am'.
make[2]: Nothing to be done for `install-data-am'.
make[2]: Leaving directory `/root/sysbench-1.0.20'
make[1]: Leaving directory `/root/sysbench-1.0.20'


```

`./configure --prefix=/root/sysbench1020`配置后确保：`MySQL support      : yes`


安装后：
```bash
2024-07-03T15:52:33 [root@node77 /root/sysbench1020] $ tree
.
├── bin
│   └── sysbench
└── share
    ├── doc
    │   └── sysbench
    │       └── manual.html
    └── sysbench
        ├── bulk_insert.lua
        ├── oltp_common.lua
        ├── oltp_delete.lua
        ├── oltp_insert.lua
        ├── oltp_point_select.lua
        ├── oltp_read_only.lua
        ├── oltp_read_write.lua
        ├── oltp_update_index.lua
        ├── oltp_update_non_index.lua
        ├── oltp_write_only.lua
        ├── select_random_points.lua
        ├── select_random_ranges.lua
        └── tests
            ├── include
            │   ├── api_sql_common.sh
            │   ├── config.sh
            │   ├── drv_common.sh
            │   ├── inspect.lua
            │   ├── mysql_common.sh
            │   ├── oltp_legacy
            │   │   ├── bulk_insert.lua
            │   │   ├── common.lua
            │   │   ├── delete.lua
            │   │   ├── insert.lua
            │   │   ├── oltp.lua
            │   │   ├── oltp_simple.lua
            │   │   ├── parallel_prepare.lua
            │   │   ├── select.lua
            │   │   ├── select_random_points.lua
            │   │   ├── select_random_ranges.lua
            │   │   ├── update_index.lua
            │   │   └── update_non_index.lua
            │   ├── pgsql_common.sh
            │   ├── script_bulk_insert_common.sh
            │   ├── script_oltp_common.sh
            │   ├── script_oltp_legacy_common.sh
            │   ├── script_select_random_common.sh
            │   └── script_select_random_legacy_common.sh
            ├── t
            │   ├── 1st.t
            │   ├── api_basic.t
            │   ├── api_histogram.t
            │   ├── api_legacy_basic.t
            │   ├── api_legacy_rand.t
            │   ├── api_legacy_sql.t
            │   ├── api_rand.t
            │   ├── api_reports.t
            │   ├── api_sql_mysql.t
            │   ├── api_sql_pgsql.t
            │   ├── cmd_cleanup.t
            │   ├── cmd_help.t
            │   ├── cmdline.t
            │   ├── cmd_prepare.t
            │   ├── cmd_run.t
            │   ├── commands.t
            │   ├── drivers.t
            │   ├── drv_mysql.t
            │   ├── drv_pgsql.t
            │   ├── help_drv_mysql.t
            │   ├── help_drv_pgsql.t
            │   ├── opt_help.t
            │   ├── opt_histogram.t
            │   ├── opt_rate.t
            │   ├── opt_report_checkpoints.t
            │   ├── opt_report_interval.t
            │   ├── opt_version.t
            │   ├── script_bulk_insert_mysql.t
            │   ├── script_bulk_insert_pgsql.t
            │   ├── script_oltp_delete_mysql.t
            │   ├── script_oltp_delete_pgsql.t
            │   ├── script_oltp_help.t
            │   ├── script_oltp_insert_mysql.t
            │   ├── script_oltp_insert_pgsql.t
            │   ├── script_oltp_point_select_mysql.t
            │   ├── script_oltp_point_select_pgsql.t
            │   ├── script_oltp_read_write_mysql.t
            │   ├── script_oltp_read_write_pgsql.t
            │   ├── script_select_random_mysql.t
            │   ├── script_select_random_pgsql.t
            │   ├── test_cpu.t
            │   ├── test_fileio.t
            │   ├── test_memory.t
            │   ├── test_mutex.t
            │   ├── tests.t
            │   └── test_threads.t
            └── test_run.sh

9 directories, 84 files
2024-07-03T15:52:44 [root@node77 /root/sysbench1020] $

```



## 二、选项

- lua脚本

| 选项                        | 功能               | 示例                  |
| :-------------------------- | :----------------- | :-------------------- |
| `oltp_insert.lua`           | 简单插入测试       | run                   |
| `bulk_insert.lua`           | 批量插入测试       | run                   |
| `oltp_delete.lua`           | 删除测试           | run                   |
| `oltp_read_only.lua`        | 只读测试           | run                   |
| `oltp_write_only.lua`       | 只写测试           | run                   |
| `oltp_read_write.lua`       | 读写测试           | prepare, run, cleanup |
| `oltp_update_index.lua`     | 带索引的更新测试   | run                   |
| `oltp_update_non_index.lua` | 不带索引的更新测试 | run                   |
| `oltp_point_select.lua`     | 等值查询测试       | run                   |
| `select_random_points.lua`  | 随机等值查询测试   | run                   |
| `select_random_ranges.lua`  | 随机范围查询测试   | run                   |
| `oltp_common.lua`           |                    |                       |


- mysql选项

| 选项                | 功能         | 示例                      |
| :------------------ | :----------- | :------------------------ |
| `--mysql-host`      | `IP`         | run                       |
| `--mysql-port`      | 端口         | run                       |
| `--mysql-user`      | 账号         | run                       |
| `--mysql-password`  | 密码         | run                       |
| `--db-driver`       | 驱动         | `mysql/postgresql/oracle` |
| `--mysql-db`        | 数据库       | `sbtest`                  |
| `--threads`         | 线程个数     | run                       |
| `--time`            | 总执行时间   | 0表示无限制               |
| `--report-interval` | 等值查询测试 | run                       |
| `--tables`          | 表个数       | run                       |
| `--table_size`      | 表行数       | run                       |
| `oltp_common.lua`   |              |                           |

```bash
2024-05-14T16:18:39 [root@node71 /data] $ ll  /usr/share/sysbench/
total 64
-rwxr-xr-x 1 root root  1452 Mar 16  2019 bulk_insert.lua
-rw-r--r-- 1 root root 14369 Mar 16  2019 oltp_common.lua
-rwxr-xr-x 1 root root  1290 Mar 16  2019 oltp_delete.lua
-rwxr-xr-x 1 root root  2415 Mar 16  2019 oltp_insert.lua
-rwxr-xr-x 1 root root  1265 Mar 16  2019 oltp_point_select.lua
-rwxr-xr-x 1 root root  1649 Mar 16  2019 oltp_read_only.lua
-rwxr-xr-x 1 root root  1824 Mar 16  2019 oltp_read_write.lua
-rwxr-xr-x 1 root root  1118 Mar 16  2019 oltp_update_index.lua
-rwxr-xr-x 1 root root  1127 Mar 16  2019 oltp_update_non_index.lua
-rwxr-xr-x 1 root root  1440 Mar 16  2019 oltp_write_only.lua
-rwxr-xr-x 1 root root  1919 Mar 16  2019 select_random_points.lua
-rwxr-xr-x 1 root root  2118 Mar 16  2019 select_random_ranges.lua
drwxr-xr-x 4 root root  4096 Apr  6 08:47 tests

```

#### 帮助

```bash
2024-07-03T15:58:02 [root@node77 /root/sysbench1020] $ ./bin/sysbench --help

2024-07-03T15:58:18 [root@node77 /root/sysbench1020] $ ./bin/sysbench share/sysbench/oltp_read_only.lua help


```

## 三、示例



#### 1. 建户建库


```sql
[root@127.0.0.1:8032 16:02:09((none))]$ create user sbtest@'%' identified by 'sbtest';
Query OK, 0 rows affected (0.78 sec)

[root@127.0.0.1:8032 16:02:45((none))]$ grant all on *.* to sbtest@'%' with grant option;
Query OK, 0 rows affected (0.19 sec)

[root@127.0.0.1:8032 16:03:33((none))]$ create database sbtest;
Query OK, 1 row affected (0.10 sec)

[root@127.0.0.1:8032 16:03:39((none))]$

```

#### 2. 准备数据


```bash
sysbench oltp_read_only --mysql-host=127.0.0.1 --mysql-port=8032 --mysql-user=sbtest --mysql-password=sbtest --mysql-db=sbtest --db-driver=mysql --tables=10 --table_size=100000 --threads=4 prepare


```

#### 3. 预热数据

```bash
sysbench oltp_read_only --mysql-host=127.0.0.1 --mysql-port=8032 --mysql-user=sbtest --mysql-password=sbtest --mysql-db=sbtest --db-driver=mysql --tables=10 --table_size=100000 --threads=4 warmup


```

#### 4. 运行
> 8并发运行10分钟

```bash
sysbench oltp_read_only --mysql-host=127.0.0.1 --mysql-port=8032 --mysql-user=sbtest --mysql-password=sbtest --mysql-db=sbtest --db-driver=mysql --tables=10 --table_size=100000 --threads=8 --time 600 run


```

#### 4. 清理
> 需要设置表个数，否则只会删除第一张表

```bash
sysbench oltp_read_only --mysql-host=127.0.0.1 --mysql-port=8032 --mysql-user=sbtest --mysql-password=sbtest --mysql-db=sbtest --db-driver=mysql --tables=10 cleanup


```


## 四、自定义脚本







```bash
sysbench --db-driver=mysql \n
--mysql-host=${mysql_host} \n
--mysql-port=${mysql_port} \n
--mysql-user=${mysql_user} \n
--mysql-password=${mysql_password} \n

```


```bash
sysbench oltp_read_write --mysql-host=10.186.63.54 --mysql-port=3306 --mysql-user=root --mysql-password=123456 --mysql-db=sysbench --tables=6 --table-size=1000000 --report-interval=1 --threads=1 --time=300 run

sysbench /usr/share/sysbench/oltp_insert.lua --mysql-user=msandbox --mysql-password=msandbox --mysql-socket=/tmp/mysql_sandbox22827.sock --mysql-db=sbtest --threads=10 --time=1500 --report-interval=1 --events=0 --db-driver=mysql run

sysbench oltp_insert --mysql-host=xxxx --mysql-db=sbtest --mysql-user=xxx --mysql-password='xxxx' --mysql-ignore-errors=all --threads=1 --time=3600 --report-interval=10 run

```


## 五、脚本

```bash
2024-05-14T19:27:48 [root@node71 /root] $ cat sysbench83.sh
#!/bin/bash
# DATE 2022-07-21
# VERSION 1.0
# DESC sysbench tool benchmark
# 测试参数的默认值
# 并发线程128
# 表的个数128
# 表的行数500万
# 注意事项：
# sysbenc连接到数据库的账户的认证方式需采用本地验证！！！
# 防止生产大量的日志文件，建议关闭慢查询日志

# 定义变量
mysql_host="172.17.135.83"
mysql_port="16310"
mysql_user="admin"
mysql_password="123456"
mysql_db="apple"
time=300000
report_interval=5

function sysbench_check() {

	if command -v sysbench; then
		echo "" >/dev/null
	else
		wget https://repo.percona.com/yum/release/7/RPMS/x86_64/sysbench-1.0.20-6.el7.x86_64.rpm
		yum localinstall sysbench-1.0.20-6.el7.x86_64.rpm -y
		command -v sysbench || {
			echo "sysbench 安装失败"
			exit 1
		}
	fi
}

sysbench_check

function input_paras() {
	read -p "请输入并发线程数量，默认值为128个 " threads
	read -p "请输入需要测试的表数量，默认值为128个 " tables
	read -p "请输入需要测试的表行数，默认值为500万行 " table_size
}

while true; do
	echo -e "\t\t\t\t\tsysbench tool stress test menu"
	echo -e "\t\t\t\t1 准备数据"
	echo -e "\t\t\t\t2 综合读写TPS"
	echo -e "\t\t\t\t3 只读性能"
	echo -e "\t\t\t\t4 写入性能"
	echo -e "\t\t\t\t5 删除性能"
	echo -e "\t\t\t\t6 更新索引字段性能"
	echo -e "\t\t\t\t7 更新非索引字段性能"
	echo -e "\t\t\t\t8 清除数据"
	echo -e "\t\t\t\t9 退出"
	read -p "请输入测试项目数字 " num0
	case $num0 in
	1)
		mysql -u${mysql_user} -p${mysql_password} -h${mysql_host} -P${mysql_port} <<eof
create database if not exists sbtest;
set global slow_query_log=off;
exit
eof
		input_paras
		sysbench --db-driver=mysql --mysql-host=${mysql_host} --mysql-port=${mysql_port} --mysql-user=${mysql_user} --mysql-password=${mysql_password} --mysql-db=${mysql_db} --db-ps-mode=disable --threads=${threads:=128} --time=${time} --report-interval=${report_interval} --tables=${tables:=128} --table_size=${table_size:=5000000} oltp_read_write --mysql-ignore-errors=all prepare
		;;
	2)
		input_paras
		sysbench --db-driver=mysql --mysql-host=${mysql_host} --mysql-port=${mysql_port} --mysql-user=${mysql_user} --mysql-password=${mysql_password} --mysql-db=${mysql_db} --db-ps-mode=disable --threads=${threads:=128} --time=${time} --report-interval=${report_interval} --tables=${tables:=128} --table_size=${table_size:=5000000} oltp_read_write --mysql-ignore-errors=all run
		;;
	3)
		input_paras
		sysbench --db-driver=mysql --mysql-host=${mysql_host} --mysql-port=${mysql_port} --mysql-user=${mysql_user} --mysql-password=${mysql_password} --mysql-db=${mysql_db} --db-ps-mode=disable --threads=${threads:=128} --time=${time} --report-interval=${report_interval} --tables=${tables:=128} --table_size=${table_size:=5000000} oltp_read_only --mysql-ignore-errors=all run
		;;
	4)
		input_paras
		sysbench --db-driver=mysql --mysql-host=${mysql_host} --mysql-port=${mysql_port} --mysql-user=${mysql_user} --mysql-password=${mysql_password} --mysql-db=${mysql_db} --db-ps-mode=disable --threads=${threads:=128} --time=${time} --report-interval=${report_interval} --tables=${tables:=128} --table_size=${table_size:=5000000} oltp_write_only --mysql-ignore-errors=all run
		;;
	5)
		input_paras
		sysbench --db-driver=mysql --mysql-host=${mysql_host} --mysql-port=${mysql_port} --mysql-user=${mysql_user} --mysql-password=${mysql_password} --mysql-db=${mysql_db} --db-ps-mode=disable --threads=${threads:=128} --time=${time} --report-interval=${report_interval} --tables=${tables:=128} --table_size=${table_size:=5000000} oltp_delete --mysql-ignore-errors=all run
		;;
	6)
		input_paras
		sysbench --db-driver=mysql --mysql-host=${mysql_host} --mysql-port=${mysql_port} --mysql-user=${mysql_user} --mysql-password=${mysql_password} --mysql-db=${mysql_db} --db-ps-mode=disable --threads=${threads:=128} --time=${time} --report-interval=${report_interval} --tables=${tables:=128} --table_size=${table_size:=5000000} oltp_update_index --mysql-ignore-errors=all run
		;;
	7)
		input_paras
		sysbench --db-driver=mysql --mysql-host=${mysql_host} --mysql-port=${mysql_port} --mysql-user=${mysql_user} --mysql-password=${mysql_password} --mysql-db=${mysql_db} --db-ps-mode=disable --threads=${threads:=128} --time=${time} --report-interval=${report_interval} --tables=${tables:=128} --table_size=${table_size:=5000000} oltp_update_non_index --mysql-ignore-errors=all run
		;;
	8)
		sysbench --db-driver=mysql --mysql-host=${mysql_host} --mysql-port=${mysql_port} --mysql-user=${mysql_user} --mysql-password=${mysql_password} --mysql-db=${mysql_db} --db-ps-mode=disable --threads=${threads:=128} --time=${time} --report-interval=${report_interval} --tables=${tables:=128} --table_size=${table_size:=5000000} oltp_read_write cleanup
		;;
	9)
		exit 0
		;;
	*)
		continue
		;;
	esac
done

```








#### 插入数据

```bash
sysbench /usr/share/sysbench/oltp_insert.lua --db-driver=mysql --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-user=root --mysql-password='123456' --mysql-db=db1 --percentile=99 --table-size=2000 --tables=5 --threads=1000 prepare
```









