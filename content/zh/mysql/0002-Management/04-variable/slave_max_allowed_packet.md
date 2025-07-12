---
title: slave_max_allowed_packet
description: slave_max_allowed_packet
date: 2023-10-25
weight: 20000
viz: true

---

{{< alert >}}
> [slave_max_allowed_packet](https://opensource.actionsky.com/20220712-mysql/)
{{< /alert >}}


## 慢日志解析


```viz-dot
digraph "slave_max_allowed_packet" {
rankdir = TB;
edge [
 fontsize="12"
];
node [
shape = octagon;
fontsize = "12"
];

"slave_max_allowed_packet" -> "64M";
"slave_max_allowed_packet" -> "128M";
}
```



