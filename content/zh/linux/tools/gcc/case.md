---
title: case
description: case
date: 2023-11-17
weight: 30000


---

{{< alert >}}
**pkg-config**：生成链接参数
{{< /alert >}}


### xxxx.h: No such file or directory
> 头文件找不到
```bash

```

### undefined reference to 'xxxxx'
首先这是链接错误，不是编译错误，也就是说如果只有这个错误，说明你的程序源码本身没有问题，是你用编译器编译时参数用得不对，你没有指定链接程序要用到得库，比如你的程序里用到了一些数学函数，那么你就要在编译
参数里指定程序要链接数学库，方法是在编译命令行里加入-lm。
```bash
[root@gip tmp]# pkg-config ncurses --libs --cflags
 -lncurses
[root@gip tmp]# pkg-config openssl --libs --cflags
 -lssl -lcrypto

```












