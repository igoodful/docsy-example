---
title: configure_file
description: 生成配置文件
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

这将根据 `config.h.in`中的设置生成 `config.h`文件，通常用于根据`CMake`选项配置源代码
```cmake

configure_file(config.h.in config.h)


```
{{<alert>}}

{{</alert>}}

