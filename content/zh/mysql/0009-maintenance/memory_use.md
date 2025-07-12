---
title: 进程内存使用监控脚本
description: 进程内存使用监控脚本
date: 2025-02-25
weight: 80000

---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


{{< alert >}}
mysqld-auto.cnf

reset persist
{{< /alert >}}

## 进程内存使用监控脚本


```bash
#!/bin/bash
while true;do
start_time=$(date "+%Y%m%dT%H%M%S")
echo "start time: ${start_time}"
cat /proc/$(pidof mysqld)/status |grep -E "VmSize|VmRSS|Threads"
echo ""
sleep 20
done >> /tmp/mem_monitor.log

```










