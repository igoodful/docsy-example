---
title: vscode
description: vscode
date: 2023-11-17
weight: 20


viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

- 编译器在编译时是以C文件为单位进行的，也就是说如果你的项目中一个C文件都没有，那么你的项目将无法编译
- 连接器是以目标文件(.obj文件)为单位，它将一个或多个目标文件进行函数与变量的重定位，生成最终的可执行文件
- xxx.h文件必须被包含在xxx.c文件中


{{< /alert >}}


{{<alert>}}


{{</alert>}}


## 调试多个main


#### 1. c_cpp_properties.json

```json
{
        "configurations": [
                {
                        "name": "linux-gcc-x64",
                        "includePath": [
                                "${workspaceFolder}/**"
                        ],
                        "compilerPath": "/opt/rh/gcc-toolset-12/root/usr/bin/g++",
                        "cStandard": "${default}",
                        "cppStandard": "${default}",
                        "intelliSenseMode": "linux-gcc-x64",
                        "compilerArgs": [
                                ""
                        ]
                }
        ],
        "version": 4
}
```


#### 2. tasks.json
ctrl+shift+P》tasks
```json
{
        "version": "2.0.0",
        "tasks": [
                {
                        "type": "shell",
                        "label": "C/C++: g++ build active file",
                        "command": "/opt/rh/gcc-toolset-12/root/usr/bin/g++",
                        "args": [
                                "-g",
                                "${file}",
                                "-o",
                                "${fileDirname}/main"
                        ],
                        "options": {
                                "cwd": "${fileDirname}"
                        },
                        "problemMatcher": [],
                        "group": {
                                "kind": "build",
                                "isDefault": true
                        },
                        "detail": "compiler: /opt/rh/gcc-toolset-12/root/usr/bin/g++"
                }
        ]
}
```


#### 3. launch.json

```json
{
        "version": "0.2.0",
        "configurations": [
                {
                        "name": "C/C++ Runner: Debug Session",
                        "type": "cppdbg",
                        "request": "launch",
                        "args": [],
                        "stopAtEntry": false,
                        "externalConsole": false,
                        "cwd": "${fileDirname}",
                        "program": "${fileDirname}/main",
                        "MIMode": "gdb",
                        "miDebuggerPath": "gdb",
                        "setupCommands": [
                                {
                                        "description": "Enable pretty-printing for gdb",
                                        "text": "-enable-pretty-printing",
                                        "ignoreFailures": true
                                }
                        ]
                }
        ]
}
```




## 离线安装插件


```bash
2025-04-14T18:49:11 [mysql@x86centos7-250 ] $ pwd
/home/mysql/.vscode-server/bin/8b3775030ed1a69b13e4f4c628c612102e30a681/bin
2025-04-14T18:59:20 [mysql@x86centos7-250 ] $ ll
total 12
-rwxr-xr-x 1 mysql mysql  249 Jan 18  2024 code-server
drwxr-xr-x 2 mysql mysql 4096 Jan 18  2024 helpers
drwxr-xr-x 2 mysql mysql 4096 Jan 18  2024 remote-cli
2025-04-14T18:59:27 [mysql@x86centos7-250 ] $ ./code-server --install-extension /tmp/codelldb-x86_64-linux-1.6.vsix
Installing extensions...
Extension 'codelldb-x86_64-linux-1.6.vsix' was successfully installed.
2025-04-14T18:59:40 [mysql@x86centos7-250 ] $

```



## centos7离线安装vscode

#### 1. 获取windows上vscode的commit id

```bash
版本: 1.99.3 (user setup)
提交: 17baf841131aa23349f217ca7c570c76ee87b957
日期: 2025-04-15T23:18:46.076Z
Electron: 34.3.2
ElectronBuildId: 11161073
Chromium: 132.0.6834.210
Node.js: 20.18.3
V8: 13.2.152.41-electron.0
OS: Windows_NT x64 10.0.26100
```
- `commit id: 17baf841131aa23349f217ca7c570c76ee87b957`


#### 2. 下载linux版本的vscode

x86:
- https://vscode.download.prss.microsoft.com/dbazure/download/stable/${commit_id}/vscode-server-linux-x64.tar.gz
- https://vscode.download.prss.microsoft.com/dbazure/download/stable/${commit_id}/vscode_cli_alpine_x64_cli.tar.gz

arm:
- https://vscode.download.prss.microsoft.com/dbazure/download/stable/${commit_id}/vscode-server-linux-arm64.tar.gz
- https://vscode.download.prss.microsoft.com/dbazure/download/stable/${commit_id}/vscode_cli_alpine_arm64_cli.tar.gz



```bash
版本: 1.99.3 (user setup)
提交: 17baf841131aa23349f217ca7c570c76ee87b957
日期: 2025-04-15T23:18:46.076Z
Electron: 34.3.2
ElectronBuildId: 11161073
Chromium: 132.0.6834.210
Node.js: 20.18.3
V8: 13.2.152.41-electron.0
OS: Windows_NT x64 10.0.26100
```


```bash
wget https://vscode.download.prss.microsoft.com/dbazure/download/stable/848b80aeb52026648a8ff9f7c45a9b0a80641e2e/vscode-server-linux-x64.tar.gz


```


#### 3. 解压vscode-server-linux-x64.tar.gz 并解决glibc版本问题

```bash
mkdir -p ${HOME}/.vscode-server/cli/servers/Stable-17baf841131aa23349f217ca7c570c76ee87b957/

tar -xzf vscode-server-linux-x64.tar.gz

mv vscode-server-linux-x64 server

mv server ${HOME}/.vscode-server/cli/servers/Stable-17baf841131aa23349f217ca7c570c76ee87b957/

cd ${HOME}/.vscode-server/cli/servers/Stable-17baf841131aa23349f217ca7c570c76ee87b957/server/

ldd -r ./node

patchelf --set-rpath ~/local/glibc-2.28/lib:~/local/gcc-11.4.0/lib64 ./node

patchelf --set-interpreter ~/local/glibc-2.28/lib/ld-linux-x86-64.so.2 ./node


```

- 这里我事先已经编译好了`gcc-11.4.0`和`glibc-2.28`

```bash
2025-04-18T21:28:22 [igoodfu@48 ~/.vscode-server/vscode-server-linux-x64] $ ll
total 96440
drwxr-xr-x  4 guolicheng guolicheng     4096 2025-04-16T07:42:12 bin
drwxr-xr-x 35 guolicheng guolicheng     4096 2025-04-16T07:42:13 extensions
-rw-r--r--  1 guolicheng guolicheng    13380 2025-04-16T07:41:05 LICENSE
-rwxr-xr-x  1 guolicheng guolicheng 98657032 2025-04-16T07:42:09 node
drwxr-xr-x 97 guolicheng guolicheng     4096 2025-04-16T07:42:16 node_modules
drwxr-xr-x  3 guolicheng guolicheng     4096 2025-04-16T07:42:12 out
-rw-rw-r--  1 guolicheng guolicheng      197 2025-04-16T07:35:29 package.json
-rw-rw-r--  1 guolicheng guolicheng    54841 2025-04-16T07:41:05 product.json
2025-04-18T21:28:23 [igoodfu@48 ~/.vscode-server/vscode-server-linux-x64] $ ldd -r ./node
./node: /lib64/libm.so.6: version `GLIBC_2.27' not found (required by ./node)
./node: /lib64/libc.so.6: version `GLIBC_2.28' not found (required by ./node)
./node: /lib64/libc.so.6: version `GLIBC_2.25' not found (required by ./node)
	linux-vdso.so.1 (0x00007ffc815a8000)
	libdl.so.2 => /lib64/libdl.so.2 (0x00007fdf6ad2c000)
	libstdc++.so.6 => ~/local/gcc-11.4.0/lib64/libstdc++.so.6 (0x00007fdf6a926000)
	libm.so.6 => /lib64/libm.so.6 (0x00007fdf6a624000)
	libgcc_s.so.1 => ~/local/gcc-11.4.0/lib64/libgcc_s.so.1 (0x00007fdf6a40c000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007fdf6a1ee000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fdf69e42000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fdf6af30000)
symbol getentropy, version GLIBC_2.25 not defined in file libc.so.6 with link time reference	(./node)
symbol fcntl64, version GLIBC_2.28 not defined in file libc.so.6 with link time reference	(./node)
symbol powf, version GLIBC_2.27 not defined in file libm.so.6 with link time reference	(./node)
symbol expf, version GLIBC_2.27 not defined in file libm.so.6 with link time reference	(./node)
symbol getrandom, version GLIBC_2.25 not defined in file libc.so.6 with link time reference	(./node)
2025-04-18T21:28:28 [igoodfu@48 ~/.vscode-server/vscode-server-linux-x64] $ patchelf --set-rpath '~/local/glibc-2.28/lib:~/local/gcc-11.4.0/lib64' ./node
2025-04-18T21:29:10 [igoodfu@48 ~/.vscode-server/vscode-server-linux-x64] $ ll
total 96448
drwxr-xr-x  4 guolicheng guolicheng     4096 2025-04-16T07:42:12 bin
drwxr-xr-x 35 guolicheng guolicheng     4096 2025-04-16T07:42:13 extensions
-rw-r--r--  1 guolicheng guolicheng    13380 2025-04-16T07:41:05 LICENSE
-rwxr-xr-x  1 guolicheng guolicheng 98665224 2025-04-18T21:29:10 node
drwxr-xr-x 97 guolicheng guolicheng     4096 2025-04-16T07:42:16 node_modules
drwxr-xr-x  3 guolicheng guolicheng     4096 2025-04-16T07:42:12 out
-rw-rw-r--  1 guolicheng guolicheng      197 2025-04-16T07:35:29 package.json
-rw-rw-r--  1 guolicheng guolicheng    54841 2025-04-16T07:41:05 product.json
2025-04-18T21:29:12 [igoodfu@48 ~/.vscode-server/vscode-server-linux-x64] $ ldd -r ./node
	linux-vdso.so.1 (0x00007ffd2ea81000)
	libdl.so.2 => ~/local/glibc-2.28/lib/libdl.so.2 (0x00007f45cd5b3000)
	libstdc++.so.6 => ~/local/gcc-11.4.0/lib64/libstdc++.so.6 (0x00007f45cd1ad000)
	libm.so.6 => ~/local/glibc-2.28/lib/libm.so.6 (0x00007f45cce30000)
	libgcc_s.so.1 => ~/local/gcc-11.4.0/lib64/libgcc_s.so.1 (0x00007f45ccc18000)
	libpthread.so.0 => ~/local/glibc-2.28/lib/libpthread.so.0 (0x00007f45cc9f9000)
	libc.so.6 => ~/local/glibc-2.28/lib/libc.so.6 (0x00007f45cc641000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f45cd7b7000)
symbol _dl_exception_create, version GLIBC_PRIVATE not defined in file ld-linux-x86-64.so.2 with link time reference	(~/local/glibc-2.28/lib/libc.so.6)
symbol __tunable_get_val, version GLIBC_PRIVATE not defined in file ld-linux-x86-64.so.2 with link time reference	(~/local/glibc-2.28/lib/libc.so.6)
symbol __tunable_get_val, version GLIBC_PRIVATE not defined in file ld-linux-x86-64.so.2 with link time reference	(~/local/glibc-2.28/lib/libpthread.so.0)
2025-04-18T21:29:15 [igoodfu@48 ~/.vscode-server/vscode-server-linux-x64] $ patchelf --set-interpreter ~/local/glibc-2.28/lib/ld-linux-x86-64.so.2 ./node
2025-04-18T21:29:39 [igoodfu@48 ~/.vscode-server/vscode-server-linux-x64] $ ldd -r ./node
	linux-vdso.so.1 (0x00007ffcc01d7000)
	libdl.so.2 => ~/local/glibc-2.28/lib/libdl.so.2 (0x00007f44be158000)
	libstdc++.so.6 => ~/local/gcc-11.4.0/lib64/libstdc++.so.6 (0x00007f44bdd52000)
	libm.so.6 => ~/local/glibc-2.28/lib/libm.so.6 (0x00007f44bd9d5000)
	libgcc_s.so.1 => ~/local/gcc-11.4.0/lib64/libgcc_s.so.1 (0x00007f44bd7bd000)
	libpthread.so.0 => ~/local/glibc-2.28/lib/libpthread.so.0 (0x00007f44bd59e000)
	libc.so.6 => ~/local/glibc-2.28/lib/libc.so.6 (0x00007f44bd1e6000)
	~/local/glibc-2.28/lib/ld-linux-x86-64.so.2 (0x00007f44be35c000)



```


#### 替换`~/.vscode-server/bin/17baf841131aa23349f217ca7c570c76ee87b957/node`为`~/.vscode-server/cli/servers/Stable-17baf841131aa23349f217ca7c570c76ee87b957/server/node`

```bash
[guolicheng@48 ~/.vscode-server/bin/17baf841131aa23349f217ca7c570c76ee87b957] $ cp ~/.vscode-server/cli/servers/Stable-17baf841131aa23349f217ca7c570c76ee87b957/server/node ./
[guolicheng@48 ~/.vscode-server/bin/17baf841131aa23349f217ca7c570c76ee87b957] $ ll
total 96456
drwxr-xr-x  4 guolicheng guolicheng     4096 2025-04-16T07:42:12 bin
drwxr-xr-x 35 guolicheng guolicheng     4096 2025-04-16T07:42:13 extensions
-rw-r--r--  1 guolicheng guolicheng    13380 2025-04-16T07:41:05 LICENSE
-rwxr-xr-x  1 guolicheng guolicheng 98673416 2025-04-21T08:56:38 node
drwxr-xr-x 97 guolicheng guolicheng     4096 2025-04-16T07:42:16 node_modules
drwxr-xr-x  3 guolicheng guolicheng     4096 2025-04-16T07:42:12 out
-rw-rw-r--  1 guolicheng guolicheng      197 2025-04-16T07:35:29 package.json
-rw-rw-r--  1 guolicheng guolicheng    54841 2025-04-16T07:41:05 product.json
[guolicheng@48 ~/.vscode-server/bin/17baf841131aa23349f217ca7c570c76ee87b957] $ ./node --version
v20.18.3

```





#### vscode报glibc是否大于v2.28.0

###### 报错日志

```bash
[09:48:51.860] Log Level: 2
[09:48:51.880] VS Code version: 1.99.3
[09:48:51.880] Remote-SSH version: remote-ssh@0.119.0
[09:48:51.881] win32 x64
[09:48:51.883] SSH Resolver called for "ssh-remote+172.17.130.48", attempt 1
[09:48:51.885] remote.SSH.useLocalServer = false
[09:48:51.885] remote.SSH.useExecServer = true
[09:48:51.885] remote.SSH.bindHost = {}
[09:48:51.886] remote.SSH.showLoginTerminal = false
[09:48:51.886] remote.SSH.remotePlatform = {"172.17.134.70":"linux","172.17.137.12":"linux","172.17.130.48":"linux","172.17.135.146":"linux","172.17.135.250":"linux","172.17.136.25":"linux"}
[09:48:51.886] remote.SSH.path = undefined
[09:48:51.886] remote.SSH.configFile = undefined
[09:48:51.886] remote.SSH.useFlock = true
[09:48:51.886] remote.SSH.lockfilesInTmp = false
[09:48:51.886] remote.SSH.localServerDownload = auto
[09:48:51.886] remote.SSH.remoteServerListenOnSocket = false
[09:48:51.886] remote.SSH.defaultExtensions = []
[09:48:51.887] remote.SSH.defaultExtensionsIfInstalledLocally = []
[09:48:51.887] remote.SSH.loglevel = 2
[09:48:51.887] remote.SSH.enableDynamicForwarding = true
[09:48:51.887] remote.SSH.enableRemoteCommand = false
[09:48:51.887] remote.SSH.serverPickPortsFromRange = {}
[09:48:51.887] remote.SSH.serverInstallPath = {}
[09:48:51.887] remote.SSH.permitPtyAllocation = false
[09:48:51.887] remote.SSH.preferredLocalPortRange = undefined
[09:48:51.888] remote.SSH.useCurlAndWgetConfigurationFiles = false
[09:48:51.888] remote.SSH.experimental.chat = false
[09:48:51.888] remote.SSH.experimental.enhancedSessionLogs = false
[09:48:51.888] remote.SSH.httpProxy = {}
[09:48:51.888] remote.SSH.httpsProxy = {}
[09:48:51.901] SSH Resolver called for host: 172.17.130.48
[09:48:51.901] Setting up SSH remote "172.17.130.48"
[09:48:51.909] Using commit id "17baf841131aa23349f217ca7c570c76ee87b957" and quality "stable" for server
[09:48:51.909] Extensions to install: 
[09:48:51.916] Install and start server if needed
[09:48:51.930] Checking ssh with "C:\guolicheng\app\python\Scripts\ssh.exe -V"
[09:48:51.933] Got error from ssh: spawn C:\guolicheng\app\python\Scripts\ssh.exe ENOENT
[09:48:51.933] Checking ssh with "C:\guolicheng\app\python\ssh.exe -V"
[09:48:51.934] Got error from ssh: spawn C:\guolicheng\app\python\ssh.exe ENOENT
[09:48:51.934] Checking ssh with "C:\Program Files\VanDyke Software\Clients\ssh.exe -V"
[09:48:51.935] Got error from ssh: spawn C:\Program Files\VanDyke Software\Clients\ssh.exe ENOENT
[09:48:51.936] Checking ssh with "C:\WINDOWS\system32\ssh.exe -V"
[09:48:51.937] Got error from ssh: spawn C:\WINDOWS\system32\ssh.exe ENOENT
[09:48:51.937] Checking ssh with "C:\WINDOWS\ssh.exe -V"
[09:48:51.938] Got error from ssh: spawn C:\WINDOWS\ssh.exe ENOENT
[09:48:51.939] Checking ssh with "C:\WINDOWS\System32\Wbem\ssh.exe -V"
[09:48:51.939] Got error from ssh: spawn C:\WINDOWS\System32\Wbem\ssh.exe ENOENT
[09:48:51.940] Checking ssh with "C:\WINDOWS\System32\WindowsPowerShell\v1.0\ssh.exe -V"
[09:48:51.940] Got error from ssh: spawn C:\WINDOWS\System32\WindowsPowerShell\v1.0\ssh.exe ENOENT
[09:48:51.941] Checking ssh with "C:\WINDOWS\System32\OpenSSH\ssh.exe -V"
[09:48:51.974] > OpenSSH_for_Windows_9.5p1, LibreSSL 3.8.2

[09:48:51.978] Running script with connection command: "C:\WINDOWS\System32\OpenSSH\ssh.exe" -T -D 63830 "172.17.130.48" sh
[09:48:51.981] Generated SSH command: 'type "C:\Users\12626\AppData\Local\Temp\vscode-linux-multi-line-command-172.17.130.48-144877105.sh" | "C:\WINDOWS\System32\OpenSSH\ssh.exe" -T -D 63830 "172.17.130.48" sh'
[09:48:51.981] Using connect timeout of 17 seconds
[09:48:51.983] Terminal shell path: C:\WINDOWS\System32\cmd.exe
[09:48:52.204] > 
[09:48:52.205] Got some output, clearing connection timeout
[09:48:52.733] > 438e51175601: running
[09:48:52.764] > Script executing under PID: 337910
> Found existing installation at /db/storage/guolicheng/.vscode-server...
> Starting VS Code CLI...
> Removing old logfile at /db/storage/guolicheng/.vscode-server/.cli.17baf841131aa23349f217ca7c570c76ee87b957.log
> Spawned remote CLI: 337948
[09:48:52.779] > Waiting for server log...
[09:48:52.795] > Waiting for server log...
[09:48:52.830] > sh: line 418: kill: (337948) - No such process
> Exec server process not found
> [2025-05-01 09:48:52] error This machine does not meet Visual Studio Code Server's prerequisites, expected either...
>   - find GLIBC >= v2.28.0 (but found v2.18.0 instead) for GNU environments
>   - find /lib/ld-musl-x86_64.so.1, which is required to run the Visual Studio Code Server in musl environments
> 438e51175601: start
> exitCode==207==
> listeningOn====
> osReleaseId==centos==
> arch==x86_64==
> vscodeArch==x64==
> bitness==64==
> tmpDir==/run/user/54579==
> platform==linux==
> unpackResult====
> didLocalDownload==0==
> downloadTime====
> installTime====
> serverStartTime====
> execServerToken==aa111aa1-11aa-1aa1-aa11-11111a111a11==
> platformDownloadPath==cli-alpine-x64==
> SSH_AUTH_SOCK====
> DISPLAY====
> 438e51175601: end
[09:48:52.830] Received install output: 
exitCode==207==
listeningOn====
osReleaseId==centos==
arch==x86_64==
vscodeArch==x64==
bitness==64==
tmpDir==/run/user/54579==
platform==linux==
unpackResult====
didLocalDownload==0==
downloadTime====
installTime====
serverStartTime====
execServerToken==aa111aa1-11aa-1aa1-aa11-11111a111a11==
platformDownloadPath==cli-alpine-x64==
SSH_AUTH_SOCK====
DISPLAY====

[09:48:52.833] Resolver error: Error: 远程主机不满足运行VS Code服务器的先决条件
	at y.ServerInstallError (c:\Users\12626\.vscode\extensions\ms-vscode-remote.remote-ssh-0.119.0\out\extension.js:2:742711)
	at p (c:\Users\12626\.vscode\extensions\ms-vscode-remote.remote-ssh-0.119.0\out\extension.js:2:737130)
	at t.handleInstallOutput (c:\Users\12626\.vscode\extensions\ms-vscode-remote.remote-ssh-0.119.0\out\extension.js:2:738606)
	at t.tryInstall (c:\Users\12626\.vscode\extensions\ms-vscode-remote.remote-ssh-0.119.0\out\extension.js:2:860735)
	at async c:\Users\12626\.vscode\extensions\ms-vscode-remote.remote-ssh-0.119.0\out\extension.js:2:819559
	at async t.withShowDetailsEvent (c:\Users\12626\.vscode\extensions\ms-vscode-remote.remote-ssh-0.119.0\out\extension.js:2:822810)
	at async x (c:\Users\12626\.vscode\extensions\ms-vscode-remote.remote-ssh-0.119.0\out\extension.js:2:816073)
	at async t.resolve (c:\Users\12626\.vscode\extensions\ms-vscode-remote.remote-ssh-0.119.0\out\extension.js:2:820211)
	at async c:\Users\12626\.vscode\extensions\ms-vscode-remote.remote-ssh-0.119.0\out\extension.js:2:1107798
[09:48:52.838] ------




[09:48:52.839]  ---------- [Session Summary] ----------- 
[09:48:52.839] [LinuxPrereqs]: 远程主机可能不符合 glibc 和 libstdc++ VS Code 服务器的先决条件 (远程主机不满足运行VS Code服务器的先决条件)
[09:48:52.839]  ---------------------------------------- 
[09:48:53.196] > 
[09:48:53.514] Opening exec server for ssh-remote+172.17.130.48
[09:48:53.525] Initizing new exec server for ssh-remote+172.17.130.48
[09:48:53.525] Using commit id "17baf841131aa23349f217ca7c570c76ee87b957" and quality "stable" for server
[09:48:53.525] Extensions to install: 
[09:48:53.528] Install and start server if needed
[09:48:53.532] Running script with connection command: "C:\WINDOWS\System32\OpenSSH\ssh.exe" -T -D 63830 "172.17.130.48" sh
[09:48:53.536] Generated SSH command: 'type "C:\Users\12626\AppData\Local\Temp\vscode-linux-multi-line-command-172.17.130.48-51462439.sh" | "C:\WINDOWS\System32\OpenSSH\ssh.exe" -T -D 63830 "172.17.130.48" sh'
[09:48:53.536] Using connect timeout of 17 seconds
[09:48:53.537] Terminal shell path: C:\WINDOWS\System32\cmd.exe
[09:48:53.959] > 
[09:48:53.959] Got some output, clearing connection timeout
[09:48:54.116] "install" terminal command done
[09:48:54.116] Install terminal quit with output: 
[09:48:54.452] > 7c791573fa8b: running
[09:48:54.467] > Script executing under PID: 338106
> Found existing installation at /db/storage/guolicheng/.vscode-server...
[09:48:54.483] > Starting VS Code CLI...
> Removing old logfile at /db/storage/guolicheng/.vscode-server/.cli.17baf841131aa23349f217ca7c570c76ee87b957.log
> Spawned remote CLI: 338144
> Waiting for server log...
[09:48:54.513] > Waiting for server log...
[09:48:54.538] > sh: line 418: kill: (338144) - No such process
> Exec server process not found
> [2025-05-01 09:48:54] error This machine does not meet Visual Studio Code Server's prerequisites, expected either...
>   - find GLIBC >= v2.28.0 (but found v2.18.0 instead) for GNU environments
>   - find /lib/ld-musl-x86_64.so.1, which is required to run the Visual Studio Code Server in musl environments
> 7c791573fa8b: start
> exitCode==207==
> listeningOn====
> osReleaseId==centos==
> arch==x86_64==
> vscodeArch==x64==
> bitness==64==
> tmpDir==/run/user/54579==
> platform==linux==
> unpackResult====
> didLocalDownload==0==
> downloadTime====
> installTime====
> serverStartTime====
> execServerToken==11a11a11-1a11-111a-1a11-1a111a11a1a1==
> platformDownloadPath==cli-alpine-x64==
> SSH_AUTH_SOCK====
> DISPLAY====
> 7c791573fa8b: end
[09:48:54.538] Received install output: 
exitCode==207==
listeningOn====
osReleaseId==centos==
arch==x86_64==
vscodeArch==x64==
bitness==64==
tmpDir==/run/user/54579==
platform==linux==
unpackResult====
didLocalDownload==0==
downloadTime====
installTime====
serverStartTime====
execServerToken==11a11a11-1a11-111a-1a11-1a111a11a1a1==
platformDownloadPath==cli-alpine-x64==
SSH_AUTH_SOCK====
DISPLAY====

[09:48:54.539] Exec server for ssh-remote+172.17.130.48 failed: Error: 远程主机不满足运行VS Code服务器的先决条件
[09:48:54.539] Error opening exec server for ssh-remote+172.17.130.48: Error: 远程主机不满足运行VS Code服务器的先决条件
[09:48:54.539]  ---------- [Session Summary] ----------- 
[09:48:54.539] [LinuxPrereqs]: 远程主机可能不符合 glibc 和 libstdc++ VS Code 服务器的先决条件 (远程主机不满足运行VS Code服务器的先决条件)
[09:48:54.539]  ---------------------------------------- 
[09:48:54.559] > 
[09:48:55.822] "install" terminal command done
[09:48:55.822] Install terminal quit with output: 

```


###### 二、源码位置：vscode/cli/src/util/prereqs.rs

```rust
/// Checks the glibc version, returns "true" if the default server is required.
#[cfg(target_os = "linux")]
async fn check_glibc_version() -> Result<bool, String> {
	#[cfg(target_env = "gnu")]
	let version = {
		let v = unsafe { libc::gnu_get_libc_version() };
		let v = unsafe { std::ffi::CStr::from_ptr(v) };
		let v = v.to_str().unwrap();
		extract_generic_version(v)
	};
	#[cfg(not(target_env = "gnu"))]
	let version = {
		super::command::capture_command("ldd", ["--version"])
			.await
			.ok()
			.and_then(|o| extract_ldd_version(&o.stdout))
	};

	if let Some(v) = version {
		return if v >= *MIN_LDD_VERSION {
			Ok(true)
		} else {
			Err(format!(
				"find GLIBC >= {} (but found {} instead) for GNU environments",
				*MIN_LDD_VERSION, v
			))
		};
	}

	Ok(false)
}
```


###### 下面是将node的依赖库用patchelf改为自己编译的gibc动态库

```bash
[guolicheng@48 /db/storage/guolicheng/.vscode-server/cli/servers/Stable-17baf841131aa23349f217ca7c570c76ee87b957/server] $ /usr/bin/ldd --version
ldd (GNU libc) 2.18
Copyright (C) 2013 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
Written by Roland McGrath and Ulrich Drepper.
[guolicheng@48 /db/storage/guolicheng/.vscode-server/cli/servers/Stable-17baf841131aa23349f217ca7c570c76ee87b957/server] $ which ldd
~/local/glibc-2.28/bin/ldd
[guolicheng@48 /db/storage/guolicheng/.vscode-server/cli/servers/Stable-17baf841131aa23349f217ca7c570c76ee87b957/server] $ ldd --version
ldd (GNU libc) 2.28
Copyright (C) 2018 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
Written by Roland McGrath and Ulrich Drepper.

[guolicheng@48 /db/storage/guolicheng/.vscode-server/cli/servers/Stable-17baf841131aa23349f217ca7c570c76ee87b957/server] $ ldd -r ./node
	linux-vdso.so.1 (0x00007ffe423c5000)
	libdl.so.2 => /db/storage/guolicheng/local/glibc-2.28/lib/libdl.so.2 (0x00007f3ea5477000)
	libstdc++.so.6 => /db/storage/guolicheng/local/gcc-11.4.0/lib64/libstdc++.so.6 (0x00007f3ea5071000)
	libm.so.6 => /db/storage/guolicheng/local/glibc-2.28/lib/libm.so.6 (0x00007f3ea4cf4000)
	libgcc_s.so.1 => /db/storage/guolicheng/local/gcc-11.4.0/lib64/libgcc_s.so.1 (0x00007f3ea4adc000)
	libpthread.so.0 => /db/storage/guolicheng/local/glibc-2.28/lib/libpthread.so.0 (0x00007f3ea48bd000)
	libc.so.6 => /db/storage/guolicheng/local/glibc-2.28/lib/libc.so.6 (0x00007f3ea4505000)
	/db/storage/guolicheng/local/glibc-2.28/lib/ld-linux-x86-64.so.2 (0x00007f3ea567b000)
```


#### 20250610

```bash
版本: 1.100.3 (user setup)
提交: 258e40fedc6cb8edf399a463ce3a9d32e7e1f6f3
日期: 2025-06-02T13:30:54.273Z
Electron: 34.5.1
ElectronBuildId: 11369351
Chromium: 132.0.6834.210
Node.js: 20.19.0
V8: 13.2.152.41-electron.0
OS: Windows_NT x64 10.0.26100

```



```bash
wget https://vscode.download.prss.microsoft.com/dbazure/download/stable/258e40fedc6cb8edf399a463ce3a9d32e7e1f6f3/vscode-server-linux-x64.tar.gz

mkdir -p ${HOME}/.vscode-server/cli/servers/Stable-258e40fedc6cb8edf399a463ce3a9d32e7e1f6f3/

tar -xzf vscode-server-linux-x64.tar.gz

mv vscode-server-linux-x64 server

mv server ${HOME}/.vscode-server/cli/servers/Stable-258e40fedc6cb8edf399a463ce3a9d32e7e1f6f3/

cd ${HOME}/.vscode-server/cli/servers/Stable-258e40fedc6cb8edf399a463ce3a9d32e7e1f6f3/server/

ldd -r ./node

patchelf --set-rpath ~/local/glibc-2.28/lib:~/local/gcc-11.4.0/lib64 ./node

patchelf --set-interpreter ~/local/glibc-2.28/lib/ld-linux-x86-64.so.2 ./node

./node --version

```




#### 禁用VScode升级设置

运行菜单File中Preferences子菜单中选择Settings项，搜索update mode，将其设置为none









