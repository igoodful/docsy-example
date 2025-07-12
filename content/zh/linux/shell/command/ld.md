---
title: ld
description: ld是binutils包中的一个可执行文件
date: 2023-10-31
weight: 20000
viz: true

---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

- 如果链接器版本过低，升级 binutils
- 如果 GCC 版本过低，升级 GCC：

{{< /alert >}}


{{< alert title="sort参数详解" color="secondary">}}

{{< /alert >}}

> 只针对公司英文名称的第二个字母进行排序，如果相同的按照员工工资进行降序排序：


#### dwarf error

- `dwarf error` 一般出现在gcc编译环境版本与gdb调试环境版本不匹配导致，一般可以通过编译时指定dwarf版本解决
- 除了我们自身的源码需要指定dwarf版本，程序所依赖的第三方库也需要使用指定的dwarf版本进行编译
- https://segmentfault.com/a/1190000043552134
- https://zhuanlan.zhihu.com/p/419908664
- https://gcc.gnu.org/onlinedocs/gcc/Debugging-Options.html

```bash

[guolicheng@48 /db/storage/guolicheng/deploy_database_ansible] $ sed -n '/2025-04-27 15:21:16,602/,/2025-04-27 15:21:50,355/p' ticbase_ansible.log
2025-04-27 15:21:16,602 p=275855 u=guolicheng n=ansible | fatal: [node-227]: FAILED! => changed=true
  cmd: make -j1
  delta: '0:01:13.102412'
  end: '2025-04-27 15:21:16.320359'
  msg: non-zero return code
  rc: 2
  start: '2025-04-27 15:20:03.217947'
  stderr: |-
    ../lib/canonicalize.c: In function 'canonicalize_filename_mode':
    ../lib/canonicalize.c:484:5: warning: #warning "GCC might issue a bogus -Wreturn-local-addr warning here." [-Wcpp]
      484 |    #warning "GCC might issue a bogus -Wreturn-local-addr warning here."
          |     ^~~~~~~
    ../lib/canonicalize.c:485:5: warning: #warning "See <https://gcc.gnu.org/bugzilla/show_bug.cgi?id=93644>." [-Wcpp]
      485 |    #warning "See <https://gcc.gnu.org/bugzilla/show_bug.cgi?id=93644>."
          |     ^~~~~~~
    ../lib/careadlinkat.c: In function 'careadlinkat':
    ../lib/careadlinkat.c:178:5: warning: #warning "GCC might issue a bogus -Wreturn-local-addr warning here." [-Wcpp]
      178 |    #warning "GCC might issue a bogus -Wreturn-local-addr warning here."
          |     ^~~~~~~
    ../lib/careadlinkat.c:179:5: warning: #warning "See <https://gcc.gnu.org/bugzilla/show_bug.cgi?id=93644>." [-Wcpp]
      179 |    #warning "See <https://gcc.gnu.org/bugzilla/show_bug.cgi?id=93644>."
          |     ^~~~~~~
    ../lib/careadlinkat.c:182:10: warning: function may return address of local variable [-Wreturn-local-addr]
      182 |   return readlink_stk (fd, filename, buffer, buffer_size, alloc,
          |          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
      183 |                        preadlinkat, stack_buf);
          |                        ~~~~~~~~~~~~~~~~~~~~~~~
    ../lib/careadlinkat.c:181:8: note: declared here
      181 |   char stack_buf[STACK_BUF_SIZE];
          |        ^~~~~~~~~
    /usr/bin/ld: /usr/bin/ld: DWARF error: can't find .debug_ranges section.
    lib/libcoreutils.a(mgetgroups.o): in function `mgetgroups':
    mgetgroups.c:(.text+0x80): warning: Using 'getgrouplist' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: lib/libcoreutils.a(userspec.o): in function `parse_with_separator':
    /glc/coreutils/source/coreutils-9.0/build_20250427T151610/../lib/userspec.c:190: warning: Using 'getgrgid' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: /usr/bin/ld: DWARF error: can't find .debug_ranges section.
    src/chroot.o: in function `parse_additional_groups':
    chroot.c:(.text+0x98): warning: Using 'getgrnam' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: lib/libcoreutils.a(userspec.o): in function `parse_with_separator':
    /glc/coreutils/source/coreutils-9.0/build_20250427T151610/../lib/userspec.c:214: warning: Using 'endgrent' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: /glc/coreutils/source/coreutils-9.0/build_20250427T151610/../lib/userspec.c:161: warning: Using 'getpwnam' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: src/chroot.o: in function `main':
    chroot.c:(.text.startup+0x340): warning: Using 'getpwuid' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: lib/libcoreutils.a(userspec.o): in function `parse_with_separator':
    /glc/coreutils/source/coreutils-9.0/build_20250427T151610/../lib/userspec.c:195: warning: Using 'endpwent' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: lib/libcoreutils.a(canon-host.o): in function `canon_host_r':
    /glc/coreutils/source/coreutils-9.0/build_20250427T151610/../lib/canon-host.c:69: warning: Using 'getaddrinfo' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: /usr/bin/ld: DWARF error: can't find .debug_ranges section.
    src/pinky.o: in function `print_long_entry':
    pinky.c:(.text+0x144): warning: Using 'getpwnam' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: lib/libcoreutils.a(canon-host.o): in function `canon_host_r':
    /glc/coreutils/source/coreutils-9.0/build_20250427T151610/../lib/canon-host.c:69: warning: Using 'getaddrinfo' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: /usr/bin/ld: DWARF error: can't find .debug_ranges section.
    src/chown-core.o: in function `gid_to_name':
    chown-core.c:(.text+0xf0): warning: Using 'getgrgid' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: /usr/bin/ld: DWARF error: can't find .debug_ranges section.
    src/chgrp.o: in function `main':
    chgrp.c:(.text.startup+0x2fc): warning: Using 'getgrnam' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: chgrp.c:(.text.startup+0x308): warning: Using 'endgrent' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: src/chown-core.o: in function `uid_to_name':
    chown-core.c:(.text+0x140): warning: Using 'getpwuid' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: /usr/bin/ld: DWARF error: can't find .debug_ranges section.
    src/chown-core.o: in function `gid_to_name':
    chown-core.c:(.text+0xf0): warning: Using 'getgrgid' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: lib/libcoreutils.a(userspec.o): in function `parse_with_separator':
    /glc/coreutils/source/coreutils-9.0/build_20250427T151610/../lib/userspec.c:202: warning: Using 'getgrnam' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: /glc/coreutils/source/coreutils-9.0/build_20250427T151610/../lib/userspec.c:214: warning: Using 'endgrent' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: /glc/coreutils/source/coreutils-9.0/build_20250427T151610/../lib/userspec.c:161: warning: Using 'getpwnam' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: src/chown-core.o: in function `uid_to_name':
    chown-core.c:(.text+0x140): warning: Using 'getpwuid' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: lib/libcoreutils.a(userspec.o): in function `parse_with_separator':
    /glc/coreutils/source/coreutils-9.0/build_20250427T151610/../lib/userspec.c:195: warning: Using 'endpwent' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: /lib/../lib64/libselinux.a(seusers.o): in function `getseuserbyname':
    (.text+0x518): warning: Using 'getgrouplist' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: (.text+0x4f0): warning: Using 'getgrnam_r' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: (.text+0x7c): warning: Using 'getpwnam_r' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: /lib/../lib64/libselinux.a(procattr.o): in function `openattr':
    (.text+0xd4): undefined reference to `security_lsm_is_enabled'
    /usr/bin/ld: /lib/../lib64/libselinux.a(regex.o): in function `regex_version':
    (.text+0x80): undefined reference to `pcre2_config_8'
    /usr/bin/ld: (.text+0xac): undefined reference to `pcre2_config_8'
    /usr/bin/ld: /lib/../lib64/libselinux.a(regex.o): in function `regex_writef':
    (.text+0x114): undefined reference to `pcre2_serialize_encode_8'
    /usr/bin/ld: (.text+0x194): undefined reference to `pcre2_serialize_free_8'
    /usr/bin/ld: /lib/../lib64/libselinux.a(regex.o): in function `regex_data_free':
    (.text+0x1e4): undefined reference to `pcre2_code_free_8'
    /usr/bin/ld: (.text+0x1f0): undefined reference to `pcre2_match_data_free_8'
    /usr/bin/ld: /lib/../lib64/libselinux.a(regex.o): in function `regex_match':
    (.text+0x270): undefined reference to `pcre2_match_8'
    /usr/bin/ld: /lib/../lib64/libselinux.a(regex.o): in function `regex_cmp':
    (.text+0x308): undefined reference to `pcre2_pattern_info_8'
    /usr/bin/ld: (.text+0x31c): undefined reference to `pcre2_pattern_info_8'
    /usr/bin/ld: /lib/../lib64/libselinux.a(regex.o): in function `regex_prepare_data':
    (.text+0x454): undefined reference to `pcre2_compile_8'
    /usr/bin/ld: (.text+0x46c): undefined reference to `pcre2_match_data_create_from_pattern_8'
    /usr/bin/ld: /lib/../lib64/libselinux.a(regex.o): in function `regex_load_mmap':
    (.text+0x534): undefined reference to `pcre2_serialize_get_number_of_codes_8'
    /usr/bin/ld: (.text+0x55c): undefined reference to `pcre2_serialize_decode_8'
    /usr/bin/ld: (.text+0x57c): undefined reference to `pcre2_match_data_create_from_pattern_8'
    /usr/bin/ld: /lib/../lib64/libselinux.a(regex.o): in function `regex_format_error':
    (.text+0x654): undefined reference to `pcre2_get_error_message_8'
    /usr/bin/ld: /lib/../lib64/libselinux.a(load_policy.o): in function `selinux_mkload_policy':
    (.text+0x15c): undefined reference to `sepol_policy_kern_vers_max'
    /usr/bin/ld: (.text+0x168): undefined reference to `sepol_policy_kern_vers_min'
    /usr/bin/ld: (.text+0x300): undefined reference to `sepol_policy_kern_vers_max'
    /usr/bin/ld: (.text+0x308): undefined reference to `sepol_policy_kern_vers_min'
    /usr/bin/ld: (.text+0x334): undefined reference to `sepol_policy_kern_vers_max'
    /usr/bin/ld: (.text+0x33c): undefined reference to `sepol_policy_kern_vers_min'
    /usr/bin/ld: (.text+0x398): undefined reference to `sepol_policy_file_create'
    /usr/bin/ld: (.text+0x3a4): undefined reference to `sepol_policydb_create'
    /usr/bin/ld: (.text+0x3b4): undefined reference to `sepol_policy_file_set_mem'
    /usr/bin/ld: (.text+0x3bc): undefined reference to `sepol_policydb_read'
    /usr/bin/ld: (.text+0x3cc): undefined reference to `sepol_policydb_set_vers'
    /usr/bin/ld: (.text+0x3e0): undefined reference to `sepol_policydb_to_image'
    /usr/bin/ld: (.text+0x410): undefined reference to `sepol_policy_file_free'
    /usr/bin/ld: (.text+0x418): undefined reference to `sepol_policydb_free'
    /usr/bin/ld: (.text+0x44c): undefined reference to `sepol_policy_kern_vers_max'
    /usr/bin/ld: (.text+0x458): undefined reference to `sepol_policy_kern_vers_min'
    /usr/bin/ld: (.text+0x468): undefined reference to `sepol_policy_file_free'
    /usr/bin/ld: (.text+0x470): undefined reference to `sepol_policydb_free'
    /usr/bin/ld: (.text+0x578): undefined reference to `sepol_policy_file_free'
    /usr/bin/ld: (.text+0x588): undefined reference to `sepol_policy_file_free'
    /usr/bin/ld: (.text+0x590): undefined reference to `sepol_policydb_free'
    /usr/bin/ld: (.text+0x67c): undefined reference to `sepol_genbools_array'
    /usr/bin/ld: (.text+0x6dc): undefined reference to `sepol_genusers'
    /usr/bin/ld: (.text+0x710): undefined reference to `sepol_genbools'
    collect2: error: ld returned 1 exit status
    make[2]: *** [Makefile:9437: src/cp] Error 1
    make[1]: *** [Makefile:13464: all-recursive] Error 1
    make: *** [Makefile:7386: all] Error 2
  stderr_lines: <omitted>
  stdout: |2-
      GEN      lib/alloca.h
      GEN      lib/arpa/inet.h
      GEN      lib/configmake.h
      GEN      lib/ctype.h
      GEN      lib/dirent.h
      GEN      lib/malloc/dynarray.gl.h
      GEN      lib/malloc/dynarray-skeleton.gl.h
      GEN      lib/fcntl.h
      GEN      lib/iconv.h
      GEN      lib/inttypes.h
      GEN      lib/langinfo.h
      GEN      lib/limits.h
      GEN      lib/locale.h
      GEN      lib/math.h
      GEN      lib/netdb.h
      GEN      lib/pthread.h
      GEN      lib/sched.h
      GEN      lib/malloc/scratch_buffer.gl.h
      GEN      lib/selinux/selinux.h
      GEN      lib/signal.h
      GEN      lib/stdio.h
      GEN      lib/stdlib.h
      GEN      lib/string.h
      GEN      lib/sys/ioctl.h
      GEN      lib/sys/random.h
      GEN      lib/sys/resource.h
      GEN      lib/sys/select.h
      GEN      lib/sys/socket.h
      GEN      lib/sys/stat.h
      GEN      lib/sys/time.h
      GEN      lib/sys/types.h
      GEN      lib/sys/uio.h
      GEN      lib/sys/utsname.h
      GEN      lib/sys/wait.h
      GEN      lib/termios.h
      GEN      lib/time.h
      GEN      lib/unistd.h
      GEN      lib/unistr.h
      GEN      lib/unitypes.h
      GEN      lib/uniwidth.h
      GEN      lib/utime.h
      GEN      lib/wchar.h
      GEN      lib/wctype.h
      GEN      src/coreutils.h
      GEN      src/version.c
      GEN      src/version.h
    make  all-recursive
    make[1]: Entering directory '/glc/coreutils/source/coreutils-9.0/build_20250427T151610'
    Making all in po
    make[2]: Entering directory '/glc/coreutils/source/coreutils-9.0/build_20250427T151610/po'
    make[2]: Nothing to be done for 'all'.
    make[2]: Leaving directory '/glc/coreutils/source/coreutils-9.0/build_20250427T151610/po'
    Making all in .
    make[2]: Entering directory '/glc/coreutils/source/coreutils-9.0/build_20250427T151610'
      CC       src/chroot.o
      CC       src/version.o
      AR       src/libver.a
      CC       lib/copy-acl.o
      CC       lib/set-acl.o
      CC       lib/acl-errno-valid.o
      CC       lib/acl-internal.o
      CC       lib/get-permissions.o
      CC       lib/set-permissions.o
      CC       lib/allocator.o
      CC       lib/areadlink.o
      CC       lib/areadlink-with-size.o
      CC       lib/areadlinkat.o
      CC       lib/argmatch.o
      CC       lib/argv-iter.o
      CC       lib/openat-proc.o
      CC       lib/backupfile.o
      CC       lib/backup-rename.o
      CC       lib/backup-find.o
      CC       lib/base32.o
      CC       lib/base64.o
      CC       lib/basename-lgpl.o
      CC       lib/binary-io.o
      CC       lib/bitrotate.o
      CC       lib/buffer-lcm.o
      CC       lib/c-ctype.o
      CC       lib/c-strcasecmp.o
      CC       lib/c-strncasecmp.o
      CC       lib/c-strtod.o
      CC       lib/c-strtold.o
      CC       lib/canon-host.o
      CC       lib/canonicalize.o
      CC       lib/careadlinkat.o
      CC       lib/chmodat.o
      CC       lib/chownat.o
      CC       lib/cl-strtod.o
      CC       lib/cl-strtold.o
      CC       lib/cloexec.o
      CC       lib/close-stream.o
      CC       lib/closein.o
      CC       lib/closeout.o
      CC       lib/count-leading-zeros.o
      CC       lib/af_alg.o
      CC       lib/md5-stream.o
      CC       lib/md5.o
      CC       lib/sha1-stream.o
      CC       lib/sha1.o
      CC       lib/sha256-stream.o
      CC       lib/sha256.o
      CC       lib/sha512-stream.o
      CC       lib/sha512.o
      CC       lib/sm3-stream.o
      CC       lib/sm3.o
      CC       lib/cycle-check.o
      CC       lib/di-set.o
      CC       lib/opendir-safer.o
      CC       lib/dirname.o
      CC       lib/basename.o
      CC       lib/dirname-lgpl.o
      CC       lib/stripslash.o
      CC       lib/dtoastr.o
      CC       lib/dtotimespec.o
      CC       lib/malloc/dynarray_at_failure.o
      CC       lib/malloc/dynarray_emplace_enlarge.o
      CC       lib/malloc/dynarray_finalize.o
      CC       lib/malloc/dynarray_resize.o
      CC       lib/malloc/dynarray_resize_clear.o
      CC       lib/exclude.o
      CC       lib/exitfail.o
      CC       lib/fadvise.o
      CC       lib/creat-safer.o
      CC       lib/open-safer.o
      CC       lib/fd-hook.o
      CC       lib/fd-reopen.o
      CC       lib/fd-safer-flag.o
      CC       lib/dup-safer-flag.o
      CC       lib/fdutimensat.o
      CC       lib/file-has-acl.o
      CC       lib/file-set.o
      CC       lib/file-type.o
      CC       lib/filemode.o
      CC       lib/filenamecat.o
      CC       lib/filenamecat-lgpl.o
      CC       lib/filevercmp.o
      CC       lib/fopen-safer.o
      CC       lib/fprintftime.o
      CC       lib/freading.o
      CC       lib/freadseek.o
      CC       lib/freopen-safer.o
      CC       lib/ftoastr.o
      CC       lib/full-read.o
      CC       lib/full-write.o
      CC       lib/gethrxtime.o
      CC       lib/xtime.o
      CC       lib/getndelim2.o
      CC       lib/getprogname.o
      CC       lib/gettime.o
      CC       lib/getugroups.o
      CC       lib/hard-locale.o
      CC       lib/hash.o
      CC       lib/hash-pjw.o
      CC       lib/hash-triple.o
      CC       lib/hash-triple-simple.o
      CC       lib/heap.o
      CC       lib/human.o
      CC       lib/i-ring.o
      CC       lib/ialloc.o
      CC       lib/idcache.o
      CC       lib/ino-map.o
      CC       lib/imaxtostr.o
      CC       lib/inttostr.o
      CC       lib/offtostr.o
      CC       lib/uinttostr.o
      CC       lib/umaxtostr.o
      CC       lib/ldtoastr.o
      CC       lib/linebuffer.o
      CC       lib/localcharset.o
      CC       lib/glthread/lock.o
      CC       lib/long-options.o
      CC       lib/malloca.o
      CC       lib/math.o
      CC       lib/mbchar.o
      CC       lib/mbiter.o
      CC       lib/mbsalign.o
      CC       lib/mbscasecmp.o
      CC       lib/mbschr.o
      CC       lib/mbslen.o
      CC       lib/mbsstr.o
      CC       lib/mbswidth.o
      CC       lib/mbuiter.o
      CC       lib/memcasecmp.o
      CC       lib/memchr2.o
      CC       lib/memcmp2.o
      CC       lib/memcoll.o
      CC       lib/mgetgroups.o
      CC       lib/mkancesdirs.o
      CC       lib/dirchownmod.o
      CC       lib/mkdir-p.o
      CC       lib/modechange.o
      CC       lib/mpsort.o
      CC       lib/nproc.o
      CC       lib/nstrftime.o
      CC       lib/openat-die.o
      CC       lib/openat-safer.o
      CC       lib/opendirat.o
      CC       lib/parse-datetime.o
      CC       lib/physmem.o
      CC       lib/pipe2.o
      CC       lib/posixtm.o
      CC       lib/posixver.o
      CC       lib/printf-frexp.o
      CC       lib/printf-frexpl.o
      CC       lib/priv-set.o
      CC       lib/progname.o
      CC       lib/propername.o
      CC       lib/qcopy-acl.o
      CC       lib/qset-acl.o
      CC       lib/quotearg.o
      CC       lib/randint.o
      CC       lib/randperm.o
      CC       lib/randread.o
      CC       lib/rand-isaac.o
      CC       lib/read-file.o
      CC       lib/readtokens.o
      CC       lib/readtokens0.o
      CC       lib/renameatu.o
      CC       lib/root-dev-ino.o
      CC       lib/safe-read.o
      CC       lib/safe-write.o
      CC       lib/same.o
      CC       lib/save-cwd.o
      CC       lib/savedir.o
      CC       lib/savewd.o
      CC       lib/malloc/scratch_buffer_dupfree.o
      CC       lib/malloc/scratch_buffer_grow.o
      CC       lib/malloc/scratch_buffer_grow_preserve.o
      CC       lib/malloc/scratch_buffer_set_array_size.o
      CC       lib/selinux-at.o
      CC       lib/se-context.o
      CC       lib/se-label.o
      CC       lib/se-selinux.o
      CC       lib/setlocale_null.o
      CC       lib/settime.o
      CC       lib/sig-handler.o
      CC       lib/sockets.o
      CC       lib/stat-time.o
      CC       lib/statat.o
      CC       lib/mkstemp-safer.o
      CC       lib/striconv.o
      CC       lib/strnlen1.o
      CC       lib/strintcmp.o
      CC       lib/strnumcmp.o
      CC       lib/sys_socket.o
      CC       lib/tempname.o
      CC       lib/glthread/threadlib.o
      CC       lib/timespec.o
      CC       lib/glthread/tls.o
      CC       lib/trim.o
      CC       lib/u64.o
      CC       lib/unicodeio.o
      CC       lib/unistd.o
      CC       lib/dup-safer.o
      CC       lib/fd-safer.o
      CC       lib/pipe-safer.o
      CC       lib/unistr/u8-mbtoucr.o
      CC       lib/unistr/u8-uctomb.o
      CC       lib/unistr/u8-uctomb-aux.o
      CC       lib/uniwidth/width.o
      CC       lib/unlinkdir.o
      CC       lib/userspec.o
      CC       lib/utimecmp.o
      CC       lib/utimens.o
      CC       lib/verror.o
      CC       lib/version-etc.o
      CC       lib/version-etc-fsf.o
      CC       lib/wctype-h.o
      CC       lib/write-any-file.o
      CC       lib/xmalloc.o
      CC       lib/xalloc-die.o
      CC       lib/xbinary-io.o
      CC       lib/xdectoimax.o
      CC       lib/xdectoumax.o
      CC       lib/xfts.o
      CC       lib/xgetcwd.o
      CC       lib/xgetgroups.o
      CC       lib/xgethostname.o
      CC       lib/xmemcoll.o
      CC       lib/xnanosleep.o
      CC       lib/xprintf.o
      CC       lib/xreadlink.o
      CC       lib/xsize.o
      CC       lib/xstriconv.o
      CC       lib/xstrtod.o
      CC       lib/xstrtoimax.o
      CC       lib/xstrtol.o
      CC       lib/xstrtoul.o
      CC       lib/xstrtol-error.o
      CC       lib/xstrtold.o
      CC       lib/xstrtoumax.o
      CC       lib/xvasprintf.o
      CC       lib/xasprintf.o
      CC       lib/yesno.o
      CC       lib/asnprintf.o
      CC       lib/chdir-long.o
      CC       lib/fchmodat.o
      CC       lib/fclose.o
      CC       lib/fcntl.o
      CC       lib/fflush.o
      CC       lib/fpurge.o
      CC       lib/freadahead.o
      CC       lib/freadptr.o
      CC       lib/free.o
      CC       lib/fseek.o
      CC       lib/fseeko.o
      CC       lib/fseterr.o
      CC       lib/fsusage.o
      CC       lib/fts.o
      CC       lib/getfilecon.o
      CC       lib/isapipe.o
      CC       lib/lchmod.o
      CC       lib/mbrlen.o
      CC       lib/mbrtowc.o
      CC       lib/mknod.o
      CC       lib/mountlist.o
      CC       lib/nanosleep.o
      CC       lib/obstack.o
      CC       lib/printf-args.o
      CC       lib/printf-parse.o
      CC       lib/readutmp.o
      CC       lib/regex.o
      CC       lib/sig2str.o
      CC       lib/strstr.o
      CC       lib/time_rz.o
      CC       lib/vasnprintf.o
      AR       lib/libcoreutils.a
      CCLD     src/chroot
      CC       src/hostid.o
      CCLD     src/hostid
      CC       src/timeout.o
      CC       src/operand2sig.o
      CCLD     src/timeout
      CC       src/nice.o
      CCLD     src/nice
      CC       src/who.o
      CCLD     src/who
      CC       src/users.o
      CCLD     src/users
      CC       src/pinky.o
      CCLD     src/pinky
      CC       src/uptime.o
      CCLD     src/uptime
      CC       src/stty.o
      CCLD     src/stty
      CC       src/df.o
      CC       src/find-mount-point.o
      CCLD     src/df
      CC       src/stdbuf.o
      CCLD     src/stdbuf
      CC       src/lbracket.o
      CCLD     src/[
      CC       src/b2sum-digest.o
      CC       src/blake2/b2sum-blake2b-ref.o
      CC       src/blake2/b2sum-b2sum.o
      CCLD     src/b2sum
      CC       src/base64-basenc.o
      CCLD     src/base64
      CC       src/base32-basenc.o
      CCLD     src/base32
      CC       src/basenc-basenc.o
      CCLD     src/basenc
      CC       src/basename.o
      CCLD     src/basename
      CC       src/cat.o
      CCLD     src/cat
      CC       src/chcon.o
      CCLD     src/chcon
      CC       src/chgrp.o
      CC       src/chown-core.o
      CCLD     src/chgrp
      CC       src/chmod.o
      CCLD     src/chmod
      CC       src/chown.o
      CCLD     src/chown
      CC       src/cksum-digest.o
      CC       src/blake2/cksum-blake2b-ref.o
      CC       src/blake2/cksum-b2sum.o
      CC       src/cksum-sum.o
      CC       src/cksum-cksum.o
      CC       src/cksum-crctab.o
      CCLD     src/cksum
      CC       src/comm.o
      CCLD     src/comm
      CC       src/cp.o
      CC       src/copy.o
      CC       src/cp-hash.o
      CC       src/force-link.o
      CC       src/selinux.o
      CCLD     src/cp
    make[2]: Leaving directory '/glc/coreutils/source/coreutils-9.0/build_20250427T151610'
    make[1]: Leaving directory '/glc/coreutils/source/coreutils-9.0/build_20250427T151610'
  stdout_lines: <omitted>
2025-04-27 15:21:46,889 p=275855 u=guolicheng n=ansible | changed: [node-250] => changed=true
  cmd: make -j1
  delta: '0:01:43.500071'
  end: '2025-04-27 15:21:46.662337'
  rc: 0
  start: '2025-04-27 15:20:03.162266'
  stderr: |-
    ../lib/canonicalize.c: In function 'canonicalize_filename_mode':
    ../lib/canonicalize.c:484:5: warning: #warning "GCC might issue a bogus -Wreturn-local-addr warning here." [-Wcpp]
      484 |    #warning "GCC might issue a bogus -Wreturn-local-addr warning here."
          |     ^~~~~~~
    ../lib/canonicalize.c:485:5: warning: #warning "See <https://gcc.gnu.org/bugzilla/show_bug.cgi?id=93644>." [-Wcpp]
      485 |    #warning "See <https://gcc.gnu.org/bugzilla/show_bug.cgi?id=93644>."
          |     ^~~~~~~
    ../lib/careadlinkat.c: In function 'careadlinkat':
    ../lib/careadlinkat.c:178:5: warning: #warning "GCC might issue a bogus -Wreturn-local-addr warning here." [-Wcpp]
      178 |    #warning "GCC might issue a bogus -Wreturn-local-addr warning here."
          |     ^~~~~~~
    ../lib/careadlinkat.c:179:5: warning: #warning "See <https://gcc.gnu.org/bugzilla/show_bug.cgi?id=93644>." [-Wcpp]
      179 |    #warning "See <https://gcc.gnu.org/bugzilla/show_bug.cgi?id=93644>."
          |     ^~~~~~~
    ../lib/careadlinkat.c:182:10: warning: function may return address of local variable [-Wreturn-local-addr]
      182 |   return readlink_stk (fd, filename, buffer, buffer_size, alloc,
          |          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
      183 |                        preadlinkat, stack_buf);
          |                        ~~~~~~~~~~~~~~~~~~~~~~~
    ../lib/careadlinkat.c:181:8: note: declared here
      181 |   char stack_buf[STACK_BUF_SIZE];
          |        ^~~~~~~~~
    In file included from ../lib/libc-config.h:164,
                     from ../lib/malloc/dynarray_at_failure.c:20:
    ../lib/cdefs.h:453: warning: "__glibc_likely" redefined
      453 | # define __glibc_likely(cond)   __builtin_expect ((cond), 1)
          |
    In file included from /usr/include/features.h:375,
                     from /usr/include/errno.h:28,
                     from ../lib/libc-config.h:42,
                     from ../lib/malloc/dynarray_at_failure.c:20:
    /usr/include/sys/cdefs.h:386: note: this is the location of the previous definition
      386 | # define __glibc_likely(cond) __builtin_expect((cond), 1)
          |
    In file included from ../lib/libc-config.h:164,
                     from ../lib/malloc/dynarray_emplace_enlarge.c:20:
    ../lib/cdefs.h:453: warning: "__glibc_likely" redefined
      453 | # define __glibc_likely(cond)   __builtin_expect ((cond), 1)
          |
    In file included from /usr/include/features.h:375,
                     from /usr/include/errno.h:28,
                     from ../lib/libc-config.h:42,
                     from ../lib/malloc/dynarray_emplace_enlarge.c:20:
    /usr/include/sys/cdefs.h:386: note: this is the location of the previous definition
      386 | # define __glibc_likely(cond) __builtin_expect((cond), 1)
          |
    In file included from ../lib/libc-config.h:164,
                     from ../lib/malloc/dynarray_finalize.c:20:
    ../lib/cdefs.h:453: warning: "__glibc_likely" redefined
      453 | # define __glibc_likely(cond)   __builtin_expect ((cond), 1)
          |
    In file included from /usr/include/features.h:375,
                     from /usr/include/errno.h:28,
                     from ../lib/libc-config.h:42,
                     from ../lib/malloc/dynarray_finalize.c:20:
    /usr/include/sys/cdefs.h:386: note: this is the location of the previous definition
      386 | # define __glibc_likely(cond) __builtin_expect((cond), 1)
          |
    In file included from ../lib/libc-config.h:164,
                     from ../lib/malloc/dynarray_resize.c:20:
    ../lib/cdefs.h:453: warning: "__glibc_likely" redefined
      453 | # define __glibc_likely(cond)   __builtin_expect ((cond), 1)
          |
    In file included from /usr/include/features.h:375,
                     from /usr/include/errno.h:28,
                     from ../lib/libc-config.h:42,
                     from ../lib/malloc/dynarray_resize.c:20:
    /usr/include/sys/cdefs.h:386: note: this is the location of the previous definition
      386 | # define __glibc_likely(cond) __builtin_expect((cond), 1)
          |
    In file included from ../lib/libc-config.h:164,
                     from ../lib/malloc/dynarray_resize_clear.c:20:
    ../lib/cdefs.h:453: warning: "__glibc_likely" redefined
      453 | # define __glibc_likely(cond)   __builtin_expect ((cond), 1)
          |
    In file included from /usr/include/features.h:375,
                     from /usr/include/errno.h:28,
                     from ../lib/libc-config.h:42,
                     from ../lib/malloc/dynarray_resize_clear.c:20:
    /usr/include/sys/cdefs.h:386: note: this is the location of the previous definition
      386 | # define __glibc_likely(cond) __builtin_expect((cond), 1)
          |
    In file included from ../lib/libc-config.h:164,
                     from ../lib/malloc/scratch_buffer_dupfree.c:20:
    ../lib/cdefs.h:453: warning: "__glibc_likely" redefined
      453 | # define __glibc_likely(cond)   __builtin_expect ((cond), 1)
          |
    In file included from /usr/include/features.h:375,
                     from /usr/include/errno.h:28,
                     from ../lib/libc-config.h:42,
                     from ../lib/malloc/scratch_buffer_dupfree.c:20:
    /usr/include/sys/cdefs.h:386: note: this is the location of the previous definition
      386 | # define __glibc_likely(cond) __builtin_expect((cond), 1)
          |
    In file included from ../lib/libc-config.h:164,
                     from ../lib/malloc/scratch_buffer_grow.c:20:
    ../lib/cdefs.h:453: warning: "__glibc_likely" redefined
      453 | # define __glibc_likely(cond)   __builtin_expect ((cond), 1)
          |
    In file included from /usr/include/features.h:375,
                     from /usr/include/errno.h:28,
                     from ../lib/libc-config.h:42,
                     from ../lib/malloc/scratch_buffer_grow.c:20:
    /usr/include/sys/cdefs.h:386: note: this is the location of the previous definition
      386 | # define __glibc_likely(cond) __builtin_expect((cond), 1)
          |
    In file included from ../lib/libc-config.h:164,
                     from ../lib/malloc/scratch_buffer_grow_preserve.c:20:
    ../lib/cdefs.h:453: warning: "__glibc_likely" redefined
      453 | # define __glibc_likely(cond)   __builtin_expect ((cond), 1)
          |
    In file included from /usr/include/features.h:375,
                     from /usr/include/errno.h:28,
                     from ../lib/libc-config.h:42,
                     from ../lib/malloc/scratch_buffer_grow_preserve.c:20:
    /usr/include/sys/cdefs.h:386: note: this is the location of the previous definition
      386 | # define __glibc_likely(cond) __builtin_expect((cond), 1)
          |
    In file included from ../lib/libc-config.h:164,
                     from ../lib/malloc/scratch_buffer_set_array_size.c:20:
    ../lib/cdefs.h:453: warning: "__glibc_likely" redefined
      453 | # define __glibc_likely(cond)   __builtin_expect ((cond), 1)
          |
    In file included from /usr/include/features.h:375,
                     from /usr/include/errno.h:28,
                     from ../lib/libc-config.h:42,
                     from ../lib/malloc/scratch_buffer_set_array_size.c:20:
    /usr/include/sys/cdefs.h:386: note: this is the location of the previous definition
      386 | # define __glibc_likely(cond) __builtin_expect((cond), 1)
          |
    In file included from ../lib/libc-config.h:164,
                     from ../lib/tempname.c:19:
    ../lib/cdefs.h:453: warning: "__glibc_likely" redefined
      453 | # define __glibc_likely(cond)   __builtin_expect ((cond), 1)
          |
    In file included from /usr/include/features.h:375,
                     from /usr/include/errno.h:28,
                     from ../lib/libc-config.h:42,
                     from ../lib/tempname.c:19:
    /usr/include/sys/cdefs.h:386: note: this is the location of the previous definition
      386 | # define __glibc_likely(cond) __builtin_expect((cond), 1)
          |
    In file included from ../lib/libc-config.h:164,
                     from ../lib/regex.c:23:
    ../lib/cdefs.h:453: warning: "__glibc_likely" redefined
      453 | # define __glibc_likely(cond)   __builtin_expect ((cond), 1)
          |
    In file included from /usr/include/features.h:375,
                     from /usr/include/errno.h:28,
                     from ../lib/libc-config.h:42,
                     from ../lib/regex.c:23:
    /usr/include/sys/cdefs.h:386: note: this is the location of the previous definition
      386 | # define __glibc_likely(cond) __builtin_expect((cond), 1)
          |
    /usr/bin/ld: Dwarf Error: found dwarf version '5', this reader only handles version 2, 3 and 4 information.
    lib/libcoreutils.a(mgetgroups.o): In function `mgetgroups':
    mgetgroups.c:(.text+0x8b): warning: Using 'getgrouplist' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: Dwarf Error: found dwarf version '5', this reader only handles version 2, 3 and 4 information.
    lib/libcoreutils.a(userspec.o): In function `parse_with_separator':
    userspec.c:(.text+0x2d1): warning: Using 'getgrgid' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: Dwarf Error: found dwarf version '5', this reader only handles version 2, 3 and 4 information.
    src/chroot.o: In function `parse_additional_groups':
    chroot.c:(.text+0x88): warning: Using 'getgrnam' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    lib/libcoreutils.a(userspec.o): In function `parse_with_separator':
    userspec.c:(.text+0x99): warning: Using 'endgrent' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: Dwarf Error: found dwarf version '7424', this reader only handles version 2, 3 and 4 information.
    userspec.c:(.text+0x13c): warning: Using 'getpwnam' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    src/chroot.o: In function `main':
    chroot.c:(.text.startup+0x311): warning: Using 'getpwuid' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    lib/libcoreutils.a(userspec.o): In function `parse_with_separator':
    userspec.c:(.text+0x15a): warning: Using 'endpwent' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: Dwarf Error: found dwarf version '5', this reader only handles version 2, 3 and 4 information.
    lib/libcoreutils.a(canon-host.o): In function `canon_host_r':
    canon-host.c:(.text+0x2e): warning: Using 'getaddrinfo' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: Dwarf Error: found dwarf version '5', this reader only handles version 2, 3 and 4 information.
    src/pinky.o: In function `print_long_entry':
    pinky.c:(.text+0x121): warning: Using 'getpwnam' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: Dwarf Error: found dwarf version '5', this reader only handles version 2, 3 and 4 information.
    lib/libcoreutils.a(canon-host.o): In function `canon_host_r':
    canon-host.c:(.text+0x2e): warning: Using 'getaddrinfo' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: Dwarf Error: found dwarf version '5', this reader only handles version 2, 3 and 4 information.
    src/chown-core.o: In function `gid_to_name':
    chown-core.c:(.text+0xd8): warning: Using 'getgrgid' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: Dwarf Error: found dwarf version '5', this reader only handles version 2, 3 and 4 information.
    src/chgrp.o: In function `main':
    chgrp.c:(.text.startup+0x2c6): warning: Using 'getgrnam' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    chgrp.c:(.text.startup+0x2d8): warning: Using 'endgrent' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    src/chown-core.o: In function `uid_to_name':
    chown-core.c:(.text+0x118): warning: Using 'getpwuid' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: Dwarf Error: found dwarf version '5', this reader only handles version 2, 3 and 4 information.
    src/chown-core.o: In function `gid_to_name':
    chown-core.c:(.text+0xd8): warning: Using 'getgrgid' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: Dwarf Error: found dwarf version '5', this reader only handles version 2, 3 and 4 information.
    lib/libcoreutils.a(userspec.o): In function `parse_with_separator':
    userspec.c:(.text+0x85): warning: Using 'getgrnam' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    userspec.c:(.text+0x99): warning: Using 'endgrent' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: Dwarf Error: found dwarf version '7424', this reader only handles version 2, 3 and 4 information.
    userspec.c:(.text+0x13c): warning: Using 'getpwnam' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    src/chown-core.o: In function `uid_to_name':
    chown-core.c:(.text+0x118): warning: Using 'getpwuid' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    lib/libcoreutils.a(userspec.o): In function `parse_with_separator':
    userspec.c:(.text+0x15a): warning: Using 'endpwent' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: Dwarf Error: found dwarf version '5', this reader only handles version 2, 3 and 4 information.
    lib/libcoreutils.a(idcache.o): In function `getgroup':
    idcache.c:(.text+0x1f9): warning: Using 'getgrgid' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    lib/libcoreutils.a(idcache.o): In function `getgidbyname':
    idcache.c:(.text+0x2ec): warning: Using 'getgrnam' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: Dwarf Error: found dwarf version '7424', this reader only handles version 2, 3 and 4 information.
    lib/libcoreutils.a(idcache.o): In function `getuidbyname':
    idcache.c:(.text+0x13c): warning: Using 'getpwnam' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    lib/libcoreutils.a(idcache.o): In function `getuser':
    idcache.c:(.text+0x49): warning: Using 'getpwuid' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: Dwarf Error: found dwarf version '5', this reader only handles version 2, 3 and 4 information.
    src/install.o: In function `main':
    install.c:(.text.startup+0x56e): warning: Using 'getgrnam' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    install.c:(.text.startup+0x585): warning: Using 'endgrent' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: Dwarf Error: found dwarf version '7424', this reader only handles version 2, 3 and 4 information.
    install.c:(.text.startup+0x542): warning: Using 'getpwnam' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    install.c:(.text.startup+0x559): warning: Using 'endpwent' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: Dwarf Error: found dwarf version '5', this reader only handles version 2, 3 and 4 information.
    lib/libcoreutils.a(mgetgroups.o): In function `mgetgroups':
    mgetgroups.c:(.text+0x8b): warning: Using 'getgrouplist' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: Dwarf Error: found dwarf version '5', this reader only handles version 2, 3 and 4 information.
    src/group-list.o: In function `print_group':
    group-list.c:(.text+0x16): warning: Using 'getgrgid' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: Dwarf Error: found dwarf version '5', this reader only handles version 2, 3 and 4 information.
    src/groups.o: In function `main':
    groups.c:(.text.startup+0x16b): warning: Using 'getpwnam' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    src/group-list.o: In function `print_group_list':
    group-list.c:(.text+0xae): warning: Using 'getpwuid' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: Dwarf Error: found dwarf version '5', this reader only handles version 2, 3 and 4 information.
    lib/libcoreutils.a(mgetgroups.o): In function `mgetgroups':
    mgetgroups.c:(.text+0x8b): warning: Using 'getgrouplist' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: Dwarf Error: found dwarf version '5', this reader only handles version 2, 3 and 4 information.
    src/id.o: In function `print_stuff':
    id.c:(.text+0x236): warning: Using 'getgrgid' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: Dwarf Error: found dwarf version '5', this reader only handles version 2, 3 and 4 information.
    lib/libcoreutils.a(userspec.o): In function `parse_with_separator':
    userspec.c:(.text+0x85): warning: Using 'getgrnam' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    userspec.c:(.text+0x99): warning: Using 'endgrent' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: Dwarf Error: found dwarf version '7424', this reader only handles version 2, 3 and 4 information.
    userspec.c:(.text+0x13c): warning: Using 'getpwnam' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    src/id.o: In function `print_stuff':
    id.c:(.text+0xcb): warning: Using 'getpwuid' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    lib/libcoreutils.a(userspec.o): In function `parse_with_separator':
    userspec.c:(.text+0x15a): warning: Using 'endpwent' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: Dwarf Error: found dwarf version '5', this reader only handles version 2, 3 and 4 information.
    lib/libcoreutils.a(idcache.o): In function `getgroup':
    idcache.c:(.text+0x1f9): warning: Using 'getgrgid' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    lib/libcoreutils.a(idcache.o): In function `getgidbyname':
    idcache.c:(.text+0x2ec): warning: Using 'getgrnam' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: Dwarf Error: found dwarf version '7424', this reader only handles version 2, 3 and 4 information.
    lib/libcoreutils.a(idcache.o): In function `getuidbyname':
    idcache.c:(.text+0x13c): warning: Using 'getpwnam' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    lib/libcoreutils.a(idcache.o): In function `getuser':
    idcache.c:(.text+0x49): warning: Using 'getpwuid' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: Dwarf Error: found dwarf version '5', this reader only handles version 2, 3 and 4 information.
    src/stat.o: In function `print_stat':
    stat.c:(.text+0x1d73): warning: Using 'getgrgid' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    stat.c:(.text+0x1c4b): warning: Using 'getpwuid' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: Dwarf Error: found dwarf version '5', this reader only handles version 2, 3 and 4 information.
    lib/libcoreutils.a(idcache.o): In function `getgroup':
    idcache.c:(.text+0x1f9): warning: Using 'getgrgid' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    lib/libcoreutils.a(idcache.o): In function `getgidbyname':
    idcache.c:(.text+0x2ec): warning: Using 'getgrnam' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: Dwarf Error: found dwarf version '7424', this reader only handles version 2, 3 and 4 information.
    lib/libcoreutils.a(idcache.o): In function `getuidbyname':
    idcache.c:(.text+0x13c): warning: Using 'getpwnam' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    lib/libcoreutils.a(idcache.o): In function `getuser':
    idcache.c:(.text+0x49): warning: Using 'getpwuid' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /usr/bin/ld: Dwarf Error: found dwarf version '5', this reader only handles version 2, 3 and 4 information.
    src/whoami.o: In function `main':
    whoami.c:(.text.startup+0xa8): warning: Using 'getpwuid' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
  stderr_lines: <omitted>
  stdout: |2-
      GEN      lib/alloca.h
      GEN      lib/arpa/inet.h
      GEN      lib/configmake.h
      GEN      lib/ctype.h
      GEN      lib/dirent.h
      GEN      lib/malloc/dynarray.gl.h
      GEN      lib/malloc/dynarray-skeleton.gl.h
      GEN      lib/fcntl.h
      GEN      lib/iconv.h
      GEN      lib/inttypes.h
      GEN      lib/langinfo.h
      GEN      lib/limits.h
      GEN      lib/locale.h
      GEN      lib/math.h
      GEN      lib/netdb.h
      GEN      lib/pthread.h
      GEN      lib/sched.h
      GEN      lib/malloc/scratch_buffer.gl.h
      GEN      lib/selinux/selinux.h
      GEN      lib/selinux/context.h
      GEN      lib/selinux/label.h
      GEN      lib/signal.h
      GEN      lib/stdint.h
      GEN      lib/stdio.h
      GEN      lib/stdlib.h
      GEN      lib/string.h
      GEN      lib/sys/ioctl.h
      GEN      lib/sys/random.h
      GEN      lib/sys/resource.h
      GEN      lib/sys/select.h
      GEN      lib/sys/socket.h
      GEN      lib/sys/stat.h
      GEN      lib/sys/time.h
      GEN      lib/sys/types.h
      GEN      lib/sys/uio.h
      GEN      lib/sys/utsname.h
      GEN      lib/sys/wait.h
      GEN      lib/termios.h
      GEN      lib/time.h
      GEN      lib/unistd.h
      GEN      lib/unistr.h
      GEN      lib/unitypes.h
      GEN      lib/uniwidth.h
      GEN      lib/utime.h
      GEN      lib/wchar.h
      GEN      lib/wctype.h
      GEN      src/coreutils.h
      GEN      src/version.c
      GEN      src/version.h
    make  all-recursive
    make[1]: Entering directory `/glc/coreutils/source/coreutils-9.0/build_20250427T151610'
    Making all in po
    make[2]: Entering directory `/glc/coreutils/source/coreutils-9.0/build_20250427T151610/po'
    make[2]: Nothing to be done for `all'.
    make[2]: Leaving directory `/glc/coreutils/source/coreutils-9.0/build_20250427T151610/po'
    Making all in .
    make[2]: Entering directory `/glc/coreutils/source/coreutils-9.0/build_20250427T151610'
      CC       src/chroot.o
      CC       src/version.o
      AR       src/libver.a
      CC       lib/copy-acl.o
      CC       lib/set-acl.o
      CC       lib/acl-errno-valid.o
      CC       lib/acl-internal.o
      CC       lib/get-permissions.o
      CC       lib/set-permissions.o
      CC       lib/allocator.o
      CC       lib/areadlink.o
      CC       lib/areadlink-with-size.o
      CC       lib/areadlinkat.o
      CC       lib/argmatch.o
      CC       lib/argv-iter.o
      CC       lib/openat-proc.o
      CC       lib/backupfile.o
      CC       lib/backup-rename.o
      CC       lib/backup-find.o
      CC       lib/base32.o
      CC       lib/base64.o
      CC       lib/basename-lgpl.o
      CC       lib/binary-io.o
      CC       lib/bitrotate.o
      CC       lib/buffer-lcm.o
      CC       lib/c-ctype.o
      CC       lib/c-strcasecmp.o
      CC       lib/c-strncasecmp.o
      CC       lib/c-strtod.o
      CC       lib/c-strtold.o
      CC       lib/canon-host.o
      CC       lib/canonicalize.o
      CC       lib/careadlinkat.o
      CC       lib/chmodat.o
      CC       lib/chownat.o
      CC       lib/cl-strtod.o
      CC       lib/cl-strtold.o
      CC       lib/cloexec.o
      CC       lib/close-stream.o
      CC       lib/closein.o
      CC       lib/closeout.o
      CC       lib/count-leading-zeros.o
      CC       lib/af_alg.o
      CC       lib/md5-stream.o
      CC       lib/md5.o
      CC       lib/sha1-stream.o
      CC       lib/sha1.o
      CC       lib/sha256-stream.o
      CC       lib/sha256.o
      CC       lib/sha512-stream.o
      CC       lib/sha512.o
      CC       lib/sm3-stream.o
      CC       lib/sm3.o
      CC       lib/cycle-check.o
      CC       lib/di-set.o
      CC       lib/opendir-safer.o
      CC       lib/dirname.o
      CC       lib/basename.o
      CC       lib/dirname-lgpl.o
      CC       lib/stripslash.o
      CC       lib/dtoastr.o
      CC       lib/dtotimespec.o
      CC       lib/malloc/dynarray_at_failure.o
      CC       lib/malloc/dynarray_emplace_enlarge.o
      CC       lib/malloc/dynarray_finalize.o
      CC       lib/malloc/dynarray_resize.o
      CC       lib/malloc/dynarray_resize_clear.o
      CC       lib/exclude.o
      CC       lib/exitfail.o
      CC       lib/fadvise.o
      CC       lib/creat-safer.o
      CC       lib/open-safer.o
      CC       lib/fd-hook.o
      CC       lib/fd-reopen.o
      CC       lib/fd-safer-flag.o
      CC       lib/dup-safer-flag.o
      CC       lib/fdutimensat.o
      CC       lib/file-has-acl.o
      CC       lib/file-set.o
      CC       lib/file-type.o
      CC       lib/filemode.o
      CC       lib/filenamecat.o
      CC       lib/filenamecat-lgpl.o
      CC       lib/filevercmp.o
      CC       lib/fopen-safer.o
      CC       lib/fprintftime.o
      CC       lib/freading.o
      CC       lib/freadseek.o
      CC       lib/freopen-safer.o
      CC       lib/ftoastr.o
      CC       lib/full-read.o
      CC       lib/full-write.o
      CC       lib/gethrxtime.o
      CC       lib/xtime.o
      CC       lib/getndelim2.o
      CC       lib/getprogname.o
      CC       lib/gettime.o
      CC       lib/getugroups.o
      CC       lib/hard-locale.o
      CC       lib/hash.o
      CC       lib/hash-pjw.o
      CC       lib/hash-triple.o
      CC       lib/hash-triple-simple.o
      CC       lib/heap.o
      CC       lib/human.o
      CC       lib/i-ring.o
      CC       lib/ialloc.o
      CC       lib/idcache.o
      CC       lib/ino-map.o
      CC       lib/imaxtostr.o
      CC       lib/inttostr.o
      CC       lib/offtostr.o
      CC       lib/uinttostr.o
      CC       lib/umaxtostr.o
      CC       lib/ldtoastr.o
      CC       lib/linebuffer.o
      CC       lib/localcharset.o
      CC       lib/glthread/lock.o
      CC       lib/long-options.o
      CC       lib/malloca.o
      CC       lib/math.o
      CC       lib/mbchar.o
      CC       lib/mbiter.o
      CC       lib/mbsalign.o
      CC       lib/mbscasecmp.o
      CC       lib/mbschr.o
      CC       lib/mbslen.o
      CC       lib/mbsstr.o
      CC       lib/mbswidth.o
      CC       lib/mbuiter.o
      CC       lib/memcasecmp.o
      CC       lib/memchr2.o
      CC       lib/memcmp2.o
      CC       lib/memcoll.o
      CC       lib/mgetgroups.o
      CC       lib/mkancesdirs.o
      CC       lib/dirchownmod.o
      CC       lib/mkdir-p.o
      CC       lib/modechange.o
      CC       lib/mpsort.o
      CC       lib/nproc.o
      CC       lib/nstrftime.o
      CC       lib/openat-die.o
      CC       lib/openat-safer.o
      CC       lib/opendirat.o
      CC       lib/parse-datetime.o
      CC       lib/physmem.o
      CC       lib/pipe2.o
      CC       lib/posixtm.o
      CC       lib/posixver.o
      CC       lib/printf-frexp.o
      CC       lib/printf-frexpl.o
      CC       lib/priv-set.o
      CC       lib/progname.o
      CC       lib/propername.o
      CC       lib/qcopy-acl.o
      CC       lib/qset-acl.o
      CC       lib/quotearg.o
      CC       lib/randint.o
      CC       lib/randperm.o
      CC       lib/randread.o
      CC       lib/rand-isaac.o
      CC       lib/read-file.o
      CC       lib/readtokens.o
      CC       lib/readtokens0.o
      CC       lib/renameatu.o
      CC       lib/root-dev-ino.o
      CC       lib/safe-read.o
      CC       lib/safe-write.o
      CC       lib/same.o
      CC       lib/save-cwd.o
      CC       lib/savedir.o
      CC       lib/savewd.o
      CC       lib/malloc/scratch_buffer_dupfree.o
      CC       lib/malloc/scratch_buffer_grow.o
      CC       lib/malloc/scratch_buffer_grow_preserve.o
      CC       lib/malloc/scratch_buffer_set_array_size.o
      CC       lib/selinux-at.o
      CC       lib/se-context.o
      CC       lib/se-label.o
      CC       lib/se-selinux.o
      CC       lib/setlocale_null.o
      CC       lib/settime.o
      CC       lib/sig-handler.o
      CC       lib/sockets.o
      CC       lib/stat-time.o
      CC       lib/statat.o
      CC       lib/mkstemp-safer.o
      CC       lib/striconv.o
      CC       lib/strnlen1.o
      CC       lib/strintcmp.o
      CC       lib/strnumcmp.o
      CC       lib/sys_socket.o
      CC       lib/tempname.o
      CC       lib/glthread/threadlib.o
      CC       lib/timespec.o
      CC       lib/glthread/tls.o
      CC       lib/trim.o
      CC       lib/u64.o
      CC       lib/unicodeio.o
      CC       lib/unistd.o
      CC       lib/dup-safer.o
      CC       lib/fd-safer.o
      CC       lib/pipe-safer.o
      CC       lib/unistr/u8-mbtoucr.o
      CC       lib/unistr/u8-uctomb.o
      CC       lib/unistr/u8-uctomb-aux.o
      CC       lib/uniwidth/width.o
      CC       lib/unlinkdir.o
      CC       lib/userspec.o
      CC       lib/utimecmp.o
      CC       lib/utimens.o
      CC       lib/verror.o
      CC       lib/version-etc.o
      CC       lib/version-etc-fsf.o
      CC       lib/wctype-h.o
      CC       lib/write-any-file.o
      CC       lib/xmalloc.o
      CC       lib/xalloc-die.o
      CC       lib/xbinary-io.o
      CC       lib/xdectoimax.o
      CC       lib/xdectoumax.o
      CC       lib/xfts.o
      CC       lib/xgetcwd.o
      CC       lib/xgetgroups.o
      CC       lib/xgethostname.o
      CC       lib/xmemcoll.o
      CC       lib/xnanosleep.o
      CC       lib/xprintf.o
      CC       lib/xreadlink.o
      CC       lib/xsize.o
      CC       lib/xstriconv.o
      CC       lib/xstrtod.o
      CC       lib/xstrtoimax.o
      CC       lib/xstrtol.o
      CC       lib/xstrtoul.o
      CC       lib/xstrtol-error.o
      CC       lib/xstrtold.o
      CC       lib/xstrtoumax.o
      CC       lib/xvasprintf.o
      CC       lib/xasprintf.o
      CC       lib/yesno.o
      CC       lib/asnprintf.o
      CC       lib/chdir-long.o
      CC       lib/copy-file-range.o
      CC       lib/explicit_bzero.o
      CC       lib/fchmodat.o
      CC       lib/fclose.o
      CC       lib/fcntl.o
      CC       lib/fflush.o
      CC       lib/fpurge.o
      CC       lib/freadahead.o
      CC       lib/freadptr.o
      CC       lib/free.o
      CC       lib/freopen.o
      CC       lib/fseek.o
      CC       lib/fseeko.o
      CC       lib/fseterr.o
      CC       lib/fsusage.o
      CC       lib/fts.o
      CC       lib/getrandom.o
      CC       lib/isapipe.o
      CC       lib/lchmod.o
      CC       lib/mbrlen.o
      CC       lib/mbrtowc.o
      CC       lib/mknod.o
      CC       lib/mountlist.o
      CC       lib/nanosleep.o
      CC       lib/nl_langinfo.o
      CC       lib/obstack.o
      CC       lib/printf-args.o
      CC       lib/printf-parse.o
      CC       lib/readutmp.o
      CC       lib/reallocarray.o
      CC       lib/regex.o
      CC       lib/sig2str.o
      CC       lib/time_rz.o
      CC       lib/vasnprintf.o
      AR       lib/libcoreutils.a
      CCLD     src/chroot
      CC       src/hostid.o
      CCLD     src/hostid
      CC       src/timeout.o
      CC       src/operand2sig.o
      CCLD     src/timeout
      CC       src/nice.o
      CCLD     src/nice
      CC       src/who.o
      CCLD     src/who
      CC       src/users.o
      CCLD     src/users
      CC       src/pinky.o
      CCLD     src/pinky
      CC       src/uptime.o
      CCLD     src/uptime
      CC       src/stty.o
      CCLD     src/stty
      CC       src/df.o
      CC       src/find-mount-point.o
      CCLD     src/df
      CC       src/lbracket.o
      CCLD     src/[
      CC       src/b2sum-digest.o
      CC       src/blake2/b2sum-blake2b-ref.o
      CC       src/blake2/b2sum-b2sum.o
      CCLD     src/b2sum
      CC       src/base64-basenc.o
      CCLD     src/base64
      CC       src/base32-basenc.o
      CCLD     src/base32
      CC       src/basenc-basenc.o
      CCLD     src/basenc
      CC       src/basename.o
      CCLD     src/basename
      CC       src/cat.o
      CCLD     src/cat
      CC       src/chcon.o
      CCLD     src/chcon
      CC       src/chgrp.o
      CC       src/chown-core.o
      CCLD     src/chgrp
      CC       src/chmod.o
      CCLD     src/chmod
      CC       src/chown.o
      CCLD     src/chown
      CC       src/cksum-digest.o
      CC       src/blake2/cksum-blake2b-ref.o
      CC       src/blake2/cksum-b2sum.o
      CC       src/cksum-sum.o
      CC       src/cksum-cksum.o
      CC       src/cksum-crctab.o
      CC       src/libcksum_pclmul_a-cksum_pclmul.o
      AR       src/libcksum_pclmul.a
      CCLD     src/cksum
      CC       src/comm.o
      CCLD     src/comm
      CC       src/cp.o
      CC       src/copy.o
      CC       src/cp-hash.o
      CC       src/force-link.o
      CC       src/selinux.o
      CCLD     src/cp
      CC       src/csplit.o
      CCLD     src/csplit
      CC       src/cut.o
      CC       src/set-fields.o
      CCLD     src/cut
      CC       src/date.o
      CCLD     src/date
      CC       src/dd.o
      CCLD     src/dd
      CC       src/ls.o
      CC       src/ls-dir.o
      CCLD     src/dir
      CC       src/dircolors.o
      CCLD     src/dircolors
      CC       src/dirname.o
      CCLD     src/dirname
      CC       src/du.o
      CCLD     src/du
      CC       src/echo.o
      CCLD     src/echo
      CC       src/env.o
      CCLD     src/env
      CC       src/expand.o
      CC       src/expand-common.o
      CCLD     src/expand
      CC       src/expr.o
      CCLD     src/expr
      CC       src/factor.o
      CCLD     src/factor
      CC       src/false.o
      CCLD     src/false
      CC       src/fmt.o
      CCLD     src/fmt
      CC       src/fold.o
      CCLD     src/fold
      CC       src/install.o
      CC       src/prog-fprintf.o
      CCLD     src/ginstall
      CC       src/groups.o
      CC       src/group-list.o
      CCLD     src/groups
      CC       src/head.o
      CCLD     src/head
      CC       src/id.o
      CCLD     src/id
      CC       src/join.o
      CCLD     src/join
      CC       src/kill.o
      CCLD     src/kill
      CC       src/link.o
      CCLD     src/link
      CC       src/ln.o
      CC       src/relpath.o
      CCLD     src/ln
      CC       src/logname.o
      CCLD     src/logname
      CC       src/ls-ls.o
      CCLD     src/ls
      CC       src/md5sum-digest.o
      CCLD     src/md5sum
      CC       src/mkdir.o
      CCLD     src/mkdir
      CC       src/mkfifo.o
      CCLD     src/mkfifo
      CC       src/mknod.o
      CCLD     src/mknod
      CC       src/mktemp.o
      CCLD     src/mktemp
      CC       src/mv.o
      CC       src/remove.o
      CCLD     src/mv
      CC       src/nl.o
      CCLD     src/nl
      CC       src/nproc.o
      CCLD     src/nproc
      CC       src/nohup.o
      CCLD     src/nohup
      CC       src/numfmt.o
      CCLD     src/numfmt
      CC       src/od.o
      CCLD     src/od
      CC       src/paste.o
      CCLD     src/paste
      CC       src/pathchk.o
      CCLD     src/pathchk
      CC       src/pr.o
      CCLD     src/pr
      CC       src/printenv.o
      CCLD     src/printenv
      CC       src/printf.o
      CCLD     src/printf
      CC       src/ptx.o
      CCLD     src/ptx
      CC       src/pwd.o
      CCLD     src/pwd
      CC       src/readlink.o
      CCLD     src/readlink
      CC       src/realpath.o
      CCLD     src/realpath
      CC       src/rm.o
      CCLD     src/rm
      CC       src/rmdir.o
      CCLD     src/rmdir
      CC       src/runcon.o
      CCLD     src/runcon
      CC       src/seq.o
      CCLD     src/seq
      CC       src/sha1sum-digest.o
      CCLD     src/sha1sum
      CC       src/sha224sum-digest.o
      CCLD     src/sha224sum
      CC       src/sha256sum-digest.o
      CCLD     src/sha256sum
      CC       src/sha384sum-digest.o
      CCLD     src/sha384sum
      CC       src/sha512sum-digest.o
      CCLD     src/sha512sum
      CC       src/shred.o
      CCLD     src/shred
      CC       src/shuf.o
      CCLD     src/shuf
      CC       src/sleep.o
      CCLD     src/sleep
      CC       src/sort.o
      CCLD     src/sort
      CC       src/split.o
      CCLD     src/split
      CC       src/stat.o
      CCLD     src/stat
      CC       src/sum-sum.o
      CC       src/sum-digest.o
      CCLD     src/sum
      CC       src/sync.o
      CCLD     src/sync
      CC       src/tac.o
      CCLD     src/tac
      CC       src/tail.o
      CCLD     src/tail
      CC       src/tee.o
      CCLD     src/tee
      CC       src/test.o
      CCLD     src/test
      CC       src/touch.o
      CCLD     src/touch
      CC       src/tr.o
      CCLD     src/tr
      CC       src/true.o
      CCLD     src/true
      CC       src/truncate.o
      CCLD     src/truncate
      CC       src/tsort.o
      CCLD     src/tsort
      CC       src/tty.o
      CCLD     src/tty
      CC       src/uname.o
      CC       src/uname-uname.o
      CCLD     src/uname
      CC       src/unexpand.o
      CCLD     src/unexpand
      CC       src/uniq.o
      CCLD     src/uniq
      CC       src/unlink.o
      CCLD     src/unlink
      CC       src/ls-vdir.o
      CCLD     src/vdir
      CC       src/wc.o
      CC       src/libwc_avx2_a-wc_avx2.o
      AR       src/libwc_avx2.a
      CCLD     src/wc
      CC       src/whoami.o
      CCLD     src/whoami
      CC       src/yes.o
      CCLD     src/yes
      CC       src/getlimits.o
      CCLD     src/getlimits
      CC       src/make-prime-list.o
      CCLD     src/make-prime-list
      GEN      man/chroot.1
      GEN      man/hostid.1
      GEN      man/timeout.1
      GEN      man/nice.1
      GEN      man/who.1
      GEN      man/users.1
      GEN      man/pinky.1
      GEN      man/uptime.1
      GEN      man/stty.1
      GEN      man/df.1
      GEN      man/b2sum.1
      GEN      man/base64.1
      GEN      man/base32.1
      GEN      man/basenc.1
      GEN      man/basename.1
      GEN      man/cat.1
      GEN      man/chcon.1
      GEN      man/chgrp.1
      GEN      man/chmod.1
      GEN      man/chown.1
      GEN      man/cksum.1
      GEN      man/comm.1
      GEN      man/cp.1
      GEN      man/csplit.1
      GEN      man/cut.1
      GEN      man/date.1
      GEN      man/dd.1
      GEN      man/dir.1
      GEN      man/dircolors.1
      GEN      man/dirname.1
      GEN      man/du.1
      GEN      man/echo.1
      GEN      man/env.1
      GEN      man/expand.1
      GEN      man/expr.1
      GEN      man/factor.1
      GEN      man/false.1
      GEN      man/fmt.1
      GEN      man/fold.1
      GEN      man/install.1
      GEN      man/groups.1
      GEN      man/head.1
      GEN      man/id.1
      GEN      man/join.1
      GEN      man/kill.1
      GEN      man/link.1
      GEN      man/ln.1
      GEN      man/logname.1
      GEN      man/ls.1
      GEN      man/md5sum.1
      GEN      man/mkdir.1
      GEN      man/mkfifo.1
      GEN      man/mknod.1
      GEN      man/mktemp.1
      GEN      man/mv.1
      GEN      man/nl.1
      GEN      man/nproc.1
      GEN      man/nohup.1
      GEN      man/numfmt.1
      GEN      man/od.1
      GEN      man/paste.1
      GEN      man/pathchk.1
      GEN      man/pr.1
      GEN      man/printenv.1
      GEN      man/printf.1
      GEN      man/ptx.1
      GEN      man/pwd.1
      GEN      man/readlink.1
      GEN      man/realpath.1
      GEN      man/rm.1
      GEN      man/rmdir.1
      GEN      man/runcon.1
      GEN      man/seq.1
      GEN      man/sha1sum.1
      GEN      man/sha224sum.1
      GEN      man/sha256sum.1
      GEN      man/sha384sum.1
      GEN      man/sha512sum.1
      GEN      man/shred.1
      GEN      man/shuf.1
      GEN      man/sleep.1
      GEN      man/sort.1
      GEN      man/split.1
      GEN      man/stat.1
      GEN      man/sum.1
      GEN      man/sync.1
      GEN      man/tac.1
      GEN      man/tail.1
      GEN      man/tee.1
      GEN      man/test.1
      GEN      man/touch.1
      GEN      man/tr.1
      GEN      man/true.1
      GEN      man/truncate.1
      GEN      man/tsort.1
      GEN      man/tty.1
      GEN      man/uname.1
      GEN      man/unexpand.1
      GEN      man/uniq.1
      GEN      man/unlink.1
      GEN      man/vdir.1
      GEN      man/wc.1
      GEN      man/whoami.1
      GEN      man/yes.1
    make[2]: Leaving directory `/glc/coreutils/source/coreutils-9.0/build_20250427T151610'
    Making all in gnulib-tests
    make[2]: Entering directory `/glc/coreutils/source/coreutils-9.0/build_20250427T151610/gnulib-tests'
    ## ---------------------------------------------------- ##
    ## ------------------- Gnulib tests ------------------- ##
    ## You can ignore compiler warnings in this directory.  ##
    ## ---------------------------------------------------- ##
    make  all-recursive
    make[3]: Entering directory `/glc/coreutils/source/coreutils-9.0/build_20250427T151610/gnulib-tests'
    Making all in .
    make[4]: Entering directory `/glc/coreutils/source/coreutils-9.0/build_20250427T151610/gnulib-tests'
      CC       bench-md5.o
      CC       localename.o
      CC       localename-table.o
      CC       glthread/thread.o
      CC       timespec-add.o
      CC       timespec-sub.o
      CC       tmpdir.o
      CC       vma-iter.o
      CC       ioctl.o
      CC       strerror_r.o
      AR       libtests.a
      CCLD     bench-md5
      CC       bench-sha1.o
      CCLD     bench-sha1
      CC       bench-sha224.o
      CCLD     bench-sha224
      CC       bench-sha256.o
      CCLD     bench-sha256
      CC       bench-sha384.o
      CCLD     bench-sha384
      CC       bench-sha512.o
      CCLD     bench-sha512
      CC       locale.o
      CCLD     current-locale
      CC       test-localcharset.o
      CCLD     test-localcharset
    make[4]: Leaving directory `/glc/coreutils/source/coreutils-9.0/build_20250427T151610/gnulib-tests'
    make[3]: Leaving directory `/glc/coreutils/source/coreutils-9.0/build_20250427T151610/gnulib-tests'
    make[2]: Leaving directory `/glc/coreutils/source/coreutils-9.0/build_20250427T151610/gnulib-tests'
    make[1]: Leaving directory `/glc/coreutils/source/coreutils-9.0/build_20250427T151610'
  stdout_lines: <omitted>
2025-04-27 15:21:50,326 p=275855 u=guolicheng n=ansible | TASK [coreutils : cd /glc/coreutils/source/coreutils-9.0/build_20250427T151610;make install] ********************************************************************************************************************************************************************************************
2025-04-27 15:21:50,355 p=275855 u=guolicheng n=ansible | changed: [node-250] => changed=true


```


#### undefined reference to

```bash
    /glc/binutils/app/binutils-2.40/bin/ld: src/chown-core.o: in function `uid_to_name':
    /glc/coreutils/source/coreutils-9.0/build_20250427T171659/../src/chown-core.c:98: warning: Using 'getpwuid' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /glc/binutils/app/binutils-2.40/bin/ld: lib/libcoreutils.a(userspec.o): in function `parse_with_separator':
    /glc/coreutils/source/coreutils-9.0/build_20250427T171659/../lib/userspec.c:195: warning: Using 'endpwent' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /glc/binutils/app/binutils-2.40/bin/ld: /lib/../lib64/libselinux.a(seusers.o): in function `getseuserbyname':
    (.text+0x518): warning: Using 'getgrouplist' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /glc/binutils/app/binutils-2.40/bin/ld: (.text+0x4f0): warning: Using 'getgrnam_r' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /glc/binutils/app/binutils-2.40/bin/ld: (.text+0x7c): warning: Using 'getpwnam_r' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /glc/binutils/app/binutils-2.40/bin/ld: /lib/../lib64/libselinux.a(procattr.o): in function `openattr':
    (.text+0xd4): undefined reference to `security_lsm_is_enabled'
    /glc/binutils/app/binutils-2.40/bin/ld: /lib/../lib64/libselinux.a(regex.o): in function `regex_version':
    (.text+0x80): undefined reference to `pcre2_config_8'
    /glc/binutils/app/binutils-2.40/bin/ld: (.text+0xac): undefined reference to `pcre2_config_8'
    /glc/binutils/app/binutils-2.40/bin/ld: /lib/../lib64/libselinux.a(regex.o): in function `regex_writef':
    (.text+0x114): undefined reference to `pcre2_serialize_encode_8'
    /glc/binutils/app/binutils-2.40/bin/ld: (.text+0x194): undefined reference to `pcre2_serialize_free_8'
    /glc/binutils/app/binutils-2.40/bin/ld: /lib/../lib64/libselinux.a(regex.o): in function `regex_data_free':
    (.text+0x1e4): undefined reference to `pcre2_code_free_8'
    /glc/binutils/app/binutils-2.40/bin/ld: (.text+0x1f0): undefined reference to `pcre2_match_data_free_8'
    /glc/binutils/app/binutils-2.40/bin/ld: /lib/../lib64/libselinux.a(regex.o): in function `regex_match':
    (.text+0x270): undefined reference to `pcre2_match_8'
    /glc/binutils/app/binutils-2.40/bin/ld: /lib/../lib64/libselinux.a(regex.o): in function `regex_cmp':
    (.text+0x308): undefined reference to `pcre2_pattern_info_8'
    /glc/binutils/app/binutils-2.40/bin/ld: (.text+0x31c): undefined reference to `pcre2_pattern_info_8'
    /glc/binutils/app/binutils-2.40/bin/ld: /lib/../lib64/libselinux.a(regex.o): in function `regex_prepare_data':
    (.text+0x454): undefined reference to `pcre2_compile_8'
    /glc/binutils/app/binutils-2.40/bin/ld: (.text+0x46c): undefined reference to `pcre2_match_data_create_from_pattern_8'
    /glc/binutils/app/binutils-2.40/bin/ld: /lib/../lib64/libselinux.a(regex.o): in function `regex_load_mmap':
    (.text+0x534): undefined reference to `pcre2_serialize_get_number_of_codes_8'
    /glc/binutils/app/binutils-2.40/bin/ld: (.text+0x55c): undefined reference to `pcre2_serialize_decode_8'
    /glc/binutils/app/binutils-2.40/bin/ld: (.text+0x57c): undefined reference to `pcre2_match_data_create_from_pattern_8'
    /glc/binutils/app/binutils-2.40/bin/ld: /lib/../lib64/libselinux.a(regex.o): in function `regex_format_error':
    (.text+0x654): undefined reference to `pcre2_get_error_message_8'
    /glc/binutils/app/binutils-2.40/bin/ld: /lib/../lib64/libselinux.a(load_policy.o): in function `selinux_mkload_policy':
    (.text+0x15c): undefined reference to `sepol_policy_kern_vers_max'
    /glc/binutils/app/binutils-2.40/bin/ld: (.text+0x168): undefined reference to `sepol_policy_kern_vers_min'
    /glc/binutils/app/binutils-2.40/bin/ld: (.text+0x300): undefined reference to `sepol_policy_kern_vers_max'
    /glc/binutils/app/binutils-2.40/bin/ld: (.text+0x308): undefined reference to `sepol_policy_kern_vers_min'
    /glc/binutils/app/binutils-2.40/bin/ld: (.text+0x334): undefined reference to `sepol_policy_kern_vers_max'
    /glc/binutils/app/binutils-2.40/bin/ld: (.text+0x33c): undefined reference to `sepol_policy_kern_vers_min'
    /glc/binutils/app/binutils-2.40/bin/ld: (.text+0x398): undefined reference to `sepol_policy_file_create'
    /glc/binutils/app/binutils-2.40/bin/ld: (.text+0x3a4): undefined reference to `sepol_policydb_create'
    /glc/binutils/app/binutils-2.40/bin/ld: (.text+0x3b4): undefined reference to `sepol_policy_file_set_mem'
    /glc/binutils/app/binutils-2.40/bin/ld: (.text+0x3bc): undefined reference to `sepol_policydb_read'
    /glc/binutils/app/binutils-2.40/bin/ld: (.text+0x3cc): undefined reference to `sepol_policydb_set_vers'
    /glc/binutils/app/binutils-2.40/bin/ld: (.text+0x3e0): undefined reference to `sepol_policydb_to_image'
    /glc/binutils/app/binutils-2.40/bin/ld: (.text+0x410): undefined reference to `sepol_policy_file_free'
    /glc/binutils/app/binutils-2.40/bin/ld: (.text+0x418): undefined reference to `sepol_policydb_free'
    /glc/binutils/app/binutils-2.40/bin/ld: (.text+0x44c): undefined reference to `sepol_policy_kern_vers_max'
    /glc/binutils/app/binutils-2.40/bin/ld: (.text+0x458): undefined reference to `sepol_policy_kern_vers_min'
    /glc/binutils/app/binutils-2.40/bin/ld: (.text+0x468): undefined reference to `sepol_policy_file_free'
    /glc/binutils/app/binutils-2.40/bin/ld: (.text+0x470): undefined reference to `sepol_policydb_free'
    /glc/binutils/app/binutils-2.40/bin/ld: (.text+0x578): undefined reference to `sepol_policy_file_free'
    /glc/binutils/app/binutils-2.40/bin/ld: (.text+0x588): undefined reference to `sepol_policy_file_free'
    /glc/binutils/app/binutils-2.40/bin/ld: (.text+0x590): undefined reference to `sepol_policydb_free'
    /glc/binutils/app/binutils-2.40/bin/ld: (.text+0x67c): undefined reference to `sepol_genbools_array'
    /glc/binutils/app/binutils-2.40/bin/ld: (.text+0x6dc): undefined reference to `sepol_genusers'
    /glc/binutils/app/binutils-2.40/bin/ld: (.text+0x710): undefined reference to `sepol_genbools'
    collect2: error: ld returned 1 exit status
    make[2]: *** [Makefile:9437: src/cp] Error 1
    make[1]: *** [Makefile:13464: all-recursive] Error 1
    make: *** [Makefile:7386: all] Error 2

```

- glibc中缺少静态库




