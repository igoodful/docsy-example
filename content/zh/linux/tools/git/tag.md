---
title: git tag
description: >
  发布一个版本时，我们通常先在版本库中打一个标签（tag）
date: 2017-01-05
weight: 4
---

{{< alert >}}
tag就是一个让人容易记住的有意义的名字，它跟某个commit绑在一起。

默认标签是打在最新提交的commit上的

在Git中打标签非常简单，首先，切换到需要打标签的分支上

注意，标签不是按时间顺序列出，而是按字母排序的。
{{< /alert >}}





### 一、本地添加标签

#### （1）master分支上最新commit上打标签
```sql

git branch

git checkout master

git tag v2.0

git tag

```

#### （2）master分支上指定commit上打标签
```sql
git branch

git checkout master

git log --pretty=oneline --abbrev-commit

git tag v1.0 f52c633

#  "创建带有说明的标签，用-a指定标签名，-m指定说明文字"
git tag -a v1.0 -m "version 1.0 released" f52c633

git tag

# "查看标签信息"
git show v1.0



```





### 二、推送标签到远程


```sql
# "推送某个标签到远程"
git push origin v1.0

# "一次性推送全部尚未推送到远程的本地标签"
git push origin --tags


```


### 三、删除本地标签

```sql
git branch

git checkout master

git tag -d v1.0

```

### 四、删除远程标签


```sql
git branch

git checkout master

git tag -d v0.9

git push origin :refs/tags/v0.9

```




