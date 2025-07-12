---
title: dmesg
description: /var/log/dmesg
date: 2023-11-08
weight: 1000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

{{< /alert >}}

---
&#128311;【】2017年上半年，城镇居民人均可支配收入18332元，增长8.1%（以下如无特别说明，均为同比名义增长）

2016年上半年，城镇居民人均可支配收入约为多少元？

【解析】$\cfrac{18332}{ 1 + 0.081 } = 17000$



## 日志时间

```bash
dmesg -T

dmesg -H

```


```bash
[May14 10:57] docker0: port 1(vethadb29c4) entered blocking state
[  +0.000010] docker0: port 1(vethadb29c4) entered disabled state
[  +0.000083] device vethadb29c4 entered promiscuous mode
[  +0.000139] IPv6: ADDRCONF(NETDEV_UP): vethadb29c4: link is not ready
[  +0.000002] docker0: port 1(vethadb29c4) entered blocking state
[  +0.000002] docker0: port 1(vethadb29c4) entered forwarding state
[  +0.000543] docker0: port 1(vethadb29c4) entered disabled state
[  +0.288881] IPv6: ADDRCONF(NETDEV_UP): eth0: link is not ready
[  +0.000367] IPv6: ADDRCONF(NETDEV_CHANGE): eth0: link becomes ready
[  +0.000013] IPv6: ADDRCONF(NETDEV_CHANGE): vethadb29c4: link becomes ready
[  +0.000052] docker0: port 1(vethadb29c4) entered blocking state
[  +0.000002] docker0: port 1(vethadb29c4) entered forwarding state
[May14 11:00] docker0: port 2(vethab30a8b) entered blocking state
[  +0.000011] docker0: port 2(vethab30a8b) entered disabled state
[  +0.000079] device vethab30a8b entered promiscuous mode
[  +0.000134] IPv6: ADDRCONF(NETDEV_UP): vethab30a8b: link is not ready
[  +0.000002] docker0: port 2(vethab30a8b) entered blocking state
[  +0.000002] docker0: port 2(vethab30a8b) entered forwarding state
[  +0.000703] docker0: port 2(vethab30a8b) entered disabled state
[  +0.241023] IPv6: ADDRCONF(NETDEV_UP): eth0: link is not ready
[  +0.000323] IPv6: ADDRCONF(NETDEV_CHANGE): eth0: link becomes ready
[  +0.000035] IPv6: ADDRCONF(NETDEV_CHANGE): vethab30a8b: link becomes ready

```






## 日志级别

```bash
dmesg -l info

dmesg -l emerg,alert,crit,err


```

| 日志级别 | 含义                             |   |
|:---------|:---------------------------------|:--|
| emerg    | System is unusable               |   |
| alert    | Action must be taken immediately |   |
| crit     | Critical conditions              |   |
| err      | Error conditions                 |   |
| warn     | Warning conditions               |   |
| notice   | Normal but significant condition |   |
| info     | Informational                    |   |
| debug    | Debug-level messages             |   |




## 日志显示颜色

```bash
dmesg -L
```


























