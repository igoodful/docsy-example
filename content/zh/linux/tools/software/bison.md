---
title: bison
description: bison
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
- https://ftp.gnu.org/gnu/bison/bison-3.8.2.tar.gz
- 一定要安装最新版本

{{</note>}}


## 1. 依赖



## 2. 内容


```viz-dot
digraph "bison-package" {
node [
        fontsize = "12"
];
    "bison-package" [ label = "bison-package", shape = octagon ];
    "bison" [ label = "bison", shape = octagon ];
    "yacc" [ label = "yacc", shape = octagon ];
    "liby.a" [ label = "liby.a", shape = octagon ];

    "bison-package" -> "bison";
    "bison-package" -> "yacc";
    "bison-package" -> "liby.a";

}
```




## 3. 安装


#### 3.1 yum安装
```bash
yum -y install bison

```

```bash
2025-03-23T19:15:54 [root@x86centos7-101 /lib64] # which bison
/usr/bin/bison
2025-03-23T19:52:00 [root@x86centos7-101 /lib64] # rpm -qf /usr/bin/bison
bison-3.0.4-2.el7.x86_64
2025-03-23T19:52:13 [root@x86centos7-101 /lib64] # rpm -qf /usr/bin/yacc
byacc-1.9.20130304-3.el7.x86_64
2025-03-23T19:52:19 [root@x86centos7-101 /lib64] # rpm -ql byacc-1.9.20130304-3.el7.x86_64
/usr/bin/byacc
/usr/bin/yacc
/usr/share/doc/byacc-1.9.20130304
/usr/share/doc/byacc-1.9.20130304/ACKNOWLEDGEMENTS
/usr/share/doc/byacc-1.9.20130304/CHANGES
/usr/share/doc/byacc-1.9.20130304/NEW_FEATURES
/usr/share/doc/byacc-1.9.20130304/NOTES
/usr/share/doc/byacc-1.9.20130304/NO_WARRANTY
/usr/share/doc/byacc-1.9.20130304/README
/usr/share/man/man1/byacc.1.gz
/usr/share/man/man1/yacc.1.gz
2025-03-23T19:52:45 [root@x86centos7-101 /lib64] # rpm -ql bison-3.0.4-2.el7.x86_64
/usr/bin/bison
/usr/share/aclocal/bison-i18n.m4
/usr/share/bison
/usr/share/bison/README
/usr/share/bison/bison.m4
/usr/share/bison/c++-skel.m4
/usr/share/bison/c++.m4
/usr/share/bison/c-like.m4
/usr/share/bison/c-skel.m4
/usr/share/bison/c.m4
/usr/share/bison/glr.c
/usr/share/bison/glr.cc
/usr/share/bison/java-skel.m4
/usr/share/bison/java.m4
/usr/share/bison/lalr1.cc
/usr/share/bison/lalr1.java
/usr/share/bison/location.cc
/usr/share/bison/m4sugar
/usr/share/bison/m4sugar/foreach.m4
/usr/share/bison/m4sugar/m4sugar.m4
/usr/share/bison/stack.hh
/usr/share/bison/variant.hh
/usr/share/bison/xslt
/usr/share/bison/xslt/bison.xsl
/usr/share/bison/xslt/xml2dot.xsl
/usr/share/bison/xslt/xml2text.xsl
/usr/share/bison/xslt/xml2xhtml.xsl
/usr/share/bison/yacc.c
/usr/share/doc/bison-3.0.4
/usr/share/doc/bison-3.0.4/AUTHORS
/usr/share/doc/bison-3.0.4/COPYING
/usr/share/doc/bison-3.0.4/ChangeLog
/usr/share/doc/bison-3.0.4/NEWS
/usr/share/doc/bison-3.0.4/README
/usr/share/doc/bison-3.0.4/THANKS
/usr/share/doc/bison-3.0.4/TODO
/usr/share/info/bison.info.gz
/usr/share/locale/da/LC_MESSAGES/bison.mo
/usr/share/locale/de/LC_MESSAGES/bison.mo
/usr/share/locale/el/LC_MESSAGES/bison.mo
/usr/share/locale/eo/LC_MESSAGES/bison.mo
/usr/share/locale/es/LC_MESSAGES/bison.mo
/usr/share/locale/et/LC_MESSAGES/bison.mo
/usr/share/locale/fi/LC_MESSAGES/bison.mo
/usr/share/locale/fr/LC_MESSAGES/bison.mo
/usr/share/locale/ga/LC_MESSAGES/bison.mo
/usr/share/locale/hr/LC_MESSAGES/bison.mo
/usr/share/locale/id/LC_MESSAGES/bison.mo
/usr/share/locale/it/LC_MESSAGES/bison.mo
/usr/share/locale/ja/LC_MESSAGES/bison.mo
/usr/share/locale/ms/LC_MESSAGES/bison.mo
/usr/share/locale/nb/LC_MESSAGES/bison.mo
/usr/share/locale/nl/LC_MESSAGES/bison.mo
/usr/share/locale/pl/LC_MESSAGES/bison.mo
/usr/share/locale/pt/LC_MESSAGES/bison.mo
/usr/share/locale/pt_BR/LC_MESSAGES/bison.mo
/usr/share/locale/ro/LC_MESSAGES/bison.mo
/usr/share/locale/ru/LC_MESSAGES/bison.mo
/usr/share/locale/sr/LC_MESSAGES/bison.mo
/usr/share/locale/sv/LC_MESSAGES/bison.mo
/usr/share/locale/tr/LC_MESSAGES/bison.mo
/usr/share/locale/uk/LC_MESSAGES/bison.mo
/usr/share/locale/vi/LC_MESSAGES/bison.mo
/usr/share/locale/zh_CN/LC_MESSAGES/bison.mo
/usr/share/locale/zh_TW/LC_MESSAGES/bison.mo
/usr/share/man/man1/bison.1.gz

```



#### 3.2 源码安装


```bash
wget https://ftp.gnu.org/gnu/bison/bison-3.8.2.tar.gz
tar -xzvf bison-3.8.2.tar.gz && cd bison-3.8.2
./configure --prefix=$HOME/local/ && make -j4 && make install

```

```bash
[root@k8s-node-76 local]# tree bison-3.8.2
bison-3.8
├── bin
│   ├── bison
│   └── yacc
├── lib
│   └── liby.a
└── share
```





## 问题


#### 4.1 Please port gnulib fseterr.c to your platform! Look at the definitions of ferror and clearerr on your system, then report this to bug-gnulib

```bash
ASK [bison : cd /glc/bison/source/bison-3.0.4;make -j4] ************************************************************************************************************************************************************************************************************************************************
fatal: [node2]: FAILED! => changed=true
  cmd: make -j4
  delta: '0:00:02.882246'
  end: '2025-03-28 11:31:22.894792'
  msg: non-zero return code
  rc: 2
  start: '2025-03-28 11:31:20.012546'
  stderr: |-
    extexi:  GEN examples/rpcalc/rpcalc.y
    extexi:  GEN examples/mfcalc/mfcalc.y
    extexi:  GEN examples/mfcalc/calc.h
    extexi:  GEN examples/calc++/calc++-driver.hh
    extexi:  GEN examples/calc++/calc++-driver.cc
    extexi:  GEN examples/calc++/calc++-parser.yy
    extexi:  GEN examples/calc++/calc++-scanner.ll
    extexi:  GEN examples/calc++/calc++.cc
    lib/fseterr.c: In function 'fseterr':
    lib/fseterr.c:77:3: error: #error "Please port gnulib fseterr.c to your platform! Look at the definitions of ferror and clearerr on your system, then report this to bug-gnulib."
      #error "Please port gnulib fseterr.c to your platform! Look at the definitions of ferror and clearerr on your system, then report this to bug-gnulib."
       ^~~~~
    make[2]: *** [Makefile:3461: lib/fseterr.o] Error 1
    make[2]: *** Waiting for unfinished jobs....
    make[1]: *** [Makefile:4522: all-recursive] Error 1
    make: *** [Makefile:2625: all] Error 2
  stderr_lines: <omitted>
  stdout: |2-
      GEN      examples/extracted.stamp
      GEN      lib/alloca.h
      GEN      lib/configmake.h
      GEN      lib/c++defs.h
      GEN      lib/arg-nonnull.h
      GEN      lib/warn-on-use.h
      GEN      lib/unitypes.h
      GEN      lib/sys/types.h
      GEN      lib/uniwidth.h
      GEN      lib/wchar.h
      GEN      lib/wctype.h
      GEN      lib/fcntl.h
      GEN      lib/inttypes.h
      GEN      lib/signal.h
      GEN      lib/math.h
      GEN      lib/spawn.h
      GEN      lib/stdio.h
      GEN      lib/stdlib.h
      GEN      lib/string.h
      GEN      lib/sys/stat.h
      GEN      lib/sys/time.h
      GEN      lib/sys/wait.h
      GEN      lib/time.h
      GEN      lib/unistd.h
    make  all-recursive
    make[1]: Entering directory '/glc/bison/source/bison-3.0.4'
    Making all in po
    make[2]: Entering directory '/glc/bison/source/bison-3.0.4/po'
    make[2]: Nothing to be done for 'all'.
    make[2]: Leaving directory '/glc/bison/source/bison-3.0.4/po'
    Making all in runtime-po
    make[2]: Entering directory '/glc/bison/source/bison-3.0.4/runtime-po'
    make[2]: Nothing to be done for 'all'.
    make[2]: Leaving directory '/glc/bison/source/bison-3.0.4/runtime-po'
    Making all in .
    make[2]: Entering directory '/glc/bison/source/bison-3.0.4'
      CC       lib/wait-process.o
      CC       lib/wctype-h.o
      CC       lib/xmalloc.o
      CC       lib/xalloc-die.o
      CC       lib/xconcat-filename.o
      CC       lib/xsize.o
      CC       lib/xstrndup.o
      CC       lib/abitset.o
      CC       lib/bitset.o
      CC       lib/bitset_stats.o
      CC       lib/bitsetv.o
      CC       lib/ebitset.o
      CC       lib/lbitset.o
      CC       lib/vbitset.o
      CC       lib/bitsetv-print.o
      CC       lib/timevar.o
      CC       lib/get-errno.o
      CC       lib/asnprintf.o
      CC       lib/fcntl.o
      CC       lib/fseterr.o
    make[2]: Leaving directory '/glc/bison/source/bison-3.0.4'
    make[1]: Leaving directory '/glc/bison/source/bison-3.0.4'
  stdout_lines: <omitted>

PLAY RECAP **********************************************************************************************************************************************************************************************************************************************************************************************
node2                      : ok=19   changed=10   unreachable=0    failed=1    skipped=8    rescued=0    ignored=0
```

- https://github.com/ARM-software/arm-enterprise-acs/issues/72





