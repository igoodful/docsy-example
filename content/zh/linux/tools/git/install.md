---
title: git安装、配置
description: >
  Git是目前世界上最先进的分布式版本控制系统.
date: 2017-01-05
weight: 4
---

{{< alert >}}


1. Git可以在Linux、Unix、Mac和Windows







{{< /alert >}}


### 安装






### 配置文件


```sql
git config --global user.name "igoodful"

git config --global user.email "igoodful@qq.com"

git config --global color.ui true


```



```sql
# "cat .gitignore"

# Windows:
Thumbs.db
ehthumbs.db
Desktop.ini

# Python:
*.py[cod]
*.so
*.egg
*.egg-info
dist
build

# My configurations:
db.ini
deploy_key_rsa

```

配置Git的时候，加上--global是针对当前用户起作用的，如果不加，那只针对当前的仓库起作用。

配置文件放哪了？每个仓库的Git配置文件都放在.git/config文件中：


```sql
# "cat .git/config"
[core]
    repositoryformatversion = 0
    filemode = true
    bare = false
    logallrefupdates = true
    ignorecase = true
    precomposeunicode = true
[remote "origin"]
    url = git@github.com:igoodful/learngit.git
    fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
    remote = origin
    merge = refs/heads/master
[alias]
    last = log -1

```


而当前用户的Git配置文件放在用户主目录下的一个隐藏文件.gitconfig中：

```sql
# "cat .gitconfig"
[alias]
    co = checkout
    ci = commit
    br = branch
    st = status
[user]
    name = igoodful
    email = igoodful@qq.com

```
配置别名也可以直接修改这个文件，如果改错了，可以删掉文件重新通过命令配置。







```sql

# "在10.10.10.10机器上执行："

useradd git

su - git

ssh-keygen

mkdir -p /home/git/maths

cd /home/git/maths

git init --bare maths.git

#

git clone git@10.10.10.10:/home/work/maths/maths.git

```


