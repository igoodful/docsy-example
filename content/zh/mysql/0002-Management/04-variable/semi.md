---
title: 半同步
description: semi
date: 2023-10-25
weight: 20000


viz: true
---

{{< alert >}}
> [rpl_semi_sync_master_timeout](https://www.cnblogs.com/cchust/p/5100067.html)
{{< /alert >}}


```sql

[admin@127.0.0.1:3306 16:09:46((none))]$show global variables like '%semi%';
+-------------------------------------------+------------+
| Variable_name                             | Value      |
+-------------------------------------------+------------+
| rpl_semi_sync_master_enabled              | OFF        |
| rpl_semi_sync_master_timeout              | 10000      |
| rpl_semi_sync_master_trace_level          | 32         |
| rpl_semi_sync_master_wait_for_slave_count | 1          |
| rpl_semi_sync_master_wait_no_slave        | ON         |
| rpl_semi_sync_master_wait_point           | AFTER_SYNC |
| rpl_semi_sync_slave_enabled               | OFF        |
| rpl_semi_sync_slave_trace_level           | 32         |
+-------------------------------------------+------------+
8 rows in set (0.05 sec)

[admin@127.0.0.1:3306 16:10:02((none))]$

```


## wait_timeout


```viz-dot
digraph "pfs_spawn_thread" {
        rankdir = TB;
        edge [
                fontsize="12"
                ];
        node [
                shape = octagon;
                fontsize = "12"
        ];
        "pfs_spawn_thread" -> "handle_connection";
        "handle_connection" -> "do_command";
        "do_command" -> "my_net_set_read_timeout"[label="设置线程timeout"];
        "do_command" -> "Protocol_classic::get_command";
        "Protocol_classic::get_command" -> "Protocol_classic::read_packet";
        "Protocol_classic::read_packet" -> "my_net_read";
        "my_net_read" -> "net_read_uncompressed_packet" ;
        "net_read_uncompressed_packet" -> "net_read_packet" ;
        "net_read_packet" -> "net_read_packet_header" ;
        "net_read_packet_header" -> "net_read_raw_loop" ;


}
```








## rpl_semi_sync_master_timeout









## rpl_semi_sync_master_trace_level




## rpl_semi_sync_slave_trace_level















