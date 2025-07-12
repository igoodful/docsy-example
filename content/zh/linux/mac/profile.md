---
title: profile
description: profile
date: 2023-10-24
weight: 20000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}


{{< /alert >}}



## /etc/profile

```bash
PS1="\u@\h:\w\\$"
PS1="\e[1;33m\u@\h\e[m:\e[1;35m\w\e[m\\$ "
TMOUT=60000
HISTTIMEFORMAT="%F %T `whoami` "
export HISTSIZE=1024
export HISTFILESIZE=102400
```


## 问题

#### Error: unknown or unsupported macOS version: :dunno

```bash
Running `brew update --auto-update`...
Warning: You are using macOS 15.
We do not provide support for this pre-release version.
It is expected behaviour that some formulae will fail to build in this pre-release version.
It is expected behaviour that Homebrew will be buggy and slow.
Do not create any issues about this on Homebrew's GitHub repositories.
Do not create any issues even if you think this message is unrelated.
Any opened issues will be immediately closed without response.
Do not ask for help from Homebrew or its maintainers on social media.
You may ask for help in Homebrew's discussions but are unlikely to receive a response.
Try to figure out the problem yourself and submit a fix as a pull request.
We will review it but may or may not accept it.

Error: unknown or unsupported macOS version: :dunno

```

- 解决
```bash
brew update-reset

brew install ansible


```

#### 安装指定版本

brew link --force ansible@9

```bash

brew search ansible

2025-03-26T21:54:51 [igoodful@/Users/igoodful] $ brew install ansible@9
==> Downloading https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles/api/formula.jws.json
==> Downloading https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles/api/cask.jws.json
Warning: ansible@9 9.13.0 is already installed and up-to-date.
To reinstall 9.13.0, run:
  brew reinstall ansible@9
2025-03-26T21:55:04 [igoodful@/Users/igoodful] $ ansible
-bash: ansible: command not found
2025-03-26T21:55:13 [igoodful@/Users/igoodful] $ brew link --force ansible@9

```


