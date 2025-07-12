---
title: 01. 安装
description: install
date: 2023-10-12
weight: 100


viz: true
---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



## CoreDump

#### CoreDump简单概念

```viz-dot
digraph "system" {
        rankdir=TB;
node [
        fontsize = "12";
        shape = octagon;
];

"系统管理"->"service";
"系统管理"->"systemd";
"系统管理"->"cron";
"系统管理"->"firewalld";
"系统管理"->"hostname";
"系统管理"->"timezone";
"系统管理"->"hostname";
"系统管理"->"hostname";
"系统管理"->"pam_limits";
}
```



