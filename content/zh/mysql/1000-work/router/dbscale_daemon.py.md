---
title: dbscale_daemon.py
description: dbscale_daemon.py
date: 2023-10-24
weight: 70000
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;

{{< alert >}}


{{< /alert >}}

 ## 图形

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


## daemon_job

```viz-dot
digraph "daemon_job" {

node [
        rankdir=LR;
        fontsize = "12"
];
    "daemon_job" [ label = "daemon_job", shape = septagon ];
    "check_dbscale_pid" [ label = "check_dbscale_pid", shape = septagon ];
    "check_dbscale_login" [ label = "check_dbscale_login", shape = septagon ];
    "get_process_map" [ label = "get_process_map", shape = septagon ];
    "clean_zk_node" [ label = "clean_zk_node", shape = septagon ];
    "dbscale-service.sh" [ label = "dbscale-service.sh", shape = septagon ];

    "daemon_job" -> "check_dbscale_pid"
    "check_dbscale_pid" -> "check_dbscale_login"
    "check_dbscale_login" -> "get_process_map"
    "get_process_map" -> "clean_zk_node"
    "clean_zk_node" -> "dbscale-service.sh"
}
```


```bash
dbscale-service.sh


```


















