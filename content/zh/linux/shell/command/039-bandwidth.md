---
title: 39. bandwidth
description: bandwidth
date: 2025-04-19
weight: 390

---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



{{<alert>}}


{{</alert>}}



## 1. vnstat


```bash
systemctl enable --now vnstat

systemctl status vnstat

vnstat

vnstat -d -i eth0


vnstat -vs -i eth0 -o summary.png

```
- `-h`：以人类可读的格式打印结果
- `-s 1`：设置打印结果的延迟；这里我们使用了一秒
- `-c 3`：设置打印结果的计数



## 2. cat /proc/net/dev

监控自上次启动以来的带宽使用情况

使用这个文件的好处是我们不需要安装任何额外的软件。

/proc/net/dev文件记录了自上次启动以来的总带宽使用情况。因此，我们无法知道某个月的带宽使用量。此外，系统启动时，这些信息会被重置为零。因此，如果服务器重启，我们就会失去对带宽使用情况的追踪。

```bash
!/bin/bash
LOG=/var/log/bandwidth_usage_$(date +%Y%m).csv
echo -n "$(date --rfc-3339=date)," >> $LOG
awk '/^\s*eth0:/ {
    RX=$2/1024/1024
    TX=$10/1024/1024
    TOTAL=RX+TX
    print RX "," TX "," TOTAL
}' /proc/net/dev >> $LOG

```


```bash
awk '/^\s*eth0:/ {
    RX=$2/1024/1024
    TX=$10/1024/1024
    TOTAL=RX+TX
    print "RX:", RX, "MiB\nTX:", TX, "MiB\nTotal:", TOTAL, "MiB"
}' /proc/net/dev

```








