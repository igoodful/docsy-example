---
title: vscode
description: vscode
date: 2023-10-31
weight: 2000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

{{< /alert >}}


{{< alert title="sort参数详解" color="secondary">}}

{{< /alert >}}





## 配置


#### launch.json
> `Debug mysqld`


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
                                "--defaults-file=/root/local/mysql_8032/conf/my.cnf",
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






## 插件




#### Bookmarks

1. 创建或删除书签：ctrl+alt+k
2. 跳转到前一个书签：Ctrl+alt+J
3. 跳转到后一个书签：Ctrl+alt+L


- 查看所有书签：你可以在VS Code的活动栏中点击Bookmarks图标，查看所有的书签。在这个视图中，你可以点击任何一个书签，VS Code会立即导航到对应的代码行。

- 删除书签：你可以在书签视图中右键点击任何一个书签，然后选择"Remove Bookmark"来删除书签。如果你想删除所有的书签，你可以选择"Remove All Bookmarks"。









#### `clang-format`

能够格式化哪些语言？
- c
- c++
- java
- javascript


- [clang-format](https://clang.llvm.org/docs/ClangFormatStyleOptions.html)
- [详解 clang-format 配置选项](https://blog.csdn.net/weixin_44488341/article/details/130864934)
- `.clang-format`文件使用 `YAML` 格式，大小写敏感
- 获取特定预定义样式的所有配置选项的配置文件：`clang-format -style=llvm -dump-config > .clang-format`

常见的样式：
- [LLVM](https://llvm.org/docs/CodingStandards.html)
- [Google](http://google-styleguide.googlecode.com/svn/trunk/cppguide.xml)
- [Chromium](http://www.chromium.org/developers/coding-style)
- [Mozilla](https://developer.mozilla.org/en-US/docs/Developer_Guide/Coding_Style)
- [WebKit](https://www.webkit.org/code-style-guidelines/)








## 远程离线安装


1. vscode界面》帮助》关于：
```yaml
版本: 1.85.2 (user setup)
提交: 8b3775030ed1a69b13e4f4c628c612102e30a681
日期: 2024-01-18T06:40:10.514Z
Electron: 25.9.7
ElectronBuildId: 26354273
Chromium: 114.0.5735.289
Node.js: 18.15.0
V8: 11.4.183.29-electron.0
OS: Windows_NT x64 10.0.22621
```

2. 手动下载：

```yaml
x64: https://update.code.visualstudio.com/commit:8b3775030ed1a69b13e4f4c628c612102e30a681/server-linux-x64/stable
arm64: https://update.code.visualstudio.com/commit:8b3775030ed1a69b13e4f4c628c612102e30a681/server-linux-arm64/stable
```

- [x64](https://update.code.visualstudio.com/commit:8b3775030ed1a69b13e4f4c628c612102e30a681/server-linux-x64/stable)
- [arm ](https://update.code.visualstudio.com/commit:8b3775030ed1a69b13e4f4c628c612102e30a681/server-linux-arm64/stable)





#### 关闭windows的防火墙



#### 服务器ssh转发端口被禁用

报错信息：
```bash
Authorized users only. All activities may be monitored and reported.
37cacbdd778b: running
Acquiring lock on /root/.vscode-server/bin/8b3775030ed1a69b13e4f4c628c612102e30a681/vscode-remote-lock.root.8b3775030ed1a69b13e4f4c628c612102e30a681
Found existing installation at /root/.vscode-server/bin/8b3775030ed1a69b13e4f4c628c612102e30a681...
Checking /root/.vscode-server/.8b3775030ed1a69b13e4f4c628c612102e30a681.log and /root/.vscode-server/.8b3775030ed1a69b13e4f4c628c612102e30a681.pid for a running server
Looking for server with pid: 4125701
Found running server...

*
* Visual Studio Code Server
*
* By using the software, you agree to
* the Visual Studio Code Server License Terms (https://aka.ms/vscode-server-license) and
* the Microsoft Privacy Statement (https://privacy.microsoft.com/en-US/privacystatement).
*

Checking server status on port 38247 with wget
37cacbdd778b: start
SSH_AUTH_SOCK====
DISPLAY====
webUiAccessToken====
listeningOn==38247==
osReleaseId==kylin==
arch==aarch64==
vscodeArch==arm64==
bitness==64==
tmpDir==/run/user/0==
platform==linux==
unpackResult====
didLocalDownload==0==
downloadTime====
installTime====
extInstallTime====
serverStartTime====
connectionToken==fa3ef11e-e5b3-484d-8355-b092888307f7==
37cacbdd778b: end
channel 3: open failed: administratively prohibited: open failed
channel 4: open failed: administratively prohibited: open failed
```



1. `vi /etc/ssh/sshd_config`:修改内容，yes表示允许，no表示不允许
```bash
AllowTcpForwarding yes
GatewayPorts yes
```

重启下sshd服务：
```bash
systemctl restart sshd
```


2. `vi /etc/sysctl.conf`，修改内容：

```ini
net.ipv4.ip_forward = 1
```

立即生效：
```bash
sysctl -p
```






## 保存时，自动格式化

editor.formatOnSave






## Colorful Comments






## WakaTime




## Error Lens




