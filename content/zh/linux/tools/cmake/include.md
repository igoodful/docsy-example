---
title: include
description: include
date: 2023-11-06
weight: 30000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{%pageinfo%}}
- `include` 指令一般用于语句的复用，也就是说，如果有一些语句需要在很多CMakeLists.txt文件中使用，为避免重复编写，可以将其写在.cmake文件中，然后在需要的CMakeLists.txt文件中进行include操作就行了。

在CMakeLists.txt中进行include然后调用函数。包含方式有两种：
1. 直接包含路径及.cmake文件名
2. 先设置`CMAKE_MODULE_PATH`变量，再包含文件(**不要带.cmake文件扩展名，带了会提示找不到**)
{{%/pageinfo%}}



{{<alert>}}



{{</alert>}}






