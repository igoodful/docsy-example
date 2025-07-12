---
title: option
description: 定义选项
date: 2023-11-17
weight: 400


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
指定C++标准

{{< /alert >}}

这定义了一个选项 `MY_OPTION`，可以通过 `-DMY_OPTION=ON`或 `-DMY_OPTION=OF`F在`CMake`命令行中设置。
```cmake

option(MY_OPTION "Description of my option" ON)


```
{{<alert>}}

{{</alert>}}

