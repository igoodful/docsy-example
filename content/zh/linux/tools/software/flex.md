---
title: flex
description: flex
date: 2023-11-06
weight: 200


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{%pageinfo%}}
- [flex download](https://github.com/westes/flex/releases)

**依赖**：
- gettext


{{%/pageinfo%}}



{{<note>}}
<!---->
要从 git 存储库构建：

首先，确保您在路径上的某个位置安装了 Flex 的副本，以便 configure 可以找到它。您通常可以使用操作系统安装软件的标准方法来完成此操作。然而，有时，您必须从最近发布的 Flex 进行构建。如果您已经引导这样做，那么使用从 Flex 代码库构建的 Flex 版本始终是可以接受的。

您还需要 Flex 需要的所有程序才能从头开始构建：
- gcc
- bash
- m4 - m4 -P needs to work;
- bison; to generate parse.c from parse.y
- autoconf; for handling the build system
- automake; for Makefile generation
- make; for running the generated Makefiles
- gettext; for i18n support
- help2man; to generate the flex man page
- tar, gzip, lzip, etc.; for packaging of the source distribution
- texinfo



autogen.sh 成功完成后，构建 Flex 遵循与从发布存档构建 Flex 相同的步骤。

{{</note>}}



### 源码编译安装
```bash

wget https://github.com/westes/flex/releases/download/v2.6.4/flex-2.6.4.tar.gz
tar -xzvf flex-2.6.4.tar.gz
cd gettext-0.22.4
./autogen.sh
./configure --prefix=$HOME/local  && make && make install

```

### 配置环境变量PATH
`cat $HOME/.bashrc`
```bash
export PATH=$HOME/local/bin:$PATH
```

### 生效
```bash
source $HOME/.bashrc
```









