---
title: 24. make
description: make
date: 2023-10-31
weight: 240
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}

{{< /alert >}}



| 选项                                     | 作用                           | 示例                                |
| :--------------------------------------- | :----------------------------- | :---------------------------------- |
| `-f FILE, --file=FILE, --makefile=FILE ` | 指定`makefile`位置             | `make  -f CMakeFiles/Makefile2 all` |
| `-o`                                     | 指定生成可执行文件的名称       | `-o dir/tmp/mi_open.cc.o`           |
| `-c`                                     | 只编译，不链接，只生成目标文件 |                                     |


GNU Autotools 有非常具体的预期规范，如果你经常下载和构建源代码，可能大多数都很熟悉。首先，源代码本身应该位于一个名为 src 的子目录中。

你的项目不必遵循所有这些预期规范，但如果你将文件放在非标准位置（从 Autotools 的角度来看），那么你将不得不稍后在 Makefile 中对其进行调整。

此外，这些文件是必需的：

NEWS
README
AUTHORS
ChangeLog
你不必主动使用这些文件，它们可以是包含所有信息的单个汇总文档（如 README.md）的符号链接，但它们必须存在。






#### /glc/gdb/source/gdb-10.2/missing: line 81: makeinfo: command not found



```bash
make[3]: Entering directory '/glc/gdb/source/gdb-10.2/build_20250428T085819/gdb'
make[4]: Entering directory '/glc/gdb/source/gdb-10.2/build_20250428T085819/gdb/doc'
(test "ln -s" = "ln -s" && \
  ln -s ../../../gdb/doc/all-cfg.texi gdb-cfg.texi) || \
ln ../../../gdb/doc/all-cfg.texi gdb-cfg.texi || \
cp ../../../gdb/doc/all-cfg.texi gdb-cfg.texi
date=`sed -n -e 's/^.* BFD_VERSION_DATE \(.*\)$/\1/p' ../../../gdb/doc/../../bfd/version.h`; \
sed -e "s/DATE/$date/" < ../../../gdb/doc/../version.in > version.subst
echo "@set GDBVN `sed q version.subst`" > ./GDBvn.new
if [ -n "(GDB) " ]; then \
  echo "@set VERSION_PACKAGE (GDB) " >> ./GDBvn.new; \
fi
echo "@set BUGURL @uref{https://www.gnu.org/software/gdb/bugs/}" >> ./GDBvn.new
if [ "@uref{https://www.gnu.org/software/gdb/bugs/}" = "@uref{http://www.gnu.org/software/gdb/bugs/}" ]; then \
  echo "@set BUGURL_DEFAULT" >> ./GDBvn.new; \
fi
if test -z "-I ../../../gdb/doc/../../readline/readline/doc"; then \
  echo "@set SYSTEM_READLINE" >> ./GDBvn.new; \
fi
if [ -n "" ]; then \
  echo "@set SYSTEM_GDBINIT " >> ./GDBvn.new; \
fi
if [ -n "" ]; then \
  echo "@set SYSTEM_GDBINIT_DIR " >> ./GDBvn.new; \
fi
mv GDBvn.new GDBvn.texi
/glc/gdb/source/gdb-10.2/missing makeinfo --split-size=5000000 --split-size=5000000   -I ../../../gdb/doc/../../readline/readline/doc -I ../../../gdb/doc/../mi -I ../../../gdb/doc \
	-o gdb.info ../../../gdb/doc/gdb.texinfo
/glc/gdb/source/gdb-10.2/missing: line 81: makeinfo: command not found
WARNING: 'makeinfo' is missing on your system.
         You should only need it if you modified a '.texi' file, or
         any other file indirectly affecting the aspect of the manual.
         You might want to install the Texinfo package:
         <http://www.gnu.org/software/texinfo/>
         The spurious makeinfo call might also be the consequence of
         using a buggy 'make' (AIX, DU, IRIX), in which case you might
         want to install GNU make:
         <http://www.gnu.org/software/make/>
make[4]: *** [Makefile:490: gdb.info] Error 127
make[4]: Leaving directory '/glc/gdb/source/gdb-10.2/build_20250428T085819/gdb/doc'
make[3]: *** [Makefile:1963: subdir_do] Error 1
make[3]: Leaving directory '/glc/gdb/source/gdb-10.2/build_20250428T085819/gdb'
make[2]: *** [Makefile:1613: all] Error 2
make[2]: Leaving directory '/glc/gdb/source/gdb-10.2/build_20250428T085819/gdb'
make[1]: *** [Makefile:10400: all-gdb] Error 2
make[1]: Leaving directory '/glc/gdb/source/gdb-10.2/build_20250428T085819'
make: *** [Makefile:854: all] Error 2

```
- 原因：源码安装`gdb-10.2`时，`/glc/gdb/source/gdb-10.2/missing`脚本中使用`makeinfo`时找不到该二进制文件
- 解决：先源码安装`textinfo`，在centos8中默认没有提供textinfo，且没有找到dnf安装的方法



