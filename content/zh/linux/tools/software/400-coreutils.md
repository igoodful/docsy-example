---
title: coreutils
description: coreutils
date: 2025-03-22
weight: 40000
viz: true

---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


{{<note>}}
<!---->
coreutils 是一组开发工具，包括连接器、汇编器和其他用于目标文件和档案的工具。



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






## 问题记录




####  /glc/binutils/app/binutils-2.32/bin/ld: DWARF error: can't find .debug_ranges section



```bash
fatal: [node2]: FAILED! => changed=true 
  cmd: make -j1
  delta: '0:01:53.176590'
  end: '2025-04-15 21:25:44.860802'
  msg: non-zero return code
  rc: 2
  start: '2025-04-15 21:23:51.684212'
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
    /glc/binutils/app/binutils-2.32/bin/ld: /glc/binutils/app/binutils-2.32/bin/ld: DWARF error: can't find .debug_ranges section.
    lib/libcoreutils.a(mgetgroups.o): in function `mgetgroups':
    mgetgroups.c:(.text+0x80): warning: Using 'getgrouplist' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /glc/binutils/app/binutils-2.32/bin/ld: lib/libcoreutils.a(userspec.o): in function `parse_with_separator':
    /glc/coreutils/source/coreutils-9.0/build_20250415T211827/../lib/userspec.c:190: warning: Using 'getgrgid' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /glc/binutils/app/binutils-2.32/bin/ld: /glc/binutils/app/binutils-2.32/bin/ld: DWARF error: can't find .debug_ranges section.
    src/chroot.o: in function `parse_additional_groups':
    chroot.c:(.text+0x98): warning: Using 'getgrnam' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /glc/binutils/app/binutils-2.32/bin/ld: lib/libcoreutils.a(userspec.o): in function `parse_with_separator':
    /glc/coreutils/source/coreutils-9.0/build_20250415T211827/../lib/userspec.c:214: warning: Using 'endgrent' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /glc/binutils/app/binutils-2.32/bin/ld: /glc/coreutils/source/coreutils-9.0/build_20250415T211827/../lib/userspec.c:161: warning: Using 'getpwnam' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /glc/binutils/app/binutils-2.32/bin/ld: src/chroot.o: in function `main':
    chroot.c:(.text.startup+0x340): warning: Using 'getpwuid' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /glc/binutils/app/binutils-2.32/bin/ld: lib/libcoreutils.a(userspec.o): in function `parse_with_separator':
    /glc/coreutils/source/coreutils-9.0/build_20250415T211827/../lib/userspec.c:195: warning: Using 'endpwent' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /glc/binutils/app/binutils-2.32/bin/ld: lib/libcoreutils.a(canon-host.o): in function `canon_host_r':
    /glc/coreutils/source/coreutils-9.0/build_20250415T211827/../lib/canon-host.c:69: warning: Using 'getaddrinfo' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /glc/binutils/app/binutils-2.32/bin/ld: /glc/binutils/app/binutils-2.32/bin/ld: DWARF error: can't find .debug_ranges section.
    src/pinky.o: in function `print_long_entry':
    pinky.c:(.text+0x144): warning: Using 'getpwnam' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /glc/binutils/app/binutils-2.32/bin/ld: lib/libcoreutils.a(canon-host.o): in function `canon_host_r':
    /glc/coreutils/source/coreutils-9.0/build_20250415T211827/../lib/canon-host.c:69: warning: Using 'getaddrinfo' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /glc/binutils/app/binutils-2.32/bin/ld: /glc/binutils/app/binutils-2.32/bin/ld: DWARF error: can't find .debug_ranges section.
    src/chown-core.o: in function `gid_to_name':
    chown-core.c:(.text+0xf0): warning: Using 'getgrgid' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /glc/binutils/app/binutils-2.32/bin/ld: /glc/binutils/app/binutils-2.32/bin/ld: DWARF error: can't find .debug_ranges section.
    src/chgrp.o: in function `main':
    chgrp.c:(.text.startup+0x2fc): warning: Using 'getgrnam' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /glc/binutils/app/binutils-2.32/bin/ld: chgrp.c:(.text.startup+0x308): warning: Using 'endgrent' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /glc/binutils/app/binutils-2.32/bin/ld: src/chown-core.o: in function `uid_to_name':
    chown-core.c:(.text+0x140): warning: Using 'getpwuid' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /glc/binutils/app/binutils-2.32/bin/ld: /glc/binutils/app/binutils-2.32/bin/ld: DWARF error: can't find .debug_ranges section.
    src/chown-core.o: in function `gid_to_name':
    chown-core.c:(.text+0xf0): warning: Using 'getgrgid' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /glc/binutils/app/binutils-2.32/bin/ld: lib/libcoreutils.a(userspec.o): in function `parse_with_separator':
    /glc/coreutils/source/coreutils-9.0/build_20250415T211827/../lib/userspec.c:202: warning: Using 'getgrnam' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /glc/binutils/app/binutils-2.32/bin/ld: /glc/coreutils/source/coreutils-9.0/build_20250415T211827/../lib/userspec.c:214: warning: Using 'endgrent' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /glc/binutils/app/binutils-2.32/bin/ld: /glc/coreutils/source/coreutils-9.0/build_20250415T211827/../lib/userspec.c:161: warning: Using 'getpwnam' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /glc/binutils/app/binutils-2.32/bin/ld: src/chown-core.o: in function `uid_to_name':
    chown-core.c:(.text+0x140): warning: Using 'getpwuid' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /glc/binutils/app/binutils-2.32/bin/ld: lib/libcoreutils.a(userspec.o): in function `parse_with_separator':
    /glc/coreutils/source/coreutils-9.0/build_20250415T211827/../lib/userspec.c:195: warning: Using 'endpwent' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /glc/binutils/app/binutils-2.32/bin/ld: /lib/../lib64/libselinux.a(seusers.o): in function `getseuserbyname':
    (.text+0x518): warning: Using 'getgrouplist' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /glc/binutils/app/binutils-2.32/bin/ld: (.text+0x4f0): warning: Using 'getgrnam_r' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /glc/binutils/app/binutils-2.32/bin/ld: (.text+0x7c): warning: Using 'getpwnam_r' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
    /glc/binutils/app/binutils-2.32/bin/ld: /lib/../lib64/libselinux.a(procattr.o): in function `openattr':
    (.text+0xd4): undefined reference to `security_lsm_is_enabled'
    /glc/binutils/app/binutils-2.32/bin/ld: /lib/../lib64/libselinux.a(regex.o): in function `regex_version':
    (.text+0x80): undefined reference to `pcre2_config_8'
    /glc/binutils/app/binutils-2.32/bin/ld: (.text+0xac): undefined reference to `pcre2_config_8'
    /glc/binutils/app/binutils-2.32/bin/ld: /lib/../lib64/libselinux.a(regex.o): in function `regex_writef':
    (.text+0x114): undefined reference to `pcre2_serialize_encode_8'
    /glc/binutils/app/binutils-2.32/bin/ld: (.text+0x194): undefined reference to `pcre2_serialize_free_8'
    /glc/binutils/app/binutils-2.32/bin/ld: /lib/../lib64/libselinux.a(regex.o): in function `regex_data_free':
    (.text+0x1e4): undefined reference to `pcre2_code_free_8'
    /glc/binutils/app/binutils-2.32/bin/ld: (.text+0x1f0): undefined reference to `pcre2_match_data_free_8'
    /glc/binutils/app/binutils-2.32/bin/ld: /lib/../lib64/libselinux.a(regex.o): in function `regex_match':
    (.text+0x270): undefined reference to `pcre2_match_8'
    /glc/binutils/app/binutils-2.32/bin/ld: /lib/../lib64/libselinux.a(regex.o): in function `regex_cmp':
    (.text+0x308): undefined reference to `pcre2_pattern_info_8'
    /glc/binutils/app/binutils-2.32/bin/ld: (.text+0x31c): undefined reference to `pcre2_pattern_info_8'
    /glc/binutils/app/binutils-2.32/bin/ld: /lib/../lib64/libselinux.a(regex.o): in function `regex_prepare_data':
    (.text+0x454): undefined reference to `pcre2_compile_8'
    /glc/binutils/app/binutils-2.32/bin/ld: (.text+0x46c): undefined reference to `pcre2_match_data_create_from_pattern_8'
    /glc/binutils/app/binutils-2.32/bin/ld: /lib/../lib64/libselinux.a(regex.o): in function `regex_load_mmap':
    (.text+0x534): undefined reference to `pcre2_serialize_get_number_of_codes_8'
    /glc/binutils/app/binutils-2.32/bin/ld: (.text+0x55c): undefined reference to `pcre2_serialize_decode_8'
    /glc/binutils/app/binutils-2.32/bin/ld: (.text+0x57c): undefined reference to `pcre2_match_data_create_from_pattern_8'
    /glc/binutils/app/binutils-2.32/bin/ld: /lib/../lib64/libselinux.a(regex.o): in function `regex_format_error':
    (.text+0x654): undefined reference to `pcre2_get_error_message_8'
    /glc/binutils/app/binutils-2.32/bin/ld: /lib/../lib64/libselinux.a(load_policy.o): in function `selinux_mkload_policy':
    (.text+0x15c): undefined reference to `sepol_policy_kern_vers_max'
    /glc/binutils/app/binutils-2.32/bin/ld: (.text+0x168): undefined reference to `sepol_policy_kern_vers_min'
    /glc/binutils/app/binutils-2.32/bin/ld: (.text+0x300): undefined reference to `sepol_policy_kern_vers_max'
    /glc/binutils/app/binutils-2.32/bin/ld: (.text+0x308): undefined reference to `sepol_policy_kern_vers_min'
    /glc/binutils/app/binutils-2.32/bin/ld: (.text+0x334): undefined reference to `sepol_policy_kern_vers_max'
    /glc/binutils/app/binutils-2.32/bin/ld: (.text+0x33c): undefined reference to `sepol_policy_kern_vers_min'
    /glc/binutils/app/binutils-2.32/bin/ld: (.text+0x398): undefined reference to `sepol_policy_file_create'
    /glc/binutils/app/binutils-2.32/bin/ld: (.text+0x3a4): undefined reference to `sepol_policydb_create'
    /glc/binutils/app/binutils-2.32/bin/ld: (.text+0x3b4): undefined reference to `sepol_policy_file_set_mem'
    /glc/binutils/app/binutils-2.32/bin/ld: (.text+0x3bc): undefined reference to `sepol_policydb_read'
    /glc/binutils/app/binutils-2.32/bin/ld: (.text+0x3cc): undefined reference to `sepol_policydb_set_vers'
    /glc/binutils/app/binutils-2.32/bin/ld: (.text+0x3e0): undefined reference to `sepol_policydb_to_image'
    /glc/binutils/app/binutils-2.32/bin/ld: (.text+0x410): undefined reference to `sepol_policy_file_free'
    /glc/binutils/app/binutils-2.32/bin/ld: (.text+0x418): undefined reference to `sepol_policydb_free'
    /glc/binutils/app/binutils-2.32/bin/ld: (.text+0x44c): undefined reference to `sepol_policy_kern_vers_max'
    /glc/binutils/app/binutils-2.32/bin/ld: (.text+0x458): undefined reference to `sepol_policy_kern_vers_min'
    /glc/binutils/app/binutils-2.32/bin/ld: (.text+0x468): undefined reference to `sepol_policy_file_free'
    /glc/binutils/app/binutils-2.32/bin/ld: (.text+0x470): undefined reference to `sepol_policydb_free'
    /glc/binutils/app/binutils-2.32/bin/ld: (.text+0x578): undefined reference to `sepol_policy_file_free'
    /glc/binutils/app/binutils-2.32/bin/ld: (.text+0x588): undefined reference to `sepol_policy_file_free'
    /glc/binutils/app/binutils-2.32/bin/ld: (.text+0x590): undefined reference to `sepol_policydb_free'
    /glc/binutils/app/binutils-2.32/bin/ld: (.text+0x67c): undefined reference to `sepol_genbools_array'
    /glc/binutils/app/binutils-2.32/bin/ld: (.text+0x6dc): undefined reference to `sepol_genusers'
    /glc/binutils/app/binutils-2.32/bin/ld: (.text+0x710): undefined reference to `sepol_genbools'
    collect2: error: ld returned 1 exit status
    make[2]: *** [Makefile:9437: src/cp] Error 1
    make[1]: *** [Makefile:13464: all-recursive] Error 1
    make: *** [Makefile:7386: all] Error 2


```

- https://github.com/triton-lang/triton/issues/1230
- https://gcc.gnu.org/bugzilla/show_bug.cgi?id=100725









