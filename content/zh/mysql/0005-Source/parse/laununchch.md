---
title: launch
description: launch
date: 2023-10-30
weight: 10000


---

{{< alert >}}


{{< /alert >}}


#### launch.json
```json
{
        "version": "0.2.0",
        "configurations": [
                {
                        "type": "cppdbg",
                        "request": "launch",
                        "name": "Debug mysqld",
                        "program": "/db/storage/guolicheng/local/mysql_3306/bin/mysqld",
                        "args": [
                                "--defaults-file=/db/storage/guolicheng/local/mysql_3306/conf/my.cnf",
                                "--debug=d,info:F:L",
                                "--user=guolicheng"
                        ],
                        "cwd": "${workspaceFolder}",
                },
                {
                        "type": "cppdbg",
                        "request": "launch",
                        "name": "Debug mysql",
                        "program": "/db/storage/guolicheng/local/mysql_3306/bin/mysql",
                        "args": [
                                "-uroot",
                                "-proot",
                                "-P3306",
                                "-h127.0.0.1"
                        ],
                        "cwd": "${workspaceFolder}",
                }
        ]
}

```







```json
{
        "version": "0.2.0",
        "configurations": [
                {
                        "type": "cppdbg",
                        "request": "launch",
                        "name": "Debug mysqld",
                        "program": "/root/local/mysql_8026/bin/mysqld",
                        "args": [
                                "--defaults-file=/root/local/mysql_8026/conf/my.cnf",
                                "--debug=d,info:F:L",
                                "--user=root"
                        ],
                        "cwd": "${workspaceFolder}",
                },
                {
                        "type": "cppdbg",
                        "request": "launch",
                        "name": "Debug mysql",
                        "program": "/root/local/mysql_8026/bin/mysql",
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

### 8.0.26

#### launch.json
```json

{
        "version": "0.2.0",
        "configurations": [
                {
                        "type": "cppdbg",
                        "request": "launch",
                        "name": "Debug mysqld",
                        "program": "/root/local/mysql_8026/bin/mysqld",
                        "args": [
                                "--defaults-file=/root/local/mysql_8026/conf/my.cnf",
                                "--debug=debug",
                                "--user=root"
                        ],
                        "cwd": "${workspaceFolder}",
                },
                {
                        "type": "cppdbg",
                        "request": "launch",
                        "name": "Debug mysql",
                        "program": "/root/local/mysql_8026/bin/mysql",
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

#### settings.json
```json
{
        "files.associations": {
                "iostream": "cpp",
                "thread": "cpp",
                "*.ipp": "cpp"
        }
}
```




