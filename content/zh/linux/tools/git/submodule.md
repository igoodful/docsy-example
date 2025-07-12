---
title: git submodule
description: git submodule
date: 2024-06-05
weight: 1000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

1. submodule 的父子关系存在哪里？ 关系是保存在主项目的Git仓库中
2. submodule 的父子关系信息怎么存？父子关系的信息保存在主项目的.gitmodules文件


{{< /alert >}}


## 查看子模块

```bash
git submodule

git status --submodule

git status --submodule=recursive

```



## 添加子模块

```bash
# 其中 <repository> 是子模块仓库的 URL，<path> 是子模块在父仓库中的路径
git submodule add <repository> <path>

git submodule add https://github.com/gohugoio/hugo.git

# 将子模块docsy添加到当前themes目录下面
git submodule add https://github.com/google/docsy.git themes

```

## 初始化子模块

```bash
git submodule init



```




## 更新子模块


```bash
# 更新项目内子模块到最新版本
git submodule update

# 更新子模块为远程项目的最新版本
git submodule update --remote

# 拉取所有子模块
git submodule foreach git pull

# 使用 rebase 来更新子模块（如果子模块有本地更改）
git submodule update --rebase

```

## 初始化并更新子模块
```bash
git submodule update --init


```



## 克隆包含子模块的项目


#### 1. 递归克隆整个项目
```bash
git clone https://github.com/maonx/vimwiki-assets.git  --recursive
```

#### 2. 克隆父项目，再更新子模块

1. 先git clone 主项目仓库并进入主项目文件夹，这时候submodule的文件夹都是空的
2. 执行git submodule init [submodule的文件夹的相对路径]
3. 执行git submodule update [submodule的文件夹的相对路径]

第二、三步可合并：git submodule update --init [submodule的文件夹的相对路径]

```bash
git clone https://github.com/maonx/vimwiki-assets.git

git submodule

git submodule init

git submodule update

```


## 删除子模块


#### 1. 删除子模块文件夹
```bash
git rm --cached xxx

rm -rf xxx

```

#### 2. `.gitmodules`

```bash
2024-06-05T20:33:33 [root@node77 /root/test] $ cat .gitmodules
[submodule "hugo"]
	path = hugo
	url = https://github.com/gohugoio/hugo.git
[submodule "themes"]
	path = themes
	url = https://github.com/google/docsy.git

# 删除项目目录下.gitmodules文件中子模块相关条目

```



#### 3. `.git/config`
```bash
2024-06-05T20:35:37 [root@node77 /root/test] $ cat .git/config
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true
[submodule "hugo"]
	url = https://github.com/gohugoio/hugo.git
	active = true
[submodule "themes"]
	url = https://github.com/google/docsy.git
	active = true

# 删除配置项中子模块相关条目
```

#### 4. `.git/modules/`

```bash
2024-06-05T20:35:43 [root@node77 /root/test] $ ll .git/modules/
total 8
drwxr-xr-x 8 root root 4096 Jun  5 14:14 hugo
drwxr-xr-x 8 root root 4096 Jun  5 20:32 themes

# 删除模块下的子模块目录，每个子模块对应一个目录，注意只删除对应的子模块目录即可
rm .git/module/*
```











