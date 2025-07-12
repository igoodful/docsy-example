---
title: 13. date
description: date
date: 2023-10-31
weight: 130
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

{{< /alert >}}


{{< alert title="sort参数详解" color="secondary">}}

{{< /alert >}}


查看系统开机时间

```bash
2024-06-23T15:16:13 [root@igoodful /root] $ date -d "`cut -f1 -d. /proc/uptime` seconds ago"
Sun Jun  2 10:27:28 CST 2024
2024-06-23T16:40:18 [root@igoodful /root] $ date -d "$(awk -F. '{print $1}' /proc/uptime) second ago" +"%Y-%m-%d %H:%M:%S"
2024-06-02 10:27:28

```











