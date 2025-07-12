---
title: multiprocessing
date: 2025-03-16
weight: 1000
description: 实现多进程编程
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}

{{< /alert >}}

##


```viz-dot
digraph "dbscale_daemon" {

node [
        rankdir=LR;
        fontsize = "12"
];
    "dbscale_daemon" [ label = "dbscale_daemon.py", shape = octagon ];
    "sys.argv" [ label = "sys.argv", shape = septagon ];
    "enable_lvs_dest" [ label = "enable_lvs_dest", shape = septagon ];
    "conf_file" [ label = "dbscale.conf", shape = septagon ];
    "fork_proc" [ label = "fork_proc", shape = septagon ];
    "daemon_job" [ label = "daemon_job", shape = septagon ];

    "dbscale_daemon" -> "sys.argv"
    "sys.argv" -> "enable_lvs_dest" 
    "enable_lvs_dest" -> "conf_file" 
    "conf_file" -> "fork_proc" 
    "fork_proc" -> "daemon_job" 
}
```
























