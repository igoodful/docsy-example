---
title: 10. debug
description: bashdb bash -x -v
date: 2024-09-06
weight: 1000
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

- `bashdb`
- `bash -x -v`

## 1. bash -x -v

生成详细执行过程

```bash

bash -x -v  ../configure --prefix=/glc/glibc/app/glibc-2.28 > configure.log 2>&1



```

- 行首显示+，+后面显示经过替换之后的命令行内容，有助于分析实际执行的是什么命令。++代表函数或脚本进一步的调用


## 2. vscode

拷贝这两个文件到vscode中进行详细跟踪排查

```bash

cp configure configure.log



```



## 3. 案例



### 3.1 glibc


#### 3.1.1 ansible执行报错

```yaml
TASK [glibc : cd /glc/glibc/source/glibc-2.28/build_20250326T093604;./configure --prefix=/glc/glibc/app/glibc-2.28] *************************************************************************************************************************************************************************
fatal: [node1]: FAILED! => changed=true
  cmd: ../configure --prefix=/glc/glibc/app/glibc-2.28
  delta: '0:00:02.397663'
  end: '2025-03-26 09:40:58.901773'
  msg: non-zero return code
  rc: 1
  start: '2025-03-26 09:40:56.504110'
  stderr: |-
    configure: error:
    *** These critical programs are missing or too old: make
    *** Check the INSTALL file for required versions.
  stderr_lines: <omitted>
  stdout: |-
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
    checking for sysdeps preconfigure fragments... aarch64 alpha arm hppa i386 m68k microblaze mips nios2 powerpc riscv s390 sh sparc x86_64 checking whether gcc compiles in -mx32 mode by default... no

    checking for use of fpu sysdeps directories... yes
    checking for -fstack-protector... yes
    checking for -fstack-protector-strong... yes
    checking for -fstack-protector-all... yes
    checking for assembler and linker STT_GNU_IFUNC support... yes
    checking for gcc attribute ifunc support... yes
    checking if compiler warns about alias for function with incompatible types... yes
    checking sysdep dirs... sysdeps/unix/sysv/linux/x86_64/64 sysdeps/unix/sysv/linux/x86_64 sysdeps/unix/sysv/linux/x86 sysdeps/x86/nptl sysdeps/unix/sysv/linux/wordsize-64 sysdeps/x86_64/nptl sysdeps/unix/sysv/linux sysdeps/nptl sysdeps/pthread sysdeps/gnu sysdeps/unix/inet sysdeps/unix/sysv sysdeps/unix/x86_64 sysdeps/unix sysdeps/posix sysdeps/x86_64/64 sysdeps/x86_64/fpu/multiarch sysdeps/x86_64/fpu sysdeps/x86/fpu sysdeps/x86_64/multiarch sysdeps/x86_64 sysdeps/x86 sysdeps/ieee754/float128 sysdeps/ieee754/ldbl-96 sysdeps/ieee754/dbl-64/wordsize-64 sysdeps/ieee754/dbl-64 sysdeps/ieee754/flt-32 sysdeps/wordsize-64 sysdeps/ieee754 sysdeps/generic
    checking for a BSD-compatible install... /usr/bin/install -c
    checking whether ln -s works... yes
    checking whether as is GNU as... yes
    checking whether ld is GNU ld... yes
    checking for as... as
    checking version of as... 2.31.1, ok
    checking for ld... ld
    checking version of ld... 2.31.1, ok
    checking for gnumake... no
    checking for gmake... gmake
    checking version of gmake... 3.82, bad
    checking for gnumsgfmt... no
    checking for gmsgfmt... no
    checking for msgfmt... msgfmt
    checking version of msgfmt... 0.19.8.1, ok
    checking for makeinfo... makeinfo
    checking version of makeinfo... 6.5, ok
    checking for sed... sed
    checking version of sed... 4.2.2, ok
    checking for gawk... gawk
    checking version of gawk... 5.3.1, ok
    checking for bison... bison
    checking version of bison... 3.0.4, ok
    checking if gcc is sufficient to build libc... yes
    checking for nm... nm
    checking for python3... python3
  stdout_lines: <omitted>

```


#### 3.1.2 手动调试脚本configure

```bash
 bash -x -v  ../configure --prefix=/glc/glibc/app/glibc-2.28 > configure.log 2>&1

```



#### 3.1.3 vscode分析configure和configure.log


- configure.log

```bash
+ test -z /bin
+ for ac_exec_ext in ''\'''\''' '$ac_executable_extensions'
+ as_fn_executable_p /bin/gmake
+ test -f /bin/gmake
+ test -x /bin/gmake
+ ac_cv_prog_MAKE=gmake
+ printf '%s\n' 'configure:5203: found /bin/gmake'
+ break 2
+ IFS='
'
+ MAKE=gmake
+ test -n gmake
+ printf '%s\n' 'configure:5215: result: gmake'
+ printf '%s\n' gmake
gmake
+ test -n gmake
+ break

if test -z "$MAKE"; then
	ac_verc_fail=yes
else
	# Found it, now check the version.
	{
		$as_echo "$as_me:${as_lineno-$LINENO}: checking version of $MAKE" >&5
		$as_echo_n "checking version of $MAKE... " >&6
	}
	ac_prog_version=$($MAKE --version 2>&1 | sed -n 's/^.*GNU Make[^0-9]*\([0-9][0-9.]*\).*$/\1/p')
	case $ac_prog_version in
	'')
		ac_prog_version="v. ?.??, bad"
		ac_verc_fail=yes
		;;
	[4-9].* | [1-9][0-9]*)
		ac_prog_version="$ac_prog_version, ok"
		ac_verc_fail=no
		;;
	*)
		ac_prog_version="$ac_prog_version, bad"
		ac_verc_fail=yes
		;;

	esac
	{
		$as_echo "$as_me:${as_lineno-$LINENO}: result: $ac_prog_version" >&5
		$as_echo "$ac_prog_version" >&6
	}
fi
+ test -z gmake
+ printf '%s\n' 'configure:5233: checking version of gmake'
+ printf %s 'checking version of gmake... '
checking version of gmake... ++ gmake --version
++ sed -n 's/^.*GNU Make[^0-9]*\([0-9][0-9.]*\).*$/\1/p'
+ ac_prog_version=3.82
+ case $ac_prog_version in
+ ac_prog_version='3.82, bad'
+ ac_verc_fail=yes
+ printf '%s\n' 'configure:5253: result: 3.82, bad'
+ printf '%s\n' '3.82, bad'
3.82, bad
if test $ac_verc_fail = yes; then
	critic_missing="$critic_missing make"
fi
+ test yes = yes
+ critic_missing=' make'



。。。。。。。。。。。。。。


test -n "$critic_missing" && as_fn_error $? "
*** These critical programs are missing or too old:$critic_missing
*** Check the INSTALL file for required versions." "$LINENO" 5
+ test -n ' make'
+ as_fn_error 0 '
*** These critical programs are missing or too old: make
*** Check the INSTALL file for required versions.' 5962 5
+ as_status=0
+ test 0 -eq 0
+ as_status=1
+ test 5
+ as_lineno=5962
+ as_lineno_stack=as_lineno_stack=
+ printf '%s\n' 'configure:5962: error:
*** These critical programs are missing or too old: make
*** Check the INSTALL file for required versions.'
+ printf '%s\n' 'configure: error:
*** These critical programs are missing or too old: make
*** Check the INSTALL file for required versions.'
configure: error:
*** These critical programs are missing or too old: make
*** Check the INSTALL file for required versions.
+ as_fn_exit 1
+ set +e
+ as_fn_set_status 1


```

- `critic_missing=' make'` 这里是关键，说缺少了make
- 为什么缺少了呢？因为脚本去/usr/bin和/usr/local/bin找gmake执行



#### 3.1.4 核对机器上的gmake


```bash
2025-03-26T12:38:45 [root@RDS-146 /root] # make --version
GNU Make 3.82
Built for x86_64-redhat-linux-gnu
Copyright (C) 2010  Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
2025-03-26T12:38:50 [root@RDS-146 /root] # which make
/usr/bin/make
2025-03-26T12:38:59 [root@RDS-146 /root] # ll /usr/bin/ |grep make
-rwxr-xr-x. 2 root root      252350 2014-06-10T16:03:11 automake
-rwxr-xr-x. 2 root root      252350 2014-06-10T16:03:11 automake-1.13
-rwxr-xr-x. 1 root root        2911 2018-10-31T03:36:26 eu-make-debug-archive
lrwxrwxrwx. 1 root root           4 2022-09-19T11:22:59 gmake -> make
-rwxr-xr-x. 1 root root      182752 2019-08-09T08:46:49 make
-rwxr-xr-x. 1 root root       19072 2018-10-30T16:20:27 makedb
-rwxr-xr-x. 1 root root       70032 2014-06-10T06:59:43 makedeltarpm
-rwxr-xr-x. 1 root root       11392 2018-04-11T11:58:18 pwmake


```

- /usr/bin/make和/usr/bin/gmake的版本确实是小于4.0
- 我自己编译安装的gmake和make放在/glc/make/app/make-4.2.1/bin/make,且添加了环境变量PATH，但是glibc的configure脚本不检查PATH中的路径的优先级，所以导致检测到了旧版本gmake

#### 3.1.5 解决问题


- 方案一：备份旧gmake和软链接新gmake

```bash
2025-03-26T12:39:10 [root@RDS-146 /root] # mv /usr/bin/make /usr/bin/make.3.82

2025-03-26T12:40:06 [root@RDS-146 /root] # mv /usr/bin/gmake /usr/bin/gmake.3.82

2025-03-26T12:41:09 [root@RDS-146 /root] # ln -s  /glc/make/app/make-4.2.1/bin/make /usr/bin/gmake
2025-03-26T12:41:42 [root@RDS-146 /root] # ln -s  /glc/make/app/make-4.2.1/bin/make /usr/bin/make

```

- 方案二：将configure脚本中MAKE变量配置为新安装的make


