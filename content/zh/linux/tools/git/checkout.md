---
title: git checkout
description: git checkout
date: 2023-10-31
weight: 2000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
- [checkout](https://www.cnblogs.com/xiaomaomao/p/13849825.html)
{{< /alert >}}


{{< alert title="sort参数详解" color="secondary">}}

{{< /alert >}}








### 拉取远程指定分支到本地

```sql

# "默认仅仅拉取远程的master分支"
 git clone git@github.com:igoodful/learngit.git

# "拉取远程origin的dev分支"
git checkout -b dev origin/dev

git add env.txt

git commit -m "add env"

git push origin dev

```


### 撤销工作区的修改

> 工作区修改了文件,但是未使用 git add 添加到暂存区

```bash
git checkout -- file

git checkout -- readme.txt
```

命令`git checkout -- readme.txt`意思就是，把`readme.txt`文件在工作区的修改全部撤销，这里有两种情况：

- 一种是readme.txt自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；

- 一种是readme.txt已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

总之，就是让这个文件回到最近一次git commit或git add时的状态。


### 分支管理

```sql
git checkout -b dev

git branch dev

git checkout dev


```



```sql
git switch dev

git stash

git checkout master

git checkout -b issue-101

git add readme.txt

git commit -m "fix bug 101"

git switch master

git merge --no-ff -m "merged bug fix 101" issue-101

git switch dev

git status

git stash list

git stash pop

git stash apply stash@{0}
```
