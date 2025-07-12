---
title: Graphviz
description: Graphviz
date: 2023-11-06
weight: 30000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{%pageinfo%}}
- [官网](http://www.graphviz.org)
{{%/pageinfo%}}



{{<alert>}}



{{</alert>}}

使用add_subdirectory调用树构建项目的一个限制是，CMake不允许将target_link_libraries与定义在当前目录范围之外的目标一起使用。

对于本示例来说，这不是问题。在下一个示例中，我们将演示另一种方法，我们不使用add_subdirectory，而是使用module include来组装不同的CMakeLists.txt文件，它允许我们链接到当前目录之外定义的目标。

CMake可以使用Graphviz图形可视化软件(http://www.graphviz.org )生成项目的依赖关系图:

```bash
cd build
cmake --graphviz=example.dot ..
dot -T png example.dot -o example.png
```
生成的图表将显示不同目录下的目标之间的依赖关系:



