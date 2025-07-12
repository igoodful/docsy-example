---
title: golang
description: golang
date: 2025-05-27
weight: 100
viz: true
---


<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;

$ \rightleftharpoons $

{{< alert >}}

Backup & Restore (BR) 是一个对 TiDB 进行分布式备份和恢复的工具，可以高效地对大数据量的 TiDB 集群进行数据备份和恢复。

{{< /alert >}}

## 安装golang

- https://go.dev/dl/
- https://go.dev/dl/go1.23.9.linux-amd64.tar.gz

#### 下载
```bash
cd ~
tar -xzf go1.23.9.linux-amd64.tar.gz

```

#### `vim ~/.bashrc`
```bash
export GOROOT=${HOME}/go
export PATH=${GOROOT}/bin:$PATH
export GOPATH=${HOME}/gopath

```

#### 配置代理

```bash
go env -w GO111MODULE=on

go env -w GOPROXY=https://goproxy.cn,direct

```


#### vscode配置

`ctrl+shift+p` 》`go:Install/Update Tools`

```bash
2025-05-27 14:32:01.081 [info] Try to start language server - activation (enabled: true)
2025-05-27 14:32:01.288 [info] Running language server gopls(v0.18.1/go1.23.9)
2025-05-27 14:32:52.949 [info] Tools environment: GOPATH=/db/storage/guolicheng/gopath, GOTOOLCHAIN=auto
2025-05-27 14:32:52.949 [info] Installing 7 tools at /db/storage/guolicheng/gopath/bin
2025-05-27 14:32:52.949 [info]   gopls
2025-05-27 14:32:52.949 [info]   gotests
2025-05-27 14:32:52.949 [info]   gomodifytags
2025-05-27 14:32:52.949 [info]   impl
2025-05-27 14:32:52.949 [info]   goplay
2025-05-27 14:32:52.949 [info]   dlv
2025-05-27 14:32:52.949 [info]   staticcheck
2025-05-27 14:32:52.949 [info] 
2025-05-27 14:32:54.253 [info] Installing golang.org/x/tools/gopls@v0.18.1 (/db/storage/guolicheng/gopath/bin/gopls) SUCCEEDED
2025-05-27 14:32:54.434 [info] Try to start language server - installation (enabled: true)
2025-05-27 14:32:54.613 [info] Running language server gopls(v0.18.1/go1.23.9)
2025-05-27 14:32:57.071 [info] Installing github.com/cweill/gotests/gotests@v1.6.0 (/db/storage/guolicheng/gopath/bin/gotests) SUCCEEDED
2025-05-27 14:33:05.254 [info] Installing github.com/fatih/gomodifytags@v1.17.0 (/db/storage/guolicheng/gopath/bin/gomodifytags) SUCCEEDED
2025-05-27 14:33:13.002 [info] Installing github.com/josharian/impl@v1.4.0 (/db/storage/guolicheng/gopath/bin/impl) SUCCEEDED
2025-05-27 14:33:15.506 [info] Installing github.com/haya14busa/goplay/cmd/goplay@v1.0.0 (/db/storage/guolicheng/gopath/bin/goplay) SUCCEEDED
2025-05-27 14:33:16.804 [info] Installing github.com/go-delve/delve/cmd/dlv@latest (/db/storage/guolicheng/gopath/bin/dlv) SUCCEEDED
2025-05-27 14:33:25.531 [info] Installing honnef.co/go/tools/cmd/staticcheck@latest (/db/storage/guolicheng/gopath/bin/staticcheck) SUCCEEDED
2025-05-27 14:33:25.532 [info] 
2025-05-27 14:33:25.532 [info] All tools successfully installed. You are ready to Go. :)

```

#### `Go`插件
```bash
Rich Go language support for Visual Studio Code

```
