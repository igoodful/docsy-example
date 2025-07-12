---
title: git fetch
description: >
  A short lead description about this content page. It can be **bold** or _italic_ and can be split over multiple paragraphs.
date: 2017-01-05
weight: 4
---

{{< alert >}}
This is a placeholder page. Replace it with your own content.
{{< /alert >}}





`git fetch` 和 `git pull` 都是用于从远程存储库获取更新的 Git 命令，但它们之间有重要的区别：

1. `git fetch`:
   - 仅从远程存储库下载最新的提交和分支信息，但不自动合并这些更改到你的当前分支。
   - 它将更新远程分支的引用，但你需要手动合并或将这些更改应用到你的分支。

2. `git pull`:
   - 执行了 `git fetch` 的操作，然后自动将远程分支的更改合并到你的当前分支。
   - 这可能会导致自动合并冲突，需要解决。

因此，主要区别在于自动合并：`git fetch` 仅获取更新，不自动合并，而 `git pull` 获取更新并自动尝试合并它们。选择使用哪个取决于你的工作流程和需求。如果你想手动审查和控制合并，使用 `git fetch` 后再手动合并。如果你想自动获取并合并最新更改，可以使用 `git pull`。




[git pull](https://zhuanlan.zhihu.com/p/123370920)









