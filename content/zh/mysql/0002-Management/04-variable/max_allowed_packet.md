---
title: max_allowed_packet
description: max_allowed_packet
date: 2023-10-25
weight: 20000
viz: true

---

{{< alert >}}
> [max_allowed_packet](https://opensource.actionsky.com/20220712-mysql/)
{{< /alert >}}


## 慢日志解析


```viz-dot
digraph "max_allowed_packet" {
rankdir = TB;
edge [
 fontsize="12"
];
node [
shape = octagon;
fontsize = "12"
];

"max_allowed_packet" -> "64M";
"max_allowed_packet" -> "128M";
}
```



