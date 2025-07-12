---
title: net-tools 弃用
description: net-tools
date: 2025-03-22
weight: 102
viz: true

---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


{{<note>}}
<!---->

- https://sourceforge.net/p/acl/
- https://github.com/acl-dev/acl/


{{</note>}}


## 1. 依赖



## 2. 内容


### 2.1 bin

```viz-dot
digraph "net-tools" {
node [
        fontsize = "12"
];
    rankdir="LR";
    "net-tools" [ label = "net-tools", shape = octagon ];
    "netstat" [ label = "netstat", shape = octagon ];
    "arp" [ label = "arp", shape = octagon ];
    "ifconfig" [ label = "ifconfig", shape = octagon ];
    "route" [ label = "route", shape = octagon ];
    "ipmaddr" [ label = "ipmaddr", shape = octagon ];
    "iptunnel" [ label = "iptunnel", shape = octagon ];
    "slattach" [ label = "slattach", shape = octagon ];
    "plipconfig" [ label = "plipconfig", shape = octagon ];
    "nameif" [ label = "nameif", shape = octagon ];
    "ether-wake" [ label = "ether-wake", shape = octagon ];
    "mii-diag" [ label = "mii-diag", shape = octagon ];
    "mii-tool" [ label = "mii-tool", shape = octagon ];
    "net-tools" -> "netstat";
    "net-tools" -> "arp";
    "net-tools" -> "ifconfig";
    "net-tools" -> "route";
    "net-tools" -> "ipmaddr";
    "net-tools" -> "iptunnel";
    "net-tools" -> "slattach";
    "net-tools" -> "plipconfig";
    "net-tools" -> "nameif";
    "net-tools" -> "ether-wake";
    "net-tools" -> "mii-diag";
    "net-tools" -> "mii-tool";
}
```




## 3. 安装


#### 3.1 yum安装
```bash
yum -y install net-tools

```




#### 3.2 源码安装

```bash


```

