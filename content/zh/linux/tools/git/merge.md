---
title: git merge
description: >
  人生不如意之事十之八九，合并分支往往也不是一帆风顺的
date: 2017-01-05
weight: 4
---

{{< alert >}}

1. Fast forward 从分支历史上不能看出分支信息

2. --no-ff 从分支历史上就可以看出分支信息

{{< /alert >}}



### 没有冲突
```sql
git switch -c dev

git add readme.txt

git commit -m "add merge"

git switch master

git merge --no-ff -m "merge with no-ff" dev

git log --graph --pretty=oneline --abbrev-commit


```

### 出现冲突
```sql
git switch -c dev

git add readme.txt

git commit -m "add merge"

git switch master

git merge --no-ff -m "merge with no-ff" dev

# "查看冲突"
git status

# "解决冲突"

git add

git commit -m

# "查看分支合并情况"
git log --graph --pretty=oneline --abbrev-commit


git branch -d feature1

```
