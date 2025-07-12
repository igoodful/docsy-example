---
title: 25. cmake
description: cmake
date: 2023-10-31
weight: 250
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

{{< alert >}}

{{< /alert >}}



| 选项                                     | 作用                           | 示例                                |
| :--------------------------------------- | :----------------------------- | :---------------------------------- |
| `-f FILE, --file=FILE, --makefile=FILE ` | 指定`makefile`位置             | `make  -f CMakeFiles/Makefile2 all` |
| `-o`                                     | 指定生成可执行文件的名称       | `-o dir/tmp/mi_open.cc.o`           |
| `-c`                                     | 只编译，不链接，只生成目标文件 |                                     |


GNU Autotools 有非常具体的预期规范，如果你经常下载和构建源代码，可能大多数都很熟悉。首先，源代码本身应该位于一个名为 src 的子目录中。

你的项目不必遵循所有这些预期规范，但如果你将文件放在非标准位置（从 Autotools 的角度来看），那么你将不得不稍后在 Makefile 中对其进行调整。

此外，这些文件是必需的：

NEWS
README
AUTHORS
ChangeLog
你不必主动使用这些文件，它们可以是包含所有信息的单个汇总文档（如 README.md）的符号链接，但它们必须存在。













