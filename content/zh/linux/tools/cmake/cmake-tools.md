---
title: vscode cmake-tools
description: vscode cmake-tools
date: 2023-11-17
weight: 30000


---

{{< alert >}}


{{< /alert >}}

`cat .vscode/settings.json`
```json
{
        "cmake.buildBeforeRun": true,
        "cmake.buildDirectory": "${workspaceFolder}/cmake-build-debug/build",
        "cmake.configureSettings": {
                "WITH_DEBUG": "1",
                "CMAKE_INSTALL_PREFIX": "${workspaceFolder}/cmake-build-debug",
                "MYSQL_DATADIR": "${workspaceFolder}/cmake-build-debug/data",
                "SYSCONFDIR": "${workspaceFolder}/cmake-build-debug/etc",
                "MYSQL_TCP_PORT": "3332",
                "MYSQL_UNIX_ADDR": "${workspaceFolder}/cmake-build-debug/tmp/mysql.sock",
                "WITH_BOOST": "${workspaceFolder}/boost",
                "DOWNLOAD_BOOST": "0",
                "DOWNLOAD_BOOST_TIMEOUT": "600",
                "DEFAULT_CHARSET": "utf8mb4",
                "DEFAULT_COLLATION": "utf8mb4_0900_ai_ci",
                "WITH_ICU": "/db/storage/guolicheng/local",
                "CMAKE_BUILD_TYPE": "Debug",
                "ENABLED_LOCAL_INFILE": "1",
                "WITH_PERFSCHEMA_STORAGE_ENGINE": "1",
                "WITH_MYISAM_STORAGE_ENGINE": "1",
                "WITH_FEDERATED_STORAGE_ENGINE": "1",
                "WITH_INNOBASE_STORAGE_ENGINE": "1",
                "WITH_BLACKHOLE_STORAGE_ENGINE": "1",
        },
        "cmake.cmakePath": "/usr/bin/cmake3",
        "cmake.configureOnEdit": true,
}

``

`cat .vscode/launch.json`
```json
{
        "version": "0.2.0",
        "env": {
                "CC": "/db/storage/guolicheng/local/bin/gcc",
                "CXX": "/db/storage/guolicheng/local/bin/g++"
        },
        "configurations": [
                {
                        "type": "lldb",
                        "request": "launch",
                        "name": "Debug mysqld",
                        "program": "${workspaceFolder}/cmake-build-debug/build/sql/mysqld",
                        "args": [
                                "--defaults-file=${workspaceFolder}/cmake-build-debug/etc/my.cnf",
                                "--debug"
                        ],
                        "cwd": "${workspaceFolder}"
                },
                {
                        "type": "lldb",
                        "request": "launch",
                        "name": "Debug mysql",
                        "program": "${workspaceFolder}/cmake-build-debug/build/client/mysql",
                        "args": [
                                "-uroot",
                                "-P3332",
                                "-h127.0.0.1"
                        ],
                        "cwd": "${workspaceFolder}"
                }
        ]
}
```

### CMAKE_C_COMPILER 和 CMAKE_CXX_COMPILER 路径不对
>
```bash
[main] 正在配置项目: mysql-server-mysql-8.0.32
[proc] 执行命令: /usr/bin/cmake3 --no-warn-unused-cli -DCMAKE_BUILD_TYPE:STRING=Debug -DWITH_DEBUG:STRING=1 -DCMAKE_INSTALL_PREFIX:STRING=/db/storage/guolicheng/test/mysql-server-mysql-8.0.32/cmake-build-debug -DMYSQL_DATADIR:STRING=/db/storage/guolicheng/test/mysql-server-mysql-8.0.32/cmake-build-debug/data -DSYSCONFDIR:STRING=/db/storage/guolicheng/test/mysql-server-mysql-8.0.32/cmake-build-debug/etc -DMYSQL_TCP_PORT:STRING=3332 -DMYSQL_UNIX_ADDR:STRING=/db/storage/guolicheng/test/mysql-server-mysql-8.0.32/cmake-build-debug/tmp/mysql.sock -DWITH_BOOST:STRING=/db/storage/guolicheng/test/mysql-server-mysql-8.0.32/boost -DDOWNLOAD_BOOST:STRING=0 -DDOWNLOAD_BOOST_TIMEOUT:STRING=600 -DDEFAULT_CHARSET:STRING=utf8mb4 -DDEFAULT_COLLATION:STRING=utf8mb4_0900_ai_ci -DWITH_ICU:STRING=/db/storage/guolicheng/local -DENABLED_LOCAL_INFILE:STRING=1 -DWITH_PERFSCHEMA_STORAGE_ENGINE:STRING=1 -DWITH_MYISAM_STORAGE_ENGINE:STRING=1 -DWITH_FEDERATED_STORAGE_ENGINE:STRING=1 -DWITH_INNOBASE_STORAGE_ENGINE:STRING=1 -DWITH_BLACKHOLE_STORAGE_ENGINE:STRING=1 -DCMAKE_C_COMPILER:FILEPATH=/db/storage/guolicheng/local/gcc-10.2.0/bin/gcc -DCMAKE_CXX_COMPILER:FILEPATH=/db/storage/guolicheng/local/gcc-10.2.0/bin/g++ -DCMAKE_EXPORT_COMPILE_COMMANDS:BOOL=TRUE -S/db/storage/guolicheng/test/mysql-server-mysql-8.0.32 -B/db/storage/guolicheng/test/mysql-server-mysql-8.0.32/cmake-build-debug/build -G "Unix Makefiles"
[cmake] Not searching for unused variables given on the command line.
```
**解决**：添加软连接
```bash
[guolicheng@48 build]$ mkdir -p /db/storage/guolicheng/local/gcc-10.2.0/bin
[guolicheng@48 build]$ ln -s /db/storage/guolicheng/local/bin/gcc /db/storage/guolicheng/local/gcc-10.2.0/bin/gcc
[guolicheng@48 build]$ ln -s /db/storage/guolicheng/local/bin/g++ /db/storage/guolicheng/local/gcc-10.2.0/bin/g++

```













`
