---
title: 15. strace
description: strace
date: 2025-03-24
weight: 150
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}



{{< /alert >}}


## 1. 启动要跟踪的进程

```bash
2025-03-24T17:01:23 [root@x86centos7-136 /root] # strace -tt -T -f   mysql --login-path=suplogin_local
17:01:30.089743 execve("/usr/bin/mysql", ["mysql", "--login-path=suplogin_local"], [/* 24 vars */]) = 0 <0.000640>
17:01:30.090937 brk(NULL)               = 0x131b000 <0.000044>
17:01:30.091147 mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7ff4a462c000 <0.000044>
17:01:30.091315 access("/etc/ld.so.preload", R_OK) = -1 ENOENT (No such file or directory) <0.000040>
17:01:30.091503 open("/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3 <0.000036>
17:01:30.091633 fstat(3, {st_mode=S_IFREG|0644, st_size=91027, ...}) = 0 <0.000030>
17:01:30.091777 mmap(NULL, 91027, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7ff4a4615000 <0.000043>
17:01:30.091862 close(3)                = 0 <0.000027>
17:01:30.091959 open("/lib64/libpthread.so.0", O_RDONLY|O_CLOEXEC) = 3 <0.000046>
17:01:30.092052 read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\260l\0\0\0\0\0\0"..., 832) = 832 <0.000032>
17:01:30.092150 fstat(3, {st_mode=S_IFREG|0755, st_size=141968, ...}) = 0 <0.000028>
17:01:30.092226 mmap(NULL, 2208904, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7ff4a41f0000 <0.000036>
17:01:30.092302 mprotect(0x7ff4a4207000, 2093056, PROT_NONE) = 0 <0.000037>
17:01:30.092390 mmap(0x7ff4a4406000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x16000) = 0x7ff4a4406000 <0.000038>
17:01:30.092478 mmap(0x7ff4a4408000, 13448, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7ff4a4408000 <0.000031>
17:01:30.092576 close(3)                = 0 <0.000027>
17:01:30.092655 open("/lib64/librt.so.1", O_RDONLY|O_CLOEXEC) = 3 <0.000037>
17:01:30.092735 read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\340!\0\0\0\0\0\0"..., 832) = 832 <0.000029>
17:01:30.092816 fstat(3, {st_mode=S_IFREG|0755, st_size=43776, ...}) = 0 <0.000029>
17:01:30.092889 mmap(NULL, 2128920, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7ff4a3fe8000 <0.000036>
17:01:30.092965 mprotect(0x7ff4a3fef000, 2093056, PROT_NONE) = 0 <0.000038>
17:01:30.093041 mmap(0x7ff4a41ee000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x6000) = 0x7ff4a41ee000 <0.000034>
17:01:30.093137 close(3)                = 0 <0.000027>
17:01:30.093228 open("/lib64/libdl.so.2", O_RDONLY|O_CLOEXEC) = 3 <0.000037>
17:01:30.093306 read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\220\r\0\0\0\0\0\0"..., 832) = 832 <0.000029>
17:01:30.093380 fstat(3, {st_mode=S_IFREG|0755, st_size=19288, ...}) = 0 <0.000033>
17:01:30.093460 mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7ff4a4614000 <0.000052>
17:01:30.093561 mmap(NULL, 2109712, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7ff4a3de4000 <0.000035>
17:01:30.093637 mprotect(0x7ff4a3de6000, 2097152, PROT_NONE) = 0 <0.000040>
17:01:30.093716 mmap(0x7ff4a3fe6000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x2000) = 0x7ff4a3fe6000 <0.000033>
17:01:30.093825 close(3)                = 0 <0.000028>
17:01:30.093904 open("/lib64/libncurses.so.5", O_RDONLY|O_CLOEXEC) = 3 <0.000042>
17:01:30.093991 read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0Pm\0\0\0\0\0\0"..., 832) = 832 <0.000058>
17:01:30.094102 fstat(3, {st_mode=S_IFREG|0755, st_size=163696, ...}) = 0 <0.000028>
17:01:30.094174 mmap(NULL, 2254920, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7ff4a3bbd000 <0.000031>
17:01:30.094246 mprotect(0x7ff4a3be3000, 2093056, PROT_NONE) = 0 <0.000034>
17:01:30.094319 mmap(0x7ff4a3de2000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x25000) = 0x7ff4a3de2000 <0.000031>
17:01:30.094428 close(3)                = 0 <0.000027>
17:01:30.094545 open("/lib64/libstdc++.so.6", O_RDONLY|O_CLOEXEC) = 3 <0.000064>
17:01:30.094654 read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\340\264\5\0\0\0\0\0"..., 832) = 832 <0.000046>
17:01:30.094746 fstat(3, {st_mode=S_IFREG|0755, st_size=995840, ...}) = 0 <0.000028>
17:01:30.094838 mmap(NULL, 3175456, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7ff4a38b5000 <0.000043>
17:01:30.094922 mprotect(0x7ff4a399e000, 2097152, PROT_NONE) = 0 <0.000054>
17:01:30.095015 mmap(0x7ff4a3b9e000, 40960, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0xe9000) = 0x7ff4a3b9e000 <0.000045>
17:01:30.095125 mmap(0x7ff4a3ba8000, 82976, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7ff4a3ba8000 <0.000031>
17:01:30.095224 close(3)                = 0 <0.000027>
17:01:30.095315 open("/lib64/libm.so.6", O_RDONLY|O_CLOEXEC) = 3 <0.000035>
17:01:30.095393 read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\20S\0\0\0\0\0\0"..., 832) = 832 <0.000032>
17:01:30.095475 fstat(3, {st_mode=S_IFREG|0755, st_size=1137016, ...}) = 0 <0.000039>
17:01:30.095559 mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7ff4a4613000 <0.000032>
17:01:30.095641 mmap(NULL, 3150120, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7ff4a35b3000 <0.000033>
17:01:30.095715 mprotect(0x7ff4a36b4000, 2093056, PROT_NONE) = 0 <0.000034>
17:01:30.095788 mmap(0x7ff4a38b3000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x100000) = 0x7ff4a38b3000 <0.000053>
17:01:30.095906 close(3)                = 0 <0.000032>
17:01:30.096005 open("/lib64/libgcc_s.so.1", O_RDONLY|O_CLOEXEC) = 3 <0.000039>
17:01:30.096091 read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\320*\0\0\0\0\0\0"..., 832) = 832 <0.000041>
17:01:30.096184 fstat(3, {st_mode=S_IFREG|0755, st_size=88720, ...}) = 0 <0.000031>
17:01:30.096263 mmap(NULL, 2184192, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7ff4a339d000 <0.000039>
17:01:30.096345 mprotect(0x7ff4a33b2000, 2093056, PROT_NONE) = 0 <0.000043>
17:01:30.096429 mmap(0x7ff4a35b1000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x14000) = 0x7ff4a35b1000 <0.000037>
17:01:30.096551 close(3)                = 0 <0.000034>
17:01:30.096643 open("/lib64/libc.so.6", O_RDONLY|O_CLOEXEC) = 3 <0.000041>
17:01:30.096731 read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\340$\2\0\0\0\0\0"..., 832) = 832 <0.000033>
17:01:30.096820 fstat(3, {st_mode=S_IFREG|0755, st_size=2151672, ...}) = 0 <0.000032>
17:01:30.096900 mmap(NULL, 3981792, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7ff4a2fd0000 <0.000037>
17:01:30.096980 mprotect(0x7ff4a3192000, 2097152, PROT_NONE) = 0 <0.000044>
17:01:30.097066 mmap(0x7ff4a3392000, 24576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x1c2000) = 0x7ff4a3392000 <0.000039>
17:01:30.097162 mmap(0x7ff4a3398000, 16864, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7ff4a3398000 <0.000034>
17:01:30.097256 close(3)                = 0 <0.000031>
17:01:30.097343 open("/lib64/libtinfo.so.5", O_RDONLY|O_CLOEXEC) = 3 <0.000052>
17:01:30.097439 read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0@\316\0\0\0\0\0\0"..., 832) = 832 <0.000048>
17:01:30.097539 fstat(3, {st_mode=S_IFREG|0755, st_size=174576, ...}) = 0 <0.000031>
17:01:30.097617 mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7ff4a4612000 <0.000033>
17:01:30.097702 mmap(NULL, 2268928, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7ff4a2da6000 <0.000037>
17:01:30.097781 mprotect(0x7ff4a2dcb000, 2097152, PROT_NONE) = 0 <0.000064>
17:01:30.097887 mmap(0x7ff4a2fcb000, 20480, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x25000) = 0x7ff4a2fcb000 <0.000036>
17:01:30.098015 close(3)                = 0 <0.000032>
17:01:30.098192 mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7ff4a4611000 <0.000033>
17:01:30.098288 mmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7ff4a460f000 <0.000031>
17:01:30.098378 arch_prctl(ARCH_SET_FS, 0x7ff4a460f740) = 0 <0.000031>
17:01:30.098756 mprotect(0x7ff4a3392000, 16384, PROT_READ) = 0 <0.000058>
17:01:30.099004 mprotect(0x7ff4a2fcb000, 16384, PROT_READ) = 0 <0.000039>
17:01:30.099103 mprotect(0x7ff4a35b1000, 4096, PROT_READ) = 0 <0.000042>
17:01:30.099248 mprotect(0x7ff4a38b3000, 4096, PROT_READ) = 0 <0.000041>
17:01:30.100631 mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7ff4a460e000 <0.000048>
17:01:30.100820 mprotect(0x7ff4a3b9e000, 32768, PROT_READ) = 0 <0.000042>
17:01:30.100918 mprotect(0x7ff4a3fe6000, 4096, PROT_READ) = 0 <0.000043>
17:01:30.101080 mprotect(0x7ff4a3de2000, 4096, PROT_READ) = 0 <0.000040>
17:01:30.101188 mprotect(0x7ff4a4406000, 4096, PROT_READ) = 0 <0.000042>
17:01:30.101286 mprotect(0x7ff4a41ee000, 4096, PROT_READ) = 0 <0.000043>
17:01:30.101673 mprotect(0x7ff4a462d000, 4096, PROT_READ) = 0 <0.000065>
17:01:30.101787 munmap(0x7ff4a4615000, 91027) = 0 <0.000075>
17:01:30.101919 set_tid_address(0x7ff4a460fa10) = 23824 <0.000030>
17:01:30.101986 set_robust_list(0x7ff4a460fa20, 24) = 0 <0.000042>
17:01:30.102135 rt_sigaction(SIGRTMIN, {0x7ff4a41f6790, [], SA_RESTORER|SA_SIGINFO, 0x7ff4a41ff5d0}, NULL, 8) = 0 <0.000045>
17:01:30.102343 rt_sigaction(SIGRT_1, {0x7ff4a41f6820, [], SA_RESTORER|SA_RESTART|SA_SIGINFO, 0x7ff4a41ff5d0}, NULL, 8) = 0 <0.000034>
17:01:30.102445 rt_sigprocmask(SIG_UNBLOCK, [RTMIN RT_1], NULL, 8) = 0 <0.000023>
17:01:30.102550 getrlimit(RLIMIT_STACK, {rlim_cur=8192*1024, rlim_max=RLIM64_INFINITY}) = 0 <0.000030>
17:01:30.103185 brk(NULL)               = 0x131b000 <0.000031>
17:01:30.103297 brk(0x133c000)          = 0x133c000 <0.000055>
17:01:30.103416 brk(NULL)               = 0x133c000 <0.000030>
17:01:30.103584 ioctl(0, TCGETS, {B38400 opost isig icanon echo ...}) = 0 <0.000052>
17:01:30.103762 ioctl(1, TCGETS, {B38400 opost isig icanon echo ...}) = 0 <0.000026>
17:01:30.103859 dup(1)                  = 3 <0.000026>
17:01:30.103931 close(3)                = 0 <0.000031>
17:01:30.104174 stat("/etc/my.cnf", {st_mode=S_IFREG|0644, st_size=570, ...}) = 0 <0.000060>
17:01:30.104329 open("/etc/my.cnf", O_RDONLY) = 3 <0.000040>
17:01:30.104447 fstat(3, {st_mode=S_IFREG|0644, st_size=570, ...}) = 0 <0.000038>
17:01:30.104559 mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7ff4a462b000 <0.000034>
17:01:30.104648 read(3, "[mysqld]\ndatadir=/var/lib/mysql\n"..., 4096) = 570 <0.000055>
17:01:30.104828 openat(AT_FDCWD, "/etc/my.cnf.d/", O_RDONLY|O_NONBLOCK|O_DIRECTORY|O_CLOEXEC) = 4 <0.000156>
17:01:30.105076 getdents(4, /* 3 entries */, 32768) = 88 <0.000054>
17:01:30.105178 getdents(4, /* 0 entries */, 32768) = 0 <0.000018>
17:01:30.105227 close(4)                = 0 <0.000021>
17:01:30.105307 stat("/etc/my.cnf.d/mysql-clients.cnf", {st_mode=S_IFREG|0644, st_size=232, ...}) = 0 <0.000022>
17:01:30.105370 open("/etc/my.cnf.d/mysql-clients.cnf", O_RDONLY) = 4 <0.000021>
17:01:30.105423 fstat(4, {st_mode=S_IFREG|0644, st_size=232, ...}) = 0 <0.000025>
17:01:30.105518 mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7ff4a462a000 <0.000027>
17:01:30.105579 read(4, "#\n# These groups are read by Mar"..., 4096) = 232 <0.000032>
17:01:30.105659 read(4, "", 4096)       = 0 <0.000021>
17:01:30.105734 close(4)                = 0 <0.000018>
17:01:30.105803 munmap(0x7ff4a462a000, 4096) = 0 <0.000038>
17:01:30.105898 read(3, "", 4096)       = 0 <0.000023>
17:01:30.105961 close(3)                = 0 <0.000022>
17:01:30.106015 munmap(0x7ff4a462b000, 4096) = 0 <0.000022>
17:01:30.106071 stat("/etc/mysql/my.cnf", 0x7ffd4896d3d0) = -1 ENOENT (No such file or directory) <0.000029>
17:01:30.106143 stat("/usr/local/mysql/etc/my.cnf", 0x7ffd4896d3d0) = -1 ENOENT (No such file or directory) <0.000026>
17:01:30.106210 stat("/root/.my.cnf", 0x7ffd4896d3d0) = -1 ENOENT (No such file or directory) <0.000031>
17:01:30.106317 stat("/root/.mylogin.cnf", {st_mode=S_IFREG|0660, st_size=172, ...}) = 0 <0.000018>
17:01:30.106423 write(2, "mysql: ", 7mysql: )  = 7 <0.000021>
17:01:30.106481 write(2, "[Warning] /root/.mylogin.cnf sho"..., 78[Warning] /root/.mylogin.cnf should be readable/writable only by current user.) = 78 <0.000023>
17:01:30.106545 write(2, "\n", 1
)       = 1 <0.000019>
17:01:30.106751 socket(AF_LOCAL, SOCK_STREAM|SOCK_CLOEXEC|SOCK_NONBLOCK, 0) = 3 <0.000040>
17:01:30.106866 connect(3, {sa_family=AF_LOCAL, sun_path="/var/run/nscd/socket"}, 110) = -1 ENOENT (No such file or directory) <0.000033>
17:01:30.106950 close(3)                = 0 <0.000022>
17:01:30.107001 socket(AF_LOCAL, SOCK_STREAM|SOCK_CLOEXEC|SOCK_NONBLOCK, 0) = 3 <0.000024>
17:01:30.107072 connect(3, {sa_family=AF_LOCAL, sun_path="/var/run/nscd/socket"}, 110) = -1 ENOENT (No such file or directory) <0.000025>
17:01:30.107130 close(3)                = 0 <0.000021>
17:01:30.107188 open("/etc/nsswitch.conf", O_RDONLY|O_CLOEXEC) = 3 <0.000022>
17:01:30.107245 fstat(3, {st_mode=S_IFREG|0644, st_size=1746, ...}) = 0 <0.000017>
17:01:30.107294 mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7ff4a462b000 <0.000021>
17:01:30.107361 read(3, "#\n# /etc/nsswitch.conf\n#\n# An ex"..., 4096) = 1746 <0.000024>
17:01:30.107446 read(3, "", 4096)       = 0 <0.000019>
17:01:30.107512 close(3)                = 0 <0.000017>
17:01:30.107569 munmap(0x7ff4a462b000, 4096) = 0 <0.000026>
17:01:30.107647 open("/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3 <0.000027>
17:01:30.107727 fstat(3, {st_mode=S_IFREG|0644, st_size=91027, ...}) = 0 <0.000023>
17:01:30.107806 mmap(NULL, 91027, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7ff4a4615000 <0.000028>
17:01:30.107887 close(3)                = 0 <0.000025>
17:01:30.107978 open("/lib64/libnss_files.so.2", O_RDONLY|O_CLOEXEC) = 3 <0.000032>
17:01:30.108046 read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0000!\0\0\0\0\0\0"..., 832) = 832 <0.000019>
17:01:30.108110 fstat(3, {st_mode=S_IFREG|0755, st_size=61624, ...}) = 0 <0.000019>
17:01:30.108178 mmap(NULL, 2173016, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7ff4a2b93000 <0.000026>
17:01:30.108240 mprotect(0x7ff4a2b9f000, 2093056, PROT_NONE) = 0 <0.000036>
17:01:30.108312 mmap(0x7ff4a2d9e000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0xb000) = 0x7ff4a2d9e000 <0.000036>
17:01:30.108396 mmap(0x7ff4a2da0000, 22616, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7ff4a2da0000 <0.000017>
17:01:30.108464 close(3)                = 0 <0.000028>
17:01:30.108557 mprotect(0x7ff4a2d9e000, 4096, PROT_READ) = 0 <0.000024>
17:01:30.108619 munmap(0x7ff4a4615000, 91027) = 0 <0.000031>
17:01:30.108703 open("/etc/services", O_RDONLY|O_CLOEXEC) = 3 <0.000037>
17:01:30.108775 fstat(3, {st_mode=S_IFREG|0644, st_size=670293, ...}) = 0 <0.000017>
17:01:30.108837 mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7ff4a462b000 <0.000019>
17:01:30.108887 read(3, "# /etc/services:\n# $Id: services"..., 4096) = 4096 <0.000051>
17:01:30.109020 read(3, " 74/udp                         "..., 4096) = 4096 <0.000019>
17:01:30.109113 read(3, "     # Quick Mail Transfer Proto"..., 4096) = 4096 <0.000026>
17:01:30.109200 read(3, "    636/udp                     "..., 4096) = 4096 <0.000021>
17:01:30.109278 read(3, "          # Microsoft-SQL-Monito"..., 4096) = 4096 <0.000028>
17:01:30.109363 read(3, "                       # discp s"..., 4096) = 4096 <0.000021>
17:01:30.109423 close(3)                = 0 <0.000019>
17:01:30.109468 munmap(0x7ff4a462b000, 4096) = 0 <0.000033>
17:01:30.109542 rt_sigaction(SIGPIPE, {SIG_IGN, [PIPE], SA_RESTORER|SA_RESTART, 0x7ff4a3006280}, {SIG_DFL, [], 0}, 8) = 0 <0.000019>
17:01:30.109667 open("/usr/lib/locale/locale-archive", O_RDONLY|O_CLOEXEC) = 3 <0.000021>
17:01:30.109735 fstat(3, {st_mode=S_IFREG|0644, st_size=106075056, ...}) = 0 <0.000020>
17:01:30.109801 mmap(NULL, 106075056, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7ff49c669000 <0.000027>
17:01:30.109883 close(3)                = 0 <0.000018>
17:01:30.110254 stat("/usr/local/mysql/share/charsets/Index.xml", 0x7ffd48971520) = -1 ENOENT (No such file or directory) <0.000018>
17:01:30.110331 futex(0xa4ab20, FUTEX_WAKE_PRIVATE, 2147483647) = 0 <0.000025>
17:01:30.110483 socket(AF_LOCAL, SOCK_STREAM, 0) = 3 <0.000030>
17:01:30.110574 connect(3, {sa_family=AF_LOCAL, sun_path="/tmp/mysql.sock"}, 110) = -1 ENOENT (No such file or directory) <0.000046>
17:01:30.110666 shutdown(3, SHUT_RDWR)  = 0 <0.000020>
17:01:30.110721 close(3)                = 0 <0.000026>
17:01:30.110789 fstat(1, {st_mode=S_IFCHR|0620, st_rdev=makedev(136, 1), ...}) = 0 <0.000031>
17:01:30.110860 mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7ff4a462b000 <0.000023>
17:01:30.110927 write(2, "ERROR 2002 (HY000): ", 20ERROR 2002 (HY000): ) = 20 <0.000021>
17:01:30.110980 write(2, "Can't connect to local MySQL ser"..., 72Can't connect to local MySQL server through socket '/tmp/mysql.sock' (2)) = 72 <0.000019>
17:01:30.111028 write(2, "\n", 1
)       = 1 <0.000019>
17:01:30.111077 write(1, "\7", 1)       = 1 <0.000018>
17:01:30.111132 rt_sigaction(SIGQUIT, {SIG_IGN, [QUIT], SA_RESTORER|SA_RESTART, 0x7ff4a3006280}, {SIG_DFL, [], 0}, 8) = 0 <0.000019>
17:01:30.111192 rt_sigaction(SIGINT, {SIG_IGN, [INT], SA_RESTORER|SA_RESTART, 0x7ff4a3006280}, {SIG_DFL, [], 0}, 8) = 0 <0.000021>
17:01:30.111248 rt_sigaction(SIGHUP, {SIG_IGN, [HUP], SA_RESTORER|SA_RESTART, 0x7ff4a3006280}, {SIG_DFL, [], 0}, 8) = 0 <0.000018>
17:01:30.111429 exit_group(1)           = ?
17:01:30.111706 +++ exited with 1 +++
2025-03-24T17:01:30 [root@x86centos7-136 /root] #



```

## 2. 跟踪已经运行进程

```bash
2025-03-24T16:59:10 [root@x86centos7-136 /root] # ps aux|grep sshd
root      3430  0.0  0.0 112864  4352 ?        Ss   Mar10   0:00 /usr/sbin/sshd -D
root     16566  0.0  0.0 157260  5892 ?        Ss   15:29   0:00 sshd: root@pts/1
root     22900  0.0  0.0 112712   976 pts/1    S+   16:59   0:00 grep --color=auto sshd

2025-03-24T16:59:27 [root@x86centos7-136 /root] # strace -p 3430
strace: Process 3430 attached
select(9, [3 4], NULL, NULL, NULL)      = 1 (in [3])
accept(3, {sa_family=AF_INET, sin_port=htons(54695), sin_addr=inet_addr("172.17.120.253")}, [16]) = 5
fcntl(5, F_GETFL)                       = 0x2 (flags O_RDWR)
pipe([6, 7])                            = 0
socketpair(AF_LOCAL, SOCK_STREAM, 0, [8, 9]) = 0
clone(child_stack=0, flags=CLONE_CHILD_CLEARTID|CLONE_CHILD_SETTID|SIGCHLD, child_tidptr=0x7f6fe15cab90) = 23089
close(7)                                = 0
write(8, "\0\0\2\306\0", 5)             = 5
write(8, "\0\0\2\301\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\nHostKey"..., 709) = 709
close(8)                                = 0
close(9)                                = 0
close(5)                                = 0
select(9, [3 4 6], NULL, NULL, NULL)    = 1 (in [6])
close(6)                                = 0
select(9, [3 4], NULL, NULL, NULL^Cstrace: Process 3430 detached
 <detached ...>

2025-03-24T17:00:02 [root@x86centos7-136 /root] # pidof sshd
23089 16566 3430

2025-03-24T17:00:28 [root@x86centos7-136 /root] # pidof mysqld
17760
2025-03-24T17:00:39 [root@x86centos7-136 /root] #

```



