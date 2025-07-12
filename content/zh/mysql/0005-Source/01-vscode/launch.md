---
title: launch.json
description: launch.json
date: 2023-10-30
weight: 10001


---

{{< alert >}}

{{< /alert >}}


## launch.json

```json
{
        "version": "0.2.0",
        "configurations": [
                {
                        "type": "cppdbg",
                        "request": "launch",
                        "name": "Debug mysqld",
                        "program": "/root/local/mysql_8032/bin/mysqld",
                        "args": [
                                "--defaults-file=/root/local/mysql_8032/conf/my.conf",
                                "--debug=d,info:F:L",
                                "--user=root"
                        ],
                        "cwd": "${workspaceFolder}",
                },
                {
                        "type": "cppdbg",
                        "request": "launch",
                        "name": "Debug mysql",
                        "program": "/root/local/mysql_8032/bin/mysql",
                        "args": [
                                "-uroot",
                                "-proot",
                                "-P8026",
                                "-h127.0.0.1"
                        ],
                        "cwd": "${workspaceFolder}",
                }
        ]
}
```




