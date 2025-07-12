---
title: doxygen
description: doxygen
date: 2025-05-29
weight: 100
viz: true
---


<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;

$ \rightleftharpoons $

{{< alert >}}

- https://www.doxygen.nl/download.html
- https://github.com/doxygen/doxygen
- 二进制安装包：https://www.doxygen.nl/files/doxygen-1.14.0.linux.bin.tar.gz
- 源码安装包：https://www.doxygen.nl/files/doxygen-1.14.0.src.tar.gz
- [graphviz](https://graphviz.org/download/)
- https://www.doxygen.nl/manual/docblocks.html
- https://doxygen.cpp.org.cn/manual/doxygen_usage.html
{{< /alert >}}


## 简介

支持：`IDL, Java, JavaScript, Csharp (C#), C, C++, Lex, D, PHP, md (Markdown), Objective-C, Python, Slice,VHDL, Fortran`


#### 支持的语言

- `C`
- `C++`
- `Python`
- `Java`
- `C#`


#### 支持生成的文档格式

- `HTML`
- `RTF`
- `PDF`


## 安装

- 建议使用ubuntu-25.10版本及以上

```bash
yum -y install graphviz

# 源码升级gcc和glibc后源码安装


```



## 生成配置文件: `Doxygen.config`
```bash
doxygen -g Doxygen.config

```

## 详解: `Doxygen.config`
```toml


```



| 配置项                   | 建议值              | 默认值    | 含义                                                           |
|:-------------------------|:--------------------|:----------|:---------------------------------------------------------------|
| `DOXYFILE_ENCODING`      | `UTF-8`             | `UTF-8`   | 指定Doxygen配置文件的字符编码格式                              |
| `GENERATE_HTMLHELP`      | `YES`               | `NO`      | 是否生成 HTML Help（CHM 文件）                                   |
| `CHM_FILE`               | 项目名称            | ``        | CHM文件名称                                                    |
| `SOURCE_BROWSER`         | `YES`               | `NO`      | 是否生成源代码浏览器                                           |
| `INLINE_SOURCES`         | `YES`               | `NO`      | 是否在文档中内联显示源代码                                     |
| `STRIP_CODE_COMMENTS`    | `NO`                | `YES`     | 是否移除代码中的注释                                           |
| `VERBATIM_HEADERS`       | `YES`               | `YES`     | 是否保留头文件中的原始格式                                     |
| `REFERENCED_BY_RELATION` | `YES`               | `NO`      | 是否显示被引用关系图                                           |
| `REFERENCES_RELATION`    | `YES`               | `NO`      | 是否显示引用关系图                                             |
| `REFERENCES_LINK_SOURCE` | `YES`               | `YES`     | 是否在引用处链接到源代码                                       |
| `SOURCE_TOOLTIPS`        | `YES`               | `YES`     | 是否显示源代码悬停提示                                         |
| `RECURSIVE`              | `YES`               | `NO`      | 是否递归扫描子目录                                             |
| `PROJECT_NAME`           | `mysql`             | ``        | 项目名称                                                       |
| `PROJECT_NUMBER`         | `8.0.32`            | ``        | 指定项目的版本号或版本标识                                     |
| `PROJECT_BRIEF`          | `a database manage` | ``        | 项目的简短描述                                                 |
| `PROJECT_LOGO`           | `YES`               | ``        | 指定项目的 Logo 图片路径                                       |
| `OUTPUT_DIRECTORY`       | `mysql-8.0.32-docs` | `./`      | 指定生成的文档输出目录                                         |
| `CREATE_SUBDIRS`         | `YES`               | `NO`      | 是否在输出目录中创建子目录来组织文件,适合大型项目              |
| `OUTPUT_LANGUAGE`        | `Chinese`           | `English` | 指定生成文档的语言                                             |
| `ENABLE_PREPROCESSIN`    | `YES`               | `NO`      | 是否启用预处理                                                 |
| `BUILTIN_STL_SUPPORT`    | `YES`               | `NO`      | STL                                                            |
| `OPTIMIZE_OUTPUT_FOR_C`  | `NO`                | `NO`      | 如果是制作C程序文档，该选项必须设为 YES，否则默认生成C++文档格式 |
| `OPTIMIZE_OUTPUT_JAVA`   | `NO`                | `NO`      | 如果您的项目仅包含 Java 或 Python 源代码则配置为YES            |
| `OPTIMIZE_FOR_FORTRAN`   | `NO`                | `NO`      | 项目包含 Fortran 源代码                                        |
| `OPTIMIZE_OUTPUT_VHDL`   | `NO`                | `NO`      | 项目包含 VHDL 源代码                                           |
| `PTIMIZE_OUTPUT_SLICE`   | `NO`                | `NO`      | 项目包含 Slice 源代码                                          |
| `OPTIMIZE_FOR_FORTRAN`   | `NO`                | `NO`      | 项目包含 Fortran 源代码                                        |
| `OPTIMIZE_FOR_FORTRAN`   | `NO`                | `NO`      | 项目包含 Fortran 源代码                                        |
| `OPTIMIZE_FOR_FORTRAN`   | `NO`                | `NO`      | 项目包含 Fortran 源代码                                        |
| `OPTIMIZE_FOR_FORTRAN`   | `NO`                | `NO`      | 项目包含 Fortran 源代码                                        |
| `OPTIMIZE_FOR_FORTRAN`   | `NO`                | `NO`      | 项目包含 Fortran 源代码                                        |
| `OPTIMIZE_FOR_FORTRAN`   | `NO`                | `NO`      | 项目包含 Fortran 源代码                                        |
| `INLINE_INHERITED_MEMB`  | `YES`               | `NO`      | 是否将在该类的文档中显示所有继承自该类的成员                   |
| `TAB_SIZE`               | `8`                 | `4`       | 使用此值在代码片段中将制表符替换为空格                         |
| `SOURCE_BROWSER`         | `YES`               | `NO`      |                                                                |
| `SOURCE_BROWSER`         | `YES`               | `NO`      |                                                                |
| `SOURCE_BROWSER`         | `YES`               | `NO`      |                                                                |
| `SOURCE_BROWSER`         | `YES`               | `NO`      |                                                                |
| `SOURCE_BROWSER`         | `YES`               | `NO`      |                                                                |
| `SOURCE_BROWSER`         | `YES`               | `NO`      |                                                                |
| `SOURCE_BROWSER`         | `YES`               | `NO`      |                                                                |
| `SOURCE_BROWSER`         | `YES`               | `NO`      |                                                                |
| `SOURCE_BROWSER`         | `YES`               | `NO`      |                                                                |
| `SOURCE_BROWSER`         | `YES`               | `NO`      |                                                                |
| `SOURCE_BROWSER`         | `YES`               | `NO`      |                                                                |
| `SOURCE_BROWSER`         | `YES`               | `NO`      |                                                                |
| `SOURCE_BROWSER`         | `YES`               | `NO`      |                                                                |
| `SOURCE_BROWSER`         | `YES`               | `NO`      |                                                                |
| `SOURCE_BROWSER`         | `YES`               | `NO`      |                                                                |
| `SOURCE_BROWSER`         | `YES`               | `NO`      |                                                                |
| `SOURCE_BROWSER`         | `YES`               | `NO`      |                                                                |
| `SOURCE_BROWSER`         | `YES`               | `NO`      |                                                                |
| `SOURCE_BROWSER`         | `YES`               | `NO`      |                                                                |
| `SOURCE_BROWSER`         | `YES`               | `NO`      |                                                                |
| `SOURCE_BROWSER`         | `YES`               | `NO`      |                                                                |
| `SOURCE_BROWSER`         | `YES`               | `NO`      |                                                                |
|                          |                     |           |                                                                |














