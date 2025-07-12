---
title: 编译
description: introduction
date: 2023-11-17
weight: 20


viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

- 编译器在编译时是以C文件为单位进行的，也就是说如果你的项目中一个C文件都没有，那么你的项目将无法编译
- 连接器是以目标文件(.obj文件)为单位，它将一个或多个目标文件进行函数与变量的重定位，生成最终的可执行文件
- xxx.h文件必须被包含在xxx.c文件中


{{< /alert >}}


{{<alert>}}


{{</alert>}}


## 编译过程

- hexagon
- septagon
- octagon
- doubleoctagon
- tripleoctagon

```viz-dot
digraph "c++" {
        node[ fontsize = "12" ];
        rankdir = LR;
        node0 [ label="编译单元\n.c", shape = tripleoctagon ];
        node1 [ label=".i", shape = octagon ];
        node2 [ label=".S", shape = octagon ];
        node3 [ label=".o", shape = octagon ];
        node4 [ label="可执行文件\n动态库\n静态库", shape = tripleoctagon ];
        node0 -> node1 [label="预处理\ngcc -E"];
        node1 -> node2 [label="编译\ngcc -S"];
        node2 -> node3 [label="汇编\ngcc -c"];
        node3 -> node4 [label="链接"];
}
```


##  一、预处理

C++ 预处理器在编译源代码之前执行一系列操作，以准备源代码进行实际的编译。预处理器的主要任务包括以下几个方面：

- 头文件包含（Include）：处理 #include 指令，将指定的头文件内容插入到当前文件中。这样可以将多个源文件中共享的代码放在单独的头文件中，并在需要时包含进来。

- 宏替换（Macro Replacement）：处理宏定义指令，将宏名称替换为其对应的定义。宏定义可以是简单的文本替换，也可以包含参数的宏，甚至是复杂的宏函数。

- 条件编译（Conditional Compilation）：处理条件编译指令，如 #if、#ifdef、#ifndef、#elif 和 #endif 等。根据条件编译指令的条件，选择性地包含或排除一些代码。

- 行连接（Line Concatenation）：处理反斜杠 \，将多行代码连接为单行。这在宏定义或长字符串中特别有用。

- 注释移除（Comment Removal）：移除源代码中的注释，包括单行注释 // 和多行注释 /* */。注释被移除后，不会包含在编译后的代码中。

- 字符编码处理：处理字符编码指令，如 #pragma 指令等，以及处理字符集指令 #charset。

- 其他预处理指令：处理其他预处理指令，如 #define、#undef、#error、#warning 等。

预处理器通过执行这些操作，可以在实际的编译过程中对源代码进行一些必要的处理，以便生成可编译的代码。预处理器处理完源代码后，生成的结果通常被送到编译器进行实际的编译。


## 二、编译





## 三、汇编



平时编译用的命令 gcc -o hello.o hello.c 相当于预处理、编译、汇编这三步骤命令的组合拳








## 四、链接


在C++的连接阶段，主要进行以下几个操作：

- 符号解析（Symbol Resolution）：连接器会对源文件中引用的函数和变量进行符号解析，确定它们在程序中的具体地址或者绑定到其他目标文件中的符号。

- 重定位（Relocation）：如果源文件中引用了其他目标文件或库中的函数或变量，连接器会将这些引用修正为正确的地址或偏移量，以便程序执行时能够正确访问这些符号。

- 符号合并（Symbol Merging）：对于相同名称的全局符号，连接器需要确保只有一个定义被链接进最终的可执行文件中。如果有多个定义，则会导致重定义错误。

- 生成可执行文件（Executable File Generation）：连接器将所有目标文件和库文件合并在一起，生成最终的可执行文件。这个可执行文件包含了所有的代码和数据，以及符号表和其他元信息，用于程序的执行。

- 动态链接（Dynamic Linking，可选）：如果程序使用了动态链接库（DLL）或共享目标文件（SO），连接器可能会将一部分代码和数据保留为对这些库的动态链接，而不是静态地将其合并到可执行文件中。这样可以减少可执行文件的大小，并允许库的更新和共享。

这些操作组合在一起，将单个源文件、多个源文件以及可能的库文件连接到一起，生成最终的可执行文件或者动态链接库。连接器是编译过程中的最后一步，它将不同的代码模块整合在一起，使得程序能够正确执行。







## 前置声明

- [前置声明](https://www.cnblogs.com/rednodel/p/5000602.html)
- [前置声明](https://www.cnblogs.com/Rainingday/p/13869853.html)
- [前置声明](https://www.runoob.com/w3cnote/cpp-header.html)





























