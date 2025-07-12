---
title: git reset
description: git reset
date: 2023-10-31
weight: 2000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
- [reset](https://www.cnblogs.com/xiaomaomao/p/13849825.html)
{{< /alert >}}


{{< alert title="sort参数详解" color="secondary">}}

{{< /alert >}}



> 工作区修改了文件,使用 git add 添加到了暂存区



```bash
# 将提交到暂存区的内容撤销回工作区
git reset HEAD filename

# 将工作区的修改去掉
git checkout -- filename

```


> 工作区修改了文件,使用 git add 添加到了暂存区,并且使用了 git commit 提交到了分支上

```bash
git reset --hard 提交版本号
```


首先需要知道工作区（working diretory）和暂存区（Stage）这两个概念
- 工作区的概念不仅包含你实际更改的文件还应当包括当前修改但未 add 存入暂存区的文件变化信息
- 暂存区的作用则是临时存储文件的变化信息,在 git add file 操作之后,暂存区中将记录 file 文件上的修改信息.暂存区的存在更细化了时间节点,要知道有重大改变的版本或者是在一次修改工作整体完成之后才使用 commit
- 而在这之间需要保存的修改,自然需要一个缓存区暂时存放,廖雪峰的 git 教程中在"撤销修改"部分中,提到了几种情景和相应的 git checkout 与 git reset 命令
- 通过我的思考,在这个更直接地分析这两个命令的含义
  - `git checkout -- file`:撤销对工作区修改,这个命令是以最新的存储时间节点（add 和 commit）为参照,覆盖工作区对应文件 file,这个命令改变的是工作区
  - `git reset HEAD -- file`:清空 add 命令向暂存区提交的关于file文件的修改,这个命令仅改变暂存区,并不改变工作区,这意味着在无任何其他操作的情况下,工作区中的实际文件同该命令运行之前无任何改变(仅仅是将暂存区的状态变成了工作区)













