---
title: source
description: source
date: 2023-10-31
weight: 300000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

{{< /alert >}}


{{< alert title="source参数详解" color="secondary">}}

{{< /alert >}}



## 一、原理

以下两个命令等价：

- `source /home/test.sh`
  - 用source执行的脚本所做的任何改变会影响当前shell，而不是新开一个shell环境

- `. /home/test.sh`
  - 当该脚本没有可执行权限时，需要root用户才能执行














