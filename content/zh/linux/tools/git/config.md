---
title: git config
description: git config
date: 2025-02-09
weight: 2000
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;


Git相关的配置文件有三个

- `/etc/gitconfig` 包含了适用于系统所有用户和所有项目的值
- `$HOME/.gitconfig` 只适用于当前登录用户的配置
- `project/.git/config` 适用于特定git项目的配置。















## git config --global

## 大文件关闭git传输压缩，提高大文件传输速度
```bash
git config --global core.compression 0

# 处理window与linux之间的回车换行
git config --global core.autocrlf true

# 账号
git config --global user.name igoodful
# 邮箱
git config --global user.email igoodful@qq.com


git config --global --unset http.proxy
```


## git config --global --unset

```bash
git config --global --unset http.proxy



# 设置http:
git config --global http.proxy http://127.0.0.1:1080
# 设置https:
git config --global https.proxy https://127.0.0.1:1080
# 设置socks:
git config --global http.proxy 'socks5://127.0.0.1:1080'
git config --global https.proxy 'socks5://127.0.0.1:1080'
## 取消
git config --global --unset http.proxy
git config --global --unset https.proxy

# 自动处理windows与linux换行
git config --global core.autocrlf true
# 设置 git status utf-8编码
git config --global core.quotepath false 
# 设置Git GUI界面utf-8编码
git config --global gui.encoding utf-8 
# 设置commit信息utf-8编码
git config --global i18n.commit.encoding utf-8 
# 设置输出 log utf-8 编码
git config --global i18n.logoutputencoding utf-8 


# git log 日期格式
git config --global log.date iso-strict-local
git config --global log.date iso




```


### log.date
- iso
```bash
[igoodfu@48 /db/storage/guolicheng/ticbase_deploy] $ git config --global log.date iso
[igoodfu@48 /db/storage/guolicheng/ticbase_deploy] $ git --version
git version 1.8.3.1
[igoodfu@48 /db/storage/guolicheng/ticbase_deploy] $ git log
commit 531543c03ae91af08088730e65bed2555838d42f
Author: igoodful <igoodful@qq.com>
Date:   2025-02-21 15:37:25 +0800

    mysql done

commit 453a995995af094a6198ac9d62785dc0de474e06
Author: igoodful <igoodful@qq.com>
Date:   2025-02-21 12:38:08 +0800

    init

```









## git config --list

```bash
diff.astextplain.textconv=astextplain
filter.lfs.clean=git-lfs clean -- %f
filter.lfs.smudge=git-lfs smudge -- %f
filter.lfs.process=git-lfs filter-process
filter.lfs.required=true
http.sslbackend=openssl
http.sslcainfo=C:/guolicheng/app/Git/mingw64/etc/ssl/certs/ca-bundle.crt
core.autocrlf=true
core.fscache=true
core.symlinks=false
pull.rebase=false
credential.helper=manager
credential.https://dev.azure.com.usehttppath=true
init.defaultbranch=master
safe.directory=D:/hugo/maths
safe.directory=D:/hugo/maths
safe.directory=C:/guolicheng/hugo/maths_doc
safe.directory=C:/guolicheng/maths_doc
safe.directory=D:/maths_doc
safe.directory=D:\maths\maths.git
safe.directory=D:/tmp/maths
core.autocrlf=true
core.compression=0
user.email=igoodful@qq.com
user.name=igoodful
http.postbuffer=15728640000
core.repositoryformatversion=0
core.filemode=false
core.bare=false
core.logallrefupdates=true
core.symlinks=false
core.ignorecase=true
remote.origin.url=guolicheng@172.17.130.48:/db/storage/guolicheng/scripts/mygit/mygit.git
remote.origin.fetch=+refs/heads/*:refs/remotes/origin/*
branch.master.remote=origin
branch.master.merge=refs/heads/master
remote.test_mygit.url=guolicheng@172.17.130.48:/db/storage/guolicheng/scripts/test_mygit/test_mygit.git
remote.test_mygit.fetch=+refs/heads/*:refs/remotes/test_mygit/*
```

## core



## diff


## difftool


## fetch




## format



## fsck




## gc




## gitcvs



## gitweb



## gpg




## grep








## gui



## guitool



## http









## log


## merge






## mergetool



## pack




## pull







## push






## rebase






## receive






## remote










## repack







## sendemail





## status





## submodule






## tag



















