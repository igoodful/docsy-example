---
title: unique_checks
description: unique_checks
date: 2025-02-12
weight: 20000
viz: true

---

{{< alert >}}

- https://bugs.mysql.com/bug.php?id=106121

{{< /alert >}}


## unique_checks


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


此功能可能会导致一系列严重的数据问题，例如

- 唯一键失败：允许插入两个同一个uk的数据；

- 复制中断：主服务器成功插入的数据可能无法插入到从服务器。

- 通过uk和pk查询结果不一致。






