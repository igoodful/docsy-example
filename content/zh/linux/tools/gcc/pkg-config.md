---
title: pkg-config
description: pkg-config
date: 2023-11-17
weight: 30000


---

{{< alert >}}
**pkg-config**：生成链接参数
{{< /alert >}}


```bash
pkg-config --list-all

pkg-config openssl --libs --cflags
```


```bash
[root@gip tmp]# pkg-config ncurses --libs --cflags
 -lncurses
[root@gip tmp]# pkg-config openssl --libs --cflags
 -lssl -lcrypto

```

