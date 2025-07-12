---
title: add_subdirectory
description: 项目嵌套
date: 2023-11-17
weight: 30000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}
- 在一个大型项目中，通常会涉及到多个组件，比如库（静态库或动态库）、应用程序、测试单元等。为了更好地管理和维护这些组件，我们可以将它们组织成不同的子目录，并为每个子目录创建一个 CMakeLists.txt文件。

- `add_subdirectory` 增加的构建子目录，CMake构建工程会自动将该子目录添加到编译和链接的搜索目录中，以保证整个构建工程能满足依赖，这也是为什么使用add_subdirectory后不需要将子文件夹加入到头文件或库文件搜索目录也能搜索到子目录的头文件或库文件。
{{< /alert >}}


```cmake

add_subdirectory(source_dir [binary_dir] [EXCLUDE_FROM_ALL])


```
{{<alert>}}
- `source_dir` 指定了包含 `CMakeLists.txt`文件的子目录的路径。`CMake`将会处理这个目录下的 `CMakeLists.txt`，并包含其定义的目标（`targets`）到主项目中。

- `binary_dir` 是可选参数，用来指定生成的中间文件和目标文件的存放路径。如果不指定，`CMake`默认在当前正在执行的构建目录中创建与 `source_dir`同名的目录作为` binary_dir`。

- `EXCLUDE_FROM_ALL` 也是可选的。添加这个选项会使得这个子目录中的目标不会被顶级目标 `ALL`包含，即使用 `make`或 `make all`命令时，这个子目录中的目标不会被构建。这对于可选组件或示例代码非常有用。
{{</alert>}}






