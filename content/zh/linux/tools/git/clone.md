---
title: git clone
description: git clone
date: 2024-06-05
weight: 1000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}

{{< /alert >}}






## depth

depth用于指定克隆深度，为1即表示只克隆最近一次commit

```bash
git clone --depth 1 https://github.com/google/docsy-example.git

```

说明：可以看到我们只克隆下包含最近一次commit的一个分支，这样这个项目文件就不会很大


```bash
git clone --depth 1 --branch main https://github.com/google/docsy-example.git

```

而如果我们想只克隆某个指定分支的最近一次commit，可以使用下面命令


用 git clone --depth=1 的好处是限制 clone 的深度，不会下载 Git 协作的历史记录，这样可以大大加快克隆的速度


