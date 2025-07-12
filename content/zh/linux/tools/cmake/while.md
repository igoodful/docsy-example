---
title: while
description: 循环语句
date: 2023-11-17
weight: 30000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}



{{< /alert >}}





```cmake

while(<condition>)

<commands>

endwhile()


```
{{<alert>}}
- condition：这是循环继续执行的条件。只要条件评估为真（即，非0或非空字符串，或特定的CMake真值，如 ON、 TRUE等），循环就会继续。
- commands：在每次循环迭代中执行的命令集。
{{</alert>}}














