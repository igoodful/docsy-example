---
title: staticx
description: 将一个Python应用程序和它的所有依赖项捆绑成一个单一的软件包
date: 2025-03-16
weight: 3400
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

众所周知，Python 属于动态语言。

通过上一步，我们已经得到了打包好的「动态」可执行文件。若我们直接将该文件部署到其他机器，兴许能够成功运行。因为，即使是在同平台和同架构的情况下，你仍有极大概率因动态链接库的依赖问题而翻车，特别是 Linux 平台。

PyInstaller 构建的可执行文件不是完全静态的，因为它仍然依赖于系统的 libc。在 Linux 下，GLIBC 的 ABI 是向后兼容的，但不是向前兼容的。因此，如果你用较新的 GLIBC 链接，你就不能在较旧的系统上运行编译好的可执行文件。

你此时可能想说：“那么，使用 PyInstaller 前指定旧的 GLIBC 不就好了吗？”

问题在于，Python 应用程序最关键的 Python 解释器 libpython.so 和其他动态库仍然依赖于较新的 GLIBC。一种解决方法是在你身边最老的系统上编译 Python 解释器及其模块。这确实可行但有些麻烦，好在我们还有其他手段。

既然动态链接库存在依赖问题，那么直接将 Python 应用程序编译成「静态」可执行文件就是最简单粗暴的解决方法。这里，我们需要使用如 StaticX 这样的工具。

首先，我们需要安装 StaticX 包。


```bash
pip install -U staticx

staticx <动态可执行文件路径> <静态可执行文件路径>

```
现在，你可以在你所指定的静态可执行文件路径找到最终生成的「静态」可执行文件，不再受 GLIBC 所困。



## 若要使用 StaticX，除了安装 StaticX 包，你还需要确保你的开发环境中已安装如下命令：

ldd

readelf

objcopy

patchelf

你可以在 Shell 中执行 command -v <COMMAND> 来检查命令是否存在。如有缺失，这些命令也都可以通过 Linux 发行版的包管理器轻松安装。




## 存在的问题

StaticX 生成的「静态」可执行文件在运行时将在系统的 /tmp/ 路径下创建随机临时文件夹，其中存放有应用程序运行所需的库，并且该目录将在应用程序退出时被自动清除。

应用程序在运行时以该临时文件夹为其工作目录，这意味着诸如 __file__ 变量和 os, pathlib 等库所获取到的路径将出现异常。






## 参考

- [StaticX](https://hydrotho.github.io/Compiling-Python-Application-Into-Static-Binary-Using-PyInstaller-And-StaticX/)

















##



























