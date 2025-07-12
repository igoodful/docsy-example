---
title: memory
description: >
  物理内存还有剩余，但是用了swap内存，怎么处理？
date: 2023-10-17
weight: 4
---

{{< alert >}}



{{< /alert >}}



{{< alert title="swappiness" color="success" >}}



{{< /alert >}}



### 安装sysstat
```bash
yum -y install sysstat
```


### （1）free
{{< alert title="free参数详解：" color="primary" >}}
- -g  按照GB为单位显示
- -m  按照MB为单位显示
- -h  按照人性化可读的单位显示
{{< /alert >}}


```sql
[root@k8s-node-76 tmp]# free -h
              total        used        free      shared  buff/cache   available
Mem:           7.8G        4.5G        114M        393M        3.2G        2.5G
Swap:            0B          0B          0B

```


### （2）top
{{< alert title="top参数详解：" color="primary" >}}
- 在top界面上，按照内存使用量进行排序：shift+M
- 在top界面上，按照CPU使用量进行排序：shift+P

{{< /alert >}}

```sql
[root@k8s-node-76 tmp]# top
top - 16:05:36 up 3 days,  7:13,  3 users,  load average: 9.80, 6.14, 4.23
Tasks: 179 total,   2 running, 108 sleeping,   0 stopped,   0 zombie
%Cpu(s): 12.0 us,  5.3 sy,  0.0 ni,  4.3 id, 73.0 wa,  0.0 hi,  0.4 si,  5.1 st
KiB Mem :  8142784 total,   228688 free,  4653764 used,  3260332 buff/cache
KiB Swap:        0 total,        0 free,        0 used.  2666240 avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
 2885 mysql     20   0 6028500   3.7g   4828 S  12.3 47.3 169:08.98 mysqld
 6223 mysql     20   0 4668428 173800   8656 S   0.3  2.1  13:01.54 java
 9040 mysql     20   0 1303268  76164   6188 S   4.0  0.9  66:59.95 dbscale
 3729 root      20   0  554344  69824  32120 S   0.3  0.9   6:46.32 rsyslogd
32683 root      20   0 1212596  67052  43756 S   1.0  0.8   0:00.80 promtail
 4217 root      20   0 1657352  63276  22184 S   1.3  0.8  68:18.17 kubelet
 4842 root      20   0 1683312  50660  25996 S   1.7  0.6  84:04.54 calico-node
 3741 root      20   0 1362820  40640  10588 S   0.7  0.5  29:32.78 containerd
 1644 root      20   0   77136  39292  38844 S   0.3  0.5  15:43.78 systemd-journal
 4838 root      20   0 1756788  29920  12012 S   0.0  0.4   0:43.37 calico-node
 4840 root      20   0 1314140  28032  14856 S   0.0  0.3   0:23.89 calico-node
 4839 root      20   0 1240664  25380  10184 S   0.0  0.3   0:38.71 calico-node
 4843 root      20   0 1240408  22812   7704 S   0.0  0.3   0:34.91 calico-node
15548 mysql     20   0 1149420  22620   8144 S   1.7  0.3  74:52.41 dbinit
 4844 root      20   0 1240408  22368   7312 S   0.0  0.3   0:31.97 calico-node
 4386 root      20   0  764844  21788   2984 S   0.0  0.3   1:36.69 kube-proxy
 3734 root      20   0  727588  20212   6232 S   1.3  0.2  31:07.34 node_exporter

```



### （3）查看swap被哪些进程占用较多

```bash
[root@k8s-node-70 ~]# free -h;
              total        used        free      shared  buff/cache   available
Mem:           7.6G        7.3G        134M        388K        174M         88M
Swap:          2.0G        1.2G        765M
[root@k8s-node-70 ~]# for file in /proc/*/status ; do awk '/VmSwap|Name|^Pid/{printf $2 " " $3}END{ print ""}' $file; done | sort -k 3 -n -r|head
mysqld 3495 778880 kB
java 1001 177000 kB
dbscale 8687 85360 kB
greatrds-dbinit 980 76332 kB
mysql 2721 66564 kB
greatrds-export 576 19068 kB
containerd 872 17760 kB
greatrds-export 11847 13772 kB
dockerd 2490 9368 kB
node_exporter 850 8428 kB


[root@k8s-node-70 ~]# smem --sort swap |head -n 10
  PID User     Command                         Swap      USS      PSS      RSS
    1 root     /usr/lib/systemd/systemd --        0     2824     3010     5304
  346 root     nginx: master process nginx        0      128      254      812
  796 101      nginx: worker process              0      592      830     2136
  797 101      nginx: worker process              0      592      830     2136
  798 101      nginx: worker process              0      592      830     2136
  802 101      nginx: worker process              0      592      830     2136
 1020 root     /usr/bin/containerd-shim-ru        0     3200     3680     8492
 1040 root     nginx: master process nginx        0      236      382     1012
 1083 101      nginx: worker process              0      768     1461     4144

```

### ps

### vmstat


### pidstat
{{< alert title="pidstat参数详解：" color="primary" >}}
- 在top界面上，按照内存使用量进行排序：shift+M
- 在top界面上，按照CPU使用量进行排序：shift+P

{{< /alert >}}










