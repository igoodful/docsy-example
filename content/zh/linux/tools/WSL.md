---
title: WSL
description: WSL
date: 2023-11-06
weight: 30000


---
{{%pageinfo%}}
WSL
{{%/pageinfo%}}

{{<note>}}
<!---->






{{</note>}}



```sql
-- 列出可用的 Linux 发行版
wsl --list --online

-- 列出已安装的 Linux 发行版
wsl --list --verbose

-- 安装
wsl --install

-- 设置默认 WSL 版本
wsl --set-default-version <Version>

-- 设置默认 Linux 发行版
wsl --set-default <Distribution Name>

-- 更新 WSL
wsl --update

-- 检查 WSL 状态
wsl --status

-- 检查 WSL 版本
wsl --version
```

### wsl: 检测到 localhost 代理配置，但未镜像到 WSL。NAT 模式下的 WSL 不支持 localhost 代理。
> 解决：在Windows中的C:\Users\<your_username>目录下创建一个.wslconfig文件，然后在文件中写入如下内容

```sql
[experimental]
autoMemoryReclaim=gradual
networkingMode=mirrored
dnsTunneling=true
firewall=true
autoProxy=true
```
> 然后用`wsl --shutdown`关闭WSL，之后再重启，提示就消失了。




