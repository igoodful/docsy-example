---
title: lib
description: 动态库和静态库
date: 2023-11-06
weight: 30000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{%pageinfo%}}
**头文件位置**：
- . 当前目录
- -I 指定的路径(大写i)
- C_INCLUDE_PATH CPLUS_INCLUDE_PATH
- /usr/include 及其子目录底下的 include 文件夹
- /usr/local/include 及其子目录底下的 include 文件夹
- /usr/lib/gcc/x86_64-linux-gnu/12.2.0/include （与gcc安装路径有关，可以通过：which gcc）


**编译时库文件位置**：
- -l -L；-WI；rpath（从左到右搜索）
- LIBRARY_PATH
- runpath
- /etc/ld.so.conf ldconfig
- /lib
- /usr/lib
> -L指定的库路径，在动态链接的时候，库的路径信息不会被存到到目标文件中，运行时目标文件只能通过LD_LIBRARY_PATH以及ld.so.conf提供的路径查找，如果使用-Wl, rpath则库的路径会存到目标文件中，运行时目标文件会先去这个路径查找，找不到了，再去LD_LIBRARY_PATH和ld.so.conf中找
> 动态库优先：直接使用-l查找路径的时候，gcc/g++默认优先链接动态库，找不到动态库后，才去找静态库：`gcc xxx.c -o xxx -lopenssl ` （链接openssl.so）
> 要链接静态库就需要使用或者 -static 进行显示指定全都使用静态库：`gcc xxx.c -o xxx -static -lopenssl ` （链接libopenssl.a）
> 也可以使用 -Wl,-Bstatic， 这个选项使用后，后续的-l都是去找静态库，但可以通过Wl,-Bdynamic，再指定后面的都是动态库：`gcc xxx.c -o xxx -WL,-Bstatic -lxx1 -lxx2 -WL,-Bdynamic -lxx3`

**运行时库文件位置**：
- LD_LIBRARY_PATH
- /etc/ld.so.conf ldconfig
- /lib
- /usr/lib
{{%/pageinfo%}}



{{<note>}}
<!---->

**目标文件**：目标文件xxx.oc的生成，使用 -c 选项，指定只生成而不链接，形如：`gcc -c xxx.c -o xxx.o`












{{</note>}}








### 制作目标文件
```bash
gcc -c xxx.c -o xxx.o
```









### 制作动态库
> 动态库的生成，使用-shared要求生成动态库，通常还加上-fpic要求生成的代码地址无关，就是要动态库内部的使用相对偏移寻址，防止安全攻击

1. 直接生成
```bash
gcc x1.c -fPIC -shared -o libx.so
```

2. 先生成目标文件，再生成动态库
```bash
gcc -Wall -c -fPIC test_shared.c
gcc -shared -fPIC test_shared.o -o libtest_shared.so
```


## 制作静态库
> 静态库的生成，需先生成目标文件，使用ar rcs命令对其做转换
```bash
gcc -c x1.c -o x1.o
gcc -c x2.c -o x2.o
ar rsc libx.a x1.o x2.o

```












