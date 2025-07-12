---
title: clang
description: clang
date: 2023-11-06
weight: 30000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{%pageinfo%}}
- [llvm-project](https://github.com/llvm/llvm-project/releases/tag/llvmorg-18.1.2)
{{%/pageinfo%}}



| os              | arch   | llvm version |
| :-------------- | :----- | :----------- |
| CentOS-8.5.2111 | x86_64 | llvm-18.1.2  |






{{<note>}}
<!---->

{{</note>}}



```bash
wget https://github.com/llvm/llvm-project/archive/refs/tags/llvmorg-18.1.2.tar.gz

tar -xzf llvmorg-18.1.2.tar.gz

cd  llvm-project-llvmorg-18.1.2

mkdir -p build && cd build

cmake3 -DLLVM_ENABLE_PROJECTS=clang -DCMAKE_INSTALL_PREFIX=/root/local/ -DCMAKE_BUILD_TYPE=Release  ../llvm

make -j8

make install

```


```bash
[root@dev /root/local/bin]# ./clang-format --version
clang-format version 18.1.2

[root@dev /root/local]# ll
total 20
drwxr-xr-x 2 root root  4096 Mar 20 19:40 bin
drwxr-xr-x 6 root root    60 Mar 20 19:40 include
drwxr-xr-x 6 root root 12288 Mar 20 19:40 lib
drwxr-xr-x 2 root root   124 Mar 20 19:40 libexec
drwxr-xr-x 7 root root    83 Mar 20 19:40 share

[root@dev /root/local/bin]# ll
total 2961428
-rwxr-xr-x 1 root root    314696 Mar 20 18:52 amdgpu-arch
-rwxr-xr-x 1 root root       557 Mar 20 05:06 analyze-build
-rwxr-xr-x 1 root root 114408072 Mar 20 19:28 bugpoint
-rwxr-xr-x 1 root root  45949320 Mar 20 19:31 c-index-test
lrwxrwxrwx 1 root root         8 Mar 20 19:40 clang -> clang-18
lrwxrwxrwx 1 root root         5 Mar 20 19:40 clang++ -> clang
-rwxr-xr-x 1 root root 194167496 Mar 20 19:28 clang-18
-rwxr-xr-x 1 root root 148372608 Mar 20 19:27 clang-check
lrwxrwxrwx 1 root root         5 Mar 20 19:40 clang-cl -> clang
lrwxrwxrwx 1 root root         5 Mar 20 19:40 clang-cpp -> clang
-rwxr-xr-x 1 root root  71236648 Mar 20 19:27 clang-extdef-mapping
-rwxr-xr-x 1 root root   4174984 Mar 20 18:53 clang-format
-rwxr-xr-x 1 root root 116155376 Mar 20 19:27 clang-linker-wrapper
-rwxr-xr-x 1 root root   5408504 Mar 20 18:52 clang-offload-bundler
-rwxr-xr-x 1 root root   5585192 Mar 20 19:26 clang-offload-packager
-rwxr-xr-x 1 root root  44090064 Mar 20 19:21 clang-refactor
-rwxr-xr-x 1 root root  42596632 Mar 20 19:20 clang-rename
-rwxr-xr-x 1 root root 186077696 Mar 20 19:27 clang-repl
-rwxr-xr-x 1 root root 138455800 Mar 20 19:29 clang-scan-deps
-rwxr-xr-x 1 root root   2388424 Mar 20 17:35 clang-tblgen
-rwxr-xr-x 1 root root  14724408 Mar 20 18:54 diagtool
-rwxr-xr-x 1 root root  91881736 Mar 20 19:29 dsymutil
-rwxr-xr-x 1 root root     25338 Mar 20 05:06 git-clang-format
-rwxr-xr-x 1 root root      9997 Mar 20 05:06 hmaptool
-rwxr-xr-x 1 root root       563 Mar 20 05:06 intercept-build
-rwxr-xr-x 1 root root 124890696 Mar 20 19:28 llc
-rwxr-xr-x 1 root root  48084016 Mar 20 19:22 lli
lrwxrwxrwx 1 root root        15 Mar 20 19:40 llvm-addr2line -> llvm-symbolizer
-rwxr-xr-x 1 root root  34885408 Mar 20 18:38 llvm-ar
-rwxr-xr-x 1 root root   4872312 Mar 20 18:35 llvm-as
-rwxr-xr-x 1 root root    680016 Mar 20 18:07 llvm-bcanalyzer
lrwxrwxrwx 1 root root        12 Mar 20 19:40 llvm-bitcode-strip -> llvm-objcopy
-rwxr-xr-x 1 root root   4248016 Mar 20 18:35 llvm-cat
-rwxr-xr-x 1 root root  42051848 Mar 20 18:43 llvm-cfi-verify
-rwxr-xr-x 1 root root    549848 Mar 20 17:37 llvm-config
-rwxr-xr-x 1 root root   6385232 Mar 20 18:32 llvm-cov
-rwxr-xr-x 1 root root  92435832 Mar 20 19:27 llvm-c-test
-rwxr-xr-x 1 root root    714632 Mar 20 18:24 llvm-cvtres
-rwxr-xr-x 1 root root   5246744 Mar 20 18:24 llvm-cxxdump
-rwxr-xr-x 1 root root    617304 Mar 20 17:39 llvm-cxxfilt
-rwxr-xr-x 1 root root    688112 Mar 20 18:31 llvm-cxxmap
-rwxr-xr-x 1 root root  33118832 Mar 20 18:43 llvm-debuginfo-analyzer
-rwxr-xr-x 1 root root   5254640 Mar 20 18:30 llvm-debuginfod
-rwxr-xr-x 1 root root    758208 Mar 20 18:30 llvm-debuginfod-find
-rwxr-xr-x 1 root root   3927008 Mar 20 18:19 llvm-diff
-rwxr-xr-x 1 root root   3654952 Mar 20 18:07 llvm-dis
lrwxrwxrwx 1 root root         7 Mar 20 19:40 llvm-dlltool -> llvm-ar
-rwxr-xr-x 1 root root  25572552 Mar 20 18:37 llvm-dwarfdump
-rwxr-xr-x 1 root root  91996888 Mar 20 19:28 llvm-dwarfutil
-rwxr-xr-x 1 root root  89809472 Mar 20 19:28 llvm-dwp
-rwxr-xr-x 1 root root 107017216 Mar 20 19:28 llvm-exegesis
-rwxr-xr-x 1 root root  11477552 Mar 20 19:03 llvm-extract
-rwxr-xr-x 1 root root  81873600 Mar 20 19:28 llvm-gsymutil
-rwxr-xr-x 1 root root   5518352 Mar 20 18:28 llvm-ifs
lrwxrwxrwx 1 root root        12 Mar 20 19:40 llvm-install-name-tool -> llvm-objcopy
-rwxr-xr-x 1 root root  45683776 Mar 20 19:23 llvm-jitlink
lrwxrwxrwx 1 root root         7 Mar 20 19:40 llvm-lib -> llvm-ar
-rwxr-xr-x 1 root root  34833760 Mar 20 19:28 llvm-libtool-darwin
-rwxr-xr-x 1 root root   5719808 Mar 20 18:52 llvm-link
-rwxr-xr-x 1 root root  34735904 Mar 20 19:28 llvm-lipo
-rwxr-xr-x 1 root root 115812432 Mar 20 19:27 llvm-lto
-rwxr-xr-x 1 root root 123951568 Mar 20 19:29 llvm-lto2
-rwxr-xr-x 1 root root  36824088 Mar 20 18:43 llvm-mc
-rwxr-xr-x 1 root root  31664320 Mar 20 18:44 llvm-mca
-rwxr-xr-x 1 root root  36628504 Mar 20 18:43 llvm-ml
-rwxr-xr-x 1 root root   4386248 Mar 20 18:35 llvm-modextract
-rwxr-xr-x 1 root root    451952 Mar 20 17:39 llvm-mt
-rwxr-xr-x 1 root root  36180984 Mar 20 18:38 llvm-nm
-rwxr-xr-x 1 root root   6108736 Mar 20 18:27 llvm-objcopy
-rwxr-xr-x 1 root root  33127368 Mar 20 18:44 llvm-objdump
-rwxr-xr-x 1 root root    777280 Mar 20 17:36 llvm-opt-report
lrwxrwxrwx 1 root root        12 Mar 20 19:40 llvm-otool -> llvm-objdump
-rwxr-xr-x 1 root root   8304704 Mar 20 18:30 llvm-pdbutil
-rwxr-xr-x 1 root root   7344272 Mar 20 18:31 llvm-profdata
-rwxr-xr-x 1 root root  33168128 Mar 20 18:54 llvm-profgen
lrwxrwxrwx 1 root root         7 Mar 20 19:40 llvm-ranlib -> llvm-ar
-rwxr-xr-x 1 root root    964920 Mar 20 18:24 llvm-rc
lrwxrwxrwx 1 root root        12 Mar 20 19:40 llvm-readelf -> llvm-readobj
-rwxr-xr-x 1 root root   9052216 Mar 20 18:30 llvm-readobj
-rwxr-xr-x 1 root root   5468840 Mar 20 18:24 llvm-readtapi
-rwxr-xr-x 1 root root 114860624 Mar 20 19:30 llvm-reduce
-rwxr-xr-x 1 root root   1141384 Mar 20 17:36 llvm-remarkutil
-rwxr-xr-x 1 root root  27894984 Mar 20 18:44 llvm-rtdyld
-rwxr-xr-x 1 root root   4218624 Mar 20 18:34 llvm-sim
-rwxr-xr-x 1 root root   5220160 Mar 20 18:24 llvm-size
-rwxr-xr-x 1 root root   5147096 Mar 20 18:38 llvm-split
-rwxr-xr-x 1 root root   2790200 Mar 20 18:03 llvm-stress
-rwxr-xr-x 1 root root    543800 Mar 20 18:24 llvm-strings
lrwxrwxrwx 1 root root        12 Mar 20 19:40 llvm-strip -> llvm-objcopy
-rwxr-xr-x 1 root root   7509160 Mar 20 18:30 llvm-symbolizer
-rwxr-xr-x 1 root root   5047624 Mar 20 17:37 llvm-tblgen
-rwxr-xr-x 1 root root   5400864 Mar 20 18:34 llvm-tli-checker
-rwxr-xr-x 1 root root    676448 Mar 20 17:34 llvm-undname
lrwxrwxrwx 1 root root         7 Mar 20 19:40 llvm-windres -> llvm-rc
-rwxr-xr-x 1 root root   7855424 Mar 20 18:31 llvm-xray
-rwxr-xr-x 1 root root    306296 Mar 20 18:52 nvptx-arch
-rwxr-xr-x 1 root root 124995992 Mar 20 19:29 opt
-rwxr-xr-x 1 root root  32198264 Mar 20 18:44 sancov
-rwxr-xr-x 1 root root   7199256 Mar 20 18:30 sanstats
-rwxr-xr-x 1 root root     56738 Mar 20 05:06 scan-build
-rwxr-xr-x 1 root root       551 Mar 20 05:06 scan-build-py
-rwxr-xr-x 1 root root      4691 Mar 20 05:06 scan-view
-rwxr-xr-x 1 root root   5276744 Mar 20 18:36 verify-uselistorder

```












